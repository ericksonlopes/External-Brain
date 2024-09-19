from openai.lib.azure import AzureOpenAI  
  
client = AzureOpenAI(  
    azure_endpoint="x",  
    api_key="x",  
    api_version="x"  
)  
  
message_text = [{"role": "system", "content": "You are an AI assistant that helps people find information."},  
                {"role": "user", "content": "I need help finding information about the best way to train a dog."}]  
  
completion = client.chat.completions.create(  
    model="gpt-35-turbo",  
    messages=message_text,  
    max_tokens=1000,  
    n=1,  
    stop=None,  
    temperature=0.05,  
    frequency_penalty=0.99,  
    presence_penalty=0.1  
)  
  
print(completion.choices[0].message.content)