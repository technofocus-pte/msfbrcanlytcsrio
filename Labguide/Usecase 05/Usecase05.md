# Caso de uso 04: Crear un data warehouse de ventas y geografía para Contoso en Microsoft Fabric

**Introducción**

Contoso, una empresa minorista multinacional, busca modernizar su
infraestructura de datos para mejorar el análisis de ventas y
geográfico. Actualmente, sus datos de ventas y clientes están dispersos
en varios sistemas, lo que dificulta que sus analistas de negocio y
citizen developers obtengan información relevante. La empresa planea
consolidar estos datos en una plataforma unificada mediante Microsoft
Fabric para permitir realizar consultas cruzadas, análisis de ventas e
informes geográficos.

En este laboratorio, asumirá el rol de ingeniero de datos en Contoso,
encargado de diseñar e implementar una solución de data warehouse
mediante Microsoft Fabric. Comenzará configurando un workspace de
Fabric, creando un data warehouse, cargando datos desde Azure Blob
Storage y realizando tareas analíticas para proporcionar información a
los responsables de la toma de decisiones de Contoso.

Aunque muchos conceptos de Microsoft Fabric pueden resultar familiares
para los profesionales de datos y análisis, puede ser difícil aplicar
estos conceptos en un nuevo entorno. Este laboratorio está diseñado para
guiarle paso a paso a través de un escenario integral, desde la
adquisición de datos hasta su consumo, con el objetivo de proporcionar
una comprensión básica de la experiencia de usuario de Microsoft Fabric,
las distintas experiencias y sus puntos de integración, así como las
experiencias de Microsoft Fabric para profesionales y citizen
developers..

**Objetivos**

- Configurar un workspace de Fabric con la versión de prueba habilitada.

- Crear un nuevo Warehouse denominado WideWorldImporters en Microsoft
  Fabric.

- Cargar datos en el workspace Warehouse_FabricXX mediante un pipeline
  de Data Factory.

- Generar las tablas dimension_city y fact_sale en el data warehouse.

- Rellenar las tablas dimension_city y fact_sale con datos de Azure Blob
  Storage.

- Crear clones de las tablas dimension_city y fact_sale en el Warehouse.

- Clonar las tablas dimension_city y fact_sale en el esquema dbo1.

- Desarrollar un procedimiento almacenado para transformar los datos y
  crear la tabla aggregate_sale_by_date_city.

- Generar una consulta mediante el generador de consultas visual para
  combinar y agregar datos.

- Utilizar un notebook para consultar y analizar datos de la tabla
  dimension_customer.

- Incluir los warehouses WideWorldImporters y ShortcutExercise para
  realizar consultas cruzadas.

- Ejecutar una consulta T-SQL en los warehouses WideWorldImporters y
  ShortcutExercise.

- Habilitar la integración de la visualización Azure Maps en el portal
  de administración.

- Generar visualizaciones de gráfico de columnas, mapa y tabla para el
  informe Sales Analysis.

- Crear un informe utilizando datos del conjunto de datos
  WideWorldImporters en OneLake data hub.

- Eliminar el workspace y los elementos asociados.

## Ejercicio 1: Crear un workspace de Microsoft Fabric

### Tarea 1: Crear un workspace

1.  Abra el navegador, vaya a la barra de direcciones y escriba o pegue
    la siguiente URL: +++https://app.fabric.microsoft.com/+++ y, a
    continuación, presione el botón **Enter**.

\[!note\]**Nota:** Si se le dirige a la página de inicio de Microsoft
Fabric, omita el paso n.º 5.

![](./media/image1.png)

2.  En la ventana de **Microsoft Fabric**, introduzca sus credenciales y
    haga clic en el botón **Submit**.

| Credential | Value |
|---|---|
| Username | +++@lab.CloudPortalCredential(User1).Username+++ |
| Password | +++@lab.CloudPortalCredential(User1).Password+++ |

> ![](./media/image2.png)

3.  A continuación, en la ventana de **Microsoft**, introduzca la
    contraseña y haga clic en el botón **Sign in**.

> ![](./media/image3.png)

4.  En la ventana **Stay signed in?,** haga clic en el botón **Yes**.

5.  Si Power BI se abre de forma predeterminada, siga los pasos que se
    indican a continuación; de lo contrario, omita este paso.

- Haga clic en **Power BI**.

![](./media/image4.png)

- Seleccione Fabric entre las opciones.

![](./media/image5.png)

6.  En la página de inicio de Fabric, seleccione el mosaico **+ New
    workspace**.

![](./media/image6.png)

7.  En la pestaña **Create a workspace**, introduzca los siguientes
    detalles y haga clic en el botón **Apply**.

| Field | Value |
|---|---|
| Name | +++Warehouse_Fabric@lab.LabInstance.Id+++ (must be a unique Id) |
| Description | +++This workspace contains all the artifacts for the data warehouse+++ |
| Advanced Under License mode | Fabric |
| Default storage format | Small dataset storage format |

![](./media/image7.png)

![](./media/image8.png)

![](./media/image9.png)

3.  Espere a que finalice la implementación. El proceso tarda entre 1 y
    2 minutos. Cuando se abra el nuevo workspace, debería estar vacío.

![](./media/image10.png)

### Tarea 2: Crear un Warehouse en Microsoft Fabric

1.  En la página de **Fabric**, seleccione **+ New item** para crear un
    lakehouse y seleccione **Warehouse.**

![A screenshot of a computer Description automatically
generated](./media/image11.png)

2.  En el cuadro de diálogo **New warehouse**, introduzca
    +++**WideWorldImporters**+++ y haga clic en el botón **Create**.

![](./media/image12.png)

3.  Cuando finalice el aprovisionamiento, aparecerá la página de inicio
    del Warehouse **WideWorldImporters**.

![](./media/image13.png)

## Ejercicio 2: Ingerir datos en un Warehouse de Microsoft Fabric

### Tarea 1: Ingerir datos en un Warehouse

1.  Desde la página de inicio del Warehouse **WideWorldImporters**,
    seleccione **Warehouse_FabricXX** en el menú de navegación del lado
    izquierdo para volver a la lista de elementos del workspace.

![](./media/image14.png)

2.  En la página **Warehouse_FabricXX**, seleccione **+ New item**. A
    continuación, haga clic en **Copy job** para ver la lista completa
    de elementos disponibles en **Get data**.

![](./media/image15.png)

3.  En la ventana **New copy job**, en el cuadro **Name**, introduzca
    +++**Load Customer Data**+++. Seleccione **Create.**

> ![](./media/image16.png)

4.  El aprovisionamiento habrá finalizado cuando se abra la página
    **Copy job**.

> ![](./media/image17.png)

5.  En la primera página de la ventana **Copy job**, seleccione **Sample
    data** en la barra de menús de esta página. Para este tutorial,
    utilizaremos el modelo de datos **Retail Data Model del ejemplo Wide
    World Importers**. Seleccione esta opción para ir a la página
    siguiente.

> ![](./media/image18.png)

6.  Se cargará la vista previa de los datos de ejemplo. En la página
    **Choose data**, puede obtener una vista previa del conjunto de
    datos seleccionado. Después de revisar los datos, seleccione
    **Next**.

![](./media/image19.png)

5.  La página **Choose data destination** permite configurar el tipo de
    elemento. En **OneLake catalog**, seleccione su **Warehouse Wide
    World Importers** y, a continuación, seleccione **Next**.

> ![](./media/image20.png)

6.  En la página **Choose copy job mode**, seleccione **Full copy** y, a
    continuación, seleccione **Next**.

> ![](./media/image21.png)

7.  Introduzca las siguientes tablas de destino y, a continuación,
    seleccione **Next**.

- dbo.dimension_city

- dbo.dimension_customer

- dbo.dimension_date

- dbo.dimension_employee

- dbo.dimension_stock_item

- dbo.fact_sale

> ![](./media/image22.png)

8.  En la página **Review + save**, revise el **Source** y el
    **Destination**.

![](./media/image23.png)

9.  Utilice la pestaña **Results** para supervisar la ejecución del Copy
    job.

![](./media/image24.png)

10. Cuando finalice, el **Copy job** mostrará una notificación y un
    estado **Succeeded**. Ahora verá seis tablas nuevas del conjunto de
    datos Wide World Importers en su Warehouse.

![](./media/image25.png)

11. En la página **Load Customer Data**, haga clic en el workspace
    **Warehouse_FabricXX** en la barra de navegación del lado izquierdo
    y seleccione el Warehouse **WideWorldImporters**.

> ![](./media/image26.png)

12. En el Warehouse **WideWorldImporters**, expanda **Schemas \> dbo \>
    Tables** y compruebe que las tablas (**dimension_city**,
    **dimension_customer**, **dimension_date**, **dimension_employee**,
    **dimension_stock_item** y **fact_sale**) se hayan creado
    correctamente.

![](./media/image27.png)

## Ejercicio 3: Clonar una tabla con T-SQL en un Warehouse

### Tarea 1: Clonar una tabla dentro del mismo esquema

1.  En la página **WideWorldImporters**, vaya a la pestaña **Home**,
    seleccione **SQL** en la lista desplegable y haga clic en **New SQL
    query**.

![](./media/image28.png)

3.  En el editor de consultas, pegue el código siguiente. El código crea
    un clon de las tablas **dimension_city** y **fact_sale**.

```
--Create a clone of the dbo.dimension_city table.
 CREATE TABLE [dbo].[dimension_city1] AS CLONE OF [dbo].[dimension_city];

 --Create a clone of the dbo.fact_sale table.
 CREATE TABLE [dbo].[fact_sale1] AS CLONE OF [dbo].[fact_sale];
```

> ![](./media/image29.png)

4.  Para ejecutar la consulta, en la cinta del diseñador de consultas,
    seleccione **Run**.

![](./media/image30.png)

![](./media/image31.png)

5.  En el editor de consultas, pegue el código siguiente. La función
    T-SQL CURRENT_TIMESTAMP devuelve la marca de tiempo UTC actual como
    un valor de tipo **datetime**. Seleccione **Run** para ejecutar la
    consulta.

```
SELECT CURRENT_TIMESTAMP;
```

![](./media/image32.png)

6.  Para crear un clon de una tabla correspondiente a un *past point in
    time*, en el editor de consultas, pegue el código siguiente **para
    reemplazar las instrucciones existentes**. El código crea un clon de
    las tablas dimension_city y fact_sale en un momento determinado.
    Ejecute la consulta.

```
--Create a clone of the dbo.dimension_city table at a specific point in time.   
CREATE TABLE [dbo].[dimension_city2] AS CLONE OF [dbo].[dimension_city] AT '2025-01-01T10:00:00.000';

 --Create a clone of the dbo.fact_sale table at a specific point in time.
CREATE TABLE [dbo].[fact_sale2] AS CLONE OF [dbo].[fact_sale] AT '2025-01-01T10:00:00.000';
```

![](./media/image33.png)

![](./media/image34.png)

7.  Cambie el nombre de la consulta a +++**Clone Tables+++**.

> ![](./media/image35.png)
>
> ![](./media/image36.png)

### Tarea 2: Clonar una tabla entre esquemas dentro del mismo Warehouse

En esta tarea, aprenderá a clonar una tabla entre esquemas dentro del
mismo Warehouse.

1.  Para crear una nueva consulta, en la cinta **Home**, seleccione
    **New SQL query**.

> ![](./media/image37.png)

2.  En el editor de consultas, pegue el código siguiente. El código crea
    un esquema y, a continuación, crea clones de las tablas
    **fact_sale** y **dimension_city** en el nuevo esquema. Ejecute la
    consulta.

```
--Create a new schema within the warehouse named dbo1.
 CREATE SCHEMA dbo1;
 GO

 --Create a clone of dbo.fact_sale table in the dbo1 schema.
 CREATE TABLE [dbo1].[fact_sale1] AS CLONE OF [dbo].[fact_sale];

 --Create a clone of dbo.dimension_city table in the dbo1 schema.
 CREATE TABLE [dbo1].[dimension_city1] AS CLONE OF [dbo].[dimension_city];
```

![](./media/image38.png)

3.  Cuando finalice la ejecución, obtenga una vista previa de los datos
    cargados en la tabla **dimension_city1** del esquema **dbo1**.

> ![](./media/image39.png)

4.  Para crear clones de las tablas correspondientes a un *previous
    point in time*, en el editor de consultas, pegue el código siguiente
    para **reemplazar las instrucciones existentes**. El código crea un
    clon de las tablas **dimension_city** y **fact_sale** en
    determinados momentos del pasado en el nuevo esquema. Ejecute la
    consulta.

```
--Create a clone of the dbo.dimension_city table in the dbo1 schema.
CREATE TABLE [dbo1].[dimension_city2] AS CLONE OF [dbo].[dimension_city] AT '2025-01-01T10:00:00.000';

--Create a clone of the dbo.fact_sale table in the dbo1 schema.
CREATE TABLE [dbo1].[fact_sale2] AS CLONE OF [dbo].[fact_sale] AT '2025-01-01T10:00:00.000';
```

 ![](./media/image40.png)

5.  Cuando finalice la ejecución, obtenga una vista previa de los datos
    cargados en la tabla **fact_sale2** del esquema **dbo1**.

> ![](./media/image41.png)

6.  Cambie el nombre de la consulta a +++**Clone Tables Across Schemas**+++.

> ![](./media/image42.png)
>
> ![](./media/image43.png)

## Ejercicio 4: Transformar datos mediante un procedimiento almacenado

### Tarea 1: Crear un procedimiento almacenado

En esta tarea, aprenderá a crear un procedimiento almacenado para
transformar datos en una tabla del Warehouse.

1.  En la página **WideWorldImporters**, vaya a la pestaña **Home**,
    seleccione **SQL** en la lista desplegable y haga clic en **New SQL
    query**.

![](./media/image44.png)

2.  En el editor de consultas, pegue el código siguiente. El código
    elimina el procedimiento almacenado (si existe) y, a continuación,
    crea un procedimiento almacenado denominado
    **populate_aggregate_sale_by_city**. La lógica del procedimiento
    almacenado crea una tabla denominada **aggregate_sale_by_date_city**
    e inserta datos en ella mediante una consulta **GROUP BY** que
    combina las tablas **fact_sale** y **dimension_city**.

```
--Drop the stored procedure if it already exists.
 DROP PROCEDURE IF EXISTS [dbo].[populate_aggregate_sale_by_city];
 GO

 --Create the populate_aggregate_sale_by_city stored procedure.
 CREATE PROCEDURE [dbo].[populate_aggregate_sale_by_city]
 AS
 BEGIN
     --Drop the aggregate table if it already exists.
     DROP TABLE IF EXISTS [dbo].[aggregate_sale_by_date_city];
     --Create the aggregate table.
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

     --Load aggregated data into the table.
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
 END;
```
> ![](./media/image45.png)

3.  Para ejecutar la consulta, en la cinta del diseñador de consultas,
    seleccione **Run**.

> ![](./media/image46.png)

4.  Cuando finalice la ejecución, cambie el nombre de la consulta a
    +++**Create Aggregate Procedure**+++.

> ![A screenshot of a computer Description automatically
> generated](./media/image47.png)
>
> ![](./media/image48.png)

5.  En el panel **Explorer**, dentro de la carpeta **Stored Procedures**
    del esquema **dbo**, compruebe que exista el procedimiento
    almacenado **aggregate_sale_by_date_city**.

![](./media/image49.png)

### Tarea 2: Ejecutar el procedimiento almacenado

En esta tarea, aprenderá a ejecutar el procedimiento almacenado para
transformar datos en una tabla del Warehouse.

1.  En la página **WideWorldImporters**, vaya a la pestaña **Home**,
    seleccione SQL en la lista desplegable y haga clic en **New SQL
    query**.

> ![](./media/image50.png)

2.  En el editor de consultas, pegue el código siguiente. El código
    ejecuta el procedimiento almacenado
    **populate_aggregate_sale_by_city**. Ejecute la consulta.

```
--Execute the stored procedure to create and load aggregated data.
 EXEC [dbo].[populate_aggregate_sale_by_city];
```

![](./media/image51.png)

3.  Cuando finalice la ejecución, cambie el nombre de la consulta a
    +++**Run Aggregate Procedure**+++.

> ![](./media/image52.png)
>
> ![](./media/image53.png)

4.  Para obtener una vista previa de los datos agregados, en el panel
    **Explorer**, seleccione la tabla **aggregate_sale_by_date_city**.

> ![](./media/image54.png)

** Nota:** Si la tabla no aparece, seleccione los puntos suspensivos (…)
de la carpeta **Tables** y, a continuación, seleccione **Refresh**.

##  Ejercicio 5: Utilizar time travel mediante T-SQL a nivel de instrucción

### Tarea 1: Trabajar con consultas de time travel

En esta tarea, aprenderá a crear una vista de los 10 principales
clientes por ventas. Utilizará la vista en la siguiente tarea para
ejecutar consultas de time travel.

1.  En la página **WideWorldImporters**, vaya a la pestaña **Home**,
    seleccione SQL en la lista desplegable y haga clic en **New SQL
    query**.

![](./media/image55.png)

2.  En el editor de consultas, pegue el código siguiente. El código crea
    una vista denominada Top10Customers. La vista utiliza una consulta
    para recuperar los 10 principales clientes según las ventas.
    Seleccione **Run** para ejecutar la consulta.

```
--Create the Top10Customers view.
CREATE VIEW [dbo].[Top10Customers]
AS
SELECT TOP(10)
    FS.[CustomerKey],
    DC.[Customer],
    SUM(FS.[TotalIncludingTax]) AS [TotalSalesAmount]
FROM
    [dbo].[dimension_customer] AS DC
    INNER JOIN [dbo].[fact_sale] AS FS
        ON DC.[CustomerKey] = FS.[CustomerKey]
GROUP BY
    FS.[CustomerKey],
    DC.[Customer]
ORDER BY
    [TotalSalesAmount] DESC;
```
> ![](./media/image56.png)

3.  Cuando finalice la ejecución, cambie el nombre de la consulta a
    +++**Create Top 10 Customer View**+++.

![](./media/image57.png)

![](./media/image58.png)

3.  En **Explorer**, compruebe que puede ver la vista recién creada
    **Top10CustomersView** expandiendo el nodo Views del esquema
    **dbo**.

![](./media/image59.png)

4.  Cree otra consulta nueva, como en el paso 1. En la pestaña **Home**
    de la cinta, seleccione **New SQL query**.

> ![](./media/image60.png)

5.  En el editor de consultas, pegue el código siguiente. El código
    actualiza el valor **TotalIncludingTax** de una sola fila de fact
    para inflar deliberadamente sus ventas totales. También recupera la
    marca de tiempo actual.

```
--Update the TotalIncludingTax for a single fact row to deliberately inflate its total sales.
 UPDATE [dbo].[fact_sale]
 SET [TotalIncludingTax] = 200000000
 WHERE [SaleKey] = 22632918; --For customer 'Tailspin Toys (Muir, MI)'
 GO

 --Retrieve the current (UTC) timestamp.
 SELECT CURRENT_TIMESTAMP;
```
![](./media/image61.png)

6.  Copie el valor de la marca de tiempo devuelto al portapapeles.

![](./media/image62.png)

**Nota:** Actualmente, solo puede utilizar la zona horaria **Coordinated
Universal Time (UTC)** para **time travel**.

7.  Cuando finalice la ejecución, cambie el nombre de la consulta a
    +++**Time Travel**+++.

![](./media/image63.png)

![](./media/image64.png)

8.  Pegue el código siguiente en el editor de consultas y reemplace el
    valor de la marca de tiempo por el valor de la marca de tiempo
    actual obtenido en el paso anterior. El formato de sintaxis de la
    marca de tiempo es **YYYY-MM-DDTHH:MM:SS**.

9.  Elimine los ceros finales, por ejemplo: **2026-07-27T06:20:55.823**.

&nbsp;

10. Para recuperar los 10 principales clientes *a partir de ahora*, en
    un nuevo editor de consultas, pegue la siguiente instrucción. El
    código recupera los 10 principales clientes mediante la sugerencia
    de consulta **FOR TIMESTAMP AS OF**.

11. Reemplace YOUR_TIMESTAMP por la marca de tiempo que copió al
    portapapeles.

```
--Retrieve the top 10 customers as of now.
 SELECT *
 FROM [dbo].[Top10Customers]
 OPTION (FOR TIMESTAMP AS OF 'YOUR_TIMESTAMP');
```

![](./media/image65.png)

12. Cambie el nombre de la consulta a+++**Time Travel Now+++**

> ![](./media/image66.png)
>
> ![](./media/image67.png)

13. Observe que el segundo valor de **CustomerKey** de los principales
    clientes es **49** para Tailspin Toys (Muir, MI).

> ![](./media/image68.png)

14. Modifique el valor de la marca de tiempo a una hora anterior
    **s*ubtracting one minute*** de la marca de tiempo.

15. Vuelva a ejecutar la consulta y observe que el segundo **valor de**
    **CustomerKey** de los principales clientes es **381** para
    **Wingtip Toys (Sarversville, PA)**.

## Ejercicio 6: Crear una consulta con el generador de consultas visual en un Warehouse

### Tarea 1: Utilizar el generador de consultas visual

En esta tarea, aprenderá a crear una consulta con el generador de
consultas visual.

1.  En la cinta **Home**, abra la lista desplegable **New SQL query** y,
    a continuación, seleccione **New visual query**.

![](./media/image69.png)

2.  En el panel **Explorer**, desde la carpeta Tables del esquema
    **dbo**, arrastre la tabla **fact_sale** al lienzo de la consulta
    visual.

![](./media/image70.png)

3.  Vaya a la cinta Transformations del panel **Query design** y limite
    el tamaño del conjunto de datos haciendo clic en la lista
    desplegable **Reduce rows** y, a continuación, en **Keep top rows**,
    como se muestra en la imagen siguiente.

![](./media/image71.png)

4.  En el cuadro de diálogo **Keep top rows**, introduzca
    +++**10000**+++ y seleccione **OK.**

![](./media/image72.png)

![](./media/image73.png)

5.  En el panel **Explorer**, desde la carpeta Tables del esquema
    **dbo**, arrastre la tabla **dimension_city** al lienzo de la
    consulta visual.

6.  Haga clic con el botón derecho en **dimension_city** y seleccione
    **Insert into canvas**.

> ![](./media/image74.png)

![](./media/image75.png)

6.  En la cinta **Transformations**, seleccione la lista desplegable
    situada junto a **Combine** y seleccione **Merge queries as new**,
    como se muestra en la imagen siguiente.

![](./media/image76.png)

7.  En la página de configuración **Merge**, introduzca los siguientes
    detalles.

- En la lista desplegable **Left table for merge**, seleccione
  **dimension_city**.

- En la lista desplegable **Right table for merge**, seleccione
  **fact_sale** (utilice las barras de desplazamiento horizontal y
  vertical).

- Seleccione el campo **CityKey** en la tabla **dimension_city**
  seleccionando el nombre de la columna en la fila de encabezado para
  indicar la columna de combinación.

- Seleccione el campo **CityKey** en la **tabla fact_sale**
  seleccionando el nombre de la columna en la fila de encabezado para
  indicar la columna de combinación.

- En la selección del diagrama **Join kind**, seleccione **Inner** y
  haga clic en el botón **OK.**

![](./media/image77.png)

![](./media/image78.png)

8.  Con el paso **Merge** seleccionado, seleccione el botón **Expand**
    situado junto a **fact_sale** en el encabezado de la cuadrícula de
    datos, como se muestra en la imagen siguiente. A continuación,
    seleccione las columnas **TaxAmount**, **Profit**,
    **TotalIncludingTax** y seleccione **OK.**

![](./media/image79.png)

![](./media/image80.png)

![](./media/image81.png)

9.  En la cinta **Transformations,** haga clic en la lista desplegable
    situada junto a **Transform** y, a continuación, seleccione **Group
    by**.

![](./media/image82.png)

10. En la página de configuración **Group by**, introduzca los
    siguientes detalles.

- Seleccione el botón de opción **Advanced**.

- En **Group by**, seleccione lo siguiente:

  1.  **Country**

  2.  **StateProvince**

  3.  **City**

- En **New column name**, introduzca **SumOfTaxAmount.** En el campo
  **Operation**, seleccione **Sum** y, a continuación, en el campo
  **Column**, seleccione **TaxAmount**. Haga clic en **Add aggregation**
  para agregar otra columna y operación de agregación.

- En **New column name**, introduzca **SumOfProfit.** En el campo
  **Operation**, seleccione **Sum** y, a continuación, en el campo
  **Column**, seleccione **Profit**. Haga clic en **Add aggregation**
  para agregar otra columna y operación de agregación.

- En **New column name**, introduzca **SumOfTotalIncludingTax**. En el
  campo Operation, seleccione **Sum** y, a continuación, en el campo
  **Column**, seleccione **TotalIncludingTax.** 

- Haga clic en el botón **OK**.

![](./media/image83.png)

![](./media/image84.png)

11. En **Explorer,** vaya a **Queries** y haga clic con el botón derecho
    en **Visual query 1** dentro de **Queries.** A continuación,
    seleccione **Rename.**

![](./media/image85.png)

12. Escriba +++**Sales Summary**+++ para cambiar el nombre de la
    consulta. Presione Enter en el teclado o seleccione cualquier lugar
    fuera de la pestaña para guardar el cambio.

![](./media/image86.png)

13. Haga clic en el icono **Refresh** situado debajo de la pestaña
    **Home**.

![A screenshot of a computer Description automatically
generated](./media/image87.png)

## Ejercicio 7: Analizar datos con un notebook

### Tarea 1: Crear un notebook de T-SQL

En esta tarea, aprenderá a crear un notebook de T-SQL.

1.  En la cinta **Home**, abra la lista desplegable **New SQL query** y,
    a continuación, seleccione **New SQL query in notebook**.

> ![](./media/image88.png)

2.  En el panel **Explorer**, seleccione **Warehouses** para mostrar los
    objetos del Warehouse **WideWorldImporters**.

3.  Para generar una plantilla SQL para explorar los datos, a la derecha
    de la tabla **dimension_city**, seleccione los puntos suspensivos
    **(…)** y, a continuación, seleccione **SELECT TOP 100**.

> ![](./media/image89.png)

4.  Para ejecutar el código T-SQL de esta celda, seleccione el botón
    **Run cell** correspondiente a la celda de código.

> ![](./media/image90.png)

5.  Revise el resultado de la consulta en el panel **results**.

> ![](./media/image91.png)

### Tarea 2: Crear un acceso directo a un lakehouse y analizar datos con un notebook

En esta tarea, aprenderá a crear un acceso directo a un lakehouse y
analizar datos con un notebook.

1.  En el menú de la izquierda, seleccione el icono del workspace
    **Warehouse_Fabric65897@lab.labinstance.id** y, a continuación,
    seleccione el nombre del workspace.

> ![](./media/image92.png)

2.  Seleccione **+ New item** para mostrar la lista completa de tipos de
    elementos disponibles.

3.  En la lista, en la sección **Store data**, seleccione el tipo de
    elemento **Lakehouse**.

> ![](./media/image93.png)

4.  Cuando finalice el aprovisionamiento del lakehouse, introduzca
    +++**Shortcut_Exercise**+++ como nombre del lakehouse y desmarque
    **Lakehouse schemas**. Seleccione
    **Create**.![](./media/image94.png)

> ![](./media/image95.png)

5.  Cuando se abra el nuevo lakehouse, en la página de inicio,
    seleccione la opción **New shortcut**.

> ![](./media/image96.png)

6.  En la ventana **New shortcut**, seleccione la opción **Microsoft
    OneLake**.

> ![](./media/image97.png)

7.  En la ventana **Select a data source type**, seleccione el
    **Warehouse Wide World Importers** y, a continuación, seleccione
    **Next**.

> ![](./media/image98.png)

8.  Haga clic en **Connect**.

> ![](./media/image99.png)

9.  En **OneLake object browser**, expanda **Tables**, expanda el
    esquema **dbo** y, a continuación, seleccione la casilla de
    verificación de la tabla **dimension_customer**. Seleccione
    **Next**.

> ![](./media/image100.png)

10. Seleccione **Create**.

> ![](./media/image101.png)

11. En el panel **Explorer**, seleccione la tabla **dimension_customer**
    para obtener una vista previa de los datos y, a continuación, revise
    los datos recuperados de la tabla **dimension_customer** en el
    **Warehouse**.

> ![](./media/image102.png)

12. En la página de la tabla **dimension_customer**, haga clic en
    **Analyze data with**, seleccione **Notebook** y, a continuación,
    elija **New notebook** para crear un nuevo **notebook de Spark**
    para el análisis de datos.

> ![](./media/image103.png)

13. En el panel **Explorer,** seleccione **Lakehouses.**

14. Arrastre la tabla **dimension_customer** a la celda abierta del
    notebook.

> ![](./media/image104.png)

15. Observe la consulta de **PySpark** que se agregó a la celda del
    notebook. Esta consulta recupera las primeras **1,000** filas del
    acceso directo **Shortcut_Exercise.dimension_customer**. Esta
    experiencia de notebook es similar a la experiencia de Jupyter
    notebook de **Visual Studio Code**. También puede abrir el notebook
    en **VS Code**.

> ![](./media/image105.png)

16. En la cinta **Home**, seleccione el botón **Run all.**

> ![](./media/image106.png)
>
> ![](./media/image107.png)

## Ejercicio 8: Crear consultas entre warehouses con el editor de consultas SQL

### Tarea 1: Agregar varios warehouses al Explorer

En esta tarea, aprenderá cómo crear y ejecutar fácilmente consultas
T-SQL con el editor de consultas SQL en varios warehouses, incluida la
combinación de datos de un SQL Endpoint y un Warehouse en Microsoft
Fabric.

1.  Desde la página **Notebook2**, vaya al workspace
    **WideWorldImporters** y haga clic en él en el menú de navegación
    del lado izquierdo.

> ![](./media/image108.png)

2.  En el panel **Explorer**, seleccione **+ Warehouses**.

![](./media/image109.png)

3.  En la ventana **OneLake catalog**, seleccione **Shortcut_Exercise
    SQL analytics endpoint**. Seleccione **Confirm**.

![](./media/image110.png)

4.  En el panel **Explorer,** observe que **Shortcut_Exercise SQL
    analytics endpoint** está disponible.

![](./media/image111.png)

### Tarea 2: Ejecutar la consulta entre warehouses

En esta tarea, aprenderá a ejecutar una consulta entre warehouses. En
concreto, ejecutará una consulta que combina el Warehouse Wide World
Importers con el Shortcut_Exercise SQL analytics endpoint.

** Nota:** Una consulta entre bases de datos utiliza una nomenclatura de
tres partes, *database.schema.table*, para hacer referencia a los
objetos.

1.  En la pestaña **Home** de la cinta, seleccione **New SQL query**.

![](./media/image112.png)

2.  En el editor de consultas, pegue el código siguiente. El código
    recupera una agregación de la cantidad vendida por artículo de
    inventario, descripción y cliente.

```
--Retrieve an aggregate of quantity sold by stock item, description, and customer.
SELECT
    Sales.StockItemKey,
    Sales.Description,
    c.Customer,
    SUM(CAST(Sales.Quantity AS int)) AS SoldQuantity
FROM
    [dbo].[fact_sale] AS Sales
    INNER JOIN [Shortcut_Exercise].[dbo].[dimension_customer] AS c
        ON Sales.CustomerKey = c.CustomerKey
GROUP BY
    Sales.StockItemKey,
    Sales.Description,
    c.Customer;
```
3.  **Ejecute** y revise el resultado de la consulta**.**

![](./media/image113.png)

![](./media/image114.png)

3.  Cambie el nombre de la consulta para facilitar su referencia. Haga
    clic con el botón derecho en **SQL query** en **Explorer** y
    seleccione **Rename**.

> ![](./media/image115.png)

![](./media/image116.png)

4.  En el cuadro de diálogo **Rename**, en el campo **Name**, introduzca
    +++**Cross-warehouse query**+++ y, a continuación, haga clic en el
    botón **Rename**. 

> ![](./media/image117.png)

## Ejercicio 9: Crear un modelo semántico Direct Lake y un informe de Power BI

### Tarea 1: Crear un modelo semántico

En esta tarea, aprenderá a crear un modelo semántico Direct Lake basado
en el Warehouse Wide World Importers.

1.  En la página **WideWorldImporters**, en la pestaña **Home,**
    seleccione **New semantic model**.

![](./media/image118.png)

2.  En la ventana **New semantic model**, en el cuadro **Direct Lake
    semantic model name**, introduzca +++**Sales Model**+++.

3.  Expanda el esquema **dbo**, expanda la carpeta **Tables** y, a
    continuación, seleccione las casillas de verificación de las tablas
    **dimension_city** y **fact_sale**. Seleccione **Confirm.**

> ![](./media/image119.png)

9.  En la navegación de la izquierda, seleccione
    ***Warehouse_FabricXXXXX***, como se muestra en la imagen siguiente.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image120.png)

10. Para abrir el modelo semántico, vuelva a la página de inicio del
    workspace y, a continuación, seleccione el modelo semántico **Sales
    Model**.

![](./media/image121.png)

![](./media/image122.png)

12. En la página **Sales Model**, para editar **Manage Relationships**,
    cambie el modo de **Viewing a Editing**. ![A screenshot of a
    computer AI-generated content may be
    incorrect.](./media/image123.png)

13. Para crear una relación, en el diseñador de modelos, en la cinta
    **Home**, seleccione **Manage relationships**.

![](./media/image124.png)

14. En la ventana **Manage relationship**, seleccione **+ New
    relationship**.

![](./media/image125.png)

14. En la ventana **New relationship**, complete los siguientes pasos
    para crear la relación:

-  En la lista desplegable **From table**, seleccione la tabla
  **dimension_city**.

- En la lista desplegable **To table**, seleccione la tabla
  **fact_sale**.

- En la lista desplegable **Cardinality**, seleccione **One to many
  (1:\*).**

- En la lista desplegable **Cross-filter direction**, seleccione
  **Single**.

- Seleccione la casilla **Assume referential integrity**.

- Seleccione **Save**.

![](./media/image126.png)

![](./media/image127.png)

15. En la ventana **Manage relationship**, seleccione **Close.**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image128.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image129.png)

### Tarea 2: Crear un informe de Power BI

En esta tarea, aprenderá a crear un informe de Power BI basado en el
modelo semántico que creó en la tarea anterior.

1.  En la cinta **File**, seleccione **Create new report**.

![](./media/image130.png)

2.  En el diseñador de informes, complete los siguientes pasos para
    crear una visualización de gráfico de columnas:

-  En el panel **Data**, expanda la tabla **fact_sale** y, a
  continuación, seleccione el campo **Profit**.

- En el panel **Data**, expanda la tabla **dimension_city** y, a
  continuación, seleccione el campo **SalesTerritory**.

![](./media/image131.png)

3.  En el panel **Visualizations**, seleccione la visualización **Azure
    Map**.

![](./media/image132.png)

4.  En el panel **Data**, dentro de la tabla *dimension_city*, arrastre
    el campo **StateProvince** al área **Location** del panel
    **Visualizations**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image133.png)

5.  En el panel **Data**, dentro de la tabla **fact_sale**, seleccione
    el campo **Profit** para agregarlo al área **Size** de la
    **visualización de mapa**.

6.  En el panel **Visualizations**, seleccione la visualización
    **Table**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image134.png)

7.  En el panel **Data**, seleccione los siguientes campos:

-  SalesTerritory de la tabla dimension_city

- StateProvince de la tabla dimension_city

- Profit de la tabla fact_sale

- TotalExcludingTax de la tabla fact_sale![A screenshot of a computer
  AI-generated content may be incorrect.](./media/image135.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image136.png)

8.  Compruebe que el diseño final de la página del informe sea similar a
    la siguiente imagen.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image137.png)

9.  Para guardar el informe, en la cinta **Home**, seleccione **File \>
    Save**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image138.png)

10. En la ventana **Save your report**, en el cuadro **Enter a name for
    your report**, introduzca +++**Sales Analysis**+++ y seleccione
    **Save**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image139.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image140.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image141.png)

### Tarea 3: Eliminar los recursos

Puede eliminar informes, pipelines, warehouses y otros elementos
individuales, o eliminar todo el workspace. En este tutorial, limpiará
el workspace, los informes, pipelines, warehouses y otros elementos que
creó como parte del laboratorio.

1.  Seleccione **Warehouse_FabricXX** en el menú de navegación para
    volver a la lista de elementos del workspace.

![](./media/image142.png)

2.  En el menú del encabezado del workspace, seleccione **Workspace
    settings**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image143.png)

3.  En el cuadro de diálogo **Workspace settings**, seleccione
    **General** y seleccione **Remove this workspace**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image144.png)

4.  En el cuadro de diálogo **Delete workspace?,** haga clic en el botón
    **Delete**.![](./media/image145.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image146.png)

**Resumen**

Este completo laboratorio guía a través de una serie de tareas
destinadas a establecer un entorno de datos funcional en Microsoft
Fabric. Comienza con la creación de un workspace, esencial para las
operaciones de datos, y garantiza que la versión de prueba esté
habilitada. Posteriormente, se crea un Warehouse denominado
WideWorldImporters dentro del entorno de Fabric para servir como
repositorio central de almacenamiento y procesamiento de datos. A
continuación, se detalla la ingesta de datos en el workspace
Warehouse_FabricXX mediante la implementación de un pipeline de Data
Factory. Este proceso implica obtener datos de fuentes externas e
integrarlos de forma fluida en el workspace.

Se crean tablas fundamentales, dimension_city y fact_sale, en el data
warehouse para servir como estructuras base para el análisis de datos.
El proceso de carga de datos continúa mediante T-SQL, donde los datos de
Azure Blob Storage se transfieren a las tablas especificadas.

Las tareas posteriores profundizan en la administración y manipulación
de datos. Se muestra cómo clonar tablas, una técnica útil para la
replicación y las pruebas de datos. Además, el proceso de clonación se
extiende a un esquema diferente (dbo1) dentro del mismo Warehouse,
mostrando un enfoque estructurado para la organización de los datos.

El laboratorio continúa con la transformación de datos mediante la
creación de un procedimiento almacenado para agregar datos de ventas de
forma eficiente. A continuación, se utiliza el generador de consultas
visual para proporcionar una interfaz intuitiva para consultas
complejas. Esto es seguido por una exploración de notebooks, que
demuestra su utilidad para consultar y analizar datos de la tabla
dimension_customer.

A continuación, se presentan las capacidades de consulta entre varios
warehouses, lo que permite recuperar datos de forma fluida entre
diferentes warehouses dentro del workspace. El laboratorio culmina con
la habilitación de la integración de visualizaciones de Azure Maps,
mejorando la representación de datos geográficos en Power BI.
Posteriormente, se crean diversos informes de Power BI, incluidos
gráficos de columnas, mapas y tablas, para facilitar un análisis
detallado de los datos de ventas.

La tarea final se centra en generar un informe a partir de OneLake data
hub, destacando aún más la versatilidad de las fuentes de datos en
Fabric. Finalmente, el laboratorio proporciona información sobre la
administración de recursos y destaca la importancia de los
procedimientos de limpieza para mantener un workspace eficiente.

En conjunto, estas tareas proporcionan una comprensión integral de cómo
configurar, administrar y analizar datos en Microsoft Fabric.
