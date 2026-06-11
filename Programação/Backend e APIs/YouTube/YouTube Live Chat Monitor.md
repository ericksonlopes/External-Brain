# 📺 YouTube Live Chat — Monitoramento em Tempo Real

Script para captura e processamento de mensagens de chat ao vivo do YouTube em tempo real, utilizando a biblioteca `pytchat`. O código diferencia mensagens de texto comuns de **Super Chats** (doações).

## 📦 Dependências

```bash
pip install pytchat
```

## ⚙️ Implementação

```python
import pytchat

def monitor_live_chat(video_id: str):
    """
    Connects to a YouTube Live stream and prints chat messages.
    """
    chat = pytchat.create(video_id=video_id)

    while chat.is_alive():
        try:
            for c in chat.get().sync_items():
                # Super Chat processing
                if c.type == "superChat":
                    print(f"{c.author.name}: {c.message} - Valor: {c.amountString}")
                
                # Standard message processing
                elif c.type == "textMessage":
                    print(f"{c.author.name}: {c.message}")
                
                # Additional types (e.g., membership) can be handled here
                
            # Internal tick for maintenance
            chat.get().tick()
            
        except Exception as e:
            print(f"Erro ao capturar chat: {e}")
            chat.terminate()
            break
```

## 🧪 Exemplo de Uso

```python
# Substitua pelo ID de uma live ativa (ex: 'https://www.youtube.com/watch?v=VIDEO_ID')
VIDEO_ID = "dQw4w9WgXcQ" 

if __name__ == "__main__":
    print(f"Iniciando monitoramento da live: {VIDEO_ID}")
    monitor_live_chat(VIDEO_ID)
```

## 📝 Tabela de Conceitos

| Conceito | Descrição |
|----------|-----------|
| `video_id` | O identificador único da live no URL do YouTube. |
| `superChat` | Tipo de mensagem que contém uma doação monetária. |
| `amountString` | Valor formatado da doação (ex: "R$ 10,00"). |
| `tick()` | Método que mantém a sincronização e o status da conexão. |

## 🔗 Notas

- O método `chat.is_alive()` verifica se o stream ainda está ativo ou se a conexão não caiu.
- Para capturar IDs de vídeos dinamicamente, pode-se usar a `YouTube Data API v3`.
- **Limitação**: O `pytchat` não requer API Key, mas pode sofrer rate limit se muitas conexões forem abertas simultaneamente.

---
#python #youtube #backend #streaming #api
