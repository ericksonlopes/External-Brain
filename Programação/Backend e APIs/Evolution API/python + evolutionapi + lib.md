#python #evolution-api 

pip install evolutionapi

```python
from evolutionapi.client import EvolutionClient  
from evolutionapi.models.message import TextMessage

number = ""  
conversation = ""

client = EvolutionClient(  
	base_url='http://localhost:8080/',  
	api_token=''  
)  

message = TextMessage(  
	number=number,  
	text=conversation,  
)  

instance_id = ""  
instance_token = ""  

response = client.messages.send_text(instance_id, message, instance_token)  
print(response)
```