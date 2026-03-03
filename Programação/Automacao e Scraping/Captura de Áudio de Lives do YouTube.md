
Este script Python permite extrair o fluxo de áudio de uma transmissão ao vivo (ou vídeo) do YouTube em tempo real, salvando-o localmente em formato `.wav`.

## 🛠️ Pré-requisitos

Para utilizar este script, você precisará ter instalado em sua máquina:

1.  **Python 3.x**
2.  **FFmpeg**: Ferramenta essencial para processamento de áudio e vídeo.
3.  **yt-dlp**: Uma biblioteca robusta para extrair informações e links de streaming do YouTube.

### Instalação das dependências Python:

```bash
pip install yt-dlp
```

## ⚙️ Como Funciona

O script opera em duas etapas principais:

1.  **Extração do Stream**: Utiliza o `yt-dlp` para obter a URL direta do fluxo de áudio com a melhor qualidade disponível (`bestaudio`), sem baixar o arquivo completo.
2.  **Captura com FFmpeg**: O `ffmpeg` se conecta a essa URL de stream e grava o áudio.
    *   `-t 60`: Define a duração da captura (neste caso, 60 segundos).
    *   `-acodec pcm_s16le`: Define o codec para áudio sem compressão (WAV).
    *   `-ar 44100`: Define a taxa de amostragem de 44.1kHz.

## 📄 O Código

```python
import subprocess
import yt_dlp as youtube_dl

def capturar_audio_live(youtube_url, output_file):
    # Configuração do yt-dlp para obter a URL do stream
    ydl_opts = {'format': 'bestaudio'}
    
    with youtube_dl.YoutubeDL(ydl_opts) as ydl:
        info = ydl.extract_info(youtube_url, download=False)
        stream_url = info['url']

    # Comando ffmpeg para processar o stream e salvar em disco
    command = [
        'ffmpeg',
        '-i', stream_url,    # URL de entrada do stream
        '-t', '60',           # Duração da captura (segundos)
        '-vn',                # Ignorar vídeo
        '-acodec', 'pcm_s16le',
        '-ar', '44100',
        output_file
    ]

    # Executa o processo via shell
    subprocess.run(command)

if __name__ == "__main__":
    url = 'https://www.youtube.com/watch?v=3e_pbrjXdjU'
    arquivo_saida = 'audio_capturado.wav'

    print(f"Iniciando captura de: {url}")
    capturar_audio_live(url, arquivo_saida)
    print(f"Captura finalizada: {arquivo_saida}")
```

## 🚀 Como Usar

1.  Certifique-se de que o `ffmpeg` está no seu PATH do sistema.
2.  Altere a variável `youtube_url` no final do script para o link desejado.
3.  Execute o script:
    ```bash
    python "Captação audio.py"
    ```

## 📝 Notas de Customização

*   **Duração**: Para capturar por mais tempo, altere o valor após `-t` no comando `ffmpeg`.
*   **Formato**: Para salvar em MP3, mude a extensão do arquivo para `.mp3` e ajuste o codec para `libmp3lame`.

---
#python #audio #youtube #ffmpeg #tutorial
