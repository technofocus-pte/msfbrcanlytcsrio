# 使用案例 04：從語意到洞察：搭配 Fabric 資料代理程式使用 Fabric IQ Ontology

**案例情境**

**Lakeshore Retail** 是一家虛構的公司，在多個門市地點銷售冰淇淋。銷售資料、產品詳細資料和門市資訊儲存在 Lakehouse 中，而冷凍櫃感應器則會將溫度和濕度讀數串流到 Eventhouse。目前，業務使用者必須知道要聯結哪些資料表、查詢哪些系統，才能回答簡單的跨領域問題，例如：*「當冷凍櫃溫度升高到 -18 °C 以上時，哪些門市的冰淇淋銷量較低？」*

Lakeshore Retail 希望建立一個**以業務為中心的語意層**，以自己的業務用語（門市、產品、銷售事件和冷凍櫃）描述其業務，並將這些概念連結到底層資料。有了這一層，分析師就能以視覺化方式探索關聯性，並以自然語言提問，而不需要了解底層的資料表或結構描述。

身為資料工程師，您將準備 Fabric 工作區、載入銷售和遙測資料、使用 **Fabric IQ Ontology（預覽）** 建置 ontology、透過圖形查詢探索 ontology、將其連線至 **Fabric 資料代理程式**以進行自然語言提問，並使用 **Project Rayfin** 建置和部署配套應用程式。

**簡介**

在現代資料平台中，企業通常需要一個**以業務為中心的語意層**，以統一不同資料來源和分析模型之間的意義。Microsoft Fabric IQ 中的 **Ontology（預覽）** 功能可讓您定義**企業概念**（例如產品、門市和事件）及其**關聯性**，並將這些定義繫結到 Lakehouse、語意模型和事件串流中的實際資料，以建置這個語意層。

在本實驗中，您將使用範例資料建置 ontology，涵蓋 *Store*、*Products*、*SaleEvent* 和 *Freezer* 等業務概念。您也會將串流資料（來自 Eventhouse 的冷凍櫃遙測資料）連線到這些概念，讓 ontology 能夠支援**跨領域推理和查詢**。

**本實驗建立的 Fabric 項目**

| **項目** | **名稱** | **在實驗中的用途** |
|----|----|----|
| 工作區 | Fabric IQ OntologyXXXX\<實驗執行個體 ID\> | 包含本實驗的所有項目 |
| Lakehouse | IQ_Lakehouse | 儲存產品、門市、銷售和冷凍櫃資料表 |
| Eventhouse / KQL 資料庫 | TelemetryDataEH | 儲存 FreezerTelemetry 時間序列資料 |
| Ontology（預覽） | RetailSalesOntology | 定義 Store、Products、SaleEvent 和 Freezer 實體類型及其關聯性 |
| 資料代理程式（預覽） | RetailOntologyAgent | 以 ontology 為基礎回答自然語言問題 |
| Fabric data app + SQL Database | Fabricapp | 使用 Project Rayfin 建置和部署的配套 Todo 應用程式 |

**目標**：

- 準備包含必要項目的 Microsoft Fabric 工作區，包括 Lakehouse、Eventhouse 和 Ontology（預覽）。

- 定義 Store、Products、SaleEvent 和 Freezer 等核心實體類型，建置以業務為中心的 ontology。

- 將 OneLake 資料表的靜態資料和 Eventhouse 的時間序列資料繫結到 ontology 實體。

- 在實體之間建立代表實際業務流程的關聯性（例如 SaleEvent from Store 和 Store operates Freezer）。

- 使用實體執行個體、關聯性圖形和查詢產生器篩選條件，探索和驗證 ontology。

- 將 ontology 與 Fabric 資料代理程式（預覽）整合，以啟用自然語言查詢。

- 使用 Project Rayfin 建置、測試配套應用程式，並將其部署到 Fabric。

**注意：** 本實驗的螢幕擷取畫面使用英文介面，因此步驟中的介面名稱（例如 **+ New workspace**、**Apply**）保留英文，方便您對照畫面操作。

## 練習 1：環境設定

在本練習中，您將建立 Fabric 工作區、將範例銷售資料載入 Lakehouse，並將冷凍櫃遙測資料上傳到 Eventhouse。

### 任務 1：建立 Fabric 工作區

在此任務中，您將建立 Fabric 工作區。工作區包含本實驗所需的所有項目，包括 Lakehouse、Eventhouse、ontology 和資料代理程式。

1.  開啟瀏覽器，在網址列中輸入或貼上以下 URL：+++https://app.fabric.microsoft.com/+++，然後按 **Enter** 鍵，並使用以下認證登入。

| **使用者名稱** | **+++@lab.CloudPortalCredential(User1).Username+++** |
|----|----|
| **密碼** | **+++@lab.CloudPortalCredential(User1).Password+++** |

2.  在 **Workspaces** 窗格中，按一下 **+ New workspace** 圖格。

![](./media/image1.png)

3.  在右側出現的 **Create a workspace** 窗格中，輸入以下詳細資料，然後按一下 **Apply** 按鈕。

| **設定** | **值** |
|----|----|
| **Name** | +++Fabric IQ Ontology@lab.LabInstance.Id+++ |
| **Advanced** | 在 **License mode** 下選取 **Fabric capacity** |
| **Default storage format** | **Small dataset storage format** |

![](./media/image2.png)

![](./media/image3.png)

![](./media/image4.png)

### 任務 2：建立 Lakehouse

1.  按一下導覽列中的 **+ New item** 按鈕，建立新的 Lakehouse。

![](./media/image5.png)

2.  篩選 +++Lakehouse+++ 並選取 **Lakehouse** 圖格。

![](./media/image6.png)

3.  在 **New lakehouse** 對話方塊的 **Name** 欄位中輸入 +++IQ_Lakehouse+++，並**取消選取** **Lakehouse schemas**。按一下 **Create** 按鈕，並開啟新的 Lakehouse。

![](./media/image7.png)

![](./media/image8.png)

4.  您會看到 **Successfully created SQL endpoint** 的通知。

![](./media/image9.png)

### 任務 3：擷取範例資料

1.  在 **IQ_Lakehouse** 頁面上，前往 **Get data in your lakehouse** 區段，然後按一下 **Upload files**。

![](./media/image10.png)

2.  在 **Upload files** 索引標籤上，按一下 **Files** 下方的資料夾圖示。

![](./media/image11.png)

3.  在虛擬機器上瀏覽至 **C:\LabFiles\Lab1**，選取 **DimProducts.csv**、**DimStore.csv**、**FactSales.csv** 和 **Freezer.csv** 檔案，然後按一下 **Open** 按鈕。

![](./media/image12.png)

4.  按一下 **Upload** 按鈕，然後選取 **X** 圖示關閉 **Upload files** 對話方塊。

![](./media/image13.png)

![](./media/image14.png)

5.  選取 **Files**，檔案會出現在 Files 窗格中。

![](./media/image15.png)

6.  在 **Explorer** 窗格中選取 **Files**。將滑鼠游標停留在 **DimProducts.csv** 檔案上，按一下旁邊的水平省略號 **(…)**，按一下 **Load Table**，然後選取 **New table**。

![](./media/image16.png)

![](./media/image17.png)

7.  在 **Load file to new table** 對話方塊中，按一下 **Load** 按鈕。

![](./media/image18.png)

8.  **DimProducts** 資料表已成功建立。

![](./media/image19.png)

9.  選取 **DimProducts** 資料表以預覽資料。

**注意：** 您可能需要按一下 **Refresh** 按鈕多次，才能預覽資料。

![](./media/image20.png)

10. 重複步驟 6 到 9，將其餘檔案（**DimStore.csv**、**FactSales.csv** 和 **Freezer.csv**）載入資料表。

![](./media/image21.png)

![](./media/image22.png)

![](./media/image23.png)

![](./media/image24.png)

![](./media/image25.png)

![](./media/image26.png)

![](./media/image27.png)

11. 從左側導覽列中，選取 **Fabric IQ Ontology@lab.LabInstance.Id** 工作區。

![](./media/image28.png)

### 任務 4：準備 Eventhouse

請依照以下步驟，將裝置串流資料檔案上傳到 Eventhouse 中的 KQL 資料庫。

1.  在工作區頁面上，選取 **+ New item**，然後選取 **Eventhouse**。

![](./media/image29.png)

2.  將 Eventhouse 命名為 +++TelemetryDataEH+++，然後按一下 **Create** 按鈕。

![](./media/image30.png)

3.  Eventhouse 準備就緒後會自動開啟。

![](./media/image31.png)

4.  選取 KQL 資料庫的名稱以開啟它。

![](./media/image32.png)

![](./media/image33.png)

5.  在 **KQL database** 的下方功能區中，按一下 **Get data**，然後選取 **Local file**，從本機系統將檔案上傳到資料庫。

![](./media/image34.png)

6.  選取將資料擷取到新資料表的選項，按一下 **+ New table**，然後輸入 +++FreezerTelemetry+++ 作為資料表名稱。

![](./media/image35.png)

![](./media/image36.png)

7.  選取目的地資料表，然後拖放檔案，或按一下 **Browse for files** 上傳資料。

![](./media/image37.png)

8.  在虛擬機器上瀏覽至 **C:\LabFiles\Lab1**，選取 **FreezerTelemetry.csv** 檔案，然後按一下 **Open** 按鈕。

![](./media/image38.png)

9.  按一下 **Next** 按鈕。

![](./media/image39.png)

10. 按一下 **Finish** 按鈕。

![](./media/image40.png)

11. 等待資料擷取完成，然後按一下 **Close**。

![](./media/image41.png)

12. 完成後，KQL 資料庫會顯示 **FreezerTelemetry** 資料表。

![](./media/image42.png)

13. 在左側導覽窗格中，選取 **Fabric IQ Ontology@lab.LabInstance.Id** 工作區。

![](./media/image43.png)

## 練習 2：從 OneLake 建置 ontology

在本練習中，您將建立 ontology 項目、新增 Store、Products 和 SaleEvent 實體類型、將它們繫結到 Lakehouse 資料表，並在它們之間建立關聯性。

### 任務 1：建立 Ontology（預覽）項目

1.  在您的 Fabric 工作區中，選取 **+ New item**。搜尋並選取 **Ontology (preview)** 項目。

![](./media/image44.png)

2.  輸入 +++RetailSalesOntology+++ 作為 ontology 的 **Name**，然後按一下 **Create**。

![](./media/image45.png)

**提示：** Ontology 名稱可以包含數字、字母和底線，但不可使用空格或破折號。

3.  Ontology 準備就緒後會自動開啟。

![](./media/image46.png)

接下來，您將根據 Lakehouse 資料表中的資料，建立實體類型、資料繫結和關聯性。

### 任務 2：建立實體類型和資料繫結

首先，建立實體類型。實體類型代表業務中的物件類型。此任務包含三種實體類型：*Store*、*Products* 和 *SaleEvent*。建立實體類型後，您將繫結 **IQ_Lakehouse** Lakehouse 資料表中的來源資料行，以建立它們的屬性。

**新增第一個實體類型 (Store)**

1.  從頂端功能區或設定畫布的中央，選取 **Add entity type**。

![](./media/image47.png)

2.  輸入 +++Store+++ 作為實體類型的名稱，然後選取 **Add Entity Type**。

![](./media/image48.png)

3.  *Store* 實體類型會新增到設定畫布上，並顯示 **Entity type configuration** 窗格。

![](./media/image49.png)

4.  在設定畫布上，選取實體名稱旁的 **...**，然後選取 **Bind data**。

![](./media/image50.png)

5.  選取 **Add data binding \> Lakehouse table**。

![](./media/image51.png)

6.  選擇資料來源。選取 **IQ_Lakehouse** Lakehouse，然後按一下 **Next**。

![](./media/image52.png)

7.  選取 **dimstore** 資料表，然後按一下 **Select**。

![](./media/image53.png)

8.  來源資料表中的欄位會填入資料繫結設定。請觀察設定頁面的各個區段：

- **Entity type key**：識別可用來唯一識別每筆擷取資料記錄的欄位。

- **Binding selection**：識別保存繫結資料的來源資料表。

- **Entity type key mapping**：識別來源資料表中對應到實體類型索引鍵屬性的資料行。您可以選取來源資料中的字串和整數資料行作為實體類型索引鍵，所選的資料行會共同唯一識別一筆記錄。

- **Properties**：列出來源資料中將作為 *Store* 實體類型屬性的資料行。**Source column** 一側會自動填入 *dimstore* 資料表的資料行，**Property name** 一側則列出 *Store* 實體類型中對應的屬性名稱。在本實驗中，請保留預設的屬性名稱。

![](./media/image54.png)

9.  選取設定頂端的 **Define entity type key**。

![](./media/image55.png)

10. 從屬性清單中選取 **StoreId**，然後按一下 **Save**。

![](./media/image56.png)

11. **Save** 資料繫結。

![](./media/image57.png)

![](./media/image58.png)

12. 確認實體類型已成功更新，然後選取 **Cancel** 關閉設定選項。

![](./media/image59.png)

13. 您會看到實體類型詳細資料的 **Configure** 頁面。此頁面會顯示實體類型的重要資訊，包括其屬性和資料繫結。檢視您設定的資料繫結。

![](./media/image60.png)

14. 選取 **Home** 返回設定畫布，以新增其他實體類型。

![](./media/image61.png)

**新增其他實體類型 (Products、SaleEvent)**

15. 依照建立 **Store** 實體類型的相同步驟，建立下表所述的實體類型。每個實體都有一個靜態資料繫結，使用其來源資料表的預設資料行。請先建立 **Products**。

| **實體類型名稱** | **IQ_Lakehouse 中的來源資料表** | **實體類型索引鍵** |
|----|----|----|
| +++Products+++ | **dimproducts** | **ProductId** |
| +++SaleEvent+++ | **factsales** | **SaleId** |

**注意：** 請使用複數形式 **Products**，以避免與 GQL 保留字 **PRODUCT** 衝突。

![](./media/image62.png)

![](./media/image63.png)

![](./media/image64.png)

![](./media/image65.png)

![](./media/image66.png)

![](./media/image67.png)

![](./media/image68.png)

![](./media/image69.png)

![](./media/image70.png)

![](./media/image71.png)

![](./media/image72.png)

16. 選取 **Home** 返回設定畫布，並新增 **SaleEvent** 實體類型。

![](./media/image73.png)

![](./media/image74.png)

![](./media/image75.png)

![](./media/image76.png)

![](./media/image77.png)

![](./media/image78.png)

![](./media/image79.png)

![](./media/image80.png)

![](./media/image81.png)

![](./media/image82.png)

![](./media/image83.png)

![](./media/image84.png)

17. 完成後，您會在 **Entity Types** 窗格中看到這些實體類型。

![](./media/image85.png)

### 任務 3：建立關聯性類型

接下來，在實體類型之間建立關聯性類型，以表示資料中的情境連結。

**SaleEvent from Store**

1.  從 **Explorer** 中選取 **SaleEvent** 實體類型。

![](./media/image86.png)

2.  從功能區選取 **Add relationship**。

![](./media/image87.png)

3.  輸入以下關聯性類型詳細資料，然後選取 **Add relationship type**。

| **Relationship type name** | +++from+++ |
|----|----|
| **Source entity type** | **SaleEvent** |
| **Target entity type** | **Store** |

![](./media/image88.png)

![](./media/image89.png)

4.  關聯性會新增到畫布上。選取它以開啟關聯性詳細資料設定。請觀察設定頁面的各個區段：

- **Origin entity type**：列出來源實體的詳細資料（此處為 **SaleEvent**）。

- **Relationship type**：設定關聯性類型的詳細資料。

- **Target entity type**：列出目標實體的詳細資料（此處為 **Store**）。

![](./media/image90.png)

![](./media/image91.png)

5.  在中間區段的 **Mapping table** 中，選取 **Browse available sources**，然後選取 **factsales** 資料表。此資料表包含兩種實體類型的識別資訊，因此可以將 *Store* 和 *SaleEvent* 實體連結在一起。資料表中的每一列都會依 ID 參考一個門市和一個銷售事件。

![](./media/image92.png)

6.  在 **Matched SaleEvent: SaleId** 中，選取 **SaleId**。此設定指定關聯性來源資料表中，其值與 *SaleEvent* 實體所定義之索引鍵屬性相符的資料行。在此案例中，關聯性資料來源和實體資料來源都使用 *factsales* 資料表，因此您選取的是相同的資料行 (SaleId)。

7.  在 **Matched Store: StoreId** 中，選取 **StoreId**。此設定指定關聯性來源資料表（*factsales \> StoreId*）中，其值與 *Store* 實體所定義之索引鍵屬性（*dimstore \> StoreId*）相符的資料行。在實驗資料中，兩個資料表的資料行名稱 (StoreId) 相同。

![](./media/image93.png)

**重要：** 請務必選取與實體類型索引鍵屬性相符的正確 **Matched** 資料行。

8.  **Save** 關聯性類型。確認關聯性類型已成功更新，然後選取 **Cancel** 關閉設定選項。

![](./media/image94.png)

![](./media/image95.png)

![](./media/image96.png)

第一個關聯性現在已建立，並繫結到來源資料表中的資料。

**SaleEvent sold Products**

9.  選取 **Home** 返回設定畫布。

![](./media/image97.png)

10. 依照建立第一個關聯性類型的相同步驟，從 **SaleEvent** 實體類型建立第二個關聯性，詳細資料如下表所示。

| **Relationship type name** | **Origin entity type** | **Target entity type** | **Mapping table** | **Matched SaleEvent: SaleId** | **Matched Products: ProductId** |
|----|----|----|----|----|----|
| +++sold+++ | SaleEvent | Products | factsales | SaleId | ProductId |

![](./media/image98.png)

![](./media/image99.png)

![](./media/image100.png)

![](./media/image101.png)

![](./media/image102.png)

![](./media/image103.png)

![](./media/image104.png)

## 練習 3：以其他資料擴充 ontology

在本練習中，您將新增 **Freezer** 實體類型來擴充 ontology。此實體類型會增加更多領域情境，並引入時間序列資料的屬性，以反映即時營運資訊。最後，您會建立新的關聯性類型，以表示門市與其冷凍櫃之間的連結。

**注意：** 對於靜態和時間序列資料，您可以先建立屬性、稍後再繫結資料，也可以在單一步驟中建立屬性並繫結資料。本練習會示範這兩種方法。

### 任務 1：建立 Freezer 實體類型並新增屬性

請依照以下步驟建立 *Freezer* 實體類型並新增屬性。這些屬性尚未繫結到資料。

1.  從頂端功能區選取 **Add entity type**。輸入 +++Freezer+++ 作為實體類型的名稱，然後選取 **Add Entity Type**。

![](./media/image105.png)

![](./media/image106.png)

2.  在 **Explorer** 中選取 **Freezer** 實體類型後，從頂端功能區選取 **View entity type details**。

![](./media/image107.png)

3.  實體類型詳細資料的 **Configure** 頁面隨即開啟。展開 **Manage property bindings**，然後選取 **Add properties**。

![](./media/image108.png)

4.  新增以下屬性，然後按一下 **Save**。

| **名稱** | **屬性類型** |
|----|----|
| +++FreezerId+++ | String |
| +++Model+++ | String |
| +++minSafeTempC+++ | Double |
| +++StoreId+++ | String |

![](./media/image109.png)

**注意：** 屬性名稱在所有實體類型中必須是唯一的。

![](./media/image110.png)

5.  屬性會新增到 **Configure** 頁面，且尚未繫結到任何資料來源。

![](./media/image111.png)

### 任務 2：將靜態資料繫結到屬性

接下來，將靜態資料繫結到您在 *Freezer* 實體類型上建立的屬性。

1.  展開 **Manage property bindings**，然後選取 **Add binding and properties**。

![](./media/image112.png)

2.  選取 **Add data binding \> Lakehouse table**。

![](./media/image113.png)

3.  選擇資料來源。選取 **IQ_Lakehouse** Lakehouse 並按一下 **Next**，然後選取 **freezer** 資料表並按一下 **Select**。

![](./media/image114.png)

![](./media/image115.png)

4.  來源資料表中的欄位會填入資料繫結設定。與 Store 實體類型相同，請檢閱 **Entity type key**、**Binding selection**、**Entity type key mapping** 和 **Properties** 區段。**Source column** 一側會自動填入 **freezer** 資料表的資料行，**Property name** 一側則列出 **Freezer** 實體類型中對應的屬性名稱。在本實驗中，請保留預設的屬性名稱。

![](./media/image116.png)

5.  選取設定頂端的 **Define entity type key**。從屬性清單中選取 **FreezerId**，然後按一下 **Save**。

![](./media/image117.png)

![](./media/image118.png)

6.  **Save** 資料繫結。確認實體類型已成功更新，然後選取 **Cancel** 關閉設定選項。

![](./media/image119.png)

![](./media/image120.png)

### 任務 3：將時間序列資料繫結到其他屬性

接下來，在單一資料繫結作業中建立新屬性並繫結時間序列資料，為 **Freezer** 實體新增時間序列資料。

1.  在 **Configure** 頁面上，展開 **Manage property bindings**，再次選取 **Add binding and properties** 以重新開啟繫結設定。

![](./media/image121.png)

2.  在 **Binding selection** 下，展開 **Add data binding**，然後選取 **Eventhouse table or materialized view**。

![](./media/image122.png)

3.  選擇資料來源。選取 **TelemetryDataEH** Eventhouse，然後按一下 **Add**。

![](./media/image123.png)

4.  選取 **FreezerTelemetry** 資料表，然後按一下 **Add**。

![](./media/image124.png)

5.  設定中會出現 **Timeseries data** 區段。在 **Timestamp column** 中，選取 **timestamp**。

![](./media/image125.png)

6.  向下捲動到 **Properties** 區段，**StoreId** 會顯示錯誤，因為它已在靜態資料繫結中繫結。使用垃圾桶圖示刪除重複的屬性。

![](./media/image126.png)

7.  **Save** 資料繫結。確認實體類型已成功更新，然後選取 **Cancel** 關閉設定選項。

![](./media/image127.png)

![](./media/image128.png)

8.  回到 *Freezer* 的 **Configure** 頁面，請注意現在有更多實體類型屬性，而新的屬性都繫結到 *FreezerTelemetry* 資料來源。

![](./media/image129.png)

現在 *Freezer* 實體有兩個資料繫結：一個是來自 *freezer* Lakehouse 資料表的靜態資料，另一個是來自 *FreezerTelemetry* Eventhouse 資料表的串流資料。

### 任務 4：新增 Store operates Freezer 關聯性類型

最後，建立新的關聯性類型，以表示門市與其冷凍櫃之間的連結。

1.  在 **Configure** 頁面上，展開 **Manage relationships**，然後選取 **Add new relationship**。

![](./media/image130.png)

2.  輸入以下關聯性類型詳細資料，然後選取 **Add relationship type**。

| **Relationship type name** | +++operates+++ |
|----|----|
| **Source entity type** | **Store** |
| **Target entity type** | **Freezer** |

![](./media/image131.png)

3.  關聯性會新增到 **Relationships** 區段。在畫布上選取 **operates** 關聯性，以開啟關聯性詳細資料設定。請觀察 **Origin entity type**（*Store*）、**Relationship type** 和 **Target entity type**（*Freezer*）區段。

![](./media/image132.png)

![](./media/image133.png)

4.  在中間區段中輸入以下詳細資料：

- **Mapping table**：選取 **freezer** 資料表。資料表中的每一列都會依 ID 參考一個門市和一個冷凍櫃，因此可以將 **Store** 和 **Freezer** 實體連結在一起。

- **Matched Store: StoreId**：選取 **StoreId**。此資料行（*freezer \> StoreId*）與 *Store* 實體所定義的索引鍵屬性（*dimstore \> StoreId*）相符。

- **Matched Freezer: FreezerId**：選取 **FreezerId**。關聯性資料來源和實體資料來源都使用 *freezer* 資料表，因此您選取的是相同的資料行 (FreezerId)。

![](./media/image134.png)

**重要：** 請務必選取與實體類型索引鍵屬性相符的正確來源資料行。

5.  **Save** 關聯性類型。確認關聯性類型已成功更新，然後選取 **Cancel** 關閉設定選項。

![](./media/image135.png)

![](./media/image136.png)

6.  在實體的 **Configure** 頁面上，新的關聯性會顯示在 **Relationships** 區段中。

![](./media/image137.png)

## 練習 4：檢視 ontology

在本練習中，您將使用預覽體驗探索 ontology。您會檢查以資料將實體類型具體化的實體執行個體，並探索跨銷售和裝置串流資料的圖形情境。

### 任務 1：檢視執行個體清單和靜態資料

在先前的練習中將資料繫結到實體類型時，ontology 會自動建立與來源資料列繫結的實體執行個體。在此任務中，您將檢視這些實體執行個體。

1.  從 ontology 的 **Home** 設定畫布開始。選取 **SaleEvent** 實體類型，然後從頂端功能區選取 **View entity type details**。

![](./media/image138.png)

2.  開啟 **Instances** 索引標籤。確認它顯示六個實體執行個體，其中的資料（例如營收和單位數量）來自 **factsales** Lakehouse 資料表。

![](./media/image139.png)

### 任務 2：檢視時間序列資料

1.  在頁面左上角，使用實體類型名稱旁的選取器切換到 **Freezer** 實體類型。

![](./media/image140.png)

2.  開啟 **Overview** 索引標籤。由於預設的時間範圍 **Last 30 days** 不包含任何資料，因此索引標籤載入時圖表是空的。

![](./media/image141.png)

3.  將時間範圍從預設的 **Last 30 days** 更新為自訂日期範圍：開始於 **Fri Aug 01 2025 12:00 AM**，結束於 **Mon Aug 04 2025 12:00 AM**，**Time granularity** 設為 **5 minutes**。

![](./media/image142.png)

4.  觀察在您所選的時間範圍內，多個 **Freezer** 實體執行個體現在可見的時間序列資料。

![](./media/image143.png)

### 任務 3：檢視 ontology 圖形

**Overview** 索引標籤也包含 **Relationship graph**，可讓您以節點和邊緣的圖形視覺化 ontology。

1.  使用實體類型選取器切換到 **SaleEvent** 實體類型。在 **Relationship graph** 圖格中，選取 **Expand**。

![](./media/image144.png)

2.  展開的圖形檢視隨即開啟。觀察從 **SaleEvent** 實體類型到 **Products** 和 **Store** 的關聯性詳細資料。

![](./media/image145.png)

3.  使用實體類型選取器切換到 **Store** 實體類型，並展開其 **Relationship graph**。

![](./media/image146.png)

4.  在圖形中，觀察 **Store** 與 **Freezer** 和 **SaleEvent** 之間的關聯性。然後，在查詢產生器功能區中選取 **Run query**。此動作會執行預設查詢，並顯示實體執行個體及其連結的圖形。

![](./media/image147.png)

![](./media/image148.png)

![](./media/image149.png)

### 任務 4：查詢圖形執行個體

在關聯性圖形檢視中，您可以查詢符合特定條件的實體執行個體。使用頂端功能區中的 **Query builder** 篩選條件來建立查詢。

![](./media/image150.png)

首先，建立這個查詢：**顯示巴黎門市營運的所有冷凍櫃。**

1.  在 *Store* 實體的關聯性圖形中，從查詢產生器功能區選取 **Add filter \> Store \> StoreId**。將篩選條件設為 **StoreId = +++S-PAR-01+++**。此值是巴黎門市的門市 ID。

![](./media/image151.png)

![](./media/image152.png)

2.  在 **Components** 區段中，取消勾選 **SaleEvent**，只勾選 **Nodes \> Store**、**Nodes \> Freezer** 和 **Edges \> operates**。

![](./media/image153.png)

3.  選取 **Run query**，並確認執行個體圖形顯示兩台冷凍櫃連結到 **Paris** 門市。

![](./media/image154.png)

![](./media/image155.png)

4.  選取 **Clear query** 以清除查詢結果。

![](./media/image156.png)

接下來，建立這個查詢：**顯示所有曾有營收大於 150 之銷售的門市。**

5.  選取 **Add a node**，並新增 **SaleEvent** 節點。

![](./media/image157.png)

6.  在 **Components** 區段中，勾選 **Nodes \> Store** 和 **Edges \> from** 旁的方塊，將它們新增到圖形中。

![](./media/image158.png)

7.  從查詢產生器功能區選取 **Add filter \> SaleEvent \> RevenueUSD**。將篩選條件設為 **RevenueUSD \> +++150+++**。

![](./media/image159.png)

![](./media/image160.png)

8.  選取 **Run query**，並確認執行個體圖形顯示兩家門市，其相關銷售事件符合篩選條件。您也可以選取圖形中的節點，以取得特定銷售事件的詳細資料。

![](./media/image161.png)

![](./media/image162.png)

此流程可讓您檢查將營運問題（例如某些門市的冷凍櫃溫度升高）與業務成果（銷售）連結起來的路徑。

## 練習 5：從資料代理程式使用 ontology

Ontology（預覽）與 [Fabric 資料代理程式（預覽）](https://learn.microsoft.com/en-us/fabric/data-science/concept-data-agent)整合，可讓您以自然語言提問，並取得以 ontology 的定義和繫結為基礎的答案。

### 任務 1：建立以 ontology（預覽）為來源的資料代理程式

1.  在左側導覽窗格中，按一下 **Fabric IQ Ontology@lab.LabInstance.Id** 工作區。

![](./media/image163.png)

2.  在工作區頁面上，選取 **+ New item**。在 **Filter by item type** 搜尋方塊中輸入 +++data agent+++，然後選取 **Data agent**。

![](./media/image164.png)

3.  輸入 +++RetailOntologyAgent+++ 作為資料代理程式名稱，然後按一下 **Create**。

![](./media/image165.png)

4.  在 **RetailOntologyAgent** 頁面上，選取 **Add a data source**。

![](./media/image166.png)

5.  在 **OneLake catalog** 索引標籤上，選取 **RetailSalesOntology** ontology，然後按一下 **Add**。

![](./media/image167.png)

6.  代理程式準備就緒後會自動開啟。

![](./media/image168.png)

### 任務 2：提供代理程式指示

**注意：** 新增此步驟是為了因應影響查詢彙總的已知問題。

1.  從功能區選取 **Agent instructions**。

![](./media/image169.png)

2.  在輸入方塊底部新增 +++Support group by in GQL+++。此指示可改善 ontology 資料的彙總。

![](./media/image170.png)

3.  指示會自動套用。您可以選擇關閉 **Agent instructions** 索引標籤。

![](./media/image171.png)

### 任務 3：以自然語言查詢代理程式

接下來，以自然語言問題探索您的 ontology。

1.  輸入以下文字，然後按一下 **Submit** 圖示。

> +++For each store, show any freezers operated by that store that ever had a humidity lower than 46 percent.+++

![](./media/image172.png)

![](./media/image173.png)

2.  輸入以下文字，然後按一下 **Submit** 圖示。

> +++What is the top product by revenue across all stores?+++

![](./media/image174.png)

![](./media/image175.png)

3.  請注意，回應會參考實體類型（**Store**、**Products**、**Freezer**）及其關聯性，而不只是原始資料表。

![](./media/image176.png)

**提示：** 如果執行範例查詢時看到沒有資料的錯誤，請等待幾分鐘，讓代理程式有更多時間初始化，然後再次執行查詢。

繼續嘗試您自己的提示，探索資料代理程式。

## 練習 6：使用 Project Rayfin 建置並測試配套應用程式

在本練習中，您將使用 Project Rayfin 建立 Todo 應用程式的架構、在本機執行，並將它部署到您的 Fabric 工作區。

### 任務 1：在本機建置並測試應用程式

1.  開啟**檔案總管**，前往 **C:\\** 磁碟機，按一下工具列上的 **New**，選取 **Folder**，輸入 +++Lab4+++ 作為資料夾名稱，然後按 **Enter**。

![](./media/image177.png)

2.  在 Windows 搜尋方塊中輸入 +++Visual Studio Code+++，然後按一下 **Visual Studio Code**。

![](./media/image178.png)

3.  在 Visual Studio Code 對話方塊中，按一下 **Allow** 以繼續 Microsoft 驗證流程。

![](./media/image179.png)

4.  在 **Sign in** 視窗中，選取 **Work or school account**，然後按一下 **Continue**。

![](./media/image180.png)

5.  使用以下認證登入。

| **使用者名稱** | **+++@lab.CloudPortalCredential(User1).Username+++** |
|----|----|
| **密碼** | **+++@lab.CloudPortalCredential(User1).Password+++** |

![](./media/image181.png)

![](./media/image182.png)

6.  在 Visual Studio Code 中，按一下 **More Actions (...)** 功能表，選取 **Terminal**，然後選擇 **New Terminal** 以開啟新的整合式終端機視窗。

![](./media/image183.png)

7.  在終端機中，瀏覽至 **Lab4** 目錄。

> +++cd C:\Lab4+++

![](./media/image184.png)

8.  執行以下命令，使用 **\[Experimental\] Todo app with full local dev** 範本建立應用程式架構。

```powershell
npm create @microsoft/rayfin@latest -- --template https://github.com/microsoft/awesome-rayfin --template-name "[Experimental] Todo app with full local dev"
```

![](./media/image185.png)

![](./media/image186.png)

9.  輸入 +++Fabricapp+++ 作為專案名稱。

![](./media/image187.png)

![](./media/image188.png)

10. 專案建立成功後，瀏覽至 **Fabricapp** 專案目錄，並啟動本機開發伺服器。

> +++cd Fabricapp+++
>
> +++npm run dev+++

![](./media/image189.png)

11. 出現提示時，輸入 Fabric 工作區名稱 +++Fabric IQ Ontology@lab.LabInstance.Id+++，然後按 **Enter**，繼續將應用程式部署到所選的 Fabric 工作區。

![](./media/image190.png)

12. 當 **Windows Security** 對話方塊出現時，按一下 **Allow**，允許 **Node.js JavaScript Runtime** 在公用和私人網路上通訊。

![](./media/image191.png)

13. 複製終端機中顯示的本機前端 URL（應類似於 +++http://localhost:5173+++），並在新的瀏覽器索引標籤中開啟。

![](./media/image192.png)

14. 選取 **Sign in with Microsoft** 按鈕。由於您在練習 1 中已有有效的 SSO 工作階段，應該會自動登入，不需要再次輸入認證。如果沒有自動登入，請使用登入 Fabric 時的同一個 Microsoft 帳戶登入：

| **電子郵件** | **+++@lab.CloudPortalCredential(User1).Username+++** |
|----|----|
| **TAP** | **+++@lab.CloudPortalCredential(User1).AccessToken+++** |

![](./media/image193.png)

15. 在 **Todo App** 的工作欄位中輸入 +++Review Lakeshore Retail ontology relationships+++，然後按一下 **Add** 建立新的待辦事項。

![](./media/image194.png)

16. 在工作欄位中輸入 +++Validate freezer telemetry ingestion+++，然後按一下 **Add** 建立另一個待辦事項。

![](./media/image195.png)

![](./media/image196.png)

17. 選取其中一個工作。

![](./media/image197.png)

![](./media/image198.png)

### 任務 2：將應用程式部署到 Fabric

1.  回到 Visual Studio Code 終端機，按 **Ctrl+C** 停止 Vite 開發伺服器。

![](./media/image199.png)

2.  執行以下命令，將應用程式部署到您的 Fabric 工作區。

> +++npm run up+++

![](./media/image200.png)

3.  部署完成後，CLI 會列出**靜態裝載 URL**，類似於 **https://{random-prefix}.webapp.rayfin….com**。按一下 **App URL** 啟動應用程式。

![](./media/image201.png)

4.  當出現 **Do you want Code to open the external website?** 提示時，按一下 **Open**，在預設瀏覽器中啟動已部署的應用程式。

![](./media/image202.png)

5.  如同任務 1 中的步驟，選取 **Sign in with Microsoft**。

![](./media/image203.png)

![](./media/image204.png)

### 任務 3：在 Fabric 中檢查部署

讓我們在 Microsoft Fabric 入口網站中查看已部署的應用程式和資料庫。

1.  開啟 Microsoft Fabric 入口網站：+++https://app.fabric.microsoft.com+++。

2.  開啟您在練習 1 中建立的 **Fabric IQ Ontology@lab.LabInstance.Id** 工作區。

![](./media/image205.png)

3.  確認工作區包含一個 **Fabric data app** 項目和一個 **SQL Database** 項目。

![](./media/image206.png)

![](./media/image207.png)

## 練習 7：清除資源

1.  從左側導覽功能表中選取您的工作區 **Fabric IQ Ontology@lab.LabInstance.Id**，隨即開啟工作區項目檢視。

![](./media/image208.png)

2.  選取工作區名稱下的 **...** 選項，然後選取 **Workspace settings**。

![](./media/image209.png)

3.  瀏覽至 **General** 索引標籤底部，然後選取 **Remove this workspace**。

![](./media/image210.png)

4.  在彈出的警告中按一下 **Delete**。

![](./media/image211.png)

**摘要**

在本實驗中，您使用 Microsoft Fabric IQ Ontology（預覽）建立了一個連結的語意資料模型，用來表示真實世界的業務概念及其關聯性。透過將結構化的 Lakehouse 資料與串流遙測資料結合，ontology 提供了統一且易於業務理解的企業資料檢視。

透過實體定義、資料繫結和關聯性建模，您分析了營運訊號（例如冷凍櫃溫度或濕度）如何與銷售和營收等業務成果相關。您使用圖形查詢探索 ontology、將其連線到 Fabric 資料代理程式以進行自然語言提問，並使用 Project Rayfin 建置配套應用程式並部署到 Fabric 工作區。這些技能說明了 Fabric IQ Ontology 如何協助連結營運資料與分析，支援跨領域更明智的決策。
