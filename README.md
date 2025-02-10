# Desafio Técnico
Desafio entregue para um processo seletivo utilizando o notebook do Google Colab.

## Visão Geral
Para otimizar a receita de Food Delivery e aumentar a competitividade do
App, é fundamental aprimorar as estratégias de comissionamento dos nossos
parceiros. Atualmente, o desafio envolve reduzir a comissão cobrada de
parceiros de alto AOV (Average Order Value) que utilizam o plano Full Service
Área Completa, sem comprometer a receita absoluta mensal do Food Delivery.
O objetivo final é manter a saúde financeira da plataforma e, ao mesmo tempo,
atrair mais parceiros para a nossa solução de logística completa.

## Objetivo Estratégico

Aumentar a penetração da nossa logística completa entre os restaurantes
atualmente cadastrados no App. Quando gerenciamos a logística dos
restaurantes, temos maior controle sobre o nível de serviço oferecido ao cliente
final, o que melhora a satisfação do consumidor, fortalece a nossa proposta de
valor e eleva as barreiras contra a concorrência.

## Desafio Específico

O seu objetivo é propor diferentes cenários estratégicos para alcançar o
objetivo mencionado anteriormente (reduzir a comissão cobrada de parceiros
de alto AOV (Average Order Value) que utilizam o plano Full Service Área
Completa, sem comprometer a receita absoluta mensal do Food Delivery).

Algumas possibilidades incluem:

- Ajustar a comissão de outros planos, como o marketplace, equilibrando
os impactos financeiros.
- Revisar as comissões de acordo com a performance individual dos
parceiros, utilizando critérios como volume de vendas, fidelização e
contribuição para a receita global.

## Dados Disponíveis

Todos os dados apresentados são fictícios e foram criados
exclusivamente para este estudo de caso. Na base de dados você irá encontrar
as seguintes informações:

- reference_month: mês de referência dos dados
- frn_id: identificador do restaurante
- group_name: identificador da marca
- performance_classification: classificação do restaurante
- business_model: tipo de plano do parceiro
  - FULL_SERVICE: App realiza as entregas (logística App)
  - MARKETPLACE: restaurante realiza as entregas (logística própria)
- orders: total de pedidos realizados pelo restaurante
- gmv_basket: valor pago pelo usuário no pedido (sem a taxa de entrega)
- delivery_fee: taxa de entrega do pedido
- paid_delivery_fee: taxa de entrega do pedido paga pelo usuário
- commission: valor pago pelo restaurante em comissão para o App
- service_fee: valor pego pelo usuário em taxa de serviço para o App
- anticipation_fee: valor pago pelo restaurante em taxa de antecipação de
recebíveis para o App
- olp_fee: valor pago pelo restaurante para o App pela transação online
- delivery_cost: custo da entrega para o App
- occurrences_cost: custo de ocorrências com o pedido para o App

## Entrega Esperada

O material final deve incluir uma apresentação concisa, de até 5 slides,
destinada a uma audiência de líderes de negócio com pouca familiaridade
técnica.

O conteúdo deve destacar:

- Análise dos dados históricos de comissionamento.
- Projeções de receita e performance dos cenários propostos (pelo menos
2 cenários distintos).
- Recomendações para ajustes estratégicos, considerando o impacto
esperado na receita mensal e na penetração da logística.
- A apresentação deve ser clara, objetiva e baseada em estimativas ou
simulações que mostrem o potencial de sucesso da estratégia proposta.
Também deverá ser enviado o notebook/script com todas os tratamentos e
análises realizadas.
Análise para um desafio técnico de processo seletivo de um aplicativo de restaurantes.

# Insights e Solução

## Análise histórica

A análise histórica do comissionamento revelou que mensalmente a média mensal de comissão paga por restaurantes Key Accounts (clientes estratégicos) era 5x superior a restaurantes Long Tail (clientes de nicho).

Todavia, analisando todo o período da base (3 meses), no montante geral, os clientes Key Accounts (contas estratégicas) e Long Tails (restaurantes de nicho) que estão no modelo de negócio Marketplace pagam cerca de 3x mais em comissão do que o modelo Full Service.

Uma vez sendo o principal objetivo levar os restaurantes para o modelo Full Service (logística de entrega gerenciada pelo App), levantei algumas questões: deveriam existir tantos Key Accounts no modelo marketplace? Ademais, se Long Tails geram tanto comissionamento, não existem restaurantes que poderiam ser elencados como Key Accounts?

A partir desses questionamento identifiquei duas principais oportunidades:

- Revisão do critério de classificação de performance (key account/long tail);
- Estruturar uma nova estratégia de comissão

## Solução

### Revisão do critério de classificação de performance

Por padrão, um cliente estratégico era classificado basicamente por ter um alto AOV (Average order value), porém, ao notar potencial de fazer upgrade de vários clientes long tail e talvez possíveis downgrades de alguns key accounts, foi estruturado um modelo estatístico para realizar essa classificação levando em conta diversos dados (Regressão Logística Múltipla - 89% de acurácia).

Usando a técnica de análise de feature importances, estes dados foram elencados para o modelo:

- orders (pedidos totais)
- olp_fee (taxa para transações online)
- gmv_basket (valor pago pelo cliente, sem entrega)
- net_revenue (receita total [commission+service_fee+anticipation_fee+olp_fee])
- commission (valor pago em comissão para o app)
- paid_delivery_fee (taxa de entrega final paga pelo usuário, pode ter desconto)
- delivery_fee (taxa de entrega original)
- gross_margin (lucro bruto [net_revenue-costs])
- aov (average order value por restaurante [gmv_basket+paid_delivery_fee])

Com isso, mais de 9 mil restaurantes foram reclassificados, sendo que os Key Accounts diminuiram 56% enquanto Long Tails cresceram 8%. Mas quando voltamos a analisar o quanto contribuem para o App em comissão, os Key Accounts **dobraram** o valor médio mensal de comissão, ficando agora **10x acima** da média mensal dos Long Tails, que não tiveram alterações relevantes na sua média.

### Nova estratégia de comissão

Agora com a nova classificação estatística dos key accounts (KA) e long tails (LT), podemos pensar em aplicar uma nova estratégia de comissionamento.

Essa estratégia baseia-se em: dar descontos relevantes para KAs, enquanto LTs têm um incremento sutil no valor, mas que no montante compensam os descontos cedidos. Vale ressaltar que apenas KAs que já estão no modelo de negócio Full Service vão ser contemplados com este desconto, assim como apenas LTs que estão no marketplace irão receber os incrementos.

Para estimar o possível impacto de receita desta nova estratégia, foi criado um segundo modelo estatístico onde basicamente ele é alimentado por um novo valor de comissão - este já com o desconto ou incremento para os devidos restaurantes - e prevê um novo valor de receita, utilizando uma Regressão Linear Simples (Coeficiente de determinação[R²]: 99%).

Com o resultado dessa base de dados com a nova comissão simulada, entra em ação o terceiro modelo estatístico, que leva diversas variáveis em consideração para tentar prever a probabilidade de um restaurante migrar de modelo de negócio (se tornar Full Service[FS] ou Marketplace[MKTP] - Regressão Logística Múltipla com 94% de acurácia)

### Resultado

Foram realizados diversos cenários combinando descontos e incrementos distintos. Foram elencados 4 cenários finais para avaliação:

- Cenário A
  - Desconto (FS): 	-25%
  - Incremento (MKTP): +10%
  - Impacto R$: +2,3% (FS -5,2% || MKTP +4,4%)
  - Penetrabilidade* (+FS): -18,4%

- Cenário B
  - Desconto (FS): -10%
  - Incremento (MKTP): +10%
  - Impacto R$: +3,7% (FS +0,7% || MKTP +4,5%)
  - Penetrabilidade* (+FS): -16,9%

- Cenário C
  - Desconto (FS): -40%
  - Incremento (MKTP): +20%
  - Impacto R$: +5,5% (FS -11,2% || MKTP +10%)
  - Penetrabilidade* (+FS): -19,5%

- Cenário D (este cenário avalia o resultado contrariando a lógica da nova estratégia)
  - Desconto (FS): -5%
  - Incremento (MKTP): +30%
  - Impacto R$: +13,2% (FS +2,8% || MKTP +16%)
  - Penetrabilidade* (+FS): -18%

\* Penetrabilidade: foi constatado um comportamento contra-intuitivo do modelo que avalia a possibilidade de mudança dos dados do modelo de negócio - mesmo havendo desconto para clientes FS, sempre é notado uma transição significativa para o modelo MKTP. Esse enviesamento pode indicar uma alta correlação entre as variáveis independentes utilizadas no modelo.

## Possível evolução da solução

Por limitação de tempo e alguns desafios de tecnologia e infraestrutura, não foi possível implementar todas as ideias que surgiram, mas alguns próximos passos incluem:

- Criação de um modelo de CHURN, pois será um atributo essencial para complementar os modelos já criados;
- Otimizar os modelos atuais:
  - Diminuir volume de variáveis
  - Garantir menos correlação entre variáveis independentes
  - testar variáveis diferentes que entreguem mais resultado com menos complexidade
- Construir um modelo melhor para avaliar o impacto da penetrabilidade da nova estratégia de comissão;
- Testar modelos de Machine Learning (foi testado Auto ARIMA, mas obtive resultados ruins devido à dificuldade de treinar o modelo com o máximo de dados possível).
