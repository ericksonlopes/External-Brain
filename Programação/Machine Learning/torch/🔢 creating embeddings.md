#embedding #torch #sentence_transformers

```python
import numpy as np  
import torch  
  
from sentence_transformers import SentenceTransformer  
  
device = "cuda" if torch.cuda.is_available() else "cpu"  
model_instance = SentenceTransformer("intfloat/multilingual-e5-small", device=device)  
  
text = "Hello world! This is a test sentence to generate an embedding."  
embedding: np.ndarray = model_instance.encode(text, convert_to_numpy=True).astype(np.float32)  
  
print(embedding.shape)  
# (384,)  
  
print(type(embedding))  
print(f"Embedding for the input text: {embedding[:5]}...")  # Print the first 5 dimensions of the embedding
```