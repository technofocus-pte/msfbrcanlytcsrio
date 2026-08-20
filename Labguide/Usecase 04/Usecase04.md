# Caso de uso 03: Analizar datos con Apache Spark

**Introducción**

Apache Spark es un motor de código abierto para el procesamiento
distribuido de datos y se utiliza ampliamente para explorar, procesar y
analizar grandes volúmenes de datos en el almacenamiento de data lakes.
Spark está disponible como opción de procesamiento en muchos productos
de plataformas de datos, incluidos Azure HDInsight, Azure Databricks,
Azure Synapse Analytics y Microsoft Fabric. Uno de los beneficios de
Spark es la compatibilidad con una amplia variedad de lenguajes de
programación, incluidos Java, Scala, Python y SQL, lo que convierte a
Spark en una solución muy flexible para cargas de trabajo de
procesamiento de datos, incluida la limpieza y manipulación de datos, el
análisis estadístico y el aprendizaje automático, así como el análisis y
la visualización de datos.

Las tablas de un lakehouse de Microsoft Fabric se basan en el formato de
código abierto Delta Lake para Apache Spark. Delta Lake agrega
compatibilidad con semántica relacional para operaciones de datos por
lotes y de streaming, y permite crear una arquitectura de Lakehouse en
la que Apache Spark puede utilizarse para procesar y consultar datos en
tablas basadas en archivos subyacentes de un data lake.

En Microsoft Fabric, Dataflows (Gen2) se conectan a diversos orígenes de
datos y realizan transformaciones en Power Query Online. A continuación,
pueden utilizarse en Data Pipelines para ingerir datos en un lakehouse u
otro almacén analítico, o para definir un conjunto de datos para un
informe de Power BI.

Este laboratorio está diseñado para presentar los diferentes elementos
de Dataflows (Gen2) y no para crear una solución compleja como la que
podría existir en una empresa.

**Objetivos**:

- Crear un workspace en Microsoft Fabric con la versión de prueba de
  Fabric habilitada.

- Establecer un entorno de lakehouse y cargar archivos de datos para su
  análisis.

- Generar un notebook para la exploración y el análisis interactivos de
  datos.

- Cargar datos en un dataframe para su posterior procesamiento y
  visualización.

- Aplicar transformaciones a los datos mediante PySpark.

- Guardar y particionar los datos transformados para optimizar las
  consultas.

- Crear una tabla en el metastore de Spark para la administración
  estructurada de datos.

- Guardar el DataFrame como una tabla delta administrada denominada
  "salesorders".

- Guardar el DataFrame como una tabla delta externa denominada
  "external_salesorder" con una ruta especificada.

- Describir y comparar las propiedades de las tablas administradas y
  externas.

- Ejecutar consultas SQL en las tablas para el análisis y la generación
  de informes.

- Visualizar los datos mediante bibliotecas de Python como matplotlib y
  seaborn.

- Establecer un data lakehouse en la experiencia de Data Engineering e
  ingerir los datos pertinentes para su análisis posterior.

- Definir un dataflow para extraer, transformar y cargar datos en el
  lakehouse.

- Configurar los destinos de datos en Power Query para almacenar los
  datos transformados en el lakehouse.

- Incorporar el dataflow en un pipeline para habilitar el procesamiento
  y la ingesta de datos programados.

Eliminar el workspace y los elementos asociados para finalizar el
ejercicio.

## Ejercicio 1: Crear un workspace, un lakehouse, un notebook y cargar datos en un dataframe

### Tarea 1: Crear un workspace

1.  Abra el navegador, vaya a la barra de direcciones y escriba o pegue
    la siguiente URL: +++https://app.fabric.microsoft.com/+++ y, a
    continuación, presione el botón **Enter**.

**Nota**: Si se le dirige a la página principal de Microsoft Fabric,
omita los pasos y vaya directamente al paso n.º 5.

![](./media/image1.png)

2.  En la ventana de **Microsoft Fabric**, introduzca sus credenciales y
    haga clic en el botón **Submit.**

| Credential | Value |
|---|---|
| Username | +++@lab.CloudPortalCredential(User1).Username+++ |
| Password | +++@lab.CloudPortalCredential(User1).Password+++ |

> ![](./media/image2.png)

3.  A continuación, en la ventana de **Microsoft**, introduzca la
    contraseña y haga clic en el botón **Sign in.**

> ![](./media/image3.png)

4.  En la ventana **Stay signed in?,** haga clic en el botón **Yes**.

5.  Si Power BI se abre de forma predeterminada, siga los pasos que se
    indican a continuación; de lo contrario, omita este paso.

- Haga clic en **Power BI.**

![](./media/image4.png)

- Seleccione Fabric entre las opciones.

![](./media/image5.png)

6.  En la página principal de Fabric, seleccione el mosaico **+New
    workspace**.

![](./media/image6.png)

7.  En la pestaña **Create a workspace**, introduzca los siguientes
    detalles y haga clic en el botón **Apply**.

| Setting | Value |
|---|---|
| Name | +++dp_Fabric@lab.LabInstance.Id+++ (must be a unique ID) |
| Description | `This workspace contains Analyze data with Apache Spark` |
| Advanced | Under **License mode**, select **Fabric** |
| Default storage format | **Small dataset storage format** |

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image7.png)
>
> ![](./media/image8.png)

8.  Espere a que finalice la implementación. El proceso tarda entre 2 y
    3 minutos en completarse. Cuando se abra el nuevo workspace, debería
    estar vacío.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image9.png)

### Tarea 2: Crear un lakehouse y cargar archivos

Ahora que tiene un workspace, es momento de cambiar a la experiencia de
*Data engineering* en el portal y crear un data lakehouse para los
archivos de datos que va a analizar.

1.  Cree un nuevo Eventhouse haciendo clic en el botón **+ New item** de
    la barra de navegación.

> ![](./media/image10.png)

2.  En Filter by, busque y seleccione el mosaico **Lakehouse**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image11.png)

3.  En el cuadro de diálogo **New lakehouse**, introduzca
    +++**Fabric_lakehouse**+++ en el campo **Name**, haga clic en el
    botón **Create** y abra el nuevo lakehouse.

![](./media/image12.png)

\[!note\]**Nota:** Después de aproximadamente un minuto, se creará un
nuevo lakehouse vacío. Debe ingerir algunos datos en el data lakehouse
para su análisis.

![](./media/image13.png)

Verá una notificación que indica **Successfully created SQL endpoint**.

![](./media/image14.png)

4.  En la sección **Explorer**, en **fabric_lakehouse**, coloque el
    cursor sobre la carpeta **Files** y, a continuación, haga clic en el
    menú de puntos suspensivos horizontales **(…)**. Vaya a **Upload** y
    haga clic en esta opción; después, haga clic en **Upload folder**,
    como se muestra en la siguiente imagen.

![](./media/image15.png)

5.  En el panel **Upload folder** que aparece en el lado derecho,
    seleccione el icono de carpeta situado debajo de **Files/** y, a
    continuación, vaya a **C:\LabFiles\LabFiles**, seleccione la carpeta
    **orders** y haga clic en el botón **Upload**.

![](./media/image16.png)

6.  Si aparece el cuadro de diálogo **Upload 3 files to this site?**,
    haga clic en el botón **Upload.**

![](./media/image17.png)

7.  En el panel **Upload folder**, haga clic en el botón **Upload**.

![](./media/image18.png)

8.  Después de cargar los archivos, cierre el panel **Upload folder**.

![](./media/image19.png)

9.  Expanda **Files**, seleccione la carpeta **orders** y compruebe que
    los archivos CSV se hayan cargado.

![](./media/image20.png)

### Tarea 3: Crear un notebook

Para trabajar con datos en Apache Spark, puede crear un notebook. Los
notebooks proporcionan un entorno interactivo en el que puede escribir y
ejecutar código (en varios lenguajes) y agregar notas para documentarlo.

1.  En la página de **Fabric**, vaya a **Import** en la barra de
    comandos, haga clic en el menú desplegable y, a continuación,
    seleccione **New notebook \> From this computer**.

![](./media/image21.png)

2.  Después de unos segundos, se abrirá un nuevo notebook que contiene
    una única celda. Los notebooks están compuestos por una o más celdas
    que pueden contener código o *Markdown* (texto con formato).

![](./media/image22.png)

3.  Seleccione la primera celda (que actualmente es una celda de
    *código*) y, a continuación, en la barra de herramientas dinámica
    situada en la parte superior derecha, utilice el botón **M↓** para
    **convertir la celda en una celda de Markdown**.

![](./media/image23.png)

4.  Cuando la celda cambie a una celda de Markdown, se representará el
    texto que contiene.

![A screenshot of a computer Description automatically
generated](./media/image24.png)

5.  Utilice el botón 🖉 (Edit) para cambiar la celda al modo de edición,
    reemplace todo el texto y, a continuación, modifique el Markdown de
    la siguiente manera:

 +++# Sales order data exploration+++

6.  Utilice el código de este notebook para explorar los datos de
    pedidos de ventas.

![](./media/image25.png)

![A screenshot of a computer Description automatically
generated](./media/image26.png)

6.  Haga clic en cualquier lugar del notebook fuera de la celda para
    dejar de editarla y ver el Markdown representado.

![A screenshot of a computer Description automatically
generated](./media/image27.png)

### Tarea 4: Cargar datos en un dataframe

Ahora está listo para ejecutar código que carga los datos en un
*dataframe*. Los dataframes de Spark son similares a los dataframes de
Pandas en Python y proporcionan una estructura común para trabajar con
datos en filas y columnas.

**Nota:** Spark admite varios lenguajes de programación, incluidos
Scala, Java y otros. En este ejercicio, utilizaremos *PySpark*, una
variante de Python optimizada para Spark. PySpark es uno de los
lenguajes más utilizados en Spark y es el lenguaje predeterminado en los
notebooks de Fabric.

1.  Con el notebook visible, expanda la lista **Files** y seleccione la
    carpeta **orders** para que los archivos CSV aparezcan junto al
    editor del notebook.

![A screenshot of a computer Description automatically
generated](./media/image28.png)

2.  Ahora, coloque el cursor sobre el archivo **2019.csv**. Haga clic en
    los puntos suspensivos horizontales **(…)** situados junto a
    **2019.csv**. Vaya a **Load data** y haga clic en esta opción;
    después, seleccione **Spark**. Se agregará al notebook una nueva
    celda de código que contiene el siguiente código:

```
df = spark.read.format("csv").option("header","true").load("Files/orders/2019.csv")
# df now is a Spark DataFrame containing CSV data from "Files/orders/2019.csv".
display(df)
```

![A screenshot of a computer Description automatically
generated](./media/image29.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image30.png)

**Sugerencia:** Puede ocultar los paneles de exploración de Lakehouse de
la izquierda utilizando sus iconos «. Esto le ayudará a centrarse en el
notebook.

3.  Utilice el botón **▷ Run cell** situado a la izquierda de la celda
    para ejecutarla.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image31.png)

**Nota:** Como es la primera vez que ejecuta código de Spark, debe
iniciarse una sesión de Spark. Esto significa que la primera ejecución
de la sesión puede tardar aproximadamente un minuto en completarse. Las
ejecuciones posteriores serán más rápidas.

4.  Cuando el comando de la celda haya finalizado, revise el resultado
    debajo de la celda, que debería ser similar al siguiente:

![](./media/image32.png)

5.  El resultado muestra las filas y columnas de datos del archivo
    **2019.csv**. Sin embargo, observe que los encabezados de las
    columnas no parecen correctos. El código predeterminado utilizado
    para cargar los datos en un dataframe supone que el archivo CSV
    incluye los nombres de las columnas en la primera fila, pero en este
    caso el archivo CSV solo incluye los datos sin información de
    encabezado.

6.  Modifique el código para establecer la opción **header** en
    **false**. Reemplace todo el código de la **celda** con el siguiente
    código, haga clic en el botón **▷ Run cell** y revise el resultado.

```
df = spark.read.format("csv").option("header","false").load("Files/orders/2019.csv")
# df now is a Spark DataFrame containing CSV data from "Files/orders/2019.csv".
display(df)
```

![](./media/image33.png)

7.  Ahora el dataframe incluye correctamente la primera fila como
    valores de datos, pero los nombres de las columnas se generan
    automáticamente y no son muy útiles. Para comprender los datos, debe
    definir explícitamente el esquema correcto y el tipo de datos de los
    valores del archivo.

8.  Reemplace todo el código de la **celda** con el siguiente código,
    haga clic en el botón **▷ Run cell** y revise el resultado.

```
from pyspark.sql.types import *

orderSchema = StructType([
    StructField("SalesOrderNumber", StringType()),
    StructField("SalesOrderLineNumber", IntegerType()),
    StructField("OrderDate", DateType()),
    StructField("CustomerName", StringType()),
    StructField("Email", StringType()),
    StructField("Item", StringType()),
    StructField("Quantity", IntegerType()),
    StructField("UnitPrice", FloatType()),
    StructField("Tax", FloatType())
    ])

df = spark.read.format("csv").schema(orderSchema).load("Files/orders/2019.csv")
display(df)
```

![](./media/image34.png)

![](./media/image35.png)

9.  Ahora el dataframe incluye los nombres de columna correctos (además
    de **Index**, que es una columna integrada en todos los dataframes
    basada en la posición ordinal de cada fila). Los tipos de datos de
    las columnas se especifican mediante un conjunto estándar de tipos
    definido en la biblioteca **Spark SQL**, que se importó al principio
    de la celda.

10. Confirme que los cambios se hayan aplicado a los datos visualizando
    el dataframe.

11. Utilice el icono **+ Code** situado debajo del resultado de la celda
    para agregar una nueva celda de código al notebook e introduzca en
    ella el siguiente código. Haga clic en el botón **▷ Run cell** y
    revise el resultado.

+++display(df)+++

![](./media/image36.png)

12. El dataframe incluye únicamente los datos del archivo **2019.csv.**
    Modifique el código para que la ruta del archivo utilice un comodín
    \* para leer los datos de pedidos de ventas de todos los archivos de
    la carpeta **orders**.

13. Utilice el icono **+ Code** situado debajo del resultado de la celda
    para agregar una nueva celda de código al notebook e introduzca en
    ella el siguiente código.

```
from pyspark.sql.types import *

orderSchema = StructType([
    StructField("SalesOrderNumber", StringType()),
    StructField("SalesOrderLineNumber", IntegerType()),
    StructField("OrderDate", DateType()),
    StructField("CustomerName", StringType()),
    StructField("Email", StringType()),
    StructField("Item", StringType()),
    StructField("Quantity", IntegerType()),
    StructField("UnitPrice", FloatType()),
    StructField("Tax", FloatType())
    ])

df = spark.read.format("csv").schema(orderSchema).load("Files/orders/*.csv")
display(df)
```
![](./media/image37.png)

14. Ejecute la celda de código modificada y revise el resultado, que
    ahora debería incluir las ventas de 2019, 2020 y 2021.

![](./media/image38.png)

**Nota:** Solo se muestra un subconjunto de las filas, por lo que es
posible que no pueda ver ejemplos de todos los años.

## Ejercicio 2: Explorar datos en un dataframe

El objeto dataframe incluye una amplia variedad de funciones que puede
utilizar para filtrar, agrupar y manipular de otras formas los datos que
contiene.

### Tarea 1: Filtrar un dataframe

1.  Utilice el icono **+ Code** situado debajo del resultado de la celda
    para agregar una nueva celda de código al notebook e introduzca en
    ella el siguiente código.

```
customers = df['CustomerName', 'Email']
print(customers.count())
print(customers.distinct().count())
display(customers.distinct())
```

2.  **Ejecute** la nueva celda de código y revise los resultados.
    Observe los siguientes detalles:

    - Cuando realiza una operación en un dataframe, el resultado es un
      nuevo dataframe (en este caso, se crea un nuevo dataframe
      **customers** seleccionando un subconjunto específico de columnas
      del dataframe **df**).

    - Los dataframes proporcionan funciones como **count** y
      **distinct** que se pueden utilizar para resumir y filtrar los
      datos que contienen.

    - La sintaxis dataframe\['Field1', 'Field2', ...\] es una forma
      abreviada de definir un subconjunto de columnas. También puede
      utilizar el método **select**, por lo que la primera línea del
      código anterior podría escribirse como customers =
      df.select("CustomerName", "Email")

![](./media/image39.png)

3.  Modifique el código, reemplace todo el código de la **celda** con el
    siguiente código y haga clic en el botón **▷ Run cell**, como se
    indica a continuación:

```
customers = df.select("CustomerName", "Email").where(df['Item']=='Road-250 Red, 52')
print(customers.count())
print(customers.distinct().count())
display(customers.distinct())
```

4.  Ejecute el código modificado para ver los clientes que han comprado
    el producto **Road-250 Red, 52**. Tenga en cuenta que puede
    “**encadenar”** varias funciones para que el resultado de una
    función se convierta en la entrada de la siguiente; en este caso, el
    dataframe creado mediante el método **select** es el dataframe de
    origen para el método **where**, que se utiliza para aplicar los
    criterios de filtrado.

![](./media/image40.png)

### Tarea 2: Agregar y agrupar datos en un dataframe

1.  Haga clic en **+ Code**, copie y pegue el siguiente código y, a
    continuación, haga clic en el botón **Run cell**.

```
productSales = df.select("Item", "Quantity").groupBy("Item").sum()
display(productSales)
```
> ![](./media/image41.png)

2.  Observe que los resultados muestran la suma de las cantidades de
    pedidos agrupadas por producto. El método **groupBy** agrupa las
    filas por *Item*, y la función de agregación **sum** posterior se
    aplica a todas las columnas numéricas restantes (en este caso,
    *Quantity*).

3.  Haga clic en **+ Code**, copie y pegue el siguiente código y, a
    continuación, haga clic en el botón **Run cell**.

```
from pyspark.sql.functions import *

yearlySales = df.select(year("OrderDate").alias("Year")).groupBy("Year").count().orderBy("Year")
display(yearlySales)
```

![](./media/image42.png)

4.  Observe que los resultados muestran el número de pedidos de ventas
    por año. Tenga en cuenta que el método **select** incluye una
    función SQL **year** para extraer el componente del año del campo
    *OrderDate* (por este motivo, el código incluye una instrucción
    **import** para importar funciones de la biblioteca **Spark SQL**).
    A continuación, se utiliza un método **alias** para asignar un
    nombre de columna al valor del año extraído. Después, los datos se
    agrupan por la columna derivada *Year* y se calcula el recuento de
    filas de cada grupo. Finalmente, se utiliza el método **orderBy**
    para ordenar el dataframe resultante.

## Ejercicio 3: Utilizar Spark para transformar archivos de datos

Una tarea común para los ingenieros de datos es ingerir datos en un
formato o estructura determinados y transformarlos para su posterior
procesamiento o análisis.

### Tarea 1: Utilizar métodos y funciones de dataframe para transformar datos

1.  Haga clic en + Code y copie y pegue el siguiente código.

```
from pyspark.sql.functions import *

## Create Year and Month columns
transformed_df = df.withColumn("Year", year(col("OrderDate"))).withColumn("Month", month(col("OrderDate")))

# Create the new FirstName and LastName fields
transformed_df = transformed_df.withColumn("FirstName", split(col("CustomerName"), " ").getItem(0)).withColumn("LastName", split(col("CustomerName"), " ").getItem(1))

# Filter and reorder columns
transformed_df = transformed_df["SalesOrderNumber", "SalesOrderLineNumber", "OrderDate", "Year", "Month", "FirstName", "LastName", "Email", "Item", "Quantity", "UnitPrice", "Tax"]

# Display the first five orders
display(transformed_df.limit(5))
```

2.  **Ejecute** el código para crear un nuevo dataframe a partir de los
    datos de pedidos originales con las siguientes transformaciones:

    - Agregue las columnas **Year** y **Month** basadas en la columna
      **OrderDate.**

    - Agregue las columnas **FirstName** y **LastName** basadas en la
      columna **CustomerName**.

    - Filtre y reordene las columnas, eliminando la columna
      **CustomerName**.

![](./media/image43.png)

3.  Revise el resultado y compruebe que las transformaciones se hayan
    aplicado a los datos.

![](./media/image44.png)

Puede utilizar toda la potencia de la biblioteca Spark SQL para
transformar los datos mediante el filtrado de filas, la derivación, la
eliminación y el cambio de nombre de columnas, así como la aplicación de
cualquier otra modificación de datos necesaria.

**Sugerencia:** Consulte la [*Spark dataframe
documentation*](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/dataframe.html) para
obtener más información sobre los métodos del objeto Dataframe.

### Tarea 2: Guardar los datos transformados

1.  **Agregue una nueva celda** con el siguiente código para guardar el
    dataframe transformado en formato Parquet (sobrescribiendo los datos
    si ya existen). **Ejecute** la celda y espere a que aparezca el
    mensaje que indica que los datos se han guardado.

```
transformed_df.write.mode("overwrite").parquet('Files/transformed_data/orders')
print ("Transformed data saved!")
```

**Nota:** Comúnmente, se prefiere el formato *Parquet* para los archivos
de datos que se utilizarán para análisis posteriores o para la ingesta
en un almacén analítico. Parquet es un formato muy eficiente compatible
con la mayoría de los sistemas de análisis de datos a gran escala. De
hecho, en ocasiones, el requisito de transformación de datos puede
consistir simplemente en convertir los datos de otro formato (como CSV)
a Parquet.

![](./media/image45.png)

2.  A continuación, en el panel **Lakehouse explorer** de la izquierda,
    en el menú **…** del nodo **Files**, seleccione **Refresh**.

![](./media/image46.png)

3.  Haga clic en la carpeta **transformed_data** para comprobar que
    contiene una nueva carpeta denominada **orders**, que a su vez
    contiene uno o varios **archivos Parquet**.

![](./media/image47.png)

4.  Haga clic en **+ Code** y agregue el siguiente código para cargar un
    nuevo dataframe a partir de los archivos Parquet de la carpeta
    **transformed_data -\> orders**:

```
orders_df = spark.read.format("parquet").load("Files/transformed_data/orders")
display(orders_df)
```

5.  **Ejecute** la celda y compruebe que los resultados muestran los
    datos de pedidos que se han cargado desde los archivos Parquet.

![](./media/image48.png)

### Tarea 3: Guardar datos en archivos particionados

1.  Agregue una nueva celda haciendo clic en **+ Code** con el siguiente
    código, que guarda el dataframe y particiona los datos por **Year**
    y **Month**. Ejecute la celda y espere a que aparezca el mensaje que
    indica que los datos se han guardado.

```
orders_df.write.partitionBy("Year","Month").mode("overwrite").parquet("Files/partitioned_data")
print ("Transformed data saved!")
```

![](./media/image49.png)

2.  A continuación, en el panel **Lakehouse explorer** de la izquierda,
    en el menú de tres puntos **(…)** del nodo **Files**, seleccione
    **Refresh.**

![](./media/image50.png)

3.  Expanda la carpeta **partitioned_orders** para comprobar que
    contiene una jerarquía de carpetas denominadas **Year=xxxx**, cada
    una de las cuales contiene carpetas denominadas **Month=xxxx**. Cada
    carpeta de mes contiene un archivo Parquet con los pedidos
    correspondientes a ese mes.

![](./media/image51.png)

![](./media/image52.png)

La partición de los archivos de datos es una forma habitual de optimizar
el rendimiento cuando se trabaja con grandes volúmenes de datos. Esta
técnica puede mejorar significativamente el rendimiento y facilitar el
filtrado de datos.

4.  Agregue una nueva celda, haga clic en **+ Code** y agregue el
    siguiente código para cargar un nuevo dataframe desde el archivo
    **orders.parquet:**

```
orders_2021_df = spark.read.format("parquet").load("Files/partitioned_data/Year=2021/Month=*")
display(orders_2021_df)
```

5.  **Ejecute** la celda y compruebe que los resultados muestran los
    datos de los pedidos correspondientes a las ventas de 2021. Tenga en
    cuenta que las columnas de partición especificadas en la ruta
    (**Year** y **Month**) no se incluyen en el dataframe.

![](./media/image53.png)

## Ejercicio 4: Trabajar con tablas y SQL

Como ha visto, los métodos nativos del objeto dataframe permiten
consultar y analizar datos de un archivo de forma bastante eficaz. Sin
embargo, muchos analistas de datos se sienten más cómodos trabajando con
tablas que pueden consultar mediante la sintaxis SQL. Spark proporciona
un metastore en el que puede definir tablas relacionales. La biblioteca
Spark SQL, que proporciona el objeto dataframe, también admite el uso de
instrucciones SQL para consultar tablas en el *metastore*. Al utilizar
estas capacidades de Spark, puede combinar la flexibilidad de un data
lake con el esquema de datos estructurado y las consultas basadas en SQL
de un almacén de datos relacional; de ahí el término «data lakehouse».

### Tarea 1: Crear una tabla administrada

Las tablas de un metastore de Spark son abstracciones relacionales sobre
los archivos de un data lake. Las tablas pueden ser *administradas* (en
cuyo caso los archivos son administrados por el metastore) o *externas*
(en cuyo caso la tabla hace referencia a una ubicación de archivos en el
data lake que se administra independientemente del metastore).

1.  Agregue una nueva celda de código haciendo clic en **+ Code** en el
    notebook e introduzca el siguiente código, que guarda el dataframe
    de datos de pedidos de ventas como una tabla denominada
    **salesorders:**

```
# Create a new table
df.write.format("delta").saveAsTable("salesorders")

# Get the table description
spark.sql("DESCRIBE EXTENDED salesorders").show(truncate=False)
```

**Nota:** Cabe destacar un par de aspectos sobre este ejemplo. En primer
lugar, no se proporciona ninguna ruta explícita, por lo que los archivos
de la tabla serán administrados por el metastore. En segundo lugar, la
tabla se guarda en formato **delta**. Puede crear tablas basadas en
varios formatos de archivo (incluidos CSV, Parquet, Avro y otros), pero
*delta lake* es una tecnología de Spark que agrega capacidades de bases
de datos relacionales a las tablas, incluida la compatibilidad con
transacciones, versiones de filas y otras características útiles. Se
recomienda crear tablas en formato delta para los data lakehouses en
Fabric.

2.  **Ejecute** la celda de código y revise el resultado, que describe
    la definición de la nueva tabla.

![](./media/image54.png)

3.  En el panel **Lakehouse explorer**, en el menú de tres puntos
    **(…)** de la carpeta **Tables**, seleccione **Refresh.**

![](./media/image55.png)

4.  A continuación, expanda el nodo **Tables** y compruebe que la tabla
    **salesorders** se haya creado en el esquema **dbo**.

![](./media/image56.png)

5.  Coloque el cursor sobre la tabla **salesorders** y, a continuación,
    haga clic en los puntos suspensivos horizontales **(…)**. Vaya a
    **Load data** y haga clic en esta opción; después, seleccione
    **Spark.**

![](./media/image57.png)

6.  Haga clic en el botón **▷ Run cell**, que utiliza la biblioteca
    **Spark SQL** para incorporar una consulta SQL a la tabla
    **salesorder** en el código de PySpark y cargar los resultados de la
    consulta en un dataframe.

```
df = spark.sql("SELECT * FROM [your_lakehouse].salesorders LIMIT 1000")
display(df)
```

![](./media/image58.png)

### Tarea 2: Crear una tabla externa

También puede crear tablas externas para las que los metadatos del
esquema se definen en el metastore del lakehouse, pero los archivos de
datos se almacenan en una ubicación externa.

1.  Debajo de los resultados devueltos por la primera celda de código,
    utilice el botón **+ Code** para agregar una nueva celda de código
    si aún no existe. A continuación, introduzca el siguiente código en
    la nueva celda.

```
df.write.format("delta").saveAsTable("external_salesorder", path="<abfs_path>/external_salesorder")
```

![](./media/image59.png)

2.  En el panel **Lakehouse explorer**, en el menú de tres puntos
    **(…)** de la carpeta **Files**, seleccione **Copy ABFS path** y
    péguelo en el bloc de notas:

abfss://<dp_Fabric29@onelake.dfs.fabric.microsoft.com>/Fabric_lakehouse.Lakehouse/Files/external_salesorder

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image60.png)

3.  Ahora, vaya a la celda de código y reemplace **\<abfs_path\>** por
    la **ruta** que copió en el bloc de notas, de modo que el código
    guarde el dataframe como una tabla externa con los archivos de datos
    en una carpeta denominada **external_salesorder** dentro de la
    ubicación de la carpeta **Files**. La ruta completa debería ser
    similar a la siguiente:

abfss://<dp_Fabric29@onelake.dfs.fabric.microsoft.com>/Fabric_lakehouse.Lakehouse/Files/external_salesorder

4.  Utilice el botón **▷ (Run cell)** situado a la izquierda de la celda
    para ejecutarla.

![](./media/image61.png)

5.  En el panel **Lakehouse explorer**, en el menú de tres puntos
    **(…)** de la carpeta Tables, seleccione **Refresh**.

![](./media/image62.png)

6.  En el panel **Lakehouse explorer**, en el menú de tres puntos
    **(…)** de la carpeta **Tables**, seleccione **Refresh**.

![](./media/image63.png)

7.  En el panel **Lakehouse explorer**, en el menú de tres puntos
    **(…)** de la carpeta **Files**, seleccione **Refresh**.

![](./media/image64.png)

8.  A continuación, expanda el nodo **Files** y compruebe que se haya
    creado la carpeta **external_salesorder** para los archivos de datos
    de la tabla.

![](./media/image65.png)

### Tarea 3: Comparar tablas administradas y externas

Exploremos las diferencias entre las tablas administradas y externas.

1.  Debajo de los resultados devueltos por la celda de código, utilice
    el botón **+ Code** para agregar una nueva celda de código. Copie el
    código siguiente en la celda de código y utilice el botón **▷ (*Run
    cell*)** situado a la izquierda de la celda para ejecutarlo.

```
%%sql

DESCRIBE FORMATTED salesorders;
```

![](./media/image66.png)

2.  En los resultados, consulte la propiedad **Location** de la tabla,
    que debería ser una ruta al almacenamiento de OneLake para el
    lakehouse que termina en **/Tables/salesorders** (es posible que
    deba ampliar la columna **Data type** para ver la ruta completa).

> ![](./media/image67.png)

3.  Modifique el comando **DESCRIBE** para mostrar los detalles de la
    tabla **external_saleorder**, como se muestra aquí.

4.  Debajo de los resultados devueltos por la celda de código, utilice
    el botón **+ Code** para agregar una nueva celda de código. Copie el
    código siguiente y utilice el botón **▷ (Run cell)** situado a la
    izquierda de la celda para ejecutarlo.

```
%%sql

DESCRIBE FORMATTED external_salesorder;
```

5.  En los resultados, consulte la propiedad **Location** de la tabla,
    que debería ser una ruta al almacenamiento de OneLake para el
    lakehouse que termina en **/Files/external_salesorder** (es posible
    que deba ampliar la columna **Data type** para ver la ruta
    completa).

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image68.png)

### Tarea 4: Ejecutar código SQL en una celda

Aunque es útil poder incorporar instrucciones SQL en una celda que
contiene código de PySpark, los analistas de datos a menudo simplemente
quieren trabajar directamente en SQL.

1.  Haga clic en **+ Code** para agregar una celda al notebook e
    introduzca el siguiente código en ella. Haga clic en el botón **▷
    Run cell** y revise los resultados. Observe que:

    - La línea %%**sql** al principio de la celda (denominada *magic*)
      indica que se debe utilizar el runtime del lenguaje **Spark SQL**
      para ejecutar el código de esta celda en lugar de **PySpark**.

    - El código SQL hace referencia a la tabla **salesorders** que creó
      anteriormente.

    - El resultado de la consulta SQL se muestra automáticamente debajo
      de la celda.

```
%%sql
SELECT YEAR(OrderDate) AS OrderYear,
       SUM((UnitPrice * Quantity) + Tax) AS GrossRevenue
FROM salesorders
GROUP BY YEAR(OrderDate)
ORDER BY OrderYear;
```

![](./media/image69.png)

**Nota:** Para obtener más información sobre Spark SQL y dataframes,
consulte la [*Spark SQL
documentation*](https://spark.apache.org/docs/2.2.0/sql-programming-guide.html).

## Ejercicio 4: Visualizar datos con Spark

Una imagen vale más que mil palabras y, a menudo, un gráfico es mejor
que mil filas de datos. Aunque los notebooks de Fabric incluyen una
vista de gráficos integrada para los datos que se muestran desde un
dataframe o una consulta de Spark SQL, no está diseñada para crear
gráficos completos. Sin embargo, puede utilizar bibliotecas de gráficos
de Python como **matplotlib** y **seaborn** para crear gráficos a partir
de los datos de los dataframes.

### Tarea 1: Ver los resultados como un gráfico

1.  Haga clic en **+ Code** para agregar una celda al notebook e
    introduzca el siguiente código en ella. Haga clic en el botón **▷
    Run cell** y observe que devuelve los datos de la vista
    **salesorders** que creó anteriormente.

```
%%sql
SELECT * FROM salesorders
```

![](./media/image70.png)

2.  En la sección de resultados situada debajo de la celda, cambie la
    opción **View de Table** a **+New chart**.

![](./media/image71.png)

3.  Utilice el botón **Start editing** situado en la parte superior
    derecha del gráfico para mostrar el panel de opciones del gráfico. A
    continuación, configure las opciones como se indica y seleccione
    **Apply:**

    - Chart type: Bar chart

    - X-axis: Item

    - Y-axis: Quantity

    - Series Group: –None–

    - Aggregation: Sum

    - Missing and NULL values: Display as 0

    - Stacked: Sin seleccionar

![](./media/image72.png)

![](./media/image73.png)

![](./media/image74.png)

4.  Compruebe que el gráfico tenga un aspecto similar al siguiente.

![](./media/image75.png)

### Tarea 2: Comenzar a trabajar con matplotlib

1.  Haga clic en **+ Code**, copie y pegue el siguiente código. Ejecute
    el código y observe que devuelve un **dataframe** de Spark que
    contiene los ingresos anuales.

```
sqlQuery = "SELECT CAST(YEAR(OrderDate) AS CHAR(4)) AS OrderYear, \
                SUM((UnitPrice * Quantity) + Tax) AS GrossRevenue \
            FROM salesorders \
            GROUP BY CAST(YEAR(OrderDate) AS CHAR(4)) \
            ORDER BY OrderYear"
df_spark = spark.sql(sqlQuery)
df_spark.show()
```

![](./media/image76.png)

2.  Para visualizar los datos como un gráfico, comenzaremos utilizando
    la biblioteca de Python **matplotlib**. Esta biblioteca es la
    biblioteca principal de gráficos en la que se basan muchas otras y
    proporciona una gran flexibilidad para crear gráficos.

3.  Haga clic en **+ Code**, copie y pegue el siguiente código.

```
from matplotlib import pyplot as plt

# matplotlib requires a Pandas dataframe, not a Spark one
df_sales = df_spark.toPandas()

# Create a bar plot of revenue by year
plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'])

# Display the plot
plt.show()
```

4.  Haga clic en el botón **Run cell** y revise los resultados, que
    consisten en un gráfico de columnas con los ingresos brutos totales
    de cada año. Observe las siguientes características del código
    utilizado para generar este gráfico:

    - La biblioteca **matplotlib** requiere un *dataframe* de *Pandas*,
      por lo que debe convertir el dataframe de *Spark* devuelto por la
      consulta de Spark SQL a este formato.

    - En el núcleo de la biblioteca **matplotlib** se encuentra el
      objeto **pyplot**. Este es la base de la mayoría de las
      funcionalidades de gráficos.

    - La configuración predeterminada genera un gráfico utilizable, pero
      existe un amplio margen para personalizarlo.

![](./media/image77.png)

![](./media/image78.png)

5.  Modifique el código para representar el gráfico de la siguiente
    manera. Reemplace todo el código de la celda con el siguiente código
    y haga clic en el botón **▷ Run cell** para ejecutar la celda y
    revisar el resultado.

```
from matplotlib import pyplot as plt

# Clear the plot area
plt.clf()

# Create a bar plot of revenue by year
plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')

# Customize the chart
plt.title('Revenue by Year')
plt.xlabel('Year')
plt.ylabel('Revenue')
plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
plt.xticks(rotation=45)

# Show the figure
plt.show()
```

![](./media/image79.png)

![](./media/image80.png)

6.  Ahora el gráfico incluye un poco más de información. Técnicamente,
    un gráfico está contenido en una **Figure**. En los ejemplos
    anteriores, la figura se creó implícitamente; pero puede crearla
    explícitamente.

7.  Modifique el código para representar el gráfico de la siguiente
    manera. Reemplace todo el código de la **celda** con el siguiente
    código.

```
from matplotlib import pyplot as plt

# Clear the plot area
plt.clf()

# Create a Figure
fig = plt.figure(figsize=(8,3))

# Create a bar plot of revenue by year
plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')

# Customize the chart
plt.title('Revenue by Year')
plt.xlabel('Year')
plt.ylabel('Revenue')
plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
plt.xticks(rotation=45)

# Show the figure
plt.show()
```

8.  **Vuelva a ejecutar** la celda de código y revise los resultados. La
    Figure determina la forma y el tamaño del gráfico.

Una figura puede contener varios subgráficos, cada uno en su propio
**eje**.

![](./media/image81.png)

![](./media/image82.png)

9. Modifique el código para representar el gráfico de la siguiente
    manera. **Vuelva a ejecutar** la celda de código y revise los
    resultados. La figura contiene los subgráficos especificados en el
    código.

```
# Clear the plot area
plt.clf()

# Create a figure for 2 subplots (1 row, 2 columns)
fig, ax = plt.subplots(1, 2, figsize = (10,4))

# Create a bar plot of revenue by year on the first axis
ax[0].bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')
ax[0].set_title('Revenue by Year')

# Create a pie chart of yearly order counts on the second axis
yearly_counts = df_sales['OrderYear'].value_counts()
ax[1].pie(yearly_counts)
ax[1].set_title('Orders per Year')
ax[1].legend(yearly_counts.keys().tolist())

# Add a title to the Figure
fig.suptitle('Sales Data')

# Show the figure
plt.show()
```
![](./media/image83.png)

![](./media/image84.png)

**Nota:** Para obtener más información sobre cómo crear gráficos con
matplotlib, consulte el [*matplotlib
documentation*](https://matplotlib.org/).

### Tarea 3: Utilizar la biblioteca seaborn

Aunque **matplotlib** permite crear gráficos complejos de varios tipos,
puede requerir código complejo para obtener los mejores resultados. Por
este motivo, a lo largo de los años se han creado muchas bibliotecas
nuevas basadas en matplotlib para abstraer su complejidad y ampliar sus
capacidades. Una de estas bibliotecas es **seaborn**.

1.  Haga clic en **+ Code**, copie y pegue el siguiente código.

```
import seaborn as sns

# Clear the plot area
plt.clf()

# Create a bar chart
ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
plt.show()
```

2.  **Ejecute** el código y observe que muestra un gráfico de barras
    utilizando la biblioteca seaborn.

![](./media/image85.png)

![](./media/image86.png)

3.  **Modifique** el código de la siguiente manera. **Ejecute el
    código** modificado y observe que seaborn permite establecer un tema
    de color coherente para los gráficos.

```
import seaborn as sns

# Clear the plot area
plt.clf()

# Set the visual theme for seaborn
sns.set_theme(style="whitegrid")

# Create a bar chart
ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
plt.show()
```

![](./media/image87.png)

![](./media/image88.png)

4.  **Modifique** nuevamente el código de la siguiente manera.
    **Ejecute** el código modificado para ver los ingresos anuales como
    un gráfico de líneas.

```
import seaborn as sns

# Clear the plot area
plt.clf()

# Create a bar chart
ax = sns.lineplot(x="OrderYear", y="GrossRevenue", data=df_sales)
plt.show()
```
![](./media/image89.png)

![](./media/image90.png)

**Nota:** Para obtener más información sobre cómo crear gráficos con
seaborn, consulte el [*seaborn
documentation*](https://seaborn.pydata.org/index.html).

### Tarea 4: Utilizar tablas delta para datos de streaming

Delta Lake admite datos de streaming. Las tablas delta pueden ser un
*sink* o un source para los flujos de datos creados mediante la API de
Spark Structured Streaming. En este ejemplo, utilizará una tabla delta
como sink para algunos datos de streaming en un escenario simulado de
Internet de las cosas (IoT).

1.  Haga clic en **+ Code**, copie y pegue el código siguiente y, a
    continuación, haga clic en el botón **Run cell**.

```
from notebookutils import mssparkutils
from pyspark.sql.types import *
from pyspark.sql.functions import *

# Create a folder
inputPath = 'Files/data/'
mssparkutils.fs.mkdirs(inputPath)

# Create a stream that reads data from the folder, using a JSON schema
jsonSchema = StructType([
StructField("device", StringType(), False),
StructField("status", StringType(), False)
])
iotstream = spark.readStream.schema(jsonSchema).option("maxFilesPerTrigger", 1).json(inputPath)

# Write some event data to the folder
device_data = '''{"device":"Dev1","status":"ok"}
{"device":"Dev1","status":"ok"}
{"device":"Dev1","status":"ok"}
{"device":"Dev2","status":"error"}
{"device":"Dev1","status":"ok"}
{"device":"Dev1","status":"error"}
{"device":"Dev2","status":"ok"}
{"device":"Dev2","status":"error"}
{"device":"Dev1","status":"ok"}'''
mssparkutils.fs.put(inputPath + "data.txt", device_data, True)
print("Source stream created...")
```

![](./media/image91.png)

2.  Asegúrese de que se muestre el mensaje ***Source stream created*…**.
    El código que acaba de ejecutar ha creado un origen de datos de
    streaming basado en una carpeta en la que se han guardado algunos
    datos, que representan lecturas de dispositivos IoT hipotéticos.

3.  Haga clic en **+ Code**, copie y pegue el código siguiente y, a
    continuación, haga clic en el botón **Run cell**.

```
# Write the stream to a delta table
delta_stream_table_path = 'Tables/dbo/iotdevicedata'
checkpointpath = 'Files/delta/checkpoint'
deltastream = iotstream.writeStream.format("delta").option("checkpointLocation", checkpointpath).start(delta_stream_table_path)
print("Streaming to delta sink...")
```

![](./media/image92.png)

4.  Este código escribe los datos de los dispositivos de streaming en
    formato delta en una carpeta denominada **iotdevicedata**. Dado que
    la ruta de la ubicación de la carpeta se encuentra en la carpeta
    **Tables**, se creará automáticamente una tabla para ella. Haga clic
    en los puntos suspensivos horizontales (…) situados junto a
    **Tables** y, a continuación, haga clic en **Refresh**.

![](./media/image93.png)

![](./media/image94.png)

5.  Haga clic en **+ Code**, copie y pegue el código siguiente y, a
    continuación, haga clic en el botón **Run cell**.

```
%%sql
SELECT * FROM dbo.iotdevicedata;
```

![](./media/image95.png)

6.  Este código consulta la tabla **IotDeviceData**, que contiene los
    datos de los dispositivos del origen de streaming.

7.  Haga clic en **+ Code**, copie y pegue el código siguiente y, a
    continuación, haga clic en el botón **Run cell**.

```
# Add more data to the source stream
more_data = '''{"device":"Dev1","status":"ok"}
{"device":"Dev1","status":"ok"}
{"device":"Dev1","status":"ok"}
{"device":"Dev1","status":"ok"}
{"device":"Dev1","status":"error"}
{"device":"Dev2","status":"error"}
{"device":"Dev1","status":"ok"}'''

mssparkutils.fs.put(inputPath + "more-data.txt", more_data, True)
```

![](./media/image96.png)

8.  Este código escribe más datos hipotéticos de dispositivos en el
    origen de **streaming**.

9.  Haga clic en **+ Code**, copie y pegue el código siguiente y, a
    continuación, haga clic en el botón **Run cell**.

```
%%sql
SELECT * FROM dbo.iotdevicedata;
```

![](./media/image97.png)

10. Este código consulta nuevamente la tabla **IotDeviceData**, que
    ahora debería incluir los datos adicionales que se agregaron al
    origen de **streaming**.

11. Haga clic en **+ Code**, copie y pegue el código siguiente y, a
    continuación, haga clic en el botón **Run cell**.

> deltastream.stop()

![](./media/image98.png)

12. Este código detiene el streaming.

### Tarea 5: Guardar el notebook y finalizar la sesión de Spark

Ahora que ha terminado de trabajar con los datos, puede guardar el
notebook con un nombre significativo y finalizar la sesión de Spark.

1.  En la barra de menús del notebook, utilice el icono ⚙️ **Settings**
    para ver la configuración del notebook.

![](./media/image99.png)

2.  Establezca el **Name** del notebook en +++**Explore Sales
    Orders**+++ y, a continuación, cierre el panel de configuración.

![](./media/image100.png)

3.  En el menú del notebook, seleccione **Stop session** para finalizar
    la sesión de Spark.

![](./media/image101.png)

![A screenshot of a computer Description automatically
generated](./media/image102.png)

### Tarea 6: Eliminar los recursos

En este ejercicio, ha aprendido a utilizar Spark para trabajar con datos
en Microsoft Fabric.

Si ha terminado de explorar su lakehouse, puede eliminar el workspace
que creó para este ejercicio.

1.  En la barra de la izquierda, seleccione el icono de su workspace
    para ver todos los elementos que contiene.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image103.png)

2.  En el menú de tres puntos **(…)** de la barra de herramientas,
    seleccione **Workspace settings**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image104.png)

3.  Seleccione **General** y haga clic en **Remove this workspace.**

![A screenshot of a computer settings Description automatically
generated](./media/image105.png)

4.  En el cuadro de diálogo **Delete workspace?**, haga clic en el botón
    **Delete**.

![A screenshot of a computer Description automatically
generated](./media/image106.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image107.png)

**Resumen**

Este caso de uso le guía a través del proceso de trabajar con Microsoft
Fabric en Power BI. Abarca diversas tareas, como configurar un
workspace, crear un lakehouse, cargar y administrar archivos de datos, y
utilizar notebooks para explorar datos. Los participantes aprenderán a
manipular y transformar datos mediante PySpark, crear visualizaciones y
guardar y particionar datos para realizar consultas de manera eficiente.

En este caso de uso, los participantes realizarán una serie de tareas
centradas en trabajar con tablas delta en Microsoft Fabric. Las tareas
incluyen cargar y explorar datos, crear tablas delta administradas y
externas y comparar sus propiedades. El laboratorio introduce las
funcionalidades de SQL para administrar datos estructurados y
proporciona información sobre la visualización de datos mediante
bibliotecas de Python como matplotlib y seaborn. Los ejercicios tienen
como objetivo proporcionar una comprensión integral del uso de Microsoft
Fabric para el análisis de datos e incorporar tablas delta para datos de
streaming en un contexto de IoT.
