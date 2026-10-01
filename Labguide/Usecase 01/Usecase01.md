# 用例 01：创建 Lakehouse、导入示例数据并构建报表

**场景**

**Wide World Importers (WWI)** 是一家全球零售组织，在多个地区经营数百家门店。客户信息来自多个运营系统，包括销售点 (POS) 应用程序、CRM 平台和电子商务渠道。这些数据以 CSV 文件形式存储，每天从不同的业务部门接收。

公司的分析团队目前花费大量时间手动导入文件、验证数据质量以及准备报表所需的数据集。这些手动流程导致客户洞察的产出延迟，也让业务用户难以获取一致且可靠的信息。

为了实现分析平台的现代化，Wide World Importers 采用 **Microsoft Fabric** 作为统一的数据平台。数据工程团队的任务是使用 **Microsoft Fabric Data Factory** 和 **Lakehouse** 实现可扩展的解决方案，以集中管理客户数据、提高数据管理效率并简化报表制作。

作为数据工程师，你的职责是创建 Fabric 工作区、预配 Lakehouse、将客户数据引入 OneLake、将源文件转换为托管 Delta 表、使用 SQL 分析终结点验证导入的数据、创建 Direct Lake 语义模型，并生成 Power BI 报表，使业务利益相关者能够以最低延迟分析客户信息。

通过实施此解决方案，Wide World Importers 可以免除手动数据准备、为客户分析提供单一事实来源，并利用 Microsoft Fabric 做出更快速、数据驱动的业务决策。

**简介**

在本用例中，你将使用 **Microsoft Fabric Data Factory** 和 **Fabric Lakehouse** 构建完整的数据工程解决方案。从新的 Fabric 工作区开始，你将把数据导入 Lakehouse、将文件转换为托管 Delta 表、使用 SQL 分析终结点查询数据、使用管道和笔记本转换数据、创建语义模型，并生成交互式 Power BI 报表。

在整个实验中，你将了解 Microsoft Fabric 如何将数据集成、存储、转换、分析和报表统一到单一的软件即服务 (SaaS) 平台中。

**本实验创建的 Fabric 项**

| **项** | **名称** | **在实验中的用途** |
|----|----|----|
| 工作区 | Fabric Dataengineering-DataFactory-\<实验实例 ID\> | 包含本实验的所有项 |
| Lakehouse | wwilakehouse | 在 OneLake 中存储原始文件和 Delta 表 |
| 语义模型 | wwisemanticmodel | 用于报表的 Direct Lake 模型 |
| 管道 | IngestDataFromSourceToLakehouse | 复制 WWI 示例数据 |
| 笔记本 | Prepare and transform data – PySpark | 创建事实表、维度表和汇总 Delta 表 |
| 报表 | dimension_customer-report、Profit Reporting | 基于 Lakehouse 数据构建的 Power BI 报表 |

**目标**：

- 创建并配置 Microsoft Fabric 工作区。

- 构建并配置 Fabric Lakehouse。

- 将源数据引入 OneLake。

- 将文件加载到托管 Delta 表中。

- 使用 SQL 分析终结点查询 Lakehouse 数据。

- 使用管道和笔记本引入并转换数据。

- 创建 Direct Lake 语义模型。

- 基于 Fabric 数据生成并浏览 Power BI 报表。

**注意：** 本实验的屏幕截图使用英文界面，因此步骤中的界面名称（例如 **+ New workspace**、**Apply**）保留英文，方便你对照界面操作。

## 练习 1：设置 Microsoft Fabric 数据工程环境

在构建数据工程解决方案之前，你需要先准备 Microsoft Fabric 环境。在本练习中，你将登录 Microsoft Fabric、创建专用工作区，并预配作为分析解决方案集中存储的 Lakehouse。

### 任务 1：登录 Power BI 帐户

1.  打开浏览器，在地址栏中输入或粘贴以下 URL：+++https://app.fabric.microsoft.com/+++，然后按 **Enter** 键。

![](./media/image1.png)

2.  在 **Microsoft Fabric** 窗口中输入你的凭据，然后单击 **Submit** 按钮。

| **用户名** | **+++@lab.CloudPortalCredential(User1).Username+++** |
|----|----|
| **密码** | **+++@lab.CloudPortalCredential(User1).Password+++** |

![](./media/image2.png)

3.  在 **Microsoft** 窗口中输入密码，然后单击 **Sign in** 按钮。

![](./media/image3.png)

4.  在 **Stay signed in?** 窗口中，单击 **Yes** 按钮。

5.  系统会将你转到 Power BI 主页。

![](./media/image4.png)

6.  选择屏幕左下角默认的 Power BI 图标，然后选择 **Fabric**。

![](./media/image5.png)

![](./media/image6.png)

### 任务 2：创建 Fabric 工作区

在此任务中，你将创建 Fabric 工作区。工作区包含本实验所需的所有项，包括 Lakehouse、Data Factory 管道、笔记本、Power BI 语义模型和报表。

1.  在 Fabric 主页上，选择 **+ New workspace** 磁贴。

![](./media/image7.png)

2.  在右侧显示的 **Create a workspace** 窗格中，输入以下详细信息，然后单击 **Apply** 按钮。

| **属性** | **值** |
|----|----|
| **Name** | +++Fabric Dataengineering-DataFactory-@lab.LabInstance.Id+++ |
| **Advanced** | 在 **License mode** 下选择 **Fabric** |
| **Default storage format** | **Small dataset storage format** |

![](./media/image8.png)

**注意：** 若要查找你的实验实例 ID，请选择 **Help** 并复制实例 ID。

![](./media/image9.png)

![](./media/image10.png)

![](./media/image11.png)

3.  等待部署完成，大约需要 2-3 分钟。

![](./media/image12.png)

### 任务 3：创建 Lakehouse

1.  单击导航栏中的 **+ New item** 按钮，创建新的 Lakehouse。

![](./media/image13.png)

2.  单击 **Lakehouse** 磁贴。

![](./media/image14.png)

3.  在 **New lakehouse** 对话框的 **Name** 字段中输入 +++wwilakehouse+++，并**取消选择** **Lakehouse schemas**。单击 **Create** 按钮，然后打开新的 Lakehouse。

**注意**：请确保 **wwilakehouse** 前面没有空格。

![](./media/image15.png)

4.  你会看到 **Successfully created SQL endpoint** 的通知。

![](./media/image16.png)

### 任务 4：导入示例数据

1.  在 **wwilakehouse** 页面上，转到 **Get data in your lakehouse** 部分，然后单击 **Upload files**。

![](./media/image17.png)

2.  在 **Upload files** 选项卡上，单击 **Files** 下的文件夹图标。

![](./media/image18.png)

3.  在虚拟机上浏览到 **C:\LabFiles**，选择 **dimension_customer.csv** 文件，然后单击 **Open** 按钮。

![](./media/image19.png)

4.  单击 **Upload** 按钮。

![](./media/image20.png)

5.  **关闭** **Upload files** 窗格。

![](./media/image21.png)

6.  选择 **Files** 并单击 **Refresh**，文件随即显示。

![](./media/image22.png)

7.  在 **Explorer** 窗格中选择 **Files**。将鼠标指针悬停在 **dimension_customer.csv** 文件上，单击旁边的水平省略号 **(…)**，单击 **Load Table**，然后选择 **New table**。

![](./media/image23.png)

![](./media/image24.png)

8.  在 **Load file to new table** 对话框中，单击 **Load** 按钮。

![](./media/image25.png)

9.  **dimension_customer** 表已成功创建。

![](./media/image26.png)

10. 选择 **Tables** 下的 **dimension_customer** 表。

![](./media/image27.png)

11. 你还可以使用 Lakehouse 的 SQL 终结点，通过 SQL 语句查询数据。从屏幕右上角的 **Analyze data with** 下拉菜单中选择 **SQL analytics endpoint**。

![](./media/image28.png)

12. 在 **wwilakehouse** 页面的 **Explorer** 下，选择 **dimension_customer** 表以预览其数据，然后选择 **New SQL query** 编写 SQL 语句。

![](./media/image29.png)

13. 以下示例查询按 **dimension_customer** 表的 **BuyingGroup** 列汇总行数。SQL 查询文件会自动保存以供日后参考，你可以根据需要重命名或删除这些文件。粘贴代码，然后单击播放图标**运行**脚本。

```sql
SELECT BuyingGroup, Count(*) AS Total
FROM dimension_customer
GROUP BY BuyingGroup
```

![](./media/image30.png)

**注意**：如果执行脚本时出现错误，请检查脚本语法中是否有多余的空格。

14. 以前，所有 Lakehouse 表和视图都会自动添加到语义模型中。在最近的更新之后，新的 Lakehouse 需要手动将表添加到语义模型中。

15. 在 Lakehouse 的 **Home** 选项卡上，选择 **New semantic model**。

![](./media/image31.png)

16. 在 **New semantic model** 对话框中输入 +++wwisemanticmodel+++，从表列表中选择 **dimension_customer** 表，然后单击 **Confirm** 创建新模型。

![](./media/image32.png)

### 任务 5：构建报表

1.  在左侧导航窗格中，选择 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**。

![](./media/image33.png)

2.  在工作区中找到 **wwisemanticmodel** 语义模型，选择 **...**（省略号）菜单，然后选择 **Auto-create report**。

![](./media/image34.png)

![](./media/image35.png)

3.  报表准备就绪后，单击 **View report now** 打开并查看报表。

![](./media/image36.png)

![](./media/image37.png)

4.  由于此表是维度表且没有度量值，Power BI 会创建行计数度量值、在不同列之间汇总，并生成如上图所示的各种图表。

5.  从顶部功能区选择 **Save** 保存此报表。

![](./media/image38.png)

6.  在 **Save your report** 对话框中，输入 +++dimension_customer-report+++ 作为名称，然后单击 **Save**。

![](./media/image39.png)

7.  你会看到 **Report saved** 的通知。

![](./media/image40.png)

## 练习 2：在 Fabric Lakehouse 中引入和管理数据

在本练习中，你将使用 Data Factory 管道，把 Wide World Importers (WWI) 示例数据中的其他维度表和事实表引入 Lakehouse。

### 任务 1：引入数据

1.  在左侧导航窗格中，选择 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**。

![](./media/image41.png)

2.  在工作区页面上，单击 **+ New item** 按钮，然后选择 **Pipeline**。

![](./media/image42.png)

3.  在 **New pipeline** 对话框中，输入 +++IngestDataFromSourceToLakehouse+++ 作为名称，然后单击 **Create**。系统会创建并打开新的 Data Factory 管道。

![](./media/image43.png)

![](./media/image44.png)

4.  在新管道的 **Home** 选项卡上，选择 **Pipeline activity** \> **Copy data**。

![](./media/image45.png)

5.  选择画布上新的 **Copy data** 活动。活动属性显示在画布下方的窗格中，分为 **General**、**Source**、**Destination**、**Mapping** 和 **Settings** 等选项卡。你可能需要拖动窗格顶部边缘，将窗格向上展开。

![](./media/image46.png)

6.  在 **General** 选项卡的 **Name** 字段中输入 +++Data Copy to Lakehouse+++。其他字段保留默认值。

![](./media/image47.png)

7.  在 **Source** 选项卡上，选择 **Connection** 下拉列表，然后选择 **Browse all**。

![](./media/image48.png)

8.  在 **Choose a data source to get started** 页面上，搜索并选择 **Azure Blobs**。

![](./media/image49.png)

9.  在 **Connect data source** 页面上输入以下详细信息，然后单击 **Connect**。所有示例数据都存放在 Azure Blob 存储的公共容器中。

| **属性** | **值** |
|----|----|
| **Account name or URL** | +++https://fabrictutorialdata.blob.core.windows.net/sampledata/+++ |
| **Connection** | **Create new connection** |
| **Connection name** | +++wwisampledata+++ |
| **Authentication kind** | **Anonymous** |

![](./media/image50.png)

10. 在 **Source** 选项卡上，默认会选择新创建的连接。在配置目标之前，请先指定以下属性。

| **属性** | **值** |
|----|----|
| **Connection** | **wwisampledata** |
| **File path type** | **File path** |
| **File path** | 容器名称（第一个文本框）：+++sampledata+++ <br> 目录名称（第二个文本框）：+++WideWorldImportersDW/parquet+++ |
| **Recursively** | **已勾选** |
| **File format** | **Binary** |

![](./media/image51.png)

11. 在 **Destination** 选项卡上，指定以下属性。

| **属性** | **值** |
|----|----|
| **Connection** | **wwilakehouse**（如果你的 Lakehouse 使用了其他名称，请选择该 Lakehouse） |
| **Root folder** | **Files** |
| **File path** | 目录名称（第一个文本框）：+++wwi-raw-data+++ |
| **File format** | **Binary** |

![](./media/image52.png)

12. 单击 **Run** 运行复制数据活动。

![](./media/image53.png)

13. 单击 **Save and run** 按钮，保存并运行管道。

![](./media/image54.png)

14. 数据复制过程大约需要 1-2 分钟完成。

![](./media/image55.png)

15. 在 **Output** 选项卡上，选择 **Data Copy to Lakehouse** 以查看数据传输的详细信息。确认 **Status** 为 **Succeeded** 后，单击 **Close** 按钮。

![](./media/image56.png)

![](./media/image57.png)

16. 管道成功执行后，转到你的 Lakehouse (**wwilakehouse**) 并打开 Explorer 查看导入的数据。

![](./media/image58.png)

17. 刷新 **Files** 部分以查看引入的数据。**Files** 部分中会出现新的 **wwi-raw-data** 文件夹，Azure Blob 存储中的数据已复制到此处。

![](./media/image59.png)

## 练习 3：在 Lakehouse 中准备和转换数据

在本练习中，你将导入 PySpark 笔记本，并使用它从原始数据创建事实表、维度表和汇总 Delta 表。

### 任务 1：导入笔记本并关联到 Lakehouse

1.  在左侧导航窗格中，选择 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**。

![](./media/image60.png)

2.  在工作区页面上，单击命令栏中的 **Import** 下拉列表，然后选择 **New notebook \> From this computer**。

![](./media/image61.png)

3.  在屏幕右侧打开的 **Import status** 窗格中，选择 **Upload**。

![](./media/image62.png)

4.  在虚拟机上浏览到 **C:\LabFiles**，选择 **Prepare and transform data – PySpark** 笔记本，然后单击 **Open** 按钮。

![](./media/image63.png)

![](./media/image64.png)

5.  选择 **wwilakehouse** Lakehouse 将其打开，使接下来打开的笔记本关联到此 Lakehouse。

![](./media/image65.png)

6.  从工具栏选择 **Analyze data with** 下拉菜单，指向 **Notebook**，然后选择 **Existing notebook**。

![](./media/image66.png)

7.  选择导入的笔记本 **Prepare and transform data – PySpark**，然后单击 **Open**。

![](./media/image67.png)

![](./media/image68.png)

### 任务 2：创建 Delta 表

在此任务中，你将运行笔记本单元格，从原始数据创建 Delta 表。这些表采用星型架构，这是组织分析数据的常见模式：

- **事实表** (fact_sale) 包含可衡量的业务事件——在本例中是单笔销售交易，包括数量、价格和利润。

- **维度表** (dimension_city、dimension_customer、dimension_date、dimension_employee、dimension_stock_item) 包含为事实提供上下文的描述性属性，例如销售发生的地点、由谁完成以及发生的时间。

1.  **Cell 1 - Spark 会话配置。** 此单元格会启用两项 Fabric 功能，优化后续单元格写入和读取数据的方式。[V-order](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order) 会优化 Parquet 文件布局，以加快读取速度并提高压缩率。[Optimize write](https://learn.microsoft.com/en-us/fabric/data-engineering/tune-file-size#optimize-write) 会减少写入的文件数量并增大单个文件的大小。

```python
spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize", "1073741824")
```

2.  **运行**此单元格，并等待完成后再进行下一步。

![](./media/image69.png)

![](./media/image70.png)

3.  **Cell 2 - Fact - Sale。** 此单元格会从 **Files/wwi-raw-data/full/fact_sale_1y_full** 读取原始 Parquet 数据、添加日期部分列（**Year**、**Quarter** 和 **Month**），并将 **fact_sale** 写入按 **Year** 和 **Quarter** 分区的 Delta 表。

4.  **运行**此单元格，并等待完成后再进行下一步。

![](./media/image71.png)

5.  **Cell 3 - Dimensions。** 此单元格会读取五个维度 Parquet 数据集，并将其写入 Delta 表（**dimension_city**、**dimension_customer**、**dimension_date**、**dimension_employee** 和 **dimension_stock_item**）。

6.  **运行**此单元格，并等待完成后再进行下一步。

![](./media/image72.png)

7.  若要验证创建的表，请在 Explorer 中右键单击 **wwilakehouse** Lakehouse，然后选择 **Refresh**。表随即显示。

![](./media/image73.png)

![](./media/image74.png)

### 任务 3：转换业务数据以进行汇总

在此任务中，你将继续使用同一个笔记本，运行接下来的单元格，基于上一个任务创建的 Delta 表创建汇总表。

1.  确认笔记本仍关联到 **wwilakehouse**。

2.  **Cell 4 - 加载要转换的源表。** 运行此单元格，将 Delta 表加载到 DataFrame 中，供后续汇总步骤使用。等待完成后再进行下一步。

![](./media/image75.png)

3.  **Cell 5 - 创建 aggregate_sale_by_date_city。** 此单元格会联接销售、日期和城市数据，然后创建城市级别的汇总表。**运行**此单元格并等待完成。

![](./media/image76.png)

4.  **Cell 6 - 创建 aggregate_sale_by_date_employee。** 此单元格会联接销售、日期和员工数据，然后创建员工级别的汇总表。**运行**此单元格并等待完成。

![](./media/image77.png)

5.  若要验证创建的表，请在 Explorer 中右键单击 **wwilakehouse** Lakehouse，然后选择 **Refresh**。汇总表随即显示。

![](./media/image78.png)

![](./media/image79.png)

6. 執行筆記本中「Path 2 - Lakehouse schemas not enabled (alternate path)」區段的所有儲存格，以在 Lakehouse 中建立所需的資料表。

## 练习 4：在 Microsoft Fabric 中构建报表

在本练习中，你将把所有表添加到 Direct Lake 语义模型、在表之间创建关系，并从头开始构建 Power BI 报表。

### 任务 1：在 Direct Lake 语义模型中创建关系

Power BI 原生集成在整个 Fabric 体验中。这种原生集成带来一种称为 **Direct Lake** 的独特模式，可访问 Lakehouse 中的数据，提供最高性能的查询和报表体验。Direct Lake 直接从数据湖加载 Parquet 格式的文件，无需查询数据仓库或 Lakehouse 终结点，也无需将数据导入或复制到 Power BI 语义模型中。

在传统的 **DirectQuery** 模式中，Power BI 引擎会针对每个查询直接从源查询数据，查询性能取决于数据检索速度。DirectQuery 无需复制数据，可确保源中的任何更改都能立即反映在查询结果中。在 **Import** 模式中，由于数据已存放在内存中，性能更好，但 Power BI 引擎必须在数据刷新时先将数据复制到内存中，而源中的更改只有在下次刷新时才会被获取。

Direct Lake 直接将数据文件加载到内存中，免除了这一导入要求。由于没有显式的导入过程，它可以在源发生更改时即时获取更改，结合了 DirectQuery 和 Import 模式的优点，同时避免了它们的缺点。因此，Direct Lake 是分析超大型数据集以及源频繁更新的数据集的理想选择。

1.  从左侧菜单中选择 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id** 工作区，然后选择名为 **wwisemanticmodel** 的语义模型。

2.  打开语义模型，选择右上角的模式下拉列表，从 **Viewing** 切换为 **Editing**，然后选择 **Make any changes**。

![](./media/image80.png)

3.  在功能区中选择 **Edit tables**，以显示表同步对话框。

![](./media/image81.png)

4.  在 **Edit semantic model** 对话框中**选择所有**表，然后单击对话框底部的 **Confirm**，以同步语义模型。

![](./media/image82.png)

![](./media/image83.png)

5.  从 **fact_sale** 表中拖动 **CityKey** 字段，放到 **dimension_city** 表的 **CityKey** 字段上以创建关系。此时会显示 **Create Relationship** 对话框。

**注意：** 单击并拖动表来重新排列，使 **dimension_city** 和 **fact_sale** 表彼此相邻。在任意两个要创建关系的表之间都可以这样做，以便更轻松地在表之间拖放列。

![](./media/image84.png)

6.  在 **Create Relationship** 对话框中：

- **Table 1** 已填充 **fact_sale** 和 **CityKey** 列。

- **Table 2** 已填充 **dimension_city** 和 **CityKey** 列。

- **Cardinality**：**Many to one (\*:1)**

- **Cross filter direction**：**Single**

- 保持选中 **Make this relationship active** 旁边的复选框。

- 选中 **Assume referential integrity** 旁边的复选框。

- 单击 **Save**。

![](./media/image85.png)

7.  使用与上述相同的 **Create Relationship** 设置，添加以下关系：

| **源 (fact_sale)** | **目标** |
|----|----|
| **StockItemKey** | **StockItemKey** (dimension_stock_item) |
| **SalespersonKey** | **EmployeeKey** (dimension_employee) |
| **CustomerKey** | **CustomerKey** (dimension_customer) |
| **InvoiceDateKey** | **Date** (dimension_date) |

![](./media/image86.png)

![](./media/image87.png)

![](./media/image88.png)

8.  添加这些关系后，你的数据模型应如下图所示，并可用于报表。

![](./media/image89.png)

### 任务 2：构建报表

1.  从顶部功能区选择 **File**，然后选择 **Create new report**，开始在 Power BI 中创建报表。

![](./media/image90.png)

2.  在 Power BI 报表画布上，你可以将 **Data** 窗格中的列拖到画布上，并使用一个或多个可用的视觉对象，创建满足业务需求的报表。

![](./media/image91.png)

**添加标题：**

3.  在功能区中选择 **Text box**。输入 +++WW Importers Profit Reporting+++，突出显示文本，并将字号增大为 **20**。

![](./media/image92.png)

4.  调整文本框大小，将其放在报表页面的**左上角**，然后单击文本框外部。

![](./media/image93.png)

**添加卡片：**

5.  在 **Data** 窗格中展开 **fact_sale**，并勾选 **Profit** 旁边的复选框。此选择会创建柱形图，并将字段添加到 Y 轴。

![](./media/image94.png)

6.  选中图表后，在 **Visualizations** 窗格中选择 **Card** 视觉对象。

![](./media/image95.png)

7.  此选择会将视觉对象转换为卡片。将卡片放在标题下方。

![](./media/image96.png)

8.  单击空白画布上的任意位置（或按 **Esc** 键），使卡片不再处于选中状态。

**添加条形图：**

9.  在 **Data** 窗格中展开 **fact_sale**，并勾选 **Profit** 旁边的复选框。此选择会创建柱形图，并将字段添加到 Y 轴。

![](./media/image97.png)

10. 在 **Data** 窗格中展开 **dimension_city**，并勾选 **SalesTerritory** 的复选框。此选择会将字段添加到 X 轴。

![](./media/image98.png)

11. 选中图表后，在 **Visualizations** 窗格中选择 **Clustered bar chart** 视觉对象。此选择会将柱形图转换为条形图。

![](./media/image99.png)

12. 调整条形图大小，填满标题和卡片下方的区域。

![](./media/image100.png)

13. 单击空白画布上的任意位置（或按 **Esc** 键），使条形图不再处于选中状态。

**构建堆积面积图视觉对象：**

14. 在 **Visualizations** 窗格中，选择 **Stacked area chart** 视觉对象。

![](./media/image101.png)

15. 将堆积面积图重新定位并调整大小，放在卡片和条形图视觉对象的右侧。

![](./media/image102.png)

16. 在 **Data** 窗格中展开 **fact_sale**，并勾选 **Profit** 旁边的复选框。展开 **dimension_date**，并勾选 **FiscalMonthNumber** 旁边的复选框。此选择会创建按财月显示利润的填充折线图。

![](./media/image103.png)

17. 在 **Data** 窗格中展开 **dimension_stock_item**，并将 **BuyingPackage** 拖到 **Legend** 字段。此选择会为每个购买包添加一条线。

![](./media/image104.png)

![](./media/image105.png)

18. 单击空白画布上的任意位置（或按 **Esc** 键），使堆积面积图不再处于选中状态。

**构建柱形图：**

19. 在 **Visualizations** 窗格中，选择 **Stacked column chart** 视觉对象。

![](./media/image106.png)

20. 在 **Data** 窗格中展开 **fact_sale**，并勾选 **Profit** 旁边的复选框。此选择会将字段添加到 Y 轴。

21. 在 **Data** 窗格中展开 **dimension_employee**，并勾选 **Employee** 旁边的复选框。此选择会将字段添加到 X 轴。

![](./media/image107.png)

22. 单击空白画布上的任意位置（或按 **Esc** 键），使图表不再处于选中状态。

23. 从功能区选择 **File** \> **Save**。

![](./media/image108.png)

24. 输入 +++Profit Reporting+++ 作为报表名称，然后单击 **Save**。

![](./media/image109.png)

25. 你会收到报表已保存的通知。

![](./media/image110.png)

## 练习 5：清理资源

你可以删除单个报表、管道、仓库和其他项，或删除整个工作区。请按照以下步骤删除你为本实验创建的工作区。

1.  从左侧导航菜单中选择你的工作区 **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**，随即打开工作区项视图。

2.  选择工作区名称下的 **...** 选项，然后选择 **Workspace settings**。

![](./media/image111.png)

3.  选择 **General**，然后选择 **Remove this workspace**。

![](./media/image112.png)

4.  在弹出的警告中单击 **Delete**。

![](./media/image113.png)

5.  等待工作区已删除的通知出现后，再继续进行下一个实验。

![](./media/image114.png)

**摘要**

在本实验中，你实现了完整的 Microsoft Fabric 数据工程工作流。你创建了 Fabric 工作区和 Lakehouse、上传源数据并加载到 Delta 表、使用 SQL 查询验证数据，并构建了快速报表。接着，你使用 Data Factory 管道引入 Wide World Importers 示例数据，并使用 PySpark 笔记本创建事实表、维度表和汇总 Delta 表。最后，你构建了包含关系的 Direct Lake 语义模型，并创建了 Power BI 报表。这些技能为使用 Microsoft Fabric 开发可扩展的数据工程解决方案奠定了基础。
