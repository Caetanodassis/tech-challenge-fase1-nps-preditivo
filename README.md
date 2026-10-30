# NPS Preditivo: Transformando Dados Operacionais em Lealdade

**Tech Challenge Fase 1 | PosTech AI Scientist (FIAP)** | Case NPS Preditivo

Análise exploratória orientada a negócio e modelo preditivo para antecipar clientes com risco de se tornarem detratores **antes** da pesquisa de NPS, seguindo a metodologia CRISP-DM.

> **Estado deste repositório:** a estrutura entregue contém a base de dados e o notebook de EDA. O notebook de modelagem, o código de inferência, os modelos salvos e os gráficos exportados descritos adiante não estão presentes; portanto, os resultados da seção 8 não podem ser reproduzidos a partir deste checkout.

---

## Sumário

1. [Descrição do Projeto e Objetivos](#1-descrição-do-projeto-e-objetivos)
2. [Resumo Executivo](#2-resumo-executivo)
3. [Entendimento do Negócio (Requisito 1)](#3-entendimento-do-negócio-requisito-1)
4. [Definição da Target (Requisito 2)](#4-definição-da-target-requisito-2)
5. [Descrição da Base de Dados](#5-descrição-da-base-de-dados)
6. [Metodologia CRISP-DM](#6-metodologia-crisp-dm)
7. [Principais Insights da EDA (Requisito 3)](#7-principais-insights-da-eda-requisito-3)
8. [Solução Preditiva (Requisito 4)](#8-solução-preditiva-requisito-4)
9. [Estrutura do Repositório](#9-estrutura-do-repositório)
10. [Instruções de Reprodução](#10-instruções-de-reprodução)
11. [Limitações e Riscos](#11-limitações-e-riscos)
12. [Status dos Entregáveis](#12-status-dos-entregáveis)
13. [Autores](#13-autores)

---

## 1. Descrição do Projeto e Objetivos

### Contexto

Com o crescimento acelerado do e-commerce nacional, a empresa passou a lidar com um volume cada vez maior de pedidos, entregas e interações com clientes. A escala trouxe ganhos, mas também uma alta variabilidade do Net Promoter Score (NPS): mesmo com indicadores operacionais aparentemente semelhantes, alguns clientes se tornam promotores e outros, detratores.

Hoje o NPS é coletado apenas após o encerramento da jornada de compra. A gestão é, portanto, **reativa**: a empresa descobre a insatisfação quando o cliente já a manifestou, sem tempo de agir.

### Pergunta orientadora

> Quais fatores operacionais realmente influenciam a satisfação do cliente e como a empresa pode agir de forma proativa para melhorar a experiência antes da aplicação da pesquisa de NPS?

### Objetivos

- Documentar a visão de negócio e a definição da variável alvo (Requisitos 1 e 2).
- Realizar uma Análise Exploratória (EDA) com foco operacional e rigor estatístico: fatores críticos, causas de detração, pontos de ruptura e perfil de clientes (Requisito 3).
- Propor e implementar um modelo de classificação para identificar potenciais detratores antes da pesquisa (Requisito 4).
- Produzir material executivo (slides e vídeo) com linguagem gerencial.
- Entregar um repositório organizado e reprodutível.

---

## 2. Resumo Executivo

| Pergunta | Resposta em uma frase |
|---|---|
| O que mais derruba o NPS? | **Atraso na entrega** e **reclamações**. Perfil do cliente e dados do pedido não têm efeito relevante. |
| Existe ponto de ruptura? | Sim: com **1 dia de atraso** a maioria dos clientes (60%) já é detratora; a nota cai cerca de **1 ponto por dia**. |
| Quem é o cliente mais insatisfeito? | Não há perfil: idade, região e tempo de relacionamento não alteram a nota. O problema é **operacional, não demográfico**. |
| O atendimento resolve? | Mitiga (cerca de 1,6 ponto entre o atendimento mais rápido e o mais lento), mas **não reverte** a insatisfação. Prevenir a falha é mais eficaz que remediá-la. |
| Dá para prever? | Sim, **depois da entrega**: ROC-AUC de **0,88** no teste (baseline: 0,50), com Recall de 0,89 e Precision de 0,88. **No momento do pedido, não** (ROC-AUC de 0,51). |
| Qual o maior ganho de negócio? | Prever e prevenir o **atraso**, que é o fator mais forte e só é conhecido na entrega. |

> **Nota sobre os dados:** a base tem sinais fortes de ser sintética (ver [Limitações](#11-limitações-e-riscos)). Os resultados descrevem a base fornecida e devem ser revalidados com dados reais antes de decisões de alto impacto.

---

## 3. Entendimento do Negócio (Requisito 1)

### 3.1 Qual problema de negócio está sendo resolvido?

A **inércia operacional causada pelo NPS reativo**. Como a nota só existe depois que a jornada termina, a empresa não consegue priorizar ações corretivas nem intervir enquanto o cliente ainda pode ser recuperado. O desafio é identificar, a partir de dados que já existem durante a jornada (pedido, entrega, atendimento), quais situações levam à insatisfação e quais clientes estão em risco, para atuar de forma preventiva.

### 3.2 Por que o NPS é importante para um e-commerce?

- **Retenção e LTV:** promotores tendem a permanecer mais tempo e gastar mais, elevando o Lifetime Value.
- **Custo de Aquisição (CAC):** promotores geram indicações espontâneas; cada detrator perdido precisa ser reposto a um custo de aquisição.
- **Diferenciação:** em e-commerce, produto e preço são facilmente comparáveis; a experiência (entrega e atendimento) é um dos principais diferenciais.
- **Indicador antecedente de crescimento:** o NPS costuma sinalizar tendências de recompra e reputação antes que apareçam na receita.

### 3.3 Áreas que se beneficiam dos insights

| Área | Como se beneficia |
|---|---|
| Logística | Limiares de atraso que degradam a experiência; priorização de rotas e transportadoras. |
| Atendimento / SAC | Impacto do volume de reclamações e do tempo de resolução; priorização de casos de maior risco. |
| Pricing | Verificar que frete, desconto e parcelamento **não** estão associados à satisfação nesta base. |
| Gestão de Produto | Acompanhar reclamações por categoria e perfil de pedido. |
| CRM / Marketing | Acionar recuperação para clientes em risco; evitar segmentações demográficas sem poder discriminante. |
| Diretoria | Acompanhar a experiência com indicadores preditivos, não apenas retrospectivos. |

### 3.4 Reflexão estratégica: impacto do NPS

- **Recompra:** na base, a recompra em 30 dias é de 100% entre promotores, 3,8% entre neutros e 0% entre detratores. A direção do efeito é clara, mas a relação é perfeitamente determinística (recompra ocorre se e somente se a nota é ≥ 8), o que é um **artefato da base** e impede inferir magnitude ou causalidade (ver seção 7.6).
- **Boca a boca:** promotores recomendam a marca espontaneamente, gerando aquisição orgânica de baixo custo; detratores geram o efeito contrário, amplificado por avaliações públicas.
- **Market share:** reter clientes e atrair novos por indicação permite crescer em participação sem aumentar proporcionalmente o investimento em aquisição.

### 3.5 Indicadores complementares

| Indicador | Por que complementa o NPS |
|---|---|
| OTIF (On-Time In-Full) | Mede entregas no prazo e completas; ligado à principal causa de detração encontrada. |
| SLA logístico | Compara prazo prometido e realizado; permite definir tolerâncias de atraso. |
| FCR (First Contact Resolution) | Mede resolução no primeiro contato; reflete a eficiência do atendimento. |
| Taxa de recompra e churn | Traduzem o NPS em resultado financeiro. |
| Reclamações por mil pedidos | Antecipa problemas de produto e operação. |
| Benchmarks de NPS do setor e reputação em marketplaces | Contextualizam a nota frente à concorrência. |

---

## 4. Definição da Target (Requisito 2)

| Pergunta | Resposta |
|---|---|
| Qual variável representa a satisfação? | `nps_score`, nota de 0 a 10. |
| Por que foi escolhida? | É a medida padronizada de lealdade e recomendação, comparável a benchmarks e representa a percepção direta do cliente. |
| Quando é coletada? | Após a conclusão do pedido, depois da entrega e de eventuais interações com o atendimento. |
| Há risco de uso inadequado? | Sim (abaixo). |

### Classificação adotada

A nota na base é **contínua** (83% dos valores têm casa decimal). A convenção do projeto é:

| Categoria | Regra | Pedidos | % |
|---|---|---|---|
| Detrator | nota ≤ 6 | 1.851 | 74,0% |
| Neutro | 6 < nota ≤ 8 | 448 | 17,9% |
| Promotor | nota > 8 | 201 | 8,0% |

O NPS líquido da base (% promotores − % detratores) é de **−66 pontos**. A sensibilidade à convenção foi testada: com o corte `< 7`, a taxa de detratores sobe para 84,4%.

### Riscos de uso inadequado

1. **Data Leakage:** `csat_internal_score` (correlação de 0,56 com a nota) é medida concomitante da satisfação e `repeat_purchase_30d` ocorre **após** o pedido e é consequência da nota. Ambas foram **excluídas** das variáveis preditoras, assim como `nps_score` e os identificadores.
2. **Viés de resposta:** clientes em extremos tendem a responder mais; a opinião do meio é sub-representada.
3. **Contaminação por eventos pontuais:** uma falha isolada pode distorcer a nota de um cliente com boa relação geral.
4. **Correlação não é causalidade:** os fatores associados à detração não provam que agir sobre eles muda a nota.
5. **Escala:** a diferença entre duas notas não é uniforme ao longo da escala, e o corte entre categorias é uma convenção.

---

## 5. Descrição da Base de Dados

**Arquivo:** `data/desafio_nps_fase_1.csv` | **2.500 pedidos x 19 colunas** | sem valores nulos, sem duplicidades, identificadores únicos (um pedido por cliente).

| Coluna | Descrição | Grupo |
|---|---|---|
| `customer_id`, `order_id` | Identificadores | Identificação |
| `customer_age` | Idade do cliente | Perfil |
| `customer_region` | Região geográfica | Perfil |
| `customer_tenure_months` | Tempo de relacionamento (meses) | Perfil |
| `order_value` | Valor total do pedido | Pedido |
| `items_quantity` | Quantidade de itens | Pedido |
| `discount_value` | Valor de desconto | Pedido |
| `payment_installments` | Parcelas do pagamento | Pedido |
| `freight_value` | Valor do frete | Pedido |
| `delivery_time_days` | Tempo total de entrega (dias) | Logística |
| `delivery_delay_days` | Dias de atraso | Logística |
| `delivery_attempts` | Tentativas de entrega | Logística |
| `customer_service_contacts` | Contatos com o atendimento | Atendimento |
| `complaints_count` | Reclamações registradas | Atendimento |
| `resolution_time_days` | Tempo de resolução (dias) | Atendimento |
| `repeat_purchase_30d` | Recompra em até 30 dias (0/1) | Resultado de negócio (excluída do modelo) |
| `csat_internal_score` | Score interno de satisfação | Indicador interno (excluída do modelo) |
| `nps_score` | Nota NPS (0 a 10, contínua) | **Target** |

### Qualidade dos dados (auditoria completa no notebook 01)

- Estrutura íntegra; nenhuma linha removida.
- **121 pedidos (4,8%)** têm `delivery_delay_days` maior que `delivery_time_days`, o que é logicamente inconsistente; além disso, as duas variáveis são praticamente independentes (ρ ≈ 0). Linhas mantidas, por não ser possível determinar qual campo está incorreto.
- 35 pedidos têm desconto maior que o valor do pedido.
- Apenas **23 pedidos (0,9%)** não têm reclamações; comparações "com vs. sem reclamação" se apoiam em um grupo pequeno.

---

## 6. Metodologia CRISP-DM

| Fase | O que foi feito | Onde |
|---|---|---|
| 1. Entendimento do Negócio | Problema, pergunta orientadora, target, riscos e indicadores complementares. | Seções 3 e 4 |
| 2. Entendimento dos Dados | Auditoria de qualidade, consistência lógica, distribuição da target e análise de sensibilidade. | `notebooks/01_eda_negocio.ipynb` |
| 3. Preparação dos Dados | Target binária, categorias NPS, flags operacionais, governança de variáveis contra Data Leakage. | `notebooks/01`, `src/nps_utils.py`, `data/processed/` |
| 4. Modelagem | Baseline, Regressão Logística, Random Forest e XGBoost com busca de hiperparâmetros por validação cruzada. | `notebooks/02_modelo_preditivo.ipynb` |
| 5. Avaliação | Teste usado uma vez; ROC-AUC, PR-AUC, Recall, Precision, calibração, bootstrap, cenários e robustez. | `notebooks/02` |
| 6. Implantação (proposta) | Modelo e metadados persistidos; módulo de inferência e fluxo de priorização. | `models/`, `src/predict.py` |

**Princípios de rigor aplicados:** intervalos de confiança de 95% e tamanho de amostra em todas as comparações; correlação de Spearman; controle de confundimento por regressão multivariada; seleção do modelo por validação cruzada apenas no treino; limiar escolhido sem uso do teste; semente fixa.

---

## 7. Principais Insights da EDA (Requisito 3)

Os números abaixo são apresentados no notebook `notebooks/01_eda_negocio.ipynb`. Os gráficos são exibidos durante a execução do notebook; arquivos exportados em `reports/figures/` ainda não estão incluídos.

### Respostas às perguntas do desafio

| Pergunta | Resposta |
|---|---|
| **Quais fatores parecem mais críticos para a satisfação?** | O **atraso na entrega** (correlação de Spearman ρ = −0,59) e a **quantidade de reclamações** (ρ = −0,49), seguidos por contatos com o atendimento (ρ = −0,34) e tempo de resolução (ρ = −0,19). Tentativas de entrega (ρ = +0,03, p = 0,19), perfil e dados do pedido não têm efeito relevante. |
| **O que mais gera detratores?** | **Atraso** (36,5% de detratores no prazo → 78,7% com atraso) e **reclamações** (efeito graduado: 8% com 1 reclamação, 43% com 2, 68% com 3, 95% com 7 ou mais). Na regressão multivariada, os maiores efeitos independentes são reclamações (odds ratio ≈ 4,8 por desvio-padrão), atraso (≈ 3,6) e tempo de resolução (≈ 1,8). Múltiplas tentativas de entrega não geram detratores (73,9% vs. 74,4%). |
| **Existe ponto de ruptura?** | **Sim, logo no 1º dia de atraso.** A nota média cai de 6,86 (no prazo) para 5,55 (1 dia) e a taxa de detratores cruza 50% (36,5% → 59,7%), chegando a 90% com 3 dias de atraso. A queda é contínua, de cerca de 1 ponto por dia, e o primeiro dia tem a maior queda marginal (−1,3 ponto). |
| **Que tipo de cliente tem NPS mais alto ou mais baixo?** | **Nenhum perfil se destaca.** A nota média fica entre 4,1 e 4,7 em todas as regiões, faixas etárias e faixas de tempo de relacionamento, e nenhum teste de Kruskal-Wallis é significativo a 5% (região p = 0,50; tempo de relacionamento p = 0,19; faixa etária p = 0,058, sem padrão monotônico). |
| **O NPS influencia a recompra?** | Sim, na direção esperada: recompra em 30 dias de 100% (promotores), 3,8% (neutros) e 0% (detratores). **Ressalva:** a relação é determinística na base (ver 7.6). |
| **O atendimento é um gargalo?** | Sim. A nota média é 7,8 com 1 reclamação, 6,05 com 2 e 4,9 com 3. Resolver rápido **mitiga** (nota de 5,3 com resolução imediata contra 3,7 acima de 10 dias), mas **nenhuma faixa de resolução leva a nota à zona neutra**. |

### Resumo executivo dos achados

**Insight 1. O cumprimento do prazo é o pilar da satisfação.** O atraso na entrega é o fator operacional mais fortemente associado à queda do NPS (ρ = −0,59) e eleva a taxa de detratores de 36,5% para 78,7%. Ações preventivas devem priorizar a mitigação de falhas logísticas antes que elas gerem reclamações.

**Insight 2. Não há margem de tolerância ao atraso.** Com 1 dia de atraso, a nota média passa de 6,86 para 5,55 e a maioria dos clientes (60%) já é detratora. A promessa de prazo deve ser tratada como compromisso inegociável, e a previsão de um único dia de atraso já justifica uma ação de contingência.

**Insight 3. Reclamações são o segundo grande gatilho, e o efeito é cumulativo.** O cliente tolera uma reclamação (8% de detratores), mas a taxa sobe para 43% na segunda e 68% na terceira. Atraso e reclamações se somam: com 3 ou mais dias de atraso e 5 ou mais reclamações, de 97% a 100% dos clientes são detratores; sem atraso e com até 2 reclamações, são 16%.

**Insight 4. O problema é operacional, não demográfico.** Idade, região e tempo de relacionamento não alteram a nota. A experiência é ditada pela eficiência da operação, não pelas características do consumidor. Campanhas de retenção segmentadas por perfil teriam pouco poder discriminante; o foco deve estar em quem vive a falha operacional.

**Insight 5. O atendimento reduz o dano, mas não o reverte.** Resolver rápido recupera cerca de 1,6 ponto de nota em relação à resolução mais lenta, mas nenhuma faixa de tempo devolve o cliente à zona neutra. Como a maioria dos pedidos (99%) teve ao menos uma reclamação, a conclusão vale para quem já teve problema. A prevenção da falha é mais eficaz que a remediação.

**Insight 6. A satisfação está ligada à recompra.** Promotores recompram; detratores não. Do ponto de vista de negócio, cada detrator representa receita futura em risco. A magnitude exata (100% e 0%) é um artefato da base e não deve ser usada para estimar ROI (ver 7.6).

As visualizações podem ser consultadas executando o notebook de EDA. Imagens exportadas ainda não estão incluídas no repositório.

### 7.6 Rigor e cuidados na leitura dos achados

- **Recompra determinística:** `repeat_purchase_30d = 1` se e somente se `nps_score ≥ 8` (concordância de 100%). Em dados reais isso não ocorre; indica que a variável foi gerada a partir da nota. A relação deve ser lida como **direção**, não como prova nem como estimativa de ROI, e a variável **não pode** ser preditora.
- **Grupo sem reclamação tem n = 23:** o salto de 4,3% para 74,7% é qualitativamente correto, mas o IC 95% do valor inicial vai de 0,8% a 21%. A análise graduada por número de reclamações é mais confiável.
- **O ponto de ruptura não é um degrau isolado:** a nota cai de forma contínua; o 1º dia é a maior queda e o ponto em que a maioria passa a ser detratora.
- **Contatos com o atendimento:** correlacionam-se com a nota (ρ = −0,34), mas na regressão multivariada, controlados por reclamações (ρ entre as duas = 0,74), não têm efeito próprio. É multicolinearidade, não evidência de que contatar o atendimento ajuda ou prejudica.

---

## 8. Solução Preditiva (Requisito 4)

> Os resultados desta seção foram mantidos como referência documental, mas o notebook de modelagem e os artefatos necessários para reproduzi-los não fazem parte deste repositório.

### 8.1 Estratégia: classificação

| Abordagem | Avaliação |
|---|---|
| Regressão da nota | Melhor modelo (Ridge): R² = 0,54, erro absoluto médio de 1,4 ponto. Como ranking de risco, ROC-AUC = 0,873, **igual** ao do classificador (0,876). Não melhora a decisão. |
| **Classificação binária (escolhida)** | Responde diretamente à decisão de negócio (agir ou não agir), entrega probabilidade calibrada e métricas alinhadas à decisão. |

### 8.2 Especificação

| Item | Definição |
|---|---|
| Pergunta | Dado o que se sabe antes da pesquisa, qual a probabilidade de o cliente se tornar detrator? |
| Variável alvo | `detrator = 1` se `nps_score ≤ 6`, caso contrário 0 (prevalência de 74%) |
| Variáveis de entrada | 14 variáveis de perfil, pedido, logística e atendimento |
| Excluídas | `nps_score`, `csat_internal_score`, `repeat_purchase_30d` e identificadores (Data Leakage) |
| Pré-processamento | One-Hot Encoding em `customer_region`; padronização das numéricas; tudo dentro de um `Pipeline` ajustado apenas no treino |
| Separação | 80% treino / 20% teste, estratificada, semente 42. Validação cruzada de 5 folds somente no treino |
| Modelos | Baseline trivial, Regressão Logística, Random Forest, XGBoost (busca aleatória de 20 combinações cada) |
| Seleção | Regra do erro padrão: modelo mais simples estatisticamente equivalente ao melhor |

### 8.3 Resultados

**Validação cruzada (treino):**

| Modelo | ROC-AUC | PR-AUC |
|---|---|---|
| Baseline (sempre detrator) | 0,500 | 0,741 |
| **Regressão Logística (selecionado)** | 0,876 ± 0,012 | 0,952 |
| Random Forest | 0,874 ± 0,016 | 0,949 |
| XGBoost | 0,879 ± 0,017 | 0,952 |

Os três algoritmos são estatisticamente equivalentes; a Regressão Logística foi selecionada por ser a mais simples e interpretável.

**Teste (usado uma única vez), Regressão Logística, limiar de operação = 0,59 (meta de Recall de 90% definida no treino):**

| Métrica | Valor | IC 95% (bootstrap) | Baseline trivial |
|---|---|---|---|
| ROC-AUC | 0,876 | 0,839 a 0,908 | 0,500 |
| PR-AUC | 0,947 | 0,924 a 0,966 | 0,740 |
| Recall (detratores) | 0,889 | 0,855 a 0,920 | 1,000 |
| Precision | 0,880 | 0,847 a 0,913 | 0,740 |
| Brier score | 0,116 | | 0,260 |

Matriz de confusão (500 pedidos): 329 detratores detectados, 41 não detectados, 45 falsos alarmes, 85 não detratores corretamente poupados.

> **Por que o Recall isolado não basta:** com 74% de detratores, um classificador que sempre prevê "detrator" tem Recall de 100%. O projeto prioriza o Recall, como pede o plano de execução, mas o avalia **em conjunto com a Precision em um limiar explícito** e com ROC-AUC e PR-AUC comparadas ao baseline.

### 8.4 Interpretação

- Embaralhar `complaints_count` reduz o ROC-AUC em 0,167 e `delivery_delay_days` em 0,143; `resolution_time_days` em 0,024. Todas as demais variáveis (perfil, pedido, tentativas) contribuem de forma nula.
- A chance de detração é multiplicada por ≈ 3,2 a cada desvio-padrão de reclamações, ≈ 3,0 de atraso e ≈ 1,6 de tempo de resolução.
- O modelo reproduz os achados da EDA, o que reforça a confiança em ambos.

### 8.5 O momento da previsão importa

| Cenário | Informação disponível | ROC-AUC (teste) | Precision (Recall ≈ 90%) |
|---|---|---|---|
| A. Pós-pedido | Perfil e pedido | **0,512** (equivale ao acaso) | 0,733 (= baseline) |
| B. Pós-entrega | + logística | 0,790 | 0,804 |
| C. Pós-atendimento | + atendimento | **0,876** | 0,880 |

Perfil e dados do pedido **não permitem prever a nota no momento da compra**. A informação preditiva surge com a logística e o atendimento. Recomenda-se uma arquitetura em dois estágios: pontuar na entrega (B) e reavaliar a cada interação com o atendimento (C). O próximo passo de maior valor é um modelo de **previsão de atraso na expedição**.

### 8.6 Uso prático

Priorização por probabilidade (amostra de teste, 500 pedidos):

| Prioridade | Pedidos | Detratores reais |
|---|---|---|
| Alta (probabilidade ≥ 0,90) | 191 | 95,8% |
| Média (0,59 a 0,90) | 183 | 79,8% |
| Baixa (< 0,59) | 126 | 32,5% |

Fluxo proposto: (1) pontuação automática na entrega e a cada interação; (2) prioridade alta recebe contato proativo, reentrega prioritária ou compensação; (3) média recebe comunicação preventiva; (4) baixa segue o fluxo padrão; (5) comparação mensal entre previsão e NPS efetivo para recalibrar o limiar e retreinar; (6) teste A/B antes de estimar ROI. Como a base tem 74% de detratores, acionar 75% dos pedidos não é seletivo: o valor está em **ordenar por risco** e ajustar o corte à **capacidade operacional**.

Exemplo de inferência (a partir da raiz do projeto):

```bash
python -m src.predict data/desafio_nps_fase_1.csv reports/pedidos_pontuados.csv
```

### 8.7 Artefatos

Os seguintes arquivos são esperados para reproduzir a inferência, mas ainda não estão disponíveis:

- `models/modelo_nps_lr.joblib`: pipeline completo (pré-processamento + Regressão Logística).
- `models/modelo_nps_metadados.json`: limiar, variáveis, métricas, hiperparâmetros e versões das bibliotecas.
- `src/predict.py`: módulo de inferência e priorização.

---

## 9. Estrutura do Repositório

```
tech-challenge-fase1-nps-preditivo/
├── data/
│   └── desafio_nps_fase_1.csv
├── notebooks/
│   └── 01_eda_negocio.ipynb
├── apresentacao/
│   ├── Estratégia Proativa de NPS.pptx
│   ├── Estratégia Proativa de NPS.pdf
│   └── video/
│       └── README.md
├── requirements.txt
├── .gitignore
└── README.md
```

Os arquivos da apresentação estão em `apresentacao/`. A subpasta `apresentacao/video/` está preparada para receber o vídeo final. As pastas e arquivos de modelagem (`data/processed/`, `src/`, `models/`, `reports/` e `notebooks/02_modelo_preditivo.ipynb`) permanecem como próximos entregáveis e não foram criados como arquivos vazios.

---

## 10. Instruções de Reprodução

**Pré-requisitos:** Python 3.10 ou superior e Git. Testado com Python 3.12, pandas 3.0, scikit-learn 1.8 e xgboost 3.4.

```bash
# 1. Clonar o repositório
git clone https://github.com/<seu-usuario>/tech-challenge-fase1-nps-preditivo.git
cd tech-challenge-fase1-nps-preditivo

# 2. Criar e ativar o ambiente virtual
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate

# 3. Instalar as dependências
pip install -r requirements.txt

# 4. Executar a EDA a partir da pasta notebooks/
cd notebooks
jupyter nbconvert --to notebook --execute --inplace 01_eda_negocio.ipynb
```

Também é possível abrir o notebook com `jupyter notebook` e executar célula a célula. Execute-o com a pasta `notebooks/` como diretório de trabalho; o CSV é carregado de `../data/desafio_nps_fase_1.csv`. A reprodução da modelagem e da inferência ficará disponível quando os artefatos citados acima forem adicionados.

---

## 11. Limitações e Riscos

- **Base provavelmente sintética:** a independência entre tempo de entrega e atraso, a inconsistência lógica em 4,8% dos pedidos, a recompra determinística e a taxa de 74% de detratores indicam dados gerados por simulação. Os resultados descrevem a base fornecida e precisam ser revalidados com dados reais.
- **Associação não é causalidade:** o modelo prevê quem tende a ser detrator; só um experimento (teste A/B) mede o efeito de intervir.
- **Sem dimensão temporal:** não há data do pedido, então não foi possível validar fora do tempo nem analisar sazonalidade.
- **Prevalência alta (74%):** limiar e Precision dependem dessa proporção e devem ser recalibrados em outra operação.
- **Viés de resposta:** o NPS reflete apenas quem respondeu à pesquisa.
- **Nota contínua:** a classificação de detrator depende de convenção; a robustez foi testada (ROC-AUC entre 0,85 e 0,90 nas definições alternativas).
- **Um pedido por cliente:** sem histórico para capturar reincidência.
- **Monitoramento:** acompanhar deriva de dados, calibração e desempenho em produção.

### Próximos passos

1. Modelo de previsão de atraso na expedição, para atuar antes da falha.
2. Teste A/B das ações preventivas e estimativa de ROI com custo real de intervenção.
3. Escolha do limiar por custo esperado (custo da ação x valor de reter o cliente).
4. Validação temporal e monitoramento de deriva com dados datados.
5. Em base real, avaliar modelos não lineares com explicabilidade (SHAP) e métodos de inferência causal.

---

## 12. Status dos Entregáveis

| Item | Situação |
|---|---|
| Base de dados e notebook de EDA organizados | Concluído |
| README com respostas aos Requisitos 1 a 3 | Concluído |
| Notebook de modelagem e artefatos reproduzíveis do Requisito 4 | Pendente |
| Apresentação executiva (PPTX e PDF em `apresentacao/`) | Concluído |
| Vídeo executivo (até 5 minutos) | Pendente |

---

## 13. Autores

| Nome | Contato |
|---|---|
| [PREENCHER] | [PREENCHER] |

---

*Projeto desenvolvido para o Tech Challenge da Fase 1 da PosTech AI Scientist (FIAP).*