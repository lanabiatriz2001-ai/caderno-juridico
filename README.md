# Caderno Jurídico 📚⚖️

Biblioteca visual e construtor local de **resumos jurídicos** em páginas A4 e 4:3 — títulos, subtítulos, marcações e molduras inspirados em cadernos feitos à mão.

**✨ Acesse online:** https://lanabiatriz2001-ai.github.io/caderno-juridico/

## Funcionalidades

- **Meus resumos** — crie cadernos visuais por matéria, tema ou revisão (até 250 resumos, 50 páginas cada).
- **Construtor** — monte páginas com blocos de título, subtítulo, tópico, listas, tabelas e elementos visuais, além de blocos jurídicos: conceito, artigo de lei, jurisprudência, súmula, prazo, mnemônico, doutrina, linha do tempo e mais.
- **Tipografia por bloco** — escolha entre 15 fontes (manuscritas, serifadas, display) para cada bloco.
- **Páginas** — adicione, renomeie, duplique e exclua páginas de cada resumo.
- **Biblioteca visual** — 43 coleções de modelos de lettering para usar nos seus resumos.
- **Favoritos** — guarde os estilos que você mais usa.
- **Backup** — exporte e importe seus dados a qualquer momento.
- **Exportação** — exporte a página do resumo como PNG em alta resolução ou imprima direto do Construtor (A4/4:3).

## Privacidade

Tudo fica salvo **no seu dispositivo** (IndexedDB, com salvamento automático e diário de recuperação). Nenhum dado é enviado para servidores.

## Tecnologia

Aplicativo de arquivo único (`index.html`) em HTML, CSS e JavaScript puro, sem build e sem dependências instaladas — apenas [html2canvas](https://html2canvas.hertzen.com/) (via CDN) para exportar PNG e Google Fonts para as tipografias.

## Rodando localmente

Basta abrir o `index.html` no navegador, ou servir a pasta:

```bash
python3 -m http.server 8000
```

e acessar http://localhost:8000.
