## Caso de uso 05 – Creación de un Data Warehouse geográfico y de ventas para Contoso en Microsoft Fabric

**Introducción**
Contoso, una empresa multinacional del sector retail, busca modernizar
su infraestructura de datos para optimizar el análisis geográficoy de
ventas. Actualmente, sus datos de ventas y clientes se encuentran
dispersos en múltiples sistemas, lo que dificulta que sus analistas de
negocio y desarrolladores ciudadanos obtengan información relevante. La
compañía planea consolidar estos datos en una plataforma unificada
mediante Microsoft Fabric, con el objetivo de habilitar consultas
cruzadas, análisis de ventas e informes geográficos.

En este laboratorio, asumirá el rol de ingeniero de datos en Contoso,
encargado de diseñar e implementar una solución de Data Warehouse
utilizando Microsoft Fabric. Comenzará configurando un espacio de
trabajo en Fabric, creando un Data Warehouse, cargando datos desde Azure
Blob Storage y realizando tareas analíticas para proporcionar
información útil a los tomadores de decisiones de la empresa.

Aunque muchos conceptos de Microsoft Fabric pueden resultar familiares
para profesionales de datos y analítica, aplicarlos en un entorno nuevo
puede representar un desafío. Por ello, este laboratorio está diseñado
para guiar paso a paso a través de un escenario de extremo a extremo,
desde la adquisición hasta el consumo de datos, con el objetivo de
construir una comprensión básica de la experiencia de usuario de
Microsoft Fabric, las distintas experiencias que ofrece, sus puntos de
integración y las funcionalidades disponibles tanto para profesionales
de datos como para desarrolladores ciudadanos.

**Objetivos**

- Configurar un espacio de trabajo en Fabric con prueba habilitada.

- Establecer un nuevo Data Warehouse llamado WideWorldImporters en
  Microsoft Fabric.

- Cargar datos en el espacio de trabajo Warehouse_FabricXX usando un
  pipeline de Data Factory.

- Generar las tablas dimension_city y fact_sale dentro del Data
  Warehouse.

- Poblar las tablas dimension_city y fact_sale con datos provenientes de
  Azure Blob Storage.

- Crear clones de las tablas dimension_city y fact_sale dentro del Data
  Warehouse.

- Clonar las tablas dimension_city y fact_sale en el esquema dbo1.

- Desarrollar un procedimiento almacenado para transformar datos y crear
  la tabla aggregate_sale_by_date_city.

- Generar una consulta utilizando el generador visual de consultas para
  combinar y agregar datos.

- Utilizar un notebook para consultar y analizar datos de la tabla
  dimension_customer.

- Incluir los Data Warehouses WideWorldImporters y ShortcutExercise para
  consultas cruzadas.

- Ejecutar una consulta T-SQL a través de los Data Warehouses
  WideWorldImporters y ShortcutExercise.

- Habilitar la integración visual con Azure Maps en el portal de
  administración.

- Generar gráficos de columnas, mapas y tablas para el informe de
  análisis de ventas.

- Crear un informe utilizando datos del conjunto de datos
  WideWorldImporters en el hub de datos OneLake.

- Eliminar el espacio de trabajo y sus elementos asociados.

# **Ejercicio 1: Crear un espacio de trabajo en Microsoft Fabric**

## **Tarea 1: Iniciar sesión en la cuenta de Power BI y registrarse en la prueba gratuita de Microsoft Fabric**

1.  Abra su navegador, vaya a la barra de direcciones y escriba o pegue
    la siguiente URL: +++https://app.fabric.microsoft.com/+++ y luego
    presione la tecla **Enter**.

> ![](./media/image1.png)

2.  En la ventana de **Microsoft Fabric**, ingrese las credenciales
    asignadas y haga clic en el botón **Submit**.

> ![](./media/image2.png)

3.  Luego, en la ventana de **Microsoft**, ingrese la contraseña y haga
    clic en el botón **Sign in.**

> ![](./media/image3.png)

4.  En la ventana **Stay signed in?,** haga clic en **Yes.**

> ![](./media/image4.png)

5.  Será redirigido a la página de inicio de Power BI.

> ![](./media/image5.png)

## Tarea 2: Crear un espacio de trabajo

Antes de trabajar con datos en Fabric, cree un espacio de trabajo con la
prueba gratuita de Fabric habilitada.

1.  En el panel **Workspaces**, seleccione **+ New workspace**.

> ![A screenshot of a computer Description automatically
> generated](./media/image6.png)

2.  En la pestaña **Create a workspace**, ingrese los siguientes datos y
    haga clic en el botón **Apply:**

    |  |  |
    |----|---|
    |Name	|+++Warehouse_Fabric@lab.LabInstance.Id+++ (must be a unique Id) |
    |Description	|+++This workspace contains all the artifacts for the data warehouse+++|
    |Advanced	Under License mode| select Fabric capacity|
    |Default storage format	|Small dataset storage format|

> ![](./media/image7.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

3.  Espere a que finalice la implementación. Este proceso tarda entre 1
    y 2 minutos. Cuando se abra su nuevo espacio de trabajo, debería
    estar vacío.

> ![](./media/image9.png)

## Tarea 3: Crear un Warehouse en Microsoft Fabric

1.  En la página de **Fabric**, seleccione **+ New item** para crear un
    lakehouse y luego seleccione Warehouse.

> ![A screenshot of a computer Description automatically
> generated](./media/image10.png)

2.  En el cuadro de diálogo **New warehouse**, ingrese
    +++**WideWorldImporters**+++ y haga clic en el botón **Create**.

> ![](./media/image11.png)

3.  Cuando finalice el aprovisionamiento, aparecerá la página inicial
    del almacén de datos **WideWorldImporters**.

> ![](./media/image12.png)

# **Ejercicio 2: Ingerir datos en un Warehouse en Microsoft Fabric**

## Tarea 1: Ingerir datos en un Warehouse

1.  Desde la página inicial del warehouse **WideWorldImporters**,
    seleccione **Warehouse_FabricXX** en el menú de navegación izquierdo
    para regresar a la lista de elementos del espacio de trabajo.

> ![](./media/image13.png)

2.  En la página **Warehouse_FabricXX**, seleccione **+ New item**.
    Luego haga clic en **Pipeline** para ver la lista completa de
    elementos disponibles en Get data.

> ![](./media/image14.png)

3.  En el cuadro de diálogo **New pipeline**, en el campo **Name**,
    ingrese +++**Load Customer Data**+++ y haga clic en el botón
    **Create**.

> ![](./media/image15.png)

4.  En la página **Load Customer Data**, navegue hasta la sección
    **Start building your data pipeline** y haga clic en **Pipeline
    activity**.

> ![](./media/image16.png)

5.  Navegue y seleccione **Copy data** en la sección **Move &
    transform**.

> ![A screenshot of a computer Description automatically
> generated](./media/image17.png)

6.  Seleccione la actividad recién creada **Copy data 1** en el lienzo
    de diseño para configurarla.

> **Nota:** Arrastre la línea horizontal en el lienzo de diseño para ver
> por completo las diferentes características.
>
> ![A screenshot of a computer Description automatically
> generated](./media/image18.png)

7.  En la pestaña **General**, en el campo **Name**, ingrese +++**CD
    Load dimension_customer+++**

> ![A screenshot of a computer Description automatically
> generated](./media/image19.png)

8.  En la página **Source**, seleccione el menú desplegable
    **Connection**. Seleccione **Browse all** para ver todas las fuentes
    de datos disponibles.

> ![](./media/image20.png)

9.  En la ventana **Get data**, busque +++**Azure Blobs**+++ y luego
    haga clic en el botón **Azure Blob Storage**.

> ![](./media/image21.png)

10. En el panel **Connection settings** que aparece en el lado derecho,
    configure los siguientes valores y haga clic en el botón
    **Connect**:

- En **Account name or URL**, ingrese
  +++**https://fabrictutorialdata.blob.core.windows.net/sampledata/+++**

- En **Connection credentials**, haga clic en el menú desplegable
  **Connection** y seleccione **Create new connection**.

- En **Connection name**, ingrese +++**Wide World Importers Public Sample+++**.

- Establezca **Authentication kind** en **Anonymous**.

> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

11. Cambie los valores restantes en la página **Source** de la actividad
    de copia para llegar a los archivos .parquet ubicados en:
    **https://fabrictutorialdata.blob.core.windows.net/sampledata/WideWorldImportersDW/parquet/full/dimension_customer/\*.parquet**

12. En las cajas de texto **File path**, proporcione:

- **Container:** +++**sampledata+++**

- **File path - Directory:** +++**WideWorldImportersDW/tables+++**

- **File path - File name:** +++**dimension_customer.parquet+++**

- En el menú desplegable **File format**, seleccione **Parquet** (si no
  aparece, escríbalo en la barra de búsqueda y selecciónelo).

> ![](./media/image23.png)

13. Haga clic en **Preview data** en el lado derecho de la configuración
    de **File path** para asegurarse de que no existan errores y luego
    cierre la ventana.

> ![](./media/image24.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

14. En la pestaña **Destination**, ingrese los siguientes valores:

    |  |  |
    |---|---|
    |Connection	|WideWorldImporters|
    |Table option	|select the Auto create table radio button.|
    |Table	|•	In the first box enter +++dbo+++<br>•	In the second box enter +++dimension_customer+++|

> **Nota: Al agregar la conexión como WideWorldImporters warehouse,
> agréguela desde el catálogo OneLake navegando a la opción Browse
> all.**
>
> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image27.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image28.png)

15. En la cinta de opciones, seleccione **Run**.

> ![A screenshot of a computer Description automatically
> generated](./media/image29.png)

16. En el cuadro de diálogo **Save and run?**, haga clic en **Save and
    run**.

> ![](./media/image30.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image31.png)

17. Supervise el progreso de la actividad de copia en la página
    **Output** y espere a que finalice.

> ![A screenshot of a computer Description automatically
> generated](./media/image32.png)

# Ejercicio 3: Crear tablas en un Data Warehouse

## Tarea 1: Crear una tabla en un Data Warehouse

1.  En la página **Load Customer Data**, haga clic en el espacio de
    trabajo **Warehouse_FabricXX** en la barra de navegación izquierda y
    seleccione **WideWorldImporters** Warehouse.

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

2.  En la página **WideWorldImporters**, vaya a la pestaña **Home**,
    seleccione **SQL** en el menú desplegable y haga clic en **New SQL
    query**.

> ![A screenshot of a computer Description automatically
> generated](./media/image34.png)

3.  En el editor de consultas, pegue el siguiente código y seleccione
    **Run** para ejecutar la consulta:

    ```
    /*
    1. Drop the dimension_city table if it already exists.
    2. Create the dimension_city table.
    3. Drop the fact_sale table if it already exists.
    4. Create the fact_sale table.
    */
    
    --dimension_city
    DROP TABLE IF EXISTS [dbo].[dimension_city];
    CREATE TABLE [dbo].[dimension_city]
        (
            [CityKey] [int] NULL,
            [WWICityID] [int] NULL,
            [City] [varchar](8000) NULL,
            [StateProvince] [varchar](8000) NULL,
            [Country] [varchar](8000) NULL,
            [Continent] [varchar](8000) NULL,
            [SalesTerritory] [varchar](8000) NULL,
            [Region] [varchar](8000) NULL,
            [Subregion] [varchar](8000) NULL,
            [Location] [varchar](8000) NULL,
            [LatestRecordedPopulation] [bigint] NULL,
            [ValidFrom] [datetime2](6) NULL,
            [ValidTo] [datetime2](6) NULL,
            [LineageKey] [int] NULL
        );
    
    --fact_sale
    
    DROP TABLE IF EXISTS [dbo].[fact_sale];
    
    CREATE TABLE [dbo].[fact_sale]
    
        (
            [SaleKey] [bigint] NULL,
            [CityKey] [int] NULL,
            [CustomerKey] [int] NULL,
            [BillToCustomerKey] [int] NULL,
            [StockItemKey] [int] NULL,
            [InvoiceDateKey] [datetime2](6) NULL,
            [DeliveryDateKey] [datetime2](6) NULL,
            [SalespersonKey] [int] NULL,
            [WWIInvoiceID] [int] NULL,
            [Description] [varchar](8000) NULL,
            [Package] [varchar](8000) NULL,
            [Quantity] [int] NULL,
            [UnitPrice] [decimal](18, 2) NULL,
            [TaxRate] [decimal](18, 3) NULL,
            [TotalExcludingTax] [decimal](29, 2) NULL,
            [TaxAmount] [decimal](38, 6) NULL,
            [Profit] [decimal](18, 2) NULL,
            [TotalIncludingTax] [decimal](38, 6) NULL,
            [TotalDryItems] [int] NULL,
            [TotalChillerItems] [int] NULL,
            [LineageKey] [int] NULL,
            [Month] [int] NULL,
            [Year] [int] NULL,
            [Quarter] [int] NULL
        );
    ```

> ![A screenshot of a computer Description automatically
> generated](./media/image35.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image36.png)

4.  Para guardar esta consulta, haga clic derecho sobre la pestaña **SQL
    query 1** ubicada encima del editor y seleccione **Rename**.

> ![A screenshot of a computer Description automatically
> generated](./media/image37.png)

5.  En el cuadro de diálogo **Rename**, en el campo **Name**, ingrese
    +++**Create Tables**+++ para cambiar el nombre de **SQL query 1**.
    Luego haga clic en el botón **Rename**.

> ![](./media/image38.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image39.png)

6.  Valide que la tabla se creó correctamente seleccionando el ícono
    **Refresh** en la cinta de opciones.

> ![A screenshot of a computer Description automatically
> generated](./media/image40.png)

7.  En el panel **Explorer**, verá las tablas **fact_sale** y
    **dimension_city**.

> ![A screenshot of a computer Description automatically
> generated](./media/image41.png)

## Tarea 2: Cargar datos mediante T-SQL

Ahora que ya sabe cómo crear un data warehouse, cargar una tabla y
generar un informe, es momento de ampliar la solución explorando otros
métodos para cargar datos.

1.  En la página **WideWorldImporters,** vaya a la pestaña **Home**,
    seleccione **SQL** en el menú desplegable y haga clic en **New SQL
    query**.

> ![A screenshot of a computer Description automatically
> generated](./media/image42.png)

2.  En el editor de consultas, **pegue** el siguiente código y luego
    haga clic en **Run** para ejecutarlo:

    ```
    --Copy data from the public Azure storage account to the dbo.dimension_city table.
    COPY INTO [dbo].[dimension_city]
    FROM 'https://fabrictutorialdata.blob.core.windows.net/sampledata/WideWorldImportersDW/tables/dimension_city.parquet'
    WITH (FILE_TYPE = 'PARQUET');
    
    --Copy data from the public Azure storage account to the dbo.fact_sale table.
    COPY INTO [dbo].[fact_sale]
    FROM 'https://fabrictutorialdata.blob.core.windows.net/sampledata/WideWorldImportersDW/tables/fact_sale.parquet'
    WITH (FILE_TYPE = 'PARQUET');
    ```
> ![A screenshot of a computer Description automatically
> generated](./media/image43.png)

3.  Una vez que la consulta se complete, revise los mensajes, los cuales
    indican la cantidad de filas que se cargaron en las tablas
    **dimension_city** y **fact_sale**, respectivamente.

> ![A screenshot of a computer Description automatically
> generated](./media/image44.png)

4.  Cargue la vista previa de los datos para validar que se hayan
    cargado correctamente, seleccionando la tabla **fact_sale** en el
    panel **Explorer**.

> ![](./media/image45.png)

5.  Cambie el nombre de la consulta. Haga clic derecho en **SQL query
    1** en el panel **Explore**r y seleccione **Rename**.

> ![](./media/image46.png)

6.  En el cuadro de diálogo **Rename**, en el campo **Name**, ingrese
    +++**Load Tables**+++. Luego haga clic en el botón **Rename**.

> ![A screenshot of a computer Description automatically
> generated](./media/image47.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image48.png)

7.  Haga clic en el ícono **Refresh** en la barra de comandos situada
    debajo de la pestaña **Home**.

> ![A screenshot of a computer Description automatically
> generated](./media/image49.png)

# Ejercicio 4: Clonar una tabla mediante T-SQL en Microsoft Fabric

## Tarea 1: Crear un clon de tabla dentro del mismo esquema en un warehouse

Esta tarea le guía en la creación de un [clon de
tabla](https://learn.microsoft.com/en-in/fabric/data-warehouse/clone-table)
en un warehouse de Microsoft Fabric utilizando la sintaxis T-SQL [CREATE
TABLE AS CLONE
OF](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-table-as-clone-of-transact-sql?view=fabric&preserve-view=true).

1.  Cree un clon de tabla dentro del mismo esquema en un warehouse.

2.  En la página **WideWorldImporters**, vaya a la pestaña **Home**,
    seleccione **SQL** en el menú desplegable y haga clic en **New SQL
    query**.

> ![A screenshot of a computer Description automatically
> generated](./media/image50.png)

3.  En el editor de consultas, pegue el siguiente código para crear
    clones de las tablas **dbo.dimension_city** y **dbo.fact_sale**:

    ```
    --Create a clone of the dbo.dimension_city table.
    CREATE TABLE [dbo].[dimension_city1] AS CLONE OF [dbo].[dimension_city];
    
    --Create a clone of the dbo.fact_sale table.
    CREATE TABLE [dbo].[fact_sale1] AS CLONE OF [dbo].[fact_sale];
    ```
> ![A screenshot of a computer Description automatically
> generated](./media/image51.png)

4.  Seleccione **Run** para ejecutar la consulta. La ejecución tarda
    unos segundos. Una vez concluida, se habrán creado los clones de
    tablas **dimension_city1** y **fact_sale1**.

> ![A screenshot of a computer Description automatically
> generated](./media/image52.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image53.png)

5.  Cargue la vista previa de datos para validar que se hayan cargado
    correctamente, seleccionando la tabla **dimension_city1** en el
    panel **Explorer.**

> ![A screenshot of a computer Description automatically
> generated](./media/image54.png)

6.  Haga clic derecho en la **consulta SQL** que creó para clonar las
    tablas, ubicada en el panel **Explorer**, y seleccione **Rename.**

> ![A screenshot of a computer Description automatically
> generated](./media/image55.png)

7.  En el cuadro de diálogo **Rename**, en el campo **Name**, ingrese
    +++**Clone Table**+++, luego haga clic en el botón **Rename**.

> ![A screenshot of a computer Description automatically
> generated](./media/image56.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image57.png)

8.  Haga clic en el ícono **Refresh** en la barra de comandos situada
    debajo de la pestaña **Home**.

> ![A screenshot of a computer Description automatically
> generated](./media/image58.png)

## Tarea 2: Crear un clon de tabla entre esquemas dentro del mismo warehouse

1.  En la página **WideWorldImporters,** vaya a la pestaña **Home**,
    seleccione **SQL** en el menú desplegable y haga clic en **New SQL
    query**.

> ![A screenshot of a computer Description automatically
> generated](./media/image59.png)

2.  Cree un nuevo esquema dentro del warehouse **WideWorldImporters**
    llamado **dbo1**. Copie, pegue y ejecute el siguiente código
    **T-SQL:**

    +++CREATE SCHEMA dbo1+++
> ![A screenshot of a computer Description automatically
> generated](./media/image60.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

3.  En el editor de consultas, elimine el código existente y pegue lo
    siguiente para crear clones de las tablas **dbo.dimension_city** y
    **dbo.fact_sale** en el esquema **dbo1**:

    ```
    --Create a clone of the dbo.dimension_city table in the dbo1 schema.
    CREATE TABLE [dbo1].[dimension_city1] AS CLONE OF [dbo].[dimension_city];
    
    --Create a clone of the dbo.fact_sale table in the dbo1 schema.
    CREATE TABLE [dbo1].[fact_sale1] AS CLONE OF [dbo].[fact_sale];
    ```

4.  Seleccione **Run** para ejecutar la consulta. La ejecución tarda
    unos segundos.

> ![A screenshot of a computer Description automatically
> generated](./media/image62.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image63.png)

5.  Una vez completada la consulta, los clones **dimension_city1** y
    **fact_sale1** habrán sido creados en el esquema **dbo1**.

> ![A screenshot of a computer Description automatically
> generated](./media/image64.png)

6.  Cargue la vista previa de datos para validar que se hayan cargado
    correctamente, seleccionando la tabla **dimension_city1** bajo el
    esquema **dbo1** en el panel **Explorer**.

> ![A screenshot of a computer Description automatically
> generated](./media/image65.png)

7.  **Cambie** el nombre de la consulta para futuras referencias. Haga
    clic derecho en **SQL query 1** en el panel **Explorer** y
    seleccione **Rename**.

> ![A screenshot of a computer Description automatically
> generated](./media/image66.png)

8.  En el cuadro de diálogo **Rename**, en el campo **Name**, ingrese
    +++**Clone Table in another schema**+++. Luego haga clic en el botón
    **Rename**.

> ![](./media/image67.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image68.png)

9.  Haga clic en el ícono **Refresh** en la barra de comandos situada
    debajo de la pestaña **Home**.

> ![A screenshot of a computer Description automatically
> generated](./media/image69.png)

# **Ejercicio 5: Transformar datos mediante un procedimiento almacenado**

Aprenda cómo crear y guardar un nuevo procedimiento almacenado para
transformar datos.

1.  En la página **WideWorldImporters**, vaya a la pestaña **Home**,
    seleccione **SQL** en el menú desplegable y haga clic en **New SQL
    query**.

> ![A screenshot of a computer Description automatically
> generated](./media/image70.png)

2.  En el editor de consultas, pegue el siguiente código para crear el
    procedimiento almacenado **dbo.populate_aggregate_sale_by_city**.
    Este procedimiento creará y cargará la tabla
    **dbo.aggregate_sale_by_date_city** en un paso posterior:

    ```
    --Drop the stored procedure if it already exists.
    DROP PROCEDURE IF EXISTS [dbo].[populate_aggregate_sale_by_city]
    GO
    
    --Create the populate_aggregate_sale_by_city stored procedure.
    CREATE PROCEDURE [dbo].[populate_aggregate_sale_by_city]
    AS
    BEGIN
        --If the aggregate table already exists, drop it. Then create the table.
        DROP TABLE IF EXISTS [dbo].[aggregate_sale_by_date_city];
        CREATE TABLE [dbo].[aggregate_sale_by_date_city]
            (
                [Date] [DATETIME2](6),
                [City] [VARCHAR](8000),
                [StateProvince] [VARCHAR](8000),
                [SalesTerritory] [VARCHAR](8000),
                [SumOfTotalExcludingTax] [DECIMAL](38,2),
                [SumOfTaxAmount] [DECIMAL](38,6),
                [SumOfTotalIncludingTax] [DECIMAL](38,6),
                [SumOfProfit] [DECIMAL](38,2)
            );
    
        --Reload the aggregated dataset to the table.
        INSERT INTO [dbo].[aggregate_sale_by_date_city]
        SELECT
            FS.[InvoiceDateKey] AS [Date], 
            DC.[City], 
            DC.[StateProvince], 
            DC.[SalesTerritory], 
            SUM(FS.[TotalExcludingTax]) AS [SumOfTotalExcludingTax], 
            SUM(FS.[TaxAmount]) AS [SumOfTaxAmount], 
            SUM(FS.[TotalIncludingTax]) AS [SumOfTotalIncludingTax], 
            SUM(FS.[Profit]) AS [SumOfProfit]
        FROM [dbo].[fact_sale] AS FS
        INNER JOIN [dbo].[dimension_city] AS DC
            ON FS.[CityKey] = DC.[CityKey]
        GROUP BY
            FS.[InvoiceDateKey],
            DC.[City], 
            DC.[StateProvince], 
            DC.[SalesTerritory]
        ORDER BY 
            FS.[InvoiceDateKey], 
            DC.[StateProvince], 
            DC.[City];
    END
    ```
> ![A screenshot of a computer Description automatically
> generated](./media/image71.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image72.png)

3.  Haga clic derecho en la consulta SQL creada y seleccione **Rename**.

> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)

4.  En el cuadro de diálogo **Rename**, en el campo **Name**, ingrese
    +++**Create Aggregate Procedure**+++ y haga clic en **Rename**.

> ![A screenshot of a computer screen Description automatically
> generated](./media/image74.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image75.png)

5.  Haga clic en el ícono **Refresh** debajo de la pestaña **Home**.

> ![A screenshot of a computer Description automatically
> generated](./media/image76.png)

6.  En la pestaña **Explorer**, verifique que el procedimiento
    almacenado recién creado esté visible expandiendo el nodo **Stored
    Procedures** bajo el esquema **dbo**.

> ![A screenshot of a computer Description automatically
> generated](./media/image77.png)

7.  En la página **WideWorldImporters**, vaya a la pestaña **Home**,
    seleccione **SQL** en el menú desplegable y haga clic en **New SQL
    query.**

> ![A screenshot of a computer Description automatically
> generated](./media/image78.png)

8.  En el editor de consultas, pegue el siguiente código para ejecutar
    **dbo.populate_aggregate_sale_by_city** y crear la tabla
    **dbo.aggregate_sale_by_date_city**, luego ejecute la consulta:

    ```
    --Execute the stored procedure to create the aggregate table.
    EXEC [dbo].[populate_aggregate_sale_by_city];
    ```
> ![A screenshot of a computer Description automatically
> generated](./media/image79.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image80.png)

9.  Para guardar esta consulta, haga clic derecho en la pestaña de la
    consulta ubicada sobre el editor y seleccione **Rename.**

![A screenshot of a computer Description automatically
generated](./media/image81.png)

10. En el cuadro de diálogo **Rename**, en el campo **Name**, ingrese
    +++**Run Create Aggregate Procedure**+++, luego haga clic en
    **Rename**.

![](./media/image82.png)

![A screenshot of a computer Description automatically
generated](./media/image83.png)

11. Seleccione el ícono **Refresh** en la cinta de opciones.

![A screenshot of a computer Description automatically
generated](./media/image84.png)

12. En la pestaña **Object Explorer**, cargue la vista previa de los
    datos para validar que se hayan cargado correctamente, seleccionando
    la tabla **aggregate_sale_by_date_city** en el panel **Explorer.**

![A screenshot of a computer Description automatically
generated](./media/image85.png)

# Ejercicio 6: Viaje en el tiempo mediante T-SQL a nivel de sentencia

1.  En la página **WideWorldImporters**, vaya a la pestaña **Home**,
    seleccione **SQL** en el menú desplegable y haga clic en **New SQL
    query**.

> ![A screenshot of a computer Description automatically
> generated](./media/image86.png)

2.  En el editor de consultas, pegue el siguiente código para crear la
    vista **Top10CustomersView** y seleccione **Run** para ejecutarlo:

    ```
    CREATE VIEW dbo.Top10CustomersView
    AS
    SELECT TOP (10)
        FS.[CustomerKey],
        DC.[Customer],
        SUM(FS.TotalIncludingTax) AS TotalSalesAmount
    FROM
        [dbo].[dimension_customer] AS DC
    INNER JOIN
        [dbo].[fact_sale] AS FS ON DC.[CustomerKey] = FS.[CustomerKey]
    GROUP BY
        FS.[CustomerKey],
        DC.[Customer]
    ORDER BY
        TotalSalesAmount DESC;
    ```

![A screenshot of a computer Description automatically
generated](./media/image87.png)

![A screenshot of a computer Description automatically
generated](./media/image88.png)

3.  En el panel **Explorer**, verifique que la vista
    **Top10CustomersView** se haya creado correctamente expandiendo el
    nodo **View** bajo el esquema **dbo.**

![](./media/image89.png)

4.  Para guardar esta consulta, haga clic derecho en la pestaña de la
    consulta ubicada sobre el editor y seleccione **Rename.**

![A screenshot of a computer Description automatically
generated](./media/image90.png)

5.  En el cuadro de diálogo **Rename**, en el campo **Name**, ingrese
    +++**Top10CustomersView**+++ y haga clic en **Rename**.

![](./media/image91.png)

6.  Cree otra nueva consulta, de manera similar al paso 1. Desde la
    pestaña **Home**, seleccione **New SQL query**.

![A screenshot of a computer Description automatically
generated](./media/image92.png)

7.  En el editor de consultas, pegue el siguiente código para actualizar
    la columna **TotalIncludingTax** a **200000000** para el registro
    con **SaleKey** igual a **22632918**. Seleccione **Run** para
    ejecutarlo:

    ```
    /*Update the TotalIncludingTax value of the record with SaleKey value of 22632918*/
    UPDATE [dbo].[fact_sale]
    SET TotalIncludingTax = 200000000
    WHERE SaleKey = 22632918;
    ```

![A screenshot of a computer Description automatically
generated](./media/image93.png)

![A screenshot of a computer Description automatically
generated](./media/image94.png)

8.  En el editor de consultas, pegue el siguiente código. La función
    **T-SQL CURRENT_TIMESTAMP** devuelve la fecha y hora actual en
    **UTC** como un valor de tipo datetime. Seleccione **Run** para
    ejecutar la consulta:

    ```
    SELECT CURRENT_TIMESTAMP;
    ```

![](./media/image95.png)

9.  Copie el valor de timestamp que se devuelve en su portapapeles.

![A screenshot of a computer Description automatically
generated](./media/image96.png)

10. Pegue el siguiente código en el editor y reemplace el valor del
    timestamp con el obtenido en el paso anterior. El formato de
    timestamp es **YYYY-MM-DDTHH:MM:SS**.

11. Elimine los ceros finales si es necesario, por
    ejemplo: **2025-06-09T06:16:08.807**.

12. Este ejemplo devuelve la lista de los diez principales clientes
    según **TotalIncludingTax**, incluyendo el nuevo valor para
    **SaleKey 22632918**. Pegue el código siguiente y seleccione
    **Run**:

    ```
    /*View of Top10 Customers as of today after record updates*/
    SELECT *
    FROM [WideWorldImporters].[dbo].[Top10CustomersView]
    OPTION (FOR TIMESTAMP AS OF '2025-06-09T06:16:08.807');
    ```

![A screenshot of a computer Description automatically
generated](./media/image97.png)

13. Pegue el siguiente código en el editor y reemplace el valor del
    timestamp con un momento anterior a la ejecución del script de
    actualización de **TotalIncludingTax**. Esto devolverá la lista de
    los diez principales clientes *antes* de la actualización del
    registro **SaleKey** 22632918. Seleccione **Run**:

    ```
    /*View of Top10 Customers as of today before record updates*/
    SELECT *
    FROM [WideWorldImporters].[dbo].[Top10CustomersView]
    OPTION (FOR TIMESTAMP AS OF '2024-04-24T20:49:06.097');
    ```

![A screenshot of a computer Description automatically
generated](./media/image98.png)

# Ejercicio 7: Crear una consulta con visual query builder

## Tarea 1: Usar visual query builder

Cree y guarde una consulta utilizando visual query builder en el portal
de Microsoft Fabric.

1.  En la página **WideWorldImporters**, desde la pestaña **Home** de la
    cinta de opciones, seleccione **New visual query**.

> ![A screenshot of a computer Description automatically
> generated](./media/image99.png)

2.  Haga clic derecho sobre **fact_sale** y seleccione **Insert into
    canvas**.

> ![A screenshot of a computer Description automatically
> generated](./media/image100.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image101.png)

3.  En el panel de diseño de la consulta, en la cinta de
    **Transformations**, limite el tamaño del conjunto de datos haciendo
    clic en el menú desplegable **Reduce rows**, luego seleccione **Keep
    top rows.**

> ![A screenshot of a computer Description automatically
> generated](./media/image102.png)

4.  En el cuadro de diálogo **Keep top rows**, ingrese 10000 y haga clic
    en **OK.**

> ![](./media/image103.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image104.png)

5.  Haga clic derecho sobre **dimension_city** y seleccione **Insert
    into canvas.**

> ![A screenshot of a computer Description automatically
> generated](./media/image105.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image106.png)

6.  En la cinta de **Transformations**, haga clic en el menú desplegable
    junto a **Combine** y seleccione **Merge queries as new**.

> ![A screenshot of a computer Description automatically
> generated](./media/image107.png)

7.  En la página de configuración **Merge settings**, ingrese los
    siguientes valores:

- En **Left table for merge**, seleccione dimension_city

- En **Right table for merge**, seleccione **fact_sale** (use las barras
  de desplazamiento horizontal y vertical si es necesario)

- Seleccione la columna **CityKey** en la tabla **dimension_city**
  haciendo clic en el encabezado de la columna para indicar la columna
  de unión.

- Seleccione la columna **CityKey** en la tabla **fact_sale** de la
  misma manera.

- En el diagrama **Join kind**, seleccione **Inner** y haga clic en
  **Ok**.

> ![A screenshot of a computer Description automatically
> generated](./media/image108.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image109.png)

8.  Con el paso **Merge** seleccionado, haga clic en el botón **Expand**
    junto a **fact_sale** en el encabezado de la cuadrícula de datos,
    luego seleccione las columnas **TaxAmount, Profit,
    TotalIncludingTax** y haga clic en **Ok**.

> ![A screenshot of a computer Description automatically
> generated](./media/image110.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image111.png)

9.  En la cinta de **Transformations**, haga clic en el menú desplegable
    junto a **Transform** y seleccione **Group by**.

> ![A screenshot of a computer Description automatically
> generated](./media/image112.png)

10. En la página de configuración **Group by**, ingrese los siguientes
    valores:

- Seleccione el botón **Advanced.**

- En **Group by**, seleccione las siguientes columnas:

  1.  **Country**

  2.  **StateProvince**

  3.  **City**

- En **New column name**, ingrese **SumOfTaxAmount,** en **Operation**
  seleccione **Sum**, y en **Column** seleccione **TaxAmount**. Haga
  clic en **Add aggregation** para agregar más columnas agregadas.

- En **New column name**, ingrese **SumOfProfit**, en **Operation**
  seleccione **Sum**, y en **Column** seleccione **Profit**. Haga clic
  en **Add aggregation**.

- En **New column name**, ingrese **SumOfTotalIncludingTax**, en
  **Operation** seleccione **Sum**, y en **Column** seleccione
  **TotalIncludingTax.**

- Haga clic en **OK.**

![](./media/image113.png)

![A screenshot of a computer Description automatically
generated](./media/image114.png)

11. En el panel **Explorer**, navegue a **Queries**, haga clic derecho
    sobre **Visual query 1** y seleccione **Rename**.

> ![A screenshot of a computer Description automatically
> generated](./media/image115.png)

12. Ingrese +++**Sales Summary**+++ para cambiar el nombre de la
    consulta. Presione **Enter** o haga clic fuera de la pestaña para
    guardar el cambio.

> ![A screenshot of a computer Description automatically
> generated](./media/image116.png)

13. Haga clic en el ícono **Refresh** debajo de la pestaña **Home**.

> ![A screenshot of a computer Description automatically
> generated](./media/image117.png)

# **Ejercicio 8: Analizar datos con un notebook**

## Tarea 1: Crear un acceso directo a un lakehouse y analizar datos con un notebook

En esta tarea, aprenderá cómo guardar sus datos una vez y luego
utilizarlos en múltiples servicios. También se pueden crear accesos
directos a datos almacenados en Azure Data Lake Storage y S3, lo que le
permite acceder directamente a las tablas delta desde sistemas externos.

Primero, crearemos un nuevo lakehouse. Para ello, siga estos pasos en su
espacio de trabajo de Microsoft Fabric:

1.  En la página **WideWorldImporters**, haga clic en el espacio de
    trabajo **Warehouse_FabricXX** en el menú de navegación izquierdo.

> ![A screenshot of a computer Description automatically
> generated](./media/image118.png)

2.  En la página de inicio **Synapse Data Engineering
    Warehouse_FabricXX**, bajo el panel **Warehouse_FabricXX**, haga
    clic en **+New item** y luego seleccione **Lakehouse** bajo **Stored
    data.**

> ![A screenshot of a computer Description automatically
> generated](./media/image119.png)

3.  En el campo **Name**, ingrese +++ShortcutExercise+++ y haga clic en
    **Create.**

> ![A screenshot of a computer Description automatically
> generated](./media/image120.png)

4.  El nuevo **lakehouse** se carga y se abre la vista **Explorer**,
    mostrando el menú **Get data in your lakehouse**. Bajo **Load data
    in your lakehouse**, seleccione el botón **New shortcut**.

> ![A screenshot of a computer Description automatically
> generated](./media/image121.png)

5.  En la ventana **New shortcut**, seleccione **Microsoft OneLake**.

> ![A screenshot of a computer Description automatically
> generated](./media/image122.png)

6.  En la ventana **Select a data source type**, navegue cuidadosamente
    y haga clic en el Warehouse llamado WideWorldImporters que creó
    previamente, luego haga clic en **Next**.

> ![A screenshot of a computer Description automatically
> generated](./media/image123.png)

7.  En el explorador de objetos **OneLake**, expanda **Tables**, luego
    expanda el esquema **dbo** y seleccione el botón de opción junto a
    **dimension_customer**. Haga clic en **Next**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image124.png)

8.  En la ventana **New shortcut**, haga clic en **Create** y luego en
    **Close**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image125.png)
>
> ![](./media/image126.png)

9.  Espere unos momentos y luego haga clic en el ícono **Refresh**.

10. Seleccione **dimension_customer** en la lista de tablas para
    previsualizar los datos. Observe que el **lakehouse** muestra los
    datos de la tabla **dimension_customer** del **Warehouse**.

> ![](./media/image127.png)

11. A continuación, cree un nuevo **notebook** para consultar la tabla
    **dimension_customer**. En la cinta **Home**, seleccione el menú
    desplegable **Open notebook** y elija **New notebook**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image128.png)

12. Seleccione y arrastre dimension_customer desde la lista de tablas
    hacia la celda del notebook abierto. Se generará automáticamente una
    consulta **PySpark** para consultar todos los datos de
    ShortcutExercise.dimension_customer. Esta experiencia en el notebook
    es similar a la de **Visual Studio Code Jupyter notebook**. También
    puede abrir el **notebook** en **VS Code.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image129.png)

13. En la cinta **Home**, seleccione el botón **Run all**. Una vez
    completada la consulta, verá que puede usar **PySpark** fácilmente
    para consultar las tablas del **Warehouse.**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image130.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image131.png)

# **Ejercicio 9: Crear consultas entre warehouses con el SQL query editor**

## Tarea 1: Agregar múltiples warehouses al Explorer

En esta tarea, aprenderá cómo crear y ejecutar consultas T-SQL en el SQL
query editor utilizando datos de múltiples warehouses, incluyendo la
posibilidad de unir datos entre un SQL Endpoint y un Warehouse en
Microsoft Fabric.

1.  Desde la página **Notebook1**, navegue y haga clic en el espacio de
    trabajo **Warehouse_FabricXX** en el menú de navegación izquierdo.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image132.png)

2.  En la vista **Warehouse_FabricXX**, seleccione el warehouse
    **WideWorldImporters**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image133.png)

3.  En la página de **WideWorldImporters**, en la pestaña **Explorer**,
    seleccione el botón + **Warehouses**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image134.png)

4.  En la ventana **Add warehouses**, seleccione **ShortcutExercise** y
    haga clic en **Confirm**. Ambas experiencias de **warehouse** se
    agregarán al entorno de consulta.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image135.png)

5.  Los warehouses seleccionados ahora aparecen en el mismo panel
    **Explorer.**

![](./media/image136.png)

## Tarea 2: Ejecutar una consulta entre warehouses

En este ejemplo, verá lo fácil que es ejecutar consultas T-SQL entre el
warehouse **WideWorldImporters** y el **SQL Endpoint ShortcutExercise**.
Puede escribir consultas entre bases de datos utilizando nombres de tres
partes para hacer referencia a **database.schema.table**, tal como en
SQL Server.

1.  En la cinta **Home**, seleccione **New SQL query**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image137.png)

2.  En el editor de consultas, copie y pegue el siguiente código T-SQL.
    Seleccione **Run** para ejecutar la consulta. Una vez completada,
    podrá ver los resultados.

    ```
    SELECT Sales.StockItemKey, 
    Sales.Description, 
    SUM(CAST(Sales.Quantity AS int)) AS SoldQuantity, 
    c.Customer
    FROM [dbo].[fact_sale] AS Sales,
    [ShortcutExercise].[dbo].[dimension_customer] AS c
    WHERE Sales.CustomerKey = c.CustomerKey
    GROUP BY Sales.StockItemKey, Sales.Description, c.Customer;
    ```

![](./media/image138.png)

3.  Cambie el nombre de la consulta para referencia futura. En el panel
    **Explorer**, haga clic derecho sobre **SQL query** y seleccione
    **Rename**.

> ![](./media/image139.png)

4.  En el cuadro de diálogo **Rename**, en el campo **Name**, escriba
    +++**Cross-warehouse query**+++, luego haga clic en **Rename**.

> ![](./media/image140.png)

# Ejercicio 10: Crear informes de Power BI

## Tarea 1: Crear un modelo semántico

En esta tarea aprenderá a crear y guardar varios tipos de informes de
Power BI.

1.  En la página **WideWorldImporters**, en la pestaña **Home**,
    seleccione **New semantic model**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image141.png)

2.  En la ventana **New semantic model**, en el cuadro **Direct Lake
    semantic model name**, escriba **+++Sales Model+++**.

3.  Expanda el esquema **dbo**, expanda la carpeta **Tables**, y luego
    marque las tablas **dimension_city** y **fact_sale**. Seleccione
    **Confirm**.

> ![](./media/image142.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image143.png)

9.  En la navegación izquierda, seleccione **Warehouse_FabricXXXXX**,
    como se muestra en la imagen.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image144.png)

10. Para abrir el modelo semántico, vuelva a la página principal del
    espacio de trabajo y luego seleccione el modelo semántico **Sales
    Model**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image145.png)

11. Para abrir el diseñador del modelo, en el menú seleccione **Open
    data model**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image146.png)
>
> ![](./media/image147.png)

12. En la página **Sales Model**, para editar **Manage Relationships**,
    cambie el modo de **Viewing** a **Editing.  
    **![A screenshot of a computer AI-generated content may be
    incorrect.](./media/image148.png)

13. Para crear una relación, en el diseñador del modelo, en la cinta
    **Home**, seleccione **Manage relationships**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image149.png)

14. En la ventana **New relationship**, complete los siguientes pasos
    para crear la relación:

> a\) En la lista desplegable **From table**, seleccione la tabla
> **dimension_city**.  
> b) En la lista desplegable **To table**, seleccione la tabla
> **fact_sale.**  
> c) En la lista desplegable **Cardinality**, seleccione **One to many
> (1:\*).**  
> d) En la lista desplegable **Cross-filter direction**, seleccione
> **Single**.  
> e) Marque la casilla **Assume referential integrity**.  
> f) Seleccione **Save**.
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image150.png)
>
> ![](./media/image151.png)
>
> ![](./media/image152.png)

15. En la ventana **Manage relationships**, seleccione **Close**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image153.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image154.png)

## Tarea 2: Crear un informe de Power BI

En esta tarea, aprenderá a crear un informe de Power BI basado en el
modelo semántico que creó en la tarea anterior.

1.  En la cinta **File**, seleccione **Create new report**.

> ![](./media/image155.png)

2.  En el diseñador de informes, complete los siguientes pasos para
    crear un gráfico de columnas:  
    a) En el panel **Data**, expanda la tabla **fact_sale** y seleccione
    el campo **Profit**.  
    b) En el panel **Data**, expanda la tabla **dimension_city** y
    seleccione el campo **SalesTerritory**.

> ![](./media/image156.png)

3.  En el panel **Visualizations**, seleccione el **visual Azure Map**.

> ![](./media/image157.png)

4.  En el panel **Data**, dentro de la tabla dimension_city, arrastre el
    campo StateProvince al contenedor **Location** en el panel
    **Visualizations**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image158.png)

5.  En el panel **Data**, dentro de la tabla **fact_sale**, seleccione
    el campo **Profit** para agregarlo al contenedor **Size map
    visual**.

6.  En el panel **Visualizations**, seleccione el **visual Table**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image159.png)

7.  En el panel **Data**, seleccione los siguientes campos:

&nbsp;

1)  **SalesTerritory** de la tabla **dimension_city**

2)  **StateProvince** de la tabla **dimension_city**

3)  **Profit** de la tabla **fact_sale**

4)  **TotalExcludingTax** de la tabla **fact_sale**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image160.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image161.png)

8.  Verifique que el diseño final de la página del informe coincida con
    la imagen proporcionada.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image162.png)

9.  Para guardar el informe, en la cinta **Home**, seleccione **File \>
    Save**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image163.png)

10. En la ventana **Save your report**, en el cuadro **Enter a name for
    your report**, ingrese **Sales Analysis** y seleccione **Save**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image164.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image165.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image166.png)

## Tarea 3: Eliminación de recursos

Puede eliminar informes, pipelines, warehouses y otros elementos de
forma individual, o eliminar todo el espacio de trabajo. En este
ejercicio, llevará a cabo la eliminación del espacio de trabajo y de los
informes, pipelines, warehouses y demás elementos creados durante el
laboratorio.

1.  Seleccione **Warehouse_FabricXX** en el menú de navegación para
    volver a la lista de elementos del espacio de trabajo.

> ![](./media/image167.png)

2.  En el menú del encabezado del espacio de trabajo, seleccione
    **Workspace settings**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image168.png)

3.  En el cuadro de diálogo **Workspace settings**, seleccione
    **General** y luego **Remove this workspace**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image169.png)

4.  En el cuadro de diálogo **Delete workspace?,** haga clic en
    **Delete**. ![](./media/image170.png)

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image171.png)

**Resumen**

Este laboratorio integral guía a través de una serie de tareas
destinadas a establecer un entorno de datos funcional en Microsoft
Fabric. Inicia con la creación de un espacio de trabajo, fundamental
para las operaciones de datos, y la habilitación del periodo de prueba.
Posteriormente, se crea un Warehouse denominado WideWorldImporters
dentro del entorno de Fabric, el cual funciona como repositorio central
para el almacenamiento y procesamiento de datos.

A continuación, se detalla la ingesta de datos en el espacio de trabajo
Warehouse_FabricXX mediante la implementación de un pipeline de Data
Factory, cuyo propósito es obtener datos desde orígenes externos e
integrarlos en el espacio de trabajo. Luego se crean las tablas
dimension_city y fact_sale, elementos esenciales para el análisis de
datos dentro del warehouse.

El proceso continúa con la carga de datos a través de T-SQL, trasladando
información desde Azure Blob Storage hacia estas tablas. Las tareas
siguientes se centran en la gestión y transformación de datos. Se
demuestra el proceso de clonación de tablas, una técnica valiosa para
replicar información y realizar pruebas. Asimismo, se ejemplifica cómo
clonar una tabla hacia otro esquema (dbo1), mostrando un enfoque
estructurado para la organización de datos.

El laboratorio avanza hacia la transformación de datos mediante la
creación de un stored procedure para agregar eficientemente información
de ventas. Posteriormente, se introduce el uso del visual query builder,
brindando una interfaz intuitiva para consultas complejas. Esto se
complementa con el uso de notebooks, demostrando su utilidad para
consultar y analizar datos de la tabla dimension_customer.

Luego se presentan las capacidades de consultas entre múltiples
warehouses, habilitando la recuperación de datos desde distintas fuentes
dentro del espacio de trabajo. El laboratorio continúa con la
integración de Azure Maps, mejorando la representación geográfica en
Power BI. Seguidamente, se crean distintos informes de Power BI
incluyendo gráficos de columnas, mapas y tablas para facilitar un
análisis detallado de ventas.

La penúltima tarea aborda la creación de un informe desde OneLake data
hub, destacando la versatilidad de las fuentes de datos en Fabric.
Finalmente, el laboratorio explica los procedimientos de eliminación de
recursos, subrayando la importancia de mantener un entorno ordenado y
eficiente.  
  
En conjunto, estas tareas proporcionan una comprensión completa sobre
cómo configurar, administrar y analizar datos dentro de Microsoft
Fabric.
