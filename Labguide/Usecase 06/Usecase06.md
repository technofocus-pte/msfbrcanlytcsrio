**介紹 **

分析結構化數據已經是個容易的過程，但非結構化數據則不然。非結構化數據，如文本、圖片和視頻，更難分析和解讀。然而，隨著先進AI模型的出現，如OpenAI的GPT-3和GPT-4，分析和獲取非結構化數據的洞察變得更容易。

此類分析的一個例子是利用自然語言查詢文檔的特定信息，這可以通過信息檢索和語言生成相結合實現。

通過利用RAG（檢索增強生成）框架，你可以創建一個強大的問答流程，利用大型語言模型（LLM）和你自己的數據生成回答。

此類應用的架構如下所示:

![Architecture diagram connecting Azure OpenAI with Azure AI Search and
Document Intelligence](./media/image1.png)

**目標**

- 使用 Azure 門戶為 Azure AI 服務創建多服務資源

- 創建 Fabric 容量和工作區，Key Vault 和 Fabric 工作區

- 在Azure AI服務中使用Azure AI文檔智能預處理PDF文檔。

- 使用SynapseML進行文本分塊處理。

- 使用 SynapseML 和 Azure OpenAI Services 生成區塊嵌入。

- 將嵌入存儲在 Azure AI 搜索中。

- 建立一個問答流程。

# **練習1：環境設置**

## 任務1：為Azure AI服務創建多服務資源

多服務資源列在門戶的“**Azure AI services \> Azure AI services
multi-service account**”下。要創建多服務資源，請按照以下說明操作:

1.  選擇此鏈接創建多服務資源: 

++++https://portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne+++

[TABLE]

2.  在**Create**頁面，提供以下信息:

3.  根據需要配置資源的其他設置，閱讀並接受條件（如適用），然後選擇
    **Review + create**。

![](./media/image2.png)

4.  在**“Review+submit**”標簽中，驗證通過後，點擊 **Create** 按鈕。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image3.png)

5.  部署完成後，點擊“ **Go to resource** ”按鈕。

> ![A screenshot of a computer Description automatically
> generated](./media/image4.png)

6.  在 **Azure** **AI service** 窗口中，導航到“**Resource
    Management**”部分，然後單擊“**Keys and Endpoints**”。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image5.png)

7.  在“**Keys and Endpoints**”頁面，複製**KEY1、KEY 2**和 **Endpoint**
    的值，並像下圖所示粘貼到記事本中，然後**保存**記事本以便在接下來的任務中使用這些信息。

![](./media/image6.png)

## **任務2： 使用Azure門戶創建密鑰庫**

1.  在 Azure 門戶主頁，點擊 **+ Create Resource**。

> ![A screenshot of a computer Description automatically
> generated](./media/image7.png)

2.  在“**Create a resource**”頁面搜索欄中，輸入“**Key
    vault**”，點擊已出現的**Key vault**。![](./media/image8.png)

3.  點擊 **Key vault** 部分。

> ![](./media/image9.png)

4.  在**“Create a key Vault** ”頁面，提供以下信息，點擊
    **“Review+create**”按鈕。

[TABLE]

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image10.png)

5.  驗證通過後，點擊 **Create** 按鈕。

> ![](./media/image11.png)

6.  部署完成後，點擊“ **Go to resource** ”按鈕。

> ![](./media/image12.png)

5.  在你的 **fabrickeyvaultXX** 窗口中，從左側菜單點擊 **Access
    control(IAM)。**

![](./media/image13.png)

6.  在 Access control(IAM) 頁面，點擊+**Add**並選擇**Add role
    assignments**。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image14.png)

5.  在 **Job function roles** 中，在搜索框中輸入 +++**Key vault
    administrator+++**並選擇它。點擊 **Next**

> ![](./media/image15.png)

6.  在“**Add role assignment**”標簽中，選擇“Assign access to User group
    or service principal”。在“Members”下，點擊 **+Select members**

> ![](./media/image16.png)

7.  在“Select members”標簽頁中，搜索你的Azure
    OpenAI訂閱並點擊“**Select**”。

![](./media/image17.png)

8.  在“**Add role assignment**”頁面，點擊 **Review +
    Assign**，角色分配完成後你會收到通知。

> ![A screenshot of a computer Description automatically
> generated](./media/image18.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image19.png)

9.  您將看到一個通知——已添加為Azure AI開發者 for Azure-openai-testXX

![A screenshot of a computer Description automatically
generated](./media/image20.png)

## 任務3：使用 Azure Key Vault 創建秘密

1.  在鑰匙庫左側欄，選擇**“Objects**”，然後選擇**“Secrets**”。

> ![](./media/image21.png)

2.  選擇 **+ Generate/Import**。

> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

3.  在**Create a secret** 頁面，輸入以下信息並點擊 **Create** 按鈕。.

[TABLE]

> ![](./media/image23.png)

4.  選擇 **+ Generate/Import**。

> ![](./media/image24.png)

5.  在 Create a secret 頁面，輸入以下信息並點擊 **Create** 按鈕。

[TABLE]

![](./media/image25.png)

![](./media/image26.png)

6.  在**Key vault**頁面，複製 **Key vault**
    名稱和**秘密**值，並像下圖所示粘貼到記事本，然後**保存**記事本以便在接下來的任務中使用這些信息。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image27.png)

## **任務4：在門戶中創建Azure AI搜索服務**

1.  在 Azure 門戶主頁，點擊 **+ Create Resource**。

> ![A screenshot of a computer Description automatically
> generated](./media/image7.png)

2.  在“**Create a resource**”搜索欄中，輸入“**Azure cognitive
    Search**”，點擊已出現的 **azure AI search**。

![](./media/image28.png)

3.  點擊 **azure ai search**部分。

![A screenshot of a computer Description automatically
generated](./media/image29.png)

4.  在 **Azure AI Search**頁面，點擊“**Create**”按鈕。

> ![A screenshot of a computer Description automatically
> generated](./media/image30.png)

5.  在“**Create a search
    service**”頁面，輸入以下信息，點擊“**Review+create**”按鈕。

[TABLE]

![](./media/image31.png)

![A screenshot of a computer Description automatically
generated](./media/image32.png)

6.  驗證通過後，點擊 **Create** 按鈕。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image33.png)

8.  部署完成後，點擊“ **Go to resource** ”按鈕。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image34.png)

9.  複製 **AI search name**
    並粘貼到筆記本中，如下圖所示，然後**保存**筆記本以便在即將到來的實驗中使用這些信息。

![](./media/image35.png)

## **任務5：創建Fabric工作區**

在這個任務中，你需要創建一個Fabric工作區。工作區包含了本 lakehouse
教程所需的所有內容，包括 lakehouse、數據流、Data Factory
管道、筆記本、Power BI 數據集和報表。

1.  打開瀏覽器，進入地址欄，輸入或粘貼以下URL：
    https://app.fabric.microsoft.com/ 然後按下 **Enter** 鍵。

> ![A search engine window with a red box Description automatically
> generated with medium confidence](./media/image36.png)

2.  在 **Microsoft Fabric** 窗口中，輸入你的憑證，然後點擊 **Submit**
    按鈕。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image37.png)

3.  然後，在 **Microsoft** 窗口輸入密碼，點擊 **Sign in** 按鈕**。**

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image38.png)

4.  在 **Stay signed in?** 窗口，點擊**“Yes”**按鈕。

> ![A screenshot of a computer error AI-generated content may be
> incorrect.](./media/image39.png)

5.  在工作區面板中選擇 **+New workspace**。

> ![](./media/image40.png)

6.  在右側的 **Create a workspace**
    面板中，輸入以下細節，然後點擊“**Apply**”按鈕。

[TABLE]

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image41.png)
>
> ![](./media/image42.png)

10. 等待部署完成。完成大約需要2-3分鐘。

![](./media/image43.png)

## **任務6：建造 lakehouse**

1.  在 **Fabric主**頁，選擇 **+New item**，選擇 **Lakehouse**  瓷磚。

> ![A screenshot of a computer Description automatically
> generated](./media/image44.png)

2.  在“**New
    lakehouse** ”對話框中，在“**Name**”字段中輸入+++**data_lakehouse**+++，單擊“**Create**”按鈕，打開新的
    lakehouse。

> **注意**：**data_lakehouse** 前請務必清空。
>
> ![A screenshot of a computer Description automatically
> generated](./media/image45.png)

3.  你會看到一條通知，提示 **Successfully created SQL endpoint**。

> ![A screenshot of a computer Description automatically
> generated](./media/image46.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image47.png)

# **練習 2：PDF文檔加載與預處理 **

## **任務1：配置Azure API密鑰**

首先，回到工作區中的rag_workshop Lakehouse，通過選擇“Open
Notebook”和選項中的“New Notebook”創建新筆記本。

1.  在 **Lakehouse** 頁面中，導航並單擊命令欄中的“**Open
    notebook**”下拉列表，然後選擇“**New notebook**”。

![A screenshot of a computer Description automatically
generated](./media/image48.png)

2.  在查詢編輯器中，粘貼以下代碼。 提供Azure AI服務的密鑰、Azure Key
    Vault名稱和訪問服務的密鑰

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image49.png)

\# Azure AI Search

AI_SEARCH_NAME = ""

AI_SEARCH_INDEX_NAME = "rag-demo-index"

AI_SEARCH_API_KEY = ""

\# Azure AI Services

AI_SERVICES_KEY = ""

AI_SERVICES_LOCATION = ""

> ![](./media/image50.png)

## 任務2：加載和分析文檔

1.  我們將使用一個名為 **support.pdf** 的特定文檔 ，作為我們數據的來源。

2.  要下載文檔，請使用單元格輸出下方的 **+ Code**
    圖標，向筆記本添加一個新的代碼單元格，並在其中輸入以下代碼。點擊 **▷
    Run cell**  按鈕，查看輸出結果

**Copy**

import requests

import os

url =
"https://github.com/Azure-Samples/azure-openai-rag-workshop/raw/main/data/support.pdf"

response = requests.get(url)

\# Specify your path here

path = "/lakehouse/default/Files/"

\# Ensure the directory exists

os.makedirs(path, exist_ok=True)

\# Write the content to a file in the specified path

filename = url.rsplit("/")\[-1\]

with open(os.path.join(path, filename), "wb") as f:

f.write(response.content)

![](./media/image51.png)

3.  現在，使用 Apache Spark 提供的
    spark.read.format（“binaryFile”）方法將 PDF 文檔加載到 Spark
    DataFrame 中

4.  使用單元格輸出下方的 **+ Code** 
    圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊 **▷ Run
    cell** 按鈕，查看輸出結果

**Copy**

from pyspark.sql.functions import udf

from pyspark.sql.types import StringType

document_path = f"Files/{filename}"

df =
spark.read.format("binaryFile").load(document_path).select("\_metadata.file_name",
"content").limit(10).cache()

display(df)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image52.png)

該代碼將讀取 PDF 文檔，並生成名為 df 的 Spark DataFrame，包含 PDF
內容。DataFrame 將包含一個表示 PDF 文檔結構的模式，包括其文本內容。

5.  接下來，我們將使用Azure AI文檔智能讀取PDF文檔並提取文本。

6.  使用單元格輸出下方的 **+ Code**
    圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊 **▷ Run
    cell **按鈕，查看輸出結果

**Copy**

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

![A screenshot of a computer code AI-generated content may be
incorrect.](./media/image53.png)

7.  我們可以通過以下代碼觀察分析的名為analyzed_df的火花數據幀。注意我們去掉內容欄，因為它不再需要了。

8.  使用單元格輸出下方的 **+
    Code** 圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊
    **▷ Run cell ** 按鈕，查看輸出結果

**Copy**

analyzed_df = analyzed_df.drop("content")

display(analyzed_df)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image54.png)

# 練習 3：生成和存儲嵌入

## **任務1：文本分塊處理**

在生成嵌入之前，我們需要將文本拆分成多個塊。為此，我們利用SynapseML的PageSplitter將文檔劃分為更小的部分，這些部分隨後存儲在區塊列中。這使得文檔內容能夠實現更細緻的表示和處理。

1.  使用單元格輸出下方的 **+ Code**
    圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊 **▷ Run
    cell** 按鈕，查看輸出結果

**Copy**

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

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image55.png)

注意每個文檔的區塊以數組內的單行形式呈現。為了將所有區塊嵌入後續單元格，我們需要將每個區塊放在獨立的行中。

2.  使用單元格輸出下方的  **+
    Code** 圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊
    **▷ Run cell** 按鈕，查看輸出結果

**Copy**

from pyspark.sql.functions import posexplode, col, concat

\# Each "chunks" column contains the chunks for a single document in an
array

\# The posexplode function will separate each chunk into its own row

exploded_df = splitted_df.select("file_name",
posexplode(col("chunks")).alias("chunk_index", "chunk"))

\# Add a unique identifier for each chunk

exploded_df = exploded_df.withColumn("unique_id",
concat(exploded_df.file_name, exploded_df.chunk_index))

display(exploded_df)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image56.png)

從這段代碼片段中，我們首先將這些數組炸開，使每行只有一個塊，然後過濾
Spark DataFrame，使文檔路徑和塊只保留在同一行。

## 任務2：生成嵌入

接下來我們會為每個塊生成嵌入。為此，我們同時使用SynapseML和Azure
OpenAI服務。通過將內置的Azure
OpenAI服務與SynapseML集成，我們可以利用Apache
Spark分布式計算框架的力量，利用OpenAI服務處理大量提示。

1.  使用單元格輸出下方的 **+
    Code** 圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊
    **▷ Run cell** 按鈕，查看輸出結果

**Copy**

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

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image57.png)

這種集成使SynapseML嵌入客戶端能夠以分布式方式生成嵌入，從而高效處理大量數據

## 任務3：存儲嵌入 

[Azure AI
Search](https://learn.microsoft.com/azure/search/search-what-is-azure-search?WT.mc_id=data-114676-jndemenge)
是一款強大的搜索引擎，具備全文搜索、矢量搜索和混合搜索功能。欲瞭解更多向量搜索功能的示例，請參見
[azure-search-vector-samples
repository](https://github.com/Azure/azure-search-vector-samples/)。 

在Azure AI Search中存儲數據主要包括兩個步驟:

**創建索引：**第一步是定義搜索索引的模式，包括每個字段的屬性以及將使用的任何向量搜索策略。

**添加分塊文檔和嵌入：**第二步是將分塊文檔及其對應嵌入上傳到索引中。這使得數據的高效存儲和檢索成為利用混合和向量搜索的實現。

1.  以下代碼片段演示如何使用Azure AI Search REST API在Azure AI
    Search中創建索引。該代碼創建索引，包含每個文檔的唯一標識符、文檔文本內容以及文本內容的向量嵌入字段。

2.  使用單元格輸出下方的 **+
    Code**圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊**▷
    Run cell** 按鈕，查看輸出結果

**Copy**

import requests

import json

\# Length of the embedding vector (OpenAI ada-002 generates embeddings
of length 1536)

EMBEDDING_LENGTH = 1536

\# Define your AI Search index name and API key

AI_SEARCH_INDEX_NAME = " rag-demo-index"

AI_SEARCH_API_KEY = "your_api_key"

\# Create index for AI Search with fields id, content, and contentVector

url =
f"https://mysearchservice356.search.windows.net/indexes/{AI_SEARCH_INDEX_NAME}?api-version=2024-07-01"

payload = json.dumps(

{

"name": AI_SEARCH_INDEX_NAME,

"fields": \[

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

\],

"vectorSearch": {

"algorithms": \[{"name": "hnswConfig", "kind": "hnsw", "hnswParameters":
{"metric": "cosine"}}\],

"profiles": \[{"name": "vectorConfig", "algorithm": "hnswConfig"}\],

},

}

)

headers = {"Content-Type": "application/json", "api-key":
AI_SEARCH_API_KEY}

response = requests.put(url, headers=headers, data=payload)

if response.status_code == 201:

print("Index created!")

elif response.status_code == 204:

print("Index updated!")

else:

print(f"HTTP request failed with status code {response.status_code}")

print(f"HTTP response body: {response.text}")

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image58.png)

![](./media/image59.png)

3.  下一步是將區塊上傳到新創建的 Azure AI 搜索索引中。Azure AI Search
    REST API 支持每個請求最多 1000
    個“文檔”。請注意，在這種情況下，我們的每一份“文檔”實際上都是原始文件的一部分

4.  使用單元格輸出下方的 **+ Code**
    圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊 **▷ Run
    cell **按鈕，查看輸出結果

**Copy**

import re

from pyspark.sql.functions import monotonically_increasing_id

def insert_into_index(documents):

"""Uploads a list of 'documents' to Azure AI Search index."""

url =
f"https://{AI_SEARCH_NAME}.search.windows.net/indexes/{AI_SEARCH_INDEX_NAME}/docs/index?api-version=2023-11-01"

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

"""Strips disallowed characters from row id for use as Azure AI search
document ID."""

return re.sub("\[^0-9a-zA-Z\_-\]", "\_", row_id)

def upload_rows(rows):

"""Uploads the rows in a Spark dataframe to Azure AI Search.

Limits uploads to 1000 rows at a time due to Azure AI Search API limits.

"""

BATCH_SIZE = 1000

rows = list(rows)

for i in range(0, len(rows), BATCH_SIZE):

row_batch = rows\[i : i + BATCH_SIZE\]

documents = \[\]

for row in rows:

documents.append(

{

"id": make_safe_id(row\["unique_id"\]),

"content": row\["chunk"\],

"contentVector": row\["embeddings"\].tolist(),

"@search.action": "upload",

},

)

status = insert_into_index(documents)

yield \[row_batch\[0\]\["row_index"\], row_batch\[-1\]\["row_index"\],
status\]

\# Add ID to help track what rows were successfully uploaded

df_embeddings = df_embeddings.withColumn("row_index",
monotonically_increasing_id())

\# Run upload_batch on partitions of the dataframe

res = df_embeddings.rdd.mapPartitions(upload_rows)

display(res.toDF(\["start_index", "end_index", "insertion_status"\]))

![](./media/image60.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image61.png)

# 練習4：檢索相關文件並回答問題

處理完文件後，我們可以提出一個問題。我們將使用
[SynapseML](https://microsoft.github.io/SynapseML/docs/Explore%20Algorithms/OpenAI/Quickstart%20-%20OpenAI%20Embedding/)
將用戶問題轉換為嵌入，然後利用余弦相似性檢索與用戶問題高度匹配的頂部 K
個文檔塊。

## 任務1：配置環境及Azure API密鑰

在湖屋裡創建一個新筆記本，並保存為rag_application。我們將用這個筆記本來構建RAG應用程序。

1.  提供訪問Azure AI
    Search的憑據。你可以從Azure門戶複製這些數值。（演習1\>任務4）

2.  使用單元格輸出下方的 **+ Code**
    圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊 **▷ Run
    cell** 按鈕，查看輸出結果

Copy

\# Azure AI Search

AI_SEARCH_NAME = ''

AI_SEARCH_INDEX_NAME = 'rag-demo-index'

AI_SEARCH_API_KEY = ''

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image62.png)

3.  以下函數將用戶的問題作為輸入，並利用文本嵌入-ada-002模型將其轉換為嵌入。本代碼假設你使用的是Microsoft
    Fabric中的預構建AI服務

4.  使用單元格輸出下方的 **+
    Code **圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊**▷
    Run cell** 按鈕，查看輸出結果

**Copy**

def gen_question_embedding(user_question):

"""Generates embedding for user_question using SynapseML."""

from synapse.ml.services import OpenAIEmbedding

df_ques = spark.createDataFrame(\[(user_question, 1)\], \["questions",
"dummy"\])

embedding = (

OpenAIEmbedding()

.setDeploymentName('text-embedding-ada-002')

.setTextCol("questions")

.setErrorCol("errorQ")

.setOutputCol("embeddings")

)

df_ques_embeddings = embedding.transform(df_ques)

row = df_ques_embeddings.collect()\[0\]

question_embedding = row.embeddings.tolist()

return question_embedding

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image63.png)

## 任務2：檢索相關文件

1.  下一步是利用用戶問題及其嵌入，從搜索索引中檢索最相關的前K個文檔塊。以下函數通過混合搜索檢索頂部的K個條目

2.  使用單元格輸出下方的 **+ Code**
    圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊 **▷ Run
    cell** 按鈕，查看輸出結果

**Copy**

import json

import requests

def retrieve_top_chunks(k, question, question_embedding):

"""Retrieve the top K entries from Azure AI Search using hybrid
search."""

url =
f"https://{AI_SEARCH_NAME}.search.windows.net/indexes/{AI_SEARCH_INDEX_NAME}/docs/search?api-version=2023-11-01"

payload = json.dumps({

"search": question,

"top": k,

"vectorQueries": \[

{

"vector": question_embedding,

"k": k,

"fields": "contentVector",

"kind": "vector"

}

\]

})

headers = {

"Content-Type": "application/json",

"api-key": AI_SEARCH_API_KEY,

}

response = requests.request("POST", url, headers=headers, data=payload)

output = json.loads(response.text)

return output

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image64.png)

定義了這些函數後，我們可以定義一個函數，接收用戶的問題，生成問題嵌入，檢索頂部K個文檔塊，並將檢索到的文檔內容串接起來，形成用戶問題的上下文。

3.  使用單元格輸出下方的 **+ Code**
    圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊**▷ Run
    cell** 按鈕，查看輸出結果

**Copy**

def get_context(user_question, retrieved_k = 5):

\# Generate embeddings for the question

question_embedding = gen_question_embedding(user_question)

\# Retrieve the top K entries

output = retrieve_top_chunks(retrieved_k, user_question,
question_embedding)

\# concatenate the content of the retrieved documents

context = \[chunk\["content"\] for chunk in output\["value"\]\]

return context

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image65.png)

## **任務3：回答用戶的問題**

最後，我們可以定義一個函數，將用戶的問題獲取上下文，並將上下文和問題發送到大型語言模型以生成回答。本次演示將使用GPT-35-turbo-16k，一款專為對話優化的型號。

1.  使用單元格輸出下方的 **+ Code**
    圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊**▷ Run
    cell** 按鈕，查看輸出結果

**Copy**

from pyspark.sql import Row

from synapse.ml.services.openai import OpenAIChatCompletion

def make_message(role, content):

return Row(role=role, content=content, name=role)

def get_response(user_question):

context = get_context(user_question)

\# Write a prompt with context and user_question as variables

prompt = f"""

context: {context}

Answer the question based on the context above.

If the information to answer the question is not present in the given
context then reply "I don't know".

"""

chat_df = spark.createDataFrame(

\[

(

\[

make_message(

"system", prompt

),

make_message("user", user_question),

\],

),

\]

).toDF("messages")

chat_completion = (

OpenAIChatCompletion()

.setDeploymentName("gpt-35-turbo-16k") \# deploymentName could be one of
{gpt-35-turbo, gpt-35-turbo-16k}

.setMessagesCol("messages")

.setErrorCol("error")

.setOutputCol("chat_completions")

)

result_df =
chat_completion.transform(chat_df).select("chat_completions.choices.message.content")

result = \[\]

for row in result_df.collect():

content_string = ' '.join(row\['content'\])

result.append(content_string)

\# Join the list into a single string

result = ' '.join(result)

return result

![](./media/image66.png)

![](./media/image67.png)

2.  現在，我們可以用示例問題調用該函數，查看響應:

3.  使用單元格輸出下方的 **+ Code**
    圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊**▷ Run
    cell** 按鈕，查看輸出結果

**Copy**

user_question = "how do i make a booking?"

response = get_response(user_question)

print(response)

![](./media/image68.png)

## 任務4：刪除資源

為了避免不必要的 Azure
成本，如果不再需要，你應該刪除你在快速入門中創建的資源。管理資源時，可以使用
[Azure門戶](https://portal.azure.com/?azure-portal=true)。

1.  要刪除存儲賬戶，**Azure portal 主**頁，點擊**Resource groups**。

> ![A screenshot of a computer Description automatically
> generated](./media/image69.png)

2.  點擊指定的資源組。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image70.png)

3.  在 **Resource group** 主頁中，選擇資源 Azure AI 服務、Key Valut 和
    Search 服務。![](./media/image71.png)

4.  選擇 **Delete**

![](./media/image72.png)

![A screenshot of a computer error AI-generated content may be
incorrect.](./media/image73.png)

5.  在右側出現的 **Delete Resources** 面板中，點擊 **Enter +++delete+++
    to confirm deletion**字段，然後點擊 **Delete** 按鈕。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image74.png)

6.  在 **Delete confirmation** 對話框中，點擊 **Delete** 按鈕。

> ![A screenshot of a computer error Description automatically
> generated](./media/image75.png)

7.  打開瀏覽器，進入地址欄，輸入或粘貼以下URL：+++https://app.fabric.microsoft.com/+++，
    然後按下 **Enter** 鍵。

> ![](./media/image76.png)

8.  選擇...... 在工作區名稱下選擇選項，選擇**Workspace settings**。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image77.png)

9.  選擇“**General**”，點擊“**Remove this workspace**”。 

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image78.png)

10. 點擊彈出的警告中“**Delete**”。

![A white background with black text Description automatically
generated](./media/image79.png)

11. 等待工作區被刪除的通知後，再進入下一個實驗室。

![A screenshot of a computer Description automatically
generated](./media/image80.png)
