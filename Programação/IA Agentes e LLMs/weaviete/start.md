
#weaviate #mongodb #langchain #embedding 

```python
from pprint import pprint  
from typing import List  
  
import weaviate  
from langchain_core.documents import Document  
from langchain_core.prompts import PromptTemplate  
from langchain_core.runnables import RunnableLambda, RunnableParallel, RunnablePassthrough  
from langchain_weaviate import WeaviateVectorStore  
from weaviate.auth import AuthApiKey  
from weaviate.classes.query import Filter  
  
from src.config.settings import settings  
from src.infrastructure.repositories.mongodb.knowledge_repository import KnowledgeRepository  
from src.infrastructure.repositories.mongodb.mongo_connector import MongoConnector  
from src.infrastructure.services.embedding_service import EmbeddingService  
from src.infrastructure.services.lc_embedding_service import LCEmbeddingService  
from src.infrastructure.services.llm_factory import LLMFactory  
from src.infrastructure.services.model_loader_service import ModelLoaderService  
  
weaviate_client = weaviate.connect_to_weaviate_cloud(  
    cluster_url=settings.WEAVIATE_URL,  
    auth_credentials=AuthApiKey(settings.WEAVIATE_KEY)  
)  
  
model_loader = ModelLoaderService(model_name="intfloat/multilingual-e5-small")  
embedding = EmbeddingService(model_loader)  
lc_emb = LCEmbeddingService(embedding)  
  
vectorstore = WeaviateVectorStore(  
    client=weaviate_client,  
    index_name="Livros_Infloat",  
    text_key="fileName",  
    embedding=lc_emb  
)  
  
filters = Filter.all_of([  
    Filter.by_property("segmento").equal("PV")  
])  
  
retriever = vectorstore.as_retriever(  
    search_kwargs={"k": 5, "filters": filters}  
)  
  
ask = "Quem foi don pedro ii?"  
docs: List[Document] = retriever.invoke(ask)  
  
pprint(docs)  
  
file_names = [  
    doc.page_content  
    for doc in docs  
]  
  
if not file_names:  
    raise ValueError("Nenhum fileName encontrado nos documentos recuperados.")  
  
print(file_names[0])  
  
connector = MongoConnector(  
    uri=settings.MONGO_URI,  
    db_name=settings.MONGO_DB  
)  
  
repo = KnowledgeRepository(connector)  
  
mongo_records = repo.find_by_filenames(filenames=file_names)  
mongo_payload = [record.model_dump() for record in mongo_records]  
pprint(mongo_payload)  
  
mongo_docs = []  
for payload in mongo_payload:  
    content = payload.pop("content", "")  
    if not content:  
        continue  
    mongo_docs.append(  
        Document(            page_content=content,  
            metadata=payload  
        )  
    )  
if not mongo_docs:  
    raise ValueError("Nenhum documento retornado pelo Mongo para montar o retriever.")  
  
mongo_retriever = RunnableLambda(lambda _: mongo_docs)  
  
llm = LLMFactory(model="gpt-4o-mini").get_llm()  
  
prompt = PromptTemplate.from_template(  
    "Você é um assistente especializado em responder perguntas usando somente o contexto fornecido.\n"  
    "Regras:\n"  
    "- Se o contexto estiver vazio, em branco ou for igual a \"N/A\", responda com uma string vazia.\n"  
    "- Caso contrário, use apenas informações presentes no contexto para responder.\n\n"  
    "- Forcena uma resposta com o maximo de aproveitamento e detalhes com o contexto.\n\n"  
    "Contexto:\n{context}\n\n"  
    "Pergunta:\n{question}\n\n"  
    "Resposta:")  
  
weaviate_client.close()  
  
rag_chain = (  
        RunnableParallel(            context=mongo_retriever | (lambda ds: "\n\n".join(d.page_content for d in ds)),  
            question=RunnablePassthrough()  
        )        | prompt  
        | llm  
)  
  
answer = rag_chain.invoke("Quem foi pedro II?")  
print(answer.content)

```