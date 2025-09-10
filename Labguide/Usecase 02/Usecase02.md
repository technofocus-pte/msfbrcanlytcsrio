# Caso de uso 02: Solución de Data Factory para mover y transformar datos con dataflows y data pipelines

**Introducción**

This lab helps you accelerate the evaluation process for Data Factory in
Microsoft Fabric by providing a step-by-step guidance for a full data
integration scenario within one hour. By the end of this tutorial, you
understand the value and key capabilities of Data Factory and know how
to complete a common end-to-end data integration scenario.

**Objetivo**

Este laboratorio esta dividido en tres ejercicios:

- **Ejercicio 1:** Crear un pipeline con Data Factory para ingerir datos
  sin procesar desde un almacenamiento Blob hacia una tabla bronze en un
  data Lakehouse.

- **Ejercicio 2:** Transformar los datos con un dataflow en Data Factory
  para procesar los datos sin procesar de su tabla bronze y moverlos a
  una tabla Gold en el data Lakehouse.

- **Ejercicio 3:** Automatizar y enviar notificaciones con Data Factory
  para enviar un correo electrónico que le notifique cuando todos los
  trabajos estén completos y, finalmente, configurar todo el flujo para
  que se ejecute de manera programada.

# Ejercicio 1: Crear un pipeline con Data Factory

## Tarea 1: Crear un espacio de trabajo

Antes de trabajar con datos en Fabric, cree un espacio de trabajo con la
versión de prueba de Fabric habilitada..

1.  Abra su explorador, vaya a la barra de direcciones y escriba o pegue
    la siguiente URL: +++https://app.fabric.microsoft.com/+++ luego
    presione el botón **Enter**.

> **Nota:** Si es dirigido a la página principal de Microsoft Fabric,
> omita los pasos del 2 al 4.
>
> ![](./media/image1.png)

2.  En la ventana de **Microsoft Fabric**, ingrese sus credenciales y
    haga clic en el botón **Submit**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image2.png)

3.  Luego, en la ventana de **Microsoft**, ingrese la contraseña y haga
    clic en el botón **Sign in**.

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image3.png)

4.  En la ventana **Stay signed in?** haga clic en el botón **Yes**.

> ![A screenshot of a computer error AI-generated content may be
> incorrect.](./media/image4.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image5.png)

5.  En la página principal de **Microsoft Fabric**, seleccione la opción
    **New workspace**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image6.png)

6.  En la pestaña **Create a workspace**, ingrese los siguientes
    detalles y haga clic en el botón **Apply**.

    |  |  |
    |----|----|
    |Name	| +++Data-FactoryXXXX+++ (XXXX can be a unique number)| 
    |Advanced	|Under License mode, select Fabric capacity|
    |Default storage format|	Small semantic model storage format|

> ![](./media/image7.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

7.  Espere a que se complete la implementación. Tomará aproximadamente
    de 2 a 3 minutos.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image9.png)

## Tarea 2: Crear un lakehouse

1.  En la página del espacio de trabajo **Data-FactoryXX**, navegue y
    haga clic en el botón **+New item.** ![A screenshot of a computer
    AI-generated content may be incorrect.](./media/image10.png)

2.  Haga clic en el recuadro "**Lakehouse**".

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image11.png)

3.  ![](./media/image12.png) En el cuadro de diálogo **New lakehouse**,
    ingrese **+++DataFactoryLakehouse+++** en el campo **Name**, haga
    clic en el botón **Create** y abra el nuevo lakehouse.

![A screenshot of a computer Description automatically
generated](./media/image13.png)

4.  Ahora, haga clic en **Data-FactoryXX** en el panel de navegación
    izquierdo.

## ![](./media/image14.png) Tarea 3: Crear un pipeline de datos

1.  Seleccione la opción **+ New item** en la página del espacio de
    trabajo.

> ![A screenshot of a computer Description automatically
> generated](./media/image15.png)

2.  Seleccione **Data Pipeline** en el menú desplegable New item.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image16.png)

3.  Proporcione un nombre de pipeline como **+++First_Pipeline1+++** y
    luego seleccione **Create**.

![](./media/image17.png)

## Tarea 4: Usar una actividad Copy en el pipeline para cargar datos de muestra en un data Lakehouse

1.  En la página principal de **First_Pipeline1**, seleccione **Copy
    data assistant** para abrir la herramienta de copia asistida.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image18.png)

2.  El cuadro de diálogo **Copy data** se muestra con el primer paso,
    **Choose data source**, resaltado. Seleccione la sección **Sample
    data** y seleccione el tipo de origen de datos **NYC Taxi-Green**.
    Luego seleccione **Next**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image19.png)

3.  En **Connect to data source**, haga clic en el botón **Next**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image20.png)

4.  Seleccione **OneLake catalog** y seleccione **Existing Lakehouse**
    en la página de configuración de destino de datos que aparece.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image21.png)

5.  Ahora configure los detalles de su destino Lakehouse en la página
    **Select and map to folder path or table**. Seleccione **Tables**
    para Root folder, proporcione el nombre de tabla **+++Bronze+++** y
    seleccione **Next**.

6.  ![](./media/image22.png)![A screenshot of a computer Description
    automatically generated](./media/image23.png)Finalmente, en la
    página **Review + save** del asistente de copia de datos, revise la
    configuración. Para este laboratorio, desmarque la casilla **Start
    data transfer immediately**, ya que ejecutaremos la actividad
    manualmente en el siguiente paso. Luego seleccione **OK**.

## Tarea 5: Ejecute y vea los resultados de su actividad Copy

1.  En la pestaña **Home** de la ventana del editor de pipeline,
    seleccione el botón **Run** para ejecutar manualmente el pipeline.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image24.png)

2.  En el cuadro de diálogo **Save and run?**, haga clic en el botón
    **Save and run** para ejecutar estas actividades. Esta actividad
    tomará alrededor de 12 a 13 minutos.

> ![](./media/image25.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)

3.  Puede monitorear la ejecución y verificar los resultados en la
    pestaña **Output** debajo del lienzo del pipeline. Seleccione el
    **nombre de la actividad** para ver los detalles de la ejecución.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image27.png)

4.  Los detalles de la ejecución muestran 76,513,115 filas leídas y
    escritas.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image28.png)

5.  Expanda la sección **Duration breakdown** para ver la duración de
    cada etapa de la actividad Copy. Después de revisar los detalles de
    la copia, seleccione **Close**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image29.png)

**Ejercicio 2: Transformar datos con un dataflow en Data Factory**

## Tarea 1: Obtener datos de una tabla de Lakehouse

1.  En la página **First_Pipeline1**, desde la barra lateral seleccione
    **Create.**

![](./media/image30.png)

2.  En la página **New item**, para crear un nuevo **dataflow gen2**,
    haga clic en **Dataflow Gen2** en la sección **Data Factory**. 

![](./media/image31.png)

3.  Proporcione un nombre para el nuevo Dataflow Gen2 como
    **+++nyc_taxi_data_with_discounts+++** y luego seleccione
    **Create**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image32.png)

4.  En el nuevo menú de dataflow, en el panel de **Power Query**, haga
    clic en el menú desplegable **Get data** y luego seleccione
    **More...**.

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

5.  En la pestaña **Choose data source**, en el cuadro de búsqueda
    escriba +++**Lakehouse+++** y luego haga clic en el conector
    **Lakehouse**.

> ![A screenshot of a computer Description automatically
> generated](./media/image34.png)

6.  Aparece el cuadro de diálogo **Connect to data source**, y se crea
    automáticamente una nueva conexión para usted según el usuario con
    el que haya iniciado sesión. Seleccione **Next**.

> ![A screenshot of a computer Description automatically
> generated](./media/image35.png)

7.  Se muestra el cuadro de diálogo **Choose data**. Use el panel de
    navegación para buscar el espacio de trabajo **Data-FactoryXX** y
    expándalo. Luego, expanda el **Lakehouse - DataFactoryLakehouse**
    que creó como destino en el módulo anterior y seleccione la tabla
    **Bronze** de la lista. Después, haga clic en el botón **Create**.

![A screenshot of a computer Description automatically
generated](./media/image36.png)

8.  Verá que el lienzo ahora está poblado con los datos.

![A screenshot of a computer Description automatically
generated](./media/image37.png)

## Tarea 2: Transformar los datos importados desde el Lakehouse

1.  ![A screenshot of a computer Description automatically
    generated](./media/image38.png)Seleccione el icono de tipo de datos
    en el encabezado de la segunda columna, **IpepPickupDatetime**, para
    mostrar un menú desplegable y seleccione el tipo de datos en el menú
    para convertir la columna de **Date/Time** a **Date**.

2.  ![](./media/image39.png) En la pestaña **Home** de la cinta de
    opciones, seleccione la opción **Choose** **columns** del grupo
    **Manage** **columns**.

3.  En el cuadro de diálogo **Choose columns**, **deseleccione** algunas
    de las columnas listadas y luego seleccione **OK**.

    - lpepDropoffDatetime

    &nbsp;

    - puLocationId

    &nbsp;

    - doLocationId

    &nbsp;

    - pickupLatitude

    &nbsp;

    - dropoffLongitude

    &nbsp;

    - rateCodeID

> ![](./media/image40.png)

4.  Seleccione el menú desplegable de filtro y ordenamiento de la
    columna **storeAndFwdFlag**. (Si ve la advertencia **List may be
    incomplete**, seleccione **Load more** para ver todos los datos).

![](./media/image41.png)

5.  Seleccione **'Y'** para mostrar solo las filas donde se aplicó un
    descuento y luego seleccione **OK**.

![](./media/image42.png)

6.  Seleccione el menú desplegable de ordenamiento y filtro de la
    columna **Ipep_Pickup_Datetime**, luego seleccione **Date filters**
    y elija el filtro **Between...** disponible para los tipos **Date**
    y **Date/Time**.

![](./media/image43.png)

7.  En el cuadro de diálogo **Filter rows**, seleccione fechas entre el
    **1 de enero de 2019** y el **31 de enero de 2019**, luego
    seleccione **OK**.

> ![](./media/image44.png)

## Tarea 3: Conectarse a un archivo CSV que contiene datos de descuentos

Ahora, con los datos de los viajes listos, cargaremos los datos que
contienen los descuentos correspondientes para cada día y VendorID, y
prepararemos los datos antes de combinarlos con los datos de los viajes.

1.  En la pestaña **Home** del menú del editor de dataflow, seleccione
    la opción **Get data** y luego seleccione **Text/CSV**.

> ![A screenshot of a computer Description automatically
> generated](./media/image45.png)

2.  En el panel **Connect to data source**, en **Connection settings**,
    seleccione el botón de opción **Link to file**, luego ingrese
    +++https://raw.githubusercontent.com/ekote/azure-architect/master/Generated-NYC-Taxi-Green-Discounts.csv+++,
    asegúrese de que el tipo de autenticación esté configurado como
    **Anonymous** y haga clic en el botón **Next**.

> ![A screenshot of a computer Description automatically
> generated](./media/image46.jpeg)

3.  En el cuadro de diálogo **Preview file data**, seleccione
    **Create**.

![](./media/image47.png)

## Tarea 4: Transformar los datos de descuentos 

1.  Al revisar los datos, observamos que los encabezados aparecen en la
    primera fila. Promuévalos a encabezados seleccionando el menú
    contextual de la tabla en la parte superior izquierda del área de
    vista previa y seleccione **Use first row as headers**.

> ![A screenshot of a computer Description automatically
> generated](./media/image48.png)
>
> ***Nota:** Después de promover los encabezados, podrá ver un nuevo
> paso agregado en el panel **Applied steps** en la parte superior del
> editor de dataflow, aplicado a los tipos de datos de sus columnas.*
>
> ![](./media/image49.png)

2.  Haga clic con el botón derecho en la columna **VendorID** y, en el
    menú contextual que se muestra, seleccione la opción **Unpivot other
    columns**. Esto le permite transformar columnas en pares
    atributo-valor, donde las columnas se convierten en filas.

![A screenshot of a computer Description automatically
generated](./media/image50.png)

3.  Con la tabla desnormalizada, cambie el nombre de las columnas
    **Attribute** y **Value** haciendo doble clic sobre ellas y
    modificando **Attribute** a **+++Date+++** y **Value** a
    **+++Discount+++**.

![A screenshot of a computer Description automatically
generated](./media/image51.png)

![A screenshot of a computer Description automatically
generated](./media/image52.png)

4.  Cambie el tipo de datos de la columna **Date** seleccionando el menú
    de tipo de datos a la izquierda del nombre de la columna y
    seleccionando **Date**.

> ![A screenshot of a computer Description automatically
> generated](./media/image53.png)

5.  Seleccione la columna **Discount** y luego seleccione la pestaña
    **Transform** en el menú. Seleccione **Number column**, después
    seleccione **Standard numeric transformations** en el submenú y
    elija **Divide**.

> ![](./media/image54.png)

6.  En el cuadro de diálogo **Divide**, ingrese el valor +++100+++ y
    luego haga clic en el botón **OK**.

![](./media/image55.png)

**Tarea 7: Combinar los datos de viajes y descuentos**

El siguiente paso es combinar ambas tablas en una sola tabla que
contenga el descuento que debe aplicarse al viaje y el total ajustado.

1.  Primero, active el botón **Diagram view** para poder ver ambas
    consultas.

![](./media/image56.png)

2.  Seleccione la consulta **Bronze** y, en la pestaña **Home**,
    seleccione el menú **Combine** y elija **Merge queries**, luego
    **Merge queries as new**.

![A screenshot of a computer Description automatically
generated](./media/image57.png)

3.  En el cuadro de diálogo **Merge**, seleccione
    **Generated-NYC-Taxi-Green-Discounts** en el menú desplegable
    **Right table for merge** y luego seleccione el icono de
    **bombilla** en la parte superior derecha del cuadro de diálogo para
    ver el mapeo sugerido de columnas entre las tres tablas.

![A screenshot of a computer Description automatically
generated](./media/image58.png)

4.  Elija cada uno de los dos mapeos de columnas sugeridos, uno a la
    vez, asignando las columnas **VendorID** y **Date** de ambas tablas.
    Cuando ambos mapeos se hayan agregado, los encabezados de las
    columnas coincidentes se resaltarán en cada tabla.

> ![A screenshot of a computer Description automatically
> generated](./media/image59.png)

5.  Se muestra un mensaje que le solicita permitir la combinación de
    datos de múltiples orígenes para ver los resultados. Seleccione
    **OK**.

> ![A screenshot of a computer Description automatically
> generated](./media/image60.png)

6.  En el área de la tabla, verá inicialmente una advertencia que
    indica: *"*The evaluation was canceled because combining data from
    multiple sources may reveal data from one source to another. Select
    continue if the possibility of revealing data is okay.*"* Seleccione
    **Continue** para mostrar los datos combinados.

> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

7.  En el cuadro de diálogo **Privacy Levels**, seleccione la casilla
    **Ignore Privacy Levels checks for this document. Ignorar los
    niveles de privacidad podría exponer datos confidenciales a una
    persona no autorizada**. Luego haga clic en el botón **Save**.

> ![A screenshot of a computer Description automatically
> generated](./media/image62.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image63.png)

8.  Observe que se creó una nueva consulta en la vista **Diagram**,
    mostrando la relación de la nueva consulta **Merge** con las dos
    consultas que creó previamente. En el panel de tabla del editor,
    desplácese hacia la derecha en la lista de columnas de la consulta
    **Merge** para ver que hay una nueva columna con valores de tabla.
    Esta es la columna “**Generated NYC Taxi-Green-Discounts”** y su
    tipo es **\[Table\]**.

En el encabezado de la columna hay un icono con dos flechas en
direcciones opuestas, que le permite seleccionar columnas de la tabla.
Deseleccione todas las columnas excepto **Discount** y luego seleccione
**OK**.

![A screenshot of a computer Description automatically
generated](./media/image64.png)

9.  Con el valor del descuento ahora a nivel de fila, podemos crear una
    nueva columna para calcular el monto total después del descuento.
    Para hacerlo, seleccione la pestaña **Add column** en la parte
    superior del editor y elija **Custom column** en el grupo
    **General**.

> ![A screenshot of a computer Description automatically
> generated](./media/image65.png)

10. En el cuadro de diálogo **Custom column**, puede usar el lenguaje de
    fórmulas de Power Query (también conocido como M) para definir cómo
    debe calcularse su nueva columna.
    Ingrese +++**TotalAfterDiscount+++** como el **New column name**,
    seleccione **Currency** para el **Data type**, y proporcione la
    siguiente expresión M para **Custom column formula**:

 +++if [totalAmount] > 0 then [totalAmount] * ( 1 -[Discount] ) else [totalAmount]+++

Luego seleccione **OK**.

![A screenshot of a computer Description automatically
generated](./media/image66.png)

![A screenshot of a computer Description automatically
generated](./media/image67.png)

11. Seleccione la columna recién creada **TotalAfterDiscount** y luego
    seleccione la pestaña **Transform** en la parte superior de la
    ventana del editor. En el grupo **Number column**, seleccione el
    menú desplegable **Rounding** y luego elija **Round...**.

**Nota**: Si no encuentra la opción de **redondeo**, expanda el menú
para ver **Number column**.

![A screenshot of a computer Description automatically
generated](./media/image68.png)

![A screenshot of a computer Description automatically
generated](./media/image69.png)

12. En el cuadro de diálogo **Round**, ingrese **2** como el número de
    decimales y luego seleccione **OK**.

> ![](./media/image70.png)

13. ![A screenshot of a computer Description automatically
    generated](./media/image71.png) Cambie el tipo de datos de
    **IpepPickupDatetime** de **Date** a **Date/Time**.

![](./media/image72.png)

14. Finalmente, expanda el panel **Query settings** en el lado derecho
    del editor (si aún no está expandido) y cambie el nombre de la
    consulta de **Merge** a +++**Output+++**.

![A screenshot of a computer Description automatically
generated](./media/image73.png)

![A screenshot of a computer Description automatically
generated](./media/image74.png)

**Tarea 8: Cargar la consulta de salida en una tabla del Lakehouse**

Con la consulta de salida ahora completamente preparada y con los datos
listos para exportarse, podemos definir el destino de salida para la
consulta..

1.  Seleccione la consulta de combinación **Output** creada previamente.
    Luego seleccione el **icono** **+** para agregar un **destino de
    datos** a este **Dataflow**.

> ![A screenshot of a computer Description automatically
> generated](./media/image75.png)

2.  En la lista de destinos de datos, seleccione la opción **Lakehouse**
    en la sección **New destination**.

![A screenshot of a computer Description automatically
generated](./media/image76.png)

3.  En el cuadro de diálogo **Connect to data destination**, su conexión
    ya debería estar seleccionada. Seleccione **Next** para continuar.

![A screenshot of a computer Description automatically
generated](./media/image77.png)

4.  ![](./media/image78.png) En el cuadro de diálogo **Choose
    destination target**, busque el Lakehouse donde desea cargar los
    datos y asigne el nombre de la nueva tabla
    **+++nyc_taxi_with_discounts+++**, luego seleccione **Next**
    nuevamente.

5.  En el cuadro de diálogo **Choose destination settings**, deje el
    método de actualización predeterminado **Replace**, verifique que
    sus columnas estén mapeadas correctamente y seleccione **Save
    settings**.

![](./media/image79.png)

6.  De regreso en la ventana principal del editor, confirme que en el
    panel **Query settings** para la tabla **Output** se muestre su
    destino de salida como **Lakehouse**, y luego seleccione la opción
    **Save and Run** en la pestaña **Home**.

> ![A screenshot of a computer Description automatically
> generated](./media/image80.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image81.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image82.png)

7.  En el panel **Data_FactoryXX**, seleccione **DataFactoryLakehouse**
    para ver la nueva tabla cargada allí.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image83.png)

![](./media/image84.png)

# Ejercicio 3: Automatizar y enviar notificaciones con Data Factory

## Tarea 1: Agregar una actividad de Office 365 Outlook a su pipeline

1.  Navegue y haga clic en el espacio de trabajo **Data_FactoryXX** en
    el menú de navegación izquierdo.

> ![](./media/image85.png)

2.  En la vista **Data_FactoryXX**, seleccione **First_Pipeline1**.

> ![A screenshot of a computer Description automatically
> generated](./media/image86.png)

3.  Seleccione la pestaña **Activities** en el editor de pipeline y
    busque la actividad **Office Outlook**.

> ![](./media/image87.png)

4.  Seleccione y arrastre la ruta On success (un recuadro verde con una
    marca de verificación en la parte superior derecha de la actividad
    en el lienzo del pipeline) desde su actividad Copy hasta su nueva
    actividad Office 365 Outlook.

5.  ![](./media/image88.png) Seleccione la actividad **Office 365
    Outlook** en el lienzo del pipeline, luego seleccione la pestaña
    **Settings** en el área de propiedades debajo del lienzo para
    configurar el correo electrónico. Haga clic en el botón **Sign in**.

6.  ![](./media/image89.png) Seleccione su cuenta organizacional de
    Power BI y luego seleccione **Allow access** para confirmar.

7.  Seleccione la actividad Office 365 Outlook en el lienzo del pipeline
    y, en la pestaña **Settings** del área de propiedades debajo del
    lienzo, configure el correo electrónico.

    - • Ingrese su dirección de correo electrónico en la sección **To**.
      Si desea usar varias direcciones, sepárelas con **;**.

    &nbsp;

    - ![](./media/image90.png) Para el **Subject**, seleccione el campo
      para que aparezca la opción **Add dynamic content**, luego
      selecciónela para mostrar el lienzo del generador de expresiones
      del pipeline.

8.  Aparece el cuadro de diálogo **Pipeline expression builder**.
    Ingrese la siguiente expresión y luego seleccione **OK**:

+++@concat('DI in an Hour Pipeline Succeeded with Pipeline Run Id', pipeline().RunId)+++

> ![](./media/image91.png)

9.  Para el **Body**, seleccione nuevamente el campo y elija la opción
    **View in expression builder** cuando aparezca debajo del área de
    texto. Agregue la siguiente expresión en el cuadro de diálogo
    **Pipeline expression builder** que aparece y luego seleccione
    **OK**:

+++@concat('RunID = ', pipeline().RunId, ' ; ', 'Copied rows ', activity('Copy data1').output.rowsCopied, ' ; ','Throughput ', activity('Copy data1').output.throughput)+++
>
> ![](./media/image92.png)
>
> ![](./media/image93.png)

**  Nota:** Reemplace **Copy data1** con el nombre de su propia
actividad de copia en el pipeline.

10. Finalmente, seleccione la pestaña **Home** en la parte superior del
    editor de pipeline y elija **Run**. Luego seleccione **Save and
    run** nuevamente en el cuadro de confirmación para ejecutar estas
    actividades.

> ![](./media/image94.png)
>
> ![](./media/image95.png)

![](./media/image96.png)

11. Después de que el pipeline se ejecute correctamente, verifique su
    correo electrónico para encontrar el correo de confirmación enviado
    desde el pipeline.

![A screenshot of a computer Description automatically
generated](./media/image97.png)

![](./media/image98.png)

**Tarea 2: Programar la ejecución del pipeline**

Una vez que termine de desarrollar y probar su pipeline, puede
programarlo para que se ejecute automáticamente.

1.  En la pestaña **Home** de la ventana del editor de pipeline,
    seleccione **Schedule**.

![](./media/image99.png)

2.  Configure la programación según sea necesario. En este ejemplo, el
    pipeline se programará para ejecutarse diariamente a las **8:00 PM**
    hasta fin de año.

![](./media/image100.png)

**Tarea 3: Agregar una actividad de Dataflow al pipeline**

1.  Pase el cursor sobre la línea verde que conecta la actividad
    **Copy** y la actividad **Office 365 Outlook** en el lienzo de su
    pipeline, y seleccione el botón **+** para insertar una nueva
    actividad.

> ![](./media/image101.png)

2.  ![](./media/image102.png) Elija **Dataflow** en el menú que aparece.

3.  La actividad de **Dataflow** recién creada se inserta entre la
    actividad **Copy** y la actividad **Office 365 Outlook**, y se
    selecciona automáticamente mostrando sus propiedades en el área
    debajo del lienzo. Seleccione la pestaña **Settings** en el área de
    propiedades y luego seleccione su dataflow creado en el **Ejercicio
    2: Transformar datos con un dataflow en Data Factory**.

![](./media/image103.png)

4.  ![](./media/image104.png) Seleccione la pestaña **Home** en la parte
    superior del editor de pipeline y seleccione **Run**. Luego
    seleccione nuevamente **Save and run** en el cuadro de confirmación
    para ejecutar estas actividades.

![](./media/image105.png)

![A screenshot of a computer Description automatically
generated](./media/image106.png)

## Tarea 4: Limpiar los recursos

Puede eliminar informes, pipelines, almacenes y otros elementos de forma
individual o eliminar el espacio de trabajo completo. Use los siguientes
pasos para eliminar el espacio de trabajo que creó para este tutorial.

1.  Seleccione su espacio de trabajo, **Data-FactoryXX**, desde el menú
    de navegación izquierdo. Esto abrirá la vista de elementos del
    espacio de trabajo.

![](./media/image107.png)

2.  Seleccione la opción **Workspace settings** en la página del espacio
    de trabajo, ubicada en la esquina superior derecha.

![A screenshot of a computer Description automatically
generated](./media/image108.png)

3.  Seleccione **General tab** y **Remove this workspace.**

![A screenshot of a computer Description automatically
generated](./media/image109.png)
