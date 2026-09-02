# Caso de uso 02: Solução do Data Factory para transferência e transformação de dados com fluxos de dados e pipelines de dados

**Introdução**

Este laboratório ajuda você a acelerar o processo de avaliação do Data
Factory no Microsoft Fabric, fornecendo um guia passo a passo para um
cenário completo de integração de dados em uma hora. Ao final deste
tutorial, você compreenderá o valor e os principais recursos do Data
Factory e saberá como concluir um cenário comum de integração de dados
de ponta a ponta.

**Objetivo**

O laboratório está dividido em três exercícios:

- **Exercício 1:** Criar um pipeline com o Data Factory para importar
  dados brutos de um armazenamento Blob para uma tabela Bronze em um
  Data Lakehouse.

- **Exercício 2:** Transformar dados com um fluxo de dados no Data
  Factory para processar os dados brutos da sua tabela Bronze e movê-los
  para uma tabela Gold no Data Lakehouse.

- **Exercício 3:** Autonomizar e enviar notificações com o Data Factory
  para enviar um e-mail avisando assim que todas as tarefas forem
  concluídas e, por fim, configurar todo o fluxo para ser executado de
  forma programada.

## Exercício 1: Criar um pipeline com o Data Factory

### Tarefa 1: Criar um workspace do Fabric

Antes de trabalhar com dados no Fabric, crie um workspace com a
avaliação do Fabric habilitada.

1.  Abra o navegador, navegue até a barra de endereços e digite ou cole
    a seguinte URL: +++<https://app.fabric.microsoft.com/+++> e
    pressione o botão **Enter**.

**Observação:** Se você for direcionado para a página inicial do
Microsoft Fabric, ignore as etapas de nº 2 a nº 4.

![](./media/image1.png)

2.  Na janela do **Microsoft Fabric**, insira suas credenciais e clique
    no botão **Submit**.

![](./media/image2.png)

3.  Em seguida, na janela da **Microsoft**, insira sua senha e clique no
    botão **Sign in**.

![A login screen with a red box and blue text AI-generated content may
be incorrect.](./media/image3.png)

4.  Na janela **Stay signed in?**, clique no botão **Yes**.

![A screenshot of a computer error AI-generated content may be
incorrect.](./media/image4.png)

5.  Você será direcionado para a página inicial do Power BI.

![](./media/image5.png)

6.  Selecione o ícone padrão do Power BI no canto inferior esquerdo da
    tela e, em seguida, selecione **Fabric**.

![](./media/image6.png)

![](./media/image7.png)

![](./media/image8.png)

7.  Na **Microsoft Fabric Home Page**, selecione a opção **New
    workspace**.

![](./media/image9.png)

8.  Na aba **Create a workspace**, insira os seguintes detalhes e clique
    no botão **Apply**:

| Setting | Value |
|---|---|
| Name | +++Data-FactoryXXXX+++ (XXXX can be a unique number) |
| Advanced | Under **License mode**, select **Fabric** |
| Default storage format | **Small semantic model storage format** |

![](./media/image10.png)

![](./media/image11.png)

9.  Aguarde a conclusão da implementação. Esse processo levará
    aproximadamente 2–3 minutos.

![A screenshot of a computer Description automatically
generated](./media/image12.png)

### Tarefa 2: Criar um lakehouse e importar dados de amostra

1.  Na página do workspace **Data-FactoryXX**, navegue até a opção
    **+New item** e clique nela.

![A screenshot of a computer Description automatically
generated](./media/image13.png)

2.  Clique no bloco **“Lakehouse”.**

![A screenshot of a computer Description automatically
generated](./media/image14.png)

3.  Na caixa de diálogo **New lakehouse**, insira
    **+++DataFactoryLakehouse+++** no campo **Name** e **desmarque** a
    opção lakehouses schemas. Clique no botão **Create** e abra o novo
    lakehouse.

> ![](./media/image15.png)

![](./media/image16.png)

4.  Navegue até o Lakehouse, clique com o botão direito na pasta Files e
    selecione Upload \> Upload files para adicionar os arquivos.

![](./media/image17.png)

5.  Na aba Upload files, clique no ícone de **pasta** na seção Files.

![](./media/image18.png)

6.  Navegue até **C:\LabFiles** na sua VM, selecione o arquivo
    **/Labfiles/NYCTaxi/part-00000-907cea6d-0f54-4639-9a14-042dc04185ef-c000.snappy.parquet**
    e clique no botão **Open**.

![](./media/image19.png)

7.  Em seguida, clique no botão **Upload** e feche a janela.

![](./media/image20.png)

![](./media/image21.png)

![](./media/image22.png)

8.  Na barra de ferramentas, selecione **Analyze data** no menu
    suspenso, aponte para **Notebook** e, em seguida, selecione **New
    notebook**.

![](./media/image23.png)

9.  Adicione o seguinte código PySpark para criar uma sessão do Spark,
    ler o arquivo Parquet carregado da pasta Files do Lakehouse e gravar
    os dados em uma tabela chamada Bronze, substituindo quaisquer dados
    existentes na tabela.

```
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("LoadParquet").getOrCreate()
# Read the green_tripdata_2017 parquet file
df2 = spark.read.format("parquet").load("Files/part-00000-907cea6d-0f54-4639-9a14-042dc04185ef-c000.snappy.parquet")

# Write to table
df2.write.mode("overwrite").saveAsTable("Bronze")
```

![](./media/image24.png)

![](./media/image25.png)

7.  Para validar as tabelas criadas, clique com o botão direito do mouse
    no **DataFactoryLakehouse** no Explorer e selecione **Refresh**. As
    tabelas serão exibidas.

![](./media/image26.png)

![](./media/image27.png)

![](./media/image28.png)

## Exercício 2: Transformar dados com um fluxo de dados no Data Factory

### Tarefa 1: Obter dados de uma tabela do Lakehouse

1.  Agora, clique no workspace **Data Factory-@lab.LabInstance.Id** no
    painel de navegação à esquerda.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image29.png)

2.  Crie um novo Dataflow Gen2 clicando no botão **+New item** na barra
    de navegação. Na lista de itens disponíveis, selecione o item
    **Dataflow Gen2**.

![](./media/image30.png)

3.  Forneça o nome **+++nyc_taxi_data_with_discounts+++** para o novo
    Dataflow Gen2 e, em seguida, selecione **Create**.

![](./media/image31.png)

4.  No novo menu do Dataflow, no painel **Power Query**, clique na
    **menu suspenso** **Get data** e, em seguida, selecione **More...**.

![A screenshot of a computer Description automatically
generated](./media/image32.png)

5.  Na aba **Choose data source**, no campo de pesquisa, digite
    **+++Lakehouse+++** e, em seguida, clique no conector **Lakehouse**.

![A screenshot of a computer Description automatically
generated](./media/image33.png)

6.  A caixa de diálogo **Connect to data source** será exibida, e uma
    nova conexão será criada automaticamente com base no usuário
    conectado no momento. Selecione **Next**.

![A screenshot of a computer Description automatically
generated](./media/image34.png)

7.  A caixa de diálogo **Choose data** será exibida. Use o painel de
    navegação para localizar o **workspace- Data-FactoryXX** e
    expanda-o. Em seguida, expanda **Lakehouse - DataFactoryLakehouse**,
    criado para o destino no módulo anterior, selecione a tabela
    **Bronze** na lista e clique no botão **Create**.

![](./media/image35.png)

8.  Você verá que o canvas agora está preenchido com os dados.

> ![](./media/image36.png)

### Tarefa 2: Transformar os dados importados do Lakehouse

1.  Selecione o ícone de data type no cabeçalho da segunda
    coluna, **IpepPickupDatetime**, para exibir um menu suspenso. No
    menu, selecione o tipo de dados Date para converter a coluna de
    **Date/Time** para o tipo **Date**.

![](./media/image37.png)

2.  Na aba **Home** da faixa de opções, selecione a opção **Choose
    columns** no grupo **Manage columns**.

![](./media/image38.png)

3.  Na caixa de diálogo **Choose columns**, **desmarque** algumas das
    colunas listadas e, em seguida, selecione **OK**.

    - lpepDropoffDatetime

    -  DoLocationID

![](./media/image39.png)

4.  Selecione o menu suspenso de filter and sort da coluna
    **storeAndFwdFlag**. (Se aparecer o aviso **List may be
    incomplete**, selecione **Load more** para visualizar todos os
    dados.)

![](./media/image40.png)

5.  Selecione **'Y'** para exibir somente as linhas em que um desconto
    foi aplicado e, em seguida, selecione **OK**.

![](./media/image41.png)

6.  Selecione o menu suspenso de sort and filter da coluna
    **lpep_Pickup_Datetime**. Em seguida, selecione **Date filters** e
    escolha o filtro **Between...**, disponível para os tipos Date e
    Date/Time.

![](./media/image42.png)

7.  Na caixa de diálogo **Filter rows**, selecione as datas entre
    **January 1, 2017** e **January 31, 2017** e, em seguida, selecione
    **OK**.

![](./media/image43.png)

![](./media/image44.png)

### Tarefa 3: Conectar-se a um arquivo CSV que contém dados de descontos

Agora que os dados das viagens estão disponíveis, queremos carregar os
dados que contêm os respectivos descontos para cada dia e VendorID e
preparar os dados antes de combiná-los com os dados das viagens.

1.  Na aba **Home** do menu do editor de dataflow, selecione a opção
    **Get data** e, em seguida, escolha **Text/CSV**.

![](./media/image45.png)

2.  No painel **Connect to data source**, em **Connection settings**,
    selecione o botão de opção **Link to file**. Em seguida, insira
    +++https://raw.githubusercontent.com/ekote/azure-architect/master/Generated-NYC-Taxi-Green-Discounts.csv+++ e
    defina o nome da conexão como **+++dfconnection+++**. Certifique-se
    de que **authentication kind** esteja definido como **Anonymous**.
    Clique no botão **Next**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image46.png)

3.  Na caixa de diálogo **Preview file data**, selecione **Create**.

![A screenshot of a computer Description automatically
generated](./media/image47.png)

![](./media/image48.png)

### Tarefa 4: Transformar os dados de desconto

1.  Ao revisar os dados, vemos que os cabeçalhos parecem estar na
    primeira linha. Promova-os a cabeçalhos selecionando o menu de
    contexto da tabela, no canto superior esquerdo da área de
    visualização, e selecione **Use first row as headers**.

![](./media/image49.png)

***Observação:** Depois de promover os cabeçalhos, você verá uma nova
etapa adicionada ao painel **Applied steps**, na parte superior do
editor de dataflow, referente aos tipos de dados das suas colunas.*

![](./media/image50.png)

2.  Clique com o botão direito do mouse na coluna **VendorID** e, no
    menu de contexto exibido, selecione a opção **Unpivot other
    columns**. Isso permite transformar as colunas em pares de
    atributo-valor, nos quais as colunas se tornam linhas.

![](./media/image51.png)

3.  Com a tabela não dinamizada, renomeie as colunas **Attribute** e
    **Value** clicando duas vezes sobre elas e alterando **Attribute**
    para **+++Date+++** e **Value** para **+++Discount+++**.

![](./media/image52.png)

4.  Altere o tipo de dados da coluna **Date** selecionando o menu de
    tipo de dados à esquerda do nome da coluna e escolhendo **Date**.

![](./media/image53.png)

5.  Selecione a coluna **Discount** e, em seguida, selecione a aba
    **Transform** no menu. Selecione **Number column** e, depois, em
    **Standard** nas transformações numéricas, selecione **Divide**.

![](./media/image54.png)

6.  Na caixa de diálogo **Divide**, insira o valor **+++**100**+++** e,
    em seguida, clique no botão **OK**.

![A screenshot of a computer Description automatically
generated](./media/image55.png)

![](./media/image56.png)

### Tarefa 7: Combinar os dados de viagens e descontos

A próxima etapa é combinar as duas tabelas em uma única tabela que
contenha o desconto a ser aplicado à viagem e o total ajustado.

1.  Primeiro, ative o botão **Diagram view** para que você possa
    visualizar ambas as consultas.

![](./media/image57.png)

2.  Selecione a consulta **Bronze** e, na aba **Home**, selecione o menu
    **Combine**. Em seguida, escolha **Merge queries** e depois **Merge
    queries as new**.

![](./media/image58.png)

3.  Na caixa de diálogo **Merge**, selecione
    **Generated-NYC-Taxi-Green-Discounts** no menu suspenso **Right
    table for merge** e, em seguida, selecione o ícone de **“lâmpada”**
    no canto superior direito da caixa de diálogo para visualizar o
    mapeamento sugerido das colunas entre as três tabelas.

4.  Escolha cada um dos dois mapeamentos de colunas sugeridos, um de
    cada vez, mapeando as colunas VendorID e Date de ambas as tabelas.
    Quando os dois mapeamentos forem adicionados, os cabeçalhos das
    colunas correspondentes serão destacados em cada tabela.

![](./media/image59.png)

5.  Uma mensagem será exibida solicitando que você permita a combinação
    de dados de várias fontes de dados para visualizar os resultados.
    Selecione **OK**. 

![](./media/image60.png)

6.  Na área da tabela, inicialmente será exibido um aviso informando que
    “The evaluation was canceled because combining data from multiple
    sources may reveal data from one source to another. Select Continue
    if the possibility of revealing data is okay.”

> Selecione **Continue** para exibir os dados combinados.

![](./media/image61.png)

7.  Na caixa de diálogo Privacy Levels, selecione a **caixa de seleção**
    :**Ignore Privacy Levels checks for this document. Ignoring privacy
    Levels could expose sensitive or confidential data to an
    unauthorized person** e clique no botão **Save**.

![](./media/image62.png)

![](./media/image63.png)

8.  Observe que uma nova consulta foi criada na Diagram view, mostrando
    o relacionamento da nova consulta Merge com as duas consultas
    criadas anteriormente. No painel da tabela do editor, role para a
    direita na lista de colunas da consulta Merge para visualizar uma
    nova coluna com valores de tabela. Essa é a coluna **"Generated NYC
    Taxi-Green-Discounts"**, e seu tipo é **\[Table\]**.

No cabeçalho da coluna, há um ícone com duas setas apontando em direções
opostas, permitindo selecionar colunas da tabela. Desmarque todas as
colunas, exceto **Discount**, e, em seguida, selecione **OK**.

![](./media/image64.png)

9.  Como o valor do desconto agora está no nível da linha, podemos criar
    uma nova coluna para calcular o total após o desconto. Para isso,
    selecione a aba **Add column** na parte superior do editor e escolha
    **Custom column** no grupo **General**.

![](./media/image65.png)

10. Na caixa de diálogo **Custom column**, você pode usar a linguagem de
    fórmulas do Power Query (também conhecida como M) para definir como
    a nova coluna deve ser calculada. Insira
    **+++TotalAfterDiscount+++** em **New column name**, selecione
    **Currency** em **Data type** e insira a seguinte expressão M no
    campo **Custom column formula**:

+++if [total_amount] > 0 then [total_amount] * ( 1 -[Discount] ) else [total_amount]+++

Em seguida, selecione **OK**.

![](./media/image66.png)

![](./media/image67.png)

11. Selecione a coluna recém-criada **TotalAfterDiscount** e, em
    seguida, selecione a aba **Transform** na parte superior da janela
    do editor. No grupo **Number column**, selecione o menu suspenso
    **Rounding** e, depois, escolha **Round...**.

**Observação**: Se você não encontrar a opção **Rounding**, expanda o
menu para visualizar **Number column**.

![](./media/image68.png)

12. Na caixa de diálogo **Round**, insira **2** no campo de número de
    casas decimais e, em seguida, selecione **OK**.

![](./media/image69.png)

13. Altere o tipo de dados de **lpepPickupDatetime** de **Date** para
    **Date/Time**.

![](./media/image70.png)

14. Por fim, expanda o painel **Query settings** no lado direito do
    editor, caso ainda não esteja expandido, e renomeie a consulta de
    **Merge** para **+++Output+++**.

![](./media/image71.png)

![](./media/image72.png)

### Tarefa 8: Carregar a consulta de saída em uma tabela no Lakehouse

Com a consulta de saída agora totalmente preparada e com os dados
prontos para serem gerados, podemos definir o destino de saída da
consulta.

1.  Selecione a consulta **Output** criada anteriormente. Em seguida,
    selecione o **+ icon** para adicionar **data destination** a este
    Dataflow.

2.  Na lista data destination, selecione a opção **Lakehouse** em New
    destination.

![](./media/image73.png)

3.  Na caixa de diálogo **Connect to data destination**, sua conexão já
    deverá estar selecionada. Selecione **Next** para continuar.

![A screenshot of a computer Description automatically
generated](./media/image74.png)

4.  Na caixa de diálogo **Choose destination target**, navegue até o
    Lakehouse e, em seguida, selecione **Next** novamente.

![](./media/image75.png)

5.  Na caixa de diálogo **Choose destination settings**, verifique
    novamente se as colunas estão mapeadas corretamente e selecione
    **Save settings**.

![](./media/image76.png)

6.  De volta à janela principal do editor, confirme que o destino de
    saída da tabela **Output** aparece no painel **Query settings** como
    **Lakehouse** e, em seguida, selecione a opção **Save and Run** na
    aba Home.

![](./media/image77.png)

![](./media/image78.png)

![](./media/image79.png)

9.  Agora, clique no **workspace** **Data Factory-XXXX** no painel de
    navegação do lado esquerdo.

![A screenshot of a computer Description automatically
generated](./media/image80.png)

10. No painel **Data FactoryXX**, selecione **DataFactoryLakehouse**
    para visualizar a nova tabela carregada.

![](./media/image81.png)

11. Confirme se a tabela **Output** aparece no esquema **dbo**.

![](./media/image82.png)

## Exercício 3: Automatizar e enviar notificações com o Data Factory

### Tarefa 1: Adicionar uma atividade do Office 365 Outlook ao seu pipeline

1.  Navegue até o espaço de trabalho **Data_FactoryXX** e clique nele no
    menu de navegação à esquerda.

![A screenshot of a computer Description automatically
generated](./media/image83.png)

2.  Selecione a opção **+ New item** na página do **workspace** e
    selecione **Pipeline**.

![A screenshot of a computer Description automatically
generated](./media/image84.png)

3.  Forneça First_Pipeline1 como **+++First_Pipeline1+++**e selecione
    **Create**.

![](./media/image85.png)

3.  Selecione a guia **Home** no editor do pipeline e localize a
    atividade **Add copy data**.

> ![](./media/image86.png)

5.  Na guia **Source**, insira as seguintes configurações e selecione
    **Test connection**.

| Setting | Value |
|---|---|
| Connection | +++dfconnection User-XXXX+++ |
| Connection Type | Select **HTTP** |
| File format | **Delimited Text** |

![](./media/image87.png)

6.  Na guia **Destination**, insira as seguintes configurações:

| Setting | Value |
|---|---|
| Connection | **Lakehouse** |
| Lakehouse | Select **DataFactoryLakehouse** |
| Root Folder | Select the **Table** radio button |
| Table | Select **New**, enter `+++Generated-NYC-Taxi-Green-Discounts+++`, and select **Create**. |

![](./media/image88.png)

![A screenshot of a computer Description automatically
generated](./media/image89.png)

7.  Na faixa de opções, selecione **Run**.

![](./media/image90.png)

8.  Na caixa de diálogo **Save and run?**, clique no botão **Save and
    run**.

![A screenshot of a computer Description automatically
generated](./media/image91.png)

![](./media/image92.png)

9.  Selecione a guia **Activities** no editor do pipeline e localize a
    atividade **Office Outlook**.

![](./media/image93.png)

10. Selecione e arraste o caminho On Success (uma caixa de seleção verde
    no canto superior direito da atividade na tela do pipeline) da
    atividade Copy data até a nova atividade Office 365 Outlook.

![A screenshot of a computer Description automatically
generated](./media/image94.png)

11. Selecione a atividade Office 365 Outlook na tela do pipeline e, em
    seguida, selecione a guia **Settings** na área de propriedades
    abaixo da tela para configurar o e-mail. Clique no menu suspenso
    **Connection** e selecione **Browse all**.

![A screenshot of a computer Description automatically
generated](./media/image95.png)

12. Na janela ‘choose a data source’, selecione a fonte **Office 365
    Email**.

![A screenshot of a computer Description automatically
generated](./media/image96.png)

13. Entre com a conta da qual deseja enviar o e-mail. Você pode usar a
    conexão existente com a conta que já está conectada.

![A screenshot of a computer Description automatically
generated](./media/image97.png)

14. Clique em **Connect** para prosseguir.

![A screenshot of a computer Description automatically
generated](./media/image98.png)

15. Selecione a atividade Office 365 Outlook na tela do pipeline e, na
    guia **Settings** da área de propriedades abaixo da tela, configure
    o e-mail.

    - Insira seu endereço de e-mail na seção **To.** Se quiser usar
      vários endereços, use ; para separá-los.

![A screenshot of a computer Description automatically
generated](./media/image99.png)

- Para **Subject**, selecione o campo para que a opção **Add dynamic
  content** seja exibida e, em seguida, selecione-a para abrir a tela do
  construtor de expressões do pipeline.

![A screenshot of a computer Description automatically
generated](./media/image100.png)

16. A caixa de diálogo **Pipeline expression builder** será exibida.
    Insira a expressão a seguir e, em seguida, selecione **OK**:

+++@concat('DI in an Hour Pipeline Succeeded with Pipeline Run Id', pipeline().RunId)+++

![](./media/image101.png)

17. Para **Body**, selecione novamente o campo e escolha a opção **View
    in expression builder** quando ela aparecer abaixo da área de texto.
    Adicione novamente a expressão a seguir na caixa de diálogo
    **Pipeline expression builder** que será exibida e, em seguida,
    selecione **OK**:

+++@concat('RunID = ', pipeline().RunId, ' ; ', 'Copied rows ', activity('Copy data1').output.rowsCopied, ' ; ','Throughput ', activity('Copy data1').output.throughput)+++

![](./media/image102.png)

![A screenshot of a computer Description automatically
generated](./media/image103.png)

\*\*  Observação:\*\* Substitua **Copy data1** pelo nome da sua própria
atividade de cópia no pipeline.

18. Finally select the **Home** tab at the top of the pipeline editor,
    and choose **Run**. Then select **Save and run** again on the
    confirmation dialog to execute these activities.

![A screenshot of a computer Description automatically
generated](./media/image104.png)

![A screenshot of a computer Description automatically
generated](./media/image105.png)

![](./media/image106.png)

![](./media/image107.png)

19. Depois que o pipeline for executado com sucesso, verifique seu
    e-mail para localizar o e-mail de confirmação enviado pelo pipeline.

![](./media/image108.png)

### Tarefa 2: Agendar a execução do pipeline

Depois de concluir o desenvolvimento e os testes do pipeline, você pode
agendá-lo para ser executado automaticamente.

1.  Na guia **Home** da janela do editor do pipeline, selecione
    **Schedule**.

![A screenshot of a computer Description automatically
generated](./media/image109.png)

2.  Configure o agendamento conforme necessário. O exemplo abaixo agenda
    o pipeline para ser executado diariamente às 20h00 até o final do
    ano.

![A screenshot of a schedule Description automatically
generated](./media/image110.png)

![](./media/image111.png)

![](./media/image112.png)

### Tarefa 3: Adicionar uma atividade de fluxo de dados ao pipeline

1.  Passe o cursor sobre a linha verde que conecta a **atividade Copy**
    à atividade **Office 365 Outlook** na tela do pipeline e selecione o
    botão + para inserir uma nova atividade.

![](./media/image113.png)

2.  Selecione **Dataflow** no menu exibido.

![](./media/image114.png)

3.  A atividade Dataflow recém-criada é inserida entre a atividade Copy
    e a atividade Office 365 Outlook e é selecionada automaticamente,
    exibindo suas propriedades na área abaixo da tela. Selecione a guia
    **Settings** na área de propriedades e, em seguida, selecione o
    dataflow criado no **Exercício 2: Transformar dados com um dataflow
    no Data Factory**.

![](./media/image115.png)

4.  Selecione a guia **Home** na parte superior do editor do pipeline e
    selecione **Run**. Em seguida, selecione **Save and run** novamente
    na caixa de diálogo de confirmação para executar essas atividades

![](./media/image116.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image117.png)

![](./media/image118.png)

![](./media/image119.png)

### Tarefa 4: Limpar recursos

Você pode excluir relatórios, pipelines, warehouses e outros itens
individualmente ou remover o workspace inteiro. Use as etapas a seguir
para excluir o workspace criado para este tutorial.

1.  Selecione seu **workspace, Data-FactoryvXX**, no menu de navegação à
    esquerda. A exibição dos itens do workspace será aberta.

![A screenshot of a computer Description automatically
generated](./media/image83.png)

2.  Selecione a opção **Workspace settings** na página do workspace,
    localizada no canto superior direito.

![](./media/image120.png)

3.  Selecione a **guia** **General** e, em seguida, selecione **Remove
    this workspace.**

![](./media/image121.png)
