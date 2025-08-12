# Detecção de Fraudes em Transações Financeiras

Este projeto de Data Science tem como objetivo desenvolver um modelo de machine learning capaz de identificar transações financeiras fraudulentas. A análise utiliza um conjunto de dados sintéticos que simula transações de dinheiro móvel, um cenário comum onde a detecção rápida de fraudes é crucial.

---

### **Contexto e Objetivo**

A fraude financeira é um problema crescente que causa prejuízos significativos para empresas e consumidores. A capacidade de detectar transações fraudulentas em tempo real é uma demanda essencial no setor financeiro. O principal desafio deste projeto é lidar com um conjunto de dados **altamente desbalanceado**, onde as fraudes representam uma minoria ínfima das transações.

O objetivo é construir e avaliar diferentes modelos de classificação para encontrar o que melhor identifica as transações fraudulentas (classe minoritária), otimizando a métrica de **recall** sem sacrificar excessivamente a precisão.

---

### **Fonte dos Dados**

O dataset utilizado é o **"Synthetic Financial Datasets For Fraud Detection (PaySim)"**, que é uma versão sintética de dados de uma empresa de pagamentos móveis.

* **Disponível em:** [Kaggle - Synthetic Financial Datasets](https://www.kaggle.com/code/llabhishekll/fraud-transaction-detection/input)
* **Observação:** O dataset original possui mais de 6 milhões de linhas e ~600MB, portanto, não foi incluído neste repositório para seguir as boas práticas de versionamento de código. Para executar o notebook, é necessário fazer o download a partir do link acima.

---

### **Análise Exploratória de Dados (EDA)**

A análise inicial revelou insights importantes sobre o comportamento das fraudes:

* **Dataset Desbalanceado:** Apenas **0.5%** das transações no conjunto de dados de amostra (e menos de 0.2% no completo) são fraudulentas, confirmando a necessidade de técnicas específicas para lidar com o desbalanceamento.
* **Tipos de Transação:** As fraudes ocorrem **exclusivamente** em transações do tipo `CASH_OUT` e `TRANSFER`. Outros tipos como `PAYMENT`, `DEBIT` e `CASH_IN` não apresentaram nenhum caso de fraude.
* **Padrões de Fraude:** Transações fraudulentas frequentemente envolvem zerar o saldo da conta de origem (`oldbalanceOrg` e `newbalanceOrig`).
* **Correlações:** Foi observada uma forte correlação entre os saldos antigos e novos, tanto na origem quanto no destino, o que é esperado. A variável `isFlaggedFraud` não se mostrou útil na amostra analisada.

---

### **Metodologia e Modelagem**

O fluxo de trabalho para a construção do modelo foi o seguinte:

1.  **Pré-processamento:**
    * A variável categórica `type` foi transformada em variáveis numéricas usando One-Hot Encoding (`pd.get_dummies`).
    * As colunas `nameOrig` e `nameDest`, que são identificadores únicos, foram removidas por não agregarem valor preditivo ao modelo.
    * Linhas com dados nulos foram removidas.

2.  **Divisão dos Dados:**
    * O conjunto de dados foi dividido em 70% para treino e 30% para teste.
    * Foi utilizada a **estratificação** (`stratify=y`) para garantir que a proporção de fraudes e não-fraudes fosse a mesma nos conjuntos de treino e teste, o que é fundamental em problemas desbalanceados.

3.  **Modelos Testados:**
    * `KNeighborsClassifier` (KNN)
    * `DecisionTreeClassifier` (Árvore de Decisão)
    * `LogisticRegression` (Regressão Logística)

4.  **Tratamento do Desbalanceamento:**
    * Para melhorar a capacidade do modelo de identificar a classe minoritária, a Árvore de Decisão foi treinada novamente com o parâmetro `class_weight='balanced'`, que ajusta os pesos das classes de forma inversamente proporcional à sua frequência.

---

### **Resultados**

A performance dos modelos foi avaliada com foco na identificação de fraudes (classe 1).

| Modelo                      | Acurácia | Recall (Fraude) | Precisão (Fraude) | F1-Score (Fraude) |
| --------------------------- | -------- | --------------- | ----------------- | ----------------- |
| KNN                         | 99.39%   | 0.39            | 0.43              | 0.41              |
| Árvore de Decisão           | 99.65%   | 0.57            | 0.72              | 0.63              |
| Regressão Logística         | 99.56%   | 0.17            | 1.00              | 0.30              |
| **Árvore (Balanceada)** | **99.51%** | **0.26** | **0.60** | **0.36** |

**Conclusão da Modelagem:**

A **Árvore de Decisão** padrão apresentou o melhor F1-Score, mostrando um bom equilíbrio entre identificar fraudes (recall) e não classificar transações legítimas como fraude (precisão). Embora a Regressão Logística tenha tido precisão máxima, seu recall foi muito baixo, o que significa que ela deixou de detectar a maioria das fraudes. A versão balanceada da árvore, embora com um recall menor neste caso, é uma técnica importante a ser considerada.

**Features Mais Importantes (Árvore de Decisão):**
A análise de importância de features mostrou que `oldbalanceOrg`, `amount` e `newbalanceOrig` são as variáveis mais determinantes para o modelo.

---

### **Como Executar o Projeto**
 Faça o download do dataset no [Kaggle](https://www.kaggle.com/code/llabhishekll/fraud-transaction-detection/input) e coloque o arquivo `PS_20174392719_1491204439457_log.csv` na pasta raiz do projeto.
 Abra e execute o notebook `deteccao_de_fraudes.ipynb` em um ambiente Jupyter.

---

**Autor:** Gustavo Czarneski    

[LinkedIn] https://www.linkedin.com/in/gustavo-czarneski-245081318/ | [GitHub] https://github.com/gustavoczrk
