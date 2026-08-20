# Caso de uso 01: Crear un Lakehouse, ingerir datos de ejemplo y crear un informe

**Escenario**

**Wide World Importers (WWI)** es una organización minorista global que
opera cientos de tiendas en varias regiones. La información de los
clientes se recopila de diversos sistemas operativos, incluidas
aplicaciones de punto de venta (POS), plataformas de CRM y canales de
comercio electrónico. Los datos se almacenan como archivos CSV y se
reciben diariamente de diferentes unidades de negocio.

Actualmente, el equipo de análisis de la empresa dedica una cantidad
considerable de tiempo a importar archivos manualmente, validar la
calidad de los datos y preparar conjuntos de datos para la generación de
informes. Estos procesos manuales provocan retrasos en la generación de
información sobre los clientes y dificultan que los usuarios
empresariales accedan a información coherente y fiable.

Para modernizar su plataforma de análisis, Wide World Importers ha
adoptado Microsoft Fabric como su plataforma de datos unificada. El
equipo de ingeniería de datos tiene la tarea de implementar una solución
escalable mediante Microsoft Fabric Data Factory y Lakehouse para
centralizar los datos de clientes, permitir una gestión eficiente de los
datos y simplificar la generación de informes.

Como Data Engineer, su responsabilidad es crear un workspace de Fabric,
aprovisionar un Lakehouse, ingerir los datos de clientes en OneLake,
convertir los archivos de origen en tablas Delta administradas, validar
los datos importados mediante SQL analytics endpoint, crear un modelo
semántico Direct Lake y generar un informe de Power BI que permita a las
partes interesadas del negocio analizar la información de los clientes
con una latencia mínima.

Al implementar esta solución, Wide World Importers puede eliminar la
preparación manual de datos, proporcionar una única fuente de verdad
para el análisis de clientes y permitir decisiones empresariales más
rápidas basadas en datos mediante Microsoft Fabric.

**Introducción**

En este caso de uso, creará una solución completa de ingeniería de datos
mediante **Microsoft Fabric Data Factory** y **Fabric Lakehouse**.
Comenzando con un nuevo workspace de Fabric, ingerirá datos en un
Lakehouse, convertirá los archivos en tablas Delta administradas,
consultará los datos mediante SQL analytics endpoints, creará modelos
semánticos y generará informes interactivos de Power BI.

A lo largo del laboratorio, explorará cómo Microsoft Fabric unifica la
integración, el almacenamiento, la transformación, el análisis y la
generación de informes de datos en una única plataforma de Software como
servicio (SaaS). Al completar este ejercicio práctico, comprenderá cómo
se implementan los flujos de trabajo modernos de ingeniería de datos
mediante Fabric Data Factory, siguiendo las prácticas recomendadas del
sector para la ingesta, la administración y el análisis de datos.

**Objetivos:**

- Crear y configurar un workspace de Microsoft Fabric.

- Crear y configurar un Fabric Lakehouse.

- Ingerir datos de origen en OneLake.

- Cargar archivos en tablas Delta administradas.

- Consultar datos de Lakehouse mediante SQL Analytics Endpoint.

- Crear un modelo semántico Direct Lake.

- Generar y explorar informes de Power BI a partir de datos de Fabric.

- Comprender cómo Fabric Data Factory integra la ingeniería de datos y
  el análisis en una plataforma unificada.

## Ejercicio 1: Configurar el entorno de ingeniería de datos de Microsoft Fabric 

Antes de crear una solución de ingeniería de datos, debe preparar el
entorno de Microsoft Fabric. En este ejercicio, iniciará sesión en
Microsoft Fabric, creará un workspace dedicado y aprovisionará un
Lakehouse que servirá como almacenamiento centralizado para su solución
de análisis.

### Tarea 1: Iniciar sesión en la cuenta de Power BI

1.  Abra el navegador, vaya a la barra de direcciones y escriba o pegue
    la siguiente URL:+++https://app.fabric.microsoft.com/+++ y, a
    continuación, presione el botón **Enter**.

![](./media/image1.png)

2.  En la ventana de **Microsoft Fabric**, introduzca sus credenciales y
    haga clic en el botón **Submit.**

| Credential | Value |
|---|---|
| Username | +++@lab.CloudPortalCredential(User1).Username+++ |
| Password | +++@lab.CloudPortalCredential(User1).Password+++ |

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image2.png)

3.  A continuación, en la ventana de **Microsoft**, introduzca la
    contraseña y haga clic en el botón **Sign in**.

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image3.png)

4.  En la ventana **Stay signed in?,** haga clic en el botón **Yes**.

5.  Se le dirigirá a la página principal de Power BI.

> ![](./media/image4.png)

6.  Seleccione el icono predeterminado de Power BI situado en la parte
    inferior izquierda de la pantalla y seleccione **Fabric**.

> ![](./media/image5.png)
>
> ![](./media/image6.png)

### Tarea 2: Crear un workspace de Fabric

En esta tarea, creará un workspace de Fabric. El workspace contiene
todos los elementos necesarios para este tutorial de Lakehouse,
incluidos el lakehouse, los flujos de datos, las canalizaciones de Data
Factory, los notebooks, los conjuntos de datos de Power BI y los
informes.

1.  En la página principal de Fabric, seleccione el mosaico **+New
    workspace**.

![](./media/image7.png)

2.  En el panel **Create a workspace** que aparece en el lado derecho,
    introduzca los siguientes detalles y haga clic en el botón
    **Apply**.

| Property | Value |
|---|---|
| Name | +++Fabric Dataengineering-DataFactoryXXXXXX+++ |
| Advanced | Under License mode, select Fabric |
| Default storage format | Small dataset storage format |

![](./media/image8.png)

Nota: Para encontrar el ID de instancia del laboratorio, seleccione
**Help** y copie el **ID** de instancia.

![A screenshot of a computer Description automatically
generated](./media/image9.png)

![](./media/image10.png)

![](./media/image11.png)

3.  Espere a que finalice la implementación. El proceso tarda entre 2 y
    3 minutos.

![](./media/image12.png)

### Tarea 3: Crear un lakehouse

1.  Cree un nuevo lakehouse haciendo clic en el botón **+New item** de
    la barra de navegación.

![](./media/image13.png)

2.  Haga clic en el mosaico **Lakehouse**.

![](./media/image14.png)

3.  En el cuadro de diálogo **New lakehouse**, introduzca
    +++**wwilakehouse**+++ en el campo **Name**, desactive **lakehouse
    schemas**, haga clic en el botón **Create** y abra el nuevo
    lakehouse.

**Nota:** Asegúrese de eliminar el espacio antes de **wwilakehouse.**

![](./media/image15.png)

4.  Verá una notificación que indica **Successfully created SQL
    endpoint**.

![](./media/image16.png)

### Tarea 4: Ingerir datos de ejemplo

1.  En la página **wwilakehouse**, vaya a la sección **Get data in your
    lakehouse** y haga clic en **Upload files**, como se muestra en la
    siguiente imagen.

![](./media/image17.png)

2.  En la pestaña **Upload files**, haga clic en la carpeta situada
    debajo de **Files**.

![](./media/image18.png)

3.  Vaya a **C:\LabFiles** en su **VM,** seleccione el archivo
    **dimension_customer.csv** y haga clic en el botón **Open**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image19.png)

4.  A continuación, haga clic en el botón **Upload** y cierre la
    Ventana.

![](./media/image20.png)

5.  **Cierre** el panel **Upload files**.

![](./media/image21.png)

6.  Haga clic y seleccione **Refresh** en **Files**. Aparecerá el
    archivo.

![](./media/image22.png)

7.  En la página **Lakehouse**, en el panel **Explorer**, seleccione
    **Files**. A continuación, coloque el cursor sobre el archivo
    **dimension_customer.csv**. Haga clic en los puntos suspensivos
    horizontales (…) situados junto a **dimension_customer.csv**. Vaya a
    **Load Table** y haga clic en esta opción; después, seleccione **New
    table.**

![](./media/image23.png)

> ![](./media/image24.png)

8.  En el cuadro de diálogo **Load file** **to new table**, haga clic en
    el botón **Load**.

![](./media/image25.png)

9.  Ahora se ha creado correctamente la tabla **dimension_customer**.

![](./media/image26.png)

10. Seleccione la tabla **dimension_customer** en **Tables**.

![](./media/image27.png)

11. También puede utilizar el **SQL analytics endpoi**nt del lakehouse
    para consultar los datos mediante instrucciones SQL. Seleccione
    **SQL analytics endpoint** en el menú desplegable **Analyze data
    with**, situado en la parte superior derecha de la pantalla.

![](./media/image28.png)

12. En la página **wwilakehouse**, en **Explorer**, seleccione la tabla
    **dimension_customer** para obtener una vista previa de sus datos y
    seleccione **New SQL query** para escribir sus instrucciones SQL.

![](./media/image29.png)

13. La siguiente consulta de ejemplo agrega el recuento de filas en
    función de la columna **BuyingGroup** de la tabla
    **dimension_customer**. Los archivos de consultas SQL se guardan
    automáticamente para futuras referencias, y puede cambiarles el
    nombre o eliminarlos según sus necesidades. Pegue el código como se
    muestra en la siguiente imagen y, a continuación, haga clic en el
    icono de reproducción para ejecutar el script:

```
SELECT BuyingGroup, Count(*) AS Total
FROM dimension_customer
GROUP BY BuyingGroup
```

![](./media/image30.png)

**Nota:** Si encuentra un error durante la ejecución del script,
compruebe la sintaxis del script para asegurarse de que no contenga
espacios innecesarios.

14. Previously all the lakehouse tables and views were automatically
    added to the semantic model. With the recent updates, for new
    lakehouses, you have to manually add your tables to the semantic
    model.

15. En la pestaña **Home** del lakehouse, seleccione **New semantic
    model** y seleccione las tablas que desea agregar al modelo
    semántico.

> ![](./media/image31.png)

16. En el cuadro de diálogo **New semantic model**, introduzca
    +++**wwwsemanticmodel**+++ y, a continuación, seleccione la tabla
    dimension**\_customer** de la lista de tablas y seleccione
    **Confirm** para crear el nuevo modelo.

![](./media/image32.png)

### Tarea 5: Crear un informe

1.  En el panel de navegación izquierdo, seleccione **Fabric
    Dataengineering-DataFactory-XX**.

![](./media/image33.png)

2.  En su workspace, busque el modelo semántico que creó, seleccione el
    menú de puntos suspensivos (…) y, a continuación, seleccione
    **Auto-create report**.

![](./media/image34.png)

![](./media/image35.png)

4.  Ahora que el informe está listo, haga clic en **View report now**
    para abrirlo y revisarlo.

> ![](./media/image36.png)

![](./media/image37.png)

5.  Dado que la tabla es una dimensión y no contiene medidas, Power BI
    crea una medida para el recuento de filas, la agrega en las
    diferentes columnas y crea distintos gráficos, como se muestra en la
    siguiente imagen.

6.  Guarde este informe para utilizarlo en el futuro seleccionando
    **Save** en la cinta superior.

![](./media/image38.png)

7.  En el cuadro de diálogo **Save your report**, introduzca
    +++**dimension_customer-report**+++ como nombre del informe y
    seleccione **Save.**

![](./media/image39.png)

8.  Verá una notificación que indica **Report saved**.

![](./media/image40.png)

## Ejercicio 2: Ingerir y administrar datos en Fabric Lakehouse

En este ejercicio, ingerirá tablas dimensionales y de hechos adicionales
de Wide World Importers (WWI) en el lakehouse.

### Tarea 1: Ingerir datos

1.  En el panel de navegación izquierdo, seleccione **Fabric
    Dataengineering-DataFactory-XX.**

![](./media/image41.png)

2.  En la página del workspace **Fabric
    Dataengineering-DataFactory-XX**, vaya a **+New item**, haga clic en
    este botón y, a continuación, seleccione **Pipeline**.

![](./media/image42.png)

3.  En el cuadro de diálogo New pipeline, especifique el nombre
    **+++IngestDataFromSourceToLakehouse+++** y seleccione **Create**.
    Se crea y se abre una nueva canalización de **Data Factory**.

![](./media/image43.png)

![](./media/image44.png)

4.  En la pestaña **Home** de la nueva canalización, seleccione
    **Pipeline activity \> Copy data**.

![](./media/image45.png)

5.  Seleccione la nueva actividad **Copy data** en el lienzo. Las
    propiedades de la actividad aparecen en un panel debajo del lienzo,
    organizadas en pestañas que incluyen **General**, **Source**,
    **Destination**, **Mapping** y **Settings**. Es posible que deba
    ampliar el panel hacia arriba arrastrando el borde superior.

![](./media/image46.png)

6.  En la pestaña **General**, introduzca +++**Data Copy to Lakehouse**+++ en el campo **Name**. Deje los demás campos con sus
    valores predeterminados.

![](./media/image47.png)

7.  En la pestaña **Source**, seleccione la lista desplegable
    **Connection** y, a continuación, seleccione **Browse all**.

![](./media/image48.png)

8.  En la página **Choose a data source to get started**, busque y
    seleccione **Azure blobs**.

![](./media/image49.png)

9.  Introduzca los siguientes detalles en la página **Connect data
    source**. A continuación, seleccione **Connect** para crear la
    conexión al origen de datos. Para este tutorial, todos los datos de
    ejemplo están disponibles en un contenedor público de Azure Blob
    Storage. Se conectará a este contenedor para copiar los datos desde
    él.

| Property | Value |
|---|---|
| Account name or URL | !!https://fabrictutorialdata.blob.core.windows.net/sampledata/!! |
| Connection | Create new connection |
| Connection name | !!wwisampledata!! |
| Authentication kind | Anonymous |

![](./media/image50.png)

10. En la pestaña **Source**, la conexión que acaba de crear se
    selecciona de forma predeterminada. Especifique las siguientes
    propiedades antes de pasar a la configuración del destino.

| Property | Value |
|---|---|
| Connection | wwisampledata |
| File path type | File path |
| File path | Container name (first text box): !!sampledata!!<br>Directory name (second text box): !!WideWorldImportersDW/parquet!! |
| Recursively | Checked |
| File format | Binary |

![](./media/image51.png)

11. En la pestaña **Destination**, especifique las siguientes
    propiedades:

| Property | Value |
|---|---|
| Connection | wwilakehouse (choose your lakehouse if you named it differently) |
| Root folder | Files |
| File path | Directory name (first text box): !!wwi-raw-data!! |
| File format | Binary |

![](./media/image52.png)

12. Haga clic en **Run** para ejecutar la copia de datos.

![](./media/image53.png)

13. Haga clic en el botón **Save and run** para guardar y ejecutar la
    canalización.

> ![](./media/image54.png)

14. El proceso de copia de datos tarda aproximadamente entre 1 y 2
    minutos en completarse.

![](./media/image55.png)

15. En la pestaña Output, seleccione **Data Copy to Lakehouse** para
    consultar los detalles de la transferencia de datos. Después de
    comprobar que el estado es **Succeeded**, haga clic en el botón
    **Close**.

![](./media/image56.png)

![](./media/image57.png)

16. Después de la ejecución correcta de la canalización, vaya a su
    lakehouse (**wwilakehouse**) y abra Explorer para ver los datos
    importados.

![](./media/image58.png)

17. Actualice la sección **Files** para ver los datos ingeridos.
    Aparecerá una nueva carpeta **wwi-raw-data** en la sección
    **Files**, y los datos de las tablas de **Azure Blob** se copiarán
    allí.
    ![](./media/image59.png)

## Ejercicio 3: Preparar y transformar datos en el lakehouse

### Tarea 1: Transformar datos y cargarlos en una tabla Delta silver

1.  En el panel de navegación izquierdo, seleccione **Fabric
    Dataengineering-DataFactory-XX.**

![](./media/image60.png)

2.  En la página de **Fabric**, vaya a **Import** en la barra de
    comandos, haga clic en la lista desplegable y, a continuación,
    seleccione **New notebook \> From this computer**.

![](./media/image61.png)

3.  Seleccione **Upload** en el panel **Import status** que se abre en
    el lado derecho de la pantalla.

> ![](./media/image62.png)

4.  Vaya a **C:\LabFiles** en su **VM**, seleccione el notebook
    **Prepare and transform data – PySpark** y haga clic en el botón
    **Open**.

> ![](./media/image63.png)
>
> ![](./media/image64.png)

5.  Seleccione el lakehouse **wwilakehouse** para abrirlo, de modo que
    el notebook que abra a continuación quede vinculado a él.

![](./media/image65.png)

6.  En la barra de herramientas, seleccione el menú desplegable
    **Analyze data with**, coloque el cursor sobre **Notebook** y, a
    continuación, seleccione **Existing notebook**.

> ![](./media/image66.png)

7.  Seleccione el notebook importado **Prepare and transform** **data –
    PySpark** y, a continuación, haga clic en **Open.**

> ![](./media/image67.png)
>
> ![](./media/image68.png)

### Tarea 2: Crear tablas Delta

En esta tarea, ejecutará las celdas del notebook para crear tablas Delta
a partir de los datos sin procesar.

Las tablas siguen un esquema de estrella, que es un patrón común para
organizar datos analíticos:

- Una tabla de hechos (**fact_sale**) contiene los eventos medibles del
  negocio; en este caso, transacciones de ventas individuales con
  cantidades, precios y beneficios.

- Las tablas de dimensiones (**dimension_city, dimension_customer,
  dimension_date, dimension_employee, dimension_stock_item**) contienen
  los atributos descriptivos que proporcionan contexto a los hechos,
  como dónde se realizó una venta, quién la realizó y cuándo.

1.  **Celda 1 - Configuración de la sesión de Spark.** Esta celda
    habilita dos características de Fabric que optimizan la forma en que
    se escriben y leen los datos en las celdas posteriores.
    [V-order](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order)
    optimiza el diseño de los archivos Parquet para obtener lecturas más
    rápidas y una mejor compresión. [Optimize
    write](https://learn.microsoft.com/en-us/fabric/data-engineering/tune-file-size#optimize-write)
    reduce el número de archivos escritos y aumenta el tamaño de los
    archivos individuales.

```
spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize", "1073741824")
```

2.  **Ejecute** esta celda y espere a que finalice antes de continuar
    con el siguiente paso.

> ![](./media/image69.png)
>
> ![](./media/image70.png)

3.  **Celda 2 - Fact - Sale.** Esta celda lee los datos sin procesar en
    formato Parquet de Files/wwi-raw-data/full/fact_sale_1y_full, agrega
    columnas correspondientes a partes de la fecha (**Year**,
    **Quarter** y **Month**) y escribe **fact_sale** como una tabla
    Delta particionada por **Year** y **Quarter**.

4.  Ejecute esta celda y espere a que finalice antes de continuar con el
    siguiente paso.

> ![](./media/image71.png)

5.  **Celda 3 -** Dimensions**.** Esta celda lee los cinco conjuntos de
    datos de dimensiones en formato Parquet y los escribe como tablas
    Delta (dimension_city, dimension_customer, dimension_date,
    dimension_employee y dimension_stock_item) en Tables/dbo/

6.  **Ejecute** esta celda y espere a que finalice antes de continuar
    con el siguiente paso.

> ![](./media/image72.png)

7.  Para validar las tablas creadas, haga clic con el botón derecho en
    el lakehouse wwilakehouse en **Explorer** y, a continuación,
    seleccione **Refresh**. Aparecerán las tablas.

> ![](./media/image73.png)
>
> ![](./media/image74.png)

### Tarea 3: Transformar datos empresariales para la agregación

En esta tarea, continuará en el mismo notebook y ejecutará las
siguientes celdas para crear tablas agregadas a partir de las tablas
Delta creadas en la sección anterior.

1.  Asegúrese de que el notebook siga vinculado a **wwilakehouse**.

2.  **Celda 4 - Cargar tablas de origen para la transformación (solo
    PySpark).** Si utiliza el notebook de PySpark, ejecute esta celda
    para cargar las tablas Delta en DataFrames para los pasos de
    agregación posteriores.

3.  Ejecute esta celda y espere a que finalice antes de continuar con el
    siguiente paso.

![](./media/image75.png)

4.  **Celda 5 - Crear aggregate_sale_by_date_city.** Esta celda combina
    los datos de ventas, fecha y ciudad y, a continuación, crea la tabla
    agregada a nivel de ciudad.

5.  Ejecute esta celda y espere a que finalice antes de continuar con el
    siguiente paso.

> ![](./media/image76.png)

6.  **Celda 6 – Crear aggregate_sale_by_date_employee.** Esta celda
    combina los datos de ventas, fecha y empleado y, a continuación,
    crea la tabla agregada a nivel de empleado.

7.  Ejecute esta celda y espere a que finalice antes de continuar con el
    siguiente paso.

> ![](./media/image77.png)

8.  Para validar las tablas creadas, haga clic con el botón derecho en
    el lakehouse **wwilakehouse** en **Explorer** y, a continuación,
    seleccione **Refresh**. Aparecerán las tablas agregadas.

> ![](./media/image78.png)
>
> ![](./media/image79.png)

## Ejercicio 4: Crear informes en Microsoft Fabric

En esta sección del tutorial, creará un modelo de datos de Power BI y
creará un informe desde cero.

### Tarea 1: Explorar los datos de la capa silver mediante SQL analytics endpoint

Power BI está integrado de forma nativa en toda la experiencia de
Fabric. Esta integración nativa incorpora un modo único, denominado
DirectLake, para acceder a los datos del lakehouse y proporcionar la
experiencia de consulta y generación de informes de mayor rendimiento.
El modo DirectLake es una nueva capacidad revolucionaria del motor para
analizar conjuntos de datos muy grandes en Power BI. Esta tecnología se
basa en la idea de cargar archivos con formato Parquet directamente
desde un lago de datos, sin tener que consultar un almacén de datos ni
un endpoint de lakehouse y sin tener que importar o duplicar los datos
en un conjunto de datos de Power BI. DirectLake proporciona una ruta
rápida para cargar los datos directamente desde el lago de datos al
motor de Power BI, listos para el análisis.

En el modo tradicional DirectQuery, el motor de Power BI consulta
directamente los datos del origen para ejecutar cada consulta, y el
rendimiento de las consultas depende de la velocidad de recuperación de
los datos. DirectQuery elimina la necesidad de copiar los datos, lo que
garantiza que cualquier cambio en el origen se refleje inmediatamente en
los resultados de la consulta durante la importación. Por otro lado, en
el modo Import, el rendimiento es mejor porque los datos están
disponibles directamente en la memoria sin necesidad de consultar el
origen para cada ejecución de una consulta. Sin embargo, el motor de
Power BI primero debe copiar los datos en la memoria durante la
actualización de los datos. Solo los cambios realizados en el origen de
datos subyacente se detectan durante la siguiente actualización de
datos, tanto en las actualizaciones programadas como en las iniciadas
bajo demanda.

El modo DirectLake elimina ahora este requisito de importación al cargar
los archivos de datos directamente en la memoria. Como no existe un
proceso de importación explícito, es posible detectar los cambios en el
origen a medida que se producen, combinando así las ventajas de
DirectQuery y del modo Import, al tiempo que se evitan sus desventajas.
Por lo tanto, el modo DirectLake es la opción ideal para analizar
conjuntos de datos muy grandes y conjuntos de datos que reciben
actualizaciones frecuentes en el origen.

1.  En el menú de la izquierda, seleccione **Fabric
    Dataengineering-DataFactory-@lab.LabInstance.Id** y, a continuación,
    seleccione el modelo semántico denominado **wwisemanticmodel**.

2.  Abra el modelo semántico, seleccione el menú desplegable del modo
    situado en la esquina superior derecha, cambie de **Viewing a
    Editing** y, a continuación, seleccione **Make any changes**.

![](./media/image80.png)

5.  En la cinta de opciones, seleccione **Edit tables** para mostrar el
    cuadro de diálogo de sincronización de tablas.

![](./media/image81.png)

6.  En el cuadro de diálogo **Edit semantic model**, seleccione todas
    las tablas y, a continuación, seleccione **Confirm** en la parte
    inferior del cuadro de diálogo para sincronizar el modelo semántico.

![](./media/image82.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image83.png)

7.  Desde la tabla **fact_sale**, arrastre el campo **CityKey** y
    colóquelo sobre el campo **CityKey** de la tabla **dimension_city**
    para crear una relación. Aparecerá el cuadro de diálogo **Create
    Relationship.**

**Nota:** Reorganice las tablas haciendo clic en una tabla,
arrastrándola y colocándola junto a la otra para que las tablas
**dimension_city y fact_sale** queden una junto a la otra. Lo mismo se
aplica a cualquier par de tablas entre las que intente crear una
relación. Esto facilita el proceso de arrastrar y colocar las columnas
entre las tablas. ![](./media/image84.png)

8.  En el cuadro de diálogo **Create Relationship**:

    - **Table 1** se completa con **fact_sale** y la columna
      **CityKey**.

    - **Table 2** se completa con **dimension_city** y la columna
      **CityKey**.

    - Cardinality: **Many to one (\*:1)**

    - Cross filter direction: **Single**

    - Deje seleccionada la casilla situada junto a **Make this
      relationship active**.

    - Seleccione la casilla situada junto a **Assume referential
      integrity**.

    - Seleccione **Save**

![](./media/image85.png)

9.  A continuación, agregue las siguientes relaciones con la misma
    configuración de **Create Relationship** que se mostró
    anteriormente, pero con las siguientes tablas y columnas:

    - **StockItemKey(fact_sale)** - **StockItemKey(dimension_stock_item)**

![](./media/image86.png)

![](./media/image87.png)

- **Salespersonkey(fact_sale)** - **EmployeeKey(dimension_employee)**

![](./media/image88.png)

10. Asegúrese de crear las relaciones entre los siguientes dos conjuntos
    utilizando los mismos pasos anteriores:

    - **CustomerKey(fact_sale)** - **CustomerKey(dimension_customer)**

    - **InvoiceDateKey(fact_sale)** - **Date(dimension_date)**

11. Después de agregar estas relaciones, el modelo de datos debería
    aparecer como se muestra en la siguiente imagen y estará listo para
    la generación de informes.

![](./media/image89.png)

### Tarea 2: Crear un informe

1.  En la cinta superior, seleccione **File** y, a continuación,
    seleccione **Create new report** para comenzar a crear informes o
    dashboards en **Power BI**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image90.png)

2.  En el lienzo del informe de Power BI, puede crear informes que
    cumplan sus requisitos empresariales arrastrando las columnas
    necesarias desde el panel **Data** hasta el lienzo y utilizando una
    o varias de las visualizaciones disponibles.

![](./media/image91.png)

**Agregar un título:**

3.  En la cinta de opciones, seleccione **Text box**. Escriba **WW
    Importers Profit Reporting**. Resalte el texto y aumente el tamaño a
    **20**.

![](./media/image92.png)

4.  Cambie el tamaño del cuadro de texto, colóquelo en la **parte
    superior izquierda** de la página del informe y haga clic fuera del
    cuadro de texto.

![](./media/image93.png)

**Agregar una tarjeta:**

- En el panel **Data**, expanda **fact_sales** y active la casilla
  situada junto a **Profit**. Esta selección crea un gráfico de columnas
  y agrega el campo al eje **Y**.

![](./media/image94.png)

5.  Con el gráfico de barras seleccionado, seleccione la visualización
    **Card** en el panel de visualizaciones.

![](./media/image95.png)

6.  Esta selección convierte la visualización en una tarjeta. Coloque la
    tarjeta debajo del título.

![](./media/image96.png)

7.  Haga clic en cualquier lugar del lienzo en blanco (o presione la
    tecla **Esc**) para que la **Card** que acaba de colocar deje de
    estar seleccionada.

**Agregar un gráfico de barras:**

8.  En el panel **Data**, expanda **fact_sales** y active la casilla
    situada junto a **Profit**. Esta selección crea un gráfico de
    columnas y agrega el campo al eje **Y**.

![](./media/image97.png)

9.  En el panel **Data**, expanda **dimension_city** y active la casilla
    de **SalesTerritory**. Esta selección agrega el campo al eje **Y**.

![](./media/image98.png)

10. Con el gráfico de barras seleccionado, seleccione la visualización
    **Clustered bar chart** en el panel de visualizaciones. Esta
    selección convierte el gráfico de columnas en un gráfico de barras.

![](./media/image99.png)

11. Cambie el tamaño del gráfico de barras para que ocupe el área
    situada debajo del título y la tarjeta.

![](./media/image100.png)

12. Haga clic en cualquier lugar del lienzo en blanco (o presione la
    tecla **Esc**) para que el gráfico de barras deje de estar
    seleccionado.

**Crear una visualización de gráfico de áreas apiladas:**

13. En el panel **Visualizations**, seleccione la visualización
    **Stacked area chart**.

![](./media/image101.png)

14. Cambie la posición y el tamaño del gráfico de áreas apiladas para
    colocarlo a la derecha de la tarjeta y del gráfico de barras creados
    en los pasos anteriores.

![](./media/image102.png)

15. En el panel **Data, expanda fact_sales** y active la casilla situada
    junto a **Profit**. Expanda **dimension_date** y active la casilla
    situada junto a **FiscalMonthNumber**. Esta selección crea un
    gráfico de áreas apiladas que muestra el beneficio por mes fiscal.

![](./media/image103.png)

16. En el panel **Data**, expanda **dimension_stock_item** y arrastre
    **BuyingPackage** al contenedor de campos **Legend**. Esta selección
    agrega una línea para cada uno de los **Buying Packages.**

![](./media/image104.png) ![](./media/image105.png)

17. Haga clic en cualquier lugar del lienzo en blanco (o presione la
    tecla Esc) para que el gráfico de áreas apiladas deje de estar
    seleccionado.

**Crear un gráfico de columnas:**

18. En el panel **Visualizations**, seleccione la visualización
    **Stacked column chart**.

![](./media/image106.png)

19. En el panel **Data**, expanda **fact_sales** y active la casilla
    situada junto a **Profit**. Esta selección agrega el campo al eje
    **Y**.

20.  En el panel **Data**, expanda **dimension_employee** y active la
    casilla situada junto a **Employee**. Esta selección agrega el campo
    al eje **X**.

![](./media/image107.png)

21. Haga clic en cualquier lugar del lienzo en blanco (o presione la
    tecla **Esc**) para que el gráfico deje de estar seleccionado.

22. En la cinta de opciones, seleccione **File \> Save**.

![](./media/image108.png)

23. Introduzca **Profit Reporting** como nombre del informe. Seleccione
    **Save**.

![](./media/image109.png)

24. Recibirá una notificación que indica que el informe se ha guardado. 

![](./media/image110.png)

# Ejercicio 7: Eliminar los recursos

Puede eliminar informes, canalizaciones, warehouses y otros elementos
individuales, o eliminar todo el workspace. Siga los pasos siguientes
para eliminar el workspace que creó para este tutorial.

1.  Seleccione su workspace, **Fabric
    <Dataengineering-DataFactory-@lab.LabInstance.Id>**, en el menú de
    navegación de la izquierda. Se abrirá la vista de elementos del
    workspace.

&nbsp;

2.  Seleccione la opción ... situada debajo del nombre del workspace y
    seleccione **Workspace settings**.

![](./media/image111.png)

3.  Seleccione **General** y **Remove this workspace.**

![](./media/image112.png)

4.  Haga clic en **Delete** en la advertencia que aparece.

![](./media/image113.png)

5.  Espere a recibir una notificación que indique que el workspace se ha
    eliminado antes de continuar con el siguiente laboratorio.

![](./media/image114.png)

**Resumen**

En este laboratorio, implementó un flujo de trabajo completo de
ingeniería de datos de Microsoft Fabric mediante la creación de un
workspace de Fabric y un Lakehouse, la ingesta de datos de origen, su
carga en tablas Delta, la validación de los datos mediante consultas
SQL, la creación de un modelo semántico y la generación de un informe de
Power BI. Estas actividades demuestran cómo Microsoft Fabric simplifica
el análisis moderno al combinar la integración, el almacenamiento y la
transformación de datos, el modelado semántico y la generación de
informes en una plataforma unificada. Los conocimientos adquiridos en
este laboratorio proporcionan la base para desarrollar soluciones
escalables de ingeniería de datos mediante Microsoft Fabric.
