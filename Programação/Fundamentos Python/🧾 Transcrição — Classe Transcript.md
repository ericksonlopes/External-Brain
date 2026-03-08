# 🧾 Container de Transcrição — Classe iterável com foco em Iterator

Representa uma transcrição composta por fragmentos (snippets) com metadados do vídeo. Este documento destaca como usar a classe como um Iterable/Iterator em Python (for, iter(), next(), itertools), mostrando padrões práticos e alternativas para consumo em pipeline.

```
┌───────────────┐
│ Transcript    │
│ (video_id, ...)│
└──────┬────────┘
       │ contains
       ▼
┌────────────────────────┐
│ TranscriptSnippet      │
│ (text, start, end, ...)│
└────────────────────────┘
```

## 📦 Dependências

```bash
# Nenhuma dependência externa; usa a biblioteca padrão (Python 3.7+).
# Opcional (Python < 3.7):
# pip install dataclasses
```

## 🔁 Foco: Uso do Iterator

- A classe Transcript implementa `__iter__` retornando um `Iterator[TranscriptSnippet]`. Isso permite usar `for s in transcript`, `iter(transcript)` e `next(...)`.
- `__getitem__` e `__len__` delegam para a lista interna (`self.snippets`), o que também habilita comportamento de sequência: indexing, `reversed(transcript)` e suporte a slices (ex.: `transcript[0:3]` retorna uma lista de snippets).
- Para processamento em pipeline (map/filter/comprehensions) basta passar o objeto `transcript` onde se espera um Iterable.
- Observação: a implementação atual usa um iterator da lista interna (não é "lazy" em origem), se for necessário consumir de uma fonte streaming, veja a sugestão de `__iter__` lazy abaixo.

## ⚙️ Implementação

```python
from dataclasses import dataclass, asdict
from typing import List, Iterator, Dict, Optional

@dataclass
class TranscriptSnippet:
    """
    A single transcript snippet: contiguous text with start/end timestamps.
    """
    text: str
    start: float  # start time in seconds
    end: float    # end time in seconds
    speaker: Optional[str] = None

@dataclass
class Transcript:
    """
    Container de snippets. Implementa protocolo de iteração e sequencia.
    """
    snippets: List[TranscriptSnippet]
    video_id: str
    language: str
    language_code: str
    is_generated: bool = False

    def __iter__(self) -> Iterator[TranscriptSnippet]:
        """
        Retorna um iterator sobre `self.snippets`.

        - Uso: `it = iter(transcript)` produz um iterator que pode ser consumido com `next(it)`.
        - Se desejar comportamento "lazy/streaming" (ex.: fetch por pedaços), substituir por um generator que `yield` snippets.
        """
        return iter(self.snippets)

    def __getitem__(self, index):
        """
        Suporta index e slices porque delega para a lista interna.
        - `transcript[0]` -> TranscriptSnippet
        - `transcript[0:3]` -> List[TranscriptSnippet]
        """
        return self.snippets[index]

    def __len__(self) -> int:
        return len(self.snippets)

    def to_raw_data(self) -> List[Dict]:
        """
        Serializa para lista de dicts (pronto para json / persistência).
        """
        return [asdict(snippet) for snippet in self]

    # Sugestão (opcional) para __iter__ lazy, quando os snippets são obtidos sob demanda:
    # def __iter__(self) -> Iterator[TranscriptSnippet]:
    #     for piece in fetch_snippets_from_source(self.video_id):
    #         yield piece
```

## 🧪 Exemplos práticos centrados no Iterator

```python
from itertools import islice

# setup (mesmo do exemplo anterior)
transcript = Transcript(snippets=[
    TranscriptSnippet(text="Olá mundo", start=0.0, end=1.4, speaker="Locutor"),
    TranscriptSnippet(text="Exemplo de snippet.", start=1.5, end=3.0)
], video_id="v1", language="Português", language_code="pt")

# 1) Loop simples (mais comum)
for s in transcript:
    print(s.text)

# 2) Iterator manual com next()
it = iter(transcript)
first = next(it, None)        # obtém o primeiro snippet ou None
second = next(it, None)       # próximo

# 3) Consumir até uma condição (StopIteration)
it = iter(transcript)
try:
    while True:
        s = next(it)
        if "erro" in s.text:
            break
except StopIteration:
    pass

# 4) Trabalhando com slices e islice
print(transcript[0])          # primeiro snippet
print(transcript[0:2])       # lista de snippets
for s in islice(transcript, 0, 1):
    print("islice:", s.text)

# 5) Pipeline / filtros (comprehension ou generator)
short_texts = [s for s in transcript if len(s.text) < 50]
long_gen = (s for s in transcript if len(s.text) >= 50)

# 6) reversed, enumerate
for i, s in enumerate(transcript, 1):
    print(i, s.start)
for s in reversed(transcript):
    print("reversed", s.text)

# 7) Pegar o primeiro elemento com segurança
first = next(iter(transcript), None)
```

## 📝 Tabela de Conceitos / Configuração

| Conceito | Descrição |
|----------|----------|
| Transcript | Container iterável de snippets com metadados do vídeo (video_id, language, language_code, is_generated). É um Iterable. |
| TranscriptSnippet | Fragmento de transcrição com texto e timestamps (start, end) e campo opcional `speaker`. |
| Iterator vs Iterable | `Transcript` é um Iterable (implementa `__iter__`); `iter(transcript)` retorna um Iterator que pode ser consumido com `next()`.
| to_raw_data | Retorna `List[Dict]` com os campos dos snippets (pronto para JSON/armazenamento). |

## 🔗 Notas

- A implementação atual usa a lista interna como fonte de truth; para grandes volumes, prefira uma versão lazy para reduzir uso de memória.
- `reversed(transcript)` funciona porque `__len__` + `__getitem__` estão presentes; slices retornam listas, não um novo Transcript.
- Ao alterar `__iter__` para um generator, documente a mudança pois operações que dependem de indexação/len podem não funcionar da mesma forma.

---
#python #transcricao #dataclasses #iterable
