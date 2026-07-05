# Liga Portugal 2025/26 — Rating de Desempenho & Clustering de Estilos de Jogo

Projeto de ciência de dados aplicada a futebol: rating de desempenho individual ajustado por posição e segmentação de estilos de jogo por machine learning não supervisionado, com camada de apresentação em Power BI.


## Objetivo do projeto

 **Rating composto (0–100), ajustado por posição** — combina métricas per-90 relevantes por grupo de posição (DEF/MID/FWD) via z-score ponderado, para que um central nunca seja comparado diretamente com um avançado.


## Dados

FBref, Liga Portugal, época 2025/26 (`standard.csv`, `shooting.csv`, `misc.csv`). Filtro aplicado: mínimo de 5 jogos completos (`90s ≥ 5`, ≈450 minutos) para excluir amostras estatisticamente instáveis.

## Metodologia — Rating

O rating é calculado separadamente dentro de cada grupo de posição (DEF, MID, FWD). Cada métrica é convertida em z-score usando a média e o desvio-padrão do próprio grupo, que depois foi combinada com pesos específicos da posição e reescalada para 0–100.

Foram retirados jogadores com menos de 5 jogos completos para uma análise mais fiável



## Metodologia — Clustering

**Seleção de k:** o silhouette score identifica k=2 ou k=3 como estatisticamente ótimos (≈0.32), com uma queda acentuada a partir de k=4 (≈0.19 em k=5). A escolha foi o **k=5**, por uma razão deliberada: k=2/3 produziria grupos genéricos e pouco úteis para scouting (essencialmente "mais ofensivo" vs. "mais defensivo"), enquanto k=5 permite distinguir perfis táticos com valor prático — por exemplo, separar defesas centrais de defesas com projeção ofensiva, uma distinção que nem sequer estava codificada nos dados de posição de origem (o dataset só tem `DF`/`MF`/`FW`, sem indicação de lateral vs. central) e que o modelo recuperou apenas a partir do comportamento em campo.



## Limitações conhecidas dos dados

- **Homónimos não diferenciados:** o dataset contém pelo menos dois pares de jogadores com o mesmo nome no mesmo clube.

- **Um jogador sem posição registada** (`Pos` em falta na fonte) foi excluído do rating e do clustering por não ser possível atribuir grupo de posição.


