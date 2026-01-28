# 用例02：数据工厂解决方案，用于通过数据流和数据管道移动和转换数据

**介绍**

本实验室通过在一小时内提供完整的数据集成场景的逐步指导，帮助您加快Microsoft
Fabric中Data
Factory的评估流程。完成本教程后，你将理解数据工厂的价值和关键能力，并知道如何完成常见的端到端数据集成场景。

**目标**

实验室分为三个e-xercises:

- **练习1:** 用 Data Factory 创建一个流水线，将原始数据从 Blob
  存储导入到 Data Lakehouse 中的 Bronze 表。

- **练习2：** 在Data
  Factory中用数据流转换数据，处理青铜表的原始数据，并将其迁移到Data
  Lakehouse中的Gold表。

- **练习3：** 用Data
  Factory自动发送通知，发送邮件通知所有作业完成后通知你，最后将整个流程设置为定时运行。

# 练习 1：用数据工厂创建流水线

## 任务1：创建一个工作区

在处理Fabric数据之前，先创建一个启用Fabric试用区的工作区。

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
	|**Name**	| +++Data-FactoryXXXX+++ (XXXX can be a unique number) |
	|**Advanced**|	Under License mode, select Fabric capacity|
	|**Default storage format**|	Small semantic model storage format|

> ![](./media/image7.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

7.  等待部署完成。大约需要2-3分钟。

> ![A screenshot of a computer Description automatically
> generated](./media/image9.png)

## 任务2：创建一个lakehouse并导入样本数据

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

**练习2：在数据工厂中通过数据流转换数据**

## 任务1：从Lakehouse表获取数据

1.  现在，点击
    [左侧导航窗格中的工作区](mailto:Data%20Factory-@lab.LabInstance.Id)
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

8.  你会看到画布现在已经被填满了数据。

![A screenshot of a computer Description automatically
generated](./media/image28.png)

## 任务2：转换从Lakehouse导入的数据

1.  在第二列的列头中选择数据类型图标，**IpepPickupDatetime**，显示下拉菜单，并从菜单中选择数据类型，将列从
    **Date/Time** 转换为**Date**。

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

4.  选择**storeAndFwdFlag**列的筛选并排序下拉菜单。（如果您发现警告
    **List may be incomplete**，请选择“**Load more**”以查看所有数据。）

> ![A screenshot of a computer Description automatically
> generated](./media/image32.png)

5.  选择“**Y”** 只显示应用了折扣的行，然后选择 **OK**。

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

6.  选择**Ipep_Pickup_Datetime**列排序和筛选下拉菜单，然后选择**Date
    filters**，最后选择 **Between...** 。提供日期和日期/时间类型的筛选。

![](./media/image34.png)

7.  在“**Filter rows**”对话框中，选择 **2022 年 1 月 1** **日**至 **2022
    年 1 月 31 日**之间的日期，然后选择“**OK**”。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image35.png)

## 任务3：连接包含折扣数据的CSV文件

现在，在行程数据到位后，我们想加载包含每天相应折扣和供应商ID的数据，并在与行程数据合并前准备好这些数据。

1.  在数据流编辑器菜单的**主页**标签中，选择“**Get
    data** ”选项，然后选择“**Text/CSV**”。

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

1.  查看数据时，我们发现头似乎在第一行。通过在预览网格区域左上角的表格右键菜单中选择**“Use
    first row as headers”，**将其升级为头部。

> ![](./media/image39.png)
>
> ***注意：**推广标题后，你会在数据流编辑器顶部的**“Applied
> steps**”面板中看到新增一个步骤，针对你列的数据类型。*
>
> ![](./media/image40.png)

2.  右键点击 **VendorID** 列，从显示的右键菜单中选择“**Unpivot other
    columns**”选项。这允许你将列转换为属性-值对，列变为行。

![A screenshot of a computer Description automatically
generated](./media/image41.png)

3.  在表格未进行转向后，双击**属性**列和**值列**，并将**属性**改为**+++Date+++**，**值改**为**+++Discount+++**，重命名它们。

![A screenshot of a computer Description automatically
generated](./media/image42.png)

![A screenshot of a computer Description automatically
generated](./media/image43.png)

4.  通过选择列名左侧的数据类型菜单并选择
    **Date**，来更改**Date**列的数据类型。

> ![A screenshot of a computer Description automatically
> generated](./media/image44.png)

5.  选择**Discount**栏，然后在菜单中选择“**Transform**”标签。选择**Number
    列**，然后从子菜单中选择**Standard** 数值变换，再选择**Divide**。 

> ![A screenshot of a computer Description automatically
> generated](./media/image45.png)

6.  在**Divide **对话框中输入值 +++100+++，然后点击 **ok** 按钮。

![A screenshot of a computer Description automatically
generated](./media/image46.png)

![A screenshot of a computer Description automatically
generated](./media/image47.png)

**任务7：合并行程和折扣数据**

下一步是将两张表合并成一个表，列出应应用于行程的折扣和调整后的总额。

1.  首先，切换“**Diagram view**”按钮，这样你可以看到两个查询。

![A screenshot of a computer Description automatically
generated](./media/image48.png)

2.  选择**Bronze**查询，在**主页**标签中选择合并菜单，选择 **Merge
    queries**，然后选择 **Merge queries as new**。

![](./media/image49.png)

3.  在“**Merge**”对话框中，从“**Right table for
    merge** ”下拉列表中选择“**Generated-NYC-Taxi-Green-Discounts**”，然后选择对话框右上角的“**light
    bulb**”图标，即可查看三个表格之间建议的列映射。 

4.  依次选择两种建议的列映射，分别映射两个表中的VendorID和日期列。当两个映射都被添加时，匹配的列头会在每个表中被高亮显示。

> ![](./media/image50.png)

5.  会显示一条提示，要求你允许将多个数据源的数据合并以查看结果。选择
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

8.  注意在图中新建查询，显示新合并查询与你之前创建的两个查询之间的关系。查看编辑器的表格窗格，向“合并查询列”列表右侧滚动，可以看到一个带有表值的新列。这是“**Generated
    NYC Taxi-Green-Discounts**”栏，类型为**\[Table\]**。

在列头有一个图标，上面有两个相反方向的箭头，方便你从表格中选择列。取消选中除**折扣**以外的所有列，然后选择**OK**。

![](./media/image55.png)

9.  现在贴现值定在行级，我们可以创建一个新列来计算折现后的总金额。要做到这一点，请在编辑器顶部选择“**Add
    column** ”标签，然后从“**General** ”组中选择“**Custom column** ”。

> ![](./media/image56.png)

10. 在“**Custom column**”对话框中，您可以使用 Power Query
    公式语言（也称为 M 语言）来定义新列的计算方式。在**New column
    name**中输入
    +++**TotalAfterDiscount+++** ，在数据类型中选择“**Currency**”，并在**Custom
    column formula**中提供以下 M 表达式。:

+++if [total_amount] > 0 then [total_amount] * ( 1 -[Discount] ) else [total_amount]+++

然后选择**OK**。

![](./media/image57.png)

![A screenshot of a computer Description automatically
generated](./media/image58.png)

11. 选择新创建的......**TotalAfterDiscount** 列，然后在编辑器窗口顶部选择“**Transform**”标签。在**Number
    column** 组中，选择“**Round...**.”下拉菜单，然后选择“**Rounding**”

**注意：**如果找不到**rounding**选项，请展开菜单查看**Number column**。

![](./media/image59.png)

12. 在“**Round**”对话框中，输入 **2** 作为小数位数，然后选择“**OK**”。

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

**任务8：将输出查询加载到Lakehouse中的表中**

当输出查询完全准备好并准备输出数据后，我们可以定义查询的输出目的地。

1.  选择之前创建的 **Output** 合并查询。然后选择 **+ icon** ，将**data
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

11. 确认**Output**表是否出现在 **dbo** 模式下。

![](./media/image72.png)

# 练习3：用数据工厂自动化并发送通知

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

6.  在**Destination**标签页，输入以下设置。

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

7.  从色带中选择**Run**。

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

10. 从你的复制活动中选择并拖动“成功”路径（在管道画布活动右上角的绿色复选框）到你的新的Office
    365 Outlook活动。

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

13. 用你想发送邮件的账户登录。你可以用已经登录的账户使用现有连接。

> ![A screenshot of a computer Description automatically
> generated](./media/image87.png)

14. 点击 **Connect** 以继续。

> ![A screenshot of a computer Description automatically
> generated](./media/image88.png)

15. 在管道画布中选择Office 365 Outlook活动，在 画布下方属性区域的
    **Settings** 标签中选择该邮件。

    - 在“**收件人”**栏输入您的电子邮件地址
      。如果你想使用多个地址，请使用 **;** 把他们分开。

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
>
> ![](./media/image92.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image93.png)

**  注意：**将 **Copy data1** 替换为你自己的管道复制活动名称。

18. 最后，选择管道编辑器顶部的“**Home**”选项卡，然后选择“**Run**”。接着，在确认对话框中选择“**Save
    and run**”以执行这些操作。 

> ![A screenshot of a computer Description automatically
> generated](./media/image94.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image95.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image96.png)

19. 管道成功运行后，查看你的电子邮件，查找管道发送的确认邮件。

![](./media/image97.png)

**任务2：调度流水线执行**

一旦你完成了流程的开发和测试，就可以安排它自动执行。

1.  在 管道编辑器窗口的**Home** 标签中，选择“**Schedule**”。

![A screenshot of a computer Description automatically
generated](./media/image98.png)

2.  根据需要配置时间表。这里的示例安排了流水线每天晚上8点执行，直到年底。

![A screenshot of a schedule Description automatically
generated](./media/image99.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image100.png)

![A screenshot of a schedule AI-generated content may be
incorrect.](./media/image101.png)

**任务3：向管道添加数据流活动**

1.  将鼠标悬停在连接流水线画布上 **Copy activity** 和 **Office 365
    Outlook**活动的绿色线上，选择**+**按钮插入新活动。

> ![A screenshot of a computer Description automatically
> generated](./media/image102.png)

2.  从出现的菜单中选择**Dataflow** 。

![A screenshot of a computer Description automatically
generated](./media/image103.png)

3.  新创建的数据流活动会插入复制活动和Office 365
    Outlook活动之间，并自动选择，在画布下方区域显示其属性。在属性区域选择**Settings **标签，然后选择你在**练习2：在数据工厂中用数据流转换数据时创建**的数据流。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image104.png)

4.  选择管道编辑器顶部的“**Home**”选项卡，然后选择“**Run**”。接着在确认对话框中选择“**Save
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

## 任务四：清理资源

你可以删除单个报表、管道、仓库和其他项目，或者删除整个工作区。请按照以下步骤删除你为本教程创建的工作区。

1.  在左侧导航菜单中选择您的工作区，即**Data-FactoryXX**。它会打开工作区的物品视图。

![](./media/image109.png)

2.  在右上角的工作区页面选择 **Workspace settings** 选项。

![A screenshot of a computer Description automatically
generated](./media/image110.png)

3.  选择**General标签**并**Remove this workspace**。

![A screenshot of a computer Description automatically
generated](./media/image111.png)

