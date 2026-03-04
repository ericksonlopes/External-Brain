#rag #langchain #openai #chromadb #PDF #pydantic #python #ia   #pydantic-settings
  
## Integrando documentos PDF em um fluxo de trabalho de recuperação de informações com LangChain e OpenAI  
  
# Introdução  
  
A recuperação de informações é um componente essencial em sistemas que buscam extrair conhecimento a partir de documentos não estruturados, como PDFs. Neste artigo, vamos explorar como implementar um sistema de recuperação de informações utilizando o modelo de Recuperação de Acompanhamento (RAG) com a biblioteca LangChain e a API da OpenAI. O foco será na integração de documentos PDF e na utilização do `ParentDocumentRetriever` para facilitar a pesquisa em conteúdos complexos.  
  
## Pré-requisitos  
  
Antes de começarmos, certifique-se de ter os seguintes pacotes instalados:  
  
```bash  
pip install langchain-openai langchain-chroma langchain-community loguru pydantic-settings
```  
  
Além disso, você precisará de uma chave de API da OpenAI. Crie um arquivo `.env` na raiz do seu projeto e adicione sua chave:  
  
```plaintext  
OPENAI_API_KEY=your_api_key  
USER_AGENT=your_user_agent  
RABBITMQ_DEFAULT_USER=your_user  
RABBITMQ_DEFAULT_PASS=your_pass  
```  
  
## Estrutura do Código  
  
Vamos dividir o código em seções para melhor compreensão. Abaixo, mostramos as principais partes do sistema:  
  
### 1. Configuração de Ambiente e Logger  
  
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
  
Neste trecho, utilizamos `pydantic` para carregar variáveis de ambiente e `loguru` para logging. O logger registra informações em um arquivo e também imprime mensagens de debug no console.  
  
### 2. Carregamento e Processamento de PDFs  
  
```python  
import os  
from pathlib import Path  
from langchain.retrievers import ParentDocumentRetriever  
from langchain.storage import InMemoryStore  
from langchain_chroma import Chroma  
from langchain_community.document_loaders import PyPDFLoader  
from langchain_core.output_parsers import StrOutputParser  
from langchain_core.prompts import PromptTemplate  
from langchain_core.runnables import RunnablePassthrough  
from langchain_openai import OpenAIEmbeddings, ChatOpenAI  
from langchain_text_splitters import RecursiveCharacterTextSplitter  
  
os.environ['OPENAI_API_KEY'] = settings.OPENAI_API_KEY  
  
persist_directory = "chroma_db_persist"  
Path(persist_directory).mkdir(exist_ok=True)  
  
path = r"C:\Users\ofcer\PycharmProjects\UniversityHubRAG\pdfs\Tutorial de Introducao ao Python - 32p.pdf"  
loader = PyPDFLoader(path)  
docs = loader.load()  
```  
  
Aqui, carregamos um PDF utilizando `PyPDFLoader`. O `RecursiveCharacterTextSplitter` é utilizado para dividir o texto em partes menores, facilitando a recuperação de informações.  
  
### 3. Configuração do Armazenamento e Recuperador  
  
```python  
parent_splitter = RecursiveCharacterTextSplitter(chunk_size=2000)  
child_splitter = RecursiveCharacterTextSplitter(chunk_size=400)  
  
vectorstore = Chroma(  
    collection_name="split_parents",  
    embedding_function=OpenAIEmbeddings(),  
    persist_directory=persist_directory  
)  
  
storage = InMemoryStore()  
  
retriever = ParentDocumentRetriever(  
    vectorstore=vectorstore,  
    child_splitter=child_splitter,  
    parent_splitter=parent_splitter  
)  
```  
  
Neste código, configuramos o `Chroma` como um armazenamento para os vetores dos documentos. O `ParentDocumentRetriever` é responsável por buscar documentos relevantes com base em consultas fornecidas.  
  
### 4. Executando Consultas e Gerando Respostas  
  
```python  
retrieved_docs = retriever.invoke("o que faz o while?")  
print(retrieved_docs)  
  
prompt = PromptTemplate.from_template(  
    "Use os seguintes trechos de contexto para responder à pergunta no final."    "Se você não sabe a resposta, apenas diga que não sabe, não tente inventar uma resposta."    "Contexto: {context} \n\n Pergunta: {question}")  
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)  
  
def format_docs(docs):  
    return "\n\n".join(doc.page_content for doc in docs)  
  
rag_chain = (  
        {"context": retriever | format_docs, "question": RunnablePassthrough()}  
        | prompt        | llm        | StrOutputParser()  
)  
  
# Use the RAG chain  
result = rag_chain.invoke("O que faz um while?")  
print(result)  
```  
  
Aqui, invocamos o `retriever` para buscar informações sobre a pergunta "o que faz o while?" e formatamos os documentos recuperados. Em seguida, utilizamos o modelo OpenAI para gerar uma resposta com base no contexto fornecido.  
  
## Boas Práticas  
- **Gerenciamento de Erros:** Sempre implemente gerenciamento de erros para lidar com exceções que podem ocorrer durante a leitura de arquivos ou chamadas à API.  
- **Validação de Dados:** Utilize `Pydantic` para validar e garantir que suas configurações estão corretas.  
- **Logging Eficiente:** Use diferentes níveis de logs (INFO, DEBUG, ERROR) para facilitar a depuração e monitoramento da aplicação.  
  
## Conclusão  
  
Neste artigo, cobrimos a implementação de um sistema de recuperação de informações utilizando a biblioteca LangChain e a API da OpenAI, integrando documentos PDF em um fluxo de trabalho eficiente. Com a utilização do `ParentDocumentRetriever`, conseguimos extrair informações relevantes de forma eficaz, demonstrando o potencial das ferramentas modernas para o processamento de linguagem natural.  
  
## Referências  
- [LangChain Documentation](https://langchain.readthedocs.io/en/latest/)  
- [OpenAI API Documentation](https://beta.openai.com/docs/)  
  
  
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
from pathlib import Path  
  
from langchain.retrievers import ParentDocumentRetriever  
from langchain.storage import InMemoryStore  
from langchain_chroma import Chroma  
from langchain_community.document_loaders import PyPDFLoader  
from langchain_core.output_parsers import StrOutputParser  
from langchain_core.prompts import PromptTemplate  
from langchain_core.runnables import RunnablePassthrough  
from langchain_openai import OpenAIEmbeddings, ChatOpenAI  
from langchain_text_splitters import RecursiveCharacterTextSplitter  
  
from src.config.settings import settings  
  
os.environ['OPENAI_API_KEY'] = settings.OPENAI_API_KEY  
  
persist_directory = "chroma_db_persist"  
Path(persist_directory).mkdir(exist_ok=True)  
  
path = r"C:\Users\ofcer\PycharmProjects\UniversityHubRAG\pdfs\Tutorial de Introducao ao Python - 32p.pdf"  
loader = PyPDFLoader(path)  
docs = loader.load()  
  
parent_splitter = RecursiveCharacterTextSplitter(chunk_size=2000)  
child_splitter = RecursiveCharacterTextSplitter(chunk_size=400)  
  
vectorstore = Chroma(  
    collection_name="split_parents",  
    embedding_function=OpenAIEmbeddings(),  
    persist_directory=persist_directory  
)  
  
storage = InMemoryStore()  
  
retriever = ParentDocumentRetriever(  
    vectorstore=vectorstore,  
    child_splitter=child_splitter,  
    parent_splitter=parent_splitter  
)  
  
# retriever.add_documents(docs)  
  
retrieved_docs = retriever.invoke("o que faz o while?")  
print(retrieved_docs)  
  
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
result = rag_chain.invoke("O que faz um while?")  
print(result)
```  
  
## Referências  
  
- https://langchain.readthedocs.io/en/latest/  
- https://beta.openai.com/docs/  
  
---  
*Este artigo foi escrito para ajudar desenvolvedores a entenderem como integrar sistemas de recuperação de informações usando ferramentas modernas.*  
  
---  