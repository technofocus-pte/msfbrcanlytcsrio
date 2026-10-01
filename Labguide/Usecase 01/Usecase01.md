# 實驗 1：使用 Fabric Data Factory 實作資料移動與轉換的資料工程解決方案

**情境**

**Wide World Importers (WWI)** 是一家全球零售組織，在多個地區經營數百家門市。客戶資訊從多個營運系統收集而來，包括銷售點 (POS) 應用程式、CRM 平台和電子商務通路。這些資料以 CSV 檔案形式儲存，每天從不同的業務單位接收。

公司的分析團隊目前花費大量時間手動匯入檔案、驗證資料品質，以及準備報表所需的資料集。這些人工流程導致客戶洞察的產出延遲，也讓業務使用者難以取得一致且可靠的資訊。

為了將分析平台現代化，Wide World Importers 採用 **Microsoft Fabric** 作為統一的資料平台。資料工程團隊的任務是使用 **Microsoft Fabric Data Factory** 和 **Lakehouse** 實作可擴充的解決方案，以集中管理客戶資料、提升資料管理效率並簡化報表製作。

身為資料工程師，你的職責是建立 Fabric 工作區、佈建 Lakehouse、將客戶資料擷取到 OneLake、將來源檔案轉換為受控 Delta 資料表、使用 SQL 分析端點驗證匯入的資料、建立 Direct Lake 語義模型，並產生 Power BI 報表，讓業務利害關係人能以最低延遲分析客戶資訊。

透過實作此解決方案，Wide World Importers 可以免除手動資料準備、為客戶分析提供單一事實來源，並使用 Microsoft Fabric 做出更快速、以資料為導向的業務決策。

**簡介**

在本實驗中，你將使用 **Microsoft Fabric Data Factory** 和 **Fabric Lakehouse** 建置完整的資料工程解決方案。從新的 Fabric 工作區開始，你將把資料擷取到 Lakehouse、將檔案轉換為受控 Delta 資料表、使用 SQL 分析端點查詢資料、使用管線、筆記本和 Dataflow Gen2 轉換資料、透過電子郵件通知自動化並排程管線、建立語義模型，並產生互動式 Power BI 報表。

在整個實驗中，你將了解 Microsoft Fabric 如何將資料整合、儲存、轉換、分析和報表統一到單一的軟體即服務 (SaaS) 平台中。

**本實驗建立的 Fabric 項目**

| **項目** | **名稱** | **在實驗中的用途** |
|----|----|----|
| 工作區 | Fabric Dataengineering-DataFactory-\<實驗執行個體 ID\> | 包含本實驗的所有項目 |
| Lakehouse | wwilakehouse | 在 OneLake 中儲存原始檔案和 Delta 資料表 |
| 語義模型 | wwisemanticmodel | 用於報表的 Direct Lake 模型 |
| 管線 | IngestDataFromSourceToLakehouse | 複製 WWI 範例資料、執行資料流程並傳送電子郵件 |
| 筆記本 | Prepare and transform data – PySpark | 建立事實、維度和彙總 Delta 資料表 |
| Dataflow Gen2 | wwi_fact_sale_transform | 建立 Gold_Sales_By_City 資料表 |
| 報表 | dimension_customer-report、Profit Reporting | 以 Lakehouse 資料建置的 Power BI 報表 |

**目標**：

- 建立並設定 Microsoft Fabric 工作區。

- 建置並設定 Fabric Lakehouse。

- 將來源資料擷取到 OneLake。

- 將檔案載入受控 Delta 資料表。

- 使用 SQL 分析端點查詢 Lakehouse 資料。

- 使用筆記本和 Dataflow Gen2 轉換資料。

- 透過電子郵件通知自動化、排程和監視 Data Factory 管線。

- 建立 Direct Lake 語義模型。

- 從 Fabric 資料產生並探索 Power BI 報表。

**注意：** 本實驗的螢幕擷取畫面使用英文介面，因此步驟中的介面名稱（例如 **+ New workspace**、**Apply**）保留英文，方便你對照畫面操作。

## 練習 1：設定 Microsoft Fabric 資料工程環境

在建置資料工程解決方案之前，你需要先準備 Microsoft Fabric 環境。在本練習中，你將登入 Microsoft Fabric、建立專用工作區，並佈建作為分析解決方案集中儲存區的 Lakehouse。

### 工作 1：登入 Power BI 帳戶

1.  開啟瀏覽器，在網址列中輸入或貼上以下 URL：+++https://app.fabric.microsoft.com/+++，然後按 **Enter** 鍵。

![](./media/image1.png)

2.  在 **Microsoft Fabric** 視窗中輸入你的認證，然後按一下 **Submit** 按鈕。

| **使用者名稱** | **+++@lab.CloudPortalCredential(User1).Username+++** |
|----|----|
| **密碼** | **+++@lab.CloudPortalCredential(User1).Password+++** |

![](./media/image2.png)

3.  在 **Microsoft** 視窗中輸入密碼，然後按一下 **Sign in** 按鈕。

![](./media/image3.png)

4.  在 **Stay signed in?** 視窗中，按一下 **Yes** 按鈕。

5.  系統會將你導向 Power BI 首頁。

![](./media/image4.png)

6.  選取畫面左下角預設的 Power BI 圖示，然後選取 **Fabric**。

![](./media/image5.png)

![](./media/image6.png)

### 工作 2：建立 Fabric 工作區

在這項工作中，你將建立 Fabric 工作區。工作區包含本實驗所需的所有項目，包括 Lakehouse、資料流程、Data Factory 管線、筆記本、Power BI 語義模型和報表。

1.  在 Fabric 首頁上，選取 **+ New workspace** 圖格。

![](./media/image7.png)

2.  在右側出現的 **Create a workspace** 窗格中，輸入以下詳細資料，然後按一下 **Apply** 按鈕。

| **屬性** | **值** |
|----|----|
| **Name** | +++Fabric Dataengineering-DataFactory-@lab.LabInstance.Id+++ |
| **Advanced** | 在 **License mode** 下選取 **Fabric** |
| **Default storage format** | **Small dataset storage format** |

![](./media/image8.png)

**注意：** 若要找到你的實驗執行個體 ID，請選取 **Help** 並複製執行個體 ID。

![](./media/image9.png)

![](./media/image10.png)

![](./media/image11.png)

3.  等待部署完成，大約需要 2-3 分鐘。

![](./media/image12.png)

### 工作 3：建立 Lakehouse

1.  按一下導覽列中的 **+ New item** 按鈕，建立新的 Lakehouse。

![](./media/image13.png)

2.  按一下 **Lakehouse** 圖格。

![](./media/image14.png)

3.  在 **New lakehouse** 對話方塊的 **Name** 欄位中輸入 +++wwilakehouse+++，並**取消選取** **Lakehouse schemas**。按一下 **Create** 按鈕，並開啟新的 Lakehouse。

**注意**：請確認 **wwilakehouse** 前面沒有空格。

![](./media/image15.png)

4.  你會看到 **Successfully created SQL endpoint** 的通知。

![](./media/image16.png)

### 工作 4：擷取範例資料

1.  在 **wwilakehouse** 頁面上，前往 **Get data in your lakehouse** 區段，然後按一下 **Upload files**。

![](./media/image17.png)

2.  在 **Upload files** 索引標籤上，按一下 **Files** 下方的資料夾圖示。

![](./media/image18.png)

3.  在虛擬機器上瀏覽至 **C:\LabFiles**，選取 **dimension_customer.csv** 檔案，然後按一下 **Open** 按鈕。

![](./media/image19.png)

4.  按一下 **Upload** 按鈕。

![](./media/image20.png)

5.  **關閉** **Upload files** 窗格。

![](./media/image21.png)

6.  選取 **Files** 並按一下 **Refresh**，檔案隨即出現。

![](./media/image22.png)

7.  在 **Explorer** 窗格中選取 **Files**。將滑鼠游標停留在 **dimension_customer.csv** 檔案上，按一下旁邊的水平省略號 **(…)**，按一下 **Load Table**，然後選取 **New table**。

![](./media/image23.png)

![](./media/image24.png)

8.  在 **Load file to new table** 對話方塊中，按一下 **Load** 按鈕。

![](./media/image25.png)

9.  **dimension_customer** 資料表已成功建立。

![](./media/image26.png)

10. 選取 **Tables** 下的 **dimension_customer** 資料表。

![](./media/image27.png)

11. 你也可以使用 Lakehouse 的 SQL 端點，以 SQL 陳述式查詢資料。從畫面右上角的 **Analyze data with** 下拉式功能表中選取 **SQL analytics endpoint**。

![](./media/image28.png)

12. 在 **wwilakehouse** 頁面的 **Explorer** 下，選取 **dimension_customer** 資料表以預覽其資料，然後選取 **New SQL query** 撰寫 SQL 陳述式。

![](./media/image29.png)

13. 以下範例查詢會依 **dimension_customer** 資料表的 **BuyingGroup** 資料行彙總資料列數。SQL 查詢檔案會自動儲存以供日後參考，你可以視需要重新命名或刪除這些檔案。貼上程式碼，然後按一下播放圖示**執行**指令碼。

```sql
SELECT BuyingGroup, Count(*) AS Total
FROM dimension_customer
GROUP BY BuyingGroup
```

![](./media/image30.png)

**注意**：如果執行指令碼時發生錯誤，請檢查指令碼語法中是否有多餘的空格。

14. 過去所有 Lakehouse 資料表和檢視都會自動加入語義模型。在最近的更新之後，新的 Lakehouse 需要手動將資料表加入語義模型。

15. 在 Lakehouse 的 **Home** 索引標籤上，選取 **New semantic model**。

![](./media/image31.png)

16. 在 **New semantic model** 對話方塊中輸入 +++wwisemanticmodel+++，從資料表清單中選取 **dimension_customer** 資料表，然後按一下 **Confirm** 建立新模型。

![](./media/image32.png)

### 工作 5：建置報表

1.  在左側導覽窗格中，選取 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**。

![](./media/image33.png)

2.  在工作區中找到 **wwisemanticmodel** 語義模型，選取 **...**（省略號）功能表，然後選取 **Auto-create report**。

![](./media/image34.png)

![](./media/image35.png)

3.  報表準備好後，按一下 **View report now** 開啟並檢閱報表。

![](./media/image36.png)

![](./media/image37.png)

4.  由於此資料表是維度資料表且沒有量值，Power BI 會建立資料列計數量值、在不同資料行間彙總，並產生如上圖所示的各種圖表。

5.  從頂端功能區選取 **Save** 以儲存此報表。

![](./media/image38.png)

6.  在 **Save your report** 對話方塊中，輸入 +++dimension_customer-report+++ 作為名稱，然後按一下 **Save**。

![](./media/image39.png)

7.  你會看到 **Report saved** 的通知。

![](./media/image40.png)

## 練習 2：在 Fabric Lakehouse 中擷取和管理資料

在本練習中，你將使用 Data Factory 管線，把 Wide World Importers (WWI) 範例資料中的其他維度和事實資料表擷取到 Lakehouse。

### 工作 1：擷取資料

1.  在左側導覽窗格中，選取 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**。

![](./media/image41.png)

2.  在工作區頁面上，按一下 **+ New item** 按鈕，然後選取 **Pipeline**。

![](./media/image42.png)

3.  在 **New pipeline** 對話方塊中，輸入 +++IngestDataFromSourceToLakehouse+++ 作為名稱，然後按一下 **Create**。系統會建立並開啟新的 Data Factory 管線。

![](./media/image43.png)

![](./media/image44.png)

4.  在新管線的 **Home** 索引標籤上，選取 **Pipeline activity** \> **Copy data**。

![](./media/image45.png)

5.  選取畫布上新的 **Copy data** 活動。活動屬性會顯示在畫布下方的窗格中，分為 **General**、**Source**、**Destination**、**Mapping** 和 **Settings** 等索引標籤。你可能需要拖曳窗格頂端邊緣，將窗格向上展開。

![](./media/image46.png)

6.  在 **General** 索引標籤的 **Name** 欄位中輸入 +++Data Copy to Lakehouse+++。其他欄位保留預設值。

![](./media/image47.png)

7.  在 **Source** 索引標籤上，選取 **Connection** 下拉式清單，然後選取 **Browse all**。

![](./media/image48.png)

8.  在 **Choose a data source to get started** 頁面上，搜尋並選取 **Azure Blobs**。

![](./media/image49.png)

9.  在 **Connect data source** 頁面上輸入以下詳細資料，然後按一下 **Connect**。所有範例資料都存放在 Azure Blob 儲存體的公用容器中。

| **屬性** | **值** |
|----|----|
| **Account name or URL** | +++https://fabrictutorialdata.blob.core.windows.net/sampledata/+++ |
| **Connection** | **Create new connection** |
| **Connection name** | +++wwisampledata+++ |
| **Authentication kind** | **Anonymous** |

![](./media/image50.png)

10. 在 **Source** 索引標籤上，預設會選取新建立的連線。在設定目的地之前，請先指定以下屬性。

| **屬性** | **值** |
|----|----|
| **Connection** | **wwisampledata** |
| **File path type** | **File path** |
| **File path** | 容器名稱（第一個文字方塊）：+++sampledata+++ <br> 目錄名稱（第二個文字方塊）：+++WideWorldImportersDW/parquet+++ |
| **Recursively** | **已勾選** |
| **File format** | **Binary** |

![](./media/image51.png)

11. 在 **Destination** 索引標籤上，指定以下屬性。

| **屬性** | **值** |
|----|----|
| **Connection** | **wwilakehouse**（如果你的 Lakehouse 使用其他名稱，請選取該 Lakehouse） |
| **Root folder** | **Files** |
| **File path** | 目錄名稱（第一個文字方塊）：+++wwi-raw-data+++ |
| **File format** | **Binary** |

![](./media/image52.png)

12. 按一下 **Run** 執行複製資料活動。

![](./media/image53.png)

13. 按一下 **Save and run** 按鈕，儲存並執行管線。

![](./media/image54.png)

14. 資料複製程序大約需要 1-2 分鐘完成。

![](./media/image55.png)

15. 在 **Output** 索引標籤上，選取 **Data Copy to Lakehouse** 以檢視資料傳輸的詳細資料。確認 **Status** 為 **Succeeded** 後，按一下 **Close** 按鈕。

![](./media/image56.png)

![](./media/image57.png)

16. 管線成功執行後，前往你的 Lakehouse (**wwilakehouse**) 並開啟 Explorer 檢視匯入的資料。

![](./media/image58.png)

17. 重新整理 **Files** 區段以查看擷取的資料。**Files** 區段中會出現新的 **wwi-raw-data** 資料夾，Azure Blob 儲存體中的資料已複製到此處。

![](./media/image59.png)

## 練習 3：在 Lakehouse 中準備和轉換資料

在本練習中，你將匯入 PySpark 筆記本，並使用它從原始資料建立事實、維度和彙總 Delta 資料表。

### 工作 1：匯入筆記本並連結至 Lakehouse

1.  在左側導覽窗格中，選取 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**。

![](./media/image60.png)

2.  在工作區頁面上，按一下命令列中的 **Import** 下拉式清單，然後選取 **New notebook \> From this computer**。

![](./media/image61.png)

3.  在畫面右側開啟的 **Import status** 窗格中，選取 **Upload**。

![](./media/image62.png)

4.  在虛擬機器上瀏覽至 **C:\LabFiles**，選取 **Prepare and transform data – PySpark** 筆記本，然後按一下 **Open** 按鈕。

![](./media/image63.png)

![](./media/image64.png)

5.  選取 **wwilakehouse** Lakehouse 將其開啟，讓接下來開啟的筆記本連結到此 Lakehouse。

![](./media/image65.png)

6.  從工具列選取 **Analyze data with** 下拉式功能表，指向 **Notebook**，然後選取 **Existing notebook**。

![](./media/image66.png)

7.  選取匯入的筆記本 **Prepare and transform data – PySpark**，然後按一下 **Open**。

![](./media/image67.png)

![](./media/image68.png)

### 工作 2：建立 Delta 資料表

在這項工作中，你將執行筆記本儲存格，從原始資料建立 Delta 資料表。這些資料表採用星型結構描述，這是組織分析資料的常見模式：

- **事實資料表** (fact_sale) 包含可衡量的業務事件——在此案例中是個別銷售交易，包括數量、價格和利潤。

- **維度資料表** (dimension_city、dimension_customer、dimension_date、dimension_employee、dimension_stock_item) 包含為事實提供背景資訊的描述性屬性，例如銷售發生的地點、由誰完成以及發生的時間。

1.  **Cell 1 - Spark 工作階段設定。** 此儲存格會啟用兩項 Fabric 功能，最佳化後續儲存格寫入和讀取資料的方式。[V-order](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order) 會最佳化 Parquet 檔案配置，以加快讀取速度並提升壓縮率。[Optimize write](https://learn.microsoft.com/en-us/fabric/data-engineering/tune-file-size#optimize-write) 會減少寫入的檔案數量並增加個別檔案的大小。

```python
spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize", "1073741824")
```

2.  **執行**此儲存格，並等待完成後再進行下一個步驟。

![](./media/image69.png)

![](./media/image70.png)

3.  **Cell 2 - Fact - Sale。** 此儲存格會從 **Files/wwi-raw-data/full/fact_sale_1y_full** 讀取原始 Parquet 資料、新增日期部分資料行（**Year**、**Quarter** 和 **Month**），並將 **fact_sale** 寫入依 **Year** 和 **Quarter** 分割的 Delta 資料表。

4.  **執行**此儲存格，並等待完成後再進行下一個步驟。

![](./media/image71.png)

5.  **Cell 3 - Dimensions。** 此儲存格會讀取五個維度 Parquet 資料集，並將其寫入 Delta 資料表（**dimension_city**、**dimension_customer**、**dimension_date**、**dimension_employee** 和 **dimension_stock_item**）。

6.  **執行**此儲存格，並等待完成後再進行下一個步驟。

![](./media/image72.png)

7.  若要驗證建立的資料表，請在 Explorer 中以滑鼠右鍵按一下 **wwilakehouse** Lakehouse，然後選取 **Refresh**。資料表隨即出現。

![](./media/image73.png)

![](./media/image74.png)

### 工作 3：轉換業務資料以進行彙總

在這項工作中，你將繼續使用同一個筆記本，執行接下來的儲存格，從上一項工作建立的 Delta 資料表建立彙總資料表。

1.  確認筆記本仍連結到 **wwilakehouse**。

2.  **Cell 4 - 載入要轉換的來源資料表。** 執行此儲存格，將 Delta 資料表載入 DataFrame，以供後續彙總步驟使用。等待完成後再進行下一個步驟。

![](./media/image75.png)

3.  **Cell 5 - 建立 aggregate_sale_by_date_city。** 此儲存格會聯結銷售、日期和城市資料，然後建立城市層級的彙總資料表。**執行**此儲存格並等待完成。

![](./media/image76.png)

4.  **Cell 6 - 建立 aggregate_sale_by_date_employee。** 此儲存格會聯結銷售、日期和員工資料，然後建立員工層級的彙總資料表。**執行**此儲存格並等待完成。

![](./media/image77.png)

5.  若要驗證建立的資料表，請在 Explorer 中以滑鼠右鍵按一下 **wwilakehouse** Lakehouse，然後選取 **Refresh**。彙總資料表隨即出現。

![](./media/image78.png)

![](./media/image79.png)

## 練習 4：在 Data Factory 中使用資料流程轉換資料

在本練習中，你將使用 Dataflow Gen2 合併 **fact_sale** 和 **dimension_city** 資料表、新增計算的利潤率資料行，並將結果載入 Lakehouse 中的 Gold 資料表。

### 工作 1：從 Lakehouse 資料表取得資料

1.  在左側導覽窗格中，按一下工作區名稱 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id** 返回工作區檢視。

![](./media/image80.png)

2.  按一下導覽列中的 **+ New item**。從可用項目清單中選取 **Dataflow Gen2**。

![](./media/image81.png)

3.  在 **Name** 欄位中輸入 +++wwi_fact_sale_transform+++，然後按一下 **Create**。

![](./media/image82.png)

4.  在 Power Query 編輯器中，按一下 **Get data** 下拉式清單，然後選取 **More...**

![](./media/image83.png)

5.  在 **Choose data source** 索引標籤上，搜尋 +++Lakehouse+++ 並按一下 **Lakehouse** 連接器。

![](./media/image84.png)

6.  隨即出現 **Connect to data source** 對話方塊。系統會根據你登入的使用者自動建立新連線。按一下 **Next**。

![](./media/image85.png)

7.  隨即顯示 **Choose data** 對話方塊。展開 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id** 工作區，展開 **wwilakehouse** Lakehouse，然後選取 **fact_sale** 資料表。按一下 **Create**。

![](./media/image86.png)

![](./media/image87.png)

8.  畫布現在已填入 **fact_sale** 資料。

![](./media/image88.png)

### 工作 2：轉換從 Lakehouse 匯入的資料

1.  選取 **InvoiceDateKey** 資料行標題中的資料類型圖示以顯示下拉式功能表，將資料行從 **Date/Time** 變更為 **Date** 類型。

![](./media/image89.png)

2.  在功能區的 **Home** 索引標籤上，於 **Manage columns** 群組中選取 **Choose columns** \> **Choose columns**。

![](./media/image90.png)

3.  在 **Choose columns** 對話方塊中，取消選取 **TaxRate** 資料行，然後按一下 **OK**。

![](./media/image91.png)

4.  選取 **InvoiceDateKey** 資料行的排序和篩選下拉式功能表，選取 **Date filters**，然後選擇 **Between...**

![](./media/image92.png)

5.  在 **Filter rows** 對話方塊中，選取 **2000 年 1 月 1 日**到 **2000 年 1 月 31 日**之間的日期，然後按一下 **OK**。

![](./media/image93.png)

![](./media/image94.png)

### 工作 3：連線至 dimension_city 資料表

你將載入在練習 3 中建立的 **dimension_city** 資料表，以城市和銷售區域資訊擴充 fact_sale 資料。

1.  在資料流程編輯器的 **Home** 索引標籤上，選取 **Get data**，然後選擇 **More...**

![](./media/image95.png)

2.  在 **Choose data source** 索引標籤上，搜尋 +++Lakehouse+++ 並按一下 **Lakehouse** 連接器。

![](./media/image96.png)

3.  隨即出現 **Connect to data source** 對話方塊。按一下 **Next**。

![](./media/image97.png)

4.  在 **Choose data** 對話方塊中，展開 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id** 工作區，展開 **wwilakehouse** Lakehouse，然後選取 **dimension_city** 資料表。按一下 **Create**。

![](./media/image98.png)

5.  Power Query 編輯器中現在會出現第二個查詢 **dimension_city**。

![](./media/image99.png)

### 工作 4：轉換 dimension_city 資料

1.  確認已在 **Queries** 窗格中選取 **dimension_city** 查詢。

2.  在功能區的 **Home** 索引標籤上，從 **Manage columns** 群組選取 **Choose columns**。

![](./media/image100.png)

3.  在 **Choose columns** 對話方塊中，只保留以下資料行（取消選取其他所有資料行），然後按一下 **OK**：

- **CityKey**

- **City**

- **StateProvince**

- **SalesTerritory**

![](./media/image101.png)

4.  選取 **SalesTerritory** 資料行的篩選和排序下拉式清單。取消選取 **(blank)** 或 **(null)** 項目，以移除沒有銷售區域的資料列，然後按一下 **OK**。

![](./media/image102.png)

![](./media/image103.png)

### 工作 5：合併 fact_sale 和 dimension_city 資料

下一步是將兩個資料表合併為單一資料表，為每筆銷售加入城市和銷售區域的背景資訊，並新增計算的利潤率資料行。

1.  切換 **Diagram view** 按鈕，以便同時看到兩個查詢。

![](./media/image104.png)

2.  選取 **fact_sale** 查詢。在 **Home** 索引標籤上，選取 **Combine** 功能表，選擇 **Merge queries**，然後選取 **Merge queries as new**。

![](./media/image105.png)

3.  在 **Merge** 對話方塊中，選取 **dimension_city** 作為右側資料表，接受建議的 **CityKey** 對應至 **CityKey**，確認聯結類型已選取 **Left outer**，然後按一下 **OK**。

![](./media/image106.png)

![](./media/image107.png)

![](./media/image108.png)

4.  展開 **dimension_city** 資料行，清除 **CityKey** 核取方塊，保留其餘資料行為選取狀態，然後按一下 **OK**。

![](./media/image109.png)

![](./media/image110.png)

5.  若要新增計算資料行，請選取 **Add column** 索引標籤，然後從 **General** 群組選擇 **Custom column**。

![](./media/image111.png)

6.  在 **Custom column** 對話方塊中，依下列方式設定新資料行，然後按一下 **OK**。

| **屬性** | **值** |
|----|----|
| **New column name** | +++ProfitMargin+++ |
| **Data type** | **Currency** |
| **Custom column formula** | +++if [TotalIncludingTax] > 0 then [Profit] / [TotalIncludingTax] else 0+++ |

![](./media/image112.png)

![](./media/image113.png)

7.  選取新的 **ProfitMargin** 資料行。然後選取 **Transform** 索引標籤，在 **Number column** 群組中選取 **Rounding** 下拉式清單，並選擇 **Round...**

![](./media/image114.png)

8.  在 **Round** 對話方塊中，輸入 **4** 作為小數位數，然後按一下 **OK**。

![](./media/image115.png)

![](./media/image116.png)

9.  將 **InvoiceDateKey** 資料行的資料類型從 **Date** 改回 **Date/Time**。

![](./media/image117.png)

![](./media/image118.png)

10. 展開編輯器右側的 **Query settings** 窗格，將查詢名稱從 **Merge** 重新命名為 +++Output+++。

![](./media/image119.png)

**注意：** ProfitMargin = Profit / TotalIncludingTax。值為 0.35 表示該筆銷售的利潤率為 35%。四捨五入至小數點後 4 位，以符合報表精確度。

### 工作 6：將 Output 查詢載入 Lakehouse 中的 Gold 資料表

Output 查詢準備好之後，接著定義輸出目的地。

1.  選取 **Output** 查詢，然後選取 **+** 圖示，為此資料流程新增資料目的地。

2.  從資料目的地清單中，選取 **New destination** 下的 **Lakehouse**。

![](./media/image120.png)

3.  在 **Connect to data destination** 對話方塊中，應已選取你的連線。按一下 **Next**。

![](./media/image121.png)

4.  在 **Choose destination target** 下，選取 **New table**，瀏覽至 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id** 工作區下的 **wwilakehouse**，輸入 +++Gold_Sales_By_City+++ 作為資料表名稱，然後按一下 **Next**。

![](./media/image122.png)

5.  按一下 **Save settings**。

![](./media/image123.png)

6.  回到主要編輯器視窗，確認 **Query settings** 窗格顯示 **Lakehouse** 為 Output 查詢的輸出目的地。

![](./media/image124.png)

7.  從 **Home** 索引標籤選取 **Save and run**。

![](./media/image125.png)

8.  等待資料流程執行完成（大約 2-3 分鐘）。

![](./media/image126.png)

9.  返回 **wwilakehouse**，在 **Tables** 區段按一下 **Refresh**。確認 **Gold_Sales_By_City** 資料表現在出現在 **Explorer** 窗格的 **Tables** 下。

![](./media/image127.png)

![](./media/image128.png)

![](./media/image129.png)

10. 按一下 **Gold_Sales_By_City** 資料表進行預覽，並確認其中包含 **City**、**StateProvince**、**SalesTerritory**、**Profit**、**TotalIncludingTax** 和 **ProfitMargin** 資料行。

![](./media/image130.png)

## 練習 5：使用 Data Factory 自動化並傳送通知

在本練習中，你將為管線新增電子郵件通知、設定排程，並將資料流程新增為活動，讓整個流程可以端對端執行。

### 工作 1：將 Office 365 Outlook 活動新增至管線

1.  在左側導覽功能表中，按一下 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id** 工作區。

2.  在工作區頁面上，選取 **IngestDataFromSourceToLakehouse** 管線。

![](./media/image131.png)

3.  選取管線編輯器中的 **Activities** 索引標籤，然後選取 **Office 365 Outlook** 活動。

![](./media/image132.png)

4.  選取並拖曳 Copy 活動的 **On success** 路徑（Copy 活動右側的綠色核取方塊），將它連接到新的 Office 365 Outlook 活動。

![](./media/image133.png)

5.  選取管線畫布上的 Office 365 Outlook 活動，然後選取畫布下方屬性區域的 **Settings** 索引標籤。按一下 **Connection** 下拉式清單，然後選取 **Browse all**。

![](./media/image134.png)

6.  在 **Choose a data source** 視窗中，選取 **Office 365 Email** 來源。

![](./media/image135.png)

7.  使用你要用來傳送電子郵件的帳戶登入。你可以使用已登入帳戶的現有連線。

8.  按一下 **Connect** 繼續。

![](./media/image136.png)

9.  選取管線畫布上的 Office 365 Outlook 活動。在 **Settings** 索引標籤上設定電子郵件。

10. 在 **To** 欄位中輸入你的電子郵件地址。若要使用多個地址，請以 **;** 分隔。

![](./media/image137.png)

11. 針對 **Subject**，選取該欄位讓 **Add dynamic content** 選項出現，然後選取它以開啟管線運算式產生器。

![](./media/image138.png)

12. 在 **Pipeline expression builder** 對話方塊中輸入以下運算式，然後按一下 **OK**。

```text
@concat('WWI Data Pipeline Succeeded with Pipeline Run Id: ', pipeline().RunId)
```

![](./media/image139.png)

13. 針對 **Body**，再次選取該欄位，並在文字區域下方出現 **View in expression builder** 選項時選取它。輸入以下運算式，然後按一下 **OK**。

```text
@concat('RunID = ', pipeline().RunId, ' ; ', 'Files Written: ', activity('Data Copy to Lakehouse').output.filesWritten, ' ; ', 'Throughput: ', activity('Data Copy to Lakehouse').output.throughput)
```

![](./media/image140.png)

![](./media/image141.png)

**注意：** 如果你的複製活動使用其他名稱，請將 **Data Copy to Lakehouse** 替換為實際的活動名稱。

14. 選取管線編輯器頂端的 **Home** 索引標籤，然後選擇 **Run**。接著在確認對話方塊中選取 **Save and run**，以執行這些活動。

![](./media/image142.png)

![](./media/image143.png)

15. 管線成功執行後，檢查你的電子郵件，找到管線傳送的確認郵件。

![](./media/image144.png)

![](./media/image145.png)

### 工作 2：排程管線執行

完成管線的開發和測試後，你可以排程讓它自動執行。

1.  在管線編輯器視窗的 **Home** 索引標籤上，選取 **Schedule**。

![](./media/image146.png)

![](./media/image147.png)

2.  視需要設定排程。以下範例將管線排程為每天晚上 8:00 執行，直到年底。

| **屬性** | **值** |
|----|----|
| **Repeat** | **Daily** |
| **Time** | **8:00 PM** |
| **End date** | 今年 12 月 31 日 |
| **Time zone** | 選取你的當地時區 |

![](./media/image148.png)

3.  按一下 **Close** 關閉排程。

![](./media/image149.png)

### 工作 3：將 Dataflow 活動新增至管線

1.  在 **Activities** 索引標籤上，將 **Dataflow** 活動拖放到管線畫布上。

![](./media/image150.png)

2.  從出現的功能表中選擇 **Dataflow**。

3.  新的 Dataflow 活動會插入到 Copy 活動和 Office 365 Outlook 活動之間，並自動選取，其屬性會顯示在畫布下方的區域。

![](./media/image151.png)

4.  選取 **Settings** 索引標籤，然後選取你在練習 4 中建立的 **wwi_fact_sale_transform** 資料流程。

![](./media/image152.png)

5.  選取管線編輯器頂端的 **Home** 索引標籤，然後選擇 **Run**。接著在確認對話方塊中選取 **Save and run**，以執行這些活動。

![](./media/image153.png)

![](./media/image154.png)

6.  監視 **Output** 索引標籤，確認三個活動（**Copy data**、**Dataflow**、**Office 365 Outlook**）都以 **Status: Succeeded** 完成。

![](./media/image155.png)

![](./media/image156.png)

![](./media/image157.png)

## 練習 6：在 Microsoft Fabric 中建置報表

在本練習中，你將把所有資料表加入 Direct Lake 語義模型、在資料表之間建立關聯性，並從頭開始建置 Power BI 報表。

### 工作 1：在 Direct Lake 語義模型中建立關聯性

Power BI 原生整合在整個 Fabric 體驗中。這項原生整合帶來一種稱為 **Direct Lake** 的獨特模式，可存取 Lakehouse 中的資料，提供最高效能的查詢和報表體驗。Direct Lake 直接從資料湖載入 Parquet 格式的檔案，不需要查詢資料倉儲或 Lakehouse 端點，也不需要將資料匯入或複製到 Power BI 語義模型。

在傳統的 **DirectQuery** 模式中，Power BI 引擎會針對每個查詢直接從來源查詢資料，查詢效能取決於資料擷取速度。DirectQuery 免除了複製資料的需求，確保來源的任何變更都能立即反映在查詢結果中。在 **Import** 模式中，由於資料已存放在記憶體中，效能較佳，但 Power BI 引擎必須在資料重新整理時先將資料複製到記憶體中，而來源的變更只會在下次重新整理時才會被擷取。

Direct Lake 直接將資料檔案載入記憶體，免除了這項匯入需求。由於沒有明確的匯入程序，它可以在來源發生變更時即時擷取變更，結合了 DirectQuery 和 Import 模式的優點，同時避免其缺點。因此，Direct Lake 是分析超大型資料集以及來源經常更新之資料集的理想選擇。

1.  從左側功能表中選取 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id** 工作區，然後選取名為 **wwisemanticmodel** 的語義模型。

![](./media/image158.png)

2.  開啟語義模型，選取右上角的模式下拉式清單，從 **Viewing** 切換為 **Editing**，然後選取 **Make any changes**。

![](./media/image159.png)

3.  在功能區中選取 **Edit tables**，以顯示資料表同步對話方塊。

![](./media/image160.png)

4.  在 **Edit semantic model** 對話方塊中**選取所有**資料表，然後按一下對話方塊底部的 **Confirm**，以同步語義模型。

![](./media/image161.png)

![](./media/image162.png)

5.  從 **fact_sale** 資料表中拖曳 **CityKey** 欄位，放到 **dimension_city** 資料表的 **CityKey** 欄位上以建立關聯性。隨即出現 **Create Relationship** 對話方塊。

**注意：** 按一下並拖曳資料表來重新排列，讓 **dimension_city** 和 **fact_sale** 資料表彼此相鄰。在任兩個要建立關聯性的資料表之間都可以這樣做，讓資料行在資料表之間的拖放更容易。

![](./media/image163.png)

6.  在 **Create Relationship** 對話方塊中：

- **Table 1** 已填入 **fact_sale** 和 **CityKey** 資料行。

- **Table 2** 已填入 **dimension_city** 和 **CityKey** 資料行。

- **Cardinality**：**Many to one (\*:1)**

- **Cross filter direction**：**Single**

- 保持選取 **Make this relationship active** 旁邊的方塊。

- 選取 **Assume referential integrity** 旁邊的方塊。

- 按一下 **Save**。

![](./media/image164.png)

7.  使用與上述相同的 **Create Relationship** 設定，新增以下關聯性：

| **來源 (fact_sale)** | **目標** |
|----|----|
| **StockItemKey** | **StockItemKey** (dimension_stock_item) |
| **SalespersonKey** | **EmployeeKey** (dimension_employee) |
| **CustomerKey** | **CustomerKey** (dimension_customer) |
| **InvoiceDateKey** | **Date** (dimension_date) |

![](./media/image165.png)

![](./media/image166.png)

![](./media/image167.png)

8.  新增這些關聯性之後，你的資料模型應如下圖所示，並可用於報表。

![](./media/image168.png)

### 工作 2：建置報表

1.  從頂端功能區選取 **File**，然後選取 **Create new report**，開始在 Power BI 中建立報表。

![](./media/image169.png)

2.  在 Power BI 報表畫布上，你可以將 **Data** 窗格中的資料行拖曳到畫布上，並使用一或多個可用的視覺效果，建立符合業務需求的報表。

![](./media/image170.png)

**新增標題：**

3.  在功能區中選取 **Text box**。輸入 +++WW Importers Profit Reporting+++，反白文字，並將字型大小增加為 **20**。

![](./media/image171.png)

4.  調整文字方塊大小，將其放在報表頁面的**左上角**，然後按一下文字方塊外部。

![](./media/image172.png)

**新增卡片：**

5.  在 **Data** 窗格中展開 **fact_sale**，並勾選 **Profit** 旁邊的方塊。此選取會建立直條圖，並將欄位新增至 Y 軸。

![](./media/image173.png)

6.  選取圖表後，在 **Visualizations** 窗格中選取 **Card** 視覺效果。

![](./media/image174.png)

7.  此選取會將視覺效果轉換為卡片。將卡片放在標題下方。

![](./media/image175.png)

8.  按一下空白畫布上的任何位置（或按 **Esc** 鍵），讓卡片不再處於選取狀態。

**新增橫條圖：**

9.  在 **Data** 窗格中展開 **fact_sale**，並勾選 **Profit** 旁邊的方塊。此選取會建立直條圖，並將欄位新增至 Y 軸。

![](./media/image176.png)

10. 在 **Data** 窗格中展開 **dimension_city**，並勾選 **SalesTerritory** 的方塊。此選取會將欄位新增至 X 軸。

![](./media/image177.png)

11. 選取圖表後，在 **Visualizations** 窗格中選取 **Clustered bar chart** 視覺效果。此選取會將直條圖轉換為橫條圖。

![](./media/image178.png)

12. 調整橫條圖大小，填滿標題和卡片下方的區域。

![](./media/image179.png)

13. 按一下空白畫布上的任何位置（或按 **Esc** 鍵），讓橫條圖不再處於選取狀態。

**建置堆疊區域圖視覺效果：**

14. 在 **Visualizations** 窗格中，選取 **Stacked area chart** 視覺效果。

![](./media/image180.png)

15. 將堆疊區域圖重新放置並調整大小，放在卡片和橫條圖視覺效果的右側。

![](./media/image181.png)

16. 在 **Data** 窗格中展開 **fact_sale**，並勾選 **Profit** 旁邊的方塊。展開 **dimension_date**，並勾選 **FiscalMonthNumber** 旁邊的方塊。此選取會建立依會計月份顯示利潤的填滿折線圖。

![](./media/image182.png)

17. 在 **Data** 窗格中展開 **dimension_stock_item**，並將 **BuyingPackage** 拖曳到 **Legend** 欄位。此選取會為每個購買套件新增一條線。

![](./media/image183.png)

![](./media/image184.png)

18. 按一下空白畫布上的任何位置（或按 **Esc** 鍵），讓堆疊區域圖不再處於選取狀態。

**建置直條圖：**

19. 在 **Visualizations** 窗格中，選取 **Stacked column chart** 視覺效果。

![](./media/image185.png)

20. 在 **Data** 窗格中展開 **fact_sale**，並勾選 **Profit** 旁邊的方塊。此選取會將欄位新增至 Y 軸。

21. 在 **Data** 窗格中展開 **dimension_employee**，並勾選 **Employee** 旁邊的方塊。此選取會將欄位新增至 X 軸。

![](./media/image186.png)

22. 按一下空白畫布上的任何位置（或按 **Esc** 鍵），讓圖表不再處於選取狀態。

23. 從功能區選取 **File** \> **Save**。

![](./media/image187.png)

24. 輸入 +++Profit Reporting+++ 作為報表名稱，然後按一下 **Save**。

![](./media/image188.png)

25. 你會收到報表已儲存的通知。

![](./media/image189.png)

## 練習 7：清除資源

你可以刪除個別報表、管線、倉儲和其他項目，或移除整個工作區。請使用以下步驟刪除你為本實驗建立的工作區。

1.  從左側導覽功能表中選取你的工作區 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**，隨即開啟工作區項目檢視。

2.  選取工作區名稱下的 **...** 選項，然後選取 **Workspace settings**。

![](./media/image190.png)

3.  選取 **General**，然後選取 **Remove this workspace**。

![](./media/image191.png)

4.  在彈出的警告中按一下 **Delete**。

![](./media/image192.png)

5.  等待工作區已刪除的通知出現後，再繼續進行下一個實驗。

![](./media/image193.png)

**摘要**

在本實驗中，你實作了完整的 Microsoft Fabric 資料工程工作流程。你建立了 Fabric 工作區和 Lakehouse、上傳來源資料並載入 Delta 資料表、使用 SQL 查詢驗證資料，並建置了快速報表。接著，你使用 Data Factory 管線擷取 Wide World Importers 範例資料、使用 PySpark 筆記本建立事實、維度和彙總 Delta 資料表，並使用 Dataflow Gen2 建置具有計算利潤率的 Gold 資料表。你透過 Office 365 Outlook 通知、排程和 Dataflow 活動將管線自動化。最後，你建置了具有關聯性的 Direct Lake 語義模型，並建立了 Power BI 報表。這些技能為使用 Microsoft Fabric 開發可擴充的資料工程解決方案奠定了基礎。
