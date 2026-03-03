```python
# pip install -U firecrawl-py langchain_community  
  
import os  
  
os.environ["FIRECRAWL_API_KEY"] = ""  
os.environ["OPENAI_API_KEY"] = ""  
  
from firecrawl import FirecrawlApp, ScrapeOptions  
from langchain_core.documents import Document  
  
app = FirecrawlApp()  
crawl_result = app.crawl_url(  
    url="https://python.langchain.com/docs/",  
    limit=1,  
    scrape_options=ScrapeOptions(only_main_content=True)  
)  
  
docs = []  
for page in crawl_result["pages"]:  
    docs.append(Document(page["content"], metadata={"source": page["url"]}))
```