# Use case 06: Identifying and extracting text with Document Intelligence in Microsoft Fabric

**Introduction**

Analyzing structured data has been an easy process for some time but the
same cannot be said for unstructured data. Unstructured data, such as
text, images, and videos, is more difficult to analyze and interpret.
However, with the advent of advanced AI models, it is now becoming easier to analyze and gain insights from
unstructured data.

An example of such analysis is the ability to query a document for
specific information using natural language which is achievable though a
combination of information retrieval and language generation.

By leveraging the RAG (Retrieval-Augmented Generation) framework, you
can create a powerful question-and-answering pipeline that uses a large
language model (LLM) and you own data to generate responses.

The architecture of such an application is as shown below:

> ![Architecture diagram connecting Azure OpenAI with Azure AI Search and
Document Intelligence](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image1.png)

**Objective**

- Create a multi-service resource for Azure AI services using Azure
  portal

- To create fabric capacity and workspace, Key vault, and fabric
  workspace

- Pre-process PDF Documents using Azure AI Document Intelligence in
  Azure AI Services.

- Perform text chunking using SynapseML.

- Generate embeddings for the chunks using SynapseML and Azure OpenAI
  Services.

- Store the embeddings in Azure AI Search.

- Build a question answering pipeline.

## Exercise 1: Environment Setup

### Task 1: Create a multi-service resource for Azure AI services

The multi-service resource is listed under **Azure AI
services** \> **Azure AI services multi-service account** in the portal.
To create a multi-service resource follow these instructions:

1.  Select this link to create a multi-service resource: 

    +++https://portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne+++
	
2.  On the **Create Azure AI services** page, provide the following information:

    |Project details | Description |
    |-----|----|
    |Subscription|	@lab.CloudSubscription.Name |
    |Resource group|	@lab.CloudResourceGroup(ResourceGroup1).Name|
    |Region|	Select the appropriate region for your CognitiveServices. In this lab, we have chosen the **East US 2** region.|
    |Name	|+++Cognitive-service@lab.LabInstance.Id+++ (must be a unique Id)|
    |Pricing tier	|Standard S0|

3.  Read and
    accept the conditions (as applicable), and then select **Review +
    create**.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image2.png)

4.  In the **Review+submit** tab, once the Validation is Passed, click
    on the **Create** button.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image3.png)

5.  After the deployment is completed, click on the **Go to resource**
    button.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image4.png)

6.  In your **Cognitive-service@lab.LabInstance.Id** -> **Azure AI service** window, navigate to the **Resource
    Management** section, and click on **Keys and Endpoints**.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image5.png)

7.  In **Keys and Endpoints** page, copy **KEY1, KEY 2,** and
    **Endpoint** values and paste them in a notepad as shown in the
    below image, then **Save** the notepad to use the information in the
    upcoming tasks.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image6.png)

### Task 2: Create an Azure AI Search service in the portal

1.  In Azure portal home page, click on **+ Create Resource**.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image7.png)

2.  In the **Create a resource** page search bar, type **+++Azure AI
    Search+++** and click on the appeared **azure ai search**.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image28.png)

3.  Click on **azure ai search** section.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image29.png)

4.  In the **Azure AI Search** page, click on the **Create** button.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image30.png)

5.  On the **Create a search service** page, provide the following
    information and click on **Review+create** button.
	
    |Field	|Description|
    |----|----|
    |Resource group| @lab.CloudSubscription.Name |
    |Region	| East US 2|
    |Name	|+++mysearchservice@lab.LabInstance.Id+++ (must can be a unique Id)|
    |Pricing Tier	|Click on change Price Tire > select Basic|
    
    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image31.png)
    
    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image32.png)

6.  Once the Validation is passed, click on the **Create** button.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image33.png)

8.  After the deployment is completed, click on the **Go to resource**
    button.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image34.png)

9.  copy **AI search name** and paste it in notepad as shown in the
    below image, then **Save** the notepad to use the information in the
    upcoming lab.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image35.png)

### Task 5: Create a Fabric workspace

In this task, you create a Fabric workspace. The workspace contains all
the items needed for this lakehouse tutorial, which includes lakehouse,
dataflows, Data Factory pipelines, the notebooks, Power BI datasets, and
reports.

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then press the
    **Enter** button.

    > ![A search engine window with a red box Description automatically
    > generated with medium confidence](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image36.png)

2.  In the **Microsoft Fabric** window, enter your credentials, and
    click on the **Submit** button.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image37.png)

3.  Then, In the **Microsoft** window enter the password and click on
    the **Sign in** button**.**

    > ![A login screen with a red box and blue text AI-generated content may
    > be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image38.png)

4.  In **Stay signed in?** window, click on the **Yes** button.

    > ![A screenshot of a computer error AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image39.png)

5.  In the Workspaces pane Select **+New workspace**.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image40.png)

6.  In the **Create a workspace** pane that appears on the right side,
    enter the following details, and click on the **Apply** button.

    |   |   |
    |----|-----|
    |Name	|+++Document Intelligence-Fabric@lab.LabInstance.Id+++ (must be a unique Id)|
    |Advanced|	Select **Fabric Capacity**|
    |Capacity	|Select the available capacity|

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image41.png)

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image42.png)

10. Wait for the deployment to complete. It takes 2-3 minutes to
    complete.

     ![](./media/img1.png)

### Task 6: Create a lakehouse

1.  In the Fabric Home page, select **+ New item** and filter by, and then select the **+++Lakehouse+++** tile.

     ![](./media/rio2.png)

2.  In the **New lakehouse** dialog box, enter +++**data_lakehouse**+++
    in the **Name** field, click on the **Create** button and open the
    new lakehouse.

    >[!note]**Note**: Ensure to remove space before **data_lakehouse**.

    ![](./media/rio4.png)

3.  You will see a notification stating **Successfully created SQL
    endpoint**.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image46.png)

## Exercise 2: Loading and Pre-processing PDF Documents

### Task 1: Configure Azure API keys

To begin, navigate back to the rag_workshop Lakehouse in your workspace
and create a new notebook by selecting Open Notebook and selecting New
Notebook from the options.

1.  In the **Lakehouse** page, navigate and click on **Open notebook**
    drop in the command bar, then select **New notebook**.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image48.png)

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image49.png)

2.  In the query editor, paste the following code.  Provide the keys for
    Azure AI Services, Azure Key Vault name and secrets to access the
    services.
	
    ```
    # Azure AI Search
    AI_SEARCH_NAME = ""
    AI_SEARCH_INDEX_NAME = "rag-demo-index"
    AI_SEARCH_API_KEY = ""
    
    # Azure AI Services
    AI_SERVICES_KEY = ""
    AI_SERVICES_LOCATION = ""
    ```

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image50.png)

### Task 2: Loading & Analyzing the Document

We will be using a specific document named [**support.pdf**](https://github.com/Azure-Samples/azure-openai-rag-workshop/blob/main/data/support.pdf) which
will be the source of our data.

1.  To download the document, use the **+ Code** icon below the cell
    output to add a new code cell to the notebook, and enter the
    following code in it. Click on **▷ Run cell** button and review the
    output.

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

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image51.png)

3.  Now, load the PDF document into a Spark DataFrame using the
    spark.read.format("binaryFile") method provided by Apache Spark

    Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.
	
    ```
    from pyspark.sql.functions import udf
    from pyspark.sql.types import StringType
    document_path = f"Files/{filename}"
    df = spark.read.format("binaryFile").load(document_path).select("_metadata.file_name", "content").limit(10).cache()
    display(df)
    ```

    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image52.png)

    >[!note]**Note**: This code will read the PDF document and create a Spark DataFrame
    > named df with the contents of the PDF. The DataFrame will have a schema
    > that represents the structure of the PDF document, including its textual
    > content.

5.  Next, we'll use the Azure AI Document Intelligence to read the PDF
    documents and extract the text from them.

    Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.
	
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

    > ![A screenshot of a computer code AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image53.png)

7.  We can observe the analyzed Spark DataFrame named analyzed_df using
    the following code. Note that we drop the content column as it is
    not needed anymore.

    Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.
	
    ```
    analyzed_df = analyzed_df.drop("content")
    display(analyzed_df)
    ```
	
    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image54.png)

## Exercise 3: Generating and Storing Embeddings

### Task 1: Text Chunking

Before we can generate the embeddings, we need to split the text into
chunks. To do this we leverage SynapseML's PageSplitter to divide the
documents into smaller sections, which are subsequently stored in
the chunks column. This allows for more granular representation and
processing of the document content.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.
	
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
	
    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image55.png)

    >[!note]**Note**: The chunks for each document are presented in a single row
    inside an array. In order to embed all the chunks in the following
    cells, we need to have each chunk in a separate row.

2.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.
	
    ```
    from pyspark.sql.functions import posexplode, col, concat
    
    # Each "chunks" column contains the chunks for a single document in an array
    # The posexplode function will separate each chunk into its own row
    exploded_df = splitted_df.select("file_name", posexplode(col("chunks")).alias("chunk_index", "chunk"))
    
    # Add a unique identifier for each chunk
    exploded_df = exploded_df.withColumn("unique_id", concat(exploded_df.file_name, exploded_df.chunk_index))
    
    display(exploded_df)
    ```

    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image56.png)

From this code snippet we first explode these arrays so there is only
one chunk in each row, then filter the Spark DataFrame in order to only
keep the path to the document and the chunk in a single row.

### Task 2: Generating Embeddings

Next we'll generate the embeddings for each chunk. To do this we utilize
both SynapseML and Azure OpenAI Service. By integrating the built in
Azure OpenAI service with SynapseML, we can leverage the power of the
Apache Spark distributed computing framework to process numerous prompts
using the OpenAI service.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.
	
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
	
    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image57.png)

This integration enables the SynapseML embedding client to generate
embeddings in a distributed manner, enabling efficient processing of
large volumes of data

### Task 3: Storing Embeddings 

[Azure AI Search](https://learn.microsoft.com/azure/search/search-what-is-azure-search?WT.mc_id=data-114676-jndemenge) is
a powerful search engine that includes the ability to perform full text
search, vector search, and hybrid search. For more examples of its
vector search capabilities, see the [azure-search-vector-samples
repository](https://github.com/Azure/azure-search-vector-samples/).

Storing data in Azure AI Search involves two main steps:

**Creating the index:** The first step is to define the schema of the
search index, which includes the properties of each field as well as any
vector search strategies that will be used.

**Adding chunked documents and embeddings:** The second step is to
upload the chunked documents, along with their corresponding embeddings,
to the index. This allows for efficient storage and retrieval of the
data using hybrid and vector search.

1.  The following code snippet demonstrates how to create an index in
    Azure AI Search using the Azure AI Search REST API. This code
    creates an index with fields for the unique identifier of each
    document, the text content of the document, and the vector embedding
    of the text content.

2.	Click the **➕ Code** icon below the cell output to insert a new code cell in the notebook. Replace the **AI Search API key** and the **AI Search service URL**, then paste the following code into the cell. Click the **▶ Run** cell button and review the output
	
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
     ![](./media/img4.png)
    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image58.png)
    
    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image59.png)

3.  The next step is to upload the chunks to the newly created Azure AI
    Search index. The Azure AI Search REST API supports up to 1000
    "documents" per request. Note that in this case, each of our
    "documents" is in fact a chunk of the original file

    Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.
	
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
	
    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image60.png)
    
    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image61.png)

## Exercise 4: Retrieving Relevant Documents and Answering Questions

After processing the document, we can proceed to pose a question. We
will use SynapseML to convert the user's question into an embedding and
then utilize cosine similarity to retrieve the top K document chunks
that closely match the user's question.

### Task 1: Configure Environment & Azure API Keys

Create a new notebook in the Lakehouse and save it as rag_application.
We'll use this notebook to build the RAG application.

1.  Provide the credentials for access to Azure AI Search. You can copy
    the values from the from Azure Portal (Exercise 1 \>Task 4).

    Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.

    ```
    # Azure AI Search
    AI_SEARCH_NAME = 'mysearchservice@lab.LabInstance.Id'
    AI_SEARCH_INDEX_NAME = 'rag-demo-index'
    AI_SEARCH_API_KEY = ''
    ```
	
    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image62.png)

3.  The following function takes a user's question as input and converts
    it into an embedding using the text-embedding-ada-002 model. This
    code assumes you're using the Pre-built AI Services in Microsoft
    Fabric.

    Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.
	
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

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image63.png)

### Task 2: Retrieve Relevant Documents

1.  The next step is to use the user question and its embedding to
    retrieve the top K most relevant document chunks from the search
    index. The following function retrieves the top K entries using
    hybrid search.

    Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.
	
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
	
    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image64.png)
    
    With those functions defined, we can define a function that takes a
    user's question, generates an embedding for the question, retrieves the
    top K document chunks, and concatenates the content of the retrieved
    documents to form the context for the user's question.

3.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.
	
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

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image65.png)

### Task 3: Answering the User's Question

Finally, we can define a function that takes a user's question,
retrieves the context for the question, and sends both the context and
the question to a large language model to generate a response. For this
demo, we'll use the gpt-35-turbo-16k, a model that is optimized for
conversation.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output.
	
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
	
    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image66.png)
    
    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image67.png)

    > Now, we can call that function with an example question to see the response:

3.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Replace the placeholders YOUR_ADMIN_API_KEY, YOUR_INDEX_NAME and search_service_name with your values and click on **▷Run cell** button and review the output.

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
![](./media/labimg3.png)
![](./media/labimg4.png)

### Task 4: Delete the resources

To avoid incurring unnecessary Azure costs, you should delete the
resources you created in this quickstart if they're no longer needed. To
manage resources, you can use the [Azure
portal](https://portal.azure.com/?azure-portal=true).

1.  To delete the storage account, navigate to **Azure portal Home**
    page, click on **Resource groups**.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image69.png)

2.  Click on the assigned resource group, **@lab.CloudResourceGroup(ResourceGroup1).Name**.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image70.png)

3.  In the **Resource group** home page, select the resources Azure AI
    services, Key vault and Search service.

	> ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image71.png)

4.  Select **Delete**

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image72.png)

5.  In the **Delete Resources** pane that appears on the right side,
    navigate to **Enter +++delete+++ to confirm deletion** field, then
    click on the **Delete** button.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image74.png)

6.  On **Delete confirmation** dialog box, click on **Delete** button.

    > ![A screenshot of a computer error Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image75.png)

7.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then
    press the **Enter** button.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image76.png)

8.  Select the ***...*** option under the workspace name and
    select **Workspace settings**.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image77.png)

9.  Select **General** and click on **Remove this workspace.**

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image78.png)

10. Click on **Delete** in the warning that pops up.

    > ![A white background with black text Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image79.png)

11. Wait for a notification that the Workspace has been deleted, before
    proceeding to the next lab.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2006/media/image80.png)







