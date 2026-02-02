# 用例04：用Apache Spark分析數據

**介紹**

Apache Spark
是一個開源的分布式數據處理引擎，廣泛用於探索、處理和分析數據湖存儲中的海量數據。Spark
作為處理選項在許多數據平臺產品中提供，包括 Azure HDInsight、Azure
Databricks、Azure Synapse Analytics 和 Microsoft Fabric。Spark
的一個優勢是支持多種編程語言，包括 Java、Scala、Python 和
SQL;這使得Spark成為數據處理工作負載的非常靈活解決方案，包括數據清理與作、統計分析與機器學習，以及數據分析與可視化。

Microsoft Fabric lakehouse 中的表基於開源的 Apache Spark *Delta Lake*
格式。Delta Lake 增加了對批處理和流數據作的關係語義支持，並支持創建
Lakehouse 架構，使 Apache Spark
能夠處理和查詢基於數據湖底層文件的表中的數據。

在 Microsoft Fabric 中，Dataflows（Gen2）連接多個數據源，並在 Power
Query Online 中執行轉換。然後它們可以在數據管道中用於將數據導入
lakehouse 或其他分析存儲，或定義 Power BI 報告中的數據集。

本實驗室旨在介紹
Dataflows（Gen2）的不同元素，而非創建企業中可能存在的複雜解決方案。

**目的：**

- 在 Microsoft Fabric 中創建一個工作區，並啟用 Fabric 試用。

- 建立 lakehouse 環境並上傳數據文件進行分析。

- 生成一本用於交互式數據探索和分析的筆記本。

- 將數據加載到數據幀中以便進一步處理和可視化。

- 用 PySpark 對數據進行轉換。

- 保存並分區轉換後的數據，以便優化查詢。

- 在 Spark 元存儲庫中創建一個用於結構化數據管理的表

- 將DataFrame保存為一個名為“salesorders”的管理級delta表。

- 將DataFrame保存為名為“external_salesorder”的外部delta表，並指定路徑。

- 描述並比較託管表和外部表的屬性。

- 對表執行SQL查詢以進行分析和報告。

- 使用如 matplotlib 和 seaborn 等 Python 庫來可視化數據。

- 在數據工程體驗中建立數據 lakehouse，並導入相關數據以便後續分析。

- 定義一個數據流，用於提取、轉換和加載數據到 lakehouse。

- 在 Power Query 中配置數據目的地，將轉換後的數據存儲在 lakehouse 中。

- 將數據流整合進流水線，以實現定時的數據處理和攝取。

- 移除工作區及相關元素以結束練習。

# 練習1：創建一個工作區、lakehouse、筆記本，並將數據加載到數據框架中 

## 任務1：創建一個工作區 

在處理Fabric數據之前，先創建一個啟用Fabric試用區的工作區。

1.  打開瀏覽器，進入地址欄，輸入或粘貼以下URL：+++https://app.fabric.microsoft.com/+++
    ，然後按下 **Enter** 鍵。

> **Note**：如果你被引導到Microsoft Fabric主頁，可以跳過#2到#4的步驟。
>
> ![](./media/image1.png)

2.  在 **Microsoft Fabric** 窗口中，輸入你的憑證，然後點擊 **Submit**
    按鈕。

> ![](./media/image2.png)

3.  然後，在 **Microsoft** 窗口輸入密碼，點擊 **Sign in** 按鈕**。**

> ![A login screen with a red box and blue text Description
> automatically generated](./media/image3.png)

4.  在 **Stay signed in?** 窗口，點擊“**Yes**”按鈕。

> ![A screenshot of a computer error Description automatically
> generated](./media/image4.png)

5.  Fabric 主頁，選擇 **+New workspace** 瓷磚。

> ![A screenshot of a computer Description automatically
> generated](./media/image5.png)

6.  在“**Create a
    workspace”標簽**中，輸入以下信息，點擊“**Apply**”按鈕。

[TABLE]

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image6.png)
>
> ![](./media/image7.png)

7.  等待部署完成。完成大約需要2-3分鐘。
    當你的新工作區開放時，應該是空的。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

## 任務2：創建 lakehouse 並上傳文件

現在你有了工作區，就該切換到門戶中*的數據工程*體驗，為你要分析的數據文件創建一個數據
lakehouse。

1.  點擊導航欄中的**+New item** 按鈕，創建新的活動屋。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image9.png)

2.  點擊“**Lakehouse**”瓷磚。

![A screenshot of a computer Description automatically
generated](./media/image10.png)

3.  在“**New lakehouse** ”對話框中，輸入“**Name**”欄的
    **+++Fabric_lakehouse+++** ，點擊“**Create**”按鈕，打開新lakehouse。

![A screenshot of a computer Description automatically
generated](./media/image11.png)

4.  大約一分鐘後，新的空 lakehouse
    會被創造出來。你需要把一些數據導入數據 lakehouse 進行分析。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image12.png)

5.  你會看到一條通知，提示 **Successfully created SQL endpoint**。

![](./media/image13.png)

6.  在 **Explorer** 部分，**fabric_lakehouse**下方，將鼠標懸停在 **Files
    folder**
    旁邊，然後點擊水平省略號**（...）**菜單。點擊“**Upload**”，然後點擊“**Upload
    folder**”，如下圖所示。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image14.png)

7.  在右側的“**Upload folder**”面板上，選擇 **Files/**
    下的**文件夾圖標**，然後瀏覽到
    **C：\LabFiles**，再選擇**orders**文件夾，點擊 **Upload** 按鈕。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image15.png)

8.  如果是，**Upload 3 files to this site?** 對話框出現，然後點擊
    **Upload** 按鈕。

![](./media/image16.png)

9.  在“Upload”文件夾面板中，點擊 **“Upload**”按鈕。

> ![](./media/image17.png)

10. 文件上傳後 **關閉 Upload folder** 面板。

![A screenshot of a computer Description automatically
generated](./media/image18.png)

11. 展開 **Files** ，選擇 **orders ** 文件夾，並確認CSV文件已上傳。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image19.png)

## 任務3：製作一本筆記本

要在 Apache Spark
中處理數據，你可以創建一個*筆記本*。筆記本提供了一個互動環境，你可以編寫和運行多種語言的代碼，並添加筆記來記錄代碼。

1.  在**主**頁查看 datalake 中 **orders** 文件夾內容時，在 **Open
    notebook** 菜單中選擇 **New notebook**。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image20.png)

2.  幾秒鐘後，會打開一個包含單個*單元格*的新筆記本。筆記本由一個或多個單元格組成，可以包含*代碼*或*標記（*格式化文本）。

![](./media/image21.png)

3.  選擇第一個單元格（目前是一個代碼單元格），然後在其右上角的動態工具欄中，使用**M↓**按鈕**convert
    the cell to a markdown cell**。 

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image22.png)

4.  當該單元格變為標記降低單元格時，其文本會被渲染。

![A screenshot of a computer Description automatically
generated](./media/image23.png)

5.  使用**🖉**（Edit）按鈕將單元格切換到編輯模式，替換所有文本，然後按以下方式修改標記:

> CodeCopy
>
> \# Sales order data exploration
>
> Use the code in this notebook to explore sales order data.

![](./media/image24.png)

![A screenshot of a computer Description automatically
generated](./media/image25.png)

6.  點擊筆記本中單元格外的任何位置，停止編輯並查看渲染後的標記。

![A screenshot of a computer Description automatically
generated](./media/image26.png)

## 任務4：將數據加載到數據幀中

現在你準備好運行將數據加載到*數據幀*中的代碼了。Spark 中的 Dataframes
類似於 Python 中的 Pandas dataframe，並為處理行和列數據提供了通用結構。

**注意**：Spark 支持多種編程語言，包括 Scala、Java
等。在這個練習中，我們將使用*PySpark*，它是Python的Spark優化版本。PySpark
是 Spark 上最常用的語言之一，也是 Fabric 筆記本的默認語言。

1.  筆記本可見後，展開 **Files** 列表，選擇
    **orders **文件夾，使CSV文件與筆記本編輯器並列。

> ![A screenshot of a computer Description automatically
> generated](./media/image27.png)

2.  現在，將鼠標懸停到2019.csv文件。點擊2019.csv旁邊的水平橢圓（...）。點擊
    **Load data**，然後選擇
    **Spark**。筆記本中將添加一個包含以下代碼的新代碼單元格:

> CodeCopy
>
> df =
> spark.read.format("csv").option("header","true").load("Files/orders/2019.csv")
>
> \# df now is a Spark DataFrame containing CSV data from
> "Files/orders/2019.csv".
>
> display(df)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image28.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image29.png)

**提示**：你可以用左側的“圖標”隱藏湖屋探索者面板 。正在做

這會幫你專注於筆記本。

3.  使用單元左側的 ** ▷ Run cell ** 按鈕來運行它。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image30.png)

**注意**：由於這是你第一次運行任何 Spark 代碼，必須啟動一次 Spark
會話。這意味著會話中的第一次運行可能需要一分鐘左右完成。後續的運行會更快。

4.  當單元格命令完成後，查看單元格下方的輸出，應該類似於這個:

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image31.png)

5.  輸出顯示的是2019.csv文件中的行和列數據。不過，請注意列頭看起來不太對。用於將數據加載到數據幀的默認代碼假設CSV文件第一行包含列名，但在此情況下，CSV文件僅包含數據，沒有任何頭部信息。

6.  修改代碼，將 **header** 選項設置為
    **false**。將該**單元格**中的所有代碼替換為以下代碼，點擊 **▷ Run
    cell** 按鈕，查看輸出結果 

> CodeCopy
>
> df =
> spark.read.format("csv").option("header","false").load("Files/orders/2019.csv")
>
> \# df now is a Spark DataFrame containing CSV data from
> "Files/orders/2019.csv".
>
> display(df)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image32.png)

7.  現在數據幀正確地包含了第一行作為數據值，但列名是自動生成的，幫助不大。要理解數據，你需要明確定義文件中數據值的正確模式和數據類型。

8.  將該**單元格**中的所有代碼 替換為以下代碼，點擊 **▷ Run cell**
    按鈕，查看輸出結果

> from pyspark.sql.types import \*
>
> orderSchema = StructType(\[
>
> StructField("SalesOrderNumber", StringType()),
>
> StructField("SalesOrderLineNumber", IntegerType()),
>
> StructField("OrderDate", DateType()),
>
> StructField("CustomerName", StringType()),
>
> StructField("Email", StringType()),
>
> StructField("Item", StringType()),
>
> StructField("Quantity", IntegerType()),
>
> StructField("UnitPrice", FloatType()),
>
> StructField("Tax", FloatType())
>
> \])
>
> df =
> spark.read.format("csv").schema(orderSchema).load("Files/orders/2019.csv")
>
> display(df)
>
> ![](./media/image33.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image34.png)

9.  現在，數據幀包含正確的列名（除了索引，**Index**
    是所有數據幀中基於每行序數位置的內置列）。列的數據類型使用Spark
    SQL庫中定義的標準類型集指定，這些類型在單元格開頭導入。

10. 通過查看數據幀確認你的更改已被應用到數據上。

11. 使用單元格輸出下方的 **+
    Code** 圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。點擊
    **▷ Run cell** 按鈕，查看輸出結果

> CodeCopy
>
> display(df)
>
> ![](./media/image35.png)

12. 數據幀僅包含**2019.csv**文件中的數據
    。修改代碼，使文件路徑使用\*通配符讀取**訂單**文件夾中所有文件的銷售訂單數據

13. 使用單元格輸出下方的 **+
    Code **圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。

CodeCopy

> from pyspark.sql.types import \*
>
> orderSchema = StructType(\[
>
>     StructField("SalesOrderNumber", StringType()),
>
>     StructField("SalesOrderLineNumber", IntegerType()),
>
>     StructField("OrderDate", DateType()),
>
>     StructField("CustomerName", StringType()),
>
>     StructField("Email", StringType()),
>
>     StructField("Item", StringType()),
>
>     StructField("Quantity", IntegerType()),
>
>     StructField("UnitPrice", FloatType()),
>
>     StructField("Tax", FloatType())
>
>     \])
>
> df =
> spark.read.format("csv").schema(orderSchema).load("Files/orders/\*.csv")
>
> display(df)
>
> ![](./media/image36.png)

14. 運行修改後的代碼單元格，查看輸出，現在應該包括2019、2020和2021年的銷售額。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image37.png)

**注意**：僅顯示部分行，因此你可能無法看到所有年份的示例。

# 練習2：探索數據框架內的數據

數據框對象包含多種函數，可用於過濾、分組和以其他方式作其包含的數據。

## 任務1：過濾數據幀

1.  使用單元格輸出下方的 **+ Code**
    圖標，向筆記本添加一個新的代碼單元格，並輸入以下代碼。

> customers = df\['CustomerName', 'Email'\]
>
> print(customers.count())
>
> print(customers.distinct().count())
>
> display(customers.distinct())
>
> ![](./media/image38.png)

2.  **運行** 新的代碼單元，查看結果。請注意以下細節:

    - 當你對數據幀執行作時，結果是一個新的數據幀（此例中，通過從**df**數據幀**中**選擇特定列子集創建新的**客戶**數據幀）

    - 數據幀提供**計數**和**不同**等功能，可用於總結和過濾其包含的數據。

    - dataframe\['Field1', 'Field2',
      ...\] 語法是一種簡寫方式，用來定義列的子集。
      你也可以使用**select**方法，比如上面代碼的第一行可以寫成customers
      = df.select（“CustomerName”， “Email”）

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image39.png)

3.  修改代碼，將該**單元格**中的所有代碼替換為以下代碼，然後點擊 **▷ Run
    cell** 按鈕，如下所示:

> CodeCopy
>
> customers = df.select("CustomerName",
> "Email").where(df\['Item'\]=='Road-250 Red, 52')
>
> print(customers.count())
>
> print(customers.distinct().count())
>
> display(customers.distinct())

4.  **運行**修改後的代碼以查看購買 ***Road-250 Red 52*** 產品的客戶。
    注意，你可以“**chain**”多個函數，使一個函數的輸出成為下一個函數的輸入——在這種情況下，**select**方法創建的數據幀是用於應用過濾條件的**where**方法的源數據幀。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image40.png)

## 任務2：將數據匯總和分組到數據框架中

1.  點擊 **+** **Code** ，複製粘貼下面的代碼，然後點擊 **“Run cell”**
    按鈕。

> **CodeCopy:**
>
> productSales = df.select("Item", "Quantity").groupBy("Item").sum()
>
> display(productSales)
>
> ![](./media/image41.png)

2.  請注意，結果顯示了按產品分組的訂單數量之和。**groupBy**
    方法按項目*對行進行分組*，隨後對剩餘所有數值列（此處為數量）應用和匯總函數

3.  點擊 **+** **Code**，複製粘貼下面的代碼，然後點擊 **“Run cell”**
    按鈕。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image42.png)

> **CodeCopy**
>
> from pyspark.sql.functions import \*
>
> yearlySales =
> df.select(year("OrderDate").alias("Year")).groupBy("Year").count().orderBy("Year")
>
> display(yearlySales)
>
> ![](./media/image43.png)

4.  請注意，結果顯示的是每年銷售訂單數量。注意，**select**方法包含一個SQL
    **年**函數，用於提取*OrderDate*字段中的年份成分（這也是代碼中包含
    導入語句以導入Spark
    SQL庫中的函數的原因）。然後它使用**別名**方法為提取的年份值分配列名。然後將數據按派生*的年份*列分組，計算每組的行數，最後
    使用**OrderBy**方法對所得數據幀進行排序**。**

# 練習3：使用 Spark 轉換數據文件

數據工程師的一項常見任務是以特定格式或結構導入數據，並將其轉換以供後續處理或分析。

## 任務1：使用數據框架方法和函數進行數據轉換

1.  點擊 + Code，複製粘貼下面的代碼

**CodeCopy**

> from pyspark.sql.functions import \*
>
> \## Create Year and Month columns
>
> transformed_df = df.withColumn("Year",
> year(col("OrderDate"))).withColumn("Month", month(col("OrderDate")))
>
> \# Create the new FirstName and LastName fields
>
> transformed_df = transformed_df.withColumn("FirstName",
> split(col("CustomerName"), " ").getItem(0)).withColumn("LastName",
> split(col("CustomerName"), " ").getItem(1))
>
> \# Filter and reorder columns
>
> transformed_df = transformed_df\["SalesOrderNumber",
> "SalesOrderLineNumber", "OrderDate", "Year", "Month", "FirstName",
> "LastName", "Email", "Item", "Quantity", "UnitPrice", "Tax"\]
>
> \# Display the first five orders
>
> display(transformed_df.limit(5))
>
> ![](./media/image44.png)

2.  **運行** 代碼，從原始順序數據中創建新的數據幀，並進行以下變換:

    - 根據**OrderDate**列添加**年份**和**月份**列。

    - 根據**CustomerName**列添加**FirstName**和**LastName**列。

    - 過濾並重新排序列，移除**CustomerName**列。

> ![](./media/image45.png)

3.  檢查輸出並確認數據的轉換已完成。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image46.png)

你可以充分利用 Spark SQL
庫的全部功能，通過過濾行、推導、刪除、重命名列以及應用其他必要的數據修改來轉換數據。

**提示**：請參閱 [*Spark dataframe
文檔*](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/dataframe.html)，瞭解更多關於
Dataframe 對象的方法。

## 任務2：保存轉換後的數據

1.  **添加一個新單元格，**並在其中寫入以下代碼，以將轉換後的數據框保存為
    Parquet
    格式（如果數據已存在，則覆蓋現有數據）。**運行**該單元格並等待數據保存成功的提示信息。

> CodeCopy
>
> transformed_df.write.mode("overwrite").parquet('Files/transformed_data/orders')
>
> print ("Transformed data saved!")
>
> **注意**：通常，*Parquet*格式更適合用於進一步分析或導入分析存儲的數據文件。Parquet是一種非常高效的格式，大多數大型數據分析系統都支持它。事實上，有時你的數據轉換需求可能只是將其他格式（如CSV）的數據轉換成Parquet！
>
> ![](./media/image47.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image48.png)

2.  然後，在左側的 **Lakehouse explorer** 
    窗格中，在“**Files**”節點的“…”菜單中，選擇“**Refresh**”。

> ![A screenshot of a computer Description automatically
> generated](./media/image49.png)

3.  單擊 **transformed_data** 文件夾，確認其中是否包含一個名為
    **orders** 的新文件夾，而 orders 文件夾又包含一個或多個 **Parquet
    文件**。

> ![A screenshot of a computer Description automatically
> generated](./media/image50.png)

4.  點擊 **+ Code** 跟隨代碼，從 **transformed_data -\> orders**
    文件夾中的 parquet 文件加載新數據幀 :

> **CodeCopy**
>
> orders_df =
> spark.read.format("parquet").load("Files/transformed_data/orders")
>
> display(orders_df)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image51.png)

5.  **運行** 該單元格，驗證結果是否顯示了從parquet文件加載的順序數據。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image52.png)

## 任務3：將數據保存到分區文件中

1.  添加一個新單元格，點擊以下代碼的**+
    Code**;它保存數據幀，按**年份**和**月份劃分**數據。
    **運行**小區並等待數據已保存的消息

> CodeCopy
>
> orders_df.write.partitionBy("Year","Month").mode("overwrite").parquet("Files/partitioned_data")
>
> print ("Transformed data saved!")
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image53.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image54.png)

2.  然後，在左側的 **Lakehouse explorer**
    窗格中，在“**Files**”節點的“…”菜單中，選擇“**Refresh**”。 

![A screenshot of a computer Description automatically
generated](./media/image55.png)

3.  展開**partitioned_orders**文件夾，確認其中包含名為**Year=xxxx**的文件夾層級結構，每個文件夾包含名為**Month=xxxx**的文件夾。每個月文件夾都包含一個鑲花文件，裡面有當月的訂單。

![A screenshot of a computer Description automatically
generated](./media/image56.png)

![A screenshot of a computer Description automatically
generated](./media/image57.png)

> 數據文件分區是處理大量數據時優化性能的常見方法。這種方法可以顯著提升性能，並使數據過濾變得更簡單。

4.  添加一個新單元格，點擊以下代碼的 **+Code，**從 **orders.parquet**
    文件加載新數據幀 :

> CodeCopy
>
> orders_2021_df =
> spark.read.format("parquet").load("Files/partitioned_data/Year=2021/Month=\*")
>
> display(orders_2021_df)

5.  **運行**
    單元格，確認結果顯示的是2021年的訂單數據。注意路徑中指定的分區列（**年份**和**月份**）未包含在數據幀中。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image58.png)

# **練習3：處理表和SQL**

正如你所見，dataframe對象的原生方法讓你能夠非常有效地查詢和分析文件中的數據。然而，許多數據分析師更習慣使用可以用SQL語法查詢的表。Spark
提供了一個元*存儲*庫，你可以在這裡定義關係表。提供數據框架對象的 Spark
SQL 庫也支持使用 SQL 語句查詢元存儲中的表。通過使用 Spark
的這些功能，你可以將數據湖的靈活性與關系型數據倉庫的結構化數據模式和基於
SQL 的查詢結合起來——這就是“數據lakehouse”這一術語的由來。

## 任務1：創建一個受管理表

Spark
元存儲中的表是數據湖中文件的關係抽象。表可以被*管理*（此時文件由元存儲管理）或*外部*（此時表引用數據湖中獨立於元存儲管理的文件位置）。

1.  添加新代碼，點擊筆記本中的**“+
    Code“**單元格，輸入以下代碼，該代碼會將銷售訂單數據的數據框保存為名為
    **salesorders** 的表格:

> CodeCopy
>
> \# Create a new table
>
> df.write.format("delta").saveAsTable("salesorders")
>
> \# Get the table description
>
> spark.sql("DESCRIBE EXTENDED salesorders").show(truncate=False)

**注意**：關於這個例子，值得注意幾點。首先，沒有提供顯式路徑，因此表的文件將由元存儲管理。其次，表格以
**delta** 格式保存。你可以基於多種文件格式創建表（包括
CSV、Parquet、Avro 等），但 *delta lake* 是一種 Spark
技術，為表增加了關系數據庫功能;包括對事務、行版本控制及其他實用功能的支持。在
Fabric 中創建數據湖屋更傾向於以 delta 格式創建表。

2.  **運行** 代碼單元並查看輸出，後者描述了新表的定義。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image59.png)

3.  在 **Lakehouse**
    **explorer** 窗格中，在“**Tables**”文件夾的“…”菜單中，選擇“**Refresh**”。

![A screenshot of a computer Description automatically
generated](./media/image60.png)

4.  然後展開 **Tables** 節點，確認 **SalesOrders** 表是否已在 **dbo**
    模式下創建。

> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

5.  將鼠標懸停在 **salesorders**
    表旁邊，然後單擊水平省略號（…）。導航並單擊“**Load data**”，然後選擇
    **Spark**。

> ![](./media/image62.png)

6.  點擊 **▷ Run cell** 按鈕，該按鈕使用Spark SQL庫將針對
    **salesorder** 表的SQL查詢嵌入到PySpark代碼中，並將查詢結果加載到數據幀中。

> CodeCopy
>
> df = spark.sql("SELECT \* FROM Fabric_lakehouse.dbo.salesorders LIMIT
> 1000")
>
> display(df)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image63.png)

## 任務2：創建一個外部表格

你也可以創建 外部表，模式元數據在 lakehouse
的元存儲中定義，但數據文件存儲在外部位置。

1.  在第一個代碼單元返回的結果下，如果沒有新的代碼單元格，使用 **+
    Code**按鈕添加新代碼單元。然後在新格子裡輸入以下代碼。

CodeCopy

> df.write.format("delta").saveAsTable("external_salesorder",
> path="\<abfs_path\>/external_salesorder")

![A screenshot of a computer Description automatically
generated](./media/image64.png)

2.  在 **Lakehouse
    explorer** 窗格中，“**Files**”文件夾的“…”菜單中，選擇在記事本中**Copy
    ABFS path**”。

> ABFS路徑是你 **lakehouse** **OneLake**
> 存儲中**Files**文件夾的完整合格路徑——類似於這個:

abfss://dp_Fabric29@onelake.dfs.fabric.microsoft.com/Fabric_lakehouse.Lakehouse/Files/external_salesorder

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image65.png)

3.  現在，進入代碼單元格，將 **\`\<abfs_path\>\`**
    替換為您複製到記事本中的**路徑**，以便代碼將數據幀保存為外部表，並將數據文件保存在“文件”文件夾下的名為
    **\`external_salesorder\`** 的**Files**中。完整路徑應類似於這樣

abfss://dp_Fabric29@onelake.dfs.fabric.microsoft.com/Fabric_lakehouse.Lakehouse/Files/external_salesorder

4.  使用單元左側的 **▷ (Run cell)** 按鈕來運行它。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image66.png)

5.  在 **Lakehouse
    explorer** 窗格中，在“**Tables** ”文件夾的“…”菜單中，選擇“**Refresh**”。

![A screenshot of a computer Description automatically
generated](./media/image67.png)

6.  然後展開“**Tables**”節點，並驗證 **external_salesorder**
    表是否已創建。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image68.png)

7.  在 **Lakehouse
    explorer** 窗格中，“**Files**”文件夾的“…”菜單中，選擇“**Refresh**”。 

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image69.png)

8.  然後展開**Files**節點，確認**external_salesorder**文件夾已為表中的數據文件創建。 

![](./media/image70.png)

## 任務3：比較託管表和外部表

讓我們來探討託管表和外部表之間的區別。

1.  在代碼單元返回的結果下，使用 **+ Code**
    按鈕添加新的代碼單元。將下面的代碼複製到代碼單元格，並使用單元格左側的
    **▷ (Run cell)** 按鈕來運行它。

> SqlCopy
>
> %%sql
>
> DESCRIBE FORMATTED salesorders;
>
> ![](./media/image71.png)

2.  在結果中，查看表的 **Location** 屬性，該屬性應該是指向 Lakehouse 的
    OneLake 存儲的路徑，以
    **/Tables/salesorders** 結尾（您可能需要展開“**Data
    type** ”列才能看到完整路徑）。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image72.png)

3.  修改 **DESCRIBE** 命令以顯示 **external_saleorder**
    表的詳細信息，如圖所示。

4.  在代碼單元格返回的結果下方，使用“**+
    Code** ”按鈕添加一個新的代碼單元格。複製下面的代碼，然後使用單元格左側的
    **▷ (*Run cell*)** 按鈕運行它。

> SqlCopy
>
> %%sql
>
> DESCRIBE FORMATTED external_salesorder;

5.  在結果中，查看表的 **Location** 屬性，它應該是指向 Lakehouse 的
    **OneLake** 存儲的路徑，以 **/Files/external_saleorder**
    結尾（您可能需要展開“**Data type**”列才能看到完整路徑）。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image73.png)

## 任務4：在單元格中運行SQL代碼

雖然能夠將SQL語句嵌入包含PySpark代碼的單元格很有用，但數據分析師通常只想直接用SQL工作。

1.  點擊筆記本的**+ Code**單元，輸入以下代碼。點擊 **▷ Run cell**
    按鈕，查看結果。請注意:

    - 單元格開頭的%%sql行（稱為*magic*）表示應使用Spark
      SQL語言運行時來運行該單元的代碼，而非PySpark。

    - SQL代碼引用 的是你之前創建的**salesorders** 表。

    - SQL查詢的輸出會自動顯示為單元格下的結果

> SqlCopy
>
> %%sql
>
> SELECT YEAR(OrderDate) AS OrderYear,
>
> SUM((UnitPrice \* Quantity) + Tax) AS GrossRevenue
>
> FROM salesorders
>
> GROUP BY YEAR(OrderDate)
>
> ORDER BY OrderYear;

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image74.png)

**注意**：有關 Spark SQL 和數據幀的更多信息，請參見 [*Spark SQL
文檔*](https://spark.apache.org/docs/2.2.0/sql-programming-guide.html)。

# 練習四：用Spark可視化數據

俗話說，一幅圖勝千言萬語，一張圖表往往比一千行數據更好。雖然 Fabric
中的筆記本內置了數據框架或 Spark SQL
查詢數據的圖表視圖，但它並非為全面的圖表設計。不過，你可以用 Python
圖形庫，比如 **matplotlib** 和 **seaborn**，從數據幀中生成圖表。

## 任務1：以圖表形式查看結果

1.  點擊筆記本中的**+ Code** 單元格，並在其中輸入以下代碼。點擊“ **▷ Run
    cell** ”按鈕，觀察它是否返回了您之前創建的 **salesorders**
    視圖中的數據。

> SqlCopy
>
> %%sql
>
> SELECT \* FROM salesorders

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image75.png)

2.  在單元格下方的結果部分，將 **View** 選項從 **Table** 更改為 **New
    chart**。

![](./media/image76.png)

3.  使用圖表右上角的**“Start
    editing**”按鈕，顯示圖表的選項面板。然後設置如下選項，選擇
    **Apply**:

    - **Chart type**: Bar chart

    - **Key**: Item

    - **Values**: Quantity

    - **Series Group**: *leave blank*

    - **Aggregation**: Sum

    - **Stacked**: *Unselected*

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image77.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image78.png)

4.  請確認圖表是否與此相似

> ![](./media/image79.png)

## 任務2：開始使用 matplotlib

1.  點擊 **+ Code**，複製粘貼下面的代碼。 **運行**
    代碼，觀察它返回一個包含年度收入的 Spark 數據幀。

> CodeCopy
>
> sqlQuery = "SELECT CAST(YEAR(OrderDate) AS CHAR(4)) AS OrderYear, \\
>
> SUM((UnitPrice \* Quantity) + Tax) AS GrossRevenue \\
>
> FROM salesorders \\
>
> GROUP BY CAST(YEAR(OrderDate) AS CHAR(4)) \\
>
> ORDER BY OrderYear"
>
> df_spark = spark.sql(sqlQuery)
>
> df_spark.show()
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image80.png)

2.  為了將數據可視化為圖表，我們將先使用 **matplotlib** Python
    庫。該庫是許多其他庫的核心繪圖庫，提供了極大的圖表製作靈活性。

3.  點擊 **+ Code**，複製粘貼下面的代碼。

**CodeCopy**

> from matplotlib import pyplot as plt
>
> \# matplotlib requires a Pandas dataframe, not a Spark one
>
> df_sales = df_spark.toPandas()
>
> \# Create a bar plot of revenue by year
>
> plt.bar(x=df_sales\['OrderYear'\], height=df_sales\['GrossRevenue'\])
>
> \# Display the plot
>
> plt.show()

![A screenshot of a computer Description automatically
generated](./media/image81.png)

5.  點擊 **“Run
    cell ”**按鈕查看結果，結果包括一個欄狀圖，顯示每年的總總收入。請注意用於製作該圖表的代碼的以下特點:

    - **matplotlib** 庫需要 *Pandas* 數據幀，所以你需要將 *Spark* SQL
      查詢返回的數據幀轉換成這個格式。

    - matplotlib **庫**的核心是 **pyplot**
      對象。這是大多數繪圖功能的基礎。

    - 默認設置會得到可用的圖表，但自定義空間很大

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image82.png)

6.  修改代碼，將圖表繪製如下圖，將該**單元格**中的所有代碼替換為以下代碼，點擊**▷
    Run cell** 格按鈕，查看輸出結果

> CodeCopy
>
> from matplotlib import pyplot as plt
>
> \# Clear the plot area
>
> plt.clf()
>
> \# Create a bar plot of revenue by year
>
> plt.bar(x=df_sales\['OrderYear'\], height=df_sales\['GrossRevenue'\],
> color='orange')
>
> \# Customize the chart
>
> plt.title('Revenue by Year')
>
> plt.xlabel('Year')
>
> plt.ylabel('Revenue')
>
> plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y',
> alpha=0.7)
>
> plt.xticks(rotation=45)
>
> \# Show the figure
>
> plt.show()
>
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image83.png)
>
> ![A graph with orange bars AI-generated content may be
> incorrect.](./media/image84.png)

7.  圖表現在包含了一些更多信息。劇情技術上是由**一個人物**所包含的。在前面的例子中，這個圖形是隱含地為你創造的;但你可以明確創建它。

8.  修改代碼，將圖表繪製如下圖，將**單元格**中的所有代碼替換
    為以下代碼。

> CodeCopy
>
> from matplotlib import pyplot as plt
>
> \# Clear the plot area
>
> plt.clf()
>
> \# Create a Figure
>
> fig = plt.figure(figsize=(8,3))
>
> \# Create a bar plot of revenue by year
>
> plt.bar(x=df_sales\['OrderYear'\], height=df_sales\['GrossRevenue'\],
> color='orange')
>
> \# Customize the chart
>
> plt.title('Revenue by Year')
>
> plt.xlabel('Year')
>
> plt.ylabel('Revenue')
>
> plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y',
> alpha=0.7)
>
> plt.xticks(rotation=45)
>
> \# Show the figure
>
> plt.show()

9.  **重新運行** 代碼單元，查看結果。圖形決定了地塊的形狀和大小。

> 一個圖可以包含多個子線，每個子線都圍繞其自身*軸*線。
>
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image85.png)
>
> ![A screenshot of a graph AI-generated content may be
> incorrect.](./media/image86.png)

10. 修改代碼，將圖表繪製如下圖。 **重新運行**
    代碼單元，查看結果。圖中包含了代碼中指定的子線。

> CodeCopy
>
> from matplotlib import pyplot as plt
>
> \# Clear the plot area
>
> plt.clf()
>
> \# Create a figure for 2 subplots (1 row, 2 columns)
>
> fig, ax = plt.subplots(1, 2, figsize = (10,4))
>
> \# Create a bar plot of revenue by year on the first axis
>
> ax\[0\].bar(x=df_sales\['OrderYear'\],
> height=df_sales\['GrossRevenue'\], color='orange')
>
> ax\[0\].set_title('Revenue by Year')
>
> \# Create a pie chart of yearly order counts on the second axis
>
> yearly_counts = df_sales\['OrderYear'\].value_counts()
>
> ax\[1\].pie(yearly_counts)
>
> ax\[1\].set_title('Orders per Year')
>
> ax\[1\].legend(yearly_counts.keys().tolist())
>
> \# Add a title to the Figure
>
> fig.suptitle('Sales Data')
>
> \# Show the figure
>
> plt.show()
>
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image87.png)
>
> ![A screenshot of a computer screen AI-generated content may be
> incorrect.](./media/image88.png)

**注意**：想瞭解更多關於使用 matplotlib 繪製的信息，請參閱 [*matplotlib
文檔*](https://matplotlib.org/)。

## 任務3：使用 Seaborn 庫

雖然 **matplotlib**
可以讓你創建多種類型的複雜圖表，但要達到最佳效果可能需要一些複雜的代碼。因此，多年來，許多新的庫在
matplotlib 基礎上構建，以抽象化其複雜性並增強其能力。其中一個圖書館是
**seaborn**。

1.  點擊 **+ Code**，複製粘貼下面的代碼。

CodeCopy

> import seaborn as sns
>
> \# Clear the plot area
>
> plt.clf()
>
> \# Create a bar chart
>
> ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
>
> plt.show()

2.  **運行** 代碼，觀察它顯示的是使用 Seaborn 庫的條形圖。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image89.png)

3.  **修改** 代碼如下。 **運行** 修改後的代碼，注意 seaborn
    可以讓你為地塊設置一致的顏色主題。

> CodeCopy
>
> import seaborn as sns
>
> \# Clear the plot area
>
> plt.clf()
>
> \# Set the visual theme for seaborn
>
> sns.set_theme(style="whitegrid")
>
> \# Create a bar chart
>
> ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
>
> plt.show()
>
> ![A screenshot of a graph AI-generated content may be
> incorrect.](./media/image90.png)

4.  再次**修改** 代碼如下。 **運行**
    修改後的代碼，以折線圖的形式查看年度收入。

> CodeCopy
>
> import seaborn as sns
>
> \# Clear the plot area
>
> plt.clf()
>
> \# Create a bar chart
>
> ax = sns.lineplot(x="OrderYear", y="GrossRevenue", data=df_sales)
>
> plt.show()
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image91.png)

**注意**：想瞭解更多關於用 seaborn 策劃的建議，請參見
[*seaborn文檔*](https://seaborn.pydata.org/index.html)。

## 任務4：使用delta表進行流數據流處理

Delta Lake 支持流式數據。Delta 表可以作為使用 Spark Structured Streaming
API 創建的數據流的接收器或源。在本示例中，您將使用 Delta
表作為模擬物聯網 (IoT) 場景中某些流式數據的接收器。

1.  點擊 **+ Code** ，複製粘貼下面的代碼，然後點擊 **“Run cell”** 按鈕。

CodeCopy

> from notebookutils import mssparkutils
>
> from pyspark.sql.types import \*
>
> from pyspark.sql.functions import \*
>
> \# Create a folder
>
> inputPath = 'Files/data/'
>
> mssparkutils.fs.mkdirs(inputPath)
>
> \# Create a stream that reads data from the folder, using a JSON
> schema
>
> jsonSchema = StructType(\[
>
> StructField("device", StringType(), False),
>
> StructField("status", StringType(), False)
>
> \])
>
> iotstream =
> spark.readStream.schema(jsonSchema).option("maxFilesPerTrigger",
> 1).json(inputPath)
>
> \# Write some event data to the folder
>
> device_data = '''{"device":"Dev1","status":"ok"}
>
> {"device":"Dev1","status":"ok"}
>
> {"device":"Dev1","status":"ok"}
>
> {"device":"Dev2","status":"error"}
>
> {"device":"Dev1","status":"ok"}
>
> {"device":"Dev1","status":"error"}
>
> {"device":"Dev2","status":"ok"}
>
> {"device":"Dev2","status":"error"}
>
> {"device":"Dev1","status":"ok"}'''
>
> mssparkutils.fs.put(inputPath + "data.txt", device_data, True)
>
> print("Source stream created...")
>
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image92.png)
>
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image93.png)

2.  確保消息源 ***Source stream
    created…*** 已印刷。你剛運行的代碼基於一個文件夾創建了一個流數據源，該文件夾保存了一些數據，代表假設的物聯網設備的讀數。

3.  點擊 **+ Code** ，複製粘貼下面的代碼，然後點擊 **“Run cell”** 按鈕。

CodeCopy

> \# Write the stream to a delta table
>
> delta_stream_table_path = 'Tables/iotdevicedata'
>
> checkpointpath = 'Files/delta/checkpoint'
>
> deltastream =
> iotstream.writeStream.format("delta").option("checkpointLocation",
> checkpointpath).start(delta_stream_table_path)
>
> print("Streaming to delta sink...")
>
> ![](./media/image94.png)

4.  此代碼以增量格式將流式設備數據寫入名為 **iotdevicedata**
    的文件夾。由於文件夾路徑位於 **Tables** 
    文件夾中，因此會自動為其創建一個表格。單擊表格旁邊的水平省略號，然後單擊“**Refresh**”。

![](./media/image95.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image96.png)

5.  點擊“ **+ Code**”，複製並粘貼以下代碼，然後點擊“**Run cell**”按鈕。

> SqlCopy
>
> %%sql
>
> SELECT \* FROM IotDeviceData;
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image97.png)

6.  該代碼查詢包含流媒體源設備數據的 **IotDeviceData** 表。

7.  點擊 **+ Code**，複製粘貼下面的代碼，然後點擊“**Run cell**”按鈕。

> CodeCopy
>
> \# Add more data to the source stream
>
> more_data = '''{"device":"Dev1","status":"ok"}
>
> {"device":"Dev1","status":"ok"}
>
> {"device":"Dev1","status":"ok"}
>
> {"device":"Dev1","status":"ok"}
>
> {"device":"Dev1","status":"error"}
>
> {"device":"Dev2","status":"error"}
>
> {"device":"Dev1","status":"ok"}'''
>
> mssparkutils.fs.put(inputPath + "more-data.txt", more_data, True)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image98.png)

8.  這段代碼會將更多假設的設備數據寫入流源。

9.  點擊 **+ Code**，複製粘貼下面的代碼，然後點擊“**Run cell**”按鈕。

> SqlCopy
>
> %%sql
>
> SELECT \* FROM IotDeviceData;
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image99.png)

10. 該代碼再次查詢 **IotDeviceData**
    表，表中應包含已添加到流源的額外數據。

11. 點擊 **+ Code**，複製粘貼下面的代碼，然後點擊“**Run cell**”按鈕。

> CodeCopy
>
> deltastream.stop()
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image100.png)

12. 這個代碼會停止直播。

## 任務五：保存筆記本並結束 Spark 會話

現在你已經完成數據處理，可以保存筆記本並命名有意義，並結束 Spark 會話。

1.  在筆記本菜單欄，使用 ⚙️ **Settings **圖標查看筆記本設置。

![A screenshot of a computer Description automatically
generated](./media/image101.png)

2.  將筆記本的 **Name** 設置為  +++**Explore Sales
    Orders+++**，然後關閉設置窗格。 

![A screenshot of a computer Description automatically
generated](./media/image102.png)

3.  在筆記本菜單中，選擇 **Stop session** 以結束Spark會話。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image103.png)

![A screenshot of a computer Description automatically
generated](./media/image104.png)

# 練習5：在Microsoft Fabric中創建數據流（Gen2）

在 Microsoft Fabric 中，數據流（Gen2）連接多個數據源，並在 Power Query
Online
中執行轉換。然後它們可以在數據管道中用於將數據導入湖屋或其他分析存儲，或定義
Power BI 報告中的數據集。

本練習旨在介紹數據流（Gen2）的不同元素，而非創建企業中可能存在的複雜解決方案

## 任務1：創建數據流（Gen2）以獲取數據

現在你有了湖屋，需要把一些數據導入去。一種方法是定義一個數據流，封裝提取*、轉換和加載*（ETL）過程。

1.  現在，點擊 左側導航面板上的Fabric_lakehouse。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image105.png)

2.  **Fabric_lakehouse** 主頁上，單擊“**Get
    data**”中的下拉箭頭，然後選擇“**New Dataflow
    Gen2**”。此時將打開新數據流的 Power Query 編輯器。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image106.png)

5.  在“**New Dataflow Gen2**”對話框中，在“**Name**”字段中輸入
    **+++Gen2_Dataflow+++** ，單擊“**Create**”按鈕，打開新的數據流
    Gen2。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image107.png)

3.  在 **Power Query 主頁標簽**下的窗格中，點擊“**Import from a Text/CSV
    file**”。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image108.png)

4.  在“**Connect to data source**”窗格的“**Connection
    settings**”下，選擇“**Link to file (Preview)**”單選按鈕。

- **文件鏈接**: *已選擇*

- **文件路徑或URL**: +++https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/orders.csv+++

![](./media/image109.png)

5.  在“**Connect to data source**”窗格的“**Connection
    credentials**”下，輸入以下詳細信息，然後單擊“**Next**”按鈕。

- **連接**：創造新的連接

- **連接名稱**：Orders

- **數據網關**：（無）

- **認證類型**：匿名

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image110.png)

6.  在“**Preview file data**”窗格中，單擊“**Create**”以創建數據源。![A
    screenshot of a computer Description automatically
    generated](./media/image111.png)

7.  **Power Query** 編輯器顯示數據源及初始查詢步驟，用於格式化數據。

![](./media/image112.png)

8.  在工具欄功能區上，選擇**“Add column**”標簽。然後，選擇 **Custom
    column。**

> ![](./media/image113.png) 

9.  將新列名稱設置為 +++**MonthNo+++**，數據類型設置為**Whole
    Number**，然後在“**Custom column
    formula**”下添加以下公式：+++**Date.Month(\[OrderDate\])+++**。單擊“**OK**”。

> ![](./media/image114.png)

10. 注意添加自定義列的步驟是如何添加到查詢中的。生成的列會顯示在數據窗格中。

> ![A screenshot of a computer Description automatically
> generated](./media/image115.png)

**提示：**在右側的查詢設置面板中，注意應用 **Applied
Steps** 了每個變換步驟。在底部，你還可以切換“**Diagram
flow**”按鈕，打開步驟的可視化示意圖。

步數可以上下移動，通過選擇齒輪圖標進行編輯，你還可以選擇每個步驟，在預覽窗格中看到變換的應用。

任務2：為Dataflow添加數據目的地

1.  在 **Power Query** 工具欄功能區中，選擇“**Home**”標簽。然後在 D**ata
    destination** 下拉菜單中，選擇 **Lakehouse**（如果還沒選中）。

![](./media/image116.png)

![](./media/image117.png)

**注意：**如果該選項顯示為灰色，說明你可能已經設置了數據目的地。請在
Power Query
編輯器右側的查詢設置窗底部查看數據目的地。如果目的地已經設定好，可以用檔位來更改。

2.  在 Power Query 編輯器中，**Lakehouse**
    目的地以**圖標**的形式顯示在**query**中。 

![A screenshot of a computer Description automatically
generated](./media/image118.png)

![A screenshot of a computer Description automatically
generated](./media/image119.png)

3.  在主頁窗口，選擇“**Save & run**”，然後點擊“**Save & run**”按鈕

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image120.png)

4.  在左側導航中選擇 ***dp_Fabric-XXXXX workspace圖標***，如下圖所示

![](./media/image121.png)

## 任務3：向管道添加數據流

你可以將數據流作為流水線中的活動包含。管道用於協調數據的攝取和處理活動，使你能夠將數據流與其他類型的作結合在一個單一的定時流程中。管道可以在幾種不同的體驗中創建，包括Data
Factory體驗。

1.  在 Synapse 數據工程主頁的 **dp_FabricXX** 窗格中，選擇**+New item**
    -\> P**ipeline**”。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image122.png)

2.  在“**New pipeline**”對話框中，在“**Name**”字段中輸入 +++**Load
    data+++**，然後單擊“**Create**”按鈕以打開新管道。

![A screenshot of a computer Description automatically
generated](./media/image123.png)

3.  管道編輯器打開。

> ![A screenshot of a computer Description automatically
> generated](./media/image124.png)
>
> **提示**：如果複製數據嚮導自動打開，請關閉它！

4.  選擇 **Pipeline activity**，並將 **Dataflow** 活動添加到管道中。

![A screenshot of a computer Description automatically
generated](./media/image125.png)

5.  選擇新的 **Dataflow1**
    活動後，在“**Settings**”選項卡上的“**Dataflow**”下拉列表中，選擇
    **Gen2_Dataflow**（您之前創建的數據流）。

![A screenshot of a computer Description automatically
generated](./media/image126.png)

6.  在**主頁**標簽頁，使用**🖫（*保存*）**圖標保存管道。

![A screenshot of a computer Description automatically
generated](./media/image127.png)

7.  使用 **▷ Run** 按鈕運行管道，等待它完成。可能需要幾分鐘。

> ![A screenshot of a computer Description automatically
> generated](./media/image128.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image129.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image130.png)

8.  從頂部欄選擇 **Fabric_lakehouse** 標簽。

> ![A screenshot of a computer Description automatically
> generated](./media/image131.png)

9.  在 **Explorer**
    窗格中，選擇“**Tables**”的“…”菜單，然後選擇“**refresh**”。接著展開“**Tables**”，選擇由數據流創建的
    **orders** 表。

![A screenshot of a computer Description automatically
generated](./media/image132.png)

![](./media/image133.png)

**提示**： 使用Power
BI桌面*數據流連接器*，直接連接到數據流中的數據轉換。

你還可以進行額外的轉換，作為新數據集發佈，並向目標受眾分發專門數據集。

## 任務4：清理資源

在這個練習中，你已經學會了如何使用Spark在Microsoft Fabric中處理數據。

如果你已經完成了 lakehouse 探索，可以刪除你為這個練習創建的工作區。

1.  在左側欄中，選擇工作區圖標，查看其所有項目。

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image134.png)

2.  在**......**工具欄菜單，選擇 **Workspace settings**。

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image135.png)

3.  選擇“**General**”，然後單擊“**Remove this workspace**”。

![A screenshot of a computer settings Description automatically
generated](./media/image136.png)

4.  在 **Delete workspace?** 對話框，點擊 **Delete** 按鈕。

> ![A screenshot of a computer Description automatically
> generated](./media/image137.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image138.png)

**摘要**

本 用例 將引導你在 Power BI 中使用 Microsoft Fabric
的過程。它涵蓋了多個任務，包括搭建工作區、創建
lakehouse、上傳和管理數據文件，以及使用筆記本進行數據探索。參與者將學習如何使用PySpark作和轉換數據，創建可視化，並保存和分區數據以實現高效的查詢。

在這個用例中，參與者將參與一系列專注於Microsoft
Fabric中三角表的任務。任務包括上傳和探索數據、創建託管和外部 delta
表、比較其屬性，實驗室介紹了用於結構化數據管理的 SQL 功能，並利用
Matplotlib 和 seaborn 等 Python
庫提供數據可視化的見解。這些練習旨在全面理解如何使用 Microsoft Fabric
進行數據分析，以及在物聯網環境中引入差異表進行數據流傳輸。

這個用例將引導你完成搭建Fabric工作區、創建數據湖屋以及數據導入分析的過程。它演示了如何定義數據流以處理ETL作，並配置存儲轉換後數據的數據目的地。此外，你還將學習如何將數據流集成到自動化處理的流水線中。最後，您將獲得清理資源的指導。

該實驗室為您提供使用Fabric所需的必要技能，使您能夠創建和管理工作空間，建立數據湖，並高效執行數據轉換。通過將數據流融入管道，您將學會如何自動化數據處理任務，簡化工作流程並在現實環境中提升生產力。清理說明確保不遺留多餘資源，促進有序高效的工作管理方式。
