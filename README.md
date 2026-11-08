# Data Analytics — Health Data

Projeto acadêmico da disciplina **Data Analytics (PjBL)** sobre fatores associados à readmissão hospitalar de pacientes com diabetes em até 30 dias após a alta. O projeto evolui da ingestão e preparação dos dados (RA1), passa por uma demonstração de processamento de eventos em streaming (RA2) e chega à análise descritiva, visualização e classificação supervisionada (RA3).

**Pergunta central:** quais características demográficas, clínicas e de utilização dos serviços hospitalares aparecem associadas à readmissão em até 30 dias?

As análises são exploratórias e educacionais. Os dados são históricos e observacionais; associações não demonstram causalidade nem devem ser usadas para decisões clínicas individuais.

## Etapas do projeto

| Etapa | Escopo | Artefato principal |
|---|---|---|
| RA1 — Ingestão e preparação | Aquisição, inspeção da qualidade, padronização de ausências, limpeza, validação e exportação da base preparada. | `HealthData.ipynb` |
| RA2 — Streaming | Demonstração local com Apache Spark Structured Streaming, arquivos JSON simulados e agregação em janelas temporais. | Seção de streaming em `HealthData.ipynb` e arquivo de apoio em `docs/HealthDataRA2.pdf` |
| RA3 — Análise e aprendizado de máquina | Estatísticas descritivas, gráficos interpretados e modelos de classificação por Regressão Logística. | `notebooks/RA3_Analysis_MachineLearning.ipynb` |

## Dados

O projeto utiliza **Diabetes 130-US Hospitals for Years 1999–2008**, com encontros hospitalares de 130 hospitais dos Estados Unidos. Um registro representa um atendimento; a mesma pessoa pode aparecer em vários encontros.

- Arquivo original: `diabetic_data.csv`, obtido pelo KaggleHub a partir do dataset [Diabetes Hospital Readmission Dataset](https://www.kaggle.com/datasets/razanihababdellatif/diabetes-hospital-readmission-dataset).
- Fonte original: [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/296/diabetes+130-us-hospitals+for-years+1999-2008).
- Base preparada utilizada na RA3: `data/processed/diabetic_data_clean.csv` (101.766 linhas e 48 colunas, conforme o notebook).

### RA1 — Ingestão e preparação

A inspeção inicial registrou 101.766 encontros e 50 atributos. O notebook padroniza `?` como valor ausente, verifica duplicidades e ajusta os campos `encounter_id` e `patient_nbr` para tipos próprios de identificadores. A documentação do RA1 registra alta proporção de ausências em `weight` (98.569 registros; 96,86%), `medical_specialty` (49.949; 49,08%), `payer_code` (40.256; 39,56%), `max_glu_serum` (96.420) e `A1Cresult` (84.748). O fluxo prepara a base para as etapas seguintes e cria o alvo binário `readmitido_30_dias`: 1 para `readmitted = <30`; 0 para `>30` ou `NO`.

O fluxo documentado inclui obtenção da fonte, inspeção de dimensões, colunas, tipos e ausências, avaliação de duplicidades, limpeza/preparação, validações e exportação do CSV. A verificação inicial não encontrou IDs `encounter_id` repetidos nem linhas integralmente duplicadas. A base é estruturada e tabular; relatórios clínicos e anotações médicas não são campos separados disponíveis nesta fonte. Variáveis de interesse incluem idade, gênero, diagnósticos, duração da internação, uso anterior de serviços, medicações e situação de readmissão.

O CSV preparado já está no repositório. Para repetir a ingestão desde a fonte, o notebook acessa o Kaggle via KaggleHub; essa etapa requer conexão com a internet e pode solicitar configuração de acesso ao Kaggle.

### RA2 — Demonstração de streaming

A seção de streaming de `HealthData.ipynb` usa Spark Structured Streaming em modo local. Ela gera cinco eventos **artificiais**, envia um arquivo JSON a cada três segundos e agrega os dados em janelas fixas não sobrepostas de 10 segundos. A consulta conta os atendimentos e soma os casos com readmissão em até 30 dias; utiliza watermark de um minuto e publica atualizações no console. Na execução documentada, as três janelas somaram cinco eventos, dos quais três foram marcados como readmissões precoces.

Essa demonstração é uma simulação técnica, não um fluxo ao vivo de dados hospitalares. A execução do notebook de streaming requer Java 17 instalado e disponível por `JAVA_HOME`; no macOS, a seção do notebook localiza o Java 17 com `/usr/libexec/java_home`.

### RA1 — Perguntas e escopo analítico

As perguntas definidas na etapa inicial investigam a relação entre readmissão precoce e: (1) tempo de permanência; (2) número de internações anteriores; e (3) mudança de medicamentos e quantidade de diagnósticos. A RA1 teve como foco obter, compreender, avaliar a qualidade e preparar os dados. A análise comparativa e o modelo preditivo passaram a ser implementados nas etapas seguintes.

### RA3 — Análise descritiva, gráficos e classificação

O notebook `notebooks/RA3_Analysis_MachineLearning.ipynb` analisa a base preparada. A variável-alvo é `readmitido_30_dias`: 90.409 registros (88,84%) sem readmissão precoce e 11.357 (11,16%) com readmissão precoce. O notebook calcula frequências, médias, medianas, desvios-padrão e taxas por grupo; também produz visualizações por duração da internação, faixa etária, uso de serviços, medicamentos e diagnóstico principal.

Para a previsão, a RA3 formula um problema de classificação binária e compara Regressão Logística original com uma versão que usa `class_weight="balanced"`. As variáveis categóricas são convertidas em indicadores one-hot, e os modelos são avaliados em um conjunto de teste separado. Resultados registrados para o modelo balanceado:

| Métrica da classe de readmissão | Resultado |
|---|---:|
| Acurácia | 65,01% |
| Precisão | 16,83% |
| Revocação | 54,21% |
| F1-score | 0,2569 |

A revocação indica que pouco mais da metade das readmissões reais no teste foi identificada, enquanto a precisão baixa reflete muitos falsos positivos. O balanceamento trouxe apenas ganhos marginais em relação ao modelo original. Os resultados devem ser interpretados considerando o desequilíbrio entre classes, os dados ausentes, o período histórico (1999–2008), o contexto de hospitais dos Estados Unidos e a possível repetição de pacientes.

## Como executar

### 1. Requisitos

- Python 3.10 ou superior recomendado.
- Git.
- Para a seção RA2 de streaming: Java/JDK 17 e `JAVA_HOME` configurado.
- Para baixar novamente os dados brutos: internet e acesso ao Kaggle quando solicitado pelo KaggleHub.

### 2. Preparar o ambiente

No Terminal, clone o repositório, crie um ambiente virtual e instale as dependências:

```bash
git clone https://github.com/HelenLauren/Data-analytics.git
cd Data-analytics
python3 -m venv .venv
source .venv/bin/activate  # macOS/Linux
# No Windows PowerShell, use: .venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Inicie o JupyterLab:

```bash
jupyter lab
```

Se o comando `jupyter` não estiver disponível, instale JupyterLab no ambiente virtual com `python -m pip install jupyterlab` e tente novamente.

### 3. Rodar RA1 e RA2

1. Abra `HealthData.ipynb` no JupyterLab ou VS Code.
2. Para repetir a preparação, execute as células de ingestão e limpeza do início do notebook. A saída preparada é gravada em `data/processed/diabetic_data_clean.csv`.
3. Para executar a demonstração RA2, continue até a seção de Spark Structured Streaming e execute as células em ordem. Confirme a existência dos eventos simulados e das agregações de janelas no console. A execução do streaming reinicializa `streaming_demo/input` e `streaming_demo/checkpoint`, que são pastas temporárias geradas localmente.

### 4. Rodar RA3

1. Confirme que `data/processed/diabetic_data_clean.csv` existe. O notebook RA3 procura esse caminho relativo à raiz do repositório ou a partir da pasta `notebooks`.
2. Abra `notebooks/RA3_Analysis_MachineLearning.ipynb`.
3. Execute as células em ordem. Os gráficos são exibidos no notebook; as células finais apresentam métricas e matrizes de confusão dos classificadores.
4. Confira a leitura inicial da base (101.766 linhas e 48 colunas), a distribuição da variável-alvo, as visualizações, as métricas de teste e a matriz do classificador balanceado `[[12001, 6082], [1040, 1231]]` (linhas = classe real; colunas = prevista).

## Estrutura do repositório

```text
Data-analytics/
├── README.md
├── requirements.txt
├── HealthData.ipynb                         # RA1 e demonstração de streaming RA2
├── notebooks/
│   └── RA3_Analysis_MachineLearning.ipynb
├── data/
│   └── processed/
│       └── diabetic_data_clean.csv
├── docs/
│   ├── RA1_Data_Analytics_HealthData.pdf
│   ├── HealthDataRA2.pdf
│   └── Health_Data_Documentacao_RA3.pdf
├── results/                                 # resultados e materiais do projeto
└── streaming_demo/                          # entrada e checkpoint gerados durante a execução
```

As pastas `streaming_demo/input` e `streaming_demo/checkpoint` são recriadas pelo notebook e ignoradas pelo Git.

## Documentação das entregas

Os PDFs fornecidos para as três etapas estão em `docs/`:

- [RA1 — documentação de ingestão e preparação](docs/RA1_Data_Analytics_HealthData.pdf)
- [RA2 — material PDF de apoio](docs/HealthDataRA2.pdf)
- [RA3 — documentação de análise, visualização e aprendizado de máquina](docs/Health_Data_Documentacao_RA3.pdf)

## Tecnologias

- Python, Pandas e NumPy para ingestão e preparação.
- Matplotlib para visualizações.
- scikit-learn para Regressão Logística e avaliação de classificação.
- KaggleHub para obtenção do dataset.
- Apache Spark Structured Streaming para a demonstração de eventos.
- Jupyter Notebook/JupyterLab para execução interativa.

## Entregáveis e verificações

- `HealthData.ipynb`: ingestão, qualidade, preparação e demonstração do fluxo Spark.
- `notebooks/RA3_Analysis_MachineLearning.ipynb`: análises descritivas, gráficos, treinamento e avaliação dos classificadores.
- `data/processed/diabetic_data_clean.csv`: base preparada para as análises posteriores.
- `docs/`: documentação em PDF das três etapas do trabalho.

Para conferir a reprodução, valide as dimensões e o alvo da base preparada; no RA2, confira as três janelas e cinco eventos simulados; no RA3, confira os gráficos, as métricas e a matriz de confusão indicados acima.

## Equipe e disciplina

- **Projeto:** Health Data / Readmission Rates
- **Disciplina:** Data Analytics — Projeto Baseado em Problemas (PjBL)
- **Instituição:** Pontifícia Universidade Católica do Paraná (PUCPR)
- **Curso e turma:** Sistemas de Informação • 6º período
- **Professora:** Marina de Lara
- **Integrantes:** Bruno Viccenzo Mazetto, Helen Lauren Bonato e Letícia Bastias Fabri

## Referências

- [Kaggle — Diabetes Hospital Readmission Dataset](https://www.kaggle.com/datasets/razanihababdellatif/diabetes-hospital-readmission-dataset)
- [UCI Machine Learning Repository — Diabetes 130-US Hospitals for Years 1999–2008](https://archive.ics.uci.edu/dataset/296/diabetes+130-us-hospitals+for-years+1999-2008)
- [KaggleHub documentation](https://github.com/Kaggle/kagglehub)
- [Apache Spark Structured Streaming guide](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)
- [scikit-learn LogisticRegression documentation](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html)
