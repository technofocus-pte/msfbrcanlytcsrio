# 用例04——在Microsoft Fabric中构建Contoso的销售和地理Data Warehouse

**简介**

Contoso是一家跨国零售公司，正寻求现代化其
data基础设施，以提升销售和地理分析能力。目前，他们的销售和客户
data分散在多个系统中，使业务分析师和公民开发者难以获得洞察。公司计划将这些
data整合到一个统一平台，利用 Microsoft Fabric
实现交叉查询、销售分析和地理报告。

在本实验室中，你将扮演Contoso的data
engineer角色，负责设计和实施使用Microsoft Fabric的data
warehouse解决方案。您将首先搭建 Fabric 工作区，创建data warehouse，从
Azure Blob Storage 加载 data，并执行分析任务，向 Contoso
的决策者提供洞察。

虽然Microsoft Fabric中的许多概念对
data和分析专业人士来说可能很熟悉，但在新环境中应用这些概念可能具有挑战性。本实验室旨在逐步带领从
data采集到 data消耗的端到端场景，建立对 Microsoft Fabric
用户体验、各种体验及其集成点，以及 Microsoft Fabric
专业和公民开发者体验的基本理解。

**目标**

- 搭建一个启用试用版的Fabric工作区。

- 在 Microsoft Fabric 中建立一个名为 WideWorldImporters 的新Warehouse。

- 通过Data Factory pipeline将 data加载到Warehouse_FabricXX工作区。

- 在data warehouse中生成dimension_city和fact_sale表。

- 用Azure Blob Storage的 data填充dimension_city和fact_sale表。

- 在Warehouse里创建dimension_city和fact_sale的桌子clones。

- 将 Tables dimension_city 和 Tables fact_sale Clone到 dbo1 架构中。

- 开发一个存储过程来转换data并创建aggregate_sale_by_date_city表。

- 使用可视化查询构建器生成查询，以合并和聚合data。

- 使用notebook查询和分析dimension_customer表中的data。

- 包含WideWorldImporters和ShortcutExercise warehouses以便交叉查询。

- 在 WideWorldImporters 和 ShortcutExercise 仓库之间执行 T-SQL 查询。

- 在管理门户中启用 Azure Maps 可视化集成。

- 生成销售分析报告的柱状图、地图和表格可视化。

- 利用OneLake data中心中的WideWorldImporters dataset中的data hub建报告。

- 移除工作区及其相关项目。

## 练习1：创建 Microsoft Fabric 工作区

### 任务1：创建一个工作区

1.  打开浏览器，进入地址栏，输入或粘贴以下URL：+++https://app.fabric.microsoft.com/+++，然后按下**Enter **键。

\[！note\]**注意**：如果你被引导到Microsoft Fabric主页，请跳到步骤#5。

![](./media/image1.png)

2.  在 **Microsoft Fabric**
    窗口中，输入你的凭证，然后点击**Submit**按钮。

| Credential | Value |
|---|---|
| Username | +++@lab.CloudPortalCredential(User1).Username+++ |
| Password | +++@lab.CloudPortalCredential(User1).Password+++ |

> ![](./media/image2.png)

3.  然后，在 **Microsoft** 窗口输入密码，点击**Sign in **按钮。

> ![](./media/image3.png)

4.  在 **Stay signed in? **窗口，点击**“Yes”**按钮。

5.  如果 PowerBI 默认打开，请按照以下步骤操作，否则跳过这一步

- 点击 PowerBI

![](./media/image4.png)

- 从选项中选择Fabric

![](./media/image5.png)

6.  Fabric主页，选择 **+New workspace **瓷砖。

![](./media/image6.png)

7.  在“**Create a
    workspace”**标签中**，**输入以下信息，点击**“Apply**”按钮。

| Field | Value |
|---|---|
| Name | +++Warehouse_Fabric@lab.LabInstance.Id+++ (must be a unique Id) |
| Description | +++This workspace contains all the artifacts for the data warehouse+++ |
| Advanced Under License mode | Fabric |
| Default storage format | Small dataset storage format |

![](./media/image7.png)

![](./media/image8.png)

![](./media/image9.png)

3.  等待部署完成。完成大约需要1-2分钟。当你的新工作区开放时，应该是空的。

![](./media/image10.png)

### 任务2：在 Microsoft Fabric 中创建一个Warehouse

1.  在**Fabric**页面，选择 **+ New
    item **创建lakehouse，然后选择**Warehouse**

![A screenshot of a computer Description automatically
generated](./media/image11.png)

2.  在“**New warehouse**”对话框中，输入
    +++**WideWorldImporters+++** 并点击**“Create**”按钮。

![](./media/image12.png)

3.  配置完成后，会出现**WideWorldImporters** warehouse 的登陆页面。

![](./media/image13.png)

## 练习2：在Microsoft Fabric中将 data导入Warehouse

### 任务1：将 data导入Warehouse

1.  在 **WideWorldImporters**
    仓库着陆页，左侧导航菜单中选择**Warehouse_FabricXX**返回工作区物品列表。

![](./media/image14.png)

2.  在**Warehouse_FabricXX**页面，选择 +**New item**。然后，点击**Copy
    job**，查看“Get data”下的完整可用项目列表。

![](./media/image15.png)

3.  在**“New copy job**”窗口的**Name** 框中，输入 +++**Load Customer Data**+++。选择**Create**

> ![](./media/image16.png)

4.  当复**Copy job** 页面打开时，配置就完成了。

> ![](./media/image17.png)

5.  在**Copy job** 窗口的第一页 ，从该页面的菜单栏选择**“Sample
    data**”。本教程中使用了**Retail Data Model from Wide World
    Importers** 样本。选择此选项以导航至下一页

> ![](./media/image18.png)

6.  样本data的预览加载。在**“Choose
    data**”页面，您可以预览所选dataset。查看数据后，选择**“Next**”。

![](./media/image19.png)

5.  Choose data
    destination的地页面允许您配置商品类型。在OneLake目录中，选择你的
    **Wide World Importers** 仓库，然后选择 **“Next”。**

> ![](./media/image20.png)

6.  **Choose copy job mode**页面，选择**Full copy** 并选择**Next**。

> ![](./media/image21.png)

7.  输入以下目标表，然后选择 **“Next**”。

- dbo.dimension_city

- dbo.dimension_customer

- dbo.dimension_date

- dbo.dimension_employee

- dbo.dimension_stock_item

- dbo.fact_sale

> ![](./media/image22.png)

8.  在**“Review + save**”页面，查看**Source** 和**Destination**。

![](./media/image23.png)

9.  使用**Results** 标签来监控复Copy job的执行情况。

![](./media/image24.png)

10. 完成后，**Copy
    job** 将发送**“Succeeded**”通知和状态。你现在会在仓库中看到来自Wide
    World Importers dataset的六张新表格。

![](./media/image25.png)

11. 在**Load Customer Data** 页面，点击
    左侧导航栏**Warehouse_FabricXX** 工作区，选择**WideWorldImporters**
    Warehouse。

> ![](./media/image26.png)

12. 在 **WideWorldImporters** warehouse 中，展开 **Schemas \> dbo\>
    Tables**，并验证表（**dimension_city**、**dimension_customer**、**dimension_date**、**dimension_employee**、**dimension_stock_item**
    和 **fact_sale**）是否已成功创建。

![](./media/image27.png)

## 练习3： **在Warehouse中用T-SQL克隆表**

### 任务1：**在同一模式内克隆一个表**

1.  在 **WideWorldImporters** 页面，进入**Home** 标签，从 下拉菜单选择
    **SQL**，然后点击“**New SQL query**”。

![](./media/image28.png)

3.  在查询编辑器中，粘贴以下代码。代码创建了dimension_city表和
    fact_sale表的克隆。

```
--Create a clone of the dbo.dimension_city table.
 CREATE TABLE [dbo].[dimension_city1] AS CLONE OF [dbo].[dimension_city];

 --Create a clone of the dbo.fact_sale table.
 CREATE TABLE [dbo].[fact_sale1] AS CLONE OF [dbo].[fact_sale];
```

> ![](./media/image29.png)

4.  要执行查询，在查询设计功能区上选择 **Run**。

![](./media/image30.png)

![](./media/image31.png)

5.  在查询编辑器中，粘贴以下代码。CURRENT_TIMESTAMP T-SQL 函数返回当前
    UTC 时间戳为**datetime**。选择**Run** 以执行查询。

```
SELECT CURRENT_TIMESTAMP;
```

![](./media/image32.png)

6.  要创建一个*past point in
    time*的表克隆，在查询编辑器中粘贴以下代码**替换现有语句**。代码在某个时间点创建了dimension_city表和
    fact_sale 表的克隆。运行查询。

```
--Create a clone of the dbo.dimension_city table at a specific point in time.   
CREATE TABLE [dbo].[dimension_city2] AS CLONE OF [dbo].[dimension_city] AT '2025-01-01T10:00:00.000';

 --Create a clone of the dbo.fact_sale table at a specific point in time.
CREATE TABLE [dbo].[fact_sale2] AS CLONE OF [dbo].[fact_sale] AT '2025-01-01T10:00:00.000';
```

![](./media/image33.png)

![](./media/image34.png)

7.  将查询重命名为 +++**Clone Tables+++**。

> ![](./media/image35.png)
>
> ![](./media/image36.png)

### 任务2：在同一仓库内跨模式克隆表

在这个任务中，学习如何在同一仓库内跨模式克隆一个表。

1.  要创建新查询，在**Home** 功能区选择 **New SQL query**。

> ![](./media/image37.png)

2.  在查询编辑器中，粘贴以下代码。代码创建一个模式，然后在新模式中创建
    **fact_sale**和**dimension_city**表的克隆。运行查询。

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

3.  执行完成后，预览 **dbo1**
    模式中加载到**dimension_city1**表中的data。

> ![](./media/image39.png)

4.  要创建*previous point in
    time*的表克隆，在查询编辑器中粘贴以下代码**替换现有语句**。代码在新模式的某些时间点创建**了dimension_city**表和**fact_sale**表的克隆。运行查询。

```
--Create a clone of the dbo.dimension_city table in the dbo1 schema.
CREATE TABLE [dbo1].[dimension_city2] AS CLONE OF [dbo].[dimension_city] AT '2025-01-01T10:00:00.000';

--Create a clone of the dbo.fact_sale table in the dbo1 schema.
CREATE TABLE [dbo1].[fact_sale2] AS CLONE OF [dbo].[fact_sale] AT '2025-01-01T10:00:00.000';
```
> ![](./media/image40.png)

5.  执行完成后，预览加载到**dbo1**模式中**fact_sale2**表中的data。

> ![](./media/image41.png)

6.  将查询重命名为 +++**Clone Tables Across Schemas**+++。

> ![](./media/image42.png)
>
> ![](./media/image43.png)

## 练习4：使用存储过程转换data

### 任务1：创建存储过程

在此任务中，学习如何创建存储过程以转换仓库表中的 data。

1.  在 **WideWorldImporters** 页面，进入**Home** 标签，从下拉菜单中选择
    **SQL**，然后点击“**New SQL query**”。

![](./media/image44.png)

2.  在查询编辑器中，粘贴以下代码。代码会丢弃存储过程（如果存在的话），然后创建一个名为
    **populate_aggregate_sale_by_city** 的存储过程。存储过程逻辑创建名为
    **aggregate_sale_by_date_city** 的表，并通过按组查询插入 data，连接
    **fact_sale** 和 **dimension_city** 表。

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

3.  要执行查询，在查询设计功能区选择**Run**

> ![](./media/image46.png)

4.  执行完成后，将查询重命名为 +++**Create Aggregate Procedure**+++。

> ![A screenshot of a computer Description automatically
> generated](./media/image47.png)
>
> ![](./media/image48.png)

5.  在**Explorer** 面板中，从**dbo**模式的**Stored
    Procedures** 文件夹中确认**aggregate_sale_by_date_city**存储过程是否存在。

![](./media/image49.png)

### 任务2：运行存储过程

在此任务中，学习如何执行存储过程以转换仓库表中的 data。

1.  在**WideWorldImporters** 页面，进入**Home** 标签，从下拉菜单中选择
    **SQL**，然后点击“**New SQL query**”。

> ![](./media/image50.png)

2.  在查询编辑器中，粘贴以下代码。该代码执行**populate_aggregate_sale_by_city**存储过程。运行查询。

```
--Execute the stored procedure to create and load aggregated data.
 EXEC [dbo].[populate_aggregate_sale_by_city];
```

![](./media/image51.png)

3.  执行完成后，将查询重命名为 +++**Run Aggregate Procedure+++**。

> ![](./media/image52.png)
>
> ![](./media/image53.png)

4.  要预览汇总数据，请在 **Explorer** 面板中选择
    **aggregate_sale_by_date_city** 表。

> ![](./media/image54.png)

** 注意：**如果表格未出现，选择 **Tables** 文件夹中的省略号（...），
然后选择**Refresh**。

##  练习5：在语句层面使用T-SQL进行时间旅行

### 任务1：处理时间旅行查询

在这个任务中，学习如何创建销售额排名前十的客户视图。你将在下一个任务中使用视图来运行时间旅行查询。

1.  在 **WideWorldImporters** 页面，进入**Home**标签，从下拉菜单中选择
    **SQL**，然后点击“**New SQL query**”。

![](./media/image55.png)

2.  在查询编辑器中，粘贴以下代码。代码创建了一个名为 Top10Customers
    的视图。该视图通过查询检索基于销售额的前10名客户。选择**Run **以执行查询。

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

3.  执行完成后，将查询重命名为 +++Create Top 10 Customer View+++。

![](./media/image57.png)

![](./media/image58.png)

3.  在**Explorer**中，通过在dbo
    **schema**下展开**View**节点，确认你能看到新创建的视图
    **Top10CustomersView**。

![](./media/image59.png)

4.  创建一个类似步骤1的新查询。在功能区的**Home** 标签中，选择 **New SQL
    query**。

> ![](./media/image60.png)

5.  在查询编辑器中，粘贴以下代码。该代码会更新单个事实行的
    **TotalIncludingTax**值，故意膨胀其总销售额。它还会检索当前时间戳。

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

6.  把返回的时间戳值复制到你的剪贴板上。

![](./media/image62.png)

**注意：** 目前，你只能使用 Coordinated Universal Time (UTC)
时区进行时间旅行.

7.  执行完成后，将查询重命名为 +++**Time Travel+++**。

![](./media/image63.png)

![](./media/image64.png)

8.  将以下代码粘贴到查询编辑器中，并将时间戳值替换为前一步获得的时间戳值。时间戳语法格式为
    **YYYY-MM-DDTHH:MM:SS\[.FFF\]。**

9.  去掉尾部的零，例如：**2026-07-27T06：20：55.823**。

&nbsp;

10. 要检索*now*排名前十的客户，请在新的查询编辑器中粘贴以下语句。该代码通过使用“FOR
    TIMESTAM AS OF”查询提示来获取前10名客户。

11. 用你复制到剪贴板的时间戳替换YOUR_TIMESTAMP。

```
--Retrieve the top 10 customers as of now.
 SELECT *
 FROM [dbo].[Top10Customers]
 OPTION (FOR TIMESTAMP AS OF 'YOUR_TIMESTAMP');
```

![](./media/image65.png)

12. 将查询重命名为 **+++Time Travel Now+++**

> ![](./media/image66.png)
>
> ![](./media/image67.png)

13. 注意，Tailspin Toys(Muir, MI)的**CustomerKey**排名第二大值是**49**。

> ![](./media/image68.png)

14. 通过从时间戳*中**subtracting one
    minute，***将时间戳值修改为更早的时间

15. 再次运行查询，注意到 **Wingtip Toys (Sarversville, PA)** 的
    **CustomerKey 前数值是 381。**

## 练习6：在Warehouse中使用可视化查询构建器创建查询

### 任务1：使用可视化查询构建器

在这个任务中，学习如何使用可视化查询构建器创建查询。

1.  在**Home** 功能区，打开 **New SQL query** 下拉列表，然后选择 **New
    visual query**。

![](./media/image69.png)

2.  从**Explorer** 面板，从dbo schema
    **Tables**文件夹，将**fact_sale**表拖 到可视化查询canvas。

![](./media/image70.png)

3.  点击“**Reduce rows**”下拉菜单，然后点击“**Keep top
    rows**”，如下图所示，导航到查询设计窗**transformations
    ribbon** 并限制dataset大小。

![](./media/image71.png)

4.  在“**Keep top rows**”对话框中，输入**+++10000+++**并选择**OK**。

![](./media/image72.png)

![](./media/image73.png)

5.  从**Explorer** 面板，从dbo schema
    **Tables**文件夹，将**dimension_city**表拖 到可视化查询canvas。

6.  右键点击**dimension_city**，选择 **Insert into canvas**

> ![](./media/image74.png)

![](./media/image75.png)

6.  在变换功能区中，选择“**Combine**”旁边的下拉菜单
    ，并如下图所示选择**“Merge queries as new”**。

![](./media/image76.png)

7.  在**Merge **设置页面输入以下信息。

- 在**Left table for merge**下拉菜单中，选择**dimension_city**

-  在**Right table for
  merge** 中，选择**fact_sale**（使用横向和纵向滚动条）

-  在**dimension_city**表中选择头部列名以表示连接列，选择**CityKey**字段。

-  在**fact_sale**表中选择 **CityKey** 字段
  ，方法是在头部行中选择列名，表示连接列。

-  在“**Join kind**”选择中，选择**“Inner**”并点击**“Ok**”按钮。

![](./media/image77.png)

![](./media/image78.png)

8.  选中**Merge** 步骤后，如下图所示，选择
    data网格头部**fact_sale**旁的**“Expand**”按钮，然后选择**TaxAmount, Profit,
    TotalIncludingTax** 列，选择**Ok。**

![](./media/image79.png)

![](./media/image80.png)

![](./media/image81.png)

9.  在**transformations
    ribbon，**点击“**Transform**”旁边的下拉菜单，然后选择**“Group
    by”。**

![](./media/image82.png)

10. 在“**Group by**”页面输入以下信息。

- 选择**Advanced **单选按钮。

- 在**“Group by”**下选择以下内容:

  1.  **Country**

  2.  **StateProvince**

  3.  **City**

- 在**New column name**中，在**Operation** 栏字段
  输入**SumOfTaxAmount**，选择**Sum**，然后在**Column** 字段下选择**TaxAmount。**
  点击**“Add aggregation**”以添加更多汇总列和操作。

- 在**New column name**中**，**在**Operation** 栏字段
  输入**SumOfProfit**，选择**SumOfProfit**，然后在**Column** 字段下选择**Profit**。点击**“Add
  aggregation**”以添加更多汇总列和操作。

- 在**New column name**中，在**Operation** 栏字段输入
  **SumOfTotalIncludingTax**，选择 **Sum**，然后在**列**字段下选
  **TotalIncludingTax。**

- 点击**OK**按钮

![](./media/image83.png)

![](./media/image84.png)

11. 在资源管理器中，进入**Queries**，右键点击查询中的 **Visual query
    1** 。然后，选择**Rename**。

![](./media/image85.png)

12. 输入 +++**Sales Summary+++** 以更改查询名称。按
    键盘**Enter** 键或选择标签页外的任意位置保存更改。

![](./media/image86.png)

13. 点击**Home**标签下方的**Refresh** 图标。

![A screenshot of a computer Description automatically
generated](./media/image87.png)

## 练习7：用notebook分析data

### 任务1：创建T-SQL notebook

在这个任务中，学习如何创建T-SQL notebook。

1.  在**Home** 功能区，打开 **New SQL query** 下拉列表，然后选择
    notebook中的 **New SQL query** 

> ![](./media/image88.png)

2.  在**Explorer** 面板中，选择**Warehouses** 以显示**Wide World
    Importers** 仓库的物品。

3.  要生成用于探索
    data的SQL模板，在**dimension_city**表右侧，选择**省略号（...），**然后选择**“SELECT
    TOP 100**”。

> ![](./media/image89.png)

4.  要在该单元格中运行 T-SQL 代码，选择该代码单元格的“**Run
    cell**”按钮。

> ![](./media/image90.png)

5.  查看结果面板中的查询结果。

> ![](./media/image91.png)

### 任务2：创建一个lakehouse快捷方式，并用notebook分析data

在这个任务中，学习如何创建lakehouse捷径并用notebook分析 data。

1.  在左侧菜单中，选择 **Warehouse_Fabric65897@lab.labinstance.id**
    工作区图标，然后选择工作区名称。

> ![](./media/image92.png)

2.  选择** + New Item **以显示所有可用商品类型的完整列表。

3.  在列表中，在**“Store data**”部分，选择**Lakehouse**项目类型。

> ![](./media/image93.png)

4.  配置完成后，lakehouse将
    +++**Shortcut_Exercise**+++作为lakehouse名称，并取消选择
    lakehouse的模式。选择**Create**。
     ![](./media/image94.png)

> ![](./media/image95.png)

5.  当新 lakehouse打开后，在登陆页面选择**“New shortcut**”选项。

> ![](./media/image96.png)

6.  在“** New shortcut**”窗口中，选择 **Microsoft OneLake** 选项。

> ![](./media/image97.png)

7.  在“**Select a data source type**”窗口中，选择“**Wide World
    Importers** warehouse”，然后选择**“Next**”。

> ![](./media/image98.png)

8.  点击连接

> ![](./media/image99.png)

9.  在 **OneLake object**浏览器中，展开**Tables**，展开 **dbo**
    模式，然后选择 **dimension_customer** 表的复选框。选择**Next**。

> ![](./media/image100.png)

10. 选择**Create**。

> ![](./media/image101.png)

11. 在**Explorer** 面板中，选择**dimension_customer**表预览data，然后查看从仓库dimension_customer表检索到的
    data。

> ![](./media/image102.png)

12. 在 **dimension_customer** 表格页面，点击 **“Analyze data
    with**”，选择 **“Notebook**”，然后选择 **“New notebook** ”创建新的
    Spark notebook 进行 data 分析

> ![](./media/image103.png)

13. 在**Explorer** 面板中，选择**Lakehouses**。

14. 把**dimension_customer**桌拖到打开的notebook单元。

> ![](./media/image104.png)

15. 注意笔记本单元格中添加了**PySpark**查询。该查询获取
    **Shortcut_Exercise.dimension_customer** 快捷方式中的前 **1,000 行**
    。这种笔记本体验类似于Visual Studio Code Jupyter
    notebook体验。你也可以用VS Code打开notebook。

> ![](./media/image105.png)

16. 在**Home**功能区，选择**“Run all**”按钮。

> ![](./media/image106.png)
>
> ![](./media/image107.png)

## 练习8：使用SQL查询编辑器创建跨仓库查询

### 任务1：向Explorer添加多个仓库

在本任务中，学习如何轻松地使用SQL查询编辑器在多个仓库中创建和执行T-SQL查询，包括将Microsoft
Fabric中的SQL Endpoint和仓库的 data合并在一起。

1.  从 **Notebook2** 页面，点击左侧导航菜单中的 **WideWorldImporters**
    工作区。

> ![](./media/image108.png)

2.  在**Explorer** 面板中，选择 **+ Warehouses**。

![](./media/image109.png)

3.  在 **OneLake 目录**窗口中，选择 **Shortcut_Exercise** SQL 分析
    endpoint。选择**Confirm**。

![](./media/image110.png)

4.  在**Explorer** 面板中，注意**Shortcut_Exercise**
    SQL分析endpoint可用。

![](./media/image111.png)

### 任务2：运行跨仓库查询

在这个任务中，学习如何运行跨仓库查询。具体来说，你将运行一个查询，将
Wide World Importers 仓库连接到 Shortcut_Exercise SQL 分析endpoint。

** 注意：**跨
database查询使用*database.schema.table*的三部分命名来引用对象。

1.  在功能区的**Home** 标签中，选择 **New SQL query**。

![](./media/image112.png)

2.  在查询编辑器中，粘贴以下代码。该代码检索了按库存商品、描述和客户销售数量的总量。

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
3.  **运行** 查询，并查看查询结果。

![](./media/image113.png)

![](./media/image114.png)

3.  将查询重命名以便参考。在**Explorer** 中右键点击**SQL
    query**，选择**“Rename**”。

> ![](./media/image115.png)

![](./media/image116.png)

4.  在“**Rename**”对话框中，在**“Name**”字段下输入 +++**Cross-warehouse
    query+++**，然后点击**Rename**按钮。

> ![](./media/image117.png)

## 练习9：创建Direct Lake semantic模型和Power BI报告

### 任务1：创建一个semantic模型

在此任务中，学习如何基于Wide World Importers仓库创建Direct Lake
semantic模型。

1.  在 **WideWorldImportes** 页面的 **Home**标签下，选择**New semantic
    model**。

![](./media/image118.png)

2.  在**New semantic model** 窗口中，在 **Direct Lake semantic model
    name**框中输入 +++**Sales Model+++**

3.  展开dbo模式，打开**Tables**文件夹，然后检查**dimension_city**和**fact_sale**表。选择**Confirm**。

> ![](./media/image119.png)

9.  从左侧导航选择***Warehouse_FabricXXXXX***，如下图所示

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image120.png)

10. 要打开semantic模型，返回工作区着陆页，然后选择 **Sales
    Model **semantic模型。

![](./media/image121.png)

![](./media/image122.png)

12. 在**Sales Model** 页面，要编辑**“Manage
    Relationships”**，请将模式从**“Viewing**”改为**“Editing”**![A
    screenshot of a computer AI-generated content may be
    incorrect.](./media/image123.png)

13. 要创建关系，在模型设计器中，在 **Home**功能区选择**“Manage
    relationships**”。

![](./media/image124.png)

14. 在**Manage relationship** 窗口中，选择 **+ New relationship**。

![](./media/image125.png)

14. 在**“New relationship window”**窗口中，完成以下步骤创建关系：

-  在“**From table”**下拉列表中，选择**dimension_city**表。

- 在**“To 表**”下拉列表中，选择**fact_sale**表。

- 在**Cardinality** 下拉列表中，选择 **One to many (1:\*)。**

- 在**Cross-filter direction** 下拉菜单中，选择**Single**。

- 勾选**“Assume referential integrity**”框。

- 选择**Save**。

![](./media/image126.png)

![](./media/image127.png)

15. 在**Manage relationship** 窗口中，选择**Close**。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image128.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image129.png)

### 任务2：创建Power BI报告

在这个任务中，学习如何基于你在任务中创建的语义模型创建Power BI报告。

1.  在**File**功能区，选择**Create new report**。

![](./media/image130.png)

2.  在报表设计器中，完成以下步骤以创建柱状图可视化：

-  在**Data** 面板中，展开**fact_sale**表，然后勾选Profit 字段。

- 在**Data**面板中，展开dimension_city表，然后勾选SalesTerritory字段。

![](./media/image131.png)

3.  在**Visualizations**面板中，选择 **Azure Map** 可视化。

![](./media/image132.png)

4.  在“**Data**”窗格中，从 dimension_city 表中，将 StateProvince
    字段拖到“**Visualizations**”窗格中的“**Location**”区域。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image133.png)

5.  在“**Data**”窗格中，从 fact_sale
    表中选中“Profit”字段，将其添加到地图可视化“**Size**”区域。

6.  在**Visualizations **面板中，选择**Table **可视化。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image134.png)

7.  在**Data**面板中，勾选以下字段：

-  dimension_city表中的SalesTerritory

- dimension_city表中的StateProvince

- fact_sale表的Profit 

- 从fact_sale表中的TotalExcludingTax

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image135.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image136.png)

8.  请核实报告页面的完成设计是否与以下图片相似。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image137.png)

9.  要保存报告，在**Home** 功能区选择**“File** \> **Save**”。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image138.png)

10. 在“Save your report”窗口，在“Enter a name for your
    report”框中，输入+++**Sales Analysis**+++，然后选择**Save**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image139.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image140.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image141.png)

### 任务3：清理资源

你可以删除单个报表、pipelines、仓库和其他项目，或者删除整个工作区。在这个教程中，你将清理工作区、单个报告、pipelines、仓库以及你作为实验室一部分创建的其他项目。

1.  在导航菜单中选择**Warehouse_FabricXX**返回工作区的项目列表。

![](./media/image142.png)

2.  在工作区头的菜单中，选择**Workspace settings**。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image143.png)

3.  在工作**Workspace
    settings** 对话框中，选择“**General**”，然后选择**“Remove this
    workspace**”。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image144.png)

4.  在 **Delete workspace?** 对话框，点击**Delete** 按钮。
    ![](./media/image145.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image146.png)

**摘要**

这个综合实验室介绍了一系列旨在在 Microsoft Fabric 中建立功能性
data环境的任务。它从创建一个工作区开始，这对
data操作至关重要，并确保试验的启用。 随后，在 Fabric 环境中建立了名为
WideWorldImporters 的仓库，作为
data存储和处理的中央仓库。随后，通过实现Data Factory
pipeline，详细说明了Warehouse_FabricXX工作区中的data ingestion过程。
该过程涉及从外部来源获取
data并将其无缝集成到工作区中。关键表、关键表、dimension_city和fact_sale在
data仓库中被创建，作为
data分析的基础结构。Data加载过程继续使用T-SQL进行，将Azure
Blob存储中的data传输到指定的表中。 后续任务涉及
data管理和操作领域。演示了克隆表，为
data复制和测试提供了宝贵的技术。此外，克隆过程被扩展到同一仓库内的不同模式（dbo1），展示了结构化的
data组织方法。实验室推进到 data转换，引入了存储过程以高效聚合销售
data。随后转为可视化查询构建，为复杂 data查询提供直观的界面。
接着是对笔记本的探索，展示了它们在查询和分析dimension_customer表
data方面的实用性。随后，展示了多仓库查询功能，使工作空间内不同仓库之间能够无缝检索
data。实验室最终实现了Azure地图可视化集成，增强了Power BI中的地理
data表示。随后，创建了一系列Power
BI报告，包括柱状图、地图和表格，以促进深入的销售
data分析。最后一项任务是从OneLake数据中心生成报告，进一步强调Fabric中
data源的多样性。最后，实验室还提供了资源管理的见解，强调清理程序对于保持高效工作环境的重要性。这些任务综合起来，提供了对在
Microsoft Fabric 中设置、管理和分析 data的全面理解。
