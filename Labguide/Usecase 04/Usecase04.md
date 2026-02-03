# Caso de uso 04: Analisar dados com o Apache Spark

**Introdução**

O Apache Spark é um motor de código aberto para processamento
distribuído de dados, amplamente utilizado para explorar, processar e
analisar grandes volumes de dados em armazenamento de data lake. O Spark
está disponível como uma opção de processamento em muitos produtos de
plataforma de dados, incluindo Azure HDInsight, Azure Databricks, Azure
Synapse Analytics e Microsoft Fabric. Um dos benefícios do Spark é o
suporte a uma ampla variedade de linguagens de programação, incluindo
Java, Scala, Python e SQL, tornando o Spark uma solução muito flexível
para cargas de trabalho de processamento de dados, incluindo limpeza e
manipulação de dados, análise estatística e aprendizado de máquina, além
de análise e visualização de dados.

As tabelas em um lakehouse do Microsoft Fabric são baseadas no formato
Delta Lake de código aberto para o Apache Spark. O Delta Lake adiciona
suporte à semântica relacional para operações de dados em lote e
streaming e permite a criação de uma arquitetura Lakehouse na qual o
Apache Spark pode ser usado para processar e consultar dados em tabelas
baseadas em arquivos subjacentes em um data lake.

No Microsoft Fabric, os Dataflows (Gen2) conectam-se a várias fontes de
dados e realizam transformações no Power Query Online. Podem então ser
usados em pipelines de dados para ingestão de dados em um lakehouse ou
outro armazenamento analítico, ou para definir um conjunto de dados para
um relatório do Power BI.

Este laboratório foi projetado para apresentar os diferentes elementos
dos Dataflows (Gen2), e não para criar uma solução complexa que possa
existir em uma empresa.

**Objetivos**:

• Criar um espaço de trabalho no Microsoft Fabric com a versão de
avaliação do Fabric ativada.

• Estabelecer um ambiente lakehouse e carregar arquivos de dados para
análise.

• Gerar um bloco de notas para exploração e análise interativa de dados.

• Carregar os dados em uma estrutura de dados para processamento e
visualização adicionais.

• Aplicar transformações aos dados usando PySpark.

• Guardar e particionar os dados transformados para otimizar as
consultas.

• Criar uma tabela no metastore do Spark para gerenciamento estruturado
de dados.

• Guardar a estrutura de dados como uma tabela delta gerenciada chamada
“salesorders”.

• Salvar estrutura de dados como uma tabela delta externa chamada
“external_salesorder” com um caminho especificado.

• Descrever e comparar as propriedades das tabelas gerenciadas e
externas.

• Executar consultas SQL nas tabelas para análise e geração de
relatórios.

• Visualizar os dados usando bibliotecas Python, como matplotlib e
seaborn.

• Estabelecer um data lakehouse na experiência de engenharia de dados e
ingestão de dados relevantes para análise posterior.

• Definir um fluxo de dados para extrair, transformar e carregar dados
no lakehouse.

• Configurar destinos de dados no Power Query para armazenar os dados
transformados no lakehouse.

• Incorporar o fluxo de dados em um pipeline para permitir o
processamento e a ingestão programados de dados.

• Remover o espaço de trabalho e os elementos associados para concluir o
exercício.

# Exercício 1: Criar um espaço de trabalho, lakehouse, bloco de notas e carregando dados na estrutura de dados.

## Tarefa 1: Criar um espaço de trabalho

Antes de trabalhar com dados no Fabric, crie um espaço de trabalho com a
versão de avaliação do Fabric ativada.

1.  Abra seu navegador, acesse a barra de endereços e digite ou cole a
    seguinte URL: +++https://app.fabric.microsoft.com/+++ Em seguida,
    pressione o botão **Enter**.

> **Observação**: Se você for direcionado para a página inicial do
> Microsoft Fabric, ignore as etapas de nº 2 a nº 4.
>
> ![](./media/image1.png)

2.  Na janela do **Microsoft Fabric**, insira suas credenciais e clique
    no botão **Submit**.

> ![](./media/image2.png)

3.  Em seguida, na janela da **Microsoft**, digite a senha e clique no
    botão **Sign in.**

> ![A login screen with a red box and blue text Description
> automatically generated](./media/image3.png)

4.  Na janela **Stay signed in?**, clique no botão **Yes.**

> ![A screenshot of a computer error Description automatically
> generated](./media/image4.png)

5.  Na página inicial do Fabric, selecione o bloco + **New workspace**.

> ![A screenshot of a computer Description automatically
> generated](./media/image5.png)

6.  Na aba **Create a workspace**, insira os seguintes detalhes e clique
    no botão **Apply**.

[TABLE]

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image6.png)
>
> ![](./media/image7.png)

7.  Aguarde a conclusão da implementação. Isso leva de 2 a 3 minutos.
    Quando seu novo espaço de trabalho abrir, ele deverá estar vazio.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

## Tarefa 2: Criar um lakehouse e carregar os arquivos

Agora que tem um espaço de trabalho, é hora de mudar para a experiência
de engenharia de dados no portal e criar um data lakehouse para os
arquivos de dados que vai analisar.

1.  Crie um novo Eventhouse clicando no botão **+ New item** na barra de
    navegação.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image9.png)

2.  Clique no bloco "**Lakehouse**".

![A screenshot of a computer Description automatically
generated](./media/image10.png)

3.  Na caixa de diálogo **New lakehouse**, digite
    **+++Fabric_lakehouse+++** no campo **Name**, clique no botão
    **Create** e abra o novo lakehouse.

![A screenshot of a computer Description automatically
generated](./media/image11.png)

4.  Após cerca de um minuto, um novo lakehouse vazio será criado. Você
    precisa ingerir alguns dados no data lakehouse para análise.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image12.png)

5.  Você verá uma notificação informando **Successfully created SQL
    endpoint**.

![](./media/image13.png)

6.  Na seção **Explorer**, abaixo de **fabric_lakehouse**, passe o
    cursor do mouse ao lado da **pasta Files** e clique no menu de
    reticências horizontais **(...)**. Navegue até a pasta e clique em
    **Upload,** depois clique na **Upload folder**, conforme mostrado na
    imagem abaixo.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image14.png)

7.  No painel **Upload folder** que aparece no lado direito, selecione o
    **ícone de pasta** em **Files**/ e, em seguida, navegue até
    **C:\LabFiles**, selecione a pasta **orders** e clique no botão
    **Upload**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image15.png)

8.  Caso a caixa de diálogo **Upload 3 files to this site?** seja
    exibida, clique no botão **Upload**.

![](./media/image16.png)

9.  No painel Upload folder, clique no botão **Upload**.

> ![](./media/image17.png)

10. Após o carregamento dos arquivos, **feche** o painel **Upload
    folder**.

![A screenshot of a computer Description automatically
generated](./media/image18.png)

11. Expanda **Files**, selecione a pasta **orders** e verifique se os
    arquivos CSV foram carregados.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image19.png)

## Tarefa 3: Criar um bloco de notas

Para trabalhar com dados no Apache Spark, você pode criar um *bloco de
notas*. Os blocos de notas fornecem um ambiente interativo no qual você
pode escrever e executar código (em várias linguagens) e adicionar notas
para documentá-lo.

1.  Na página **Home**, ao visualizar o conteúdo da pasta **orders** no
    seu datalake, no menu **Open notebook**, selecione **New notebook**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image20.png)

2.  Após alguns segundos, um novo bloco de notas contendo uma única
    *célula* será aberto. Os blocos de notas são compostos por uma ou
    mais células que podem conter ***code*** ou ***markdown*** (texto
    formatado).

![](./media/image21.png)

3.  Selecione a primeira célula (que atualmente é uma célula *de
    código*) e, em seguida, na barra de ferramentas dinâmica no canto
    superior direito, use o botão **M↓** para **converter a célula em
    uma célula Markdown**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image22.png)

4.  Quando a célula se transforma em uma célula Markdown, o texto que
    ela contém é renderizado.

![A screenshot of a computer Description automatically
generated](./media/image23.png)

5.  Use o botão **🖉** (Edit) para alternar a célula para o modo de
    edição, substitua todo o texto e, em seguida, modifique o Markdown
    da seguinte forma:

> Copiar código
>
> \# Sales order data exploration
>
> Use the code in this notebook to explore sales order data.

![](./media/image24.png)

![A screenshot of a computer Description automatically
generated](./media/image25.png)

6.  Clique em qualquer lugar no bloco de notas fora da célula para parar
    de editar e ver o código Markdown renderizado.

![A screenshot of a computer Description automatically
generated](./media/image26.png)

## Tarefa 4: Carregar dados em uma estrutura de dados

Agora está pronto para executar o código que carrega os dados em uma
estrutura de dados. As estruturas de dados no Spark são semelhantes às
estruturas de dados Pandas no Python e fornecem uma estrutura comum para
trabalhar com dados em linhas e colunas.

**Observação**: O Spark suporta várias linguagens de programação,
incluindo Scala, Java e outras. Neste exercício, usaremos o PySpark, que
é uma variante do Python otimizada para o Spark. O PySpark é uma das
linguagens mais utilizadas no Spark e é a linguagem padrão nos blocos de
notas do Fabric.

1.  Com o bloco de notas visível, expanda a lista **Files** e selecione
    a pasta **orders** para que os arquivos CSV sejam listados ao lado
    do editor do bloco de notas.

> ![A screenshot of a computer Description automatically
> generated](./media/image27.png)

2.  Agora, posicione o cursor do mouse sobre o arquivo 2019.csv. Clique
    nas reticências horizontais **(...)** ao lado de 2019.csv. Navegue
    até a opção **Load data** e clique nela, depois selecione **Spark**.
    Uma nova célula de código contendo o seguinte código será adicionada
    ao bloco de notas:

> Copiar código
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

**Dica**: Você pode ocultar os painéis do Lakehouse Explorer à esquerda
usando os ícones «.

Fazer isso ajudará você a se concentrar no bloco de notas.

3.  Use o botão **▷ Run cell,** localizado à esquerda da célula, para
    executá-la.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image30.png)

**Observação**: Como esta é a primeira vez que você executa um código
Spark, é necessário iniciar uma sessão Spark. Isso significa que a
primeira execução na sessão pode levar cerca de um minuto para ser
concluída. As execuções subsequentes serão mais rápidas.

4.  Quando o comando da célula for concluído, verifique a saída abaixo
    da célula, que deverá ser semelhante a esta:

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image31.png)

5.  A saída mostra as linhas e colunas de dados do ficheiro 2019.csv. No
    entanto, note que os cabeçalhos das colunas não parecem corretos. O
    código padrão usado para carregar os dados numa estrutura de dados
    assume que o arquivo CSV inclui os nomes das colunas na primeira
    linha, mas, neste caso, o arquivo CSV inclui apenas os dados, sem
    informações de cabeçalho.

6.  Modifique o código para definir a opção **header** como **false**.
    Substitua todo o código na **célula** pelo seguinte código e clique
    no botão **▷ Run cell** e revise o resultado.

> Copiar código
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

7.  Agora, a estrutura de dados inclui corretamente a primeira linha
    como valores de dados, mas os nomes das colunas são gerados
    automaticamente e não são muito úteis. Para dar sentido aos dados, é
    necessário definir explicitamente o esquema e o tipo de dados
    corretos para os valores de dados no arquivo.

8.  Substitua todo o código na **célula** pelo seguinte código e clique
    no botão **▷ Run cell** e revise o resultado.

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

9.  Agora, a estrutura de dados inclui os nomes de coluna corretos (além
    do **índice**, que é uma coluna incorporada em todas as estruturas
    de dados com base na posição ordinal de cada linha). Os tipos de
    dados das colunas são especificados usando um conjunto padrão de
    tipos definidos na biblioteca Spark SQL, que foram importados no
    início da célula.

10. Confirme se as suas alterações foram aplicadas aos dados,
    visualizando a estrutura de dados.

11. Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o seguinte código
    nela. Clique no botão **▷ Run cell** e revise a saída.

> Copiar código
>
> display(df)
>
> ![](./media/image35.png)

12. A estrutura de dados inclui apenas os dados do arquivo **2019.csv**.
    Modifique o código para que o caminho do arquivo utilize um
    caractere curinga\* para ler os dados de pedidos de venda de todos
    os arquivos na pasta **orders.**

13. Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o seguinte código
    nela.

Copiar código

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

14. Execute a célula de código modificada e revise a saída, que agora
    deve incluir as vendas de 2019, 2020 e 2021.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image37.png)

**Observação**: Apenas um subconjunto das linhas é exibido, portanto,
você pode não conseguir ver exemplos de todos os anos.

# Exercício 2: Explorar dados em uma estrutura de dados

O objeto de estrutura de dados inclui uma ampla gama de funções que você
pode usar para filtrar, agrupar e manipular os dados que ele contém.

## Tarefa 1: Filtrar uma estrutura de dados

1.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o seguinte código
    nela.

> customers = df\['CustomerName', 'Email'\]
>
> print(customers.count())
>
> print(customers.distinct().count())
>
> display(customers.distinct())
>
> ![](./media/image38.png)

2.  **Execute** a nova célula de código e revise os resultados. Observe
    os seguintes detalhes:

    - Quando você realiza uma operação em uma estrutura de dados, o
      resultado é uma nova estrutura de dados (neste caso, uma nova
      estrutura de dados de **clientes** é criada selecionando um
      subconjunto específico de colunas da estrutura de dados **df**).

    - As estruturas de dados fornecem funções como **count** e
      **distinct** que podem ser usadas para resumir e filtrar os dados
      que contêm.

    - A sintaxe dataframe\['Field1', 'Field2', ...\] é uma forma
      abreviada de definir um subconjunto de colunas. Você também pode
      usar o método **select**, então a primeira linha do código acima
      poderia ser escrita como customers = df.select("CustomerName",
      "Email").

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image39.png)

3.  Modifique o código, substitua todo o código na **célula** pelo
    seguinte código e clique no botão **▷ Run cell,** conforme mostrado
    abaixo:

> Copiar código
>
> customers = df.select("CustomerName",
> "Email").where(df\['Item'\]=='Road-250 Red, 52')
>
> print(customers.count())
>
> print(customers.distinct().count())
>
> display(customers.distinct())

4.  **Execute** o código modificado para visualizar os clientes que
    compraram o ***Road-250 Red, 52* product*.* Observe** que você pode
    "**encadear**" várias funções de forma que a saída de uma função se
    torne a entrada para a próxima - neste caso, estrutura de dados
    criada pelo método **select** é a estrutura de dados de origem para
    o método **where**, que é usado para aplicar os critérios de
    filtragem.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image40.png)

## Tarefa 2: Agregar e agrupar dados em uma estrutura de dados

1.  Clique em **+ Code**, copie e cole o código abaixo e, em seguida,
    clique no botão **Run cell**.

> **Copiar código:**
>
> productSales = df.select("Item", "Quantity").groupBy("Item").sum()
>
> display(productSales)
>
> ![](./media/image41.png)

2.  Observe que os resultados mostram a soma das quantidades dos pedidos
    agrupadas por produto. O método **groupBy** agrupa as linhas por
    *Item*, e a função de agregação **sum** subsequente é aplicada a
    todas as colunas numéricas restantes (neste caso, *Quantity* ).

3.  Clique em **+ Code**, copie e cole o código abaixo e, em seguida,
    clique no botão **Run cell**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image42.png)

> **Copiar código**
>
> from pyspark.sql.functions import \*
>
> yearlySales =
> df.select(year("OrderDate").alias("Year")).groupBy("Year").count().orderBy("Year")
>
> display(yearlySales)
>
> ![](./media/image43.png)

4.  Observe que os resultados mostram o número de pedidos de venda por
    ano. Note que o método **select** inclui uma função SQL **year**
    para extrair o componente de ano do campo **OrderDate** (motivo pelo
    qual o código inclui uma instrução de **importação** para importar
    funções da biblioteca Spark SQL). Em seguida, o método **alias** é
    usado para atribuir um nome de coluna ao valor de ano extraído. Os
    dados são então agrupados pela coluna derivada **Year**, e a
    contagem de linhas em cada grupo é calculada. Por fim, o método
    **orderBy** é usado para ordenar a estrutura de dados resultante.

# Exercício 3: Usar o Spark para transformar arquivos de dados

Uma tarefa comum para engenheiros de dados é ingerir dados em um formato
ou estrutura específicos e transformá-los para processamento ou análise
posteriores.

## Tarefa 1: Utilizar métodos e funções de estrutura de dados para transformar dados

1.  Clique em + Code e copie e cole o código abaixo.

**Copiar código**

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

2.  **Execute** o código para criar uma nova estrutura de dados a partir
    dos dados de pedidos originais com as seguintes transformações:

    - Adicionar colunas **Year** e **Month** com base na coluna
      **OrderDate**.

    - Adicionar as colunas **FirstName** e **LastName** com base na
      coluna **CustomerName.**

    - Filtrar e reordenar as colunas, removendo a coluna
      **CustomerName.**

> ![](./media/image45.png)

3.  Analise o resultado e verifique se as transformações foram aplicadas
    aos dados.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image46.png)

Você pode usar todo o poder da biblioteca Spark SQL para transformar os
dados, filtrando linhas, derivando, removendo, renomeando colunas e
aplicando quaisquer outras modificações de dados necessárias.

**Dica**: Consulte a [*Spark dataframe
documentation*](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/dataframe.html)
para aprender mais sobre os métodos do objeto de estrutura de dados.

## Tarefa 2: Salvar os dados transformados

1.  **Adicione uma nova célula** com o seguinte código para salvar a
    estrutura de dados transformado no formato Parquet (sobrescrevendo
    os dados, se já existirem). **Execute** a célula e aguarde a
    mensagem de que os dados foram salvos.

> Copiar código
>
> transformed_df.write.mode("overwrite").parquet('Files/transformed_data/orders')
>
> print ("Transformed data saved!")
>
> **Observação**: Geralmente, o formato *Parquet* é preferido para
> arquivos de dados que serão usados para análises posteriores ou para
> serem inseridos em um repositório analítico. O Parquet é um formato
> muito eficiente e compatível com a maioria dos sistemas de análise de
> dados em larga escala. Aliás, às vezes, sua necessidade de
> transformação de dados pode ser simplesmente converter dados de outro
> formato (como CSV) para Parquet!
>
> ![](./media/image47.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image48.png)

2.  Em seguida, no painel **Lakehouse Explorer** à esquerda, no menu
    **…** do nó **Files**, selecione **Refresh**.

> ![A screenshot of a computer Description automatically
> generated](./media/image49.png)

3.  Clique na pasta **transformed_data** para verificar se contém uma
    nova pasta chamada **orders**, que por sua vez contém um ou mais
    **arquivos Parquet**.

> ![A screenshot of a computer Description automatically
> generated](./media/image50.png)

4.  Clique em **+ Code** a seguir para carregar uma nova estrutura de
    dados a partir dos arquivos parquet na pasta **transformed_data -\>
    orders**:

> **Copiar código**
>
> orders_df =
> spark.read.format("parquet").load("Files/transformed_data/orders")
>
> display(orders_df)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image51.png)

5.  **Execute** a célula e verifique se os resultados mostram os dados
    do pedido que foram carregados dos arquivos Parquet.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image52.png)

## Tarefa 3: Salvar dados em arquivos particionados

1.  Adicione uma nova célula, clique em **+ Code** e insira o seguinte
    código, que salva estrutura de dados, particionando os dados por
    **Year** e **Month**. **Execute** a célula e aguarde a mensagem de
    que os dados foram salvos.

> Copiar código
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

2.  Em seguida, no painel **Lakehouse Explorer** à esquerda, no menu
    **…** do nó **Files**, selecione **Refresh.**

![A screenshot of a computer Description automatically
generated](./media/image55.png)

3.  Expanda a pasta **partitioned_orders** para verificar se contém uma
    hierarquia de pastas com o nome **Year=*xxxx***, cada uma contendo
    pastas com o nome **Month=*xxxx***. Cada pasta de mês contém um
    arquivo Parquet com os pedidos daquele mês.

![A screenshot of a computer Description automatically
generated](./media/image56.png)

![A screenshot of a computer Description automatically
generated](./media/image57.png)

> O particionamento de arquivos de dados é uma maneira comum de otimizar
> o desempenho ao lidar com grandes volumes de dados. Essa técnica pode
> melhorar significativamente o desempenho e facilitar a filtragem de
> dados.

4.  Adicione uma nova célula, clique em **+ Code** com o seguinte código
    para carregar uma nova estrutura de dados do arquivo
    **orders.parquet**:

> Copiar código
>
> orders_2021_df =
> spark.read.format("parquet").load("Files/partitioned_data/Year=2021/Month=\*")
>
> display(orders_2021_df)

5.  **Execute** a célula e verifique se os resultados mostram os dados
    de pedidos de venda de 2021. Observe que as colunas de
    particionamento especificadas no caminho (**Year** e **Month**) não
    estão incluídas na estrutura de dados.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image58.png)

**Exercício 3: Trabalhar com tabelas e SQL**

Como você viu, os métodos nativos do objeto de estrutura de dados
permitem consultar e analisar dados de um arquivo com bastante eficácia.
No entanto, muitos analistas de dados se sentem mais à vontade
trabalhando com tabelas que podem ser consultadas usando a sintaxe SQL.
O Spark fornece um metastore no qual você pode definir tabelas
relacionais. A biblioteca Spark SQL que fornece o objeto de estrutura de
dados também suporta o uso de instruções SQL para consultar tabelas no
metastore. Ao usar esses recursos do Spark, você pode combinar a
flexibilidade de um data lake com o esquema de dados estruturado e as
consultas baseadas em SQL de um armazenamento de dados relacional - daí
o termo “data lakehouse”.

**Tarefa 1: Criar uma tabela gerenciada**

As tabelas em um metastore do Spark são abstrações relacionais sobre
arquivos no data lake. As tabelas podem ser gerenciadas (caso em que os
arquivos são gerenciados pelo metastore) ou externas (caso em que a
tabela faz referência a um local de arquivos no data lake que você
gerencia de forma independente do metastore).

1.  Adicionar um novo código, clique na célula **+ Code** no bloco de
    notas e insira o seguinte código, que salva estrutura de dados de
    dados de pedidos de venda como uma tabela chamada **salesorders**:

> Copiar código
>
> \# Create a new table
>
> df.write.format("delta").saveAsTable("salesorders")
>
> \# Get the table description
>
> spark.sql("DESCRIBE EXTENDED salesorders").show(truncate=False)

**Observação**: Vale a pena notar alguns pontos sobre este exemplo.
Primeiro, nenhum caminho explícito é fornecido, portanto, os arquivos da
tabela serão gerenciados pelo metastore. Segundo, a tabela é salva no
formato **delta**. Você pode criar tabelas com base em vários formatos
de arquivo (incluindo CSV, Parquet, Avro e outros), mas *o delta lake* é
uma tecnologia do Spark que adiciona recursos de banco de dados
relacional às tabelas, incluindo suporte a transações, versionamento de
linhas e outros recursos úteis. A criação de tabelas no formato delta é
preferível para data lakehouses no Fabric.

2.  **Execute** a célula de código e revise a saída, que descreve a
    definição da nova tabela.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image59.png)

3.  No painel do **Lakehouse Explorer**, no menu **…** da pasta
    **Tables**, selecione **Refresh.**

> ![A screenshot of a computer Description automatically
> generated](./media/image60.png)

4.  Em seguida, expanda o nó **Tables** e verifique se a tabela
    **salesorders** foi criada no esquema **dbo**.

> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

5.  Posicione o cursor do mouse ao lado da tabela **salesorders** e
    clique nas reticências horizontais (...). Navegue e clique em **Load
    data** e selecione **Spark**.

> ![](./media/image62.png)

6.  Clique no botão **▷ Run cell**, que usa a biblioteca Spark SQL para
    incorporar uma consulta SQL na tabela **salesorder** PySpark e
    carregar os resultados da consulta em uma estrutura de dados.

> Copiar código
>
> df = spark.sql("SELECT \* FROM Fabric_lakehouse.dbo.salesorders LIMIT
> 1000")
>
> display(df)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image63.png)

Tarefa 2: Criar uma tabela externa

Você também pode criar tabelas externas nas quais os metadados de
esquema são definidos no metastore do lakehouse, mas os arquivos de
dados são armazenados em um local externo.

1.  Abaixo dos resultados retornados pela primeira célula de código, use
    o botão **+ Code** para adicionar uma nova célula de código, caso
    ainda não exista. Em seguida, insira o seguinte código na nova
    célula.

> Copiar código
>
> df.write.format("delta").saveAsTable("external_salesorder",
> path="\<abfs_path\>/external_salesorder")

![A screenshot of a computer Description automatically
generated](./media/image64.png)

2.  No painel do **Lakehouse Explorer**, no menu **…** da pasta
    **Files**, selecione **Copy ABFS path** no bloco de notas.

> O caminho ABFS é o caminho completo para a pasta **Files** no
> armazenamento OneLake do seu lakehouse - semelhante a este:

abfss://dp_Fabric29@onelake.dfs.fabric.microsoft.com/Fabric_lakehouse.Lakehouse/Files/external_salesorder

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image65.png)

3.  Agora, na célula de código, substitua **\<abfs_path\>** pelo
    **caminho** que você copiou para o bloco de notas, para que o código
    salve estrutura de dados como uma tabela externa com os arquivos de
    dados em uma pasta chamada **external_salesorder** na sua pasta
    **Files**. O caminho completo deve ser semelhante a este:

> abfss://dp_Fabric29@onelake.dfs.fabric.microsoft.com/Fabric_lakehouse.Lakehouse/Files/external_salesorder

4.  Use o botão **▷ (*Run cell*)** à esquerda da célula para executá-la.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image66.png)

5.  No painel do **Lakehouse Explorer**, no menu **…** da pasta
    **Tables**, selecione **Refresh**.

> ![A screenshot of a computer Description automatically
> generated](./media/image67.png)

6.  Em seguida, expanda o nó **Tables** e verifique se a tabela
    **external_salesorder** foi criada.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image68.png)

7.  No painel do **Lakehouse Explorer**, no menu **…** da pasta
    **Files**, selecione **Refresh**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image69.png)

8.  Em seguida, expanda o nó **Files** e verifique se a pasta
    **external_salesorder** foi criada para os arquivos de dados da
    tabela.

> ![](./media/image70.png)

**Tarefa 3: Comparar tabelas gerenciadas e externas**

Vamos explorar as diferenças entre tabelas gerenciadas e tabelas
externas.

1.  Abaixo dos resultados retornados pela célula de código, use o botão
    **+ Code** para adicionar uma nova célula de código. Copie o código
    abaixo para a célula de código e use o botão **▷ (*Run cell*)** à
    esquerda da célula para executá-lo.

> Copiar SQL
>
> %%sql
>
> DESCRIBE FORMATTED salesorders;
>
> ![](./media/image71.png)

2.  Nos resultados, visualize a propriedade **Location** da tabela, que
    deve ser um caminho para o armazenamento OneLake do lakehouse,
    terminando com **/Tables/ salesorders** (talvez seja necessário
    ampliar a coluna **Data type** para visualizar o caminho completo).

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image72.png)

3.  Modifique o comando **DESCRIBE**  para exibir os detalhes da tabela
    **external_saleorder**, conforme mostrado aqui.

4.  Abaixo dos resultados retornados pela célula de código, use o botão
    **+ Code** para adicionar uma nova célula de código. Copie o código
    abaixo e use o botão **▷ (*Run cell*)** à esquerda da célula para
    executá-lo.

> Copiar SQL
>
> %%sql
>
> DESCRIBE FORMATTED external_salesorder;

5.  Nos resultados, visualize a propriedade **Location** da tabela, que
    deve ser um caminho para o armazenamento OneLake do lakehouse,
    terminando com **/Files/ external_saleorder** (talvez seja
    necessário ampliar a coluna **Data type** para visualizar o caminho
    completo).

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image73.png)

**Tarefa 4: Executar código SQL em uma célula**

Embora seja útil poder incorporar instruções SQL em uma célula que
contém código PySpark, os analistas de dados geralmente preferem
trabalhar diretamente em SQL.

1.  Clique em + **Code** no bloco de notas e insira o código a seguir.
    Clique no botão ▷ **Run cell** e revise os resultados. Observe que:

    - A linha %%sql no início da célula (chamada *magic*) indica que o
      ambiente de execução da linguagem Spark SQL deve ser usado para
      executar o código nesta célula em vez do PySpark.

    - O código SQL faz referência à tabela **salesorders** que você
      criou anteriormente.

    - O resultado da consulta SQL é exibido automaticamente abaixo da
      célula.

> Copiar SQL
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

**Observação**: Para obter mais informações sobre Spark SQL e estrutura
de dados, consulte a [*Spark SQL
documentation*](https://spark.apache.org/docs/2.2.0/sql-programming-guide.html).

**Exercício 4: Visualizar dados com o Spark**

Uma imagem vale mais que mil palavras, e um gráfico é muitas vezes
melhor do que mil linhas de dados. Embora os blocos de notas no Fabric
incluam uma visualização de gráfico integrada para dados exibidos a
partir de uma estrutura de dados ou consulta Spark SQL, ela não foi
concebida para a criação de gráficos abrangentes. No entanto, pode
utilizar bibliotecas gráficas Python, como **matplotlib** e **seaborn**,
para criar gráficos a partir de dados em estruturas de dados.

**Tarefa 1: Visualizar os resultados em forma de gráfico**

1.  Clique em + **Code** no bloco de notas e insira o código a seguir.
    Clique no botão ▷ **Run cell** e observe que ele retorna os dados da
    exibição **salesorders** que você criou anteriormente.

> Copiar SQL
>
> %%sql
>
> SELECT \* FROM salesorders
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image75.png)

2.  Na seção de resultados abaixo da célula, altere a opção **View** de
    **Table** para + **New chart**.

> ![](./media/image76.png)

3.  Use o botão **Start editing** no canto superior direito do gráfico
    para exibir o painel de opções. Em seguida, defina as opções da
    seguinte forma e selecione **Apply**:

    - **Chart type:** Bar chart

    - **Key:** Item

    - **Values:** Quantity

    - **Series Group:** *deixe em branco*

    - **Aggregation**: Sum

    - **Stacked**: *Não selecionado*

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image77.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image78.png)

4.  Verifique se o gráfico é semelhante a este.

> ![](./media/image79.png)

**Tarefa 2: Primeiros passos com o matplotlib**

1.  Clique em **+ Code**, copie e cole o código abaixo. **Execute** o
    código e observe que ele retorna uma estrutura de dados do Spark
    contendo a receita anual.

> Copiar código
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

2.  Para visualizar os dados em um gráfico, começaremos usando a
    biblioteca **matplotlib** do Python. Essa biblioteca é a base para a
    criação de gráficos, na qual muitas outras se baseiam, e oferece
    grande flexibilidade na criação de gráficos.

3.  Clique em **+ Code** e copie e cole o código abaixo.

**Copiar código**

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
> ![A screenshot of a computer Description automatically
> generated](./media/image81.png)

4.  Clique no botão **Run cell** e revise os resultados, que consistem
    em um gráfico de colunas com a receita bruta total para cada ano.
    Observe as seguintes características do código usado para gerar este
    gráfico:

    - A biblioteca **matplotlib** requer uma estrutura de dados do
      **Pandas**, portanto é necessário converter a estrutura de dados
      do Spark retornado pela consulta Spark SQL para esse formato.

    - No núcleo da biblioteca **matplotlib** está o objeto **pyplot**,
      que é a base para a maior parte das funcionalidades de criação de
      gráficos.

    - As configurações padrão resultam em um gráfico utilizável, mas há
      um amplo potencial de personalização.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image82.png)

5.  Modifique o código para gerar o gráfico da seguinte forma: substitua
    todo o código na **célula** pelo código a seguir, clique no botão
    **Run cell** e revise o resultado.

> Copiar código
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

6.  O gráfico agora inclui um pouco mais de informação. Um gráfico está
    tecnicamente contido em uma **Figura**. Nos exemplos anteriores, a
    figura foi criada implicitamente para você; mas você pode criá-la
    explicitamente.

7.  Modifique o código para gerar o gráfico da seguinte forma,
    substituindo todo o código na **Célula** pelo código a seguir.

> Copiar código
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

8.  **Execute novamente** a célula de código e visualize os resultados.
    A figura determina o formato e o tamanho do gráfico.

> Uma figura pode conter vários subgráficos, cada um em seu próprio
> *eixo*.
>
> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image85.png)
>
> ![A screenshot of a graph AI-generated content may be
> incorrect.](./media/image86.png)

9.  Modifique o código para gerar o gráfico da seguinte forma. **Execute
    novamente** a célula de código e visualize os resultados. A figura
    contém os subgráficos especificados no código.

> Copiar código
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

**Observação:** Para saber mais sobre como criar gráficos com
matplotlib, consulte a [*matplotlib
documentation*](https://matplotlib.org/).

Tarefa 3: Utilizar a biblioteca seaborn

Embora o **matplotlib** permita criar gráficos complexos de vários
tipos, pode ser necessário um código complexo para obter os melhores
resultados. Por esse motivo, ao longo dos anos, muitas novas bibliotecas
foram criadas com base no matplotlib para abstrair sua complexidade e
aprimorar suas capacidades. Uma dessas bibliotecas é o **seaborn**.

1.  Clique em **+ Code** e copie e cole o código abaixo.

> Copiar código
>
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

2.  **Execute** o código e observe que ele exibe um gráfico de barras
    usando a biblioteca seaborn.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image89.png)

3.  **Modifique** o código da seguinte forma. **Execute** o código
    modificado e observe que o seaborn permite definir um tema de cores
    consistente para seus gráficos.

> Copiar código
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

4.  **Modifique** o código novamente da seguinte forma. **Execute** o
    código modificado para visualizar a receita anual em um gráfico de
    linhas.

> Copiar código
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

**Observação:** Para saber mais sobre como criar gráficos com o seaborn,
consulte a [*seaborn
documentation*](https://seaborn.pydata.org/index.html).

**Tarefa 4: Utilizar tabelas delta para dados de streaming**

O Delta Lake suporta dados de streaming. As tabelas Delta podem servir
como *destino* ou *origem* para fluxos de dados criados usando a API
Spark Structured Streaming. Neste exemplo, você usará uma tabela Delta
como destino para alguns dados de streaming em um cenário simulado de
internet of things (IoT).

1.  Clique em **+ Code**, copie e cole o código abaixo e, em seguida,
    clique no botão **Run cell**.

> Copiar código
>
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

2.  Certifique-se de que a mensagem ***Source stream created …*** seja
    exibida. O código que você acabou de executar criou uma fonte de
    dados de streaming com base em uma pasta na qual alguns dados foram
    salvos, representando leituras de dispositivos IoT hipotéticos.

3.  Clique em **+ Code**, copie e cole o código abaixo e, em seguida,
    clique no botão **Run cell**.

> Copiar código
>
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

4.  Este código grava os dados do dispositivo de streaming em formato
    Delta em uma pasta chamada **iotdevicedata**. Como o caminho para a
    localização da pasta está na pasta **Tables**, uma tabela será
    criada automaticamente para isso. Clique nas reticências horizontais
    ao lado de **Table** e, em seguida, clique em **Refresh**.

> ![](./media/image95.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image96.png)

5.  Clique em **+ Code**, copie e cole o código abaixo e, em seguida,
    clique no botão **Run cell**.

> Copiar SQL
>
> %%sql
>
> SELECT \* FROM IotDeviceData;
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image97.png)

6.  Este código consulta a tabela **IotDeviceData**, que contém os dados
    do dispositivo provenientes da fonte de streaming.

7.  Clique em **+ Code**, copie e cole o código abaixo e, em seguida,
    clique no botão **Run cell**

> Copiar código
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

8.  Este código grava mais dados hipotéticos de dispositivos na origem
    de streaming.

9.  Clique em **+ Code**, copie e cole o código abaixo e, em seguida,
    clique no botão **Run cell**.

> Copiar SQL
>
> %%sql
>
> SELECT \* FROM IotDeviceData;
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image99.png)

10. Este código consulta novamente a tabela **IotDeviceData**, que agora
    deve incluir os dados adicionais que foram adicionados à fonte de
    streaming.

11. Clique em **+ Code**, copie e cole o código abaixo e, em seguida,
    clique no botão **Run cell**.

> Copiar código
>
> deltastream.stop()
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image100.png)

12. Este código interrompe o fluxo.

**Tarefa 5: Salvar o bloco de notas e encerrar a sessão do Spark**

Agora que você terminou de trabalhar com os dados, pode salvar o bloco
de notas com um nome significativo e encerrar a sessão do Spark.

1.  Na barra de menus do bloco de notas, use o ícone de configuração
    ⚙️ **Settings** para visualizar as configurações do bloco de notas.

> ![A screenshot of a computer Description automatically
> generated](./media/image101.png)

2.  Defina o **Name** do bloco de notas como +++**Explore Sales
    Orders+++** e, em seguida, feche o painel de configurações.

> ![A screenshot of a computer Description automatically
> generated](./media/image102.png)

3.  No menu do bloco de notas, selecione **Stop session** para encerrar
    a sessão do Spark.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image103.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image104.png)

**Exercício 5: Criar um fluxo de dados (Gen2) no Microsoft Fabric**

No Microsoft Fabric, os Dataflows (Gen2) conectam-se a várias fontes de
dados e realizam transformações no Power Query Online. Podem então ser
usados em Pipelines de dados para ingerir dados em um lakehouse ou outro
repositório analítico, ou para definir um conjunto de dados para um
relatório do Power BI.

Este exercício foi concebido para apresentar os diferentes elementos dos
Dataflows (Gen2), e não para criar uma solução complexa que possa
existir em uma empresa.

**Tarefa 1: Criar um fluxo de dados (Gen2) para ingerir dados**

Agora que você tem um lakehouse, precisa inserir alguns dados nela. Uma
maneira de fazer isso é definir um fluxo de dados que englobe um
processo de *extract, transform, e load* (ETL).

1.  Agora, clique em **Fabric_lakehouse** no painel de navegação à
    esquerda.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image105.png)

2.  Na página inicial do **Fabric_lakehouse**, clique na seta suspensa
    em **Get data** e selecione **New Dataflow Gen2**. O editor do Power
    Query para o novo fluxo de dados será aberto.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image106.png)

3.  Na caixa de diálogo **New Dataflow Gen2**, digite
    **+++Gen2_Dataflow+++** no campo **Name**, clique no botão
    **Create** e abra o **New Dataflow Gen2**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image107.png)

4.  No painel do **Power Query**, na aba **Home**, clique em **Import
    from a Text/CSV file**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image108.png)

5.  No painel **Connect to data source**, em **Connection settings**,
    selecione o botão de opção **Link to file (Pré-visualização).**

- **Link to file**: *Selecionado*

- **File path or URL**:
  +++https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/orders.csv+++

![](./media/image109.png)

6.  No painel **Connect to data source**, em **Connection credentials,**
    insira os seguintes detalhes e clique no botão **Next.**

- **Connection:** Create new connection

- **Connection name:** Orders

- **data gateway:** (none)

- **Authentication kind:** Anonymous

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image110.png)

7.  No painel **Preview file data**, clique em **Create** para criar a
    fonte de dados.![A screenshot of a computer Description
    automatically generated](./media/image111.png)

8.  O editor do **Power Query** exibe a fonte de dados e um conjunto
    inicial de etapas de consulta para formatar os dados.

> ![](./media/image112.png)

9.  Na faixa de opções da barra de ferramentas, selecione a aba **Add
    column**. Em seguida, selecione **Custom column.**

> ![](./media/image113.png) 

10. Defina o nome da nova coluna como +++**MonthNo+++**, defina o tipo
    de dados como **Whole Number** e adicione a seguinte fórmula:
    +++**Date.Month(\[OrderDate\])+++** em **Custom column formula**.
    Selecione **OK**.

> ![](./media/image114.png)

11. Observe como a etapa para adicionar a coluna personalizada é
    adicionada à consulta. A coluna resultante é exibida no painel de
    dados.

> ![A screenshot of a computer Description automatically
> generated](./media/image115.png)

**Dica:** No painel configurações da consulta, à direita, observe que as
**Applied Steps** incluem cada etapa de transformação. Na parte
inferior, você também pode ativar o botão **Diagram flow** para exibir o
diagrama visual das etapas.

Os degraus podem ser movidos para cima ou para baixo, editados
selecionando o ícone de engrenagem, e pode selecionar cada etapa para
ver as transformações aplicadas no painel de pré-visualização.

Tarefa 2: Adicionar destino de dados para o fluxo de dados

1.  Na faixa de opções da barra de ferramentas do **Power Query**,
    selecione a aba **Home**. Em seguida, no menu suspenso **Data
    destination,** selecione **Lakehouse** (se ainda não estiver
    selecionado).

> ![](./media/image116.png)
>
> ![](./media/image117.png)

**Observação:** Se esta opção estiver acinzentada, você pode já ter um
destino de dados definido. Verifique o destino de dados na parte
inferior do painel **Query settings**, no lado direito do editor do
Power Query. Se um destino já estiver definido, você poderá alterá-lo
usando o ícone de engrenagem.

2.  O destino **Lakehouse** é indicado por um **ícone** na **query** no
    editor do Power Query.

> ![A screenshot of a computer Description automatically
> generated](./media/image118.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image119.png)

3.  Na janela inicial, selecione **Save & run** e clique no botão **Save
    & run.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image120.png)

4.  Na navegação à esquerda, selecione o **ícone do espaço de trabalho**
    ***dp_Fabric-XXXXX***, conforme mostrado na imagem abaixo.

> ![](./media/image121.png)

**Tarefa 3: Adicionar um fluxo de dados a um pipeline**

Você pode incluir um fluxo de dados como uma atividade em um pipeline.
Os pipelines são usados para orquestrar atividades de ingestão e
processamento de dados, permitindo combinar fluxos de dados com outros
tipos de operação em um único processo agendado. Os pipelines podem ser
criados em diferentes experiências, incluindo a experiência Data
Factory.

1.  Na página inicial de engenharia de dados da Synapse, no painel
    **dp_FabricXX**, selecione +**New item** → P**ipeline**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image122.png)

2.  Na caixa de diálogo **New pipeline**, digite +++**Load data+++** no
    campo **Name** e clique no botão **Create** para abrir o novo
    pipeline.

> ![A screenshot of a computer Description automatically
> generated](./media/image123.png)

3.  O editor de pipeline é aberto.

> ![A screenshot of a computer Description automatically
> generated](./media/image124.png)
>
> **Dica:** Se o assistente de cópia de dados abrir automaticamente,
> feche-o!

4.  Selecione **Pipeline activity** e adicione uma atividade
    **Dataflow** ao pipeline.

> ![A screenshot of a computer Description automatically
> generated](./media/image125.png)

5.  Com a nova atividade **Dataflow1** selecionada, na aba **Settings**,
    na lista suspensa **Dataflow**, selecione **Gen2_Dataflow** (o fluxo
    de dados que você criou anteriormente).

> ![A screenshot of a computer Description automatically
> generated](./media/image126.png)

6.  Na aba **Home**, salve o pipeline usando o ícone **🖫 (*Salvar*)**.

> ![A screenshot of a computer Description automatically
> generated](./media/image127.png)

7.  Use o botão **▷ Run** para executar o pipeline e aguarde a
    conclusão. Isso pode levar alguns minutos.

> ![A screenshot of a computer Description automatically
> generated](./media/image128.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image129.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image130.png)

8.  Na barra superior, selecione a aba **Fabric_lakehouse**.

> ![A screenshot of a computer Description automatically
> generated](./media/image131.png)

9.  No painel **Explorer**, selecione o menu **…** para **Tables** e
    selecione **refresh**. Em seguida, expanda **Tables** e selecione a
    tabela **orders**, que foi criada pelo seu fluxo de dados.

> ![A screenshot of a computer Description automatically
> generated](./media/image132.png)
>
> ![](./media/image133.png)

**Dica:** Use o *conector de fluxos de dados do Power BI Desktop* para
se conectar diretamente às transformações de dados realizadas com seu
fluxo de dados.

Você também pode realizar transformações adicionais, publicar como um
novo conjunto de dados e distribuir para o público-alvo específico de
conjuntos de dados especializados.

**Tarefa 4: Limpar recursos**

Neste exercício, você aprendeu como usar o Spark para trabalhar com
dados no Microsoft Fabric.

Se você já terminou de explorar seu lakehouse, pode excluir o espaço de
trabalho que criou para este exercício.

1.  Na barra à esquerda, selecione o ícone do seu espaço de trabalho
    para visualizar todos os itens que ele contém.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image134.png)

2.  No menu **…** da barra de ferramentas, selecione **Workspace
    settings**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image135.png)

3.  Selecione **General** e clique em **Remove this workspace.**

![A screenshot of a computer settings Description automatically
generated](./media/image136.png)

4.  Na caixa de diálogo **Delete workspace?**, clique no botão
    **Delete.**

> ![A screenshot of a computer Description automatically
> generated](./media/image137.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image138.png)

**Resumo**

Este caso de uso orienta você no processo de trabalho com o Microsoft
Fabric no Power BI. Abrange várias tarefas, incluindo a configuração de
um espaço de trabalho, a criação de um lakehouse, o carregamento e
gerenciamento de arquivos de dados e o uso de bloco de notas para
exploração de dados. Os participantes aprenderão como manipular e
transformar dados usando PySpark, criar visualizações e salvar e
particionar dados para consultas eficientes.

Neste caso de uso, os participantes realizarão uma série de tarefas
focadas no trabalho com tabelas delta no Microsoft Fabric. As tarefas
incluem o carregamento e a exploração de dados, a criação de tabelas
delta gerenciadas e externas, a comparação de suas propriedades, a
introdução de recursos SQL para o gerenciamento de dados estruturados e
insights sobre visualização de dados usando bibliotecas Python como
matplotlib e seaborn. Os exercícios visam proporcionar uma compreensão
abrangente da utilização do Microsoft Fabric para análise de dados e da
incorporação de tabelas delta para streaming de dados em um contexto de
IoT.

Este caso de uso orienta você no processo de configuração de um espaço
de trabalho do Fabric, criação de um data lake e ingestão de dados para
análise. Ele demonstra como definir um fluxo de dados para lidar com
operações de ETL e configurar destinos de dados para armazenar os dados
transformados. Além disso, você aprenderá como integrar o fluxo de dados
a um pipeline para processamento automatizado. Por fim, você receberá
instruções para limpar os recursos após a conclusão do exercício.

Este laboratório fornece as habilidades essenciais para trabalhar com o
Fabric, permitindo que você crie e gerencie espaços de trabalho,
estabeleça data lakes e execute transformações de dados com eficiência.
Ao incorporar fluxos de dados em pipelines, você aprenderá a automatizar
tarefas de processamento de dados, otimizando seu fluxo de trabalho e
aumentando a produtividade em cenários reais. As instruções de limpeza
garantem que você não deixe recursos desnecessários, promovendo uma
abordagem organizada e eficiente para o gerenciamento do espaço de
trabalho.
