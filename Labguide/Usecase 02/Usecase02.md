# 用例02：Data Factory解决方案，用于通过dataflows和data pipelines移动和转换data

**简介**

本实验室通过在一小时内提供完整的data集成场景的逐步指导，帮助您加快Microsoft
Fabric中Data Factory的评估流程。完成本教程後，你將理解Data
Factory的價值和關鍵能力，並知道如何完成常見的端到端data集成場景。

**目標**

實驗分為三個練習：

- **练习1：**用Data Factory创建一个流水线，将原始 data从Blob
  storage导入到Data Lakehouse中的青铜表。

- **练习2：**在Data Factory中用dataflow转换 data，处理bronze表的原始
  data，并将其迁移到Data Lakehouse中的Gold表。

- **練習3：**用Data
  Factory自動發送通知，發送郵件通知所有作業完成後通知你，最後將整個流程設置為定時運行。

## 练习1：用Data Factory创建 pipeline

### 任务1：创建Fabric工作区

在处理Fabric data之前，先创建一个启用Fabric试用区的工作区。

1.  打开浏览器，进入地址栏，输入或粘贴以下URL：+++[https://app.fabric.microsoft.com/+++，](https://app.fabric.microsoft.com/+++)然后按下**Enter** 键。

**注意**：如果你被引导到Microsoft Fabric Home页，请跳过#2到#4的步骤。

![](./media/image1.png)

2.  在 **Microsoft Fabric**
    窗口中，输入你的凭证，然后点击**Submit** 按钮。

![](./media/image2.png)

3.  然后，在 **Microsoft** 窗口输入密码，点击**Sign in** 按钮。

![A login screen with a red box and blue text AI-generated content may
be incorrect.](./media/image3.png)

4.  在 **Stay signed in?** 窗口，点击**“Yes”**按钮。

![A screenshot of a computer error AI-generated content may be
incorrect.](./media/image4.png)

5.  你将被引导到Power BI主页。

![](./media/image5.png)

6.  选择屏幕左下角的默认 Power BI 图标，然后选择 **Fabric**。

![](./media/image6.png)

![](./media/image7.png)

![](./media/image8.png)

7.  在 Microsoft **Fabric 主页**，选择**“New workspace**”选项。

![](./media/image9.png)

8.  在“**Create a
    workspace**”标签中，输入以下信息，点击**“Apply**”按钮。

| Setting | Value |
|---|---|
| Name | +++Data-FactoryXXXX+++ (XXXX can be a unique number) |
| Advanced | Under **License mode**, select **Fabric** |
| Default storage format | **Small semantic model storage format** |

![](./media/image10.png)

![](./media/image11.png)

9.  等待部署完成。大約需要2-3分鐘。

![A screenshot of a computer Description automatically
generated](./media/image12.png)

### 任务2：创建一个lakehouse并导入样本 data

1.  在**Data-FactoryXX**工作区页面，点击 **+New item **按钮

![A screenshot of a computer Description automatically
generated](./media/image13.png)

2.  点击“**Lakehouse**”瓷砖。

![A screenshot of a computer Description automatically
generated](./media/image14.png)

3.  在**“New lakehouse**”对话框中，在**Name** 字段输入
    +++**DataFactoryLakehouse+++** ，并**取消选择**lakehouses的模式。点击**“Create**”按钮，打开新的lakehouse。

> ![](./media/image15.png)

![](./media/image16.png)

4.  进入Lakehouse，右键点击文件文件夹，选择 Upload \> Upload
    files以添加文件

![](./media/image17.png)

5.  在“Upload files”标签页中，点击Files下的**folder**

![](./media/image18.png)

6.  在VM上浏览到 **C：\LabFiles**，然后选择
    /Labfiles/**NYCTaxi/part-00000-907cea6d-0f54-4639-9a14-042dc04185ef-c000.snappy.parquet**
    文件，点击**Open** 按钮。

![](./media/image19.png)

7.  然後點擊**Upload**按鈕並關閉

![](./media/image20.png)

![](./media/image21.png)

![](./media/image22.png)

8.  在工具栏中，选择使用下拉菜单 **Analyze data**，指向
    **Notebook**，然后选择**“New notebook**”。

![](./media/image23.png)

9.  添加以下 PySpark 代码创建 Spark 会话，读取从 Lakehouse
    文件文件夹上传的 Parquet 文件，将 data写入名为 *Bronze*
    的表，覆盖表中已有的 data。

```
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("LoadParquet").getOrCreate()
# Read the green_tripdata_2017 parquet file
df2 = spark.read.format("parquet").load("Files/part-00000-907cea6d-0f54-4639-9a14-042dc04185ef-c000.snappy.parquet")

# Write to table
df2.write.mode("overwrite").saveAsTable("Bronze")
```

![](./media/image24.png)

![](./media/image25.png)

7.  要验证已创建的表，请在资源管理器中右键点击 **DataFactoryLakehouse**
    lakehouse，然后选择**Refresh**。表格出现了。

![](./media/image26.png)

![](./media/image27.png)

![](./media/image28.png)

## 练习2：在Data Factory中通过dataflow转换 data

### 任务1：从Lakehouse表获取 data

1.  現在，點擊左側導航窗格中的工作區 **Data
    Factory-@lab.LabInstance.Id** 。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image29.png)

2.  点击导航栏中的 **+New item** 按钮，创建新的Dataflow Gen2
    。从可用项目列表中选择**Dataflow Gen2**项目

![](./media/image30.png)

3.  提供一个新的 Dataflow Gen2 名称为
    +++**nyc_taxi_data_with_discounts+++**，然后选择**Create**。

![](./media/image31.png)

4.  在新dataflow菜单中，在 **Power Query** 窗格下点击“**Get
    data”下拉菜单**，然后选择**“More...**.**”**。

![A screenshot of a computer Description automatically
generated](./media/image32.png)

5.  在**“Choose data source**”标签中，搜索框搜索类型
    **+++Lakehouse+++**，然后点击 **Lakehouse** 连接器。

![A screenshot of a computer Description automatically
generated](./media/image33.png)

6.  会弹出**“Connect to data
    source**”对话框，并根据当前登录用户自动为你创建一个新的连接。选择**Next**。

![A screenshot of a computer Description automatically
generated](./media/image34.png)

7.  会显示**“Choose data**”对话框。使用导航面板找到 **workspace-
    Data-FactoryXX** 并展开它。然后，展开 你在上一个模块中为目的地创建的
    **Lakehouse** - **DataFactoryLakehouse** ，从列表中选择**Bronze**表，然后点击
    **Create**按钮。

![](./media/image35.png)

8.  你會看到畫布現在已經被填滿了 data。

> ![](./media/image36.png)

### 任务2：转换从Lakehouse导入的 data

1.  在第二列的列头中选择
    data类型图标，**IpepPickupDatetime**，显示下拉菜单，并从菜单中选择
    data类型，将列从 **Date/Time** 转换为**Date**。

![](./media/image37.png)

2.  在色带的**“Home**”标签页，从**“Manage columns”Choose
    columns**“选择列”选项 。

![](./media/image38.png)

3.  在**“Choose
    columns”**对话框中，**取消选中**这里列出的一些列，然后选择**OK**。

    - lpepDropoffDatetime

    -  DoLocationID

![](./media/image39.png)

4.  選擇**storeAndFwdFlag**列的篩選並排序下拉菜單。（如果你看到警告
    **List may be incomplete**，选择**“Load more**”以查看所有 data。）

![](./media/image40.png)

5.  選擇“**Y”**只顯示應用了折扣的行，然後選擇**OK**。

![](./media/image41.png)

6.  选择**Ipep_Pickup_Datetime**列排序和筛选下拉菜单，然后选择**Date
    filters，**再选择**“Between...** **”。**
    提供日期和日期/时间类型的筛选。

![](./media/image42.png)

7.  在**Filter
    rows**對話框中，選擇**2017年1月1日**至**2017年1月31日**之間的日期，然後選擇**OK**。

![](./media/image43.png)

![](./media/image44.png)

### 任務3：連接包含折扣data的CSV文件

现在，在行程 data到位后，我们想加载包含每天相应折扣和 VendorID的
data，并在与行程 data合并前准备好这些 data。

1.  在 dataflow编辑器菜单的**Home**标签中，选择“**Get
    data**”选项，然后选择“**Text/CSV**”。

![](./media/image45.png)

2.  在“**Connect to data source**”面板中，在**Connection
    settings**下，选择**“Link to file**”单选按钮，然后输入
    +++https://raw.githubusercontent.com/ekote/azure-architect/master/Generated-NYC-Taxi-Green-Discounts.csv+++，并将连接名称输入为
    +++**dfconnection**+++，确保**authentication** **kind** 设置为**Anonymous**。点击**“Next**”按钮。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image46.png)

3.  在**Preview file data** 对话框中，选择**Create**。

![A screenshot of a computer Description automatically
generated](./media/image47.png)

![](./media/image48.png)

### 任務4：轉換貼現data

1.  查看
    data時，我們發現頭似乎在第一行。通过在预览网格区域左上角的表格右键菜单中选择**“Use
    first row as headers”，**将其升级为头部。

![](./media/image49.png)

***注意：**推广标题后，你会在dataflow编辑器顶部的**“Applied
steps**”面板中看到新增一个步骤，针对你列的 data类型。*

![](./media/image50.png)

2.  右键点击 **VendorID** 列，从显示的右键菜单中选择“**Unpivot other
    columns**”选项。这允许你将列转换为属性-值对，列变为行。

![](./media/image51.png)

3.  在表格未进行转向后，
    双击**Attribute** 列和**Value**列，并将**Attribute** 改为
    +++**Date+++** ，**Value** 改为**+++Discount+++**，重命名它们。

![](./media/image52.png)

4.  通过选择列名左侧的 data类型菜单并选择**Date**，来更改**Date**列的
    data类型。

![](./media/image53.png)

5.  选择**Discount** 栏，然后在菜单中选择**“Transform**”标签。选择**Number列**，然后从子菜单中选择**Standard** 数值变换，再选择**Divide**。

![](./media/image54.png)

6.  在**Divide** 对话框中输入值 +++100+++，然后点击**OK** 按钮。

![A screenshot of a computer Description automatically
generated](./media/image55.png)

![](./media/image56.png)

### 任务7：合并行程和折扣data

下一步是将两张表合并成一个表，列出应应用于行程的折扣和调整后的总额。

1.  首先，切换“**Diagram view**”按钮，这样你可以看到两个查询。

![](./media/image57.png)

2.  选择**Bronze** 查询，在**Home** 标签中选择**合并**菜单，选择**Combine** 查询，选择**Merge
    queries**然后选择**Merge queries as new**。

![](./media/image58.png)

3.  在**Merge** 对话框中，从 右侧表格选择
    **Generated-NYC-Taxi-Green-Discounts** 进行合并下拉，然后选择对话框右上角的“**light
    bulb**”图标，查看三表之间建议的列映射。

4.  依次选择两种建议的列映射，映射两个表中的VendorID和日期列。当两个映射都被添加时，匹配的列头会在每个表中被高亮显示。

![](./media/image59.png)

5.  会显示一条提示，要求你允许将多个data源的data合并以查看结果。选择**OK** 

![](./media/image60.png)

6.  在表格区域，你会看到一个警告：“The evaluation was canceled because
    combining data from multiple sources may reveal data from one source
    to another. Select continue if the possibility of revealing data is
    okay.”选择**“Continue**”以显示合并 data。

![](./media/image61.png)

7.  在“Privacy Levels”对话框中，选择**“check box :Ignore Privacy Levels
    checks for this document. Ignoring privacy Levels could expose
    sensitive or confidential data to an unauthorized
    person**”，点击**“Save**”按钮。

![](./media/image62.png)

![](./media/image63.png)

8.  注意在圖中新建查詢，顯示新Merge
    query與你之前創建的兩個查詢之間的關係。查看編輯器的表格窗格，向
    “Merge
    query”列表右側滾動，可以看到一個帶有表值的新列。这是“**Generated NYC
    Taxi-Green-Discounts**”栏，类型为**\[Table\]。**

在列头有一个图标，上面有两个相反方向的箭头，方便你从表格中选择列。取消选中除**Discount**以外的所有列，然后选择**OK**。

![](./media/image64.png)

9.  现在贴现值定在行级，我们可以创建一个新列来计算折现后的总金额。要做到这一点，请在编辑器顶部选择**“Add
    column**”标签，然后 **从**“**General”**组中选择“**Custom column**”。

![](./media/image65.png)

10. 在**Custom column**对话框中，您可以使用 Power Query 公式语言（也称为
    M）来定义新列的计算方式。输入 +++**TotalAfterDiscount+++** 作为**New
    column name**，选择 **Currency** 作为**Data type**，并为**Custom
    column formula**提供以下 M 表达式：

+++if [total_amount] > 0 then [total_amount] * ( 1 -[Discount] ) else [total_amount]+++

然后选择**OK**。

![](./media/image66.png)

![](./media/image67.png)

11. 选择新创建的**TotalAfterDiscount**列，然后在编辑器窗口顶部选择
    “**Transform**”标签。在**Number
    column**组中，选择**“Rounding**”下拉菜单，然后选择**“Round...**.**”**。

**注意**：如果找不到**rounding** 选项，请展开菜单查看**Number column**。

![](./media/image68.png)

12. 在**Round** 对话框中输入**2**，输入小数点位数，然后选择**OK**。

![](./media/image69.png)

13. 将 I**pepPickupDatetime** 的 data类型从 **Date** 更改为
    **Date/Time**。

![](./media/image70.png)

14. 最后，如果编辑器右侧还没有展开**Query
    settings** 窗格，并将查询重命名從**Merge** 作為 **+++Output+++**。

![](./media/image71.png)

![](./media/image72.png)

### 任务8：将输出查询加载到Lakehouse中的表中

当输出查询完全准备好并准备输出data后，我们可以定义查询的输出目的地。

1.  选择之前创建的**Output** 合并查询。然后选择 **+ icon**，将**data
    destination** 添加到该Dataflow中。

2.  在data destination列表中，选择**“**New
    destination”下的**Lakehouse** 选项。

![](./media/image73.png)

3.  在“**Connect to data
    destination**”对话框中，你的连接应该已经被选中了。选择**“Next**”继续。

![A screenshot of a computer Description automatically
generated](./media/image74.png)

4.  在“**Choose destination
    target”**对话框中，浏览到Lakehouse，然后再次选择**“Next**”。

![](./media/image75.png)

5.  在**“Choose destination
    settings**”对话框中，再次确认你的列是否正确映射，然后选择**Save
    settings**。

![](./media/image76.png)

6.  回到主编辑器窗口，确认你在**Output** 表的**Query
    settings**窗格中看到输出目的地为
    **Lakehouse**，然后从主页选项卡中选择**“Save and Run**”选项。

![](./media/image77.png)

![](./media/image78.png)

![](./media/image79.png)

9.  现在，点击左侧导航窗格上的 **Data Factory-XXXX workspace**。

![A screenshot of a computer Description automatically
generated](./media/image80.png)

10. 在**Data_FactoryXX**窗格中，选择 **DataFactoryLakehouse**
    查看新加载的表。

![](./media/image81.png)

11. 确认**Output** 表是否出现在**dbo**模式下。

![](./media/image82.png)

## 练习3：用Data Factory自动化并发送通知

### 任务1：将Office 365 Outlook活动添加到你的pipeline中

1.  在左侧导航菜单中点击**Data_FactoryXX** 工作区。

![A screenshot of a computer Description automatically
generated](./media/image83.png)

2.  在工作区页面选择 **+ New item** 选项，然后选择**“Pipeline”**

![A screenshot of a computer Description automatically
generated](./media/image84.png)

3.  提供一个管道名称 +++**First_Pipeline1+++**，然后选择**Create**。

![](./media/image85.png)

4.  在pipeline editor中选择“**Home**”标签，找到“**Add copy data
    activity”**的选项。

> ![](./media/image86.png)

5.  在“**Source**”标签页，输入以下设置，点击**Test connection**

| Setting | Value |
|---|---|
| Connection | +++dfconnection User-XXXX+++ |
| Connection Type | Select **HTTP** |
| File format | **Delimited Text** |

![](./media/image87.png)

6.  在**“Destination**”标签页，输入以下设置。

| Setting | Value |
|---|---|
| Connection | **Lakehouse** |
| Lakehouse | Select **DataFactoryLakehouse** |
| Root Folder | Select the **Table** radio button |
| Table | Select **New**, enter `+++Generated-NYC-Taxi-Green-Discounts+++`, and select **Create**. |

![](./media/image88.png)

![A screenshot of a computer Description automatically
generated](./media/image89.png)

7.  从色带中选择**“Run**”。

![](./media/image90.png)

8.  在**“Save and run?”**对话框，点击**“Save and run**”按钮。

![A screenshot of a computer Description automatically
generated](./media/image91.png)

![](./media/image92.png)

9.  在pipeline编辑器中选择**“Activities**”标签，找到 **Office Outlook**
    活动。

![](./media/image93.png)

10. 从你的复制活动中选择并拖动“Success”路径（在管道画布活动右上角的绿色复选框）到你的新的Office
    365 Outlook活动。

![A screenshot of a computer Description automatically
generated](./media/image94.png)

11. 从pipeline canvas中选择Office 365 Outlook活动，然后选择
    canvas下方属性区域的**Settings** 标签来配置邮件。点击**“Connection**”下拉菜单，选择**“Browse
    all”。**

![A screenshot of a computer Description automatically
generated](./media/image95.png)

12. 在“choose a data source”窗口中，选择**Office 365 Email**源。

![A screenshot of a computer Description automatically
generated](./media/image96.png)

13. 用你想发送邮件的账户登录。你可以用已经登录的账户使用现有连接。

![A screenshot of a computer Description automatically
generated](./media/image97.png)

14. 点击**Connect** 以继续。

![A screenshot of a computer Description automatically
generated](./media/image98.png)

15. 在pipeline canvas中选择Office 365
    Outlook活动，在canvas下方属性区域的**Settings** 标签中选择该邮件。

    - 在“**To”**栏输入您的电子邮件地址 。如果你想使用多个地址，请使用
      **;** 把他们分开。

![A screenshot of a computer Description automatically
generated](./media/image99.png)

- 对于**Subject**，选择该字段，使“**Add dynamic
  content**”选项出现，然后选择它以显示pipeline表达式构建canvas。

![A screenshot of a computer Description automatically
generated](./media/image100.png)

16. 会显示**Pipeline expression
    builder** 对话框。输入以下表达式，然后选择**OK**：

+++@concat('DI in an Hour Pipeline Succeeded with Pipeline Run Id', pipeline().RunId)+++

![](./media/image101.png)

17. 对于**Body**，再次选择字段，并在文本区域下方出现时选择“**View in
    expression builder**”选项。在出现的**Pipeline expression
    builder** 对话框中再次添加以下表达式，然后选择**OK**：

+++@concat('RunID = ', pipeline().RunId, ' ; ', 'Copied rows ', activity('Copy data1').output.rowsCopied, ' ; ','Throughput ', activity('Copy data1').output.throughput)+++

![](./media/image102.png)

![A screenshot of a computer Description automatically
generated](./media/image103.png)

\*\* 注意：\*\* 将 **Copy data1** 替换为你自己的pipeline复制活动名称。

18. 最後，在管道編輯器頂部選擇**“Home**”標簽，然後選擇**Run**。然后在确认对话框中选择“**Save
    and run**”以执行这些活动。

![A screenshot of a computer Description automatically
generated](./media/image104.png)

![A screenshot of a computer Description automatically
generated](./media/image105.png)

![](./media/image106.png)

![](./media/image107.png)

19. Pipeline成功运行后，查看你的电子邮件，查找pipeline发送的确认邮件。

![](./media/image108.png)

### 任务2：调度pipeline执行

一旦你完成了pipeline的开发和测试，就可以安排它自动执行。

1.  在pipeline editor窗口的**Home** 标签中**，**选择**“Schedule”。**

![A screenshot of a computer Description automatically
generated](./media/image109.png)

2.  根据需要配置时间表。这里的示例安排了pipeline每天晚上8点执行，直到年底。

![A screenshot of a schedule Description automatically
generated](./media/image110.png)

![](./media/image111.png)

![](./media/image112.png)

### 任务3：向 pipeline添加Dataflow活动

1.  将鼠标悬停在连接pipeline canvas上**Copy activity**和**Office 365
    Outlook**活动的绿色线上，选择 **+** 按钮插入新活动。

![](./media/image113.png)

2.  从出现的菜单中选择**Dataflow** 。

![](./media/image114.png)

3.  新创建的Dataflow活动会插入复制活动和Office 365
    Outlook活动之间，并自动选择，在canvas下方区域显示其属性。在属性区域选择**Settings** 标签，然后选择你在**练习2：在Data
    Factory中用dataflow转换 data时创建**的dataflow。

![](./media/image115.png)

4.  选择pipeline
    editor顶部的**“Home**”标签，然后选择**Run**。然后在确认对话框中选择“**Save
    and run**”以执行这些活动。

![](./media/image116.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image117.png)

![](./media/image118.png)

![](./media/image119.png)

### 任务四：清理资源

你可以删除单个报表、pipelines、仓库和其他项目，或者删除整个工作区。请按照以下步骤删除你为本教程创建的工作区。

1.  在左侧导航菜单中选择您的工作区，即**Data-FactoryXX** 。它会打开工作区的物品视图。

![A screenshot of a computer Description automatically
generated](./media/image83.png)

2.  在右上角的工作区页面选择**Workspace settings** 选项。

![](./media/image120.png)

3.  选择**General标签**并 **Remove this workspace。**

![](./media/image121.png)
