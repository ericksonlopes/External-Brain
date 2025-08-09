#agno #IA #agents #agent #openai 
# Criando um Agente Simples com AGNO e OpenAI

## Um guia prático para implementar um agente conversacional em Python

# Introdução

Neste artigo, vamos explorar como criar um agente simples utilizando a biblioteca AGNO em conjunto com o modelo OpenAI. Este agente será capaz de responder a perguntas de maneira envolvente e divertida, aproveitando a inteligência artificial para gerar conteúdo dinâmico.

## O que é AGNO?

AGNO é uma biblioteca que permite a construção de agentes conversacionais de forma fácil e intuitiva. Com AGNO, você pode configurar diferentes modelos de linguagem, como o OpenAI, para gerar respostas em tempo real, facilitando o desenvolvimento de chatbots e assistentes virtuais.

## Pré-requisitos

Antes de começarmos, você precisará ter o Python instalado em seu ambiente. Além disso, vamos instalar as bibliotecas necessárias. Abra seu terminal e execute o seguinte comando:

```bash
pip install agno openai
```

## Configuração do Ambiente

Para que o AGNO funcione corretamente com o OpenAI, você precisará de uma chave de API do OpenAI. Após obter sua chave, podemos começar a configurar o nosso agente. Aqui está um exemplo básico de como fazer isso:

### Exemplo de Código

```python
import os
from textwrap import dedent
from agno.agent import Agent
from agno.models.openai import OpenAIChat

# Configurando a chave da API do OpenAI
os.environ["OPENAI_API_KEY"] = "sua chave_api_openai"

# Criando o agente
agent = Agent(
    model=OpenAIChat(id="gpt-4o"),
    instructions=dedent("""
        Você é um programador Python experiente com um toque de humor! 🐍
        Pense em você como um mestre da codificação que adora compartilhar conhecimento de forma divertida e
        envolvente.
    """),
    markdown=True,
)

# Executando o agente
response = agent.run("Explique resumidamente o que é python").content
print(response)
```

### Explicação do Código

1. **Importando Bibliotecas**: Começamos importando as bibliotecas necessárias. `os` é utilizado para configurar variáveis de ambiente, enquanto `dedent` ajuda a formatar as instruções que passaremos ao agente.

2. **Configurando Variáveis de Ambiente**: Usamos `os.environ` para definir a chave da API do OpenAI, que é necessária para autenticar nossas solicitações ao modelo.

3. **Criando o Agente**: Instanciamos o agente passando o modelo que queremos usar (`OpenAIChat`) e as instruções que definem o comportamento do nosso agente. Neste caso, pedimos que ele tenha um toque de humor ao responder questões sobre programação.

4. **Executando o Agente**: Por fim, chamamos o método `run` do agente, passando uma pergunta e imprimindo a resposta gerada.

## Boas Práticas

- **Mantenha suas chaves de API seguras**: Nunca compartilhe sua chave de API publicamente. Considere usar variáveis de ambiente para gerenciá-las de forma segura.
- **Teste suas instruções**: Ao definir as instruções do seu agente, teste diferentes abordagens para ver como isso impacta as respostas geradas.
- **Monitore o uso da API**: O uso da API do OpenAI pode incorrer em custos. Monitore seu uso para evitar surpresas na fatura.

## Conclusão

Neste artigo, mostramos como criar um agente simples com a biblioteca AGNO e o modelo OpenAI. Esta configuração permite que você desenvolva interações dinâmicas e personalizadas em seus aplicativos. Sinta-se à vontade para expandir este exemplo e explorar mais sobre as capacidades do AGNO e do OpenAI!

## Referências
- [Documentação do AGNO](https://github.com/agno/agno)
- [OpenAI API Documentation](https://beta.openai.com/docs/)

## Dicas Finais

Experimente fazer perguntas diferentes e veja como o agente responde. Isso pode ajudá-lo a entender melhor como ajustar as instruções e melhorar a interação com os usuários.

## Exemplos de Código


```bash
pip install agno openai
```

```python
import os
from textwrap import dedent
from agno.agent import Agent
from agno.models.openai import OpenAIChat

os.environ["OPENAI_API_KEY"] = "sua chave_api_openai"

agent = Agent(
    model=OpenAIChat(id="gpt-4o"),
    instructions=dedent("""
        Você é um programador Python experiente com um toque de humor! 🐍
        Pense em você como um mestre da codificação que adora compartilhar conhecimento de forma divertida e
        envolvente.
    """),
    markdown=True,
)

response = agent.run("Explique resumidamente o que é python").content
print(response)
```


## Referências

- https://github.com/agno/agno
- https://beta.openai.com/docs/

---
*Sinta-se à vontade para compartilhar suas experiências ao criar agentes com AGNO!*

---
#Python #OpenAI #AGNO #Chatbots #Inteligência Artificial