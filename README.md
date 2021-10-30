# Previsão de Inadimplência de Faturamento

#### Aluno: [Breno Ingwersen Santos] (https://github.com/Breno94)
#### Orientador: [Dr. Leonardo Alfredo Forero Mendoza] (https://github.com/leofome8)

---

Trabalho apresentado ao curso [BI MASTER](https://ica.puc-rio.ai/bi-master) como pré-requisito para conclusão de curso e obtenção de crédito na disciplina "Projetos de Sistemas Inteligentes de Apoio à Decisão".

---

### Resumo


O Contas a Receber (CAR) é uma base de fechamento mensal que contém todas as dívidas ativas da empresa. Portanto, seu saldo total representa um indicativo do quão bem a empresa consegue recuperar o crédito fornecido. O presente trabalho visa a criação de um classificador binário para classificar faturas de acordo com a probabilidade de não serem pagas e, portanto, entrarem na base do CAR, ou serem pagas integralmente.

### Abstract


The Accounts Receivable (AR) is a data base that is generated every end of the month that contains all the company's active debts. Thus, the total open amount of this data base indicates how well the company is able to collect all the credit sold. The present work consists of the development of a binary classifier to classify the invoices according to its probability of not being paid, thus entering the AR data base, or being fully paid.

### 1. Introdução


O Contas a Receber (CAR) é uma das principais bases de fechamento mensal, que contempla todas as dívidas ativas da companhia. Sendo o cliente da área de Telecom, os registros da base representam faturas atuais e históricas que não foram pagas ou foram pagas parcialmente por meio de acordos e parcelamentos. As dívidas pertencem tanto a clientes do tipo PJ (pessoa jurídica), quanto PF (pessoa física), representando os segmentos B2B e B2C, isto é, Business to Business e Business to Customer, respectivamente. 

O objetivo da presente modelagem é a criação de um classificador binário capaz de prever se as faturas do faturamento corrente irão ou não entrar na base do CAR, isto é, se o determinado cliente não pagará sua fatura em dia ou pagará respectivamente. O enfoque do trabalho serão os clientes B2C, pois representam - em quantidade - a maior parte da base de faturamento, sendo a eles emitidas faturas de valores mais baixos. São, portanto, clientes de mais fácil contato e comportamento mais previsível. Clientes B2B, isto é, empresas, muitas vezes realizam ajuste de contas e acordos de pagamento que envolvem o setor jurídico durante as prestações. Por esses motivos, suas taxas de adimplência oscilam intensamente ao longo do ano e muitas vezes dependem da saúde financeira da própria companhia.

### 2. Modelagem

A primeira etapa da modelagem envolveu a identificação das bases de dados necessárias. Como fontes de dados, o trabalho utilizou as bases de CAR de fechamento de março a junho de 2021, o histórico de faturamento de janeiro a junho de 2021, a base de dados mestre dos clientes e a base de pagamentos. Foi construída uma query em SQL Server que relaciona as bases de faturamento e CAR para criar os flags de inadimplência (1) e adimplência (0) respectivamente. 

Durante a construção da query, foram empregados conceitos de engenharia de atributos (Feature Engineering) para a criação de novos atributos como, por exemplo, o ticket médio dos últimos três meses da conta, diferença percentual entre faturamentos, identificação de dívida histórica aberta em atraso, perfil de pagamento, entre outros. Cada atributo foi desenvolvido empregando conceitos de negócios da área de cobrança além de insights do próprio aluno oriundos da sua experiência na área. O objetivo da etapa de engenharia de atributos é a identificação de uma maior quantidade de atributos relevantes ao problema para auxiliar os modelos treinados a melhor predizer o comportamento para a determinada fatura. 

A criação das bases de dados para treino e teste foi seguida da sua análise exploratória de dados (Exploratory Data Analysis). A AED consiste foi fundamental para identificar valores faltantes (Missing Values), padrões e tendências nos dados, correlações entre os atributos e necessidade de balanceamento. Durante AED foram utilizados módulos de visualização de dados como Matplotlib e Seaborn para gerar matrizes de correlação, pairplots e histogramas.

Durante a etapa de AED foi identificada a necessidade do pré-processamento dos dados - parte do trabalho já havia sido feita na query como a conversão de rótulos categóricos de texto em número. Durante a etapa de pré-processamento, foi feito o balanceamento da base de treino para garantir uma mesma quantidade de dados de cada classe, normalização de atributos contínuos e one-hot encoding de atributos categóricos. Finalmente, a base de treino foi dividida em 80% para treino e 20% para validação. Vale ressaltar que foi construída uma base de teste apartada para simular a performance do modelos na etapa de produção.

Com as bases de treino e validação prontas, iniciou-se o treino de diversos classificadores: Random Forest, AdaBoost, Gradient Boosting, ExtraTrees, Logistic Regression, Extreme Gradient Boosting (XGB) e Support Vector Machine. A etapa de treino dos classificadores foi seguida da avaliação da sua performance na base de validação e análise de importância de atributos. Como tentativa de melhoria da performance dos modelos, foram construídos metaclassificadores que realizam predições em cima das predições de um conjunto de classificadores. Como metaclassificadores, houve a modelagem de Hard Voting e Stacking de 3 níveis. Finalmente, foi treinado um conjunto de redes neurais densas (Deep Neural Networks) utilizando a biblioteca Keras alterando hiperparâmetros como número de camadas ocultas e número de neurônios.

Os modelos treinados foram salvos utilizando a biblioteca Pickle a qual converte objetos Python em fluxo de dados para armazenamento em disco. Essa etapa consiste na simulação de um fluxograma real de modelagem preditiva no qual o modelo treinado é armazenado e posteriormente carregado na fase de produção. Finalmente, os modelos foram utilizados para realizar previsões em cima da base de teste para medir suas performances finais e identificar o classificador a ser utilizado no pipeline final.

### 3. Resultados

Durante a etapa de treino, todos os modelos obtiveram resultados semelhantes. Aquele que obteve o maior destaque foi o XGBoost dada a sua maior acurácia de 86,4% e menor tempo de treino. O modelo de menor performance foi a regressão logística com apenas 84,9% de acurácia. Os demais modelos obtiveram acurácias na faixa de 85,3 a 85,8%. As redes neurais obtiveram acurácias na faixa de 85,7 a 86,0% se aproximando ao XGBoost. Contudo, o foi identificado a partir de visualização gráfica que as redes estavam sofrendo overfitting da base de treino dado o fato da curva de acurácia da base de treino estar acima da curva da base de validação.

A etapa final de aplicação dos modelos na base de teste apresentou um resultado interessante: A acurácia de todos os modelos reduziu em cerca de 3 a 5 pontos percentuais com exceção da regressão logística que reduziu somente 2,3 pontos percentuais. Tal fato fez com que sua acurácia fosse superior aos demais modelos, apesar do fato desse classificador ter ocupado a última posição durante a etapa de treino. Portanto, pode-se afirmar que a regressão logística foi capaz de melhor generalizar seus parâmetros para realizar previsões em tempo real em uma base nunca vista. 

### 4. Conclusões

A metodologia aprendida em sala de aula foi aplicada em um problema real de previsão. A partir dos conhecimentos obtidos o aluno foi capaz de desenvolver a base de dados utilizando tabelas e relacionando os dados, além de reproduzir uma análise exploratória e etapas de pré-processamento e, finalmente, treinar diferentes modelos obtendo uma acurácia média final na base de teste na faixa de 80,8 a 82,4%. O modelo com a maior capacidade de generalização obtido foi a regressão logística, o qual poderá ser aplicado em um pipeline de dados para realizar predições em tempo real da base de faturamento corrente. 

Como próximos passos, o aluno poderá buscar novas bases de dados e aplicar engenharia de atributos visando a criação de novos atributos para que otimizar o treino dos modelos e amplificar sua capacidade generalizadora. Além de novos atributos de dados, o aluno poderá realizar uma posterior etapa de otimização de hiperparâmetros através do teste de diversas combinações de hiperparâmetros, buscando incrementar a capacidade generalizadora dos modelos. Para essa etapa seria necessário um conjunto de hardware de maior capacidade de processamento.

Finalmente, o projeto foi apresentado para o time de negócios, que demonstrou interesse na solução proposta para sua aplicação em conjunto com a solução de agente virtual. A solução poderá ser implementada para priorizar o envio de mensagens automáticas afim de lembrar possíveis clientes inadimplentes da data de vencimento da fatura.

---

Matrícula: 192.110.224

Pontifícia Universidade Católica do Rio de Janeiro

Curso de Pós Graduação *Business Intelligence Master*
