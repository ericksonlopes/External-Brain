
O `gdown` é uma ferramenta de linha de comando (CLI) e biblioteca Python extremamente útil para baixar arquivos grandes do Google Drive, contornando as restrições de segurança que impedem o uso de `curl` ou `wget` em arquivos que exigem confirmação de "verificação de vírus".

---

## 🛠️ Instalação

Você pode instalar o `gdown` via pip:

```bash
pip install gdown
```

Para garantir que você tem a versão mais recente com correções de bugs de autenticação:

```bash
pip install --upgrade gdown
```

---

## 🚀 Como Usar (Linha de Comando)

A forma mais simples de usar é através do ID do arquivo ou da URL de compartilhamento.

### 1. Baixar um arquivo individual
Substitua `FILE_ID` pelo ID do arquivo (aquela sequência de letras e números na URL do Drive):

```bash
gdown https://drive.google.com/uc?id=FILE_ID
```

### 2. Baixar uma pasta completa
Para baixar pastas, utilize a flag `--folder`:

```bash
gdown --folder https://drive.google.com/drive/folders/FOLDER_ID
```

### 3. Definir o nome do arquivo de saída
Use a flag `-O` ou `--output`:

```bash
gdown https://drive.google.com/uc?id=FILE_ID -O meu_arquivo.zip
```

---

## 🐍 Uso em Scripts Python

Você também pode integrar o `gdown` diretamente no seu código:

```python
import gdown

url = 'https://drive.google.com/uc?id=1234567890abcdef'
output = 'dados_treinamento.zip'

# Baixa o arquivo
gdown.download(url, output, quiet=False)

# Baixa uma pasta (retorna lista de arquivos baixados)
folder_url = 'https://drive.google.com/drive/folders/folder_id'
files = gdown.download_folder(folder_url)
```

---

## 🔍 Como encontrar o ID do arquivo?

Na URL de compartilhamento do Google Drive:
`https://drive.google.com/file/d/1A2B3C4D5E6F7G8H9I0J/view?usp=sharing`

O ID é a parte entre `/d/` e `/view`: **`1A2B3C4D5E6F7G8H9I0J`**.

---

## 📝 Dicas e Soluções de Problemas

*   **Acesso Negado**: Certifique-se de que o link no Google Drive está configurado como **"Qualquer pessoa com o link"**. O `gdown` não funciona nativamente com arquivos privados sem autenticação complexa.
*   **Arquivos Pesados**: O `gdown` gerencia automaticamente a confirmação de "arquivo muito grande para verificação de vírus" do Google.

---
#python #automacao #google-drive #scraping #tutorial
