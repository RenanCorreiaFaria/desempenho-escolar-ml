# 📊 School Performance Prediction — Machine Learning

> **Estudo de Caso 3 — Case H | MBA em Analytics, Inteligência Artificial e Data Science — FIA**

[🇧🇷 Português](#-português) · [🇺🇸 English](#-english)

---

# 🇧🇷 Português

## 📌 Sobre o projeto

Este projeto aplica **Machine Learning a um problema de previsão de desempenho escolar**, utilizando modelos baseados em árvores de decisão.

O objetivo foi investigar:

1. **Predição:** qual algoritmo apresenta o menor erro na previsão da nota final dos alunos?
2. **Interpretação:** quais características e hábitos mais influenciam as previsões do modelo?

O estudo foi desenvolvido como parte do **Estudo de Caso 3 — Case H (Desempenho Escolar)** do MBA em Analytics, Inteligência Artificial e Data Science da **FIA**.

---

## 🎯 O problema

Uma instituição privada de ensino médio deseja entender quais fatores estão associados ao desempenho final dos alunos, buscando apoiar ações pedagógicas e de suporte.

A base está estruturada na visão aluno, em que cada observação representa um estudante com características relacionadas a:

* hábitos de estudo;
* frequência escolar;
* desempenho acadêmico anterior;
* rotina;
* contexto familiar;
* infraestrutura e acesso a recursos.

O modelo foi desenvolvido para prever a **nota final** e identificar quais variáveis mais contribuem para essa previsão.

---

## 📊 Base de dados

* **1.976 alunos**
* **12 variáveis explicativas**
* Variáveis quantitativas e qualitativas
* Variável-alvo: **nota final**

### Variáveis utilizadas

**Desempenho e estudo**

* Horas de estudo por semana
* Frequência às aulas
* Média de notas anteriores
* Participação em monitoria
* Atividades extracurriculares

**Rotina e hábitos**

* Trabalha além de estudar
* Turno
* Tempo de deslocamento
* Possui transporte próprio

**Contexto familiar**

* Escolaridade dos pais
* Acesso à internet em casa
* Número de irmãos

---

## 🌳 Modelos comparados

Foram comparados oito algoritmos baseados em árvores:

| Modelo               | Abordagem                                               |
| -------------------- | ------------------------------------------------------- |
| Decision Tree        | Árvore de regressão individual                          |
| Random Forest        | Ensemble de árvores treinadas em amostras aleatórias    |
| AdaBoost             | Boosting sequencial                                     |
| Gradient Boosting    | Boosting baseado em árvores                             |
| HistGradientBoosting | Gradient Boosting baseado em histogramas                |
| XGBoost              | Implementação otimizada de Gradient Boosting            |
| LightGBM             | Gradient Boosting otimizado para eficiência             |
| CatBoost             | Boosting com tratamento nativo de variáveis categóricas |

Todos os modelos foram comparados utilizando a mesma divisão de treino/teste e uma estratégia consistente de validação.

---

## 🔬 Metodologia

### 1. Divisão dos dados

A base foi dividida em:

* **80% treino**
* **20% teste externo**

Foi utilizada uma semente fixa (`random_state=123`) para garantir a reprodutibilidade e permitir uma comparação justa entre os modelos.

### 2. Pré-processamento

Para os modelos que necessitam de variáveis codificadas, as variáveis categóricas foram transformadas utilizando **One-Hot Encoding**.

O **CatBoost** foi tratado separadamente, utilizando as variáveis categóricas em seu formato original, por possuir tratamento nativo para esse tipo de variável.

### 3. Random Search — exploração

Os hiperparâmetros dos oito algoritmos foram inicialmente otimizados utilizando:

**`RandomizedSearchCV` + validação cruzada de 5 folds**

Foram testadas **100 combinações aleatórias por algoritmo**.

O objetivo dessa etapa foi explorar o espaço de hiperparâmetros e identificar configurações promissoras.

### 4. Seleção do modelo

Os modelos foram comparados principalmente pelo:

**RMSE médio na validação cruzada**

Também foi considerado um filtro de estabilidade para evitar configurações com indícios de superajuste.

O **CatBoost** apresentou o melhor desempenho na comparação inicial.

### 5. Grid Search — refinamento

Depois de identificar o CatBoost como modelo campeão, foi realizada uma segunda etapa de otimização.

Foi construída uma grade com **405 combinações** ao redor dos hiperparâmetros da melhor configuração encontrada anteriormente.

Essa etapa buscou fazer um refinamento local da solução encontrada pelo Random Search.

---

## 🏆 Resultado

O modelo final foi um **CatBoost**, com:

```text
depth = 3
iterations = 213
learning_rate = 0.0341
l2_leaf_reg = 0.1789
subsample = 0.7342
```

### Desempenho no teste externo

| Métrica | Random Search | Modelo final |
| ------- | ------------: | -----------: |
| RMSE    |        11.411 |   **11.071** |
| MAE     |         8.897 |    **8.625** |
| MAPE    |         22.5% |    **21.2%** |
| R²      |         0.697 |    **0.715** |

O RMSE caiu de **11,411 para 11,071** após o refinamento por Grid Search.

Além disso, **288 das 405 combinações** avaliadas na grade passaram pelo filtro de superajuste.

---

## 🔎 Interpretabilidade

Além da capacidade preditiva, o projeto investigou quais variáveis mais influenciam as previsões.

Foram utilizados três métodos:

* Importância por redução de impureza;
* Permutation Importance;
* **SHAP (SHapley Additive Explanations)**.

Os três métodos produziram rankings bastante semelhantes.

### Principais variáveis

| Variável                   | Importância média SHAP |
| -------------------------- | ---------------------: |
| Média de notas anteriores  |                   6.63 |
| Frequência às aulas        |                   4.41 |
| Escolaridade dos pais      |                   3.93 |
| Participação em monitoria  |                   3.62 |
| Horas de estudo por semana |                   3.54 |

Um dos principais aprendizados foi que **desempenho acadêmico anterior e frequência escolar aparecem entre os fatores mais relevantes para as previsões**, enquanto características relacionadas ao contexto familiar também apresentam contribuição importante.

---

## ⚠️ Limitações

Os resultados devem ser interpretados com cautela.

**Associação não significa causalidade.**

Por exemplo, o fato de a participação em monitoria estar associada a previsões mais altas não significa que a monitoria, isoladamente, seja responsável pelo aumento da nota. Uma conclusão causal exigiria um desenho experimental ou quasi-experimental.

Além disso:

* o modelo possui um RMSE de aproximadamente **11 pontos** em uma escala de 0 a 100;
* o desempenho observado é válido dentro do domínio representado pela base;
* algumas variáveis possuem poucos casos em determinadas categorias;
* o MAPE deve ser interpretado com cuidado em notas baixas.

Portanto, o modelo é mais adequado para **identificar padrões e apoiar a priorização de grupos para ações pedagógicas** do que para tratar a previsão individual como uma nota exata.

---

## 🛠️ Tecnologias

* Python
* Pandas
* NumPy
* Scikit-learn
* XGBoost
* LightGBM
* CatBoost
* SHAP
* Matplotlib
* Seaborn
* Jupyter Notebook

---

## 📁 Estrutura do projeto

```text
case-desempenho-escolar-machine-learning/
│
├── README.md
├── Case_Desempenho_Escolar_Modelo_Projecao_RenanCorreia_GitHub.ipynb
├── data/
│   └── Desempenho_Escolar_Dados-1.txt
│
└── images/
    └── ...
```

---

## 👨‍💻 Autor

**Renan Correia**

MBA em Analytics, Inteligência Artificial e Data Science — FIA

Interesses: **Data Science · Analytics · Growth · CRM · Machine Learning**

---

# 🇺🇸 English

## 📌 About the project

This project applies **Machine Learning to a school performance prediction problem**, using tree-based models.

The study aimed to answer two main questions:

1. **Prediction:** which algorithm achieves the lowest error when predicting students' final grades?
2. **Interpretability:** which characteristics and habits have the greatest influence on model predictions?

The project was developed as part of **Case Study 3 — Case H (School Performance)** in the MBA program in Analytics, Artificial Intelligence and Data Science at **FIA**.

---

## 🎯 Problem statement

A private high school wants to understand which factors are associated with students' final academic performance in order to support educational and student-support initiatives.

The dataset contains student-level observations covering:

* study habits;
* attendance;
* previous academic performance;
* daily routine;
* family context;
* access to resources.

The goal was to predict students' **final grades** and identify the variables that contribute most to those predictions.

---

## 📊 Dataset

* **1,976 students**
* **12 explanatory variables**
* Numerical and categorical features
* Target variable: **final grade**

### Features

**Academic performance and study**

* Weekly study hours
* Class attendance
* Previous average grade
* Tutoring participation
* Extracurricular activities

**Routine and habits**

* Works while studying
* Study shift
* Commute time
* Own transportation

**Family context**

* Parents' education level
* Internet access at home
* Number of siblings

---

## 🌳 Models evaluated

Eight tree-based algorithms were compared:

* Decision Tree
* Random Forest
* AdaBoost
* Gradient Boosting
* HistGradientBoosting
* XGBoost
* LightGBM
* CatBoost

The models were evaluated using the same train/test split and a consistent validation strategy.

---

## 🔬 Methodology

### 1. Train/test split

The dataset was divided into:

* **80% training**
* **20% external test**

A fixed random seed (`random_state=123`) was used to ensure reproducibility and fair model comparison.

### 2. Preprocessing

Categorical variables were transformed using **One-Hot Encoding** for the models that required encoded inputs.

**CatBoost** was handled separately and received categorical variables in their original form through its native categorical feature support.

### 3. Random Search

Hyperparameters were initially optimized using:

**`RandomizedSearchCV` + 5-fold cross-validation**

A total of **100 random configurations were evaluated for each algorithm**.

The goal was to broadly explore the hyperparameter space and identify promising configurations.

### 4. Model selection

Models were primarily compared using:

**Mean cross-validation RMSE**

A stability filter was also applied to avoid configurations showing signs of overfitting.

**CatBoost** achieved the best performance in the initial comparison.

### 5. Grid Search

After selecting CatBoost as the leading model, a second optimization stage was performed.

A grid of **405 configurations** was created around the best configuration found during the Random Search stage.

This allowed the model to be locally refined around a promising region of the hyperparameter space.

---

## 🏆 Results

The final model was a **CatBoost** model with:

```text
depth = 3
iterations = 213
learning_rate = 0.0341
l2_leaf_reg = 0.1789
subsample = 0.7342
```

### External test performance

| Metric | Random Search | Final Model |
| ------ | ------------: | ----------: |
| RMSE   |        11.411 |  **11.071** |
| MAE    |         8.897 |   **8.625** |
| MAPE   |         22.5% |   **21.2%** |
| R²     |         0.697 |   **0.715** |

RMSE decreased from **11.411 to 11.071** after Grid Search refinement.

Additionally, **288 out of 405 configurations** passed the overfitting filter.

---

## 🔎 Model interpretability

The project also investigated which variables contributed most to the model's predictions.

Three interpretability approaches were used:

* Impurity-based feature importance;
* Permutation Importance;
* **SHAP (SHapley Additive Explanations)**.

The three approaches produced highly similar rankings.

### Top features

| Feature                | Mean absolute SHAP |
| ---------------------- | -----------------: |
| Previous average grade |               6.63 |
| Class attendance       |               4.41 |
| Parents' education     |               3.93 |
| Tutoring participation |               3.62 |
| Weekly study hours     |               3.54 |

The analysis suggests that **previous academic performance and attendance are among the most influential features in the model's predictions**, while family-context variables also contribute substantially.

---

## ⚠️ Limitations

The results should be interpreted carefully.

**Association does not imply causation.**

For example, the association between tutoring participation and higher predicted grades does not mean that tutoring alone causes an increase in academic performance. Establishing a causal effect would require an experimental or quasi-experimental design.

Additional limitations include:

* approximately **11 points of RMSE** on a 0–100 grading scale;
* model performance is tied to the population represented in the dataset;
* some categorical groups have relatively few observations;
* MAPE should be interpreted carefully for low grades.

Therefore, the model is better suited to **identifying patterns and supporting the prioritization of student groups for educational interventions** than to treating an individual prediction as an exact grade.

---

## 🛠️ Technologies

* Python
* Pandas
* NumPy
* Scikit-learn
* XGBoost
* LightGBM
* CatBoost
* SHAP
* Matplotlib
* Seaborn
* Jupyter Notebook

---

## 📁 Project structure

```text
case-desempenho-escolar-machine-learning/
│
├── README.md
├── Case_Desempenho_Escolar_Modelo_Projecao_RenanCorreia_GitHub.ipynb
├── data/
│   └── Desempenho_Escolar_Dados-1.txt
│
└── images/
    └── ...
```

---

## 👨‍💻 Author

**Renan Correia**

MBA in Analytics, Artificial Intelligence and Data Science — FIA

Interests: **Data Science · Analytics · Growth · CRM · Machine Learning**
