# 🎙️ Voice Recognition POC — Transcrição, Diarização e Identificação

POC (Prova de Conceito) para transcrição automática de vídeos do YouTube com separação de falantes (**Diarização**) e reconhecimento de vozes através de comparação de assinaturas (embeddings). Utiliza o pipeline state-of-the-art para processamento de áudio local.

## 🚀 Fluxo de Funcionamento

```
┌─────────────┐     ┌──────────────┐     ┌──────────────────┐
│ YouTube URL │────▶│ Audio Extract│────▶│ Faster-Whisper   │───┐
└─────────────┘     └──────────────┘     │ (Transcrição)    │   │
                                         └──────────────────┘   │
                                                                │   ┌──────────────┐
                                         ┌──────────────────┐   ├──▶│ Consolidação │
                                         │ Pyannote Audio   │───┤   │ Final        │
                                         │ (Diarização)     │   │   └──────────────┘
                                         └──────────────────┘   │
                                                                │
                                         ┌──────────────────┐   │
                                         │ Resemblyzer      │───┘
                                         │ (Speaker ID)     │
                                         └──────────────────┘
```

## 📦 Dependências

```bash
pip install faster-whisper pyannote.audio resemblyzer yt-dlp imageio-ffmpeg torch numpy
```
*Nota: Requer FFmpeg instalado no sistema ou via `imageio-ffmpeg`.*

## ⚙️ Implementação

Abaixo, a versão otimizada e modularizada do pipeline de processamento.

```python
import os
import torch
import yt_dlp
import numpy as np
from faster_whisper import WhisperModel
from pyannote.audio import Pipeline
from resemblyzer import VoiceEncoder, preprocess_wav
from pathlib import Path

class VoiceRecognitionPOC:
    def __init__(self, hf_token: str, model_size: str = "medium"):
        self.hf_token = hf_token
        self.device = "cuda" if torch.cuda.is_available() else "cpu"
        self.compute_type = "float16" if self.device == "cuda" else "int8"
        
        # Load Models
        self.whisper = WhisperModel(model_size, device=self.device, compute_type=self.compute_type)
        self.diarizer = Pipeline.from_pretrained("pyannote/speaker-diarization-3.1", use_auth_token=hf_token)
        self.encoder = VoiceEncoder()

    def process(self, video_url: str, profiles_dir: str = "perfis"):
        # 1. Download
        audio_path = self._download_audio(video_url)
        
        # 2. Transcribe (Word-level timestamps)
        segments, _ = self.whisper.transcribe(audio_path, beam_size=5, word_timestamps=True)
        
        # 3. Diarize (Who speaks when)
        diarization = self.diarizer(audio_path)
        
        # 4. Speaker Matching (Identify who is who)
        speaker_map = self._map_speakers(audio_path, diarization, profiles_dir)
        
        return segments, diarization, speaker_map

    def _download_audio(self, url: str) -> str:
        ydl_opts = {
            'format': 'bestaudio/best',
            'postprocessors': [{'key': 'FFmpegExtractAudio', 'preferredcodec': 'wav'}],
            'outtmpl': 'downloads/%(title)s.%(ext)s',
            'quiet': True
        }
        with yt_dlp.YoutubeDL(ydl_opts) as ydl:
            info = ydl.extract_info(url, download=True)
            return os.path.splitext(ydl.prepare_filename(info))[0] + ".wav"

    def _map_speakers(self, audio_path, diarization, profiles_dir):
        # Lógica para carregar .npy e comparar usando similaridade de cosseno
        # (Veja o código fonte completo para detalhes de implementação)
        pass
```

## 🧪 Exemplo de Uso

```python
HF_TOKEN = "seu_token_aqui"
video_url = "https://www.youtube.com/watch?v=VfJQdFIc0-M"

poc = VoiceRecognitionPOC(hf_token=HF_TOKEN)
poc.process(video_url)
```

## 📝 Tabela de Conceitos

| Conceito | Descrição |
|----------|-----------|
| **Diarização** | Processo de particionar o áudio em segmentos de acordo com a identidade do falante. |
| **Embeddings** | Vetores numéricos que representam as características únicas de uma voz. |
| **Threshold** | Limiar de similaridade (ex: 0.86) para considerar que duas vozes pertencem à mesma pessoa. |
| **Faster-Whisper** | Implementação otimizada do modelo Whisper da OpenAI, até 4x mais rápida. |

## 🔗 Notas

- **HuggingFace Token**: Necessário para usar os modelos da Pyannote. É preciso aceitar os termos de uso no site do HF.
- **Perfis de Voz**: O sistema busca arquivos `.npy` na pasta `perfis/`. Cada arquivo deve conter o centroid embedding do falante.
- **GPU (CUDA)**: Altamente recomendado. O processamento em CPU de vídeos longos pode ser muito lento.

> 🔗 Veja também: [[🎬 YouTranscripta]] — Versão focada em RAG e Perguntas/Respostas.

---
#python #ia #audio #whisper #pyannote #resemblyzer
