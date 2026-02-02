# ユースケース04: Apache Sparkでデータを分析する

**導入**

Apache
Sparkは、分散データ処理用のオープンソースエンジンであり、datalakeストレージ内の膨大なデータの探索、処理、分析に広く利用されています。Sparkは、Azure
HDInsight、Azure Databricks、Azure Synapse Analytics、Microsoft
Fabricなど、多くのデータプラットフォーム製品の処理オプションとして利用できます。Sparkの利点の一つは、Java、Scala、Python、SQLなど、幅広いプログラミング言語をサポートしていることです。これにより、Sparkは、データクレンジングと操作、統計分析と機械学習、データ分析と可視化といったデータ処理ワークロードに非常に柔軟なソリューションを提供します。

Microsoft Fabric Lakehouse内のテーブルは、Apache Spark
向けのオープンソース Delta Lake 形式に基づいています。Delta Lake
は、バッチデータ操作とストリーミングデータ操作の両方でリレーショナルセマンティクスのサポートを追加し、datalake内の基盤ファイルに基づくテーブル内のデータを
Apache Spark
で処理およびクエリできるLakehouseアーキテクチャの構築を可能にします。

Microsoft Fabric では、Dataflows (Gen2)
が様々なデータソースに接続し、Power Query Online
で変換を実行します。その後、Data
Pipelinesでデータフローを使用して、Lakehouseやその他の分析ストアにデータを取り込み、Power
BI レポート用のデータセットを定義することができます。

このラボは、Dataflows (Gen2)
のさまざまな要素を紹介することを目的としており、企業内に存在する可能性のある複雑なソリューションを作成するものではありません。

**目的**:

- Fabric の試用版を有効にして、Microsoft Fabric
  にワークスペースを作成します。

- Lakehouse環境を確立し、分析用のデータ ファイルをアップロードします。

- インタラクティブなデータの探索と分析のためのノートブックを生成します。

- さらに処理して視覚化するために、データをDataframes に読み込みます。

- PySpark を使用してデータに変換を適用します。

- クエリを最適化するために、変換されたデータを保存してパーティション分割します。

- 構造化データ管理のために Spark メタストアにテーブルを作成します。

- DataFrame を「salesorders」という名前の管理されたデルタ
  テーブルとして保存します。

- 指定されたパスを持つ「external_salesorder」という名前の外部デルタ
  テーブルとして DataFrame を保存します。

- マネージドテーブルと外部テーブルのプロパティについて説明し、比較します。

- 分析とレポートのためにテーブルに対して SQL クエリを実行します。

- matplotlib や seaborn などの Python
  ライブラリを使用してデータを視覚化します。

- データ エンジニアリング エクスペリエンスでデータ
  Lakehouseを確立し、後続の分析のために関連データを取り込みます。

- データを抽出、変換し、Lakehouseにロードするためのデータフローを定義します。

- 変換されたデータをLakehouseに保存するために、Power Query
  内でデータの保存先を構成します。

- データフローをパイプラインに組み込んで、スケジュールされたデータの処理と取り込みを有効にします。

- 演習を終了するには、ワークスペースと関連要素を削除します。

# 演習 1: ワークスペース、Lakehouse、ノートブックを作成し、Dataframes にデータをロードする

## タスク1: ワークスペースを作成する

Fabric でデータを操作する前に、Fabric
トライアルが有効になっているワークスペースを作成します。

1.  ブラウザを開き、アドレスバーに移動して、次の URL
    を入力または貼り付けます:
    +++https://app.fabric.microsoft.com/+++。Enterボタンを押します。

> **注記**: Microsoft Fabric Homeページに移動した場合は、手順 2 から 4
> をスキップしてください。
>
> ![](./media/image1.png)

2.  **Microsoft Fabric** ウィンドウで資格情報を入力し、\[**Submit**\]
    ボタンをクリックします。
    |---|---|
    | Username | +++@lab.CloudPortalCredential(User1).Username+++ |
    | Password | +++@lab.CloudPortalCredential(User1).Password+++ |

> ![](./media/image2.png)

3.  次に、**Microsoft** ウィンドウでパスワードを入力し、「**Sign
    in**」ボタンをクリックします。

> ![A login screen with a red box and blue text Description
> automatically generated](./media/image3.png)

4.  「**Stay signed
    in?**」ウィンドウで、「**Yes**」ボタンをクリックします。

> ![A screenshot of a computer error Description automatically
> generated](./media/image4.png)

5.  ファブリックホームページで、**+New workspace** を選択します。

> ![A screenshot of a computer Description automatically
> generated](./media/image5.png)

6.  「**Create a
    workspace**」タブで、次の詳細を入力し、「**Apply**」ボタンをクリックします。

    |  |  |
    |-----|----|
    |Name|	+++dp_Fabric@lab.LabInstance.Id+++ (must be a unique Id)| 
    |Description|	This workspace contains Analyze data with Apache Spark|
    |Advanced|	Under License mode, select Fabric capacity|
    |Default storage format	|Small dataset storage format|

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image6.png)
>
> ![](./media/image7.png)

7.  デプロイが完了するまでお待ちください。完了まで2～3分かかります。新しいワークスペースが開くと、空になっているはずです。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

## タスク2: Lakehouseを作成し、ファイルをアップロードする

ワークスペースが作成されたので、ポータルでデータ エンジニアリング
エクスペリエンスに切り替えて、分析するデータ ファイル用のdata
lakehouseを作成します。

1.  ナビゲーション バーの **+New item** ボタンをクリックして、新しい
    Eventhouse を作成します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image9.png)

2.  「**Lakehouse**」タイルをクリックします。

![A screenshot of a computer Description automatically
generated](./media/image10.png)

3.  \[**New lakehouse** \] ダイアログ ボックスで、\[**Name**\]
    フィールドに「+++**Fabric_lakehouse**+++」と入力し、\[Create\]
    ボタンをクリックして新しいLakehouseを開きます。

![A screenshot of a computer Description automatically
generated](./media/image11.png)

4.  1分ほど経つと、新しい空のLakehouseが作成されます。分析のために、データLakehouseにデータをインジェストする必要があります。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image12.png)

5.  **Successfully created SQL endpoint**を示す通知が表示されます。

![](./media/image13.png)

6.  **Explorer**セクションの **fabric_lakehouse**
    で、**Files**フォルダの横にマウスを移動し、横長の省略記号 (…)
    メニューをクリックします。「**Upload**」に移動し、下の画像のように「**Upload
    folder」**をクリックします。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image14.png)

7.  右側に表示される「**Upload**」フォルダーペインで、**Files/**の下にある**フォルダーアイコン**を選択し、**C:\LabFiles**
    に移動して「**orders**」フォルダーを選択し、「**Upload**」ボタンをクリックします。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image15.png)

8.  「**Upload 3 files to this site?**」というダイアログ
    ボックスが表示された場合は、「**Upload**」ボタンをクリックします。

![](./media/image16.png)

9.  Uploadフォルダーペインで、\[**Upload**\] ボタンをクリックします。

> ![](./media/image17.png)

10. ファイルがアップロードされたら、**Uploadフォルダー**ペインを閉じます。

![A screenshot of a computer Description automatically
generated](./media/image18.png)

11. \[**Files** \] を展開し、**orders** フォルダを選択して、CSV
    ファイルがアップロードされていることを確認します。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image19.png)

## タスク3: ノートブックを作成する

Apache Spark
でデータを操作するには、ノートブックを作成します。ノートブックは、複数の言語でコードを記述して実行し、メモを追加して記録できるインタラクティブな環境を提供します。

1.  **Home** ページで、datalakeの**orders** フォルダの内容を表示しているときに、\[**Open
    notebook** \] メニューで \[**New notebook**\] を選択します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image20.png)

2.  数秒後、1つのセルを含む新しいノートブックが開きます。ノートブックは、*コードまたはマークダウン*（フォーマットされたテキスト）を含む1つ以上のセルで構成されています。

![](./media/image21.png)

3.  最初のセル (現在は*コード セル*) を選択し、右上にある動的ツール
    バーで **M**↓ ボタンを使用して、**セルをマークダウン
    セルに変換します。**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image22.png)

4.  セルがマークダウン
    セルに変更されると、そこに含まれるテキストがレンダリングされます。

![A screenshot of a computer Description automatically
generated](./media/image23.png)

5.  **🖉**(編集)
    ボタンをクリックして、セルを編集モードに切り替え、すべてのテキストを置き換えてから、次のようにマークダウンを変更します。

    ```
    # Sales order data exploration
    
    Use the code in this notebook to explore sales order data.
    ```

![](./media/image24.png)

![A screenshot of a computer Description automatically
generated](./media/image25.png)

6.  編集を停止し、レンダリングされたマークダウンを表示するには、ノートブックのセルの外側の任意の場所をクリックします。

![A screenshot of a computer Description automatically
generated](./media/image26.png)

## タスク4: Dataframes にデータをロードする

これで、データを*dataframe*に読み込むコードを実行する準備が整いました。Spark
のDataframes は Python の Pandas Dataframes
に似ており、行と列のデータを扱うための共通構造を提供します。

**注記**SparkはScala、Javaなど、複数のコーディング言語をサポートしています。この演習では、Spark向けに最適化されたPythonであるPySparkを使用します。PySparkはSparkで最もよく使われる言語の1つであり、Fabricノートブックのデフォルト言語です。

1.  ノートブックが表示されている状態で、\[**Files** \]
    リストを展開し、orders フォルダーを選択して、CSV
    ファイルがノートブック エディターの横に表示されるようにします。

> ![A screenshot of a computer Description automatically
> generated](./media/image27.png)

2.  2019.csvファイルにマウスを移動します。2019.csvの横にある水平の省略記号（…）をクリックします。「**Load
    data**」をクリックし、「**Spark**」を選択します。以下のコードを含む新しいコードセルがノートブックに追加されます。


    ```
    df = spark.read.format("csv").option("header","true").load("Files/orders/2019.csv")
    # df now is a Spark DataFrame containing CSV data from "Files/orders/2019.csv".
    display(df)
    ```
> ![A screenshot of a computer Description automatically
> generated](./media/image28.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image29.png)

**ヒント**:
左側のLakehouseエクスプローラーパネルを非表示にするには、«アイコンを使用します。

ノートブックに集中するのに役立ちます。

3.  セルの左側にある**▷ Run cell**ボタンを使用して、実行します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image30.png)

**注記**:
Sparkコードを実行するのは初めてなので、Sparkセッションを開始する必要があります。そのため、セッションの最初の実行には1分ほどかかる場合があります。その後の実行はより短時間で完了します。

4.  セル
    コマンドが完了したら、セルの下の出力を確認します。出力は次のようになります。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image31.png)

5.  出力には、2019.csvファイルのデータの行と列が表示されます。ただし、列ヘッダーが正しく表示されないことに注意してください。Dataframes
    にデータを読み込むために使用されるデフォルトのコードでは、CSVファイルの1行目に列名が含まれていると想定されていますが、このCSVファイルにはヘッダー情報のないデータのみが含まれています。

6.  コードを変更して、**header** オプションを**false**に設定します。セル内のすべてのコードを以下のコードに置き換え、クリックします。**▷
    Run cell** ボタンを押して出力を確認します

    ```
    df = spark.read.format("csv").option("header","false").load("Files/orders/2019.csv")
    # df now is a Spark DataFrame containing CSV data from "Files/orders/2019.csv".
    display(df)
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image32.png)

7.  これでDataframes
    の最初の行はデータ値として正しく含まれるようになりましたが、列名は自動生成されており、あまり役に立ちません。データを理解するには、ファイル内のデータ値に対して正しいスキーマとデータ型を明示的に定義する必要があります。

8.  セル内のすべてのコードを次のコードに置き換えてクリックします。**▷
    Run cell** ボタンを押して出力を確認します

    ```
    from pyspark.sql.types import *
    
    orderSchema = StructType([
        StructField("SalesOrderNumber", StringType()),
        StructField("SalesOrderLineNumber", IntegerType()),
        StructField("OrderDate", DateType()),
        StructField("CustomerName", StringType()),
        StructField("Email", StringType()),
        StructField("Item", StringType()),
        StructField("Quantity", IntegerType()),
        StructField("UnitPrice", FloatType()),
        StructField("Tax", FloatType())
        ])
    
    df = spark.read.format("csv").schema(orderSchema).load("Files/orders/2019.csv")
    display(df)
    ```
> ![](./media/image33.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image34.png)

9.  これで、Dataframes
    に正しい列名（各行の順序に基づいてすべてのDataframes
    に組み込む列である**インデックス**に加えて）が含まれるようになりました。列のデータ型は、セルの先頭にインポートされたSpark
    SQLライブラリで定義された標準の型セットを使用して指定されます。

10. Dataframes を表示して、変更がデータに適用されたことを確認します。

11. セル出力の下にある「+
    **Code** 」アイコンを使用してノートブックに新しいコードセルを追加し、そこに次のコードを入力します。**▷
    Run cell** ボタンを押して出力を確認します

    ```
    display(df)
    ```
> ![](./media/image35.png)

12. Dataframes
    には2019.csvファイルのデータのみが含まれています。コードを修正し、ファイルパスにワイルドカード「\*」を使用して、**orders**フォルダ内のすべてのファイルから販売注文データを読み取ってください。

13. セル出力の下にある +
    **Code** アイコンを使用してノートブックに新しいコード
    セルを追加し、そこに次のコードを入力します。

CodeCopy

    ```
    from pyspark.sql.types import *
    
    orderSchema = StructType([
        StructField("SalesOrderNumber", StringType()),
        StructField("SalesOrderLineNumber", IntegerType()),
        StructField("OrderDate", DateType()),
        StructField("CustomerName", StringType()),
        StructField("Email", StringType()),
        StructField("Item", StringType()),
        StructField("Quantity", IntegerType()),
        StructField("UnitPrice", FloatType()),
        StructField("Tax", FloatType())
        ])
    
    df = spark.read.format("csv").schema(orderSchema).load("Files/orders/*.csv")
    display(df)
    ```
> ![](./media/image36.png)

14. 変更したコード セルを実行し、出力を確認します。出力には 2019
    年、2020 年、2021 年の売上が含まれるようになります。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image37.png)

**注記**:
行のサブセットのみが表示されるため、すべての年の例を表示できない場合があります。

# 演習2: Dataframes 内のデータの探索

Dataframes
オブジェクトには、そこに含まれるデータのフィルター処理、グループ化、その他の操作に使用できるさまざまな関数が含まれています。

## タスク1: Dataframes をフィルタリングする

1.  セル出力の下にある +
    **Code** アイコンを使用して、ノートブックに新しいコード
    セルを追加し、そこに次のコードを入力します。

    ```
    customers = df['CustomerName', 'Email']
    print(customers.count())
    print(customers.distinct().count())
    display(customers.distinct())
    ```
> ![](./media/image38.png)

2.  新しいコードセルを**実行し**、結果を確認してください。以下の点に注目してください。

    - Dataframes に対して操作を実行すると、結果は新しいDataframes
      になります（この場合、**df** Dataframes
      から特定の列のサブセットを選択して、新しい顧客Dataframes
      が作成されます）。

    - Dataframes
      には、含まれるデータを要約したりフィルタリングしたりするために使用できる
      count や distinct などの関数が用意されています。

    - そのdataframe\['Field1', 'Field2',
      ...\] 構文は列のサブセットを定義するための簡潔な方法です。**select**メソッドも使用できるため、上記のコードの最初の行は次のように記述できます。customers
      = df.select("CustomerName", "Email")

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image39.png)

3.  コードを変更し、セル内のすべてのコードを次のコードに置き換えてクリックします。**▷
    Run cell** ボタンを次のようにクリックします。

    ```
    customers = df.select("CustomerName", "Email").where(df['Item']=='Road-250 Red, 52')
    print(customers.count())
    print(customers.distinct().count())
    display(customers.distinct())
    ```

4.  修正したコードを**実行して**、「**Road-250 Red,
    52**」という商品を購買した顧客を表示してください。複数の関数を「**chain**」することで、ある関数の出力が次の関数の入力となるようにできることに注意してください。この場合、selectメソッドによって作成されたデータフレームが、フィルタリング条件を適用するために使用されるwhereメソッドのソースデータフレームとなります。　

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image40.png)

## タスク2: Dataframes 内のデータを集計してグループ化する

1.  「**+
    Code**」をクリックし、以下のコードをコピーして貼り付け、「**Run
    cell**」ボタンをクリックします。

    ```
    productSales = df.select("Item", "Quantity").groupBy("Item").sum()
    display(productSales)
    ```
>
> ![](./media/image41.png)

2.  結果には、商品ごとにグループ化された注文数量の合計が表示されています。**groupBy**メソッドは行を*Item*ごとにグループ化し、その後に続くsum集計関数は残りの数値列（この場合は*Quantity*）に適用されます。

3.  「+ Code」をクリックし、以下のコードをコピーして貼り付け、「**Run
    cell**」ボタンをクリックします。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image42.png)

    ```
    from pyspark.sql.functions import *
    
    yearlySales = df.select(year("OrderDate").alias("Year")).groupBy("Year").count().orderBy("Year")
    display(yearlySales)
    ```
>
> ![](./media/image43.png)

4.  結果には年間の販売注文数が表示されています。**select**メソッドには、OrderDateフィールドの年要素を抽出するSQL
    year関数が含まれていることに注目してください（そのため、コードにはSpark
    SQLライブラリから関数をインポートするための**import**文が含まれています）。次に、**alias**メソッドを使用して、抽出された年値に列名を割り当てます。次に、データは導出された*Year*列でグループ化され、各グループの行数が計算された後、最後に**orderBy**メソッドを使用して結果のDataframes
    を並べ替えます。

# 演習3: Sparkを使用してデータファイルを変換する

データ
エンジニアの一般的なタスクは、特定の形式または構造でデータを取り込み、それをさらに下流の処理や分析のために変換することです。

## タスク 1: Dataframes のメソッドと関数を使用してデータを変換する

1.  Click on + Code and copy and paste the below code

**CodeCopy**

    ```
    from pyspark.sql.functions import *
    
    ## Create Year and Month columns
    transformed_df = df.withColumn("Year", year(col("OrderDate"))).withColumn("Month", month(col("OrderDate")))
    
    # Create the new FirstName and LastName fields
    transformed_df = transformed_df.withColumn("FirstName", split(col("CustomerName"), " ").getItem(0)).withColumn("LastName", split(col("CustomerName"), " ").getItem(1))
    
    # Filter and reorder columns
    transformed_df = transformed_df["SalesOrderNumber", "SalesOrderLineNumber", "OrderDate", "Year", "Month", "FirstName", "LastName", "Email", "Item", "Quantity", "UnitPrice", "Tax"]
    
    # Display the first five orders
    display(transformed_df.limit(5))
    ```

> ![](./media/image44.png)

2.  以下の変換を適用して、元の注文データから新しいデータフレームを作成するコードを**実行します**:

    - **OrderDate** 列に基づいて、**Year** 列と **Month**
      列を追加します。

    - **CustomerName** 列に基づいて **FirstName** 列と **LastName**
      列を追加します。

    - 列をフィルターして並べ替え、**CustomerName** 列を削除します。

> ![](./media/image45.png)

3.  出力を確認し、データに変換が行われたことを確認します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image46.png)

Spark SQL
ライブラリの全機能を活用して、行のフィルタリング、列の派生、削除、名前変更、その他の必要なデータ変更の適用などにより、データを変換できます。

**ヒント**: 参照[*Spark Dataframes
のドキュメント*](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/dataframe.html)Dataframe
オブジェクトのメソッドについて詳しく学習します。

## タスク2: 変換されたデータを保存する

1.  以下のコードを含む新しい**セルを追加し**、変換後のデータフレームをParquet形式で保存します（既にデータが存在する場合は上書きされます）。セルを**実行し**、データが保存されたことを示すメッセージが表示されるまで待ってください。

    ```
    transformed_df.write.mode("overwrite").parquet('Files/transformed_data/orders')
    print ("Transformed data saved!")
    ```
>
> **注記**:
> 一般的に、さらなる分析や分析ストアへの取り込みに使用するデータファイルには、Parquet
> 形式が好まれます。Parquet
> は非常に効率的な形式で、ほとんどの大規模データ分析システムでサポートされています。実際、データ変換の要件が、別の形式（CSV
> など）から Parquet 形式へのデータ変換だけである場合もあります。　
>
> ![](./media/image47.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image48.png)

2.  次に、左側の **Lakehouseエクスプローラー** ペインで、\[**Files** \]
    ノードの \[...\] メニューの \[**Refresh**\] を選択します。

> ![A screenshot of a computer Description automatically
> generated](./media/image49.png)

3.  **transformed_data** フォルダーをクリックして、**orders**
    という名前の新しいフォルダーが含まれており、その中に 1 つ以上の
    **Parquet ファイル**が含まれていることを確認します。

> ![A screenshot of a computer Description automatically
> generated](./media/image50.png)

4.  次のコードをクリックして、**transformed_data -\> orders**
    フォルダー内の parquet ファイルから新しいDataframes を読み込みます。

> **CodeCopy**
    ```
    orders_df = spark.read.format("parquet").load("Files/transformed_data/orders")
    display(orders_df)
    ```
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image51.png)

5.  セルを**実行し**、結果に parquet
    ファイルから読み込まれた注文データが表示されていることを確認します。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image52.png)

## タスク3: パーティションファイルにデータを保存する

1.  新しいセルを追加し、「+Code」をクリックして以下のコードを入力します。これにより、Dataframes
    が保存され、データが**年**と**月**で分割されます。セルを実行し、データが保存されたことを示すメッセージが表示されるまで待ちます。

    ```
    orders_df.write.partitionBy("Year","Month").mode("overwrite").parquet("Files/partitioned_data")
    print ("Transformed data saved!")
    ```
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image53.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image54.png)

2.  次に、左側の **Lakehouse explorer** ペインで、\[Files\] ノードの
    \[...\] メニューの \[**Refresh**\] を選択します。

![A screenshot of a computer Description automatically
generated](./media/image55.png)

3.  「**partitioned_orders**」フォルダを展開し、「**Year=xxxx**」という名前のフォルダが階層構造になっていることを確認します。各フォルダには「**Month=xxxx**」という名前のフォルダが含まれています。各月フォルダには、その月の注文情報を含むParquetファイルが含まれています。

![A screenshot of a computer Description automatically
generated](./media/image56.png)

![A screenshot of a computer Description automatically
generated](./media/image57.png)

> データファイルのパーティション分割は、大量のデータを扱う際にパフォーマンスを最適化するための一般的な方法です。この手法により、パフォーマンスが大幅に向上し、データのフィルタリングが容易になります。

4.  新しいセルを追加し、次のコードを含む **+
    Code**をクリックして、**orders.parquet**
    ファイルから新しいDataframes を読み込みます。

    ```
    orders_2021_df = spark.read.format("parquet").load("Files/partitioned_data/Year=2021/Month=*")
    display(orders_2021_df)
    ```

5.  セルを**実行し**、結果に2021年の売上に関する注文データが表示されていることを確認してください。パスで指定されたパーティショニング列（**年と月**）はデータフレームに含まれていないことに注意してください。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image58.png)

# **演習3: テーブルとSQLの操作**

ご覧いただいたように、データフレームオブジェクトのネイティブメソッドを使えば、ファイルからデータを効率的にクエリして分析できます。しかし、多くのデータアナリストは、SQL構文を使ってクエリできるテーブルを扱う方が慣れています。Sparkは、リレーショナルテーブルを定義できる*メタストア*を提供しています。データフレームオブジェクトを提供するSpark
SQLライブラリは、メタストア内のテーブルに対するSQLステートメントによるクエリもサポートしています。Sparkのこれらの機能を利用することで、データレイクの柔軟性と、リレーショナルデータウェアハウスの構造化データスキーマおよびSQLベースのクエリを組み合わせることができます。これが「data
lakehouse」という用語の由来です。

## タスク1: マネージドテーブルを作成する 

Spark メタストア内のテーブルは、データ
レイク内のファイルに対するリレーショナル抽象化です。テーブルは**管理対象**
(この場合、ファイルはメタストアによって管理されます) または**外部**
(この場合、テーブルはメタストアとは独立して管理されるdata
lake内のファイルの場所を参照する) になります。

1.  新しいコードを追加し、 ノートブックの **+ Code**
    セルをクリックし、次のコードを入力します。これにより、販売注文データのDataframes
    が salesorders という名前のテーブルとして保存されます。

    ```
    # Create a new table
    df.write.format("delta").saveAsTable("salesorders")
    
    # Get the table description
    spark.sql("DESCRIBE EXTENDED salesorders").show(truncate=False)
    ```

**注記**この例にはいくつか注目すべき点があります。まず、明示的なパスが指定されていないため、テーブルのファイルはメタストアによって管理されます。次に、テーブルは**デルタ**形式で保存されます。テーブルは複数のファイル形式（CSV、Parquet、Avroなど）に基づいて作成できますが、*delta
lake* は、トランザクションのサポート、行のバージョン管理、その他の便利な機能を含むリレーショナルデータベース機能をテーブルに追加するSparkテクノロジーです。Fabricのdata
Lakehouseでは、デルタ形式でのテーブル作成が推奨されます。　

2.  コード セルを実行して、新しいテーブルの定義を示す出力を確認します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image59.png)

3.  **Lakehouse** **explorer** ペインの \[**Tables** \] フォルダーの
    \[…\] メニューで、\[**Refresh**\] を選択します。

![A screenshot of a computer Description automatically
generated](./media/image60.png)

4.  次に、\[**Tables** \] ノードを展開し、**salesorders** テーブルが
    **dbo** スキーマの下に作成されていることを確認します。

> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

5.  **salesorders**テーブルの横にマウスを移動し、水平の省略記号（…）をクリックします。「**Load
    data**」をクリックし、「**Spark**」を選択します。

> ![](./media/image62.png)

6.  **▷ Run cell** ボタンをクリックすると、Spark
    SQLライブラリを使用してPySparkコード内にsalesorderテーブルに対するSQLクエリが埋め込まれ、クエリの結果がデータフレームに読み込まれます。

    ```
    df = spark.sql("SELECT * FROM [your_lakehouse].salesorders LIMIT 1000")
    display(df)
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image63.png)

## タスク2: 外部テーブルを作成する

また、スキーマメタデータはレイクハウスのメタストアに定義されているものの、データファイルは外部の場所に保存されている*外部*テーブルを作成することもできます。

1.  最初のコードセルから返された結果の下にある「**+Code**」ボタンを使用して、新しいコードセルを追加します（まだコードセルが存在しない場合）。新しいセルに次のコードを入力します。

CodeCopy

    ```
    df.write.format("delta").saveAsTable("external_salesorder", path="<abfs_path>/external_salesorder")
    ```

![A screenshot of a computer Description automatically
generated](./media/image64.png)

2.  **Lakehouse エクスプローラー** ペインの \[**Files**\] フォルダーの
    \[…\] メニューで、メモ帳で \[**Copy ABFS path**\] を選択します。

> ABFS パスは、Lakehouseの OneLake ストレージ内の **Files**
> フォルダーへの完全修飾パスです。次のようになります。

abfss://dp_Fabric29@onelake.dfs.fabric.microsoft.com/Fabric_lakehouse.Lakehouse/Files/external_salesorder

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image65.png)

3.  コードセルに移動し、\<**abfs_path**\>
    をメモ帳にコピーした**パス**に置き換えます。これにより、Dataframes
    が外部テーブルとして保存され、Files フォルダ内の external_salesorder
    というフォルダにデータファイルが保存されます。完全なパスは次のようになります。

abfss:// dp_Fabric29@onelake.dfs.fabric.microsoft.com
/Fabric_lakehouse.Lakehouse/Files/external_salesorder

4.  セルの左側にある**▷（セルを実行）**ボタンをクリックして実行します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image66.png)

5.  **Lakehouse explorer** ペインの \[**Tables** \] フォルダーの \[...\]
    メニューで、\[**Refresh**\] を選択します。

![A screenshot of a computer Description automatically
generated](./media/image67.png)

6.  次に、**テーブル** ノードを展開し、**external_salesorder**
    テーブルが作成されたことを確認します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image68.png)

7.  **Lakehouse エクスプローラー** ペインの **Files** フォルダーの …
    メニューで、\[Refresh\] を選択します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image69.png)

8.  次に、\[**Files**\] ノードを展開し、テーブルのデータ ファイル用に
    **external_salesorder** フォルダーが作成されていることを確認します。

![](./media/image70.png)

## タスク 3: マネージドテーブルと外部テーブルを比較する

マネージドテーブルと外部テーブルの違いを見てみましょう。

1.  コードセルから返された結果の下にある「**+Code**」ボタンを使用して新しいコードセルを追加します。以下のコードをコードセルにコピーし、セルの左側にある**▷（セルを実行）**ボタンをクリックして実行します。

    ```
    %%sql
    
    DESCRIBE FORMATTED salesorders;
    ```
> ![](./media/image71.png)

2.  結果で、テーブルの **Location**
    プロパティを確認します。これは、**/Tables/salesorders**
    で終わるLakehouseの OneLake ストレージへのパスである必要があります
    (完全なパスを表示するには、**データ型**の列を広げる必要がある場合があります)。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image72.png)

3.  **DESCRIBE**コマンドを修正して、ここに示されているようにexternal_saleorderテーブルの詳細を表示します。

4.  コードセルから返された結果の下にある「**+Code**」ボタンを使用して新しいコードセルを追加します。以下のコードをコピーして、セルの左側にある**▷（セルを実行）**ボタンをクリックして実行します。

    ```
    %%sql
    
    DESCRIBE FORMATTED external_salesorder;
    ```

5.  結果で、テーブルの **Location**
    プロパティを確認します。これは、**/Files/external_saleorder**
    で終わるLakehouseの OneLake ストレージへのパスである必要があります
    (完全なパスを表示するには、**Data
    type** 列を広げる必要がある場合があります)。　

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image73.png)

## タスク4: セル内でSQLコードを実行する

PySpark コードを含むセルに SQL
ステートメントを埋め込むことができると便利ですが、データ アナリストは
SQL で直接作業したい場合がよくあります。

1.  ノートブックの「**+Code**」セルをクリックし、次のコードを入力します。**▷
    Run cell**
    ボタンをクリックして、結果を確認します。以下の点に注意してください。　

    - その%%sqlセルの先頭の行 (*magic*と呼ばれる)
      は、このセル内のコードを実行するために PySpark ではなく Spark SQL
      言語ランタイムを使用する必要があることを示します。

    - SQL コードは、以前に作成した **salesorders**
      テーブルを参照します。

    - SQLクエリの出力は、セルの下に結果として自動的に表示されます。

      ```
      %%sql
      SELECT YEAR(OrderDate) AS OrderYear,
             SUM((UnitPrice * Quantity) + Tax) AS GrossRevenue
      FROM salesorders
      GROUP BY YEAR(OrderDate)
      ORDER BY OrderYear;
      ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image74.png)

**注記**: Spark SQLとDataframes の詳細については、[*Spark SQL
documentation*](https://spark.apache.org/docs/2.2.0/sql-programming-guide.html)を参考してください

# 演習4: Sparkでデータを視覚化する

諺にもあるように、一枚の写真は千の言葉に値し、グラフは千行のデータよりも優れている場合が多いです。Fabricのノートブックには、Dataframes
またはSpark
SQLクエリから表示されるデータ用のグラフビューが組み込まれていますが、包括的なグラフ作成には設計されていません。ただし、**matplotlib**や**seaborn**などのPythonグラフィックライブラリを使用すれば、Dataframes
内のデータからグラフを作成できます。

## タスク1: 結果をグラフとして表示する

1.  ノートブックの「+Code」セルをクリックし、次のコードを入力します。**▷
    Run cell** ボタンをクリックし、以前に作成した **salesorders**
    ビューからデータが返されることを確認します。　

    ```
    %%sql
    SELECT * FROM salesorders
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image75.png)

2.  セルの下の結果セクションで、\[**View** \] オプションを
    \[**Table** \] から \[**+New chart**\] に変更します。

![](./media/image76.png)

3.  チャートの右上にある「**Start
    editing** 」ボタンをクリックすると、チャートのオプションパネルが表示されます。以下のオプションを設定し、「**Apply**」を選択します。

    - **Chart type**: Bar chart

    - **Key**: Item

    - **Values**: Quantity

    - **Series Group**: 空白のままにする

    - **Aggregation**: Sum

    - **Stacked**: 未選択

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image77.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image78.png)

4.  チャートが次のようになっていることを確認します

> ![](./media/image79.png)

## タスク2: matplotlibを使い始める

1.  「**+Code**」をクリックし、以下のコードをコピー＆ペーストしてください。コードを**実行する**と、年間収益を含むSparkDataframes
    が返されることがわかります。

    ```
    sqlQuery = "SELECT CAST(YEAR(OrderDate) AS CHAR(4)) AS OrderYear, \
                    SUM((UnitPrice * Quantity) + Tax) AS GrossRevenue \
                FROM salesorders \
                GROUP BY CAST(YEAR(OrderDate) AS CHAR(4)) \
                ORDER BY OrderYear"
    df_spark = spark.sql(sqlQuery)
    df_spark.show()
    ```
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image80.png)

2.  データをグラフとして視覚化するために、まずは**matplotlib**
    Pythonライブラリを使用します。このライブラリは、他の多くのプロットライブラリのベースとなっているコアライブラリであり、グラフ作成において非常に柔軟な機能を提供します。

3.  「**+ Code**」をクリックし、以下のコードをコピーして貼り付けます。

**CodeCopy**

    ```
    from matplotlib import pyplot as plt
    
    # matplotlib requires a Pandas dataframe, not a Spark one
    df_sales = df_spark.toPandas()
    
    # Create a bar plot of revenue by year
    plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'])
    
    # Display the plot
    plt.show()
    ```
![A screenshot of a computer Description automatically
generated](./media/image81.png)

5.  「**セルを実行**」ボタンをクリックして結果を確認します。結果は、各年の総売上高を示す縦棒グラフで構成されています。このグラフを作成するために使用されたコードの以下の特徴に注意してください。

    - **matplotlib** ライブラリには *Pandas* Dataframes
      が必要なので、Spark SQL クエリによって返される *Spark* Dataframes
      をこの形式に変換する必要があります。

    - **matplotlib** ライブラリの中核は **pyplot**
      オブジェクトです。これはほとんどのプロット機能の基盤となります。

    - デフォルト設定でも使えるチャートが出来上がりますが、カスタマイズの余地はかなりあります。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image82.png)

6.  次のようにコードを変更してグラフをプロットし、セル内のすべてのコードを次のコードに置き換えてクリックします。**▷
    Run cell** ボタンを押して出力を確認します

    ```
    from matplotlib import pyplot as plt
    
    # Clear the plot area
    plt.clf()
    
    # Create a bar plot of revenue by year
    plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')
    
    # Customize the chart
    plt.title('Revenue by Year')
    plt.xlabel('Year')
    plt.ylabel('Revenue')
    plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
    plt.xticks(rotation=45)
    
    # Show the figure
    plt.show()
    ```
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image83.png)
>
> ![A graph with orange bars AI-generated content may be
> incorrect.](./media/image84.png)

7.  グラフに少し情報が追加されました。プロットは技術的には**Figure**に含まれています。前の例では、図は暗黙的に作成されていましたが、明示的に作成することもできます。

8.  次のようにグラフをプロットするコードを変更し、セル内のすべてのコードを次のコードに置き換えます。

    ```
    from matplotlib import pyplot as plt
    
    # Clear the plot area
    plt.clf()
    
    # Create a Figure
    fig = plt.figure(figsize=(8,3))
    
    # Create a bar plot of revenue by year
    plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')
    
    # Customize the chart
    plt.title('Revenue by Year')
    plt.xlabel('Year')
    plt.ylabel('Revenue')
    plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
    plt.xticks(rotation=45)
    
    # Show the figure
    plt.show()
    ```
9.  コードセルを**再実行して、**結果を確認します。図によってプロットの形状とサイズが決まります。　

> 一つの図の中に、それぞれ独自の*軸*を持つ複数のサブプロットを含めることができます。
>
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image85.png)
>
> ![A screenshot of a graph AI-generated content may be
> incorrect.](./media/image86.png)

10. コードを以下のように修正してグラフをプロットします。コードセルを**再実行し**、結果を確認します。図には、コードで指定したサブプロットが含まれています。

    ```
    from matplotlib import pyplot as plt
    
    # Clear the plot area
    plt.clf()
    
    # Create a figure for 2 subplots (1 row, 2 columns)
    fig, ax = plt.subplots(1, 2, figsize = (10,4))
    
    # Create a bar plot of revenue by year on the first axis
    ax[0].bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')
    ax[0].set_title('Revenue by Year')
    
    # Create a pie chart of yearly order counts on the second axis
    yearly_counts = df_sales['OrderYear'].value_counts()
    ax[1].pie(yearly_counts)
    ax[1].set_title('Orders per Year')
    ax[1].legend(yearly_counts.keys().tolist())
    
    # Add a title to the Figure
    fig.suptitle('Sales Data')
    
    # Show the figure
    plt.show()
    ```
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image87.png)
>
> ![A screenshot of a computer screen AI-generated content may be
> incorrect.](./media/image88.png)

**注記**: matplotlibを使ったプロットの詳細については、 [*matplotlib
documentation*](https://matplotlib.org/)を参考してください。

## タスク3: seabornライブラリを使用する

**matplotlib**
は様々な種類の複雑なグラフを作成できますが、最適な結果を得るには複雑なコードが必要になる場合があります。そのため、長年にわたり、matplotlib
をベースに多くの新しいライブラリが開発され、その複雑さを抽象化し、機能を強化してきました。そのようなライブラリの一つが
**seaborn** です。

1.  「Click on **+ Code** and copy and paste the below code.

    ```
    import seaborn as sns
    
    # Clear the plot area
    plt.clf()
    
    # Create a bar chart
    ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
    plt.show()
    ```

2.  コードを実行し、seaborn
    ライブラリを使用して、棒グラフが表示されることを確認します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image89.png)

3.  コードを以下のように**修正します**。修正したコードを**実行する**と、Seaborn
    によってプロットに一貫したカラーテーマを設定できることがわかります。

    ```
    import seaborn as sns
    
    # Clear the plot area
    plt.clf()
    
    # Set the visual theme for seaborn
    sns.set_theme(style="whitegrid")
    
    # Create a bar chart
    ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
    plt.show()
    ```
> ![A screenshot of a graph AI-generated content may be
> incorrect.](./media/image90.png)

4.  コードを以下のように**修正してください**。修正したコードを**実行する**と、年間収益が折れ線グラフで表示されます。

    ```
    import seaborn as sns
    
    # Clear the plot area
    plt.clf()
    
    # Create a bar chart
    ax = sns.lineplot(x="OrderYear", y="GrossRevenue", data=df_sales)
    plt.show()
    ```
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image91.png)

**注記**: seabornを使ったプロットの詳細については、[*seaborn
documentation*](https://seaborn.pydata.org/index.html)を参考してください。

## タスク4: ストリーミングデータにデルタテーブルを使用する

Delta
Lakeはストリーミングデータをサポートしています。Deltaテーブルは、Spark
Structured Streaming
APIを使用して作成されたデータストリームの*シンク*または*ソース*として使用できます。この例では、internet
of
things（IoT）のシミュレーションシナリオにおいて、Deltaテーブルをストリーミングデータのシンクとして使用します。

1.  「**+
    Code**」をクリックし、以下のコードをコピーして貼り付け、「セルの実行」ボタンをクリックします。

CodeCopy

    ```
    from notebookutils import mssparkutils
    from pyspark.sql.types import *
    from pyspark.sql.functions import *
    
    # Create a folder
    inputPath = 'Files/data/'
    mssparkutils.fs.mkdirs(inputPath)
    
    # Create a stream that reads data from the folder, using a JSON schema
    jsonSchema = StructType([
    StructField("device", StringType(), False),
    StructField("status", StringType(), False)
    ])
    iotstream = spark.readStream.schema(jsonSchema).option("maxFilesPerTrigger", 1).json(inputPath)
    
    # Write some event data to the folder
    device_data = '''{"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev2","status":"error"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"error"}
    {"device":"Dev2","status":"ok"}
    {"device":"Dev2","status":"error"}
    {"device":"Dev1","status":"ok"}'''
    mssparkutils.fs.put(inputPath + "data.txt", device_data, True)
    print("Source stream created...")
    ```
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image92.png)
>
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image93.png)

2.  「***Source stream
    created…*** 」というメッセージが表示されていることを確認してください。実行したコードにより、仮想IoTデバイスからの読み取りデータが格納されているフォルダーに基づいて、ストリーミングデータソースが作成されました。　

3.  「**+
    Code**」をクリックし、以下のコードをコピーして貼り付け、「**セルの実行**」ボタンをクリックします。

CodeCopy

    ```
    # Write the stream to a delta table
    delta_stream_table_path = 'Tables/iotdevicedata'
    checkpointpath = 'Files/delta/checkpoint'
    deltastream = iotstream.writeStream.format("delta").option("checkpointLocation", checkpointpath).start(delta_stream_table_path)
    print("Streaming to delta sink...")
    ```
> ![](./media/image94.png)

4.  このコードは、ストリーミングデバイスのデータを差分形式で**iotdevicedata**というフォルダに書き込みます。フォルダのパスは**Tables**フォルダ内にあるため、自動的にテーブルが作成されます。テーブルの横にある水平の省略記号をクリックし、「**Refresh**」をクリックしてください。

![](./media/image95.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image96.png)

5.  「**+
    Code**」をクリックし、以下のコードをコピーして貼り付け、「**セルの実行**」ボタンをクリックします。

    ```
    %%sql
    
    SELECT * FROM IotDeviceData;
    ```
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image97.png)

6.  このコードは、ストリーミング ソースからのデバイス データが含まれる
    **IotDeviceData** テーブルをクエリします。

7.  「**+
    Code**」をクリックし、以下のコードをコピーして貼り付け、「**セルの実行**」ボタンをクリックします。

    ```
    # Add more data to the source stream
    more_data = '''{"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"error"}
    {"device":"Dev2","status":"error"}
    {"device":"Dev1","status":"ok"}'''
    
    mssparkutils.fs.put(inputPath + "more-data.txt", more_data, True)
    ```
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image98.png)

8.  このコードは、ストリーミング ソースにさらに多くの仮想デバイス
    データを書き込みます。

9.  「**+ Code**」をクリックし、以下のコードをコピーして貼り付け、「**セルの実行**」ボタンをクリックします。

    ```
    %%sql
    
    SELECT * FROM IotDeviceData;
    ```
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image99.png)

10. このコードは **IotDeviceData**
    テーブルを再度クエリし、ストリーミング
    ソースに追加された追加データが含まれるようになります。

11. 「**+ Code**」をクリックし、以下のコードをコピーして貼り付け、「**セルの実行**」ボタンをクリックします。

    ```
    deltastream.stop()
    ```
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image100.png)

12. このコードはストリームを停止します。

## タスク5: ノートブックを保存してSparkセッションを終了する

データの操作が完了したら、ノートブックをわかりやすい名前で保存し、Spark
セッションを終了できます。

1.  ノートブックのメニューバーで、ノートブックの設定を表示するための⚙️
    **Settings** アイコンを使用します。

![A screenshot of a computer Description automatically
generated](./media/image101.png)

2.  ノートブックの**Name** を +++**Explore Sales Orders**+++
    に設定し、設定ペインを閉じます。

![A screenshot of a computer Description automatically
generated](./media/image102.png)

3.  ノートブック メニューで \[**Stop session**\] を選択して、Spark
    セッションを終了します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image103.png)

![A screenshot of a computer Description automatically
generated](./media/image104.png)

# 演習 5: Microsoft Fabric でデータフロー (Gen2) を作成する

Microsoft Fabric では、Dataflows (Gen2)
が様々なデータソースに接続し、Power Query Online
で変換を実行します。その後、データ
パイプラインでデータフローを使用して、Lakehouseやその他の分析ストアにデータを取り込み、Power
BI レポート用のデータセットを定義することができます。　

この演習は、Dataflows（Gen2）のさまざまな要素を紹介することを目的としており、企業に存在する可能性のある複雑なソリューションを作成するものではありません。　

## タスク 1: データを取り込むためのデータフロー (Gen2) を作成する

Lakehouseが完成したら、そこにデータを取り込む必要があります。その方法の一つは、*extract、transform及びload* （ETL）プロセスをカプセル化するデータフローを定義することです。

1.  次に、左側のナビゲーション ペインで **Fabric_lakehouse**
    をクリックします。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image105.png)

2.  **Fabric_lakehouse**のホームページでは、**Get
    data**ドロップダウン矢印をクリックし、「**New Dataflow
    Gen2**」を選択します。新しいデータフローのPower
    Queryエディターが開きます。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image106.png)

5.  \[**New Dataflow Gen2**\] ダイアログ ボックスで、\[**Name** \]
    フィールドに+++ **Gen2_Dataflow** +++」と入力し、\[**Create** \]
    ボタンをクリックして、New Dataflow Gen2 を開きます。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image107.png)

3.  \[**Home**\] タブの **Power Query** ペインで、\[**Import from a
    Text/CSV file**\] をクリックします。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image108.png)

4.  **Connect to data source**ペインの**Connection settings**で、**Link
    to file (Preview)**ラジオボタンを選択します。

- **Link to file**: 選択済み

- **File path or
  URL**: +++https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/orders.csv+++

![](./media/image109.png)

5.  \[**Connect to data source**\] ペインの \[**Connection
    credentials**\] で、次の詳細を入力し、\[**Next**\]
    ボタンをクリックします。

- **Connection**: Create new connection

- **Connection name**: Orders

- **data gateway**: (none)

- **Authentication kind**: Anonymous

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image110.png)

6.  **Preview file data**ペインで、\[**Create** \] をクリックしてデータ
    ソースを作成します。![A screenshot of a computer Description
    automatically generated](./media/image111.png)

7.  **Power Query** エディターには、データ
    ソースと、データをフォーマットするためのクエリ手順の初期セットが表示されます。

![](./media/image112.png)

8.  ツールバーのリボンで「**Add
    column** 」タブを選択します。次に、「**Custom
    column**」を選択します。

> ![](./media/image113.png)

9.  新しい列名を+++**MonthNo+++**と設定し、Data typeを**Whole
    Number**に設定し、次の数式を「**Custom column
    formula**」の下に追加します：+++**Date.Month(\[OrderDate\])+++**。「OK」を選択します。　

> ![](./media/image114.png)

10. カスタム列を追加する手順がクエリに追加されていることに確認できます。結果の列はデータペインに表示されます。

> ![A screenshot of a computer Description automatically
> generated](./media/image115.png)

**ヒント：**右側の「Query Settings」ペインでは、「**Applied
Steps** 」に各変換ステップが含まれていることを確認できます。また、下部にある「**Diagram
flow** 」ボタンをトグルすることで、ステップのビジュアルダイアグラムを表示できます。

ステップは上下に移動したり、歯車アイコンを選択して編集したりすることができ、各ステップを選択してプレビュー
ペインで変換の適用を確認することもできます。

タスク 2: Dataflow のデータ送信先を追加する

1.  **Power Query**
    ツールバーのリボンで、「**Home** 」タブを選択します。次に、「D**ata
    destination** 」ドロップダウンメニューで「**Lakehouse**」を選択します（まだ選択されていない場合）。

![](./media/image116.png)

![](./media/image117.png)

**注記：**このオプションがグレー表示になっている場合は、既にデータの保存先が設定されている可能性があります。Power
Query エディターの右側にあるQuery
settingsペインの下部で、データの保存先を確認してください。既に保存先が設定されている場合は、歯車アイコンを使って変更できます。

2.  **Lakehouse** の宛先は、Power Query
    エディターの**クエリ**内に**アイコン**として示されます。

![A screenshot of a computer Description automatically
generated](./media/image118.png)

![A screenshot of a computer Description automatically
generated](./media/image119.png)

3.  ホームウィンドウで、「**Save & run**」を選択し、「**Save &
    run**」ボタンをクリックします。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image120.png)

4.  左側のナビゲーションメニューで、下の画像に示すように、**dp_Fabric-XXXXXワークスペースアイコン**を選択します。![](./media/image121.png)

## タスク 3: パイプラインにデータフローを追加する

データフローをパイプラインのアクティビティとして含めることができます。パイプラインは、データの取り込みと処理のアクティビティをオーケストレーションするために使用され、データフローを他の種類の操作と組み合わせた単一のスケジュールされたプロセスを実現できます。パイプラインは、Data
Factory
エクスペリエンスを含むいくつかの異なるエクスペリエンスで作成できます。

1.  Synapse Data Engineering のHomeページの **dp_FabricXX**
    ペインで、**+New item** -\> P**ipeline**を選択します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image122.png)

2.  **New pipeline** ダイアログボックスで、**Name**フィールドに+++**Load
    data+++**と入力します。**Create**ボタンをクリック新しいパイプラインを開きます。
    　

![A screenshot of a computer Description automatically
generated](./media/image123.png)

3.  パイプライン エディターが開きます。

> ![A screenshot of a computer Description automatically
> generated](./media/image124.png)
>
> **ヒント**: Copy
> Dataウィザードが自動的に開いた場合は、閉じてください。

4.  **Pipeline
    activity**を選択し、パイプラインに**Dataflow** アクティビティを追加します。

![A screenshot of a computer Description automatically
generated](./media/image125.png)

5.  新しい
    **Dataflow1** アクティビティを選択した状態で、\[**Settings**\]
    タブの \[**Dataflow** \] ドロップダウン リストで、**Gen2_Dataflow**
    (以前に作成したデータフロー) を選択します。

![A screenshot of a computer Description automatically
generated](./media/image126.png)

6.  **Home** タブで、**🖫（*保存*）**アイコンを使用して、パイプラインを保存します。

![A screenshot of a computer Description automatically
generated](./media/image127.png)

7.  **▷ Run** ボタンをクリックして、パイプラインを実行し、完了するまでお待ちください。数分かかる場合があります。

> ![A screenshot of a computer Description automatically
> generated](./media/image128.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image129.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image130.png)

8.  上部のバーから、**Fabric_lakehouse** タブを選択します。

> ![A screenshot of a computer Description automatically
> generated](./media/image131.png)

9.  **Explorer**ペインで、「**Tables**」の「…」メニューを選択し、「**refresh**」を選択します。次に「**Tables** 」を展開し、データフローによって作成された「**orders**」テーブルを選択します。

![A screenshot of a computer Description automatically
generated](./media/image132.png)

![](./media/image133.png)

**ヒント**:Power BI Desktop *Dataflows
connector* を使用して、データフローで行われたデータ変換に直接接続します。

追加の変換を行って新しいデータセットとして公開し、特殊なデータセットの対象ユーザーに配布することもできます。

## タスク4: リソースをクリーンアップする

この演習では、Spark を使用して Microsoft Fabric
のデータを操作する方法を学習しました。

Lakehouseの探索が終了したら、この演習用に作成したワークスペースを削除できます。

1.  左側のバーで、ワークスペースのアイコンを選択すると、そこに含まれるすべてのアイテムが表示されます。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image134.png)

2.  ツールバーの … メニューで、**Workspace settings**を選択します。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image135.png)

3.  \[**General**\] を選択し、\[**Remove this workspace**\]
    をクリックします。

![A screenshot of a computer settings Description automatically
generated](./media/image136.png)

4.  \[**Delete workspace?**\] ダイアログ ボックスで、\[**Delete**\]
    ボタンをクリックします。

> ![A screenshot of a computer Description automatically
> generated](./media/image137.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image138.png)

**まとめ**

これ使用事例Power BI 内で Microsoft Fabric
を操作するプロセスをガイドします。ワークスペースの設定、Lakehouseの作成、データファイルのアップロードと管理、ノートブックを使ったデータ探索など、様々なタスクを網羅しています。参加者は、PySpark
を使ったデータの操作と変換、視覚化の作成、効率的なクエリ実行のためのデータの保存とパーティション分割の方法を学びます。

このユースケースでは、参加者はMicrosoft
Fabricにおけるデルタテーブルの操作に焦点を当てた一連のタスクに取り組みます。タスクには、データのアップロードと探索、マネージドデルタテーブルと外部デルタテーブルの作成、それらのプロパティの比較などが含まれます。この演習では、構造化データを管理するためのSQL機能を紹介し、matplotlibやseabornといったPythonライブラリを使用したデータ視覚化に関する知見を提供します。これらの演習を通して、Microsoft
Fabricをデータ分析に活用する方法、およびIoT環境におけるストリーミングデータにデルタテーブルを組み込む方法について、包括的な理解を深めることを目指します。

このユースケースでは、Fabric ワークスペースの設定、data
lakehouseの作成、分析用データの取り込みのプロセスを解説します。ETL
処理を処理するデータフローの定義方法と、変換されたデータの保存先となるデータデスティネーションの設定方法も紹介します。さらに、データフローをパイプラインに統合して自動処理を実現する方法も学習します。最後に、演習完了後にリソースをクリーンアップする手順についても説明します。

このラボでは、Fabric
を使用するために必要なスキルを習得し、ワークスペースの作成と管理、data
lakehouseの構築、そして効率的なデータ変換を行えるようになります。データフローをパイプラインに組み込むことで、データ処理タスクを自動化し、ワークフローを効率化し、実際のシナリオにおける生産性を向上させる方法を習得できます。クリーンアップ手順に従うことで、不要なリソースを残さず、整理された効率的なワークスペース管理アプローチを実現できます。
