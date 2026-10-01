# 使用案例 03：在 Microsoft Fabric 中為 Contoso 建置銷售和地理資料倉儲

**案例情境**

Contoso 是一家跨國零售公司，正在尋求實作資料基礎結構的現代化，以提升銷售和地理分析能力。目前，公司的銷售和客戶資料分散在多個系統中，業務分析師和公民開發人員難以從中擷取洞察。公司計劃使用 Microsoft Fabric 將這些資料整合到統一的平臺中，以支援跨倉儲查詢、銷售分析和地理報表。

在本實驗中，您將扮演 Contoso 的資料工程師，負責使用 Microsoft Fabric 設計和實作資料倉儲解決方案。您將首先設定 Fabric 工作區並建立資料倉儲，然後載入範例資料，並執行一系列分析任務，為 Contoso 的決策者提供洞察。

**簡介**

雖然 Microsoft Fabric 中的許多概念對資料和分析專業人員來說可能很熟悉，但在新環境中應用這些概念可能具有挑戰性。本實驗以循序漸進的方式，帶您完成從資料引入到資料使用的端到端場景，幫助您建立對 Microsoft Fabric 使用者體驗、各種工作負載及其整合點，以及專業開發人員和公民開發人員體驗的基本理解。

**本實驗建立的 Fabric 項目**

| **項目** | **名稱** | **在實驗中的用途** |
|----|----|----|
| 工作區 | Warehouse_Fabric\<實驗執行個體 ID\> | 包含本實驗的所有項目 |
| 倉儲 | WideWorldImporters | 儲存 Wide World Importers 範例資料、複製品資料表、預存程序和檢視 |
| 複製作業 | Load Customer Data | 將範例資料載入到倉儲 |
| Lakehouse | Shortcut_Exercise | 包含指向倉儲 dimension_customer 表的捷徑 |
| 語意模型 | Sales Model | 以倉儲為基礎的 Direct Lake 語意模型 |
| 報表 | Sales Analysis | 包含直條圖、地圖和表格的 Power BI 報表 |

**目標**：

- 建立 Microsoft Fabric 工作區和名為 WideWorldImporters 的倉儲。

- 使用複製作業將 Wide World Importers 範例資料載入到倉儲中。

- 使用 T-SQL 在同一結構描述內以及跨結構描述 (dbo1) 複製資料表，包括時間點複製。

- 建立並執行預存程序，以轉換資料並建立 aggregate_sale_by_date_city 表。

- 使用 T-SQL 在陳述式層級執行時間旅行查詢。

- 使用視覺化查詢產生器合併和彙總資料。

- 使用 T-SQL 筆記本和 Spark 筆記本查詢和分析資料。

- 在 WideWorldImporters 倉儲和 Shortcut_Exercise SQL 分析端點之間執行跨倉儲查詢。

- 建立 Direct Lake 語意模型，並建置包含直條圖、地圖和表格視覺效果的 Power BI 報表。

- 刪除工作區及其相關項目。

**注意：** 本實驗的螢幕擷取畫面使用英文介面，因此步驟中的介面名稱（例如 **+ New workspace**、**Apply**）保留英文，方便您對照介面操作。

## 練習 1：建立 Microsoft Fabric 工作區

在本練習中，您將登入 Microsoft Fabric、建立工作區，並在其中建立倉儲。

### 任務 1：建立工作區

1.  開啟瀏覽器，在位址列中輸入或貼上以下 URL：+++https://app.fabric.microsoft.com/+++，然後按 **Enter** 鍵。

**注意**：如果您直接進入 Microsoft Fabric 主頁，請跳到步驟 6。

![](./media/image1.png)

2.  在 **Microsoft Fabric** 視窗中輸入您的認證，然後按一下 **Submit** 按鈕。

| **使用者名稱** | **+++@lab.CloudPortalCredential(User1).Username+++** |
|----|----|
| **密碼** | **+++@lab.CloudPortalCredential(User1).Password+++** |

![](./media/image2.png)

3.  在 **Microsoft** 視窗中輸入密碼，然後按一下 **Sign in** 按鈕。

![](./media/image3.png)

4.  在 **Stay signed in?** 視窗中，按一下 **Yes** 按鈕。

5.  如果預設開啟 Power BI，請執行以下步驟，否則跳過此步驟：

- 按一下 **Power BI**。

![](./media/image4.png)

- 從選項中選擇 **Fabric**。

![](./media/image5.png)

6.  在 Fabric 主頁上，選擇 **+ New workspace** 圖格。

![](./media/image6.png)

7.  在 **Create a workspace** 窗格中，輸入以下資訊，然後按一下 **Apply** 按鈕。

| **屬性** | **值** |
|----|----|
| **Name** | +++Warehouse_Fabric@lab.LabInstance.Id+++（必須是唯一的名稱） |
| **Description** | +++This workspace contains all the artifacts for the data warehouse+++ |
| **Advanced** | 在 **License mode** 下選擇 **Fabric** |
| **Default storage format** | **Small dataset storage format** |

![](./media/image7.png)

![](./media/image8.png)

![](./media/image9.png)

8.  等待部署完成，大約需要 1-2 分鐘。新工作區開啟時應該是空的。

![](./media/image10.png)

### 任務 2：在 Microsoft Fabric 中建立倉儲

1.  在工作區頁面上，選擇 **+ New item**，然後選擇 **Warehouse**。

![](./media/image11.png)

2.  在 **New warehouse** 對話方塊中，輸入 +++WideWorldImporters+++，然後按一下 **Create** 按鈕。

![](./media/image12.png)

3.  佈建完成後，會顯示 **WideWorldImporters** 倉儲的登陸頁面。

![](./media/image13.png)

## 練習 2：在 Microsoft Fabric 中將資料擷取到倉儲

在本練習中，您將使用複製作業，把 Wide World Importers 範例資料載入到倉儲中。

### 任務 1：將資料擷取到倉儲

1.  在 **WideWorldImporters** 倉儲登陸頁面的左側導覽選單中，選擇 **Warehouse_Fabric@lab.LabInstance.Id**，返回工作區項目清單。

![](./media/image14.png)

2.  在工作區頁面上，選擇 **+ New item**，然後在 **Get data** 下按一下 **Copy job**。

![](./media/image15.png)

3.  在 **New copy job** 視窗的 **Name** 框中，輸入 +++Load Customer Data+++，然後按一下 **Create**。

![](./media/image16.png)

4.  **Copy job** 頁面隨即開啟。

![](./media/image17.png)

5.  在 **Copy job** 精靈的第一頁，從功能表列中選擇 **Sample data**，然後選擇 **Retail Data Model from Wide World Importers** 範例，進入下一頁。

![](./media/image18.png)

6.  範例資料的預覽隨即載入。在 **Choose data** 頁面上，您可以預覽所選資料集。檢閱資料後，按一下 **Next**。

![](./media/image19.png)

7.  在 **Choose data destination** 頁面上，從 OneLake 目錄中選擇您的 **WideWorldImporters** 倉儲，然後按一下 **Next**。

![](./media/image20.png)

8.  在 **Choose copy job mode** 頁面上，選擇 **Full copy**，然後按一下 **Next**。

![](./media/image21.png)

9.  輸入以下目標表，然後按一下 **Next**。

- +++dbo.dimension_city+++

- +++dbo.dimension_customer+++

- +++dbo.dimension_date+++

- +++dbo.dimension_employee+++

- +++dbo.dimension_stock_item+++

- +++dbo.fact_sale+++

![](./media/image22.png)

10. 在 **Review + save** 頁面上，檢視 **Source** 和 **Destination**，然後儲存。

![](./media/image23.png)

11. 使用 **Results** 索引標籤監視複製作業的執行情況。

![](./media/image24.png)

12. 完成後，**Copy job** 會顯示 **Succeeded** 通知和狀態。現在，您會在倉儲中看到來自 Wide World Importers 資料集的六個新表。

![](./media/image25.png)

13. 在 **Load Customer Data** 頁面上，按一下左側導覽欄中的 **Warehouse_Fabric@lab.LabInstance.Id** 工作區，然後選擇 **WideWorldImporters** 倉儲。

![](./media/image26.png)

14. 在 **WideWorldImporters** 倉儲中，展開 **Schemas \> dbo \> Tables**，並驗證表 **dimension_city**、**dimension_customer**、**dimension_date**、**dimension_employee**、**dimension_stock_item** 和 **fact_sale** 是否已成功建立。

![](./media/image27.png)

## 練習 3：在倉儲中使用 T-SQL 複製資料表

在本練習中，您將使用 T-SQL 在同一結構描述內以及跨結構描述複製資料表，包括時間點複製。

### 任務 1：在同一結構描述內複製資料表

1.  在 **WideWorldImporters** 頁面上，轉到 **Home** 索引標籤，從 **SQL** 下拉式功能表中按一下 **New SQL query**。

![](./media/image28.png)

2.  在查詢編輯器中貼上以下程式碼。此程式碼會建立 **dimension_city** 表和 **fact_sale** 表的複製品。

```sql
--Create a clone of the dbo.dimension_city table.
CREATE TABLE [dbo].[dimension_city1] AS CLONE OF [dbo].[dimension_city];

--Create a clone of the dbo.fact_sale table.
CREATE TABLE [dbo].[fact_sale1] AS CLONE OF [dbo].[fact_sale];
```

![](./media/image29.png)

3.  若要執行查詢，請在查詢設計功能區上選擇 **Run**。

![](./media/image30.png)

![](./media/image31.png)

4.  在查詢編輯器中貼上以下程式碼，取代現有陳述式。CURRENT_TIMESTAMP T-SQL 函式以 **datetime** 形式傳回目前 UTC 時間戳記。選擇 **Run** 執行查詢，並複製傳回的時間戳記值。

```sql
SELECT CURRENT_TIMESTAMP;
```

![](./media/image32.png)

5.  若要建立*過去某個時間點*的資料表複製品，請在查詢編輯器中貼上以下程式碼，**取代現有陳述式**。將 **YOUR_TIMESTAMP** 替換為上一步傳回的時間戳記（格式為 **YYYY-MM-DDTHH:MM:SS.FFF**）。此程式碼會建立 **dimension_city** 表和 **fact_sale** 表在該時間點的複製品。執行查詢。

```sql
--Create a clone of the dbo.dimension_city table at a specific point in time.
CREATE TABLE [dbo].[dimension_city2] AS CLONE OF [dbo].[dimension_city] AT 'YOUR_TIMESTAMP';

--Create a clone of the dbo.fact_sale table at a specific point in time.
CREATE TABLE [dbo].[fact_sale2] AS CLONE OF [dbo].[fact_sale] AT 'YOUR_TIMESTAMP';
```

**注意：** 時間點必須晚於表的建立時間，並在倉儲的資料保留期內。如果使用表建立之前的時間（例如 2025 年的日期），複製會失敗。

![](./media/image33.png)

![](./media/image34.png)

6.  將查詢重新命名為 +++Clone Tables+++。

![](./media/image35.png)

![](./media/image36.png)

### 任務 2：在同一倉儲內跨結構描述複製資料表

在此任務中，您將學習如何在同一倉儲內跨結構描述複製資料表。

1.  若要建立新查詢，請在 **Home** 功能區上選擇 **New SQL query**。

![](./media/image37.png)

2.  在查詢編輯器中貼上以下程式碼。此程式碼會建立一個結構描述，然後在新結構描述中建立 **fact_sale** 表和 **dimension_city** 表的複製品。執行查詢。

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

3.  執行完成後，預覽 **dbo1** 結構描述中 **dimension_city1** 表載入的資料。

![](./media/image39.png)

4.  若要建立*過去某個時間點*的資料表複製品，請在查詢編輯器中貼上以下程式碼，**取代現有陳述式**。將 **YOUR_TIMESTAMP** 替換為任務 1 中複製的時間戳記。此程式碼會在新結構描述中建立 **dimension_city** 表和 **fact_sale** 表在該時間點的複製品。執行查詢。

```sql
--Create a clone of the dbo.dimension_city table in the dbo1 schema.
CREATE TABLE [dbo1].[dimension_city2] AS CLONE OF [dbo].[dimension_city] AT 'YOUR_TIMESTAMP';

--Create a clone of the dbo.fact_sale table in the dbo1 schema.
CREATE TABLE [dbo1].[fact_sale2] AS CLONE OF [dbo].[fact_sale] AT 'YOUR_TIMESTAMP';
```

![](./media/image40.png)

5.  執行完成後，預覽 **dbo1** 結構描述中 **fact_sale2** 表載入的資料。

![](./media/image41.png)

6.  將查詢重新命名為 +++Clone Tables Across Schemas+++。

![](./media/image42.png)

![](./media/image43.png)

## 練習 4：使用預存程序轉換資料

在本練習中，您將建立並執行預存程序，以轉換倉儲表中的資料。

### 任務 1：建立預存程序

1.  在 **WideWorldImporters** 頁面上，轉到 **Home** 索引標籤，從 **SQL** 下拉式功能表中按一下 **New SQL query**。

![](./media/image44.png)

2.  在查詢編輯器中貼上以下程式碼。此程式碼會刪除預存程序（如果存在），然後建立名為 **populate_aggregate_sale_by_city** 的預存程序。預存程序的邏輯會建立名為 **aggregate_sale_by_date_city** 的表，並使用聯結 **fact_sale** 和 **dimension_city** 表的分組查詢插入資料。

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

3.  若要執行查詢，請在查詢設計功能區上選擇 **Run**。

![](./media/image46.png)

4.  執行完成後，將查詢重新命名為 +++Create Aggregate Procedure+++。

![](./media/image47.png)

![](./media/image48.png)

5.  在 **Explorer** 窗格中，確認 **dbo** 結構描述的 **Stored Procedures** 資料夾中存在 **populate_aggregate_sale_by_city** 預存程序。

![](./media/image49.png)

### 任務 2：執行預存程序

1.  在 **WideWorldImporters** 頁面上，轉到 **Home** 索引標籤，從 **SQL** 下拉式功能表中按一下 **New SQL query**。

![](./media/image50.png)

2.  在查詢編輯器中貼上以下程式碼。此程式碼會執行 **populate_aggregate_sale_by_city** 預存程序。執行查詢。

```sql
--Execute the stored procedure to create and load aggregated data.
EXEC [dbo].[populate_aggregate_sale_by_city];
```

![](./media/image51.png)

3.  執行完成後，將查詢重新命名為 +++Run Aggregate Procedure+++。

![](./media/image52.png)

![](./media/image53.png)

4.  若要預覽彙總資料，請在 **Explorer** 窗格中選擇 **aggregate_sale_by_date_city** 表。

![](./media/image54.png)

**注意：** 如果表未顯示，請選擇 **Tables** 資料夾的省略號 (**...**)，然後選擇 **Refresh**。

## 練習 5：在陳述式層級使用 T-SQL 進行時間旅行

在本練習中，您將建立銷售額排名前十的客戶檢視，並使用該檢視執行時間旅行查詢。

### 任務 1：使用時間旅行查詢

1.  在 **WideWorldImporters** 頁面上，轉到 **Home** 索引標籤，從 **SQL** 下拉式功能表中按一下 **New SQL query**。

![](./media/image55.png)

2.  在查詢編輯器中貼上以下程式碼。此程式碼會建立名為 **Top10Customers** 的檢視，該檢視根據銷售額擷取排名前 10 的客戶。選擇 **Run** 執行查詢。

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

3.  執行完成後，將查詢重新命名為 +++Create Top 10 Customer View+++。

![](./media/image57.png)

![](./media/image58.png)

4.  在 **Explorer** 中展開 **dbo** 結構描述下的 **Views** 節點，確認可以看到新建立的檢視 **Top10Customers**。

![](./media/image59.png)

5.  與步驟 1 類似，建立新查詢。在功能區的 **Home** 索引標籤上，選擇 **New SQL query**。

![](./media/image60.png)

6.  在查詢編輯器中貼上以下程式碼。此程式碼會更新單一事實資料列的 **TotalIncludingTax** 值，故意誇大其總銷售額，並擷取當前時間戳記。執行查詢。

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

7.  將傳回的時間戳記值複製到剪貼簿。

![](./media/image62.png)

**注意：** 目前，只能使用協調世界時 (UTC) 時區進行時間旅行。

8.  執行完成後，將查詢重新命名為 +++Time Travel+++。

![](./media/image63.png)

![](./media/image64.png)

9.  建立新查詢，並貼上以下陳述式，以擷取*目前*排名前 10 的客戶。此程式碼使用 **FOR TIMESTAMP AS OF** 查詢提示。將 **YOUR_TIMESTAMP** 替換為您複製到剪貼簿的時間戳記，格式為 **YYYY-MM-DDTHH:MM:SS\[.FFF\]**，並去掉末尾的零，例如 **2026-07-27T06:20:55.823**。執行查詢。

```sql
--Retrieve the top 10 customers as of now.
SELECT *
FROM [dbo].[Top10Customers]
OPTION (FOR TIMESTAMP AS OF 'YOUR_TIMESTAMP');
```

![](./media/image65.png)

10. 將查詢重新命名為 +++Time Travel Now+++。

![](./media/image66.png)

![](./media/image67.png)

11. 請注意，由於剛才誇大了銷售額，排名第一的 **CustomerKey** 現在是 **49**，即 **Tailspin Toys (Muir, MI)**。

![](./media/image68.png)

12. 將時間戳記值**減去一分鐘**，修改為更早的時間。

13. 再次執行查詢，請注意排名第一的 **CustomerKey** 是 **381**，即 **Wingtip Toys (Sarversville, PA)**。這是更新之前的結果。

## 練習 6：在倉儲中使用視覺化查詢產生器建立查詢

在本練習中，您將使用視覺化查詢產生器合併和彙總資料，而無需編寫 SQL 程式碼。

### 任務 1：使用視覺化查詢產生器

1.  在 **Home** 功能區上，開啟 **New SQL query** 下拉式功能表，然後選擇 **New visual query**。

![](./media/image69.png)

2.  在 **Explorer** 窗格中，從 **dbo** 結構描述的 **Tables** 資料夾將 **fact_sale** 表拖到視覺化查詢畫布上。

![](./media/image70.png)

3.  若要限制資料集大小，請在查詢設計器的 **transformations** 功能區中，按一下 **Reduce rows** 下拉式功能表，然後按一下 **Keep top rows**。

![](./media/image71.png)

4.  在 **Keep top rows** 對話方塊中，輸入 +++10000+++，然後選擇 **OK**。

![](./media/image72.png)

![](./media/image73.png)

5.  在 **Explorer** 窗格中，以滑鼠右鍵按一下 **dbo** 結構描述 **Tables** 資料夾中的 **dimension_city** 表，然後選擇 **Insert into canvas**（也可以直接將表拖到畫布上）。

![](./media/image74.png)

![](./media/image75.png)

6.  在 **transformations** 功能區中，選擇 **Combine** 旁邊的下拉式功能表，然後選擇 **Merge queries as new**。

![](./media/image76.png)

7.  在 **Merge** 設定頁面上輸入以下資訊，然後按一下 **OK**：

- 在 **Left table for merge** 下拉式功能表中，選擇 **dimension_city**。

- 在 **Right table for merge** 下拉式功能表中，選擇 **fact_sale**（可使用水平和垂直捲軸）。

- 在 **dimension_city** 表中，選擇標題列中的 **CityKey** 資料行名稱作為聯結資料行。

- 在 **fact_sale** 表中，選擇標題列中的 **CityKey** 資料行名稱作為聯結資料行。

- 在 **Join kind** 中，選擇 **Inner**。

![](./media/image77.png)

![](./media/image78.png)

8.  選取 **Merge** 步驟後，按一下資料網格標題中 **fact_sale** 旁邊的 **Expand** 按鈕，選擇 **TaxAmount**、**Profit** 和 **TotalIncludingTax** 資料行，然後選擇 **OK**。

![](./media/image79.png)

![](./media/image80.png)

![](./media/image81.png)

9.  在 **transformations** 功能區中，按一下 **Transform** 旁邊的下拉式功能表，然後選擇 **Group by**。

![](./media/image82.png)

10. 在 **Group by** 頁面上輸入以下資訊：

- 選擇 **Advanced** 選項按鈕。

- 在 **Group by** 下選擇以下資料行：**Country**、**StateProvince**、**City**。

- 新增以下彙總資料行（每新增一個資料行後，按一下 **Add aggregation** 新增下一個）：

| **New column name** | **Operation** | **Column** |
|----|----|----|
| +++SumOfTaxAmount+++ | **Sum** | **TaxAmount** |
| +++SumOfProfit+++ | **Sum** | **Profit** |
| +++SumOfTotalIncludingTax+++ | **Sum** | **TotalIncludingTax** |

- 按一下 **OK** 按鈕。

![](./media/image83.png)

![](./media/image84.png)

11. 在 **Explorer** 中，轉到 **Queries**，以滑鼠右鍵按一下 **Visual query 1**，然後選擇 **Rename**。

![](./media/image85.png)

12. 輸入 +++Sales Summary+++ 變更查詢名稱。按 **Enter** 鍵或按一下索引標籤外的任意位置儲存變更。

![](./media/image86.png)

13. 按一下 **Home** 索引標籤下的 **Refresh** 圖示。

![](./media/image87.png)

## 練習 7：使用筆記本分析資料

在本練習中，您將使用 T-SQL 筆記本查詢倉儲資料，並透過 Lakehouse 捷徑和 Spark 筆記本分析資料。

### 任務 1：建立 T-SQL 筆記本

1.  在 **Home** 功能區上，開啟 **New SQL query** 下拉式功能表，然後選擇 **New SQL query in notebook**。

![](./media/image88.png)

2.  在 **Explorer** 窗格中，選擇 **Warehouses** 以顯示 **WideWorldImporters** 倉儲中的物件。

3.  若要產生用於瀏覽資料的 SQL 範本，請選擇 **dimension_city** 表右側的省略號 (**...**)，然後選擇 **SELECT TOP 100**。

![](./media/image89.png)

4.  若要執行此儲存格中的 T-SQL 程式碼，請選擇程式碼儲存格的 **Run cell** 按鈕。

![](./media/image90.png)

5.  在結果窗格中檢視查詢結果。

![](./media/image91.png)

### 任務 2：建立 Lakehouse 捷徑並使用筆記本分析資料

1.  在左側選單中，選擇 **Warehouse_Fabric@lab.LabInstance.Id** 工作區圖示，然後選擇工作區名稱。

![](./media/image92.png)

2.  選擇 **+ New item**，以顯示所有可用項目類型的完整清單。

3.  在清單的 **Store data** 部分中，選擇 **Lakehouse** 項目類型。

![](./media/image93.png)

4.  輸入 +++Shortcut_Exercise+++ 作為 Lakehouse 名稱，並取消選擇 **Lakehouse schemas**。選擇 **Create**。

![](./media/image94.png)

![](./media/image95.png)

5.  新的 Lakehouse 開啟後，在登陸頁面上選擇 **New shortcut** 選項。

![](./media/image96.png)

6.  在 **New shortcut** 視窗中，選擇 **Microsoft OneLake** 選項。

![](./media/image97.png)

7.  在 **Select a data source type** 視窗中，選擇 **WideWorldImporters** 倉儲，然後選擇 **Next**。

![](./media/image98.png)

8.  按一下 **Connect**。

![](./media/image99.png)

9.  在 **OneLake object** 瀏覽器中，展開 **Tables**，展開 **dbo** 結構描述，然後勾選 **dimension_customer** 表的核取方塊。選擇 **Next**。

![](./media/image100.png)

10. 選擇 **Create**。

![](./media/image101.png)

11. 在 **Explorer** 窗格中，選擇 **dimension_customer** 表以預覽資料，並檢視從倉儲 **dimension_customer** 表擷取到的資料。

![](./media/image102.png)

12. 在 **dimension_customer** 表頁面上，按一下 **Analyze data with**，選擇 **Notebook**，然後選擇 **New notebook**，建立用於資料分析的新 Spark 筆記本。

![](./media/image103.png)

13. 在 **Explorer** 窗格中，選擇 **Lakehouses**。

14. 將 **dimension_customer** 表拖到開啟的筆記本儲存格中。

![](./media/image104.png)

15. 請注意，筆記本儲存格中新增了 **PySpark** 查詢。該查詢從 **Shortcut_Exercise.dimension_customer** 捷徑擷取前 **1,000 個資料列**。這種筆記本體驗與 Visual Studio Code 中的 Jupyter 筆記本體驗類似，您也可以在 VS Code 中開啟筆記本。

![](./media/image105.png)

16. 在 **Home** 功能區上，選擇 **Run all** 按鈕。

![](./media/image106.png)

![](./media/image107.png)

## 練習 8：使用 SQL 查詢編輯器建立跨倉儲查詢

在本練習中，您將學習如何使用 SQL 查詢編輯器在多個倉儲之間建立和執行 T-SQL 查詢，包括將 Microsoft Fabric 中的 SQL 分析端點和倉儲的資料合併在一起。

### 任務 1：向 Explorer 新增多個倉儲

1.  在筆記本頁面上，從左側導覽選單中選擇 **WideWorldImporters** 倉儲。

![](./media/image108.png)

2.  在 **Explorer** 窗格中，選擇 **+ Warehouses**。

![](./media/image109.png)

3.  在 **OneLake catalog** 視窗中，選擇 **Shortcut_Exercise** SQL 分析端點，然後選擇 **Confirm**。

![](./media/image110.png)

4.  在 **Explorer** 窗格中，請注意 **Shortcut_Exercise** SQL 分析端點現在可用。

![](./media/image111.png)

### 任務 2：執行跨倉儲查詢

在此任務中，您將執行一個查詢，將 WideWorldImporters 倉儲與 Shortcut_Exercise SQL 分析端點聯結起來。

**注意：** 跨資料庫查詢使用 *database.schema.table* 三部分命名來參考物件。

1.  在功能區的 **Home** 索引標籤上，選擇 **New SQL query**。

![](./media/image112.png)

2.  在查詢編輯器中貼上以下程式碼。此程式碼按庫存品項、說明和客戶擷取銷售數量的彙總。

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

3.  **執行**查詢，並檢視查詢結果。

![](./media/image113.png)

![](./media/image114.png)

4.  重新命名查詢以便日後參考。在 **Explorer** 中以滑鼠右鍵按一下 **SQL query**，然後選擇 **Rename**。

![](./media/image115.png)

![](./media/image116.png)

5.  在 **Rename** 對話方塊的 **Name** 欄位中，輸入 +++Cross-warehouse query+++，然後按一下 **Rename** 按鈕。

![](./media/image117.png)

## 練習 9：建立 Direct Lake 語意模型和 Power BI 報表

在本練習中，您將以 WideWorldImporters 倉儲為基礎建立 Direct Lake 語意模型，並建置 Power BI 報表。

### 任務 1：建立語意模型

1.  在 **WideWorldImporters** 頁面的 **Home** 索引標籤上，選擇 **New semantic model**。

![](./media/image118.png)

2.  在 **New semantic model** 視窗的 **Direct Lake semantic model name** 框中，輸入 +++Sales Model+++。

3.  展開 **dbo** 結構描述，展開 **Tables** 資料夾，然後勾選 **dimension_city** 和 **fact_sale** 表。選擇 **Confirm**。

![](./media/image119.png)

4.  從左側導覽中選擇 **Warehouse_Fabric@lab.LabInstance.Id** 工作區。

![](./media/image120.png)

5.  若要開啟語意模型，請在工作區登陸頁面上選擇 **Sales Model** 語意模型。

![](./media/image121.png)

![](./media/image122.png)

6.  在 **Sales Model** 頁面上，將模式從 **Viewing** 變更為 **Editing**，以便管理關係。

![](./media/image123.png)

7.  若要建立關係，請在模型設計器的 **Home** 功能區上選擇 **Manage relationships**。

![](./media/image124.png)

8.  在 **Manage relationships** 視窗中，選擇 **+ New relationship**。

![](./media/image125.png)

9.  在 **New relationship** 視窗中，完成以下步驟以建立關係：

- 在 **From table** 下拉式功能表中，選擇 **dimension_city** 表。

- 在 **To table** 下拉式功能表中，選擇 **fact_sale** 表。

- 在 **Cardinality** 下拉式功能表中，選擇 **One to many (1:\*)**。

- 在 **Cross-filter direction** 下拉式功能表中，選擇 **Single**。

- 勾選 **Assume referential integrity** 核取方塊。

- 選擇 **Save**。

![](./media/image126.png)

![](./media/image127.png)

10. 在 **Manage relationships** 視窗中，選擇 **Close**。

![](./media/image128.png)

![](./media/image129.png)

### 任務 2：建立 Power BI 報表

在此任務中，您將以上一個任務建立的語意模型為基礎建置 Power BI 報表。

1.  在 **File** 功能區上，選擇 **Create new report**。

![](./media/image130.png)

2.  在報表設計器中，完成以下步驟以建立直條圖視覺效果：

- 在 **Data** 窗格中，展開 **fact_sale** 表，然後勾選 **Profit** 欄位。

- 在 **Data** 窗格中，展開 **dimension_city** 表，然後勾選 **SalesTerritory** 欄位。

![](./media/image131.png)

3.  按一下畫布的空白區域，然後在 **Visualizations** 窗格中選擇 **Azure Map** 視覺效果。

![](./media/image132.png)

4.  在 **Data** 窗格中，將 **dimension_city** 表中的 **StateProvince** 欄位拖到 **Visualizations** 窗格的 **Location** 區域。

![](./media/image133.png)

5.  在 **Data** 窗格中，勾選 **fact_sale** 表中的 **Profit** 欄位，將其新增到地圖視覺效果的 **Size** 區域。

6.  按一下畫布的空白區域，然後在 **Visualizations** 窗格中選擇 **Table** 視覺效果。

![](./media/image134.png)

7.  在 **Data** 窗格中，勾選以下欄位：

- **dimension_city** 表中的 **SalesTerritory**

- **dimension_city** 表中的 **StateProvince**

- **fact_sale** 表中的 **Profit**

- **fact_sale** 表中的 **TotalExcludingTax**

![](./media/image135.png)

![](./media/image136.png)

8.  確認已完成的報表頁面設計與下圖類似。

![](./media/image137.png)

9.  若要儲存報表，請在 **Home** 功能區上選擇 **File** \> **Save**。

![](./media/image138.png)

10. 在 **Save your report** 視窗的 **Enter a name for your report** 框中，輸入 +++Sales Analysis+++，然後選擇 **Save**。

![](./media/image139.png)

![](./media/image140.png)

![](./media/image141.png)

## 練習 10：清除資源

您可以刪除個別報表、管線、倉儲和其他項目，或刪除整個工作區。請按照以下步驟刪除您為本實驗建立的工作區及其所有項目。

1.  在導覽選單中選擇 **Warehouse_Fabric@lab.LabInstance.Id**，返回工作區項目清單。

![](./media/image142.png)

2.  在工作區標題的選單中，選擇 **Workspace settings**。

![](./media/image143.png)

3.  在 **Workspace settings** 對話方塊中，選擇 **General**，然後選擇 **Remove this workspace**。

![](./media/image144.png)

4.  在 **Delete workspace?** 對話方塊中，按一下 **Delete** 按鈕。

![](./media/image145.png)

![](./media/image146.png)

**摘要**

在本實驗中，您在 Microsoft Fabric 中為 Contoso 建置了一個完整的資料倉儲環境。您首先建立了工作區和 WideWorldImporters 倉儲，並使用複製作業載入了 Wide World Importers 範例資料。接著，您使用 T-SQL 在同一結構描述內和跨結構描述 (dbo1) 複製了表，包括時間點複製；建立並執行了預存程序，以生成 aggregate_sale_by_date_city 彙總表；並使用時間旅行查詢比較了資料更新前後的結果。然後，您使用視覺化查詢產生器在無需編寫程式碼的情況下合併和彙總資料，使用 T-SQL 筆記本和 Spark 筆記本分析資料，並透過 Lakehouse 捷徑執行了跨倉儲查詢。最後，您建立了 Direct Lake 語意模型，建置了包含直條圖、地圖和表格的 Power BI 銷售分析報表，並清理了工作區資源。這些任務幫助您全面瞭解如何在 Microsoft Fabric 中設定、管理和分析資料。
