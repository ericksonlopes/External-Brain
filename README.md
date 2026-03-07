# External-Brain

Este repositório é o meu "external brain": um vault Obsidian com notas, projetos, tutoriais e referências organizadas por temas. O objetivo é centralizar conhecimento, documentação de projetos e material de estudo para consulta e evolução contínua.

## Estrutura (resumo)
- Programação/ — Notas, tutoriais e projetos de desenvolvimento (subpastas: Projetos, Fundamentos Python, IA & LLMs, DevOps, Bancos de Dados, etc.).
- Sociedade e Politica/ — Ensaios, reflexões e artigos.
- Componentes computador/ — Anotações sobre hardware e periféricos.
- Inbox/ — Links e conteúdo a processar.
- .obsidian/ — Configurações e plugins do Obsidian (workspaces, plugins, preferências).
- .idea/ — Configurações do PyCharm (IDE metadata).
- .gitignore — Lista de arquivos/pastas ignoradas por git (se o repositório for inicializado).

> Observação: muitos projetos incluem instruções específicas de execução dentro da própria pasta do projeto (por exemplo: `Programação/Backend e APIs/WAHA/🚀 Como rodar o WAHA.md`). Procure por arquivos contendo "Como rodar" ou por títulos com o emoji 🚀.

## Como abrir
1. Abrir a pasta `External-Brain` como um vault no Obsidian (recomendado) para aproveitar links bidirecionais, graph view e plugins.
2. Alternativamente, editar/ler os arquivos Markdown com VS Code, PyCharm ou qualquer editor de texto.

## Buscar e navegar
- Usar a busca nativa do Obsidian para pesquisas rápidas dentro do vault.
- Para buscas em terminal, usar ripgrep (rg):
  - Ex.: `rg -n "termo de busca"` 
- Para localizar instruções de execução de projetos: `rg "Como rodar" -n` ou procurar por `🚀 Como rodar`.

## Executar projetos de código
- Cada projeto costuma ter um arquivo com instruções (ex.: "Como rodar o WAHA.md"). Abrir o Markdown do projeto e seguir as instruções locais.
- Para projetos Python, passos comuns:
  - Criar ambiente virtual: `python -m venv .venv` ou usar pipenv/poetry conforme indicado.
  - Instalar dependências: `pip install -r requirements.txt` (se existir) ou seguir instruções do projeto.

## Como contribuir / adicionar notas
- Criar novos arquivos Markdown em pastas temáticas apropriadas (ex.: `Programação/Projetos/` para projetos novos).
- Use links ([[nome da nota]]) para conectar conhecimento e tags para classificação rápida.
- Se quiser versionar o vault, inicialize um repositório git (veja seção abaixo) e evite commitar arquivos sensíveis.

## Versionamento e backup
- Para iniciar controle de versão local (opcional):
  ```bash
  git init
  git add .
  git commit -m "Initial commit"
  git branch -M main
  git remote add origin <URL_DO_REPOSITORIO>
  git push -u origin main
  ```
- Recomenda-se manter backups regulares (GitHub/GitLab, OneDrive, Backblaze, etc.) e/ou usar Obsidian Sync.
- O arquivo `.gitignore` presente já contém regras básicas; revisar antes do primeiro commit.

## Plugins recomendados
- O vault já referencia alguns plugins em `.obsidian/plugins` (ex.: obsidian-git, table-editor, mindmap, wakatime, language-tool). Instalar os que achar úteis via Obsidian Community Plugins.

## Licença
- Nenhuma licença está definida por padrão. Se desejar tornar o conteúdo reutilizável, adicione um arquivo `LICENSE` (por exemplo, MIT).

---

Se quiser, posso:
- ajustar o README com mais detalhes sobre uma pasta específica;
- inicializar um repositório Git e fazer o commit inicial (preciso da sua confirmação);
- adicionar um arquivo LICENSE com a licença escolhida.

(feito automaticamente pelo assistente — peça alterações se quiser outra redação ou seção)
