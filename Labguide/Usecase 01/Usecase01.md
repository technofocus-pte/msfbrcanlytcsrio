# Caso de uso 01 – Criar um Lakehouse, importar dados de amostra e gerar um relatório

**Cenário**

**A Wide World Importers (WWI)** é uma organização global de varejo que
opera centenas de lojas em diversas regiões. As informações dos clientes
são coletadas a partir de vários sistemas operacionais, incluindo
aplicativos de point-of-sale (POS) plataformas de CRM e canais de
comércio eletrônico. Os dados são armazenados como arquivos CSV e
recebidos diariamente de diferentes unidades de negócios.

Atualmente, a equipe de análise da empresa dedica um tempo significativo
à importação manual de arquivos, à validação da qualidade dos dados e à
preparação de conjuntos de dados para a geração de relatórios. Esses
processos manuais causam atrasos na geração de insights sobre os
clientes e dificultam o acesso dos usuários de negócios a informações
consistentes e confiáveis.

Para modernizar sua plataforma de análise, a Wide World Importers adotou
o **Microsoft Fabric** como sua plataforma de dados unificada. A equipe
de engenharia de dados recebeu a tarefa de implementar uma solução
escalável utilizando o **Microsoft Fabric Data Factory** e o
**Lakehouse** para centralizar os dados dos clientes, possibilitar um
gerenciamento eficiente dos dados e simplificar a geração de relatórios.

Como engenheiro de dados, sua responsabilidade é criar um workspace no
Fabric, provisionar um Lakehouse, importar dados de clientes para o
OneLake, converter os arquivos de origem em tabelas Delta gerenciadas,
validar os dados importados usando o SQL Analytics Endpoint, criar um
modelo semântico no Direct Lake e gerar um relatório no Power BI que
permita que as partes interessadas da empresa analisem as informações
dos clientes com latência mínima.

Ao implementar essa solução, a Wide World Importers pode eliminar o
processamento manual de dados, oferecer uma fonte única e confiável de
informações para a análise de clientes e possibilitar decisões de
negócios mais rápidas e baseadas em dados, utilizando o Microsoft
Fabric.

**Introdução**

Neste caso de uso, você criará uma solução completa de engenharia de
dados utilizando o **Microsoft Fabric Data Factory** e o **Fabric
Lakehouse**. Começando com um novo workspace do Fabric, você fará a
ingestão de dados em um Lakehouse, converterá arquivos em tabelas Delta
gerenciadas, consultará os dados por meio de endpoints de análise SQL,
criará modelos semânticos e gerará relatórios interativos do Power BI.

Ao longo deste laboratório, você explorará como o Microsoft Fabric
unifica integração, armazenamento, transformação, análise e geração de
relatórios de dados em uma única plataforma de Software-as-a-Service
(SaaS). Ao concluir este exercício prático, você compreenderá como os
fluxos de trabalho modernos de engenharia de dados são implementados
usando o Fabric Data Factory, seguindo as melhores práticas do setor
para ingestão, gerenciamento e análise de dados.

**Objetivos**:

- Criar e configurar um workspace do Microsoft Fabric.

- Criar e configurar um Fabric Lakehouse.

- Importar dados de origem para o OneLake.

- Carregar arquivos em tabelas Delta gerenciadas.

- Consultar dados do Lakehouse usando o SQL Analytics Endpoint.

- Criar um modelo semântico do Direct Lake.

- Gerar e explorar relatórios do Power BI a partir dos dados do Fabric.

- Compreender como o Fabric Data Factory integra engenharia de dados e
  análise em uma plataforma unificada.

## Exercício 1: Configurar o ambiente de engenharia de dados do Microsoft Fabric 

Antes de criar uma solução de engenharia de dados, é necessário preparar
o ambiente do Microsoft Fabric. Neste exercício, você fará login no
Microsoft Fabric, criará um workspace dedicado e provisionará um
Lakehouse que servirá como armazenamento centralizado para sua solução
de análise.

### Tarefa 1: Faça login na conta do Power BI

1.  Abra seu navegador, vá até a barra de endereços e digite ou cole o
    seguinte URL:+++https://app.fabric.microsoft.com/+++ e, em seguida,
    pressione a tecla **Enter**.

![](./media/image1.png)

2.  Na janela do **Microsoft Fabric**, insira suas credenciais e clique
    no botão **Submit**.

| Credential | Value |
|---|---|
| Username | +++@lab.CloudPortalCredential(User1).Username+++ |
| Password | +++@lab.CloudPortalCredential(User1).Password+++ |

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image2.png)

3.  Em seguida, na janela da **Microsoft**, insira a senha e clique no
    botão **Sign in**.

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image3.png)

4.  Na janela **Stay signed in?**, clique no botão **Yes**.

5.  Você será direcionado para a home page do Power BI.

> ![](./media/image4.png)

6.  Selecione o ícone padrão do Power BI no canto inferior esquerdo da
    tela e selecione **Fabric**.

> ![](./media/image5.png)
>
> ![](./media/image6.png)

### Tarefa 2: Criar um workspace no Fabric

Nesta tarefa, você criará um workspace do Fabric. O workspace contém
todos os itens necessários para este tutorial sobre o Lakehouse,
incluindo o Lakehouse, fluxos de dados, pipelines do Data Factory, os
notebooks, conjuntos de dados do Power BI e relatórios.

1.  Na home page do Fabric, selecione o bloco **+New workspace**.

![](./media/image7.png)

2.  No painel **Create a workspace** que aparece no lado direito, insira
    os seguintes detalhes e clique no botão **Apply**.

| Property | Value |
|---|---|
| Name | !!Fabric Dataengineering-DataFactoryXXXXXX!! |
| Advanced | Under License mode, select Fabric |
| Default storage format | Small dataset storage format |

![](./media/image8.png)

Observação: Para encontrar o ID da sua instância do laboratório,
selecione ‘Help’ e copie o ID da instância.

![A screenshot of a computer Description automatically
generated](./media/image9.png)

![](./media/image10.png)

![](./media/image11.png)

3.  Aguarde a conclusão da implementação. O processo leva de 2 a 3
    minutos para ser concluído.

![](./media/image12.png)

### Tarefa 3: Criar um lakehouse

1.  Crie um novo lakehouse clicando no botão **+New item** na barra de
    navegação.

![](./media/image13.png)

2.  Clique no bloco **"Lakehouse"**.

![](./media/image14.png)

3.  Na caixa de diálogo **New lakehouse**, insira **+++wwilakehouse+++**
    no campo **Name** e **desmarque** a opção de esquemas do lakehouse.
    Clique no botão **Create** e abra o novo lakehouse.

**Observação:** Certifique-se de remover o espaço antes de
**wwilakehouse**.

![](./media/image15.png)

4.  Você verá uma notificação informando **Successfully created SQL
    endpoint**.

![](./media/image16.png)

### Tarefa 4**: Ingerir dados de exemplo**

1.  Na página **wwilakehouse**, navegue até a seção **Get data in your
    lakehouse** e clique em **Upload files**, conforme mostrado na
    imagem abaixo.

![](./media/image17.png)

2.  Na guia Upload files, clique na pasta localizada em Files.

![](./media/image18.png)

3.  Navegue até **C:\LabFiles** na sua VM, selecione o arquivo
    **dimension_customer.csv** e clique no botão **Open**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image19.png)

4.  Em seguida, clique no botão **Upload** e feche.

![](./media/image20.png)

5.  **Feche** o painel Upload files.

![](./media/image21.png)

6.  Clique em **Files** e selecione Refresh. O arquivo será exibido.

![](./media/image22.png)

7.  Na página do **Lakehouse**, no painel Explorer, selecione Files. Em
    seguida, passe o mouse sobre o arquivo **dimension_customer.csv**.
    Clique nas reticências horizontais **(...)** ao lado de
    **dimension_customer.csv**. Navegue até **Load Table** e clique em
    **New table**.

![](./media/image23.png)

> ![](./media/image24.png)

8.  Na caixa de diálogo **Load file to new table**, clique no botão
    **Load**.

![](./media/image25.png)

9.  Agora, a tabela **dimension_customer** foi criada com sucesso.

![](./media/image26.png)

10. Selecione a tabela **dimension_customer** em Tables.

![](./media/image27.png)

11. Você também pode usar o SQL endpoint do lakehouse para consultar os
    dados usando instruções SQL. No menu suspenso **Analyze data**, no
    canto superior direito da tela, selecione **SQL analytics
    endpoint**.

![](./media/image28.png)

12. Na página **wwilakehouse**, no painel Explorer, selecione a tabela
    **dimension_customer** para visualizar uma prévia dos dados e, em
    seguida, selecione **New SQL query** para escrever suas instruções
    SQL.

![](./media/image29.png)

13. A consulta de exemplo a seguir agrega a contagem de linhas com base
    na coluna **BuyingGroup** da tabela **dimension_customer**. Os
    arquivos de consulta SQL são salvos automaticamente para referência
    futura, e você pode renomear ou excluir esses arquivos conforme sua
    necessidade. Cole o código conforme mostrado na imagem abaixo e
    clique no ícone de reprodução para **Run** o script:

```
SELECT BuyingGroup, Count(*) AS Total
FROM dimension_customer
GROUP BY BuyingGroup
```

![](./media/image30.png)

**Observação:** Se você encontrar um erro durante a execução do script,
verifique novamente a sintaxe do script para garantir que não haja
espaços desnecessários.

14. Anteriormente, todas as tabelas e exibições do lakehouse eram
    adicionadas automaticamente ao modelo semântico. Com as atualizações
    recentes, para novos lakehouses, é necessário adicionar manualmente
    as tabelas ao modelo semântico.

15. Na guia **Home** do lakehouse, selecione **New semantic model** e
    selecione as tabelas que deseja adicionar ao modelo semântico.

> ![](./media/image31.png)

16. Na caixa de diálogo **New semantic model**, insira
    **+++wwwsemanticmodel+++** e, em seguida, selecione a tabela
    **dimension_customer** na lista de tabelas e selecione **Confirm**
    para criar o novo modelo.

![](./media/image32.png)

### Tarefa 5: Criar um relatório

1.  No painel de navegação à esquerda, selecione **Fabric
    Dataengineering-DataFactory-XX**.

![](./media/image33.png)

2.  No seu workspace, localize o modelo semântico que você criou,
    selecione o menu **...** (reticências) e, em seguida, selecione
    **Auto-create report**.

![](./media/image34.png)

![](./media/image35.png)

4.  Agora que o relatório está pronto, clique em **View report now**
    para abri-lo e revisá-lo.

> ![](./media/image36.png)

![](./media/image37.png)

5.  Como a tabela é uma dimensão e não contém medidas, o Power BI cria
    uma medida para a contagem de linhas, agrega essa contagem em
    diferentes colunas e cria diferentes gráficos, conforme mostrado na
    imagem a seguir.

6.  Salve este relatório para uso futuro selecionando **Save** na faixa
    de opções superior.

![](./media/image38.png)

7.  Na caixa de diálogo **Save your report**, insira um nome para o
    relatório, como +++dimension_customer-report+++, e selecione
    **Save**.

![](./media/image39.png)

8.  Você verá uma notificação informando **Report saved**.

![](./media/image40.png)

## Exercício 2: Ingerir e Gerenciar Dados no Lakehouse do Fabric

Neste exercício, você fará a ingestão de tabelas dimensionais e de fatos
adicionais do Wide World Importers (WWI) no lakehouse.

### Tarefa 1: Ingerir dados

1.  No painel de navegação à esquerda, selecione **Fabric
    Dataengineering-DataFactory-XX**.

![](./media/image41.png)

2.  In the **Fabric Dataengineering-DataFactory-XX** workspace page,
    navigate and click on **+New item** button, then
    select **Pipeline**.

![](./media/image42.png)

3.  Na caixa de diálogo New pipeline, especifique o nome
    **+++IngestDataFromSourceToLakehouse+++** e selecione **Create**. Um
    novo pipeline do Data Factory será criado e aberto.

![](./media/image43.png)

![](./media/image44.png)

4.  Na guia **Home** do seu novo pipeline, selecione **Pipeline activity
    \> Copy data**.

![](./media/image45.png)

5.  Selecione a nova atividade **Copy data** na tela. As propriedades da
    atividade serão exibidas em um painel abaixo da tela, organizadas
    nas guias **General, Source, Destination, Mapping** e **Settings**.
    Talvez seja necessário expandir o painel para cima, arrastando a
    borda superior.

![](./media/image46.png)

6.  Na guia **General**, insira **+++Data Copy to Lakehouse+++** no
    campo **Name**. Mantenha os demais campos com os valores padrão.

![](./media/image47.png)

7.  Na guia **Source**, selecione o menu suspenso **Connection** e, em
    seguida, selecione **Browse all**.

![](./media/image48.png)

8.  Na página **Choose a data source to get started**, pesquise e
    selecione por **Azure blobs**.

![](./media/image49.png)

9.  Insira os seguintes detalhes na página **Connect data source**. Em
    seguida, selecione **Connect** para criar a conexão com a fonte de
    dados. Para este tutorial, todos os dados de exemplo estão
    disponíveis em um contêiner público do Armazenamento de Blobs do
    Azure. Você se conectará a esse contêiner para copiar os dados.

| Property | Value |
|---|---|
| Account name or URL | !!https://fabrictutorialdata.blob.core.windows.net/sampledata/!! |
| Connection | Create new connection |
| Connection name | !!wwisampledata!! |
| Authentication kind | Anonymous |

![](./media/image50.png)

10. Na guia **Source**, a conexão recém-criada será selecionada por
    padrão. Especifique as seguintes propriedades antes de prosseguir
    para as configurações de destino.

| Property | Value |
|---|---|
| Connection | wwisampledata |
| File path type | File path |
| File path | Container name (first text box): !!sampledata!!<br>Directory name (second text box): !!WideWorldImportersDW/parquet!! |
| Recursively | Checked |
| File format | Binary |

![](./media/image51.png)

11. Na guia **Destination**, especifique as seguintes propriedades:

| Property | Value |
|---|---|
| Connection | wwilakehouse (choose your lakehouse if you named it differently) |
| Root folder | Files |
| File path | Directory name (first text box): !!wwi-raw-data!! |
| File format | Binary |

![](./media/image52.png)

12. Clique em **Run** para executar a cópia dos dados.

![](./media/image53.png)

13. Clique no botão **Save and run** para que o pipeline seja salvo e
    executado.

> ![](./media/image54.png)

14. O processo de cópia dos dados leva aproximadamente 1 a 2 minutos
    para ser concluído.

![](./media/image55.png)

15. Na guia Output, selecione **Data Copy to Lakehouse** para consultar
    os detalhes da transferência de dados. Depois de verificar se o
    **Status** está como **Succeeded**, clique no botão **Close**.

![](./media/image56.png)

![](./media/image57.png)

16. Após a execução bem-sucedida do pipeline, acesse seu **lakehouse
    (wwilakehouse)** e abra o Explorer para visualizar os dados
    importados.

![](./media/image58.png)

17. Atualize a seção **Files** para visualizar os dados ingeridos. Uma
    nova pasta **wwi-raw-data** será exibida na seção de arquivos, e os
    dados das tabelas do Azure Blob serão copiados para essa pasta.
    ![](./media/image59.png)

## Exercício 3: Preparar e transformar dados no lakehouse

### Tarefa 1: Transformar dados e carregar na tabela Delta silver

1.  No painel de navegação à esquerda, selecione **Fabric
    Dataengineering-DataFactory-XX**.

![](./media/image60.png)

2.  Na página do **Fabric**, navegue até **Import** na barra de comandos
    e clique nele. Em seguida, selecione **New notebook \> From this
    computer**.

![](./media/image61.png)

3.  Selecione **Upload** no painel **Import status** que será aberto no
    lado direito da tela.

> ![](./media/image62.png)

4.  Navegue até **C:\LabFiles** na sua VM, selecione o notebook
    **Prepare and transform data – PySpark** e clique no botão **Open**.

> ![](./media/image63.png)
>
> ![](./media/image64.png)

5.  Selecione o lakehouse **wwilakehouse** para abri-lo, de modo que o
    próximo notebook que você abrir fique vinculado a ele.

![](./media/image65.png)

6.  Na barra de ferramentas, selecione o menu suspenso **Analyze data**,
    aponte para **Notebook** e, em seguida, selecione **Existing
    notebook**.

> ![](./media/image66.png)

7.  Selecione o notebook importado **Prepare and transform data –
    PySpark** e, em seguida, clique em **Open.**

> ![](./media/image67.png)
>
> ![](./media/image68.png)

### Tarefa 2: Criar tabelas Delta

> Nesta tarefa, você executará as células do notebook para criar tabelas
> Delta a partir dos dados brutos.
>
> As tabelas seguem um esquema em estrela, que é um padrão comum para
> organizar dados analíticos:

- **Uma tabela de fatos** contém os eventos mensuráveis do negócio —
  neste caso, transações individuais de vendas com quantidades, preços e
  lucro.

- **Tabelas de dimensão** (dimension_city, dimension_customer,
  dimension_date, dimension_employee, dimension_stock_item) contêm os
  atributos descritivos que fornecem contexto aos fatos, como onde uma
  venda aconteceu, quem a realizou e quando.

1.  **Célula 1 – Configuração da sessão do Spark.** Esta célula habilita
    dois recursos do Fabric que otimizam a forma como os dados são
    gravados e lidos nas células
    subsequentes. [V-order](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order) otimiza
    o layout dos arquivos Parquet para proporcionar leituras mais
    rápidas e melhor compactação. [Optimize
    write](https://learn.microsoft.com/en-us/fabric/data-engineering/tune-file-size#optimize-write) reduz
    o número de arquivos gravados e aumenta o tamanho de cada arquivo.

```
spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize", "1073741824")
```

2.  **Execute** esta célula e aguarde a conclusão antes de passar para a
    próxima etapa.

> ![](./media/image69.png)
>
> ![](./media/image70.png)

3.  **Cell 2 - Fact - Sale.** Esta célula lê os dados brutos em formato
    Parquet de Files/wwi-raw-data/full/fact_sale_1y_full, adiciona
    colunas de data (**Year**, **Quarter**, e **Month**), e grava
    fact_sale como uma tabela Delta particionada por
    **Year** e **Quarter**.

4.  **Execute** esta célula e aguarde a conclusão antes de passar para a
    próxima etapa.

> ![](./media/image71.png)

5.  **Cell 3** - Dimensions. Esta célula lê os cinco conjuntos de dados
    de dimensão em formato Parquet e os grava como tabelas Delta
    (dimension_city, dimension_customer, dimension_date,
    dimension_employee e dimension_stock_item) em Tables/dbo/....

6.  **Execute** esta célula e aguarde a conclusão antes de passar para a
    próxima etapa.

> ![](./media/image72.png)

7.  Para validar as tabelas criadas, clique com o botão direito do mouse
    no lakehouse **wwilakehouse** no Explorer e, em seguida, selecione
    **Refresh**. As tabelas serão exibidas.

> ![](./media/image73.png)
>
> ![](./media/image74.png)

### Tarefa 3: Transformar Dados de Negócios para Agregação

Nesta tarefa, você continuará no mesmo notebook e executará as próximas
células para criar tabelas agregadas a partir das tabelas Delta criadas
na seção anterior.

1.  Certifique-se de que o notebook ainda esteja vinculado ao
    **wwilakehouse**.

2.  **Cell 4 - Load source tables para transformação (somente
    PySpark).** Se estiver usando o notebook PySpark, execute esta
    célula para carregar as tabelas Delta em DataFrames para as etapas
    de agregação a seguir.

3.  Execute esta célula e aguarde a conclusão antes de passar para a
    próxima etapa.

![](./media/image75.png)

4.  **Cell 5 - Criar aggregate_sale_by_date_city.** Esta célula combina
    os dados de vendas, data e cidade e, em seguida, cria a tabela
    agregada no nível da cidade.

5.  Execute esta célula e aguarde a conclusão antes de passar para a
    próxima etapa.

> ![](./media/image76.png)

6.  **Cell 6 - Criar aggregate_sale_by_date_employee.** Esta célula
    combina os dados de vendas, data e cidade e, em seguida, cria a
    tabela agregada no nível do funcionário.

7.  Execute esta célula e aguarde a conclusão antes de passar para a
    próxima etapa.

> ![](./media/image77.png)

8.  Para validar as tabelas criadas, clique com o botão direito do mouse
    no lakehouse **wwilakehouse** no Explorer e, em seguida, selecione
    **Refresh**. As tabelas agregadas serão exibidas.

> ![](./media/image78.png)
>
> ![](./media/image79.png)

## Exercício 4: Criar relatórios no Microsoft Fabric

Nesta seção do tutorial, você criará um modelo de dados do Power BI e
criará um relatório do zero.

### Tarefa 1: Explorar dados na camada silver usando o SQL endpoint

O Power BI é integrado nativamente a toda a experiência do Fabric. Essa
integração nativa oferece um modo exclusivo, chamado DirectLake, para
acessar os dados do lakehouse e proporcionar uma experiência de consulta
e geração de relatórios de alto desempenho. O modo DirectLake é um novo
recurso de mecanismo que permite analisar conjuntos de dados muito
grandes no Power BI. A tecnologia baseia-se na ideia de carregar
arquivos em formato Parquet diretamente de um data lake, sem precisar
consultar um data warehouse ou o SQL endpoint do lakehouse e sem
precisar importar ou duplicar os dados em um conjunto de dados do Power
BI. O DirectLake oferece uma maneira rápida de carregar os dados
diretamente do data lake para o mecanismo do Power BI, deixando-os
prontos para análise. No modo tradicional DirectQuery, o mecanismo do
Power BI consulta diretamente os dados da fonte para executar cada
consulta, e o desempenho da consulta depende da velocidade de
recuperação dos dados. O DirectQuery elimina a necessidade de copiar os
dados, garantindo que quaisquer alterações na fonte sejam imediatamente
refletidas nos resultados da consulta durante a importação. Por outro
lado, no modo Import, o desempenho é melhor porque os dados estão
prontamente disponíveis na memória, sem consultar os dados da fonte a
cada execução de consulta. No entanto, o mecanismo do Power BI precisa
primeiro copiar os dados para a memória durante a atualização dos dados.
Somente as alterações na fonte de dados subjacente são consideradas
durante a próxima atualização dos dados (agendada ou sob demanda).

O modo DirectLake elimina agora esse requisito de importação ao carregar
os arquivos de dados diretamente na memória. Como não há um processo
explícito de importação, é possível identificar quaisquer alterações na
fonte à medida que ocorrem, combinando assim as vantagens do DirectQuery
e do modo Import, ao mesmo tempo em que evita suas desvantagens.
Portanto, o modo DirectLake é a escolha ideal para analisar conjuntos de
dados muito grandes e conjuntos de dados com atualizações frequentes na
fonte.

1.  No menu à esquerda, selecione **Fabric
    Dataengineering-DataFactory-@lab.LabInstance.Id** e, em seguida,
    selecione seu modelo semântico chamado **wwisemanticmodel**.

2.  Abra o modelo semântico, selecione o menu suspenso de modo no canto
    superior direito, alterne de Viewing para Editing e, em seguida,
    selecione Make any changes.

![](./media/image80.png)

3.  Na faixa de opções do menu, selecione **Edit tables** para exibir a
    caixa de diálogo de sincronização de tabelas.

![](./media/image81.png)

4.  Na caixa de diálogo **Edit semantic model**, clique em **Select
    all** e, em seguida, clique em **Confirm**, na parte inferior da
    caixa de diálogo, para sincronizar o modelo semântico.

![](./media/image82.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image83.png)

5.  Na tabela **fact_sale**, arraste o campo **CityKey** e solte-o sobre
    o campo **CityKey** da tabela **dimension_city** para criar um
    relacionamento.  
    A caixa de diálogo **Create Relationship** será exibida.

Observação: Reorganize as tabelas clicando na tabela, arrastando-a e
soltando-a de modo que as tabelas dimension_city e fact_sale fiquem
próximas umas das outras. O mesmo se aplica a quaisquer duas tabelas
entre as quais você esteja tentando criar um relacionamento. Isso
facilita o processo de arrastar e soltar as colunas entre as
tabelas. ![](./media/image84.png)

6\. Na caixa de diálogo **Create Relationship**:

- **Table 1** é preenchida com **fact_sale** e a coluna **CityKey.**

- **Table 2** é preenchida com **dimension_city** e a coluna
  **CityKey.**

- **Cardinality: Many to one (\*:1)**

- **Cross filter direction: Single**

- Deixe selecionada a caixa ao lado de **Make this relationship
  active.**

- Selecione a caixa ao lado de **Assume referential integrity.**

- Selecione **Save.**

![](./media/image85.png)

7\. Em seguida, adicione estes relacionamentos usando as mesmas
configurações de **Create Relationship** mostradas acima, mas com as
seguintes tabelas e colunas:

- **StockItemKey(fact_sale)** - **StockItemKey(dimension_stock_item)**

1.  

![](./media/image86.png)

![](./media/image87.png)

- **Salespersonkey(fact_sale)** - **EmployeeKey(dimension_employee)**

![](./media/image88.png)

8.Certifique-se de criar os relacionamentos entre os dois conjuntos
abaixo, seguindo as mesmas etapas descritas anteriormente.

1.  **CustomerKey(fact_sale)** - **CustomerKey(dimension_customer)**

2.  **InvoiceDateKey(fact_sale)** - **Date(dimension_date)**

&nbsp;

9.  Depois de adicionar esses relacionamentos, seu modelo de dados
    deverá estar conforme mostrado na imagem abaixo e estará pronto para
    geração de relatórios.

![](./media/image89.png)

### Tarefa 2: Criar relatório

1.  Na faixa de opções superior, selecione **File** e, em seguida,
    selecione **Create new report** para começar a criar
    relatórios/painéis no Power BI.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image90.png)

2.  Na tela de relatório do Power BI, você pode criar relatórios que
    atendam aos seus requisitos de negócio arrastando as colunas
    necessárias do painel **Data** para a tela e usando uma ou mais das
    visualizações disponíveis.

![](./media/image91.png)

**Adicionar um título:**

3.  Na faixa de opções, selecione **Text box**. Digite **WW Importers
    Profit Reporting**. Selecione o texto e aumente o tamanho para
    **20**.

![](./media/image92.png)

4.  Redimensione a área de texto e posicione-a no **canto superior
    esquerdo** da página do relatório. Em seguida, clique fora da área
    de texto.

![](./media/image93.png)

**Adicionar um cartão:**

- No painel **Data**, expanda **fact_sales** e marque a caixa ao lado de
  **Profit**. Essa seleção cria um gráfico de colunas e adiciona o campo
  ao eixo Y.

![](./media/image94.png)

5.  Com o gráfico de barras selecionado, selecione o visual **Card** no
    painel de visualizações.

![](./media/image95.png)

6.  Essa seleção transforma o elemento visual em um cartão. Coloque o
    cartão abaixo do título.

![](./media/image96.png)

7.  Clique em qualquer lugar da tela em branco (ou pressione a tecla
    Esc) para que o cartão que acabamos de inserir não esteja mais
    selecionado.

**Adicionar um gráfico de barras:**

8.  No painel **Data**, expanda **fact_sales** e marque a caixa de
    seleção ao lado de **Profit**. Essa seleção cria um gráfico de
    colunas e adiciona o campo ao eixo Y. 

![](./media/image97.png)

9.  No painel **Data**, expanda **dimension_city** e marque a caixa de
    seleção ao lado de **SalesTerritory**. Essa seleção adiciona o campo
    ao eixo Y.

![](./media/image98.png)

10. Com o gráfico de barras selecionado, selecione o visual **Clustered
    bar chart** no painel de visualizações. Essa seleção converte o
    gráfico de colunas em um gráfico de barras.

![](./media/image99.png)

11. Redimensione o gráfico de barras para preencher a área abaixo do
    título e do cartão.

![](./media/image100.png)

12. Clique em qualquer área vazia da tela (ou pressione a tecla Esc)
    para que o gráfico de barras deixe de estar selecionado.

**Crie um gráfico de área empilhada:**

13. No painel **Visualizações**, selecione o visual **Stacked area
    chart**.

![](./media/image101.png)

14. Reposicione e redimensione o gráfico de área empilhada à direita dos
    gráficos de cartão e de barras criados nas etapas anteriores.

![](./media/image102.png)

15. No painel **Data**, expanda **fact_sales** e marque a caixa ao lado
    de **Profit**. Expanda **dimension_date** e marque a caixa ao lado
    de **FiscalMonthNumber**. Essa seleção cria um gráfico de linhas
    preenchido que mostra o lucro por mês fiscal.

![](./media/image103.png)

16. No painel **Data**, expanda **dimension_stock_item** e arraste
    **BuyingPackage** para o campo Legend. Essa seleção adiciona uma
    linha para cada Buying Package.

![](./media/image104.png) ![](./media/image105.png)

17. Clique em qualquer lugar da tela em branco (ou pressione a tecla
    Esc) para que o gráfico de área empilhada não esteja mais
    selecionado.

**Crie um gráfico de colunas:**

18. No painel **Visualizations**, selecione o visual **Stacked column
    chart**.

![](./media/image106.png)

19. No painel **Data**, expanda **fact_sales** e marque a caixa ao lado
    de **Profit**. Essa seleção adiciona o campo ao eixo Y.

20.  No painel **Data**, expanda **dimension_employee** e marque a caixa
    ao lado de **Employee**. Essa seleção adiciona o campo ao eixo X.

![](./media/image107.png)

21. Clique em qualquer área vazia da tela (ou pressione a tecla Esc)
    para que o gráfico deixe de estar selecionado.

22. Na faixa de opções, selecione **File \> Save**.

![](./media/image108.png)

23. Insira o nome do relatório como **Profit Reporting**. Selecione
    **Save**.

![](./media/image109.png)

24. Você receberá uma notificação informando que o relatório foi salvo. 

![](./media/image110.png)

# Exercício 7: Limpar recursos

Você pode excluir relatórios, pipelines, armazéns e outros itens
individualmente ou remover todo o workspace. Siga as etapas a seguir
para excluir o workspace que você criou para este tutorial.

1.  Selecione seu workspace,
    **Dataengineering-DataFactory-@lab.LabInstance.Id** **do Fabric**,
    no menu de navegação à esquerda. Isso abre a visualização dos itens
    do workspace.

&nbsp;

2.  Selecione a opção **...** abaixo do nome do workspace e selecione
    **Workspace settings**.

![](./media/image111.png)

3.  Selecione **General** e **Remove this workspace.**

![](./media/image112.png)

4.  Clique em **Delete** no aviso que aparecer.

![](./media/image113.png)

5.  Aguarde uma notificação informando que o Workspace foi excluído
    antes de prosseguir para o próximo laboratório.

![](./media/image114.png)

**Resumo**

Neste laboratório, você implementou um fluxo completo de engenharia de
dados no Microsoft Fabric, criando um workspace e um Lakehouse,
ingerindo dados de origem, carregando-os em tabelas Delta, validando os
dados com consultas SQL, criando um modelo semântico e gerando um
relatório do Power BI. Essas atividades demonstram como o Microsoft
Fabric simplifica a análise de dados moderna ao combinar integração de
dados, armazenamento, transformação, modelagem semântica e geração de
relatórios em uma plataforma unificada. As habilidades adquiridas neste
laboratório fornecem a base para desenvolver soluções de engenharia de
dados escaláveis usando o Microsoft Fabric.
