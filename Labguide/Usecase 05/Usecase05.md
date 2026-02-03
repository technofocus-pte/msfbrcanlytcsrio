**Introdução**

A Contoso, uma empresa multinacional do setor varejista, busca
modernizar sua infraestrutura de dados para aprimorar as análises de
vendas e geográficas. Atualmente, seus dados de vendas e de clientes
estão dispersos em diversos sistemas, dificultando a obtenção de
insights por parte de seus analistas de negócios e desenvolvedores
cidadãos. A empresa planeja consolidar esses dados em uma plataforma
unificada utilizando o Microsoft Fabric para viabilizar consultas
cruzadas, análises de vendas e relatórios geográficos.

Neste laboratório, você assumirá o papel de um engenheiro de dados na
Contoso, encarregado de projetar e implementar uma solução de armazém de
dados usando o Microsoft Fabric. Você começará configurando um espaço de
trabalho do Fabric, criando um armazém de dados, carregando dados do
Azure Blob Storage e realizando tarefas analíticas para fornecer
insights aos tomadores de decisão da Contoso.

Embora muitos conceitos do Microsoft Fabric possam ser familiares a
profissionais de dados e análises, aplicá-los em um novo ambiente pode
ser um desafio. Este laboratório foi projetado para percorrer, passo a
passo, um cenário completo desde aquisição ao consumo de dados, a fim de
criar uma compreensão básica da experiência do usuário do Microsoft
Fabric, suas diversas experiências e pontos de integração, bem como as
experiências de desenvolvedores profissionais e cidadãos do Microsoft
Fabric.

**Objetivos**

- Configurar um espaço de trabalho do Fabric com o período de avaliação
  ativado.

- Estabelecer um novo armazém chamado WideWorldImporters no Microsoft
  Fabric.

- Carregar os dados no espaço de trabalho Warehouse_FabricXX usando um
  pipeline do Data Factory.

- Gerar as tabelas dimension_city e fact_sale no armazém de dados.

- Preencher as tabelas dimension_city e fact_sale com dados do Azure
  Blob Storage.

- Criar clones das tabelas dimension_city e fact_sale no armazenamento.

- Clonar as tabelas dimension_city e fact_sale para o esquema dbo1.

- Desenvolver um procedimento armazenado para transformar os dados e
  criar a tabela aggregate_sale_by_date_city.

- Gerar uma consulta usando o criador visual de consultas para mesclar e
  agregar dados.

- Utilizar um bloco de notas para consultar e analisar dados da tabela
  dimension_customer.

- Incluir os armazéns WideWorldImporters e ShortcutExercise para
  consultas cruzadas.

- Executar uma consulta T-SQL nos bancos de dados WideWorldImporters e
  ShortcutExercise.

- Habilitar a integração visual do Azure Maps no portal de
  administração.

- Gerar gráficos de colunas, mapas e tabelas para o relatório de análise
  de vendas.

- Criar um relatório usando dados do conjunto de dados
  WideWorldImporters no hub de dados OneLake.

- Remover o espaço de trabalho e os itens associados.

# **Exercício 1: Criar um espaço de trabalho do Microsoft Fabric**

## **Tarefa 1: Iniciar sessão na sua conta do Power BI e se inscrever para [o teste gratuito do Microsoft Fabric.](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial)**

1.  Abra seu navegador, acesse a barra de endereços e digite ou cole o
    seguinte URL: +++https://app.fabric.microsoft.com/+++ e pressione a
    tecla **Enter.**

> ![](./media/image1.png)

2.  Na janela do **Microsoft Fabric**, insira as credenciais atribuídas
    e clique no botão **Submit**.

> ![](./media/image2.png)

3.  Em seguida, na janela **Microsoft**, digite a senha e clique no
    botão **Sign in.**

> ![](./media/image3.png)

4.  Na janela **Stay signed in?**, clique no botão **Yes.**

> ![](./media/image4.png)

5.  Você será redirecionado para a página inicial do Power BI.

> ![](./media/image5.png)

## Tarefa 2: Criar um espaço de trabalho

Antes de trabalhar com dados no Fabric, crie um espaço de trabalho com a
versão de avaliação do Fabric ativada.

1.  No painel **Workspace**, selecione **+** **New workspace**.

> ![A screenshot of a computer Description automatically
> generated](./media/image6.png)

2.  Na aba **Create a workspace,** insira os seguintes detalhes e clique
    no botão **Apply**.

[TABLE]

> ![](./media/image7.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

3.  Aguarde a conclusão da implementação. Isso leva de 1 a 2 minutos.
    Quando seu novo espaço de trabalho abrir, ele deverá estar vazio.

> ![](./media/image9.png)

## Tarefa 3: Criar um armazém no Microsoft Fabric

1.  Na página **Fabric**, selecione **+ New item** para criar um
    Lakehouse e selecione **Warehouse**

> ![A screenshot of a computer Description automatically
> generated](./media/image10.png)

2.  Na caixa de diálogo **New warehouse**, digite
    +++**WideWorldImporters+++** e clique no botão **Create**.

> ![](./media/image11.png)

3.  Quando o provisionamento for concluído, a página inicial do
    **WideWorldImporters** será exibida.

> ![](./media/image12.png)

# **Exercício 2: Ingerir dados em um armazém no Microsoft Fabric**

## Tarefa 1: Ingerir dados em um armazém de dados

1.  Na página inicial do armazém da **WideWorldImporters,** selecione
    **Warehouse_FabricXX** no menu de navegação à esquerda para retornar
    à lista de itens do espaço de trabalho.

> ![](./media/image13.png)

2.  Na página **Warehouse_FabricXX**, selecione + **New item**. Em
    seguida, clique em **Pipeline** para visualizar a lista completa de
    itens disponíveis em **Get data.**

> ![](./media/image14.png)

3.  Na caixa de diálogo **New** **pipeline**, no campo **Name**, digite
    +++**Load Customer Data+++** e clique no botão **Create.**

> ![](./media/image15.png)

4.  Na página **Load Customer Data**, navegue até a seção **Start
    building your data pipeline** e clique em **Pipeline activity**.

> ![](./media/image16.png)

5.  Navegue e selecione **Copy data** na secção **Move
    &** **transform**.

> ![A screenshot of a computer Description automatically
> generated](./media/image17.png)

6.  Selecione a atividade **Copy data** **1** recém-criada na tela de
    design para configurá-la.

> **Observação:** Arraste a linha horizontal na tela de design para ter
> uma visão completa de vários recursos.
>
> ![A screenshot of a computer Description automatically
> generated](./media/image18.png)

7.  Na aba **General**, no campo **Name,** insira +++**CD Load
    dimension_customer+++**

> ![A screenshot of a computer Description automatically
> generated](./media/image19.png)

8.  Na página **Source**, selecione a opção **Connection** no menu
    suspenso. Selecione **Browse all** para ver todas as fontes de dados
    disponíveis.

> ![](./media/image20.png)

9.  Na janela **Get data**, pesquise por +++**Azure Blobs+++** e, em
    seguida, clique no botão **Azure Blob Storage.**

> ![](./media/image21.png)

10. No painel **Connection settings** que aparece no lado direito,
    configure as seguintes opções e clique no botão **Connect.**

- No campo **Account name or URL**, insira
  +++**https://fabrictutorialdata.blob.core.windows.net/sampledata/+++**

- Na seção **Connection credentials**, clique no menu suspenso em
  **Connection** e selecione **Create new connection**

- No campo **Connection name,** insira +++**Wide World Importers Public
  Sample+++**

- Defina o **Authentication kind** como **Anonymous**

> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

11. Altere as configurações restantes na página **Source** da atividade
    de cópia da seguinte forma para acessar os arquivos .parquet em
    **https://fabrictutorialdata.blob.core.windows.net/sampledata/WideWorldImportersDW/parquet/full/dimension_customer/\*.parquet**

12. Nos campos de texto **File path**, forneça:

- **Container:** +++**sampledata+++**

- **File path - Directory:** +++**WideWorldImportersDW/tables+++**

- **File path - File name:** +++**dimension_customer.parquet+++**

- No menu suspenso **File format,** escolha **Parquet** (se não
  conseguir ver **Parquet**, digite na caixa de pesquisa e selecione-o).

> ![](./media/image23.png)

13. Clique em **Preview data** no lado direito da configuração do **File
    path** para garantir que não haja erros e, em seguida, clique em
    **Close.**

> ![](./media/image24.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

14. Na aba **Destination**, insira as seguintes configurações.

[TABLE]

> **Observação: Ao adicionar a conexão como warehouse
> WideWorldImporters, adicione-a a partir do catálogo do OneLake
> navegando até a opção all option.**
>
> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image27.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image28.png)

15. Na faixa de opções, selecione **Run**.

> ![A screenshot of a computer Description automatically
> generated](./media/image29.png)

16. Na caixa de diálogo **Save and run?,** clique no botão **Save and
    run.**

> ![](./media/image30.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image31.png)

17. Acompanhe o progresso da atividade de cópia na página **Output** e
    aguarde a sua conclusão.

> ![A screenshot of a computer Description automatically
> generated](./media/image32.png)

# Exercício 3: Criar tabelas em um armazém de dados

## Tarefa 1: Criar tabela em um armazém de dados

1.  Na página **Load Customer Data**, clique no espaço de trabalho
    **Warehouse_FabricXX** na barra de navegação à esquerda e selecione
    o espaço de trabalho **WideWorldImporters.**

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

2.  Na página **WideWorldImporters**, acesse a aba **Home**, selecione
    **SQL** no menu suspenso e clique em **New SQL query**.

![A screenshot of a computer Description automatically
generated](./media/image34.png)

1.  No editor de consultas, cole o seguinte código e selecione **Run**
    para executar a consulta.

> /\*
>
> 1\. Drop the dimension_city table if it already exists.
>
> 2\. Create the dimension_city table.
>
> 3\. Drop the fact_sale table if it already exists.
>
> 4\. Create the fact_sale table.
>
> \*/
>
> --dimension_city
>
> DROP TABLE IF EXISTS \[dbo\].\[dimension_city\];
>
> CREATE TABLE \[dbo\].\[dimension_city\]
>
> (
>
> \[CityKey\] \[int\] NULL,
>
> \[WWICityID\] \[int\] NULL,
>
> \[City\] \[varchar\](8000) NULL,
>
> \[StateProvince\] \[varchar\](8000) NULL,
>
> \[Country\] \[varchar\](8000) NULL,
>
> \[Continent\] \[varchar\](8000) NULL,
>
> \[SalesTerritory\] \[varchar\](8000) NULL,
>
> \[Region\] \[varchar\](8000) NULL,
>
> \[Subregion\] \[varchar\](8000) NULL,
>
> \[Location\] \[varchar\](8000) NULL,
>
> \[LatestRecordedPopulation\] \[bigint\] NULL,
>
> \[ValidFrom\] \[datetime2\](6) NULL,
>
> \[ValidTo\] \[datetime2\](6) NULL,
>
> \[LineageKey\] \[int\] NULL
>
> );
>
> --fact_sale
>
> DROP TABLE IF EXISTS \[dbo\].\[fact_sale\];
>
> CREATE TABLE \[dbo\].\[fact_sale\]
>
> (
>
> \[SaleKey\] \[bigint\] NULL,
>
> \[CityKey\] \[int\] NULL,
>
> \[CustomerKey\] \[int\] NULL,
>
> \[BillToCustomerKey\] \[int\] NULL,
>
> \[StockItemKey\] \[int\] NULL,
>
> \[InvoiceDateKey\] \[datetime2\](6) NULL,
>
> \[DeliveryDateKey\] \[datetime2\](6) NULL,
>
> \[SalespersonKey\] \[int\] NULL,
>
> \[WWIInvoiceID\] \[int\] NULL,
>
> \[Description\] \[varchar\](8000) NULL,
>
> \[Package\] \[varchar\](8000) NULL,
>
> \[Quantity\] \[int\] NULL,
>
> \[UnitPrice\] \[decimal\](18, 2) NULL,
>
> \[TaxRate\] \[decimal\](18, 3) NULL,
>
> \[TotalExcludingTax\] \[decimal\](29, 2) NULL,
>
> \[TaxAmount\] \[decimal\](38, 6) NULL,
>
> \[Profit\] \[decimal\](18, 2) NULL,
>
> \[TotalIncludingTax\] \[decimal\](38, 6) NULL,
>
> \[TotalDryItems\] \[int\] NULL,
>
> \[TotalChillerItems\] \[int\] NULL,
>
> \[LineageKey\] \[int\] NULL,
>
> \[Month\] \[int\] NULL,
>
> \[Year\] \[int\] NULL,
>
> \[Quarter\] \[int\] NULL
>
> );

![A screenshot of a computer Description automatically
generated](./media/image35.png)

![A screenshot of a computer Description automatically
generated](./media/image36.png)

2.  Para salvar esta consulta, clique com o botão direito do mouse na
    aba **SQL query 1,** logo acima do editor, e selecione **Rename**.

![A screenshot of a computer Description automatically
generated](./media/image37.png)

3.  Na caixa de diálogo **Rename**, no campo **Name**, digite
    +++**Create Tables+++** Para alterar o nome da **consulta SQL 1**,
    clique no botão **Rename**.

![](./media/image38.png)

![A screenshot of a computer Description automatically
generated](./media/image39.png)

4.  Confirme se a tabela foi criada com sucesso selecionando o botão de
    **atualização** na faixa de opções.

![A screenshot of a computer Description automatically
generated](./media/image40.png)

5.  No **painel Explorer**, você verá a tabela **fact_sale** e
    **dimension_city**.

![A screenshot of a computer Description automatically
generated](./media/image41.png)

**Tarefa 2: Carregar dados usando T-SQL**

Agora que você já sabe como criar um armazém de dados, carregar uma
tabela e gerar um relatório, é hora de expandir a solução explorando
outros métodos de carregamento de dados.

1.  Na página **WideWorldImporters**, acesse a aba **Home**, selecione
    **SQL** no menu suspenso e clique em **New SQL query**.

![A screenshot of a computer Description automatically
generated](./media/image42.png)

2.  No editor de consultas, **cole** o seguinte código e clique em
    **Run** para executar a consulta.

> --Copy data from the public Azure storage account to the
> dbo.dimension_city table.
>
> COPY INTO \[dbo\].\[dimension_city\]
>
> FROM
> 'https://fabrictutorialdata.blob.core.windows.net/sampledata/WideWorldImportersDW/tables/dimension_city.parquet'
>
> WITH (FILE_TYPE = 'PARQUET');
>
> --Copy data from the public Azure storage account to the dbo.fact_sale
> table.
>
> COPY INTO \[dbo\].\[fact_sale\]
>
> FROM
> 'https://fabrictutorialdata.blob.core.windows.net/sampledata/WideWorldImportersDW/tables/fact_sale.parquet'

WITH (FILE_TYPE = 'PARQUET');

![A screenshot of a computer Description automatically
generated](./media/image43.png)

3.  Após a conclusão da consulta, revise as mensagens, que indicam o
    número de linhas carregadas nas tabelas **dimension_city** e
    **fact_sale,** respectivamente.

![A screenshot of a computer Description automatically
generated](./media/image44.png)

4.  Carregue a visualização de dados para validar se os dados foram
    carregados com sucesso, selecionando a tabela **fact_sale** no
    painel **Explorer**.

![](./media/image45.png)

5.  Renomeie a consulta. Clique com o botão direito do mouse em **SQL
    query 1** no **Explorer** e selecione **Rename**.

![](./media/image46.png)

6.  Na caixa de diálogo **Rename**, no campo **Name**, digite +++**Load
    Tables+++**. Em seguida, clique no botão **Rename.**

![A screenshot of a computer Description automatically
generated](./media/image47.png)

![A screenshot of a computer Description automatically
generated](./media/image48.png)

7.  Clique no ícone **Refresh** na barra de comandos abaixo da aba
    **Home**.

![A screenshot of a computer Description automatically
generated](./media/image49.png)

Exercício 4: Clonar uma tabela usando T-SQL no Microsoft Fabric

**Tarefa 1: Criar um clone de tabela dentro do mesmo esquema num
armazém**

Esta tarefa orienta você na criação de um [table
clone](https://learn.microsoft.com/en-in/fabric/data-warehouse/clone-table)
no armazém do Microsoft Fabric, usando a sintaxe T-SQL [CREATE TABLE AS
CLONE
OF](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-table-as-clone-of-transact-sql?view=fabric&preserve-view=true).

1.  Criar um clone da tabela dentro do mesmo esquema em um armazém de
    dados.

2.  Na página **WideWorldImporters**, acesse a aba **Home**, selecione
    **SQL** no menu suspenso e clique em **New SQL query**.

![A screenshot of a computer Description automatically
generated](./media/image50.png)

3.  No editor de consultas, cole o seguinte código para criar clones das
    tabelas **dbo.dimension_city** e **dbo.fact_sale**.

--Create a clone of the dbo.dimension_city table.

CREATE TABLE \[dbo\].\[dimension_city1\] AS CLONE OF
\[dbo\].\[dimension_city\];

--Create a clone of the dbo.fact_sale table.

CREATE TABLE \[dbo\].\[fact_sale1\] AS CLONE OF \[dbo\].\[fact_sale\];

![A screenshot of a computer Description automatically
generated](./media/image51.png)

4.  Selecione **Run** para executar a consulta. A consulta leva alguns
    segundos para ser executada. Após a conclusão, os clones de tabela
    **dimension_city1** e **fact_sale1** serão criados.

![A screenshot of a computer Description automatically
generated](./media/image52.png)

![A screenshot of a computer Description automatically
generated](./media/image53.png)

5.  Carregue a visualização de dados para validar se os dados foram
    carregados com sucesso, selecionando a tabela **dimension_city1** no
    painel **Explorer**.

![A screenshot of a computer Description automatically
generated](./media/image54.png)

6.  Clique com o botão direito do mouse em **SQL query** que você criou
    para clonar as tabelas no painel **Explorer** e selecione
    **Rename**.

![A screenshot of a computer Description automatically
generated](./media/image55.png)

1.  Na caixa de diálogo **Rename**, no campo **Name**, digite +++**Clone
    Table+++** e clique no botão **Rename**.

> ![A screenshot of a computer Description automatically
> generated](./media/image56.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image57.png)

1.  Clique no ícone **Refresh** na barra de comandos abaixo da aba
    **Home**.

> ![A screenshot of a computer Description automatically
> generated](./media/image58.png)

## Tarefa 2: Criar um clone de tabela entre esquemas dentro do mesmo armazém

1.  Na página **WideWorldImporters**, acesse a aba **Home**, selecione
    **SQL** no menu suspenso e clique em **New SQL query**.

> ![A screenshot of a computer Description automatically
> generated](./media/image59.png)

2.  Crie um novo esquema no armazém **WideWorldImporter** chamado
    **dbo1**. Copie, cole e **execute** o código T-SQL a seguir,
    conforme mostrado na imagem abaixo:

> CREATE SCHEMA dbo1;
>
> ![A screenshot of a computer Description automatically
> generated](./media/image60.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

3.  No editor de consultas, remova o código existente e cole o seguinte
    para criar clones das **tabelas dbo.dimension_city** e
    **dbo.fact_sale** no esquema **dbo1**.

> --Create a clone of the dbo.dimension_city table in the dbo1 schema.
>
> CREATE TABLE \[dbo1\].\[dimension_city1\] AS CLONE OF
> \[dbo\].\[dimension_city\];
>
> --Create a clone of the dbo.fact_sale table in the dbo1 schema.
>
> CREATE TABLE \[dbo1\].\[fact_sale1\] AS CLONE OF
> \[dbo\].\[fact_sale\];

4.  Selecione **Run** para executar a consulta. A consulta levará alguns
    segundos para ser executada.

> ![A screenshot of a computer Description automatically
> generated](./media/image62.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image63.png)

5.  Após a conclusão da consulta, os clones **dimension_city1** e
    **fact_sale1** são criados no esquema **dbo1**.

> ![A screenshot of a computer Description automatically
> generated](./media/image64.png)

6.  Carregue a visualização de dados para validar se os dados foram
    carregados com sucesso, selecionando a tabela **dimension_city1** no
    esquema **dbo1** no painel **Explorer**.

> ![A screenshot of a computer Description automatically
> generated](./media/image65.png)

7.  **Renomeie** a consulta para referência futura. Clique com o botão
    direito do mouse na **SQL query 1** no **Explorer** e selecione
    **Rename**.

> ![A screenshot of a computer Description automatically
> generated](./media/image66.png)

8.  Na caixa de diálogo **Rename**, no campo **Name**, digite +++**Clone
    Table in another schema+++**. Em seguida, clique no botão
    **Rename**.

> ![](./media/image67.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image68.png)

9.  Clique no ícone **Refresh** na barra de comandos abaixo da aba
    **Home**.

> ![A screenshot of a computer Description automatically
> generated](./media/image69.png)

# **Exercício 5: Transformar dados usando um procedimento armazenado**

Aprenda como criar e salvar um novo procedimento armazenado para
transformar dados.

1.  Na página **WideWorldImporters**, acesse a aba **Home**, selecione
    **SQL** no menu suspenso e clique em **New SQL query**.

> ![A screenshot of a computer Description automatically
> generated](./media/image70.png)

2.  No editor de consultas, **cole** o seguinte código para criar o
    procedimento armazenado **dbo.populate_aggregate_sale_by_city**.
    Este procedimento armazenado criará e carregará a tabela
    **dbo.aggregate_sale_by_date_city** em uma etapa posterior.

> --Drop the stored procedure if it already exists.
>
> DROP PROCEDURE IF EXISTS \[dbo\].\[populate_aggregate_sale_by_city\]
>
> GO
>
> --Create the populate_aggregate_sale_by_city stored procedure.
>
> CREATE PROCEDURE \[dbo\].\[populate_aggregate_sale_by_city\]
>
> AS
>
> BEGIN
>
> --If the aggregate table already exists, drop it. Then create the
> table.
>
> DROP TABLE IF EXISTS \[dbo\].\[aggregate_sale_by_date_city\];
>
> CREATE TABLE \[dbo\].\[aggregate_sale_by_date_city\]
>
> (
>
> \[Date\] \[DATETIME2\](6),
>
> \[City\] \[VARCHAR\](8000),
>
> \[StateProvince\] \[VARCHAR\](8000),
>
> \[SalesTerritory\] \[VARCHAR\](8000),
>
> \[SumOfTotalExcludingTax\] \[DECIMAL\](38,2),
>
> \[SumOfTaxAmount\] \[DECIMAL\](38,6),
>
> \[SumOfTotalIncludingTax\] \[DECIMAL\](38,6),
>
> \[SumOfProfit\] \[DECIMAL\](38,2)
>
> );
>
> --Reload the aggregated dataset to the table.
>
> INSERT INTO \[dbo\].\[aggregate_sale_by_date_city\]
>
> SELECT
>
> FS.\[InvoiceDateKey\] AS \[Date\],
>
> DC.\[City\],
>
> DC.\[StateProvince\],
>
> DC.\[SalesTerritory\],
>
> SUM(FS.\[TotalExcludingTax\]) AS \[SumOfTotalExcludingTax\],
>
> SUM(FS.\[TaxAmount\]) AS \[SumOfTaxAmount\],
>
> SUM(FS.\[TotalIncludingTax\]) AS \[SumOfTotalIncludingTax\],
>
> SUM(FS.\[Profit\]) AS \[SumOfProfit\]
>
> FROM \[dbo\].\[fact_sale\] AS FS
>
> INNER JOIN \[dbo\].\[dimension_city\] AS DC
>
> ON FS.\[CityKey\] = DC.\[CityKey\]
>
> GROUP BY
>
> FS.\[InvoiceDateKey\],
>
> DC.\[City\],
>
> DC.\[StateProvince\],
>
> DC.\[SalesTerritory\]
>
> ORDER BY
>
> FS.\[InvoiceDateKey\],
>
> DC.\[StateProvince\],
>
> DC.\[City\];
>
> END
>
> ![A screenshot of a computer Description automatically
> generated](./media/image71.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image72.png)

3.  Clique com o botão direito do mouse na consulta SQL que você criou
    para clonar as tabelas no painel Explorer e selecione **Rename**.

> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)

4.  Na caixa de diálogo **Rename**, no campo **Name**, digite
    +++**Create Aggregate Procedure+++** e clique no botão **Rename.**

> ![A screenshot of a computer screen Description automatically
> generated](./media/image74.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image75.png)

5.  Clique no **ícone Refresh** abaixo da aba **Home**.

> ![A screenshot of a computer Description automatically
> generated](./media/image76.png)

6.  Na aba **Explorer**, verifique se você consegue visualizar o
    procedimento armazenado recém-criado expandindo o nó **Stored
    Procedures** no esquema **dbo**.

> ![A screenshot of a computer Description automatically
> generated](./media/image77.png)

7.  Na página **WideWorldImporters**, acesse a aba **Home**, selecione
    **SQL** no menu suspenso e clique em **New SQL query**.

> ![A screenshot of a computer Description automatically
> generated](./media/image78.png)

8.  No editor de consultas, cole o seguinte código. Este T-SQL executa
    **dbo.populate_aggregate_sale_by_city** para criar a tabela
    **dbo.aggregate_sale_by_date_city.** Execute a consulta.

> --Execute the stored procedure to create the aggregate table.
>
> EXEC \[dbo\].\[populate_aggregate_sale_by_city\];
>
> ![A screenshot of a computer Description automatically
> generated](./media/image79.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image80.png)

9.  Para salvar essa consulta para referência futura, clique com o botão
    direito do mouse na aba da consulta, logo acima do editor, e
    selecione **Rename.**

![A screenshot of a computer Description automatically
generated](./media/image81.png)

10. Na caixa de diálogo **Rename**, no campo **Name**, digite +++**Run**
    **Create Aggregate Procedure+++** e, em seguida, clique no botão
    **Rename**.

![](./media/image82.png)

![A screenshot of a computer Description automatically
generated](./media/image83.png)

11. Selecione o ícone **Refresh** na faixa de opções.

![A screenshot of a computer Description automatically
generated](./media/image84.png)

12. Na aba **Explorer**, carregue a visualização de dados para validar
    se os dados foram carregados com sucesso, selecionando a tabela
    **aggregate_sale_by_city** no **Explorer**.

![A screenshot of a computer Description automatically
generated](./media/image85.png)

# Exercício 6: Viajar no tempo usando T-SQL no nível da instrução

1.  Na página **WideWorldImporters**, acesse a aba **Home**, selecione
    **SQL** no menu suspenso e clique em **New SQL query**.

> ![A screenshot of a computer Description automatically
> generated](./media/image86.png)

2.  No editor de consultas, cole o seguinte código para criar a
    visualização Top10CustomerView. Selecione **Run** para executar a
    consulta.

CREATE VIEW dbo.Top10CustomersView

AS

SELECT TOP (10)

    FS.\[CustomerKey\],

    DC.\[Customer\],

    SUM(FS.TotalIncludingTax) AS TotalSalesAmount

FROM

    \[dbo\].\[dimension_customer\] AS DC

INNER JOIN

    \[dbo\].\[fact_sale\] AS FS ON DC.\[CustomerKey\] =
FS.\[CustomerKey\]

GROUP BY

    FS.\[CustomerKey\],

    DC.\[Customer\]

ORDER BY

    TotalSalesAmount DESC;

![A screenshot of a computer Description automatically
generated](./media/image87.png)

![A screenshot of a computer Description automatically
generated](./media/image88.png)

3.  No **Explorer**, verifique se consegue ver a nova visualização
    **Top10CustomersView** expandindo o nó **View** no esquema **dbo**.

![](./media/image89.png)

4.  Para salvar essa consulta para referência futura, clique com o botão
    direito do mouse na aba de consulta, logo acima do editor, e
    selecione **Rename.**

![A screenshot of a computer Description automatically
generated](./media/image90.png)

5.  Na caixa de diálogo **Rename**, no campo **Name**, digite
    +++**Top10CustomersView+++**, e clique no botão **Rename**.

![](./media/image91.png)

6.  Crie outra nova consulta, semelhante à Etapa 1. Na aba **Home** da
    faixa de opções, selecione **New SQL query**.

![A screenshot of a computer Description automatically
generated](./media/image92.png)

7.  No editor de consultas, cole o código a seguir. Ele atualiza o valor
    da coluna **TotalIncludingTax** para **200000000** no registro que
    possui o valor **SaleKey** igual a **22632918**. Selecione **Run**
    para executar a consulta.

/\*Update the TotalIncludingTax value of the record with SaleKey value
of 22632918\*/

UPDATE \[dbo\].\[fact_sale\]

SET TotalIncludingTax = 200000000

WHERE SaleKey = 22632918;

![A screenshot of a computer Description automatically
generated](./media/image93.png)

![A screenshot of a computer Description automatically
generated](./media/image94.png)

8.  No editor de consultas, cole o código a seguir. A função T-SQL
    CURRENT_TIMESTAMP retorna o carimbo de data/hora UTC atual como um
    **datetime**. Selecione **Run** para executar a consulta.

SELECT CURRENT_TIMESTAMP;

![](./media/image95.png)

9.  Copie o valor do carimbo de data/hora para a sua área de
    transferência.

![A screenshot of a computer Description automatically
generated](./media/image96.png)

10. Cole o seguinte código no editor de consultas e substitua o valor do
    carimbo de data/hora pelo valor atual obtido na etapa anterior. O
    formato da sintaxe do carimbo de data/hora é
    **YYYY-MM-DDTHH:MM:SS\[.FFF\].**

11. Remova os zeros à direita, por exemplo: **2025-06-09T06:16:08.807**.

12. O exemplo a seguir retorna a lista dos dez principais clientes por
    **TotalIncludingTax**, incluindo o novo valor para
    **SaleKey** 22632918. Substitua o código existente, cole o código a
    seguir e selecione **Run** para executar a consulta.

/\*View of Top10 Customers as of today after record updates\*/

SELECT \*

FROM \[WideWorldImporters\].\[dbo\].\[Top10CustomersView\]

OPTION (FOR TIMESTAMP AS OF '2025-06-09T06:16:08.807');

![A screenshot of a computer Description automatically
generated](./media/image97.png)

13. Cole o código a seguir no editor de consultas e substitua o valor do
    carimbo de data/hora por um horário anterior à execução do script de
    atualização do valor **TotalIncludingTax**. Isso retornará a lista
    dos dez principais clientes antes do **TotalIncludingTax** ter sido
    atualizado para o **SaleKey** 22632918. Selecione **Run** para
    executar a consulta.

/\*View of Top10 Customers as of today before record updates\*/

SELECT \*

FROM \[WideWorldImporters\].\[dbo\].\[Top10CustomersView\]

OPTION (FOR TIMESTAMP AS OF '2024-04-24T20:49:06.097');

![A screenshot of a computer Description automatically
generated](./media/image98.png)

Exercício 7: Criando uma consulta com o criador visual de consultas

**Tarefa 1: Utilizar o criador visual de consultas**

Crie e salve uma consulta com o criador visual de consultas no portal do
Microsoft Fabric.

1.  Na página **WideWorldImporters**, na aba  **Home** da faixa de
    opções, selecione **New visual query**.

![A screenshot of a computer Description automatically
generated](./media/image99.png)

2.  Clique com o botão direito do mouse em **fact_sale** e selecione
    **Insert into canvas.**

![A screenshot of a computer Description automatically
generated](./media/image100.png)

![A screenshot of a computer Description automatically
generated](./media/image101.png)

3.  Navegue até a **transformations ribbon** do painel de design da
    consulta e limite o tamanho do conjunto de dados clicando na lista
    suspensa **Reduce rows** e, em seguida, clique em **Keep top rows,**
    conforme mostrado na imagem abaixo.

![A screenshot of a computer Description automatically
generated](./media/image102.png)

4.  Na caixa de diálogo **Keep top rows**, digite **10000** e selecione
    **OK**.

![](./media/image103.png)

![A screenshot of a computer Description automatically
generated](./media/image104.png)

5.  Clique com o botão direito do mouse em **dimension_city** e
    selecione **Insert into canvas.**

![A screenshot of a computer Description automatically
generated](./media/image105.png)

![A screenshot of a computer Description automatically
generated](./media/image106.png)

6.  Na faixa de opções de transformações, selecione a lista suspensa ao
    lado de **Combine** e selecione **Merge queries as new,** conforme
    mostrado na imagem abaixo.

![A screenshot of a computer Description automatically
generated](./media/image107.png)

7.  Na página de configurações **Merge,** insira os seguintes detalhes.

> **•** No menu suspenso **Left table for merge**, escolha
> **dimension_city  
> •** No menu suspenso **Right table for merge**, escolha **fact_sale
> (**use as barras de rolagem horizontal e vertical).**  
> •** Selecione o campo **CityKey** na tabela **dimension_city**
> clicando no nome da coluna na linha de cabeçalho para indicar a coluna
> de junção.**  
> •** Selecione o campo **CityKey** na tabela **fact_sale** clicando no
> nome da coluna na linha de cabeçalho para indicar a coluna de
> junção.**  
> •** No diagrama **Join kind**, escolha **Inner** e clique no botão
> **Ok.**

![A screenshot of a computer Description automatically
generated](./media/image108.png)

![A screenshot of a computer Description automatically
generated](./media/image109.png)

8.  Com a etapa **Merge** selecionada, selecione o botão **Expand** ao
    lado de **fact_sale** no cabeçalho da grade de dados, conforme
    mostrado na imagem abaixo, selecione as colunas **TaxAmount,
    Profit** e **TotalIncludingTax** e selecione **Ok.**

![A screenshot of a computer Description automatically
generated](./media/image110.png)

![A screenshot of a computer Description automatically
generated](./media/image111.png)

9.  Em **transformations ribbon,** clique na lista suspensa ao lado de
    **Transform** e selecione **Group by**.

![A screenshot of a computer Description automatically
generated](./media/image112.png)

10. Na página de configurações **Group by**, insira os seguintes
    detalhes.

- Selecione a opção **Advanced**.

- Em **Group by**, selecione o seguinte:

  1.  **Country**

  2.  **StateProvince**

  3.  **City**

- No campo **New column name,** digite **SumOfTaxAmount** no campo
  **Operation,** selecione **Sum** e, em seguida, no campo **Column**,
  selecione **TaxAmount.** Clique em **Add aggregation** para adicionar
  mais colunas e operações de agregação.

- No campo **New column name**, digite **SumOfProfit** no campo
  **Operation**, selecione **Sum** e, em seguida, no campo **Column**,
  selecione **Profit**. Clique em **Add aggregation** para adicionar
  mais colunas e operações de agregação.

- No campo **New column name**, digite **SumOfTotalIncludingTax.** No
  campo **Operation**, selecione **Sum** e, em seguida, no campo
  **Column,TotalIncludingTax.** 

- Clique no botão **OK**

![](./media/image113.png)

![A screenshot of a computer Description automatically
generated](./media/image114.png)

11. No Explorer, navegue até **Queries** e clique com o botão direito em
    **Visual query 1** em **Queries**. Em seguida, selecione **Rename**.

![A screenshot of a computer Description automatically
generated](./media/image115.png)

12. Tipo +++**Sales Summary+++** para alterar o nome da consulta.
    Pressione **Enter** no teclado ou selecione em qualquer lugar fora
    da aba para salvar a alteração.

![A screenshot of a computer Description automatically
generated](./media/image116.png)

13. Clique no ícone **Refresh** abaixo da aba **Home**.

![A screenshot of a computer Description automatically
generated](./media/image117.png)

**Exercício 8: Analisando dados com um bloco de notas**

**Tarefa 1: Criar um atalho para o Lakehouse e analisar os dados com um
bloco de notas**

Nesta tarefa, você aprenderá como salvar seus dados uma única vez e, em
seguida, utilizá-los com muitos outros serviços. Atalhos também podem
ser criados para dados armazenados no Azure Data Lake Storage e no S3,
permitindo acessar diretamente tabelas Delta a partir de sistemas
externos.

Primeiro, vamos criar um novo Lakehouse. Para criar um novo Lakehouse no
seu espaço de trabalho do Microsoft Fabric:

1.  Na página **WideWorldImportes**, clique no espaço de trabalho
    **Warehouse_FabricXX** no menu de navegação à esquerda.

![A screenshot of a computer Description automatically
generated](./media/image118.png)

2.  Na página inicial do **Synapse Data Engineering
    Warehouse_FabricXX**, no painel **Warehouse_FabricXX**, clique em +
    **New item** e, em seguida, selecione **Lakehouse** em
    **Stored data**.

![A screenshot of a computer Description automatically
generated](./media/image119.png)

3.  No campo **Name**, digite +++**ShortcutExercise+++** e clique no
    botão **Create.**

![A screenshot of a computer Description automatically
generated](./media/image120.png)

4.  O novo Lakehouse é carregado e a exibição do **Explorer** é aberta,
    com o menu **Get data in your lakehouse**. Em **Load data in your
    lakehouse**, selecione o botão **New shortcut**.

![A screenshot of a computer Description automatically
generated](./media/image121.png)

5.  Na janela **New shortcut**, selecione **Microsoft OneLake**.

![A screenshot of a computer Description automatically
generated](./media/image122.png)

6.  Na janela **Select a data source type**, navegue com cuidado e
    clique em **Warehouse** chamado **WideWorldImporters** que você
    criou anteriormente e, em seguida, clique no botão **Next.**

![A screenshot of a computer Description automatically
generated](./media/image123.png)

7.  No navegador de objetos do **OneLake**, expanda **Tables**, depois
    expanda o esquema **dbo** e selecione o botão de opção ao lado de
    **dimension_customer**. Em seguida, selecione o botão **Next**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image124.png)

8.  Na janela **New shortcut**, clique no botão **Create,** em seguida,
    no botão **Close.**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image125.png)

![](./media/image126.png)

9.  Aguarde alguns instantes e clique no ícone **Refresh**.

10. Em seguida, selecione a tabela **dimension_customer** na lista
    **Table** para visualizar os dados. Observe que o Lakehouse está
    exibindo os dados da tabela **dimension_customer** do armazenamento.

![](./media/image127.png)

11. Em seguida, crie um novo bloco de notas para consultar a tabela
    **dimension_customer**. Na faixa de opções **Home**, selecione o
    menu suspenso **Open notebook** e escolha **New notebook**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image128.png)

12. Selecione e arraste a tabela **dimension_customer** da lista
    **Tables** para a célula aberta do bloco de notas. Você verá que uma
    consulta **PySpark** foi criada automaticamente para consultar todos
    os dados de **ShortcutExercise.dimension_customer**. Essa
    experiência de bloco de notas é semelhante à experiência do bloco de
    notas Jupyter no Visual Studio Code. Você também pode abrir o bloco
    de notas no VS Code.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image129.png)

13. Na aba **Home**, selecione o botão **Run all**. Assim que a consulta
    for concluída, você verá que pode usar o PySpark facilmente para
    consultar as tabelas do armazenamento!

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image130.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image131.png)

**Exercício 9: Criando consultas entre armazéns com o editor de
consultas SQL**

**Tarefa 1: Adicionar vários armazéns ao Explorer**

Nesta tarefa, aprenda como pode criar e executar facilmente consultas
T-SQL com o editor de consultas SQL em vários armazéns, incluindo a
junção de dados de um **SQL endpoint** e um armazém no Microsoft Fabric.

1.  Na página **Notebook1**, navegue e clique no espaço de trabalho
    **Warehouse_FabricXX** no menu de navegação à esquerda.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image132.png)

2.  Na visualização **Warehouse_FabricXX**, selecione o armazém
    **WideWorldImporters**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image133.png)

3.  Na página **WideWorldImporters**, na aba **Explorer**, selecione o
    botão **+Warehouses**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image134.png)

4.  Na janela adicionar armazéns, selecione **ShortcutExercise** e
    clique no botão **Confirm**. As duas experiências de armazém serão
    adicionadas à consulta.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image135.png)

5.  Os armazéns selecionados agora exibem o mesmo painel **Explorer.**

![](./media/image136.png)

**Tarefa 2: Executar uma consulta entre armazéns**

Neste exemplo, você pode ver como é fácil executar consultas T-SQL no
armazém WideWorldImporters e no ShortcutExercise SQL endpoint. Pode
escrever consultas entre bases de dados usando nomenclatura de três
partes para referenciar a tabela database.schema, como no Servidor SQL.

1.  Na aba **Home** da faixa de opções, selecione **New SQL query**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image137.png)

2.  No editor de consultas, copie e cole o seguinte código T-SQL.
    Selecione o botão **Run** para executar a consulta. Após a conclusão
    da consulta, você verá os resultados.

Copiar SQL

> SELECT Sales.StockItemKey,
>
> Sales.Description,
>
> SUM(CAST(Sales.Quantity AS int)) AS SoldQuantity,
>
> c.Customer
>
> FROM \[dbo\].\[fact_sale\] AS Sales,
>
> \[ShortcutExercise\].\[dbo\].\[dimension_customer\] AS c
>
> WHERE Sales.CustomerKey = c.CustomerKey
>
> GROUP BY Sales.StockItemKey, Sales.Description, c.Customer;

![](./media/image138.png)

3.  Renomeie a consulta para referência. Clique com o botão direito do
    mouse na **SQL query** no **Explorer** e selecione **Rename**.

![](./media/image139.png)

4.  Na caixa de diálogo **Rename**, no campo **Name**, digite
    +++**Cross-warehouse query+++** e clique no botão **Rename**. 

![](./media/image140.png)

Exercício 10: Criar relatórios do Power BI

**Tarefa 1: Criar um modelo semântico**

Nesta tarefa, aprenderemos como criar e salvar vários tipos de
relatórios do Power BI.

1.  Na página **WideWorldImportes**, na aba **Home**, selecione **New
    semantic model**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image141.png)

2.  Na janela **New semantic model**, no campo **Direct Lake semantic
    model name**, insira +++**Sales Model+++**.

3.  Expanda o esquema dbo, expanda a pasta **Tables** e marque as
    tabelas **dimension_city** e **fact_sale**. Selecione **Confirm**.

![](./media/image142.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image143.png)

4.  Na navegação à esquerda, selecione ***Warehouse_FabricXXXXX***,
    conforme mostrado na imagem abaixo.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image144.png)

5.  Para abrir o modelo semântico, retorne à página inicial do espaço de
    trabalho e selecione o modelo semântico **Sales Model**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image145.png)

6.  Para abrir o designer de modelos, no menu, selecione **Open data
    model**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image146.png)

![](./media/image147.png)

7.  Na página **Sales Model**, para editar **Manage Relationships**,
    altere o modo de **Viewing** para **Editing**.![A screenshot of a
    computer AI-generated content may be
    incorrect.](./media/image148.png)

8.  Para criar um relacionamento, no designer de modelos, na faixa de
    opções **Home**, selecione **Manage relationships**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image149.png)

9.  Na **janela New relationship**, siga os passos abaixo para criar o
    relacionamento:

&nbsp;

1.  Na lista suspensa **From table**, selecione a tabela dimension_city.

2.  Na lista suspensa **To table**, selecione a tabela fact_sale.

3.  Na lista suspensa **Cardinality**, selecione **One to many (1:\*)**.

4.  Na lista suspensa **Cross-filter direction**, selecione **Single**.

5.  Marque a caixa **Assume referential integrity.**

6.  Selecione **Save**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image150.png)

![](./media/image151.png)

![](./media/image152.png)

1.  Na janela **Manage relationship**, selecione **Close**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image153.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image154.png)

**Tarefa 2: Criar um relatório do Power BI**

Nesta tarefa, aprenda como criar um relatório do Power BI com base no
modelo semântico que você criou na tarefa anterior.

1.  Na aba **File**, selecione **Create new report**.

![](./media/image155.png)

2.  No designer de relatórios, siga os passos abaixo para criar um
    gráfico de colunas:

&nbsp;

1)  No painel **Data**, expanda a tabela **fact_sale** e, em seguida,
    verifique o campo Profit.

2)  No painel **Data**, expanda a tabela dimension_city e, em seguida,
    verifique o campo SalesTerritory.

![](./media/image156.png)

3.  No painel **Visualizations**, selecione o visual **Azure Map**.

![](./media/image157.png)

4.  No painel **Data**, dentro da tabela dimension_city, arraste os
    campos StateProvince para o campo **Location** no painel
    **Visualizations**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image158.png)

5.  No painel **Data**, dentro da tabela fact_sale, marque o campo
    Profit para adicioná-lo ao campo **Size** do mapa.

6.  No painel **Visualizations**, selecione a visualização **Table**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image159.png)

7.  No painel **Data**, verifique os seguintes campos:

a\) SalesTerritory da tabela dimension_city  
b) StateProvince da tabela dimension_city  
c) Profit da tabela fact_sale  
d) TotalExcludingTax da tabela fact_sale

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image160.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image161.png)

8.  Verifique se o design final da página do relatório se assemelha à
    imagem a seguir.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image162.png)

9.  Para salvar o relatório, na faixa de opções **Home**, selecione
    **File** \> **Save**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image163.png)

10. Na janela **Save your report**, no campo inserir um nome para o
    relatório, digite +++**Sales Analysis**+++ e selecione **Save**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image164.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image165.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image166.png)

**Tarefa 3: Limpar recursos**

Você pode excluir relatórios individuais, pipelines, armazéns e outros
itens ou remover todo o espaço de trabalho. Neste tutorial, você limpará
o espaço de trabalho, relatórios individuais, pipelines, armazéns e
outros itens que criou como parte do laboratório.

1.  Selecione **Warehouse_FabricXX** no menu de navegação para retornar
    à lista de itens do espaço de trabalho.

![](./media/image167.png)

2.  No menu de cabeçalho do espaço de trabalho, selecione **Workspace
    settings**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image168.png)

3.  Na caixa de diálogo **Workspace settings** selecione **General** e,
    em seguida, selecione **Remove this workspace**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image169.png)

4.  Na caixa de diálogo **Delete workspace?**, clique no botão
    **Delete.** ![](./media/image170.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image171.png)

**Resumo**

Este laboratório abrangente orienta o usuário por uma série de tarefas
destinadas a estabelecer um ambiente de dados funcional no Microsoft
Fabric. Começa com a criação de um espaço de trabalho, essencial para
operações de dados, e garante que a versão de avaliação esteja ativada.
Posteriormente, um armazém chamado WideWorldImporters é estabelecido no
ambiente Fabric para servir como repositório central para armazenamento
e processamento de dados. A ingestão de dados no espaço de trabalho
Warehouse_FabricXX é então detalhada através da implementação de um
pipeline do Data Factory. Este processo envolve a obtenção de dados de
fontes externas e a sua integração perfeita no espaço de trabalho.
Tabelas críticas, dimension_city e fact_sale, são criadas dentro do
armazém de dados para servir como estruturas fundamentais para a análise
de dados. O processo de carregamento de dados continua com o uso do
T-SQL, onde os dados do Azure Blob Storage são transferidos para as
tabelas especificadas. As tarefas subsequentes aprofundam-se no domínio
da gestão e manipulação de dados. A clonagem de tabelas é demonstrada,
oferecendo uma técnica valiosa para fins de replicação e teste de dados.
Além disso, o processo de clonagem é estendido a um esquema diferente
(dbo1) dentro do mesmo armazém, mostrando uma abordagem estruturada para
a organização de dados. O laboratório avança para a transformação de
dados, introduzindo a criação de um procedimento armazenado para agregar
dados de vendas de forma eficiente. Em seguida, faz a transição para a
criação de consultas visuais, fornecendo uma interface intuitiva para
consultas de dados complexas. Em seguida, é feita uma exploração dos
cadernos, demonstrando a sua utilidade na consulta e análise de dados da
tabela dimension_customer. Em seguida, são revelados os recursos de
consulta em vários armazéns, permitindo a recuperação contínua de dados
em vários armazéns dentro do espaço de trabalho. O laboratório termina
com a integração de recursos visuais do Azure Maps, aprimorando a
representação de dados geográficos no Power BI. Posteriormente, uma
série de relatórios do Power BI, incluindo gráficos de colunas, mapas e
tabelas, são criados para facilitar a análise aprofundada dos dados de
vendas. A tarefa final concentra-se na geração de um relatório a partir
do hub de dados OneLake, enfatizando ainda mais a versatilidade das
fontes de dados no Fabric. Por fim, o laboratório fornece insights sobre
o gerenciamento de recursos, enfatizando a importância dos procedimentos
de limpeza para manter um espaço de trabalho eficiente. Coletivamente,
essas tarefas apresentam uma compreensão abrangente da configuração,
gerenciamento e análise de dados no Microsoft Fabric.
