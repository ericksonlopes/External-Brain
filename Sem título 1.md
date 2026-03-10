# version: '3.8'#  
# services:#   app:#     build:#       context: .#       dockerfile: deploy/Dockerfile#     volumes:#       - ./:/app:rw#     environment:#       - PYTHONUNBUFFERED=1#     command: ["python", "main.py"]#     # Uncomment and adjust ports if the app exposes a server port#     # ports:#     #   - "8000:8000"  
services:  
  ollama:  
    image: ollama/ollama:latest  
    container_name: ollama-gpu  
    ports:  
      - "11434:11434"  
    volumes:  
      - ollama_data:/root/.ollama  
    deploy:  
      resources:  
        reservations:  
          devices:  
            - driver: nvidia  
              count: all  # Ou '1' para uma GPU específica              capabilities: [gpu]  
    environment:  
      - NVIDIA_VISIBLE_DEVICES=all  
    restart: unless-stopped  
    healthcheck:  
      test: ["CMD", "curl", "-f", "http://localhost:11434/api/tags"]  
      interval: 30s  
      timeout: 10s  
      retries: 3  
  
volumes:  
  ollama_data:
  
  
  from langchain_core.output_parsers import StrOutputParser  
from langchain_core.prompts import ChatPromptTemplate  
from langchain_ollama import ChatOllama  
  
# docker exec -it ollama-gpu ollama pull llama3.2  
# llama3.2:latest  
  
  
llm = ChatOllama(model="llama3.2", base_url="http://localhost:11434")  
prompt = ChatPromptTemplate.from_template("Pergunta: {input}\nResponda em PT:")  
chain = prompt | llm | StrOutputParser()  
  
print(chain.invoke({"input": """  
gere uma documentação explicativa sobre este código  
  
  
from typing import Optional    
    
from pydantic import BaseModel, Field, ConfigDict, model_validator  
  
class PdfEntity(BaseModel):    
    model_config = ConfigDict(populate_by_name=True)    
    
    id: str    
    grade: Optional[str] = Field(default=None, alias="grade")    
    
    @model_validator(mode="before")    
    def handle_grade_aliases(self, data):    
        if 'grade' not in data and 'bookGrade' in data:    
            data['grade'] = data['bookGrade']    
        return data  
"""}))