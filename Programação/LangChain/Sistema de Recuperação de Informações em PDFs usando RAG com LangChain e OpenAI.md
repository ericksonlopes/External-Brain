
#rag #langchain #openai #chromadb #PDF #pydantic #python #ai 
  
  
## Aprenda como construir um sistema de recuperação de informações eficiente utilizando PDFs, LangChain e OpenAI.  
  
# Introdução  
  
A recuperação de informações em documentos PDF é uma tarefa comum em diversas aplicações, desde a pesquisa acadêmica até o suporte ao cliente. Neste artigo, vamos explorar como implementar um sistema de recuperação de informações utilizando o conceito de Retrieval-Augmented Generation (RAG) com LangChain e OpenAI.  
  
# Pré-requisitos  
  
Antes de começarmos, você deve ter os seguintes pré-requisitos:  
- Python 3.8 ou superior  
- A biblioteca LangChain instalada  
- A biblioteca Pydantic instalada  
- Uma chave de API do OpenAI  
  
Você pode instalar as bibliotecas necessárias com o seguinte comando:  
  
```bash  
pip install langchain-openai langchain-community langchain-chroma loguru pypdf```  
  
# Configuração do Ambiente  
  
Primeiro, vamos configurar um arquivo `.env` para armazenar nossas variáveis de ambiente. Este arquivo deve conter as seguintes linhas:  
  
```  
OPENAI_API_KEY=your_openai_api_key  
USER_AGENT=your_user_agent
```  
  
Após configurar o arquivo `.env`, podemos prosseguir com a implementação.  
  
# Estrutura do Código  
  
Vamos dividir nosso código em várias seções para facilitar a compreensão:  
  
## 1. Configuração Inicial  
  
Aqui, importamos as bibliotecas necessárias e configuramos o logger e as configurações:  
  
```python  
from loguru import logger  
from pydantic_settings import BaseSettings  
  
class Settings(BaseSettings):  
    OPENAI_API_KEY: str  
    USER_AGENT: str  
  
    class Config:  
        env_file = ".env"  
settings = Settings()  
logger.add("file.log", rotation="500 MB", level="INFO")  
logger.add(lambda msg: print(msg), level="DEBUG")  
```  
  
## 2. Carregamento do PDF  
  
Usaremos a classe `PyPDFLoader` para carregar nosso PDF e a classe `CharacterTextSplitter` para dividir o texto em partes menores:  
  
```python  
from langchain.text_splitter import CharacterTextSplitter  
from langchain_community.document_loaders import PyPDFLoader  
  
path = r"path_file"  
loader = PyPDFLoader(path)  
documents = loader.load()  
  
text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=0)  
texts = text_splitter.split_documents(documents)  
```  
  
## 3. Armazenamento em Chroma  
  
Vamos usar o Chroma para armazenar nossos documentos:  
  
```python  
from langchain_chroma import Chroma  
  
persist_directory = "chroma_db"  
Path(persist_directory).mkdir(exist_ok=True)  
  
embedding = OpenAIEmbeddings()  
  
vectorstore = Chroma.from_documents(  
    documents=texts,  
    embedding=embedding,  
    persist_directory=persist_directory  
)  
```  
  
## 4. Recuperação de Documentos  
  
Agora, configuramos o recuperador para buscar informações relevantes:  
  
```python  
retriever = vectorstore.as_retriever(  
    search_type="mmr",  
    search_kwargs={"k": 3, "fetch_k": 10, "lambda_mult": 0.5},  
    filter={"total_pages": 32}  
)  
```  
  
## 5. Criação do Chain RAG  
  
Por fim, criamos o prompt e o chain RAG para processar as perguntas:  
  
```python  
from langchain_core.prompts import PromptTemplate  
from langchain_openai import ChatOpenAI  
from langchain_core.output_parsers import StrOutputParser  
from langchain_core.runnables import RunnablePassthrough  
  
prompt = PromptTemplate.from_template(  
    "Use os seguintes trechos de contexto para responder à pergunta no final.\n"    "Se você não sabe a resposta, apenas diga que não sabe, não tente inventar uma resposta.\n"    "Contexto: {context} \n\n Pergunta: {question}")  
  
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)  
  
rag_chain = (  
    {"context": retriever | format_docs, "question": RunnablePassthrough()}  
    | prompt    | llm    | StrOutputParser()  
)  
```  
  
# Usando o Sistema  
  
Agora que tudo está configurado, podemos fazer uma pergunta ao nosso sistema:  
  
```python  
result = rag_chain.invoke("Escreva três perguntas sobre While e IF e cite um exemplo de cada")  
print(result)  
```  
  
# Conclusão  
  
Neste artigo, exploramos como construir um sistema de recuperação de informações de PDFs utilizando LangChain e OpenAI. Este sistema pode ser expandido e adaptado para diferentes tipos de documentos e casos de uso. Sinta-se à vontade para experimentar e aprimorar seu sistema!  
  
# Boas Práticas  
- Sempre trate erros e exceções adequadamente para evitar falhas em produção.  
- Mantenha suas dependências atualizadas.  
- Utilize logs para monitorar o comportamento do seu sistema.  
  
# Referências  
- [LangChain Documentation](https://langchain.readthedocs.io/en/latest/)  
- [OpenAI API Documentation](https://beta.openai.com/docs/)  
  
## Nota do Autor  
Este artigo é baseado em experiências práticas e busca ajudar desenvolvedores a criarem soluções eficazes para recuperação de informações.  
  
## Exemplos de Código  
  
### Exemplo 1  
```python  
from loguru import logger  
from pydantic_settings import BaseSettings  
  
class Settings(BaseSettings):  
    OPENAI_API_KEY: str  
    USER_AGENT: str  
    RABBITMQ_DEFAULT_USER: str  
    RABBITMQ_DEFAULT_PASS: str  
  
    class Config:  
        env_file = ".env"  
settings = Settings()  
logger.add("file.log", rotation="500 MB", level="INFO")  
logger.add(lambda msg: print(msg), level="DEBUG")  
```  
  
### Exemplo 2  
```python  
import os  
import pprint  
import warnings  
from pathlib import Path  
  
from langchain.text_splitter import CharacterTextSplitter  
from langchain_chroma import Chroma  
from langchain_community.document_loaders import PyPDFLoader  
from langchain_core.output_parsers import StrOutputParser  
from langchain_core.prompts import PromptTemplate  
from langchain_core.runnables import RunnablePassthrough  
from langchain_openai import OpenAIEmbeddings, ChatOpenAI  
  
from src.config.settings import settings  
  
warnings.filterwarnings("ignore", category=UserWarning, module="langsmith.client")  
  
os.environ['OPENAI_API_KEY'] = settings.OPENAI_API_KEY  
  
# Define persist directory in project root  
persist_directory = "chroma_db"  
Path(persist_directory).mkdir(exist_ok=True)  
  
# Initialize embedding model  
embedding = OpenAIEmbeddings()  
  
# Check if the vector store already exists  
if not os.path.exists(persist_directory) or not os.listdir(persist_directory):  
    path = r"C:\Users\ofcer\PycharmProjects\UniversityHubRAG\pdfs\Tutorial de Introducao ao Python - 32p.pdf"  
  
    loader = PyPDFLoader(path)  
    documents = loader.load()  
  
    text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=0)  
    texts = text_splitter.split_documents(documents)  
  
    print(texts[-1])  
  
    vectorstore = Chroma.from_documents(  
        documents=texts,  
        embedding=embedding,  
        persist_directory=persist_directory  
    )  
else:  
    vectorstore = Chroma(  
        persist_directory=persist_directory,  
        embedding_function=embedding  
    )  
  
retriever = vectorstore.as_retriever(  
    search_type="mmr",  
    search_kwargs={"k": 3, "fetch_k": 10, "lambda_mult": 0.5},  
    filter={'total_pages': 32}  
)  
  
# docs = retriever.invoke("O que faz um while?" )  
# pprint.pprint(docs)  
  
prompt = PromptTemplate.from_template(  
    "Use os seguintes trechos de contexto para responder à pergunta no final."  
    "Se você não sabe a resposta, apenas diga que não sabe, não tente inventar uma resposta."    "Contexto: {context} \n\n Pergunta: {question}")  
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)  
  
  
def format_docs(docs):  
    return "\n\n".join(doc.page_content for doc in docs)  
  
  
rag_chain = (  
        {"context": retriever | format_docs, "question": RunnablePassthrough()}  
        | prompt  
        | llm  
        | StrOutputParser()  
)  
  
# Use the RAG chain  
result = rag_chain.invoke("Escreva três perguntas sobre While e IF e site um exmplo de cada")  
print(result)
```  
  
## Referências  
  
- https://langchain.readthedocs.io/en/latest/  
- https://beta.openai.com/docs/  
  
---  
*Este artigo é baseado em experiências práticas e busca ajudar desenvolvedores a criarem soluções eficazes para recuperação de informações.*  
  
---  