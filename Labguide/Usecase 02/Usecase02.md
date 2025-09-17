# Use Case 02: Data Factory solution for moving and transforming data with dataflows and data pipelines

**Introduction**

This lab helps you accelerate the evaluation process for Data Factory in
Microsoft Fabric by providing a step-by-step guidance for a full data
integration scenario within one hour. By the end of this tutorial, you
understand the value and key capabilities of Data Factory and know how
to complete a common end-to-end data integration scenario.

**Objective**

The lab is divided into three exercises:

- **Exercise 1:** Create a pipeline with Data Factory to ingest raw data
  from a Blob storage to a bronze table in a data Lakehouse.

- **Exercise 2:** Transform data with a dataflow in Data Factory to
  process the raw data from your bronze table and move it to a Gold
  table in the data Lakehouse.

- **Exercise 3:** Automate and send notifications with Data Factory to
  send an email to notify you once all the jobs are complete, and
  finally, setup the entire flow to run on a scheduled basis.

## Exercise 1: Create a pipeline with Data Factory

### Task 1: Create a workspace

Before working with data in Fabric, create a workspace with the Fabric
trial enabled.

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then
    press the **Enter** button.

    >[!note]**Note**: If you are directed to Microsoft Fabric Home page, then skip
    > steps from \#2 to \#4.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image1.png)

2.  In the **Microsoft Fabric** window, enter your credentials, and
    click on the **Submit** button.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image2.png)

3.  Then, In the **Microsoft** window enter the password and click on
    the **Sign in** button.

    > ![A login screen with a red box and blue text AI-generated content may
    > be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image3.png)

4.  In **Stay signed in?** window, click on the **Yes** button.

    > ![A screenshot of a computer error AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image4.png)

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image5.png)

5.  On the Microsoft **Fabric Home Page**, select **New workspace**
    option.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image6.png)

6.  In the **Create a workspace** tab, enter the following details and
    click on the **Apply** button.

    |  |  |
    |----|----|
    |Name	| +++Data-Factory@lab.LabInstance.Id+++ (must be a unique id)| 
    |Advanced	|Under License mode, select Fabric capacity|
    |Default storage format|	Small semantic model storage format|

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image7.png)

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image8.png)

7.  Wait for the deployment to complete. It'll take approximately 2-3
    minutes.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image9.png)

### Task 2: Create a lakehouse

1.  In the **Data-Factory@lab.LabInstance.Id** workspace page, navigate and click on
    **+New item** button
	
    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image10.png)

3.  Filter by, and select, the **+++Lakehouse+++** tile.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image11.png)

1.  In the **New lakehouse** dialog box, enter
    **+++DataFactoryLakehouse+++** in the **Name** field, click on the
    **Create** button to open the new lakehouse.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image12.png)
	
    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image13.png)

4.  Now, click on **Data-Factory@lab.LabInstance.Id** on the left-sided navigation pane.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image14.png)

### Task 3: Create a data pipeline

1.  Select the **+ New item** option on the workspace page.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image15.png)

2.  Select **Data Pipeline** from the new item flyout menu.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image16.png)

3.  Provide a Pipeline Name as **+++First_Pipeline1+++** and then select
    **Create**.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image17.png)

### Task 4: Use a Copy activity in the pipeline to load sample data to a data Lakehouse

1.  In the **First_Pipeline1** home page, Select **Copy data assistant**
     to open the copy assistant tool.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image18.png)

2.  The **Copy data** dialog is displayed with the first step, **Choose
    data source**, highlighted. Select  **Sample data** section, and
    select the **NYC Taxi-Green** data source type. Then
    select **Next**.

    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image19.png)

3.  In the **Connect to data source**, click on **Next** button.

    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image20.png)

4.  Select **OneLake catalog** and select **Existing Lakehouse** on the
    data destination configuration page that appears.

    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image21.png)

5.  Now configure the details of your Lakehouse destination on
    the **Select and map to folder path or table.** page.
    Select **Tables** for the **Root folder**, provide a table name
    **+++Bronze+++**, and select the **Next**.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image23.png)

1.  Finally, on the **Review + save** page of the copy data assistant, review the
    configuration. For this lab, uncheck the **Start data transfer
    immediately** checkbox, since we run the activity manually in the
    next step. Then select **OK**.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image22.png)
    
### Task 5: Run and view the results of your Copy activity.

1.  On the **Home** tab of the pipeline editor window, then select
    the **Run** button to manually trigger the run of the pipeline.

    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image24.png)

2.  In the **Save and run?** dialog box, click on **Save and run**
    button to execute these activities. This activity will take around
    12-13 min.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image25.png)

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image26.png)

3.  You can monitor the run and check the results on the **Output** tab
    below the pipeline canvas. Select the **activity name** to view the
    run details.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image27.png)

4.  The run details show 76,513,115 rows read and written.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image28.png)

5.  Expand the **Duration breakdown** section to see the duration of
    each stage of the Copy activity. After reviewing the copy details,
    select Close.

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image29.png)

### Exercise 2: Transform data with a dataflow in Data Factory

### Task 1: Get data from a Lakehouse table

1.  On the **First_Pipeline1** page, from the sidebar select **Create.**

     > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image30.png)

2.  On the **new item** page, to create a new dataflow gen2, click
    on **Dataflow Gen2** under the **Data Factory.** 

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image31.png)

3.  Provide a New Dataflow Gen2 Name as
    **+++nyc_taxi_data_with_discounts+++** and then select **Create**.

    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image32.png)

4.  From the new dataflow menu, under the **Power Query** pane click on
    **Get data drop down**, then select **More...**.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image33.png)

5.  In the **Choose data source** tab, search box search type
    **+++Lakehouse+++** and then click on the **Lakehouse** connector.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image34.png)

6.  The **Connect to data source** dialog appears, and a new connection
    is automatically created for you based on the currently signed in
    user. Select **Next**.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image35.png)

7.  The **Choose data** dialog is displayed. Use the navigation pane to
    find the **workspace- Data-Factory@lab.LabInstance.Id** and expand it. Then, expand
    **Lakehouse** - **DataFactoryLakehouse**  you created for the
    destination in the prior module, and select the **Bronze** table
    from the list then click on **Create** button.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image36.png)

    >[!note]**Note**: The canvas is now populated with the data.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image37.png)

### Task 2: Transform the data imported from the Lakehouse

1. Select the data type icon in the
    column header of the second column, **IpepPickupDatetime**, to
    display a dropdown menu and select the data type from the menu to
    convert the column from the **Date/Time** to **Date** type.
	
    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image38.png)

2.  On the **Home** tab of the ribbon, select
    the **Choose columns** option from the **Manage columns** group.
	
    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image39.png)

3.  On the **Choose columns** dialog, **deselect** the columns listed
    here, then select **OK**.

    - lpepDropoffDatetime

    - puLocationId

    - doLocationId

    - pickupLatitude

    - dropoffLongitude

    - rateCodeID

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image40.png)

4.  Select the **storeAndFwdFlag** column's filter and sort dropdown
    menu. (If you see a warning **List may be incomplete**,
    select **Load more** to see all the data.)

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image41.png)

5.  Select '**Y'** to show only rows where a discount was applied, and
    then select **OK**.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image42.png)

6.  Select the **Ipep_Pickup_Datetime** column sort and filter dropdown
    menu, then select **Date filters**, and choose
    the **Between...** filter provided for Date and Date/Time types.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image43.png)

7.  In the **Filter rows** dialog, select dates between **January 1,
    2019**, and **January 31, 2019**, then select **OK**.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image44.png)

### Task 3: Connect to a CSV file containing discount data

Now, with the data from the trips in place, we want to load the data
that contains the respective discounts for each day and VendorID, and
prepare the data before combining it with the trips data.

1.  From the **Home** tab in the dataflow editor menu, select the **Get
    data** option, and then choose **Text/CSV**.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image45.png)

2.  In the **Connect to data source** pane, under **Connection
    settings**, select **Link to file** radio button, then enter
    +++https://raw.githubusercontent.com/ekote/azure-architect/master/Generated-NYC-Taxi-Green-Discounts.csv+++. set the **Connection name** to **+++dfconnection+++**, and 
    make sure **authentication  kind** is set to **Anonymous**. click
    on the **Next** button.

    > ![](https://raw.githubusercontent.com/LODSContent/DPAI-120-msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/skillable_image1.png)

3.  On the **Preview file data** dialog, select **Create**.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image47.png)

### Task 4: Transform the discount data

1.  Reviewing the data, we see the headers appear to be in the first
    row. Promote them to headers by selecting the table's context menu
    at the top left of the preview grid area to select **Use first row
    as headers**.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image48.png)

    >[!note]**Note:** After promoting the headers, you can see a new step added
    > to the **Applied steps** pane at the top of the dataflow editor to the
    > data types of your columns.*

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image49.png)

2.  Right-click the **VendorID** column, and from the context menu
    displayed, select the option **Unpivot other columns**. This allows
    you to transform columns into attribute-value pairs, where columns
    become rows.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image50.png)

3.  With the table unpivoted, rename
    the **Attribute** and **Value** columns by double-clicking them and
    changing **Attribute** to **+++Date+++** and **Value** to **+++Discount+++**.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image51.png)

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image52.png)

4.  Change the data type of the **Date** column by selecting the data
    type menu to the left of the column name and choosing **Date**.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image53.png)

5.  Select the **Discount** column and then select the **Transform** tab
    on the menu. Select **Number column**, and then
    select **Standard** numeric transformations from the submenu, and
    choose **Divide**.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image54.png)

6.  On the **Divide** dialog, enter the value +++100+++, then click on
    **OK** button.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image55.png)

### Task 7: Combine trips and discounts data

The next step is to combine both tables into a single table that has the
discount that should be applied to the trip, and the adjusted total.

1.  First, toggle the **Diagram view** button so you can see both of
    your queries.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image56.png)

2.  Select the **Bronze** query, and on the **Home** tab, Select
    the **Combine** menu and choose **Merge queries**, then **Merge
    queries as new**.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image57.png)

3.  On the **Merge** dialog,
    select **Generated-NYC-Taxi-Green-Discounts** from the **Right table
    for merge** drop down, and then select the "**light bulb**" icon on
    the top right of the dialog to see the suggested mapping of columns
    between the three tables.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image58.png)

4.  Choose each of the two suggested column mappings, one at a time,
    mapping the VendorID and date columns from both tables. When both
    mappings are added, the matched column headers are highlighted in
    each table.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image59.png)

5.  A message is shown asking you to allow combining data from multiple
    data sources to view the results. Select **OK** 

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image60.png)

6.  In the table area, you'll initially see a warning that "The
    evaluation was canceled because combining data from multiple sources
    may reveal data from one source to another. Select continue if the
    possibility of revealing data is okay." Select **Continue** to
    display the combined data.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image61.png)

7.  In Privacy Levels dialog box, select the check box **Ignore Privacy
    Levels checks for this document. Ignoring privacy Levels could
    expose sensitive or confidential data to an unauthorized person**
    and click on the **Save** button.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image62.png)

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image63.png)

8.  Notice how a new query was created in Diagram view showing the
    relationship of the new Merge query with the two queries you
    previously created. Looking at the table pane of the editor, scroll
    to the right of the Merge query column list to see a new column with
    table values is present. This is the "Generated NYC
    Taxi-Green-Discounts" column, and its type is **\[Table\]**.

    In the column header there's an icon with two arrows going in opposite
    directions, allowing you to select columns from the table. Deselect all
    of the columns except **Discount**, and then select **OK**.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image64.png)

9.  With the discount value now at the row level, we can create a new
    column to calculate the total amount after discount. To do so,
    select the **Add column** tab at the top of the editor, and
    choose **Custom column** from the **General** group.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image65.png)

10. On the **Custom column** dialog, you can use the [Power Query
    formula language (also known as
    M)](https://learn.microsoft.com/en-us/powerquery-m) to define how
    your new column should be calculated.
    Enter +++**TotalAfterDiscount+++** for the **New column name**,
    select **Currency** for the **Data type**, and provide the following
    M expression for the **Custom column formula**:
    
	+++if [totalAmount] > 0 then [totalAmount] * ( 1 -[Discount] ) else [totalAmount]+++

    Then select **OK**.
    
    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image66.png)
      
    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image67.png)

12. Select the newly create **TotalAfterDiscount** column and then
    select the **Transform** tab at the top of the editor window. On
    the **Number column** group, select the **Rounding** drop down and
    then choose **Round...**.

    >[!note]**Note**: If you can't find the **rounding** option, expand the menu to see **Number column**.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image68.png)

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image69.png)

13. On the **Round** dialog, enter **2** for the
    number of decimal places and then select **OK**.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image70.png)

14.  Change the data type of the **IpepPickupDatetime** from **Date** to **Date/Time**.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image71.png)
	
    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image72.png)

15. Finally, expand the **Query settings** pane from the right side of
    the editor if it isn't already expanded, and rename the query
    from **Merge** to **+++Output+++**.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image73.png)

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image74.png)

### Task 8: Load the output query to a table in the Lakehouse

With the output query now fully prepared and with data ready to output,
we can define the output destination for the query.

1.  Select the **Output** merge query created previously. Then select
    the **+ icon** to add **data destination** to this Dataflow.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image75.png)

2.  From data destination list, select **Lakehouse** option under the
    New destination.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image76.png)

3.  On the **Connect to data destination** dialog, your connection
    should already be selected. Select **Next** to continue.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image77.png)

5.  On the **Choose destination target** dialog,
    browse to the Lakehouse where you wish to load the data and name the
    new table **+++nyc_taxi_with_discounts+++**, then select **Next** again.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image78.png)

6.  On the **Choose destination settings** dialog, leave the
    default **Replace** update method, double check that your columns
    are mapped correctly, and select **Save settings**.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image79.png)

7.  Back in the main editor window, confirm that you see your output
    destination on the **Query settings** pane for the **Output** table
    as **Lakehouse**, and then select **Save and Run** option from the
    Home tab.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image80.png)

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image81.png)

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image82.png)

7.  In the **Data_Factory@lab.LabInstance.Id** pane, select **DataFactoryLakehouse** to
    view the new table loaded there.

    > ![A screenshot of a computer AI-generated content may be incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image83.png)

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image84.png)

## Exercise 3: Automate and send notifications with Data Factory

### Task 1: Add an Office 365 Outlook activity to your pipeline

1.  Navigate and click on **Data_Factory@lab.LabInstance.Id** Workspace on the left-sided
    navigation menu.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image85.png)

2.  In the **Data_Factory@lab.LabInstance.Id** view, select the **First_Pipeline1**.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image86.png)

3.  Select the **Activities** tab in the pipeline editor and find the
    **Office Outlook** activity.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image87.png)

4.  Select and drag the On success path (a green checkbox on the top
    right side of the activity in the pipeline canvas) from your Copy
    activity to your new Office 365 Outlook activity.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image88.png)
	
5.  Select the Office 365 Outlook activity from
    the pipeline canvas, then select the **Settings** tab of the
    property area below the canvas to configure the email. Click on
    **Sign in** button.

6. Select your Power BI organizational account
    and then select **Allow access** to confirm.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image89.png)
	
7.  Select the Office 365 Outlook activity from the pipeline canvas, on
    the **Settings** tab of the property area below the canvas to
    configure the email.

    - Enter your email address in the **To** section. If you want to use
      several addresses, use **;** to separate them.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image90.png)

    - For the **Subject**, select the field so
      that the **Add dynamic content** option appears, and then select
      it to display the pipeline expression builder canvas.

8.  The **Pipeline expression builder** dialog appears. Enter the
    following expression, then select **OK**:

    +++@concat('DI in an Hour Pipeline Succeeded with Pipeline Run Id', pipeline().RunId)+++
 
    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image91.png)

9.  For the **Body**, select the field again and choose the **View in
    expression builder** option when it appears below the text area. Add
    the following expression again in the **Pipeline expression
    builder** dialog that appears, then select **OK**:

    +++@concat('RunID = ', pipeline().RunId, ' ; ', 'Copied rows ', activity('Copy data1').output.rowsCopied, ' ; ','Throughput ', activity('Copy data1').output.throughput)+++

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image92.png)

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image93.png)

    >[!note]**Note**: Replace **Copy data1** with the name of your own pipeline copy activity.

10. Finally select the **Home** tab at the top of the pipeline editor,
    and choose **Run**. Then select **Save and run** again on the
    confirmation dialog to execute these activities.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image94.png)

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image95.png)

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image96.png)

11. After the pipeline runs successfully, check your email to find the
    confirmation email sent from the pipeline.

    > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image97.png)

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image98.png)

### Task 2: Schedule pipeline execution

Once you finish developing and testing your pipeline, you can schedule
it to execute automatically.

1.  On the **Home** tab of the pipeline editor window,
    select **Schedule**.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image99.png)

2.  Configure the schedule as required. The example here schedules the
    pipeline to execute daily at 8:00 PM until the end of the year.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image100.png)

### Task 3: Add a Dataflow activity to the pipeline

1.  Hover over the green line connecting the **Copy activity** and the
    **Office 365 Outlook** activity on your pipeline canvas, and select
    the **+** button to insert a new activity.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image101.png)

2.  Choose **Dataflow** from the menu that
    appears.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image102.png)

3.  The newly created Dataflow activity is inserted between the Copy
    activity and the Office 365 Outlook activity, and selected
    automatically, showing its properties in the area below the canvas.
    Select the **Settings** tab on the properties area, and then select
    your dataflow created in **Exercise 2: Transform data with a
    dataflow in Data Factory**.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image103.png)

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image104.png)
	
4.  Select the **Home** tab at the top of the
    pipeline editor, and choose **Run**. Then select **Save and
    run** again on the confirmation dialog to execute these activities.

     > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image105.png)

     > ![A screenshot of a computer Description automatically generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image106.png)

### Task 4: Clean up resources

You can delete individual reports, pipelines, warehouses, and other
items or remove the entire workspace. Use the following steps to delete
the workspace you created for this tutorial.

1.  Select your workspace, the **Data-Factory@lab.LabInstance.Id** from the left-hand
    navigation menu. It opens the workspace item view.

    > ![](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image107.png)

2.  Select the  **Workspace settings** option on the workspace page
    located at the top right corner.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image108.png)

3.  Navigate to the bottom of the **General** tab and select **Remove this workspace**.

    > ![A screenshot of a computer Description automatically
    > generated](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2002/media/image109.png)


4.  Click on **Delete** in the warning that pops up.

    > ![A white background with black text AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2003/media/image63.png)

    > ![A screenshot of a computer AI-generated content may be
    > incorrect.](https://raw.githubusercontent.com/technofocus-pte/msfbrcanlytcsrio/refs/heads/Cloud-slice/Labguide/Usecase%2003/media/image64.png)
