
O `evolutionapi` é uma biblioteca (SDK) em Python projetada para facilitar a integração com a **Evolution API**, uma solução open-source robusta para automação de mensagens via WhatsApp.

---

## 🛠️ Instalação

Para começar, instale a biblioteca via `pip`:

```bash
pip install evolutionapi
```

---

## 🚀 Como Usar (Exemplo Básico)

O arquivo demonstra o envio de uma mensagem de texto simples. Abaixo está o código explicado:

```python
from evolutionapi.client import EvolutionClient  
from evolutionapi.models.message import TextMessage

# 1. Configuração de Destino
number = "5511999999999"  # Número com DDI e DDD
conversation = "Olá! Esta é uma mensagem via Evolution API."

# 2. Inicialização do Cliente
# Substitua 'api_token' pelo seu Global API Token (definido no .env da Evolution API)
client = EvolutionClient(  
	base_url='http://localhost:8080/',  
	api_token='seu_global_token_aqui'  
)  

# 3. Preparação da Mensagem
message = TextMessage(  
	number=number,  
	text=conversation,  
)  

# 4. Dados da Instância
instance_id = "MinhaInstancia"  # Nome da instância criada no painel/API
instance_token = "token_da_instancia"  # Token específico da instância

# 5. Envio
response = client.messages.send_text(instance_id, message, instance_token)  
print(response)
```

---

## 🔍 Componentes Principais

### 1. `EvolutionClient`
É o ponto de entrada da biblioteca. Ele requer a `base_url` (onde sua Evolution API está rodando) e o `api_token` global para autenticação administrativa.

### 2. `TextMessage`
Um modelo (Model) que estrutura os dados da mensagem. Além de `number` e `text`, ele pode suportar parâmetros adicionais dependendo da versão da biblioteca.

### 3. `client.messages.send_text`
O método que efetivamente realiza a requisição POST para o endpoint da API. Ele vincula a mensagem a uma instância específica através do `instance_id`.

---

## 📝 Requisitos Prévios

1.  **Evolution API Rodando**: Você deve ter uma instância da Evolution API ativa (geralmente via Docker).
2.  **Instância Conectada**: A instância (`instance_id`) deve estar com o status `open` (QR Code escaneado).
3.  **Tokens**: 
    *   **Global Token**: Usado para gerenciar a API como um todo.
    *   **Instance Token**: Usado para operações específicas daquela instância/número.

---

## 💡 Dicas e Soluções

*   **Formato do Número**: Sempre inclua o DDI (Ex: 55 para Brasil) e o DDD sem o zero.
*   **Media Messages**: A biblioteca também suporta `MediaMessage` para envio de imagens, PDFs e áudios.
*   **Logs**: Caso receba um erro, verifique se a `base_url` termina com `/` e se a porta (8080 por padrão) está aberta.

---
#python #evolution-api #whatsapp #automacao
