#langchain #chain #python #openai #langchain_community #langchain_openai 

```bash
pip install langchain[openai] langchain-community
```

```python
import os  
  
from langchain.chains import create_history_aware_retriever, create_retrieval_chain  
from langchain.chains.combine_documents import create_stuff_documents_chain  
from langchain_community.vectorstores import FAISS  
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder  
from langchain_openai import ChatOpenAI  
from langchain_openai import OpenAIEmbeddings  
  
os.environ["OPENAI_API_KEY"] = ""  
  
llm = ChatOpenAI()  
  
# Crie/obtenha um retriever válido (BaseRetriever/Runnable).  
# Exemplo mínimo usando FAISS + OpenAIEmbeddings a partir de textos em memória.  
# Substitua `texts` pelos seus dados/documentos reais.  
texts = [  
    "Para configurar a autenticação no sistema, você deve definir as variáveis de ambiente e registrar o provedor OAuth.",  
    "A documentação de configuração do sistema está localizada no README interno do projeto.",  
    "Autenticação pode ser feita via token ou OAuth2, dependendo do módulo instalado."  
]  
embeddings = OpenAIEmbeddings()  
vectorstore = FAISS.from_texts(texts, embeddings)  
my_retriever = vectorstore.as_retriever()  
  
# Prompt para contextualizar questões  
contextualize_q_system_prompt = (  
    "Dado o histórico de chat e a última pergunta do usuário, "  
    "reformule a pergunta para que seja independente do contexto anterior.")  
  
contextualize_q_prompt = ChatPromptTemplate.from_messages([  
    ("system", contextualize_q_system_prompt),  
    MessagesPlaceholder("chat_history"),  
    ("human", "{input}"),  
])  
  
# Criando retriever com consciência de histórico  
history_aware_retriever = create_history_aware_retriever(  
    llm, my_retriever, contextualize_q_prompt  
)  
  
# Prompt para responder questões  
qa_system_prompt = (  
    "Você é um assistente para responder perguntas. Use "  
    "os seguintes trechos de contexto recuperado para responder "    "a pergunta. Se não souber a resposta, diga que não sabe.\n\n"    "{context}")  
  
qa_prompt = ChatPromptTemplate.from_messages([  
    ("system", qa_system_prompt),  
    MessagesPlaceholder("chat_history"),  
    ("human", "{input}"),  
])  
  
# Chain de documentos  
question_answer_chain = create_stuff_documents_chain(llm, qa_prompt)  
  
# Chain de recuperação final  
rag_chain = create_retrieval_chain(history_aware_retriever, question_answer_chain)  
  
# Executando  
chat_history = []  
response = rag_chain.invoke({  
    "input": "Como configurar a autenticação no sistema?",  
    "chat_history": chat_history  
})  
print(response["answer"])
```