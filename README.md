# Comparação Experimental de Algoritmos de Classificação no Dataset Diamonds

Este repositório contém o código, os dados, os resultados e o artigo científico desenvolvidos para o Projeto da 2ª VA da disciplina de Inteligência Artificial da UFRPE/UABJ.

O trabalho compara algoritmos supervisionados de classificação aplicados ao dataset **Diamonds**, utilizando a variável `cut` como alvo. Foram avaliados seis modelos principais: Árvore de Decisão, KNN, Naive Bayes, Regressão Logística, SVM Linear e MLP.

## Objetivo

O objetivo do projeto é comparar o desempenho de diferentes algoritmos de classificação sob uma mesma metodologia experimental, utilizando:

- validação cruzada estratificada com 10 folds;
- busca de hiperparâmetros com `GridSearchCV`;
- pipelines de pré-processamento para evitar vazamento de dados;
- métricas macro, especialmente F1-score macro, devido ao desbalanceamento das classes;
- análise crítica dos resultados, incluindo estabilidade, balanceamento, custo computacional e limitações.

## Dataset

O dataset utilizado foi o **Diamonds**, disponível no Kaggle.

| Característica | Valor |
|---|---:|
| Nome do dataset | Diamonds |
| Fonte | Kaggle |
| Identificador | `ulrikthygepedersen/diamonds` |
| Tarefa | Classificação multiclasse |
| Variável alvo | `cut` |
| Número de exemplos | 53.940 |
| Número de colunas originais | 10 |
| Número de atributos usados em `X` | 8 |
| Número de classes | 5 |
| Classe majoritária | `Ideal` |
| Exemplos da classe majoritária | 21.551 |
| Percentual da classe majoritária | 39,95% |

Classes da variável alvo:

- `Fair`
- `Good`
- `Very Good`
- `Premium`
- `Ideal`

A variável `cut` apresenta desbalanceamento, com predominância da classe `Ideal` e baixa frequência da classe `Fair`. Por isso, a métrica principal adotada foi o **F1-score macro**.

## Pré-processamento

As principais etapas de pré-processamento foram:

- preservação da base original;
- verificação de valores ausentes;
- verificação de duplicatas;
- verificação de dimensões inválidas em `x`, `y` e `z`;
- remoção da variável `price` dos atributos preditores;
- separação entre atributos numéricos e categóricos;
- codificação one-hot para variáveis categóricas;
- uso de `StandardScaler` nos modelos sensíveis à escala;
- uso de `Pipeline` e `ColumnTransformer` para evitar vazamento de dados entre treino e validação.

A coluna `price` foi removida para evitar que os modelos dependessem diretamente de uma variável comercial fortemente associada à qualidade percebida do diamante.

## Modelos avaliados

Foram avaliados os seguintes algoritmos:

| Modelo | Estratégia principal |
|---|---|
| Árvore de Decisão | Modelo interpretável, não linear, sem necessidade de escalonamento |
| KNN | Modelo baseado em distância, com escalonamento |
| Naive Bayes | Modelo probabilístico simples |
| Regressão Logística | Modelo linear com regularização |
| SVM Linear | Modelo linear baseado em margem |
| MLP | Rede neural multicamadas |

Também foi realizado um experimento complementar com **SVM RBF**, utilizado para verificar se o desempenho inferior do SVM Linear estava relacionado à restrição de fronteiras lineares.

## Validação experimental

O procedimento experimental utilizou:

- separação inicial estratificada em treino/busca e teste final;
- 20% dos dados reservados para teste final;
- 10-fold cross-validation estratificado no conjunto de busca;
- `GridSearchCV` para seleção de hiperparâmetros;
- `random_state=42` para reprodutibilidade;
- F1-score macro como métrica principal.

Dimensões utilizadas:

| Conjunto | Quantidade de exemplos |
|---|---:|
| Conjunto de busca / treino | 43.152 |
| Conjunto de teste final | 10.788 |

## Hiperparâmetros avaliados

| Modelo | Combinações |
|---|---:|
| Árvore de Decisão | 96 |
| KNN | 5 |
| Naive Bayes | 3 |
| Regressão Logística | 6 |
| SVM Linear | 6 |
| MLP | 3 |

Todos os modelos atenderam ao requisito mínimo de três combinações de hiperparâmetros.

## Resultados principais

| Modelo | Acc. CV | Bal. Acc. CV | Prec. Macro CV | F1 Macro CV | F1 Teste |
|---|---:|---:|---:|---:|---:|
| Árvore de Decisão | 0,7636 ± 0,0046 | 0,7522 ± 0,0092 | 0,7660 ± 0,0055 | 0,7580 ± 0,0068 | 0,7584 |
| MLP | 0,7643 ± 0,0078 | 0,7691 ± 0,0088 | 0,7292 ± 0,0070 | 0,7453 ± 0,0078 | 0,7422 |
| KNN | 0,6650 ± 0,0063 | 0,6581 ± 0,0065 | 0,6098 ± 0,0078 | 0,6213 ± 0,0068 | 0,6243 |
| Regressão Logística | 0,6549 ± 0,0049 | 0,5372 ± 0,0076 | 0,6487 ± 0,0119 | 0,5576 ± 0,0088 | 0,5472 |
| SVM Linear | 0,6237 ± 0,0062 | 0,5511 ± 0,0101 | 0,5094 ± 0,0083 | 0,5089 ± 0,0110 | 0,4952 |
| Naive Bayes | 0,4614 ± 0,0068 | 0,4534 ± 0,0103 | 0,4008 ± 0,0066 | 0,3697 ± 0,0079 | 0,3822 |

## Principais conclusões

A **Árvore de Decisão** apresentou o melhor equilíbrio geral entre desempenho, estabilidade e generalização. O resultado de F1 macro foi praticamente igual na validação cruzada e no teste final, sugerindo boa capacidade de generalização.

A **MLP** obteve desempenho muito próximo ao da Árvore de Decisão e apresentou melhor comportamento por classe, sendo uma alternativa competitiva quando o objetivo é equilibrar o desempenho entre categorias.

O **KNN** teve desempenho intermediário. Apesar do escalonamento, seu resultado pode ter sido afetado pela dimensionalidade gerada pela codificação one-hot e pela sobreposição entre classes próximas.

A **Regressão Logística** e o **SVM Linear** tiveram desempenho inferior aos modelos não lineares, indicando que a separação da variável `cut` provavelmente depende de relações mais complexas entre atributos.

O **Naive Bayes** apresentou o menor desempenho, possivelmente pela violação da hipótese de independência condicional entre atributos.

O experimento complementar com **SVM RBF** obteve F1 macro de aproximadamente `0,7261` no teste final, reforçando que o baixo desempenho do SVM Linear não deve ser interpretado como limitação geral do SVM, mas como consequência da configuração linear adotada.




Ou abra o notebook no Google Colab.

## Dependências principais

As principais bibliotecas utilizadas foram:

- Python
- pandas
- numpy
- scikit-learn
- matplotlib
- seaborn
- joblib
- imbalanced-learn

Exemplo de `requirements.txt`:

```text
pandas
numpy
scikit-learn
matplotlib
seaborn
joblib
imbalanced-learn
jupyter
```

## Artigo

O artigo foi escrito em LaTeX no formato SBC e está localizado na pasta `artigo/`.

Para compilar no Overleaf, envie os seguintes arquivos:

- `main.tex`
- `referencias.bib`
- `sbc-template.sty`
- pasta `figuras/`

## Observações metodológicas

A avaliação foi feita com 10-fold cross-validation estratificado, conforme a proposta do trabalho. O conjunto de teste final foi usado apenas como verificação complementar da generalização dos melhores modelos selecionados.

A escolha da métrica F1 macro foi motivada pelo desbalanceamento da variável alvo. A acurácia isolada poderia favorecer modelos com melhor desempenho nas classes majoritárias, como `Ideal`, sem refletir adequadamente o desempenho nas classes menos frequentes.

## Limitações

As principais limitações do experimento foram:

- uso de grades de hiperparâmetros limitadas por custo computacional;
- tratamento da variável `cut` como categoria nominal, apesar de sua natureza ordinal;
- remoção da variável `price`, que reduz dependência comercial, mas também retira informação preditiva;
- aumento de dimensionalidade causado pela codificação one-hot;
- ausência de uma ablação controlada específica para medir isoladamente o efeito do balanceamento.

## Trabalhos futuros

Como trabalhos futuros, sugere-se:

- ampliar a busca de hiperparâmetros;
- testar modelos ensemble, como Random Forest, Gradient Boosting ou XGBoost;
- avaliar estratégias adicionais de balanceamento;
- investigar o impacto da variável `price`;
- testar abordagens de classificação ordinal para explorar a ordem natural das classes de qualidade do corte.

## Autor

José Yrikes de Oliveira Feitosa  
Engenharia de Computação  
Universidade Federal Rural de Pernambuco — Unidade Acadêmica de Belo Jardim  
Disciplina: Inteligência Artificial
