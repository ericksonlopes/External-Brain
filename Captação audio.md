#python #audio #youtube

```python
import subprocess  
  
import yt_dlp as youtube_dl  
  
  
def capturar_audio_live(youtube_url, output_file):  
    # Usando youtube_dl para obter informações sobre a melhor qualidade de áudio disponível  
    with youtube_dl.YoutubeDL({'format': 'bestaudio'}) as ydl:  
        info = ydl.extract_info(youtube_url, download=False)  
        stream_url = info['url']  
  
    # Comando ffmpeg para capturar áudio da live no YouTube  
    command = [  
        'ffmpeg',  
        '-i', stream_url,  
        '-t', '60',  
        '-vn',  
        '-acodec', 'pcm_s16le',  
        '-ar', '44100',  
        output_file  
    ]  
  
    # Executa o comando ffmpeg  
    subprocess.run(command)  
  
  
if __name__ == "__main__":  
    youtube_url = 'https://www.youtube.com/watch?v=3e_pbrjXdjU'  
    output_file = 'audio_capturado.wav'  
  
    capturar_audio_live(youtube_url, output_file)
    
