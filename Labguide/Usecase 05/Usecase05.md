用例 1 - 从Semantics到洞察：利用 Fabric IQ 本体和 Fabric Data Agents

**简介**

在现代数据平台中，企业通常需要一个以**业务为中心的语义层**，以统一不同数据源和分析模型之间的意义。
Microsoft Fabric IQ 中的
*Ontology（预览）*功能允许您通过定义**企业概念**（如产品、商店和事件）及其**关系**，并将这些定义绑定到跨越湖屋、语义模型和事件流的真实lakehouse，从而构建这一层。

剧情设定中，一家虚构的公司叫**Lakeshore
Retail**，在多个地点销售冰淇淋。通过示例数据，教程展示了如何搭建环境并开始构建涵盖*Store*,*Products*和*SaleEvent*等商业概念的本体。你还会将流数据（比如来自Eventhouse的冷冻室温度）连接到这些概念上，使本体能够支持**跨域推理和查询**，例如：
*“当冷冻室温度升高到-18°C以上时，哪些商店的冰淇淋销量会更低？”*

**目标**

- 准备一个包含必要服务的 Microsoft Fabric 工作空间，包括
  Lakehouse、Eventhouse 和 Ontology（预览版）。

- 通过定义核心实体类型例如Store, Products,
  SaleEvent和Freezer来构建以业务为中心的ontology。

- ind 来自 OneLake 表的静态数据以及从 Eventhouse 到ontology
  实体的时间序列数据。

- 在實體之間建立有意義的關係，以代表真實的業務流程（例如，商店有SaleEvent，商店運營Freezeer）。

- 利用實體實例、關係圖和查詢構建器過濾器探索並驗證本體。

- 通过将本体与Fabric Data Agent（预览）集成，实现自然语言查询。

# 练习一：环境设置

## 任務1：創建Fabric工作區

在這個任務中，你需要創建一個Fabric工作區。工作区包含了本 lakehouse
教程所需的所有内容，包括 lakehouse、数据流、Data Factory
管道、笔记本、Power BI 数据集和报表。

1.  打开浏览器，进入地址栏，输入或粘贴以下URL：+++https://app.fabric.microsoft.com/+++，然后按下**Enter**键，用你的凭证登录

    | Credential | Value |
    |------------|-------|
    | Username | +++@lab.CloudPortalCredential(User1).Username+++ |
    | Password | +++@lab.CloudPortalCredential(User1).Password+++ |

2.  在工作区面板中，点击**+New workspace** 磁贴

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image1.png)

3.  在右侧的**Create a
    workspace** 面板中，输入以下细节，然后点击**“Apply**”按钮。

    | Setting | Value |
    |----------|----------|
    | Name | +++Fabric IQ Ontology@Lab.LabInstance.Id+++ |
    | Advanced | Under **License mode**, select **Fabric capacity** |
    | Default storage format | **Small dataset storage format** |

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image2.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image3.png)
    >
    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image4.png)

## 任务2：建造lakehouse

1.  点击导航栏中的 **+New item **按钮创建新lakehouse。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image5.png)

2.  通過篩選並選擇 **+++Lakehouse+++** 瓦片。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image6.png)

3.  在**“New lakehouse**”对话框中，在 名称字段输入 **+++IQ_Lakehouse+++**，并**取消选择**lakehouse的模式。点击**“Create**”按钮，打开新的lakehouse。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image7.png)
    >
    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image8.png)

4.  你会看到一条通知，提示**Successfully created SQL endpoint**。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image9.png)

## 任务3：导入样本数据

1.  在**IQ_Lakehouse**页面，点击**“Get data in your
    lakehouse**”部分，点击**下图所示的“Upload files”。**

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image10.png)

2.  在“Upload files”标签页中，点击文件下的文件夹

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image11.png)

3.  在虚拟机上浏览到
    **C：\LabFiles\LabFiles**，然后选择**DimProducts.csv、DimStore.csv、FactSale.csv**和**Freezer.csv**文件，点击**Open** 按钮。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image12.png)

4.  然後，點擊**“Upload**”按鈕， 通過選擇“**Upload
    files**”對話框的**X**圖標關閉該對話框。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image13.png)
    >
    ![A screenshot of a upload box AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image14.png)

5.  點擊並選擇**Files**刷新。文件出现了。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image15.png)

6.  在**Lakehouse**页面，在资源管理器面板下选择**“Files**”。现在，将鼠标悬停在**DimProducts.csv**文件上。点击水平椭圆**（...）**
    旁边**DimProducts.csv**。点击**“Load Table**”，然后选择**“New
    table**”。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image16.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image17.png)

7.  在**“Load file to new table**”对话框中，点击**Load**按钮。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image18.png)

8.  现已成功创建了 **DimProducts** 表

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image19.png)

9.  选择 **DimProducts** 表以预览数据。

    \[！note\]**注意**：您可能需要多次點擊**Refresh** 按鈕以預覽數據。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image20.png)

10. 重複步驟7到9，將剩餘文件推入表格。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image21.png)

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image22.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image23.png)
    >
    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image24.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image25.png)
    >
    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image26.png)
    >
    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image27.png)

11. 在左侧导航栏中，选择 **Fabric IQ Ontology**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image28.png)

## 任务4：准备eventhouse

请按照以下步骤将设备流数据文件上传到Eventhouse中的KQL database。

1.  在 **Fabric IQ Ontology** 主页，选择 **+New item**，选择
    **Eventhouse**。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image29.png)

2.  将Eventhouse命名为
    +++**TelemetryDataEH**+++，然后点击**“Create**”按钮。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image30.png)

3.  Eventhouse在准备好时开放![A screenshot of a computer AI-generated
    content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image31.png)

4.  通过选择KQL database名称打开。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image32.png)
    >
    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image33.png)

5.  在**KQL database**的下层功能区，点击**“Get data”**，然后选择**“Local
    file**”，将本地系统的文件上传到database。![A screenshot of a
    computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image34.png)

6.  选择将数据导入新表的目标选项，点击 + New table，并输入表名
    +++**FreezerTelemetry**+++。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image35.png)
    >
    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image36.png)

7.  选择目标表，然后拖拽文件，或点击*Browse for files*以上传数据。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image37.png)

8.  在VM上浏览
    **C:\LabFiles\Lab1**，然后选择***FreezerTelemetry*.csv**文件，点击**Open** 按钮。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image38.png)

9.  点击 **“Next**”按钮

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image39.png)

10. 然后点击**“Finish**”按钮。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image40.png)

11. 等待Data ingestion完成后，点击**Close**。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image41.png)

12. 完成后，KQL database会显示**FreezerTelemetry**表：

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image42.png)

13. 在左侧导航窗格选择**Fabric IQ Ontology**。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image43.png)

# 练习 2：基于 OneLake 构建ontology

## 任务1：创建ontology（预览）项目

1.  在你的Fabric工作区中，选择 **+ New item**。搜索并选择 **Ontology
    (preview)** 项目。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image44.png)

2.  输入 +++**RetailSalesOntology+++** 作为 你的ontology
    **Name**，然后选择**Create**。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image45.png)
    >
    > ** 提示：**
    > Ontology名称可以包含数字、字母和下划线。不要使用空格或破折号。

3.  Ontology在准备好时才会打开。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image46.png)
    >
    > 接下來，根據Lakehouse表中的數據，創建實體類型、數據綁定和關係。

## 任務2：創建實體類型和data bindings

> 首先，創建實體類型。實體類型表示企業中的對象類型。這一步有三種實體類型：*Store*, *Products,*和*SaleEvent*。創建實體類型後，通過綁定源數據列在***IQ_Lakehouse***
> lakehouse表中創建它們的屬性。

### 添加第一entity類型（存儲）

1.  在配置画布的顶部色带或中心，选择**Add entity type**。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image47.png)

2.  输入 +++**Store+++ **作为您的实体类型名称，并选择**Add Entity
    Type**。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image48.png)

3.  *Store* 实体类型被添加到配置画布中，**Entity type
    configuration** 面板可见。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image49.png)

4.  在配置畫布中，選擇**......**在實體名稱旁邊選擇“**Bind data**”。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image50.png)

5.  选择 **Add data binding \> Lakehouse table**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image51.png)

6.  接下来，选择你的data source。选择**IQ_Lakehouse**
    lakehouse，然后选择**Next**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image52.png)

7.  选择**dimstore**表并选择**“Select**”。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image53.png)

8.  源表中的字段填充data binding
    configuration。请查看configuration页面的各个部分：

    - **Entity類型鍵**：識別可用於唯一標識每條被攝取data記錄的字段。

    - **結合選擇**: 識別包含綁定數據的源表。

    - **Entity類型密鑰映射**：識別source
    data表中映射到實體類型密鑰屬性的列。你可以從source
    data中選擇字符串列和整數列作為實體類型鍵。你選擇的列共同唯一標識一條記錄。

    - **属性**：列出源数据中将作为*Store* entity类型属性表示的列。**Source
    column** 端会自动填充来自*dimstore*表的列，**Property
    name** 端则在ontology中的*Store* entity类型中列出对应的属性名称
    。在这个教程中，保留默认的属性名称。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image54.png)

9.  在configuration顶部选择**Define entity type key** 。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image55.png)

10. 從屬性列表中選擇**StoreId**，然後選擇**Save**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image56.png)

11. **保存**data binding。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image57.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image58.png)

12. 確認entity類型已成功更新，然後選擇**Cancel **以關閉配置選項。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image59.png)

13. 你会看到entity类型详情中的**Configure** 页面。本页展示了关于entity类型的重要信息，包括其属性和data
    bindings。查看你配置的data bindings。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image60.png)

14. 选择 **“Home** ”返回configuration画布并添加新的entity类型。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image61.png)

### 添加其他entity类型（产品、SaleEvent）

15. 按照你为存**Store **entity类型所做的相同步骤
    ，创建下表中描述的entity类型。每个entity都有一个静态data
    binding，与其源表的默认列相关联。

    | Entity Type Name | Source Table in IQ_Lakehouse | Entity Type Key |
    |------------------|------------------------------|-----------------|
    | +++Products+++<br><br>**Note:** Use the plural form **Products** to avoid conflict with the GQL reserved word **PRODUCT**. | **dimproducts** | **ProductId** |
    | +++SaleEvent+++ | **factsales** | **SaleId** |

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image62.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image63.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image64.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image65.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image66.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image67.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image68.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image69.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image70.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image71.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image72.png)

16. 选择**“Home**”返回configuration画布并添加 **SaleEvent** entity类型。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image73.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image74.png)

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image75.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image76.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image77.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image78.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image79.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image80.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image81.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image82.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image83.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image84.png)

17. 完成后，你会在**Entity Types** 面板中看到这些entity类型。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image85.png)

## 任務3：創建關係類型

接下來，創建實體類型之間的關係類型，以表示數據中的上下文連接。

### 商店的SaleEvent

1.  从Explorer中选择**SaleEvent**实体类型。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image86.png)

2.  从菜单功能区选择**Add relationship**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image87.png)

3.  输入以下关系类型详情，选择**Add relationship type**。

    - **Relationship type name**: +++from+++

    - **Source entity type**: *SaleEvent*

    - **Target entity type**: *Store*

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image88.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image89.png)

4.  这种关系被加入semantic画布中。选择它以打开关系详情configuration。请查看configuration页面的各个部分：

    - **Origin entity类型**：列出起源entity的详细信息（此处为
    **SaleEvent**）。

    - **關係類型**：設置關係類型的詳細信息。

    - **目標entity類型**：列出目標entity的詳細信息（此處為**Store **）。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image90.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image91.png)

5.  在中間部分，請填寫以下細節。


1.  **Mapping table**：**Browse available
    sources** 并选择**factsales**表。源数据中的该表可以将*Store* 实体和*SaleEvent*实体连接起来，因为它包含了两种实体类型的识别信息。表中的每一行通过ID指向一个门店和一个销售事件。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image92.png)

2.  **Matched SaleEvent: SaleId**:
    选择**SaleId**。该设置指定关系源数据表中与*SaleEvent* entity定义的关键属性匹配的列
    。在这种情况下，关系数据源和entity
    data源都使用*factsales*表，所以你选择的是同一个列（SaleId）。

3.  **Matched Store: StoreId**: 选择**StoreId**。该设置指定关系源数据表
    (*factsales \>* StoreId) 中值与*Store* entity*（*dimstore \>
    *StoreId ）定义的关键属性匹配*
    的列。在教程数据中，两个表的列名（StoreId）是相同的。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image93.png)

    **  
    重要提示：**確保選擇 與entity 類型**匹配**的匹配列，關鍵屬性。

6.  **保存**关系类型。确认关系类型已成功更新，然后选择**Cancel **关闭configuration选项。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image94.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image95.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image96.png)
    >
    > 現在第一個關係被創建，並綁定到源表中的數據。繼續進入下一部分，創建另一種關係類型。

### **SaleEvent 销售产品**

1.  选择**“Home**”返回配置画布，在那里你可以添加新的entity类型。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image97.png)

2.  按照你在第一种关系类型中使用的相同步骤，从**SaleEvent**
    entity类型创建第二种关系，具体细节如下表所述。


    | Relationship Type Name | Origin Entity Type | Target Entity Type | Mapping Table | Matched SaleEvent: SaleId | Matched Products: ProductId |
    |------------------------|-------------------|-------------------|---------------|--------------------------|----------------------------|
    | `sold` | `SaleEvent` | `Products` | `factsales` | `SaleId` | `ProductId` |

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image98.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image99.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image100.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image101.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image102.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image103.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image104.png)

# 練習3：用額外數據豐富ontology

在這個練習中，你通過添加一個新的***Freezer***
entity類型來豐富你的ontology。該實體類型增加了更多的領域上下文，並引入了反映實時運營信息的時間序列數據屬性。

** 注釋**

對於靜態和時間序列數據，你可以創建屬性而無需綁定數據，之後再綁定數據，或者在一步驟內創建屬性並綁定數據。本文展示了這兩種方法。

最後，你創建一個新的關係類型來表示商店與其freezers之間的連接。

## 任務1：創建Freezer entity類型並添加屬性

按照以下步驟創建*Freezer*
entity類型並為其添加屬性。屬性還沒有綁定到數據上。

1.  从顶部的色带中选择**Add entity type**。输入
    +++**Freezer+++** 作为你的实体类型名称，然后选择 **Add Entity
    Type**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image105.png)
    >
    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image106.png)

2.  在资源管理器中选择**Explorer** entity体类型后，从顶部色带选择“**View
    entity type details**”。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image107.png)

3.  此時會打開實體類型詳細信息的“**Configure**”頁面。此页面会显示有关entity类型的重要信息，包括其属性和data
    bindings。

    展开**“Manage property bindings”**，然后选择 **Add properties**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image108.png)

4.  添加以下屬性並選擇**Save**。

    | Name | Property Type |
    |------|---------------|
    | `FreezerId` | `String` |
    | `Model` | `String` |
    | `minSafeTempC` | `Double` |
    | `StoreId` | `String` |

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image109.png)

    **注意：** 物業名稱必須在所有 entity類型中唯一。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image110.png)

5.  属性会被添加到**Configure **页面，且不受绑定到任何data source。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image111.png)

## 任務2：將靜態數據綁定到屬性

接下來，將靜態數據綁定到你在*Freezer* entity類型上創建的屬性。

1.  展开**“Manage property bindings”**，选择**Add binding and
    properties**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image112.png)

2.  选择 **Add data binding \> Lakehouse table**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image113.png)

3.  选择你的 data source。

    - 选择 **IQ_Lakehouse** lakehouse，然后选择 **Next**。

    - 选择 **freezer** 柜台并 **Select**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image114.png)
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image115.png)

4.  源表中的字段填充data binding
    configuration。请查看configuration页面的各个部分：

    - **Entity類型鍵**：識別可用於唯一標識每條被攝取數據記錄的字段。

    - **Binding選擇**：識別存放綁定data的源表。

    - **Entity類型密鑰映射**：識別源數據表中映射到entity類型密鑰屬性的列。你可以從源數據中選擇字符串列和整數列作為entity類型鍵。你选择的列共同唯一标识一条记录。

    - **屬性**：列出源數據中的列及**Freezer** entity類型對應的屬性。**源端**會自動填充來自**freezer** 表的列，**屬性名**稱端則在ontology中的**Freezer** entity類型中列出對應的屬性名稱
    。在這個教程中，保留默認的屬性名稱。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image116.png)

5.  在配置顶部选择**Define entity type
    key**。从属性列表中选择FreezerId，然后选择**Save**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image117.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image118.png)

6.  **保存**data
    binding。确认实体类型已成功更新，然后选择**Cancel** 以关闭configuration选项。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image119.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image120.png)

## 任務3：將時間序列數據綁定到附加屬性

接下来，通过创建一个新属性并在单一data binding操作中绑定时间序列
data，在 **Freezer **entity上添加时间序列 data。

1.  在**Configure** 页面，展开**“Manage property
    bindings”**，再次选择**Add binding and
    properties** 以重新打开binding configuration。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image121.png)

2.  在**“Binding”选择中**，展开**“Add data binding”**，然后选择
    **Eventhouse table or materialized view**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image122.png)

3.  选择你的data source。

    1.  选择 **TelemetryDataEH** eventhouse并选择**Add**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image123.png)

2.  选择**FreezerTelemetry**表并**Add**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image124.png)

4.  配置中会出现**Timeseries data** 部分。对于**Timestamp
    column**，选择timestamp

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image125.png)

5.  向下滾動到**Properties**部分，**StoreId**顯示錯誤，因為它已經被綁定在靜態數據綁定中。用垃圾桶圖標刪除重複的屬性。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image126.png)

6.  **保存**data
    binding。确认entity类型已成功更新，然后选择**Cancel** 以关闭配置选项。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image127.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image128.png)

7.  回到 **Configure** 页面為
    *Freezer*，注意到现在有了更多entity类型属性，而且新的属性都绑定到了
    *FreezerTelemetry* 数据源。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image129.png)

    现在 *Freezer* entity有两个data bindings：一个是来自 *Freezer* Lakehouse
    表的静态数据，另一个是来自 *FreezerTelemetry* eventhouse 表的流data。

## 任务4：添加关系类型

最後，創建一個新的關係類型來表示商店與其freezers之間的連接。

**Create Store 运营 Freezer**

1.  在 **Configure** 页面，展开“Manage relationships”并选择 **Add new
    relationship**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image130.png)

2.  输入以下关系类型详情，选择**Add relationship type**。

    1.  **Relationship type name**: *operates*

    2.  **Source entity type**: *Store*

    3.  **Target entity type**: *Freezer*

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image131.png)

3.  该关系被添加到**“Relationships”**部分。在画布上选择
    **operates** 关系以打开关系详情 configuration。请查看 configuration
    页面的各个部分：

    - **Origin entity type**: 列出源 entity 的详细信息（此处为 *Store*）。

    - **Relationship type**: 列出关系类型的详细信息。

    - **Target entity type**: 列出目标 entity 的详细信息（此处指
    *Freezer*）。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image132.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image133.png)

4.  在中間部分，請填寫以下細節。

    - **Mapping
    table**：选择**freezer** 台。源数据中的该表可以将*Store*和*Freezer* *entity*
    连接起来，因为它包含了两种entity
    类型的识别信息。表中的每一行通过ID指向一个商店和一个freezer。

    - **Matched Store:
    StoreID**：选择**StoreId**。该设置指定关系源数据表（*freezer \>*
    StoreId）中与存储 *entity（*dimstore \> *StoreId
    ）*定义的关键属性匹配的列。在教程
    data中，两个表的列名（StoreId）是相同的。

    - **Matched Freezer: FreezerId**: 选择**FreezerId。**
    该设置指定关系源数据表中与Freezer entity的关键属性匹配的列
    。在这种情况下，关系数据源和entity data
    source都使用*了freezer* 表，所以你选择的是同一个列（FreezerId）。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image134.png)

    **  
    重要提示：** 確保選擇與 entity類型關鍵屬性匹配的正確源列。

5.  **保存**关系类型。确认关系类型已成功更新，然后选择**Cancel** 关闭configuration选项。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image135.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image136.png)

6.  你会看到实体的**Configure** 页面，更新后的关系仍可见于
    **Relationships** 部分。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image137.png)

# 練習4： **查看ontology**

在這個練習中，利用預覽體驗探索你的ontology。檢查那些用數據實例化實體類型的entity實例，探索銷售和設備流數據中的圖形上下文。

## 任務1： **查看實例列表和靜態數據**

當你在之前的教程步驟中將數據綁定到entity類型時，ontology會自動創建這些實體的實例，這些實例與源數據行綁定。在本節中，你使用預覽體驗來查看這些實體實例。

1.  从ontology的 Home configuration画布开始。选择**SaleEvent**
    entity类型，并 从顶部丝带查看 **View Entity Type details**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image138.png)

2.  打开**Instances**
    标签页。确认它显示六个实体实例，数据来自**Factsales**
    lakehouse表，如收入和单位数量。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image139.png)

## 任務2：查看時間序列數據

1.  在頁面左上角，使用實體類型名稱旁的選擇器切換到**Freezer** entity類型。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image140.png)

2.  打开“**Overview**”标签页。标签页加载的是空白图表，因为默认的时间范围“**Last
    30 days** ”不包含任何data。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image141.png)

3.  将时间范围从默认的 **Last 30
    days** 更新为自定义日期范围，该时间范围从**2025年8月1日星期五凌晨12：00开始，**到**2025年8月4日星期一凌晨12：00**结束，**Time
    granularity** 为**5分钟**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image142.png)

4.  觀察你選擇的時間窗口內多個**Freezer **entity實例現在可見的時間序列
    data。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image143.png)

## 任务3： **查看Ontology图**

**“Overview**”标签还包含**Relationship
graph**，你可以用它在节点和边的图中可视化你的ontology。

1.  使用entity类型选择器切换到**SaleEvent** entity类型。在**Relationship
    graph** tile中，选择**“Expand**”。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image144.png)

2.  他擴展了圖視圖的打開。观察从**SaleEvent**
    entity类型到**Products**和**Store**之间的关系细节。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image145.png)

3.  使用entity类型选择器切换到**Store **entity类型。 **扩展** 其
    **relationship graph。**

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image146.png)

4.  在圖表中，觀察**Store**與**Freezer**和**SaleEvent**之間的關係。然後，在查詢構建功能區選擇**“Run
    query**”。此操作運行默認查詢，並顯示實體實例及其連接的圖

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image147.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image148.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image149.png)

## 任務4：查詢圖實例

在關係圖視圖中，你可以查詢符合特定條件的entity實例。使用顶部功能区的**Query
builder** 过滤器来创建查询。

![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image150.png)

首先，設計這個問題：*顯示巴黎門店運營的所有freezers。*

1.  在*Store* entity的关系图中，从查询构建器功能区选择 **Add filter \>
    Store \> StoreId** 。设置 **StoreID = S-PAR-01**
    的过滤器。这个值是巴黎门店的门店ID。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image151.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image152.png)

5.  在**Components**部分取消勾选*SaleEvent*，只勾选 **Nodes \>
    Store**, **Nodes \> Freezer**和 **Edges \> operates**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image153.png)

6.  選擇**Run query**，確認實例圖顯示兩台freezers連接到*巴黎*商店。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image154.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image155.png)

7.  选择**Clear query**以清除查询结果。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image156.png)

    接下來，設計這個問題：*顯示所有銷售額超過150的商店。*

8.  选择**Add a node**，添加**SaleEvent**节点。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image157.png)

9.  在**Components**部分，勾选 **Nodes \> Store Edges \>
    from** 的框，将它们添加到图中。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image158.png)

10. 在query builder功能区中，选择 **Add filter \> SaleEvent \>
    RevenueUSD**。将过滤器设置为 +++**RevenueUSD \> 150+++**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image159.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image160.png)

11. 選擇**Run
    query**，並驗證實例圖是否顯示兩家門店符合其關聯銷售事件的篩選條件。你也可以選擇圖表中的節點，查看具體促銷活動的詳細信息

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image161.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image162.png)

    這個流程可以讓你檢查將運營問題（比如某些門店freezer溫度升高）與業務成果（銷售）聯繫起來的路徑。

# 练习 5：从代理中获取**ontology**

Ontology（预览）与 [Fabric
数据代理（预览版）](https://learn.microsoft.com/en-us/fabric/data-science/concept-data-agent)集成，允许你用自然语言提问，并基于Ontology的定义和绑定获得答案。

## 任務 1：創建具有ontology（預覽）源的數據代理

按照以下步驟創建一個新的data代理，連接到你的ontology（預覽）項目。

1.  现在，点击左侧导航窗格上的 **Fabric IQ Ontology XX**。

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image163.png)

2.  在**Fabric**主页，选择 **+New item。**
    在“按项目类型筛选”搜索框中，输入 +++**data agent**+++ 并选择 Data
    agent

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image164.png)

3.  输入 **+++RetailOntologyAgent+++**
    作为数据代理名称，并选择**Create**。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image165.png)

4.  在 **RetailOntologyAgent** 页面中，选择**Add a data source**

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image166.png)

5.  在 OneLake catalog标签页中，选择 **RetailSalesOntology**
    Ontology，并选择 **Add。**

    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image167.png)
    >
    > 當代理準備好時，門就會打開。
    >
    ![](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image168.png)

## 任務2：提供代理指令

** 注釋：** 此步驟是針對已知影響查詢聚合的問題而添加的。

> 接下來，向代理添加自定義指令。

1.  从菜单功能区选择**Agent instructions**。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image169.png)

2.  在输入框底部添加+++**Support group by in
    GQL**+++。此指令可以更好地聚合ontology data。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image170.png)

3.  指令是自动执行的。可选地，关闭**Agent instructions** 标签页。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image171.png)

## 任務3：帶自然語言的查詢代理

> 接下来，用自然语言问题探索你的ontology。

1.  輸入以下文字，點擊下圖所示的 **Submit圖標。**

    > **+++For each store, show any freezers operated by that store that
    > ever had a humidity lower than 46 percent.+++**
    >
    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image172.png)

    ![A screenshot of a chat AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image173.png)

2.  輸入以下文字，點擊下圖所示的**Submit圖標**。

    > *+++What is the top product by revenue across all stores?+++*

    ![A screenshot of a chat AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image174.png)

    ![A screenshot of a chat AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image175.png)

    > 注意，这些响应引用的是实体类型（*Store*, *Products*, *Freezer*）及其关系，而不仅仅是原始表。
    >
    ![Screenshot of the result of a query.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image176.png)
    >
    > ** 提示：**
    > 如果你在運行示例查詢時看到錯誤提示無數據，建議等幾分鐘，給代理更多時間初始化。然後，再次運行查詢。
    >
    > 繼續探索數據代理，嘗試一些你自己的提示。

## 任务4：清理资源

1.  選擇您的工作區，即左側導航菜單中的 **Fabric IQ
    OntologyXX**。它會打開工作區的物品視圖。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image177.png)

1.  选择......在工作区名称下选择选项，选择 **Workspace settings**。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image178.png)

2.  导航到“General”标签底部，选择**“Remove this workspace**”。

    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image179.png)
    >
    ![A screenshot of a computer AI-generated content may be
    incorrect.](https://raw.githubusercontent.com/technofocus-pte/fbrciqdtagntrio/refs/heads/main/Cloudslice-CNT/Labguides/Usecase%2001/media/image180.png)

    **摘要**

    該用例展示了如何使用 Microsoft Fabric IQ
    Ontology（預覽版）創建一個連接的語義數據模型，代表真實世界的商業概念及其關係。通過將結構化lakehouse數據與流式遙測數據結合，ontology提供了統一且商業友好的企業數據視圖。

    通過實體定義、data
    bindings和關係建模，用戶可以分析運營信號——如freezer溫度或濕度——如何與銷售和收入等業務結果相關。該用例還強調了本體如何通過Fabric
    data代理支持圖探索和自然語言查詢，從而在無需用戶理解底層表或模式的情況下獲得更深入的洞察。

    总体而言，这一用例展示了Fabric IQ
    Ontology如何帮助连接运营数据和分析，支持跨域更智能的决策。
