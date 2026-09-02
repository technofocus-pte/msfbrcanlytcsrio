# Caso de uso 04 — Criação de um data warehouse de vendas e dados geográficos para a Contoso no Microsoft Fabric

**Introdução**

A Contoso, uma empresa multinacional do setor de varejo, pretende
modernizar sua infraestrutura de dados para aprimorar as análises de
vendas e geográficas. Atualmente, seus dados de vendas e de clientes
estão dispersos por vários sistemas, o que dificulta que seus analistas
de negócios e desenvolvedores leigos extraiam insights. A empresa
planeja consolidar esses dados em uma plataforma unificada usando o
Microsoft Fabric para possibilitar consultas cruzadas, análises de
vendas e relatórios geográficos.

Neste laboratório, você assumirá o papel de um engenheiro de dados na
Contoso, encarregado de projetar e implementar uma solução de data
warehouse usando o Microsoft Fabric. Você começará configurando um
espaço de trabalho do Fabric, criando um data warehouse, carregando
dados do Armazenamento de Blobs do Azure e realizando tarefas analíticas
para fornecer insights aos tomadores de decisão da Contoso.

Embora muitos conceitos do Microsoft Fabric possam ser familiares aos
profissionais de dados e análise, pode ser desafiador aplicá-los em um
novo ambiente. Este laboratório foi elaborado para orientar passo a
passo um cenário completo, desde a aquisição até o consumo de dados, a
fim de proporcionar uma compreensão básica da experiência do usuário no
Microsoft Fabric, das diversas experiências e seus pontos de integração,
bem como das experiências para desenvolvedores profissionais e leigos no
Microsoft Fabric.

**Objetivos**

- Configurar um workspace do Fabric com a avaliação habilitada.

- Criar um novo Warehouse chamado WideWorldImporters no Microsoft
  Fabric.

- Carregar dados no workspace Warehouse_FabricXX usando um pipeline do
  Data Factory.

- Criar as tabelas dimension_city e fact_sale no data warehouse.

- Preencher as tabelas dimension_city e fact_sale com dados do
  Armazenamento de Blobs do Azure.

- Criar clones das tabelas dimension_city e fact_sale no Warehouse.

- Clonar as tabelas dimension_city e fact_sale no esquema dbo1.

- Desenvolver um procedimento armazenado para transformar os dados e
  criar a tabela aggregate_sale_by_date_city.

- Criar uma consulta usando o construtor visual de consultas para
  mesclar e agregar dados.

- Usar um notebook para consultar e analisar dados da tabela
  dimension_customer.

- Incluir os warehouses WideWorldImporters e ShortcutExercise para
  realizar consultas entre warehouses.

- Executar uma consulta T-SQL entre os warehouses WideWorldImporters e
  ShortcutExercise.

- Habilitar a integração do visual dos Mapas do Azure no portal de
  administração.

- Criar visuais de gráfico de colunas, mapa e tabela para o relatório
  Sales Analysis.

- Criar um relatório usando dados do conjunto de dados
  WideWorldImporters no hub de dados do OneLake.

- Remover o workspace e os itens associados.

## Exercício 1: Criar um workspace do Microsoft Fabric

### Tarefa 1: Criar um workspace

1.  Abra o navegador, acesse a barra de endereços e digite ou cole a
    seguinte URL: +++https://app.fabric.microsoft.com/+++ em seguida,
    pressione o botão **Enter**.

\[!nota\] **Observação**: Se você for direcionado para o Microsoft
Fabric home page, pule para a etapa 5.

![](./media/image1.png)

2.  Na janela do **Microsoft Fabric**, insira suas credenciais e clique
    no botão **Submit**.

| Credential | Value |
|---|---|
| Username | +++@lab.CloudPortalCredential(User1).Username+++ |
| Password | +++@lab.CloudPortalCredential(User1).Password+++ |

> ![](./media/image2.png)

3.  Em seguida, na janela da **Microsoft**, insira a senha e clique no
    botão **Sign in**.

> ![](./media/image3.png)

4.  Na janela **Stay signed in?**, clique no botão **Yes**.

5.  Se o Power BI abrir por padrão, siga as etapas abaixo; caso
    contrário, pule esta etapa.

- Clique em Power BI

![](./media/image4.png)

- Selecione Fabric na lista de opções.

![](./media/image5.png)

6.  No Fabric home page, selecione o bloco **+ New workspace**.

![](./media/image6.png)

2.  Na guia **Create a workspace**, insira os detalhes a seguir e clique
    no botão **Apply**.

| Field | Value |
|---|---|
| Name | +++Warehouse_Fabric@lab.LabInstance.Id+++ (must be a unique Id) |
| Description | +++This workspace contains all the artifacts for the data warehouse+++ |
| Advanced Under License mode | Fabric |
| Default storage format | Small dataset storage format |

![](./media/image7.png)

![](./media/image8.png)

![](./media/image9.png)

3.  Aguarde a conclusão da implementação. Esse processo leva de 1 a 2
    minutos. Quando o novo workspace for aberto, ele deverá estar vazio.

![](./media/image10.png)

### Tarefa 2: Criar um Warehouse no Microsoft Fabric

1.  Na página do **Fabric**, selecione **+ New item** para criar um
    lakehouse e, em seguida, selecione **Warehouse**.

![A screenshot of a computer Description automatically
generated](./media/image11.png)

2.  Na caixa de diálogo **New warehouse**, insira **WideWorldImporters**
    e clique no botão **Create**.

![](./media/image12.png)

3.  Quando o provisionamento for concluído, a home page do warehouse
    **WideWorldImporters** será exibida.

![](./media/image13.png)

## Exercício 2: Ingerir dados em um Warehouse no Microsoft Fabric

### Tarefa 1: Ingerir dados em um Warehouse

1.  Na home page do warehouse **WideWorldImporters**, selecione
    **Warehouse_FabricXX** no menu de navegação à esquerda para retornar
    à lista de itens do workspace.

![](./media/image14.png)

2.  Na página **Warehouse_FabricXX**, selecione **+ New item**. Em
    seguida, clique em **Copy job** para visualizar a lista completa de
    itens disponíveis em Get data.

![](./media/image15.png)

3.  Na janela **New copy job**, no campo **Name**, insira **+++Load
    Customer Data+++**. Em seguida, selecione **Create**.

> ![](./media/image16.png)

4.  O provisionamento estará concluído quando a página **Copy job** for
    aberta.

> ![](./media/image17.png)

5.  Na primeira página da janela **Copy job**, selecione **Sample data**
    na barra de menus dessa página. Para este tutorial, usaremos o
    exemplo **Retail Data Model from Wide World Importers**. Selecione
    essa opção para navegar para a próxima página.

> ![](./media/image18.png)

6.  A pré-visualização dos dados de exemplo será carregada. Na página
    **Choose data**, você poderá visualizar o conjunto de dados
    selecionado. Depois de revisar os dados, selecione **Next**.

![](./media/image19.png)

5.  A página Choose data destination permite configurar o tipo de item.
    No OneLake catalog, selecione seu warehouse **Wide World Importers**
    e selecione **Next.**

> ![](./media/image20.png)

6.  Na página **Choose copy job mode**, selecione **Full copy** e clique
    em **Next**.

> ![](./media/image21.png)

7.  Insira as seguintes tabelas de destino e, em seguida, selecione
    **Next**:

- dbo.dimension_city

- dbo.dimension_customer

- dbo.dimension_date

- dbo.dimension_employee

- dbo.dimension_stock_item

- dbo.fact_sale

> ![](./media/image22.png)

8.  Na página **Review + save**, revise a **Source** e o
    **Destination**.

![](./media/image23.png)

9.  Use a guia **Results** para monitorar a execução do Copy job.

![](./media/image24.png)

10. Quando concluído, o **Copy job** exibirá uma notificação e um status
    **Succeeded**. Agora, você verá seis novas tabelas do conjunto de
    dados Wide World Importers no seu warehouse.

![](./media/image25.png)

11. Na página **Load Customer Data**, clique no workspace
    **Warehouse_FabricXX** na barra de navegação à esquerda e selecione
    o warehouse **WideWorldImporters**.

> ![](./media/image26.png)

12. No warehouse **WideWorldImporters**, expanda **Schemas \> dbo \>
    Tables** e verifique se as tabelas **(dimension_city,
    dimension_customer, dimension_date, dimension_employee,
    dimension_stock_item** e **fact_sale)** foram criadas com sucesso.

![](./media/image27.png)

## Exercício 3: **Clonar uma tabela com T-SQL em um Warehouse**

### Tarefa 1: **Clonar uma tabela dentro do mesmo esquema**

1.  Na página **WideWorldImporters**, acesse a guia **Home**, selecione
    **SQL** no menu suspenso e clique em **New SQL query**.

![](./media/image28.png)

3.  No editor de consultas, cole o código a seguir. O código cria um
    clone da tabela dimension_city e da tabela fact_sale.

```
--Create a clone of the dbo.dimension_city table.
 CREATE TABLE [dbo].[dimension_city1] AS CLONE OF [dbo].[dimension_city];

 --Create a clone of the dbo.fact_sale table.
 CREATE TABLE [dbo].[fact_sale1] AS CLONE OF [dbo].[fact_sale];
```
> ![](./media/image29.png)

4.  Para executar a consulta, na faixa de opções do designer de
    consultas, selecione **Run**.

![](./media/image30.png)

![](./media/image31.png)

5.  No editor de consultas, cole o código a seguir. A função T-SQL
    CURRENT_TIMESTAMP retorna o carimbo de data/hora UTC atual como um
    tipo **datetime**. Selecione **Run** para executar a consulta.

```
SELECT CURRENT_TIMESTAMP;
```

![](./media/image32.png)

6.  Para criar um clone de uma tabela em um *ponto específico no tempo*,
    no editor de consultas, cole o código a seguir **para substituir as
    instruções existentes**. O código cria um clone da tabela
    dimension_city e da tabela fact_sale em um determinado ponto no
    tempo. Execute a consulta.

```
--Create a clone of the dbo.dimension_city table at a specific point in time.   
CREATE TABLE [dbo].[dimension_city2] AS CLONE OF [dbo].[dimension_city] AT '2025-01-01T10:00:00.000';

 --Create a clone of the dbo.fact_sale table at a specific point in time.
CREATE TABLE [dbo].[fact_sale2] AS CLONE OF [dbo].[fact_sale] AT '2025-01-01T10:00:00.000';
```

![](./media/image33.png)

![](./media/image34.png)

7.  Renomeie a consulta como +++**Clone Tables+++**.

> ![](./media/image35.png)
>
> ![](./media/image36.png)

### Tarefa 2: Clonar uma tabela entre esquemas no mesmo warehouse

Nesta tarefa, você aprenderá a clonar uma tabela entre esquemas dentro
do mesmo warehouse.

1.  Para criar uma nova consulta, na faixa de opções **Home**, selecione
    **New SQL query**.

> ![](./media/image37.png)

2.  No editor de consultas, cole o código a seguir. O código cria um
    esquema e, em seguida, cria clones das tabelas **fact_sale** e
    **dimension_city** no novo esquema. Execute a consulta.

```
--Create a new schema within the warehouse named dbo1.
 CREATE SCHEMA dbo1;
 GO

 --Create a clone of dbo.fact_sale table in the dbo1 schema.
 CREATE TABLE [dbo1].[fact_sale1] AS CLONE OF [dbo].[fact_sale];

 --Create a clone of dbo.dimension_city table in the dbo1 schema.
 CREATE TABLE [dbo1].[dimension_city1] AS CLONE OF [dbo].[dimension_city];
```
> ![](./media/image38.png)

3.  Quando a execução for concluída, visualize os dados carregados na
    tabela **dimension_city1**, no esquema **dbo1**.

> ![](./media/image39.png)

4.  Para criar clones das tabelas a partir de um *ponto anterior no
    tempo*, no editor de consultas, cole o código a **seguir para
    substituir as instruções existentes**. O código cria um clone da
    tabela **dimension_city** e da tabela **fact_sale** em determinados
    pontos no tempo, no novo esquema. Execute a consulta.

```
--Create a clone of the dbo.dimension_city table in the dbo1 schema.
CREATE TABLE [dbo1].[dimension_city2] AS CLONE OF [dbo].[dimension_city] AT '2025-01-01T10:00:00.000';

--Create a clone of the dbo.fact_sale table in the dbo1 schema.
CREATE TABLE [dbo1].[fact_sale2] AS CLONE OF [dbo].[fact_sale] AT '2025-01-01T10:00:00.000';
```
> ![](./media/image40.png)

5.  Quando a execução for concluída, visualize os dados carregados na
    tabela **fact_sale2**, no esquema **dbo1**.

> ![](./media/image41.png)

6.  Renomeie a consulta como +++**Clone Tables Across Schemas**+++.

> ![](./media/image42.png)
>
> ![](./media/image43.png)

## Exercício 4: Transformar dados usando um procedimento armazenado

### Tarefa 1: Criar um procedimento armazenado

Nesta tarefa, você aprenderá a criar um procedimento armazenado para
transformar dados em uma tabela de um warehouse.

1.  Na página **WideWorldImporters**, acesse a guia **Home**, selecione
    **SQL** no menu suspenso e clique em **New SQL query**.

![](./media/image44.png)

2.  No editor de consultas, cole o código a seguir. O código exclui o
    procedimento armazenado, caso ele já exista, e depois cria um
    procedimento armazenado chamado **populate_aggregate_sale_by_city**.
    A lógica do procedimento armazenado cria uma tabela chamada
    **aggregate_sale_by_date_city** e insere dados nela usando uma
    consulta de agrupamento (group by) que faz a junção das tabelas
    **fact_sale** e **dimension_city**.

```
--Drop the stored procedure if it already exists.
 DROP PROCEDURE IF EXISTS [dbo].[populate_aggregate_sale_by_city];
 GO

 --Create the populate_aggregate_sale_by_city stored procedure.
 CREATE PROCEDURE [dbo].[populate_aggregate_sale_by_city]
 AS
 BEGIN
     --Drop the aggregate table if it already exists.
     DROP TABLE IF EXISTS [dbo].[aggregate_sale_by_date_city];
     --Create the aggregate table.
     CREATE TABLE [dbo].[aggregate_sale_by_date_city]
     (
        [Date] [DATETIME2](6),
        [City] [VARCHAR](8000),
        [StateProvince] [VARCHAR](8000),
        [SalesTerritory] [VARCHAR](8000),
        [SumOfTotalExcludingTax] [DECIMAL](38,2),
        [SumOfTaxAmount] [DECIMAL](38,6),
        [SumOfTotalIncludingTax] [DECIMAL](38,6),
        [SumOfProfit] [DECIMAL](38,2)
     );

     --Load aggregated data into the table.
     INSERT INTO [dbo].[aggregate_sale_by_date_city]
     SELECT
        FS.[InvoiceDateKey] AS [Date], 
        DC.[City], 
        DC.[StateProvince], 
        DC.[SalesTerritory], 
        SUM(FS.[TotalExcludingTax]) AS [SumOfTotalExcludingTax], 
        SUM(FS.[TaxAmount]) AS [SumOfTaxAmount], 
        SUM(FS.[TotalIncludingTax]) AS [SumOfTotalIncludingTax], 
        SUM(FS.[Profit]) AS [SumOfProfit]
     FROM [dbo].[fact_sale] AS FS
     INNER JOIN [dbo].[dimension_city] AS DC
        ON FS.[CityKey] = DC.[CityKey]
     GROUP BY
        FS.[InvoiceDateKey],
        DC.[City], 
        DC.[StateProvince], 
        DC.[SalesTerritory]
     ORDER BY 
        FS.[InvoiceDateKey], 
        DC.[StateProvince], 
        DC.[City];
 END;
```
> ![](./media/image45.png)

3.  Para executar a consulta, na faixa de opções do designer de
    consultas, selecione **Run**.

> ![](./media/image46.png)

4.  Quando a execução for concluída, renomeie a consulta como
    **+++Create Aggregate Procedure+++.**

> ![A screenshot of a computer Description automatically
> generated](./media/image47.png)
>
> ![](./media/image48.png)

5.  No painel **Explorer**, dentro da pasta **Stored Procedures** do
    esquema **dbo**, verifique se o procedimento armazenado
    **aggregate_sale_by_date_city** existe.

![](./media/image49.png)

### Tarefa 2: Executar o procedimento armazenado

Nesta tarefa, você aprenderá a executar o procedimento armazenado para
transformar dados em uma tabela do warehouse.

1.  Na página **WideWorldImporters**, acesse a guia **Home**, selecione
    **SQL** no menu suspenso e clique em **New SQL query**.

> ![](./media/image50.png)

2.  No editor de consultas, cole o código a seguir. O código executa o
    procedimento armazenado **populate_aggregate_sale_by_city**. Execute
    a consulta.

```
--Execute the stored procedure to create and load aggregated data.
 EXEC [dbo].[populate_aggregate_sale_by_city];
```

![](./media/image51.png)

3.  Quando a execução for concluída, renomeie a consulta como +++**Run
    Aggregate Procedure**+++.

> ![](./media/image52.png)
>
> ![](./media/image53.png)

4.  Para visualizar uma prévia dos dados agregados, no painel
    **Explorer**, selecione a tabela **aggregate_sale_by_date_city**.

> ![](./media/image54.png)

**Observação:** Se a tabela não aparecer, selecione as reticências
(**...**) da pasta **Tables** e, em seguida, selecione **Refresh**.

## Exercício 5: Viagem no tempo usando T-SQL no nível da instrução

### Tarefa 1: Trabalhar com consultas de viagem no tempo

Nesta tarefa, você aprenderá a criar uma exibição dos 10 principais
clientes por vendas. Você usará essa exibição na próxima tarefa para
executar consultas de viagem no tempo.

1.  Na página **WideWorldImporters**, acesse a guia **Home**, selecione
    **SQL** no menu suspenso e clique em **New SQL query**.

![](./media/image55.png)

2.  No editor de consultas, cole o código a seguir. O código cria uma
    exibição chamada Top10Customers. A exibição usa uma consulta para
    recuperar os 10 principais clientes com base nas vendas. Selecione
    **Run** para executar a consulta.

```
--Create the Top10Customers view.
CREATE VIEW [dbo].[Top10Customers]
AS
SELECT TOP(10)
    FS.[CustomerKey],
    DC.[Customer],
    SUM(FS.[TotalIncludingTax]) AS [TotalSalesAmount]
FROM
    [dbo].[dimension_customer] AS DC
    INNER JOIN [dbo].[fact_sale] AS FS
        ON DC.[CustomerKey] = FS.[CustomerKey]
GROUP BY
    FS.[CustomerKey],
    DC.[Customer]
ORDER BY
    [TotalSalesAmount] DESC;
```
> ![](./media/image56.png)

3.  Quando a execução for concluída, renomeie a consulta como
    +++**Create Top 10 Customer View**+++.

![](./media/image57.png)

![](./media/image58.png)

3.  No **Explorer**, verifique se você consegue visualizar a exibição
    recém-criada **Top10CustomersView**, expandindo o nó **View** no
    esquema **dbo**.

![](./media/image59.png)

4.  Crie outra nova consulta, semelhante à Etapa 1. Na guia **Home** da
    faixa de opções, selecione **New SQL query**.

> ![](./media/image60.png)

5.  No editor de consultas, cole o código a seguir. O código atualiza o
    valor de **TotalIncludingTax** de uma única linha da tabela
    fact_sale para aumentar deliberadamente o total de vendas. Ele
    também recupera o carimbo de data/hora atual.

```
--Update the TotalIncludingTax for a single fact row to deliberately inflate its total sales.
 UPDATE [dbo].[fact_sale]
 SET [TotalIncludingTax] = 200000000
 WHERE [SaleKey] = 22632918; --For customer 'Tailspin Toys (Muir, MI)'
 GO

 --Retrieve the current (UTC) timestamp.
 SELECT CURRENT_TIMESTAMP;
```
![](./media/image61.png)

6.  Copie o valor do carimbo de data/hora retornado para a área de
    transferência.

![](./media/image62.png)

**Observação:** Atualmente, você só pode usar o fuso horário Coordinated
Universal Time (UTC) para a viagem no tempo.

7.  Quando a execução for concluída, renomeie a consulta como +++**Time
    Travel**+++.

![](./media/image63.png)

![](./media/image64.png)

8.  Cole o código a seguir no editor de consultas e substitua o valor do
    carimbo de data/hora pelo valor do carimbo de data/hora atual obtido
    na etapa anterior. O formato da sintaxe do carimbo de data/hora é
    **YYYY-MM-DDTHH:MM:SS.**

9.  Remova os zeros à direita. Por exemplo: **2026-07-27T06:20:55.823**.

&nbsp;

10. Para recuperar os 10 principais clientes até o momento *atual*, em
    um novo editor de consultas, cole a instrução a seguir. O código
    recupera os 10 principais clientes usando a dica de consulta FOR
    TIMESTAMP AS OF.

11. Substitua YOUR_TIMESTAMP pelo carimbo de data/hora que você copiou
    para a área de transferência.

```
--Retrieve the top 10 customers as of now.
 SELECT *
 FROM [dbo].[Top10Customers]
 OPTION (FOR TIMESTAMP AS OF 'YOUR_TIMESTAMP');
```

![](./media/image65.png)

12. Renomeie a consulta como +++**Time Travel Now+++**

> ![](./media/image66.png)
>
> ![](./media/image67.png)

13. Observe que o segundo valor de **CustomerKey** entre os 10
    principais clientes é **49**, correspondente à Tailspin Toys (Muir,
    MI).

> ![](./media/image68.png)

14. Modifique o valor do carimbo de data/hora para um horário anterior,
    ***subtraindo um minuto*** do carimbo de data/hora

15. Execute a consulta novamente e observe que o segundo **valor de
    CustomerKey entre os principais é 381** para **Wingtip Toys
    (Sarversville, PA).**

## Exercício 6: Criar uma consulta com o construtor de consultas visuais em um Warehouse

### Tarefa 1: Usar o construtor de consultas visuais

Nesta tarefa, você aprenderá a criar uma consulta usando o construtor de
consultas visuais.

1.  Na faixa de opções **Home**, abra a lista suspensa **New SQL query**
    e, em seguida, selecione **New visual query**.

![](./media/image69.png)

2.  No painel **Explorer**, na pasta **Tables** do esquema dbo, arraste
    a tabela **fact_sale** para a tela da consulta visual.

![](./media/image70.png)

3.  Navegue até a **faixa de opções** **transformations** do painel de
    design da consulta e limite o tamanho do conjunto de dados clicando
    no menu suspenso **Reduce rows** e, em seguida, em **Keep top
    rows**, conforme mostrado na imagem abaixo.

![](./media/image71.png)

4.  Na caixa de diálogo **Keep top rows**, insira +++**10000**+++ e
    selecione **OK**.

![](./media/image72.png)

![](./media/image73.png)

5.  No painel **Explorer**, na pasta **Tables** do esquema dbo, arraste
    a tabela **dimension_city** para a tela da consulta visual.

6.  Clique com o botão direito do mouse em **dimension_city** e
    selecione **Insert into canvas**.

> ![](./media/image74.png)

![](./media/image75.png)

6.  Na faixa de opções **transformations**, selecione o menu suspenso ao
    lado de **Combine** e selecione **Merge queries as new**, conforme
    mostrado na imagem abaixo.

![](./media/image76.png)

7.  Na página **Merge** settings, insira os seguintes detalhes:

- No menu suspenso **Left table for merge**, selecione
  **dimension_city**.

- No menu suspenso **Right table for merge**, selecione **fact_sale**
  (use as barras de rolagem horizontal e vertical).

- Selecione o campo **CityKey** na tabela **dimension_city**, clicando
  no nome da coluna na linha de cabeçalho para indicar a coluna de
  junção.

- Selecione o campo **CityKey** na tabela **fact_sale**, clicando no
  nome da coluna na linha de cabeçalho para indicar a coluna de junção.

- Na seleção do diagrama **Join kind**, escolha **Inner** e clique no
  botão **OK**.

![](./media/image77.png)

![](./media/image78.png)

8.  Com a etapa **Merge** selecionada, selecione o botão **Expand** ao
    lado de **fact_sale** no cabeçalho da grade de dados, conforme
    mostrado na imagem abaixo. Em seguida, selecione as colunas
    **TaxAmount**, **Profit**, **TotalIncludingTax** e selecione **OK.**

![](./media/image79.png)

![](./media/image80.png)

![](./media/image81.png)

9.  Na **faixa de opções** **transformations**, clique no menu suspenso
    ao lado de **Transform** e selecione **Group by**.

![](./media/image82.png)

10. Na página **Group by settings**, insira os seguintes detalhes:

- Selecione o botão de opção **Advanced**.

- Em **Group by**, selecione:

  1.  **Country**

  2.  **StateProvince**

  3.  **City**

- Em **New column name**, insira **SumOfTaxAmount**. No campo
  **Operation**, selecione **Sum** e, em **Column field**, selecione
  **TaxAmount**. Clique em **Add aggregation** para adicionar outra
  coluna e operação de agregação.

- Em **New column name**, insira **SumOfProfit**. No campo
  **Operation**, selecione **Sum** e, em **Column field**, selecione
  **Profit**. Clique em **Add aggregation** para adicionar outra coluna
  e operação de agregação.

- Em **New column name**, insira **SumOfTotalIncludingTax**. No campo
  **Operation**, selecione **Sum** e, em **Column field**, selecione
  **TotalIncludingTax.** 

- Clique no botão **OK.**

![](./media/image83.png)

![](./media/image84.png)

11. No Explorer, navegue até **Queries** e clique com o botão direito do
    mouse em **Visual query 1**, dentro de **Queries**. Em seguida,
    selecione **Rename**.

![](./media/image85.png)

12. Digite +++**Sales Summary**+++ para alterar o nome da consulta.
    Pressione **Enter** no teclado ou selecione qualquer área fora da
    guia para salvar a alteração.

![](./media/image86.png)

13. Clique no ícone **Refresh** abaixo da guia **Home**.

![A screenshot of a computer Description automatically
generated](./media/image87.png)

## Exercício 7: Analisar dados com um notebook

### Tarefa 1: Criar um notebook T-SQL

Nesta tarefa, você aprenderá a criar um notebook T-SQL.

1.  Na faixa de opções **Home**, abra a lista suspensa **New SQL query**
    e, em seguida, selecione **New SQL query in notebook**.

> ![](./media/image88.png)

2.  No painel **Explorer**, selecione **Warehouses** para exibir os
    objetos do warehouse **Wide World Importers**.

3.  Para gerar um modelo de SQL para explorar os dados, à direita da
    tabela **dimension_city**, selecione as reticências (**...**) e, em
    seguida, selecione **SELECT TOP 100**.

> ![](./media/image89.png)

4.  Para executar o código T-SQL nesta célula, selecione o botão **Run
    cell** correspondente à célula de código.

> ![](./media/image90.png)

5.  Revise o resultado da consulta no painel de resultados.

> ![](./media/image91.png)

### Tarefa 2: Criar um atalho para um lakehouse e analisar dados com um notebook

Nesta tarefa, você aprenderá a criar um atalho para um lakehouse e
analisar dados com um notebook.

1.  No menu à esquerda, selecione o ícone do workspace
    **Warehouse_Fabric65897@lab.labinstance.id** e, em seguida,
    selecione o nome do workspace.

> ![](./media/image92.png)

2.  Selecione **+ New Item** para exibir a lista completa de tipos de
    itens disponíveis.

3.  Na lista, na seção **Store data**, selecione o tipo de item
    **Lakehouse**.

> ![](./media/image93.png)

4.  Quando o provisionamento for concluído, insira
    +++**Shortcut_Exercise**+++ como o nome do lakehouse e desmarque
    lakehouses schemas. Selecione **Create**.![](./media/image94.png)

> ![](./media/image95.png)

5.  Quando o novo lakehouse for aberto, na home page, selecione a opção
    **New shortcut**.

> ![](./media/image96.png)

6.  Na janela **New shortcut**, selecione a opção **Microsoft OneLake**.

> ![](./media/image97.png)

7.  Na janela **Select a data source type**, selecione o warehouse
    **Wide World Importers** e, em seguida, selecione **Next**.

> ![](./media/image98.png)

8.  Clique em Connect

> ![](./media/image99.png)

9.  No navegador de **objetos OneLake**, expanda **Tables**, expanda o
    esquema **dbo** e, em seguida, marque a caixa de seleção da tabela
    **dimension_customer**. Selecione **Next**.

> ![](./media/image100.png)

10. Selecione **Create**.

> ![](./media/image101.png)

11. No painel **Explorer**, selecione a tabela **dimension_customer**
    para visualizar uma prévia dos dados e, em seguida, revise os dados
    recuperados da tabela dimension_customer no warehouse.

> ![](./media/image102.png)

12. Na página da tabela **dimension_customer**, clique em **Analyze data
    with**, selecione **Notebook** e, em seguida, escolha **New
    notebook** para criar um novo notebook do Spark para análise de
    dados.

> ![](./media/image103.png)

13. No painel **Explorer**, selecione **Lakehouses**.

14. Arraste a tabela **dimension_customer** para a célula aberta do
    notebook.

> ![](./media/image104.png)

15. Observe a consulta **PySpark** que foi adicionada à célula do
    notebook. Essa consulta recupera as primeiras **1.000 linhas** do
    atalho **Shortcut_Exercise.dimension_customer**. Essa experiência de
    notebook é semelhante à experiência de um notebook Jupyter no VS
    Code. Você também pode abrir o notebook no VS Code.

> ![](./media/image105.png)

16. Na faixa de opções **Home**, selecione o botão **Run all**.

> ![](./media/image106.png)
>
> ![](./media/image107.png)

## Exercício 8: Criar consultas entre warehouses com o editor de consultas SQL

### Tarefa 1: Adicionar vários warehouses ao Explorer

Nesta tarefa, você aprenderá como criar e executar facilmente consultas
T-SQL com o editor de consultas SQL em vários warehouses, incluindo a
combinação de dados de um SQL Endpoint e de um Warehouse no Microsoft
Fabric.

1.  Na página **Notebook2**, navegue até o menu de navegação à esquerda
    e clique no workspace **WideWorldImporters**.

> ![](./media/image108.png)

2.  No painel **Explorer**, selecione **+ Warehouses**.

![](./media/image109.png)

3.  Na janela **OneLake catalog**, selecione o SQL analytics endpoint do
    **Shortcut_Exercise**. Selecione **Confirm**.

![](./media/image110.png)

4.  No painel **Explorer**, observe que o SQL analytics endpoint do
    **Shortcut_Exercise** está disponível.

![](./media/image111.png)

### Tarefa 2: Executar a consulta entre warehouses

Nesta tarefa, você aprenderá a executar uma consulta entre warehouses.
Especificamente, você executará uma consulta que faz a junção do
warehouse Wide World Importers com o SQL analytics endpoint do
Shortcut_Exercise.

** Observação:** Uma consulta entre bancos de dados usa a nomenclatura
de três partes *database.schema.table* para fazer referência aos
objetos.

1.  Na guia **Home** da faixa de opções, selecione **New SQL query**.

![](./media/image112.png)

2.  No editor de consultas, cole o código a seguir. O código recupera um
    agregado da quantidade vendida por item de estoque, descrição e
    cliente.

```
--Retrieve an aggregate of quantity sold by stock item, description, and customer.
SELECT
    Sales.StockItemKey,
    Sales.Description,
    c.Customer,
    SUM(CAST(Sales.Quantity AS int)) AS SoldQuantity
FROM
    [dbo].[fact_sale] AS Sales
    INNER JOIN [Shortcut_Exercise].[dbo].[dimension_customer] AS c
        ON Sales.CustomerKey = c.CustomerKey
GROUP BY
    Sales.StockItemKey,
    Sales.Description,
    c.Customer;
```

3.  **Execute** a consulta e revise o resultado da consulta.

![](./media/image113.png)

![](./media/image114.png)

3.  Renomeie a consulta para referência. No **Explorer**, clique com o
    botão direito do mouse em **SQL query** e selecione **Rename**.

> ![](./media/image115.png)

![](./media/image116.png)

4.  Na caixa de diálogo **Rename**, no campo **Name**, insira
    +++**Cross-warehouse query**+++ e, em seguida, clique no botão
    **Rename**. 

> ![](./media/image117.png)

## Exercício 9: Criar um modelo semântico Direct Lake e um relatório do Power BI

### Tarefa 1: Criar um modelo semântico

Nesta tarefa, você aprenderá a criar um modelo semântico Direct Lake com
base no warehouse Wide World Importers.

1.  Na página **WideWorldImporters**, na guia **Home**, selecione **New
    semantic model**.

![](./media/image118.png)

2.  Na janela **New semantic model**, no campo **Direct Lake semantic
    model name**, insira +++**Sales Model**+++.

3.  Expanda o esquema **dbo**, expanda a pasta **Tables** e, em seguida,
    marque as tabelas **dimension_city** e **fact_sale**. Selecione
    **Confirm**.

> ![](./media/image119.png)

9.  Na navegação à esquerda, selecione ***Warehouse_FabricXXXXX***,
    conforme mostrado na imagem abaixo.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image120.png)

10. Para abrir o modelo semântico, volte à home page do workspace e, em
    seguida, selecione o modelo semântico **Sales Model**.

![](./media/image121.png)

![](./media/image122.png)

12. Na página **Sales Model**, para editar **Manage Relationships**,
    altere o modo de **Viewing** para **Editing**.![A screenshot of a
    computer AI-generated content may be
    incorrect.](./media/image123.png)

13. Para criar um relacionamento, no designer do modelo, na faixa de
    opções **Home**, selecione **Manage relationships**.

![](./media/image124.png)

14. Na janela **Manage relationships**, selecione **+ New
    relationship**.

![](./media/image125.png)

14. Na janela **New relationship**, conclua as etapas a seguir para
    criar o relacionamento:

-  Na lista suspensa **From table**, selecione a tabela
  **dimension_city**.

- Na lista suspensa **To table**, selecione a tabela **fact_sale**.

- Na lista suspensa **Cardinality**, selecione **One to many (1:\*)**.

- Na lista suspensa **Cross-filter direction**, selecione **Single**.

- Marque a caixa **Assume referential integrity**.

- Selecione **Save**.

![](./media/image126.png)

![](./media/image127.png)

15. Na janela **Manage relationship**, selecione **Close**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image128.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image129.png)

### Tarefa 2: Criar um relatório do Power BI

Nesta tarefa, você aprenderá a criar um relatório do Power BI com base
no modelo semântico criado na tarefa.

1.  Na faixa de opções **File**, selecione **Create new report**.

![](./media/image130.png)

2.  No designer de relatórios, conclua as etapas a seguir para criar um
    visual de gráfico de colunas:

-  No painel **Data**, expanda a tabela **fact_sale** e marque o campo
  Profit.

- No painel **Data**, expanda a tabela dimension_city e marque o campo
  SalesTerritory.

![](./media/image131.png)

3.  No painel **Visualizations**, selecione o visual **Azure Map**.

![](./media/image132.png)

4.  No painel **Data**, dentro da tabela dimension_city, arraste o campo
    StateProvince para a área **Location** no painel **Visualizations**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image133.png)

5.  No painel **Data**, dentro da tabela fact_sale, marque o campo
    Profit para adicioná-lo à área **Size** do visual de mapa.

6.  No painel **Visualizations**, selecione o visual **Table**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image134.png)

7.  No painel **Data**, marque os seguintes campos:

- SalesTerritory da tabela dimension_city

- StateProvince da tabela dimension_city

- Profit da tabela fact_sale

- TotalExcludingTax da tabela fact_sale

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image135.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image136.png)

8.  Verifique se o design concluído da página do relatório é semelhante
    à imagem a seguir.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image137.png)

9.  To save the report, on the **Home** ribbon,
    select **File** \> **Save**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image138.png)

10. Na janela Save your report, na caixa Enter a name for your report,
    insira +++**Sales Analysis**+++ e selecione **Save**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image139.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image140.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image141.png)

### Tarefa 3: Limpar recursos

Você pode excluir relatórios, pipelines, warehouses e outros itens
individualmente ou remover todo o workspace. Neste tutorial, você
limpará o workspace, os relatórios individuais, pipelines, warehouses e
outros itens criados como parte do laboratório.

1.  Selecione **Warehouse_FabricXX** no menu de navegação para voltar à
    lista de itens do workspace.

![](./media/image142.png)

2.  No menu do cabeçalho do workspace, selecione **Workspace settings**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image143.png)

3.  Na caixa de diálogo **Workspace settings**, selecione **General** e,
    em seguida, selecione **Remove this workspace**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image144.png)

4.  Na caixa de diálogo **Delete workspace?**, clique no botão
    **Delete**.
 ![](./media/image145.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image146.png)

**Resumo**

Este laboratório abrangente apresenta uma série de tarefas destinadas a
estabelecer um ambiente funcional de dados no Microsoft Fabric. Ele
começa com a criação de um workspace, essencial para as operações de
dados, e garante que a avaliação gratuita esteja habilitada. Em seguida,
um Warehouse chamado WideWorldImporters é criado no ambiente do Fabric
para servir como repositório central para armazenamento e processamento
de dados. A ingestão de dados no workspace Warehouse_FabricXX é então
detalhada por meio da implementação de um pipeline do Data Factory. Esse
processo envolve a obtenção de dados de fontes externas e sua integração
ao workspace. As tabelas essenciais, dimension_city e fact_sale, são
criadas no data warehouse para servir como estruturas fundamentais para
a análise de dados. O processo de carregamento de dados continua com o
uso de T-SQL, no qual os dados do Armazenamento de Blobs do Azure são
transformados nas tabelas especificadas. As etapas seguintes abordam o
gerenciamento e a manipulação de dados. A clonagem de tabelas é
demonstrada, oferecendo uma técnica valiosa para fins de replicação e
teste. Além disso, o processo de clonagem é estendido a um esquema
diferente (dbo1) dentro do mesmo warehouse, demonstrando uma abordagem
estruturada. O laboratório avança para a transformação de dados,
introduzindo a criação de um procedimento armazenado para agregar dados
de vendas de forma eficiente. Em seguida, passa para a criação de
consultas visuais, oferecendo uma interface intuitiva para consultas de
dados complexas. Isso é seguido por uma exploração de notebooks,
demonstrando sua utilidade para consultar e analisar dados da tabela
dimension_customer. Em seguida, são apresentadas as funcionalidades de
consulta em vários warehouses, permitindo a recuperação integrada de
dados entre diferentes warehouses dentro do workspace. O laboratório
culmina na habilitação da integração de visuais do Mapas do Azure,
aprimorando a representação de dados geográficos no Power BI. Na
sequência, uma variedade de relatórios do Power BI, incluindo gráficos
de colunas, mapas e tabelas, é criada para facilitar uma análise
aprofundada dos dados de vendas. A tarefa final concentra-se na geração
de um relatório a partir do OneLake data hub, reforçando ainda mais a
versatilidade das fontes de dados no Fabric. Por fim, o laboratório
apresenta informações sobre o gerenciamento de recursos, enfatizando a
importância dos procedimentos de limpeza para manter um workspace
eficiente. Em conjunto, essas tarefas proporcionam uma compreensão
abrangente da configuração, do gerenciamento e da análise de dados no
Microsoft Fabric.
