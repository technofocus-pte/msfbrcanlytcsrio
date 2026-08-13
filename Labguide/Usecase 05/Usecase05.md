# Usecase 05-Building a Sales and Geography Data Warehouse for Contoso in Microsoft Fabric

**Introduction**

Contoso, a multinational retail company, is looking to modernize its
data infrastructure to improve sales and geographical analysis.
Currently, their sales and customer data is scattered across multiple
systems, making it difficult for their business analysts and citizen
developers to derive insights. The company plans to consolidate this
data into a unified platform using Microsoft Fabric to enable
cross-querying, sales analysis, and geographical reporting.

In this lab, you’ll assume the role of a data engineer at Contoso tasked
with designing and implementing a data warehouse solution using
Microsoft Fabric. You will start by setting up a Fabric workspace,
creating a data warehouse, loading data from Azure Blob Storage, and
performing analytical tasks to deliver insights to Contoso's
decision-makers.

While many concepts in Microsoft Fabric may be familiar to data and
analytics professionals, it can be challenging to apply those concepts
in a new environment. This lab has been designed to walk step-by-step
through an end-to-end scenario from data acquisition to data consumption
to build a basic understanding of the Microsoft Fabric user experience,
the various experiences and their integration points, and the Microsoft
Fabric professional and citizen developer experiences.

**Objectives**

- Set up a Fabric workspace with trial enabled.

- Establish a new Warehouse named WideWorldImporters in Microsoft
  Fabric.

- Load data into the Warehouse_FabricXX workspace using a Data Factory
  pipeline.

- Generate dimension_city and fact_sale tables within the data
  warehouse.

- Populate dimension_city and fact_sale tables with data from Azure Blob
  Storage.

- Create clones of dimension_city and fact_sale tables in the Warehouse.

- Clone dimension_city and fact_sale tables into the dbo1 schema.

- Develop a stored procedure to transform data and create
  aggregate_sale_by_date_city table.

- Generate a query using the visual query builder to merge and aggregate
  data.

- Use a notebook to query and analyze data from the dimension_customer
  table.

- Include WideWorldImporters and ShortcutExercise warehouses for
  cross-querying.

- Execute a T-SQL query across WideWorldImporters and ShortcutExercise
  warehouses.

- Enable Azure Maps visual integration in the Admin portal.

- Generate column chart, map, and table visuals for Sales Analysis
  report.

- Create a report using data from the WideWorldImporters dataset in the
  OneLake data hub.

- Remove the workspace and its associated items.

## Exercise 1: Create a Microsoft Fabric workspace

### Task 1: Create a workspace

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then
    press the **Enter** button.

\[!note\]**Note**: If you are directed to Microsoft Fabric Home page,
then skip to step \#5.

![](./media/image1.png)

2.  In the **Microsoft Fabric** window, enter your credentials, and
    click on the **Submit** button.

[TABLE]

> ![](./media/image2.png)

3.  Then, In the **Microsoft** window enter the password and click on
    the **Sign in** button.

> ![](./media/image3.png)

4.  In **Stay signed in?** window, click on the **Yes** button.

5.  If PowerBI opens by default , please folllow the below steps , other
    wise skip this step

- Click on PowerBI

![](./media/image4.png)

- Select Fabric from the option

![](./media/image5.png)

6.  Fabric home page, select **+New workspace** tile.

![](./media/image6.png)

2.  In the **Create a workspace tab, enter** the following details and
    click on the **Apply** button.

[TABLE]

![](./media/image7.png)

![](./media/image8.png)

![](./media/image9.png)

3.  Wait for the deployment to complete. It takes 1-2 minutes to
    complete. When your new workspace opens, it should be empty.

![](./media/image10.png)

### Task 2: Create a Warehouse in Microsoft Fabric

1.  In the **Fabric** page, select **+ New item** to create a lakehouse
    and select **Warehouse**

![A screenshot of a computer Description automatically
generated](./media/image11.png)

2.  On the **New warehouse** dialog,
    enter +++**WideWorldImporters+++** and click on
    the **Create** button.

![](./media/image12.png)

3.  When provisioning is complete,
    the **WideWorldImporters** warehouse landing page appears.

![](./media/image13.png)

## Exercise 2: Ingest data into a Warehouse in Microsoft Fabric

### Task 1: Ingest data into a Warehouse

1.  From the **WideWorldImporters** warehouse landing page,
    select **Warehouse_FabricXX** in the left-sided navigation menu to
    return to the workspace item list.

![](./media/image14.png)

2.  In the **Warehouse_FabricXX** page, select +**New item**. Then,
    click **Copy job** to view the full list of available items under
    Get data.

![](./media/image15.png)

3.  In the **New copy job** window, in the **Name** box, enter +++**Load
    Customer Data**+++. Select **Create**

> ![](./media/image16.png)

4.  Provisioning is complete when the **Copy job** page opens.

> ![](./media/image17.png)

5.  On the first page of the **Copy job** window, select **Sample
    data** from the menu bar on this page. For this tutorial, we use
    the **Retail Data Model from Wide World Importers** sample. Select
    this option to navigate to the next page

> ![](./media/image18.png)

6.  The data preview of the sample data loads. In the **Choose
    data** page, you can preview the selected dataset. After you review
    the data, select **Next**.

![](./media/image19.png)

5.  The Choose data destination page allows you to configure the type of
    item. In the OneLake catalog, select your **Wide World Importers**
    warehouse, and select **Next.**

> ![](./media/image20.png)

6.  The **Choose copy job mode** page select **Full copy** and
    select **Next**.

> ![](./media/image21.png)

7.  Enter the following destination tables, and then select **Next**.

- dbo.dimension_city

- dbo.dimension_customer

- dbo.dimension_date

- dbo.dimension_employee

- dbo.dimension_stock_item

- dbo.fact_sale

> ![](./media/image22.png)

8.  On the **Review + save** page, review
    the **Source** and **Destination**.

![](./media/image23.png)

9.  Use the **Results** tab to monitor the execution of the Copy job.

![](./media/image24.png)

10. When complete, the **Copy job** will deliver
    a **Succeeded** notification and status. You'll now see six new
    tables from the Wide World Importers dataset in your warehouse.

![](./media/image25.png)

11. On **Load Customer Data** page, click
    on **Warehouse_FabricXX** workspace in the left-sided navigation bar
    and select **WideWorldImporters** Warehouse.

> ![](./media/image26.png)

12. In the **WideWorldImporters** warehouse, expand **Schemas \> dbo \>
    Tables** and verify that the tables (**dimension_city**,
    **dimension_customer**, **dimension_date**, **dimension_employee**,
    **dimension_stock_item**, and **fact_sale**) have been created
    successfully.

![](./media/image27.png)

## Exercise 3: **Clone a table with T-SQL in a Warehouse**

### Task 1: **Clone a table within the same schema**

1.  On the **WideWorldImporters** page, go to the **Home** tab, select **SQL** from the drop
    down, and click on **New SQL query**.

![](./media/image28.png)

3.  In the query editor, paste the following code. The code creates a
    clone of the dimension_city table and the fact_sale table.

> **--Create a clone of the dbo.dimension_city table.**
>
> **CREATE TABLE \[dbo\].\[dimension_city1\] AS CLONE OF
> \[dbo\].\[dimension_city\];**
>
> **--Create a clone of the dbo.fact_sale table.**
>
> **CREATE TABLE \[dbo\].\[fact_sale1\] AS CLONE OF
> \[dbo\].\[fact_sale\];**
>
> ![](./media/image29.png)

4.  To execute the query, on the query designer ribbon, select **Run**.

![](./media/image30.png)

![](./media/image31.png)

5.  In the query editor, paste the following code.
    The CURRENT_TIMESTAMP T-SQL function returns the current UTC
    timestamp as a **datetime**. Select **Run** to execute the query.

> SELECT CURRENT_TIMESTAMP;

![](./media/image32.png)

6.  To create a table clone as of a *past point in time*, in the query
    editor, paste the following code **to replace the existing
    statements**. The code creates a clone of the dimension_city table
    and the fact_sale table at a certain point in time. Run the query.

> **--Create a clone of the dbo.dimension_city table at a specific point
> in time.**
>
> **CREATE TABLE \[dbo\].\[dimension_city2\] AS CLONE OF
> \[dbo\].\[dimension_city\] AT '2025-01-01T10:00:00.000';**
>
> **--Create a clone of the dbo.fact_sale table at a specific point in
> time.**
>
> **CREATE TABLE \[dbo\].\[fact_sale2\] AS CLONE OF
> \[dbo\].\[fact_sale\] AT '2025-01-01T10:00:00.000';**

![](./media/image33.png)

![](./media/image34.png)

7.  Rename the query as +++**Clone Tables+++**.

> ![](./media/image35.png)
>
> ![](./media/image36.png)

### Task 2: Clone a table across schemas within the same warehouse

In this task, learn how to clone a table across schemas within the same
warehouse.

1.  To create a new query, on the **Home** ribbon, select **New SQL
    query**.

> ![](./media/image37.png)

2.  In the query editor, paste the following code. The code creates a
    schema and then creates clones of the **fact_sale** and
    **dimension_city** tables in the new schema. Run the query.

> **--Create a new schema within the warehouse named dbo1.**
>
> **CREATE SCHEMA dbo1;**
>
> **GO**
>
> **--Create a clone of dbo.fact_sale table in the dbo1 schema.**
>
> **CREATE TABLE \[dbo1\].\[fact_sale1\] AS CLONE OF
> \[dbo\].\[fact_sale\];**
>
> **--Create a clone of dbo.dimension_city table in the dbo1 schema.**
>
> **CREATE TABLE \[dbo1\].\[dimension_city1\] AS CLONE OF
> \[dbo\].\[dimension_city\];**
>
> ![](./media/image38.png)

3.  When execution completes, preview the data loaded into
    the **dimension_city1** table in the **dbo1** schema.

> ![](./media/image39.png)

4.  To create table clones as of a *previous point in time*, in the
    query editor, paste the following code **to replace the existing
    statements**. The code creates a clone of
    the **dimension_city** table and the **fact_sale** table at certain
    points in time in the new schema. Run the query.

> --Create a clone of the dbo.dimension_city table in the dbo1 schema.
>
> CREATE TABLE \[dbo1\].\[dimension_city2\] AS CLONE OF
> \[dbo\].\[dimension_city\] AT '2025-01-01T10:00:00.000';
>
> --Create a clone of the dbo.fact_sale table in the dbo1 schema.
>
> CREATE TABLE \[dbo1\].\[fact_sale2\] AS CLONE OF \[dbo\].\[fact_sale\]
> AT '2025-01-01T10:00:00.000';
>
> ![](./media/image40.png)

5.  When execution completes, preview the data loaded into
    the **fact_sale2** table in the **dbo1** schema.

> ![](./media/image41.png)

6.  Rename the query as +++**Clone Tables Across Schemas**+++.

> ![](./media/image42.png)
>
> ![](./media/image43.png)

## Exercise 4: Transform data using a stored procedure

### Task 1: Create a stored procedure

In this task, learn how to create a stored procedure to transform data
in a warehouse table.

1.  On the **WideWorldImporters** page, go to the **Home** tab, select **SQL** from the dropdown, and click on **New SQL query**.

![](./media/image44.png)

2.  In the query editor, paste the following code. The code drops the
    stored procedure (if it exists), and it then creates a stored
    procedure named **populate_aggregate_sale_by_city**. The stored
    procedure logic creates a table
    named **aggregate_sale_by_date_city **and inserts data into it with
    a group-by query that joins
    the **fact_sale** and **dimension_city** tables.

> --Drop the stored procedure if it already exists.
>
> DROP PROCEDURE IF EXISTS \[dbo\].\[populate_aggregate_sale_by_city\];
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
> --Drop the aggregate table if it already exists.
>
> DROP TABLE IF EXISTS \[dbo\].\[aggregate_sale_by_date_city\];
>
> --Create the aggregate table.
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
> --Load aggregated data into the table.
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
> END;
>
> ![](./media/image45.png)

3.  To execute the query, on the query designer ribbon, select **Run**

> ![](./media/image46.png)

4.  When execution completes, rename the query as +++**Create Aggregate
    Procedure**+++.

> ![A screenshot of a computer Description automatically
> generated](./media/image47.png)
>
> ![](./media/image48.png)

5.  In the **Explorer** pane, from inside the **Stored
    Procedures** folder for the **dbo** schema, verify that
    the **aggregate_sale_by_date_city** stored procedure exists.

![](./media/image49.png)

### Task 2: Run the stored procedure

In this task, learn how to execute the stored procedure to transform
data in a warehouse table.

1.  On the **WideWorldImporters** page, go to the **Home** tab, select **SQL** from the dropdown, and click on **New SQL query**.

> ![](./media/image50.png)

2.  In the query editor, paste the following code. The code executes
    the **populate_aggregate_sale_by_city** stored procedure. Run the
    query.

--Execute the stored procedure to create and load aggregated data.

EXEC \[dbo\].\[populate_aggregate_sale_by_city\];

![](./media/image51.png)

3.  When execution completes, rename the query as +++**Run Aggregate
    Procedure+++**.

> ![](./media/image52.png)
>
> ![](./media/image53.png)

4.  To preview the aggregated data, in the **Explorer** pane, select the
    **aggregate_sale_by_date_city** table.

> ![](./media/image54.png)

** Note:** If the table doesn't appear, select the ellipsis (...) for
the **Tables** folder, and then select **Refresh**.

##  Exercise 5: Time travel using T-SQL at statement level

### Task 1: Work with time travel queries

In this task, learn how to create a view of the top 10 customers by
sales. You will use the view in the next task to run time-travel
queries.

1.  On the **WideWorldImporters** page, go to the **Home** tab, select **SQL** from the dropdown, and click on **New SQL query**.

![](./media/image55.png)

2.  In the query editor, paste the following code. The code creates a
    view named Top10Customers. The view uses a query to retrieve the top
    10 customers based on sales. Select **Run** to execute the query.

> --Create the Top10Customers view.
>
> CREATE VIEW \[dbo\].\[Top10Customers\]
>
> AS
>
> SELECT TOP(10)
>
> FS.\[CustomerKey\],
>
> DC.\[Customer\],
>
> SUM(FS.\[TotalIncludingTax\]) AS \[TotalSalesAmount\]
>
> FROM
>
> \[dbo\].\[dimension_customer\] AS DC
>
> INNER JOIN \[dbo\].\[fact_sale\] AS FS
>
> ON DC.\[CustomerKey\] = FS.\[CustomerKey\]
>
> GROUP BY
>
> FS.\[CustomerKey\],
>
> DC.\[Customer\]
>
> ORDER BY
>
> \[TotalSalesAmount\] DESC;
>
> ![](./media/image56.png)

3.  When execution completes, rename the query as +++**Create Top 10
    Customer View**+++.

![](./media/image57.png)

![](./media/image58.png)

3.  In the **Explorer**, verify that you can see the newly created
    view **Top10CustomersView** by expanding the **View** node
    under **dbo** schema.

![](./media/image59.png)

4.  Create another new query, similar to Step 1. From the **Home** tab
    of the ribbon, select **New SQL query**.

> ![](./media/image60.png)

5.  In the query editor, paste the following code. The code updates
    the **TotalIncludingTax** value for a single fact row to
    deliberately inflate its total sales. It also retrieves the current
    timestamp.

> --Update the TotalIncludingTax for a single fact row to deliberately
> inflate its total sales.
>
> UPDATE \[dbo\].\[fact_sale\]
>
> SET \[TotalIncludingTax\] = 200000000
>
> WHERE \[SaleKey\] = 22632918; --For customer 'Tailspin Toys (Muir,
> MI)'
>
> GO
>
> --Retrieve the current (UTC) timestamp.
>
> SELECT CURRENT_TIMESTAMP;

![](./media/image61.png)

6.  Copy the timestamp value returned to your clipboard.

![](./media/image62.png)

**Note:** Currently, you can only use the Coordinated Universal Time
(UTC) time zone for time travel.

7.  When execution completes, rename the query as +++**Time Travel+++**.

![](./media/image63.png)

![](./media/image64.png)

8.  Paste the following code in the query editor and replace the
    timestamp value with the current timestamp value obtained from the
    prior step. The timestamp syntax format
    is **YYYY-MM-DDTHH:MM:SS\[.FFF\].**

9.  Remove the trailing zeroes, for
    example: **2026-07-27T06:20:55.823**.

&nbsp;

10. To retrieve the top 10 customers *as of now*, in a new query editor,
    paste the following statement. The code retrieves the top 10
    customers by using the FOR TIMESTAMP AS OF query hint.

11. Replace YOUR_TIMESTAMP with the timestamp you copied to the
    clipboard.

> --Retrieve the top 10 customers as of now.
>
> SELECT \*
>
> FROM \[dbo\].\[Top10Customers\]
>
> OPTION (FOR TIMESTAMP AS OF 'YOUR_TIMESTAMP');

![](./media/image65.png)

12. Rename the query as +++**Time Travel Now+++**

> ![](./media/image66.png)
>
> ![](./media/image67.png)

13. Notice that the second top **CustomerKey value** is **49**
    for Tailspin Toys (Muir, MI).

> ![](./media/image68.png)

14. Modify the timestamp value to an earlier time *by **subtracting one
    minute*** from the timestamp

15. Run the query again, and notice that the second
    **top CustomerKey value is 381** for **Wingtip Toys (Sarversville,
    PA).**

## Exercise 6: Create a query with the visual query builder in a Warehouse

### Task 1: Use the visual query builder

In this task, learn how to create a query with the visual query builder.

1.  On the **Home** ribbon, open the **New SQL query** dropdown list,
    and then select **New visual query**.

![](./media/image69.png)

2.  From the **Explorer** pane, from the dbo schema **Tables** folder,
    drag the **fact_sale** table to the visual query canvas.

![](./media/image70.png)

3.  Navigate to query design pane **transformations ribbon** and limit
    the dataset size by clicking on **Reduce rows** dropdown, then click
    on **Keep top rows** as shown in the below image.

![](./media/image71.png)

4.  In the **Keep top rows** dialog box, enter +++**10000+++** and
    Select **OK**.

![](./media/image72.png)

![](./media/image73.png)

5.  From the **Explorer** pane, from the dbo schema **Tables** folder,
    drag the **dimension_city** table to the visual query canvas.

6.  Right-click on **dimension_city** and select **Insert into canvas**

> ![](./media/image74.png)

![](./media/image75.png)

6.  From the transformations ribbon, select the dropdown
    beside **Combine** and select **Merge queries as new** as shown in
    the below image.

![](./media/image76.png)

7.  On the **Merge** settings page enter the following details.

- In the **Left table for merge** dropdown, choose **dimension_city**

-  In the **Right table for merge** dropdown, choose **fact_sale** (use
  horizontal and vertical scroll bar)

-  Select the **CityKey** field in the **dimension_city** table by
  selecting on the column name in the header row to indicate the join
  column.

-  Select the **CityKey** field in the **fact_sale** table by selecting
  on the column name in the header row to indicate the join column.

-  In the **Join kind** diagram selection, choose **Inner** and click on
  the **Ok** button.

![](./media/image77.png)

![](./media/image78.png)

8.  With the **Merge** step selected, select the **Expand** button
    beside **fact_sale** on the header of the data grid as shown in the
    below image, then select the columns **TaxAmount, Profit,
    TotalIncludingTax** and select **Ok.**

![](./media/image79.png)

![](./media/image80.png)

![](./media/image81.png)

9.  In the **transformations ribbon,** click on the dropdown
    beside **Transform**, then select **Group by**.

![](./media/image82.png)

10. On the **Group by** settings page, enter the following details.

- Select **Advanced** radio button.

- Under **Group by** select the following:

  1.  **Country**

  2.  **StateProvince**

  3.  **City**

- In the **New column
  name,** enter **SumOfTaxAmount** in **Operation** column field,
  select **Sum**, then under **Column** field,
  select **TaxAmount.** Click on **Add aggregation** to add more
  aggregate column and operation.

- In the **New column
  name,** enter **SumOfProfit** in **Operation** column field,
  select **Sum**, then under **Column** field, select **Profit**. Click
  on **Add aggregation** to add more aggregate column and operation.

- In the **New column name**,
  enter **SumOfTotalIncludingTax** in **Operation** column field,
  select **Sum**, then under **Column** field, **TotalIncludingTax.** 

- Click on the **OK** button

![](./media/image83.png)

![](./media/image84.png)

11. In the explorer, navigate to **Queries** and right-click on **Visual
    query 1** under **Queries**. Then, select **Rename**.

![](./media/image85.png)

12. Type +++**Sales Summary+++** to change the name of the query.
    Press **Enter** on the keyboard or select anywhere outside the tab
    to save the change.

![](./media/image86.png)

13. Click on the **Refresh** icon below the **Home** tab.

![A screenshot of a computer Description automatically
generated](./media/image87.png)

## Exercise 7: Analyze data with a notebook

### Task 1: Create a T-SQL notebook

In this task, learn how to create a T-SQL notebook.

1.  On the **Home** ribbon, open the **New SQL query** dropdown list,
    and then select **New SQL query in notebook**

> ![](./media/image88.png)

2.  In the **Explorer** pane, select **Warehouses** to reveal the
    objects of the **Wide World Importers** warehouse.

3.  To generate a SQL template to explore data, to the right of
    the **dimension_city** table, select **the ellipsis (...),** and
    then select **SELECT TOP 100**.

> ![](./media/image89.png)

4.  To run the T-SQL code in this cell, select the **Run cell** button
    for the code cell.

> ![](./media/image90.png)

5.  Review the query result in the results pane.

> ![](./media/image91.png)

### Task 2: Create a lakehouse shortcut and analyze data with a notebook

In this task, learn how to create a lakehouse shortcut and analyze data
with a notebook.

1.  From the left menu, select 
    **Warehouse_Fabric65897@lab.labinstance.id** workspace icon and then
    select workspace name.

> ![](./media/image92.png)

2.  Select **+ New Item** to display the full list of available item
    types.

3.  From the list, in the **Store data** section, select
    the **Lakehouse** item type.

> ![](./media/image93.png)

4.  When provisioning is complete, the lakehouse
    enter +++**Shortcut_Exercise**+++ as the lakehouse name and unselect
    the lakehouses schemas. Select **Create**. ![](./media/image94.png)

> ![](./media/image95.png)

5.  When the new lakehouse opens, in the landing page, select the **New
    shortcut** option.

> ![](./media/image96.png)

6.  In the **New shortcut** window, select the **Microsoft
    OneLake** option.

> ![](./media/image97.png)

7.  In the **Select a data source type** window, select the **Wide World
    Importers **warehouse, and then select **Next**.

> ![](./media/image98.png)

8.  Click on Connect

> ![](./media/image99.png)

9.  In the **OneLake object** browser, expand **Tables**, expand
    the **dbo **schema, and then select the checkbox for
    the **dimension_customer** table. Select **Next**.

> ![](./media/image100.png)

10. Select **Create**.

> ![](./media/image101.png)

11. In the **Explorer** pane, select the **dimension_customer** table to
    preview the data, and then review the data retrieved from
    the dimension_customer table in the warehouse.

> ![](./media/image102.png)

12. On the **dimension_customer** table page, click **Analyze data
    with**, select **Notebook**, and then choose **New notebook** to
    create a new Spark notebook for data analysis

> ![](./media/image103.png)

13. In the **Explorer** pane, select **Lakehouses**.

14. Drag the **dimension_customer** table to the open notebook cell.

> ![](./media/image104.png)

15. Notice the **PySpark** query that was added to the notebook cell.
    This query retrieves the first **1,000 rows** from
    the **Shortcut_Exercise.dimension_customer** shortcut. This notebook
    experience is similar to Visual Studio Code Jupyter notebook
    experience. You can also open the notebook in VS Code.

> ![](./media/image105.png)

16. On the **Home** ribbon, select the **Run all** button.

> ![](./media/image106.png)
>
> ![](./media/image107.png)

## Exercise 8: Create cross-warehouse queries with the SQL query editor

### Task 1: Add multiple warehouses to the Explorer

In this task, learn about how you can easily create and execute T-SQL
queries with the SQL query editor across multiple warehouse, including
joining together data from a SQL Endpoint and a Warehouse in Microsoft
Fabric.

1.  From **Notebook2** page, navigate and click
    on **WideWorldImporters** Workspace on the left-sided navigation
    menu.

> ![](./media/image108.png)

2.  In the **Explorer** pane, select **+ Warehouses**.

![](./media/image109.png)

3.  In the **OneLake catalog** window, select
    the **Shortcut_Exercise** SQL analytics endpoint.
    Select **Confirm**.

![](./media/image110.png)

4.  In the **Explorer** pane, notice that the **Shortcut_Exercise** SQL
    analytics endpoint is available.

![](./media/image111.png)

### Task 2: Run the cross-warehouse query

In this task, learn how to run the cross-warehouse query. Specifically,
you will run a query that joins the Wide World Importers warehouse to
the Shortcut_Exercise SQL analytics endpoint.

** Note:** A cross-database query uses three-part naming
of *database.schema.table* to reference objects.

1.  From the **Home** tab of the ribbon, select **New SQL query**.

![](./media/image112.png)

2.  In the query editor, paste the following code. The code retrieves an
    aggregate of quantity sold by stock item, description, and customer.

--Retrieve an aggregate of quantity sold by stock item, description, and
customer.

SELECT

Sales.StockItemKey,

Sales.Description,

c.Customer,

SUM(CAST(Sales.Quantity AS int)) AS SoldQuantity

FROM

\[dbo\].\[fact_sale\] AS Sales

INNER JOIN \[Shortcut_Exercise\].\[dbo\].\[dimension_customer\] AS c

ON Sales.CustomerKey = c.CustomerKey

GROUP BY

Sales.StockItemKey,

Sales.Description,

c.Customer;

3.  **Run** the query, and review the query result.

![](./media/image113.png)

![](./media/image114.png)

3.  Rename the query for reference. Right-click on **SQL query** in
    the **Explorer** and select **Rename**.

> ![](./media/image115.png)

![](./media/image116.png)

4.  In the **Rename** dialog box, under the **Name** field, enter
    +++**Cross-warehouse query+++**, then click on
    the **Rename** button. 

> ![](./media/image117.png)

## Exercise 9: Create a Direct Lake semantic model and Power BI report

### Task 1: Create a semantic model

In this task, learn how to create a Direct Lake semantic model based the
Wide World Importers warehouse.

1.  In the **WideWorldImportes** page, under the **Home** tab, select
    the **New semantic model**.

![](./media/image118.png)

2.  In the **New semantic model** window, in the **Direct Lake semantic
    model name** box, enter +++**Sales Model+++**

3.  Expand the dbo schema, expand the **Tables** folder, and then check
    the **dimension_city** and **fact_sale** tables. Select **Confirm**.

> ![](./media/image119.png)

9.  From the left navigation select ***Warehouse_FabricXXXXX***, as
    shown in the image below

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image120.png)

10. To open the semantic model, return to the workspace landing page,
    and then select the **Sales Model** semantic model.

![](./media/image121.png)

![](./media/image122.png)

12. On the **Sales Model** page, to edit **Manage Relationships**,
    change the mode from **Viewing** to **Editing**![A screenshot of a
    computer AI-generated content may be
    incorrect.](./media/image123.png)

13. To create a relationship, in the model designer, on
    the **Home** ribbon, select **Manage relationships**.

![](./media/image124.png)

14. In the **Manage relationship** window, select **+ New
    relationship**.

![](./media/image125.png)

14. In the **New relationship window**, complete the following steps to
    create the relationship:

-  In the **From table** dropdown list, select
  the **dimension_city** table.

- In the **To table** dropdown list, select the **fact_sale** table.

- In the **Cardinality** dropdown list, select **One to many (1:\*)**.

- In the **Cross-filter direction** dropdown list, select **Single**.

- Check the **Assume referential integrity** box.

- Select **Save**.

![](./media/image126.png)

![](./media/image127.png)

15. In the **Manage relationship** window, select **Close**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image128.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image129.png)

### Task2: Create a Power BI report

In this task, learn how to create a Power BI report based on the
semantic model you created in the  task.

1.  On the **File** ribbon, select **Create new report**.

![](./media/image130.png)

2.  In the report designer, complete the following steps to create a
    column chart visual:

-  In the **Data** pane, expand the **fact_sale** table, and then check
  the Profit field.

- In the **Data** pane, expand the dimension_city table, and then check
  the SalesTerritory field.

![](./media/image131.png)

3.  In the **Visualizations** pane, select the **Azure Map** visual.

![](./media/image132.png)

4.  In the **Data** pane, from inside the dimension_city table, drag
    the StateProvince fields to the **Location** well in
    the **Visualizations** pane.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image133.png)

5.  In the **Data** pane, from inside the fact_sale table, check
    the Profit field to add it to the map visual **Size** well.

6.  In the **Visualizations** pane, select the **Table** visual.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image134.png)

7.  In the **Data** pane, check the following fields:

-  SalesTerritory from the dimension_city table

- StateProvince from the dimension_city table

- Profit from the fact_sale table

- TotalExcludingTax from the fact_sale table

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image135.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image136.png)

8.  Verify that the completed design of the report page resembles the
    following image.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image137.png)

9.  To save the report, on the **Home** ribbon,
    select **File** \> **Save**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image138.png)

10. In the Save your report window, in the Enter a name for your report
    box, enter +++**Sales Analysis**+++ and Select **Save**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image139.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image140.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image141.png)

### Task 3: Clean up resources

You can delete individual reports, pipelines, warehouses, and other
items or remove the entire workspace. In this tutorial, you will clean
up the workspace, individual reports, pipelines, warehouses, and other
items you created as part of the lab.

1.  Select **Warehouse_FabricXX** in the navigation menu to return to
    the workspace item list.

![](./media/image142.png)

2.  In the menu of the workspace header, select **Workspace settings**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image143.png)

3.  In the **Workspace settings** dialog box, select **General** and
    select the **Remove this workspace**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image144.png)

4.  In the **Delete workspace?** dialog box, click on
    the **Delete** button. ![](./media/image145.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image146.png)

**Summary**

This comprehensive lab walks through a series of tasks aimed at
establishing a functional data environment in Microsoft Fabric. It
starts with the creation of a workspace, essential for data operations,
and ensures the trial is enabled. Subsequently, a Warehouse named
WideWorldImporters is established within the Fabric environment to serve
as the central repository for data storage and processing. Data
ingestion into the Warehouse_FabricXX workspace is then detailed through
the implementation of a Data Factory pipeline. This process involves
fetching data from external sources and integrating it seamlessly into
the workspace. Critical tables, dimension_city, and fact_sale, are
created within the data warehouse to serve as foundational structures
for data analysis. The data loading process continues with the use of
T-SQL, where data from Azure Blob Storage is transferred into the
specified tables. The subsequent tasks delve into the realm of data
management and manipulation. Cloning tables is demonstrated, offering a
valuable technique for data replication and testing purposes.
Additionally, the cloning process is extended to a different schema
(dbo1) within the same warehouse, showcasing a structured approach to
data organization. The lab progresses to data transformation,
introducing the creation of a stored procedure to efficiently aggregate
sales data. It then transitions to visual query building, providing an
intuitive interface for complex data queries. This is followed by an
exploration of notebooks, demonstrating their utility in querying and
analyzing data from the dimension_customer table. Multi-warehouse
querying capabilities are then unveiled, allowing for seamless data
retrieval across various warehouses within the workspace. The lab
culminates in enabling Azure Maps visuals integration, enhancing
geographical data representation in Power BI. Subsequently, a range of
Power BI reports, including column charts, maps, and tables, are created
to facilitate in-depth sales data analysis. The final task focuses on
generating a report from the OneLake data hub, further emphasizing the
versatility of data sources in Fabric. Finally, the lab provides
insights into resource management, emphasizing the importance of cleanup
procedures to maintain an efficient workspace. Collectively, these tasks
present a comprehensive understanding of setting up, managing, and
analyzing data within Microsoft Fabric.
