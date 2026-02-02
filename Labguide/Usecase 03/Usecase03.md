# Caso de uso 03 – Interacción con sus datos mediante el Data Agent de Fabric

**Introducción:**

Este caso de uso le introduce al Data Agent de Microsoft Fabric, lo cual
permite realizar consultas en lenguaje natural sobre conjuntos de datos
estructurados. Al aprovechar los modelos de lenguaje de gran tamaño
(LLMs), el Data Agent de Fabric puede interpretar preguntas en inglés
sencillo y traducirlas en consultas T-SQL válidas, ejecutadas sobre los
datos de su lakehouse seleccionado.

Este ejercicio práctico le guía a través del proceso de configurar su
entorno, crear un espacio de trabajo en Fabric, cargar datos y utilizar
la habilidad de IA para interactuar con sus datos de forma
conversacional. Además, explorará funciones avanzadas como proporcionar
ejemplos de consultas, agregar instrucciones para mejorar la precisión y
llamar a la habilidad de IA de forma programática desde un notebook.

**Objetivos:**

- Configurar un espacio de trabajo de Fabric y cargar datos en un
  lakehouse.

- Crear y configurar un Data Agent para habilitar consultas en lenguaje
  natural.

- Formular preguntas en inglés sencillo y ver los resultados de
  consultas SQL generadas por IA.

- Mejorar las respuestas de la IA utilizando instrucciones
  personalizadas y ejemplos de consultas.

- Usar el Data Agent de forma programática desde un notebook.

## **Tarea 0: Sincronizar la hora del entorno host**

1.  En su máquina virtual, vaya a la barra **Search,** escriba
    **Settings** y luego haga clic en **Settings** en **Best match**.

> ![A screenshot of a computer Description automatically
> generated](./media/image1.png)

2.  En la ventana **Settings**, navegue y haga clic en **Time &
    language**.

![A screenshot of a computer Description automatically
generated](./media/image2.png)

3.  En la página **Time & language**, navegue y haga clic en **Date &
    time**.

![A screenshot of a computer Description automatically
generated](./media/image3.png)

4.  Desplácese hacia abajo y navegue hasta la sección **Additional
    settings**, luego haga clic en el botón **Syn now**. Este proceso
    tomará entre 3 y 5 minutos.

![A screenshot of a computer Description automatically
generated](./media/image4.png)

5.  Cierre la ventana **Settings**.

![A screenshot of a computer Description automatically
generated](./media/image5.png)

## **Tarea 1: Crear un espacio de trabajo de Fabric**

En esta tarea, creará un espacio de trabajo de Fabric. Este espacio de
trabajo contiene todos los elementos necesarios para este tutorial,
incluidos el lakehouse, los dataflows, los pipelines de Data Factory,
los notebooks, los conjuntos de datos de Power BI y los informes.

1.  Abra su navegador, navegue a la barra de direcciones y escriba o
    pegue la siguiente URL: +++**https://app.fabric.microsoft.com**/+++
    y luego presione el botón **Enter.**

> ![A search engine window with a red box Description automatically
> generated with medium confidence](./media/image6.png)

2.  En la ventana de **Microsoft Fabric**, ingrese sus credenciales y
    haga clic en el botón **Submit**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image7.png)

3.  Luego, en la ventana de **Microsoft**, ingrese la contraseña y haga
    clic en el botón **Sign in.**

> ![A login screen with a red box and blue text AI-generated content may
> be incorrect.](./media/image8.png)

4.  En la ventana **Stay signed in?,** haga clic en el botón **Yes**.

> ![A screenshot of a computer error AI-generated content may be
> incorrect.](./media/image9.png)

5.  En el panel **Workspaces**, seleccione **+New workspace**.

> ![](./media/image10.png)

6.  En el panel **Create a workspace** que aparece en el lado derecho,
    ingrese los siguientes detalles y haga clic en el botón **Apply**:

    |    |   |
    |----|----|
    |Name	|+++AI-Fabric-@lab.LabInstance.Id+++ (must be a unique Id) |
    |Advanced	|Under License mode, select Fabric capacity|
    |Default storage format	|Small dataset storage format|

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image11.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image12.png)

7.  Espere a que finalice la implementación. Este proceso tarda entre 1
    y 2 minutos.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image13.png)

## **Tarea 2: Crear un lakehouse**

1.  En la página **Home** de **Fabric**, seleccione **+New item** y
    seleccione el mosaico **Lakehouse**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image14.png)

2.  En el cuadro de diálogo **New lakehouse**, ingrese
    +++**AI_Fabric_lakehouseXX**+++ en el campo **Name**, haga clic en
    el botón **Create** y abra el nuevo **lakehouse**.

> **Nota:** Asegúrese de eliminar el espacio antes de
> **AI_Fabric_lakehouseXX**.
>
> ![A screenshot of a computer Description automatically
> generated](./media/image15.png)

3.  Verá una notificación que indica **Successfully created SQL
    endpoint**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image16.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image17.png)

4.  A continuación, cree un nuevo **notebook** para consultar la tabla.
    En la cinta **Home**, seleccione el menú desplegable **Open
    notebook** y elija **New notebook**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image18.png)

## **Tarea 3: Cargar datos de AdventureWorksDW en el lakehouse**

Primero, cree un **lakehouse** y complételo con los datos necesarios.

Si ya tiene una instancia de **AdventureWorksDW** en un warehouse o
lakehouse, puede omitir este paso. Si no es así, cree un lakehouse desde
un notebook. Use el notebook para completar el lakehouse con los datos.

1.  En el **query editor**, copie y pegue el siguiente código. Haga clic
    en el botón **Run all** para ejecutar la consulta. Cuando la
    consulta finalice, verá los resultados.

```
    import pandas as pd
    from tqdm.auto import tqdm
    base = "https://synapseaisolutionsa.z13.web.core.windows.net/data/AdventureWorks"
    
    # load list of tables
    df_tables = pd.read_csv(f"{base}/adventureworks.csv", names=["table"])
    
    for table in (pbar := tqdm(df_tables['table'].values)):
        pbar.set_description(f"Uploading {table} to lakehouse")
    
        # download
        df = pd.read_parquet(f"{base}/{table}.parquet")
    
        # save as lakehouse table
        spark.createDataFrame(df).write.mode('overwrite').saveAsTable(table)
 ```
> ![A screenshot of a computer Description automatically
> generated](./media/image19.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image20.png)
>
> ![](./media/image21.png)

Después de unos minutos, el lakehouse se completa con los datos
necesarios.

## **Tarea 4: Crear un agente de datos**

1.  Ahora, haga clic en **AI-Fabric-XXXX** en el panel de navegación
    izquierdo.

![](./media/image22.png)

2.  En la página **Home** de **Fabric**, seleccione **+New item.**

![](./media/image23.png)

3.  En el cuadro de búsqueda **Filter by item type**, escriba +++**data
    agent**+++ y seleccione **Data agent**.

![](./media/image24.png)

4.  Ingrese +++**AI-agent**+++ como nombre del **Data agent** y
    seleccione **Create**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image25.png)

![A screenshot of a computer Description automatically
generated](./media/image26.png)

5.  En la página AI-agent, seleccione **Add a data source**.

![A screenshot of a computer Description automatically
generated](./media/image27.png)

6.  En la pestaña **OneLake catalog**, seleccione el **lakehouse
    AI-Fabric_lakehouse** y seleccione **Add.**

> ![](./media/image28.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image29.png)

1.  A continuación, debe seleccionar las tablas a las que desea que la
    habilidad de IA tenga acceso. Este laboratorio utiliza las
    siguientes tablas:

- DimCustomer

- DimDate

- DimGeography

- DimProduct

- DimProductCategory

- DimPromotion

- DimReseller

- DimSalesTerritory

- FactInternetSales

- FactResellerSales

> ![](./media/image30.png)

## **Tarea 5: Proporcionar instrucciones**

1.  Al formular las preguntas inicialmente utilizando las tablas
    listadas y seleccionando **factinternetsales**, el **Data Agent**
    responde de manera bastante precisa.

2.  Por ejemplo, para la pregunta +++**What is the most sold
    product?+++**

![A screenshot of a computer Description automatically
generated](./media/image31.png)

![](./media/image32.png)

3.  Copie la pregunta y las consultas SQL, péguelas en un bloc de notas
    y guarde el archivo para utilizar la información en las próximas
    tareas.

![A screenshot of a computer Description automatically
generated](./media/image33.png)

![A screenshot of a computer Description automatically
generated](./media/image34.png)

4.  Seleccione **FactResellerSales** e ingrese el siguiente texto, luego
    haga clic en el ícono **Submit** como se muestra en la imagen a
    continuación:

+++**What is our most sold product?**+++

![A screenshot of a computer Description automatically
generated](./media/image35.png)

![A screenshot of a computer Description automatically
generated](./media/image36.png)

A medida que continúe experimentando con consultas, debería agregar más
instrucciones.

5.  Seleccione **dimcustomer**, ingrese el siguiente texto y haga clic
    en el ícono **Submit.**

+++**How many active customers did we have on June 1st, 2013?**+++

![A screenshot of a computer Description automatically
generated](./media/image37.png)

![A screenshot of a computer Description automatically
generated](./media/image38.png)

7.  Copie la pregunta y las consultas SQL, péguelas en un bloc de notas
    y guarde el archivo para utilizar la información en las próximas
    tareas.

![A screenshot of a computer Description automatically
generated](./media/image39.png)

![A screenshot of a computer Description automatically
generated](./media/image40.png)

8.  Seleccione **dimdate, FactInternetSales**, ingrese el siguiente
    texto y haga clic en el ícono **Submit:**

+++**what are the monthly sales trends for the last year?**+++

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image41.png)

![A screenshot of a computer Description automatically
generated](./media/image42.png)

6.  Seleccione **dimproduct, FactInternetSales**, ingrese el siguiente
    texto y haga clic en el ícono **Submit**:

+++**which product category had the highest average sales price?**+++

> ![A screenshot of a computer Description automatically
> generated](./media/image43.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image44.png)

Parte del problema es que “**active customer**” no tiene una definición
formal. Proporcionar más instrucciones en el cuadro de texto **Notes to
the model** podría ayudar, pero los usuarios podrían hacer esta pregunta
con frecuencia. Debe asegurarse de que la IA pueda manejar la consulta
correctamente.

7.  La consulta relevante es de complejidad moderada, por lo que se
    recomienda proporcionar un ejemplo seleccionando el botón **Example
    queries** en el panel **Setup.**

> ![A screenshot of a computer Description automatically
> generated](./media/image45.png)

8.  En la pestaña **Example queries**, seleccione **Add example.**

> ![A screenshot of a computer Description automatically
> generated](./media/image46.png)

9.  Aquí, debe agregar consultas de ejemplo para la fuente de datos
    lakehouse que ha creado. Agregue la siguiente pregunta en el campo
    **question**:

**+++What is the most sold product?+++**

![A screenshot of a computer Description automatically
generated](./media/image47.png)

10. Agregue la pregunta 1 que ha guardado en el bloc de notas:  
      
```      
SELECT TOP 1 ProductKey, SUM(OrderQuantity) AS TotalQuantitySold
FROM [dbo].[factinternetsales]
GROUP BY ProductKey
ORDER BY TotalQuantitySold DESC
```

![A screenshot of a computer Description automatically
generated](./media/image48.png)

11. Para agregar un nuevo campo de consulta, haga clic en **+Add.**

![A screenshot of a computer Description automatically
generated](./media/image49.png)

12. Para agregar una segunda pregunta, utilice el campo **Question**:

**+++What are the monthly sales trends for the last year?+++**

![A screenshot of a computer Description automatically
generated](./media/image50.png)

13. Agregue la **pregunta 3** que ha guardado en el bloc de notas:  
      
	```      
	SELECT
	    d.CalendarYear,
	    d.MonthNumberOfYear,
	    d.EnglishMonthName,
	    SUM(f.SalesAmount) AS TotalSales
	FROM
	    dbo.factinternetsales f
	    INNER JOIN dbo.dimdate d ON f.OrderDateKey = d.DateKey
	WHERE
	    d.CalendarYear = (
	        SELECT MAX(CalendarYear)
	        FROM dbo.dimdate
	        WHERE DateKey IN (SELECT DISTINCT OrderDateKey FROM dbo.factinternetsales)
	    )
	GROUP BY
	    d.CalendarYear,
	    d.MonthNumberOfYear,
	    d.EnglishMonthName
	ORDER BY
	    d.MonthNumberOfYear
	```
> ![A screenshot of a computer Description automatically
> generated](./media/image51.png)

14. Para agregar un nuevo campo de consulta, haga clic en **+Add**.

![A screenshot of a computer Description automatically
generated](./media/image52.png)

15. Para agregar una tercera pregunta, utilice el campo **Question**:

**+++Which product category has the highest average sales price?+++**

![A screenshot of a computer Description automatically
generated](./media/image53.png)

16. Agregue la **pregunta 4** que ha guardado en el bloc de notas:  
      
```      
SELECT TOP 1
    dp.ProductSubcategoryKey AS ProductCategory,
    AVG(fis.UnitPrice) AS AverageSalesPrice
FROM
    dbo.factinternetsales fis
INNER JOIN
    dbo.dimproduct dp ON fis.ProductKey = dp.ProductKey
GROUP BY
    dp.ProductSubcategoryKey
ORDER BY
    AverageSalesPrice DESC
```

![A screenshot of a computer Description automatically
generated](./media/image54.png)

11. Agregue todas las preguntas y sus correspondientes consultas SQL que
    haya guardado en el bloc de notas, y luego haga clic en **Export
    all.**

![A screenshot of a computer Description automatically
generated](./media/image55.png)

![A screenshot of a computer Description automatically
generated](./media/image56.png)

## **Tarea 6: Usar el agente de datos de forma programática**

Se han agregado instrucciones y ejemplos al Data agent. A medida que se
realizan las pruebas, agregar más ejemplos e instrucciones puede mejorar
aún más la habilidad de IA. Trabaje con sus colegas para verificar si
proporcionó ejemplos e instrucciones que cubran los tipos de preguntas
que desean hacer.

Puede usar la habilidad de IA de forma programática dentro de un
notebook de Fabric. Para determinar si la habilidad de IA tiene un valor
de URL publicado:

1.  En la página Data agent de Fabric, en la cinta **Home**, seleccione
    **Settings**.

![A screenshot of a computer Description automatically
generated](./media/image57.png)

2.  Antes de publicar la habilidad de IA, aún no dispone de un valor de
    URL publicado, como se muestra en esta captura de pantalla.

3.  Cierre la configuración de la habilidad de IA (AI Skill setting).

![A screenshot of a computer Description automatically
generated](./media/image58.png)

4.  En la cinta **Home**, seleccione **Publish**.

> ![A screenshot of a computer Description automatically
> generated](./media/image59.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image60.png)

9.  Haga clic en **View publishing details.**

> ![A screenshot of a computer Description automatically
> generated](./media/image61.png)

5.  La URL publicada para el AI agent aparece, como se muestra en esta
    captura de pantalla.

6.  Copie la URL y pégela en un bloc de notas, luego guarde el archivo
    para utilizar la información en los pasos siguientes.

> ![A screenshot of a computer Description automatically
> generated](./media/image62.png)

7.  Seleccione **Notebook1** en el panel de navegación izquierdo.

![A screenshot of a computer Description automatically
generated](./media/image63.png)

10. Utilice el ícono **+ Code** debajo de la salida de la celda para
    agregar una nueva celda de código en el notebook, ingrese el código
    siguiente y reemplace la **URL**. Luego, haga clic en el botón **▷
    Run** y revise el resultado:

+++%pip install "openai==1.70.0"+++

> ![](./media/image64.png)
>
> ![](./media/image65.png)

11. Utilice el ícono **+ Code** debajo de la salida de la celda para
    agregar una nueva celda de código en el notebook, ingrese el código
    siguiente y reemplace la **URL**. Luego, haga clic en el botón **▷
    Run** y revise el resultado:

> +++%pip install httpx==0.27.2+++
>
> ![](./media/image66.png)
>
> ![A screenshot of a computer Description automatically
> generated](./media/image67.png)

8.  Utilice el ícono **+ Code** debajo de la salida de la celda para
    agregar una nueva celda de código en el notebook, ingrese el código
    siguiente y reemplace la **URL**. Luego, haga clic en el botón **▷
    Run** y revise el resultado:

```
import requests
import json
import pprint
import typing as t
import time
import uuid

from openai import OpenAI
from openai._exceptions import APIStatusError
from openai._models import FinalRequestOptions
from openai._types import Omit
from openai._utils import is_given
from synapse.ml.mlflow import get_mlflow_env_config
from sempy.fabric._token_provider import SynapseTokenProvider
 
base_url = "https://<generic published base URL value>"
question = "What datasources do you have access to?"

configs = get_mlflow_env_config()

# Create OpenAI Client
class FabricOpenAI(OpenAI):
    def __init__(
        self,
        api_version: str ="2024-05-01-preview",
        **kwargs: t.Any,
    ) -> None:
        self.api_version = api_version
        default_query = kwargs.pop("default_query", {})
        default_query["api-version"] = self.api_version
        super().__init__(
            api_key="",
            base_url=base_url,
            default_query=default_query,
            **kwargs,
        )
    
    def _prepare_options(self, options: FinalRequestOptions) -> None:
        headers: dict[str, str | Omit] = (
            {**options.headers} if is_given(options.headers) else {}
        )
        options.headers = headers
        headers["Authorization"] = f"Bearer {configs.driver_aad_token}"
        if "Accept" not in headers:
            headers["Accept"] = "application/json"
        if "ActivityId" not in headers:
            correlation_id = str(uuid.uuid4())
            headers["ActivityId"] = correlation_id

        return super()._prepare_options(options)

# Pretty printing helper
def pretty_print(messages):
    print("---Conversation---")
    for m in messages:
        print(f"{m.role}: {m.content[0].text.value}")
    print()

fabric_client = FabricOpenAI()
# Create assistant
assistant = fabric_client.beta.assistants.create(model="not used")
# Create thread
thread = fabric_client.beta.threads.create()
# Create message on thread
message = fabric_client.beta.threads.messages.create(thread_id=thread.id, role="user", content=question)
# Create run
run = fabric_client.beta.threads.runs.create(thread_id=thread.id, assistant_id=assistant.id)

# Wait for run to complete
while run.status == "queued" or run.status == "in_progress":
    run = fabric_client.beta.threads.runs.retrieve(
        thread_id=thread.id,
        run_id=run.id,
    )
    print(run.status)
    time.sleep(2)

# Print messages
response = fabric_client.beta.threads.messages.list(thread_id=thread.id, order="asc")
pretty_print(response)

# Delete thread
fabric_client.beta.threads.delete(thread_id=thread.id)
```
> ![](./media/image68.png)
>
> ![](./media/image69.png)

## **Tarea 7: Eliminar los recursos**

1.  Seleccione su espacio de trabajo, **AI-Fabric-XXXX**, en el menú de
    navegación izquierdo. Se abrirá la vista de elementos del espacio de
    trabajo.

> ![A screenshot of a computer Description automatically
> generated](./media/image70.png)

2.  Seleccione la opción ... debajo del nombre del espacio de trabajo y
    luego seleccione **Workspace settings**.

> ![A screenshot of a computer Description automatically
> generated](./media/image71.png)

3.  Seleccione **Other** y **Remove this workspace**.

> ![A screenshot of a computer Description automatically
> generated](./media/image72.png)

4.  Haga clic en **Delete** en la advertencia que aparece.

> ![A screenshot of a computer Description automatically
> generated](./media/image73.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image74.png)

**Resumen:**

En este laboratorio, aprendió a desbloquear el poder del análisis
conversacional utilizando el **Data Agent** de Microsoft Fabric.
Configuró un **espacio de trabajo** de Fabric, ingresó datos
estructurados en un **lakehouse** y configuró una habilidad de IA para
traducir preguntas en lenguaje natural a consultas SQL. También mejoró
las capacidades del **agente de datos** proporcionando instrucciones y
ejemplos para refinar la generación de consultas. Finalmente, llamó al
agente de forma programática desde un **notebook** de Fabric,
demostrando la integración de IA de extremo a extremo. Este laboratorio
le permite hacer que los datos empresariales sean más accesibles,
utilizables e inteligentes para los usuarios de negocio mediante
lenguaje natural y tecnologías de IA generativa.
