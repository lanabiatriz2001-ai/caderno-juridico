# Biblioteca Manuscrita v13 — Especificação de Design

Data: 12 de agosto de 2026  
Produto: Caderno Jurídico  
Escopo: reformulação integral da rota Biblioteca

## 1. Objetivo

Transformar a Biblioteca em um catálogo visual de referências manuscritas para estudantes de Direito copiarem à mão. Cada referência deve ser individual, legível, funcional e vinculada a uma finalidade jurídica concreta.

A v13 substitui tanto a antiga grade de cartões digitais quanto as cinco pranchas com vários desenhos comprimidos. A unidade principal passa a ser o **modelo visual individual**.

## 2. Resultado esperado

A usuária deve conseguir:

1. encontrar rapidamente uma ideia por categoria ou finalidade jurídica;
2. enxergar o traço em tamanho suficiente para reproduzi-lo;
3. compreender quando usar o modelo;
4. abrir o modelo isoladamente;
5. aprender sua sequência básica de desenho;
6. salvar a referência como PNG;
7. navegar com a mesma eficiência no computador, tablet ou celular.

## 3. Escopo visual

A biblioteca terá **160 referências visuais reais**, distribuídas assim:

| Família | Quantidade | Conteúdo mínimo |
| --- | ---: | --- |
| Títulos | 32 | capítulos, conceitos, exceções, prazos, artigos e revisão |
| Caixas | 28 | definição, jurisprudência, alerta, observação e exemplo |
| Setas | 30 | sequência, retorno, bifurcação, reciprocidade, oposição e ciclo |
| Estruturas jurídicas | 28 | regra × exceção, requisitos, fluxo, confronto, competência e hierarquia |
| Divisores | 24 | mudança de assunto, subseção, conclusão e respiro |
| Páginas completas | 18 | composições aplicadas a conteúdos jurídicos |
| **Total** | **160** | |

Trocar apenas palavra, cor, sublinhado ou moldura não contará como uma referência nova.

## 4. Linguagens visuais obrigatórias

Os títulos devem incluir pelo menos dez linguagens claramente diferentes:

- marca-texto deslocado;
- caixa-alta editorial;
- composição cursiva combinada;
- título de margem;
- cantos abertos;
- selo orgânico;
- composição circular;
- composição vertical;
- elemento botânico funcional;
- composição geométrica assimétrica.

As demais famílias também devem variar estrutura, proporção, densidade, direção e semântica. Não será aceita uma coleção em que todos os itens usem a mesma fonte manuscrita, o mesmo lilás e o mesmo contorno.

## 5. Aplicação jurídica

Cada referência terá pelo menos uma etiqueta semântica. O vocabulário inicial será:

- regra;
- exceção;
- prazo;
- requisitos;
- competência;
- artigo de lei;
- jurisprudência;
- tese;
- confronto;
- fluxo processual;
- recursos;
- classificação;
- revisão;
- pegadinha;
- conclusão.

As estruturas completas devem demonstrar relações jurídicas, e não diagramas escolares genéricos. Exemplos: requisitos cumulativos, regra e exceção, tese e antítese, competência constitucional, sequência recursal e linha do tempo legislativa.

## 6. Arquitetura da tela

### 6.1 Cabeçalho

O cabeçalho da Biblioteca terá altura máxima de 64 px no desktop. Conterá apenas:

- identidade “Caderno Jurídico”;
- categorias visuais;
- busca.

Não exibirá Meus resumos, Construtor, Favoritos ou Backup dentro da experiência da Biblioteca.

### 6.2 Categorias

As categorias serão:

1. Títulos;
2. Caixas;
3. Setas;
4. Estruturas;
5. Divisores;
6. Páginas completas.

No celular, a barra será horizontal, rolável e terá indicação visual de continuidade.

### 6.3 Galeria editorial

A galeria usará composição editorial assimétrica, sem cartões digitais idênticos. Os modelos poderão aparecer como:

- tiras horizontais de papel;
- notas verticais estreitas;
- folhas quase quadradas;
- composições maiores em destaque;
- pequenas sequências didáticas.

Variações de proporção serão deliberadas, mas os textos e desenhos permanecerão legíveis. Bordas, sombras e raios não serão repetidos mecanicamente.

### 6.4 Modelo individual

Cada item exibirá:

- desenho principal;
- nome curto;
- finalidade jurídica;
- dificuldade: rápida, intermediária ou caprichada;
- quantidade estimada de traços.

O clique ou toque abrirá uma visualização individual.

### 6.5 Visualização individual

A visualização detalhada conterá:

- desenho grande;
- versão simplificada quando aplicável;
- sequência de dois a quatro passos;
- exemplo jurídico preenchido;
- finalidade recomendada;
- botão “Tela cheia”;
- botão “Salvar PNG”;
- controles de zoom e deslocamento no celular;
- fechamento por botão, tecla Escape e toque fora da folha.

## 7. Busca e navegação

A busca será global e filtrará os modelos efetivamente exibidos. Deve aceitar nome, categoria, finalidade e dificuldade, com normalização de acentos.

Exemplos:

- `exceção`;
- `prazo`;
- `retorno`;
- `jurisprudência rápida`;
- `fluxo processual`;
- `regra e exceção`.

A família e a consulta serão mantidas na URL:

`#/library/setas?q=retorno`

Atualizar a página, usar Voltar ou compartilhar a URL deve preservar o estado.

## 8. Direção estética

### 8.1 Materiais

- papel marfim ou branco quente;
- tinta preta levemente irregular;
- uma cor suave por referência;
- textura discreta, sem prejudicar a leitura;
- recortes e proporções inspirados em páginas reais de caderno.

### 8.2 Paleta

A biblioteca não será inteiramente lilás. As cores de apoio serão usadas com moderação:

- lilás acinzentado;
- azul lavado;
- verde sálvia;
- pêssego claro;
- rosa antigo;
- amarelo palha.

### 8.3 Princípios

- beleza subordinada à hierarquia;
- ornamentação com função;
- traço reproduzível;
- variedade perceptível;
- espaços de respiro;
- nenhuma aparência de componente SaaS genérico.

## 9. Conteúdo visual e ativos

Cada referência será um ativo visual próprio ou um recorte exportado em resolução suficiente. Sprites poderão ser usados internamente apenas se cada item tiver recorte correto, nome correspondente e nitidez adequada.

Não serão usados:

- desenhos feitos com CSS;
- emojis como ícones decorativos;
- SVG artesanal substituindo os ativos visuais;
- miniatura da prancha inteira como referência individual;
- ampliação de recorte borrado.

Os ativos serão incorporados ao HTML final para manter o arquivo autônomo.

## 10. Responsividade

### Desktop — 1440 px

- largura útil mínima da galeria: 1250 px;
- modelos principais com pelo menos 280 px;
- busca e categorias visíveis sem colisão;
- primeiro conjunto de referências acima da dobra.

### Tablet — 1024 px

- modelos com pelo menos 220 px;
- sem painel lateral consumindo largura;
- galeria com duas ou três colunas conforme a proporção do item.

### Celular — 390 px

- primeiro modelo visível antes de 180 px de rolagem;
- modelo ocupando praticamente toda a largura;
- categorias roláveis;
- modal com zoom e deslocamento;
- controles com área mínima de toque de 44 × 44 px.

## 11. Acessibilidade

- texto alternativo específico para cada desenho;
- foco visível;
- navegação completa por teclado;
- contraste suficiente para textos e controles;
- suporte a movimento reduzido;
- títulos e controles com semântica correta;
- fechamento do modal por Escape;
- foco devolvido ao item que abriu o modal.

## 12. Estados

A implementação contemplará:

- carregamento inicial;
- categoria ativa;
- busca com resultados;
- busca sem resultados;
- modelo aberto;
- erro ao gerar PNG;
- celular com barra de categorias rolada;
- foco e hover em controles.

## 13. Persistência e compatibilidade

A nova Biblioteca não apagará resumos, preferências, favoritos legados ou backups já existentes. Essas funcionalidades podem permanecer acessíveis por suas rotas antigas, mas não aparecerão na navegação visual da Biblioteca.

O HTML continuará funcionando em `file://` e no GitHub Pages, sem servidor e sem dependências externas obrigatórias.

## 14. Testes obrigatórios

### Funcionais

- seis categorias disponíveis;
- 160 referências indexadas;
- busca global e sem acento;
- URL preservando família e consulta;
- Voltar e Avançar restaurando o estado;
- abertura e fechamento do modelo;
- zoom móvel;
- exportação PNG;
- funcionamento em `file://`;
- dados legados preservados.

### Visuais

Capturas obrigatórias em:

- 1440 × 1000;
- 1024 × 768;
- 390 × 844.

A comparação deve verificar tipografia, espaçamento, cores, nitidez dos ativos, conteúdo, primeira dobra, tamanho útil e estados interativos.

## 15. Critérios de aceite

A v13 só poderá ser publicada quando:

1. as 160 referências estiverem presentes e semanticamente corretas;
2. a busca filtrar os modelos visuais mostrados;
3. cada modelo abrir isoladamente e permanecer legível;
4. não houver repetição visual disfarçada;
5. desktop, tablet e celular atenderem aos tamanhos mínimos;
6. todos os testes automatizados passarem;
7. não houver erros no console;
8. o relatório `design-qa.md` terminar com `final result: passed`;
9. o HTML local e o `index.html` publicado tiverem o mesmo conteúdo.

## 16. Fora do escopo

- editor ou construtor de resumos;
- geração automática de conteúdo jurídico;
- sincronização em nuvem;
- autenticação;
- colaboração;
- novos sistemas de favoritos;
- alterações no conteúdo jurídico salvo pela usuária.

## 17. Referências visuais pesquisadas

- Headers manuscritos: https://www.pinterest.com/pin/649503577491404669/
- Setas manuscritas: https://www.pinterest.com/pin/493566440414111264/
- Caixas e bordas: https://www.pinterest.com/darkerred/borders-bullet-journal/
- Divisores: https://www.pinterest.com/pin/10836855340894342/
- Famílias de layout: https://www.pinterest.com/pin/985231164434692/
- Guia de estruturas visuais: https://www.pinterest.com/pin/90072061276937987/

