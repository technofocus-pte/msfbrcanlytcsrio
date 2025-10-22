# ユースケース 03 - Fabric Data Agent を使用してデータとチャットする

**導入：**

このユースケースでは、Microsoft Fabric のデータ
エージェントを紹介します。これにより、構造化データセットに対する自然言語クエリが可能になります。Fabric
データ エージェントは、大規模言語モデル (LLM)
を活用することで、平易な英語の質問を解釈し、有効な T-SQL
クエリに変換して、選択したLakehouse
データに対して実行できます。このハンズオン演習では、環境の構成、Fabric
ワークスペースのセットアップ、データのアップロード、AI
スキルを使用した会話形式のデータ操作のプロセスをガイドします。また、クエリ例の提供、精度向上のための指示の追加、Fabric
ノートブックからのプログラムによる AI
スキルの呼び出しなどの高度な機能についても学習します。

**目的:**

- Fabric ワークスペースを設定し、Lakehouseにデータをロードします。

- 自然言語クエリを有効にするには、データ
  エージェントを作成して構成します。

- 平易な英語で質問し、AI によって生成された SQL
  クエリの結果を表示します。

- カスタム指示とサンプルクエリを使用して AI 応答を強化します。

- Fabric ノートブックからプログラムで Data エージェントを使用します。

## **タスク0: ホスト環境の時刻を同期する**

1.  VM で**検索バーに移動してクリックし**、 **「Settings」と入力して**、
    **「Best match」**の下の**「Settings」をクリックします**。

> ![A screenshot of a computer Description automatically
> generated](./media/image1.png)

2.  Settingsウィンドウで、 **「Time &
    language」**に移動してクリックします。

![A screenshot of a computer Description automatically
generated](./media/image2.png)

3.  **「Time & language」ページ**で、 **「Date &
    time」**に移動してクリックします。

![A screenshot of a computer Description automatically
generated](./media/image3.png)

4.  下にスクロールして**「Additional settings」**セクションに移動し、
    **「Sync now」**ボタンをクリックします。同期には3～5分かかります。

![A screenshot of a computer Description automatically
generated](./media/image4.png)

5.  **Settings**ウィンドウを閉じます。

![A screenshot of a computer Description automatically
generated](./media/image5.png)

## **タスク 1: Fabricワークスペースを作成する**

このタスクでは、Fabric
ワークスペースを作成します。ワークスペースには、Lakehouse、データフロー、Data
Factory パイプライン、ノートブック、Power BI
データセット、レポートなど、このLakehouse
チュートリアルに必要なすべてのアイテムが含まれています。

1.  ブラウザを開き、アドレス バーに移動して、次の URL
    を入力または貼り付けます: +++ https://app.fabric.microsoft.com/+++
    **Enter**ボタンを押します。

> ![A search engine window with a red box Description automatically
> generated with medium confidence](./media/image6.png)

2.  **Microsoft Fabric**ウィンドウで資格情報を入力し、
    **\[Submit\]**ボタンをクリックします。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image7.png)

3.  次に、 **Microsoft**ウィンドウでパスワードを入力し、 **\[Sign
    in\]**ボタンをクリックします**。**

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image8.png)

4.  **「Stay signed in?」ウィンドウ**で、
    **「Yes」**ボタンをクリックします。

> ![A screenshot of a computer error AI-generated content may be
> incorrect.](./media/image9.png)

5.  \[Workspaces\] ペインで**\[+New workspace\]**を選択します。

> ![](./media/image10.png)

6.  右側に表示される**「Create a
    workspace」ペイン**で、次の詳細を入力し、
    **「Apply」**ボタンをクリックします。

[TABLE]

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image11.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image12.png)

7.  デプロイが完了するまでお待ちください。完了まで2～3分かかります。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image13.png)

## **タスク2: Lakehouseを作る**

1.  **Fabric** **ホーム**ページで、 **+ New item**を選択し、
    **Lakehouse**タイルを選択します。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image14.png)

2.  **\[New Lakehouse\]**ダイアログ ボックスで、
    **\[Name\]**フィールドに「+++ **AI_Fabric_lakehouseXX
    +++」と入力し**、
    **\[Create\]**ボタンをクリックして新しいLakehouseを開きます。

> **注意**:
> **AI_Fabric_lakehouseXX**の前のスペースを必ず削除してください。
>
> ![](./media/image15.png)

3.  **「Successfully created SQL endpoint」**という通知が表示されます。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image16.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image17.png)

4.  次に、テーブルをクエリするための新しいノートブックを作成します。
    **「Home」**リボンで、 **「Open
    notebook」**のドロップダウンを選択し、 **「New
    notebook」**を選択します**。**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image18.png)

## タスク3: AdventureWorksDWデータをLakehouseにアップロードする

まず、Lakehouseを作成し、必要なデータを入力します。

ウェアハウスまたはLakehouseに既にAdventureWorksDWのインスタンスがある場合は、この手順をスキップできます。そうでない場合は、ノートブックからLakehouseを作成します。ノートブックを使用して、Lakehouseにデータを入力します。

1.  クエリエディタに以下のコードをコピーして貼り付けます。「**Run
    all」**ボタンを選択してクエリを実行します。クエリが完了すると、結果が表示されます。

> import pandas as pd
>
> from tqdm.auto import tqdm
>
> base =
> "https://synapseaisolutionsa.z13.web.core.windows.net/data/AdventureWorks"
>
> \# load list of tables
>
> df_tables = pd.read_csv(f"{base}/adventureworks.csv",
> names=\["table"\])
>
> for table in (pbar := tqdm(df_tables\['table'\].values)):
>
> pbar.set_description(f"Uploading {table} to lakehouse")
>
> \# download
>
> df = pd.read_parquet(f"{base}/{table}.parquet")
>
> \# save as lakehouse table
>
> spark.createDataFrame(df).write.mode('overwrite').saveAsTable(table)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image19.png)
>
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image20.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image21.png)

数分後、Lakehouseに必要なデータが取り込まれます。

## タスク4: データエージェントを作成する

1.  次に、左側のナビゲーション
    ペインで**AI-Fabric-XXXX**をクリックします**。**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image22.png)

1.  **Fabric**ホームページで、 **+ New item**を選択します**。**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image23.png)

2.  **Filter by item type** 検索ボックスに**「+++data
    agent+++」と入力し**、**Data agentを選択します。**![A screenshot of
    a computer AI-generated content may be
    incorrect.](./media/image24.png)

3.  データ エージェント名として**+++AI-agent+++**と入力し、
    **\[Create\]を選択します**。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image25.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image26.png)

4.  AIエージェントページで**Add a data source**を選択します

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image27.png)

5.  **OneLake catalog**で、 **AI-Fabric_lakehouse Lakehouseを選択し**、
    **\[Add\]を選択します**。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image28.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image29.png)

2.  次に、AI
    スキルにアクセスを許可するテーブルを選択する必要があります。

このラボでは次のテーブルを使用します。

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

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image30.png)

## タスク5: 指示を与える

1.  **factinternetsales**を使用して最初に質問すると、データ
    エージェントがそれらの質問にかなり適切に回答します。

2.  **\[+++What is the most sold product?+++
    」**という質問の場合、次のようになります。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image31.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image32.png)

3.  すべての質問とSQLクエリをコピーしてメモ帳に貼り付け、メモ帳を保存して、今後のタスクで情報を使用します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image33.png)

4.  **FactResellerSales**を選択し、次のテキストを入力して、下の画像に示すように
    \[**Submit\]** アイコンをクリックします**。**

+++ **What is our most sold product?**+++

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image34.png)

![A screenshot of a chat AI-generated content may be
incorrect.](./media/image35.png)

クエリの実験を続ける場合は、さらに指示を追加する必要があります。

5.  **dimcustomer**を選択し、次のテキストを入力して**Submitアイコンをクリックします。**

+++ **how many active customers did we have June 1st, 2013?**+++

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image36.png)

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image37.png)

6.  すべての質問とSQLクエリをコピーしてメモ帳に貼り付け、メモ帳を保存して、今後のタスクで情報を使用します。

7.  **ディメンション「FactInternetSales」を選択し、次のテキストを入力して「Submit」アイコンをクリックします。**

**+++what are the monthly sales trends for the last year? +++**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image38.png)

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image39.png)

6.  **dimproduct、FactInternetSales**を選択し、次のテキストを入力して**Submitアイコン**をクリックします。

+++ **which product category had the highest average sales price?** +++

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image40.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image41.png)

問題の一部は、「アクティブ顧客」に正式な定義がないことです。モデルのテキストボックスの注記に詳細な説明を追加すると役立つかもしれませんが、ユーザーからこの質問が頻繁に寄せられる可能性があります。AIが質問を正しく処理できるようにする必要があります。

7.  関連するクエリは中程度に複雑なので、\[**Examplr
    queries\]**ボタンを選択して例を指定してください。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image42.png)

8.  編集を選択

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image43.png)

8.  \[Example SQL queries\] タブで、 \[+**Add
    example**\]を選択します**。**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image44.png)

9.  手動で例を追加することもできますが、JSONファイルからアップロードすることもできます。ファイルから例を提供すると、多数のSQLクエリを一度にアップロードしたい場合、クエリを1つずつ手動でアップロードするよりも便利です。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image45.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image46.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image47.png)

10. メモ帳に保存したすべてのクエリと SQL クエリを追加し、「Download all
    as .json」をクリックします。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image48.png)

## タスク6: Dataエージェントをプログラムで使用する

データエージェントには、説明と例の両方が追加されました。テストが進むにつれて、より多くの例と説明を追加することで、AIスキルをさらに向上させることができます。同僚と協力して、彼らが尋ねたい質問の種類をカバーする例と説明を提供しているかどうかを確認してください。

AIスキルはFabricノートブック内でプログラム的に使用できます。AIスキルに公開されたURL値があるかどうかを判断します。

1.  Data agent Fabric
    ページの**\[Home\]**リボンで**\[Settings\]**を選択します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image49.png)

2.  AI
    スキルを公開する前は、このスクリーンショットに示すように、公開された
    URL 値はありません。

3.  AIスキル設定を閉じます。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image50.png)

4.  **\[Home\]**リボンで、\[**Publish\]**を選択します。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image51.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image52.png)

9.  **View publishing details**をクリックします

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image53.png)

5.  このスクリーンショットに示すように、AI エージェントの公開された URL
    が表示されます。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image54.png)

6.  URLをコピーしてメモ帳に貼り付け、メモ帳に保存して、次回の情報に使用してください。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image54.png)

7.  左側のナビゲーション ペインで**Notebook1**を選択します。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image55.png)

10. **「+Code」アイコン**を使用してノートブックに新しいコードセルを追加し、次のコードを入力して**URLを置き換えます**。
    **▷実行**ボタンをクリックして出力を確認します。

+++%pip install "openai==1.70.0"+++

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image56.png)

11. **「+Code」アイコン**を使用してノートブックに新しいコードセルを追加し、次のコードを入力して**URLを置き換えます**。
    **▷実行**ボタンをクリックして出力を確認します。

> +++%pip install httpx==0.27.2+++
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image57.png)

8.  **「+Code」アイコン**を使用してノートブックに新しいコードセルを追加し、次のコードを入力して**URLを置き換えます**。
    **▷実行**ボタンをクリックして出力を確認します。

> import requests
>
> import json
>
> import pprint
>
> import typing as t
>
> import time
>
> import uuid
>
> from openai import OpenAI
>
> from openai.\_exceptions import APIStatusError
>
> from openai.\_models import FinalRequestOptions
>
> from openai.\_types import Omit
>
> from openai.\_utils import is_given
>
> from synapse.ml.mlflow import get_mlflow_env_config
>
> from sempy.fabric.\_token_provider import SynapseTokenProvider
>
> base_url = "https://\<generic published base URL value\>"
>
> question = "What datasources do you have access to?"
>
> configs = get_mlflow_env_config()
>
> \# Create OpenAI Client
>
> class FabricOpenAI(OpenAI):
>
> def \_\_init\_\_(
>
> self,
>
> api_version: str ="2024-05-01-preview",
>
> \*\*kwargs: t.Any,
>
> ) -\> None:
>
> self.api_version = api_version
>
> default_query = kwargs.pop("default_query", {})
>
> default_query\["api-version"\] = self.api_version
>
> super().\_\_init\_\_(
>
> api_key="",
>
> base_url=base_url,
>
> default_query=default_query,
>
> \*\*kwargs,
>
> )
>
> def \_prepare_options(self, options: FinalRequestOptions) -\> None:
>
> headers: dict\[str, str | Omit\] = (
>
> {\*\*options.headers} if is_given(options.headers) else {}
>
> )
>
> options.headers = headers
>
> headers\["Authorization"\] = f"Bearer {configs.driver_aad_token}"
>
> if "Accept" not in headers:
>
> headers\["Accept"\] = "application/json"
>
> if "ActivityId" not in headers:
>
> correlation_id = str(uuid.uuid4())
>
> headers\["ActivityId"\] = correlation_id
>
> return super().\_prepare_options(options)
>
> \# Pretty printing helper
>
> def pretty_print(messages):
>
> print("---Conversation---")
>
> for m in messages:
>
> print(f"{m.role}: {m.content\[0\].text.value}")
>
> print()
>
> fabric_client = FabricOpenAI()
>
> \# Create assistant
>
> assistant = fabric_client.beta.assistants.create(model="not used")
>
> \# Create thread
>
> thread = fabric_client.beta.threads.create()
>
> \# Create message on thread
>
> message =
> fabric_client.beta.threads.messages.create(thread_id=thread.id,
> role="user", content=question)
>
> \# Create run
>
> run = fabric_client.beta.threads.runs.create(thread_id=thread.id,
> assistant_id=assistant.id)
>
> \# Wait for run to complete
>
> while run.status == "queued" or run.status == "in_progress":
>
> run = fabric_client.beta.threads.runs.retrieve(
>
> thread_id=thread.id,
>
> run_id=run.id,
>
> )
>
> print(run.status)
>
> time.sleep(2)
>
> \# Print messages
>
> response =
> fabric_client.beta.threads.messages.list(thread_id=thread.id,
> order="asc")
>
> pretty_print(response)
>
> \# Delete thread
>
> fabric_client.beta.threads.delete(thread_id=thread.id)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image58.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image59.png)

## タスク7: リソースを削除する

1.  左側のナビゲーションメニューからワークスペース「
    **AI-Fabric-XXXX」**を選択します。ワークスペースアイテムビューが開きます。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image60.png)

2.  ワークスペース名の下の**\[...\]オプション**を選択し、 **\[Workspace
    settings\]**を選択します。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image61.png)

3.  **\[Other\]**と\[**Remove this workspace**\]を選択します**。**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image62.png)

4.  ポップアップ表示される警告で**「Delete」**をクリックします。

> ![A white background with black text AI-generated content may be
> incorrect.](./media/image63.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image64.png)

**まとめ：**

このラボでは、Microsoft Fabric の Data Agent
を使用して会話型分析のパワーを最大限に引き出す方法を学習しました。Fabric
ワークスペースを構成し、構造化データをLakehouseに取り込み、自然言語の質問を
SQL クエリに変換する AI
スキルをセットアップしました。また、クエリ生成を改善するための手順と例を提供することで、AI
エージェントの機能を強化しました。最後に、Fabric
ノートブックからプログラムでエージェントを呼び出し、エンドツーエンドの
AI 統合を実証しました。このラボでは、自然言語と生成 AI
テクノロジを通じて、ビジネスユーザーがエンタープライズ
データにアクセスしやすく、使いやすく、インテリジェントに活用できるようにします。
