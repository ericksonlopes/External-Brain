#langchain #langchain_openai #langchain_community #python #playwright #scraping #web_scraping #pydantic #IA #beautifulsoup 


Este código implementa um web scraper utilizando LangChain e Playwright para extrair e estruturar informações de artigos de notícias, com foco na extração de conteúdo a partir de tags HTML específicas.

## Componentes Principais:

- **`json`**: Utilizado para salvar os resultados extraídos em um arquivo JSON.
- **`os`**: Configura a chave da API do OpenAI, necessária para utilizar o modelo de linguagem.
- **`decouple.config`**: Carrega a chave da API do OpenAI de um arquivo `.env`.
- **`langchain_community.document_loaders.AsyncChromiumLoader`**: Carrega as páginas da web de forma assíncrona usando o Chromium.
- **`langchain_community.document_transformers.BeautifulSoupTransformer`**: Transforma os documentos carregados utilizando BeautifulSoup para extrair tags HTML específicas.
- **`langchain_openai.ChatOpenAI`**: Interface para a OpenAI, configurando o modelo GPT-4 mini.
- **`langchain_text_splitters.RecursiveCharacterTextSplitter`**: Divide o conteúdo dos documentos em partes menores para facilitar o processamento.
- **`pydantic.BaseModel` e `pydantic.Field`**: Utilizados para definir o modelo estruturado `Article` e garantir que as informações extraídas sigam uma estrutura predefinida.

## Passos do Código:

1. **Classe `Article`**:
    
    - Define a estrutura de um artigo, com campos como título (`headline`), descrição (`description`), data de publicação (`date`), publisher (`publisher`) e link (`link`).
2. **Classe `WebScraper`**:
    
    - **Inicialização**: Configura a chave da API do OpenAI e cria uma instância do modelo `gpt-4o-mini`.
    - **Método `extract`**: Utiliza o modelo de linguagem para extrair informações estruturadas de um conteúdo.
    - **Método `scrape_with_playwright`**: Carrega as páginas da web com `AsyncChromiumLoader`, transforma o conteúdo HTML com `BeautifulSoupTransformer`, divide os documentos com `RecursiveCharacterTextSplitter` e extrai as informações relevantes utilizando o modelo de linguagem.
3. **Execução do Scraper**:
    
    - O scraper é configurado com uma URL (neste caso, uma busca no G1 sobre "python") e uma tag HTML (`ul`) a ser extraída.
    - O conteúdo extraído é estruturado e convertido em um formato JSON.
4. **Salvamento dos Resultados**:
    
    - O conteúdo extraído é convertido para dicionários e salvo em um arquivo `output.json`.

## Função do Código:

Este código permite automatizar o processo de raspagem de dados de sites, transformando o conteúdo extraído em informações estruturadas e salvando-as em um formato fácil de ser processado posteriormente (JSON).

```python

import json  
import os  
from pprint import pprint  
from typing import List  
  
from decouple import config  
from langchain_community.document_loaders import AsyncChromiumLoader  
from langchain_community.document_transformers import BeautifulSoupTransformer  
from langchain_openai import ChatOpenAI  
from langchain_text_splitters import RecursiveCharacterTextSplitter  
from pydantic import BaseModel, Field  
  
  
class Article(BaseModel):  
    headline: str = Field(description="The title of the article")  
    description: str = Field(description="A short description of the article")  
    date: str = Field(description="The date the article was published")  
    publisher: str = Field(description="The publisher of the article")  
    link: str = Field(description="The link to the article")  
  
  
class WebScraper:  
    def __init__(self):  
        os.environ['OPENAI_API_KEY'] = config('OPENAI_API_KEY')  
        self.llm = ChatOpenAI(  
            model="gpt-4o-mini",  
            temperature=0,  
        )        self.structured_llm = self.llm.with_structured_output(Article)  
  
    def extract(self, content):  
        return self.structured_llm.invoke(content)  
  
    def scrape_with_playwright(self, urls: List[str], tags_to_extract: List[str]):  
        loader = AsyncChromiumLoader(urls)        docs = loader.load()        bs_transformed = BeautifulSoupTransformer()        docs_transformed = bs_transformed.transform_documents(            documents=docs,  
            tags_to_extract=tags_to_extract,  
        )  
        splitter = RecursiveCharacterTextSplitter.from_tiktoken_encoder(  
            chunk_size=2000,  
            chunk_overlap=0  
        )  
  
        splits = splitter.split_documents(            documents=docs_transformed  
        )  
        extracted_content = []        for split in splits:  
            extracted_content.append(                self.extract(content=split.page_content)  
            )  
        return extracted_content  
  
  
if __name__ == '__main__':  
    scraper = WebScraper()  
  
    urls = [  
        "https://g1.globo.com/busca/?q=python",  
    ]  
    tags_to_extract = ["ul"]  
  
    extracted_content = scraper.scrape_with_playwright(  
        urls=urls,  
        tags_to_extract=tags_to_extract  
    )  
  
    # Converta os objetos Article para dicionários antes de salvar no JSON  
    extracted_content_dict = [article.model_dump_json() for article in extracted_content]  
  
    pprint(extracted_content_dict)  
  
    with open("output.json", "w", encoding="utf-8") as fp:  
        json.dump(extracted_content_dict, fp, ensure_ascii=False, indent=4)
        
