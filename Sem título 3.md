from yt_dlp import YoutubeDL  
  
  
def get_audio_stream_url(youtube_url):  
    try:  
        # Define as opções para extrair o melhor áudio disponível  
  
        # passar api key        ydl_opts = {  
            'quiet': True,  
            'no_warnings': True,  
            'simulate': True,  
            'format': 'bestaudio/best',  
        }  
        # Cria o objeto YoutubeDL e extrai a informação do vídeo  
        with YoutubeDL(ydl_opts) as ydl:  
            info = ydl.extract_info(youtube_url, download=False)  
            audio_stream_url = info['url']  
            return audio_stream_url  
    except Exception as e:  
        print(f"Erro ao obter a URL do stream de áudio: {e}")  
        return None  
  
  
# Exemplo de uso  
youtube_url = "https://www.youtube.com/watch?v=oUy6CAPgej0"  
audio_stream_url = get_audio_stream_url(youtube_url)  
print(f"URL de stream de áudio: {audio_stream_url}")