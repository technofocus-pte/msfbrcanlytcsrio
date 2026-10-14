# Caso de uso 03: Criar um data warehouse de vendas e geografia para a Contoso no Microsoft Fabric

**Cenário**

A Contoso é uma empresa multinacional de varejo que deseja modernizar sua infraestrutura de dados para aprimorar a análise de vendas e geografia. Atualmente, os dados de vendas e de clientes da empresa estão espalhados por vários sistemas, o que dificulta para analistas de negócios e desenvolvedores cidadãos obter insights. A empresa planeja usar o Microsoft Fabric para consolidar esses dados em uma plataforma unificada, oferecendo suporte a consultas entre warehouses, análise de vendas e relatórios geográficos.

Neste laboratório, você assumirá o papel de engenheiro de dados da Contoso, responsável por projetar e implementar uma solução de data warehouse usando o Microsoft Fabric. Você começará configurando um workspace do Fabric e criando um warehouse; em seguida, carregará os dados de exemplo e executará uma série de tarefas analíticas para fornecer insights aos tomadores de decisão da Contoso.

**Introdução**

Embora muitos conceitos do Microsoft Fabric possam ser familiares para profissionais de dados e análise, aplicá-los em um novo ambiente pode ser desafiador. Este laboratório conduz você passo a passo por um cenário completo, da ingestão ao consumo dos dados, para criar uma compreensão básica da experiência do usuário do Microsoft Fabric, das várias cargas de trabalho e seus pontos de integração e das experiências de desenvolvedores profissionais e cidadãos.

**Itens do Fabric criados neste laboratório**

| **Item** | **Nome** | **Função no laboratório** |
|----|----|----|
| Workspace | Warehouse_Fabric\<ID da instância do laboratório\> | Contém todos os itens do laboratório |
| Warehouse | WideWorldImporters | Armazena os dados de exemplo da Wide World Importers, as tabelas clonadas, o procedimento armazenado e as exibições |
| Copy job | Load Customer Data | Carrega os dados de exemplo no warehouse |
| Lakehouse | Shortcut_Exercise | Contém um atalho para a tabela dimension_customer do warehouse |
| Modelo semântico | Sales Model | Modelo semântico Direct Lake baseado no warehouse |
| Relatório | Sales Analysis | Relatório do Power BI com gráfico de colunas, mapa e tabela |

**Objetivos**:

- Criar um workspace do Microsoft Fabric e um warehouse chamado WideWorldImporters.

- Carregar os dados de exemplo da Wide World Importers no warehouse usando um copy job.

- Clonar tabelas com T-SQL no mesmo esquema e entre esquemas (dbo1), incluindo clones de um ponto no tempo.

- Criar e executar um procedimento armazenado para transformar dados e criar a tabela aggregate_sale_by_date_city.

- Usar a viagem no tempo (time travel) no nível da instrução com T-SQL.

- Combinar e resumir dados com o construtor de consultas visuais.

- Consultar e analisar dados com notebooks T-SQL e notebooks do Spark.

- Executar consultas entre o warehouse WideWorldImporters e o SQL analytics endpoint Shortcut_Exercise.

- Criar um modelo semântico Direct Lake e um relatório do Power BI com visuais de gráfico de colunas, mapa e tabela.

- Excluir o workspace e os itens associados.

## Exercício 1: Criar um workspace do Microsoft Fabric

Neste exercício, você faz login no Microsoft Fabric, cria um workspace e cria um warehouse nele.

### Tarefa 1: Criar um workspace

1.  Abra o navegador, vá até a barra de endereços e digite ou cole a seguinte URL: +++https://app.fabric.microsoft.com/+++. Em seguida, pressione a tecla **Enter**.

**Observação**: Se você for direcionado diretamente para a página inicial do Microsoft Fabric, pule para a etapa 6.

![](./media/image1.png)

2.  Na janela do **Microsoft Fabric**, insira suas credenciais e clique no botão **Submit**.

| **Nome de usuário** | **+++@lab.CloudPortalCredential(User1).Username+++** |
|----|----|
| **Senha** | **+++@lab.CloudPortalCredential(User1).Password+++** |

![](./media/image2.png)

3.  Na janela **Microsoft**, insira a senha e clique no botão **Sign in**.

![](./media/image3.png)

4.  Na janela **Stay signed in?**, clique no botão **Yes**.

5.  Se o Power BI abrir por padrão, siga as etapas abaixo; caso contrário, ignore esta etapa:

- Clique em **Power BI**.

![](./media/image4.png)

- Selecione **Fabric** entre as opções exibidas.

![](./media/image5.png)

6.  Na página inicial do Fabric, selecione o bloco **+ New workspace**.

![](./media/image6.png)

7.  No painel **Create a workspace**, insira os seguintes detalhes e clique no botão **Apply**.

| **Propriedade** | **Valor** |
|----|----|
| **Name** | +++Warehouse_Fabric@lab.LabInstance.Id+++ (deve ser um nome exclusivo) |
| **Description** | +++This workspace contains all the artifacts for the data warehouse+++ |
| **Advanced** | Em **License mode**, selecione **Fabric** |
| **Default storage format** | **Small dataset storage format** |

![](./media/image7.png)

![](./media/image8.png)

![](./media/image9.png)

8.  Aguarde a conclusão da implantação. Esse processo leva de 1 a 2 minutos. Quando o novo workspace for aberto, ele deverá estar vazio.

![](./media/image10.png)

### Tarefa 2: Criar um warehouse no Microsoft Fabric

1.  Na página do workspace, selecione **+ New item** e, em seguida, selecione **Warehouse**.

![](./media/image11.png)

2.  Na caixa de diálogo **New warehouse**, insira +++WideWorldImporters+++ e clique no botão **Create**.

![](./media/image12.png)

3.  Quando o provisionamento for concluído, a página inicial do warehouse **WideWorldImporters** será exibida.

![](./media/image13.png)

## Exercício 2: Ingerir dados em um warehouse no Microsoft Fabric

Neste exercício, você usa um copy job para carregar os dados de exemplo da Wide World Importers no warehouse.

### Tarefa 1: Ingerir dados no warehouse

1.  Na página inicial do warehouse **WideWorldImporters**, selecione **Warehouse_Fabric@lab.LabInstance.Id** no menu de navegação à esquerda para voltar à lista de itens do workspace.

![](./media/image14.png)

2.  Na página do workspace, selecione **+ New item** e, em **Get data**, clique em **Copy job**.

![](./media/image15.png)

3.  Na janela **New copy job**, insira +++Load Customer Data+++ na caixa **Name** e clique em **Create**.

![](./media/image16.png)

4.  A página **Copy job** é aberta.

![](./media/image17.png)

5.  Na primeira página do assistente **Copy job**, selecione **Sample data** na barra de menus e selecione a amostra **Retail Data Model from Wide World Importers** para passar para a próxima página.

![](./media/image18.png)

6.  Uma prévia dos dados de exemplo é carregada. Na página **Choose data**, você pode visualizar o conjunto de dados selecionado. Depois de revisar os dados, clique em **Next**.

![](./media/image19.png)

7.  Na página **Choose data destination**, selecione o warehouse **WideWorldImporters** no catálogo do OneLake e clique em **Next**.

![](./media/image20.png)

8.  Na página **Choose copy job mode**, selecione **Full copy** e clique em **Next**.

![](./media/image21.png)

9.  Insira as seguintes tabelas de destino e clique em **Next**.

- +++dbo.dimension_city+++

- +++dbo.dimension_customer+++

- +++dbo.dimension_date+++

- +++dbo.dimension_employee+++

- +++dbo.dimension_stock_item+++

- +++dbo.fact_sale+++

![](./media/image22.png)

10. Na página **Review + save**, revise a **Source** e o **Destination** e salve.

![](./media/image23.png)

11. Use a aba **Results** para monitorar a execução do copy job.

![](./media/image24.png)

12. Quando concluído, o **Copy job** exibe a notificação e o status **Succeeded**. Agora você verá seis novas tabelas do conjunto de dados da Wide World Importers no warehouse.

![](./media/image25.png)

13. Na página **Load Customer Data**, clique no workspace **Warehouse_Fabric@lab.LabInstance.Id** na barra de navegação à esquerda e selecione o warehouse **WideWorldImporters**.

![](./media/image26.png)

14. No warehouse **WideWorldImporters**, expanda **Schemas \> dbo \> Tables** e verifique se as tabelas **dimension_city**, **dimension_customer**, **dimension_date**, **dimension_employee**, **dimension_stock_item** e **fact_sale** foram criadas com sucesso.

![](./media/image27.png)

## Exercício 3: Clonar tabelas com T-SQL em um warehouse

Neste exercício, você usa T-SQL para clonar tabelas no mesmo esquema e entre esquemas, incluindo clones de um ponto no tempo do passado.

### Tarefa 1: Clonar tabelas no mesmo esquema

1.  Na página **WideWorldImporters**, vá até a aba **Home** e clique em **New SQL query** no menu suspenso **SQL**.

![](./media/image28.png)

2.  Cole o código a seguir no editor de consultas. Esse código cria clones das tabelas **dimension_city** e **fact_sale**.

```sql
--Create a clone of the dbo.dimension_city table.
CREATE TABLE [dbo].[dimension_city1] AS CLONE OF [dbo].[dimension_city];

--Create a clone of the dbo.fact_sale table.
CREATE TABLE [dbo].[fact_sale1] AS CLONE OF [dbo].[fact_sale];
```

![](./media/image29.png)

3.  Para executar a consulta, selecione **Run** na faixa de opções de design de consulta.

![](./media/image30.png)

![](./media/image31.png)

4.  Cole o código a seguir no editor de consultas, substituindo a instrução existente. A função T-SQL CURRENT_TIMESTAMP retorna o carimbo de data/hora UTC atual como **datetime**. Selecione **Run** para executar a consulta e copie o valor de carimbo de data/hora retornado.

```sql
SELECT CURRENT_TIMESTAMP;
```

![](./media/image32.png)

5.  Para criar clones das tabelas de *um ponto no tempo do passado*, cole o código a seguir no editor de consultas, **substituindo a instrução existente**. Substitua **YOUR_TIMESTAMP** pelo carimbo de data/hora retornado na etapa anterior (no formato **YYYY-MM-DDTHH:MM:SS.FFF**). Esse código cria clones das tabelas **dimension_city** e **fact_sale** nesse ponto no tempo. Execute a consulta.

```sql
--Create a clone of the dbo.dimension_city table at a specific point in time.
CREATE TABLE [dbo].[dimension_city2] AS CLONE OF [dbo].[dimension_city] AT 'YOUR_TIMESTAMP';

--Create a clone of the dbo.fact_sale table at a specific point in time.
CREATE TABLE [dbo].[fact_sale2] AS CLONE OF [dbo].[fact_sale] AT 'YOUR_TIMESTAMP';
```

**Observação:** O ponto no tempo deve ser posterior à criação das tabelas e estar dentro do período de retenção de dados do warehouse. Se você usar um horário anterior à criação das tabelas (por exemplo, uma data de 2025), o clone falhará.

![](./media/image33.png)

![](./media/image34.png)

6.  Renomeie a consulta como +++Clone Tables+++.

![](./media/image35.png)

![](./media/image36.png)

### Tarefa 2: Clonar tabelas entre esquemas no mesmo warehouse

Nesta tarefa, você aprende a clonar tabelas entre esquemas no mesmo warehouse.

1.  Para criar uma nova consulta, selecione **New SQL query** na faixa de opções **Home**.

![](./media/image37.png)

2.  Cole o código a seguir no editor de consultas. Esse código cria um esquema e, em seguida, cria clones das tabelas **fact_sale** e **dimension_city** no novo esquema. Execute a consulta.

```sql
--Create a new schema within the warehouse named dbo1.
CREATE SCHEMA dbo1;
GO

--Create a clone of dbo.fact_sale table in the dbo1 schema.
CREATE TABLE [dbo1].[fact_sale1] AS CLONE OF [dbo].[fact_sale];

--Create a clone of dbo.dimension_city table in the dbo1 schema.
CREATE TABLE [dbo1].[dimension_city1] AS CLONE OF [dbo].[dimension_city];
```

![](./media/image38.png)

3.  Quando a execução for concluída, visualize os dados carregados na tabela **dimension_city1** do esquema **dbo1**.

![](./media/image39.png)

4.  Para criar clones das tabelas de *um ponto no tempo do passado*, cole o código a seguir no editor de consultas, **substituindo a instrução existente**. Substitua **YOUR_TIMESTAMP** pelo carimbo de data/hora copiado na Tarefa 1. Esse código cria, no novo esquema, clones das tabelas **dimension_city** e **fact_sale** nesse ponto no tempo. Execute a consulta.

```sql
--Create a clone of the dbo.dimension_city table in the dbo1 schema.
CREATE TABLE [dbo1].[dimension_city2] AS CLONE OF [dbo].[dimension_city] AT 'YOUR_TIMESTAMP';

--Create a clone of the dbo.fact_sale table in the dbo1 schema.
CREATE TABLE [dbo1].[fact_sale2] AS CLONE OF [dbo].[fact_sale] AT 'YOUR_TIMESTAMP';
```

![](./media/image40.png)

5.  Quando a execução for concluída, visualize os dados carregados na tabela **fact_sale2** do esquema **dbo1**.

![](./media/image41.png)

6.  Renomeie a consulta como +++Clone Tables Across Schemas+++.

![](./media/image42.png)

![](./media/image43.png)

## Exercício 4: Transformar dados com um procedimento armazenado

Neste exercício, você cria e executa um procedimento armazenado para transformar os dados das tabelas do warehouse.

### Tarefa 1: Criar um procedimento armazenado

1.  Na página **WideWorldImporters**, vá até a aba **Home** e clique em **New SQL query** no menu suspenso **SQL**.

![](./media/image44.png)

2.  Cole o código a seguir no editor de consultas. Esse código exclui o procedimento armazenado (caso exista) e, em seguida, cria um procedimento armazenado chamado **populate_aggregate_sale_by_city**. A lógica do procedimento cria uma tabela chamada **aggregate_sale_by_date_city** e insere dados usando uma consulta agrupada que combina as tabelas **fact_sale** e **dimension_city**.

```sql
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

![](./media/image45.png)

3.  Para executar a consulta, selecione **Run** na faixa de opções de design de consulta.

![](./media/image46.png)

4.  Quando a execução for concluída, renomeie a consulta como +++Create Aggregate Procedure+++.

![](./media/image47.png)

![](./media/image48.png)

5.  No painel **Explorer**, confirme que o procedimento armazenado **populate_aggregate_sale_by_city** está na pasta **Stored Procedures** do esquema **dbo**.

![](./media/image49.png)

### Tarefa 2: Executar o procedimento armazenado

1.  Na página **WideWorldImporters**, vá até a aba **Home** e clique em **New SQL query** no menu suspenso **SQL**.

![](./media/image50.png)

2.  Cole o código a seguir no editor de consultas. Esse código executa o procedimento armazenado **populate_aggregate_sale_by_city**. Execute a consulta.

```sql
--Execute the stored procedure to create and load aggregated data.
EXEC [dbo].[populate_aggregate_sale_by_city];
```

![](./media/image51.png)

3.  Quando a execução for concluída, renomeie a consulta como +++Run Aggregate Procedure+++.

![](./media/image52.png)

![](./media/image53.png)

4.  Para visualizar os dados agregados, selecione a tabela **aggregate_sale_by_date_city** no painel **Explorer**.

![](./media/image54.png)

**Observação:** Se a tabela não aparecer, selecione as reticências (**...**) da pasta **Tables** e selecione **Refresh**.

## Exercício 5: Usar a viagem no tempo com T-SQL no nível da instrução

Neste exercício, você cria uma exibição dos dez principais clientes por vendas e a usa para executar consultas de viagem no tempo.

### Tarefa 1: Usar consultas de viagem no tempo

1.  Na página **WideWorldImporters**, vá até a aba **Home** e clique em **New SQL query** no menu suspenso **SQL**.

![](./media/image55.png)

2.  Cole o código a seguir no editor de consultas. Esse código cria uma exibição chamada **Top10Customers**, que recupera os 10 principais clientes com base nas vendas. Selecione **Run** para executar a consulta.

```sql
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

![](./media/image56.png)

3.  Quando a execução for concluída, renomeie a consulta como +++Create Top 10 Customer View+++.

![](./media/image57.png)

![](./media/image58.png)

4.  No **Explorer**, expanda o nó **Views** do esquema **dbo** e confirme que a nova exibição **Top10Customers** está visível.

![](./media/image59.png)

5.  Assim como na etapa 1, crie uma nova consulta. Na aba **Home** da faixa de opções, selecione **New SQL query**.

![](./media/image60.png)

6.  Cole o código a seguir no editor de consultas. Esse código atualiza o valor de **TotalIncludingTax** de uma única linha de fatos, inflando intencionalmente o total de vendas, e recupera o carimbo de data/hora atual. Execute a consulta.

```sql
--Update the TotalIncludingTax for a single fact row to deliberately inflate its total sales.
UPDATE [dbo].[fact_sale]
SET [TotalIncludingTax] = 200000000
WHERE [SaleKey] = 22632918; --For customer 'Tailspin Toys (Muir, MI)'
GO

--Retrieve the current (UTC) timestamp.
SELECT CURRENT_TIMESTAMP;
```

![](./media/image61.png)

7.  Copie o valor de carimbo de data/hora retornado para a área de transferência.

![](./media/image62.png)

**Observação:** Atualmente, só é possível usar o fuso horário UTC (Tempo Universal Coordenado) para a viagem no tempo.

8.  Quando a execução for concluída, renomeie a consulta como +++Time Travel+++.

![](./media/image63.png)

![](./media/image64.png)

9.  Crie uma nova consulta e cole a instrução a seguir para recuperar os 10 principais clientes *atuais*. Esse código usa a dica de consulta **FOR TIMESTAMP AS OF**. Substitua **YOUR_TIMESTAMP** pelo carimbo de data/hora que você copiou para a área de transferência, no formato **YYYY-MM-DDTHH:MM:SS\[.FFF\]** e sem zeros no final, por exemplo **2026-07-27T06:20:55.823**. Execute a consulta.

```sql
--Retrieve the top 10 customers as of now.
SELECT *
FROM [dbo].[Top10Customers]
OPTION (FOR TIMESTAMP AS OF 'YOUR_TIMESTAMP');
```

![](./media/image65.png)

10. Renomeie a consulta como +++Time Travel Now+++.

![](./media/image66.png)

![](./media/image67.png)

11. Observe que, como você acabou de inflar o total de vendas, o primeiro **CustomerKey** agora é **49**, ou seja, **Tailspin Toys (Muir, MI)**.

![](./media/image68.png)

12. Altere o valor do carimbo de data/hora para um horário anterior, **subtraindo um minuto**.

13. Execute a consulta novamente e observe que o primeiro **CustomerKey** é **381**, ou seja, **Wingtip Toys (Sarversville, PA)**. Esse é o resultado anterior à atualização.

## Exercício 6: Criar uma consulta com o construtor de consultas visuais em um warehouse

Neste exercício, você usa o construtor de consultas visuais para combinar e resumir dados sem escrever código SQL.

### Tarefa 1: Usar o construtor de consultas visuais

1.  Na faixa de opções **Home**, abra a lista suspensa **New SQL query** e selecione **New visual query**.

![](./media/image69.png)

2.  No painel **Explorer**, arraste a tabela **fact_sale** da pasta **Tables** do esquema **dbo** para a tela da consulta visual.

![](./media/image70.png)

3.  Para limitar o tamanho do conjunto de dados, na faixa de opções **transformations** do designer de consultas, clique no menu suspenso **Reduce rows** e, em seguida, em **Keep top rows**.

![](./media/image71.png)

4.  Na caixa de diálogo **Keep top rows**, insira +++10000+++ e selecione **OK**.

![](./media/image72.png)

![](./media/image73.png)

5.  No painel **Explorer**, clique com o botão direito do mouse na tabela **dimension_city** da pasta **Tables** do esquema **dbo** e selecione **Insert into canvas** (você também pode arrastar a tabela diretamente para a tela).

![](./media/image74.png)

![](./media/image75.png)

6.  Na faixa de opções **transformations**, selecione o menu suspenso ao lado de **Combine** e selecione **Merge queries as new**.

![](./media/image76.png)

7.  Na página de configurações **Merge**, insira os seguintes detalhes e clique em **OK**:

- Na lista suspensa **Left table for merge**, selecione **dimension_city**.

- Na lista suspensa **Right table for merge**, selecione **fact_sale** (use as barras de rolagem horizontal e vertical).

- Na tabela **dimension_city**, selecione o nome da coluna **CityKey** na linha de cabeçalho como coluna de junção.

- Na tabela **fact_sale**, selecione o nome da coluna **CityKey** na linha de cabeçalho como coluna de junção.

- Em **Join kind**, selecione **Inner**.

![](./media/image77.png)

![](./media/image78.png)

8.  Com a etapa **Merge** selecionada, clique no botão **Expand** ao lado de **fact_sale** no cabeçalho da grade de dados, selecione as colunas **TaxAmount**, **Profit** e **TotalIncludingTax** e selecione **OK**.

![](./media/image79.png)

![](./media/image80.png)

![](./media/image81.png)

9.  Na faixa de opções **transformations**, clique no menu suspenso ao lado de **Transform** e selecione **Group by**.

![](./media/image82.png)

10. Na página **Group by**, insira os seguintes detalhes:

- Selecione o botão de opção **Advanced**.

- Em **Group by**, selecione as seguintes colunas: **Country**, **StateProvince**, **City**.

- Adicione as seguintes colunas de agregação (depois de adicionar cada uma, clique em **Add aggregation** para adicionar a próxima):

| **New column name** | **Operation** | **Column** |
|----|----|----|
| +++SumOfTaxAmount+++ | **Sum** | **TaxAmount** |
| +++SumOfProfit+++ | **Sum** | **Profit** |
| +++SumOfTotalIncludingTax+++ | **Sum** | **TotalIncludingTax** |

- Clique no botão **OK**.

![](./media/image83.png)

![](./media/image84.png)

11. No **Explorer**, vá até **Queries**, clique com o botão direito do mouse em **Visual query 1** e selecione **Rename**.

![](./media/image85.png)

12. Insira +++Sales Summary+++ para alterar o nome da consulta. Pressione **Enter** ou clique em qualquer lugar fora da aba para salvar a alteração.

![](./media/image86.png)

13. Clique no ícone **Refresh** na aba **Home**.

![](./media/image87.png)

## Exercício 7: Analisar dados com um notebook

Neste exercício, você usa um notebook T-SQL para consultar os dados do warehouse e analisa dados por meio de um atalho de lakehouse e de um notebook do Spark.

### Tarefa 1: Criar um notebook T-SQL

1.  Na faixa de opções **Home**, abra a lista suspensa **New SQL query** e selecione **New SQL query in notebook**.

![](./media/image88.png)

2.  No painel **Explorer**, selecione **Warehouses** para exibir os objetos do warehouse **WideWorldImporters**.

3.  Para gerar um modelo SQL para explorar os dados, selecione as reticências (**...**) à direita da tabela **dimension_city** e selecione **SELECT TOP 100**.

![](./media/image89.png)

4.  Para executar o código T-SQL desta célula, selecione o botão **Run cell** da célula de código.

![](./media/image90.png)

5.  Revise os resultados da consulta no painel de resultados.

![](./media/image91.png)

### Tarefa 2: Criar um atalho de lakehouse e analisar dados com um notebook

1.  No menu à esquerda, selecione o ícone do workspace **Warehouse_Fabric@lab.LabInstance.Id** e selecione o nome do workspace.

![](./media/image92.png)

2.  Selecione **+ New item** para exibir a lista completa de tipos de itens disponíveis.

3.  Na seção **Store data** da lista, selecione o tipo de item **Lakehouse**.

![](./media/image93.png)

4.  Insira +++Shortcut_Exercise+++ como nome do lakehouse e desmarque **Lakehouse schemas**. Selecione **Create**.

![](./media/image94.png)

![](./media/image95.png)

5.  Quando o novo lakehouse for aberto, selecione a opção **New shortcut** na página inicial.

![](./media/image96.png)

6.  Na janela **New shortcut**, selecione a opção **Microsoft OneLake**.

![](./media/image97.png)

7.  Na janela **Select a data source type**, selecione o warehouse **WideWorldImporters** e selecione **Next**.

![](./media/image98.png)

8.  Clique em **Connect**.

![](./media/image99.png)

9.  No navegador **OneLake object**, expanda **Tables**, expanda o esquema **dbo** e marque a caixa de seleção da tabela **dimension_customer**. Selecione **Next**.

![](./media/image100.png)

10. Selecione **Create**.

![](./media/image101.png)

11. No painel **Explorer**, selecione a tabela **dimension_customer** para visualizar os dados e revisar os dados recuperados da tabela **dimension_customer** do warehouse.

![](./media/image102.png)

12. Na página da tabela **dimension_customer**, clique em **Analyze data with**, selecione **Notebook** e selecione **New notebook** para criar um novo notebook do Spark para análise de dados.

![](./media/image103.png)

13. No painel **Explorer**, selecione **Lakehouses**.

14. Arraste a tabela **dimension_customer** para a célula do notebook aberto.

![](./media/image104.png)

15. Observe que uma consulta **PySpark** foi adicionada à célula do notebook. Essa consulta recupera as primeiras **1.000 linhas** do atalho **Shortcut_Exercise.dimension_customer**. Essa experiência de notebook é semelhante à dos notebooks Jupyter no Visual Studio Code, e você também pode abrir o notebook no VS Code.

![](./media/image105.png)

16. Na faixa de opções **Home**, selecione o botão **Run all**.

![](./media/image106.png)

![](./media/image107.png)

## Exercício 8: Criar consultas entre warehouses com o editor de consultas SQL

Neste exercício, você aprende a criar e executar consultas T-SQL em vários warehouses com o editor de consultas SQL, incluindo a combinação de dados de um SQL analytics endpoint e de um warehouse no Microsoft Fabric.

### Tarefa 1: Adicionar vários warehouses ao Explorer

1.  Na página do notebook, selecione o warehouse **WideWorldImporters** no menu de navegação à esquerda.

![](./media/image108.png)

2.  No painel **Explorer**, selecione **+ Warehouses**.

![](./media/image109.png)

3.  Na janela **OneLake catalog**, selecione o SQL analytics endpoint **Shortcut_Exercise** e selecione **Confirm**.

![](./media/image110.png)

4.  No painel **Explorer**, observe que o SQL analytics endpoint **Shortcut_Exercise** agora está disponível.

![](./media/image111.png)

### Tarefa 2: Executar uma consulta entre warehouses

Nesta tarefa, você executa uma consulta que combina o warehouse WideWorldImporters com o SQL analytics endpoint Shortcut_Exercise.

**Observação:** Consultas entre bancos de dados usam a nomenclatura de três partes *database.schema.table* para referenciar objetos.

1.  Na aba **Home** da faixa de opções, selecione **New SQL query**.

![](./media/image112.png)

2.  Cole o código a seguir no editor de consultas. Esse código recupera o total das quantidades vendidas por item de estoque, descrição e cliente.

```sql
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

3.  **Execute** a consulta e revise os resultados.

![](./media/image113.png)

![](./media/image114.png)

4.  Renomeie a consulta para referência futura. No **Explorer**, clique com o botão direito do mouse em **SQL query** e selecione **Rename**.

![](./media/image115.png)

![](./media/image116.png)

5.  Na caixa de diálogo **Rename**, no campo **Name**, insira +++Cross-warehouse query+++ e clique no botão **Rename**.

![](./media/image117.png)

## Exercício 9: Criar um modelo semântico Direct Lake e um relatório do Power BI

Neste exercício, você cria um modelo semântico Direct Lake baseado no warehouse WideWorldImporters e cria um relatório do Power BI.

### Tarefa 1: Criar um modelo semântico

1.  Na aba **Home** da página **WideWorldImporters**, selecione **New semantic model**.

![](./media/image118.png)

2.  Na janela **New semantic model**, insira +++Sales Model+++ na caixa **Direct Lake semantic model name**.

3.  Expanda o esquema **dbo**, expanda a pasta **Tables** e marque as tabelas **dimension_city** e **fact_sale**. Selecione **Confirm**.

![](./media/image119.png)

4.  Na navegação à esquerda, selecione o workspace **Warehouse_Fabric@lab.LabInstance.Id**.

![](./media/image120.png)

5.  Para abrir o modelo semântico, selecione o modelo semântico **Sales Model** na página inicial do workspace.

![](./media/image121.png)

![](./media/image122.png)

6.  Na página **Sales Model**, altere o modo de **Viewing** para **Editing** para gerenciar as relações.

![](./media/image123.png)

7.  Para criar uma relação, selecione **Manage relationships** na faixa de opções **Home** do designer de modelos.

![](./media/image124.png)

8.  Na janela **Manage relationships**, selecione **+ New relationship**.

![](./media/image125.png)

9.  Na janela **New relationship**, conclua as seguintes etapas para criar a relação:

- Na lista suspensa **From table**, selecione a tabela **dimension_city**.

- Na lista suspensa **To table**, selecione a tabela **fact_sale**.

- Na lista suspensa **Cardinality**, selecione **One to many (1:\*)**.

- Na lista suspensa **Cross-filter direction**, selecione **Single**.

- Marque a caixa de seleção **Assume referential integrity**.

- Selecione **Save**.

![](./media/image126.png)

![](./media/image127.png)

10. Na janela **Manage relationships**, selecione **Close**.

![](./media/image128.png)

![](./media/image129.png)

### Tarefa 2: Criar um relatório do Power BI

Nesta tarefa, você cria um relatório do Power BI com base no modelo semântico criado na tarefa anterior.

1.  Na faixa de opções **File**, selecione **Create new report**.

![](./media/image130.png)

2.  No designer de relatórios, conclua as seguintes etapas para criar um visual de gráfico de colunas:

- No painel **Data**, expanda a tabela **fact_sale** e marque o campo **Profit**.

- No painel **Data**, expanda a tabela **dimension_city** e marque o campo **SalesTerritory**.

![](./media/image131.png)

3.  Clique em uma área em branco da tela e selecione o visual **Azure Map** no painel **Visualizations**.

![](./media/image132.png)

4.  No painel **Data**, arraste o campo **StateProvince** da tabela **dimension_city** para a área **Location** do painel **Visualizations**.

![](./media/image133.png)

5.  No painel **Data**, marque o campo **Profit** da tabela **fact_sale** para adicioná-lo à área **Size** do visual de mapa.

6.  Clique em uma área em branco da tela e selecione o visual **Table** no painel **Visualizations**.

![](./media/image134.png)

7.  No painel **Data**, marque os seguintes campos:

- **SalesTerritory** da tabela **dimension_city**

- **StateProvince** da tabela **dimension_city**

- **Profit** da tabela **fact_sale**

- **TotalExcludingTax** da tabela **fact_sale**

![](./media/image135.png)

![](./media/image136.png)

8.  Verifique se o design concluído da página do relatório é semelhante à imagem a seguir.

![](./media/image137.png)

9.  Para salvar o relatório, selecione **File** \> **Save** na faixa de opções **Home**.

![](./media/image138.png)

10. Na janela **Save your report**, insira +++Sales Analysis+++ na caixa **Enter a name for your report** e selecione **Save**.

![](./media/image139.png)

![](./media/image140.png)

![](./media/image141.png)

## Exercício 10: Limpar recursos

Você pode excluir relatórios, pipelines, warehouses e outros itens individualmente ou remover o workspace inteiro. Siga as etapas a seguir para excluir o workspace criado para este laboratório e todos os seus itens.

1.  Selecione **Warehouse_Fabric@lab.LabInstance.Id** no menu de navegação para voltar à lista de itens do workspace.

![](./media/image142.png)

2.  No menu do cabeçalho do workspace, selecione **Workspace settings**.

![](./media/image143.png)

3.  Na caixa de diálogo **Workspace settings**, selecione **General** e, em seguida, **Remove this workspace**.

![](./media/image144.png)

4.  Na caixa de diálogo **Delete workspace?**, clique no botão **Delete**.

![](./media/image145.png)

![](./media/image146.png)

**Resumo**

Neste laboratório, você criou um ambiente completo de data warehouse para a Contoso no Microsoft Fabric. Você começou criando um workspace e o warehouse WideWorldImporters e carregou os dados de exemplo da Wide World Importers com um copy job. Em seguida, usou T-SQL para clonar tabelas no mesmo esquema e entre esquemas (dbo1), incluindo clones de um ponto no tempo; criou e executou um procedimento armazenado para gerar a tabela agregada aggregate_sale_by_date_city; e usou consultas de viagem no tempo para comparar os resultados antes e depois de uma atualização dos dados. Depois, usou o construtor de consultas visuais para combinar e resumir dados sem escrever código, analisou dados com notebooks T-SQL e do Spark e executou uma consulta entre warehouses por meio de um atalho de lakehouse. Por fim, criou um modelo semântico Direct Lake, criou um relatório de análise de vendas do Power BI com gráfico de colunas, mapa e tabela e limpou os recursos do workspace. Essas tarefas oferecem uma compreensão abrangente de como configurar, gerenciar e analisar dados no Microsoft Fabric.
