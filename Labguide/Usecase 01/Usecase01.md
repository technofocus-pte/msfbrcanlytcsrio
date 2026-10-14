# Laboratório 1: Implementar uma solução de engenharia de dados para mover e transformar dados com o Fabric Data Factory

**Cenário**

**Wide World Importers (WWI)** é uma organização global de varejo que opera centenas de lojas em várias regiões. As informações dos clientes são coletadas de vários sistemas operacionais, incluindo aplicativos de ponto de venda (POS), plataformas de CRM e canais de comércio eletrônico. Os dados são armazenados como arquivos CSV e recebidos diariamente de diferentes unidades de negócios.

A equipe de análise da empresa atualmente dedica um tempo significativo à importação manual de arquivos, à validação da qualidade dos dados e à preparação de conjuntos de dados para relatórios. Esses processos manuais atrasam a geração de insights sobre os clientes e dificultam o acesso dos usuários de negócios a informações consistentes e confiáveis.

Para modernizar sua plataforma de análise, a Wide World Importers adotou o **Microsoft Fabric** como plataforma unificada de dados. A equipe de engenharia de dados foi encarregada de implementar uma solução escalável usando o **Microsoft Fabric Data Factory** e o **Lakehouse** para centralizar os dados dos clientes, permitir um gerenciamento de dados eficiente e simplificar os relatórios.

Como Engenheiro de Dados, sua responsabilidade é criar um espaço de trabalho do Fabric, provisionar um Lakehouse, ingerir os dados dos clientes no OneLake, converter os arquivos de origem em tabelas Delta gerenciadas, validar os dados importados usando o SQL analytics endpoint, criar um modelo semântico Direct Lake e gerar um relatório do Power BI que permita aos stakeholders de negócios analisar as informações dos clientes com latência mínima.

Ao implementar essa solução, a Wide World Importers pode eliminar a preparação manual de dados, fornecer uma fonte única de verdade para a análise de clientes e permitir decisões de negócios mais rápidas e baseadas em dados usando o Microsoft Fabric.

**Introdução**

Neste laboratório, você criará uma solução completa de engenharia de dados usando o **Microsoft Fabric Data Factory** e o **Fabric Lakehouse**. Começando com um novo espaço de trabalho do Fabric, você irá ingerir dados em um Lakehouse, converter arquivos em tabelas Delta gerenciadas, consultar os dados usando o SQL analytics endpoint, transformar dados com pipelines, notebooks e Dataflow Gen2, automatizar e agendar o pipeline com notificações por e-mail, criar modelos semânticos e gerar relatórios interativos do Power BI.

Ao longo do laboratório, você verá como o Microsoft Fabric unifica integração, armazenamento, transformação, análise e relatórios de dados em uma única plataforma de Software como Serviço (SaaS).

**Itens do Fabric criados neste laboratório**

| **Item** | **Nome** | **Função no laboratório** |
|----|----|----|
| Espaço de trabalho | Fabric Dataengineering-DataFactory-\<ID da instância do laboratório\> | Contém todos os itens do laboratório |
| Lakehouse | wwilakehouse | Armazena arquivos brutos e tabelas Delta no OneLake |
| Modelo semântico | wwisemanticmodel | Modelo Direct Lake usado para os relatórios |
| Pipeline | IngestDataFromSourceToLakehouse | Copia os dados de exemplo da WWI, executa o fluxo de dados e envia um e-mail |
| Notebook | Prepare and transform data – PySpark | Cria as tabelas Delta de fatos, dimensões e agregações |
| Dataflow Gen2 | wwi_fact_sale_transform | Cria a tabela Gold_Sales_By_City |
| Relatórios | dimension_customer-report, Profit Reporting | Relatórios do Power BI criados com os dados do lakehouse |

**Objetivos**:

- Criar e configurar um espaço de trabalho do Microsoft Fabric.

- Criar e configurar um Fabric Lakehouse.

- Ingerir dados de origem no OneLake.

- Carregar arquivos em tabelas Delta gerenciadas.

- Consultar os dados do Lakehouse usando o SQL analytics endpoint.

- Transformar dados com notebooks e Dataflow Gen2.

- Automatizar, agendar e monitorar um pipeline do Data Factory com notificações por e-mail.

- Criar um modelo semântico Direct Lake.

- Gerar e explorar relatórios do Power BI a partir dos dados do Fabric.

## Exercício 1: Configurar o ambiente de engenharia de dados do Microsoft Fabric

Antes de criar uma solução de engenharia de dados, você precisa preparar o ambiente do Microsoft Fabric. Neste exercício, você fará login no Microsoft Fabric, criará um espaço de trabalho dedicado e provisionará um Lakehouse que servirá como armazenamento centralizado para sua solução de análise.

### Tarefa 1: Fazer login na conta do Power BI

1.  Abra o navegador, vá até a barra de endereços e digite ou cole a seguinte URL: +++https://app.fabric.microsoft.com/+++. Em seguida, pressione **Enter**.

![](./media/image1.png)

2.  Na janela do **Microsoft Fabric**, insira suas credenciais e clique no botão **Submit**.

| **Username** | **+++@lab.CloudPortalCredential(User1).Username+++** |
|----|----|
| **Password** | **+++@lab.CloudPortalCredential(User1).Password+++** |

![](./media/image2.png)

3.  Na janela da **Microsoft**, insira a senha e clique no botão **Sign in**.

![](./media/image3.png)

4.  Na janela **Stay signed in?**, clique no botão **Yes**.

5.  Você será direcionado para a página inicial do Power BI.

![](./media/image4.png)

6.  Selecione o ícone padrão do Power BI no canto inferior esquerdo da tela e selecione **Fabric**.

![](./media/image5.png)

![](./media/image6.png)

### Tarefa 2: Criar um espaço de trabalho do Fabric

Nesta tarefa, você cria um espaço de trabalho do Fabric. O espaço de trabalho contém todos os itens necessários para este laboratório, incluindo o lakehouse, fluxos de dados, pipelines do Data Factory, notebooks, modelos semânticos e relatórios do Power BI.

1.  Na página inicial do Fabric, selecione o bloco **+ New workspace**.

![](./media/image7.png)

2.  No painel **Create a workspace**, que aparece no lado direito, insira os seguintes detalhes e clique no botão **Apply**.

| **Propriedade** | **Valor** |
|----|----|
| **Name** | +++Fabric Dataengineering-DataFactory-@lab.LabInstance.Id+++ |
| **Advanced** | Em **License mode**, selecione **Fabric** |
| **Default storage format** | **Small dataset storage format** |

![](./media/image8.png)

**Nota:** Para encontrar o ID da instância do laboratório, selecione **Help** e copie o ID da instância.

![](./media/image9.png)

![](./media/image10.png)

![](./media/image11.png)

3.  Aguarde até que a implantação seja concluída. O processo leva de 2 a 3 minutos.

![](./media/image12.png)

### Tarefa 3: Criar um lakehouse

1.  Crie um novo lakehouse clicando no botão **+ New item** na barra de navegação.

![](./media/image13.png)

2.  Clique no bloco **Lakehouse**.

![](./media/image14.png)

3.  Na caixa de diálogo **New lakehouse**, insira +++wwilakehouse+++ no campo **Name** e **desmarque** **Lakehouse schemas**. Clique no botão **Create** e abra o novo lakehouse.

**Nota**: Certifique-se de que não há espaço antes de **wwilakehouse**.

![](./media/image15.png)

4.  Você verá uma notificação informando **Successfully created SQL endpoint**.

![](./media/image16.png)

### Tarefa 4: Ingerir dados de exemplo

1.  Na página **wwilakehouse**, vá até a seção **Get data in your lakehouse** e clique em **Upload files**.

![](./media/image17.png)

2.  Na aba **Upload files**, clique no ícone de pasta em **Files**.

![](./media/image18.png)

3.  Navegue até **C:\LabFiles** na sua VM, selecione o arquivo **dimension_customer.csv** e clique no botão **Open**.

![](./media/image19.png)

4.  Clique no botão **Upload**.

![](./media/image20.png)

5.  **Feche** o painel **Upload files**.

![](./media/image21.png)

6.  Selecione **Files** e clique em **Refresh**. O arquivo aparece.

![](./media/image22.png)

7.  No painel **Explorer**, selecione **Files**. Passe o mouse sobre o arquivo **dimension_customer.csv**, clique nas reticências horizontais **(…)** ao lado dele, clique em **Load to Tables** e selecione **New table**.

![](./media/image23.png)

![](./media/image24.png)

8.  Na caixa de diálogo **Load file to new table**, clique no botão **Load**.

![](./media/image25.png)

9.  A tabela **dimension_customer** foi criada com sucesso.

![](./media/image26.png)

10. Selecione a tabela **dimension_customer** em **Tables**.

![](./media/image27.png)

11. Você também pode usar o SQL endpoint do lakehouse para consultar os dados com instruções SQL. Selecione **SQL analytics endpoint** no menu suspenso **Analyze data with**, no canto superior direito da tela.

![](./media/image28.png)

12. Na página **wwilakehouse**, em **Explorer**, selecione a tabela **dimension_customer** para visualizar os dados e selecione **New SQL query** para escrever suas instruções SQL.

![](./media/image29.png)

13. A consulta de exemplo a seguir agrega a contagem de linhas com base na coluna **BuyingGroup** da tabela **dimension_customer**. Os arquivos de consulta SQL são salvos automaticamente para referência futura, e você pode renomeá-los ou excluí-los conforme necessário. Cole o código e clique no ícone de reprodução para **executar** o script.

```sql
SELECT BuyingGroup, Count(*) AS Total
FROM dimension_customer
GROUP BY BuyingGroup
```

![](./media/image30.png)

**Nota**: Se ocorrer um erro durante a execução do script, verifique se a sintaxe do script não tem espaços desnecessários.

14. Anteriormente, todas as tabelas e exibições do lakehouse eram adicionadas automaticamente ao modelo semântico. Com as atualizações recentes, para novos lakehouses, você precisa adicionar as tabelas manualmente ao modelo semântico.

15. Na aba **Home** do lakehouse, selecione **New semantic model**.

![](./media/image31.png)

16. Na caixa de diálogo **New semantic model**, insira +++wwisemanticmodel+++, selecione a tabela **dimension_customer** na lista de tabelas e clique em **Confirm** para criar o novo modelo.

![](./media/image32.png)

### Tarefa 5: Criar um relatório

1.  No painel de navegação à esquerda, selecione **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**.

![](./media/image33.png)

2.  No seu espaço de trabalho, localize o modelo semântico **wwisemanticmodel**, selecione o menu **...** (reticências) e selecione **Auto-create report**.

![](./media/image34.png)

![](./media/image35.png)

3.  Quando o relatório estiver pronto, clique em **View report now** para abri-lo e revisá-lo.

![](./media/image36.png)

![](./media/image37.png)

4.  Como a tabela é uma dimensão e não contém medidas, o Power BI cria uma medida para a contagem de linhas, agrega-a em diferentes colunas e cria diferentes gráficos, como mostrado na imagem acima.

5.  Salve este relatório selecionando **Save** na faixa de opções superior.

![](./media/image38.png)

6.  Na caixa de diálogo **Save your report**, insira +++dimension_customer-report+++ como nome e clique em **Save**.

![](./media/image39.png)

7.  Você verá uma notificação informando **Report saved**.

![](./media/image40.png)

## Exercício 2: Ingerir e gerenciar dados no Fabric Lakehouse

Neste exercício, você usa um pipeline do Data Factory para ingerir tabelas adicionais de dimensões e fatos dos dados de exemplo da Wide World Importers (WWI) no lakehouse.

### Tarefa 1: Ingerir dados

1.  No painel de navegação à esquerda, selecione **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**.

![](./media/image41.png)

2.  Na página do espaço de trabalho, clique no botão **+ New item** e selecione **Pipeline**.

![](./media/image42.png)

3.  Na caixa de diálogo **New pipeline**, insira +++IngestDataFromSourceToLakehouse+++ como nome e clique em **Create**. Um novo pipeline do Data Factory é criado e aberto.

![](./media/image43.png)

![](./media/image44.png)

4.  Na aba **Home** do novo pipeline, selecione **Pipeline activity** \> **Copy data**.

![](./media/image45.png)

5.  Selecione a nova atividade **Copy data** na tela. As propriedades da atividade aparecem em um painel abaixo da tela, organizadas nas abas **General**, **Source**, **Destination**, **Mapping** e **Settings**. Talvez seja necessário expandir o painel para cima arrastando a borda superior.

![](./media/image46.png)

6.  Na aba **General**, insira +++Data Copy to Lakehouse+++ no campo **Name**. Mantenha os valores padrão nos outros campos.

![](./media/image47.png)

7.  Na aba **Source**, selecione o menu suspenso **Connection** e selecione **Browse all**.

![](./media/image48.png)

8.  Na página **Choose a data source to get started**, pesquise e selecione **Azure Blobs**.

![](./media/image49.png)

9.  Insira os seguintes detalhes na página **Connect data source** e clique em **Connect**. Todos os dados de exemplo estão disponíveis em um contêiner público do Armazenamento de Blobs do Azure.

| **Propriedade** | **Valor** |
|----|----|
| **Account name or URL** | +++https://fabrictutorialdata.blob.core.windows.net/sampledata/+++ |
| **Connection** | **Create new connection** |
| **Connection name** | +++wwisampledata+++ |
| **Authentication kind** | **Anonymous** |

![](./media/image50.png)

10. Na aba **Source**, a conexão recém-criada é selecionada por padrão. Especifique as seguintes propriedades antes de passar para as configurações de destino.

| **Propriedade** | **Valor** |
|----|----|
| **Connection** | **wwisampledata** |
| **File path type** | **File path** |
| **File path** | Nome do contêiner (primeira caixa de texto): +++sampledata+++ <br> Nome do diretório (segunda caixa de texto): +++WideWorldImportersDW/parquet+++ |
| **Recursively** | **Marcado** |
| **File format** | **Binary** |

![](./media/image51.png)

11. Na aba **Destination**, especifique as seguintes propriedades.

| **Propriedade** | **Valor** |
|----|----|
| **Connection** | **wwilakehouse** (escolha seu lakehouse se tiver usado outro nome) |
| **Root folder** | **Files** |
| **File path** | Nome do diretório (primeira caixa de texto): +++wwi-raw-data+++ |
| **File format** | **Binary** |

![](./media/image52.png)

12. Clique em **Run** para executar a atividade de cópia de dados.

![](./media/image53.png)

13. Clique no botão **Save and run** para salvar e executar o pipeline.

![](./media/image54.png)

14. O processo de cópia dos dados leva aproximadamente de 1 a 2 minutos.

![](./media/image55.png)

15. Na aba **Output**, selecione **Data Copy to Lakehouse** para ver os detalhes da transferência de dados. Quando o **Status** for **Succeeded**, clique no botão **Close**.

![](./media/image56.png)

![](./media/image57.png)

16. Após a execução bem-sucedida do pipeline, vá até o seu lakehouse (**wwilakehouse**) e abra o Explorer para ver os dados importados.

![](./media/image58.png)

17. Atualize a seção **Files** para ver os dados ingeridos. Uma nova pasta **wwi-raw-data** aparece na seção Files, e os dados do Armazenamento de Blobs do Azure são copiados para ela.

![](./media/image59.png)

## Exercício 3: Preparar e transformar dados no lakehouse

Neste exercício, você importa um notebook PySpark e o usa para criar tabelas Delta de fatos, dimensões e agregações a partir dos dados brutos.

### Tarefa 1: Importar o notebook e vinculá-lo ao lakehouse

1.  No painel de navegação à esquerda, selecione **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**.

![](./media/image60.png)

2.  Na página do espaço de trabalho, clique no menu suspenso **Import** na barra de comandos e selecione **Notebook \> From this computer**.

![](./media/image61.png)

3.  Selecione **Upload** no painel **Import status**, que abre no lado direito da tela.

![](./media/image62.png)

4.  Navegue até **C:\LabFiles** na sua VM, selecione o notebook **Prepare and transform data – PySpark** e clique no botão **Open**.

![](./media/image63.png)

![](./media/image64.png)

5.  Selecione o lakehouse **wwilakehouse** para abri-lo, para que o notebook que você abrir em seguida seja vinculado a ele.

![](./media/image65.png)

6.  Na barra de ferramentas, selecione o menu suspenso **Analyze data with**, aponte para **Notebook** e selecione **Existing notebook**.

![](./media/image66.png)

7.  Selecione o notebook importado, **Prepare and transform data – PySpark**, e clique em **Open**.

![](./media/image67.png)

![](./media/image68.png)

### Tarefa 2: Criar tabelas Delta

Nesta tarefa, você executa as células do notebook para criar tabelas Delta a partir dos dados brutos. As tabelas seguem um esquema em estrela, que é um padrão comum para organizar dados analíticos:

- Uma **tabela de fatos** (fact_sale) contém os eventos mensuráveis do negócio — neste caso, transações de vendas individuais com quantidades, preços e lucro.

- As **tabelas de dimensões** (dimension_city, dimension_customer, dimension_date, dimension_employee, dimension_stock_item) contêm os atributos descritivos que dão contexto aos fatos, como onde a venda ocorreu, quem a fez e quando.

1.  **Cell 1 - Spark session configuration.** Esta célula habilita dois recursos do Fabric que otimizam a forma como os dados são gravados e lidos nas células seguintes. O [V-order](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order) otimiza o layout dos arquivos parquet para leituras mais rápidas e melhor compressão. O [Optimize write](https://learn.microsoft.com/en-us/fabric/data-engineering/tune-file-size#optimize-write) reduz o número de arquivos gravados e aumenta o tamanho de cada arquivo.

```python
spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize", "1073741824")
```

2.  **Execute** esta célula e aguarde a conclusão antes de passar para a próxima etapa.

![](./media/image69.png)

![](./media/image70.png)

3.  **Cell 2 - Fact - Sale.** Esta célula lê os dados parquet brutos de **Files/wwi-raw-data/full/fact_sale_1y_full**, adiciona colunas de partes da data (**Year**, **Quarter** e **Month**) e grava **fact_sale** como uma tabela Delta particionada por **Year** e **Quarter**.

4.  **Execute** esta célula e aguarde a conclusão antes de passar para a próxima etapa.

![](./media/image71.png)

5.  **Cell 3 - Dimensions.** Esta célula lê os cinco conjuntos de dados parquet de dimensões e os grava como tabelas Delta (**dimension_city**, **dimension_customer**, **dimension_date**, **dimension_employee** e **dimension_stock_item**).

6.  **Execute** esta célula e aguarde a conclusão antes de passar para a próxima etapa.

![](./media/image72.png)

7.  Para validar as tabelas criadas, clique com o botão direito do mouse no lakehouse **wwilakehouse** no Explorer e selecione **Refresh**. As tabelas aparecem.

![](./media/image73.png)

![](./media/image74.png)

### Tarefa 3: Transformar dados de negócios para agregação

Nesta tarefa, você continua no mesmo notebook e executa as próximas células para criar tabelas agregadas a partir das tabelas Delta criadas na tarefa anterior.

1.  Certifique-se de que o notebook ainda está vinculado ao **wwilakehouse**.

2.  **Cell 4 - Load source tables for transformation.** Execute esta célula para carregar as tabelas Delta em DataFrames para as etapas de agregação seguintes. Aguarde a conclusão antes de passar para a próxima etapa.

![](./media/image75.png)

3.  **Cell 5 - Create aggregate_sale_by_date_city.** Esta célula combina os dados de vendas, data e cidade e cria a tabela agregada por cidade. **Execute** esta célula e aguarde a conclusão.

![](./media/image76.png)

4.  **Cell 6 - Create aggregate_sale_by_date_employee.** Esta célula combina os dados de vendas, data e funcionários e cria a tabela agregada por funcionário. **Execute** esta célula e aguarde a conclusão.

![](./media/image77.png)

5.  Para validar as tabelas criadas, clique com o botão direito do mouse no lakehouse **wwilakehouse** no Explorer e selecione **Refresh**. As tabelas agregadas aparecem.

![](./media/image78.png)

![](./media/image79.png)

## Exercício 4: Transformar dados com um fluxo de dados no Data Factory

Neste exercício, você usa o Dataflow Gen2 para combinar as tabelas **fact_sale** e **dimension_city**, adicionar uma coluna calculada de margem de lucro e carregar o resultado em uma tabela gold no lakehouse.

### Tarefa 1: Obter dados da tabela do lakehouse

1.  No painel de navegação à esquerda, clique no nome do espaço de trabalho **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id** para voltar à exibição do espaço de trabalho.

![](./media/image80.png)

2.  Clique em **+ New item** na barra de navegação. Na lista de itens disponíveis, selecione **Dataflow Gen2**.

![](./media/image81.png)

3.  No campo **Name**, insira +++wwi_fact_sale_transform+++ e clique em **Create**.

![](./media/image82.png)

4.  No editor do Power Query, clique no menu suspenso **Get data** e selecione **More...**

![](./media/image83.png)

5.  Na aba **Choose data source**, pesquise +++Lakehouse+++ e clique no conector **Lakehouse**.

![](./media/image84.png)

6.  A caixa de diálogo **Connect to data source** é exibida. Uma nova conexão é criada automaticamente com base no usuário conectado. Clique em **Next**.

![](./media/image85.png)

7.  A caixa de diálogo **Choose data** é exibida. Expanda o espaço de trabalho **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**, expanda o lakehouse **wwilakehouse** e selecione a tabela **fact_sale**. Clique em **Create**.

![](./media/image86.png)

![](./media/image87.png)

8.  A tela agora está preenchida com os dados de **fact_sale**.

![](./media/image88.png)

### Tarefa 2: Transformar os dados importados do lakehouse

1.  Selecione o ícone de tipo de dados no cabeçalho da coluna **InvoiceDateKey** para exibir um menu suspenso e altere o tipo da coluna de **Date/Time** para **Date**.

![](./media/image89.png)

2.  Na aba **Home** da faixa de opções, no grupo **Manage columns**, selecione **Choose columns** \> **Choose columns**.

![](./media/image90.png)

3.  Na caixa de diálogo **Choose columns**, desmarque a coluna **TaxRate** e clique em **OK**.

![](./media/image91.png)

4.  Selecione o menu suspenso de classificação e filtro da coluna **InvoiceDateKey**, selecione **Date filters** e escolha **Between...**

![](./media/image92.png)

5.  Na caixa de diálogo **Filter rows**, selecione as datas entre **1º de janeiro de 2000** e **31 de janeiro de 2000** e clique em **OK**.

![](./media/image93.png)

![](./media/image94.png)

### Tarefa 3: Conectar-se à tabela dimension_city

Você carregará a tabela **dimension_city** que criou no Exercício 3 para enriquecer os dados de fact_sale com informações de cidade e território de vendas.

1.  Na aba **Home** do editor do fluxo de dados, selecione **Get data** e escolha **More...**

![](./media/image95.png)

2.  Na aba **Choose data source**, pesquise +++Lakehouse+++ e clique no conector **Lakehouse**.

![](./media/image96.png)

3.  A caixa de diálogo **Connect to data source** é exibida. Clique em **Next**.

![](./media/image97.png)

4.  Na caixa de diálogo **Choose data**, expanda o espaço de trabalho **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**, expanda o lakehouse **wwilakehouse** e selecione a tabela **dimension_city**. Clique em **Create**.

![](./media/image98.png)

5.  Agora você vê uma segunda consulta, **dimension_city**, no editor do Power Query.

![](./media/image99.png)

### Tarefa 4: Transformar os dados de dimension_city

1.  Certifique-se de que a consulta **dimension_city** está selecionada no painel **Queries**.

2.  Na aba **Home** da faixa de opções, selecione **Choose columns** no grupo **Manage columns**.

![](./media/image100.png)

3.  Na caixa de diálogo **Choose columns**, mantenha apenas as seguintes colunas (desmarque todas as outras) e clique em **OK**:

- **CityKey**

- **City**

- **StateProvince**

- **SalesTerritory**

![](./media/image101.png)

4.  Selecione o menu suspenso de filtro e classificação da coluna **SalesTerritory**. Desmarque as entradas **(blank)** ou **(null)** para remover as linhas sem território e clique em **OK**.

![](./media/image102.png)

![](./media/image103.png)

### Tarefa 5: Combinar os dados de fact_sale e dimension_city

A próxima etapa é combinar as duas tabelas em uma única tabela que inclua o contexto de cidade e território de cada venda e adicionar uma coluna calculada de margem de lucro.

1.  Ative o botão **Diagram view** para ver as duas consultas.

![](./media/image104.png)

2.  Selecione a consulta **fact_sale**. Na aba **Home**, selecione o menu **Combine**, escolha **Merge queries** e selecione **Merge queries as new**.

![](./media/image105.png)

3.  Na caixa de diálogo **Merge**, selecione **dimension_city** como a tabela da direita, aceite o mapeamento sugerido de **CityKey** para **CityKey**, verifique se **Left outer** está selecionado como o tipo de junção e clique em **OK**.

![](./media/image106.png)

![](./media/image107.png)

![](./media/image108.png)

4.  Expanda a coluna **dimension_city**, desmarque a caixa de seleção **CityKey**, mantenha as colunas restantes selecionadas e clique em **OK**.

![](./media/image109.png)

![](./media/image110.png)

5.  Para adicionar uma coluna calculada, selecione a aba **Add column** e escolha **Custom column** no grupo **General**.

![](./media/image111.png)

6.  Na caixa de diálogo **Custom column**, configure a nova coluna da seguinte forma e clique em **OK**.

| **Propriedade** | **Valor** |
|----|----|
| **New column name** | +++ProfitMargin+++ |
| **Data type** | **Currency** |
| **Custom column formula** | +++if [TotalIncludingTax] > 0 then [Profit] / [TotalIncludingTax] else 0+++ |

![](./media/image112.png)

![](./media/image113.png)

7.  Selecione a nova coluna **ProfitMargin**. Em seguida, selecione a aba **Transform** e, no grupo **Number column**, selecione o menu suspenso **Rounding** e escolha **Round...**

![](./media/image114.png)

8.  Na caixa de diálogo **Round**, insira **4** como o número de casas decimais e clique em **OK**.

![](./media/image115.png)

![](./media/image116.png)

9.  Altere o tipo de dados da coluna **InvoiceDateKey** de volta de **Date** para **Date/Time**.

![](./media/image117.png)

![](./media/image118.png)

10. Expanda o painel **Query settings** no lado direito do editor e renomeie a consulta de **Merge** para +++Output+++.

![](./media/image119.png)

**Nota:** ProfitMargin = Profit / TotalIncludingTax. Um valor de 0,35 significa uma margem de lucro de 35% nessa venda. O valor é arredondado para 4 casas decimais para maior precisão nos relatórios.

### Tarefa 6: Carregar a consulta Output em uma tabela gold no lakehouse

Com a consulta Output totalmente preparada, defina o destino de saída.

1.  Selecione a consulta **Output**. Em seguida, selecione o ícone **+** para adicionar um destino de dados a este fluxo de dados.

2.  Na lista de destinos de dados, selecione **Lakehouse** em **New destination**.

![](./media/image120.png)

3.  Na caixa de diálogo **Connect to data destination**, sua conexão já deve estar selecionada. Clique em **Next**.

![](./media/image121.png)

4.  Em **Choose destination target**, selecione **New table**, navegue até **wwilakehouse** no espaço de trabalho **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**, insira +++Gold_Sales_By_City+++ como nome da tabela e clique em **Next**.

![](./media/image122.png)

5.  Clique em **Save settings**.

![](./media/image123.png)

6.  De volta à janela principal do editor, confirme se o painel **Query settings** mostra **Lakehouse** como destino de saída da consulta Output.

![](./media/image124.png)

7.  Selecione **Save and run** na aba **Home**.

![](./media/image125.png)

8.  Aguarde a conclusão da execução do fluxo de dados (aproximadamente de 2 a 3 minutos).

![](./media/image126.png)

9.  Volte para o **wwilakehouse** e clique em **Refresh** na seção **Tables**. Confirme se a tabela **Gold_Sales_By_City** agora aparece em **Tables** no painel **Explorer**.

![](./media/image127.png)

![](./media/image128.png)

![](./media/image129.png)

10. Clique na tabela **Gold_Sales_By_City** para visualizá-la e verifique se ela contém as colunas **City**, **StateProvince**, **SalesTerritory**, **Profit**, **TotalIncludingTax** e **ProfitMargin**.

![](./media/image130.png)

## Exercício 5: Automatizar e enviar notificações com o Data Factory

Neste exercício, você adiciona uma notificação por e-mail ao pipeline, agenda-o e adiciona o fluxo de dados como uma atividade para que todo o processo seja executado de ponta a ponta.

### Tarefa 1: Adicionar uma atividade do Office 365 Outlook ao pipeline

1.  Clique no espaço de trabalho **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id** no menu de navegação à esquerda.

2.  Na página do espaço de trabalho, selecione o pipeline **IngestDataFromSourceToLakehouse**.

![](./media/image131.png)

3.  Selecione a aba **Activities** no editor de pipeline e selecione a atividade **Office 365 Outlook**.

![](./media/image132.png)

4.  Selecione e arraste o caminho **On success** (a caixa de seleção verde no lado direito da atividade Copy) da atividade Copy até a nova atividade do Office 365 Outlook.

![](./media/image133.png)

5.  Selecione a atividade do Office 365 Outlook na tela do pipeline e, em seguida, selecione a aba **Settings** na área de propriedades abaixo da tela. Clique no menu suspenso **Connection** e selecione **Browse all**.

![](./media/image134.png)

6.  Na janela **Choose a data source**, selecione a fonte **Office 365 Email**.

![](./media/image135.png)

7.  Faça login com a conta da qual deseja enviar o e-mail. Você pode usar a conexão existente com a conta já conectada.

8.  Clique em **Connect** para continuar.

![](./media/image136.png)

9.  Selecione a atividade do Office 365 Outlook na tela do pipeline. Na aba **Settings**, configure o e-mail.

10. Insira seu endereço de e-mail no campo **To**. Para usar vários endereços, separe-os com **;**.

![](./media/image137.png)

11. Em **Subject**, selecione o campo para que a opção **Add dynamic content** apareça e, em seguida, selecione-a para abrir o construtor de expressões do pipeline.

![](./media/image138.png)

12. Na caixa de diálogo **Pipeline expression builder**, insira a seguinte expressão e clique em **OK**.

```text
@concat('WWI Data Pipeline Succeeded with Pipeline Run Id: ', pipeline().RunId)
```

![](./media/image139.png)

13. Em **Body**, selecione o campo novamente e escolha a opção **View in expression builder** quando ela aparecer abaixo da área de texto. Insira a seguinte expressão e clique em **OK**.

```text
@concat('RunID = ', pipeline().RunId, ' ; ', 'Files Written: ', activity('Data Copy to Lakehouse').output.filesWritten, ' ; ', 'Throughput: ', activity('Data Copy to Lakehouse').output.throughput)
```

![](./media/image140.png)

![](./media/image141.png)

**Nota:** Substitua **Data Copy to Lakehouse** pelo nome real da sua atividade de cópia, caso tenha usado outro nome.

14. Selecione a aba **Home** na parte superior do editor de pipeline e escolha **Run**. Em seguida, selecione **Save and run** na caixa de diálogo de confirmação para executar essas atividades.

![](./media/image142.png)

![](./media/image143.png)

15. Depois que o pipeline for executado com sucesso, verifique seu e-mail para encontrar o e-mail de confirmação enviado pelo pipeline.

![](./media/image144.png)

![](./media/image145.png)

### Tarefa 2: Agendar a execução do pipeline

Depois de concluir o desenvolvimento e o teste do pipeline, você pode agendá-lo para execução automática.

1.  Na aba **Home** da janela do editor de pipeline, selecione **Schedule**.

![](./media/image146.png)

![](./media/image147.png)

2.  Configure o agendamento conforme necessário. O exemplo abaixo agenda o pipeline para ser executado diariamente às 20h até o final do ano.

| **Propriedade** | **Valor** |
|----|----|
| **Repeat** | **Daily** |
| **Time** | **8:00 PM** |
| **End date** | 31 de dezembro do ano corrente |
| **Time zone** | Selecione o seu fuso horário local |

![](./media/image148.png)

3.  Clique em **Close** para fechar o agendamento.

![](./media/image149.png)

### Tarefa 3: Adicionar uma atividade de fluxo de dados ao pipeline

1.  Na aba **Activities**, arraste e solte a atividade **Dataflow** na tela do pipeline.

![](./media/image150.png)

2.  Escolha **Dataflow** no menu que aparece.

3.  A nova atividade Dataflow é inserida entre a atividade Copy e a atividade do Office 365 Outlook e é selecionada automaticamente, mostrando suas propriedades na área abaixo da tela.

![](./media/image151.png)

4.  Selecione a aba **Settings** e, em seguida, selecione o fluxo de dados **wwi_fact_sale_transform** que você criou no Exercício 4.

![](./media/image152.png)

5.  Selecione a aba **Home** na parte superior do editor de pipeline e escolha **Run**. Em seguida, selecione **Save and run** na caixa de diálogo de confirmação para executar essas atividades.

![](./media/image153.png)

![](./media/image154.png)

6.  Monitore a aba **Output** para confirmar que as três atividades (**Copy data**, **Dataflow**, **Office 365 Outlook**) são concluídas com **Status: Succeeded**.

![](./media/image155.png)

![](./media/image156.png)

![](./media/image157.png)

## Exercício 6: Criar relatórios no Microsoft Fabric

Neste exercício, você adiciona todas as tabelas ao modelo semântico Direct Lake, cria relações entre elas e cria um relatório do Power BI do zero.

### Tarefa 1: Criar relações no modelo semântico Direct Lake

O Power BI é integrado nativamente em toda a experiência do Fabric. Essa integração nativa oferece um modo exclusivo, chamado **Direct Lake**, de acessar os dados do lakehouse para proporcionar a experiência de consulta e de relatórios com melhor desempenho. O Direct Lake carrega arquivos no formato parquet diretamente do data lake, sem precisar consultar um data warehouse ou um endpoint do lakehouse e sem precisar importar ou duplicar dados em um modelo semântico do Power BI.

No modo tradicional **DirectQuery**, o mecanismo do Power BI consulta os dados na origem a cada consulta, e o desempenho depende da velocidade de recuperação dos dados. O DirectQuery elimina a necessidade de copiar dados, garantindo que qualquer alteração na origem seja refletida imediatamente nos resultados da consulta. No modo **Import**, o desempenho é melhor porque os dados ficam disponíveis na memória, mas o mecanismo do Power BI precisa primeiro copiar os dados para a memória durante a atualização, e as alterações na origem só são captadas na próxima atualização.

O Direct Lake elimina essa necessidade de importação ao carregar os arquivos de dados diretamente na memória. Como não há um processo de importação explícito, ele capta as alterações na origem à medida que ocorrem, combinando as vantagens do DirectQuery e do modo Import e evitando suas desvantagens. Por isso, o Direct Lake é a escolha ideal para analisar conjuntos de dados muito grandes e com atualizações frequentes na origem.

1.  No menu à esquerda, selecione o espaço de trabalho **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id** e, em seguida, selecione o modelo semântico chamado **wwisemanticmodel**.

![](./media/image158.png)

2.  Abra o modelo semântico, selecione o menu suspenso de modo no canto superior direito, mude de **Viewing** para **Editing** e selecione **Make any changes**.

![](./media/image159.png)

3.  Na faixa de opções, selecione **Edit tables** para exibir a caixa de diálogo de sincronização de tabelas.

![](./media/image160.png)

4.  Na caixa de diálogo **Edit semantic model**, **selecione todas** as tabelas e clique em **Confirm** na parte inferior da caixa de diálogo para sincronizar o modelo semântico.

![](./media/image161.png)

![](./media/image162.png)

5.  Na tabela **fact_sale**, arraste o campo **CityKey** e solte-o no campo **CityKey** da tabela **dimension_city** para criar uma relação. A caixa de diálogo **Create Relationship** é exibida.

**Nota:** Reorganize as tabelas clicando e arrastando-as para que as tabelas **dimension_city** e **fact_sale** fiquem lado a lado. Faça o mesmo para quaisquer duas tabelas entre as quais você criar uma relação. Isso facilita arrastar as colunas entre as tabelas.

![](./media/image163.png)

6.  Na caixa de diálogo **Create Relationship**:

- **Table 1** é preenchida com **fact_sale** e a coluna **CityKey**.

- **Table 2** é preenchida com **dimension_city** e a coluna **CityKey**.

- **Cardinality**: **Many to one (\*:1)**

- **Cross filter direction**: **Single**

- Deixe marcada a caixa ao lado de **Make this relationship active**.

- Marque a caixa ao lado de **Assume referential integrity**.

- Clique em **Save**.

![](./media/image164.png)

7.  Adicione as seguintes relações com as mesmas configurações de **Create Relationship** mostradas acima:

| **De (fact_sale)** | **Para** |
|----|----|
| **StockItemKey** | **StockItemKey** (dimension_stock_item) |
| **SalespersonKey** | **EmployeeKey** (dimension_employee) |
| **CustomerKey** | **CustomerKey** (dimension_customer) |
| **InvoiceDateKey** | **Date** (dimension_date) |

![](./media/image165.png)

![](./media/image166.png)

![](./media/image167.png)

8.  Depois de adicionar essas relações, seu modelo de dados deve ficar como na imagem a seguir e estará pronto para os relatórios.

![](./media/image168.png)

### Tarefa 2: Criar um relatório

1.  Na faixa de opções superior, selecione **File** e, em seguida, **Create new report** para começar a criar relatórios no Power BI.

![](./media/image169.png)

2.  Na tela de relatório do Power BI, você pode criar relatórios que atendam às suas necessidades de negócios arrastando colunas do painel **Data** para a tela e usando uma ou mais das visualizações disponíveis.

![](./media/image170.png)

**Adicionar um título:**

3.  Na faixa de opções, selecione **Text box**. Digite +++WW Importers Profit Reporting+++, realce o texto e aumente o tamanho da fonte para **20**.

![](./media/image171.png)

4.  Redimensione a caixa de texto, posicione-a no **canto superior esquerdo** da página do relatório e clique fora da caixa de texto.

![](./media/image172.png)

**Adicionar um cartão:**

5.  No painel **Data**, expanda **fact_sale** e marque a caixa ao lado de **Profit**. Essa seleção cria um gráfico de colunas e adiciona o campo ao eixo Y.

![](./media/image173.png)

6.  Com o gráfico selecionado, selecione o visual **Card** no painel **Visualizations**.

![](./media/image174.png)

7.  Essa seleção converte o visual em um cartão. Posicione o cartão abaixo do título.

![](./media/image175.png)

8.  Clique em qualquer lugar da tela em branco (ou pressione a tecla **Esc**) para que o cartão não fique mais selecionado.

**Adicionar um gráfico de barras:**

9.  No painel **Data**, expanda **fact_sale** e marque a caixa ao lado de **Profit**. Essa seleção cria um gráfico de colunas e adiciona o campo ao eixo Y.

![](./media/image176.png)

10. No painel **Data**, expanda **dimension_city** e marque a caixa de **SalesTerritory**. Essa seleção adiciona o campo ao eixo X.

![](./media/image177.png)

11. Com o gráfico selecionado, selecione o visual **Clustered bar chart** no painel **Visualizations**. Essa seleção converte o gráfico de colunas em um gráfico de barras.

![](./media/image178.png)

12. Redimensione o gráfico de barras para preencher a área abaixo do título e do cartão.

![](./media/image179.png)

13. Clique em qualquer lugar da tela em branco (ou pressione a tecla **Esc**) para que o gráfico de barras não fique mais selecionado.

**Criar um gráfico de áreas empilhadas:**

14. No painel **Visualizations**, selecione o visual **Stacked area chart**.

![](./media/image180.png)

15. Reposicione e redimensione o gráfico de áreas empilhadas à direita do cartão e do gráfico de barras.

![](./media/image181.png)

16. No painel **Data**, expanda **fact_sale** e marque a caixa ao lado de **Profit**. Expanda **dimension_date** e marque a caixa ao lado de **FiscalMonthNumber**. Essa seleção cria um gráfico de linhas preenchido que mostra o lucro por mês fiscal.

![](./media/image182.png)

17. No painel **Data**, expanda **dimension_stock_item** e arraste **BuyingPackage** para o campo **Legend**. Essa seleção adiciona uma linha para cada pacote de compra.

![](./media/image183.png)

![](./media/image184.png)

18. Clique em qualquer lugar da tela em branco (ou pressione a tecla **Esc**) para que o gráfico de áreas empilhadas não fique mais selecionado.

**Criar um gráfico de colunas:**

19. No painel **Visualizations**, selecione o visual **Stacked column chart**.

![](./media/image185.png)

20. No painel **Data**, expanda **fact_sale** e marque a caixa ao lado de **Profit**. Essa seleção adiciona o campo ao eixo Y.

21. No painel **Data**, expanda **dimension_employee** e marque a caixa ao lado de **Employee**. Essa seleção adiciona o campo ao eixo X.

![](./media/image186.png)

22. Clique em qualquer lugar da tela em branco (ou pressione a tecla **Esc**) para que o gráfico não fique mais selecionado.

23. Na faixa de opções, selecione **File** \> **Save**.

![](./media/image187.png)

24. Insira +++Profit Reporting+++ como nome do relatório e clique em **Save**.

![](./media/image188.png)

25. Você receberá uma notificação informando que o relatório foi salvo.

![](./media/image189.png)

## Exercício 7: Limpar recursos

Você pode excluir relatórios, pipelines, warehouses e outros itens individualmente ou remover o espaço de trabalho inteiro. Use as etapas a seguir para excluir o espaço de trabalho que você criou para este laboratório.

1.  Selecione o seu espaço de trabalho, **Fabric Dataengineering-DataFactory-@lab.LabInstance.Id**, no menu de navegação à esquerda. A exibição de itens do espaço de trabalho é aberta.

2.  Selecione a opção **...** abaixo do nome do espaço de trabalho e selecione **Workspace settings**.

![](./media/image190.png)

3.  Selecione **General** e, em seguida, selecione **Remove this workspace**.

![](./media/image191.png)

4.  Clique em **Delete** no aviso exibido.

![](./media/image192.png)

5.  Aguarde a notificação de que o espaço de trabalho foi excluído antes de seguir para o próximo laboratório.

![](./media/image193.png)

**Resumo**

Neste laboratório, você implementou um fluxo de trabalho completo de engenharia de dados no Microsoft Fabric. Você criou um espaço de trabalho e um lakehouse do Fabric, carregou os dados de origem em uma tabela Delta, validou os dados com consultas SQL e criou um relatório rápido. Em seguida, ingeriu os dados de exemplo da Wide World Importers com um pipeline do Data Factory, criou tabelas Delta de fatos, dimensões e agregações com um notebook PySpark e usou o Dataflow Gen2 para criar uma tabela gold com uma margem de lucro calculada. Você automatizou o pipeline com uma notificação do Office 365 Outlook, um agendamento e uma atividade Dataflow. Por fim, criou um modelo semântico Direct Lake com relações e um relatório do Power BI. Essas habilidades fornecem a base para desenvolver soluções escaláveis de engenharia de dados usando o Microsoft Fabric.
