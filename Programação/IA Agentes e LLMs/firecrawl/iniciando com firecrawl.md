# 🔥 Iniciando com Firecrawl (Scraping para LLMs)

O **Firecrawl** é um serviço de scraping projetado especificamente para agentes de IA. Ele não apenas "baixa" o HTML, mas limpa e converte o conteúdo do site em **Markdown** estruturado, ideal para ser processado por LLMs.

## 🛠️ Instalação e API Key

```bash
pip install -U firecrawl-py langchain_community
```

Você precisará de uma API Key do [Firecrawl](https://firecrawl.dev).

## 📄 Exemplo: Crawl + Integração LangChain

Este exemplo mostra como varrer uma URL e transformar as páginas encontradas em objetos `Document` do LangChain para uso em RAG (Busca Semântica).

```python
import os
from firecrawl import FirecrawlApp, ScrapeOptions
from langchain_core.documents import Document

# Configuração
os.environ["FIRECRAWL_API_KEY"] = "sua_chave_aqui"

app = FirecrawlApp()

# Crawl: Percorre links internos do site
crawl_result = app.crawl_url(
    url="https://docs.exemplo.com",
    limit=5, # Limite de páginas para economizar créditos
    scrape_options=ScrapeOptions(only_main_content=True) # Remove menus e rodapés
)

# Transformação para LangChain
docs = []
for page in crawl_result["pages"]:
    docs.append(Document(
        page_content=page["content"], # Conteúdo já em Markdown!
        metadata={"source": page["url"]}
    ))
```

## 🎯 Por que usar Firecrawl em vez de BeautifulSoup?

1.  **Markdown Nativo**: LLMs entendem melhor Markdown do que HTML puro.
2.  **Limpeza Automática**: Remove ruídos como anúncios, cookies e menus.
3.  **Crawler Integrado**: Ele descobre e percorre links internos automaticamente.

---
#ai #scraping #firecrawl #langchain #llm #python
