#WhisperX #Whisper #python #audio #openai #transcription


```python
import logging  
from dataclasses import dataclass  
from pprint import pprint  
from typing import List  
  
import torch  
import whisperx  
  
from config import HUGGINGFACE_TOKEN  
  
logger = logging.getLogger(__name__)  
  
  
@dataclass  
class Transcription:  
    text: str = ""  
    start_transcription_time: float = 0.0  
    end_transcription_time: float = 0.0  
    language: str = ""  
    speaker: str = ""  
  
  
class AudioTranscriptionProcessor:  
    def __init__(self, model_name="large-v2", batch_size=16):  
        self.device = self._get_device()  
        self.compute_type = self._get_compute_type()  
        self.batch_size = batch_size  
        self.model_name = model_name  
  
    @staticmethod  
    def _get_device():  
        return "cuda" if torch.cuda.is_available() else "cpu"  
  
    @staticmethod  
    def _get_compute_type():  
        return "float16" if torch.cuda.is_available() else "float32"  
  
    def transcribe_audio(self, audio_path, language="pt") -> List:  
        try:  
            device = self._get_device()  
            compute_type = self._get_compute_type()  
  
            # Carregar modelo de transcrição  
            model = whisperx.load_model(self.model_name, device, compute_type=compute_type, language=language)  
            audio = whisperx.load_audio(audio_path)  
            result_transcricao = model.transcribe(audio, batch_size=32, language=language)  
  
            # Carregar modelo de alinhamento  
            model_a, metadata = whisperx.load_align_model(language_code=language, device=device)  
            result_transcricao = whisperx.align(result_transcricao["segments"], model_a, metadata, audio, device)  
  
            # Melhorar diarização configurando os parâmetros adequados  
            diarize_model = whisperx.DiarizationPipeline(  
                use_auth_token=HUGGINGFACE_TOKEN,  
                device=device,  
            )            diarize_segments = diarize_model(audio)            result_segments = whisperx.assign_word_speakers(diarize_segments, result_transcricao)  
  
            merged_segments = self.__merge_segments(result_segments["segments"])  
  
            list_transcription = []  
            if not merged_segments:  
                return []  
  
            for segment in merged_segments:  
                try:  
                    list_transcription.append(Transcription(                        text=segment.get('text', ''),  
                        language=language,  
                        start_transcription_time=segment.get('start', 0),  
                        end_transcription_time=segment.get('end', 0),  
                        speaker=segment.get('speaker', '')  
                    ))                except Exception as e:  
                    logger.error(f"Erro ao processar segmento de transcrição: {e}")  
  
            return list_transcription  
  
        except Exception as e:  
            logger.info(f"Erro na transcrição: {e}")  
            return []  
  
    @staticmethod  
    def __merge_segments(segments):  
        merged_segments = []        current_segment = segments[0]  
        current_segment.pop("words", None)  
  
        for segment in segments[1:]:  
            segment.pop("words", None)  
  
            if segment['speaker'] == current_segment['speaker']:  
                if segment['start'] - current_segment['end'] < 1:  # Exemplo de filtro para pausa curta  
                    current_segment['text'] += ' ' + segment['text']  
                    current_segment['end'] = segment['end']  
                else:  
                    merged_segments.append(current_segment)                    current_segment = segment            else:  
                merged_segments.append(current_segment)                current_segment = segment  
        merged_segments.append(current_segment)        return merged_segments  
  
  
if __name__ == '__main__':  
    processor = AudioTranscriptionProcessor()  
    segments = processor.transcribe_audio(  
        audio_path="path/audio.wav"  
    )  
  
    for item in segments:  
        pprint(item)  
        print()
```

# Processador de Transcrição de Áudio com WhisperX

Este documento fornece uma explicação detalhada do código que implementa um processador de transcrição de áudio usando a biblioteca **WhisperX**. O objetivo é transcrever arquivos de áudio, realizar alinhamento temporal e diarização de falantes.

---

## Sumário

- [Introdução](#introdução)
- [Importações e Configurações Iniciais](#importações-e-configurações-iniciais)
- [Classe `Transcription`](#classe-transcription)
- [Classe `AudioTranscriptionProcessor`](#classe-audiotranscriptionprocessor)
  - [Métodos Auxiliares](#métodos-auxiliares)
  - [Método `transcribe_audio`](#método-transcribe_audio)
  - [Método `__merge_segments`](#método-__merge_segments)
- [Bloco Principal](#bloco-principal)
- [Dependências e Configurações](#dependências-e-configurações)
- [Conclusão](#conclusão)

---

## Introdução

O código implementa uma solução completa para transcrição de áudio em português, incluindo:

- **Transcrição de Áudio**: Conversão do áudio em texto.
- **Alinhamento Temporal**: Sincronização precisa entre o texto transcrito e o tempo no áudio.
- **Diarização de Falantes**: Identificação e separação de diferentes falantes no áudio.
- **Mesclagem de Segmentos**: Combinação de segmentos adjacentes para melhorar a legibilidade.

---

## Importações e Configurações Iniciais

```python
import logging
from dataclasses import dataclass
from pprint import pprint
from typing import List

import torch
import whisperx

from config import HUGGINGFACE_TOKEN

logger = logging.getLogger(__name__)
```

- **Bibliotecas Importadas**:
  - `logging`: Para registrar logs e mensagens de erro.
  - `dataclasses`: Para definir classes de dados simples.
  - `pprint`: Para impressão formatada de objetos.
  - `typing.List`: Para anotações de tipos.
  - `torch`: Para operações tensoriais e verificação de disponibilidade de GPU.
  - `whisperx`: Biblioteca que estende o Whisper com alinhamento e diarização.
  - `config`: Para importar o `HUGGINGFACE_TOKEN`.

- **Configuração do Logger**:
  - Inicializa um logger para o módulo atual.

---

## Classe `Transcription`

```python
@dataclass
class Transcription:
    text: str = ""
    start_transcription_time: float = 0.0
    end_transcription_time: float = 0.0
    language: str = ""
    speaker: str = ""
```

- **Descrição**:
  - Define uma estrutura de dados para armazenar informações de cada segmento de transcrição.
- **Atributos**:
  - `text`: Texto transcrito.
  - `start_transcription_time`: Tempo de início do segmento.
  - `end_transcription_time`: Tempo de término do segmento.
  - `language`: Idioma do segmento.
  - `speaker`: Identificador do falante.

---

## Classe `AudioTranscriptionProcessor`

### Definição da Classe

```python
class AudioTranscriptionProcessor:
    def __init__(self, model_name="large-v2", batch_size=16):
        self.device = self._get_device()
        self.compute_type = self._get_compute_type()
        self.batch_size = batch_size
        self.model_name = model_name
```

- **Descrição**:
  - Classe responsável por todo o processamento de transcrição, incluindo alinhamento e diarização.

### Métodos Auxiliares

#### `_get_device`

```python
@staticmethod
def _get_device():
    return "cuda" if torch.cuda.is_available() else "cpu"
```

- **Descrição**:
  - Determina se o processamento será feito na GPU (`cuda`) ou na CPU (`cpu`).

#### `_get_compute_type`

```python
@staticmethod
def _get_compute_type():
    return "float16" if torch.cuda.is_available() else "float32"
```

- **Descrição**:
  - Define o tipo de precisão numérica para computação.

### Método `transcribe_audio`

```python
def transcribe_audio(self, audio_path, language="pt") -> List:
    # Implementação do método
```

#### Fluxo de Processamento:

1. **Configurações Iniciais**:
   - Determina o dispositivo (`cpu` ou `cuda`) e o tipo de computação (`float32` ou `float16`).

2. **Carregamento do Modelo de Transcrição**:
   ```python
   model = whisperx.load_model(self.model_name, device, compute_type=compute_type, language=language)
   ```
   - Carrega o modelo especificado.

3. **Carregamento do Áudio**:
   ```python
   audio = whisperx.load_audio(audio_path)
   ```
   - Carrega o arquivo de áudio para processamento.

4. **Transcrição do Áudio**:
   ```python
   result_transcricao = model.transcribe(audio, batch_size=32, language=language)
   ```
   - Realiza a transcrição inicial do áudio.

5. **Alinhamento Temporal**:
   ```python
   model_a, metadata = whisperx.load_align_model(language_code=language, device=device)
   result_transcricao = whisperx.align(result_transcricao["segments"], model_a, metadata, audio, device)
   ```
   - Alinha os segmentos transcritos com o áudio original para precisão temporal.

6. **Diarização de Falantes**:
   ```python
   diarize_model = whisperx.DiarizationPipeline(use_auth_token=HUGGINGFACE_TOKEN, device=device)
   diarize_segments = diarize_model(audio)
   result_segments = whisperx.assign_word_speakers(diarize_segments, result_transcricao)
   ```
   - Identifica diferentes falantes no áudio e atribui segmentos a eles.

7. **Mesclagem de Segmentos**:
   ```python
   merged_segments = self.__merge_segments(result_segments["segments"])
   ```
   - Mescla segmentos adjacentes do mesmo falante para melhorar a legibilidade.

8. **Construção da Lista de Transcrições**:
   ```python
   list_transcription = []
   for segment in merged_segments:
       # Cria instâncias de Transcription e adiciona à lista
   ```
   - Cria uma lista de objetos `Transcription` contendo as informações finais.

9. **Tratamento de Erros**:
   - Utiliza blocos `try-except` para capturar e registrar quaisquer erros que ocorram durante o processamento.

### Método `__merge_segments`

```python
@staticmethod
def __merge_segments(segments):
    # Implementação do método
```

- **Descrição**:
  - Mescla segmentos adjacentes do mesmo falante que estão separados por uma pausa curta (menos de 1 segundo).
- **Fluxo de Trabalho**:
  - Inicializa uma lista de segmentos mesclados.
  - Itera sobre os segmentos originais e mescla conforme necessário.
  - Remove a chave `"words"` de cada segmento para simplificar os dados.

---

## Bloco Principal

```python
if __name__ == '__main__':
    processor = AudioTranscriptionProcessor()
    segments = processor.transcribe_audio(
        audio_path="/home/erickson/PycharmProjects/TranscriptionOpenaiWhisper/audios/bolsonar_vs_lula.wav"
    )

    for item in segments:
        pprint(item)
        print()
```

- **Descrição**:
  - Quando o script é executado diretamente, cria uma instância de `AudioTranscriptionProcessor` e processa um arquivo de áudio específico.
- **Funções Realizadas**:
  - Transcreve o áudio localizado em `bolsonar_vs_lula.wav`.
  - Imprime os segmentos transcritos usando `pprint`.

---

## Dependências e Configurações

- **Bibliotecas Necessárias**:
  - `torch`: Para operações com tensores e suporte a GPU.
  - `whisperx`: Extensão do Whisper com funcionalidades adicionais.
  - `config`: Deve conter a variável `HUGGINGFACE_TOKEN`.

- **Configuração do Ambiente**:
  - Certifique-se de que o PyTorch esteja instalado e configurado corretamente.
  - Uma GPU compatível com CUDA é recomendada para melhor desempenho.
  - O token `HUGGINGFACE_TOKEN` é necessário para autenticar o acesso a certos modelos.

---

## Conclusão

Este código fornece uma solução robusta para transcrição de áudio em português, integrando transcrição, alinhamento temporal e diarização de falantes. Através da combinação de várias funcionalidades da biblioteca `whisperx`, é possível obter transcrições precisas e bem estruturadas, prontas para uso em aplicações que requerem processamento de linguagem natural a partir de dados de áudio.

---

**Observação**: Para adaptar este código a outros idiomas ou modelos, ajuste os parâmetros `language` e `model_name` conforme necessário. Além disso, para melhorar o desempenho ou adequar-se a diferentes ambientes de hardware, considere modificar o `batch_size` e verificar a disponibilidade de GPU.