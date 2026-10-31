# NPS Preditivo: Transformando Dados Operacionais em Lealdade

**Tech Challenge Fase 1 | PosTech AI Scientist (FIAP)** | Case NPS Preditivo

Neste projeto, olhamos para os dados da jornada de compra para entender o que aparece junto com uma experiência melhor ou pior. A análise segue as etapas da metodologia CRISP-DM que fazem sentido para esta entrega.

> **Sobre esta entrega:** aqui você encontra a base, o notebook oficial da EDA ([`notebooks/01_eda_negocio.ipynb`](notebooks/01_eda_negocio.ipynb)) e os materiais executivos. Como a modelagem é opcional no desafio, o foco ficou na análise exploratória; não há modelo treinado nem métricas preditivas para reproduzir.

---

## Sumário

1. [Descrição do Projeto e Objetivos](#1-descrição-do-projeto-e-objetivos)
2. [Resumo Executivo](#2-resumo-executivo)
3. [Entendimento do Negócio (Requisito 1)](#3-entendimento-do-negócio-requisito-1)
4. [Definição da Target (Requisito 2)](#4-definição-da-target-requisito-2)
5. [Descrição da Base de Dados](#5-descrição-da-base-de-dados)
6. [Metodologia CRISP-DM](#6-metodologia-crisp-dm)
7. [Principais Insights da EDA (Requisito 3)](#7-principais-insights-da-eda-requisito-3)
8. [Estratégia preditiva (opcional)](#8-estratégia-preditiva-opcional-proposta-futura)
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

> Quais fatores operacionais aparecem associados à satisfação do cliente e o que a empresa pode investigar para melhorar a experiência antes da pesquisa de NPS?

### Objetivos

- Documentar a visão de negócio e a definição da variável alvo (Requisitos 1 e 2).
- Realizar uma Análise Exploratória (EDA) com foco operacional: fatores associados à satisfação, ocorrência de detratores, comportamento por atraso e perfil de clientes (Requisito 3).
- Discutir uma estratégia preditiva como possibilidade futura; a implementação do modelo é opcional (Requisito 4).
- Produzir material executivo (slides e vídeo) com linguagem gerencial.
- Entregar um repositório organizado e reprodutível.

---

## 2. Resumo Executivo

| Pergunta | Resposta em uma frase |
|---|---|
| Quais fatores estão mais associados ao NPS? | **Atraso na entrega** (rho de Spearman ≈ −0,59) e **reclamações** (≈ −0,49) apresentam as associações negativas mais fortes observadas. |
| Como muda a experiência com atraso? | A nota média observada é **6,86 sem atraso** e **5,55 com um dia de atraso**; aproximadamente 60% do grupo com um dia de atraso é classificado como detrator. |
| Que perfil tem NPS mais alto ou mais baixo? | Não há evidência clara de diferenças consistentes por região ou tempo de relacionamento; a comparação por idade requer cautela. |
| O NPS explica a recompra nesta base? | A recompra coincide perfeitamente com a regra `nps_score >= 8`; esse padrão sugere um artefato e não deve ser interpretado como efeito real de retenção. |
| O modelo preditivo foi implementado? | **Não.** A modelagem é opcional no desafio; esta entrega concentra-se na EDA e propõe validações futuras. |

> **Nota sobre os dados:** há inconsistências que precisam ser validadas com a fonte; não é possível confirmar apenas pelo arquivo se os dados são sintéticos. Os resultados descrevem esta base e devem ser revalidados com dados reais antes de decisões de alto impacto.

---

## 3. Entendimento do Negócio (Requisito 1)

### 3.1 Qual problema de negócio está sendo resolvido?

A **inércia operacional causada pelo NPS reativo**. Como a nota só existe depois que a jornada termina, a empresa não consegue priorizar ações corretivas nem intervir enquanto o cliente ainda pode ser recuperado. O desafio é identificar, a partir de dados que já existem durante a jornada (pedido, entrega, atendimento), quais situações estão associadas à insatisfação e como investigar ações preventivas.

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
| Pricing | Investigar associações entre frete, desconto, parcelamento e satisfação, sem inferir causalidade. |
| Gestão de Produto | Acompanhar reclamações por categoria e perfil de pedido. |
| CRM / Marketing | Acionar recuperação para clientes em risco; evitar segmentações demográficas sem poder discriminante. |
| Diretoria | Acompanhar a experiência com indicadores operacionais e, futuramente, avaliar sinais antecedentes. |

### 3.4 Reflexão estratégica: impacto do NPS

- **Recompra:** na base, a recompra em 30 dias é de 100% entre promotores, 3,8% entre neutros e 0% entre detratores. Como a relação coincide perfeitamente com a regra `nps_score >= 8`, ela é um **artefato provável da base** e não permite inferir retenção, magnitude ou causalidade (ver seção 7).
- **Boca a boca:** promotores recomendam a marca espontaneamente, gerando aquisição orgânica de baixo custo; detratores geram o efeito contrário, amplificado por avaliações públicas.
- **Market share:** reter clientes e atrair novos por indicação permite crescer em participação sem aumentar proporcionalmente o investimento em aquisição.

### 3.5 Indicadores complementares

| Indicador | Por que complementa o NPS |
|---|---|
| OTIF (On-Time In-Full) | Mede entregas no prazo e completas; ajuda a acompanhar o sinal operacional associado à detração nesta amostra. |
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

### Qualidade dos dados (auditoria reproduzível no notebook)

- 2.500 linhas e 19 colunas; sem valores ausentes nem linhas duplicadas completas; `customer_id` e `order_id` são únicos nesta base.
- A auditoria sinaliza **121 pedidos** em que `delivery_delay_days` supera `delivery_time_days` e **35 pedidos** em que o desconto supera o valor do pedido. Os registros são mantidos e destacados para validação com a fonte, sem correção automática.
- Não há base temporal ou histórico repetido por cliente; conclusões sobre sazonalidade e evolução individual não são possíveis com este arquivo.
- Consulte o notebook para as verificações de faixas, identificadores e regra de categoria NPS.


---

## 6. Metodologia CRISP-DM

| Fase | Situação nesta entrega | Evidência |
|---|---|---|
| 1. Entendimento do Negócio | Realizado | Problema, públicos e perguntas de negócio nas seções 1 e 3. |
| 2. Entendimento dos Dados | Realizado | Auditoria, distribuição, associações e limitações no notebook oficial [`notebooks/01_eda_negocio.ipynb`](notebooks/01_eda_negocio.ipynb). |
| 3. Preparação dos Dados | Parcial, restrita à EDA | Criação de categorias analíticas e faixas para visualização; não há pipeline de modelagem. |
| 4. Modelagem | Não implementada | Etapa opcional no enunciado; ver proposta na seção 8. |
| 5. Avaliação | Não implementada | Não há resultados de validação de modelo nesta entrega. |
| 6. Implantação | Não implementada | Não há modelo ou serviço de inferência em produção. |

A análise usa correlação de Spearman para associações monotônicas, proporções de detratores com intervalos de Wilson de 95% e teste de Kruskal–Wallis para comparações exploratórias de grupos. Esses métodos não estabelecem causalidade.

---

## 7. Principais Insights da EDA (Requisito 3)

Os resultados desta seção são reproduzíveis no notebook. As estimativas são descritivas da base fornecida e devem ser interpretadas com as contagens dos grupos e as limitações abaixo.

### Respostas às perguntas do desafio

| Pergunta | Resultado observado |
|---|---|
| **Quais fatores se associam mais à satisfação?** | Atraso na entrega (`rho` de Spearman ≈ **−0,59**) e reclamações (≈ **−0,49**) são as associações negativas mais fortes entre as variáveis operacionais analisadas. Contatos com atendimento (≈ −0,34) e tempo de resolução (≈ −0,19) também se associam negativamente; tentativas de entrega apresentam associação próxima de zero (≈ +0,03). |
| **O que mais se relaciona à detração?** | A taxa observada de detratores sobe de **36,5%** sem atraso (n=277) para **59,7%** com um dia de atraso (n=615), e aumenta nos grupos com mais reclamações. Associação não comprova que atraso ou reclamações, isoladamente, causam a nota. |
| **Existe ponto de ruptura?** | A maior queda da média observada ocorre entre zero (**6,86**) e um dia de atraso (**5,55**). É um sinal para investigação operacional, não um limiar causal; os grupos de atrasos mais longos são pequenos. |
| **Que perfil tem NPS mais alto ou mais baixo?** | Não se observou evidência de diferença por região (Kruskal–Wallis p≈**0,497**) ou tempo de relacionamento (p≈**0,189**). A faixa etária ficou próxima do corte exploratório de 5% (p≈**0,058**); portanto, não afirmamos equivalência entre perfis. |
| **Como interpretar a recompra?** | Na base, recompra coincide perfeitamente com `nps_score >= 8`. Isso sugere uma regra geradora compartilhada; as taxas não são estimativas confiáveis de retenção nem de impacto financeiro. |
| **O modelo foi avaliado?** | Não. A EDA não inclui treinamento, validação ou métricas de modelo. A modelagem é opcional no desafio. |

### Leitura para a operação

1. **Investigar atraso e reclamações primeiro.** São os sinais operacionais com maior associação negativa com o NPS observado. Validar o fluxo e as definições dos dados antes de decidir intervenções.
2. **Monitorar o primeiro dia de atraso.** É onde aparece a maior queda descritiva da amostra; comparar com contagens e intervalos e não comunicar como uma fronteira universal.
3. **Evitar segmentação demográfica sem evidência.** As diferenças por região e tempo de relacionamento não foram estatisticamente claras nesta amostra. Isso não prova que todos os clientes são iguais.
4. **Não usar a recompra desta base para estimar ROI.** A variável está perfeitamente alinhada a uma regra do NPS e pode ter sido derivada dele.

### Definição da categoria e incerteza

A nota é contínua, com valores decimais. Para a análise, adotamos detrator `nps_score <= 6`, neutro `6 < nps_score <= 8` e promotor `nps_score > 8`. A base contém **1.851 detratores (74,0%)**, **448 neutros (17,9%)** e **201 promotores (8,0%)**. Essa é uma convenção explícita para o arquivo, não um arredondamento da nota.

As proporções por atraso e reclamações são acompanhadas de intervalos de Wilson de 95% e número de pedidos. Os intervalos representam incerteza amostral sob as hipóteses do método; não corrigem viés de seleção, problemas de origem dos dados ou confundimento.

---

## 8. Estratégia preditiva (opcional; proposta futura)

O desafio permite encerrar a entrega com a reflexão exploratória. Caso a empresa decida seguir para modelagem, uma hipótese inicial seria classificar risco de detração (`nps_score <= 6`) e comparar o resultado com uma regressão da nota. A escolha final depende do momento da decisão, da disponibilidade real das variáveis e do custo das ações.

Um cuidado essencial é o **momento da previsão**: atraso realizado e dados de atendimento só ficam disponíveis durante ou depois da jornada. Eles não sustentam uma promessa de prever o risco antes da entrega. Para agir antes, seria necessário desenvolver e validar sinais antecedentes, como uma previsão de atraso, usando dados disponíveis naquele momento.

Antes de uma eventual implantação, seria necessário definir a população e o instante de previsão, excluir variáveis posteriores à decisão ou derivadas da satisfação, separar treino e teste sem vazamento, avaliar desempenho contra um baseline e validar o benefício de uma intervenção em teste controlado. **Nenhum modelo, métrica preditiva ou artefato de inferência faz parte desta entrega.**

---

## 9. Estrutura do Repositório

```
tech-challenge-fase1-nps-preditivo/
├── data/
│   └── desafio_nps_fase_1.csv
├── notebooks/
│   └── 01_eda_negocio.ipynb
├── reports/
│   └── figures/               # Gráficos de apoio à apresentação
├── apresentacao/
│   ├── Estratégia Proativa de NPS.pptx
│   ├── Estratégia Proativa de NPS.pdf
│   └── video/
│       └── README.md
├── requirements.txt
├── .gitignore
└── README.md
```

`notebooks/01_eda_negocio.ipynb` é a fonte oficial da EDA. A pasta `reports/figures/` reúne gráficos de apoio à apresentação. Os arquivos da apresentação estão em `apresentacao/`, e a subpasta `apresentacao/video/` está preparada para receber o vídeo final. As pastas e arquivos de modelagem (`data/processed/`, `src/`, `models/` e `notebooks/02_modelo_preditivo.ipynb`) permanecem como próximos entregáveis e não foram criados como arquivos vazios.

---

## 10. Instruções de Reprodução

**Pré-requisitos:** Python 3.10 ou superior e Git. O notebook foi executado com Python 3.12.3, pandas 3.1.0, NumPy 2.5.4, Matplotlib 3.11.2, seaborn 0.13.2 e SciPy 1.18.1.

```bash
# 1. Clonar o repositório
git clone https://github.com/<seu-usuario>/tech-challenge-fase1-nps-preditivo.git
cd tech-challenge-fase1-nps-preditivo

# 2. Criar e ativar o ambiente virtual
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate

# 3. Instalar as dependências
pip install -r requirements.txt

# 4. Executar o notebook oficial da EDA a partir da raiz do projeto
jupyter nbconvert --to notebook --execute --inplace notebooks/01_eda_negocio.ipynb
```

Também é possível abrir `notebooks/01_eda_negocio.ipynb` no Jupyter ou VS Code e executar as células. O notebook localiza a raiz do projeto a partir do diretório de trabalho, lê o CSV em `data/` e salva os gráficos em `reports/figures/`. Todas as análises descritas neste README estão no notebook oficial; não há modelagem ou inferência a reproduzir.

---

## 11. Limitações e Riscos

- **Origem e qualidade:** inconsistências entre colunas e a recompra perfeitamente alinhada a uma regra do NPS são sinais que requerem validação com quem gerou a base. Não é possível confirmar apenas pelo arquivo se os dados são sintéticos.
- **Associação não é causalidade:** as análises são observacionais; não demonstram que uma ação sobre atraso, reclamações ou atendimento causará aumento do NPS.
- **Sem dimensão temporal:** não há data do pedido para avaliar sazonalidade ou validar mudanças ao longo do tempo.
- **Possível viés de resposta:** a nota representa as observações disponíveis; o arquivo não permite avaliar clientes que não responderam.
- **Convenção de categoria:** a target contém notas decimais e detrator/neutro/promotor dependem dos cortes explicitados neste README e no notebook.
- **Grupos pequenos:** as estimativas para faixas raras, como atrasos mais longos, são menos estáveis; consulte as contagens e intervalos apresentados nos gráficos e tabelas.
- **Sem validação de modelo:** não há desempenho preditivo ou benefício operacional medido nesta entrega.

### Próximos passos

1. Validar com a fonte o significado de tempo de entrega, atraso, desconto e recompra; corrigir a documentação da base antes de qualquer uso decisório.
2. Repetir a análise com dados reais, datados e com definição documentada de quem recebeu e respondeu à pesquisa.
3. Se a operação optar por modelagem, definir o momento de previsão e a capacidade de atendimento antes de escolher features e métricas.
4. Medir o efeito das ações preventivas com desenho experimental antes de estimar ROI.

---

## 12. Status dos Entregáveis

| Item | Situação |
|---|---|
| Base de dados e notebook de EDA organizados | Concluído |
| README com respostas aos Requisitos 1 a 3 | Concluído |
| Notebook de modelagem e artefatos reproduzíveis do Requisito 4 (opcional) | Não implementado nesta entrega |
| Apresentação executiva (PPTX e PDF em `apresentacao/`) | Concluído |
| Vídeo executivo (até 5 minutos) | Pendente |

---

## 13. Autores

| Nome | Contato |
|---|---|
| [PREENCHER] | [PREENCHER] |

---

*Projeto desenvolvido para o Tech Challenge da Fase 1 da PosTech AI Scientist (FIAP).*