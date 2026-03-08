# 🧾 Container de Transcrição — Classe iterável para snippets de transcrição

Representa uma transcrição composta por fragmentos (snippets) com metadados do vídeo. Fornece comportamento de sequência (iterable, indexing, len) e serialização para dados brutos, facilitando iteração, inspeção e armazenamento.

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
pip install dataclasses
```

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
    Represents a fetched transcript. Iterable over TranscriptSnippet objects.
    Supports indexing and len() so it behaves like a sequence of snippets.
    """
    snippets: List[TranscriptSnippet]
    video_id: str
    language: str
    language_code: str
    is_generated: bool = False

    def __iter__(self) -> Iterator[TranscriptSnippet]:
        return iter(self.snippets)

    def __getitem__(self, index) -> TranscriptSnippet:
        return self.snippets[index]

    def __len__(self) -> int:
        return len(self.snippets)

    def to_raw_data(self) -> List[Dict]:
        """
        Serialize the transcript to a list of plain dicts (one per snippet).
        Uses dataclasses.asdict so the result is JSON-serializable.
        """
        return [asdict(snippet) for snippet in self]
```

## 🧪 Exemplo de Uso

```python
# Example usage (assumes the classes above are available in the same module).
snippets = [
    TranscriptSnippet(text="Olá mundo", start=0.0, end=1.4, speaker="Locutor"),
    TranscriptSnippet(text="Exemplo de snippet.", start=1.5, end=3.0)
]

transcript = Transcript(
    snippets=snippets,
    video_id="video_abc123",
    language="Português",
    language_code="pt",
    is_generated=True
)

# Iterate over snippets
for s in transcript:
    print(f"{s.start:.2f}-{s.end:.2f} [{s.speaker or 'unknown'}]: {s.text}")

# Indexing and length
print("First snippet:", transcript[0])
print("Total snippets:", len(transcript))

# Serialize to raw data (list of dicts) — ready for JSON or DB storage
raw = transcript.to_raw_data()
import json
print(json.dumps(raw, ensure_ascii=False, indent=2))
```

## 📝 Tabela de Conceitos / Configuração

| Conceito | Descrição |
|----------|----------|
| Transcript | Container iterável de snippets com metadados do vídeo (video_id, language, language_code, is_generated). |
| TranscriptSnippet | Fragmento de transcrição com texto e timestamps (start, end) e campo opcional `speaker`. |
| to_raw_data | Retorna `List[Dict]` com os campos dos snippets (pronto para JSON/armazenamento). |
| is_generated | Booleano que indica se a transcrição foi gerada automaticamente (speech-to-text, LLM, etc.). |

## 🔗 Notas

- Renomeação: os nomes originais (por exemplo, `FetchedTranscript` / `FetchedTranscriptSnippet`) foram alterados para `Transcript` / `TranscriptSnippet` para maior clareza; o comportamento original (iterable, indexing, len, serialização) foi preservado.
- Validações recomendadas em produção: checar ordem/consistência dos timestamps, normalizar espaços e remover caracteres indesejados antes de persistir.
- Para armazenamento/transferência, usar `json.dumps(transcript.to_raw_data(), ensure_ascii=False)` ou inserir diretamente em bases/document stores que aceitem JSON.
> 🔗 Veja também: [[Transcrições — Formatos e Conversão]]

---
#python #transcricao #dataclasses
