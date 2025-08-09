# Construindo um RAG Simples com AGNO e OpenAI

## Integração de conhecimento em PDFs com um agente de IA utilizando AGNO

# Introdução

No mundo da inteligência artificial, a capacidade de recuperar informações de maneira eficaz é crucial. Neste artigo, vamos explorar como construir um sistema de Recuperação-Augmentada de Geração (RAG) simples utilizando a biblioteca AGNO, a API da OpenAI e um banco de dados vetorial LanceDB. Vamos focar na extração de informações a partir de documentos PDF, permitindo que um agente de IA responda a perguntas baseadas no conteúdo desses documentos.

# O que é RAG?

Recuperação-Augmentada de Geração (RAG) é um modelo que combina técnicas de recuperação de informações e geração de texto. Ele permite que um agente de IA busque dados relevantes em fontes externas e os utilize para gerar respostas mais precisas e informativas. No nosso caso, utilizaremos um PDF como fonte de conhecimento.

# Pré-requisitos

Antes de começarmos, precisamos instalar algumas bibliotecas essenciais. Execute o seguinte comando para instalar as dependências necessárias:

```bash
pip install agno openai lancedb pandas pypdf
```

# Configurando o Ambiente

Primeiro, vamos importar as bibliotecas necessárias e configurar a chave da API da OpenAI:

```python
import os

from agno.agent import Agent
from agno.embedder.openai import OpenAIEmbedder
from agno.knowledge.pdf import PDFKnowledgeBase
from agno.models.openai import OpenAIChat
from agno.vectordb.lancedb import LanceDb

os.environ["OPENAI_API_KEY"] = "sua chave_api_openai"
```

Certifique-se de substituir `sua chave_api_openai` pela sua chave real da API da OpenAI.

# Criando a Base de Conhecimento

Agora, vamos criar uma base de conhecimento a partir de um documento PDF. Para isso, utilizaremos a classe `PDFKnowledgeBase` e configuraremos um banco de dados vetorial com o LanceDB:

```python
knowledge_base = PDFKnowledgeBase(
    path="sample_data/livro_da_minha_vida.pdf",
    vector_db=LanceDb(
        table_name="content",
        uri="tmp/lancedb",
        embedder=OpenAIEmbedder(id="text-embedding-3-small")
    ),
)

# Carrega o conteúdo do PDF na base de conhecimento
knowledge_base.load()
```

Neste exemplo, o caminho para o PDF é `sample_data/livro_da_minha_vida.pdf`. Você pode substituir isso pelo caminho do seu próprio arquivo PDF.

# Criando o Agente

Com a base de conhecimento carregada, agora podemos criar um agente que utilizará este conhecimento para responder perguntas:

```python
agent = Agent(
    model=OpenAIChat(id="gpt-4o"),
    knowledge=knowledge_base,
    add_references=True,
    search_knowledge=False,
    markdown=True
)
```

Aqui, estamos utilizando o modelo GPT-4 da OpenAI e configurando o agente para adicionar referências e usar a formatação Markdown nas respostas.

# Fazendo Perguntas ao Agente

Agora que tudo está configurado, podemos fazer perguntas ao nosso agente. Vamos perguntar sobre o dia do nosso nascimento:

```python
agent.print_response(
    "Qual é o dia do meu nascimento?", stream=True
)
```

Esta linha de código enviará a pergunta ao agente, que utilizará o conhecimento extraído do PDF para gerar uma resposta.

# Boas Práticas

1. **Gerenciamento de Erros**: Sempre implemente tratamentos de exceção para lidar com possíveis erros ao carregar documentos ou fazer chamadas à API.
2. **Documentação**: Mantenha uma documentação clara do seu código e das bibliotecas utilizadas.
3. **Segurança**: Nunca exponha sua chave API. Utilize variáveis de ambiente ou arquivos de configuração seguros.

# Conclusão

Neste artigo, construímos um sistema simples de RAG utilizando a biblioteca AGNO e a API da OpenAI. Essa abordagem permite que você crie agentes mais inteligentes e informativos, capazes de responder a perguntas baseadas em documentos PDF.

Experimente adaptá-lo para suas próprias necessidades e explore as possibilidades que a integração de IA pode oferecer!


## Exemplos de Código

### Exemplo 1
```python
pip install agno openai lancedb pandas pypdf
```

```python
import os

from agno.agent import Agent
from agno.embedder.openai import OpenAIEmbedder
from agno.knowledge.pdf import PDFKnowledgeBase
from agno.models.openai import OpenAIChat
from agno.vectordb.lancedb import LanceDb

os.environ["OPENAI_API_KEY"] = "sua chave_api_openai"

knowledge_base = PDFKnowledgeBase(
    path="sample_data/livro_da_minha_vida.pdf",
    vector_db=LanceDb(
        table_name="content",
        uri="tmp/lancedb",
        embedder=OpenAIEmbedder(id="text-embedding-3-small")
    ),
)

knowledge_base.load()

agent = Agent(
    model=OpenAIChat(id="gpt-4o"),
    knowledge=knowledge_base,
    add_references=True,
    search_knowledge=False,
    markdown=True
)

agent.print_response(
    "Qual é o dia do meu nascimento?", stream=True
)
```


---
*Este artigo foi escrito para ajudar desenvolvedores a entenderem como integrar diferentes tecnologias para construir agentes de IA informativos.*