# Documentação: Extração de Dados Estruturados com LangChain

Esta documentação detalha o funcionamento do script de extração de dados estruturados utilizando a biblioteca **LangChain** e modelos da **OpenAI**.

---

## 1. Visão Geral
O objetivo deste código é transformar um texto não estruturado (linguagem natural) em um objeto Python tipado e validado. Ele utiliza o recurso de **Structured Output** (Saída Estruturada) do GPT-4o-mini para garantir que a resposta do modelo siga exatamente um esquema pré-definido.

## 2. Requisitos e Dependências
Para executar este código, são necessárias as seguintes bibliotecas:
- `langchain-openai`: Integração com a API da OpenAI.
- `langchain-core`: Componentes base do ecossistema LangChain.
- `pydantic`: Para definição de esquemas de dados e validação.
- `typing`: Para suporte a tipos complexos do Python.

```bash
pip install langchain-openai pydantic
```

---

## 3. Código Exemplo

```python
from pydantic import BaseModel, Field
from typing import List, Optional
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
import os

# Classe para o objeto de retorno (adapte os campos aos seus pontos)
class ExtracaoTexto(BaseModel):
    pontos_principais: List[str] = Field(description="Lista de pontos principais extraídos")
    entidade_principal: str = Field(description="Entidade ou tema central do texto")
    data_ou_numero: Optional[str] = Field(default=None, description="Qualquer data, número ou valor chave")

# Configuração
os.environ["OPENAI_API_KEY"] = "sua-chave-aqui"
model = ChatOpenAI(model="gpt-4o-mini", temperature=0)
modelo_estruturado = model.with_structured_output(ExtracaoTexto)

# Chain com prompt que inclui o texto
prompt = ChatPromptTemplate.from_template(
    "Extraia os pontos principais deste texto:

{texto}

Retorne apenas os dados estruturados."
)
chain = prompt | modelo_estruturado

# Exemplo de uso com seu texto
seu_texto = """
Projeto NF-e: Extração de invoices usando LLMs. Data: 15/03/2026. Pontos: 
1. Processar XMLs brasileiros. 2. Integrar com Weaviate. 3. Border Collie precisa de treino.
"""
resultado = chain.invoke({"texto": seu_texto})
print(resultado)
```

---

## 4. Estrutura e Explicação

### A. Definição do Esquema (Pydantic)
O coração da extração é a classe `ExtracaoTexto`. Ela herda de `BaseModel` do Pydantic.
- **`Field`**: Usado para fornecer metadados ao LLM. A `description` ajuda o modelo a entender o que deve ser extraído para cada campo.
- **`Optional`**: Define campos que podem ser nulos caso a informação não esteja presente no texto.

### B. Configuração do Modelo
- **`ChatOpenAI`**: Instancia o modelo `gpt-4o-mini`. A `temperature=0` é crucial aqui para garantir respostas determinísticas e precisas.
- **`with_structured_output`**: Este método vincula o modelo à classe Pydantic. Ele força o LLM a retornar um JSON que corresponda exatamente aos campos de `ExtracaoTexto`.

### C. Prompt e Chain
O código utiliza a **LCEL (LangChain Expression Language)**:
1. **`ChatPromptTemplate`**: Define o contexto. O marcador `{texto}` é um placeholder que será preenchido na execução.
2. **O Operador Pipe (`|`)**: Une o prompt ao modelo estruturado, criando um fluxo de dados (Chain).

### D. Execução
O método `chain.invoke` recebe um dicionário onde a chave `"texto"` contém o conteúdo bruto. O resultado retornado já é uma instância da classe `ExtracaoTexto`, permitindo acesso via atributos (ex: `resultado.entidade_principal`).

---

## 5. Fluxo de Execução
1. O texto bruto é inserido no prompt.
2. O prompt é enviado para a OpenAI com a instrução de preencher o esquema JSON baseado na classe Pydantic.
3. A API da OpenAI retorna um JSON válido.
4. O LangChain converte automaticamente esse JSON em um objeto Python.

## 6. Vantagens desta Abordagem
- **Confiabilidade**: Diferente de prompts comuns, aqui você não precisa tratar strings ou fazer parse manual de JSON.
- **Tipagem**: Facilita a integração com bancos de dados ou APIs, pois os dados já chegam validados.
- **Manutenibilidade**: Para extrair novos campos, basta adicionar um novo atributo na classe `ExtracaoTexto`.

## 7. Notas de Segurança
O código utiliza `os.environ["OPENAI_API_KEY"]`. Em ambientes de produção, recomenda-se o uso de arquivos `.env` ou gerenciadores de segredos para evitar a exposição da chave de API diretamente no código-fonte.

---
#langchain #python #pydantic #openai #ia
