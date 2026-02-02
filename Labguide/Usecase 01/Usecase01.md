# Usecase 1: Creating a Lakehouse, ingesting sample data and building a report

**Introduction**

This lab walks you through an end-to-end scenario from data acquisition
to data consumption. It helps you build a basic understanding of Fabric,
including the different experiences and how they integrate, as well as
the professional and citizen developer experiences that come with
working on this platform. This lab isn't intended to be a reference
architecture, an exhaustive list of features and functionality, or a
recommendation of specific best practices.

Traditionally, organizations have been building modern data warehouses
for their transactional and structured data analytics needs. And data
lakehouses for big data (semi/unstructured) data analytics needs. These
two systems ran in parallel, creating silos, data duplicity, and
increased total cost of ownership.

Fabric with its unification of data store and standardization on Delta
Lake format allows you to eliminate silos, remove data duplicity, and
drastically reduce total cost of ownership.

With the flexibility offered by Fabric, you can implement either
lakehouse or data warehouse architectures or combine them together to
get the best of both with simple implementation. In this tutorial,
you're going to take an example of a retail organization and build its
lakehouse from start to finish. It uses the [medallion
architecture](https://learn.microsoft.com/en-us/azure/databricks/lakehouse/medallion) where
the bronze layer has the raw data, the silver layer has the validated
and deduplicated data, and the gold layer has highly refined data. You
can take the same approach to implement a lakehouse for any organization
from any industry.

This lab explains how a developer at the fictional Wide World Importers
company from the retail domain completes the following steps.

**Objectives**:

1\. Sign in to Power BI account and initiate a free Microsoft Fabric
trial.

2\. Start the Microsoft Fabric (Preview) trial within Power BI.

3\. Configure OneDrive sign-up for the Microsoft 365 admin center.

4\. Build and implement an end-to-end lakehouse for the organization,
including creating a Fabric workspace and a lakehouse.

5\. Ingest sample data into the lakehouse and prepare it for further
processing.

6\. Transform and prepare the data using Python/PySpark and SQL
notebooks.

7\. Create business aggregate tables using different approaches.

8\. Establish relationships between tables for seamless reporting.

9\. Build a Power BI report with visualizations based on the prepared
data.

10\. Save and store the created report for future reference and
analysis.

## Exercise 1: Setup Lakehouse end-to-end scenario

### Task 1: Sign in to Power BI account and sign up for the free Microsoft Fabric trial

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL:+++https://app.fabric.microsoft.com/+++ then press
    the **Enter** button.

> ![](./media/image1.png)

2.  In the **Microsoft Fabric** window, enter your credentials, and
    click on the **Submit** button.
    |   |   |
    |---|---|
    | Username | +++@lab.CloudPortalCredential(User1).Username+++ |
    | Password | +++@lab.CloudPortalCredential(User1).Password+++ |

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image2.png)

3.  Then, In the **Microsoft** window enter the password and click on
    the **Sign in** button.

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image3.png)

4.  In **Stay signed in?** window, click on the **Yes** button.

> ![](./media/image4.png)

5.  You’ll be directed to Power BI Home page.

> ![](./media/image5.png)

## Exercise 2: Build and implement an end-to-end lakehouse for your organization

### Task 1: Create a Fabric workspace

In this task, you create a Fabric workspace. The workspace contains all
the items needed for this lakehouse tutorial, which includes lakehouse,
dataflows, Data Factory pipelines, the notebooks, Power BI datasets, and
reports.

1.  Fabric home page, select **+New workspace** tile.

> ![A screenshot of a computer Description automatically
> generated](./media/image6.png)

2.  In the **Create a workspace** pane that appears on the right side,
    enter the following details, and click on the **Apply** button.

    | Property  | Value  |
    |-------|-----|
    |Name|	+++Fabric Lakehouse Tutorial-@lab.LabInstance.Id+++ (must be a unique Id)|
    |Advanced	|Under License mode, select Fabric capacity|
    |Default	storage format| Small dataset storage format|
    |Template apps	|Check the Develop template apps|

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image7.png)

Note: To find your lab instant ID, select 'Help' and copy the instant
ID.

> ![A screenshot of a computer Description automatically
> generated](./media/image8.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image9.png)

3.  Wait for the deployment to complete. It takes 2-3 minutes to
    complete.

> ![A screenshot of a computer Description automatically
> generated](./media/image10.png)

### Task 2: Create a lakehouse

1.  Create a new lakehouse by clicking on the **+New item** button in
    the navigation bar.

> ![A screenshot of a computer Description automatically
> generated](./media/image11.png)

2.  Click on the "**Lakehouse**" tile.

> ![A screenshot of a computer Description automatically
> generated](./media/image12.png)

3.  In the **New lakehouse** dialog box, enter +++**wwilakehouse+++** in
    the **Name** field, click on the **Create** button and open the new
    lakehouse.

> **Note**: Ensure to remove space before **wwilakehouse**.
>
> ![A screenshot of a computer Description automatically
> generated](./media/image13.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image14.png)

4.  You will see a notification stating **Successfully created SQL
    endpoint**.

> ![](./media/image15.png)

### Task 3: Ingest sample data

1.  In the **wwilakehouse** page, navigate to **Get data in your
    lakehouse** section, and click on **Upload files as shown in the
    below image.**

> ![A screenshot of a computer Description automatically
> generated](./media/image16.png)

2.  On the Upload files tab, click on the folder under the Files

> ![A screenshot of a computer Description automatically
> generated](./media/image17.png)

3.  Browse to **C:\LabFiles** on your VM, then
    select **dimension_customer.csv** file and click on **Open** button.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image18.png)

4.  Then, click on the **Upload** button and close

> ![A screenshot of a computer Description automatically
> generated](./media/image19.png)

5.  **Close** the Upload files pane.

> ![A screenshot of a computer Description automatically
> generated](./media/image20.png)

6.  Click and select refresh on the **Files**. The file appears.

> ![A screenshot of a computer Description automatically
> generated](./media/image21.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

7.  In the **Lakehouse** page, Under the Explorer pane select Files.
    Now, however your mouse to **dimension_customer.csv** file. Click on
    the horizontal ellipses **(…)** beside **dimension_customer**.csv.
    Navigate and click on **Load Table**, then select **New table**.

> ![](./media/image23.png)

8.  In the **Load file to new table** dialog box, click on
    the **Load** button.

> ![](./media/image24.png)

9.  Now **dimension_customer** table is successfully created.

> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

10. Select **dimension_customer** table under the **dbo** schema.

> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image27.png)

11. You can also use the SQL endpoint of the lakehouse to query the data
    with SQL statements. Select **SQL analytics endpoint** from
    the **Lakehouse** drop-down menu at the top right of the screen.

> ![A screenshot of a computer Description automatically
> generated](./media/image28.png)

12. In the **wwilakehouse** page, under Explorer select
    the **dimension_customer** table to preview its data and
    select **New SQL query** to write your SQL statements.

> ![A screenshot of a computer Description automatically
> generated](./media/image29.png)

13. The following sample query aggregates the row count based on
    the **BuyingGroup column** of the **dimension_customer** table. SQL
    query files are saved automatically for future reference, and you
    can rename or delete these files based on your need. Paste the code
    as shown in the below image, then click on the play icon
    to **Run** the script:

    ```
    SELECT BuyingGroup, Count(*) AS Total
    FROM dimension_customer
    GROUP BY BuyingGroup
    ```
> ![A screenshot of a computer Description automatically
> generated](./media/image30.png)

**Note**: If you encounter an error during the execution of the script,
then crosscheck the script syntax that it should not have any
unnecessary spaces.

14. Previously all the lakehouse tables and views were automatically
    added to the semantic model. With the recent updates, for new
    lakehouses, you have to manually add your tables to the semantic
    model.

> ![A screenshot of a computer Description automatically
> generated](./media/image31.png)

15. From the lakehouse **Home** tab, select **New semantic model** and
    select the tables that you want to add to the semantic model.

> ![A screenshot of a computer Description automatically
> generated](./media/image32.png)

16. In the **New semantic model** dialog enter +++wwilakehouse+++ and
    then select the **dimension_customer** table from the list of tables
    and select **Confirm** to create the new model.

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

### Task 4: Build a report

1.  Now, click on **Fabric Lakehouse** **Tutorial-XX** on the left-sided
    navigation pane.

> ![A screenshot of a computer Description automatically
> generated](./media/image34.png)

2.  In the **Fabric Lakehouse Tutorial-XX** view,
    select **wwilakehouse** of Type **Semantic model**.

> ![A screenshot of a computer Description automatically
> generated](./media/image35.png)

3.  From the semantic model pane, you can view all the tables. You have
    options to create reports either from scratch, paginated report, or
    let Power BI automatically create a report based on your data. For
    this tutorial, under **Explore this data**, select **Auto-create a
    report** as shown in the below image.

> ![A screenshot of a computer Description automatically
> generated](./media/image36.png)

4.  Now that the report is ready, click on **View report now** to open
    and review it.![A screenshot of a computer AI-generated content may
    be incorrect.](./media/image37.png)

> ![A screenshot of a computer Description automatically
> generated](./media/image38.png)

5.  Since the table is a dimension and there are no measures in it,
    Power BI creates a measure for the row count and aggregates it
    across different columns, and creates different charts as shown in
    the following image.

6.  Save this report for the future by selecting **Save** from the top
    ribbon.

> ![A screenshot of a computer Description automatically
> generated](./media/image39.png)

7.  In the **Save your report** dialog box, enter a name for your report
    as +++dimension_customer-report+++ and select **Save.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image40.png)

8.  You will see a notification stating **Report saved**.

> ![A screenshot of a computer Description automatically
> generated](./media/image41.png)

# Exercise 2: Ingest data into the lakehouse

In this exercise, you ingest additional dimensional and fact tables from
the Wide World Importers (WWI) into the lakehouse.

### Task 1: Ingest data

1.  Now, click on **Fabric Lakehouse** **Tutorial-XX** on the left-sided
    navigation pane.

> ![A screenshot of a computer Description automatically
> generated](./media/image42.png)

2.  Again, select the workspace name.

![A screenshot of a computer screen Description automatically
generated](./media/image43.png)

3.  In the **Fabric Lakehouse Tutorial-XX** workspace page, navigate and
    click on **+New item** button, then select **Pipeline**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image44.png)

4.  In the New pipeline dialog box, specify the name
    as **+++IngestDataFromSourceToLakehouse+++** and
    select **Create.** A new data factory pipeline is created and
    opened.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image45.png)

> ![A screenshot of a computer Description automatically
> generated](./media/image46.png)

5.  On newly created data factory pipeline
    i.e., **IngestDataFromSourceToLakehouse**, select **Copy data**
    dropdown and choose **Add to canvas** option.

> ![A screenshot of a computer Description automatically
> generated](./media/image47.png)

6.  With the **copy data** being selected, navigate to **Source** tab.

![](./media/image48.png)

7.  Select the **Connection** dropdown and select **Browse all** option.

![](./media/image49.png)

8.  Select **Sample data** from the left pane and choose **Retail Data
    Model from Wide World Importers**.

![](./media/image50.png)

9.  In the **Connect to data source** window, Select **Retail Data Model
    from Wide World Importers** data to preview it and select **OK**.

> ![](./media/image51.png)

10. The data source connection is selected as the sample data.

![A screenshot of a computer Description automatically
generated](./media/image52.png)

11. Now, navigate to **destination** tab.

![](./media/image53.png)

12. On the destination tab, click on the **connection** dropdown and
    choose **Browse** **all** option.

![](./media/image54.png)

13. On choose a destination window, select **OneLake catalog** from the
    left pane and select the **wwilakehouse**.

> ![](./media/image55.png)

14. The destination is now selected as Lakehouse. Specify the **Root
    Folder** as **Files** and make sure the file format is selected as
    **Parquet.**

![A screenshot of a computer Description automatically
generated](./media/image56.png)

8.  Click on **Run** to run the copy data.

> ![A screenshot of a computer Description automatically
> generated](./media/image57.png)

9.  Click on **Save and run** button so that pipeline will be save and
    run.

> ![A screenshot of a computer error Description automatically
> generated](./media/image58.png)

10. The data copy process takes approximately 1-3 minutes to complete.

> ![A screenshot of a computer Description automatically
> generated](./media/image59.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image60.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

11. Under the Output tab, select **Copy data1** to look at the details
    of the data transfer. After seeing the **Status** as **Succeeded**,
    click on the **Close** button.

> ![A screenshot of a computer Description automatically
> generated](./media/image62.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image63.png)

12. After the successful execution of the pipeline, go to your lakehouse
    (**wwilakehouse**) and open the explorer to see the imported data.

> ![A screenshot of a computer Description automatically
> generated](./media/image64.png)

13. Verify that the all the **WideWorldImporters folders** is present in
    the **Explorer** view and contains data for all tables.

> ![A screenshot of a computer Description automatically
> generated](./media/image65.png)

# Exercise 3: Prepare and transform data in the lakehouse

### Task 1: Transform data and load to silver Delta table

1.  In the **wwilakehouse** page, navigate and click on **Open
    notebook** drop in the command bar, then select **New notebook**.

> ![A screenshot of a computer Description automatically
> generated](./media/image66.png)

2.  In the open notebook in **Lakehouse explorer**, you will see that
    the notebook is already linked to your opened lakehouse.

> ![](./media/image67.png)

\[!note\] **Note**: Fabric provides
the [**V-order**](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order) capability
to write optimized delta lake files. V-order often improves compression
by three to four times and up to 10 times performance acceleration over
the Delta Lake files that aren't optimized. Spark in Fabric dynamically
optimizes partitions while generating files with a default 128 MB size.
The target file size may be changed per workload requirements using
configurations. With the [**optimize
write**](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order#what-is-optimized-write) capability,
the Apache Spark engine that reduces the number of files written and
aims to increase individual file size of the written data.

3.  Before you write data as delta lake tables in the **Tables** section
    of the lakehouse, you use two Fabric features
    (**V-order** and **Optimize Write**) for optimized data writing and
    for improved reading performance. To enable these features in your
    session, set these configurations in the first cell of your
    notebook.

4.  Update the code in the **cell** with the following code and click
    on **▷ Run cell** that appears to the left of the cell upon hover.

    ```
    # Copyright (c) Microsoft Corporation.
    # Licensed under the MIT License.
    spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
    spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
    spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize", "1073741824")
    ```

\[!note\]**Note**: When running a cell, you didn't have to specify the
underlying Spark

![A screenshot of a computer Description automatically
generated](./media/image68.png)

![A screenshot of a computer Description automatically
generated](./media/image69.png)

pool or cluster details because Fabric provides them through Live Pool.
Every Fabric workspace comes with a default Spark pool, called Live
Pool. This means when you create notebooks, you don't have to worry
about specifying any Spark configurations or cluster details. When you
execute the first notebook command, the live pool is up and running in a
few seconds. And the Spark session is established and it starts
executing the code. Subsequent code execution is almost instantaneous in
this notebook while the Spark session is active.

5.  Next, you read raw data from the **Files** section of the lakehouse
    and add more columns for different date parts as part of the
    transformation. You use partitionBy Spark API to partition the data
    before writing it as delta table based on the newly created data
    part columns (Year and Quarter).

6.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

**Note**: In case, you are unable to see the output, then click on the
horizontal lines on the left side of **Spark jobs**.

    ```
    from pyspark.sql.functions import col, year, month, quarter
    
    table_name = 'fact_sale'
    
    df = spark.read.format("parquet").load('Files/fact_sale_1y_full')
    df = df.withColumn('Year', year(col("InvoiceDateKey")))
    df = df.withColumn('Quarter', quarter(col("InvoiceDateKey")))
    df = df.withColumn('Month', month(col("InvoiceDateKey")))
    
    df.write.mode("overwrite").format("delta").partitionBy("Year","Quarter").save("Tables/" + table_name)
    ```

> ![A screenshot of a computer Description automatically
> generated](./media/image70.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image71.png)
>
> ![](./media/image72.png)

7.  After the tables load, you can move on to loading data for the rest
    of the dimensions. The following cell creates a function to read raw
    data from the **Files** section of the lakehouse for each of the
    table names passed as a parameter. Next, it creates a list of
    dimension tables. Finally, it loops through the list of tables and
    creates a delta table for each table name that's read from the input
    parameter.

8.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.

    ```
    from pyspark.sql.types import *
    
    def loadFullDataFromSource(table_name):
        df = spark.read.format("parquet").load('Files/' + table_name)
        df = df.drop("Photo")
        df.write.mode("overwrite").format("delta").save("Tables/" + table_name)
    
    full_tables = [
        'dimension_city',
        'dimension_customer',
        'dimension_date',
        'dimension_employee',
        'dimension_stock_item'
    ]
    
    for table in full_tables:
        loadFullDataFromSource(table)
    ```
> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)
>
> ![](./media/image74.png)

6.  To validate the created tables, click and select refresh on
    the **Tables** in the **Explorer** panel until all the tables appear
    in the list.

> ![](./media/image75.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image76.png)

### Task 2: Transforming Business Data for Aggregation

An organization might have data engineers working with Scala/Python and
other data engineers working with SQL (Spark SQL or T-SQL), all working
on the same copy of the data. Fabric makes it possible for these
different groups, with varied experience and preference, to work and
collaborate. The two different approaches transform and generate
business aggregates. You can pick the one suitable for you or mix and
match these approaches based on your preference without compromising on
the performance:

- **Approach \#1** - Use PySpark to join and aggregates data for
  generating business aggregates. This approach is preferable to someone
  with a programming (Python or PySpark) background.

- **Approach \#2** - Use Spark SQL to join and aggregates data for
  generating business aggregates. This approach is preferable to someone
  with SQL background, transitioning to Spark.

**Approach \#1 (sale_by_date_city)**

Use PySpark to join and aggregate data for generating business
aggregates. With the following code, you create three different Spark
dataframes, each referencing an existing delta table. Then you join
these tables using the dataframes, do group by to generate aggregation,
rename a few of the columns, and finally write it as a delta table in
the **Tables** section of the lakehouse to persist with the data.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

In this cell, you create three different Spark dataframes, each
referencing an existing delta table.

    ```
    df_fact_sale = spark.read.table("wwilakehouse.fact_sale") 
    df_dimension_date = spark.read.table("wwilakehouse.dimension_date")
    df_dimension_city = spark.read.table("wwilakehouse.dimension_city")
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image77.png)

2.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

In this cell, you join these tables using the dataframes created
earlier, do group by to generate aggregation, rename a few of the
columns, and finally write it as a delta table in the **Tables** section
of the lakehouse.

    ```
    sale_by_date_city = df_fact_sale.alias("sale") \
    .join(df_dimension_date.alias("date"), df_fact_sale.InvoiceDateKey == df_dimension_date.Date, "inner") \
    .join(df_dimension_city.alias("city"), df_fact_sale.CityKey == df_dimension_city.CityKey, "inner") \
    .select("date.Date", "date.CalendarMonthLabel", "date.Day", "date.ShortMonth", "date.CalendarYear", "city.City", "city.StateProvince", 
     "city.SalesTerritory", "sale.TotalExcludingTax", "sale.TaxAmount", "sale.TotalIncludingTax", "sale.Profit")\
    .groupBy("date.Date", "date.CalendarMonthLabel", "date.Day", "date.ShortMonth", "date.CalendarYear", "city.City", "city.StateProvince", 
     "city.SalesTerritory")\
    .sum("sale.TotalExcludingTax", "sale.TaxAmount", "sale.TotalIncludingTax", "sale.Profit")\
    .withColumnRenamed("sum(TotalExcludingTax)", "SumOfTotalExcludingTax")\
    .withColumnRenamed("sum(TaxAmount)", "SumOfTaxAmount")\
    .withColumnRenamed("sum(TotalIncludingTax)", "SumOfTotalIncludingTax")\
    .withColumnRenamed("sum(Profit)", "SumOfProfit")\
    .orderBy("date.Date", "city.StateProvince", "city.City")
    
    sale_by_date_city.write.mode("overwrite").format("delta").option("overwriteSchema", "true").save("Tables/aggregate_sale_by_date_city")
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image78.png)

**Approach \#2 (sale_by_date_employee)**

Use Spark SQL to join and aggregate data for generating business
aggregates. With the following code, you create a temporary Spark view
by joining three tables, do group by to generate aggregation, and rename
a few of the columns. Finally, you read from the temporary Spark view
and finally write it as a delta table in the **Tables** section of the
lakehouse to persist with the data.

3.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

In this cell, you create a temporary Spark view by joining three tables,
do group by to generate aggregation, and rename a few of the columns.

    ```
    %%sql
    CREATE OR REPLACE TEMPORARY VIEW sale_by_date_employee
    AS
    SELECT
           DD.Date, DD.CalendarMonthLabel
     , DD.Day, DD.ShortMonth Month, CalendarYear Year
          ,DE.PreferredName, DE.Employee
          ,SUM(FS.TotalExcludingTax) SumOfTotalExcludingTax
          ,SUM(FS.TaxAmount) SumOfTaxAmount
          ,SUM(FS.TotalIncludingTax) SumOfTotalIncludingTax
          ,SUM(Profit) SumOfProfit 
    FROM wwilakehouse.fact_sale FS
    INNER JOIN wwilakehouse.dimension_date DD ON FS.InvoiceDateKey = DD.Date
    INNER JOIN wwilakehouse.dimension_Employee DE ON FS.SalespersonKey = DE.EmployeeKey
    GROUP BY DD.Date, DD.CalendarMonthLabel, DD.Day, DD.ShortMonth, DD.CalendarYear, DE.PreferredName, DE.Employee
    ORDER BY DD.Date ASC, DE.PreferredName ASC, DE.Employee ASC
    ```
 ![A screenshot of a computer AI-generated content may be
incorrect.](./media/image79.png)

8.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

In this cell, you read from the temporary Spark view created in the
previous cell and finally write it as a delta table in
the **Tables** section of the lakehouse.

    ```
    sale_by_date_employee = spark.sql("SELECT * FROM sale_by_date_employee")
    sale_by_date_employee.write.mode("overwrite").format("delta").option("overwriteSchema", "true").save("Tables/aggregate_sale_by_date_employee")
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image80.png)

9.  To validate the created tables, click and select refresh on
    the **Tables** until the aggregate tables appear.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image81.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image82.png)

Both the approaches produce a similar outcome. You can choose based on
your background and preference, to minimize the need for you to learn a
new technology or compromise on the performance.

Also you may notice that you're writing data as delta lake files. The
automatic table discovery and registration feature of Fabric picks up
and registers them in the metastore. You don't need to explicitly call
CREATE TABLE statements to create tables to use with SQL.

# Exercise 4: Building reports in Microsoft Fabric

In this section of the tutorial, you create a Power BI data model and
create a report from scratch.

### Task 1: Explore data in the silver layer using the SQL endpoint

Power BI is natively integrated in the whole Fabric experience. This
native integration brings a unique mode, called DirectLake, of accessing
the data from the lakehouse to provide the most performant query and
reporting experience. DirectLake mode is a groundbreaking new engine
capability to analyze very large datasets in Power BI. The technology is
based on the idea of loading parquet-formatted files directly from a
data lake without having to query a data warehouse or lakehouse
endpoint, and without having to import or duplicate data into a Power BI
dataset. DirectLake is a fast path to load the data from the data lake
straight into the Power BI engine, ready for analysis.

In traditional DirectQuery mode, the Power BI engine directly queries
the data from the source to execute each query, and the query
performance depends on data retrieval speed. DirectQuery eliminates the
need to copy data, ensuring that any changes in the source are
immediately reflected in the query results during the import. On the
other hand, in Import mode performance is better because the data is
readily available in the memory without querying data from the source
for each query execution. However, the Power BI engine must first copy
the data into memory during data refresh. Only changes to the underlying
data source are picked up during the next data refresh(in scheduled as
well as on-demand refresh).

DirectLake mode now eliminates this import requirement by loading the
data files directly into memory. Because there's no explicit import
process, it's possible to pick up any changes at the source as they
occur, thus combining the advantages of DirectQuery and import mode
while avoiding their disadvantages. DirectLake mode is therefore the
ideal choice for analyzing very large datasets and datasets with
frequent updates at the source.

1.  From the left menu, select workspace icon and then select workspace
    name.

> ![A screenshot of a computer Description automatically
> generated](./media/image83.png)

2.  From the left menu select
    the **Fabric <Lakehouse-@lab.LabInstance.Id>** then select your
    Semantic model named **wwilakehouse**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image84.png)

3.  On the top menu bar select **Open semantic model** to open the data
    model designer.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image85.png)

4.  At the top-right ensure that the data model designer is in
    the **Editing** mode. This should change the drop-down text to
    “Editing”.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image86.png)

5.  In the menu ribbon select **Edit tables** to display the table
    synchronization dialog.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image87.png)

6.  On the **Edit semantic model** dialog **select all** the tables and
    then select **Confirm** at the bottom of the dialog to synchronize
    the Semantic model.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image88.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image89.png)

7.  From the **fact_sale** table, drag the **CityKey** field and drop it
    on the **CityKey** field in the **dimension_city** table to create a
    relationship. The **Create Relationship** dialog box appears.

> Note: Rearrange the tables by clicking on the table, dragging and
> dropping to have the dimension_city and the fact_sale tables next to
> each other. The same holds good for any two tables that you are trying
> to create relationship. This is just to make the drag and drop of the
> columns between the tables is easier. ![](./media/image90.png)

8.  In the **Create Relationship** dialog box:

    - **Table 1** is populated with **fact_sale** and the column
      of **CityKey**.

    - **Table 2** is populated with **dimension_city** and the column
      of **CityKey**.

    - Cardinality: **Many to one (\*:1)**

    - Cross filter direction: **Single**

    - Leave the box next to **Make this relationship active** selected.

    - Select the box next to **Assume referential integrity.**

    - Select **Save.**

> ![](./media/image91.png)

9.  Next, add these relationships with the same **Create
    Relationship** settings as shown above but with the following tables
    and columns:

    - **StockItemKey(fact_sale)** - **StockItemKey(dimension_stock_item)**

> ![](./media/image92.png)
>
> ![](./media/image93.png)

- **Salespersonkey(fact_sale)** - **EmployeeKey(dimension_employee)**

> ![](./media/image94.png)

10. Ensure to create the relationships between the below two sets using
    the same steps as above.

    - **CustomerKey(fact_sale)** - **CustomerKey(dimension_customer)**

    - **InvoiceDateKey(fact_sale)** - **Date(dimension_date)**

11. After you add these relationships, your data model should be as
    shown in the below image and is ready for reporting.

> ![](./media/image95.png)

### Task 2: Build Report

1.  From the top ribbon, select **File** and select **Create new
    report** to start creating reports/dashboards in Power BI.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image96.png)

2.  On the Power BI report canvas, you can create reports to meet your
    business requirements by dragging required columns from
    the **Data** pane to the canvas and using one or more of available
    visualizations.

> ![](./media/image97.png)

**Add a title:**

3.  In the Ribbon, select **Text box**. Type in **WW Importers Profit
    Reporting**. **Highlight** the **text** and increase size to **20**.

> ![](./media/image98.png)

4.  Resize the text box and place it in the **upper left** of the report
    page and click outside the textbox.

> ![](./media/image99.png)

**Add a Card:**

- On the **Data** pane, expand **fact_sales** and check the box next
  to **Profit**. This selection creates a column chart and adds the
  field to the Y-axis.

> ![](./media/image100.png)

5.  With the bar chart selected, select the **Card** visual in the
    visualization pane.

> ![](./media/image101.png)

6.  This selection converts the visual to a card. Place the card under
    the title.

> ![](./media/image102.png)

7.  Click anywhere on the blank canvas (or press the Esc key) so the
    Card that we just placed is no longer selected.

**Add a Bar chart:**

8.  On the **Data** pane, expand **fact_sales** and check the box next
    to **Profit**. This selection creates a column chart and adds the
    field to the Y-axis. 

> ![](./media/image103.png)

9.  On the **Data** pane, expand **dimension_city** and check the box
    for **SalesTerritory**. This selection adds the field to the
    Y-axis. 

> ![](./media/image104.png)

10. With the bar chart selected, select the **Clustered bar
    chart** visual in the visualization pane. This selection converts
    the column chart into a bar chart.

> ![](./media/image105.png)

11. Resize the Bar chart to fill in the area under the title and Card.

> ![](./media/image106.png)

12. Click anywhere on the blank canvas (or press the Esc key) so the bar
    chart is no longer selected.

**Build a stacked area chart visual:**

13. On the **Visualizations** pane, select the **Stacked area
    chart** visual.

> ![](./media/image107.png)

14. Reposition and resize the stacked area chart to the right of the
    card and bar chart visuals created in the previous steps.

> ![](./media/image108.png)

15. On the **Data** pane, expand **fact_sales** and check the box next
    to **Profit**. Expand **dimension_date** and check the box next
    to **FiscalMonthNumber**. This selection creates a filled line chart
    showing profit by fiscal month.

> ![](./media/image109.png)

16. On the **Data** pane, expand **dimension_stock_item** and
    drag **BuyingPackage** into the Legend field well. This selection
    adds a line for each of the Buying Packages.

> ![](./media/image110.png) ![](./media/image111.png)

17. Click anywhere on the blank canvas (or press the Esc key) so the
    stacked area chart is no longer selected.

**Build a column chart:**

18. On the **Visualizations** pane, select the **Stacked column
    chart** visual.

> ![](./media/image112.png)

19. On the **Data** pane, expand **fact_sales** and check the box next
    to **Profit**. This selection adds the field to the Y-axis.

20.  On the **Data** pane, expand **dimension_employee** and check the
    box next to **Employee**. This selection adds the field to the
    X-axis.

> ![](./media/image113.png)

21. Click anywhere on the blank canvas (or press the Esc key) so the
    chart is no longer selected.

22. From the ribbon, select **File** \> **Save**.

> ![](./media/image114.png)

23. Enter the name of your report as **Profit Reporting**.
    Select **Save**.

> ![](./media/image115.png)

24. You will get a notification stating that the report has been saved. 

> ![](./media/image116.png)

# Exercise 5: Clean up resources

You can delete individual reports, pipelines, warehouses, and other
items or remove the entire workspace. Use the following steps to delete
the workspace you created for this tutorial.

1.  Select your workspace, the **Fabric Lakehouse Tutorial-XX** from the
    left-hand navigation menu. It opens the workspace item view.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image117.png)

2.  Select the **...** option under the workspace name and
    select **Workspace settings**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image118.png)

3.  Select **General** and **Remove this workspace.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image119.png)

4.  Click on **Delete** in the warning that pops up.

> ![](./media/image120.png)

5.  Wait for a notification that the Workspace has been deleted, before
    proceeding to the next lab.

> ![](./media/image121.png)

**Summary**: This practical lab focuses on setting up and configuring
essential components within Microsoft Fabric and Power BI for data
management and reporting. It includes tasks like activating trials,
configuring OneDrive, creating workspaces, and setting up lakehouses.
The lab also covers tasks related to ingesting sample data, optimizing
delta tables, and building reports in Power BI for effective data
analysis. The objectives aim to provide hands-on experience in utilizing
Microsoft Fabric and Power BI for data management and reporting purpo


