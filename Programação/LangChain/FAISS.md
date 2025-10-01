#faiss #embedding #rag 

```pipfile
[[source]]
url = "https://pypi.org/simple"
verify_ssl = true
name = "pypi"

[packages]
langchain = "*"
openai = "*"
pymupdf = "*"
tiktoken = "*"
langchain-community = "*"
pypdf = "*"

[dev-packages]

[requires]
python_version = "3.10"

```

```python
from pprint import pprint

from langchain_community.document_loaders import PyPDFLoader
from langchain_community.vectorstores import FAISS
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import OpenAIEmbeddings, ChatOpenAI

from api_key import api_key


def create_index_for_pdf(path_pdf):
    """
    Carrega documentos PDF e cria um índice de embeddings usando FAISS.
    """
    loader = PyPDFLoader(path_pdf)
    documents = loader.load()

    embeddings = OpenAIEmbeddings(api_key=api_key)
    index = FAISS.from_documents(documents, embeddings)
    index.save_local(folder_path="belo_desastre")


# Criação do índice comentada para evitar execução acidental
# create_index(path_pdf="belo_desastre.pdf")

# Carrega o índice local criado previamente
vectorstore = FAISS.load_local("belo_desastre", OpenAIEmbeddings(api_key=api_key), allow_dangerous_deserialization=True)

retriever = vectorstore.as_retriever(search_type="similarity")

# docs = retriever.retrieve_documents("Por que a Abby termina com o Travis em Vegas? explique detalhadamente.", k=5)
message = "Por que a Abby termina com o Travis em Vegas? explique detalhadamente."
docs = retriever.invoke(message, k=7)

pprint(docs)

# Template para o prompt do LLM
template = ("Você é um leitor experiente do livro 'Belo Desastre' e sua tarefa é responder perguntas sobre os "
            "acontecimentos e  personagens do livro. Utilize o conhecimento disponível nos documentos fornecidos para "
            "responder de forma precisa. Caso não encontre a resposta nos documentos, por favor, informe que não "
            "encontrou a informação e não invente  informações falsas. "
            "\n Documentos: {documents} \nPergunta: {message}")

prompt = ChatPromptTemplate.from_template(template)

llm = ChatOpenAI(api_key=api_key, model="gpt-3.5-turbo")

chain = prompt | llm | StrOutputParser()
output = chain.invoke({"documents": docs, "message": message})
print(output)

```