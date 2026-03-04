#python #langchain #langchain_openai #rag #chromadb #web_scraping #ia  

```python
import os
from pathlib import Path

os.environ['USER_AGENT'] = 'NeoNews/1.0'
import warnings

from decouple import config
from langchain import hub
from langchain_community.document_loaders import WebBaseLoader
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI, OpenAIEmbeddings
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_chroma import Chroma

warnings.filterwarnings("ignore", category=UserWarning, module="langsmith.client")

os.environ['OPENAI_API_KEY'] = config('OPENAI_API_KEY')

# Define persist directory in project root
persist_directory = "chroma_db"
Path(persist_directory).mkdir(exist_ok=True)

# Initialize embedding model
embedding = OpenAIEmbeddings()

# Check if the vector store already exists
if not os.path.exists(persist_directory) or not os.listdir(persist_directory):
    # Load and process documents
    loader = WebBaseLoader("https://pt.wikipedia.org/wiki/Leif_Eriksson")
    docs = loader.load()
    text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
    splits = text_splitter.split_documents(docs)

    # Create and persist the vector store
    vectorstore = Chroma.from_documents(
        documents=splits,
        embedding=embedding,
        persist_directory=persist_directory
    )
else:
    # Load existing vector store
    vectorstore = Chroma(
        persist_directory=persist_directory,
        embedding_function=embedding
    )

# Create retriever and RAG chain
retriever = vectorstore.as_retriever()
prompt = hub.pull("rlm/rag-prompt")
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
result = rag_chain.invoke("Quem foi Leif_Eriksson?")
print(result)

```