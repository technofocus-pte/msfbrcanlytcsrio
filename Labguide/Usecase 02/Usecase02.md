# Use case 02: Data Factory solution for moving and transforming data with dataflows and data pipelines

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

- **Exercise 2:** Transform data with a dataflow in Data Factory to
  process the raw data from your bronze table and move it to a Gold
  table in the data Lakehouse.

- **Exercise 3:** Automate and send notifications with Data Factory to
  send an email to notify you once all the jobs are complete, and
  finally, setup the entire flow to run on a scheduled basis.

# Exercise 1: Create a pipeline with Data Factory

## Task 1: Create a workspace

Before working with data in Fabric, create a workspace with the Fabric
trial enabled.

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL: +++https://app.fabric.microsoft.com/+++ then
    press the **Enter** button.

> **Note**: If you are directed to Microsoft Fabric Home page, then skip
> steps from \#2 to \#4.
>
> ![](./media/image1.png)

2.  In the **Microsoft Fabric** window, enter your credentials, and
    click on the **Submit** button.

> ![](./media/image2.png)

3.  Then, In the **Microsoft** window enter the password and click on
    the **Sign in** button**.**

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image3.png)

4.  In **Stay signed in?** window, click on the **Yes** button.

> ![A screenshot of a computer error AI-generated content may be
> incorrect.](./media/image4.png)
>
> ![](./media/image5.png)

5.  On the Microsoft **Fabric Home Page**, select **New workspace**
    option.

> ![](./media/image6.png)

6.  In the **Create a workspace** tab, enter the following details and
    click on the **Apply** button.

[TABLE]

> ![](./media/image7.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

7.  Wait for the deployment to complete. It’ll take approximately 2-3
    minutes.

> ![A screenshot of a computer Description automatically
> generated](./media/image9.png)

## Task 2: Create a lakehouse and Ingest sample data

1.  In the **Data-FactoryXX** workspace page, navigate and click on
    **+New item**  button

> ![A screenshot of a computer Description automatically
> generated](./media/image10.png)

2.  Click on the "**Lakehouse**" tile.

![A screenshot of a computer Description automatically
generated](./media/image11.png)

3.  In the **New lakehouse** dialog box, enter
    +++**DataFactoryLakehouse+++** in the **Name** field, click on the
    **Create** button and open the new lakehouse.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image12.png)
>
> ![](./media/image13.png)

4.  In the **lakehouse** home page, select **Start with sample data** to
    open the copy sample data

> ![](./media/image14.png)

5.  The **Use a sample** dialog is displayed, select the **NYCTaxi**
    sample data tile.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image15.png)
>
> ![](./media/image16.png)
>
> ![](./media/image17.png)

6.  To rename the table, right-click on the **green_tripdata_2022** tab
    just above the editor and select **Rename**.

![A screenshot of a computer Description automatically
generated](./media/image18.png)

7.  In the **Rename** dialog box, under **Name** field,
    enter **+++Bronze+++** to change the name of **table**. Then, click
    on the **Rename** button.

![A screenshot of a computer Description automatically
generated](./media/image19.png)

![A screenshot of a computer Description automatically
generated](./media/image20.png)

**Exercise 2: Transform data with a dataflow in Data Factory**

## Task 1: Get data from a Lakehouse table

1.  Now, click on workspace [**Data
    Factory-@lab.LabInstance.Id**](mailto:Data%20Factory-@lab.LabInstance.Id) on
    the left-sided navigation pane.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image21.png)

2.  Create a new Dataflow Gen2 by clicking on the **+New item** button
    in the navigation bar. From the list of available items select
    the **Dataflow Gen2** item

> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

3.  Provide a New Dataflow Gen2 Name as
    +++**nyc_taxi_data_with_discounts+++** and then select **Create**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image23.png)

4.  From the new dataflow menu, under the **Power Query** pane click on
    **Get data drop down**, then select **More...**.

> ![A screenshot of a computer Description automatically
> generated](./media/image24.png)

5.  In the **Choose data source** tab, search box search type
    +++**Lakehouse+++** and then click on the **Lakehouse** connector.

> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

6.  The **Connect to data source** dialog appears, and a new connection
    is automatically created for you based on the currently signed in
    user. Select **Next**.

> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)

7.  The **Choose data** dialog is displayed. Use the navigation pane to
    find the **workspace- Data-FactoryXX** and expand it. Then, expand
    **Lakehouse** - **DataFactoryLakehouse**  you created for the
    destination in the prior module, and select the **Bronze** table
    from the list then click on **Create** button.

![A screenshot of a computer Description automatically
generated](./media/image27.png)

8.  You’ll see the canvas is now populated with the data.

![A screenshot of a computer Description automatically
generated](./media/image28.png)

## Task 2: Transform the data imported from the Lakehouse

1.  Select the data type icon in the column header of the second
    column, **IpepPickupDatetime**, to display a dropdown menu and
    select the data type from the menu to convert the column from
    the **Date/Time** to **Date** type.

![A screenshot of a computer Description automatically
generated](./media/image29.png)

2.  On the **Home** tab of the ribbon, select the **Choose
    columns** option from the **Manage columns** group.

![A screenshot of a computer Description automatically
generated](./media/image30.png)

3.  On the **Choose columns** dialog, **deselect** some columns listed
    here, then select **OK**.

    - lpepDropoffDatetime

    &nbsp;

    - DoLocationID

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image31.png)

4.  Select the **storeAndFwdFlag** column's filter and sort dropdown
    menu. (If you see a warning **List may be incomplete**,
    select **Load more** to see all the data.)

> ![A screenshot of a computer Description automatically
> generated](./media/image32.png)

5.  Select '**Y'** to show only rows where a discount was applied, and
    then select **OK**.

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

6.  Select the **Ipep_Pickup_Datetime** column sort and filter dropdown
    menu, then select **Date filters**, and choose
    the **Between...** filter provided for Date and Date/Time types.

![](./media/image34.png)

7.  In the **Filter rows** dialog, select dates between **January 1,
    2022**, and **January 31, 2022**, then select **OK**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image35.png)

## Task 3: Connect to a CSV file containing discount data

Now, with the data from the trips in place, we want to load the data
that contains the respective discounts for each day and VendorID and
prepare the data before combining it with the trips data.

1.  From the **Home** tab in the dataflow editor menu, select the **Get
    data** option, and then choose **Text/CSV**.

> ![](./media/image36.png)

2.  In the **Connect to data source** pane, under **Connection
    settings**, select **Link to file** radio button, then enter
    +++https://raw.githubusercontent.com/ekote/azure-architect/master/Generated-NYC-Taxi-Green-Discounts.csv+++
    and enter the Connection name as +++**dfconnection**+++ make sure
    **authentication** **kind** is set to **Anonymous**. click on the
    **Next** button.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image37.png)

3.  On the **Preview file data** dialog, select **Create**.

![A screenshot of a computer Description automatically
generated](./media/image38.png)

## Task 4: Transform the discount data

1.  Reviewing the data, we see the headers appear to be in the first
    row. Promote them to headers by selecting the table's context menu
    at the top left of the preview grid area to select **Use first row
    as headers**.

> ![](./media/image39.png)
>
> ***Note:** After promoting the headers, you can see a new step added
> to the **Applied steps** pane at the top of the dataflow editor to the
> data types of your columns.*
>
> ![](./media/image40.png)

2.  Right-click the **VendorID** column, and from the context menu
    displayed, select the option **Unpivot other columns**. This allows
    you to transform columns into attribute-value pairs, where columns
    become rows.

![A screenshot of a computer Description automatically
generated](./media/image41.png)

3.  With the table unpivoted, rename
    the **Attribute** and **Value** columns by double-clicking them and
    changing **Attribute** to +++**Date+++** and **Value** to +++**Discount+++**.

![A screenshot of a computer Description automatically
generated](./media/image42.png)

![A screenshot of a computer Description automatically
generated](./media/image43.png)

4.  Change the data type of the **Date** column by selecting the data
    type menu to the left of the column name and choosing **Date**.

> ![A screenshot of a computer Description automatically
> generated](./media/image44.png)

5.  Select the **Discount** column and then select the **Transform** tab
    on the menu. Select **Number column**, and then
    select **Standard** numeric transformations from the submenu, and
    choose **Divide**.

> ![A screenshot of a computer Description automatically
> generated](./media/image45.png)

6.  On the **Divide** dialog, enter the value +++100+++, then click on
    **OK** button.

![A screenshot of a computer Description automatically
generated](./media/image46.png)

![A screenshot of a computer Description automatically
generated](./media/image47.png)

**Task 7: Combine trips and discounts data**

The next step is to combine both tables into a single table that has the
discount that should be applied to the trip, and the adjusted total.

1.  First, toggle the **Diagram view** button so you can see both of
    your queries.

![A screenshot of a computer Description automatically
generated](./media/image48.png)

2.  Select the **Bronze** query, and on the **Home** tab, Select
    the **Combine** menu and choose **Merge queries**, then **Merge
    queries as new**.

![](./media/image49.png)

3.  On the **Merge** dialog,
    select **Generated-NYC-Taxi-Green-Discounts** from the **Right table
    for merge** drop down, and then select the "**light bulb**" icon on
    the top right of the dialog to see the suggested mapping of columns
    between the three tables.

4.  Choose each of the two suggested column mappings, one at a time,
    mapping the VendorID and date columns from both tables. When both
    mappings are added, the matched column headers are highlighted in
    each table.

> ![](./media/image50.png)

5.  A message is shown asking you to allow combining data from multiple
    data sources to view the results. Select **OK** 

> ![A screenshot of a computer Description automatically
> generated](./media/image51.png)

6.  In the table area, you'll initially see a warning that "The
    evaluation was canceled because combining data from multiple sources
    may reveal data from one source to another. Select continue if the
    possibility of revealing data is okay." Select **Continue** to
    display the combined data.

> ![](./media/image52.png)

7.  In Privacy Levels dialog box, select the **check box :Ignore Privacy
    Levels checks for this document. Ignoring privacy Levels could
    expose sensitive or confidential data to an unauthorized person**
    and click on the **Save** button.

> ![A screenshot of a computer screen Description automatically
> generated](./media/image53.png)
>
> ![](./media/image54.png)

8.  Notice how a new query was created in Diagram view showing the
    relationship of the new Merge query with the two queries you
    previously created. Looking at the table pane of the editor, scroll
    to the right of the Merge query column list to see a new column with
    table values is present. This is the "**Generated NYC
    Taxi-Green-Discounts**" column, and its type is **\[Table\]**.

In the column header there's an icon with two arrows going in opposite
directions, allowing you to select columns from the table. Deselect all
of the columns except **Discount**, and then select **OK**.

![](./media/image55.png)

9.  With the discount value now at the row level, we can create a new
    column to calculate the total amount after discount. To do so,
    select the **Add column** tab at the top of the editor, and
    choose **Custom column** from the **General** group.

> ![](./media/image56.png)

10. On the **Custom column** dialog, you can use the [Power Query
    formula language (also known as
    M)](https://learn.microsoft.com/en-us/powerquery-m) to define how
    your new column should be calculated.
    Enter +++**TotalAfterDiscount+++** for the **New column name**,
    select **Currency** for the **Data type**, and provide the following
    M expression for the **Custom column formula**:

> +++if \[total_amount\] \> 0 then \[total_amount\] \* ( 1 -\[Discount\]
> ) else \[total_amount\]+++

Then select **OK**.

![](./media/image57.png)

![A screenshot of a computer Description automatically
generated](./media/image58.png)

11. Select the newly create **TotalAfterDiscount** column and then
    select the **Transform** tab at the top of the editor window. On
    the **Number column** group, select the **Rounding** drop down and
    then choose **Round...**.

**Note**: If you can’t find the **rounding** option, expand the menu to
see **Number column**.

![](./media/image59.png)

12. On the **Round** dialog, enter **2** for the number of decimal
    places and then select **OK**.

![A screenshot of a computer Description automatically
generated](./media/image60.png)

13. Change the data type of the **IpepPickupDatetime** from **Date** to
    **Date/Time**.

![](./media/image61.png)

14. Finally, expand the **Query settings** pane from the right side of
    the editor if it isn't already expanded, and rename the query
    from **Merge** to +++**Output+++**.

![A screenshot of a computer Description automatically
generated](./media/image62.png)

![A screenshot of a computer Description automatically
generated](./media/image63.png)

**Task 8: Load the output query to a table in the Lakehouse**

With the output query now fully prepared and with data ready to output,
we can define the output destination for the query.

1.  Select the **Output** merge query created previously. Then select
    the **+ icon** to add **data destination** to this Dataflow.

2.  From data destination list, select **Lakehouse** option under the
    New destination.

![](./media/image64.png)

3.  On the **Connect to data destination** dialog, your connection
    should already be selected. Select **Next** to continue.

![A screenshot of a computer Description automatically
generated](./media/image65.png)

4.  On the **Choose destination target** dialog, browse to the
    Lakehouse, then select **Next** again.

![](./media/image66.png)

5.  On the **Choose destination settings** dialog, leave the
    default **Replace** update method, double check that your columns
    are mapped correctly, and select **Save settings**.

![](./media/image67.png)

6.  Back in the main editor window, confirm that you see your output
    destination on the **Query settings** pane for the **Output** table
    as **Lakehouse**, and then select **Save and Run** option from the
    Home tab.

> ![](./media/image68.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image69.png)

9.  Now, click on **Data Factory-XXXX workspace** on the left-sided
    navigation pane.

> ![A screenshot of a computer Description automatically
> generated](./media/image70.png)

10. In the **Data_FactoryXX** pane, select **DataFactoryLakehouse** to
    view the new table loaded there.

![](./media/image71.png)

11. Confirm that the **Output** table appears under the **dbo** schema.

![](./media/image72.png)

# Exercise 3: Automate and send notifications with Data Factory

## Task 1: Add an Office 365 Outlook activity to your pipeline

1.  Navigate and click on **Data_FactoryXX** Workspace on the left-sided
    navigation menu.

> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)

2.  Select the **+ New item** option on the workspace page and select
    **Pipeline**

> ![A screenshot of a computer Description automatically
> generated](./media/image74.png)

3.  Provide a Pipeline Name as +++**First_Pipeline1+++** and then select
    **Create**.

> ![](./media/image75.png)

4.  Select the **Home** tab in the pipeline editor and find the select
    **Add to canvas** activity.

> ![](./media/image76.png)

5.  On the **Source** tab, enter the following settings and click on
    **Test connection**

[TABLE]

> ![](./media/image77.png)

6.  On the **Destination** tab, enter the following settings.

[TABLE]

> ![](./media/image78.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image79.png)

7.  From the ribbon, select **Run**.

> ![](./media/image80.png)

8.  In the **Save and run?** dialog box, click on **Save and
    run** button.

> ![A screenshot of a computer Description automatically
> generated](./media/image81.png)
>
> ![](./media/image82.png)

9.  Select the **Activities** tab in the pipeline editor and find the
    **Office Outlook** activity.

> ![A screenshot of a computer Description automatically
> generated](./media/image83.png)

10. Select and drag the On Success path (a green checkbox on the top
    right side of the activity in the pipeline canvas) from your Copy
    activity to your new Office 365 Outlook activity.

![A screenshot of a computer Description automatically
generated](./media/image84.png)

11. Select the Office 365 Outlook activity from the pipeline canvas,
    then select the **Settings** tab of the property area below the
    canvas to configure the email. Click on **Connection** dropdown and
    select **Browse all.**

> ![A screenshot of a computer Description automatically
> generated](./media/image85.png)

12. On ‘choose a data source’ window, select **Office 365 Email**
    source.

> ![A screenshot of a computer Description automatically
> generated](./media/image86.png)

13. Sign in with the account from which you want to send the email. You
    can use the existing connection with the already signed in account.

> ![A screenshot of a computer Description automatically
> generated](./media/image87.png)

14. Click on **Connect** to proceed.

> ![A screenshot of a computer Description automatically
> generated](./media/image88.png)

15. Select the Office 365 Outlook activity from the pipeline canvas, on
    the **Settings** tab of the property area below the canvas to
    configure the email.

    - Enter your email address in the **To** section. If you want to use
      several addresses, use **;** to separate them.

> ![A screenshot of a computer Description automatically
> generated](./media/image89.png)

- For the **Subject**, select the field so that the **Add dynamic
  content** option appears, and then select it to display the pipeline
  expression builder canvas.

> ![A screenshot of a computer Description automatically
> generated](./media/image90.png)

16. The **Pipeline expression builder** dialog appears. Enter the
    following expression, then select **OK**:

> *+++@concat('DI in an Hour Pipeline Succeeded with Pipeline Run Id',
> pipeline().RunId)+++*
>
> ![](./media/image91.png)

17. For the **Body**, select the field again and choose the **View in
    expression builder** option when it appears below the text area. Add
    the following expression again in the **Pipeline expression
    builder** dialog that appears, then select **OK**:

> *+++@concat('RunID = ', pipeline().RunId, ' ; ', 'Copied rows ',
> activity('Copy data1').output.rowsCopied, ' ; ','Throughput ',
> activity('Copy data1').output.throughput)+++*
>
> ![](./media/image92.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image93.png)

**  Note:** Replace **Copy data1** with the name of your own pipeline
copy activity.

18. Finally select the **Home** tab at the top of the pipeline editor,
    and choose **Run**. Then select **Save and run** again on the
    confirmation dialog to execute these activities.

> ![A screenshot of a computer Description automatically
> generated](./media/image94.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image95.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image96.png)

19. After the pipeline runs successfully, check your email to find the
    confirmation email sent from the pipeline.

![](./media/image97.png)

**Task 2: Schedule pipeline execution**

Once you finish developing and testing your pipeline, you can schedule
it to execute automatically.

1.  On the **Home** tab of the pipeline editor window,
    select **Schedule**.

![A screenshot of a computer Description automatically
generated](./media/image98.png)

2.  Configure the schedule as required. The example here schedules the
    pipeline to execute daily at 8:00 PM until the end of the year.

![A screenshot of a schedule Description automatically
generated](./media/image99.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image100.png)

![A screenshot of a schedule AI-generated content may be
incorrect.](./media/image101.png)

**Task 3: Add a Dataflow activity to the pipeline**

1.  Hover over the green line connecting the **Copy activity** and the
    **Office 365 Outlook** activity on your pipeline canvas, and select
    the **+** button to insert a new activity.

> ![A screenshot of a computer Description automatically
> generated](./media/image102.png)

2.  Choose **Dataflow** from the menu that appears.

![A screenshot of a computer Description automatically
generated](./media/image103.png)

3.  The newly created Dataflow activity is inserted between the Copy
    activity and the Office 365 Outlook activity, and selected
    automatically, showing its properties in the area below the canvas.
    Select the **Settings** tab on the properties area, and then select
    your dataflow created in **Exercise 2: Transform data with a
    dataflow in Data Factory**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image104.png)

4.  Select the **Home** tab at the top of the pipeline editor, and
    choose **Run**. Then select **Save and run** again on the
    confirmation dialog to execute these activities.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image105.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image106.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image107.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image108.png)

## Task 4: Clean up resources

You can delete individual reports, pipelines, warehouses, and other
items or remove the entire workspace. Use the following steps to delete
the workspace you created for this tutorial.

1.  Select your workspace, the **Data-FactoryXX** from the left-hand
    navigation menu. It opens the workspace item view.

![](./media/image109.png)

2.  Select the  **Workspace settings** option on the workspace page
    located at the top right corner.

![A screenshot of a computer Description automatically
generated](./media/image110.png)

3.  Select **General tab** and **Remove this workspace.**

![A screenshot of a computer Description automatically
generated](./media/image111.png)
