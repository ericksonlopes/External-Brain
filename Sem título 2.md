from pytube import YouTube
from moviepy.editor import AudioFileClip

# Função para obter a duração do áudio de um vídeo do YouTube
def get_audio_duration(youtube_url):
    # Baixar o vídeo do YouTube
    yt = YouTube(youtube_url)
    video_stream = yt.streams.filter(only_audio=True).first()
    audio_file = video_stream.download(filename='audio.mp4')
    
    # Extrair a duração do áudio
    audio_clip = AudioFileClip(audio_file)
    duration = audio_clip.duration  # Duração em segundos
    audio_clip.close()
    
    # Excluir o arquivo de áudio baixado, se necessário
    import os
    os.remove(audio_file)
    
    # Converter a duração para minutos e segundos
    minutes, seconds = divmod(duration, 60)
    return int(minutes), int(seconds)

# Exemplo de uso
youtube_url = 'https://www.youtube.com/watch?v=dQw4w9WgXcQ'  # Substitua pelo URL do vídeo desejado
minutes, seconds = get_audio_duration(youtube_url)
print(f"Duração do áudio: {minutes} minutos e {seconds} segundos")
