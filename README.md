[![author](https://img.shields.io/badge/author-LeandroMinervino-red.svg)](https://www.linkedin.com/in/leandro-minervino-b469681b/) [![](https://img.shields.io/badge/python-3.7.12+-blue.svg)](https://www.python.org/downloads/release/python-365/) [![GPLv3 license](https://img.shields.io/badge/License-GPLv3-blue.svg)](http://perso.crans.org/besson/LICENSE.html) [![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)]()


![alt text](https://user-images.githubusercontent.com/48839817/149339672-676506a7-f87d-4263-a91b-5efb3c768811.png)


# Churn Prediction

Esse projeto tem como objetivo demonstrar como criar um modelo de machine learning com tunning de hiperparâmetros em um dataset de Churn rate.

Churn rate, ou simplesmente churn, representa a taxa de evasão da sua base de clientes. Em serviços como Spotify ou Netflix, ela representaria a taxa de cancelamento de assinaturas.

Ela é de extrema importância para a administração e sua análise ao longo do tempo pode mostrar que existe algum problema que deve ser atacado.

Churn também pode ser usado para identificar potenciais cancelamentos, com um tempo de antecedência, e promover ações direcionadas para tentar reter tais clientes. Essa métrica deve receber atenção pelo fato de que o Custo de Aquisição de Cliente (CAC) é normalmente mais alto que o custo para mantê-los. Ou seja, um alto valor para o churn rate é o que não desejamos.

## Aquisição dos Dados

Os dados utilizados neste projeto foram originalmente disponibilizados na plataforma de ensino da IBM Developer, e tratam de um problema típico de uma companhia de telecomunicações. O dataset completo pode ser encontrado neste [link](https://raw.githubusercontent.com/carlosfab/dsnp2/master/datasets/WA_Fn-UseC_-Telco-Customer-Churn.csv) .


## Dicionário de Variáveis




| Variável         | Dicionário                                                                                                |
|------------------|-----------------------------------------------------------------------------------------------------------|
| customerID       |  Id do usuário                                                                                            |
| gender           |  Gênero (female, male)                                                                                    |
| SeniorCitizen    |  Usuário idoso ou não (1, 0)                                                                              |
| Partner          |  O consumidor possui um parceiro(a) (Yes, No)                                                             |
| Dependents       |  O consumidor possui dependentes? (Yes, No)                                                               |
| tenure           |  Número de meses que o consumidor manteve-se como cliente                                                 |
| PhoneService     |  O consumidor possui linha telefônica? (Yes, No)                                                          |
| MultipleLines    |  O consumidor possui múltiplas linhas telefônicas? (Yes, No, No phone service)                            |
| InternetService  |  Provedor de internet do consumidor (DSL, Fiber optic, No)                                                |
| OnlineSecurity   |  O consumidor possui segurança online? (Yes, No, No internet service)                                     |
| OnlineBackup     |  O consumidor possui backup online? (Yes, No, No internet service)                                        |
| DeviceProtection |  O consumidor possui proteção ao dispositivo? (Yes, No, No internet service)                              |
| TechSupport      |  O consumidor possui assistência técnica? (Yes, No, No internet service)                                  |
| StreamingTV      |  O consumidor possui serviço de streaming de TV? (Yes, No, No internet service)                           |
| StreamingMovies  |  O consumidor possui serviço de streaming de filmes? (Yes, No, No internet service)                       |
| Contract         |  Tipo de contrato do consumidor (Month-to-month, One year, Two year)                                      |
| PaperlessBilling |  Conta digital? (Yes, No)                                                                                 |
| PaymentMethod    |  Método de pagamento (Electronic check, Mailed check, Bank transfer (automatic), Credit card (automatic)) |
| MonthlyCharges   |  Total cobrado mensalmente ao consumidor                                                                  |
| TotalCharges     |  Cobrança total ao consumidor                                                                             |
| Churn            |  O consumidor desistiu do produto? (Yes or No)                                                            |



## Tratamento da base

Por se tratar de uma base desbalanceada e com a variável alvo binária é necessário balancear o dataset e normalizar as variáveis não categórigas.
Primeiramente dividi o dataset em treino e teste. Para o balanceamento e posterior utilização nos modelos de machine learning utilizei três métodos de sampling:

![image](https://user-images.githubusercontent.com/48839817/148216159-456b560d-4f26-42a2-acca-0638200259ed.png)

A parte de standarização das variáveis foi incorporada dentro de um pipeline encapsulado dentro de uma função que gera as estatísticas dos modelos de Machine Learning.


## Machine Learning

Por se tratar de um modelo de classificação com aprendizado de máquina supervisionado optei pelos seguintes modelos para análise: LogisticRegression, DecisionTreeClassifier, RandomForestClassifier, LinearSVC , Adaboost, XGBClassifier e LGBMClassifier. Todos esses modelos foram encapsulados na supracitada função e utilizados com seus parâmetros padrão e randomstate = 42.
Como mencionado a parte de tratamento das variáveis foi incorporada nessa função. Ela fornece prints com as principais estatísticas para esses modelos.
Para cada método de sampling foi rodada essa função. Um exemplo de parte do output dela:

![image](https://user-images.githubusercontent.com/48839817/149372209-d893e4d8-39a6-4176-b0ad-df8b871b44e5.png)

![image](https://user-images.githubusercontent.com/48839817/149372279-89eb202b-94ff-46c6-ade2-f4e36aafde55.png)


Pensando nessa métrica e avaliando os resultados entre as três técnicas de sampling o Adaboost apresenta os melhores resultados em geral:. O melhor resultado sendo com Smote:


| AdaBoostClassifier |                      |
|--------------------|----------------------|
| Accuracy:          | 0.6952200662565073   |
| Precision:         | 0.4591937069813176 |
| Recall:            | 0.8324420677361853    |
| F1:                | 0.5918884664131813  |
| Roc_auc:           | 0.7390303122186082   |



## Tunning dos hiperparâmetros

Após selecionar o modelo base (Adaboost) procedemos ao tunning utilizando o dataset sem tranformção das variáveis numéricas pois tanto o Adaboost quanto o XGboost que também irei otimizar são baseados em algoritmos de árvore que lidam bem com variáveis não normalizadas. Também foram utilizados as bibliotecas StratifiedKFold e GridSearchCV para otimização:

![image](https://user-images.githubusercontent.com/48839817/149375345-a20dbb92-75d3-4e45-8aa0-042dc05e8d7a.png)



## Conclusão

A escolha da métrica de avaliação de um modelo de aprendizado de máquina deve ser atrelada ao problema que se quer atacar.
Por se tratar de uma um projeto de churn queremos minimizar a presença de falsos negativos - quando se trata de um churn mas classificamos como not churned.
Por isso, a métrica principal que levei em conta na escolha dos modelos foi Recall - mas as outras foram observadas também. O resultado foi o seguinte:


![image](https://user-images.githubusercontent.com/48839817/149375766-4980cc9f-f760-4320-8ffa-c525a3a7669d.png)

Para efeitos de comparação rodei também um modelo com XGboost. Os resultados são os que seguem:

![image](https://user-images.githubusercontent.com/48839817/149389210-8bad3e5e-665e-47c0-911f-66abda591c61.png)


Como podemos ver ambos os modelos se comportaram de maneira semelhante. A escolha nesse caso seria sobre o XGB dado o seu custo computacional.



Outras conclusões:

* Apesar de relativamente limpo a fase exploratória demonstrou a necessidade de limpezas na base
* Essa fase também revelou a necessidade de balancear o dataset.
* Apesar de proceder à Standarização de algumas variáveis para facilitar a testagem dos modelos base dentro do pipeline essa fase não se faz necessária dentro dos modelos escolhidos
* Mesmo com a escolha e tunning dos hiperparâmetros no modelo AdaBoost ele não se mostrou suficientemente melhor que o modelo XGboost - que tem melhor performance computacional.
* O XGboost é o modelo escolhido nessa análise tanto pela performance de suas métricas quanto pelo poder computacional requerido. Isso mostra que mesmo a partir de uma base segura pode ser interessante testar modelos próximos (caso possível) - em termos algorítimicos - com otimização de seus hiperparâmetros para fazer uma verificação dupla.


## Software & Bibliotecas

O Projeto utilizou Python 3 e as seguintes bibliotecas:

-   [Pandas](http://pandas.pydata.org/)
-   [Seaborn](https://seaborn.pydata.org/index.html)
-   [Matplotlib](https://matplotlib.org/)
-   [sklearn](https://scikit-learn.org/stable/)
-   [imbalanced-learn](https://imbalanced-learn.org/stable/)
-   [scikitplot](https://scikit-plot.readthedocs.io/en/stable/Quickstart.html)
-   [datetime](https://docs.python.org/3/library/datetime.html)
-   [numpy](https://numpy.org/)
-   [lightgbm](https://lightgbm.readthedocs.io/en/latest/)
-   [xgboost](https://xgboost.readthedocs.io/en/stable/)
-   [scikitplot](https://scikit-plot.readthedocs.io/en/stable/Quickstart.html)


**Meus Links:**

* [LinkedIn](https://www.linkedin.com/in/leandro-minervino-b469681b/)
* [Colab](https://colab.research.google.com/drive/1-nWd1q0R9RiVZ5cqOaQl9YhSBSBAX_C_?usp=sharing)



## Outros Projetos Meus:


* **[Scrap sites de notícia](https://github.com/leandrominer85/Scrap_sites_noticias)**

* **[Políticas Raciais no ensino superior](https://github.com/leandrominer85/Pol-tica-Racial-no-Ensino-Superior-2009-2019-)**
 
* **[Disaster-Response-Pipelines](https://github.com/leandrominer85/Disaster-Response-Pipelines)**

* **[Dados do Airbnb Lisboa](https://github.com/leandrominer85/Dados-do-Airbnb-Lisboa/blob/main/README.md)**

* **[Panorama COVID-19](https://github.com/leandrominer85/Panorama_Covid-19)**
