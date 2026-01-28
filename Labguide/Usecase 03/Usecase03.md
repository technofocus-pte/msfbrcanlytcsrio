# 用例03——使用 Fabric 数据代理与你的数据聊天

**介绍：**

该用例将向你介绍Microsoft
Fabric的数据代理，支持对结构化数据集进行自然语言查询。通过利用大型语言模型（LLM），Fabric
Data Agent 可以解释简单的英语问题，并将其翻译成有效的 T-SQL
查询，运行在您选择的湖屋数据上。这个动手练习将引导你完成配置环境、搭建Fabric工作区、上传数据，以及利用AI技能与你的数据进行对话式互动的过程。你还将探索高级功能，比如提供查询示例、添加提高准确性说明，以及从Fabric笔记本中程序化调用AI技能

**目标:**

- 搭建一个Fabric工作区，并将数据加载到 lakehouse 中。

- 创建并配置一个数据代理，以支持自然语言查询。

- 用通俗易懂的英语提问，并查看AI生成的SQL查询结果。

- 通过自定义指令和示例查询来增强AI回答。

- 在 Fabric 笔记本中编程使用 Data agent。

## **任务0：同步主机环境时间**

1.  在您的VM中，导航并单击 **Search
    bar**，键入“**Settings**”，然后单击“**Best
    match**”下的“**Settings**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image1.png)

2.  在设置窗口中，点击 **Time & language**。

![A screenshot of a computer Description automatically
generated](./media/image2.png)

3.  在“**Time & language**”页面，点击“**Date & time**”。

![A screenshot of a computer Description automatically
generated](./media/image3.png)

4.  向下滚动，进入“**Additional settings**”部分，然后点击“**Syn
    now**”按钮。同步需要3-5分钟。

![A screenshot of a computer Description automatically
generated](./media/image4.png)

5.  关闭 **Settings** 窗口。

![A screenshot of a computer Description automatically
generated](./media/image5.png)

## **任务1：创建Fabric工作区**

在这个任务中，你需要创建一个Fabric工作区。工作区包含了本 lakehouse
教程所需的所有内容，包括 lakehouse、数据流、Data Factory
管道、笔记本、Power BI 数据集和报表。

1.  打开浏览器，进入地址栏，输入或粘贴以下URL：+++https://app.fabric.microsoft.com/+++
    ，然后按下 **Enter** 键。

> ![A search engine window with a red box Description automatically
> generated with medium confidence](./media/image6.png)

2.  在 **Microsoft Fabric** 窗口中，输入你的凭证，然后点击 **Submit**
    按钮。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image7.png)

3.  然后，在 **Microsoft** 窗口输入密码，点击 **Sign in** 按钮**。**

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image8.png)

4.  在 **Stay signed in?** 窗口，点击**“Yes”**按钮。

> ![A screenshot of a computer error AI-generated content may be
> incorrect.](./media/image9.png)

5.  在工作区面板中选择 **+New workspace**。

> ![](./media/image10.png)

6.  在右侧的 **Create a workspace**
    面板中，输入以下细节，然后点击“**Apply**”按钮。

    |    |   |
    |----|----|
    |**Name**	|+++AI-Fabric-@lab.LabInstance.Id+++ (must be a unique Id) |
    |**Advanced**	|Under License mode, select Fabric capacity|
    |**Default storage format**	|Small dataset storage format|

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image11.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image12.png)

7.  等待部署完成。完成大约需要1-2分钟。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image13.png)

## **任务 2: 建造 lakehouse**

1.  在**Fabric主页**，选择 **+New item** ，选择 **Lakehouse** 瓷砖。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image14.png)

2.  在“**New lakehouse** ”对话框中，输入“**Name**”栏的
    +++**AI_Fabric_lakehouseXX**+++，点击“**Create**”按钮，打开新湖屋。

> **注意**：**AI_Fabric_lakehouseXX** 前请务必清空。
>
> ![A screenshot of a computer Description automatically
> generated](./media/image15.png)

3.  你会看到一条通知，提示 **Successfully created SQL endpoint**。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image16.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image17.png)

4.  接下来，创建一个新的笔记本来查询该表。在“**Home**”功能区中，选择“**Open
    notebook** ”下拉菜单，然后选择“**New notebook**”。 

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image18.png)

## **任务3：将 AdventureWorksDW 数据上传到 Lakehouse**

首先，创建一个lakehouse并填充必要的数据。

如果你已经在warehouse或lakehouse里有 AdventureWorksDW
实例，可以跳过这一步。如果没有，就用笔记本创建一个lakehouse。用笔记本填充lakehouse的数据。

1.  在查询编辑器中，复制粘贴以下代码。选择“**Run
    all **”按钮来执行查询。查询完成后，你会看到结果。

    ```
    import pandas as pd
    from tqdm.auto import tqdm
    base = "https://synapseaisolutionsa.z13.web.core.windows.net/data/AdventureWorks"
    
    # load list of tables
    df_tables = pd.read_csv(f"{base}/adventureworks.csv", names=["table"])
    
    for table in (pbar := tqdm(df_tables['table'].values)):
        pbar.set_description(f"Uploading {table} to lakehouse")
    
        # download
        df = pd.read_parquet(f"{base}/{table}.parquet")
    
        # save as lakehouse table
        spark.createDataFrame(df).write.mode('overwrite').saveAsTable(table)
    ```

> ![A screenshot of a computer Description automatically
> generated](./media/image19.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image20.png)
>
> ![](./media/image21.png)

几分钟后，lakehouse已经收集了所需的数据。

## **任务4：创建数据代理**

1.  现在，点击 左侧导航面板上的 **AI-Fabric-XXXX** 。

![](./media/image22.png)

2.  在 **Fabric** 主页，选择 **+New item**。

![](./media/image23.png)

3.  在“**Filter by item type**”搜索框中，输入 **+++data agent+++**
    并选择 **Data agent**。

![](./media/image24.png)

4.  输入 **+++AI-agent+++** 作为 Data 代理名称，选择 **Create**。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image25.png)

![A screenshot of a computer Description automatically
generated](./media/image26.png)

5.  在 AI 代理页面中，选择 **Add a data source**。

![A screenshot of a computer Description automatically
generated](./media/image27.png)

6.  在 **OneLake catalog** 标签页中，选择 **AI-Fabric_lakehouse
    lakehouse** 并选择 **Add**。

> ![](./media/image28.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image29.png)

1.  然后你必须选择你希望AI技能能访问的表格。

这个实验室使用这些表格:

- DimCustomer

- DimDate

- DimGeography

- DimProduct

- DimProductCategory

- DimPromotion

- DimReseller

- DimSalesTerritory

- FactInternetSales

- FactResellerSales

> ![](./media/image30.png)

## **任务5：提供指令**

1.  当你第一次用列出的表格选择 **factinternetsales**
    来提问时，数据代理会相当准确地回答。

2.  例如，对于+++**What is the most sold product?+++**

![A screenshot of a computer Description automatically
generated](./media/image31.png)

![](./media/image32.png)

3.  复制问题和SQL查询，粘贴到记事本，然后保存记事本，以便后续任务中使用这些信息。

![A screenshot of a computer Description automatically
generated](./media/image33.png)

![A screenshot of a computer Description automatically
generated](./media/image34.png)

4.  选择 **FactResellerSales**，输入以下文字，点击下图所示的
    **Submit图标**。

+++**What is our most sold product?**+++

![A screenshot of a computer Description automatically
generated](./media/image35.png)

![A screenshot of a computer Description automatically
generated](./media/image36.png)

随着你不断尝试查询，应该添加更多指令。

5.  选择 **dimcustomer** ， 输入以下文字，点击 **Submit图标**

+++**How many active customers did we have on June 1st, 2013?**+++

![A screenshot of a computer Description automatically
generated](./media/image37.png)

![A screenshot of a computer Description automatically
generated](./media/image38.png)

7.  把所有问题和SQL查询复制出来，粘贴到记事本里，然后保存记事本，方便后续任务中使用这些信息。

![A screenshot of a computer Description automatically
generated](./media/image39.png)

![A screenshot of a computer Description automatically
generated](./media/image40.png)

8.  选择 **dimdate，FactInternetSales ，**输入以下文字，点击
    **Submit图标** **:**

+++**what are the monthly sales trends for the last year?**+++

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image41.png)

![A screenshot of a computer Description automatically
generated](./media/image42.png)

6.  选择 **dimproduct，FactInternetSales ，**输入以下文字，点击
    **Submit图标 :**

+++**which product category had the highest average sales price?**+++

> ![A screenshot of a computer Description automatically
> generated](./media/image43.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image44.png)

问题的一部分在于“活跃客户”没有正式的定义。模型文本框备注中更多说明可能会有帮助，但用户可能会经常问这个问题。你需要确保AI正确地处理这个问题。

7.  相关查询较为复杂，因此请从“**Setup**”窗格中选择“**Example
    queries**”按钮来提供示例。

> ![A screenshot of a computer Description automatically
> generated](./media/image45.png)

8.  在“Example queries”标签中，选择 **Add example。**

> ![A screenshot of a computer Description automatically
> generated](./media/image46.png)

9.  这里，你应该为你创建的 lakehouse
    数据源添加示例查询。请在问题栏中添加以下问题:

+++What is the most sold product?+++

![A screenshot of a computer Description automatically
generated](./media/image47.png)

10. 添加你保存在记事本中的 query1:  
      
```      
SELECT TOP 1 ProductKey, SUM(OrderQuantity) AS TotalQuantitySold
FROM [dbo].[factinternetsales]
GROUP BY ProductKey
ORDER BY TotalQuantitySold DESC
```

![A screenshot of a computer Description automatically
generated](./media/image48.png)

11. 要添加新的查询字段，请点击 **+Add。**

![A screenshot of a computer Description automatically
generated](./media/image49.png)

12. 在问题栏中添加第二个问题:

+++What are the monthly sales trends for the last year?+++

![A screenshot of a computer Description automatically
generated](./media/image50.png)

13. 把你保存在笔记本里的query3添加进去:  
      
	```      
	SELECT
	    d.CalendarYear,
	    d.MonthNumberOfYear,
	    d.EnglishMonthName,
	    SUM(f.SalesAmount) AS TotalSales
	FROM
	    dbo.factinternetsales f
	    INNER JOIN dbo.dimdate d ON f.OrderDateKey = d.DateKey
	WHERE
	    d.CalendarYear = (
	        SELECT MAX(CalendarYear)
	        FROM dbo.dimdate
	        WHERE DateKey IN (SELECT DISTINCT OrderDateKey FROM dbo.factinternetsales)
	    )
	GROUP BY
	    d.CalendarYear,
	    d.MonthNumberOfYear,
	    d.EnglishMonthName
	ORDER BY
	    d.MonthNumberOfYear
	```
> ![A screenshot of a computer Description automatically
> generated](./media/image51.png)

14. 要添加新的查询字段，请点击 **+Add。**

![A screenshot of a computer Description automatically
generated](./media/image52.png)

15. 在问题栏中添加第三个问题:

+++Which product category has the highest average sales price?+++

![A screenshot of a computer Description automatically
generated](./media/image53.png)

16. 添加你保存在笔记本中的 query4:  
      
```      
SELECT TOP 1
    dp.ProductSubcategoryKey AS ProductCategory,
    AVG(fis.UnitPrice) AS AverageSalesPrice
FROM
    dbo.factinternetsales fis
INNER JOIN
    dbo.dimproduct dp ON fis.ProductKey = dp.ProductKey
GROUP BY
    dp.ProductSubcategoryKey
ORDER BY
    AverageSalesPrice DESC
```

![A screenshot of a computer Description automatically
generated](./media/image54.png)

11. 把你保存在笔记本里的所有查询和SQL查询添加进去，然后点击“**Export
    all”**

![A screenshot of a computer Description automatically
generated](./media/image55.png)

![A screenshot of a computer Description automatically
generated](./media/image56.png)

## **任务6：程序化使用Data代理**

指令和示例都被添加到了Data代理中。随着测试的推进，更多的示例和说明可以进一步提升AI技能。和同事一起看看你是否提供了涵盖他们想问的问题的例子和说明。

你可以在Fabric笔记本中编程使用AI技能。用来判断AI技能是否有已发布的URL值。

1.  在 Data agent Fabric页面，**Home**功能区选择**Settings**。

![A screenshot of a computer Description automatically
generated](./media/image57.png)

2.  在你发布AI技能之前，它没有发布的URL值，如这张截图所示。

3.  关闭AI技能设置。

![A screenshot of a computer Description automatically
generated](./media/image58.png)

4.  在 **Home**页功能区，选择 **Publish**。 

> ![A screenshot of a computer Description automatically
> generated](./media/image59.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image60.png)

9.  点击查看 **View publishing details**

> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

5.  AI代理的公开URL显示在这张截图中。

6.  复制URL粘贴到记事本，然后保存记事本以便在后续步骤中使用这些信息。

> ![A screenshot of a computer Description automatically
> generated](./media/image62.png)

7.  在左侧导航窗格选择**Notebook1。**

![A screenshot of a computer Description automatically
generated](./media/image63.png)

10. 使用单元格输出下方的 **+
    Code** 图标，向笔记本添加一个新的代码单元，输入以下代码并替换
    **URL**。点击 **▷ Run** 按钮，查看输出结果

+++%pip install "openai==1.70.0"+++

> ![](./media/image64.png)
>
> ![](./media/image65.png)

11. 使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元，输入以下代码并替换 **URL**。点击
    **▷ Run** 按钮，查看输出结果

> +++%pip install httpx==0.27.2+++
>
> ![](./media/image66.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image67.png)

8.  使用单元格输出下方的 **+
    Code** 图标，向笔记本添加一个新的代码单元，输入以下代码并替换
    **URL**。点击 **▷ Run** 按钮，查看输出结果

```
import requests
import json
import pprint
import typing as t
import time
import uuid

from openai import OpenAI
from openai._exceptions import APIStatusError
from openai._models import FinalRequestOptions
from openai._types import Omit
from openai._utils import is_given
from synapse.ml.mlflow import get_mlflow_env_config
from sempy.fabric._token_provider import SynapseTokenProvider
 
base_url = "https://<generic published base URL value>"
question = "What datasources do you have access to?"

configs = get_mlflow_env_config()

# Create OpenAI Client
class FabricOpenAI(OpenAI):
    def __init__(
        self,
        api_version: str ="2024-05-01-preview",
        **kwargs: t.Any,
    ) -> None:
        self.api_version = api_version
        default_query = kwargs.pop("default_query", {})
        default_query["api-version"] = self.api_version
        super().__init__(
            api_key="",
            base_url=base_url,
            default_query=default_query,
            **kwargs,
        )
    
    def _prepare_options(self, options: FinalRequestOptions) -> None:
        headers: dict[str, str | Omit] = (
            {**options.headers} if is_given(options.headers) else {}
        )
        options.headers = headers
        headers["Authorization"] = f"Bearer {configs.driver_aad_token}"
        if "Accept" not in headers:
            headers["Accept"] = "application/json"
        if "ActivityId" not in headers:
            correlation_id = str(uuid.uuid4())
            headers["ActivityId"] = correlation_id

        return super()._prepare_options(options)

# Pretty printing helper
def pretty_print(messages):
    print("---Conversation---")
    for m in messages:
        print(f"{m.role}: {m.content[0].text.value}")
    print()

fabric_client = FabricOpenAI()
# Create assistant
assistant = fabric_client.beta.assistants.create(model="not used")
# Create thread
thread = fabric_client.beta.threads.create()
# Create message on thread
message = fabric_client.beta.threads.messages.create(thread_id=thread.id, role="user", content=question)
# Create run
run = fabric_client.beta.threads.runs.create(thread_id=thread.id, assistant_id=assistant.id)

# Wait for run to complete
while run.status == "queued" or run.status == "in_progress":
    run = fabric_client.beta.threads.runs.retrieve(
        thread_id=thread.id,
        run_id=run.id,
    )
    print(run.status)
    time.sleep(2)

# Print messages
response = fabric_client.beta.threads.messages.list(thread_id=thread.id, order="asc")
pretty_print(response)

# Delete thread
fabric_client.beta.threads.delete(thread_id=thread.id)
```
> ![](./media/image68.png)
>
> ![](./media/image69.png)

## **任务7：删除资源**

1.  选择你的工作区，在左侧导航菜单中使用
    **AI-Fabric-XXXX**。它会打开工作区的物品视图。

> ![A screenshot of a computer Description automatically
> generated](./media/image70.png)

2.  选择...... 在工作区名称下选择选项，选择 **Workspace settings**。

> ![A screenshot of a computer Description automatically
> generated](./media/image71.png)

3.  选择 **Other** 并 **Remove this workspace**。 

> ![A screenshot of a computer Description automatically
> generated](./media/image72.png)

4.  点击弹出的警告中“**Delete**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image74.png)

**摘要：**

在本实验室中，你学习了如何利用Microsoft
Fabric的数据代理，释放对话式分析的力量。你配置了一个Fabric工作区，将结构化数据导入
lakehouse，并设置了一个AI技能将自然语言问题转换为SQL查询。你还通过提供指导和示例来优化查询生成，增强了AI代理的能力。最后，你通过Fabric笔记本程序化调用了代理，展示了端到端的AI集成。该实验室通过自然语言和
generative AI 技术，赋能您让企业数据对企业用户更易访问、更易用且更智能。
