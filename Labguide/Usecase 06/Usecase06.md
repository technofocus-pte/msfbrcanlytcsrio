**Introdução**

Analisar dados estruturados tem sido um processo fácil há algum tempo,
mas o mesmo não se pode dizer de dados não estruturados. Dados não
estruturados, como texto, imagens e vídeos, são mais difíceis de
analisar e interpretar. No entanto, com o surgimento de modelos
avançados de AI, como o GPT-3 e o GPT-4 da OpenAI, está se tornando mais
fácil analisar e obter insights a partir de dados não estruturados.

Um exemplo desse tipo de análise é a capacidade de consultar um
documento para obter informações específicas usando linguagem natural, o
que é possível por meio de uma combinação de recuperação de informações
e geração de linguagem.

Ao aproveitar a estrutura RAG (Retrieval-Augmented Generation), você
pode criar um poderoso sistema de perguntas e respostas que usa um large
language model (LLM ) e seus próprios dados para gerar respostas.

A arquitetura de tal aplicação é mostrada abaixo:

![Architecture diagram connecting Azure OpenAI with Azure AI Search and
Document Intelligence](./media/image1.png)

**Objetivo**

- Criar um recurso de vários serviços para os serviços do Azure AI
  usando o portal do Azure.

- Criar capacidade do Fabric e espaço de trabalho, Key Vault e espaço de
  trabalho do Fabric.

- Pré-processar documentos PDF usando o Azure AI Document Intelligence
  nos serviços do Azure AI.

- Realizar a segmentação de texto usando o SynapseML.

- Gerar incorporações para os blocos usando o SynapseML e os serviços
  Azure OpenAI.

- Armazenar os dados incorporados no Azure AI Search.

- Criar um sistema de perguntas e respostas.

# **Exercício 1: Configurar o ambiente**

## Tarefa 1: Criar um recurso de vários serviços para os serviços do Azure AI

O recurso de vários serviços está listado no portal **Azure AI
services** \> **Azure AI services multi-service account**. Para criar um
recurso de vários serviços, siga estas instruções:

1.  Selecione este link para criar um recurso com vários serviços:

++++https://portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne+++

[TABLE]

2.  Na página **Create**, forneça as seguintes informações:

3.  Configure outras definições para o seu recurso conforme necessário,
    leia e aceite as condições (se aplicável) e, em seguida, selecione
    **Review + create**.

![](./media/image2.png)

4.  Na aba **Review+submit**, após a validação ser aprovada, clique no
    botão **Create**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image3.png)

5.  Após a conclusão da implementação, clique no botão **Go to
    resource**.

> ![A screenshot of a computer Description automatically
> generated](./media/image4.png)

6.  Em sua janela de **serviço do Azure** **AI**, navegue até a seção
    **Resource Management** e clique em **Keys and Endpoints**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image5.png)

7.  Na página **Keys and Endpoints**, copie os valores de **KEY1, KEY2**
    e **endpoint** e cole-os em um bloco de notas, conforme mostrado na
    imagem abaixo. Em seguida, **salve** o bloco de notas para usar as
    informações nas próximas tarefas.

![](./media/image6.png)

## **Tarefa 2: Criar um Key Vault usando o portal do Azure**

1.  Na página inicial do portal do Azure, clique em **+ Create
    Resource**.

> ![A screenshot of a computer Description automatically
> generated](./media/image7.png)

2.  Na barra de pesquisa da página **Create a resource**, digite **Key
    vault** e clique no **Key vault** que
    aparecer.![](./media/image8.png)

3.  Clique na seção **Key vault.**

> ![](./media/image9.png)

4.  Na página **Create a key Vault**, forneça as seguintes informações e
    clique no botão **Review+create.**

[TABLE]

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image10.png)

5.  Após a validação ser aprovada, clique no botão **Create**.

> ![](./media/image11.png)

6.  Após a conclusão da implementação, clique no botão **Go to
    resource**.

> ![](./media/image12.png)

7.  Na janela **fabrickeyvaultXX**, no menu à esquerda, clique em
    **Access control(IAM).**

![](./media/image13.png)

8.  Na página **Access control(IAM)**, clique em + **Add** e selecione
    **Add role assignments.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image14.png)

9.  Em **Job function roles,** digite +++**Key vault administrator+++**
    Na caixa de pesquisa, selecione o resultado. Clique em **Next.**

> ![](./media/image15.png)

10. Na aba **Add role assignment**, selecione **Assign access** ao
    **User group** or **service principal**. Em **Members**, clique em
    **+ Select members.**

> ![](./media/image16.png)

11. Na aba **Select members**, pesquise sua assinatura do Azure OpenAI e
    clique em **Select.**

![](./media/image17.png)

12. Na página **Add role assignment,** clique em **Review + Assign**.
    Você receberá uma notificação assim que a atribuição da função for
    concluída.

> ![A screenshot of a computer Description automatically
> generated](./media/image18.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image19.png)

13. Você verá uma notificação – added as Azure AI Developer for
    Azure-openai-testXX

![A screenshot of a computer Description automatically
generated](./media/image20.png)

## Tarefa 3: Criar um segredo usando o Azure Key Vault

1.  Na barra lateral esquerda do Key Vault, selecione **Objects** e, em
    seguida, selecione **Secrets**.

> ![](./media/image21.png)

2.  Selecione **+ Generate/Import**.

> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

3.  Na página **Create a secret**, forneça as seguintes informações e
    clique no botão **Create**.

[TABLE]

> ![](./media/image23.png)

4.  Selecionar **+ Generate/Import**.

> ![](./media/image24.png)

5.  Na página **Create a secret**, forneça as seguintes informações e
    clique no botão **Create**.

[TABLE]

![](./media/image25.png)

![](./media/image26.png)

6.  Na página **Key vault**, copie o nome do **Key vault** e os valores
    dos **Secrets** e cole-os em um bloco de notas, como mostrado na
    imagem abaixo. Em seguida, **salve** o bloco de notas para usar as
    informações nas próximas tarefas.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image27.png)

## **Tarefa 4: Criar um serviço Azure AI Search no portal**

1.  Na página inicial do portal do Azure, clique em **+ Create
    Resource**.

> ![A screenshot of a computer Description automatically
> generated](./media/image7.png)

2.  Na barra de pesquisa da página **Create a resource**, digite **Azure
    cognitive Search** e clique em **Azure AI Search** que aparecer.

![](./media/image28.png)

3.  Clique na seção **Azure AI Search**.

![A screenshot of a computer Description automatically
generated](./media/image29.png)

4.  Na página do **Azure AI Search**, clique no botão **Create**.

> ![A screenshot of a computer Description automatically
> generated](./media/image30.png)

5.  Na página **Create a search service**, forneça as seguintes
    informações e clique no botão **Review+create.**

[TABLE]

![](./media/image31.png)

![A screenshot of a computer Description automatically
generated](./media/image32.png)

6.  Após a validação ser aprovada, clique no botão **Create**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image33.png)

7.  Após a conclusão da implementação, clique no botão **Go to
    resource.**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image34.png)

8.  Copie o **nome da pesquisa de AI** e cole-o em um bloco de notas,
    como mostrado na imagem abaixo. Em seguida, **salve** o bloco de
    notas para usar as informações no próximo laboratório.

![](./media/image35.png)

## **Tarefa 5: Criar um espaço de trabalho do Fabric**

Nesta tarefa, você criará um espaço de trabalho do Fabric. O espaço de
trabalho contém todos os itens necessários para este tutorial do
Lakehouse, incluindo o próprio Lakehouse, fluxos de dados, canais do
Data Factory, blocos de notas, conjuntos de dados do Power BI e
relatórios.

1.  Abra seu navegador, acesse a barra de endereços e digite ou cole a
    seguinte URL: https://app.fabric.microsoft.com/ Em seguida,
    pressione o botão **Enter**.

> ![A search engine window with a red box Description automatically
> generated with medium confidence](./media/image36.png)

2.  Na janela do **Microsoft Fabric**, insira suas credenciais e clique
    no botão **Submit**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image37.png)

3.  Em seguida, na janela da **Microsoft**, digite a senha e clique no
    botão **Sign in.**

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image38.png)

4.  Na janela **Stay signed in?**, clique no botão **Yes.**

> ![A screenshot of a computer error AI-generated content may be
> incorrect.](./media/image39.png)

5.  No painel espaço de trabalho, selecione **+ New workspace**.

> ![](./media/image40.png)

6.  No painel **Create a workspace** que aparece no lado direito, insira
    os seguintes detalhes e clique no botão **Apply**.

[TABLE]

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image41.png)
>
> ![](./media/image42.png)

7.  Aguarde a conclusão da implementação. Isso leva de 2 a 3 minutos.

![](./media/image43.png)

## **Tarefa 6: Criar um Lakehouse**

8.  Na página inicial do **Fabric**, selecione **+ New item** e escolha
    o bloco **Lakehouse**.

> ![A screenshot of a computer Description automatically
> generated](./media/image44.png)

9.  Na caixa de diálogo **New lakehouse**, digite
    +++**data_lakehouse**+++ no campo **Name**, clique no botão
    **Create** e abra o novo Lakehouse.

> **Observação**: Certifique-se de remover o espaço antes de
> **data_lakehouse**.
>
> ![A screenshot of a computer Description automatically
> generated](./media/image45.png)

10. Você verá uma notificação informando **Successfully created SQL
    endpoint**.

> ![A screenshot of a computer Description automatically
> generated](./media/image46.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image47.png)

# **Exercício 2: Carregar o pré-processamento de documentos PDF**

## **Tarefa 1: Configurar chaves de API do Azure**

Para começar, volte ao Lakehouse rag_workshop no seu espaço de trabalho
e crie um novo bloco de notas selecionando **Open Notebook** e, em
seguida, **New Notebook** nas opções.

1.  Na página **Lakehouse**, navegue até o menu suspenso **Open
    notebook** na barra de comandos e clique em **New notebook**.

![A screenshot of a computer Description automatically
generated](./media/image48.png)

2.  No editor de consultas, cole o seguinte código. Forneça as chaves
    para os serviços do Azure AI, o nome do Azure Key Vault e os
    segredos para acessar os serviços.

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

## Tarefa 2: Carregar e analisar o documento

1.  Utilizaremos um documento específico chamado
    [**support.pdf**](https://github.com/Azure-Samples/azure-openai-rag-workshop/blob/main/data/support.pdf),
    que será a fonte dos nossos dados.

2.  Para baixar o documento, use o ícone **+ Code** abaixo da saída da
    célula para adicionar uma nova célula de código ao bloco de notas e
    insira o código a seguir. Clique no botão **▷ Run cell** e revise a
    saída.

**Copiar**

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

3.  Agora, carregue o documento PDF em um Spark DataFrame usando o
    método spark.read.format(“binaryFile”) fornecido pelo Apache Spark.

4.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

from pyspark.sql.functions import udf

from pyspark.sql.types import StringType

document_path = f"Files/{filename}"

df =
spark.read.format("binaryFile").load(document_path).select("\_metadata.file_name",
"content").limit(10).cache()

display(df)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image52.png)

Este código irá ler o documento PDF e criar um Spark DataFrame chamado
df com o conteúdo do PDF. O DataFrame terá um esquema que representa a
estrutura do documento PDF, incluindo o seu conteúdo textual.

5.  Em seguida, usaremos o Azure AI Document Intelligence para ler os
    documentos PDF e extrair o texto deles.

6.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

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

7.  Podemos observar o Spark DataFrame analisado chamado analyzed_df
    usando o código a seguir. Observe que descartamos a coluna de
    conteúdo, pois ela não é mais necessária.

8.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

analyzed_df = analyzed_df.drop("content")

display(analyzed_df)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image54.png)

# Exercício 3: Gerar armazenamento de incorporações

## **Tarefa 1: Fragmentar texto**

Antes de gerarmos as incorporações, precisamos dividir o texto em
partes. Para isso, utilizamos o SynapseML. O PageSplitter divide os
documentos em seções menores, que são posteriormente armazenadas na
coluna de blocos. Isso permite uma representação e um processamento mais
detalhados do conteúdo do documento.

1.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

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

Observe que os trechos de cada documento são apresentados numa única
linha dentro de uma matriz. Para incorporar todos os trechos nas células
seguintes, precisamos ter cada trecho numa linha separada.

2.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

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

A partir deste trecho de código, primeiro expandimos essas matrizes para
que haja apenas um fragmento em cada linha e, em seguida, filtramos o
Spark DataFrame para manter apenas o caminho para o documento e o
fragmento em uma única linha.

## Tarefa 2: Gerar Incorporações

Em seguida, vamos gerar as incorporações para cada fragmento. Para isso,
utilizamos o SynapseML e o Azure OpenAI Service. Ao integrar o serviço
Azure OpenAI integrado ao SynapseML, podemos aproveitar o poder da
estrutura de computação distribuída Apache Spark para processar inúmeras
solicitações usando o serviço OpenAI.

1.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

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

Essa integração permite que o cliente de incorporação SynapseML gere
incorporações de forma distribuída, possibilitando o processamento
eficiente de grandes volumes de dados.

## Tarefa 3: Armazenar Incorporações

[O Azure AI
Search](https://learn.microsoft.com/azure/search/search-what-is-azure-search?WT.mc_id=data-114676-jndemenge)
é um mecanismo de busca poderoso que inclui a capacidade de realizar
buscas de texto completo, buscas vetoriais e buscas híbridas. Para mais
exemplos de seus recursos de busca vetorial, consulte o
[azure-search-vector-samples
repository](https://github.com/Azure/azure-search-vector-samples/).

O armazenamento de dados no Azure AI Search envolve duas etapas
principais:

**Criação do índice:** O primeiro passo é definir o esquema do índice de
pesquisa, que inclui as propriedades de cada campo, bem como quaisquer
estratégias de pesquisa vetorial que serão utilizadas.

**Adicionando documentos fragmentados e incorporações:** O segundo passo
é carregar os documentos fragmentados, juntamente com as suas
incorporações correspondentes, para o índice. Isto permite o
armazenamento e a recuperação eficientes dos dados utilizando pesquisa
híbrida e vetorial.

1.  O trecho de código a seguir demonstra como criar um índice no Azure
    AI Search usando a API REST do Azure AI Search. Este código cria um
    índice com campos para o identificador exclusivo de cada documento,
    o conteúdo de texto do documento e o vetor incorporado do conteúdo
    de texto.

2.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

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

3.  O próximo passo é carregar os fragmentos para o índice recém-criado
    do Azure AI Search. A API REST do Azure AI Search suporta até 1000
    "documents" por solicitação. Observe que, neste caso, cada um dos
    nossos "documents" é, na verdade, um fragmento do arquivo original.

4.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

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

# Exercício 4: Recuperar documentos relevantes e responder a perguntas

Após processar o documento, podemos prosseguir para a formulação da
pergunta. Usaremos [o
[SynapseML](https://microsoft.github.io/SynapseML/docs/Explore%20Algorithms/OpenAI/Quickstart%20-%20OpenAI%20Embedding/)](https://microsoft.github.io/SynapseML/docs/Explore%20Algorithms/OpenAI/Quickstart%20-%20OpenAI%20Embedding/)
para converter a pergunta do usuário numa incorporação e, em seguida,
utilizaremos a similaridade cosseno para recuperar os principais trechos
do documento K que correspondem mais à pergunta do utilizador.

## Tarefa 1: Configurar ambiente e chaves API do Azure

Crie um novo bloco de notas no Lakehouse e salve-o como rag_application.
Usaremos este bloco de notas para criar o aplicativo RAG.

1.  Forneça as credenciais de acesso ao Azure AI Search. Você pode
    copiar os valores do Portal do Azure. (Exercício 1 \> Tarefa 4)

2.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

Copiar

\# Azure AI Search

AI_SEARCH_NAME = ''

AI_SEARCH_INDEX_NAME = 'rag-demo-index'

AI_SEARCH_API_KEY = ''

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image62.png)

3.  A função a seguir recebe a pergunta de um usuário como entrada e a
    converte em um vetor de incorporação usando o modelo
    text-embedding-ada-002. Este código pressupõe que você esteja usando
    os serviços de AI pré-criados no Microsoft Fabric.

4.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

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

## Tarefa 2: Recuperar documentos relevantes

1.  A próxima etapa é usar a pergunta do usuário e sua incorporação para
    recuperar os K principais partes de documentos mais relevantes do
    índice de pesquisa. A função a seguir recupera as K principais
    entradas usando pesquisa híbrida.

2.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

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

Com essas funções definidas, podemos definir uma função que recebe a
pergunta do utilizador, gera uma incorporação para a pergunta, recupera
os principais trechos do documento K e une o conteúdo dos documentos
recuperados para formar o contexto da pergunta do utilizador.

3.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

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

## **Tarefa 3: Responder à pergunta do usuário**

Finalmente, podemos definir uma função que recebe a pergunta do usuário,
recupera o contexto da pergunta e envia tanto o contexto quanto a
pergunta para um Large Language Model para gerar uma resposta. Para esta
demonstração, usaremos o gpt-35-turbo-16k, um modelo otimizado para
conversação.

1.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

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

2.  Agora, podemos chamar essa função com uma pergunta de exemplo para
    ver a resposta:

3.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

**Copiar**

user_question = "how do i make a booking?"

response = get_response(user_question)

print(response)

![](./media/image68.png)

## Tarefa 4: Excluir os recursos

Para evitar custos desnecessários no Azure, você deve excluir os
recursos criados neste guia de início rápido caso não sejam mais
necessários. Para gerenciar recursos, você pode usar o [Azure
portal](https://portal.azure.com/?azure-portal=true).

1.  Para excluir a conta de armazenamento, acesse a página **Azure
    portal Home** e clique em **Resource groups**.

> ![A screenshot of a computer Description automatically
> generated](./media/image69.png)

2.  Clique no grupo de recursos atribuído.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image70.png)

3.  Na página inicial do **Resource group**, selecione os recursos de
    serviços do Azure AI, Key valut e serviço de
    pesquisa.![](./media/image71.png)

4.  Selecione **Delete.**

![](./media/image72.png)

![A screenshot of a computer error AI-generated content may be
incorrect.](./media/image73.png)

5.  No painel **Delete Resources** que aparece no lado direito, navegue
    até o campo **Enter +++delete+++ para confirmar a exclusão** e, em
    seguida, Clique no botão **Delete**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image74.png)

6.  Na caixa de diálogo **Delete confirmation**, clique no botão
    **Delete.**

> ![A screenshot of a computer error Description automatically
> generated](./media/image75.png)

7.  Abra seu navegador, acesse a barra de endereços e digite ou cole a
    seguinte URL: +++https://app.fabric.microsoft.com/+++ Em seguida,
    pressione o botão **Enter**.

> ![](./media/image76.png)

8.  Selecione a opção*... abaixo do nome do espaço de trabalho e*
    selecione **Workspace settings**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image77.png)

9.  Selecione **General** e clique em **Remove this workspace.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image78.png)

10. Clique em **Delete** no aviso que aparecer.

![A white background with black text Description automatically
generated](./media/image79.png)

11. Aguarde uma notificação informando que o espaço de trabalho foi
    excluído antes de prosseguir para o próximo laboratório.

![A screenshot of a computer Description automatically
generated](./media/image80.png)
