```python
import torch  
  
from sentence_transformers import SentenceTransformer  
  
device = "cuda" if torch.cuda.is_available() else "cpu"  
model_instance = SentenceTransformer("intfloat/multilingual-e5-small", device=device)  
  
text = "Olá, como vai você?"  
embedding = model_instance.encode(text, convert_to_numpy=True)
```