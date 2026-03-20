from langchain.retrievers import ContextualCompressionRetriever

from langchain.retrievers.document_compressors import FlashRankRerank

from langchain_openai import ChatOpenAI

# 1. O re-ranker (pode ser FlashRank, Cohere, ou um Cross-Encoder local)

compressor = FlashRankRerank()

# 2. O seu retriever base (Weaviate ou FAISS)

base_retriever = vectorstore.as_retriever()

# 3. O retriever "turbinado" com re-rank

compression_retriever = ContextualCompressionRetriever(

base_compressor=compressor,

base_retriever=base_retriever

)

# Ao chamar, ele já recupera e re-ranqueia automaticamente

docs = compression_retriever.get_relevant_documents("Quem é Erickson?")