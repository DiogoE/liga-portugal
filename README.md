# Liga Portugal 2025/26 — Rating de Desempenho & Clustering de Estilos de Jogo

Projeto de ciência de dados aplicada a futebol: rating de desempenho individual ajustado por posição e segmentação de estilos de jogo por machine learning não supervisionado, com camada de apresentação em Power BI.


## O que este projeto faz

1. **Rating composto (0–100), ajustado por posição** — combina métricas per-90 relevantes por grupo de posição (DEF/MID/FWD) via z-score ponderado, para que um central nunca seja comparado diretamente com um avançado.
2. **Clustering de estilos de jogo (K-Means, k=5)** — segmenta jogadores de campo em 5 perfis de estilo com base em métricas per-90, com seleção de k através de elbow method e silhouette score.
3. **Dashboard interativo em Power BI** — camada de apresentação com filtros, KPIs, visualizações e ficha de jogador, pensada para stakeholders não-técnicos.

## Dados

FBref, Liga Portugal, época 2025/26 (`standard.csv`, `shooting.csv`, `misc.csv`). Filtro aplicado: mínimo de 5 jogos completos (`90s ≥ 5`, ≈450 minutos) para excluir amostras estatisticamente instáveis — decisão validada empiricamente durante o desenvolvimento (ver secção de metodologia).

## Metodologia — Rating

O rating é calculado **separadamente dentro de cada grupo de posição** (DEF, MID, FWD): cada métrica é convertida em z-score usando a média e o desvio-padrão do próprio grupo, depois combinada com pesos específicos da posição e reescalada para 0–100.

**Consequência importante:** o rating não é comparável entre posições. Um defesa com rating 70 não é "melhor" que um avançado com rating 60 — cada rating só tem sentido *dentro* do seu grupo posicional.

Numa primeira versão sem este filtro, jogadores com poucos jogos dominavam artificialmente o topo do rating (ex: um jogador com 5-6 jogos e um golo isolado obtinha um z-score acima de 4 desvios-padrão em métricas per-90). Ao investigar, confirmou-se que 59 dos jogadores com rating mais alto tinham menos de 8 jogos — um padrão sistemático de amostra pequena, não um sinal de qualidade real. O filtro de minutos resolve isto ao remover jogadores cuja amostra é demasiado curta para produzir métricas per-90 fiáveis.

## Metodologia — Clustering

**Seleção de k:** o silhouette score identifica k=2 ou k=3 como estatisticamente ótimos (≈0.32), com uma queda acentuada a partir de k=4 (≈0.19 em k=5). Optou-se, ainda assim, por **k=5**, por uma razão deliberada: k=2/3 produziria grupos genéricos e pouco úteis para scouting (essencialmente "mais ofensivo" vs. "mais defensivo"), enquanto k=5 permite distinguir perfis táticos com valor prático — por exemplo, separar defesas centrais de defesas com projeção ofensiva, uma distinção que nem sequer estava codificada nos dados de posição de origem (o dataset só tem `DF`/`MF`/`FW`, sem indicação de lateral vs. central) e que o modelo recuperou apenas a partir do comportamento em campo.



## Limitações conhecidas dos dados

- **Homónimos não diferenciados:** o dataset contém pelo menos dois pares de jogadores com o mesmo nome no mesmo clube (ex: dois jogadores chamados "Zé Carlos" no Gil Vicente FC, de idades e posições diferentes). Não foi feita diferenciação adicional (ex: nome + ano de nascimento) por decisão de âmbito do projeto — cada linha de dados está correta individualmente, a limitação é apenas de identificação inequívoca na apresentação.
- **Um jogador sem posição registada** (`Pos` em falta na fonte) foi excluído do rating e do clustering por não ser possível atribuir grupo de posição.
- **Granularidade dos dados:** métricas como `Cruzamentos/90` e `Ações_Defensivas/90` são contagens de eventos, não dados de tracking físico — o pipeline é desenhado para ser facilmente adaptável a uma fonte de dados mais rica (ex: eventos + tracking de um provider como Opta/StatsBomb), mantendo a mesma lógica de rating e clustering.

