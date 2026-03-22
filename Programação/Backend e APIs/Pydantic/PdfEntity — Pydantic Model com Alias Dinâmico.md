# 🔧 PdfEntity — Pydantic Model com Alias Dinâmico

Modelo Pydantic para representação de entidades de PDF, utilizando model_validator para gerenciar aliases dinâmicos (ex: mapear ookGrade para grade).

## 📦 Dependências

`ash
pip install pydantic
`

## ⚙️ Implementação

`python
from typing import Optional
from pydantic import BaseModel, Field, ConfigDict, model_validator

class PdfEntity(BaseModel):
    model_config = ConfigDict(populate_by_name=True)

    id: str
    grade: Optional[str] = Field(default=None, alias="grade")

    @model_validator(mode="before")
    def handle_grade_aliases(cls, data):
        # Maps legacy or alternative field names to the canonical 'grade' field
        if isinstance(data, dict):
            if 'grade' not in data and 'bookGrade' in data:
                data['grade'] = data['bookGrade']
        return data
`

## 🧪 Exemplo de Uso

`python
# Suporta tanto o nome original quanto o alias/mapeamento manual
data_1 = {"id": "123", "grade": "A"}
data_2 = {"id": "456", "bookGrade": "B"}

obj1 = PdfEntity(**data_1)
obj2 = PdfEntity(**data_2)

print(obj1.grade) # A
print(obj2.grade) # B
`

## 📝 Tabela de Conceitos

| Conceito | Descrição |
|----------|-----------|
| populate_by_name | Permite instanciar o modelo usando tanto o nome do campo quanto seu alias. |
| model_validator | Validador que roda antes da inicialização para normalizar os dados de entrada. |

## 📌 Notas

- Útil para lidar com APIs instáveis ou legadas que mudam nomes de campos.
- O uso de ConfigDict moderniza a configuração em relação ao antigo class Config.

---
#python #pydantic #backend #data-validation
