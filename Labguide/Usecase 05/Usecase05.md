**介紹**

Contoso是一家跨國零售公司，正尋求現代化其數據基礎設施，以提升銷售和地理分析能力。目前，他們的銷售和客戶數據分散在多個系統中，使業務分析師和公民開發者難以獲得洞察。公司計劃將這些數據整合到一個統一平臺，利用
Microsoft Fabric 實現交叉查詢、銷售分析和地理報告。

在本實驗室中，你將扮演Contoso的數據工程師角色，負責設計和實施使用Microsoft
Fabric的數據倉庫解決方案。您將首先搭建 Fabric 工作區，創建數據倉庫，從
Azure Blob Storage 加載數據，並執行分析任務，向 Contoso
的決策者提供洞察。

雖然Microsoft
Fabric中的許多概念對數據和分析專業人士來說可能很熟悉，但在新環境中應用這些概念可能具有挑戰性。本實驗室旨在逐步帶領從數據採集到數據消耗的端到端場景，建立對
Microsoft Fabric 用戶體驗、各種體驗及其集成點，以及 Microsoft Fabric
專業和公民開發者體驗的基本理解。

**目標**

- 搭建一個啟用試用版的Fabric工作區。

- 在 Microsoft Fabric 中建立一個名為 WideWorldImporters 的新倉庫。

- 通過數據工廠流水線將數據加載到Warehouse_FabricXX工作區。

- 在數據倉庫中生成dimension_city和fact_sale表。

- 用Azure Blob Storage的數據填充dimension_city和fact_sale表。

- 在倉庫裡創建dimension_city和fact_sale的桌子克隆。

- 將 Tables dimension_city 和 Tables fact_sale 克隆到 dbo1 架構中。

- 開發一個存儲過程來轉換數據並創建aggregate_sale_by_date_city表。

- 使用可視化查詢構建器生成查詢，以合併和聚合數據。

- 使用筆記本查詢和分析dimension_customer表中的數據。

- 包含WideWorldImporters和ShortcutExercise倉庫以便交叉查詢。

- 在 WideWorldImporters 和 ShortcutExercise 倉庫之間執行 T-SQL 查詢。

- 在管理門戶中啟用 Azure Maps 可視化集成。

- 生成銷售分析報告的柱狀圖、地圖和表格可視化。

- 利用OneLake數據中心中的WideWorldImporters數據集中的數據創建報告。

- 移除工作區及其相關項目。

# **練習一： 創建 Microsoft Fabric 工作區**

## **任務1：登錄Power BI賬戶並註冊免費[Microsoft Fabric試用版](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial)**

1.  打開瀏覽器，進入地址欄，輸入或粘貼以下URL：+++https://app.fabric.microsoft.com/+++，
    然後按下 **Enter** 鍵。

> ![](./media/image1.png)

2.  在 **Microsoft Fabric** 窗口中，輸入已分配的憑證，然後點擊
    **Submit** 按鈕。

> ![](./media/image2.png)

3.  然後，在 **Microsoft** 窗口輸入密碼，點擊 **Sign in** 按鈕**。**

> ![](./media/image3.png)

4.  在 **Stay signed in?** 窗口，點擊“**Yes**”按鈕。

> ![](./media/image4.png)

5.  你將被引導到Power BI主頁。

> ![](./media/image5.png)

## 任務2：創建一個工作區

在處理Fabric數據之前，先創建一個啟用Fabric試用區的工作區。

1.  在工作區面板中選擇 **+** **New workspace**。

> ![A screenshot of a computer Description automatically
> generated](./media/image6.png)

2.  在“**Create a
    workspace**”**選項卡**中，**輸入**以下詳細信息，然後單擊“**Apply**”按鈕。

[TABLE]

> ![](./media/image7.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

3.  等待部署完成。完成大約需要1-2分鐘。
    當你的新工作區開放時，應該是空的。

> ![](./media/image9.png)

## 任務3：在 Microsoft Fabric 中創建倉庫

1.  在 **Fabric** 頁面，選擇  **+ New item**  創建
    **lakehouse**，然後選擇 **Warehouse**

> ![A screenshot of a computer Description automatically
> generated](./media/image10.png)

2.  在“**New warehouse** ”對話框中，輸入
    +++**WideWorldImporters+++** 並點擊“**Create**”按鈕。

> ![](./media/image11.png)

3.  配置完成後，會出現 **WideWorldImporters** 倉庫的登陸頁面。

> ![](./media/image12.png)

# **練習2：在Microsoft Fabric中將數據導入倉庫**

## 任務1：將數據導入倉庫

1.  在**WideWorldImporters**倉庫的落地頁面，選擇左側導航菜單中的
    **Warehouse_FabricXX**返回工作區物品列表。

> ![](./media/image13.png)

2.  在 **Warehouse_FabricXX** 頁面，選擇 +**New item**。然後，點擊
    **“Pipeline**”，在“獲取數據”下查看所有可用項目的完整列表。

> ![](./media/image14.png)

3.  在“**New** **pipeline**”對話框的 **Name** 字段中，輸入 +++**Load
    Customer Data+++**並點擊**Create**按鈕。

> ![](./media/image15.png)

4.  在“**Load Customer Data**”頁面中，導航至“**Start building your data
    pipeline** ”部分，然後單擊“**Pipeline activity**”。

> ![](./media/image16.png)

5.  在“**Move &** **transform**”部分下，導航並選擇“**Copy data**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image17.png)

6.  從設計畫布中選擇新創建的**“Copy data 1**”活動進行配置。

> **注意**：在設計畫布中拖動橫線，可以完整查看各種特徵。
>
> ![A screenshot of a computer Description automatically
> generated](./media/image18.png)

7.  在“**General**”選項卡上的“**Name**”字段中，輸入+++**CD Load
    dimension_customer+++**

> ![A screenshot of a computer Description automatically
> generated](./media/image19.png)

8.  在“**Source**”頁面上，選擇“**Connection**”下拉菜單。選擇“**Browse
    all**”以查看所有可供選擇的數據源。

> ![](./media/image20.png)

9.  在“**Get data**”窗口中，搜索 +++**Azure Blobs+++**，然後點擊 **Azure
    Blob Storage** 按鈕。 

> ![](./media/image21.png)

10. 在右側出現的“**Connection
    settings**”窗格中，配置以下設置，然後單擊“**Connect**”按鈕。

- 在 **Account name or URL**中輸入
  +++**https://fabrictutorialdata.blob.core.windows.net/sampledata/+++**

- 在**Connection
  credentials**部分，點擊**Connection**下的下拉菜單，然後選擇**Create
  new connection**。 

- 在 **Connection name** 字段中輸入 +++**Wide World Importers Public
  Sample+++**.

- 將**Authentication kind**設置為**Anonymous**。

> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

11. 在複製活動的 Source 頁上更改剩餘設置如下，以訪問
    **https：//fabrictutorialdata.blob.core.windows.net/sampledata/WideWorldImportersDW/parquet/full/dimension_customer/\*.parquet**
    中的 .parquet 文件

12. 在 **File path** 文本框中，提供:

- **容器:** +++**sampledata+++**

- **文件路徑 - 目錄:** +++**WideWorldImportersDW/tables+++**

- **文件路徑- 文件名:** +++**dimension_customer.parquet+++**

- 在“**File
  format**”下拉菜單中，選擇**Parquet**（如果看不到**Parquet**，請在搜索框中輸入並選擇它）

> ![](./media/image23.png)

13. 點擊 **File path** 設置右側的“**Preview
    data**”，確保沒有錯誤，然後點擊“**close**”。 

> ![](./media/image24.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

14. 在**“Destination**”標簽頁，輸入以下設置。

[TABLE]

> **注意：在將連接添加到WideWorldImporters倉庫時，請通過導航從OneLake目錄中添加，以便瀏覽所有選項。**
>
> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image27.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image28.png)

15. 從色帶中選擇**“Run**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image29.png)

16. 在 **Save and run?** 對話框，點擊“**Save and run**”按鈕。

> ![](./media/image30.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image31.png)

17. 在輸**Output**面監控複製活動的進度 ，等待它完成。

> ![A screenshot of a computer Description automatically
> generated](./media/image32.png)

# 練習3：在數據倉庫中創建表格

## 任務1：在數據倉庫中創建表格

1.  在 **Load Customer Data**
    頁面，點擊左側導航欄**Warehouse_FabricXX**工作區，選擇**WideWorldImporters**
    Warehouse。

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

2.  在 **WideWorldImporters** 頁面，進入**Home**頁標簽，從下拉菜單選擇
    **SQL**，然後點擊“**New SQL query**”。 

> ![A screenshot of a computer Description automatically
> generated](./media/image34.png)

3.  在查詢編輯器中，粘貼以下代碼，選擇 **Run **以執行查詢

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
>
> ![A screenshot of a computer Description automatically
> generated](./media/image35.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image36.png)

4.  要保存此查詢，請右鍵單擊編輯器上方的 **SQL query
    1**選項卡，然後選擇“**Rename**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image37.png)

5.  在“**Rename**”對話框中，在 **Name** 字段中輸入 +++**Create
    Tables+++**以更改 **SQL query
    1**的名稱。然後，點擊“**Rename**”按鈕。

> ![](./media/image38.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image39.png)

6.  通過選擇功能區上的**刷新圖標**按鈕驗證表已成功創建。

> ![A screenshot of a computer Description automatically
> generated](./media/image40.png)

7.  在**Explorer**面板中，你會看到**fact_sale**表和**dimension_city**表。

> ![A screenshot of a computer Description automatically
> generated](./media/image41.png)

## 任務2：使用T-SQL加載數據

既然你已經知道如何構建數據倉庫、加載表和生成報告，接下來是時候通過探索其他加載數據的方法來擴展解決方案了。

1.  在 **WideWorldImporters** 頁面，進入**主**頁標簽，從下拉菜單中選擇
    **SQL**，然後點擊“**New SQL query**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image42.png)

2.  在查詢編輯器中， **粘貼** 以下代碼，然後點擊 **Run **以執行查詢。

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
>
> WITH (FILE_TYPE = 'PARQUET');
>
> ![A screenshot of a computer Description automatically
> generated](./media/image43.png)

3.  查詢完成後，查看消息，顯示**dimension_city**表中**fact_sale**行的數量。

> ![A screenshot of a computer Description automatically
> generated](./media/image44.png)

4.  在 **Explorer**
    中的**fact_sale**表中選擇，加載數據預覽以驗證已加載的數據。

> ![](./media/image45.png)

5.  重新命名查詢。在 **Explorer** 中右鍵點擊**SQL query
    1** ，然後選擇“**Rename**”。

> ![](./media/image46.png)

6.  在“**Rename**”對話框中，在 **Name** 字段下輸入 +++**Load
    Tables+++**。然後，點擊“**Rename**”按鈕。

> ![A screenshot of a computer Description automatically
> generated](./media/image47.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image48.png)

7.  點擊**主**頁標簽下方命令欄中的**刷新**圖標。

> ![A screenshot of a computer Description automatically
> generated](./media/image49.png)

# 練習4：在Microsoft Fabric中使用T-SQL克隆表

## 任務1：在倉庫中創建同一模式內的表克隆

這個任務會引導你在 Microsoft Fabric 的 Warehouse 中創建 [table
clone](https://learn.microsoft.com/en-in/fabric/data-warehouse/clone-table)，使用“[CREATE
TABLE AS CLONE
OF](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-table-as-clone-of-transact-sql?view=fabric&preserve-view=true)”語法。 

1.  在倉庫中創建同一模式內的表克隆。

2.  在 **WideWorldImporters** 頁面，進入**主**頁標簽，從下拉菜單中選擇
    **SQL**，然後點擊“**New SQL query**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image50.png)

3.  在查詢編輯器中，粘貼以下代碼創建**dbo.dimension_city**和**dbo.fact_sale**表的克隆。

> --Create a clone of the dbo.dimension_city table.
>
> CREATE TABLE \[dbo\].\[dimension_city1\] AS CLONE OF
> \[dbo\].\[dimension_city\];
>
> --Create a clone of the dbo.fact_sale table.
>
> CREATE TABLE \[dbo\].\[fact_sale1\] AS CLONE OF \[dbo\].\[fact_sale\];
>
> ![A screenshot of a computer Description automatically
> generated](./media/image51.png)

4.  選擇**運行**以執行查詢。查詢執行需要幾秒鐘。查詢完成後，會創建表克隆
    的 **dimension_city1** 和 **fact_sale1** 。

> ![A screenshot of a computer Description automatically
> generated](./media/image52.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image53.png)

5.  在 **Explorer**
    中的**dimension_city1**表中選擇，加載數據預覽以驗證已加載的數據。

> ![A screenshot of a computer Description automatically
> generated](./media/image54.png)

6.  右鍵點擊你創建的 **SQL query**，在 **Explorer** 中克隆表，然後選擇
    **Rename**。

> ![A screenshot of a computer Description automatically
> generated](./media/image55.png)

7.  在“**Rename**”對話框中，在“**Name**”字段下輸入 +++**Clone
    Table+++**，然後點擊“**Rename**”按鈕。

> ![A screenshot of a computer Description automatically
> generated](./media/image56.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image57.png)

8.  點擊**主**頁標簽下方命令欄中的**刷新**圖標。

> ![A screenshot of a computer Description automatically
> generated](./media/image58.png)

## 任務2：在同一倉庫內創建跨模式的表克隆

1.  在 **WideWorldImporters** 頁面，進入**主**頁標簽，從下拉菜單中選擇
    **SQL**，然後點擊“**New SQL query**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image59.png)

2.  在 **WideWorldImporter** 倉庫中創建一個名為 **dbo1**
    的新模式。**複製粘貼**並**運行**如下 T-SQL 代碼，如下圖所示:

> CREATE SCHEMA dbo1;
>
> ![A screenshot of a computer Description automatically
> generated](./media/image60.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

3.  在查詢編輯器中，刪除現有代碼，粘貼以下內容以創建 **dbo1** 模式中
    **dbo.dimension_city** 和 dbo.**fact_sale tables** 的克隆。

> --Create a clone of the dbo.dimension_city table in the dbo1 schema.
>
> CREATE TABLE \[dbo1\].\[dimension_city1\] AS CLONE OF
> \[dbo\].\[dimension_city\];
>
> --Create a clone of the dbo.fact_sale table in the dbo1 schema.
>
> CREATE TABLE \[dbo1\].\[fact_sale1\] AS CLONE OF
> \[dbo\].\[fact_sale\];

4.  選擇 **Run** 以執行查詢。查詢執行需要幾秒鐘。

> ![A screenshot of a computer Description automatically
> generated](./media/image62.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image63.png)

5.  查詢完成後，**dbo1** 模式中會創建克隆 **dimension_city1** 和
    **fact_sale1**。

> ![A screenshot of a computer Description automatically
> generated](./media/image64.png)

6.  在 **Explorer** 的 **dbo1** 模式下，在 **dimension_city1**
    表中選擇，加載數據預覽以驗證已加載的數據。

> ![A screenshot of a computer Description automatically
> generated](./media/image65.png)

7.  **重命名**查詢語句以便後續引用。在 **Explorer** 中右鍵單擊 **SQL
    query 1**，然後選擇“**Rename**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image66.png)

8.  在“**Rename**”對話框的“**Name**”字段下，輸入+++**Clone Table in
    another schema+++**。然後，單擊“**Rename**”按鈕。

> ![](./media/image67.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image68.png)

9.  點擊**主**頁標簽下方命令欄中的**刷新**圖標。

> ![A screenshot of a computer Description automatically
> generated](./media/image69.png)

# **練習5：使用存儲過程轉換數據**

學習如何創建和保存新的存儲過程以轉換數據。

1.  在 **WideWorldImporters** 頁面，進入**主**頁標簽，從下拉菜單中選擇
    **SQL**，然後點擊“**New SQL query**”。![A screenshot of a computer
    Description automatically generated](./media/image70.png)

2.  在查詢編輯器中，**粘貼**以下代碼以創建存儲過程**dbo.populate_aggregate_sale_by_city**。該存儲過程將在後續步驟創建並加載**dbo.aggregate_sale_by_date_city**表。

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

3.  右鍵點擊你創建的SQL查詢，在資源管理器中克隆表，然後選擇 **Rename**。

> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)

4.  在“**Rename**”對話框中，在**Name**字段下方輸入 +++**Create Aggregate
    Procedure+++**，然後點擊**Rename**按鈕。

> ![A screenshot of a computer screen Description automatically
> generated](./media/image74.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image75.png)

5.  點擊**主**頁標簽下方的**刷新圖標**。

> ![A screenshot of a computer Description automatically
> generated](./media/image76.png)

6.  在 **Explorer** 標簽頁中，通過在**dbo**
    schema下展開**存儲過程**節點，確認你能看到新創建的存儲過程。

> ![A screenshot of a computer Description automatically
> generated](./media/image77.png)

7.  在 **WideWorldImporters** 頁面，進入**主**頁標簽，從下拉菜單中選擇
    **SQL**，然後點擊“**New SQL query**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image78.png)

8.  在查詢編輯器中，粘貼以下代碼。該 T-SQL 執行
    **dbo.populate_aggregate_sale_by_city** 以創建
    **dbo.aggregate_sale_by_date_city** 表。運行查詢。

> --Execute the stored procedure to create the aggregate table.
>
> EXEC \[dbo\].\[populate_aggregate_sale_by_city\];
>
> ![A screenshot of a computer Description automatically
> generated](./media/image79.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image80.png)

9.  要保存此查詢以備後續參考，請右鍵點擊編輯器上方的查詢標簽，選擇
    **Rename。**

![A screenshot of a computer Description automatically
generated](./media/image81.png)

10. 在**Rename**對話框中，在**Name**字段下方輸入 +++**Run** **Create
    Aggregate Procedure+++**，然後點擊**Rename**按鈕。

![](./media/image82.png)

![A screenshot of a computer Description automatically
generated](./media/image83.png)

11. 選擇 功能區上的**刷新**圖標。

![A screenshot of a computer Description automatically
generated](./media/image84.png)

12. 在 **Object Explorer**
    標簽頁中，加載數據預覽以驗證已加載的數據，方法是在 **Explorer**
    的**aggregate_sale_by_city**表中選擇。

![A screenshot of a computer Description automatically
generated](./media/image85.png)

# 練習6：在語句層面使用T-SQL進行時間旅行

1.  在 **WideWorldImporters** 頁面，進入**主**頁標簽，從下拉菜單中選擇
    **SQL**，然後點擊“**New SQL query**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image86.png)

2.  在查詢編輯器中，粘貼以下代碼創建視圖 Top10CustomerView。選擇
    **Run** 以執行查詢。

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

3.  在 **Explorer** 中，通過在**dbo
    schema**下展開**View**節點**，**確認你能看到新創建的視圖**Top10CustomersView**。

![](./media/image89.png)

4.  要保存此查詢以備後續參考，請右鍵點擊編輯器上方的查詢標簽，選擇
    **Rename。**

![A screenshot of a computer Description automatically
generated](./media/image90.png)

5.  在“**Rename**”對話框中，在“**Name**”字段下輸入
    +++**Top10CustomersView+++**，然後點擊“**Rename**”按鈕。

![](./media/image91.png)

6.  創建一個類似步驟1的新查詢。在 功能區的**主**頁標簽中，選擇 **New SQL
    query**。

![A screenshot of a computer Description automatically
generated](./media/image92.png)

7.  在查詢編輯器中，粘貼以下代碼。這將**TotalIncludingTax**列值更新為**20000000000**，適用於**SaleKey**
    值為**22632918**的記錄**。** 選擇 **Run** 以執行查詢。

/\*Update the TotalIncludingTax value of the record with SaleKey value
of 22632918\*/

UPDATE \[dbo\].\[fact_sale\]

SET TotalIncludingTax = 200000000

WHERE SaleKey = 22632918;

![A screenshot of a computer Description automatically
generated](./media/image93.png)

![A screenshot of a computer Description automatically
generated](./media/image94.png)

8.  在查詢編輯器中，粘貼以下代碼。CURRENT_TIMESTAMP T-SQL 函數返回當前
    UTC 時間戳為**datetime**。選擇**Run**以執行查詢。

SELECT CURRENT_TIMESTAMP;

![](./media/image95.png)

9.  把返回的時間戳值複製到你的剪貼板上。

![A screenshot of a computer Description automatically
generated](./media/image96.png)

10. 將以下代碼粘貼到查詢編輯器中，並將時間戳值替換為前一步獲得的時間戳值。時間戳語法格式為
    **YYYY-MM-DDTHH：MM：SS\[。**真是太棒了。

11. 例如，去除尾部的零: **2025-06-09T06:16:08.807**。

12. 以下示例返回了按**TotalIncludingTax**排名前十的客戶列表，包括**SaleKey
    22632918**的新值。替換現有代碼，粘貼以下代碼，選擇**Run**以執行查詢。

/\*View of Top10 Customers as of today after record updates\*/

SELECT \*

FROM \[WideWorldImporters\].\[dbo\].\[Top10CustomersView\]

OPTION (FOR TIMESTAMP AS OF '2025-06-09T06:16:08.807');

![A screenshot of a computer Description automatically
generated](./media/image97.png)

13. 將以下代碼粘貼到查詢編輯器中，並將時間戳值替換為執行更新腳本以更新**TotalIncludingTax**值之前的時間。這將返回
    在**TotalIncludingTax**更新**SaleKey**
    **22632918**前的十大客戶名單。選擇**Run**以執行查詢。

/\*View of Top10 Customers as of today before record updates\*/

SELECT \*

FROM \[WideWorldImporters\].\[dbo\].\[Top10CustomersView\]

OPTION (FOR TIMESTAMP AS OF '2024-04-24T20:49:06.097');

![A screenshot of a computer Description automatically
generated](./media/image98.png)

# 練習7：使用可視化查詢構建器創建查詢

## 任務1：使用可視化查詢構建器

在 Microsoft Fabric 門戶中使用可視化查詢構建器創建並保存查詢。

1.  在 **WideWolrdImporters** 頁面，從 功能區的**主**頁選項卡中，選擇
    **New visual query。**

> ![A screenshot of a computer Description automatically
> generated](./media/image99.png)

2.  右鍵點擊 **fact_sale** ，選擇 **Insert into canvas**

> ![A screenshot of a computer Description automatically
> generated](./media/image100.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image101.png)

3.  導航至查詢設計窗格 **transformations ribbon**，單擊“**Reduce
    rows**”下拉列表限制數據集大小，然後單擊“**Keep top
    rows** ”，如下圖所示。

> ![A screenshot of a computer Description automatically
> generated](./media/image102.png)

4.  在“**Keep top rows** ”對話框中，輸入**10000**並選擇 **OK**。

> ![](./media/image103.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image104.png)

5.  右鍵點擊 **dimension_city**，選擇 **Insert into canvas**

> ![A screenshot of a computer Description automatically
> generated](./media/image105.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image106.png)

6.  在變換功能區中，選擇“**Combine**”旁邊的下拉菜單，並如下圖所示選擇“**Merge
    queries as new**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image107.png)

7.  在 **Merge **設置頁面輸入以下信息。

- 在**左側表格中的合併下**拉菜單中，選擇**dimension_city**

&nbsp;

- 在**右側合併下**拉菜單中，選擇**fact_sale** （使用橫向和縱向滾動條）

&nbsp;

- 在**dimension_city**表中選擇 **CityKey**
  字段，方法是在頭部行的列名中選擇 CityKey 字段，以表示連接列。

&nbsp;

- 在**fact_sale**表中選擇 **CityKey** 字段，方法是在頭部行的列名中選擇
  **CityKey** 字段，以表示連接列。

&nbsp;

- 在 **Join kind** 圖選擇中，選擇**“ Inner**”並點擊**“Ok**”按鈕。

> ![A screenshot of a computer Description automatically
> generated](./media/image108.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image109.png)

8.  選擇 **Merge**
    步驟後，如下圖所示，選擇數據網格頭部**fact_sale**旁的“**Expand**”按鈕，然後選擇“**TaxAmount, Profit,
    TotalIncludingTax**”列，選擇 **Ok**。 

> ![A screenshot of a computer Description automatically
> generated](./media/image110.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image111.png)

9.  在**transformations
    ribbon**，點擊“**Transform**”旁邊的下拉菜單，然後選擇“**Group
    by**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image112.png)

10. 在“**Group by** ”頁面輸入以下信息。

- 選擇 **Advanced** 單選按鈕。

- 在 **“Group by** **”**下選擇以下內容:

  1.  **Country**

  2.  **StateProvince**

  3.  **City**

- 在 **New column name** 中，在 **Operation**
  欄字段輸入**SumOfTaxAmount**，選擇**Sum**，然後在**Column**字段下選擇**TaxAmount**。點擊“**Add
  aggregation** ”以添加更多匯總列和作。

- 在**New column name**中，在 **Operation**
  欄字段輸入**SumOfProfit**，選擇
  SumOfProfit，然後在**Column**字段下選擇**Profit**。點擊“**Add
  aggregation**”以添加更多匯總列和作。

- 在**New column name**中，在作欄字段輸入
  **SumOfTotalIncludingTax**，選擇 **Sum**，然後在**Column**字段下選
  **TotalIncludingTax**。

- 點擊 **OK** 按鈕

![](./media/image113.png)

![A screenshot of a computer Description automatically
generated](./media/image114.png)

11. 在資源管理器中，進入**Queries**，右鍵點擊 **Queries** 中的**Visual
    query 1**。然後，選擇 **Rename**。

> ![A screenshot of a computer Description automatically
> generated](./media/image115.png)

12. 輸入 +++**Sales Summary+++** 以更改查詢名稱。按鍵盤上的
    **Enter**鍵或選擇選項卡外的任意位置以保存更改。 

> ![A screenshot of a computer Description automatically
> generated](./media/image116.png)

13. 點擊**主**頁標簽下方的**刷新**圖標。

> ![A screenshot of a computer Description automatically
> generated](./media/image117.png)

# **練習8：用筆記本分析數據**

## 任務1：創建一個湖邊小屋快捷方式，並用筆記本分析數據

在這個任務中，學習如何一次性保存數據，然後將其用於其他多種服務。還可以為存儲在
Azure Data Lake Storage 和 S3
中的數據創建快捷方式，使您可以直接訪問外部系統的 delta 表。

首先，我們建造一個新的 lakehouse。在您的 Microsoft Fabric
工作區創建新的lakehouse:

1.  在**WideWorldImportes**頁面，點擊
    左側導航菜單**Warehouse_FabricXX**工作區。

> ![A screenshot of a computer Description automatically
> generated](./media/image118.png)

2.  在**Synapse Data Engineering Warehouse_FabricXX** 主頁，**Warehouse_FabricXX** 
    窗格下點擊**+New item**，然後在 **Stored data** 中選擇**Lakehouse**

> ![A screenshot of a computer Description automatically
> generated](./media/image119.png)

3.  在**Name**字段中輸入
    +++**ShortcutExercise+++**並點擊“**Create**”按鈕。 

> ![A screenshot of a computer Description automatically
> generated](./media/image120.png)

4.  新的 lakehouse 加載完畢後，**Explorer** 視圖打開，其中包含“**Get
    data in your lakehouse**”菜單。在“**Load data in your
    lakehouse**”下，選擇 **New shortcut** 按鈕。 view opens up, with
    the  menu. Under , select the  button.

> ![A screenshot of a computer Description automatically
> generated](./media/image121.png)

5.  在**“New shortcut**”窗口中，選擇 **Microsoft OneLake**。

> ![A screenshot of a computer Description automatically
> generated](./media/image122.png)

6.  在“**Select a data source type** ”窗口中，仔細點擊你之前創建的名為
    **WideWorldImporters** 的**Warehouse**，然後點擊“**Next**”按鈕。

> ![A screenshot of a computer Description automatically
> generated](./media/image123.png)

7.  在 **OneLake** 對象瀏覽器中，展開“**Tables**”，然後展開 **dbo**
    模式，選擇**dimension_customer**旁邊的單選按鈕。選擇“**Next**”按鈕。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image124.png)

8.  在 **New shortcut** 窗口中，點擊 **“Create** ”按鈕，點擊 **Close**
    按鈕

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image125.png)
>
> ![](./media/image126.png)

9.  等一會兒，然後點擊 **刷新**圖標。

10. 然後，在 **Table** 列表中選擇 **dimension_customer**
    以預覽數據。請注意，lakehouse顯示的是倉庫中 **dimension_customer**
    表的數據。

> ![](./media/image127.png)

11. 接下來，創建一個新的筆記本來查詢**dimension_customer**表。在“**Home**”功能區中，選擇“**Open
    notebook** ”下拉菜單，然後選擇“**New notebook**”。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image128.png)

12. 選擇後，將**Tables**列表中的**dimension_customer** 拖曳到打開的筆記本單元格中。你可以看到已經寫了一個
    **PySpark** 查詢，用於查詢
    **ShortcutExercise.dimension_customer**上的所有數據。這種筆記本體驗類似於Visual
    Studio Code Jupyter筆記本體驗。你也可以用VS Code打開筆記本。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image129.png)

13. 在**主**頁功能區，選擇“**Run
    all** ”按鈕。查詢完成後，你會發現可以輕鬆用 PySpark 查詢倉庫表！ 

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image130.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image131.png)

# **練習9：使用SQL查詢編輯器創建跨倉庫查詢**

## 任務1：向Explorer添加多個倉庫

在本任務中，學習如何輕鬆地使用SQL查詢編輯器在多個倉庫中創建和執行T-SQL查詢，包括將Microsoft
Fabric中的SQL端點和倉庫的數據合併在一起。

1.  從**Notebook1**頁面，在
    左側導航菜單中點擊**Warehouse_FabricXX**工作區。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image132.png)

2.  在 **Warehouse_FabricXX** 視圖中，選擇**WideWorldImporters**倉庫。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image133.png)

3.  在**WideWorldImporters**頁面的**Explorer**標簽下，選擇**Warehouse**
    按鈕。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image134.png)

4.  在添加倉庫窗口中，選擇 **ShortcutExercise** ，點擊 **Confirm**
    按鈕。這兩種倉庫經驗都會被添加到查詢中。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image135.png)

5.  你選中的倉庫現在顯示的是相同的**Explorer**面板。

![](./media/image136.png)

## 任務2：執行跨倉庫查詢

在這個例子中，你可以看到在 WideWorldImporters 倉庫和 ShortcutExercise
SQL 端點之間運行 T-SQL 查詢是多麼容易 。你可以像 SQL Server
一樣，使用三部分命名來引用 database.schema.table 來寫跨數據庫查詢。

1.  在功能區的**主**頁標簽中，選擇 **New SQL query**。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image137.png)

2.  在查詢編輯器中，複製粘貼以下 T-SQL
    代碼。選擇**Run**按鈕來執行查詢。查詢完成後，你會看到結果。

> SQLCopy
>
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

3.  將查詢重命名以便參考。在 **Explorer** 中右鍵點擊 **SQL
    query**，選擇“**Rename**”。

> ![](./media/image139.png)

4.  在“**Rename**”對話框中，在“**Name**”字段下輸入 +++**Cross-warehouse
    query+++**，然後點擊**Rename** 按鈕。

> ![](./media/image140.png)

# 練習10：創建Power BI報告

## 任務1：創建一個 semantic 模型

在這個任務中，我們學習如何創建和保存多種類型的 Power BI 報告。

1.  在 **WideWorldImportes** 頁面的 **Home** 標簽下，選擇 **New semantic
    model**。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image141.png)

2.  在“**New semantic model”** 窗口中，在 **Direct Lake semantic model
    name** 框中輸入 +++**Sales Model+++**

3.  展開dbo模式，打開**Tables**文件夾，然後檢查**dimension_city**和**fact_sale**表。選擇
    **Confirm**。

> ![](./media/image142.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image143.png)

9.  從左側導航選擇 ***Warehouse_FabricXXXXX***，如下圖所示

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image144.png)

10. 要打開語義模型，返回工作區著陸頁，然後選擇 **Sales
    Model** 語義模型。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image145.png)

11. 要打開模型設計器，在菜單中選擇**“Open data model**”。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image146.png)
>
> ![](./media/image147.png)

12. 在 **Sales Model** 頁面，要編輯“**Manage
    Relationships**”，請將模式從“**Viewing**”改為“**Editing”**![A
    screenshot of a computer AI-generated content may be
    incorrect.](./media/image148.png)

13. 要創建關係，在模型設計器中，在**主**功能區選擇**“Manage
    relationships**”。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image149.png)

14. 在**New relationship窗口**中，完成以下步驟創建關係:

&nbsp;

1)  在“**From table”**下拉菜單中，選擇dimension_city表。

2)  在**“To table** ”下拉列表中，選擇fact_sale表。

3)  在**Cardinality** 下拉列表中，選擇 **One to many (1:\*)。**

4)  在 **Cross-filter direction** 下拉菜單中，選擇 **Single**。

5)  勾選**“Assume referential integrity** ”框。

6)  選擇 **Save**。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image150.png)
>
> ![](./media/image151.png)
>
> ![](./media/image152.png)

15. 在 **Manage relationship** 窗口中，選擇 **Close**。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image153.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image154.png)

## 任務2：創建Power BI報告

在這個任務中，學習如何基於你在任務中創建的語義模型創建Power BI報告 。

1.  在 **File** 功能區，選擇 **Create new report**。

> ![](./media/image155.png)

2.  在報告設計器中，完成以下步驟創建柱狀圖表可視化:

&nbsp;

1)  在 **Data**面板中，展開**fact_sale**表，然後勾選利潤字段。

2)  在 **Data**
    面板中，展開dimension_city表，然後勾選SalesTerritory字段。

> ![](./media/image156.png)

3.  在**Visualizations**面板中，選擇 **Azure Map** 可視化。

> ![](./media/image157.png)

4.  在 **Data** 面板中，從dimension_city表內，將 StateProvince 字段拖到
    **Visualizations**  面板的 **Location** 井中。 

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image158.png)

5.  在**Data **面板中，從fact_sale表內部，檢查利潤字段，將其添加到地圖可視化的**尺寸**井中。

6.  在 **Visualizations** 面板中，選擇 **Table**可視化。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image159.png)

7.  在 **Data** 面板中，勾選以下字段:

&nbsp;

1)  dimension_city表中的SalesTerritory

2)  來自 dimension_city 表的 StateProvince

3)  fact_sale表的利潤

4)  從fact_sale表中剔除的TotalExcludingTax

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image160.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image161.png)

8.  請核實報告頁面的完成設計是否與以下圖片相似。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image162.png)

9.  要保存報告，請在首頁功能區選擇“**File** \> **Save**”。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image163.png)

10. 在“保存您的報告”窗口，在“輸入報告名稱”框中，輸入+++**Sales
    Analysis**+++，然後選擇 **Save**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image164.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image165.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image166.png)

## 任務3：清理資源

你可以刪除單個報表、管道、倉庫和其他項目，或者刪除整個工作區。在這個教程中，你將清理工作區、單個報告、管道、倉庫以及你作為實驗室一部分創建的其他項目。

1.  在導航菜單中選擇**Warehouse_FabricXX**返回工作區的項目列表。

> ![](./media/image167.png)

2.  在工作區頭的菜單中，選擇 **Workspace settings**。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image168.png)

3.  在 **Workspace settings**
    對話框中，選擇“**General**”，然後選擇“**Remove this workspace**”。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image169.png)

4.  在 **Delete workspace?** 對話框，點擊 **Delete**
    按鈕。![](./media/image170.png)

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image171.png)

**摘要**

這個綜合實驗室介紹了一系列旨在在 Microsoft Fabric
中建立功能性數據環境的任務。它從創建一個工作區開始，這對數據作至關重要，並確保試驗的啟用。隨後，在
Fabric 環境中建立了名為 WideWorldImporters
的倉庫，作為數據存儲和處理的中央倉庫。隨後，通過實現數據工廠流水線，詳細說明瞭Warehouse_FabricXX工作區中的數據攝取過程。該過程涉及從外部來源獲取數據並將其無縫集成到工作區中。關鍵表、關鍵表、dimension_city和fact_sale在數據倉庫中被創建，作為數據分析的基礎結構。數據加載過程繼續使用T-SQL進行，將Azure
Blob存儲中的數據傳輸到指定的表中。後續任務涉及數據管理和作領域。演示了克隆表，為數據複製和測試提供了寶貴的技術。此外，克隆過程被擴展到同一倉庫內的不同模式（dbo1），展示了結構化的數據組織方法。實驗室推進到數據轉換，引入了存儲過程以高效聚合銷售數據。隨後轉為可視化查詢構建，為複雜數據查詢提供直觀的界面。接著是對筆記本的探索，展示了它們在查詢和分析dimension_customer表數據方面的實用性。隨後，展示了多倉庫查詢功能，使工作空間內不同倉庫之間能夠無縫檢索數據。實驗室最終實現了Azure地圖可視化集成，增強了Power
BI中的地理數據表示。隨後，創建了一系列Power
BI報告，包括柱狀圖、地圖和表格，以促進深入的銷售數據分析。最後一項任務是從OneLake數據中心生成報告，進一步強調Fabric中數據源的多樣性。最後，實驗室還提供了資源管理的見解，強調清理程序對於保持高效工作環境的重要性。這些任務綜合起來，提供了對在
Microsoft Fabric 中設置、管理和分析數據的全面理解。
