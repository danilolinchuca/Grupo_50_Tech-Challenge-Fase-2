# Tech Challenge — Fase 2 | POSTECH Data Analytics


---

## 1. Identificação

| Campo | Valor |
|---|---|
| Turma | 2DTATBB |
| Grupo | Grupo 50 |
| Data de entrega | 10/10/2026 |

### Integrantes

| Nome completo | RM | E-mail |
|---|---|---|
| Danilo da Costa Linchuca | RM377841 | danilo_linchuca@yahoo.com.br |
| Fabio Andre Ribeiro Cortez | RM377820 | farcortez@gmail.com.br |
| | | |
| | | |
| | | |

---

## 2. Links da entrega

Estes três links são **obrigatórios** e devem ser idênticos aos do PDF de submissão.

| Item | Link |
|---|---|
| Repositório | https://github.com/danilolinchuca/Grupo_50_Tech-Challenge-Fase-2 |
| Vídeo executivo (≤ 5 min) | https://drive.google.com/file/d/1cdHyayVz_2fk-qB6ZKLkYYOA2WKRUfGR/view?usp=sharing |
| Apresentação | https://github.com/danilolinchuca/Grupo_50_Tech-Challenge-Fase-2/blob/main/docs/apresentacao_executiva.pdf |

---

## 3. O problema

O projeto tem como objetivo desenvolver um modelo de aprendizado de máquina capaz de classificar solicitantes de cartão de crédito como **bons ou maus pagadores**, utilizando informações pessoais, financeiras e socioeconômicas.

A proposta busca apoiar a instituição financeira na análise de risco de crédito, identificando padrões associados ao comportamento de pagamento e contribuindo para um processo mais padronizado e orientado por dados.

### Variável alvo

A variável `TARGET` representa a classificação do cliente:

- `0` = Bom pagador
- `1` = Mau pagador

Para a modelagem, clientes que apresentaram pelo menos um registro nos status `2`, `3`, `4` ou `5` foram classificados como maus pagadores (`TARGET = 1`). Os demais foram classificados como bons pagadores (`TARGET = 0`).

O histórico de crédito foi utilizado para construção da variável-alvo, mas não foi utilizado diretamente como variável preditora, evitando vazamento de informação (data leakage).

A definição adotada resulta em forte desbalanceamento entre as classes, com 1,69% de maus pagadores na base final.

### Dataset

| Campo | Valor |
|---|---|
| Fonte | Base disponibilizada para o Tech Challenge — Fase 2 |
| Linhas × colunas | `application_record.csv`: 438.557 × 18; `credit_record.csv`: 1.048.575 × 3 |
| Período / versão | Base disponibilizada para o Tech Challenge — Fase 2 |
| Licença de uso | Não especificada na documentação do desafio |

Descrição das variáveis:

| Variável | Tipo | Descrição |
|---|---|---|
| `AGE_YEARS` | Numérica | Idade do cliente em anos |
| `EMPLOYED_YEARS` | Numérica | Tempo de emprego em anos |
| `AMT_INCOME_TOTAL` | Numérica | Renda total informada |
| `CNT_FAM_MEMBERS` | Numérica | Quantidade de integrantes do grupo familiar |
| `CODE_GENDER_M` | Binária | Indicador de gênero após transformação |
| `FLAG_OWN_REALTY_Y` | Binária | Indicador de posse de imóvel após transformação |
| `CNT_CHILDREN` | Numérica | Quantidade de filhos |

---

## 4. Como reproduzir

```bash
git clone https://github.com/danilolinchuca/Grupo_50_Tech-Challenge-Fase-2.git
cd Grupo_50_Tech-Challenge-Fase-2

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

pip install -r requirements.txt
jupyter notebook
```

Baixe os datasets e coloque os arquivos brutos em `data/raw/` (os dados **não** são versionados — veja `data/README.md`).

```text
data/raw/application_record.csv
data/raw/credit_record.csv
```

Depois execute os notebooks nesta ordem:

| # | Notebook | O que faz |
|---|---|---|
| 1 | `notebooks/01_eda.ipynb` | Análise exploratória |
| 2 | `notebooks/02_preprocessamento.ipynb` | Limpeza, transformação e preparação dos dados |
| 3 | `notebooks/03_modelagem.ipynb` | Treino e comparação dos modelos |
| 4 | `notebooks/04_avaliacao.ipynb` | Métricas, importância de variáveis e conclusões |

**Semente fixa:** `RANDOM_STATE = 42`, declarada na primeira célula de cada notebook.
A divisão dos dados utiliza agrupamento por perfil cadastral, evitando que registros associados ao mesmo perfil sejam distribuídos simultaneamente entre treino, validação e teste.

---

## 5. Resultados

| Modelo | Acurácia | Precisão | Recall | F1 | AUC-ROC |
|---|---|---|---|---|---|
| KNN | 96,50% | 1,98% | 2,20% | 2,08% | 0,5016 |
| Regressão Logística | 62,31% | 1,60% | 35,16% | 3,07% | 0,5065 |
| Random Forest | 94,93% | 4,52% | 9,89% | 6,21% | 0,6109 |

**Modelo escolhido:** Random Forest — selecionado pelo maior F1-Score para a classe de maus pagadores (`TARGET = 1`), com F1 de 6,21%.

**Métricas priorizadas:** F1-Score da classe de maus pagadores, por combinar precisão e recall em um cenário de forte desbalanceamento. A AUC-ROC e o Recall também foram analisados para complementar a avaliação.

O diagnóstico dos dados identificou 9.728 perfis cadastrais distintos, com 33.320 clientes pertencentes a perfis repetidos. Foram identificados 269 perfis com `TARGET` conflitante entre IDs do mesmo perfil.

A divisão final resultou em:

| Conjunto | Registros | Maus pagadores |
|---|---:|---:|
| Treino | 25.743 | 427 (1,66%) |
| Validação | 5.347 | 98 (1,83%) |
| Teste | 5.367 | 91 (1,70%) |

O diagnóstico confirmou zero sobreposição de perfis entre os conjuntos.

No conjunto de teste, o Random Forest obteve a seguinte matriz de confusão:

| | Predito bom | Predito mau |
|---|---:|---:|
| **Real bom** | 5.086 | 190 |
| **Real mau** | 82 | 9 |

Assim, o modelo identificou corretamente 9 dos 91 maus pagadores do conjunto de teste e classificou 190 bons pagadores como maus.

---

## 6. Principais conclusões

1. A divisão por grupos de perfil cadastral evita que o mesmo perfil esteja simultaneamente em treino, validação e teste, proporcionando uma avaliação mais conservadora da generalização para perfis não vistos.
2. O Random Forest foi selecionado pelo critério definido no projeto, apresentando o maior F1 para a classe de maus pagadores. A Regressão Logística apresentou o maior Recall, enquanto o Random Forest também apresentou a maior AUC-ROC.
3. O Random Forest apresentou AUC-ROC de 0,6109, indicando capacidade discriminativa superior à dos demais modelos avaliados, embora ainda limitada. A acurácia deve ser interpretada com cautela devido ao forte desbalanceamento da base.
4. No Random Forest, `AGE_YEARS`, `EMPLOYED_YEARS` e `AMT_INCOME_TOTAL` foram as variáveis com maior importância observada, com importâncias de aproximadamente 0,1601, 0,1562 e 0,1316, respectivamente. Essa importância representa contribuição para o comportamento do modelo e não implica causalidade.

### Limitações e próximos passos

Entre as principais limitações estão o forte desbalanceamento entre as classes, a base final condicionada à existência de registros nas duas fontes, o possível viés de seleção decorrente dessa integração, a construção do `TARGET` por uma regra específica do projeto, a presença de perfis cadastrais repetidos e conflitos de `TARGET`, além da capacidade discriminativa limitada observada nas AUCs.

O modelo deve ser interpretado como **ferramenta de apoio à análise de crédito**, e não como único critério de decisão.

---

## 7. Estrutura do repositório

```
.
├── data/          dados brutos (raw) e tratados (processed) — não versionados
├── notebooks/     análise em ordem numerada
└── docs/          apresentação executiva
```

Detalhes e convenções em [`ESTRUTURA.md`](ESTRUTURA.md).
Antes de enviar, percorra o [`CHECKLIST.md`](CHECKLIST.md).

---

## 8. Tecnologias

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-learn
- Jupyter Notebook
- Google Colab
- Machine Learning supervisionado
- K-Nearest Neighbors (KNN)
- Regressão Logística
- Random Forest
- Git
- GitHub
