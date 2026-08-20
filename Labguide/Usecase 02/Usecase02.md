# Caso de uso 02: Crear una solución de Data Factory para mover y transformar datos con dataflows y pipelines

**Introducción**

Este laboratorio le ayuda a acelerar el proceso de evaluación de Data
Factory en Microsoft Fabric mediante una guía paso a paso para completar
un escenario completo de integración de datos en una hora. Al finalizar
este tutorial, comprenderá el valor y las funcionalidades principales de
Data Factory y sabrá cómo completar un escenario común de integración de
datos de extremo a extremo.

**Objetivo**

El laboratorio se divide en tres ejercicios:

- **Ejercicio 1:** Crear un pipeline con Data Factory para ingerir datos
  sin procesar de Blob storage en una tabla bronze de un data Lakehouse.

- **Ejercicio 2:** Transformar datos con un dataflow en Data Factory
  para procesar los datos sin procesar de la tabla bronze y moverlos a
  una tabla Gold en el data Lakehouse.

- **Ejercicio 3:** Automatizar y enviar notificaciones con Data Factory
  para enviar un correo electrónico que le notifique una vez que todos
  los trabajos hayan finalizado y, finalmente, configurar todo el flujo
  para que se ejecute según una programación.

## Ejercicio 1: Crear un pipeline con Data Factory

### Tarea 1: Crear un workspace de Fabric

Antes de trabajar con datos en Fabric, cree un workspace con la versión
de prueba de Fabric habilitada.

1.  Abra el navegador, vaya a la barra de direcciones y escriba o pegue
    la siguiente URL: +++<https://app.fabric.microsoft.com/+++> y, a
    continuación, presione el botón **Enter**.

**Nota:** Si se le dirige a la página principal de Microsoft Fabric,
omita los pasos del \# 2 al \#4.

![](./media/image1.png)

2.  En la ventana de **Microsoft Fabric**, introduzca sus credenciales y
    haga clic en el botón **Submit**.

![](./media/image2.png)

3.  A continuación, en la ventana de **Microsoft,** introduzca la
    contraseña y haga clic en el botón **Sign in**.

![A login screen with a red box and blue text AI-generated content may
be incorrect.](./media/image3.png)

4.  En la ventana **Stay signed in?**, haga clic en el botón **Yes**.

![A screenshot of a computer error AI-generated content may be
incorrect.](./media/image4.png)

5.  Se le dirigirá a la página principal de Power BI.

![](./media/image5.png)

6.  Seleccione el icono predeterminado de Power BI situado en la parte
    inferior izquierda de la pantalla y seleccione **Fabric**.

![](./media/image6.png)

![](./media/image7.png)

![](./media/image8.png)

7.  En la página principal de **Microsoft Fabric**, seleccione la opción
    **New workspace**.

![](./media/image9.png)

8.  En la pestaña **Create a workspace**, introduzca los siguientes
    detalles y haga clic en el botón **Apply**.

| Setting | Value |
|---|---|
| Name | +++Data-FactoryXXXX+++ (XXXX can be a unique number) |
| Advanced | Under **License mode**, select **Fabric** |
| Default storage format | **Small semantic model storage format** |

![](./media/image10.png)

![](./media/image11.png)

9.  Espere a que finalice la implementación. Tardará aproximadamente
    entre 2 y 3 minutos.

![A screenshot of a computer Description automatically
generated](./media/image12.png)

### Tarea 2: Crear un lakehouse e ingerir datos de ejemplo

1.  En la página del **workspace Data-FactoryXX**, vaya a **+New item**
    y haga clic en este botón.

![A screenshot of a computer Description automatically
generated](./media/image13.png)

2.  Haga clic en el mosaico **Lakehouse**.

![A screenshot of a computer Description automatically
generated](./media/image14.png)

3.  En el cuadro de diálogo **New lakehouse**, introduzca
    +++DataFactoryLakehouse+++ en el campo **Name**, desactive
    **lakehouse schemas**, haga clic en el botón **Create** y abra el
    nuevo lakehouse.

> ![](./media/image15.png)

![](./media/image16.png)

4.  Vaya al Lakehouse, haga clic con el botón derecho en la carpeta
    **Files** y seleccione **Upload \> Upload files** para agregar
    archivos.

![](./media/image17.png)

5.  En la pestaña **Upload files**, haga clic en la carpeta situada
    debajo de **Files**.

![](./media/image18.png)

6.  Vaya a **C:\LabFiles** en su VM, seleccione el archivo
    **/Labfiles/NYCTaxi/part-00000-907cea6d-0f54-4639-9a14-042dc04185ef-c000.snappy.parquet**
    y haga clic en el botón **Open.**

![](./media/image19.png)

7.  A continuación, haga clic en el botón **Upload** y cierre la
    ventana.

![](./media/image20.png)

![](./media/image21.png)

![](./media/image22.png)

8.  En la barra de herramientas, seleccione el menú desplegable
    **Analyze data with**, coloque el cursor sobre **Notebook** y, a
    continuación, seleccione **New notebook**.

![](./media/image23.png)

9.  Agregue el siguiente código de PySpark para crear una sesión de
    Spark, leer el archivo Parquet cargado desde la carpeta *Files* del
    Lakehouse y escribir los datos en una tabla denominada Bronze,
    sobrescribiendo cualquier dato existente en la tabla.

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

7.  Para validar las tablas creadas, haga clic con el botón derecho en
    el lakehouse **DataFactoryLakehouse** en Explorer y, a continuación,
    seleccione **Refresh**. Aparecerán las tablas.

![](./media/image26.png)

![](./media/image27.png)

![](./media/image28.png)

## Ejercicio 2: Transformar datos con un dataflow en Data Factory

### Tarea 1: Obtener datos de una tabla de Lakehouse

1.  Ahora, haga clic en el workspace **Data
    Factory-@lab.LabInstance.Id** en el panel de navegación del lado
    izquierdo.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image29.png)

2.  Cree un nuevo **Dataflow Gen2** haciendo clic en el botón **+New
    item** de la barra de navegación. En la lista de elementos
    disponibles, seleccione **Dataflow Gen2.**

![](./media/image30.png)

3.  Proporcione el nombre +++**nyc_taxi_data_with_discounts+++** para
    **New Dataflow Gen2** y, a continuación, seleccione **Create**.

![](./media/image31.png)

4.  En el menú del nuevo dataflow, en el panel **Power Query**, haga
    clic en el menú desplegable **Get data** y, a continuación,
    seleccione **More....**

![A screenshot of a computer Description automatically
generated](./media/image32.png)

5.  En la pestaña **Choose data source**, escriba +++Lakehouse+++ en el
    cuadro de búsqueda y, a continuación, haga clic en el **conector
    Lakehouse**.

![A screenshot of a computer Description automatically
generated](./media/image33.png)

6.  Aparecerá el cuadro de diálogo **Connect to data source** y se
    creará automáticamente una nueva conexión en función del usuario que
    haya iniciado sesión actualmente. Seleccione **Next.**

![A screenshot of a computer Description automatically
generated](./media/image34.png)

7.  Se muestra el cuadro de diálogo **Choose data**. Utilice el panel de
    navegación para buscar el workspace **Data-FactoryXX** y expándalo.
    A continuación, expanda el Lakehouse **DataFactoryLakehouse** que
    creó como destino en el módulo anterior y seleccione la tabla
    **Bronze** de la lista. Después, haga clic en el botón **Create.**

![](./media/image35.png)

8.  Verá que el lienzo ahora está rellenado con los datos.

> ![](./media/image36.png)

### Tarea 2: Transformar los datos importados del Lakehouse

1.  Seleccione el icono de tipo de datos en el encabezado de la segunda
    columna, **IpepPickupDatetime**, para mostrar un menú desplegable y
    seleccione el tipo de datos correspondiente en el menú para
    convertir la columna de **Date/Time** a **Date**.

![](./media/image37.png)

2.  En la pestaña **Home** de la cinta de opciones, seleccione la opción
    **Choose columns** del grupo **Manage columns**.

![](./media/image38.png)

3.  En el cuadro de diálogo **Choose columns**, desactive algunas de las
    columnas que aparecen en la lista y, a continuación, seleccione
    **OK.**

    - lpepDropoffDatetime

    -  DoLocationID

![](./media/image39.png)

4.  Seleccione el menú desplegable de filtro y ordenación de la columna
    **storeAndFwdFlag**. (Si aparece la advertencia **List may be
    incomplete**, seleccione **Load more** para ver todos los datos.)

![](./media/image40.png)

5.  Seleccione **Y** para mostrar únicamente las filas en las que se
    aplicó un descuento y, a continuación, seleccione **OK**.

![](./media/image41.png)

6.  Seleccione el menú desplegable de ordenación y filtro de la columna
    **Ipep_Pickup_Datetime**, seleccione **Date filters** y, a
    continuación, elija el filtro **Between...** disponible para los
    tipos **Date** y **Date/Time**.

![](./media/image42.png)

7.  En el cuadro de diálogo **Filter rows**, seleccione las fechas
    comprendidas entre el 1 de enero de 2017 y el 31 de enero de 2017 y,
    a continuación, seleccione **OK**.

![](./media/image43.png)

![](./media/image44.png)

### Tarea 3: Conectarse a un archivo CSV que contiene datos de descuentos

Ahora que los datos de los viajes están disponibles, queremos cargar los
datos que contienen los descuentos correspondientes para cada día y
VendorID y preparar los datos antes de combinarlos con los datos de los
viajes.

1.  En la pestaña **Home** del menú del editor de **dataflow**,
    seleccione la opción **Get data** y, a continuación, seleccione
    **Text/CSV.**

![](./media/image45.png)

2.  En el panel **Connect to data source**, en **Connection settings**,
    seleccione el botón de opción **Link to file**. A continuación,
    introduzca
    +++https://raw.githubusercontent.com/ekote/azure-architect/master/Generated-NYC-Taxi-Green-Discounts.csv+++
    y establezca **dfconnection** como **Connection name**. Asegúrese de
    que **Authentication kind** esté establecido en **Anonymous**. Haga
    clic en el botón **Next**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image46.png)

3.  En el cuadro de diálogo **Preview file data**, seleccione
    **Create**.

![A screenshot of a computer Description automatically
generated](./media/image47.png)

![](./media/image48.png)

### Tarea 4: Transformar los datos de descuentos

1.  Al revisar los datos, observamos que los encabezados aparecen en la
    primera fila. Promuévalos a encabezados seleccionando el menú
    contextual de la tabla situado en la parte superior izquierda del
    área de cuadrícula de vista previa y, a continuación, seleccione
    **Use first row as headers**.

![](./media/image49.png)

***Nota:** Después de promover los encabezados, verá un nuevo paso
agregado al panel **Applied steps** en la parte superior del editor de
dataflow para los tipos de datos de las columnas**.***

![](./media/image50.png)

2.  Haga clic con el botón derecho en la columna **VendorID** y, en el
    menú contextual que aparece, seleccione la opción **Unpivot other
    columns**. Esto permite transformar las columnas en pares de
    atributo y valor, donde las columnas se convierten en filas.

![](./media/image51.png)

3.  Con la tabla sin dinamizar, cambie el nombre de las columnas
    **Attribute** y **Value** haciendo doble clic en ellas y cambiando
    **Attribute** a +++**Date**+++ y **Value** a +++Discount+++.

![](./media/image52.png)

4.  Cambie el tipo de datos de la columna **Date** seleccionando el menú
    de tipo de datos situado a la izquierda del nombre de la columna y
    eligiendo **Date**.

![](./media/image53.png)

5.  Seleccione la columna **Discount** y, a continuación, seleccione la
    pestaña **Transform** en el menú. Seleccione **Number column**,
    después **Standard numeric transformations** en el submenú y elija
    **Divide**.

![](./media/image54.png)

6.  En el cuadro de diálogo **Divide**, introduzca el valor
    +++**100**+++ y, a continuación, haga clic en el botón **OK**.

![A screenshot of a computer Description automatically
generated](./media/image55.png)

![](./media/image56.png)

### Tarea 7: Combinar los datos de viajes y descuentos

El siguiente paso consiste en combinar ambas tablas en una única tabla
que contenga el descuento que se debe aplicar al viaje y el total
ajustado.

1.  Primero, active el botón **Diagram view** para poder ver ambas
    consultas.

![](./media/image57.png)

2.  Seleccione la consulta **Bronze** y, en la pestaña **Home**,
    seleccione el menú **Combine** y elija **Merge queries** y, a
    continuación, **Merge queries as new**.

![](./media/image58.png)

3.  En el cuadro de diálogo **Merge**, seleccione
    **Generated-NYC-Taxi-Green-Discounts** en la lista desplegable
    **Right table for merge** y, a continuación, seleccione el icono de
    la bombilla situado en la parte superior derecha del cuadro de
    diálogo para ver la asignación sugerida de columnas entre las tres
    tablas.

4.  Seleccione cada una de las dos asignaciones de columnas sugeridas,
    una a la vez, asignando las columnas VendorID y Date de ambas
    tablas. Cuando se hayan agregado ambas asignaciones, los encabezados
    de las columnas coincidentes aparecerán resaltados en cada tabla.

![](./media/image59.png)

5.  Aparecerá un mensaje en el que se le solicitará permitir la
    combinación de datos de varios orígenes de datos para ver los
    resultados. Seleccione **OK. **

![](./media/image60.png)

6.  En el área de la tabla, inicialmente verá una advertencia que indica
    "**The evaluation was canceled because combining data from multiple
    sources may reveal data from one source to another. Select continue
    if the possibility of revealing data is okay**." Seleccione
    **Continue** para mostrar los datos combinados.

![](./media/image61.png)

7.  En el cuadro de diálogo **Privacy Levels**, seleccione la casilla
    **Ignore Privacy Levels checks for this document**. Ignorar los
    niveles de privacidad podría exponer datos confidenciales o
    sensibles a una persona no autorizada. A continuación, haga clic en
    el botón **Save.**

![](./media/image62.png)

![](./media/image63.png)

8.  Observe cómo se creó una nueva consulta en **Diagram view** que
    muestra la relación de la nueva consulta **Merge** con las dos
    consultas creadas anteriormente. En el panel de tablas del editor,
    desplácese hacia la derecha de la lista de columnas de la consulta
    Merge para ver que hay una nueva columna con valores de tabla. Esta
    es la columna **Generated NYC Taxi-Green-Discounts**, y su tipo es
    \[**Table**\].

En el encabezado de la columna hay un icono con dos flechas que apuntan
en direcciones opuestas, que permite seleccionar columnas de la tabla.
Desactive todas las columnas excepto **Discount** y, a continuación,
seleccione **OK**.

![](./media/image64.png)

9.  Con el valor del descuento ahora en el nivel de fila, podemos crear
    una nueva columna para calcular el importe total después del
    descuento. Para ello, seleccione la pestaña **Add column** en la
    parte superior del editor y elija **Custom column** en el grupo
    **General**.

![](./media/image65.png)

10. En el cuadro de diálogo **Custom column**, puede utilizar el
    lenguaje de fórmulas de Power Query (también conocido como M) para
    definir cómo debe calcularse la nueva columna. Introduzca
    +++TotalAfterDiscount+++ en **New column name**, seleccione
    **Currency** en **Data type** y proporcione la siguiente expresión M
    para la fórmula de **Custom column**:

+++if [total_amount] > 0 then [total_amount] * ( 1 -[Discount] ) else [total_amount]+++

Luego, seleccione **OK**.

![](./media/image66.png)

![](./media/image67.png)

11. Seleccione la columna recién creada **TotalAfterDiscount** y, a
    continuación, seleccione la pestaña **Transform** en la parte
    superior de la ventana del editor. En el grupo **Number column**,
    seleccione el menú desplegable **Rounding** y, a continuación, elija
    **Round...**

**Nota:** Si no encuentra la opción de redondeo, expanda el menú para
ver **Number column.**

![](./media/image68.png)

12. En el cuadro de diálogo **Round**, introduzca **2** como número de
    posiciones decimales y, a continuación, seleccione **OK**.

![](./media/image69.png)

13. Cambie el tipo de datos de **IpepPickupDatetime** de **Date** a
    **Date/Time**.

![](./media/image70.png)

14. Por último, expanda el panel **Query settings** situado en el lado
    derecho del editor, si aún no está expandido, y cambie el nombre de
    la consulta de **Merge** a +++**Output**+++.

![](./media/image71.png)

![](./media/image72.png)

### Tarea 8: Cargar la consulta de salida en una tabla del Lakehouse

Con la consulta de salida completamente preparada y los datos listos
para la salida, podemos definir el destino de salida de la consulta.

1.  Seleccione la consulta **Output** creada anteriormente. A
    continuación, seleccione el **icono** + para agregar un destino de
    datos a este **Dataflow**.

2.  En la lista de destinos de datos, seleccione la opción **Lakehouse**
    en **New destination**.

![](./media/image73.png)

3.  En el cuadro de diálogo **Connect to data destination**, la conexión
    ya debería estar seleccionada. Seleccione **Next** para continuar.

![A screenshot of a computer Description automatically
generated](./media/image74.png)

4.  En el cuadro de diálogo **Choose destination target**, vaya al
    Lakehouse y, a continuación, seleccione **Next** nuevamente.

![](./media/image75.png)

5.  En el cuadro de diálogo **Choose destination settings**, compruebe
    que las columnas estén asignadas correctamente y seleccione **Save
    settings**.

![](./media/image76.png)

6.  De nuevo en la ventana principal del editor, confirme que el destino
    de salida aparezca como Lakehouse en el panel **Query settings**
    para la tabla **Output** y, a continuación, seleccione la opción
    **Save** **and Run** en la pestaña **Home**.

![](./media/image77.png)

![](./media/image78.png)

![](./media/image79.png)

9.  Ahora, haga clic en el workspace **Data Factory-XXXX** en el panel
    de navegación del lado izquierdo.

![A screenshot of a computer Description automatically
generated](./media/image80.png)

10. En el panel **Data_FactoryXX**, seleccione **DataFactoryLakehouse**
    para ver la nueva tabla cargada allí.

![](./media/image81.png)

11. Confirme que la tabla **Output** aparece en el esquema **dbo**.

![](./media/image82.png)

## Ejercicio 3: Automatizar y enviar notificaciones con Data Factory

### Tarea 1: Agregar una actividad de Office 365 Outlook al pipeline

1.  Vaya al workspace **Data_FactoryXX** y haga clic en él en el menú de
    navegación del lado izquierdo.

![A screenshot of a computer Description automatically
generated](./media/image83.png)

2.  Seleccione la opción **+ New item** en la página del workspace y
    seleccione **Pipeline**.

![A screenshot of a computer Description automatically
generated](./media/image84.png)

3.  Proporcione el nombre +++**First_Pipeline1**+++ al Pipeline y, a
    continuación, seleccione **Create**.

![](./media/image85.png)

4.  Seleccione la pestaña **Home** en el editor del pipeline y busque y
    seleccione **Add copy** **data activity**.

> ![](./media/image86.png)

5.  En la pestaña **Source**, introduzca las siguientes configuraciones
    y haga clic en **Test connection**.

| Setting | Value |
|---|---|
| Connection | +++dfconnection User-XXXX+++ |
| Connection Type | Select **HTTP** |
| File format | **Delimited Text** |

![](./media/image87.png)

6.  En la pestaña **Destination**, introduzca las siguientes
    configuraciones.

| Setting | Value |
|---|---|
| Connection | **Lakehouse** |
| Lakehouse | Select **DataFactoryLakehouse** |
| Root Folder | Select the **Table** radio button |
| Table | Select **New**, enter `+++Generated-NYC-Taxi-Green-Discounts+++`, and select **Create**. |

![](./media/image88.png)

![A screenshot of a computer Description automatically
generated](./media/image89.png)

7.  En la cinta de opciones, seleccione **Run**.

![](./media/image90.png)

8.  En el cuadro de diálogo **Save and run?,** haga clic en el botón
    **Save and run**.

![A screenshot of a computer Description automatically
generated](./media/image91.png)

![](./media/image92.png)

9.  Seleccione la pestaña **Activities** en el editor del pipeline y
    busque la actividad **Office Outlook**.

![](./media/image93.png)

10. Seleccione y arrastre la ruta On Success (una casilla de
    verificación verde situada en la parte superior derecha de la
    actividad en el lienzo del pipeline) desde la actividad Copy hasta
    la nueva actividad Office 365 Outlook.

![A screenshot of a computer Description automatically
generated](./media/image94.png)

11. Seleccione la actividad **Office 365 Outlook** en el lienzo del
    pipeline y, a continuación, seleccione la pestaña **Settings** del
    área de propiedades situada debajo del lienzo para configurar el
    correo electrónico. Haga clic en el menú desplegable **Connection**
    y seleccione **Browse all.**

![A screenshot of a computer Description automatically
generated](./media/image95.png)

12. En la ventana **Choose a data source**, seleccione el origen
    **Office 365 Email**.

![A screenshot of a computer Description automatically
generated](./media/image96.png)

13. Inicie sesión con la cuenta desde la que desea enviar el correo
    electrónico. Puede utilizar la conexión existente con la cuenta que
    ya tiene iniciada la sesión.

![A screenshot of a computer Description automatically
generated](./media/image97.png)

14. Haga clic en **Connect** para continuar.

![A screenshot of a computer Description automatically
generated](./media/image98.png)

15. Seleccione la actividad Office 365 Outlook en el lienzo del pipeline
    y, en la pestaña Settings del área de propiedades situada debajo del
    lienzo, configure el correo electrónico.

    - Introduzca su dirección de correo electrónico en la sección
      **To**. Si desea utilizar varias direcciones, utilice**;** para
      separarlas.

![A screenshot of a computer Description automatically
generated](./media/image99.png)

- Para **Subject**, seleccione el campo para que aparezca la opción
  **Add dynamic content** y, a continuación, selecciónela para mostrar
  el lienzo del generador de expresiones del pipeline.

![A screenshot of a computer Description automatically
generated](./media/image100.png)

16. Aparecerá el cuadro de diálogo **Pipeline expression builder**.
    Introduzca la siguiente expresión y, a continuación, seleccione
    **OK**:

+++@concat('DI in an Hour Pipeline Succeeded with Pipeline Run Id', pipeline().RunId)+++

![](./media/image101.png)

17. Para **Body**, vuelva a seleccionar el campo y elija la opción View
    in expression **builder** cuando aparezca debajo del área de texto.
    Agregue nuevamente la siguiente expresión en el cuadro de diálogo
    **Pipeline expression builder** que aparece y, a continuación,
    seleccione **OK**:

+++@concat('RunID = ', pipeline().RunId, ' ; ', 'Copied rows ', activity('Copy data1').output.rowsCopied, ' ; ','Throughput ', activity('Copy data1').output.throughput)+++

![](./media/image102.png)

![A screenshot of a computer Description automatically
generated](./media/image103.png)

Nota: Reemplace **Copy data1** por el nombre de su propia actividad de
copia del pipeline.

18. Por último, seleccione la pestaña **Home** en la parte superior del
    editor del pipeline y elija **Run**. A continuación, seleccione
    **Save and run** nuevamente en el cuadro de diálogo de confirmación
    para ejecutar estas actividades.

![A screenshot of a computer Description automatically
generated](./media/image104.png)

![A screenshot of a computer Description automatically
generated](./media/image105.png)

![](./media/image106.png)

![](./media/image107.png)

19. Después de que el pipeline se ejecute correctamente, compruebe su
    correo electrónico para encontrar el correo de confirmación enviado
    desde el pipeline.

![](./media/image108.png)

### Tarea 2: Programar la ejecución del pipeline

Una vez que termine de desarrollar y probar el pipeline, puede
programarlo para que se ejecute automáticamente.

1.  En la pestaña **Home** de la ventana del editor del pipeline,
    seleccione **Schedule**.

![A screenshot of a computer Description automatically
generated](./media/image109.png)

2.  Configure la programación según sea necesario. En este ejemplo, el
    pipeline está programado para ejecutarse diariamente a las
    8:00 p. m. hasta finales de año.

![A screenshot of a schedule Description automatically
generated](./media/image110.png)

![](./media/image111.png)

![](./media/image112.png)

### Tarea 3: Agregar una actividad de Dataflow al pipeline

1.  Pase el cursor sobre la línea verde que conecta la actividad
    **Copy** y la actividad **Office 365 Outlook** en el lienzo del
    pipeline y seleccione el botón **+** para insertar una nueva
    actividad.

![](./media/image113.png)

2.  Seleccione **Dataflow** en el menú que aparece.

![](./media/image114.png)

3.  La actividad **Dataflow** recién creada se inserta entre las
    actividades **Copy y Office 365 Outlook** y se selecciona
    automáticamente, mostrando sus propiedades en el área situada debajo
    del lienzo. Seleccione la pestaña **Settings** en el área de
    propiedades y, a continuación, seleccione el dataflow que creó en el
    **Ejercicio 2: Transformar datos con un dataflow en Data Factory**.

![](./media/image115.png)

4.  Seleccione la pestaña **Home** en la parte superior del editor del
    pipeline y elija **Run**. A continuación, seleccione **Save and
    run** nuevamente en el cuadro de diálogo de confirmación para
    ejecutar estas actividades.

![](./media/image116.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image117.png)

![](./media/image118.png)

![](./media/image119.png)

### Tarea 4: Elimine los recursos

Puede eliminar informes, pipelines, warehouses y otros elementos
individuales, o eliminar todo el workspace. Siga los pasos siguientes
para eliminar el workspace que creó para este tutorial.

1.  Seleccione su workspace, **Data-FactoryXX**, en el menú de
    navegación de la izquierda. Se abrirá la vista de elementos del
    workspace.

![A screenshot of a computer Description automatically
generated](./media/image83.png)

2.  Seleccione la opción **Workspace settings** en la página del
    workspace, situada en la esquina superior derecha.

![](./media/image120.png)

3.  Seleccione la pestaña **General** y **Remove this workspace**.

![](./media/image121.png)
