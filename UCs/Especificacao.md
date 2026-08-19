# Especificação textual dos casos de uso
## Sistema WebGIS de Localização de Serviços de Saúde

---

### UC1 — Selecionar hospital de referência

| Campo | Conteúdo |
|---|---|
| **Nome** | Selecionar hospital de referência |
| **Descrição** | Permite ao usuário escolher o hospital de Belo Horizonte que servirá como ponto de origem para as buscas de farmácias e laboratórios próximos. |
| **Fluxo principal** | 1. Usuário acessa o sistema. 2. Sistema exibe o mapa com a lista/marcadores de hospitais disponíveis. 3. Usuário seleciona um hospital na lista ou clica no marcador correspondente no mapa. 4. Sistema centraliza o mapa no hospital selecionado e o define como ponto de referência (origem) para as buscas seguintes. |
| **Fluxos alternativos** | A1. Usuário pesquisa o hospital pelo nome em um campo de busca textual. A2. Usuário altera o hospital selecionado a qualquer momento, reiniciando o ponto de referência. |
| **Pré-condições** | Existir ao menos um hospital cadastrado na base geográfica do sistema. |
| **Pós-condições** | Hospital definido como ponto de referência ativo para as consultas espaciais subsequentes. |

---

### UC2 — Localizar farmácias próximas

| Campo | Conteúdo |
|---|---|
| **Nome** | Localizar farmácias próximas |
| **Descrição** | Permite ao familiar do paciente localizar farmácias em torno do hospital selecionado, dentro de um raio definido. |
| **Fluxo principal** | 1. Usuário seleciona hospital (UC1 — *include*). 2. Usuário define o raio de busca (UC4 — *include*). 3. Sistema executa a consulta espacial farmácias × raio (UC6 — *include*). 4. Sistema lista os resultados por distância (UC7 — *include*). 5. Sistema exibe farmácias encontradas em lista e no mapa. |
| **Fluxos alternativos** | A1. Nenhuma farmácia encontrada dentro do raio — sistema exibe mensagem informativa e sugere aumentar o raio. A2. Usuário aplica filtro por categoria (UC5 — *extend*), refinando o tipo de farmácia (ex.: com plantão 24h). A3. Usuário altera o raio e refaz a busca. |
| **Pré-condições** | Hospital disponível e selecionado no sistema (UC1 concluído). |
| **Pós-condições** | Lista de farmácias exibida em ordem de proximidade, com marcadores correspondentes no mapa. |

---

### UC3 — Localizar laboratórios de radiografia próximos

| Campo | Conteúdo |
|---|---|
| **Nome** | Localizar laboratórios de radiografia próximos |
| **Descrição** | Permite ao médico localizar laboratórios de radiografia em torno do hospital selecionado, dentro de um raio definido, para encaminhamento do paciente. |
| **Fluxo principal** | 1. Usuário seleciona hospital (UC1 — *include*). 2. Usuário define o raio de busca (UC4 — *include*). 3. Sistema executa a consulta espacial laboratórios × raio (UC6 — *include*). 4. Sistema lista os resultados por distância (UC7 — *include*). 5. Sistema exibe laboratórios encontrados em lista e no mapa. |
| **Fluxos alternativos** | A1. Nenhum laboratório encontrado dentro do raio — sistema exibe mensagem informativa e sugere aumentar o raio. A2. Usuário aplica filtro por categoria (UC5 — *extend*), restringindo por especialidade do exame. A3. Usuário altera o raio e refaz a busca. |
| **Pré-condições** | Hospital disponível e selecionado no sistema (UC1 concluído). |
| **Pós-condições** | Lista de laboratórios exibida em ordem de proximidade, com marcadores correspondentes no mapa. |

---

### UC4 — Definir raio de busca

| Campo | Conteúdo |
|---|---|
| **Nome** | Definir raio de busca |
| **Descrição** | Permite ao usuário estabelecer a distância máxima (ex.: 200 m, 500 m, 1 km) a partir do hospital, dentro da qual os estabelecimentos serão buscados. |
| **Fluxo principal** | 1. Sistema apresenta opções pré-definidas de raio (ex.: 200 m, 500 m, 1 km) ou campo para valor customizado. 2. Usuário seleciona ou digita o raio desejado. 3. Sistema desenha uma área de busca (buffer circular) ao redor do hospital. 4. Sistema utiliza esse raio como parâmetro para a consulta espacial. |
| **Fluxos alternativos** | A1. Usuário informa valor inválido (ex.: negativo ou zero) — sistema exibe mensagem de erro e mantém o raio anterior. A2. Usuário redefine o raio após visualizar os resultados, disparando nova consulta. |
| **Pré-condições** | Hospital de referência já selecionado (UC1). |
| **Pós-condições** | Raio de busca definido e área de influência (buffer) delimitada em torno do hospital. |

---

### UC5 — Filtrar por categoria

| Campo | Conteúdo |
|---|---|
| **Nome** | Filtrar por categoria |
| **Descrição** | Permite ao usuário restringir os resultados exibidos por tipo de estabelecimento (hospital, farmácia, laboratório) ou por subcategoria. |
| **Fluxo principal** | 1. Sistema exibe opções de categoria disponíveis para o contexto da busca. 2. Usuário seleciona uma ou mais categorias. 3. Sistema reaplica a consulta espacial considerando apenas as categorias selecionadas. 4. Sistema atualiza lista e mapa com os resultados filtrados. |
| **Fluxos alternativos** | A1. Usuário remove todos os filtros — sistema volta a exibir todos os estabelecimentos da busca original. A2. Filtro aplicado não retorna nenhum resultado — sistema informa a ausência de resultados para a combinação escolhida. |
| **Pré-condições** | Uma busca de farmácias ou laboratórios já ter sido realizada (UC2 ou UC3 em andamento). |
| **Pós-condições** | Resultados exibidos restritos às categorias selecionadas pelo usuário. |

---

### UC6 — Realizar consulta espacial

| Campo | Conteúdo |
|---|---|
| **Nome** | Realizar consulta espacial |
| **Descrição** | Executa a análise geográfica que determina quais estabelecimentos (pontos) estão relacionados à área de busca (polígono/buffer) gerada a partir do hospital, aplicando operadores espaciais como "está contido", "contém" e "intercepta". |
| **Fluxo principal** | 1. Sistema recebe o ponto do hospital e o raio definido (UC4). 2. Sistema gera a área de influência (buffer/polígono) ao redor do hospital. 3. Sistema testa, para cada estabelecimento (ponto ou área), se está contido na área de busca, se a área de busca o contém, ou se há interseção entre as geometrias. 4. Sistema retorna o conjunto de estabelecimentos que satisfazem a condição espacial, junto com a distância calculada até o hospital. |
| **Fluxos alternativos** | A1. Estabelecimento representado como área (ex.: hospital com terreno extenso) — sistema utiliza o operador "intercepta" em vez de "contido", já que parte da geometria pode ultrapassar o raio. A2. Nenhum estabelecimento satisfaz a condição espacial — sistema retorna conjunto vazio para tratamento pelo caso de uso chamador. |
| **Pré-condições** | Hospital selecionado e raio de busca definido. |
| **Pós-condições** | Conjunto de estabelecimentos que atendem à condição espacial disponibilizado para listagem. |

---

### UC7 — Listar resultados por distância

| Campo | Conteúdo |
|---|---|
| **Nome** | Listar resultados por distância |
| **Descrição** | Organiza e apresenta ao usuário os estabelecimentos retornados pela consulta espacial, ordenados por proximidade ao hospital. |
| **Fluxo principal** | 1. Sistema recebe o conjunto de estabelecimentos retornado pela consulta espacial (UC6). 2. Sistema calcula a distância de cada estabelecimento até o hospital. 3. Sistema ordena os estabelecimentos em ordem crescente de distância. 4. Sistema exibe a lista ordenada ao usuário. |
| **Fluxos alternativos** | A1. Usuário opta por ordenar a lista alfabeticamente em vez de por distância (UC8 — *extend*). A2. Usuário opta por visualizar os resultados no mapa em vez de apenas na lista (UC9 — *extend*). |
| **Pré-condições** | Consulta espacial concluída, retornando ao menos um estabelecimento. |
| **Pós-condições** | Lista de estabelecimentos apresentada em ordem de proximidade ao hospital. |

---

### UC8 — Ordenar alfabeticamente

| Campo | Conteúdo |
|---|---|
| **Nome** | Ordenar alfabeticamente |
| **Descrição** | Permite ao usuário alterar o critério de ordenação da lista de resultados, exibindo os estabelecimentos em ordem alfabética em vez de por distância. |
| **Fluxo principal** | 1. Usuário seleciona a opção de ordenação alfabética na interface. 2. Sistema reordena a lista de estabelecimentos já retornada pelo caso de uso base (UC7), organizando pelo nome do estabelecimento. 3. Sistema atualiza a lista exibida, mantendo os marcadores no mapa inalterados. |
| **Fluxos alternativos** | A1. Usuário retorna à ordenação por distância a qualquer momento. |
| **Pré-condições** | Lista de resultados por distância já exibida (UC7 em execução). |
| **Pós-condições** | Lista de estabelecimentos reexibida em ordem alfabética. |

---

### UC9 — Visualizar resultados no mapa

| Campo | Conteúdo |
|---|---|
| **Nome** | Visualizar resultados no mapa |
| **Descrição** | Permite ao usuário visualizar espacialmente, sobre o mapa, os estabelecimentos retornados na listagem, complementando a exibição em lista. |
| **Fluxo principal** | 1. Sistema recebe a lista de estabelecimentos gerada pelo caso de uso base (UC7). 2. Sistema plota um marcador no mapa para cada estabelecimento, na posição geográfica correspondente. 3. Sistema desenha também o hospital de referência e a área de busca (buffer) no mapa. 4. Usuário pode clicar em um marcador para destacar o item correspondente na lista, e vice-versa. |
| **Fluxos alternativos** | A1. Usuário aproxima (zoom) ou afasta o mapa — sistema reposiciona os marcadores mantendo suas coordenadas reais. A2. Usuário clica em um estabelecimento na lista — sistema centraliza o mapa no marcador correspondente. |
| **Pré-condições** | Lista de resultados por distância já disponível (UC7 em execução). |
| **Pós-condições** | Estabelecimentos exibidos como marcadores no mapa, sincronizados com a lista de resultados. |
