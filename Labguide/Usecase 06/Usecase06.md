##  用例06——在Microsoft Fabric中利用文档智能识别和提取文本
**介绍 **

分析结构化数据已经是个容易的过程，但非结构化数据则不然。非结构化数据，如文本、图片和视频，更难分析和解读。然而，随着先进AI模型的出现，如OpenAI的GPT-3和GPT-4，分析和获取非结构化数据的洞察变得更容易。

此类分析的一个例子是利用自然语言查询文档的特定信息，这可以通过信息检索和语言生成相结合实现。

通过利用RAG（检索增强生成）框架，你可以创建一个强大的问答流程，利用大型语言模型（LLM）和你自己的数据生成回答。

此类应用的架构如下所示:

![Architecture diagram connecting Azure OpenAI with Azure AI Search and
Document Intelligence](./media/image1.png)

**目标**

- 使用 Azure 门户为 Azure AI 服务创建多服务资源

- 创建 Fabric 容量和工作区，Key Vault 和 Fabric 工作区

- 在Azure AI服务中使用Azure AI文档智能预处理PDF文档。

- 使用SynapseML进行文本分块处理。

- 使用 SynapseML 和 Azure OpenAI Services 生成区块嵌入。

- 将嵌入存储在 Azure AI 搜索中。

- 建立一个问答流程。

# **练习1：环境设置**

## 任务1：为Azure AI服务创建多服务资源

多服务资源列在门户的“**Azure AI services \> Azure AI services
multi-service account**”下。要创建多服务资源，请按照以下说明操作:

1.  选择此链接创建多服务资源: 

++++https://portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne+++

    |Project details | Description |
    |-----|----|
    |Subscription|	@lab.CloudSubscription.Name |
    |Resource group|	@lab.CloudResourceGroup(ResourceGroup1).Name|
    |Region|	Select the appropriate region for your CognitiveServices. In this lab, we have chosen the **East US 2** region.|
    |Name	|+++Cognitive-service@lab.LabInstance.Id+++ (must be a unique Id)|
    |Pricing tier	|Standard S0|

2.  在**Create**页面，提供以下信息:

3.  根据需要配置资源的其他设置，阅读并接受条件（如适用），然后选择
    **Review + create**。

![](./media/image2.png)

4.  在**“Review+submit**”标签中，验证通过后，点击 **Create** 按钮。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image3.png)

5.  部署完成后，点击“ **Go to resource** ”按钮。

> ![A screenshot of a computer Description automatically
> generated](./media/image4.png)

6.  在 **Azure** **AI service** 窗口中，导航到“**Resource
    Management**”部分，然后单击“**Keys and Endpoints**”。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image5.png)

7.  在“**Keys and Endpoints**”页面，复制**KEY1、KEY 2**和 **Endpoint**
    的值，并像下图所示粘贴到记事本中，然后**保存**记事本以便在接下来的任务中使用这些信息。

![](./media/image6.png)


## **任务2：在门户中创建Azure AI搜索服务**

1.  在 Azure 门户主页，点击 **+ Create Resource**。

> ![A screenshot of a computer Description automatically
> generated](./media/image7.png)

2.  在“**Create a resource**”搜索栏中，输入“**Azure cognitive
    Search**”，点击已出现的 **azure AI search**。

![](./media/image28.png)

3.  点击 **azure ai search**部分。

![A screenshot of a computer Description automatically
generated](./media/image29.png)

4.  在 **Azure AI Search**页面，点击“**Create**”按钮。

> ![A screenshot of a computer Description automatically
> generated](./media/image30.png)

5.  在“**Create a search
    service**”页面，输入以下信息，点击“**Review+create**”按钮。

|Field	|Description|
|-----|------|
|Subscription	|Select the assigned subscription|
|Resource group	|Select your Resource group|
|Region|	EastUS 2|
|Name	|+++mysearchserviceXXXXX+++(XXXXX can be Lab instant ID)|
|Pricing Tier|	Click on change Price Tire>select Basic|

![](./media/image31.png)

![A screenshot of a computer Description automatically
generated](./media/image32.png)

6.  验证通过后，点击 **Create** 按钮。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image33.png)

8.  部署完成后，点击“ **Go to resource** ”按钮。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image34.png)

9.  复制 **AI search name**
    并粘贴到笔记本中，如下图所示，然后**保存**笔记本以便在即将到来的实验中使用这些信息。

![](./media/image35.png)

## **任务5：创建Fabric工作区**

在这个任务中，你需要创建一个Fabric工作区。工作区包含了本 lakehouse
教程所需的所有内容，包括 lakehouse、数据流、Data Factory
管道、笔记本、Power BI 数据集和报表。

1.  打开浏览器，进入地址栏，输入或粘贴以下URL：
    https://app.fabric.microsoft.com/ 然后按下 **Enter** 键。

> ![A search engine window with a red box Description automatically
> generated with medium confidence](./media/image36.png)

2.  在 **Microsoft Fabric** 窗口中，输入你的凭证，然后点击 **Submit**
    按钮。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image37.png)

3.  然后，在 **Microsoft** 窗口输入密码，点击 **Sign in** 按钮**。**

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image38.png)

4.  在 **Stay signed in?** 窗口，点击**“Yes”**按钮。

> ![A screenshot of a computer error AI-generated content may be
> incorrect.](./media/image39.png)

5.  在工作区面板中选择 **+New workspace**。

> ![](./media/image40.png)

6.  在右侧的 **Create a workspace**
    面板中，输入以下细节，然后点击“**Apply**”按钮。

    |   |   |
    |----|-----|
    |Name	|+++Document Intelligence-Fabric@lab.LabInstance.Id+++ (must be a unique Id)|
    |Advanced|	Select **Fabric Capacity**|
    |Capacity	|Select the available capacity|

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image41.png)
>
> ![](./media/image42.png)

10. 等待部署完成。完成大约需要2-3分钟。

![](./media/image43.png)

## **任务6：建造 lakehouse**

1.  在 **Fabric主**页，选择 **+New item**，选择 **Lakehouse**  瓷砖。

> ![A screenshot of a computer Description automatically
> generated](./media/image44.png)

2.  在“**New
    lakehouse** ”对话框中，在“**Name**”字段中输入+++**data_lakehouse**+++，单击“**Create**”按钮，打开新的
    lakehouse。

> **注意**：**data_lakehouse** 前请务必清空。
>
> ![A screenshot of a computer Description automatically
> generated](./media/image45.png)

3.  你会看到一条通知，提示 **Successfully created SQL endpoint**。

> ![A screenshot of a computer Description automatically
> generated](./media/image46.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image47.png)

# **练习 2：PDF文档加载与预处理 **

## **任务1：配置Azure API密钥**

首先，回到工作区中的rag_workshop Lakehouse，通过选择“Open
Notebook”和选项中的“New Notebook”创建新笔记本。

1.  在 **Lakehouse** 页面中，导航并单击命令栏中的“**Open
    notebook**”下拉列表，然后选择“**New notebook**”。

![A screenshot of a computer Description automatically
generated](./media/image48.png)

2.  在查询编辑器中，粘贴以下代码。 提供Azure AI服务的密钥、Azure Key
    Vault名称和访问服务的密钥

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image49.png)

    ```
    # Azure AI Search
    AI_SEARCH_NAME = ""
    AI_SEARCH_INDEX_NAME = "rag-demo-index"
    AI_SEARCH_API_KEY = ""
    
    # Azure AI Services
    AI_SERVICES_KEY = ""
    AI_SERVICES_LOCATION = ""
    ```

> ![](./media/image50.png)

## 任务2：加载和分析文档

1.  我们将使用一个名为 **support.pdf** 的特定文档 ，作为我们数据的来源。

2.  要下载文档，请使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元格，并在其中输入以下代码。点击 **▷
    Run cell**  按钮，查看输出结果

**Copy**

    ```
    import requests
    import os
    
    url = "https://github.com/Azure-Samples/azure-openai-rag-workshop/raw/main/data/support.pdf"
    response = requests.get(url)
    
    # Specify your path here
    path = "/lakehouse/default/Files/"
    
    # Ensure the directory exists
    os.makedirs(path, exist_ok=True)
    
    # Write the content to a file in the specified path
    filename = url.rsplit("/")[-1]
    with open(os.path.join(path, filename), "wb") as f:
        f.write(response.content)
    ```
![](./media/image51.png)

3.  现在，使用 Apache Spark 提供的
    spark.read.format（“binaryFile”）方法将 PDF 文档加载到 Spark
    DataFrame 中

4.  使用单元格输出下方的 **+ Code** 
    图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击 **▷ Run
    cell** 按钮，查看输出结果

**Copy**

    ```
    from pyspark.sql.functions import udf
    from pyspark.sql.types import StringType
    document_path = f"Files/{filename}"
    df = spark.read.format("binaryFile").load(document_path).select("_metadata.file_name", "content").limit(10).cache()
    display(df)
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image52.png)

该代码将读取 PDF 文档，并生成名为 df 的 Spark DataFrame，包含 PDF
内容。DataFrame 将包含一个表示 PDF 文档结构的模式，包括其文本内容。

5.  接下来，我们将使用Azure AI文档智能读取PDF文档并提取文本。

6.  使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击 **▷ Run
    cell **按钮，查看输出结果

**Copy**

    ```
    from synapse.ml.services import AnalyzeDocument
    from pyspark.sql.functions import col
    
    analyze_document = (
        AnalyzeDocument()
        .setPrebuiltModelId("prebuilt-layout")
        .setSubscriptionKey(AI_SERVICES_KEY)
        .setLocation(AI_SERVICES_LOCATION)
        .setImageBytesCol("content")
        .setOutputCol("result")
    )
    
    analyzed_df = (
        analyze_document.transform(df)
        .withColumn("output_content", col("result.analyzeResult.content"))
        .withColumn("paragraphs", col("result.analyzeResult.paragraphs"))
    ).cache()
    ```

![A screenshot of a computer code AI-generated content may be
incorrect.](./media/image53.png)

7.  我们可以通过以下代码观察分析的名为analyzed_df的火花数据帧。注意我们去掉内容栏，因为它不再需要了。

8.  使用单元格输出下方的 **+
    Code** 图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击
    **▷ Run cell ** 按钮，查看输出结果

**Copy**

    ```
    analyzed_df = analyzed_df.drop("content")
    display(analyzed_df)
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image54.png)

# 练习 3：生成和存储嵌入

## **任务1：文本分块处理**

在生成嵌入之前，我们需要将文本拆分成多个块。为此，我们利用SynapseML的PageSplitter将文档划分为更小的部分，这些部分随后存储在区块列中。这使得文档内容能够实现更细致的表示和处理。

1.  使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击 **▷ Run
    cell** 按钮，查看输出结果

**Copy**

    ```
    from synapse.ml.featurize.text import PageSplitter
    
    ps = (
        PageSplitter()
        .setInputCol("output_content")
        .setMaximumPageLength(4000)
        .setMinimumPageLength(3000)
        .setOutputCol("chunks")
    )
    
    splitted_df = ps.transform(analyzed_df)
    display(splitted_df)
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image55.png)

注意每个文档的区块以数组内的单行形式呈现。为了将所有区块嵌入后续单元格，我们需要将每个区块放在独立的行中。

2.  使用单元格输出下方的  **+
    Code** 图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击
    **▷ Run cell** 按钮，查看输出结果

**Copy**

    ```
    from pyspark.sql.functions import posexplode, col, concat
    
    # Each "chunks" column contains the chunks for a single document in an array
    # The posexplode function will separate each chunk into its own row
    exploded_df = splitted_df.select("file_name", posexplode(col("chunks")).alias("chunk_index", "chunk"))
    
    # Add a unique identifier for each chunk
    exploded_df = exploded_df.withColumn("unique_id", concat(exploded_df.file_name, exploded_df.chunk_index))
    
    display(exploded_df)
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image56.png)

从这段代码片段中，我们首先将这些数组炸开，使每行只有一个块，然后过滤
Spark DataFrame，使文档路径和块只保留在同一行。

## 任务2：生成嵌入

接下来我们会为每个块生成嵌入。为此，我们同时使用SynapseML和Azure
OpenAI服务。通过将内置的Azure
OpenAI服务与SynapseML集成，我们可以利用Apache
Spark分布式计算框架的力量，利用OpenAI服务处理大量提示。

1.  使用单元格输出下方的 **+
    Code** 图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击
    **▷ Run cell** 按钮，查看输出结果

**Copy**

    ```
    from synapse.ml.services import OpenAIEmbedding
    
    embedding = (
        OpenAIEmbedding()
        .setDeploymentName("text-embedding-ada-002")
        .setTextCol("chunk")
        .setErrorCol("error")
        .setOutputCol("embeddings")
    )
    
    df_embeddings = embedding.transform(exploded_df)
    
    display(df_embeddings)
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image57.png)

这种集成使SynapseML嵌入客户端能够以分布式方式生成嵌入，从而高效处理大量数据

## 任务3：存储嵌入 

[Azure AI
Search](https://learn.microsoft.com/azure/search/search-what-is-azure-search?WT.mc_id=data-114676-jndemenge)
是一款强大的搜索引擎，具备全文搜索、矢量搜索和混合搜索功能。欲了解更多向量搜索功能的示例，请参见
[azure-search-vector-samples
repository](https://github.com/Azure/azure-search-vector-samples/)。 

在Azure AI Search中存储数据主要包括两个步骤:

**创建索引：**第一步是定义搜索索引的模式，包括每个字段的属性以及将使用的任何向量搜索策略。

**添加分块文档和嵌入：**第二步是将分块文档及其对应嵌入上传到索引中。这使得数据的高效存储和检索成为利用混合和向量搜索的实现。

1.  以下代码片段演示如何使用Azure AI Search REST API在Azure AI
    Search中创建索引。该代码创建索引，包含每个文档的唯一标识符、文档文本内容以及文本内容的向量嵌入字段。

2.  使用单元格输出下方的 **+
    Code**图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击**▷
    Run cell** 按钮，查看输出结果

**Copy**

    ```
    import requests
    import json
    
    # Length of the embedding vector (OpenAI ada-002 generates embeddings of length 1536)
    EMBEDDING_LENGTH = 1536
    
    # Define your AI Search index name and API key
    AI_SEARCH_INDEX_NAME = "rag-demo-index"
    AI_SEARCH_API_KEY = "your_api_key"
    
    # Create index for AI Search with fields id, content, and contentVector
    url = f"https://mysearchservice@lab.LabInstance.Id.search.windows.net/indexes/{AI_SEARCH_INDEX_NAME}?api-version=2024-07-01"
    payload = json.dumps(
        {
            "name": AI_SEARCH_INDEX_NAME,
            "fields": [
                {
                    "name": "id",
                    "type": "Edm.String",
                    "key": True,
                    "filterable": True,
                },
                {
                    "name": "content",
                    "type": "Edm.String",
                    "searchable": True,
                    "retrievable": True,
                },
                {
                    "name": "contentVector",
                    "type": "Collection(Edm.Single)",
                    "searchable": True,
                    "retrievable": True,
                    "dimensions": EMBEDDING_LENGTH,
                    "vectorSearchProfile": "vectorConfig",
                },
            ],
            "vectorSearch": {
                "algorithms": [{"name": "hnswConfig", "kind": "hnsw", "hnswParameters": {"metric": "cosine"}}],
                "profiles": [{"name": "vectorConfig", "algorithm": "hnswConfig"}],
            },
        }
    )
    headers = {"Content-Type": "application/json", "api-key": AI_SEARCH_API_KEY}
    
    response = requests.put(url, headers=headers, data=payload)
    if response.status_code == 201:
        print("Index created!")
    elif response.status_code == 204:
        print("Index updated!")
    else:
        print(f"HTTP request failed with status code {response.status_code}")
        print(f"HTTP response body: {response.text}")
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image58.png)

![](./media/image59.png)

3.  下一步是将区块上传到新创建的 Azure AI 搜索索引中。Azure AI Search
    REST API 支持每个请求最多 1000
    个“文档”。请注意，在这种情况下，我们的每一份“文档”实际上都是原始文件的一部分

4.  使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击 **▷ Run
    cell **按钮，查看输出结果

**Copy**

    ```
    import re
    
    from pyspark.sql.functions import monotonically_increasing_id
    
    
    def insert_into_index(documents):
        """Uploads a list of 'documents' to Azure AI Search index."""
    
        url = f"https://{AI_SEARCH_NAME}.search.windows.net/indexes/{AI_SEARCH_INDEX_NAME}/docs/index?api-version=2023-11-01"
    
        payload = json.dumps({"value": documents})
        headers = {
            "Content-Type": "application/json",
            "api-key": AI_SEARCH_API_KEY,
        }
    
        response = requests.request("POST", url, headers=headers, data=payload)
    
        if response.status_code == 200 or response.status_code == 201:
            return "Success"
        else:
            return f"Failure: {response.text}"
    
    def make_safe_id(row_id: str):
        """Strips disallowed characters from row id for use as Azure AI search document ID."""
        return re.sub("[^0-9a-zA-Z_-]", "_", row_id)
    
    
    def upload_rows(rows):
        """Uploads the rows in a Spark dataframe to Azure AI Search.
        Limits uploads to 1000 rows at a time due to Azure AI Search API limits.
        """
        BATCH_SIZE = 1000
        rows = list(rows)
        for i in range(0, len(rows), BATCH_SIZE):
            row_batch = rows[i : i + BATCH_SIZE]
            documents = []
            for row in rows:
                documents.append(
                    {
                        "id": make_safe_id(row["unique_id"]),
                        "content": row["chunk"],
                        "contentVector": row["embeddings"].tolist(),
                        "@search.action": "upload",
                    },
                )
            status = insert_into_index(documents)
            yield [row_batch[0]["row_index"], row_batch[-1]["row_index"], status]
    
    # Add ID to help track what rows were successfully uploaded
    df_embeddings = df_embeddings.withColumn("row_index", monotonically_increasing_id())
    
    # Run upload_batch on partitions of the dataframe
    res = df_embeddings.rdd.mapPartitions(upload_rows)
    display(res.toDF(["start_index", "end_index", "insertion_status"]))
    ```
![](./media/image60.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image61.png)

# 练习4：检索相关文件并回答问题

处理完文件后，我们可以提出一个问题。我们将使用 SynapseML
将用户问题转换为嵌入，然后利用余弦相似性检索与用户问题高度匹配的顶部 K
个文档块。

## 任务1：配置环境及Azure API密钥

在湖屋里创建一个新笔记本，并保存为rag_application。我们将用这个笔记本来构建RAG应用程序。

1.  提供访问Azure AI
    Search的凭据。你可以从Azure门户复制这些数值。（演习1\>任务4）

2.  使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击 **▷ Run
    cell** 按钮，查看输出结果

Copy

    ```
    # Azure AI Search
    AI_SEARCH_NAME = 'mysearchservice@lab.LabInstance.Id'
    AI_SEARCH_INDEX_NAME = 'rag-demo-index'
    AI_SEARCH_API_KEY = ''
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image62.png)

3.  以下函数将用户的问题作为输入，并利用文本嵌入-ada-002模型将其转换为嵌入。本代码假设你使用的是Microsoft
    Fabric中的预构建AI服务

4.  使用单元格输出下方的 **+
    Code **图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击**▷
    Run cell** 按钮，查看输出结果

**Copy**

    ```
    def gen_question_embedding(user_question):
        """Generates embedding for user_question using SynapseML."""
        from synapse.ml.services import OpenAIEmbedding
    
        df_ques = spark.createDataFrame([(user_question, 1)], ["questions", "dummy"])
        embedding = (
            OpenAIEmbedding()
            .setDeploymentName('text-embedding-ada-002')
            .setTextCol("questions")
            .setErrorCol("errorQ")
            .setOutputCol("embeddings")
        )
        df_ques_embeddings = embedding.transform(df_ques)
        row = df_ques_embeddings.collect()[0]
        question_embedding = row.embeddings.tolist()
        return question_embedding
    ```
![A screenshot of a computer AI-generated content may be
incorrect.](./media/image63.png)

## 任务2：检索相关文件

1.  下一步是利用用户问题及其嵌入，从搜索索引中检索最相关的前K个文档块。以下函数通过混合搜索检索顶部的K个条目

2.  使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击 **▷ Run
    cell** 按钮，查看输出结果

**Copy**

    ```
    import json 
    import requests
    
    def retrieve_top_chunks(k, question, question_embedding):
        """Retrieve the top K entries from Azure AI Search using hybrid search."""
        url = f"https://{AI_SEARCH_NAME}.search.windows.net/indexes/{AI_SEARCH_INDEX_NAME}/docs/search?api-version=2023-11-01"
    
        payload = json.dumps({
            "search": question,
            "top": k,
            "vectorQueries": [
                {
                    "vector": question_embedding,
                    "k": k,
                    "fields": "contentVector",
                    "kind": "vector"
                }
            ]
        })
    
        headers = {
            "Content-Type": "application/json",
            "api-key": AI_SEARCH_API_KEY,
        }
    
        response = requests.request("POST", url, headers=headers, data=payload)
        output = json.loads(response.text)
        return output
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image64.png)

定义了这些函数后，我们可以定义一个函数，接收用户的问题，生成问题嵌入，检索顶部K个文档块，并将检索到的文档内容串接起来，形成用户问题的上下文。

3.  使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击**▷ Run
    cell** 按钮，查看输出结果

**Copy**

    ```
    def get_context(user_question, retrieved_k = 5):
        # Generate embeddings for the question
        question_embedding = gen_question_embedding(user_question)
    
        # Retrieve the top K entries
        output = retrieve_top_chunks(retrieved_k, user_question, question_embedding)
    
        # concatenate the content of the retrieved documents
        context = [chunk["content"] for chunk in output["value"]]
    
        return context
    ```
![A screenshot of a computer AI-generated content may be
incorrect.](./media/image65.png)

## **任务3：回答用户的问题**

最后，我们可以定义一个函数，将用户的问题获取上下文，并将上下文和问题发送到大型语言模型以生成回答。本次演示将使用GPT-35-turbo-16k，一款专为对话优化的型号。

1.  使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击**▷ Run
    cell** 按钮，查看输出结果

**Copy**

    ```
    from pyspark.sql import Row
    from synapse.ml.services.openai import OpenAIChatCompletion
    
    
    def make_message(role, content):
        return Row(role=role, content=content, name=role)
    
    def get_response(user_question):
        context = get_context(user_question)
    
        # Write a prompt with context and user_question as variables 
        prompt = f"""
        context: {context}
        Answer the question based on the context above.
        If the information to answer the question is not present in the given context then reply "I don't know".
        """
    
        chat_df = spark.createDataFrame(
            [
                (
                    [
                        make_message(
                            "system", prompt
                        ),
                        make_message("user", user_question),
                    ],
                ),
            ]
        ).toDF("messages")
    
        chat_completion = (
            OpenAIChatCompletion()
            .setDeploymentName("gpt-35-turbo-16k") # deploymentName could be one of {gpt-35-turbo, gpt-35-turbo-16k}
            .setMessagesCol("messages")
            .setErrorCol("error")
            .setOutputCol("chat_completions")
        )
    
        result_df = chat_completion.transform(chat_df).select("chat_completions.choices.message.content")
    
        result = []
        for row in result_df.collect():
            content_string = ' '.join(row['content'])
            result.append(content_string)
    
        # Join the list into a single string
        result = ' '.join(result)
        
        return result
    ```
![](./media/image66.png)

![](./media/image67.png)

2.  现在，我们可以用示例问题调用该函数，查看响应:

3.  使用单元格输出下方的 **+ Code**
    图标，向笔记本添加一个新的代码单元格，并输入以下代码。点击**▷ Run
    cell** 按钮，查看输出结果

**Copy**

```
import requests

# Azure Search configuration
search_service_name = ''
index_name = 'rag-demo-index'
api_key = ''
endpoint = f'https://{search_service_name}.search.windows.net'
api_version = '2023-07-01-Preview'
search_url = f"{endpoint}/indexes/{index_name}/docs/search?api-version={api_version}"

headers = {
    "Content-Type": "application/json",
    "api-key": api_key
}

def get_response(user_question, top_k=1):
    payload = {
        "search": user_question,
        "queryType": "simple",   # Can be "semantic" if enabled in your Azure Search
        "top": top_k
    }
    response = requests.post(search_url, headers=headers, json=payload)
    response.raise_for_status()
    results = response.json().get('value', [])
    if not results:
        return "No answer found in the knowledge base."
    return results[0].get('content', '').strip()

# Example usage
user_question = "how do i make a booking?"
response = get_response(user_question)
print(response)
```

![](./media/image68.png)

## 任务4：删除资源

为了避免不必要的 Azure
成本，如果不再需要，你应该删除你在快速入门中创建的资源。管理资源时，可以使用
[Azure门户](https://portal.azure.com/?azure-portal=true)。

1.  要删除存储账户，**Azure portal 主**页，点击**Resource groups**。

> ![A screenshot of a computer Description automatically
> generated](./media/image69.png)

2.  点击指定的资源组。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image70.png)

3.  在 **Resource group** 主页中，选择资源 Azure AI 服务、Key Valut 和
    Search 服务。![](./media/image71.png)

4.  选择 **Delete**

![](./media/image72.png)

![A screenshot of a computer error AI-generated content may be
incorrect.](./media/image73.png)

5.  在右侧出现的 **Delete Resources** 面板中，点击 **Enter +++delete+++
    to confirm deletion**字段，然后点击 **Delete** 按钮。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image74.png)

6.  在 **Delete confirmation** 对话框中，点击 **Delete** 按钮。

> ![A screenshot of a computer error Description automatically
> generated](./media/image75.png)

7.  打开浏览器，进入地址栏，输入或粘贴以下URL：+++https://app.fabric.microsoft.com/+++，
    然后按下 **Enter** 键。

> ![](./media/image76.png)

8.  选择...... 在工作区名称下选择选项，选择**Workspace settings**。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image77.png)

9.  选择“**General**”，点击“**Remove this workspace**”。 

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image78.png)

10. 点击弹出的警告中“**Delete**”。

![A white background with black text Description automatically
generated](./media/image79.png)

11. 等待工作区被删除的通知后，再进入下一个实验室。

![A screenshot of a computer Description automatically
generated](./media/image80.png)
