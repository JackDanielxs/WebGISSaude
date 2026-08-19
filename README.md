
# Protótipo WebGIS — Serviços de saúde próximos (Belo Horizonte)

## Resumo

Protótipo funcional de uma aplicação WebGIS que permite localizar **farmácias** e
**laboratórios de radiografia** próximos a um hospital selecionado em Belo Horizonte,
atendendo às duas estórias de usuário do trabalho:

- **Familiar do paciente** — localizar farmácias próximas do hospital, dentro de um raio.
- **Médico do paciente** — localizar laboratórios de radiografia próximos do hospital, dentro de um raio.

É uma aplicação de página única (`prototipo_webgis_saude_bh.html`), sem necessidade de
instalação ou servidor: basta abrir o arquivo em um navegador com conexão à internet
(as bibliotecas de mapa são carregadas via CDN).

## Tecnologias

| Tecnologia | Função |
|---|---|
| **Leaflet.js** | Renderização do mapa interativo, marcadores, polígonos e linhas |
| **Turf.js** | Cálculos geoespaciais: buffer, distância e operadores espaciais |
| **OpenStreetMap** | Camada base de tiles do mapa |
| HTML/CSS/JS puro | Interface e lógica da aplicação, sem framework ou build step |

## Como funciona

### 1. Seleção do hospital (ponto de referência)
O usuário escolhe um hospital em uma lista suspensa. O mapa centraliza nele e um
marcador é desenhado — esse ponto passa a ser a origem de todos os cálculos seguintes.

### 2. Definição do raio de busca
O usuário escolhe um raio pré-definido (200 m, 500 m, 1 km, 2 km) ou informa um valor
personalizado. A aplicação gera, com `turf.buffer`, um **polígono circular** ao redor
do hospital representando a área de busca.

### 3. Filtro por categoria
Checkboxes permitem incluir/excluir farmácias e laboratórios do resultado. O filtro é
reaplicado a cada mudança, sem precisar de nova seleção de hospital.

### 4. Consulta espacial
A cada busca, três operadores espaciais são calculados e exibidos em um painel:

- **está contido** — testa, para cada estabelecimento (ponto), se ele está dentro do
  buffer do hospital (`turf.booleanPointInPolygon`). Só os estabelecimentos que
  satisfazem essa condição entram na lista de resultados.
- **intercepta** — testa se o polígono do buffer intercepta o polígono de um bairro
  ilustrativo (`turf.booleanIntersects`), demonstrando uma relação polígono × polígono.
- **contém** — testa se o polígono do bairro contém o buffer inteiro
  (`turf.booleanContains`).

### 5. Ordenação e listagem
Os resultados podem ser ordenados por **proximidade** (distância calculada com
`turf.distance`) ou em **ordem alfabética**. A lista lateral fica sincronizada com os
marcadores do mapa: clicar em um item centraliza o mapa nele e abre seu popup, e
clicar em um marcador destaca o item correspondente na lista.

### 6. Entidades geográficas representadas
- **Pontos** — hospital e cada estabelecimento (farmácia/laboratório)
- **Polígonos** — buffer do raio de busca e o bairro ilustrativo
- **Linha** — rota tracejada do hospital até o estabelecimento mais próximo do
  resultado atual

## Dados utilizados

- Os **hospitais** correspondem a localizações aproximadas de estabelecimentos reais
  de Belo Horizonte (informação pública), usados apenas como pontos de referência.
- As **farmácias**, os **laboratórios** e o **polígono de bairro** são dados fictícios,
  criados exclusivamente para demonstrar o funcionamento dos filtros, do raio de busca
  e dos operadores espaciais — não representam estabelecimentos reais.
- Para uso com dados reais, basta substituir os arrays `hospitals` e `facilities` (ou o
  polígono `bairroPoligono`) no início do script por dados carregados de um arquivo
  GeoJSON/CSV real.

## Limitações conhecidas

- Não há persistência de dados (tudo é reconstruído em memória a cada carregamento).
- O buffer é aproximado (Turf.js calcula um polígono geodésico, não um círculo perfeito
  em todas as projeções), o que é adequado para escalas de bairro como as usadas aqui.
- O polígono de bairro é meramente ilustrativo, usado para demonstrar os operadores
  "contém" e "intercepta" exigidos pelo enunciado.
