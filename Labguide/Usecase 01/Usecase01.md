# Caso de uso 1: Crear un Lakehouse, ingerir datos de muestra y crear un informe

**Introducción**

Este laboratorio lo guía a través de un escenario completo de extremo a
extremo, desde la adquisición hasta el consumo de datos. Su propósito es
ayudarle a desarrollar una comprensión básica de Fabric, incluidas sus
distintas experiencias y la forma en que se integran, así como los roles
y dinámicas tanto de los desarrolladores profesionales como de los
desarrolladores ciudadanos que trabajan en esta plataforma. Este
laboratorio no pretende funcionar como una arquitectura de referencia,
una lista exhaustiva de características y funcionalidades, ni una
recomendación de prácticas específicas.

Tradicionalmente, las organizaciones han construido data warehouses
modernos para satisfacer sus necesidades de análisis de datos
transaccionales y estructurados, y data lakehouses para cubrir los
análisis de big data (datos semiestructurados y no estructurados). Estos
dos sistemas han operado en paralelo, generando silos, duplicidad de
datos y un mayor costo total de propiedad.

Fabric, gracias a la unificación del almacenamiento de datos y su
estandarización en el formato Delta Lake, permite eliminar los silos,
reducir la duplicidad de datos y disminuir de forma considerable el
costo total de propiedad.

Con la flexibilidad que ofrece Fabric, puede implementar arquitecturas
de lakehouse o de data warehouse, o combinarlas para obtener lo mejor de
ambas con una implementación sencilla. En este tutorial, tomará como
ejemplo una organización minorista y creará su lakehouse de principio a
fin. Utiliza la [arquitectura de
medallones](https://learn.microsoft.com/en-us/azure/databricks/lakehouse/medallion),
donde la capa **Bronze** contiene los datos sin procesar, la capa
**Silver** contiene los datos validados y desduplicados, y la capa
**Gold** contiene los datos altamente refinados.  
  
Puede aplicar el mismo enfoque para implementar un lakehouse en
cualquier organización de cualquier industria.

Este laboratorio explica cómo un desarrollador de la empresa ficticia
Wide World Importers, del sector minorista, completa los siguientes
pasos.

**Objetivos**:

1.  Iniciar sesión en la cuenta de Power BI e iniciar una prueba
    gratuita de Microsoft Fabric.

2.  Iniciar la prueba de **Microsoft Fabric (Vista previa)** dentro de
    Power BI.

3.  Configurar el registro de OneDrive en el centro de administración de
    Microsoft 365.

4.  Crear e implementar un lakehouse de extremo a extremo para la
    organización, incluyendo la creación de un espacio de trabajo de
    Fabric y un lakehouse.

5.  Ingerir datos de muestra en el lakehouse y prepararlos para su
    posterior procesamiento.

6.  Transformar y preparar los datos utilizando notebooks de
    Python/PySpark y SQL.

7.  Crear tablas de agregados de negocio utilizando diferentes enfoques.

8.  Establecer relaciones entre tablas para generar informes de forma
    fluida.

9.  Crear un informe de Power BI con visualizaciones basadas en los
    datos preparados.

10. Guardar y almacenar el informe creado para referencia y análisis
    futuros.

## Ejercicio 1: Configurar un escenario de Lakehouse de extremo a extremo

### Tarea 1: Iniciar sesión en la cuenta de Power BI y registrarse en la prueba gratuita de Microsoft Fabric

1.  Abra su navegador, vaya a la barra de direcciones y escriba o pegue
    la siguiente URL:+++https://app.fabric.microsoft.com/+++ y luego
    presione el botón **Enter**.

> ![](./media/image1.png)

2.  En la ventana de **Microsoft Fabric**, ingrese sus credenciales y
    haga clic en el botón **Submit**.
    |   |   |
    |---|---|
    | Username | +++@lab.CloudPortalCredential(User1).Username+++ |
    | Password | +++@lab.CloudPortalCredential(User1).Password+++ |

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image2.png)

3.  Luego, en la ventana de **Microsoft**, ingrese la contraseña y haga
    clic en el botón **Sign in**.

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image3.png)

4.  En la ventana **Stay signed in?,** haga clic en el botón **Yes**.

> ![](./media/image4.png)

5.  Será dirigido a la página principal de Power BI.

> ![](./media/image5.png)

## Ejercicio 2: Crear e implementar un lakehouse de extremo a extremo para su organización

### Tarea 1: Crear un espacio de trabajo de Fabric

En esta tarea, creará un espacio de trabajo de Fabric. El espacio de
trabajo contiene todos los elementos necesarios para este tutorial de
lakehouse, lo que incluye el lakehouse, los dataflows, los pipelines de
Data Factory, los notebooks, los datasets de Power BI y los informes.

1.  En la página principal de Fabric, seleccione el mosaico **+New
    workspace**.

> ![A screenshot of a computer Description automatically
> generated](./media/image6.png)

2.  En el panel **Create a workspace**, que aparece en el lado derecho,
    ingrese los siguientes detalles y haga clic en el botón **Apply**:

    | Property  | Value  |
    |-------|-----|
    |Name|	**+++Fabric Lakehouse Tutorial-@lab.LabInstance.Id+++** (must be a unique Id)|
    |Advanced	|Under License mode, select **Fabric capacity**|
    |Default	storage format| Small dataset storage format|
    |Template apps	|Check the Develop template apps|

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image7.png)

Nota: Para encontrar su lab instant ID, seleccione **Help** y copie el
**instant ID**.

> ![A screenshot of a computer Description automatically
> generated](./media/image8.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image9.png)

3.  Espere a que la implementación se complete. Este proceso suele
    tardar entre 2 y 3 minutos.

> ![A screenshot of a computer Description automatically
> generated](./media/image10.png)

### Tarea 2: Crear un lakehouse

1.  Cree un nuevo lakehouse haciendo clic en el botón **+New item** en
    la barra de navegación.

> ![A screenshot of a computer Description automatically
> generated](./media/image11.png)

2.  Haga clic en el mosaico **Lakehouse**.

> ![A screenshot of a computer Description automatically
> generated](./media/image12.png)

3.  En el cuadro de diálogo **New lakehouse**, ingrese
    +++**wwilakehouse+++** en el campo **Name**, haga clic en el botón
    **Create** y abra el nuevo **lakehouse.**

> **Nota:** Asegúrese de eliminar el espacio antes de **wwilakehouse.**
>
> ![A screenshot of a computer Description automatically
> generated](./media/image13.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image14.png)

4.  Verá una notificación indicando **Successfully created SQL
    endpoint**.

> ![](./media/image15.png)

### Tarea 3: Ingerir datos de muestra

1.  En la página de **wwilakehouse**, navegue a la sección **Get data in
    your lakehouse** y haga clic en **Upload files**, como se muestra en
    la imagen inferior.

> ![A screenshot of a computer Description automatically
> generated](./media/image16.png)

2.  En la pestaña **Upload files**, haga clic en la carpeta en
    **Files**.

> ![A screenshot of a computer Description automatically
> generated](./media/image17.png)

3.  Busque la ruta **C:\LabFiles** en su máquina virtual (VM),
    seleccione el archivo **dimension_customer.csv** y haga clic en el
    botón **Open**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image18.png)

4.  Luego, haga clic en el botón **Upload** y cierre.

> ![A screenshot of a computer Description automatically
> generated](./media/image19.png)

5.  **Cierre** el panel **Upload files.**

> ![A screenshot of a computer Description automatically
> generated](./media/image20.png)

6.  Seleccione y haga clic en **Refresh** en la sección **Files**. El
    archivo aparecerá.

> ![A screenshot of a computer Description automatically
> generated](./media/image21.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

7.  En la página del **lakehouse**, en el panel Explorer, seleccione
    **Files**. Luego, pase el mouse sobre el archivo
    **dimension_customer.csv**. Haga clic en los puntos suspensivos
    horizontales **(…)** junto a **dimension_customer.csv**. Navegue y
    haga clic en **Load Table**, y luego seleccione **New table**.

> ![](./media/image23.png)

8.  En el cuadro de diálogo **Load file to new table**, haga clic en el
    botón **Load**.

> ![](./media/image24.png)

9.  Ahora la tabla **dimension_customer** se ha creado correctamente.

> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

10. Seleccione la tabla **dimension_customer** bajo el esquema **dbo**.

> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image27.png)

11. También puede usar el SQL endpoint del lakehouse para consultar los
    datos con instrucciones SQL. Seleccione **SQL analytics endpoint**
    desde el menú desplegable **Lakehouse** en la esquina superior
    derecha de la pantalla.

> ![A screenshot of a computer Description automatically
> generated](./media/image28.png)

12. En la página **wwilakehouse**, en Explorer, seleccione la tabla
    **dimension_customer** para obtener una vista previa de sus datos y
    seleccione **New SQL query** para escribir sus sentencias SQL.

> ![A screenshot of a computer Description automatically
> generated](./media/image29.png)

13. La siguiente consulta de ejemplo agrega el recuento de filas en
    función de la **columna BuyingGroup** de la tabla
    **dimension_customer**.

> Los archivos de consultas SQL se guardan automáticamente para
> referencia futura, y puede renombrarlos o eliminarlos según lo
> necesite.
>
> Pegue el código tal como se muestra en la imagen inferior y luego haga
> clic en el ícono de **reproducir** para ejecutar el script:

    ```
    SELECT BuyingGroup, Count(*) AS Total
    FROM dimension_customer
    GROUP BY BuyingGroup
    ```

> ![A screenshot of a computer Description automatically
> generated](./media/image30.png)

**Nota:** Si encuentra un error durante la ejecución del script,
verifique nuevamente la sintaxis y asegúrese de que no tenga espacios
innecesarios.

14. Anteriormente, todas las tablas y vistas del lakehouse se agregaban
    automáticamente al modelo semántico. Con las actualizaciones
    recientes, para nuevos lakehouses, debe agregar manualmente sus
    tablas al modelo semántico.

> ![A screenshot of a computer Description automatically
> generated](./media/image31.png)

15. En la pestaña **Home** del lakehouse, seleccione **New semantic
    model** y seleccione las tablas que desea agregar al modelo
    semántico.

> ![A screenshot of a computer Description automatically
> generated](./media/image32.png)

16. En el cuadro de diálogo **New semantic model**, ingrese
    +++wwilakehouse+++ y luego seleccione la tabla
    **dimension_customer** de la lista de tablas. Seleccione **Confirm**
    para crear el nuevo modelo.

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

### Tarea 4: Crear un informe

1.  Ahora, haga clic en **Fabric Lakehouse Tutorial-XX** en el panel de
    navegación del lado izquierdo.

> ![A screenshot of a computer Description automatically
> generated](./media/image34.png)

2.  En la vista **Fabric Lakehouse Tutorial-XX**, seleccione
    **wwilakehouse** de tipo **Semantic model**.

> ![A screenshot of a computer Description automatically
> generated](./media/image35.png)

3.  En el panel del **semantic model**, puede ver todas las tablas.
    Tiene opciones para crear informes desde cero, informes paginados o
    permitir que Power BI cree automáticamente un informe basado en sus
    datos.  
    Para este tutorial, en **Explore this data**, seleccione
    **Auto-create a report**, como se muestra en la siguiente imagen.

> ![A screenshot of a computer Description automatically
> generated](./media/image36.png)

4.  Una vez que el informe esté listo, haga clic en **View report now**
    para abrirlo y revisarlo.![A screenshot of a computer AI-generated
    content may be incorrect.](./media/image37.png)

> ![A screenshot of a computer Description automatically
> generated](./media/image38.png)

5.  Dado que la tabla es una dimensión y no contiene medidas, Power BI
    crea una medida para el recuento de filas, la agrega en diferentes
    columnas y genera varios gráficos, como se muestra en la siguiente
    imagen.

6.  Guarde este informe para el futuro seleccionando **Save** en la
    cinta superior.

> ![A screenshot of a computer Description automatically
> generated](./media/image39.png)

7.  En el cuadro de diálogo **Save your report**, ingrese un nombre para
    su informe como +++dimension_customer-report+++ y seleccione
    **Save.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image40.png)

8.  Verá una notificación que indica **Report saved**.

> ![A screenshot of a computer Description automatically
> generated](./media/image41.png)

# Ejercicio 2: Ingerir datos en el lakehouse

En este ejercicio, ingerirá tablas dimensionales y de hechos adicionales
de Wide World Importers (WWI) en el lakehouse.

### Tarea 1: Ingerir datos

1.  Ahora, haga clic en **Fabric Lakehouse Tutorial-XX** en el panel de
    navegación del lado izquierdo.

> ![A screenshot of a computer Description automatically
> generated](./media/image42.png)

2.  Nuevamente, seleccione el nombre del espacio de trabajo.

![A screenshot of a computer screen Description automatically
generated](./media/image43.png)

3.  En la página del espacio de trabajo de **Fabric Lakehouse
    Tutorial-XX**, navegue y haga clic en el botón **+New item**, luego
    seleccione **Pipeline**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image44.png)

4.  En el cuadro de diálogo **New pipeline**, especifique el nombre
    **+++IngestDataFromSourceToLakehouse+++** y seleccione **Create.  
    **Se creará y abrirá un nuevo **Data Factory pipeline.**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image45.png)

> ![A screenshot of a computer Description automatically
> generated](./media/image46.png)

5.  En el pipeline recién creado, es decir,
    **IngestDataFromSourceToLakehouse,** seleccione el menú desplegable
    **Copy data** y elija la opción **Add to canvas**.

> ![A screenshot of a computer Description automatically
> generated](./media/image47.png)

6.  Con **Copy data** seleccionado, navegue a la pestaña **Source**.

![](./media/image48.png)

7.  Seleccione el menú desplegable **Connection** y elija la opción
    **Browse all**.

![](./media/image49.png)

8.  Seleccione **Sample data** en el panel izquierdo y elija **Retail
    Data Model from Wide World Importers**.

![](./media/image50.png)

9.  En la ventana **Connect to data source**, seleccione **Retail Data
    Model from Wide World Importers** para obtener una vista previa y
    seleccione **OK.**

> ![](./media/image51.png)

10. La conexión al origen de datos queda seleccionada como datos de
    muestra.

![A screenshot of a computer Description automatically
generated](./media/image52.png)

11. Ahora, navegue a la pestaña **Destination**.

![](./media/image53.png)

12. En la pestaña **Destination**, haga clic en el menú desplegable
    **Connection** y seleccione **Browse all**.

![](./media/image54.png)

13. En la ventana **Choose a destination**, seleccione **OneLake
    catalog** en el panel izquierdo y seleccione **wwilakehouse**.

> ![](./media/image55.png)

14. El destino ahora está configurado como **Lakehouse**.

Especifique **Root Folder** como **Files** y asegúrese de que el formato
de archivo esté seleccionado como **Parquet**.

![A screenshot of a computer Description automatically
generated](./media/image56.png)

8.  Haga clic en **Run** para ejecutar la operación de copia de datos.

> ![A screenshot of a computer Description automatically
> generated](./media/image57.png)

9.  Haga clic en **Save and run** para que el pipeline sea guardado y
    ejecutado.

> ![A screenshot of a computer error Description automatically
> generated](./media/image58.png)

10. El proceso de copia de datos toma aproximadamente entre 1 y 3
    minutos en completarse.

> ![A screenshot of a computer Description automatically
> generated](./media/image59.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image60.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

11. En la pestaña **Output**, seleccione **Copy data1** para ver los
    detalles de la transferencia de datos.

> Después de ver el estado como **Succeeded**, haga clic en el botón
> **Close**.
>
> ![A screenshot of a computer Description automatically
> generated](./media/image62.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image63.png)

12. Tras la ejecución correcta del pipeline, vaya a su lakehouse
    (**wwilakehouse**) y abra el Explorer para ver los datos importados.

> ![A screenshot of a computer Description automatically
> generated](./media/image64.png)

13. Verifique que todas las carpetas de **WideWorldImporters** estén
    presentes en la vista del Explorer y que contengan datos para todas
    las tablas.

> ![A screenshot of a computer Description automatically
> generated](./media/image65.png)

# Ejercicio 3: Preparar y transformar datos en el lakehouse

### Tarea 1: Transformar datos y cargarlos en una tabla Delta de la capa Silver

1.  En la página de **wwilakehouse**, navegue y haga clic en **Open
    notebook** en la barra de comandos, luego seleccione **New
    notebook**.

> ![A screenshot of a computer Description automatically
> generated](./media/image66.png)

2.  En el notebook abierto en el **Lakehouse explorer**, verá que el
    notebook ya está vinculado al lakehouse que tiene abierto.

> ![](./media/image67.png)

**Nota:** Fabric ofrece la capacidad
[**V-order**](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order)
para escribir archivos Delta Lake optimizados.

V-order suele mejorar la compresión entre tres y cuatro veces y
proporciona hasta diez veces mayor rendimiento comparado con archivos
Delta Lake no optimizados.

Spark en Fabric optimiza dinámicamente las particiones mientras genera
los archivos con un tamaño predeterminado de 128 MB.

El tamaño objetivo del archivo puede modificarse según los requisitos de
la carga de trabajo.

Con la capacidad [**optimize
write**](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order#what-is-optimized-write),
el motor Apache Spark reduce la cantidad de archivos generados y aumenta
el tamaño individual de los archivos escritos.

3.  Antes de escribir datos como tablas Delta Lake en la sección
    **Tables** del lakehouse, utilizará dos características de Fabric
    (**V-order** y **Optimize Write**) para optimizar la escritura y
    mejorar el rendimiento de lectura.  
    Para habilitar estas características en su sesión, establezca estas
    configuraciones en la primera celda del notebook.

4.  Actualice el código de la **celda** con el siguiente contenido y
    haga clic en **▷ Run cell**, que aparece al pasar el cursor sobre la
    celda:

    ```
    # Copyright (c) Microsoft Corporation.
    # Licensed under the MIT License.
    spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
    spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
    spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize", "1073741824")
    ```

**Nota**: Al ejecutar una celda, no necesita especificar detalles del
pool o clúster de Spark, porque Fabric los proporciona mediante **Live
Pool**.

![A screenshot of a computer Description automatically
generated](./media/image68.png)

![A screenshot of a computer Description automatically
generated](./media/image69.png)

Cada espacio de trabajo de Fabric incluye un Spark pool predeterminado
llamado **Live Pool**. Esto significa que, cuando crea notebooks, no
necesita preocuparse por configuraciones de Spark ni detalles de
clúster.  
Al ejecutar el primer comando del notebook, el live pool se activa en
pocos segundos y se establece la sesión de Spark, iniciando la ejecución
del código.  
Las ejecuciones posteriores son casi instantáneas mientras la sesión
Spark permanece activa.

5.  A continuación, leerá datos sin procesar desde la sección **Files**
    del lakehouse y agregará columnas adicionales para diferentes partes
    de fecha como parte de la transformación.  
    Utilizará la **API partitionBy de Spark** para particionar los datos
    antes de escribirlos como tabla Delta, basándose en las nuevas
    columnas de partición (**Year y Quarter**).

6.  Use el ícono **+ Code** debajo del resultado de la celda para
    agregar una nueva celda de código al notebook e ingrese el siguiente
    código. Haga clic en **▷ Run cell** y revise el resultado.

**Nota:** Si no puede ver el resultado, haga clic en las líneas
horizontales del lado izquierdo de **Spark jobs.**

    ```
    from pyspark.sql.functions import col, year, month, quarter
    
    table_name = 'fact_sale'
    
    df = spark.read.format("parquet").load('Files/fact_sale_1y_full')
    df = df.withColumn('Year', year(col("InvoiceDateKey")))
    df = df.withColumn('Quarter', quarter(col("InvoiceDateKey")))
    df = df.withColumn('Month', month(col("InvoiceDateKey")))
    
    df.write.mode("overwrite").format("delta").partitionBy("Year","Quarter").save("Tables/" + table_name)
    ```

> ![A screenshot of a computer Description automatically
> generated](./media/image70.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image71.png)
>
> ![](./media/image72.png)

7.  Una vez cargada la tabla, puede continuar con la carga de datos para
    el resto de las dimensiones.  
    La siguiente celda crea una función para leer datos sin procesar
    desde la sección **Files** del lakehouse según el nombre de tabla
    que se ingrese como parámetro.  
    Luego, crea una lista de tablas de dimensiones y recorre dicha lista
    para generar una tabla Delta por cada nombre de tabla.

8.  Use el ícono **+ Code** debajo del resultado de la celda para
    agregar una nueva celda  
    de código al notebook e ingrese el siguiente código en ella. Haga
    clic en el botón **▷ Run cell** y revise el resultado.

    ```
    from pyspark.sql.types import *
    
    def loadFullDataFromSource(table_name):
        df = spark.read.format("parquet").load('Files/' + table_name)
        df = df.drop("Photo")
        df.write.mode("overwrite").format("delta").save("Tables/" + table_name)
    
    full_tables = [
        'dimension_city',
        'dimension_customer',
        'dimension_date',
        'dimension_employee',
        'dimension_stock_item'
    ]
    
    for table in full_tables:
        loadFullDataFromSource(table)
    ```
> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)
>
> ![](./media/image74.png)

6.  Para validar las tablas creadas, haga clic y seleccione **Refresh**
    en la sección **Tables** del panel Explorer, hasta que todas las
    tablas aparezcan en la lista.

> ![](./media/image75.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image76.png)

### Tarea 2: Transformar datos de negocio para agregación

Una organización puede tener ingenieros de datos trabajando con
Scala/Python y otros trabajando con SQL (Spark SQL o T-SQL), todos
operando sobre la misma copia de los datos. Fabric permite que estos
distintos grupos, con experiencia y preferencias variadas, trabajen y
colaboren juntos.  
Los dos enfoques diferentes transforman y generan agregados de negocio.
Puede elegir el que le resulte más adecuado o combinarlos según su
preferencia, sin comprometer el rendimiento:

- **Enfoque \#1 –** Usar PySpark para unir y agregar datos para generar
  agregados de negocio. Este enfoque es recomendable para quienes tienen
  experiencia en programación (Python o PySpark).

- **Enfoque \#2 –** Usar Spark SQL para unir y agregar datos para
  generar agregados de negocio. Este enfoque es recomendable para
  quienes tienen experiencia en SQL y están en transición hacia Spark.

**Enfoque \#1 (sale_by_date_city)**

Use PySpark para unir y agregar datos para generar agregados de negocio.
Con el siguiente código, crea tres dataframes de Spark, cada uno
referenciando una tabla Delta existente. Luego, une estas tablas usando
los dataframes, realiza un **group by** para generar la agregación,
renombra algunas columnas y finalmente escribe el resultado como una
tabla Delta en la sección **Tables** del lakehouse para persistir los
datos.

1.  Use el ícono **+ Code** debajo del resultado de la celda para
    agregar una nueva celda de código al notebook e ingrese el siguiente
    código. Haga clic en **▷ Run cell** y revise el resultado.

    ```
    df_fact_sale = spark.read.table("wwilakehouse.fact_sale") 
    df_dimension_date = spark.read.table("wwilakehouse.dimension_date")
    df_dimension_city = spark.read.table("wwilakehouse.dimension_city")
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image77.png)

2.  Haga clic en el ícono **+ Code** que se encuentra debajo del
    resultado de la celda para agregar una nueva celda de código al
    notebook e ingrese el siguiente código. Luego, haga clic en **▷ Run
    cell** y revise el resultado.

En esta celda, se unen las tablas usando los dataframes creados
anteriormente, se realiza un **group by**, se renombran algunas columnas
y finalmente se escribe como tabla Delta en **Tables**:

    ```
    sale_by_date_city = df_fact_sale.alias("sale") \
    .join(df_dimension_date.alias("date"), df_fact_sale.InvoiceDateKey == df_dimension_date.Date, "inner") \
    .join(df_dimension_city.alias("city"), df_fact_sale.CityKey == df_dimension_city.CityKey, "inner") \
    .select("date.Date", "date.CalendarMonthLabel", "date.Day", "date.ShortMonth", "date.CalendarYear", "city.City", "city.StateProvince", 
     "city.SalesTerritory", "sale.TotalExcludingTax", "sale.TaxAmount", "sale.TotalIncludingTax", "sale.Profit")\
    .groupBy("date.Date", "date.CalendarMonthLabel", "date.Day", "date.ShortMonth", "date.CalendarYear", "city.City", "city.StateProvince", 
     "city.SalesTerritory")\
    .sum("sale.TotalExcludingTax", "sale.TaxAmount", "sale.TotalIncludingTax", "sale.Profit")\
    .withColumnRenamed("sum(TotalExcludingTax)", "SumOfTotalExcludingTax")\
    .withColumnRenamed("sum(TaxAmount)", "SumOfTaxAmount")\
    .withColumnRenamed("sum(TotalIncludingTax)", "SumOfTotalIncludingTax")\
    .withColumnRenamed("sum(Profit)", "SumOfProfit")\
    .orderBy("date.Date", "city.StateProvince", "city.City")
    
    sale_by_date_city.write.mode("overwrite").format("delta").option("overwriteSchema", "true").save("Tables/aggregate_sale_by_date_city")
    ```
![A screenshot of a computer AI-generated content may be
incorrect.](./media/image78.png)

**Enfoque \#2 (sale_by_date_employee)**

Use Spark SQL para unir y agregar datos para generar agregados de
negocio. Con el siguiente código, se crea una vista temporal de Spark
uniendo tres tablas, se realiza un **group by** para generar la
agregación y se renombran algunas columnas. Finalmente, se lee desde la
vista temporal de Spark y se escribe como una tabla Delta en la sección
**Tables** del lakehouse para persistir los datos.

3.  Use el ícono **+ Code** debajo del resultado de la celda para
    agregar una nueva celda de código al notebook e ingrese el siguiente
    código. Haga clic en **▷ Run cell** y revise el resultado.

En esta celda, se crea una vista temporal de Spark uniendo tres tablas,
se realiza un **group by** y se renombran algunas columnas:

    ```
    %%sql
    CREATE OR REPLACE TEMPORARY VIEW sale_by_date_employee
    AS
    SELECT
           DD.Date, DD.CalendarMonthLabel
     , DD.Day, DD.ShortMonth Month, CalendarYear Year
          ,DE.PreferredName, DE.Employee
          ,SUM(FS.TotalExcludingTax) SumOfTotalExcludingTax
          ,SUM(FS.TaxAmount) SumOfTaxAmount
          ,SUM(FS.TotalIncludingTax) SumOfTotalIncludingTax
          ,SUM(Profit) SumOfProfit 
    FROM wwilakehouse.fact_sale FS
    INNER JOIN wwilakehouse.dimension_date DD ON FS.InvoiceDateKey = DD.Date
    INNER JOIN wwilakehouse.dimension_Employee DE ON FS.SalespersonKey = DE.EmployeeKey
    GROUP BY DD.Date, DD.CalendarMonthLabel, DD.Day, DD.ShortMonth, DD.CalendarYear, DE.PreferredName, DE.Employee
    ORDER BY DD.Date ASC, DE.PreferredName ASC, DE.Employee ASC
    ```

 ![A screenshot of a computer AI-generated content may be
incorrect.](./media/image79.png)

8.  Use el ícono **+ Code** debajo del resultado de la celda para
    agregar una nueva celda de código al notebook e ingrese el siguiente
    código. Haga clic en **▷ Run cell** y revise el resultado.

En esta celda, se lee desde la vista temporal de Spark creada en la
celda anterior y, finalmente, se escribe como una tabla Delta en la
sección **Tables** del lakehouse.

    ```
    sale_by_date_employee = spark.sql("SELECT * FROM sale_by_date_employee")
    sale_by_date_employee.write.mode("overwrite").format("delta").option("overwriteSchema", "true").save("Tables/aggregate_sale_by_date_employee")
    ```

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image80.png)

9.  Para validar las tablas creadas, haga clic y seleccione Refresh en
    **Tables** hasta que aparezcan las tablas de agregados.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image81.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image82.png)

Ambos enfoques producen resultados similares. Puede elegir según su
experiencia y preferencia, minimizando la necesidad de aprender una
nueva tecnología sin comprometer el rendimiento.

Además, notará que los datos se escriben como archivos **Delta Lake**.
La función de descubrimiento y registro automático de tablas de Fabric
los detecta y los registra en el metastore. No es necesario ejecutar
explícitamente sentencias **CREATE TABLE** para usar las tablas con SQL.

# Ejercicio 4: Creación de informes en Microsoft Fabric

En esta sección del tutorial, se crea un modelo de datos de Power BI y
se genera un informe desde cero.

### Tarea 1: Explorar datos en la capa Silver usando el SQL endpoint

Power BI está integrado de forma nativa en toda la experiencia de
Fabric. Esta integración permite un modo exclusivo, denominado
DirectLake, que facilita el acceso a los datos del lakehouse y ofrece
una experiencia de consulta e informes altamente eficiente.

DirectLake es una capacidad revolucionaria para analizar conjuntos de
datos de gran escala en Power BI. Su tecnología se basa en cargar
directamente archivos Parquet desde un data lake, sin necesidad de
consultar un data warehouse, un endpoint de lakehouse, ni de importar o
duplicar los datos en un dataset de Power BI. En esencia, DirectLake
proporciona una ruta rápida para cargar datos desde el data lake
directamente en el motor de Power BI, listos para el análisis.

En el modo tradicional DirectQuery, el motor de Power BI consulta los
datos directamente en la fuente para cada operación, por lo que el
rendimiento depende de la velocidad de recuperación de dicha fuente.
DirectQuery evita la copia de datos y garantiza que cualquier cambio se
refleje de inmediato en las consultas. Por su parte, el modo Import
ofrece un mejor rendimiento porque los datos se encuentran disponibles
en memoria, sin consultar la fuente en cada ejecución. No obstante,
requiere copiar los datos durante cada actualización, y los cambios solo
se reflejan en la siguiente actualización programada o manual.

El modo DirectLake elimina la necesidad de importación al cargar
directamente los archivos de datos en memoria. Al no requerir un proceso
de importación explícito, es posible reflejar los cambios de la fuente
prácticamente en tiempo real. Esto combina las ventajas de DirectQuery y
Import, evitando sus limitaciones. Por ello, DirectLake se convierte en
la opción ideal para analizar conjuntos de datos muy grandes y con
actualizaciones frecuentes en la fuente.

1.  En el menú izquierdo, seleccione el ícono del espacio de trabajo y,
    a continuación, haga clic en el nombre del espacio de trabajo.

> ![A screenshot of a computer Description automatically
> generated](./media/image83.png)

2.  En el menú izquierdo, seleccione
    **Fabric <Lakehouse-@lab.LabInstance.Id>** y luego su modelo
    semántico llamado **wwilakehouse.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image84.png)

3.  En la barra de menú superior, seleccione **Open semantic model**
    para abrir el diseñador de datos del modelo.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image85.png)

4.  En la esquina superior derecha, asegúrese de que el diseñador del
    modelo de datos esté en **Editing mode**. Esto debería cambiar el
    texto del menú desplegable a **Editing**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image86.png)

5.  En la cinta de menú, seleccione **Edit tables** para mostrar el
    cuadro de diálogo de sincronización de tablas.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image87.png)

6.  En el cuadro **Edit semantic model**, seleccione todas las tablas y
    luego haga clic en **Confirm** en la parte inferior para sincronizar
    el modelo semántico.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image88.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image89.png)

7.  Desde la tabla **fact_sale**, arrastre el campo **CityKey** y
    suéltelo sobre el campo **CityKey** en la tabla **dimension_city**
    para crear una relación. Aparecerá el cuadro de diálogo **Create
    Relationship**.

> **Nota:** Reorganice las tablas haciendo clic, arrastrando y soltando,
> de modo que **dimension_city y fact_sale** estén una junto a la otra.
> Esto también aplica para cualquier par de tablas en las que intente
> crear relaciones, para facilitar el arrastre de columnas entre las
> tablas.   
> ![](./media/image90.png)

8.  En el cuadro **Create Relationship**:

    - **Table 1** se completa con **fact_sale** y la columna
      **CityKey**.

    - **Table 2** se completa con **dimension_city** y la columna
      **CityKey**.

    - Cardinality: **Many to one (\*:1)**

    - Cross filter direction: **Single**

    - Deje seleccionada la casilla junto a **Make this relationship
      active**.

    - Seleccione la casilla junto a **Assume referential integrity.**

    - Seleccione **Save.**

> ![](./media/image91.png)

9.  A continuación, agregue estas relaciones usando la misma
    configuración de **Create Relationship** con las siguientes tablas y
    columnas:

    - **StockItemKey(fact_sale)** - **StockItemKey(dimension_stock_item)**

> ![](./media/image92.png)
>
> ![](./media/image93.png)

- **Salespersonkey(fact_sale)** - **EmployeeKey(dimension_employee)**

> ![](./media/image94.png)

10. Asegúrese de crear también las relaciones entre los siguientes pares
    usando los mismos pasos:

    - **CustomerKey(fact_sale)** - **CustomerKey(dimension_customer)**

    - **InvoiceDateKey(fact_sale)** - **Date(dimension_date)**

11. Después de agregar estas relaciones, su modelo de datos debería
    verse como en la imagen inferior y estará listo para la creación de
    informes.

> ![](./media/image95.png)

### Tarea 2: Crear un informe

1.  En la cinta superior, seleccione **File** y luego **Create new
    report** para comenzar a crear informes o paneles en Power BI.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image96.png)

2.  En el lienzo de informes de Power BI, puede crear informes según los
    requisitos de su negocio arrastrando las columnas necesarias desde
    el panel **Data** hacia el lienzo y utilizando una o varias de las
    visualizaciones disponibles.

> ![](./media/image97.png)

**Agregar un título:**

3.  En la cinta, seleccione **Text box**. Escriba **WW Importers Profit
    Reporting**. Resalte el **texto** y establezca el tamaño en **20**.

> ![](./media/image98.png)

4.  Ajuste el tamaño del cuadro de texto, colóquelo en la **esquina
    superior izquierda** de la página del informe y haga clic fuera del
    cuadro para finalizar.

> ![](./media/image99.png)

**Agregar una tarjeta:**

- En el panel **Data**, expanda **fact_sales** y marque la casilla junto
  a **Profit**. Esta selección crea un gráfico de columnas y agrega el
  campo al eje Y.

> ![](./media/image100.png)

5.  Con el gráfico seleccionado, elija el visual **Card** en el panel de
    visualizaciones.

> ![](./media/image101.png)

6.  La visualización se convertirá en una tarjeta. Ubíquela debajo del
    título.

> ![](./media/image102.png)

7.  Haga clic en cualquier parte del lienzo vacío (**o presione Esc**)
    para deseleccionar la tarjeta.

**Agregar un gráfico de barras:**

8.  En el panel **Data**, expanda **fact_sales** y marque la casilla
    junto a **Profit**. Esta acción crea un gráfico de columnas y agrega
    el campo al eje Y.

> ![](./media/image103.png)

9.  En el panel **Data**, expanda **dimension_city** y marque la casilla
    junto a **SalesTerritory**. Esta acción agrega el campo al eje Y.

> ![](./media/image104.png)

10. Con el gráfico de barras seleccionado, en el panel de
    visualizaciones seleccione el visual **Clustered bar chart**. Esta
    acción convierte el gráfico de columnas en un gráfico de barras.

> ![](./media/image105.png)

11. Redimensione el gráfico de barras para ocupar el área situada debajo
    del título y de la tarjeta.

> ![](./media/image106.png)

12. Haga clic en cualquier parte del lienzo en blanco (**o presione la
    tecla Esc**) para deseleccionar el gráfico de barras.

**Crear una visualización de área apilada:**

13. En el panel **Visualizations**, seleccione el visual **Stacked area
    chart**.

> ![](./media/image107.png)

14. Reposicione y redimensione el gráfico de área apilada a la derecha
    de la tarjeta y del gráfico de barras creados en los pasos
    anteriores.

> ![](./media/image108.png)

15. En el panel **Data**, expanda **fact_sales** y marque la casilla
    junto a **Profit**. Luego, expanda **dimension_date** y marque la
    casilla junto a **FiscalMonthNumber**. Esta acción crea un gráfico
    de líneas relleno que muestra la ganancia por mes fiscal.

> ![](./media/image109.png)

16. En el panel **Data**, expanda **dimension_stock_item** y arrastre
    **BuyingPackage** al campo Legend. Esta acción agrega una línea para
    cada uno de los Buying Packages.

> ![](./media/image110.png) ![](./media/image111.png)

17. Haga clic en cualquier parte del lienzo en blanco (**o presione la
    tecla Esc**) para deseleccionar el gráfico de área apilada.

**Crear un gráfico de columnas:**

18. En el panel **Visualizations**, seleccione el visual **Stacked
    column chart**.

> ![](./media/image112.png)

19. En el panel **Data**, expanda **fact_sales** y marque la casilla
    junto a **Profit**. Esta acción agrega el campo al eje Y.

20.   En el panel **Data**, expanda **dimension_employee** y marque la
    casilla junto a **Employee**. Esta acción agrega el campo al eje X.

> ![](./media/image113.png)

21. Haga clic en cualquier parte del lienzo en blanco (**o presione la
    tecla Esc**) para deseleccionar el gráfico.

22. En la cinta de opciones, seleccione **File \> Save**.

> ![](./media/image114.png)

23. Ingrese el nombre de su informe como **Profit Reporting** y
    seleccione **Save.**

> ![](./media/image115.png)

24. Aparecerá una notificación indicando que el informe se ha guardado
    correctamente. 

> ![](./media/image116.png)

# Ejercicio 5: Eliminar los recursos

Puede eliminar informes, pipelines, warehouses y otros elementos de
forma individual, o bien eliminar el espacio de trabajo completo. Siga
los pasos que se indican a continuación para eliminar el espacio de
trabajo que creó para este tutorial.

1.  Seleccione su espacio de trabajo **Fabric Lakehouse Tutorial-XX**
    desde el menú de navegación izquierdo. Esto abrirá la vista de
    elementos del espacio de trabajo.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image117.png)

2.  Seleccione la opción … que aparece debajo del nombre del espacio de
    trabajo y haga clic en **Workspace settings**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image118.png)

3.  En el panel de configuración, seleccione **General** y luego
    **Remove this workspace**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image119.png)

4.  En la advertencia emergente, seleccione **Delete**.

> ![](./media/image120.png)

5.  Espere la notificación que confirma que el espacio de trabajo ha
    sido eliminado antes de continuar con el siguiente laboratorio.

> ![](./media/image121.png)

**Resumen**:  
Este laboratorio práctico se centra en la configuración y puesta en
marcha de los componentes clave de Microsoft Fabric y Power BI para la
gestión y el análisis de datos. Incluye tareas como la activación de
entornos de prueba, la configuración de OneDrive, la creación de
espacios de trabajo y la implementación de lakehouses.

También abarca actividades relacionadas con la ingesta de datos de
ejemplo, la optimización de tablas Delta y la creación de informes en
Power BI, con el fin de realizar un análisis de datos eficiente.

El objetivo principal es ofrecer una experiencia práctica que permita
comprender cómo utilizar Microsoft Fabric y Power BI para la gestión
integral de datos y la generación de informes empresariales.
