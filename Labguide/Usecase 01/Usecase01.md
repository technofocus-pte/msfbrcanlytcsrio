# Caso de uso 1: Criar um Lakehouse, ingerir dados de amostra e gerar um relatório

**Introdução**

Este laboratório guia você por um cenário completo, da aquisição ao
consumo de dados. Ele ajuda você a criar uma compreensão básica do
Fabric, incluindo as diferentes experiências e como elas se integram,
bem como as experiências de desenvolvedores profissionais e cidadãos que
acompanham o trabalho nesta plataforma. Este laboratório não pretende
ser uma arquitetura de referência, uma lista exaustiva de recursos e
funcionalidades ou uma recomendação de práticas específicas.

Tradicionalmente, as organizações têm criado armazéns de dados modernos
para suas necessidades de análise de dados transacionais e estruturadas,
e Data Lakehouse para suas necessidades de análise de big data (semi/não
estruturados). Esses dois sistemas funcionavam em paralelo, criando
silos, duplicação de dados e aumentando o custo total de propriedade.

O Fabric, com sua unificação de armazenamento de dados e padronização no
formato Delta Lake, permite eliminar silos, remover duplicidade de dados
e reduzir drasticamente o custo total de propriedade.

Com a flexibilidade oferecida pelo Fabric, você pode implementar
arquiteturas de Lakehouse ou de armazenamento de dados, ou combiná-las
para obter o melhor de ambas com uma implementação simples. Neste
tutorial, você usará como exemplo uma organização do setor varejista e
criará seu Lakehouse do início ao fim. Utilizando a [medallion
architecture](https://learn.microsoft.com/en-us/azure/databricks/lakehouse/medallion),
onde a camada bronze contém os dados brutos, a camada prata contém os
dados validados e desduplicados, e a camada ouro contém os dados
altamente refinados. Você pode adotar a mesma abordagem para implementar
um Lakehouse para qualquer organização, de qualquer setor.

Este laboratório explica como um desenvolvedor da empresa fictícia Wide
World Importers, do setor varejista, executa as seguintes etapas.

**Objetivos**:

1\. Iniciar sessão na sua conta do Power BI e iniciar um teste gratuito
do Microsoft Fabric.

2\. Iniciar a avaliação do Microsoft Fabric (Pré-visualização) no Power
BI.

3\. Configurar o cadastro no OneDrive para o centro de administração do
Microsoft 365.

4\. Criar e implementar um Lakehouse completo para a organização,
incluindo a criação de um espaço de trabalho Fabric e um Lakehouse.

5\. Introduzir os dados de amostra no ambiente de processamento e
prepará-los para o processamento posterior.

6\. Transformar e preparar os dados usando Python/PySpark e blocos de
notas SQL.

7\. Criar tabelas agregadas de negócios usando diferentes abordagens.

8\. Estabelecer relações entre as tabelas para gerar relatórios
contínuos.

9\. Criar um relatório do Power BI com visualizações com base nos dados
preparados.

10\. Salvar e armazenar o relatório criado para referência e análise
futuras.

## Exercício 1: Configurar o cenário completo do Lakehouse

### Tarefa 1: Iniciar sessão na sua conta do Power BI e se inscrever na versão de avaliação gratuita do Microsoft Fabric.

1.  Abra seu navegador, navegue até a barra de endereço e digite ou cole
    o seguinte URL:+++https://app.fabric.microsoft.com/+++ e pressione o
    botão **Enter**.

> ![](./media/image1.png)

2.  Na janela do **Microsoft Fabric**, insira suas credenciais e clique
    no botão **Submit**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image2.png)

3.  Em seguida, na janela da **Microsoft**, digite a senha e clique no
    botão **Sign in**.

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image3.png)

4.  Na janela **Stay signed in?**, clique no botão **Yes.**

> ![](./media/image4.png)

5.  Você será redirecionado para a página inicial do Power BI.

> ![](./media/image5.png)

## Exercício 2: Criar e implementar um Lakehouse completo para a sua organização

### Tarefa 1: Criar um espaço de trabalho Fabric

Nesta tarefa, você criará um espaço de trabalho do Fabric. O espaço de
trabalho contém todos os itens necessários para este tutorial do
Lakehouse, incluindo o próprio Lakehouse, fluxos de dados, pipelines do
Data Factory, blocos de notas, conjuntos de dados do Power BI e
relatórios.

1.  Na página inicial do Fabric, selecione o bloco **+ New workspace**.

> ![A screenshot of a computer Description automatically
> generated](./media/image6.png)

2.  No painel **Create a workspace** que aparece no lado direito, insira
    os seguintes detalhes e clique no botão **Apply**.

[TABLE]

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image7.png)

**Observação**: Para encontrar seu ID instantâneo do laboratório,
selecione 'Help' e copie o ID instantâneo.

> ![A screenshot of a computer Description automatically
> generated](./media/image8.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image9.png)

3.  Aguarde a conclusão da implementação. Isso leva de 2 a 3 minutos.

> ![A screenshot of a computer Description automatically
> generated](./media/image10.png)

### Tarefa 2: Criar um Lakehouse

1\. Crie um novo Lakehouse clicando no botão **+ New item** na barra de
navegação.

> ![A screenshot of a computer Description automatically
> generated](./media/image11.png)

2.  Clique no bloco **Lakehouse**.

> ![A screenshot of a computer Description automatically
> generated](./media/image12.png)

3.  Na caixa de diálogo **New lakehouse**, digite +++**wwilakehouse+++**
    no campo **Name**, clique no botão **Create** e abra o novo
    Lakehouse.

> **Observação:** Certifique-se de remover o espaço antes de
> **wwilakehouse**.
>
> ![A screenshot of a computer Description automatically
> generated](./media/image13.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image14.png)

4.  Você verá uma notificação informando que o **Successfully created
    SQL endpoint**.

> ![](./media/image15.png)

### Tarefa 3: Ingerir dados de amostra

1.  Na página **wwilakehouse**, navegue até a seção **Get data in your
    lakehouse** e clique em **Upload files as shown in the below
    image.**

> ![A screenshot of a computer Description automatically
> generated](./media/image16.png)

2.  Na aba **Upload files**, clique na pasta abaixo de arquivos.

> ![A screenshot of a computer Description automatically
> generated](./media/image17.png)

3.  Navegue até **C:\LabFiles** na sua máquina virtual, selecione o
    arquivo **dimension_customer.csv** e clique no botão **Open**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image18.png)

4.  Em seguida, clique no botão **Upload** e feche.

> ![A screenshot of a computer Description automatically
> generated](./media/image19.png)

5.  **Feche** o painel **Upload files**.

> ![A screenshot of a computer Description automatically
> generated](./media/image20.png)

6.  Clique e selecione **Refresh** em **Files**. O arquivo aparecerá.

> ![A screenshot of a computer Description automatically
> generated](./media/image21.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

7.  Na página **Lakehouse**, no painel Explorer, selecione **Files**.
    Agora, posicione o cursor sobre o arquivo
    **dimension_customer.csv**. Clique nas reticências horizontais
    **(...)** ao lado de **dimension_customer.csv**. Navegue até **Load
    Table** e clique em **New table**.

> ![](./media/image23.png)

8.  Na caixa de diálogo **Load file to new table**, clique no botão
    **Load**.

> ![](./media/image24.png)

9.  Agora, a tabela **dimension_customer** foi criada com sucesso.

> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

10. Selecione a tabela **dimension_customer** no esquema **dbo**.

> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image27.png)

11. Você também pode usar o endpoint SQL do Lakehouse para consultar os
    dados com instruções SQL. Selecione **SQL analytics endpoint** no
    menu suspenso do **Lakehouse**, localizado no canto superior direito
    da tela.

> ![A screenshot of a computer Description automatically
> generated](./media/image28.png)

12. Na página **wwilakehouse**, em Explorer, selecione a tabela
    **dimension_customer** para visualizar seus dados e selecione **New
    SQL query** para escrever suas instruções SQL.

> ![A screenshot of a computer Description automatically
> generated](./media/image29.png)

13. A consulta de exemplo a seguir agrega a contagem de linhas com base
    na **coluna BuyingGroup** da tabela **dimension_customer**. Os
    arquivos de consulta SQL são salvos automaticamente para referência
    futura e você pode renomear ou excluir conforme necessário. Cole o
    código como mostrado na imagem abaixo e clique no ícone de
    reprodução para **executar** o script:

> SELECT BuyingGroup, Count(\*) AS Total
>
> FROM dimension_customer
>
> GROUP BY BuyingGroup
>
> ![A screenshot of a computer Description automatically
> generated](./media/image30.png)

**Observação:** Se você encontrar um erro durante a execução do script,
verifique a sintaxe do script para garantir que não haja espaços
desnecessários.

14. Anteriormente, todas as tabelas e visualizações do Lakehouse eram
    adicionadas automaticamente ao modelo semântico. Com as atualizações
    recentes, para novos Lakehouses, você precisa adicionar manualmente
    suas tabelas ao modelo semântico.

> ![A screenshot of a computer Description automatically
> generated](./media/image31.png)

15. Na página **Home** do Lakehouse, selecione **New semantic model** e
    escolha as tabelas que você deseja adicionar ao modelo semântico.
    ![A screenshot of a computer Description automatically
    generated](./media/image32.png)

16. Na caixa de diálogo **New semantic model,** digite
    +++wwilakehouse+++ e selecione a tabela **dimension_customer** na
    lista de tabelas e clique em **Confirm** para criar o novo modelo.

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

### Tarefa 4: Elaborar um relatório

1.  Agora, clique em **Fabric Lakehouse** **Tutorial-XX** no painel de
    navegação à esquerda.

> ![A screenshot of a computer Description automatically
> generated](./media/image34.png)

2.  Na visualização **Fabric Lakehouse Tutorial-XX**, selecione
    **wwilakehouse** do tipo **Semantic model**.

> ![A screenshot of a computer Description automatically
> generated](./media/image35.png)

3.  No painel do modelo semântico, você pode visualizar todas as
    tabelas. Você tem opções para criar relatórios do zero, relatórios
    paginados ou deixar o Power BI criar um relatório automaticamente
    com base nos seus dados. Para este tutorial, em **Explore this
    data**, selecione **Auto-create a report,** como mostrado na imagem
    abaixo.

> ![A screenshot of a computer Description automatically
> generated](./media/image36.png)

4.  Agora que o relatório está pronto, clique em **View report now**
    para abri-lo e analisá-lo.![A screenshot of a computer AI-generated
    content may be incorrect.](./media/image37.png)

> ![A screenshot of a computer Description automatically
> generated](./media/image38.png)

5.  Como a tabela é uma dimensão e não possui medidas, o Power BI cria
    uma medida para a contagem de linhas, agrega os dados em diferentes
    colunas e gera diferentes gráficos, conforme mostrado na imagem a
    seguir.

6.  Salve este relatório para uso futuro selecionando **Save** na faixa
    de opções superior.

> ![A screenshot of a computer Description automatically
> generated](./media/image39.png)

7.  Na caixa de diálogo **Save your report**, insira um nome para o seu
    relatório como +++dimension_customer-report+++ e selecione **Save.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image40.png)

8.  Você verá uma notificação informando **Report saved**.

> ![A screenshot of a computer Description automatically
> generated](./media/image41.png)

# Exercício 2: Ingerir dados no Lakehouse

Neste exercício, você ingere tabelas dimensionais e de fatos adicionais
da Wide World Importers (WWI) no Lakehouse.

### Tarefa 1: Ingerir dados

1.  Agora, clique no **Fabric Lakehouse** **Tutorial-XX** no painel de
    navegação à esquerda.

> ![A screenshot of a computer Description automatically
> generated](./media/image42.png)

2.  Selecione novamente o nome do espaço de trabalho.

![A screenshot of a computer screen Description automatically
generated](./media/image43.png)

3.  Na página do espaço de trabalho **Fabric Lakehouse Tutorial-XX**,
    navegue e clique no botão **+New item** e, em seguida, selecione
    **Pipeline**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image44.png)

4.  Na caixa de diálogo **New pipeline**, especifique o nome como
    **+++IngestDataFromSourceToLakehouse+++** e selecione **Create.** Um
    novo pipeline do Data Factory será criado e aberto.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image45.png)

> ![A screenshot of a computer Description automatically
> generated](./media/image46.png)

5.  No pipeline do Data Factory recém-criado, ou seja,
    **IngestDataFromSourceToLakehouse**, selecione o menu suspenso
    **Copy data** e escolha a opção **Add to canvas**.

> ![A screenshot of a computer Description automatically
> generated](./media/image47.png)

6.  Com o **copy data** selecionado, navegue até a aba **Source**.

![](./media/image48.png)

7.  Selecione **Connection** no menu suspenso e escolha a opção **Browse
    all.**

![](./media/image49.png)

8.  Selecione **Sample data** no painel esquerdo e escolha **Retail Data
    Model from Wide World Importers**.

![](./media/image50.png)

9.  Na janela **Connect to data source**, selecione **Retail Data Model
    from Wide World Importers** para visualizá-lo e selecione **OK**.

> ![](./media/image51.png)

10. A conexão da fonte de dados é selecionada como dados de exemplo.

![A screenshot of a computer Description automatically
generated](./media/image52.png)

11. Agora, navegue até a aba **destination**.

![](./media/image53.png)

12. Na aba **Destination**, clique no menu suspenso **connection** e
    selecione a opções **Browse** **all**.

![](./media/image54.png)

13. Na janela **Destination Window**, selecione **OneLake catalog** no
    painel esquerdo e escolha o **wwilakehouse**.

> ![](./media/image55.png)

14. O destino agora está selecionado como Lakehouse. Especifique a
    **Root Folder** como **Files** e certifique-se de que o formato de
    arquivo selecionado seja **Parquet.**

![A screenshot of a computer Description automatically
generated](./media/image56.png)

15. Clique em **Run** para executar a cópia dos dados.

> ![A screenshot of a computer Description automatically
> generated](./media/image57.png)

16. Clique no botão **Save and run** para que o pipeline seja salvo e
    executado.

> ![A screenshot of a computer error Description automatically
> generated](./media/image58.png)

17. O processo de cópia de dados leva aproximadamente de 1 a 3 minutos
    para ser concluído.

> ![A screenshot of a computer Description automatically
> generated](./media/image59.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image60.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

18. Na aba **Output**, selecione **Copy data1** para visualizar os
    detalhes da transferência de dados. Após visualizar o
    **Status,** como **Succeeded**, clique no botão **Close**.

> ![A screenshot of a computer Description automatically
> generated](./media/image62.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image63.png)

19. Após a execução bem-sucedida do pipeline, acesse seu Lakehouse
    (**wwilakehouse**) e abra o Explorer para visualizar os dados
    importados.

> ![A screenshot of a computer Description automatically
> generated](./media/image64.png)

20. Verifique se todas as **pastas WideWorldImporters** estão presentes
    na visualização do **Explorer** e contêm dados para todas as
    tabelas.

> ![A screenshot of a computer Description automatically
> generated](./media/image65.png)
>
> Exercício 3: Preparar e transformar dados no Lakehouse

### Tarefa 1: Transformar dados e carregar na tabela Delta prateada

1.  Na página **wwilakehouse**, navegue até o menu suspenso **Open
    notebook** na barra de comandos e clique em **New notebook**.

> ![A screenshot of a computer Description automatically
> generated](./media/image66.png)

2.  No bloco de notas aberto no **Lakehouse Explorer**, você verá que o
    bloco de notas já está vinculado ao Lakehouse aberto.

> ![](./media/image67.png)
>
> **Observação:** O Fabric oferece o recurso
> [**V-order**](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order)
> para gravar arquivos do Delta Lake de forma otimizada. O V-order
> geralmente melhora a compactação em três a quatro vezes e pode
> oferecer até 10 vezes mais aceleração de desempenho em comparação com
> arquivos do Delta Lake que não estão otimizados. O Spark no Fabric
> otimiza dinamicamente as partições ao gerar arquivos com um tamanho
> padrão de 128 MB. O tamanho de arquivo de destino pode ser alterado de
> acordo com os requisitos de cada carga de trabalho por meio de
> configurações. Com o recurso [**optimize
> write**](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order#what-is-optimized-write),
> o mecanismo do Apache Spark reduz o número de arquivos gravados e
> busca aumentar o tamanho individual dos arquivos de dados gerados.

3.  Antes de gravar dados como tabelas Delta Lake na seção **Tables** do
    Lakehouse, você usa dois recursos do Fabric (**V-order** e
    **Optimize Write**) para otimizar a gravação de dados e melhorar o
    desempenho de leitura. Para habilitar esses recursos em sua sessão,
    defina essas configurações na primeira célula do seu bloco de notas.

4.  Atualize o código na **Célula** com o seguinte código e clique em
    **▷ Run cell**, que aparece à esquerda da célula ao passar o cursor
    sobre ela.

> \# Copyright (c) Microsoft Corporation.
>
> \# Licensed under the MIT License.
>
> spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
>
> spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
>
> spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize",
> "1073741824")
>
> ![A screenshot of a computer Description automatically
> generated](./media/image68.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image69.png)
>
> **Observação**: Ao executar uma célula, não foi necessário especificar
> os detalhes do Pool ou cluster Spark subjacente, pois o Fabric os
> fornece através do Live Pool. Cada espaço de trabalho do Fabric vem
> com um pool Spark padrão, chamado Live Pool. Isso significa que, ao
> criar blocos de notas, não precisa se preocupar em especificar nenhuma
> configuração do Spark ou detalhes do cluster. Ao executar o primeiro
> comando do bloco de notas, o Live Pool fica pronto e começa a
> funcionar em poucos segundos. A sessão do Spark é estabelecida e
> começa a executar o código. A execução do código subsequente é quase
> instantânea neste bloco de notas enquanto a sessão do Spark estiver
> ativa.

5.  Em seguida, você lê os dados brutos da seção **Files** do Lakehouse
    e adiciona mais colunas para diferentes partes da data como parte da
    transformação. Você usa a API do Spark partitionBy para particionar
    os dados antes de gravá-los como uma tabela Delta, com base nas
    colunas de parte de dados recém-criadas (Ano e Trimestre).

6.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

> **Observação**: Caso não consiga visualizar o resultado, clique nas
> linhas horizontais à esquerda dos **Spark jobs**.
>
> from pyspark.sql.functions import col, year, month, quarter
>
> table_name = 'fact_sale'
>
> df = spark.read.format("parquet").load('Files/fact_sale_1y_full')
>
> df = df.withColumn('Year', year(col("InvoiceDateKey")))
>
> df = df.withColumn('Quarter', quarter(col("InvoiceDateKey")))
>
> df = df.withColumn('Month', month(col("InvoiceDateKey")))
>
> df.write.mode("overwrite").format("delta").partitionBy("Year","Quarter").save("Tables/" +
> table_name)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image70.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image71.png)
>
> ![](./media/image72.png)

7.  Após o carregamento das tabelas, você pode prosseguir com o
    carregamento dos dados para as demais dimensões. A célula a seguir
    cria uma função para ler os dados brutos da seção **Files** do
    Lakehouse para cada um dos nomes de tabela passados como parâmetro.
    Em seguida, cria uma lista de tabelas de dimensão. Por fim, percorre
    a lista de tabelas e cria uma tabela Delta para cada nome de tabela
    lido do parâmetro de entrada.

8.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão ▷ **Run cell** e revise a saída.

> from pyspark.sql.types import \*
>
> def loadFullDataFromSource(table_name):
>
> df = spark.read.format("parquet").load('Files/' + table_name)
>
> df = df.drop("Photo")
>
> df.write.mode("overwrite").format("delta").save("Tables/" +
> table_name)
>
> full_tables = \[
>
> 'dimension_city',
>
> 'dimension_customer',
>
> 'dimension_date',
>
> 'dimension_employee',
>
> 'dimension_stock_item'
>
> \]
>
> for table in full_tables:
>
> loadFullDataFromSource(table)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)
>
> ![](./media/image74.png)

9.  Para validar as tabelas criadas, clique e selecione **Refresh** em
    **Tables** no painel **Explorer** até que todas as tabelas apareçam
    na lista.

> ![](./media/image75.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image76.png)
>
> Tarefa 2: Transformar dados empresariais para agregação
>
> Uma organização pode ter engenheiros de dados que trabalham com
> Scala/Python e outros engenheiros de dados que trabalham com SQL
> (Spark SQL ou T-SQL), todos trabalhando na mesma cópia dos dados. O
> Fabric possibilita que esses diferentes grupos, com experiências e
> preferências variadas, trabalhem e colaborem. As duas abordagens
> diferentes transformam e geram agregados de negócios. Pode escolher a
> que for mais adequada para si ou misturar e combinar essas abordagens
> com base na sua preferência, sem comprometer o desempenho:

- **Abordagem nº 1** - Utilizar PySpark para unir e agregar dados para
  gerar agregados de negócios. Essa abordagem é mais indicada para quem
  tem experiência em programação (Python ou PySpark).

- **Abordagem nº 2** - Use o Spark SQL para unir e agregar dados para
  gerar agregados de negócios. Essa abordagem é mais indicada para quem
  tem experiência em SQL e está fazendo a transição para o Spark.

> **Abordagem nº 1 (sale_by_date_city)**
>
> Use o PySpark para unir e agregar dados a fim de gerar agregações de
> negócios. Com o código a seguir, você cria três estruturas de dados do
> Spark diferentes, cada um fazendo referência a uma tabela Delta
> existente. Em seguida, você une essas tabelas usando as estruturas de
> dados, realiza um **group by** para gerar as agregações, renomeia
> algumas das colunas e, por fim, grava o resultado como uma tabela
> Delta na seção **Tables** do Lakehouse para persistir com os dados.

1.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

> Nesta célula, você cria três estruturas de dados do Spark diferentes,
> cada um fazendo referência a uma tabela Delta existente.
>
> df_fact_sale = spark.read.table("wwilakehouse.fact_sale")
>
> df_dimension_date = spark.read.table("wwilakehouse.dimension_date")
>
> df_dimension_city = spark.read.table("wwilakehouse.dimension_city")
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image77.png)

2.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

> Nesta célula, você une essas tabelas usando as estruturas de dados
> criados anteriormente, realiza um group by para gerar agregações,
> renomeia algumas colunas e, finalmente, grava o resultado como uma
> tabela Delta na seção **Tables** do Lakehouse.
>
> sale_by_date_city = df_fact_sale.alias("sale") \\
>
> .join(df_dimension_date.alias("date"), df_fact_sale.InvoiceDateKey ==
> df_dimension_date.Date, "inner") \\
>
> .join(df_dimension_city.alias("city"), df_fact_sale.CityKey ==
> df_dimension_city.CityKey, "inner") \\
>
> .select("date.Date", "date.CalendarMonthLabel", "date.Day",
> "date.ShortMonth", "date.CalendarYear", "city.City",
> "city.StateProvince",
>
> "city.SalesTerritory", "sale.TotalExcludingTax", "sale.TaxAmount",
> "sale.TotalIncludingTax", "sale.Profit")\\
>
> .groupBy("date.Date", "date.CalendarMonthLabel", "date.Day",
> "date.ShortMonth", "date.CalendarYear", "city.City",
> "city.StateProvince",
>
> "city.SalesTerritory")\\
>
> .sum("sale.TotalExcludingTax", "sale.TaxAmount",
> "sale.TotalIncludingTax", "sale.Profit")\\
>
> .withColumnRenamed("sum(TotalExcludingTax)",
> "SumOfTotalExcludingTax")\\
>
> .withColumnRenamed("sum(TaxAmount)", "SumOfTaxAmount")\\
>
> .withColumnRenamed("sum(TotalIncludingTax)",
> "SumOfTotalIncludingTax")\\
>
> .withColumnRenamed("sum(Profit)", "SumOfProfit")\\
>
> .orderBy("date.Date", "city.StateProvince", "city.City")
>
> sale_by_date_city.write.mode("overwrite").format("delta").option("overwriteSchema",
> "true").save("Tables/aggregate_sale_by_date_city")
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image78.png)
>
> **Abordagem nº 2 (sale_by_date_employee)**
>
> Use o Spark SQL para unir e agregar dados para gerar agregações de
> negócios. Com o código a seguir, você cria uma visualização temporária
> do Spark unindo três tabelas, realiza um **group by** para gerar
> agregações e renomeia algumas colunas. Por fim, você lê os dados da
> visualização temporária do Spark e os grava como uma tabela Delta na
> seção **Tables** do Lakehouse para persistir com os dados.

3.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

> Nessa célula, você cria uma visualização temporária do Spark unindo
> três tabelas, usa o **group by** para gerar agregações e renomeia
> algumas colunas.
>
> %%sql
>
> CREATE OR REPLACE TEMPORARY VIEW sale_by_date_employee
>
> AS
>
> SELECT
>
> DD.Date, DD.CalendarMonthLabel
>
> , DD.Day, DD.ShortMonth Month, CalendarYear Year
>
> ,DE.PreferredName, DE.Employee
>
> ,SUM(FS.TotalExcludingTax) SumOfTotalExcludingTax
>
> ,SUM(FS.TaxAmount) SumOfTaxAmount
>
> ,SUM(FS.TotalIncludingTax) SumOfTotalIncludingTax
>
> ,SUM(Profit) SumOfProfit
>
> FROM wwilakehouse.fact_sale FS
>
> INNER JOIN wwilakehouse.dimension_date DD ON FS.InvoiceDateKey =
> DD.Date
>
> INNER JOIN wwilakehouse.dimension_Employee DE ON FS.SalespersonKey =
> DE.EmployeeKey
>
> GROUP BY DD.Date, DD.CalendarMonthLabel, DD.Day, DD.ShortMonth,
> DD.CalendarYear, DE.PreferredName, DE.Employee
>
> ORDER BY DD.Date ASC, DE.PreferredName ASC, DE.Employee ASC
>
>  ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image79.png)

4.  Use o ícone **+ Code** abaixo da saída da célula para adicionar uma
    nova célula de código ao bloco de notas e insira o código a seguir.
    Clique no botão **▷ Run cell** e revise a saída.

> Nesta célula, você lê a visualização temporária do Spark criada na
> célula anterior e, finalmente, a grava como uma tabela Delta na seção
> **Tables** do Lakehouse.
>
> sale_by_date_employee = spark.sql("SELECT \* FROM
> sale_by_date_employee")
>
> sale_by_date_employee.write.mode("overwrite").format("delta").option("overwriteSchema",
> "true").save("Tables/aggregate_sale_by_date_employee")
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image80.png)

5.  Para validar as tabelas criadas, clique e selecione **Refresh** em
    **Tables** até que as tabelas agregadas apareçam.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image81.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image82.png)
>
> Ambas as abordagens produzem resultados semelhantes. Você pode
> escolher com base em sua experiência e preferência, para minimizar a
> necessidade de aprender uma nova tecnologia ou comprometer o
> desempenho.
>
> Você também pode notar que está gravando dados como arquivos Delta
> Lake. O recurso de descoberta e registro automático de tabelas do
> Fabric os detecta e registra no metastore. Você não precisa chamar
> explicitamente instruções CREATE TABLE para criar tabelas para usar
> com SQL.
>
> Exercício 4: Criar relatórios no Microsoft Fabric
>
> Nesta seção do tutorial, você criará um modelo de dados do Power BI e
> um relatório do zero.

### Tarefa 1: Explorar os dados na camada prateada usando o endpoint SQL

> O Power BI está integrado nativamente em toda a experiência do Fabric.
> Essa integração nativa traz um modo exclusivo, chamado DirectLake,
> para acessar os dados do Lakehouse e proporcionar a experiência de
> consulta e geração de relatórios mais eficiente. O modo DirectLake é
> um novo recurso inovador do mecanismo para analisar conjuntos de dados
> muito grandes no Power BI. A tecnologia se baseia na ideia de carregar
> arquivos no formato Parquet diretamente de um Data Lake, sem precisar
> consultar um armazenamento de dados ou endpoint do Lakehouse, e sem
> precisar importar ou duplicar dados em um conjunto de dados do Power
> BI. O DirectLake é um caminho rápido para carregar os dados do data
> lake diretamente no mecanismo do Power BI, prontos para análise.
>
> No modo DirectQuery tradicional, o mecanismo do Power BI consulta
> diretamente os dados da fonte para executar cada consulta, e o
> desempenho da consulta depende da velocidade de recuperação dos dados.
> O DirectQuery elimina a necessidade de copiar dados, garantindo que
> quaisquer alterações na fonte sejam refletidas imediatamente nos
> resultados da consulta durante a importação. Por outro lado, no modo
> de importação, o desempenho é melhor porque os dados estão prontamente
> disponíveis na memória, sem a necessidade de consultar os dados da
> fonte para cada execução de consulta. No entanto, o mecanismo do Power
> BI precisa primeiro copiar os dados para a memória durante a
> atualização de dados. Somente as alterações na fonte de dados
> subjacente são detectadas durante a próxima atualização de dados
> (tanto em atualizações agendadas quanto sob demanda).
>
> DirectLake agora elimina essa necessidade de importação, carregando os
> arquivos de dados diretamente na memória. Como não há um processo de
> importação explícito, é possível detectar quaisquer alterações na
> origem à medida que ocorrem, combinando assim as vantagens do
> DirectQuery e do modo de importação, evitando suas desvantagens. O
> modo DirectLake é, portanto, a escolha ideal para analisar conjuntos
> de dados muito grandes e conjuntos de dados com atualizações
> frequentes na origem.

1.  No menu à esquerda, selecione o ícone do espaço de trabalho e, em
    seguida, selecione o nome do espaço de trabalho.

> ![A screenshot of a computer Description automatically
> generated](./media/image83.png)

2.  No menu à esquerda, selecione **Fabric
    <Lakehouse-@lab.LabInstance.Id>** e, em seguida, selecione seu
    modelo semântico chamado **wwilakehouse**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image84.png)

3.  Na barra de menu superior, selecione **Open semantic model** para
    abrir o designer de modelo de dados.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image85.png)

4.  No canto superior direito, verifique se o designer do modelo de
    dados está no modo **Editing**. Isso deve alterar o texto da lista
    suspensa para “Editing”.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image86.png)

5.  Na faixa de opções do menu, selecione **Edit tables** para exibir a
    caixa de diálogo de sincronização de tabelas.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image87.png)

6.  Na caixa de diálogo **Edit semantic model,** selecione **select
    all**, em seguida, selecione **Confirm** na parte inferior da caixa
    de diálogo para sincronizar o modelo semântico.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image88.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image89.png)

7.  Na tabela **fact_sale**, arraste o campo **CityKey** e solte-o no
    campo **CityKey** da tabela **dimension_city** para criar uma
    relação. A caixa de diálogo **Create Relationship** será exibida.

> **Observação:** Reorganize as tabelas clicando na tabela, arrastando e
> soltando para que as tabelas **dimension_city** e **fact_sale** fiquem
> lado a lado. O mesmo vale para quaisquer duas tabelas com as quais
> você esteja tentando criar uma relação. Isso serve apenas para
> facilitar o processo de arrastar e soltar colunas entre as
> tabelas.![](./media/image90.png)

8.  Na caixa de diálogo **Create Relationship**:

    - **A Tabela 1** é preenchida com **fact_sale** e a coluna
      **CityKey**.

    - **A Tabela 2** é preenchida com a **dimension_city** e a coluna
      **CityKey**.

    - Cardinality: **Many to one (\*:1)**

    - Cross filter direction: **Single**

    - Deixe a caixa ao lado selecionada **Make this relationship
      active**.

    - Selecione a caixa ao lado **Assume referential integrity.**

    - Selecione **Save.**

> ![](./media/image91.png)

9.  Em seguida, adicione essas relações com as mesmas configurações de
    **Create Relationship** mostradas acima, mas com as seguintes
    tabelas e colunas:

    - **StockItemKey(fact_sale)** - **StockItemKey(dimension_stock_item)**

> ![](./media/image92.png)
>
> ![](./media/image93.png)

- **Salespersonkey(fact_sale)** - **EmployeeKey(dimension_employee)**

> ![](./media/image94.png)

10. Certifique-se de criar as relações entre os dois conjuntos abaixo
    usando os mesmos passos descritos acima.

    - **CustomerKey(fact_sale) - CustomerKey(dimension_customer)**

    - **InvoiceDateKey(fact_sale) - Date(dimension_date)**

11. Após adicionar essas relações, seu modelo de dados deverá ficar como
    mostrado na imagem abaixo e estará pronto para gerar relatórios.

> ![](./media/image95.png)
>
> Tarefa 2: Elaborar Relatório

1.  Na faixa de opções superior, selecione **File** e, em seguida,
    **Create new report** para começar a criar relatórios/painéis no
    Power BI.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image96.png)

2.  Na tela de relatório do Power BI, você pode criar relatórios para
    atender às suas necessidades de negócios arrastando as colunas
    necessárias do painel **Data** para a tela e usando uma ou mais
    visualizações disponíveis.

> ![](./media/image97.png)
>
> **Adicione um título:**

3.  Na faixa de opções, selecione **Text box**. Digite **WW Importers
    Profit Reporting**. **Selecione** o **texto** e aumente o tamanho
    para **20**.

> ![](./media/image98.png)

4.  Redimensione a caixa de texto e posicione-a no **canto superior
    esquerdo** da página do relatório. Em seguida, clique fora da caixa
    de texto.

> ![](./media/image99.png)
>
> **Adicione um cartão:**

- No painel **Data**, expanda **fact_sales** e marque a caixa ao lado de
  **Profit**. Essa seleção cria um gráfico de colunas e adiciona o campo
  ao eixo Y.

> ![](./media/image100.png)

5.  Com o gráfico de barras selecionado, selecione o visual **Card** no
    painel de visualização.

> ![](./media/image101.png)

6.  Esta opção converte a imagem em um cartão. Coloque o cartão abaixo
    do título.

> ![](./media/image102.png)

7.  Clique em qualquer lugar na tela em branco (ou pressione a tecla
    Esc) para que o cartão que acabamos de inserir não esteja mais
    selecionado.

> **Adicione um gráfico de barras:**

8.  No painel **Data**, expanda **fact_sales** e marque a caixa ao lado
    de **Profit**. Essa seleção cria um gráfico de colunas e adiciona o
    campo ao eixo Y.

> ![](./media/image103.png)

9.  No painel **Data**, expanda a **dimension_city** e marque a caixa de
    seleção **SalesTerritory**. Essa seleção adiciona o campo ao eixo Y.

> ![](./media/image104.png)

10. Com o gráfico de barras selecionado, escolha o visual **Clustered
    bar chart** no painel de visualização. Essa seleção converte o
    gráfico de colunas em um gráfico de barras.

> ![](./media/image105.png)

11. Redimensione o gráfico de barras para preencher a área abaixo do
    título e do cartão.

> ![](./media/image106.png)

12. Clique em qualquer lugar na tela em branco (ou pressione a tecla
    Esc) para que o gráfico de barras não seja mais selecionado.

> **Crie um gráfico de área empilhada visual:**

13. No painel **Visualizations**, selecione o **Stacked area chart**.

> ![](./media/image107.png)

14. Reposicione e redimensione o gráfico de área empilhada à direita dos
    visuais do gráfico de cartão e barra criados nas etapas anteriores.

> ![](./media/image108.png)

15. No painel **Data**, expanda **fact_sales** e marque a caixa ao lado
    de **Profit**. Expanda **dimension_date** e marque a caixa ao lado
    de **FiscalMonthNumber**. Essa seleção cria um gráfico de linhas
    preenchido que mostra o lucro por mês fiscal.

> ![](./media/image109.png)

16. No painel **Data**, expanda **dimension_stock_item** e arraste
    **BuyingPackage** para o campo **Legend**. Essa seleção adiciona uma
    linha para cada um dos **Buying Packages**.

> ![](./media/image110.png) ![](./media/image111.png)

17. Clique em qualquer lugar da tela em branco (ou pressione a tecla
    Esc) para que o gráfico de área empilhada não fique mais
    selecionado.

> **Crie um gráfico de colunas:**

18. No painel **Visualizations**, selecione o visual **Stacked column
    chart**.

> ![](./media/image112.png)

19. No painel **Data**, expanda **fact_sales** e marque a caixa ao lado
    de **Profit**. Essa seleção adiciona o campo ao eixo Y.

20. No painel **Data**, expanda **dimension_employee** e marque a caixa
    ao lado de **Employee**. Essa seleção adiciona o campo ao eixo X.

> ![](./media/image113.png)

21. Clique em qualquer lugar na tela em branco (ou pressione a tecla
    Esc) para que o gráfico não seja mais selecionado.

22. Na faixa de opções, selecione **File** \> **Save**.

> ![](./media/image114.png)

23. Insira o nome do seu relatório como **Profit Reporting**. Selecione
    **Save**.

> ![](./media/image115.png)

24. Você receberá uma notificação informando que o relatório foi salvo.

> ![](./media/image116.png)
>
> Exercício 5: Limpar recursos
>
> Você pode excluir relatórios individuais, pipelines, armazéns e outros
> itens ou remover todo o espaço de trabalho. Siga as etapas a seguir
> para excluir o espaço de trabalho que você criou para este tutorial.

1.  Selecione seu espaço de trabalho, o **Fabric Lakehouse
    Tutorial-XX,** no menu de navegação à esquerda. Isso abrirá a
    visualização do item do espaço de trabalho.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image117.png)

2.  Selecione a opção**...** abaixo do nome do espaço de trabalho e
    selecione **Workspace settings**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image118.png)

3.  Selecione **General** e **Remove this workspace.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image119.png)

4.  Clique em **Delete** no aviso que aparecer.

> ![](./media/image120.png)

5.  Aguarde uma notificação informando que o espaço de trabalho foi
    excluído antes de prosseguir para o próximo laboratório.

> ![](./media/image121.png)
>
> **Resumo**: Este laboratório prático concentra-se na configuração e no
> setup de componentes essenciais do Microsoft Fabric e do Power BI para
> gerenciamento e geração de relatórios de dados. Inclui tarefas como
> ativação de versões de avaliação, configuração do OneDrive, criação de
> espaços de trabalho e configuração de Lakehouses. O laboratório também
> aborda tarefas relacionadas à ingestão de dados de amostra, otimização
> de tabelas Delta e criação de relatórios no Power BI para uma análise
> de dados eficaz. Os objetivos visam proporcionar experiência prática
> na utilização do Microsoft Fabric e do Power BI para gerenciamento e
> geração de relatórios de dados.
