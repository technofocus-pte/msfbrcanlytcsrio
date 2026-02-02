# Caso de uso 02: Solución de Data Factory para mover y transformar datos con dataflows y data pipelines

**Introducción**

Este laboratorio tiene como propósito acelerar el proceso de evaluación
de Data Factory en Microsoft Fabric, ofreciendo una guía detallada y
secuencial para completar en una hora un escenario completo de
integración de datos. Al finalizar este tutorial, comprenderá el valor y
las capacidades fundamentales de Data Factory, así como la forma de
ejecutar un escenario común de integración de datos de extremo a
extremo.

**Objetivos**

El laboratorio se encuentra dividido en tres ejercicios:

- **Ejercicio 1:** Crear un pipeline con Data Factory para ingerir datos
  sin procesar desde un Blob Storage hacia una tabla **bronze** en un
  data Lakehouse.

- **Ejercicio 2:** Transformar los datos mediante un dataflow en Data
  Factory, con el objetivo de procesar los datos en bruto provenientes
  de la tabla **bronze** y trasladarlos a una tabla **gold** dentro del
  Data Lakehouse.

- **Ejercicio 3:** Automatizar y enviar notificaciones con Data Factory
  para generar un correo electrónico que le informe una vez que todos
  los trabajos hayan concluido. Finalmente, configurar el flujo completo
  para su ejecución de manera programada.

# Ejercicio 1: Crear un pipeline con Data Factory

## Tarea 1: Crear un espacio de trabajo

Antes de trabajar con datos en Fabric, debe crear un espacio de trabajo
que tenga habilitada la versión de prueba de Fabric.

1.  Abra su navegador, vaya a la barra de direcciones y escriba o pegue
    la siguiente URL: +++https://app.fabric.microsoft.com/+++, luego
    presione **Enter**.

> **Nota:** Si es dirigido a la página de inicio de Microsoft Fabric,
> entonces omita los pasos del \#2 al \#4.
>
> ![](./media/image1.png)

2.  En la ventana de **Microsoft Fabric**, ingrese sus credenciales y
    haga clic en el botón **Submit**.

> ![](./media/image2.png)

3.  Luego, en la ventana de **Microsoft**, ingrese la contraseña y haga
    clic en **Sign in**.

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image3.png)

4.  En la ventana **Stay signed in?,** haga clic en **Yes**.

> ![A screenshot of a computer error AI-generated content may be
> incorrect.](./media/image4.png)
>
> ![](./media/image5.png)

5.  En la página de inicio de **Microsoft Fabric**, seleccione la opción
    **New workspace**.

> ![](./media/image6.png)

6.  En la pestaña **Create a workspace**, ingrese los siguientes datos y
    haga clic en **Apply**.

	|   |   |
	|----|----|
	|Name	| Data-FactoryXXXX (XXXX can be a unique number) |
	|Advanced|	Under License mode, select Fabric capacity|
	|Default storage format|	Small semantic model storage format|

> ![](./media/image7.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

7.  Espere a que la implementación se complete. Esto tomará
    aproximadamente **2–3 minutos**.

> ![A screenshot of a computer Description automatically
> generated](./media/image9.png)

## Tarea 2: Crear un lakehouse e ingerir datos de ejemplo

1.  En la página del espacio de trabajo **Data-FactoryXX**, navegue y
    haga clic en el botón **+New item.**

> ![A screenshot of a computer Description automatically
> generated](./media/image10.png)

2.  Haga clic en el recuadro **Lakehouse**.

![A screenshot of a computer Description automatically
generated](./media/image11.png)

3.  En el cuadro de diálogo **New lakehouse**, ingrese
    +++**DataFactoryLakehouse**+++ en el campo **Name**, haga clic en
    **Create** y abra el nuevo lakehouse.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image12.png)
>
> ![](./media/image13.png)

4.  En la página principal del **lakehouse**, seleccione **Start with
    sample data** para abrir la opción de copiar datos de ejemplo.

> ![](./media/image14.png)

5.  Se mostrará el cuadro de diálogo **Use a sample**, seleccione el
    recuadro de datos de ejemplo **NYCTaxi**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image15.png)
>
> ![](./media/image16.png)
>
> ![](./media/image17.png)

6.  Para cambiar el nombre de la tabla, haga clic derecho sobre la
    pestaña **green_tripdata_2022** justo encima del editor y seleccione
    **Rename.**

![A screenshot of a computer Description automatically
generated](./media/image18.png)

7.  En el cuadro de diálogo **Rename**, en el campo **Name**, ingrese
    +++**Bronze**+++ para cambiar el nombre de la tabla. Luego, haga
    clic en **Rename**.

![A screenshot of a computer Description automatically
generated](./media/image19.png)

![A screenshot of a computer Description automatically
generated](./media/image20.png)

**Ejercicio 2: Transformar datos con un dataflow en Data Factory**

## Tarea 1: Obtener datos desde una tabla de un lakehouse

1.  Ahora, haga clic en el espacio de trabajo[**Data
    Factory-@lab.LabInstance.Id**](mailto:Data%20Factory-@lab.LabInstance.Id) en
    el panel de navegación izquierdo.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image21.png)

2.  Cree un nuevo **Dataflow Gen2** haciendo clic en el botón **+New
    item** en la barra de navegación. En la lista de elementos
    disponibles, seleccione **Dataflow Gen2**.

> ![A screenshot of a computer Description automatically
> generated](./media/image22.png)

3.  Proporcione un nombre para el nuevo Dataflow Gen2 como
    **+++nyc_taxi_data_with_discounts+++** y luego seleccione
    **Create.**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image23.png)

4.  En el menú del nuevo dataflow, en el panel de **Power Query**, haga
    clic en el menú desplegable **Get data**, luego seleccione
    **More....**

> ![A screenshot of a computer Description automatically
> generated](./media/image24.png)

5.  En la pestaña **Choose data source**, en el cuadro de búsqueda
    escriba +++ **Lakehouse** +++ y haga clic en el conector
    **Lakehouse**.

> ![A screenshot of a computer Description automatically
> generated](./media/image25.png)

6.  Aparece el cuadro de diálogo **Connect to data source**, y se crea
    automáticamente una nueva conexión basada en el usuario actualmente
    autenticado. Seleccione **Next**.

> ![A screenshot of a computer Description automatically
> generated](./media/image26.png)

7.  Se muestra el cuadro de diálogo **Choose data**. Utilice el panel de
    navegación para buscar el espacio de trabajo **Data-FactoryXX** y
    expándalo. Luego, expanda el **Lakehouse – DataFactoryLakehouse**
    que creó para el destino en el módulo anterior y seleccione la tabla
    **Bronze** de la lista. Después haga clic en **Create**.

![A screenshot of a computer Description automatically
generated](./media/image27.png)

8.  Verá que el canvas ahora se encuentra poblado con los datos.

![A screenshot of a computer Description automatically
generated](./media/image28.png)

## Tarea 2: Transformar los datos importados desde el lakehouse

1.  Seleccione el ícono de tipo de datos en el encabezado de la segunda
    columna, **IpepPickupDatetime**, para implementar el menú de
    opciones y, a continuación, elija el tipo de datos **Date** para
    convertir la columna de **Date/Time** a **Date**.

![A screenshot of a computer Description automatically
generated](./media/image29.png)

2.  En la pestaña **Home** de la cinta, seleccione la opción **Choose
    columns** del grupo **Manage columns.**

![A screenshot of a computer Description automatically
generated](./media/image30.png)

3.  En el cuadro de diálogo **Choose columns**, deseleccione las
    columnas indicadas a continuación y luego seleccione **OK**:

    - lpepDropoffDatetime

    &nbsp;

    - DoLocationID

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image31.png)

4.  Seleccione el menú desplegable de filtro y ordenación de la columna
    **storeAndFwdFlag**. (Si ve la advertencia **List may be
    incomplete**, seleccione **Load more** para ver todos los datos).

> ![A screenshot of a computer Description automatically
> generated](./media/image32.png)

5.  Seleccione '**Y**' para mostrar únicamente las filas en las que se
    aplicó un descuento y luego seleccione **OK**.

> ![A screenshot of a computer Description automatically
> generated](./media/image33.png)

6.  Seleccione el menú desplegable de ordenación y filtro de la columna
    **Ipep_Pickup_Datetime**, luego haga clic en **Date filters** y
    elija el filtro **Between…**, disponible para los tipos Date y
    Date/Time.

![](./media/image34.png)

7.  En el cuadro de diálogo **Filter rows**, seleccione fechas entre
    **January 1, 2022** y **January 31, 2022**, luego seleccione **OK**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image35.png)

## Tarea 3: Conectarse a un archivo CSV que contiene los datos de los descuentos

Ahora, con los datos de los viajes disponibles, cargaremos los datos que
contienen los descuentos correspondientes a cada día y a cada
**VendorID**, para luego prepararlos antes de combinarlos con la
información de los viajes.

1.  En la pestaña **Home** del menú del editor de **dataflow**,
    seleccione la opción **Get data** y luego elija **Text/CSV**.

> ![](./media/image36.png)

2.  En el panel **Connect to data source**, bajo **Connection
    settings**, seleccione el botón de opción **Link to file**, luego
    ingrese:

+++https://raw.githubusercontent.com/ekote/azure-architect/master/Generated-NYC-Taxi-Green-Discounts.csv+++
y coloque **dfconnection** como **Connection name**. Asegúrese de que
**authentication kind** esté configurado en **Anonymous**. Haga clic en
**Next**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image37.png)

3.  En el cuadro de diálogo **Preview file data**, seleccione
    **Create**.

![A screenshot of a computer Description automatically
generated](./media/image38.png)

## Tarea 4: Transformar los datos de descuentos

1.  Al revisar los datos, se observa que los encabezados se encuentran
    en la primera fila. Para promoverlos a encabezados, abra el menú
    contextual de la tabla situado en la esquina superior izquierda del
    área de vista previa y seleccione la opción **Use first row as
    headers**.

> ![](./media/image39.png)
>
> ***Nota:** Después de promover los encabezados, se agregará un nuevo
> paso en el panel **Applied steps** en la parte superior del editor de
> dataflow, reflejando los tipos de datos de sus columnas.*
>
> ![](./media/image40.png)

2.  Haga clic derecho en la columna **VendorID** y, en el menú
    contextual que aparece, seleccione **Unpivot other columns**. Esto
    permite transformar columnas en pares atributo-valor, donde las
    columnas se convierten en filas.

![A screenshot of a computer Description automatically
generated](./media/image41.png)

3.  Con la tabla desagregada (unpivoted), cambie el nombre de las
    columnas **Attribute** y **Value** haciendo doble clic en ellas,
    reemplazando **Attribute** por **Date** y **Value** por
    **Discount**.

![A screenshot of a computer Description automatically
generated](./media/image42.png)

![A screenshot of a computer Description automatically
generated](./media/image43.png)

4.  Cambie el tipo de datos de la columna **Date** seleccionando el menú
    de tipo de datos a la izquierda del nombre de la columna y eligiendo
    **Date**.

> ![A screenshot of a computer Description automatically
> generated](./media/image44.png)

5.  Seleccione la columna **Discount**, luego vaya a la pestaña
    **Transform** en el menú. Seleccione **Number column**, luego
    **Standard numeric transformations** y elija **Divide**.

> ![A screenshot of a computer Description automatically
> generated](./media/image45.png)

6.  En el cuadro de diálogo **Divide**, ingrese el valor +++100+++,
    luego haga clic en **OK**.

![A screenshot of a computer Description automatically
generated](./media/image46.png)

![A screenshot of a computer Description automatically
generated](./media/image47.png)

**Tarea 7: Combinar los datos de viajes y descuentos**

El siguiente paso es combinar ambas tablas en una sola que contenga el
descuento que debe aplicarse a cada viaje y el total ajustado.

1.  Primero, active el botón **Diagram view** para poder ver ambas
    consultas.

![A screenshot of a computer Description automatically
generated](./media/image48.png)

2.  Seleccione la consulta **Bronze**, y en la pestaña **Home**,
    seleccione el menú **Combine** y elija **Merge queries**, luego
    **Merge queries as new.**

![](./media/image49.png)

3.  En el cuadro de diálogo **Merge**, seleccione
    **Generated-NYC-Taxi-Green-Discounts** en **Right table for merge**
    y luego haga clic en el ícono de **light bulb** en la esquina
    superior derecha del diálogo para ver la sugerencia de mapeo de
    columnas entre las dos tablas.

4.  Elija cada uno de los dos mapeos de columnas sugeridos, uno a la
    vez, mapeando las columnas **VendorID** y **Date** de ambas tablas.
    Cuando ambos mapeos se agreguen, los encabezados de columna
    coincidentes se resaltarán en cada tabla.

> ![](./media/image50.png)

5.  Aparecerá un mensaje solicitando permitir combinar datos de
    múltiples fuentes para ver los resultados. Seleccione **OK**. 

> ![A screenshot of a computer Description automatically
> generated](./media/image51.png)

6.  En el área de la tabla, inicialmente verá una advertencia que dice:

"The evaluation was canceled because combining data from multiple
sources may reveal data from one source to another. Select continue if
the possibility of revealing data is okay." Seleccione **Continue** para
mostrar los datos combinados...

> ![](./media/image52.png)

7.  En el cuadro de diálogo **Privacy Levels**, marque la casilla:
    **Ignore Privacy Levels checks for this document. Ignoring privacy
    Levels could expose sensitive or confidential data to an
    unauthorized person**. Luego haga clic en **Save**.

> ![A screenshot of a computer screen Description automatically
> generated](./media/image53.png)
>
> ![](./media/image54.png)

8.  Observe que se creó una nueva consulta en **Diagram view**,
    mostrando la relación de la nueva consulta **Merge** con las dos
    consultas que creó anteriormente. En el panel de la tabla del
    editor, desplácese hacia la derecha en la lista de columnas de la
    consulta **Merge** para ver una nueva columna con valores de tabla.
    Esta es la columna **Generated NYC Taxi-Green-Discounts**, y su tipo
    es **\[Table\]**.

En el encabezado de la columna hay un ícono con dos flechas en
direcciones opuestas, que permite seleccionar columnas de la tabla.
Deseleccione todas las columnas excepto **Discount** y luego seleccione
**OK**.

![](./media/image55.png)

9.  Con el valor de **Discount** ahora a nivel de fila, podemos crear
    una nueva columna para calcular el total después del descuento. Para
    ello, seleccione la pestaña **Add column** en la parte superior del
    editor y elija **Custom column** del grupo **General**.

> ![](./media/image56.png)

10. En el cuadro de diálogo **Custom column**, puede usar el lenguaje de
    fórmulas de Power Query (también conocido como M) para definir cómo
    se calculará la nueva columna. Ingrese +++**TotalAfterDiscount**+++
    para **New column name**, seleccione **Currency** como **Data
    type**, y proporcione la siguiente expresión M para la fórmula de la
    **columna personalizada**:

> +++if [total_amount] > 0 then [total_amount] * ( 1 -[Discount] ) else [total_amount]+++

Luego, seleccione **OK**.

![](./media/image57.png)

![A screenshot of a computer Description automatically
generated](./media/image58.png)

11. Seleccione la columna recién creada **TotalAfterDiscount**, luego
    vaya a la pestaña **Transform** en la parte superior del editor. En
    el grupo **Number column**, abra el menú desplegable **Rounding** y
    seleccione **Round...**

**Nota**: Si no encuentra la opción **rounding**, expanda el menú para
ver **Number column**.

![](./media/image59.png)

12. En el cuadro de diálogo **Round**, ingrese 2 como número de
    decimales y luego seleccione **OK**.

![A screenshot of a computer Description automatically
generated](./media/image60.png)

13. Cambie el tipo de datos de la columna **IpepPickupDatetime** de
    **Date** a **Date/Time**.

![](./media/image61.png)

14. Finalmente, expanda el panel **Query settings** en el lado derecho
    del editor si aún no está abierto, y cambie el nombre de la consulta
    de **Merge** a +++**Output**+++.

![A screenshot of a computer Description automatically
generated](./media/image62.png)

![A screenshot of a computer Description automatically
generated](./media/image63.png)

**Tarea 8: Guardar los resultados de la consulta en una tabla del
Lakehouse**

Con la consulta de resultados completamente preparada y los datos listos
para exportar, podemos definir el destino donde se guardarán los datos
de resultado de la consulta.

1.  Seleccione la consulta **Output** creada previamente. Luego haga
    clic en el ícono **+** para agregar un destino de datos a este
    **Dataflow**.

2.  En la lista de destinos de datos, seleccione la opción **Lakehouse**
    bajo **New destination**.

![](./media/image64.png)

3.  En el cuadro de diálogo **Connect to data destination**, su conexión
    ya debería estar seleccionada. Seleccione **Next** para continuar.

![A screenshot of a computer Description automatically
generated](./media/image65.png)

4.  En el cuadro de diálogo **Choose destination target**, busque el
    Lakehouse, luego seleccione **Next** nuevamente.

![](./media/image66.png)

5.  En el cuadro de diálogo **Choose destination settings**, deje el
    método de actualización predeterminado **Replace**, verifique que
    las columnas estén mapeadas correctamente y seleccione **Save
    settings**.

![](./media/image67.png)

6.  En la ventana principal del editor, verifique que su destino de
    resultado aparezca en el panel **Query settings** para la tabla
    **Output** como **Lakehouse**, y luego haga clic en **Save and Run**
    en la pestaña **Home.**

> ![](./media/image68.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image69.png)

9.  Ahora, haga clic en el espacio de trabajo **Data Factory-XXXX** en
    el panel de navegación izquierdo.

> ![A screenshot of a computer Description automatically
> generated](./media/image70.png)

10. En el panel **Data_FactoryXX**, seleccione **DataFactoryLakehouse**
    para ver la nueva tabla cargada allí.

![](./media/image71.png)

11. Confirme que la tabla **Output** aparece bajo el esquema **dbo**.

![](./media/image72.png)

# Ejercicio 3: Automatizar y enviar notificaciones con Data Factory

## Tarea 1: Agregar una actividad de Office 365 Outlook a su pipeline

1.  Navegue y haga clic en el espacio de trabajo **Data_FactoryXX** en
    el menú de navegación izquierdo.

> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)

2.  Seleccione la opción **+ New item** en la página del espacio de
    trabajo y elija **Pipeline**.

> ![A screenshot of a computer Description automatically
> generated](./media/image74.png)

3.  Proporcione un nombre para el Pipeline como
    +++**First_Pipeline1**+++ y luego seleccione **Create**.

> ![](./media/image75.png)

4.  Seleccione la pestaña **Home** en el editor de pipeline y busque la
    opción **Add to canvas activity**.

> ![](./media/image76.png)

5.  En la pestaña **Source**, ingrese los siguientes valores y haga clic
    en **Test connection**:

	|     |    |
	|------|------|
	|Connection|	dfconnection User-XXXX|
	|Connection Type|	select HTTP.|
	|File format	|Delimited Text|

> ![](./media/image77.png)

6.  En la pestaña **Destination**, ingrese los siguientes valores:

	|    |    |
	|-----|----|
	|Connection	|**Lakehouse**|
	|Lakehouse|	Select **DataFactoryLakehouse**|
	|Root Folder	|select the **Table** radio button.|
	|Table|	• Select New, enter +++Generated-NYC-Taxi-Green-Discounts+++ and click on Create button|

> ![](./media/image78.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image79.png)

7.  Desde la cinta, seleccione **Run**.

> ![](./media/image80.png)

8.  En el cuadro de diálogo **Save and run?**, haga clic en **Save and
    run**.

> ![A screenshot of a computer Description automatically
> generated](./media/image81.png)
>
> ![](./media/image82.png)

9.  Seleccione la pestaña **Activities** en el editor de pipeline y
    localice la actividad **Office Outlook**.

> ![A screenshot of a computer Description automatically
> generated](./media/image83.png)

10. Seleccione y arrastre la ruta **On Success** (una casilla verde
    ubicada en la esquina superior derecha de la actividad en el canvas
    del pipeline) desde su actividad **Copy** hasta la nueva actividad
    **Office 365 Outlook**.

![A screenshot of a computer Description automatically
generated](./media/image84.png)

11. Seleccione la actividad **Office 365 Outlook** en el canvas del
    pipeline, luego seleccione la pestaña **Settings** en el área de
    propiedades debajo del canvas para configurar el correo. Haga clic
    en el menú desplegable **Connection** y seleccione **Browse all**.

> ![A screenshot of a computer Description automatically
> generated](./media/image85.png)

12. En la ventana **choose a data source**, seleccione **Office 365
    Email source**.

> ![A screenshot of a computer Description automatically
> generated](./media/image86.png)

13. Inicie sesión con la cuenta desde la cual desea enviar el correo.
    Puede usar la conexión existente con la cuenta ya iniciada.

> ![A screenshot of a computer Description automatically
> generated](./media/image87.png)

14. Haga clic en **Connect** para continuar.

> ![A screenshot of a computer Description automatically
> generated](./media/image88.png)

15. Seleccione la actividad **Office 365 Outlook** en el canvas del
    pipeline, en la pestaña **Settings** del área de propiedades:

    - En **To**, ingrese su dirección de correo. Si desea usar varias
      direcciones, sepárelas con ;.

> ![A screenshot of a computer Description automatically
> generated](./media/image89.png)

- En **Subject**, seleccione el campo para que aparezca la opción **Add
  dynamic content**, luego selecciónela para mostrar el canvas del
  pipeline **expression builder**.

> ![A screenshot of a computer Description automatically
> generated](./media/image90.png)

16. En el cuadro de diálogo **Pipeline expression builder**, ingrese la
    siguiente expresión y luego seleccione **OK**:

+++@concat('DI in an Hour Pipeline Succeeded with Pipeline Run Id', pipeline().RunId)+++
>
> ![](./media/image91.png)

17. Para el **Body**, seleccione nuevamente el campo y elija **View in
    expression builder** cuando aparezca debajo del área de texto.
    Agregue la siguiente expresión en el **Pipeline expression builder**
    y luego seleccione **OK**:

+++@concat('RunID = ', pipeline().RunId, ' ; ', 'Copied rows ', activity('Copy data1').output.rowsCopied, ' ; ','Throughput ', activity('Copy data1').output.throughput)+++

> ![](./media/image92.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image93.png)

**  Nota:** Reemplace **Copy data1** con el nombre de su propia
actividad de copia en el pipeline.

18. Finalmente, seleccione la pestaña **Home** en la parte superior del
    editor de pipeline y elija **Run**. Luego seleccione **Save and
    run** nuevamente en el cuadro de confirmación para ejecutar estas
    actividades.

> ![A screenshot of a computer Description automatically
> generated](./media/image94.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image95.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image96.png)

19. Después de que el pipeline se ejecute correctamente, revise su
    correo electrónico para confirmar que recibió el correo enviado por
    el pipeline.

![](./media/image97.png)

**Tarea 2: Programar la ejecución del pipeline**

Una vez que haya terminado de desarrollar y probar su pipeline, puede
programarlo para que se ejecute automáticamente.

1.  En la pestaña **Home** del editor de pipeline, seleccione
    **Schedule**.

![A screenshot of a computer Description automatically
generated](./media/image98.png)

2.  Configure la programación según sus necesidades. En este ejemplo, el
    pipeline se programa para ejecutarse diariamente a las 8:00 PM hasta
    el final del año.

![A screenshot of a schedule Description automatically
generated](./media/image99.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image100.png)

![A screenshot of a schedule AI-generated content may be
incorrect.](./media/image101.png)

**Tarea 3: Agregar una actividad de Dataflow al pipeline**

1.  Pase el cursor sobre la línea verde que conecta la actividad
    **Copy** con la actividad **Office 365 Outlook** en el canvas del
    pipeline y seleccione el botón **+** para insertar una nueva
    actividad.

> ![A screenshot of a computer Description automatically
> generated](./media/image102.png)

2.  Elija **Dataflow** en el menú que aparece.

![A screenshot of a computer Description automatically
generated](./media/image103.png)

3.  La nueva actividad **Dataflow** se inserta entre la actividad Copy y
    la actividad Office 365 Outlook, y se selecciona automáticamente,
    mostrando sus propiedades en el área debajo del canvas. Seleccione
    la pestaña **Settings** en el área de propiedades y luego seleccione
    el dataflow que creó en el **Ejercicio 2: Transformar datos con un
    dataflow en Data Factory**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image104.png)

4.  Seleccione la pestaña **Home** en la parte superior del editor de
    pipeline y elija **Run**. Luego seleccione **Save and run**
    nuevamente en el cuadro de confirmación para ejecutar estas
    actividades.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image105.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image106.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image107.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image108.png)

## Tarea 4: Eliminar recursos

Puede eliminar informes individuales, pipelines, warehouses y otros
elementos, o eliminar todo el espacio de trabajo. Use los siguientes
pasos para eliminar el espacio de trabajo que creó para este tutorial.

1.  Seleccione su espacio de trabajo, **Data-FactoryXX**, en el menú de
    navegación izquierdo. Esto abrirá la vista de elementos del espacio
    de trabajo.

![](./media/image109.png)

2.  Seleccione la opción **Workspace settings** en la página del espacio
    de trabajo, ubicada en la esquina superior derecha.

![A screenshot of a computer Description automatically
generated](./media/image110.png)

3.  Seleccione la pestaña **General** y haga clic en **Remove this
    workspace.**

![A screenshot of a computer Description automatically
generated](./media/image111.png)
