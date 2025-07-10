## Use Case 02 - Perform Sentiment analysis and Text translation with AI functions in Microsoft Fabric

**Introduction**

Microsoft Fabric now offers powerful AI functions—such as similarity
scoring, classification, sentiment analysis, entity extraction, grammar
correction, summarization, translation, and custom response
generation—that can be seamlessly applied to data within pandas or Spark
with a single line of code. These are built on industry-leading LLMs and
are available with minimal setup, enabling data scientists and analysts
to effortlessly enhance, transform, and analyze textual data as part of
their data engineering and data science workflows

**Objective**

- Set up the Microsoft Fabric notebook environment with required
  packages and configurations.

- Import and explore sample data using pandas or Spark DataFrames.

- Apply AI functions like similarity scoring, classification, and
  sentiment analysis to text columns.

- Use functions for grammar correction, summarization, and translation
  on textual data.

- Generate AI-based custom responses using generate_response for various
  prompts.

- Configure AI function behavior using ai func.Conf for custom settings
  like temperature or timeout.

- Evaluate and compare original vs AI-transformed outputs to understand
  their impact.

## Exercise 1: Create a workspace, lakehouse and notebook

### Task 1: Create a workspace

1.  In the Workspaces pane Select **+** **New Workspace**. 

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image1.png)

    |  |   |
    |----|----|
    |Name|	+++AI-FunctionsXXXX+++ (XX XXcan be a unique number) |
    |Advanced|	Under License mode, select Fabric capacity |
    |Default	storage format |Small dataset storage format|


2.  In the **Create a workspace tab**, enter the following details and
    click on the **Apply** button.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image2.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image3.png)

3.  Wait for the deployment to complete. It takes 1-2 minutes to
    complete. When your new workspace opens, it should be empty.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image4.png)

### Task 2: Create a lakehouse

1.  In the Workspaces pane, select **+ New item**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image5.png)

4.  In the **Filter by item type** search box, enter **+++Lakehouse+++**
    and select the lakehouse item.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image6.png)

5.  Enter **+++AI_Functions+++**as the lakehouse name and
    select **Create**. When provisioning is complete, the lakehouse
    explorer page is shown.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image7.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

### Task 3: Create a Notebook and Install the AI Functions Library

Using AI functions in Fabric notebooks requires certain custom packages,
which are preinstalled on the Fabric runtime. For the latest features
and bugfixes, you can run the following code to install and import the
most up-to-date packages. Afterward, you can use AI functions with
pandas or PySpark, depending on your preference.

1.  On the **Home** page, select **Open notebook** menu and select **New
    notebook**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image9.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image10.png)

2.   Replace all the code in the **cell** with the following code and
    click on **▷ Run cell** button and review the output.
    ```
    # Install fixed version of packages
    %pip install -q --force-reinstall openai==1.30 httpx==0.27.0
    
    # Install latest version of SynapseML-core
    %pip install -q --force-reinstall https://mmlspark.blob.core.windows.net/pip/1.0.11-spark3.5/synapseml_core-1.0.11.dev1-py2.py3-none-any.whl
    
    # Install SynapseML-Internal .whl with AI functions library from blob storage:
    %pip install -q --force-reinstall https://mmlspark.blob.core.windows.net/pip/1.0.11.1-spark3.5/synapseml_internal-1.0.11.1.dev1-py2.py3-none-any.whl
    ```

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image11.png)
>
> ![A screenshot of a computer code AI-generated content may be
> incorrect.](./media/image12.png)
>
> **Note:** It can happen that the notebook will throw some errors in
> cell 1. These errors are caused by libaries that already have been
> installed in the environment. You can safely ignore these errors. The
> notebook will execute successfully regardless of these errors.
>
> ![](./media/image13.png)

3.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

4.  This code cell imports the AI functions library and its
    dependencies. The pandas cell also imports an optional Python
    library to display progress bars that track the status of every AI
    function call.
    ```
    # Required imports
    import synapse.ml.aifunc as aifunc
    import pandas as pd
    import openai
    
    # Optional import for progress bars
    from tqdm.auto import tqdm
    tqdm.pandas()
    ```
> ![](./media/image14.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image15.png)

## Exercise 2: Applying AI functions

### Task 1: Calculate similarity with ai.similarity

The ai.similarity function invokes AI to compare input text values with
a single common text value, or with pairwise text values in another
column. The output similarity scores are relative, and they can range
from **-1** (opposites) to **1** (identical). A score of **0** indicates
that the values are completely unrelated in meaning.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

   > PythonCopy
    ```
    # This code uses AI. Always review output for mistakes. 
    # Read terms: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
    df = pd.DataFrame([ 
            ("Bill Gates", "Microsoft"), 
            ("Satya Nadella", "Toyota"), 
            ("Joan of Arc", "Nike") 
        ], columns=["names", "companies"])
        
    df["similarity"] = df["names"].ai.similarity(df["companies"])
    display(df)
    ```
> ![A screenshot of a computer code AI-generated content may be
> incorrect.](./media/image16.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image17.png)

### Task 2: Categorize text with ai.classify

The ai.classify function invokes AI to categorize input text according
to custom labels

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

   > PythonCopy

    ```
    # This code uses AI. Always review output for mistakes. 
    # Read terms: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
    
    df = pd.DataFrame([
            "This duvet, lovingly hand-crafted from all-natural fabric, is perfect for a good night's sleep.",
            "Tired of friends judging your baking? With these handy-dandy measuring cups, you'll create culinary delights.",
            "Enjoy this *BRAND NEW CAR!* A compact SUV perfect for the professional commuter!"
        ], columns=["descriptions"])
    
    df["category"] = df['descriptions'].ai.classify("kitchen", "bedroom", "garage", "other")
    display(df)
    ```

![](./media/image18.png)

### Task 3: Detect sentiment with ai.analyze_sentiment

The ai.analyze_sentiment function invokes AI to identify whether the
emotional state expressed by input text is positive, negative, mixed, or
neutral. If AI can't make this determination, the output is left blank.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output


    ```
    # This code uses AI. Always review output for mistakes. 
    # Read terms: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
    
    df = pd.DataFrame([
            "The cleaning spray permanently stained my beautiful kitchen counter. Never again!",
            "I used this sunscreen on my vacation to Florida, and I didn't get burned at all. Would recommend.",
            "I'm torn about this speaker system. The sound was high quality, though it didn't connect to my roommate's phone.",
            "The umbrella is OK, I guess."
        ], columns=["reviews"])
    
    df["sentiment"] = df["reviews"].ai.analyze_sentiment()
    display(df)
    ```
 
> ![](./media/image19.png)

### Task 4: Extract entities with ai.extract

The ai.extract function invokes AI to scan input text and extract
specific types of information designated by labels you choose—for
example, locations or names.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output

    ```
    # This code uses AI. Always review output for mistakes. 
    # Read terms: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
    
    df = pd.DataFrame([
            "MJ Lee lives in Tuscon, AZ, and works as a software engineer for Microsoft.",
            "Kris Turner, a nurse at NYU Langone, is a resident of Jersey City, New Jersey."
        ], columns=["descriptions"])
    
    df_entities = df["descriptions"].ai.extract("name", "profession", "city")
    display(df_entities)
    ```
> ![](./media/image20.png)

### Task 5: Fix grammar with ai.fix_grammar

The ai.fix_grammar function invokes AI to correct the spelling, grammar,
and punctuation of input text.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output
    ```
    # This code uses AI. Always review output for mistakes. 
    # Read terms: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
    
    df = pd.DataFrame([
            "There are an error here.",
            "She and me go weigh back. We used to hang out every weeks.",
            "The big picture are right, but you're details is all wrong."
        ], columns=["text"])
    
    df["corrections"] = df["text"].ai.fix_grammar()
    display(df)
    ```
> ![](./media/image21.png)

### Task 6: Summarize text with ai.summarize

The ai.summarize function invokes AI to generate summaries of input text
(either values from a single column of a DataFrame, or row values across
all the columns).
1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷ Run cell** button and review the output
    ```
    # This code uses AI. Always review output for mistakes. 
    # Read terms: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
    
    df= pd.DataFrame([
            ("Microsoft Teams", "2017",
            """
            The ultimate messaging app for your organization—a workspace for real-time 
            collaboration and communication, meetings, file and app sharing, and even the 
            occasional emoji! All in one place, all in the open, all accessible to everyone.
            """),
            ("Microsoft Fabric", "2023",
            """
            An enterprise-ready, end-to-end analytics platform that unifies data movement, 
            data processing, ingestion, transformation, and report building into a seamless, 
            user-friendly SaaS experience. Transform raw data into actionable insights.
            """)
        ], columns=["product", "release_year", "description"])
    
    df["summaries"] = df["description"].ai.summarize()
    display(df)
    ```
![A screenshot of a computer AI-generated content may be
incorrect.](./media/image22.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image23.png)

### Task 7: Translate text with ai.translate

The ai.translate function invokes AI to translate input text to a new
language of your choice.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output
    ```
    # This code uses AI. Always review output for mistakes. 
    # Read terms: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
    
    df = pd.DataFrame([
            "Hello! How are you doing today?", 
            "Tell me what you'd like to know, and I'll do my best to help.", 
            "The only thing we have to fear is fear itself."
        ], columns=["text"])
    
    df["translations"] = df["text"].ai.translate("spanish")
    display(df)
    ```
> ![](./media/image24.png)

### Task 8: Answer custom user prompts with ai.generate_response

The **ai.generate**\_response function invokes AI to generate custom
text based on your own instructions.

1.  Use the **+ Code** icon below the cell output to add a new code cell
    to the notebook, and enter the following code in it. Click on **▷
    Run cell** button and review the output
    ```
    # This code uses AI. Always review output for mistakes. 
    # Read terms: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
    
    df = pd.DataFrame([
            ("Scarves"),
            ("Snow pants"),
            ("Ski goggles")
        ], columns=["product"])
    
    df["response"] = df.ai.generate_response("Write a short, punchy email subject line for a winter sale.")
    display(df)
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image25.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image26.png)

### Task 9: Clean up resources

1.  Now, click on **AI-FunctionsXXXX** on the left-sided navigation
    pane.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image27.png)

2.  Select the **...** option under the workspace name and
    select **Workspace settings**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image28.png)

3.  Select **Other** and **Remove this workspace.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image29.png)

4.  Click on **Delete** in the warning that pops up.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image30.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image31.png)
>
> **Summary**
>
> In this lab, you explored Microsoft Fabric's built-in AI functions
> that allow seamless integration of powerful language models into data
> workflows. Using both pandas and Spark DataFrames, you applied
> functions such as similarity scoring, classification, sentiment
> analysis, grammar correction, summarization, translation, entity
> extraction, and response generation—all with minimal code. You also
> learned how to customize these functions using configuration settings
> to control model behavior, such as temperature and concurrency.
> Finally, the lab demonstrated how to connect to a custom Azure OpenAI
> endpoint, offering flexibility for enterprise deployments. Overall,
> this lab showcased how Microsoft Fabric simplifies the use of
> generative AI for data scientists and analysts, enabling smarter and
> faster data transformation and analysis.
