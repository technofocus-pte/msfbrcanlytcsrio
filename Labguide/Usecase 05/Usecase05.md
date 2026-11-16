# Caso de uso 4: Análise de próxima geração com Fabric IQ, Agentes de Dados e Rayfin

**Introdução**

As plataformas de dados modernas precisam de uma **camada semântica centrada nos negócios**: um modelo compartilhado que descreve o negócio em seus próprios termos e funciona da mesma forma em muitas fontes de dados. O item **Ontologia (pré-visualização)** no **Microsoft Fabric IQ** fornece essa camada. Em uma ontologia, você define **tipos de entidade** (conceitos de negócios como lojas, produtos e remessas), suas **propriedades**, os **relacionamentos** entre eles e as **regras** de negócio e, em seguida, associa essas definições a dados reais no OneLake: tabelas de lakehouse, tabelas de eventhouse (streaming) e modelos semânticos do Power BI.

Depois que a ontologia estiver pronta, pessoas e agentes de IA poderão explorá-la como um **grafo** e fazer perguntas em **linguagem natural** sem conhecer as tabelas ou junções subjacentes.

Este laboratório se baseia na série de tutoriais do Microsoft Learn [Ontology (preview) tutorial](https://learn.microsoft.com/fabric/iq/ontology/tutorial-0-introduction).

**Cenário**

**Lakeshore Retail** é um varejista multirregional fictício que vende mercadorias em geral, produtos perecíveis e produtos congelados (como sorvete) por meio de lojas abastecidas por centros de distribuição regionais.

Os dados da Lakeshore Retail estão distribuídos em vários sistemas:

- **Tabelas de lakehouse** com localizações (lojas e centros de distribuição), produtos, fornecedores, posições de estoque, remessas e unidades de refrigeração.

- **Telemetria de streaming** das unidades de refrigeração de cada loja (temperatura, umidade, status da porta) em um eventhouse.

- Um **modelo semântico do Power BI** com transações de vendas e medidas de vendas.

A equipe de operações precisa responder rapidamente a perguntas entre domínios, por exemplo:

*Quais lojas de alta prioridade no Oeste têm produtos congelados abaixo do estoque de segurança, uma exceção recente de temperatura de refrigeração e disponibilidade na prateleira abaixo da meta? Para cada loja, mostre a próxima remessa de entrada e sua chegada prevista.*

Hoje, responder a isso significa unir manualmente dados de estoque, telemetria, localização e remessa. Neste laboratório, você é um engenheiro de dados da Lakeshore Retail. Você cria uma ontologia chamada **RetailSalesOntology** que conecta todas essas fontes em termos de negócios, a enriquece com descrições e regras de negócio, a explora como um grafo e usa o **Agente de Ontologia** para responder à pergunta do cenário em linguagem natural.

**Objetivos**

Depois de concluir este laboratório, você poderá:

- Preparar um workspace do Microsoft Fabric com um lakehouse, um eventhouse e um modelo semântico como fontes de dados

- Criar um item **Ontologia (pré-visualização)** e definir tipos de entidade, incluindo tipos de entidade herdados

- Associar tipos de entidade a dados estáticos em tabelas do OneLake, a dados de série temporal em um eventhouse e a um modelo semântico do Power BI

- Criar tipos de relacionamento que representam processos corporativos reais (por exemplo, Store *operates* Refrigeration Unit)

- Enriquecer a ontologia com metadados de entidade, propriedade e relacionamento e com regras de negócio

- Explorar e validar a ontologia com exibições da tela, instâncias de entidade, o grafo materializado e consultas de caminho

- Fazer perguntas em linguagem natural com o **Agente de Ontologia**

**Arquitetura**

| **Componente**                                   | **Função no laboratório**                                                                                      |
|--------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| Workspace do Fabric Fabric IQ Ontology\<number\> | Contém todos os itens do laboratório                                                                           |
| Lakehouse IQ_Lakehouse                           | Dados estáticos: localizações, produtos, fornecedores, posições de estoque, remessas, unidades de refrigeração |
| Eventhouse TelemetryDataEH (banco de dados KQL)  | Dados de streaming: tabela RefrigerationTelemetry                                                              |
| Modelo semântico SalesReport                     | Transações de vendas e medidas (Gross Margin %, Net Sales)                                                     |
| Ontologia RetailSalesOntology                    | Tipos de entidade de negócios, relacionamentos, metadados e regras associados aos dados                        |
| Modelo de grafo                                  | Grafo materializado da ontologia para exploração e consultas de caminho                                        |
| Agente de ontologia (pré-visualização)           | Copilot que responde a perguntas em linguagem natural sobre a ontologia                                        |

**Ontologia de destino**

| **Tipo de entidade**    | **Herda de** | **Fonte de dados**                                                       |
|-------------------------|--------------|--------------------------------------------------------------------------|
| Location                |              | IQ_Lakehouse \> dimlocations                                             |
| Store                   | Location     | IQ_Lakehouse \> dimlocations                                             |
| Distribution Center     | Location     | IQ_Lakehouse \> dimlocations                                             |
| Product                 |              | IQ_Lakehouse \> dimproducts                                              |
| Frozen Product          | Product      | IQ_Lakehouse \> dimproducts                                              |
| Perishable Product      | Product      | IQ_Lakehouse \> dimproducts                                              |
| Inventory               |              | IQ_Lakehouse \> fact_inventory_positions                                 |
| Supplier                |              | IQ_Lakehouse \> dimsuppliers                                             |
| Shipment                |              | IQ_Lakehouse \> factshipments                                            |
| Refrigeration Unit      |              | IQ_Lakehouse \> dim_refrigeration_units                                  |
| Refrigeration Telemetry |              | TelemetryDataEH \> RefrigerationTelemetry                                |
| Sale                    |              | Modelo semântico SalesReport \> Sales (criado com o agente de ontologia) |

**Pré-requisitos**

- Um workspace em uma **capacidade habilitada para o Microsoft Fabric**.

- Um administrador do Fabric habilitou as configurações de locatário para itens **Ontologia (pré-visualização)**, itens do Fabric e recursos do Copilot / Azure OpenAI (necessários para o agente de ontologia).

- Os arquivos do laboratório em **C:\LabFiles\Lab1** na VM do laboratório: seis arquivos CSV com os dados estáticos, **refrigeration_telemetry.csv** e **SalesReport.pbix**.

**Observação:** A ontologia, o agente de ontologia e o grafo no Microsoft Fabric estão em **pré-visualização**. As telas e os rótulos podem sofrer pequenas alterações. As respostas geradas por IA podem estar incorretas; sempre verifique as respostas importantes em relação aos dados de origem.

## Exercício 1: Configurar o ambiente

Neste exercício, você prepara as três fontes de dados para a ontologia: um lakehouse com dados estáticos, um eventhouse com dados de streaming e um modelo semântico do Power BI com dados de vendas.

### Tarefa 1: Criar um workspace do Fabric

1.  Abra o navegador da web, acesse +++https://app.fabric.microsoft.com/+++ e entre com suas credenciais.

    |                 |                                                  |
    |-----------------|--------------------------------------------------|
    | Nome de usuário | +++@lab.CloudPortalCredential(User1).Username+++ |
    | Senha           | +++@lab.CloudPortalCredential(User1).Password+++ |

2.  Na home page do Fabric, selecione **+ New workspace**.

    ![](./media/image1.png)

3.  No painel **Create a workspace**, insira os seguintes detalhes:

    - **Name**: +++Fabric IQ Ontology@lab.LabInstance.Id+++ (o nome deve ser único)

    - Expanda **Advanced**.

    ![](./media/image2.png)

4.  Em **License mode**, selecione **Fabric** e, em seguida, selecione **Apply**.

    ![](./media/image3.png)

5.  O novo workspace é aberto.

    ![](./media/image4.png)

### Tarefa 2: Criar um lakehouse

1.  No workspace, selecione **+ New item**.

    ![](./media/image5.png)

2.  Filtre por e selecione **Lakehouse**.

    ![](./media/image6.png)

3.  Na caixa de diálogo **New Lakehouse**, insira +++IQ_Lakehouse+++ na caixa **Name**, desmarque a caixa de seleção **Lakehouse schemas** e, em seguida, selecione **Create**.

    ![](./media/image7.png)

    **Observação:** O tutorial do Microsoft Learn mantém **Lakehouse schemas** selecionado e, nesse caso, as tabelas são criadas no esquema **dbo**. O restante deste laboratório funciona das duas formas.

4.  O lakehouse é aberto. Aguarde a notificação **Successfully created SQL analytics endpoint**.

    ![](./media/image8.png)

    ![](./media/image9.png)

### Tarefa 3: Carregar os dados estáticos em tabelas de lakehouse

1.  Na página **IQ_Lakehouse**, em **Get data in your lakehouse**, selecione **Upload files**.

    ![](./media/image10.png)

2.  No painel **Upload files**, selecione o ícone de pasta.

    ![](./media/image11.png)

3.  Navegue até **C:\LabFiles\Lab1**, selecione os seis arquivos CSV **dim_refrigeration_units, dimlocations, dimproducts, dimsuppliers, fact_inventory_positions e factshipments** e, em seguida, selecione **Open**.

    ![](./media/image12.png)

    **Importante:** Não carregue **refrigeration_telemetry.csv** nem **SalesReport.pbix**. Esses arquivos serão carregados no Eventhouse e no workspace posteriormente neste exercício.

4.  Selecione **Upload**.

    ![](./media/image13.png)

5.  Verifique se todos os seis arquivos foram carregados e, em seguida, feche o painel **Upload files** selecionando **X**.

    ![](./media/image14.png)

6.  No **Explorer**, selecione **Files** e verifique se os seis arquivos estão listados. Selecione **Refresh** se necessário.

    ![](./media/image15.png)

7.  Passe o cursor sobre **dim_refrigeration_units.csv**, selecione **...** e, em seguida, selecione **Load to Tables \> New table**.

    ![](./media/image16.png)

    ![](./media/image17.png)

8.  Na caixa de diálogo **Load file to new table**, mantenha o nome da tabela e as configurações padrão e, em seguida, selecione **Load**.

    ![](./media/image18.png)

9.  Verifique se a tabela **dim_refrigeration_units** aparece em **Tables**.

    ![](./media/image19.png)

    **Observação:** Talvez seja necessário selecionar **Refresh** mais de uma vez para ver a tabela e pré-visualizar seus dados.

10. Repita as etapas 7-9 para cada um dos cinco arquivos restantes. Por exemplo, para **dimlocations.csv**, selecione **... \> Load to Tables \> New table** e, em seguida, selecione **Load**.

    ![](./media/image20.png)

    ![](./media/image21.png)

11. Ao concluir, o lakehouse terá seis tabelas: **dim_refrigeration_units**, **dimlocations**, **dimproducts**, **dimsuppliers**, **fact_inventory_positions** e **factshipments**. Os nomes padrão das tabelas correspondem aos nomes dos arquivos em letras minúsculas.

    ![](./media/image22.png)

12. Na barra de navegação à esquerda, selecione seu workspace **Fabric IQ Ontology\<number\>**.

    ![](./media/image23.png)

### Tarefa 4: Carregar os dados de streaming em um eventhouse

1.  No workspace, selecione **+ New item** e, em seguida, selecione **Eventhouse**.

    ![](./media/image24.png)

2.  Insira +++TelemetryDataEH+++ como **Eventhouse name** e, em seguida, selecione **Create**.

    ![](./media/image25.png)

3.  O eventhouse é aberto quando está pronto. Um banco de dados KQL com o mesmo nome é criado automaticamente.

    ![](./media/image26.png)

4.  Em **KQL databases**, selecione **TelemetryDataEH** para abrir o banco de dados.

    ![](./media/image27.png)

    ![](./media/image28.png)

5.  Na faixa de opções, selecione **Get data \> Local file**.

    ![](./media/image29.png)

6.  Em **Select or create a destination table**, selecione **+ New table**.

    ![](./media/image30.png)

7.  Insira +++RefrigerationTelemetry+++ como o nome da tabela, selecione a marca de seleção e, em seguida, selecione **Browse for files**.

    ![](./media/image31.png)

    ![](./media/image32.png)

8.  Navegue até **C:\LabFiles\Lab1**, selecione **refrigeration_telemetry.csv** e, em seguida, selecione **Open**.

    ![](./media/image33.png)

9.  Selecione **Next**.

    ![](./media/image34.png)

10. Na página **Inspect the data**, examine as colunas (**TelemetryId**, **UnitId**, **Timestamp**, **TemperatureC** e outras) e, em seguida, selecione **Finish**.

    ![](./media/image35.png)

11. Aguarde até que a ingestão mostre **1 blobs: 1 succeeded, 0 failed** e, em seguida, selecione **Close**.

    ![](./media/image36.png)

12. Verifique se o banco de dados KQL mostra a tabela **RefrigerationTelemetry**.

    ![](./media/image37.png)

13. Na barra de navegação esquerda, selecione seu workspace.

    ![](./media/image38.png)

### Tarefa 5: Carregar o modelo semântico

1.  No workspace, selecione **Import \> Report, Paginated Report or Workbook \> From this computer**.

    ![](./media/image39.png)

2.  Navegue até **C:\LabFiles\Lab1**, selecione **SalesReport.pbix** e, em seguida, selecione **Open**.

    ![](./media/image40.png)

3.  Verifique se o relatório **SalesReport** e um modelo semântico **SalesReport** com o mesmo nome aparecem no workspace.

    ![](./media/image41.png)

## Exercício 2: Construir a ontologia

Neste exercício, você cria o item de ontologia e seus **tipos de entidade**. Um tipo de entidade representa um tipo de objeto de negócios. Você cria suas **propriedades** associando-as a colunas em uma fonte de dados. Você também usa a **herança**: um tipo de entidade derivado (por exemplo, Store) herda todas as propriedades de seu tipo de entidade base (*Location*)

### Tarefa 1: Criar o item de ontologia

1.  No workspace, selecione **+ New item**. Pesquise e selecione **Ontology (preview)**.

    ![](./media/image42.png)

2.  Na caixa de diálogo **New Ontology**, insira +++RetailSalesOntology+++ como **Name**, mantenha seu workspace como **Location** e, em seguida, selecione **Create**.

    ![](./media/image43.png)

    **Dica:** Os nomes das ontologias devem começar com uma letra e podem conter apenas letras, números e sublinhados. Não use espaços nem hífens.

    **Importante:** Se o Fabric não conseguir criar o item de ontologia, peça ao administrador para verificar se as configurações de locatário necessárias estão habilitadas.

3.  A ontologia é aberta na tela de configuração.

    ![](./media/image44.png)

### Tarefa 2: Criar o tipo de entidade Location

1.  Na faixa de opções, selecione **+ Add entity type**.

    ![](./media/image45.png)

2.  Insira +++Location+++ como **Entity type name** e, em seguida, selecione **Add Entity Type**.

    ![](./media/image46.png)

3.  O tipo de entidade **Location** é adicionado à tela e ao **Explorer**.

    ![](./media/image47.png)

4.  Na tela, selecione **...** ao lado de **Location** e, em seguida, selecione **Bind data**.

    ![](./media/image48.png)

5.  Em **Add a data source**, selecione **Add**.

    ![](./media/image49.png)

6.  No **OneLake catalog**, expanda **IQ_Lakehouse**, selecione a tabela **dimlocations** e, em seguida, selecione **Select table**.

    ![](./media/image50.png)

7.  Examine a fonte. **dimlocations** está definida como **Primary source**.

    ![](./media/image51.png)

8.  Selecione **Entity type properties**.

    ![](./media/image52.png)

9.  As colunas da tabela **dimlocations** são populadas como propriedades propostas (**LocationId**, **LocationType**, **Name**, **Region**, **City** e outras). Sem fazer nenhuma alteração, selecione **Create**.

    ![](./media/image53.png)

10. Quando a mensagem **Entity type updated successfully** aparecer, selecione **Cancel** para fechar a página de associação.

    ![](./media/image54.png)

11. A página **Configure** do tipo de entidade é aberta, exibindo suas propriedades e a respectiva fonte de dados.

    ![](./media/image55.png)

### Tarefa 3: Criar o tipo de entidade Store (herda de Location)

1.  Selecione **Home** para retornar à tela de configuração.

    ![](./media/image56.png)

2.  Na faixa de opções, selecione **+ Add entity type**.

    ![](./media/image57.png)

3.  Insira +++Store+++ como **Entity type name**. Expanda **Additional configuration**, defina **Choose entity to inherit from (optional)** como **Location** e, em seguida, selecione **Add Entity Type**.

    ![](./media/image58.png)

4.  O tipo de entidade **Store** aparece na tela.

    ![](./media/image59.png)

5.  Com **Store** selecionado no **Explorer**, selecione **View Entity Type details**.

    ![](./media/image60.png)

6.  Na guia **Configure**, observe que **Store** já tem propriedades. Elas são herdadas de **Location**, mas sua **Data source** é **Unbound**.

    ![](./media/image61.png)

7.  Selecione **Manage property bindings \> Add binding and properties**.

    ![](./media/image62.png)

8.  Selecione **Add**.

    ![](./media/image63.png)

9.  Expanda **IQ_Lakehouse**, selecione **dimlocations** e, em seguida, selecione **Select table**.

    ![](./media/image64.png)

10. Selecione **Entity type properties**, verifique se cada propriedade tem uma coluna de origem e, em seguida, selecione **Create**.

    ![](./media/image65.png)

11. Quando **Entity type updated successfully** aparecer, selecione **Cancel**.

    ![](./media/image66.png)

    **Observação:** Tanto **Store** quanto **Distribution Center** usam a tabela **dimlocations**. A coluna **LocationType** (STORE ou DC) diferencia os dois. Você documenta isso nas descrições das entidades no Exercício 4.

### Tarefa 4: Criar os tipos de entidade restantes

Crie os seguintes tipos de entidade com as mesmas etapas. Para cada um, associe a tabela listada e mantenha todas as colunas da fonte como propriedades.

| **Nome do tipo de entidade** | **Herda de** | **Tabela da fonte de dados**              |
|------------------------------|--------------|-------------------------------------------|
| Distribution Center          | Location     | IQ_Lakehouse \> dimlocations              |
| Product                      |              | IQ_Lakehouse \> dimproducts               |
| Frozen Product               | Product      | IQ_Lakehouse \> dimproducts               |
| Perishable Product           | Product      | IQ_Lakehouse \> dimproducts               |
| Inventory                    |              | IQ_Lakehouse \> fact_inventory_positions  |
| Supplier                     |              | IQ_Lakehouse \> dimsuppliers              |
| Shipment                     |              | IQ_Lakehouse \> factshipments             |
| Refrigeration Unit           |              | IQ_Lakehouse \> dim_refrigeration_units   |
| Refrigeration Telemetry      |              | TelemetryDataEH \> RefrigerationTelemetry |

As etapas a seguir mostram os primeiros como exemplos.

1.  **Distribution Center:** Selecione **Home \> + Add entity type**. Insira +++Distribution Center+++, expanda **Additional configuration**, selecione **Location** como tipo de entidade base e, em seguida, selecione **Add Entity Type**.

    ![](./media/image67.png)

    ![](./media/image68.png)

2.  Selecione **...** ao lado de **Distribution Center \> Bind data**, selecione **Add**, selecione **IQ_Lakehouse \> dimlocations** e, em seguida, selecione **Select table**.

    ![](./media/image69.png)

    ![](./media/image70.png)

    ![](./media/image71.png)

3.  Selecione **Entity type properties \> Create** e, quando **Entity type updated successfully** aparecer, selecione **Cancel**. A página **Configure** mostra as propriedades associadas.

    ![](./media/image72.png)

    ![](./media/image73.png)

    ![](./media/image74.png)

4.  **Product:** Selecione **Home \> + Add entity type**, insira +++Product+++ e selecione **Add Entity Type**.

    ![](./media/image75.png)

    **Observação:** Use **Product** (ou o plural, Products), conforme indicado aqui. Evite nomes de tipos de entidade que sejam palavras reservadas no GQL, a linguagem de consulta de grafos, como **ORDER**.

5.  Selecione **... \> Bind data \> Add**, selecione **IQ_Lakehouse \> dimproducts**, selecione **Select table**, depois **Entity type properties \> Create** e, em seguida, **Cancel**.

    ![](./media/image76.png)

    ![](./media/image77.png)

    ![](./media/image78.png)

6.  **Frozen Product:** Selecione **Home \> + Add entity type**, insira +++Frozen Product+++, defina **Product** como tipo de entidade base e selecione **Add Entity Type**. Em seguida, vincule-o a **IQ_Lakehouse \> dimproducts** da mesma forma.

    ![](./media/image79.png)

    ![](./media/image80.png)

7.  Crie **Perishable Product** (com **Product** como tipo de entidade base), **Inventory**, **Supplier**, **Shipment** e **Refrigeration Unit** da mesma forma, usando as tabelas da tabela acima.

8.  **Refrigeration Telemetry:** Selecione **Home \> + Add entity type**, insira +++Refrigeration Telemetry+++ e, em seguida, selecione **Add Entity Type**.

    ![](./media/image81.png)

9.  Selecione **... \> Bind data \> Add**. No **OneLake catalog**, expanda **TelemetryDataEH \> TelemetryDataEH** (banco de dados KQL), selecione **RefrigerationTelemetry** e, em seguida, **Select table**.

    ![](./media/image82.png)

    **Observação:** Este tipo de entidade usa a tabela do **eventhouse**, e não uma tabela de lakehouse, como sua fonte de dados.

10. Selecione **Entity type properties**, examine as propriedades (**TelemetryId**, **UnitId**, **Timestamp**, **TemperatureC**, **HumidityPct**, **DoorOpen** e outras), selecione **Create** e, em seguida, **Cancel**.

    ![](./media/image83.png)

11. **Ponto de verificação:** Selecione **Home**. O **Explorer** lista 11 tipos de entidade: **Location**, **Store**, **Distribution Center**, **Product**, **Frozen Product**, **Perishable Product**, **Inventory**, **Supplier**, **Shipment**, **Refrigeration Unit** e **Refrigeration Telemetry**.

    ![](./media/image84.png)

### Tarefa 5: Adicionar o tipo de entidade Sale com o agente de ontologia

O **Agente de Ontologia** também pode *criar* a ontologia. Nesta tarefa, você o utilizará para criar um tipo de entidade **Sale** associado ao modelo semântico **SalesReport**.

1.  Na faixa de opções, selecione **Ontology agent**. O agente será aberto em um painel à direita, no modo **Plan**.

    ![](./media/image85.png)

    **Observação:** No modo **Plan**, o agente pode consultar e explorar a ontologia e propor alterações, mas não altera nada. No modo **Act**, ele aplica as alterações.

2.  Na caixa **Say something**, insira o seguinte prompt e selecione **Send**:

    +++Create a new entity type called Sale, bound to data in the Sales table from the SalesReport semantic model inside this workspace.+++

    ![](./media/image86.png)

3.  O agente cria um plano para o novo tipo de entidade. Revise o resumo **Enrichment added**.

    ![](./media/image87.png)

4.  Mude o botão de alternância para **Act**, insira +++Apply the entity type plan+++ e, em seguida, selecione **Send**.

    ![](./media/image88.png)

5.  Aguarde enquanto o agente gera as alterações na ontologia.

    ![](./media/image89.png)

6.  O agente adiciona o tipo de entidade **Sale** à tela e relata o resultado.

    ![](./media/image90.png)

    **Observação:** O agente pode relatar que a aplicação foi *parcial* devido a conflitos com outras definições de rascunho. Desde que **Sale exista agora**, você poderá continuar.

7.  Selecione **Sale** no **Explorer** e, em seguida, selecione **View Entity Type details**.

    ![](./media/image91.png)

8.  Verifique se as propriedades (**Channel**, **CostAmount**, **NetSalesAmount**, **ProductId**, **SaleId** e outras) estão associadas à tabela **Sales** do modelo semântico.

    ![](./media/image92.png)

9.  Role para baixo até a seção **Metrics** e verifique se duas métricas, **Gross Margin %** e **Net Sales**, foram adicionadas a partir das medidas DAX do modelo semântico.

    ![](./media/image93.png)

## Exercício 3: Criar tipos de relacionamento

Um **tipo de relacionamento** conecta um tipo de entidade de origem a um tipo de entidade de destino. Neste laboratório, você os vinculará por meio de propriedades: a **propriedade de origem** e a **propriedade de destino** devem conter os mesmos valores.

### Tarefa 1: Criar o relacionamento Store operates Refrigeration Unit

1.  Na tela de edição, selecione **...** ao lado de um tipo de entidade e selecione **Add relationship type** (ou selecione **Add relationship** na faixa de opções).

    ![](./media/image94.png)

2.  Insira os seguintes detalhes e, em seguida, selecione **Create**:

    - **Relationship type name**: +++operates+++

    - **Origin entity type**: **Store**

    - **Target entity type**: **Refrigeration Unit**

    ![](./media/image95.png)

3.  O relacionamento aparecerá na tela de edição entre **Store** e **Refrigeration Unit**.

    ![](./media/image96.png)

4.  Selecione o relacionamento **operates** para abrir sua configuração.

    ![](./media/image97.png)

5.  A página tem três seções: **Origin entity type** (Store), **Relationship** (operates) e o **Target entity type** (Refrigeration Unit). Mantenha a opção **Use mapping table?** definida como **Off**.

    ![](./media/image98.png)

6.  Em **Property**, no tipo de entidade de origem, selecione **LocationId**. No tipo de entidade de destino, em **Property**, selecione **StoreId**. Selecione **Save**.

    ![](./media/image99.png)

    **Observação:** Essas configurações significam que **LocationId** em uma entidade Store corresponde ao valor **StoreId** em uma Refrigeration Unit.

7.  Quando a mensagem **Successfully updated the relationship type** aparecer, selecione **Cancel**.

    ![](./media/image100.png)

8.  A página **Configure** de **Store** mostra o novo relacionamento na seção **Relationships**.

    ![](./media/image101.png)

### Tarefa 2: Criar os demais tipos de relacionamento

1.  Selecione **Home** e, em seguida, selecione **Add relationship**.

    ![](./media/image102.png)

2.  Crie o relacionamento **deliversTo**: origem **Shipment**, destino **Store** e, em seguida, selecione **Create**.

    ![](./media/image103.png)

    ![](./media/image104.png)

3.  Selecione o relacionamento **deliversTo**, defina a **Property** de origem como **ToStoreId** e a **Property** de destino como **LocationId**, selecione **Save** e, em seguida, **Cancel**.

    ![](./media/image105.png)

4.  Crie os demais tipos de relacionamento da mesma maneira:

    | **Nome do tipo de relacionamento** | **Tipo de entidade de origem (propriedade)** | **Tipo de entidade de destino (propriedade)** |
    |------------------------------------|----------------------------------------------|-----------------------------------------------|
    | operates                           | Store (LocationId)                           | Refrigeration Unit (StoreId)                  |
    | deliversTo                         | Shipment (ToStoreId)                         | Store (LocationId)                            |
    | occursAt                           | Sale (StoreId)                               | Store (LocationId)                            |
    | stockedAt                          | Inventory (StoreId)                          | Store (LocationId)                            |
    | originatesAt                       | Shipment (FromLocationId)                    | Distribution Center (LocationId)              |
    | forProduct                         | Sale (ProductId)                             | Product (ProductId)                           |
    | stockedAt                          | Product (ProductId)                          | Inventory (ProductId)                         |
    | suppliedBy                         | Product (SupplierId)                         | Supplier (SupplierId)                         |
    | contains                           | Shipment (ProductId)                         | Product (ProductId)                           |
    | hasTelemetryReading                | Refrigeration Unit (UnitId)                  | Refrigeration Telemetry (UnitId)              |

5.  **Ponto de verificação:** Selecione **Home**. A tela de edição exibirá os relacionamentos que conectam os tipos de entidade.

    ![](./media/image106.png)

    **Importante:** Selecione as propriedades corretas. Se as propriedades de origem e de destino contiverem valores diferentes, o relacionamento será criado, mas não encontrará correspondências.

## Exercício 4: Enriquecer a ontologia

Os metadados e as regras fornecem aos agentes de IA o contexto de negócios necessário para interpretar os dados corretamente. Neste exercício, você adicionará descrições aos tipos de entidade, às propriedades e aos tipos de relacionamento, além de definir regras de negócios.

### Tarefa 1: Adicionar metadados aos tipos de entidade

1.  Selecione **Store** no **Explorer** e, em seguida, selecione **View Entity Type details**.

    ![](./media/image107.png)

2.  Role a página até **Entity metadata**. Em **Description**, insira o texto a seguir e, em seguida, selecione **Update**:

    +++Filtered locations binding where LocationType = STORE; priorityDefinition=Tier 1 stores require same-day response.+++

    ![](./media/image108.png)

3.  Verifique a descrição e, em seguida, selecione **Home**.

    ![](./media/image109.png)

4.  Adicione descrições aos seguintes tipos de entidade da mesma forma (selecione o tipo de entidade, selecione **View Entity Type details**, insira a **Description** e selecione **Update**):

    | **Tipo de entidade** | **Descrição**                                                 |
    |----------------------|---------------------------------------------------------------|
    | Distribution Center  | +++Filtered locations binding where LocationType = DC.+++     |
    | Frozen Product       | +++Filtered items binding where StorageClass = FROZEN.+++     |
    | Perishable Product   | +++Filtered items binding where StorageClass = PERISHABLE.+++ |

    ![](./media/image110.png)

    ![](./media/image111.png)

    ![](./media/image112.png)

    ![](./media/image113.png)

    ![](./media/image114.png)

5.  Abra **Inventory**. Em **Additional metadata**, selecione **+** e adicione os seguintes pares de chave e valor. Em seguida, selecione **Update**:

    - **On-Shelf Availability %**: +++On-shelf availability percentage is calculated by dividing the total ShelfAvailableUnits by the total ShelfCapacityUnits. If total ShelfCapacityUnits is zero, the result is blank to avoid division by zero.+++

    - **Low On-Shelf Availability**: +++On-Shelf Availability % less than 95+++

    ![](./media/image115.png)

6.  Abra **Sale**. Revise os campos **Description** e **Synonyms**, que foram adicionados a partir do modelo semântico. Não é necessário fazer alterações.

    ![](./media/image116.png)

### Tarefa 2: Adicionar metadados às propriedades

1.  No **Explorer**, selecione **...** ao lado de **Product** e, em seguida, selecione **Bind data**.

    ![](./media/image117.png)

2.  Selecione **Entity type properties**.

    ![](./media/image118.png)

3.  Na linha **ProductId**, selecione **Metadata**.

    ![](./media/image119.png)

4.  Em **Description**, insira +++Enterprise product identifier; not a supplier SKU or UPC.+++ e selecione **Update**.

    ![](./media/image120.png)

5.  Selecione **Save** e, quando a mensagem **Entity type updated successfully** aparecer, selecione **Cancel**.

    ![](./media/image121.png)

    ![](./media/image122.png)

6.  Repita o procedimento para **Refrigeration Telemetry**: na linha **TemperatureC**, selecione **Metadata**, insira +++Temperature in Celsius+++, selecione **Update** e, em seguida, **Save**.

    ![](./media/image123.png)

    ![](./media/image124.png)

    ![](./media/image125.png)

7.  Repita o procedimento para **Inventory**: na linha **InventoryStatus**, selecione **Metadata**, insira +++0 is AT_RISK and 1 is HEALTHY.+++, selecione **Update** e, em seguida, **Save**.

    ![](./media/image126.png)

    ![](./media/image127.png)

### Tarefa 3: Adicionar metadados aos relacionamentos

1.  Selecione **Home**, selecione **Store** e, na tela de edição, selecione o relacionamento **operates**.

    ![](./media/image128.png)

    ![](./media/image129.png)

2.  Na seção **Metadata**, selecione **Edit**.

    ![](./media/image130.png)

3.  Em **Description**, insira +++Identifies the refrigeration equipment operating in a store.+++ e selecione **Update**.

    ![](./media/image131.png)

4.  Selecione **Save** e, quando a mensagem **Successfully updated the relationship type** aparecer, selecione **Cancel**.

    ![](./media/image132.png)

    ![](./media/image133.png)

5.  Adicione descrições aos demais tipos de relacionamento da mesma maneira:

    | **Relacionamento**                                                  | **Descrição**                                                                                            |
    |---------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|
    | deliversTo (Shipment \> Store)                                      | Identifica a loja que recebe a remessa.                                                                  |
    | occursAt (Sale \> Store)                                            | Identifica onde a venda ocorreu.                                                                         |
    | stockedAt (Inventory \> Store)                                      | Representa um sortimento ativo da loja e sua posição atual de estoque, e não apenas uma venda histórica. |
    | originatesAt (Shipment \> Distribution Center)                      | Identifica o centro de distribuição que envia a remessa.                                                 |
    | forProduct (Sale \> Product)                                        | Identifica o item vendido.                                                                               |
    | stockedAt (Product \> Inventory)                                    | O item faz parte do sortimento ativo da loja e possui uma posição de estoque atual nessa loja.           |
    | suppliedBy (Product \> Supplier)                                    | Identifica o fornecedor responsável pelos itens.                                                         |
    | contains (Shipment \> Product)                                      | Identifica o item que está sendo reabastecido.                                                           |
    | hasTelemetryReading (Refrigeration Unit \> Refrigeration Telemetry) | Identifica as leituras de telemetria dos sensores das unidades de refrigeração.                          |

### Tarefa 4: Adicionar regras de negócio

As **regras** descrevem, em linguagem natural o que deve, não deve ou deveria ser verdadeiro no negócio. As palavras que correspondem aos tipos de entidade são vinculadas à ontologia.

1.  Selecione **Home** e, no **Explorer**, selecione **Rules**.

    ![](./media/image134.png)

2.  Selecione **+ Create rule**.

    ![](./media/image135.png)

3.  Insira +++Cold-chain exception+++ como **Rule name** e selecione **Create**.

    ![](./media/image136.png)

4.  Em **Rule definition**, insira o texto a seguir. Em **Linked ontology concepts**, selecione **Add concept** e adicione **Frozen Product** e **Refrigeration Unit**. Selecione **Save**.

    +++A frozen product has a cold-chain exception when the temperature of the refrigeration unit storing it remains above the product's maximum storage temperature for more than 20 minutes.+++

    ![](./media/image137.png)

5.  Quando **Rule saved** aparecer, selecione **Cancel**.

    ![](./media/image138.png)

6.  Selecione **New rule**, insira +++Inventory at risk+++ e selecione **Create**.

    ![](./media/image139.png)

    ![](./media/image140.png)

7.  Insira a definição +++A store inventory position is at risk when projected on-hand inventory falls below safety stock before the next scheduled delivery+++, vincule **Store** e **Inventory**, selecione **Save** e, em seguida, **Cancel**.

    ![](./media/image141.png)

8.  Selecione **New rule**, insira +++Late replenishment+++ e selecione **Create**.

    ![](./media/image142.png)

9.  Insira a definição +++A replenishment shipment is late when its estimated arrival is more than four hours after its scheduled arrival.+++, vincule **Shipment**, selecione **Save** e, em seguida, **Cancel**.

    ![](./media/image143.png)

10. Verifique se a lista **Business rules** mostra as três regras.

    ![](./media/image144.png)

## Exercício 5: Explorar a ontologia

### Tarefa 1: Explorar as visualizações da tela de edição e as instâncias de entidades

1.  Selecione **Home** e, em seguida, selecione **Product**. No canto superior direito da tela de edição, selecione **Full ontology** para visualizar todos os tipos de entidade e relacionamentos.

    ![](./media/image145.png)

2.  Selecione **Lineage** para visualizar a estrutura de herança: **Product** possui dois tipos derivados, **Frozen Product** e **Perishable Product**.

    ![](./media/image146.png)

3.  Selecione **Relationship** para visualizar apenas os relacionamentos do tipo de entidade selecionado.

    ![](./media/image147.png)

4.  Com **Product** selecionado, escolha **View Entity Type details**.

    ![](./media/image148.png)

5.  Selecione a guia **Instances**. A lista exibe os registros de produtos preenchidos a partir da tabela **dimproducts**.

    ![](./media/image149.png)

### Tarefa 2: Definir as chaves dos tipos de entidade

Uma **chave de tipo de entidade** identifica exclusivamente cada registro de um tipo de entidade. Todos os tipos de entidade no grafo precisam de uma chave.

1.  Selecione **Location** e, em seguida, **View Entity Type details**.

    ![](./media/image150.png)

2.  Ao lado de **Entity type key**, selecione **Define entity type key**.

    ![](./media/image151.png)

3.  Selecione **LocationId** e, em seguida, **Save**.

    ![](./media/image152.png)

4.  Verifique se **Entity type key** exibe **LocationId**.

    ![](./media/image153.png)

5.  Repita o procedimento para cada um dos outros tipos de entidade. Por exemplo, para **Store**, selecione **LocationId**.

    ![](./media/image154.png)

    | **Tipo de entidade**                        | **Chave**                        |
    |---------------------------------------------|----------------------------------|
    | Location, Store, Distribution Center        | LocationId                       |
    | Product, Frozen Product, Perishable Product | ProductId                        |
    | Inventory                                   | a coluna de ID da posição de estoque |
    | Supplier                                    | SupplierId                       |
    | Shipment                                    | a coluna de ID da remessa |
    | Refrigeration Unit                          | UnitId                           |

    **Observação:** Ignore **Sale** e **Refrigeration Telemetry**; eles não são incluídos no grafo deste laboratório.

### Tarefa 3: Materializar o grafo

**Importante:** A materialização de toda a ontologia pode levar mais de duas horas para ser concluída. Esse é um problema temporário. Para evitar tempos de processamento prolongados, materialize apenas os componentes necessários.

1.  Selecione **Home** e, em seguida, selecione **Manage graph** na faixa de opções.

    ![](./media/image155.png)

2.  Na página **Configure Graph**, **Use the entire Ontology** está definido como **Yes**. Examine as entidades.

    ![](./media/image156.png)

3.  Verifique se todos os tipos de entidade estão selecionados, exceto **Sale** e **Refrigeration_Telemetry** (exibidos em cinza na pré-visualização) e, em seguida, selecione **Continue**.

    ![](./media/image157.png)

    ![](./media/image158.png)

4.  Na página **Projection summary**, selecione **Materialize**.

    ![](./media/image159.png)

5.  Uma notificação com a mensagem **Creating graph model** será exibida. Aguarde até que o modelo de grafo seja criado. Isso pode levar alguns minutos.

    ![](./media/image160.png)

    ![](./media/image161.png)

    **Importante:** Materializar a ontologia **inteira** pode levar **mais de uma hora**. Para concluir o laboratório dentro do prazo, crie o grafo com apenas **três tipos de entidade**, conforme descrito na próxima etapa. Um grafo menor será criado em poucos minutos e será suficiente para a exploração neste laboratório.

6.  Defina **Use the entire Ontology** como **No**. Na lista **Entities**, selecione apenas estes três tipos de entidade e desmarque todos os demais: **Store**, **Product** e **Shipment**. Verifique se somente esses três tipos estão destacados em azul em **Preview** e, em seguida, selecione **Continue**.

    ![](./media/image162.png)

    **Observação:** A captura de tela mostra como fazer a seleção. Certifique-se de que **Shipment** esteja selecionado, pois a próxima tarefa utiliza os relacionamentos entre **Shipment**, **Store** e **Product**. Os relacionamentos são incluídos somente quando os dois tipos de entidade correspondentes estão selecionados.

    **Dica:** Se você tiver mais tempo, poderá manter **Use the entire Ontology** definido como **Yes** para materializar todos os tipos de entidade, exceto **Sale** e **Refrigeration_Telemetry** (exibidos em cinza). Esse processo pode levar mais de uma hora.

### Tarefa 4: Explorar o grafo

1.  Na faixa de opções, selecione **Explore graph**. Esse botão fica disponível depois que o grafo for materializado.

    ![](./media/image163.png)

2.  A consulta do grafo será aberta. No lado direito, selecione o ícone de peça de quebra-cabeça para expandir o painel **Components**.

    ![](./media/image164.png)

    ![](./media/image165.png)

3.  O painel **Components** lista os **Nodes** e **Edges** disponíveis no grafo.

    ![](./media/image166.png)

4.  Em **Nodes**, selecione **Store**, **Product** e **Shipment**. Em **Edges**, selecione **Shipment_Store** e **Shipment_Product**. Os nós e as arestas serão adicionados à tela de edição.

    ![](./media/image167.png)

5.  Na faixa de opções, selecione **Path query** e confirme **Switch** quando solicitado. Insira os seguintes detalhes para localizar os caminhos com até dois saltos entre a loja de Seattle e o fornecedor Cascade Fresh Products. Em seguida, selecione **Run**:

    - **Start node**: **Store**

    - **End node**: **Supplier**

    - **Filter start node**: **City** = +++Seattle+++

    - **Filter end node**: **SupplierName** = +++Cascade Fresh Products+++

    - **Max hops**: **2**

    ![](./media/image168.png)

6.  Revise o resultado na tela de consulta do grafo e no painel **Results**.

    ![](./media/image169.png)

## Exercício 6: Fazer perguntas ao agente de ontologia

O **agente de ontologia (pré-visualização)** é um Copilot com tecnologia de IA que ajuda você a criar e utilizar ontologias por meio de linguagem natural. Ele raciocina com base na ontologia, incluindo seus metadados e regras, e consulta as fontes de dados vinculadas para responder às perguntas.

### Tarefa 1: Fazer perguntas em linguagem natural

1.  Selecione **Home** e, na faixa de opções, selecione **Ontology agent**.

    ![](./media/image170.png)

2.  O agente será aberto no modo **Plan** em um painel à direita.

    ![](./media/image171.png)

3.  Insira +++Which frozen products are stocked at Tier 1 stores in the West?+++ e selecione **Send**.

    ![](./media/image172.png)

4.  O agente levará alguns instantes para processar a solicitação e, em seguida, responderá com base na sua ontologia. Expanda **Reasoning** para ver como ele chegou à resposta.

    ![](./media/image173.png)

    ![](./media/image174.png)

5.  Insira +++Which of those inventory positions are below safety stock?+++ e selecione **Send**.

    ![](./media/image175.png)

6.  Revise a resposta, que lista as posições de estoque com suas respectivas quantidades disponíveis, estoque de segurança e déficit.

    ![](./media/image176.png)

7.  (Opcional) Explore mais a ontologia com estas perguntas ou faça suas próprias perguntas:

    - +++Which affected stores had a qualifying refrigeration-temperature exception?+++

    - +++What inbound shipments are expected for the affected products and stores?+++

    - +++What is the on-shelf availability of frozen products for each store?+++

    - +++Which of these stores have low on-shelf availability?+++

    - +++Which of these stores have declining gross margin?+++

### Tarefa 2: Responder à pergunta do cenário

1.  Insira a pergunta principal do cenário da Lakeshore Retail e selecione **Send**:

    +++Which high-priority stores in the West have frozen products below safety stock, a recent refrigeration-temperature exception, and on-shelf availability below target? For each store, show the next inbound shipment and its expected arrival.+++

    ![](./media/image177.png)

2.  Revise a resposta. O agente combina dados de estoque, telemetria de refrigeração, lojas e remessas por meio da ontologia, aplica as regras de negócios e os metadados (por exemplo, a meta de disponibilidade nas prateleiras) e lista as lojas que atendem aos critérios, juntamente com suas próximas remessas de entrada.

    ![](./media/image178.png)

    ![](./media/image179.png)

    **Observação:** As respostas podem variar entre as execuções, pois o agente decide como interpretar termos como *recent* e *target*. O agente informa as premissas utilizadas na resposta. Verifique as respostas importantes com base nos dados de origem.

## Exercício 7: Preparar o ambiente

### Tarefa 1: Verificar o Node.js e criar a pasta do laboratório

1.  Se o Node.js não estiver instalado, instale a versão **LTS** (20 ou posterior) de +++https://nodejs.org+++.

2.  Na caixa de Pesquisa do Windows, digite +++Visual Studio Code+++ e, em seguida, selecione **Visual Studio Code**.

    ![](./media/image180.png)

3.  No VS Code, selecione o menu **More Actions (...)**, selecione **Terminal** e, em seguida, selecione **New Terminal**.

    ![](./media/image181.png)

4.  Execute os seguintes comandos para verificar as versões:

    +++node --version+++

    +++npm --version+++

5.  Verifique se a versão do Node.js é **v20.x** ou posterior.

6.  Crie a pasta do laboratório e acesse-a:

    +++mkdir C:\LabFiles\FabricApps+++

    +++cd C:\LabFiles\FabricApps+++

    ![](./media/image182.png)

    **Observação:** Mantenha este terminal aberto. Você o utilizará no Exercício 8.

### Tarefa 2: Criar um item Fabric App

1.  No workspace, selecione **+ New item**.

    ![](./media/image183.png)

2.  No painel **New item**, insira +++app+++ na caixa de pesquisa e, em seguida, selecione **App (preview)**.

    ![](./media/image184.png)

    **Observação:** Se **App (preview)** não estiver listado, peça ao administrador para habilitar a configuração de locatário **Fabric apps (preview)** e, em seguida, aguarde alguns minutos.

3.  Na caixa de diálogo **New App**, insira +++To do_App+++ como **Name**, mantenha seu workspace como **Location** e selecione **Create**.

    ![](./media/image185.png)

4.  O item App será aberto e implantará seu modelo. Aguarde até que a implantação seja concluída. Isso leva alguns minutos.

    ![](./media/image186.png)

5.  Na página **Overview**, em **Getting started**, localize a etapa **2 Set up your project**. Selecione o ícone **Copy** para copiar o comando de estruturação do projeto e salve-o no Bloco de Notas do Windows.

    ![](./media/image187.png)

6.  Revise as outras etapas: **Editar o aplicativo** (**cd \<your-project-directory\>** e **npm run dev**) e **Publicar suas alterações** (**npx rayfin up**). Você executará esses comandos nos próximos exercícios.

    ![](./media/image188.png)

    **Importante:** Use o comando exatamente como foi copiado do portal. Ele contém o nome do seu aplicativo, o nome do modelo e o nome do seu workspace, por exemplo: **npm create @microsoft/rayfin@latest -- "To do_App" --template blankapp --workspace "Rayfin-Fabric-Todoapp56478"**.

## Exercício 8: Criar a estrutura inicial e explorar o projeto

### Tarefa 1: Criar a estrutura inicial do projeto

1.  No terminal do VS Code, certifique-se de que você está em **C:\LabFiles\FabricApps** e, em seguida, cole e execute o comando que você copiou do portal.

    ![](./media/image189.png)

2.  Se o npm exibir a mensagem **Need to install the following packages: @microsoft/create-rayfin ... Ok to proceed? (y)**, digite **y** e pressione **Enter**.

    ![](./media/image190.png)

3.  Se um navegador for aberto, entre com sua conta do laboratório. Aguarde até que a CLI exiba **Project created successfully!**

    ![](./media/image191.png)

4.  Acesse a pasta do projeto e instale as dependências:

    +++cd to-do-app+++

    +++npm install+++

    ![](./media/image192.png)

    **Observação:** Avisos sobre vulnerabilidades moderadas ou scripts de instalação são esperados neste exemplo. Não é necessário executar **npm audit fix**.

5.  No VS Code, selecione **File \> Open Folder**, abra **C:\LabFiles\FabricApps\to-do-app** e examine o projeto no **Explorer**.

    ![](./media/image193.png)

6.  Selecione **More Actions (...) \> Terminal \> New Terminal** para abrir um terminal na pasta **to-do-app**.

    ![](./media/image194.png)

7.  Se aparecer a pergunta **Do you trust the authors of the files in this folder?**, selecione **Trust Folder & Continue**.

    ![](./media/image195.png)

    **Observação:** Execute todos os comandos restantes neste terminal, a partir da pasta **to-do-app**.

### Tarefa 2: Entender a estrutura do projeto

```text
to-do-app/
├── .agents/                 # example kits for AI agents (reference only)
├── packages/
│   ├── data/src/index.ts    # data schema registration (empty at start)
│   ├── shared/src/index.ts  # UniversalAppSchema record contracts
│   └── frontend/src/        # React app: Welcome.tsx, hooks/, lib/
├── rayfin/
│   ├── rayfin.yml           # backend configuration
│   ├── .env                 # deployment values (do not share)
│   └── .deployments.json    # deployment history
├── scripts/
└── package.json
```

| **Arquivo**                                | **O que ele faz**                                                                                                                    |
|--------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| rayfin/rayfin.yml                          | Configura os serviços. **data** inicia com **enabled: false**; a **autenticação** usa o Microsoft Entra (**fabric: enabled: true**). |
| packages/data/src/index.ts                 | Exporta **schema = \[\]**. Toda entidade deve ser registrada aqui.                                                                   |
| packages/shared/src/index.ts               | Define **UniversalAppSchema**, que tipifica **client.data.\<Entity\>** no front-end.                                                 |
| packages/frontend/src/Welcome.tsx          | A página inicial do aplicativo ("Your app is taking shape"). Você a substitui no Exercício 12.                                        |
| packages/frontend/src/lib/rayfin-client.ts | Exporta **getRayfinClient()**, que retorna o cliente de dados tipado.                                                                |
| .agents/skills/data-modeling/kit/          | Exemplos de referência (Item.ts, schema.ts). Leia esses arquivos, mas não os edite.                                                  |

**Observação:** Tanto **packages/data/src** quanto **packages/shared/src** contêm um arquivo chamado **index.ts**. Antes de editar qualquer um deles, verifique a **trilha de navegação** na parte superior do editor do VS Code para garantir que o arquivo certo esteja aberto.

## Exercício 9: Implantar o aplicativo inicial

*Tempo estimado: 10 minutos*

1.  Entre no Fabric pela CLI (ignore esta etapa se você já tiver entrado durante a criação da estrutura inicial):

    +++npx rayfin login+++

    ![](./media/image196.png)

2.  No navegador da web, selecione sua conta do laboratório.

    ![](./media/image197.png)

3.  Verifique se o terminal exibe **Signed in successfully**.

    ![](./media/image198.png)

4.  Pré-visualize a implantação sem fazer nenhuma alteração:

    +++npx rayfin up --dry-run+++

    ![](./media/image199.png)

5.  Examine as **Planned operations**, por exemplo, **Create or reuse Rayfin item "to-do-app" (AppBackend)** e **POST runtime settings (auth=true, data=false)**.

6.  Implante o aplicativo inicial:

    +++npx rayfin up+++

7.  Aguarde até que o terminal exiba **Project "to-do-app" is now deployed to Fabric!** e **Your app is live at: https://\<your-app\>.webapp.fabricapps.net**. Copie a URL para o Bloco de notas do Windows.

    ![](./media/image200.png)

8.  Verifique o status da implantação:

    +++npx rayfin up status+++

    ![](./media/image201.png)

9.  Abra **rayfin/.env**. Agora ele contém valores como **RAYFIN_PUBLIC_ITEM_ID** e **RAYFIN_PUBLIC_WORKSPACE_ID**, o que demonstra que a implantação foi concluída com sucesso.

10. Selecione a URL de hospedagem no terminal (**Ctrl + click**). Se o VS Code perguntar **Do you want Code to open the external website?**, selecione **Open**.

    ![](./media/image202.png)

11. Na página **Sign in to continue**, selecione **Sign in** e entre com sua conta do laboratório.

    ![](./media/image203.png)

12. Verifique se a página inicial do aplicativo, **Your app is taking shape**, é exibida.

    ![](./media/image204.png)

    **Observação:** A URL de hospedagem também é adicionada a **rayfin/rayfin.yml** na seção **allowedRedirectUris** (ela termina em **.webapp.fabricapps.net**).

## Exercício 10: Executar o aplicativo localmente

*Tempo estimado: 5 minutos*

1.  Inicie o servidor de desenvolvimento:

    +++npm run dev+++

    ![](./media/image205.png)

2.  Mantenha **Ctrl** pressionado e selecione **http://localhost:5173/** no terminal. Entre, se solicitado.

    ![](./media/image206.png)

3.  Verifique se a página inicial Welcome é exibida.

    ![](./media/image207.png)

4.  No terminal, pressione **Ctrl + C** para interromper o servidor. Se aparecer a pergunta **Terminate batch job (Y/N)?**, digite **Y**.

    **Observação:** Você pode ignorar as mensagens **DeprecationWarning** e **\[vite:react-swc\] We recommend switching...**. Elas não causam problemas.

## Exercício 11: Adicionar o modelo de dados Todo

*Tempo estimado: 25 minutos*

Para adicionar dados a um aplicativo Rayfin, você habilita o serviço de dados, declara a entidade como uma classe TypeScript decorada, exporta essa classe, adiciona-a ao **UniversalAppSchema** e registra-a em **schema**. Toda entidade também precisa de controles de acesso explícitos.

### Tarefa 1: Habilitar o serviço de dados em rayfin.yml

1.  No **Explorer**, abra **rayfin \> rayfin.yml**.

2.  Altere o bloco **data:** para que fique exatamente como mostrado a seguir. Defina **enabled** como **true** e **adicione** a linha **dialect: mssql**:

    ```yaml
      data:
        enabled: true
        dialect: mssql
        path: packages/data
        buildCommand: npm run build
    ```

    ![](./media/image208.png)

3.  Verifique a indentação: **data:** deve ficar alinhado com **auth:** e **staticHosting:** (com dois espaços de indentação), e as quatro linhas abaixo dele devem ter quatro espaços de indentação. Use espaços, não tabulações.

4.  Salve o arquivo (**Ctrl + S**).

    Para referência, o arquivo completo deve ser semelhante ao exemplo a seguir. Sua URL de hospedagem e quaisquer valores adicionados pela CLI serão diferentes:

    ```yaml
    id: to-do-app
    name: To do_App
    version: 1.0.0
    services:
      auth:
        enabled: true
        fabric:
          enabled: true
          externalEntraExchange: true
        password:
          enabled: false
        allowedRedirectUris:
          - http://localhost:5173
          - http://127.0.0.1:5173
          - https://<your-app>.webapp.fabricapps.net
      data:
        enabled: true
        dialect: mssql
        path: packages/data
        buildCommand: npm run build
      staticHosting:
        enabled: true
        path: packages/frontend
        folder: dist
        buildCommand: npm run build:fabric
        indexDocument: index.html
        assetAccess: protected
        embedded:
          only: false
      storage:
        enabled: false
      functions:
        enabled: false
    ```

    **Importante:** Sem **dialect: mssql**, a implantação falhará com a mensagem **Dialect is required when Data module is enabled**. Se **data:** estiver muito indentado, ocorrerá o erro **Map keys must be unique**.

### Tarefa 2: Criar a entidade Todo

1.  No **Explorer**, expanda **packages \> data**, clique com o botão direito do mouse em **src**, selecione **New File...** e nomeie o arquivo como +++Todo.ts+++.

    ![](./media/image209.png)

    ![](./media/image210.png)

2.  Cole o código a seguir e salve o arquivo:

    ```typescript
    import { entity, role, uuid, text, boolean, date } from '@microsoft/rayfin-core';

    /**
     * A to-do item. Each signed-in user sees only their own items.
     */
    @entity()
    @role('authenticated', '*', {
      policy: (claims, item) => claims.sub.eq(item.owner_id),
    })
    export class Todo {
      @uuid() id!: string;
      @text({ min: 1, max: 200 }) title!: string;
      @text({ optional: true, max: 2000 }) notes?: string;
      @boolean({ default: false }) done!: boolean;
      @date() createdAt!: Date;
      @text({ max: 200 }) owner_id!: string;
    }
    ```

    ![](./media/image211.png)

    **Observação:** a captura de tela mostra uma versão anterior da classe. Use o código acima.

3.  Revise a função de cada parte:

    | **Parte**                                | **Significado**                                                                                                                            |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
    | @entity()                                | Transforma a classe em uma tabela de banco de dados                                                                                        |
    | @role('authenticated', '\*', { policy }) | Somente usuários autenticados podem acessar a tabela, e apenas as linhas em que **owner_id** corresponde ao ID do usuário (**claims.sub**) |
    | max em cada @text                        | Obrigatório. Sem esse parâmetro, a coluna se torna NVARCHAR(MAX) e a API falha com o erro **Internal server error**.                       |
    | { optional: true }                       | Permite que a coluna aceite valores nulos. O **?** do TypeScript, por si só, não faz isso.                                                 |

### Tarefa 3: Registrar a entidade no pacote de dados

1.  Abra **packages \> data \> src \> index.ts**. Verifique se a trilha de navegação mostra **packages \> data \> src \> index.ts**.

2.  Substitua o arquivo inteiro pelo código a seguir e salve-o:

    ```typescript
    //----------------------------------------------------------------------
    // <copyright company="Microsoft Corporation">
    //   Copyright (c) Microsoft Corporation. All rights reserved.
    //   Licensed under the MIT license.
    // </copyright>
    //----------------------------------------------------------------------

    /**
     * The app's Rayfin data schema registration.
     */
    import { Todo } from './Todo.js';

    export type { UniversalAppSchema } from '@rayfin-app/shared';
    export { Todo };

    export const schema = [Todo];
    ```

    ![](./media/image212.png)

    **Observação:** Mantenha a extensão **.js** em **./Todo.js**, embora o nome do arquivo seja **Todo.ts**. O projeto usa módulos ES e precisa dessa extensão.

### Tarefa 4: Adicionar o contrato de registro ao pacote compartilhado

1.  Expanda **packages \> shared \> src** e abra **index.ts**. Verifique se a trilha de navegação mostra **packages \> shared \> src \> index.ts**.

2.  Substitua todo o conteúdo do arquivo pelo código a seguir e salve-o:

    ```typescript
    /**
     * Entity map shared by the data registration package and typed browser client.
     *
     * Every entity registered in `packages/data/src/index.ts` (the `schema` array)
     * must have a matching record contract here, so the frontend client
     * (`(await getRayfinClient()).data.Todo`) is fully typed.
     */

    /**
     * Record contract for the Todo entity.
     * Keep these fields in step with `packages/data/src/Todo.ts`.
     */
    export interface TodoRecord {
      id: string;
      title: string;
      notes?: string;
      done: boolean;
      createdAt: Date;
      owner_id: string;
    }

    /**
     * Map of entity name -> record type used by the Rayfin client.
     */
    export type UniversalAppSchema = {
      Todo: TodoRecord;
    };
    ```

    ![](./media/image213.png)

3.  Selecione **File \> Save All**. Nenhuma guia do editor deve exibir um ponto branco (indicador de alteração não salva).

### Tarefa 5: Compilar os pacotes

1.  Execute os seguintes comandos:

    +++npm run build -w @rayfin-app/shared+++

    +++npm run build -w @rayfin-app/data+++

    ![](./media/image214.png)

2.  Verifique se os dois comandos são concluídos com **tsc -b** e sem erros. Uma pasta **dist** será criada em cada pacote.

    ![](./media/image215.png)

### Tarefa 6: Implantar a tabela Todo no Fabric

1.  Pré-visualize a implantação:

    +++npx rayfin up --dry-run+++

    ![](./media/image216.png)

2.  Verifique se as operações planejadas incluem **POST runtime settings (auth=true, data=true)** e **Generate and apply DAB configuration**.

3.  Faça a implantação:

    +++npx rayfin up+++

4.  Confirme a implantação:

    +++npx rayfin up status+++

    **Observação:** DAB é o construtor de API de dados. O Rayfin usa esse recurso para gerar a API de dados das suas entidades.

## Exercício 12: Criar a tela de tarefas

*Tempo estimado: 20 minutos*

Neste exercício, você substitui a página inicial Welcome por um rastreador de tarefas que lista, adiciona, conclui e exclui itens de tarefa. O novo componente mantém o nome **Welcome**, para que o restante do aplicativo (**App.tsx**, **Root.tsx**) continue funcionando sem alterações.

### Tarefa 1: Fazer backup da página inicial

1.  No terminal, execute:

    +++Copy-Item packages\frontend\src\Welcome.tsx packages\frontend\Welcome.backup.tsx+++

    ![](./media/image217.png)

### Tarefa 2: Substituir Welcome.tsx

1.  Abra **packages \> frontend \> src \> Welcome.tsx**.

2.  Selecione todo o conteúdo (**Ctrl + A**), cole o código a seguir e salve o arquivo:

    ```tsx
    import { useEffect, useState } from 'react';
    import { getRayfinClient } from './lib/rayfin-client';
    import { useAuth } from './hooks/auth.context';

    type Todo = {
      id: string;
      title: string;
      notes?: string;
      done: boolean;
      createdAt: string | Date;
      owner_id: string;
    };

    /** Reads the signed-in user's id from the session. */
    function getUserId(session: any): string | undefined {
      return session?.user?.id ?? session?.user?.sub ?? session?.claims?.sub ?? session?.sub ?? session?.userId;
    }

    function toList(result: any): Todo[] {
      if (Array.isArray(result)) return result;
      return result?.items ?? result?.data ?? [];
    }

    export function Welcome() {
      const { session } = useAuth() as any;
      const userId = getUserId(session);
      const [todos, setTodos] = useState<Todo[]>([]);
      const [title, setTitle] = useState('');
      const [loading, setLoading] = useState(true);
      const [error, setError] = useState<string | null>(null);

      async function loadTodos() {
        try {
          setLoading(true);
          setError(null);
          const client: any = await getRayfinClient();
          const result = await client.data.Todo.select(['id', 'title', 'notes', 'done', 'createdAt', 'owner_id'])
            .orderBy({ createdAt: 'desc' })
            .execute();
          setTodos(toList(result));
        } catch (e: any) {
          console.error('Load failed', e);
          setError(e?.message ?? 'Could not load to-do items.');
        } finally {
          setLoading(false);
        }
      }

      useEffect(() => {
        loadTodos();
      }, []);

      async function addTodo(e: React.FormEvent) {
        e.preventDefault();
        const text = title.trim();
        if (!text) return;
        if (!userId) {
          console.log('Session object:', session);
          setError('Signed-in user id not found. Open F12 > Console and check the "Session object".');
          return;
        }
        try {
          const client: any = await getRayfinClient();
          await client.data.Todo.create({
            title: text,
            done: false,
            createdAt: new Date(),
            owner_id: userId,
          });
          setTitle('');
          await loadTodos();
        } catch (e: any) {
          console.error('Create failed', e);
          setError(e?.message ?? 'Could not add the item.');
        }
      }

      async function toggleDone(todo: Todo) {
        try {
          const client: any = await getRayfinClient();
          await client.data.Todo.update({ id: todo.id }, { done: !todo.done });
          await loadTodos();
        } catch (e: any) {
          console.error('Update failed', e);
          setError(e?.message ?? 'Could not update the item.');
        }
      }

      async function deleteTodo(todo: Todo) {
        try {
          const client: any = await getRayfinClient();
          await client.data.Todo.delete({ id: todo.id });
          await loadTodos();
        } catch (e: any) {
          console.error('Delete failed', e);
          setError(e?.message ?? 'Could not delete the item.');
        }
      }

      const remaining = todos.filter((t) => !t.done).length;

      return (
        <main style={styles.page}>
          <section style={styles.card}>
            <h1 style={styles.h1}>Zava Training Team Tracker</h1>
            <p style={styles.sub}>
              Zava Training Team &middot; {remaining} of {todos.length} open
            </p>
            <form onSubmit={addTodo} style={styles.form}>
              <input
                value={title}
                onChange={(e) => setTitle(e.target.value)}
                placeholder="What needs to be done?"
                maxLength={200}
                style={styles.input}
              />
              <button type="submit" style={styles.addBtn}>
                Add
              </button>
            </form>
            {error && <div style={styles.error}>{error}</div>}
            {loading ? (
              <p style={styles.muted}>Loading...</p>
            ) : todos.length === 0 ? (
              <p style={styles.muted}>No items yet. Add your first task above.</p>
            ) : (
              <ul style={styles.list}>
                {todos.map((t) => (
                  <li key={t.id} style={styles.item}>
                    <label style={styles.label}>
                      <input type="checkbox" checked={t.done} onChange={() => toggleDone(t)} />
                      <span style={t.done ? styles.doneText : undefined}>{t.title}</span>
                    </label>
                    <button onClick={() => deleteTodo(t)} style={styles.delBtn}>
                      Delete
                    </button>
                  </li>
                ))}
              </ul>
            )}
          </section>
        </main>
      );
    }

    export default Welcome;

    const styles: Record<string, React.CSSProperties> = {
      page: {
        minHeight: '100vh',
        display: 'flex',
        justifyContent: 'center',
        padding: '48px 16px',
        background: '#f5f7fa',
        fontFamily: 'Segoe UI, sans-serif',
      },
      card: {
        width: '100%',
        maxWidth: 560,
        background: '#fff',
        borderRadius: 12,
        padding: 28,
        boxShadow: '0 2px 12px rgba(0,0,0,0.08)',
        height: 'fit-content',
      },
      h1: { margin: 0, fontSize: 26, color: '#0b5394' },
      sub: { marginTop: 6, color: '#666' },
      form: { display: 'flex', gap: 8, margin: '20px 0' },
      input: { flex: 1, padding: '10px 12px', fontSize: 15, border: '1px solid #ccc', borderRadius: 8 },
      addBtn: {
        padding: '10px 18px',
        background: '#0b5394',
        color: '#fff',
        border: 'none',
        borderRadius: 8,
        cursor: 'pointer',
      },
      error: { background: '#fdecea', color: '#a00', padding: 10, borderRadius: 8, marginBottom: 12 },
      muted: { color: '#888' },
      list: { listStyle: 'none', padding: 0, margin: 0 },
      item: {
        display: 'flex',
        justifyContent: 'space-between',
        alignItems: 'center',
        padding: '10px 4px',
        borderBottom: '1px solid #eee',
      },
      label: { display: 'flex', gap: 10, alignItems: 'center', cursor: 'pointer' },
      doneText: { textDecoration: 'line-through', color: '#999' },
      delBtn: {
        background: 'transparent',
        border: '1px solid #ddd',
        borderRadius: 6,
        padding: '4px 10px',
        cursor: 'pointer',
        color: '#a00',
      },
    };
    ```

    ![](./media/image218.png)

3.  Revise como o código funciona:

    | **Código**                              | **Finalidade**                                                                                                                                  |
    |-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
    | getRayfinClient()                       | Retorna o cliente Rayfin tipado. **client.data.Todo** é sua tabela.                                                                             |
    | useAuth()                               | Fornece a sessão do usuário autenticado. O ID do usuário é armazenado em **owner_id**, permitindo que a política de acesso autorize a gravação. |
    | .select(\[...\]).orderBy(...).execute() | Lê as tarefas do usuário, começando pelas mais recentes.                                                                                        |
    | .create({...})                          | Adiciona uma tarefa                                                                                                                             |
    | .update({ id }, { done })               | Marca uma tarefa como concluída ou não concluída                                                                                                |
    | .delete({ id })                         | Exclui uma tarefa                                                                                                                               |

    **Dica:** Se **useAuth** estiver sublinhado em vermelho, localize o arquivo que exporta o hook executando: +++Select-String -Path packages\frontend\src\hooks\*.ts, packages\frontend\src\hooks\*.tsx -Pattern "^export"+++. Em seguida, altere a linha 3 para importar o hook desse arquivo, por exemplo, **./hooks/use-auth**.

### Tarefa 3: Testar o aplicativo localmente

1.  Execute +++npm run dev+++ e abra **http://localhost:5173**.

    ![](./media/image219.png)

2.  Verifique se a página exibe **Zava Training Team Tracker** com a mensagem **No items yet. Add your first task above.** Se nenhuma caixa de erro vermelha aparecer, o aplicativo está conectado à tabela Todo.

    ![](./media/image220.png)

3.  Insira +++Complete Fabric Apps lab setup+++ e selecione **Add**.

    ![](./media/image221.png)

4.  Adicione +++Review Rayfin project structure+++.

    ![](./media/image222.png)

5.  Selecione a caixa de seleção ao lado de **Complete Fabric Apps lab setup**.

    ![](./media/image223.png)

6.  Verifique se a tarefa está riscada e se o contador mostra **1 of 2 open**.

    ![](./media/image224.png)

7.  Pressione **F5** para recarregar a página. As tarefas continuarão aparecendo, pois estão armazenadas no Fabric.

8.  No terminal, pressione **Ctrl + C** para interromper o servidor.

## Exercício 13: Publicar e verificar no Fabric

### Tarefa 1: Publicar o aplicativo

1.  Publique o front-end e o back-end:

    +++npx rayfin up+++

2.  Verifique se a saída termina com a mensagem **Your app is live at: https://\<your-app\>.webapp.fabricapps.net**.

    ![](./media/image225.png)

3.  Abra a URL de hospedagem no navegador da web e entre com sua conta do laboratório, se solicitado. Verifique se **Zava Training Team Tracker** é exibido com as suas tarefas.

    ![](./media/image226.png)

    ![](./media/image227.png)

    **Observação:** O aplicativo local e o aplicativo hospedado usam o mesmo back-end do Fabric, portanto exibem os mesmos dados.

4.  Adicione +++Publish app to Fabric workspace+++ e selecione **Add**. O contador deverá mostrar **2 of 3 open**.

    ![](./media/image228.png)

    ![](./media/image229.png)

### Tarefa 2: Verificar o aplicativo no portal do Fabric

1.  No portal do Fabric, abra seu workspace **Rayfin-Fabric-Todoapp\<number\>**. Observe o item de aplicativo **to-do-app** e seu **SQL database** e **SQL analytics endpoint**, que foram criados por **npx rayfin up**. Selecione **to-do-app**.

    ![](./media/image230.png)

    **Observação:** O workspace também contém o item **To do_App** que você criou no portal no Exercício 7. A CLI implanta o projeto em seu próprio item, **to-do-app**.

2.  O aplicativo será aberto dentro do Fabric com suas tarefas.

    ![](./media/image231.png)

3.  Marque a caixa de seleção ao lado de **Review Rayfin project structure**.

    ![](./media/image232.png)

4.  Selecione **Delete** ao lado de **Complete Fabric Apps lab setup**.

    ![](./media/image233.png)

5.  Insira +++Explore the app+++ e selecione **Add**.

    ![](./media/image234.png)

6.  Verifique o resultado: três tarefas, com o contador mostrando **2 of 3 open**.

    ![](./media/image235.png)

7.  No portal do Fabric, selecione o workspace *Rayfin-Fabric-TodoappXXXXX* no painel de navegação.

    ![](./media/image236.png)

8.  Selecione a opção **...** abaixo do nome do workspace e selecione **Workspace settings**.

    ![](./media/image237.png)

9.  Vá até a parte inferior da guia **General** e selecione **Remove this workspace**.

    ![](./media/image238.png)

    ![](./media/image239.png)

    ![](./media/image240.png)

## Comandos úteis de implantação

| **Comando**                    | **Finalidade**                                              |
|--------------------------------|-------------------------------------------------------------|
| npx rayfin up                  | Implantar tudo: configurações, banco de dados e front-end |
| npx rayfin up --dry-run        | Pré-visualizar as alterações sem aplicá-las                 |
| npx rayfin up db apply         | Aplicar somente as alterações de esquema                    |
| npx rayfin up staticapp deploy | Implantar somente o front-end                             |
| npx rayfin up status           | Exibe o estado da implantação.                            |
| npx rayfin login               | Faz login novamente (corrige erros 401 e 403).              |

## Solução de problemas

| **Sintoma**                                               | **Causa e conserto**                                                                                                                                 |
|-----------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| Pasta rayfin/data ausente                                 | Este modelo mantém as entidades em **packages/data/src**. Siga as instruções do Exercício 11.                                                         |
| Dialect is required when Data module is enabled           | Adicione **dialect: mssql** em **services.data** em **rayfin.yml**.                                                                                  |
| Map keys must be unique at line 16 ... data:              | **data:** está indentado dentro de **auth:**. Mova-o de volta para que fique alinhado com **auth:** (com dois espaços de indentação).                |
| Sublinhado amarelo ou vermelho em import { Todo }         | Você editou o arquivo **index.ts** errado. Verifique a trilha de navegação: o contrato compartilhado deve estar em **packages/shared/src/index.ts**. |
| Ponto branco em uma guia do editor                        | O arquivo não está salvo. Selecione **File \> Save All**.                                                                                            |
| useAuth sublinhado em vermelho                            | Importe-o do arquivo que o exporta (consulte a dica no Exercício 12).                                                                                 |
| Signed-in user id not found                               | Pressione **F12 \> Console**, examine o **Session object** registrado e atualize **getUserId()** para usar o campo que contém a ID do usuário.       |
| Internal server error após uma implantação bem-sucedida | Está faltando **max** em um campo **@text**. Adicione um limite e execute **npx rayfin up** novamente.                                               |
| 401 ou 403 da CLI                                         | Execute **npx rayfin login** e, em seguida, execute **npx rayfin up** novamente.                                                                     |
| Mensagem DeprecationWarning ou react-swc                  | Não causa problemas. Você pode ignorá-la.                                                                                                            |
| Item App ausente em **New item**                          | Peça ao administrador para habilitar **Fabric apps (preview)** e, em seguida, aguarde alguns minutos.                                                |

## Limpar recursos do aplicativo

1.  No portal do Fabric, abra o workspace, selecione **...** ao lado de **to-do-app** e **To do_App** e selecione **Delete**.

2.  Se você criou o workspace exclusivamente para este laboratório, remova-o: selecione **Workspace settings \> General \> Remove this workspace**.

3.  Se você não precisar mais do projeto local, exclua a pasta **C:\LabFiles\FabricApps\to-do-app**.

## Exercício 14: Limpar recursos

1.  Na barra de navegação à esquerda, selecione seu workspace **Fabric IQ Ontology\<number\>** e, em seguida, selecione **Workspace settings**.

    ![](./media/image241.png)

2.  Na guia **General**, role até **Delete workspace** e, em seguida, selecione **Remove this workspace**. Confirme a exclusão.

    ![](./media/image242.png)

3.  Verifique se a notificação **Workspace deleted** foi exibida.

    ![](./media/image243.png)

**Resumo**

Neste laboratório, você usou o **Microsoft Fabric IQ Ontologia (pré-visualização)** para criar um modelo conectado e alinhado às necessidades de negócios das operações da Lakeshore Retail. Você preparou um lakehouse com dados estáticos, um eventhouse com telemetria de refrigeração e um modelo semântico do Power BI com dados de vendas. Você criou a **RetailSalesOntology** com tipos de entidade e herança (Location \> Store e Distribution Center, Product \> Frozen e Perishable Product), vinculou esses elementos às três fontes de dados e usou o agente de ontologia para adicionar um tipo de entidade **Sale** a partir do modelo semântico. Você conectou os tipos de entidade por meio de tipos de relacionamento, enriqueceu o modelo com metadados de entidades, propriedades e relacionamentos, além de regras de negócios, e explorou o modelo por meio de exibições de tela, instâncias, um grafo materializado e consultas de caminho, e, por fim, usou o **Agente de Ontologia** para responder, em linguagem natural, à pergunta de um cenário que envolvia vários domínios, sem escrever uma única junção. Isso demonstra como uma ontologia conecta dados operacionais e analíticos e proporciona às pessoas e aos agentes de IA uma compreensão compartilhada dos negócios.
