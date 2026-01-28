# 用例1：创建Lakehouse，导入样本数据并生成报告

**介绍**

本实验室将带你从数据采集到数据消费的全流程。它帮助你建立对Fabric的基本理解，包括不同的体验及其集成方式，以及在该平台上工作所带来的专业和公民开发者的体验。本实验室并非旨在作为参考架构、详尽的功能和功能列表，或推荐具体的最佳实践。

传统上，组织一直在构建现代数据仓库以满足其交易型和结构化数据分析需求。以及用于大数据（半结构化/非结构化）数据分析需求的数据lakehouse。这两个系统并行运行，造成了silos、数据重复和增加的总拥有成本。

Fabric通过统一数据存储和Delta
Lake格式的标准化，帮助你消除数据silos，消除数据重复性，并大幅降低总拥有成本。

借助Fabric提供的灵活性，你可以实现lakehouse或数据仓库架构，或者将它们结合起来，通过简单的实现实现实现两者的最佳优势。在本教程中，你将以一家零售企业为例，从头到尾建造其lakehouse别墅。
它采用[了 medallion
架构](https://learn.microsoft.com/en-us/azure/databricks/lakehouse/medallion)，青铜层有原始数据，银层有验证和去重的数据，金层有高度精炼的数据。你也可以用同样的方法为任何行业的组织实施lakehouse。

本实验室解释了虚构的Wide World
Importers公司零售领域的开发者如何完成以下步骤。

**目标**:

1\. 登录 Power BI 账户，启动免费的 Microsoft Fabric 试用。

2\. 在Power BI中启动Microsoft Fabric（预览版）试用。

3\. 配置 Microsoft 365 管理中心的 OneDrive 注册功能。

4\. 为组织构建并实施端到端的lakehouse，包括创建Fabric工作区和lakehouse。

5\. 将样本数据导入湖屋，并为后续处理做准备。

6\. 用Python/PySpark和SQL笔记本转换和准备数据。

7\. 用不同的方法创建业务汇总表。

8\. 建立表格间的关系，以实现无缝报告。

9\. 基于准备好的数据构建带有可视化的Power BI报告。

10\. 保存并存储已创建的报告，以便未来参考和分析。

## 练习 1：搭建 Lakehouse 端到端场景

### 任务1：登录Power BI账户并注册免费Microsoft Fabric试用版

1.  打开浏览器，进入地址栏，输入或粘贴以下URL：+++https：//app.fabric.microsoft.com/+++，然后按下**Enter **键。

> ![](./media/image1.png)

2.  在 **Microsoft Fabric**
    窗口中，输入你的凭证，然后点击**Submit** 按钮。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image2.png)

3.  然后，在 **Microsoft** 窗口输入密码，点击**Sign in** 按钮。

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image3.png)

4.  在 **Stay signed in?**  窗口，点击“**Yes**”按钮。

> ![](./media/image4.png)

5.  你将被引导到Power BI主页。

> ![](./media/image5.png)

## 练习 2：为您的组织构建并实施一个完整的lakehouse项目

### 任务1：创建Fabric工作区

在这个任务中，你需要创建一个Fabric工作区。工作区包含了本 lakehouse
教程所需的所有内容，包括 lakehouse、数据流、Data Factory
管道、笔记本、Power BI 数据集和报表。

1.  Fabric主页，选择**+New workspace**瓷砖。

> ![A screenshot of a computer Description automatically
> generated](./media/image6.png)

2.  在右侧的**Create a
    workspace**面板中，输入以下细节，然后点击“**Apply** ”按钮。 

[TABLE]

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image7.png)

注意：要查找您的实验室即时ID，请选择“Help”并复制即时ID。

> ![A screenshot of a computer Description automatically
> generated](./media/image8.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image9.png)

3.  等待部署完成。完成大约需要2-3分钟。

> ![A screenshot of a computer Description automatically
> generated](./media/image10.png)

### 任务 2: 创建 lakehouse

1.  点击导航栏中的**+New item**按钮创建新lakehouse。

> ![A screenshot of a computer Description automatically
> generated](./media/image11.png)

2.  点击“**Lakehouse**”瓷砖。

> ![A screenshot of a computer Description automatically
> generated](./media/image12.png)

3.  在“**New lakehouse** ”对话框中，输入“**Name** ”栏的
    +++**wwilakehouse+++** ，点击“**Create**”按钮，打开新湖屋。 

> **注意：**确保在**wwilakehouse**之前清空。
>
> ![A screenshot of a computer Description automatically
> generated](./media/image13.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image14.png)

4.  你会看到一条通知，提示**Successfully created SQL endpoint**。

> ![](./media/image15.png)

### 任务3：导入样本数据

1.  In th在**wwilakehouse**页面，点击“**Get data in your
    lakehouse** ”部分，点击**Upload files as shown in the below image**
    。

> ![A screenshot of a computer Description automatically
> generated](./media/image16.png)

2.  在“Upload files”标签页中，点击文件下的文件夹

> ![A screenshot of a computer Description automatically
> generated](./media/image17.png)

3.  在虚拟机上浏览到
    **C：\LabFiles**，然后选择**dimension_customer.csv**文件，点击**Open**按钮。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image18.png)

4.  然后点击**Upload **按钮并关闭

> ![A screenshot of a computer Description automatically
> generated](./media/image19.png)

5.  **关闭** 上传文件面板。

> ![A screenshot of a computer Description automatically
> generated](./media/image20.png)

6.  点击并选择Files刷新。文件出现了。

> ![A screenshot of a computer Description automatically
> generated](./media/image21.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

7.  在**Lakehouse**页面，在资源管理器面板下选择“文件”。不过，现在你的鼠标可以**dimension_customer.csv**文件。点击**dimension_customer.csv**旁边的水平省略号**（...）。**点击“**Load
    Table**”，然后选择“**New table**”。

> ![](./media/image23.png)

8.  在“**Load file to new table** ”对话框中，点击**Load** 按钮。

> ![](./media/image24.png)

9.  现在**dimension_customer**表已经成功创建。

> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

10. 选择 **dbo** 模式下的 **dimension_customer** 表。

> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image27.png)

11. 你也可以用lakehouse的SQL端点用SQL语句查询数据。在屏幕右上角的
    **Lakehouse** 下拉菜单中选择 **SQL analytics endpoint** 。

> ![A screenshot of a computer Description automatically
> generated](./media/image28.png)

12. 在 **wwilakehouse** 页面，在 Explorer 下选择 **dimension_customer**
    表预览其数据，并选择 **New SQL query **来写你的 SQL 语句。

> ![A screenshot of a computer Description automatically
> generated](./media/image29.png)

13. 以下示例查询基于**dimension_customer**表的**BuyingGroup列**汇总行数
    。SQL查询文件会自动保存以供未来参考，你可以根据需要重命名或删除这些文件。按照下图所示粘贴代码，然后点击播放图标**运行**
    脚本:

> SELECT BuyingGroup, Count(\*) AS Total
>
> FROM dimension_customer
>
> GROUP BY BuyingGroup
>
> ![A screenshot of a computer Description automatically
> generated](./media/image30.png)

**注意**：如果你在脚本执行过程中遇到错误，请交叉检查脚本语法，确保没有不必要的空格。

14. 此前，所有lakehouse表和视图都会自动添加到语义模型中。根据最近的更新，对于新的lakehouse，你必须手动将表格添加到语义模型中。

> ![A screenshot of a computer Description automatically
> generated](./media/image31.png)

15. 在lakehouse **主页**标签中，选择“**New semantic
    model** ”，选择你想添加到语义模型中的表格。

> ![A screenshot of a computer Description automatically
> generated](./media/image32.png)

16. 在“**New semantic model** ”对话框中输入
    +++wwilakehouse+++，然后从表列表中选择**dimension_customer**表，选择**Confirm** 以创建新模型。

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

### 任务4：制作报告

1.  现在，点击 左侧导航面板上的 **Fabric Lakehouse Tutorial-XX** 。

> ![A screenshot of a computer Description automatically
> generated](./media/image34.png)

2.  在 **Fabric Lakehouse Tutorial-XX** 视图中，选择类型**Semantic
    model**的 **wwilakehouse**。

> ![A screenshot of a computer Description automatically
> generated](./media/image35.png)

3.  从语义模型窗格中，你可以查看所有表格。你可以选择从零创建报告、分页报告，或者让
    Power BI 根据你的数据自动生成报告。在本教程中，在“**Explore this
    data**”中，选择“**Auto-create a report**”，如下图所示。

> ![A screenshot of a computer Description automatically
> generated](./media/image36.png)

4.  报告准备好后，点击“ **View report now** ”以打开并查看。![A
    screenshot of a computer AI-generated content may be
    incorrect.](./media/image37.png)

> ![A screenshot of a computer Description automatically
> generated](./media/image38.png)

5.  由于表格是一个维度，里面没有测量值，Power BI
    会为行数创建一个度量，并在不同列中汇总，并生成不同的图表，如下图所示。

6.  通过从顶部的色带选择**“Save**”，将此报告保存以备将来使用。

> ![A screenshot of a computer Description automatically
> generated](./media/image39.png)

7.  在“**Save your report** ”对话框中，输入你的报告名称为
    +++dimension_customer-report+++，然后选择**Save**。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image40.png)

8.  你会看到一条通知，说“**Report saved**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image41.png)

# 练习2：将数据导入lakehouse

在这个练习中，你会将来自世界大战（WWI）的额外维度和事实表导入lakehouse。

### 任务1：数据导入

1.  现在，点击 左侧导航面板上的 **Fabric Lakehouse Tutorial-XX**。

> ![A screenshot of a computer Description automatically
> generated](./media/image42.png)

2.  同样，选择工作区名称。

![A screenshot of a computer screen Description automatically
generated](./media/image43.png)

3.  在 **Fabric Lakehouse Tutorial-XX** 工作区页面，点击 **+New
    item** 按钮，然后选择**Pipeline**。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image44.png)

4.  在“New pipeline”对话框中，将名称指定为
    **+++IngestDataFromSourceToLakehouse+++**，并选择**Create。**
    创建一个新的数据工厂流水线并被创建。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image45.png)

> ![A screenshot of a computer Description automatically
> generated](./media/image46.png)

5.  在新建的数据工厂流水线（即
    **IngestDataFromSourceToLakehouse**）中，选择“**Copy
    data**”下拉菜单，并选择“**Add to canvas**”选项。

> ![A screenshot of a computer Description automatically
> generated](./media/image47.png)

6.  选择**copy data**后，导航到**Source**签页。

![](./media/image48.png)

7.  选择**Connection**下拉菜单，选择“**Browse all**”选项。

![](./media/image49.png)

8.  从左侧窗格选择“**Sample data**”，并选择**Retail Data Model from Wide
    World Importers**。

![](./media/image50.png)

9.  在“**Connect to data source**”窗口中，选择“**Retail Data Model from
    Wide World Importers** ”数据预览并选择**OK**。

> ![](./media/image51.png)

10. 选择数据源连接作为样本数据。

![A screenshot of a computer Description automatically
generated](./media/image52.png)

11. 现在，导航到**destination**标签页。

![](./media/image53.png)

12. 在目标标签页，点击**connection**下拉菜单，选择“**Browse**
    **all**”选项。

![](./media/image54.png)

13. 在选择目的地窗口时，从左侧窗格选择**OneLake
    catalog**，然后选择**wwilakehouse**。

> ![](./media/image55.png)

14. 目的地现定为Lakehouse。将**Root
    Folder**指定为**Files**，并确保文件格式为**Parquet**。

![A screenshot of a computer Description automatically
generated](./media/image56.png)

8.  点击 **“Run”** 以运行复制数据。

> ![A screenshot of a computer Description automatically
> generated](./media/image57.png)

9.  点击“**Save and run**”按钮，这样该流程就会被保存并运行。

> ![A screenshot of a computer error Description automatically
> generated](./media/image58.png)

10. 数据复制过程大约需要1-3分钟完成。

> ![A screenshot of a computer Description automatically
> generated](./media/image59.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image60.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

11. 在输出标签下，选择 **Copy
    data1** 查看数据传输的详细信息。看到**Status** 为**Succeeded**后，点击**Close** 按钮。

> ![A screenshot of a computer Description automatically
> generated](./media/image62.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image63.png)

12. 管道成功执行后，进入你的湖屋（**wwilakehouse**）打开资源管理器查看导入的数据。

> ![A screenshot of a computer Description automatically
> generated](./media/image64.png)

13. 验证所有 **WideWorldImporters
    文件夹**是否都存在于资源**Explorer** 视图中，并且包含所有表的数据。

> ![A screenshot of a computer Description automatically
> generated](./media/image65.png)

# 练习3：准备并转换lakehouse中的数据

### 任务1：转换数据并加载为银色Delta表

1.  在 **wwilakehouse** 页面，点击命令栏的“**Open
    notebook** ”下拉，然后选择 **New notebook**。

> ![A screenshot of a computer Description automatically
> generated](./media/image66.png)

2.  在**Lakehouse
    explorer**中的打开笔记本中，你会发现笔记本已经与你打开的lakehouse关联。

> ![](./media/image67.png)

\[！note\] **注意**：Fabric 提供
[**V-order**](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order)功能，用于写入优化的三角洲湖文件。V-order通常能将压缩率提升三到四倍，性能加速高达十倍，而这些文件则是未优化的Delta
Lake文件。Fabric中的Spark动态优化分区，生成默认大小为128
MB的文件。目标文件大小可根据工作负载需求通过配置进行调整。
通过[**优化写**](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order#what-is-optimized-write)入功能，Apache
Spark引擎减少写入文件数量，并旨在增加写入数据的单个文件大小。

3.  在你将数据写入湖屋的**表**部分为三角湖表之前，你需要使用Fabric的两个功能（**V-order**和**优化写入**）来优化数据写入并提升读取性能。要在会话中启用这些功能，请在笔记本的第一个单元格中设置这些配置。

4.  用以下代码更新该**单元格**的代码 ，并点击悬停时左侧出现的**▷ Run
    cell**。

> \# Copyright (c) Microsoft Corporation.
>
> \# Licensed under the MIT License.
>
> spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
>
> spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
>
> spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize",
> "1073741824")

\[!note\]注意：运行单元格时，您无需指定底层 Spark 池或集群的详细信息，

![A screenshot of a computer Description automatically
generated](./media/image68.png)

![A screenshot of a computer Description automatically
generated](./media/image69.png)

因为 Fabric 通过 Live Pool 提供这些资源。每个 Fabric
工作区都自带一个默认的 Spark 池，称为 Live
Pool。这意味着创建笔记本时，您无需担心指定任何 Spark
配置或集群详细信息。执行第一个笔记本命令后，Live Pool
会在几秒钟内启动并运行。Spark
会话建立后，代码便开始执行。在此笔记本中，只要 Spark
会话处于活动状态，后续代码的执行几乎是瞬间完成的。

5.  接下来，你从 lakehouse 的**文件**部分读取原始数据
    ，并在转换过程中为不同日期部分添加更多列。你使用 partitionBy Spark
    API 对数据进行分区，然后再根据新创建的数据部分列（年度和季度）写成
    delta 表。

6.  使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击**▷ Run
    cell**按钮，查看输出结果

**注意**：如果你看不到输出，请点击**Spark**作业左侧的水平线。

from pyspark.sql.functions import col, year, month, quarter

table_name = 'fact_sale'

df = spark.read.format("parquet").load('Files/fact_sale_1y_full')

df = df.withColumn('Year', year(col("InvoiceDateKey")))

df = df.withColumn('Quarter', quarter(col("InvoiceDateKey")))

df = df.withColumn('Month', month(col("InvoiceDateKey")))

df.write.mode("overwrite").format("delta").partitionBy("Year","Quarter").save("Tables/" +
table_name)

> ![A screenshot of a computer Description automatically
> generated](./media/image70.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image71.png)
>
> ![](./media/image72.png)

7.  表格加载完成后，你可以继续加载其余尺寸的数据。接下来的单元格创建一个函数，用于读取
    lakehouse
    **文件**部分中每个作为参数传递的表名的原始数据。接下来，它创建一个维度表列表。最后，它会循环处理表列表，并为每个从输入参数读取的表名创建一个增量表。

8.  使用单元格输出下方的 **+
    Code**图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击 **▷
    Run cell**  按钮，查看输出结果。

> from pyspark.sql.types import \*
>
> def loadFullDataFromSource(table_name):
>
> df = spark.read.format("parquet").load('Files/' + table_name)
>
> df = df.drop("Photo")
>
> df.write.mode("overwrite").format("delta").save("Tables/" +
> table_name)
>
> full_tables = \[
>
> 'dimension_city',
>
> 'dimension_customer',
>
> 'dimension_date',
>
> 'dimension_employee',
>
> 'dimension_stock_item'
>
> \]
>
> for table in full_tables:
>
> loadFullDataFromSource(table)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)
>
> ![](./media/image74.png)

6.  要验证已创建的表，请在**Explorer**面板的“**Tables**”中点击并选择刷新，直到所有表都出现在列表中。

> ![](./media/image75.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image76.png)

### **任务2：整合业务数据以实现聚合**

一个组织可能有数据工程师在使用Scala/Python，还有其他数据工程师在使用SQL（Spark
SQL或T-SQL），他们都处理同一份数据。Fabric使这些经验和偏好各异的群体能够合作和合作。这两种不同的方法能够转化并生成业务汇总。你可以根据喜好选择合适的方案，或根据喜好混合搭配这些方法，而不牺牲性能:

- **Approach \#1** - 使用 PySpark
  连接和汇总数据以生成业务聚合。这种方法比有编程背景（Python或PySpark）的人更合适。

- **Approach \#2** - 使用 Spark SQL
  连接和汇总数据以生成业务聚合。这种方法对有 SQL 背景、转用 Spark
  的人来说更受欢迎。

**Approach \#1 (sale_by_date_city)**

使用 PySpark
进行合并和汇总数据，生成业务聚合。用以下代码，你创建三个不同的 Spark
数据帧，每个数据帧都引用一个已有的 delta
表。然后你用数据帧连接这些表，进行分组生成聚合，重命名几列，最后在湖屋的**表**部分写成一个增量表，以保持数据的保存。

1.  使用单元格输出下方的 **+
    Code**图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击 **▷
    Run cell** 按钮，查看输出结果

在这个单元格中，你创建三个不同的 Spark
数据帧，每个数据帧都引用一个已有的 delta 表。

> df_fact_sale = spark.read.table("wwilakehouse.fact_sale")
>
> df_dimension_date = spark.read.table("wwilakehouse.dimension_date")
>
> df_dimension_city = spark.read.table("wwilakehouse.dimension_city")

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image77.png)

2.  使用单元格输出下方的 **+
    Code**图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击 **▷
    Run cell**按钮，查看输出结果

在这个单元格里，你用之前创建的数据帧连接这些表，进行分组生成聚合，重命名几列，最后在
lakehouse 的**表格**部分写成一个delta表。

sale_by_date_city = df_fact_sale.alias("sale") \\

.join(df_dimension_date.alias("date"), df_fact_sale.InvoiceDateKey ==
df_dimension_date.Date, "inner") \\

.join(df_dimension_city.alias("city"), df_fact_sale.CityKey ==
df_dimension_city.CityKey, "inner") \\

.select("date.Date", "date.CalendarMonthLabel", "date.Day",
"date.ShortMonth", "date.CalendarYear", "city.City",
"city.StateProvince",

"city.SalesTerritory", "sale.TotalExcludingTax", "sale.TaxAmount",
"sale.TotalIncludingTax", "sale.Profit")\\

.groupBy("date.Date", "date.CalendarMonthLabel", "date.Day",
"date.ShortMonth", "date.CalendarYear", "city.City",
"city.StateProvince",

"city.SalesTerritory")\\

.sum("sale.TotalExcludingTax", "sale.TaxAmount",
"sale.TotalIncludingTax", "sale.Profit")\\

.withColumnRenamed("sum(TotalExcludingTax)", "SumOfTotalExcludingTax")\\

.withColumnRenamed("sum(TaxAmount)", "SumOfTaxAmount")\\

.withColumnRenamed("sum(TotalIncludingTax)", "SumOfTotalIncludingTax")\\

.withColumnRenamed("sum(Profit)", "SumOfProfit")\\

.orderBy("date.Date", "city.StateProvince", "city.City")

sale_by_date_city.write.mode("overwrite").format("delta").option("overwriteSchema",
"true").save("Tables/aggregate_sale_by_date_city")

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image78.png)

**Approach \#2 (sale_by_date_employee)**

使用Spark
SQL进行连接和聚合数据，生成业务聚合。用以下代码，你通过连接三个表创建临时
Spark 视图，进行分组生成聚合，并重命名部分列。最后，你从临时的 Spark
视图读取数据，最后将其写入 Lakehouse 的 **Tables** 部分的 delta
表，以保持数据的持久化。

3.  使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击** ▷ Run
    cell**按钮，查看输出结果

在这个单元格里，你通过连接三个表创建临时 Spark
视图，进行分组生成聚合，并重命名部分列。

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

INNER JOIN wwilakehouse.dimension_Employee DE ON FS.SalespersonKey =
DE.EmployeeKey

GROUP BY DD.Date, DD.CalendarMonthLabel, DD.Day, DD.ShortMonth,
DD.CalendarYear, DE.PreferredName, DE.Employee

ORDER BY DD.Date ASC, DE.PreferredName ASC, DE.Employee ASC

 ![A screenshot of a computer AI-generated content may be
incorrect.](./media/image79.png)

8.  使用单元格输出下方的 **+
    Code**图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击 **▷
    Run cell** 按钮，查看输出结果

在这个单元格中，你从前一个单元创建的临时 Spark
视图读取数据，最后将其写成 Lakehouse 的 **Tables** 部分的 delta 表。

sale_by_date_employee = spark.sql("SELECT \* FROM
sale_by_date_employee")

sale_by_date_employee.write.mode("overwrite").format("delta").option("overwriteSchema",
"true").save("Tables/aggregate_sale_by_date_employee")

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image80.png)

9.  要验证已创建的**表**，请点击并选择“refresh”，直到汇总表出现。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image81.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image82.png)

这两种方法的结果相似。你可以根据自己的背景和偏好选择，以减少学习新技术或在性能上做出妥协。

你可能还会发现你把数据写成了三角洲湖的文件。Fabric
的自动表发现和注册功能会在元存储中获取并注册这些数据。你不需要显式调用
CREATE TABLE 语句来创建用于 SQL 的表。

# 练习4：在Microsoft Fabric中构建报表

在教程的这一部分中，你将创建一个Power
BI数据模型，并从零开始创建一份报告。

### 任务1：利用SQL端点探索银层中的数据

Power
BI是原生集成在整个Fabric体验中的。这种原生集成带来了一种独特的模式，称为DirectLake，能够访问湖屋中的数据，提供最高性能的查询和报告体验。DirectLake
模式是一种开创性的全新引擎功能，用于分析 Power BI
中非常庞大的数据集。该技术基于这样一个理念：直接从data
lake加载parquet格式文件，无需查询数据仓库或lakehouse端点，也无需导入或复制数据到Power
BI数据集中。DirectLake 是一种快速路径，可以直接将数据湖的数据加载到
Power BI 引擎，供分析。

在传统的DirectQuery模式下，Power
BI引擎直接从源端查询数据以执行每个查询，查询性能取决于数据检索速度。DirectQuery
消除了复制数据的需求，确保源代码的任何变化在导入过程中立即反映在查询结果中。另一方面，导入模式下性能更好，因为数据在内存中易于获取，无需每次查询都从源端查询数据。然而，Power
BI
引擎必须在数据刷新时先将数据复制到内存中。只有在下一次数据刷新（无论是计划刷新还是按需刷新）时，才会被接收到底层数据源的变更。

DirectLake
模式现在通过直接将数据文件加载到内存中，消除了这种导入要求。由于没有明确的导入流程，用户可以在源头实时捕捉任何变化，从而结合了DirectQuery和导入模式的优势，同时避免了它们的缺点。因此，DirectLake
模式是分析非常大型数据集和源头频繁更新数据集的理想选择。

1.  在左侧菜单中，选择工作区图标，然后选择工作区名称。

> ![A screenshot of a computer Description automatically
> generated](./media/image83.png)

2.  在左侧菜单中选择**Fabric** [**Lakehouse-@lab.LabInstance.Id**
    然后选择名为](mailto:Lakehouse-@lab.LabInstance.Id)**wwilakehouse**的语义模型。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image84.png)

3.  在顶部菜单栏选择“**Open semantic model**”以打开数据模型设计器。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image85.png)

4.  在右上角，确保数据模型设计器处于**Editing**模式。这样下拉文本应该会变成“Editing”。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image86.png)

5.  在菜单功能区中选择**“Edit tables**”以显示表格同步对话框。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image87.png)

6.  在“**Edit semantic
    model** ”对话框中，**选择所有**表格，然后在对话框底部选择“**Confirm** ”以同步语义模型。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image88.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image89.png)

7.  从**fact_sale**表中，拖动**CityKey**字段并将其放置在**dimension_city**表中的CityKey**字段**
    上，创建关联。创建**关系对话框**出现了。

> 注意：通过点击表格，拖放表格，将dimension_city和fact_sale表格相邻来重新排列表格。同样的道理也适用于你想要建立关系的两张桌子。这样做只是为了方便在表格之间拖拽列。 ![](./media/image90.png)

8.  在**Create Relationship**对话框中:

    - **表1**由**fact_sale**和**CityKey**列填充。

    - **表2**包含**dimension_city**和**CityKey**列。

    - Cardinality: **Many to one (\*:1)**

    - 交叉滤波器方向: **Single**

    - 保持“**Make this relationship active**”旁边的复选框选中。 

    - 选中“**Assume referential integrity**”旁边的复选框。

    - 选择**Save。**

> ![](./media/image91.png)

9.  接下来，将这些关系添加在与上述相同的**Create
    Relationship**置中，但表格和列为基础:

    - **StockItemKey(fact_sale)** - **StockItemKey(dimension_stock_item)**

> ![](./media/image92.png)
>
> ![](./media/image93.png)

- **Salespersonkey(fact_sale)** - **EmployeeKey(dimension_employee)**

> ![](./media/image94.png)

10. 确保按照上述步骤创建下面两组之间的关系。

    - **CustomerKey(fact_sale)** - **CustomerKey(dimension_customer)**

    - **InvoiceDateKey(fact_sale)** - **Date(dimension_date)**

11. 添加这些关系后，您的数据模型应如下图所示，准备进行报告。

> ![](./media/image95.png)

### 任务2：建造报告

1.  从顶部功能区选择**文件**，选择**Create new report**，开始在 Power BI
    中创建报表/仪表盘。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image96.png)

2.  在 Power BI
    报表画布中，您可以通过将所需列从**数据**窗格拖入画布，并使用一个或多个可用的可视化工具来创建满足业务需求的报表。

> ![](./media/image97.png)

**添加标题:**

3.  在功能区中，选择**Text box**。输入**WW Importers Profit
    Reporting**。高**Highlight** 文本并放大到**20**。

> ![](./media/image98.png)

4.  调整文本框大小，放在报告页面左**上角**，点击文本框外。

> ![](./media/image99.png)

**添加卡片:**

- 在**数据**面板上，展开**fact_sales**并勾选利润旁边的框。此选择会生成柱状图表，并将字段添加到Y轴。

> ![](./media/image100.png)

5.  选择柱状图后，在可视化面板中选择**卡片**可视化。

> ![](./media/image101.png)

6.  此选择将视觉化转换为一张卡片。把卡片放在标题下面。

> ![](./media/image102.png)

7.  点击空白画布上的任意位置（或按Esc键），这样刚放置的卡牌就不再被选中。

**添加条形图:**

8.  在**数据**面板上，展开**fact_sales**并勾选利润旁边的框。此选择会生成柱状图表，并将字段添加到Y轴。

> ![](./media/image103.png)

9.  在**数据**面板中，展开**dimension_city**并勾选**SalesTerritory**的选项。该选择将场添加到Y轴上。

> ![](./media/image104.png)

10. 选择条形图后，在可视化窗格中选择**“Clustered bar
    chart**”可视化。此选择将柱状图转换为柱状图。

> ![](./media/image105.png)

11. 调整条形图大小，填满标题和卡片下方的区域。

> ![](./media/image106.png)

12. 点击空白画布上的任意位置（或按Esc键），这样条形图就不再被选中。

**构建叠加面积图可视化:**

13. 在**Visualizations**面板中，选择**Stacked area chart** 可视化。 

> ![](./media/image107.png)

14. 重新定位并调整堆叠区域图，位于卡片右侧，以及之前步骤中创建的条形图可视化。

> ![](./media/image108.png)

15. 在**数据**面板上，展开**fact_sales**并勾选利润旁边的框。展开**dimension_date**，勾选“**FiscalMonthNumber**”旁边的框。该选择会生成一个充满折线图，显示按财政月份的利润。

> ![](./media/image109.png)

16. 在**数据**面板中，展开**dimension_stock_item**，并将
    **BuyingPackage** 拖入图例字段。该选项为每个购买套餐添加一行。

> ![](./media/image110.png) ![](./media/image111.png)

17. 点击空白画布上的任意位置（或按Esc键），这样堆叠面积图就不再被选中。

**制作柱状图:**

18. 在**Visualizations **面板中，选择**Stacked column chart**可视化。

> ![](./media/image112.png)

19. 在**数据**面板上，展开**fact_sales**并勾选**利润**旁边的框。该选择将场添加到Y轴上。

20.  在**数据**面板中，展开**dimension_employee**，勾选“**Employee**”旁边的框。该选择将场加到X轴上。

> ![](./media/image113.png)

21. 在空白画布上任意点击（或按Esc键），这样图表就不再被选中。

22. 从功能区选择**“File \> Save**”。

> ![](./media/image114.png)

23. 请输入您的报告名称为**“Profit Reporting**”。选择**Save**。

> ![](./media/image115.png)

24. 你会收到通知，说报告已被保存。

> ![](./media/image116.png)

# 练习5：清理资源

你可以删除单个报表、管道、仓库和其他项目，或者删除整个工作区。请按照以下步骤删除你为本教程创建的工作区。

1.  选择你的工作区，从左侧导航菜单中选择**Fabric Lakehouse
    Tutorial-XX**。它会打开工作区的物品视图。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image117.png)

2.  选择...... 在工作区名称下选择选项，选择**Workspace settings**。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image118.png)

3.  选择**“General**”并**Remove this workspace。**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image119.png)

4.  点击弹出的警告中“**Delete**”。

> ![](./media/image120.png)

5.  等待工作区被删除的通知后，再进入下一个实验室。

> ![](./media/image121.png)

**总结**：本实践实验室重点在Microsoft Fabric和Power
BI中设置和配置数据管理与报告所需的关键组件。它包括激活试用、配置
OneDrive、创建工作区和设置湖屋等任务。实验室还涵盖采样数据的导入、优化差异表以及在
Power BI 中构建报告以实现有效数据分析等任务。目标旨在提供使用Microsoft
Fabric和Power BI进行数据管理和报告目的的实践经验
