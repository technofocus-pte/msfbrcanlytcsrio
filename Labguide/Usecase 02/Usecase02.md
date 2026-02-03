# Caso de uso 02: Solução Data Factory para movimentação e transformação de dados com fluxos de dados e pipelines de dados

**Introdução**

Este laboratório ajuda você a acelerar o processo de avaliação do Data
Factory no Microsoft Fabric, fornecendo um guia passo a passo para um
cenário completo de integração de dados em apenas uma hora. Ao final
deste tutorial, você compreenderá o valor e os principais recursos do
Data Factory e saberá como concluir um cenário comum de integração de
dados completo.

**Objetivo**

O laboratório está dividido em três seções de exercícios:

- **Exercício 1:** Criar um pipeline com o Data Factory para ingerir
  dados brutos de um Blob storage para uma tabela bronze em um Lakehouse
  de dados.

- **Exercício 2:** Transformar os dados com um fluxo de dados no Data
  Factory para processar os dados brutos da sua tabela bronze e movê-los
  para uma tabela ouro no Lakehouse.

- **Exercício 3:** Automatizar e enviar notificações com o Data Factory
  para enviar um e-mail avisando quando todas as tarefas forem
  concluídas e, por fim, configure todo o fluxo para ser executado de
  forma agendada.

# Exercício 1: Criar um pipeline com o Data Factory

## Tarefa 1: Criar um espaço de trabalho

Antes de trabalhar com dados no Fabric, crie um espaço de trabalho com a
versão de avaliação do Fabric ativada.

1.  Abra seu navegador, acesse a barra de endereços e digite ou cole o
    seguinte URL: +++https://app.fabric.microsoft.com/+++ e pressione a
    tecla **Enter.**

> **Observação**: Se você for direcionado para a página inicial do
> Microsoft Fabric, ignore as etapas de nº 2 a nº 4.
>
> ![](./media/image1.png)

2.  Na janela do **Microsoft Fabric**, insira suas credenciais e clique
    no botão **Submit**.

> ![](./media/image2.png)

3.  Em seguida, na janela da **Microsoft**, digite a senha e clique no
    botão **Sign in.**

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image3.png)

4.  Na janela **Stay signed in?**, clique no botão **Yes.**

> ![A screenshot of a computer error AI-generated content may be
> incorrect.](./media/image4.png)
>
> ![](./media/image5.png)

5.  Na **página inicial do Microsoft Fabric**, selecione a opção **New
    workspace**.

> ![](./media/image6.png)

6.  Na aba **Create a workspace**, insira os seguintes detalhes e clique
    no botão **Apply**.

[TABLE]

> ![](./media/image7.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

7.  Aguarde a conclusão da implementação. Isso levará aproximadamente de
    2 a 3 minutos.

> ![A screenshot of a computer Description automatically
> generated](./media/image9.png)

## Tarefa 2: Criar um Lakehouse e ingerir dados de amostra

1.  Na página do espaço de trabalho **Data- FactoryXX,** navegue até o
    botão **+New item** e clique nele.

> ![A screenshot of a computer Description automatically
> generated](./media/image10.png)

2.  Clique no bloco "**Lakehouse**".

![A screenshot of a computer Description automatically
generated](./media/image11.png)

3.  Na caixa de diálogo **New Lakehouse**, digite
    +++**DataFactoryLakehouse+++** no campo **Name**, clique no botão
    **Create** e abra o novo Lakehouse.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image12.png)
>
> ![](./media/image13.png)

4.  Na página inicial do **Lakehouse,** selecione **Start with sample
    data** para abrir a cópia dos dados de exemplo.

> ![](./media/image14.png)

5.  A caixa de diálogo **Use a sample** é exibida; selecione o bloco de
    dados de amostra do **NYCTaxi**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image15.png)
>
> ![](./media/image16.png)
>
> ![](./media/image17.png)

6.  Para renomear a tabela, clique com o botão direito do mouse na aba
    **green_tripdata_2022**, logo acima do editor, e selecione
    **Rename**.

![A screenshot of a computer Description automatically
generated](./media/image18.png)

7.  Na caixa de diálogo **Rename**, no campo **Name**, digite
    **+++Bronze+++** para alterar o nome da **table**. Em seguida,
    clique no botão **Rename**.

![A screenshot of a computer Description automatically
generated](./media/image19.png)

![A screenshot of a computer Description automatically
generated](./media/image20.png)

**Exercício 2:** **Transformar dados com um fluxo de dados no Data
Factory**

## Tarefa 1: Obter dados de uma tabela Lakehouse

1.  Agora, clique no espaço de trabalho [**Data
    Factory-@lab.LabInstance.Id**](mailto:Data%20Factory-@lab.LabInstance.Id)
    no painel de navegação à esquerda.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image21.png)

2.  Crie um novo Dataflow Gen2 clicando no botão **+ New item** na barra
    de navegação. Na lista de itens disponíveis, selecione o item
    **Dataflow Gen2.**

> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

3.  Forneça um novo nome de fluxo de dados Gen2 como
    +++**nyc_taxi_data_with_discounts+++** e selecione **Create**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image23.png)

4.  No novo menu de fluxo de dados, no painel do **Power Query,** clique
    na **lista suspensa Get data** e selecione **More...**

> ![A screenshot of a computer Description automatically
> generated](./media/image24.png)

5.  Na aba **Choose data source**, na caixa de pesquisa, digite
    +++**Lakehouse+++** e, em seguida, clique no conector **Lakehouse**.

> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

6.  A caixa de diálogo **Connect to data source** é exibida e uma nova
    conexão é criada automaticamente com base no usuário conectado no
    momento. Selecione **Next**.

> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)

7.  A caixa de diálogo **Choose data** é exibida. Use o painel de
    navegação para encontrar o **workspace- Data-FactoryXX** e
    expanda-o. Em seguida, expanda o **Lakehouse** -
    **DataFactoryLakehouse,** que você criou para o destino no módulo
    anterior, selecione a tabela **Bronze** na lista e clique no botão
    **Create**.

![A screenshot of a computer Description automatically
generated](./media/image27.png)

8.  Você verá que a tela agora está preenchida com os dados.

![A screenshot of a computer Description automatically
generated](./media/image28.png)

**Tarefa 2: Transformando os dados importados do Lakehouse**

1.  Selecione o ícone de tipo de dados no cabeçalho da segunda coluna,
    **IpepPickupDatetime**, para exibir um menu suspenso e selecione o
    tipo de dados no menu para converter a coluna de **Date/Time** para
    **Date**.

![A screenshot of a computer Description automatically
generated](./media/image29.png)

2.  Na aba **Home** da faixa de opções, selecione a opção **Choose
    columns** no grupo **Manage columns**.

![A screenshot of a computer Description automatically
generated](./media/image30.png)

3.  Na caixa de diálogo **Choose columns**, **desmarque** algumas
    colunas listadas aqui e selecione **OK**.

    1.  lpepDropoffDatetime

    &nbsp;

    1.  DoLocationID

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image31.png)

4.  Selecione o filtro da coluna **storeAndFwdFlag** e o menu suspenso
    de ordenação. (Se aparecer o aviso **List may be incomplete**,
    selecione **Load more** para ver todos os dados.)

![A screenshot of a computer Description automatically
generated](./media/image32.png)

5.  Selecione 'Y**'** para mostrar apenas as linhas onde um desconto foi
    aplicado e, em seguida, selecione **OK**.

![A screenshot of a computer Description automatically
generated](./media/image33.png)

6.  Selecione a coluna **Ipep_Pickup_Datetime** no menu suspenso de
    classificação e filtro, depois selecione **Date filters** e escolha
    o filtro **Between...** fornecido para os tipos Data e Data/Hora.

![](./media/image34.png)

7.  Na caixa de diálogo **Filter rows**, selecione as datas entre
    **January 1, 2022** e **January 31, 2022** e, em seguida, selecione
    **OK**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image35.png)

**Tarefa 3: Conectar um arquivo CSV contendo dados de desconto**

Agora que os dados das viagens já estão disponíveis, queremos carregar
os dados que contêm os respectivos descontos para cada dia e ID do
fornecedor e preparar esses dados antes de combiná-los com os dados das
viagens.

1.  Na aba **Home** do menu do editor de fluxo de dados, selecione a
    opção **Get data** e, em seguida, escolha **Text/CSV**.

![](./media/image36.png)

2.  No painel **Connect to data source**, em **Connection settings**,
    selecione o botão de opção **Link to file**. Em seguida, insira
    +++https://raw.githubusercontent.com/ekote/azure-architect/master/Generated-NYC-Taxi-Green-Discounts.csv+++
    e informe o **Connection name** como **+++dfconnection+++.**
    Certifique-se de que o **authentication** **kind** esteja definido
    como **Anonymous** e clique no botão **Next.**![A screenshot of a
    computer AI-generated content may be
    incorrect.](./media/image37.png)

3.  Na caixa de diálogo **Preview file data**, selecione **Create**.

![A screenshot of a computer Description automatically
generated](./media/image38.png)

**Tarefa 4: Transformar os dados de desconto**

1.  Ao analisar os dados, vemos que os cabeçalhos parecem estar na
    primeira linha. Para promovê-los a cabeçalhos, selecione o menu de
    contexto da tabela no canto superior esquerdo da área de
    visualização da grade e escolha **Use first row as headers**.

![](./media/image39.png)

***Observação:** após promover os cabeçalhos, você verá uma nova etapa
adicionada ao painel **Applied steps** na parte superior do editor de
fluxo de dados, referente aos tipos de dados das suas colunas.*

![](./media/image40.png)

2.  Clique com o botão direito do mouse na coluna **VendorID** e, no
    menu de contexto exibido, selecione a opção **Unpivot other
    columns**. Isso permite transformar as colunas em pares
    atributo-valor, onde as colunas se tornam linhas.

![A screenshot of a computer Description automatically
generated](./media/image41.png)

3.  Com a tabela desagrupada, renomeie as colunas **Attribute** e
    **Value** clicando duas vezes nelas e alterando **Attribute** para
    +++**Date+++** e **Value** para +++**Discount+++**.

![A screenshot of a computer Description automatically
generated](./media/image42.png)

![A screenshot of a computer Description automatically
generated](./media/image43.png)

4.  Para alterar o tipo de dados da coluna **Date**, selecione o menu de
    tipo de dados à esquerda do nome da coluna e escolha **Date**.

![A screenshot of a computer Description automatically
generated](./media/image44.png)

5.  Selecione a coluna **Discount** e, em seguida, selecione a aba
    **Transform** no menu. Selecione **Number column** e, em seguida,
    selecione **Standard numeric transformations** no submenu e escolha
    **Divide**.

![A screenshot of a computer Description automatically
generated](./media/image45.png)

6.  Na caixa de diálogo **Divide**, insira o valor +++100+++ e clique no
    botão **OK**.

![A screenshot of a computer Description automatically
generated](./media/image46.png)

![A screenshot of a computer Description automatically
generated](./media/image47.png)

**Tarefa 7: Combinar dados de viagens e descontos**

O próximo passo é combinar as duas tabelas em uma única tabela que
contenha o desconto a ser aplicado à viagem e o total ajustado.

1.  Primeiro, ative o botão **Diagram view** para que você possa ver
    ambas as suas consultas.

![A screenshot of a computer Description automatically
generated](./media/image48.png)

2.  Selecione a consulta **Bronze** e, na aba **Home**, selecione o menu
    **Combine** e escolha **Merge queries** e, em seguida, **Merge
    queries as new**.

![](./media/image49.png)

3.  Na caixa de diálogo **Merge**, selecione
    **Generated-NYC-Taxi-Green-Discounts** no menu suspenso **Right
    table for merge** e, em seguida, selecione o ícone de "**light
    bulb**" no canto superior direito da caixa de diálogo para ver o
    mapeamento sugerido de colunas entre as três tabelas.

4.  Selecione cada um dos dois mapeamentos de coluna sugeridos, um de
    cada vez, mapeando as colunas VendorID e data de ambas as tabelas.
    Quando ambos os mapeamentos forem adicionados, os cabeçalhos das
    colunas correspondentes serão destacados em cada tabela.

![](./media/image50.png)

5.  Uma mensagem será exibida solicitando que você permita a combinação
    de dados de várias fontes para visualizar os resultados. Selecione
    **OK.** 

![A screenshot of a computer Description automatically
generated](./media/image51.png)

6.  Na área da tabela, você verá inicialmente um aviso informando **"The
    evaluation was canceled because combining data from multiple sources
    may reveal data from one source to another. Select continue if the
    possibility of revealing data is okay."** Selecione **Continue**
    para exibir os dados combinados.

![](./media/image52.png)

7.  Na caixa de diálogo **Privacy Levels**, selecione a **caixa de
    seleção: Ignore Privacy Levels checks for this document. Ignoring
    privacy Levels could expose sensitive or confidential data to an
    unauthorized person.** Em seguida, clique no botão **Save**.

![A screenshot of a computer screen Description automatically
generated](./media/image53.png)

![](./media/image54.png)

8.  Observe como uma nova consulta foi criada na visualização diagrama,
    mostrando a relação da nova consulta de mesclagem com as duas
    consultas que você criou anteriormente. No painel de tabelas do
    editor, role para a direita da lista de colunas da consulta mesclar
    para ver que há uma nova coluna com valores de tabela. Essa é a
    coluna **“Generated NYC Taxi-Green-Discounts”**, e o tipo dela é
    **\[Table\].**

No cabeçalho da coluna, há um ícone com duas setas apontando em direções
opostas, que permite selecionar colunas da tabela. Desmarque todas as
colunas, exceto **Discount**, e clique em **"OK"**.

![](./media/image55.png)

9.  Com o valor do desconto agora no nível da linha, podemos criar uma
    nova coluna para calcular o valor total após o desconto. Para isso,
    selecione a aba **Add column** na parte superior do editor e escolha
    **Custom column** no grupo **General**.

![](./media/image56.png)

10. Na caixa de diálogo **Custom column**, você pode usar a [linguagem
    de fórmulas do Power Query (também conhecida como
    M)](https://learn.microsoft.com/en-us/powerquery-m) para definir
    como sua nova coluna deve ser calculada. Digite
    +++**TotalAfterDiscount+++** para o **New column name**, selecione
    **Currency** para o **Data type** e forneça a seguinte expressão M
    para a **Custom column formula**:

+++if \[total_amount\] \> 0 then \[total_amount\] \* ( 1 -\[Discount\] )
else \[total_amount\]+++

Em seguida, selecione **OK**.

![](./media/image57.png)

![A screenshot of a computer Description automatically
generated](./media/image58.png)

11. Selecione a coluna **TotalAfterDiscount** recém-criada e, em
    seguida, selecione a aba **Transform** na parte superior da janela
    do editor. No grupo **Number column**, selecione o menu suspenso
    **Rounding** e escolha **Round** …

**Observação**: Se você não encontrar a opção **Round**, expanda o menu
para visualizar a **Number column**.

![](./media/image59.png)

12. Na caixa de diálogo **Round**, digite **2** para o número de casas
    decimais e selecione **OK**.

![A screenshot of a computer Description automatically
generated](./media/image60.png)

13. Altere o tipo de dados do **IpepPickupDatetime** de **Date** para
    **Date/Time**.

![](./media/image61.png)

14. Por fim, expanda o painel **Query settings** no lado direito do
    editor, caso ainda não esteja expandido, e renomeie a consulta de
    **Merge** para +++**Output+++**.

![A screenshot of a computer Description automatically
generated](./media/image62.png)

![A screenshot of a computer Description automatically
generated](./media/image63.png)

**Tarefa 8: Carregar a consulta de saída em uma tabela no Lakehouse**

Com a consulta de saída agora totalmente preparada e com os dados
prontos para serem exibidos, podemos definir o destino da saída da
consulta.

1.  Selecione a consulta de mesclagem **Output** criada anteriormente.
    Em seguida, selecione o **ícone +** para adicionar um **data
    destination** a este fluxo de dados.

2.  Na lista de destinos de dados, selecione a opção **Lakehouse** em
    **New destination.**

![](./media/image64.png)

3.  Na caixa de diálogo **Connect to data destination**, sua conexão já
    deve estar selecionada. Selecione **Next** para continuar.

![A screenshot of a computer Description automatically
generated](./media/image65.png)

4.  Na caixa de diálogo **Choose destination target**, navegue até
    Lakehouse e selecione **Next** novamente.

![](./media/image66.png)

5.  Na caixa de diálogo **Choose destination settings**, mantenha o
    método de atualização padrão **Replace**, verifique novamente se
    suas colunas estão mapeadas corretamente e selecione **Save
    settings**.

![](./media/image67.png)

6.  De volta à janela principal do editor, confirme se o destino de
    saída exibido no painel **Query settings** para a tabela **Output**
    e, em seguida, selecione a opção **Save and Run** na aba **Home**.

![](./media/image68.png)

![A screenshot of a computer Description automatically
generated](./media/image69.png)

7.  Agora, clique no **Data Factory-XXXX workspace** no painel de
    navegação à esquerda.

![A screenshot of a computer Description automatically
generated](./media/image70.png)

8.  No painel **Data_FactoryXX**, selecione **DataFactoryLakehouse**
    para visualizar a nova tabela carregada ali.

![](./media/image71.png)

9.  Confirme se a tabela **Output** aparece no esquema **dbo**.

![](./media/image72.png)

**Exercício 3: Automatizando e enviando notificações com o Data
Factory**

**Tarefa 1: Adicionar uma atividade do Office 365 Outlook ao seu
pipeline**

1.  Navegue até o espaço de trabalho **Data_FactoryXX** no menu de
    navegação à esquerda e clique nele.

![A screenshot of a computer Description automatically
generated](./media/image73.png)

2.  Selecione a opção **+ New item** na página do espaço de trabalho e
    selecione **Pipeline.**

![A screenshot of a computer Description automatically
generated](./media/image74.png)

3.  Forneça um nome de pipeline como +++**First_Pipeline1+++** e
    selecione **Create**.

![](./media/image75.png)

4.  Selecione a aba **Home** no editor de pipeline e encontre a
    atividade **Add to canvas.**

![](./media/image76.png)

5.  Na aba **Source**, insira as seguintes configurações e clique em
    **Test connection.**

[TABLE]

![](./media/image77.png)

6.  Na aba **Destination**, insira as seguintes configurações.

[TABLE]

![](./media/image78.png)

![A screenshot of a computer Description automatically
generated](./media/image79.png)

7.  Na faixa de opções, selecione **Run**.

![](./media/image80.png)

8.  Na caixa de diálogo **Save and run?,** clique no botão **Save and
    run.**

![A screenshot of a computer Description automatically
generated](./media/image81.png)

![](./media/image82.png)

9.  Selecione a aba **Activities** no editor de pipeline e encontre a
    atividade **Office Outlook.**

![A screenshot of a computer Description automatically
generated](./media/image83.png)

10. Selecione e arraste o caminho e em caso de sucesso aparecerá (uma
    caixa de seleção verde no canto superior direito da atividade na
    tela do pipeline) da sua atividade de cópia para a sua nova
    atividade do Office 365 Outlook.

![A screenshot of a computer Description automatically
generated](./media/image84.png)

11. Selecione a atividade do Office 365 Outlook na tela do pipeline e,
    em seguida, selecione a aba **Settings** na área de propriedades
    abaixo da tela para configurar o e-mail. Clique no menu suspenso
    **Connection** e selecione **Browse all.**

![A screenshot of a computer Description automatically
generated](./media/image85.png)

12. Na janela ‘choose a data source’, selecione a fonte de dados
    **Office 365 Email**.

![A screenshot of a computer Description automatically
generated](./media/image86.png)

13. Inicie com a conta a partir da qual você deseja enviar o e-mail.
    Você pode usar a conexão existente com a conta já conectada.

![A screenshot of a computer Description automatically
generated](./media/image87.png)

14. Clique em **Connect** para prosseguir.

![A screenshot of a computer Description automatically
generated](./media/image88.png)

15. Selecione a atividade do Office 365 Outlook na tela do pipeline, na
    aba **Settings** da área de propriedades abaixo da tela, para
    configurar o e-mail.

    1.  Insira seu endereço de e-mail no campo **To**. Se desejar usar
        vários endereços, utilize**;** para separá-los.

![A screenshot of a computer Description automatically
generated](./media/image89.png)

1.  No campo **Subject**, selecione-o para que a opção **Add dynamic
    content** apareça e, em seguida, selecione para exibir a tela do
    criador de expressões de pipeline.

![A screenshot of a computer Description automatically
generated](./media/image90.png)

16. A caixa de diálogo **Pipeline expression builder** é exibida. Insira
    a seguinte expressão e selecione **OK**:

*+++@concat('DI in an Hour Pipeline Succeeded with Pipeline Run Id',
pipeline().RunId)+++*

![](./media/image91.png)

17. Para o **Body**, selecione o campo novamente e escolha a opção
    **View in expression builder** quando aparecer abaixo do espaço de
    trabalho. Adicione a seguinte expressão novamente na caixa de
    diálogo **Pipeline expression builder** que aparece e selecione
    **OK**:

*concat('RunID = ', pipeline().RunId, ' ; ', 'Copied rows ',
activity('Copy data1').output.rowsCopied, ' ; ','Throughput ',
activity('Copy data1').output.throughput)+++*

![](./media/image92.png)

![A screenshot of a computer Description automatically
generated](./media/image93.png)

**Observação:** Substitua **Copy data1** pelo nome da sua própria
atividade de cópia de pipeline.

18. Por fim, selecione a aba **Home** na parte superior do editor de
    pipeline e escolha **Run**. Em seguida, selecione **Save and run**
    novamente na caixa de diálogo de confirmação para executar essas
    atividades.

![A screenshot of a computer Description automatically
generated](./media/image94.png)

![A screenshot of a computer Description automatically
generated](./media/image95.png)

![A screenshot of a computer Description automatically
generated](./media/image96.png)

19. Após a execução bem-sucedida do pipeline, verifique seu e-mail para
    encontrar a mensagem de confirmação enviada pelo pipeline.

![](./media/image97.png)

**Tarefa 2: Agendar a execução do pipeline**

Após concluir o desenvolvimento e os testes do seu pipeline, você pode
agendá-lo para ser executado automaticamente.

1.  Na aba **Home** da janela do editor de pipeline, selecione
    **Schedule**.

![A screenshot of a computer Description automatically
generated](./media/image98.png)

2.  Configure o agendamento conforme necessário. O exemplo aqui agenda a
    execução do pipeline diariamente às 20h até o final do ano.

![A screenshot of a schedule Description automatically
generated](./media/image99.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image100.png)

![A screenshot of a schedule AI-generated content may be
incorrect.](./media/image101.png)

**Tarefa 3: Adicionar uma atividade de fluxo de dados ao pipeline**

1.  Posicione o cursor sobre a linha verde que conecta a atividade
    **Copy** e a atividade do **Office 365 Outlook** na tela do seu
    pipeline e selecione o botão **+** para inserir uma nova atividade.

![A screenshot of a computer Description automatically
generated](./media/image102.png)

2.  Selecione **Dataflow** no menu que aparece.

![A screenshot of a computer Description automatically
generated](./media/image103.png)

3.  A atividade de fluxo de dados recém-criada é inserida entre a
    atividade **Copy** e a atividade do **Office 365 Outlook** e
    selecionada automaticamente, exibindo suas propriedades na área
    abaixo da tela. Selecione a aba **Settings** na área de propriedades
    e, em seguida, selecione o fluxo de dados criado no **Exercício 2:
    Transformar dados com um fluxo de dados no Data Factory**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image104.png)

4.  Selecione a aba **Home** na parte superior do editor de pipeline e
    escolha **Run**. Em seguida, selecione **Save and run** novamente na
    caixa de diálogo de confirmação para executar essas atividades.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image105.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image106.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image107.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image108.png)

**Tarefa 4: Limpar recursos**

Você pode excluir relatórios individuais, pipelines, armazéns e outros
itens ou remover todo o espaço de trabalho. Siga as etapas a seguir para
excluir o espaço de trabalho que você criou para este tutorial.

1.  Selecione seu espaço de trabalho, o **Data- FactoryXX**, no menu de
    navegação à esquerda. Isso abrirá a visualização dos itens do espaço
    de trabalho.

![](./media/image109.png)

2.  Selecione a opção **Workspace settings** na página do espaço de
    trabalho, localizada no canto superior direito.

![A screenshot of a computer Description automatically
generated](./media/image110.png)

3.  Selecione a aba **General** e **Remove this workspace.**

![A screenshot of a computer Description automatically
generated](./media/image111.png)
