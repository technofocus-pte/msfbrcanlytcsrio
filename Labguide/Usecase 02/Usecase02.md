# 用例02：數據工廠解決方案，用於通過數據流和數據管道移動和轉換數據

**介紹**

本實驗室通過在一小時內提供完整的數據集成場景的逐步指導，幫助您加快Microsoft
Fabric中Data
Factory的評估流程。完成本教程後，你將理解數據工廠的價值和關鍵能力，並知道如何完成常見的端到端數據集成場景。

**目标**

实验室分为三个e-xercises:

- **练习1:** 用 Data Factory 创建一个流水线，将原始数据从 Blob
  存储导入到 Data Lakehouse 中的 Bronze 表。

- **练习2：** 在Data
  Factory中用数据流转换数据，处理青铜表的原始数据，并将其迁移到Data
  Lakehouse中的Gold表。

- **練習3：** 用Data
  Factory自動發送通知，發送郵件通知所有作業完成後通知你，最後將整個流程設置為定時運行。

# 練習 1：用數據工廠創建流水線

## 任務1：創建一個工作區

在處理Fabric數據之前，先創建一個啟用Fabric試用區的工作區。

1.  打开浏览器，进入地址栏，输入或粘贴以下URL：+++https://app.fabric.microsoft.com/+++
    ，然后按下 **Enter** 键。

> **注意**：如果你被引导到Microsoft Fabric主页，请跳过#2到#4的步骤。
>
> ![](./media/image1.png)

2.  在 **Microsoft Fabric** 窗口中，输入你的凭证，然后点击 **Submit**
    按钮。

> ![](./media/image2.png)

3.  然后，在 **Microsoft** 窗口输入密码，点击 **Sign in** 按钮**。**

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image3.png)

4.  留在 **Stay signed in?** 窗口，点击**“Yes”**按钮。

> ![A screenshot of a computer error AI-generated content may be
> incorrect.](./media/image4.png)
>
> ![](./media/image5.png)

5.  在 **Microsoft Fabric 主页**，选择 **“New workspace**”选项。

> ![](./media/image6.png)

6.  在“**Create a workspace**”标签中，输入以下信息，点击 **“Apply应用**
    ”按钮。

	|   |   |
	|----|----|
	|Name	| Data-FactoryXXXX (XXXX can be a unique number) |
	|Advanced|	Under License mode, select Fabric capacity|
	|Default storage format|	Small semantic model storage format|

> ![](./media/image7.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

7.  等待部署完成。大約需要2-3分鐘。

> ![A screenshot of a computer Description automatically
> generated](./media/image9.png)

## 任務2：創建一個lakehouse並導入樣本數據

1.  在 **Data-FactoryXX** 工作区页面，点击 **+New item** 按钮

> ![A screenshot of a computer Description automatically
> generated](./media/image10.png)

2.  点击“**Lakehouse**”瓷砖。

![A screenshot of a computer Description automatically
generated](./media/image11.png)

3.  在“**New
    lakehouse** ”对话框中，在“**Name**”字段中输入**+++DataFactoryLakehouse+++**，单击“**Create**”按钮，打开新的lakehouse。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image12.png)
>
> ![](./media/image13.png)

4.  在 **Lakehouse** 主页中，选择 **Start with sample
    data** 以打开复制样本数据

> ![](./media/image14.png)

5.  显示“**Use a sample**”对话框，选择 **NYCTaxi** 样本数据瓷砖。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image15.png)
>
> ![](./media/image16.png)
>
> ![](./media/image17.png)

6.  要重命名表，右键点击 编辑器上方的 **green_tripdata_2022** 标签，选择
    **Rename**。

![A screenshot of a computer Description automatically
generated](./media/image18.png)

7.  在“**Rename**”对话框的“**Name**”字段中，输入**+++Bronze+++**以更改**表**名称。然后，单击“**Rename**”按钮。

![A screenshot of a computer Description automatically
generated](./media/image19.png)

![A screenshot of a computer Description automatically
generated](./media/image20.png)

**練習2：在數據工廠中通過數據流轉換數據**

## 任務1：從Lakehouse表獲取數據

1.  現在，點擊
    [左側導航窗格中的工作區](mailto:Data%20Factory-@lab.LabInstance.Id)
    [**Data
    Factory-@lab.LabInstance.Id**](mailto:Data%20Factory-@lab.LabInstance.Id)。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image21.png)

2.  点击导航栏中的 **+New item** 按钮，创建新的 Dataflow
    Gen2。从可用项目列表中选择**Dataflow Gen2**项目

> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

3.  提供一个新的 Dataflow Gen2 名称为
    **+++nyc_taxi_data_with_discounts+++**，然后选择创建。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image23.png)

4.  在新数据流菜单中，在 **Power Query** 面板下点击“**Get
    data**”**下拉菜单**，然后选择 **More**......

> ![A screenshot of a computer Description automatically
> generated](./media/image24.png)

5.  在“**Choose data source**”标签中，搜索框搜索类型
    +++**Lakehouse+++**，然后点击 **Lakehouse** 连接器。

> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

6.  会弹出“**Connect to data
    source** ”对话框，并根据当前登录用户自动为你创建一个新的连接。选择
    **Next**。 

> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)

7.  会显示“**Choose data**”对话框。使用导航面板找到 **workspace-
    Data-FactoryXX** 并展开它。然后，展开你在上一个模块中为目的地创建的
    **Lakehouse** - **DataFactoryLakehouse**，从列表中选择 **Bronze**
    表，然后点击**Create**按钮。  

![A screenshot of a computer Description automatically
generated](./media/image27.png)

8.  你會看到畫布現在已經被填滿了數據。

![A screenshot of a computer Description automatically
generated](./media/image28.png)

## 任務2：轉換從Lakehouse導入的數據

1.  在第二列的列頭中選擇數據類型圖標，**IpepPickupDatetime**，顯示下拉菜單，並從菜單中選擇數據類型，將列從
    **Date/Time** 轉換為**Date**。

![A screenshot of a computer Description automatically
generated](./media/image29.png)

2.  在色带的“**Home**”标签页，从“**Choose columns** ”组中选择“**Manage
    columns** ”选项。 

![A screenshot of a computer Description automatically
generated](./media/image30.png)

3.  在“**Choose
    columns** ”对话框中，取消选中这里列出的一些列，然后选择**OK**。

    - lpepDropoffDatetime

    &nbsp;

    - DoLocationID

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image31.png)

4.  選擇**storeAndFwdFlag**列的篩選並排序下拉菜單。（如果您发现警告
    **List may be incomplete**，请选择“**Load more**”以查看所有数据。）

> ![A screenshot of a computer Description automatically
> generated](./media/image32.png)

5.  選擇“**Y”** 只顯示應用了折扣的行，然後選擇 **OK**。

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

6.  选择**Ipep_Pickup_Datetime**列排序和筛选下拉菜单，然后选择**Date
    filters**，最后选择 **Between...** 。提供日期和日期/时间类型的筛选。

![](./media/image34.png)

7.  在“**Filter rows**”对话框中，选择 **2022 年 1 月 1** **日**至 **2022
    年 1 月 31 日**之间的日期，然后选择“**OK**”。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image35.png)

## 任務3：連接包含折扣數據的CSV文件

現在，在行程數據到位後，我們想加載包含每天相應折扣和供應商ID的數據，並在與行程數據合併前準備好這些數據。

1.  在數據流編輯器菜單的**主頁**標簽中，選擇“**Get
    data** ”選項，然後選擇“**Text/CSV**”。

> ![](./media/image36.png)

2.  在“**Connect to data
    source**”面板中，在**Connection设置**下，选择“**Link to
    file**”单选按钮，然后输入
    +++https://raw.githubusercontent.com/ekote/azure-architect/master/Generated-NYC-Taxi-Green-Discounts.csv+++，再输入连接名称为
    **+++dfconnection+++**，确保**认证类型**设置为**匿名**。点击“**Next**”按钮。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image37.png)

3.  在**Preview file data**对话框中，选择 **Create**。

![A screenshot of a computer Description automatically
generated](./media/image38.png)

## 任务4：转换贴现数据

1.  查看數據時，我們發現頭似乎在第一行。通过在预览网格区域左上角的表格右键菜单中选择**“Use
    first row as headers”，**将其升级为头部。

> ![](./media/image39.png)
>
> ***注意：**推廣標題後，你會在數據流編輯器頂部的**“Applied
> steps**”面板中看到新增一個步驟，針對你列的數據類型。*
>
> ![](./media/image40.png)

2.  右鍵點擊 **VendorID** 列，從顯示的右鍵菜單中選擇“**Unpivot other
    columns**”選項。這允許你將列轉換為屬性-值對，列變為行。

![A screenshot of a computer Description automatically
generated](./media/image41.png)

3.  在表格未進行轉向後，雙擊**屬性**列和**值列**，並將**屬性**改為**+++Date+++**，**值改**為**+++Discount+++**，重命名它們。

![A screenshot of a computer Description automatically
generated](./media/image42.png)

![A screenshot of a computer Description automatically
generated](./media/image43.png)

4.  通過選擇列名左側的數據類型菜單並選擇
    **Date**，來更改**Date**列的數據類型。

> ![A screenshot of a computer Description automatically
> generated](./media/image44.png)

5.  选择**Discount**栏，然后在菜单中选择“**Transform**”标签。选择**Number
    列**，然后从子菜单中选择**Standard** 数值变换，再选择**Divide**。 

> ![A screenshot of a computer Description automatically
> generated](./media/image45.png)

6.  在**Divide **對話框中輸入值 +++100+++，然後點擊 **ok** 按鈕。

![A screenshot of a computer Description automatically
generated](./media/image46.png)

![A screenshot of a computer Description automatically
generated](./media/image47.png)

**任務7：合併行程和折扣數據**

下一步是將兩張表合併成一個表，列出應應用於行程的折扣和調整後的總額。

1.  首先，切换“**Diagram view**”按钮，这样你可以看到两个查询。

![A screenshot of a computer Description automatically
generated](./media/image48.png)

2.  选择**Bronze**查询，在**主页**标签中选择合并菜单，选择 **Merge
    queries**，然后选择 **Merge queries as new**。

![](./media/image49.png)

3.  在“**Merge**”对话框中，从“**Right table for
    merge** ”下拉列表中选择“**Generated-NYC-Taxi-Green-Discounts**”，然后选择对话框右上角的“**light
    bulb**”图标，即可查看三个表格之间建议的列映射。 

4.  依次選擇兩種建議的列映射，分別映射兩個表中的VendorID和日期列。當兩個映射都被添加時，匹配的列頭會在每個表中被高亮顯示。

> ![](./media/image50.png)

5.  會顯示一條提示，要求你允許將多個數據源的數據合併以查看結果。选择
    **OK** 

> ![A screenshot of a computer Description automatically
> generated](./media/image51.png)

6.  在表格区域，你会看到一个警告：“The evaluation was canceled because
    combining data from multiple sources may reveal data from one source
    to another. Select continue if the possibility of revealing data is
    okay.”选择“**Continue**”以显示合并数据。

> ![](./media/image52.png)

7.  在“Privacy Levels”对话框中，选择“**check box :Ignore Privacy Levels
    checks for this document. Ignoring privacy Levels could expose
    sensitive or confidential data to an unauthorized
    person**，点击“**Save**”按钮。

> ![A screenshot of a computer screen Description automatically
> generated](./media/image53.png)
>
> ![](./media/image54.png)

8.  注意在圖中新建查詢，顯示新合併查詢與你之前創建的兩個查詢之間的關係。查看編輯器的表格窗格，向“合併查詢列”列表右側滾動，可以看到一個帶有表值的新列。这是“**Generated
    NYC Taxi-Green-Discounts**”栏，类型为**\[Table\]**。

在列頭有一個圖標，上面有兩個相反方向的箭頭，方便你從表格中選擇列。取消選中除**折扣**以外的所有列，然後選擇**OK**。

![](./media/image55.png)

9.  現在貼現值定在行級，我們可以創建一個新列來計算折現後的總金額。要做到这一点，请在编辑器顶部选择“**Add
    column** ”标签，然后从“**General** ”组中选择“**Custom column** ”。

> ![](./media/image56.png)

10. 在“**Custom column**”对话框中，您可以使用 Power Query
    公式语言（也称为 M 语言）来定义新列的计算方式。在**New column
    name**中输入
    +++**TotalAfterDiscount+++** ，在数据类型中选择“**Currency**”，并在**Custom
    column formula**中提供以下 M 表达式。:

 +++if [total_amount] > 0 then [total_amount] * ( 1 -[Discount] ) else [total_amount]

然后选择**OK**。

![](./media/image57.png)

![A screenshot of a computer Description automatically
generated](./media/image58.png)

11. 选择新创建的......**TotalAfterDiscount** 列，然后在编辑器窗口顶部选择“**Transform**”标签。在**Number
    column** 组中，选择“**Round...**.”下拉菜单，然后选择“**Rounding**”

**注意：**如果找不到**rounding**选项，请展开菜单查看**Number column**。

![](./media/image59.png)

12. 在“**Round**”對話框中，輸入 **2** 作為小數位數，然後選擇“**OK**”。

![A screenshot of a computer Description automatically
generated](./media/image60.png)

13. 将 **IpepPickupDatetime** 的数据类型从 **Date**
    更改为**Date/Time**。

![](./media/image61.png)

14. 最后，如果编辑器右侧的“**Query
    settings** ”窗格尚未展开，请将其展开，并将查询名称从“**Merge** ”重命名为+++**Output+++**”。 

![A screenshot of a computer Description automatically
generated](./media/image62.png)

![A screenshot of a computer Description automatically
generated](./media/image63.png)

**任務8：將輸出查詢加載到Lakehouse中的表中**

當輸出查詢完全準備好並準備輸出數據後，我們可以定義查詢的輸出目的地。

1.  選擇之前創建的 **Output** 合併查詢。然后选择 **+ icon** ，将**data
    destination** 添加到该数据流中。

2.  在数据目的地列表中，选择 **“New destination”**下的 Lakehouse 选项。

![](./media/image64.png)

3.  在“**Connect to data
    destination** ”对话框中，你的连接应该已经被选中了。选择“**Next**”继续。

![A screenshot of a computer Description automatically
generated](./media/image65.png)

4.  在“**Choose destination target**”对话框中，浏览到
    Lakehouse，然后再次选择“**Next**”。 

![](./media/image66.png)

5.  在“**Choose destination
    settings**”对话框中，保留默认的**Replace**更新方法，再次确认列是否正确映射，然后选择**Save
    settings**。

![](./media/image67.png)

6.  回到主编辑器窗口，确认你在输出表的**Query
    settings**窗格中看到**Output**目的地为
    **Lakehouse**，然后从主页选项卡中选择“**Save and Run**”选项。

> ![](./media/image68.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image69.png)

9.  现在，点击左侧导航窗格上的 **Data Factory-XXXX workspace** 。

> ![A screenshot of a computer Description automatically
> generated](./media/image70.png)

10. 在 **Data_FactoryXX** 窗格中， 选择 **DataFactoryLakehouse**
    查看加载到那里的新表。

![](./media/image71.png)

11. 确认**Output **表是否出现在 **dbo** 模式下。

![](./media/image72.png)

# 練習3：用數據工廠自動化並發送通知

## 任务1：将Office 365 Outlook活动添加到你的管道中

1.  在左侧导航菜单中点击**Data_FactoryXX**工作区。

> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)

2.  在工作区页面选择**+ New item**选项，然后选择“**Pipeline**”

> ![A screenshot of a computer Description automatically
> generated](./media/image74.png)

3.  提供一个流水线名称为 +++First_Pipeline1+++，然后选择 **Create**。

> ![](./media/image75.png)

4.  在管道编辑器中选择“**Home**”标签，找到“**Add to canvas”**活动。

> ![](./media/image76.png)

5.  在“**Source**”标签页，输入以下设置，点击**Test connection**

	|     |    |
	|------|------|
	|Connection|	dfconnection User-XXXX|
	|Connection Type|	select HTTP.|
	|File format	|Delimited Text|

> ![](./media/image77.png)

6.  在**“Destination**”标签页，输入以下设置。

	|    |    |
	|-----|----|
	|Connection	|**Lakehouse**|
	|Lakehouse|	Select **DataFactoryLakehouse**|
	|Root Folder	|select the **Table** radio button.|
	|Table|	• Select New, enter +++Generated-NYC-Taxi-Green-Discounts+++ and click on Create button|

> ![](./media/image78.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image79.png)

7.  从色带中选择**“Run**”。

> ![](./media/image80.png)

8.  在**“Save and run?”**对话框，点击**“Save and run**”按钮。

> ![A screenshot of a computer Description automatically
> generated](./media/image81.png)
>
> ![](./media/image82.png)

9.  在管道编辑器中选择“**Activities**”标签，找到 **Office Outlook**
    活动。 

> ![A screenshot of a computer Description automatically
> generated](./media/image83.png)

10. 從你的複製活動中選擇並拖動“成功”路徑（在管道畫布活動右上角的綠色複選框）到你的新的Office
    365 Outlook活動。

![A screenshot of a computer Description automatically
generated](./media/image84.png)

11. 从管道画布中选择Office 365
    Outlook活动，然后选择画布下方属性区域的**Settings**标签来配置邮件。点击**Connection**下拉菜单，选择**Browse
    all**。 

> ![A screenshot of a computer Description automatically
> generated](./media/image85.png)

12. 在“choose a data source”窗口中，选择 **Office 365 Email** 件源。

> ![A screenshot of a computer Description automatically
> generated](./media/image86.png)

13. 用你想發送郵件的賬戶登錄。你可以用已經登錄的賬戶使用現有連接。

> ![A screenshot of a computer Description automatically
> generated](./media/image87.png)

14. 点击 **Connect** 以继续。

> ![A screenshot of a computer Description automatically
> generated](./media/image88.png)

15. 在管道画布中选择Office 365 Outlook活动，在 画布下方属性区域的
    **Settings** 标签中选择该邮件。

    - 在“**收件人”**欄輸入您的電子郵件地址
      。如果你想使用多個地址，請使用 **;** 把他們分開。

> ![A screenshot of a computer Description automatically
> generated](./media/image89.png)

- 对于**Subject**，选择该字段，使“**Add dynamic
  content** ”选项出现，然后选择它以显示流水线表达式构建画布。

> ![A screenshot of a computer Description automatically
> generated](./media/image90.png)

16. 会显示 **Pipeline expression builder**
    对话框。输入以下表达式，然后选择**OK** :

+++@concat('DI in an Hour Pipeline Succeeded with Pipeline Run Id', pipeline().RunId)+++

> ![](./media/image91.png)

17. 对于正文，再次选择字段，并在文本区域下方出现时选择“**View in
    expression builder**”选项。在出现的 **Pipeline expression
    builder** 对话框中再次添加以下表达式，然后选择**OK**  :

+++@concat('RunID = ', pipeline().RunId, ' ; ', 'Copied rows ', activity('Copy data1').output.rowsCopied, ' ; ','Throughput ', activity('Copy data1').output.throughput)+++

> ![](./media/image92.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image93.png)

**  注意：**将 **Copy data1** 替换为你自己的管道复制活动名称。

18. 最後，選擇管道編輯器頂部的“**Home**”選項卡，然後選擇“**Run**”。接着，在确认对话框中选择“**Save
    and run**”以执行这些操作。 

> ![A screenshot of a computer Description automatically
> generated](./media/image94.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image95.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image96.png)

19. 管道成功運行後，查看你的電子郵件，查找管道發送的確認郵件。

![](./media/image97.png)

**任務2：調度流水線執行**

一旦你完成了流程的開發和測試，就可以安排它自動執行。

1.  在 管道编辑器窗口的**Home** 标签中，选择“**Schedule**”。

![A screenshot of a computer Description automatically
generated](./media/image98.png)

2.  根據需要配置時間表。這裡的示例安排了流水線每天晚上8點執行，直到年底。

![A screenshot of a schedule Description automatically
generated](./media/image99.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image100.png)

![A screenshot of a schedule AI-generated content may be
incorrect.](./media/image101.png)

**任務3：向管道添加數據流活動**

1.  将鼠标悬停在连接流水线画布上 **Copy activity** 和 **Office 365
    Outlook**活动的绿色线上，选择**+**按钮插入新活动。

> ![A screenshot of a computer Description automatically
> generated](./media/image102.png)

2.  從出現的菜單中選擇**Dataflow** 。

![A screenshot of a computer Description automatically
generated](./media/image103.png)

3.  新創建的數據流活動會插入複製活動和Office 365
    Outlook活動之間，並自動選擇，在畫布下方區域顯示其屬性。在屬性區域選擇**Settings **標簽，然後選擇你在**練習2：在數據工廠中用數據流轉換數據時創建**的數據流。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image104.png)

4.  選擇管道編輯器頂部的“**Home**”選項卡，然後選擇“**Run**”。接着在确认对话框中选择“**Save
    and run**”以执行这些活动。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image105.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image106.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image107.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image108.png)

## 任務四：清理資源

你可以刪除單個報表、管道、倉庫和其他項目，或者刪除整個工作區。請按照以下步驟刪除你為本教程創建的工作區。

1.  在左側導航菜單中選擇您的工作區，即**Data-FactoryXX**。它會打開工作區的物品視圖。

![](./media/image109.png)

2.  在右上角的工作区页面选择 **Workspace settings** 选项。

![A screenshot of a computer Description automatically
generated](./media/image110.png)

3.  选择**General标签**并**Remove this workspace**。

![A screenshot of a computer Description automatically
generated](./media/image111.png)
