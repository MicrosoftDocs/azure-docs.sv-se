---
title: Synapse Studio notebook-datorer
description: I den här artikeln får du lära dig hur du skapar och utvecklar Azure Synapse Studio-anteckningsböcker för dataförberedelse och visualisering.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: c28bc47945882e6b7bbd39d1ba8524a1f7491ceb
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835224"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Skapa, utveckla och underhålla Synapse Studio notebook-Azure Synapse Analytics

En Synapse Studio notebook-fil är ett webbgränssnitt där du kan skapa filer som innehåller livekod, visualiseringar och berättelsetext. Notebooks är en bra plats för att validera idéer och använda snabba experiment för att få insikter från dina data. Notebooks används också ofta för förberedelse av data, datavisualisering, maskininlärning och andra stordatascenarier.

Med en Azure Synapse Studio-notebook-dator kan du:

* Kom igång med noll konfigurationsarbete.
* Skydda data med inbyggda säkerhetsfunktioner för företag.
* Analysera data i rådataformat (CSV, txt, JSON osv.), bearbetade filformat (parquet, Delta Lake, ORC osv.) och SQL-tabelldatafiler mot Spark och SQL.
* Var produktiv med förbättrade redigeringsfunktioner och inbyggd datavisualisering.

Den här artikeln beskriver hur du använder notebook-datorer i Azure Synapse Studio.

## <a name="preview-of-the-new-notebook-experience"></a>Förhandsversion av den nya notebook-upplevelsen
Synapse-teamet införde den nya notebook-komponenten i Synapse Studio för att tillhandahålla konsekvent notebook-upplevelse för Microsoft-kunder och maximera identifiering, produktivitet, delning och samarbete. Den nya notebook-upplevelsen är redo för förhandsversion. Markera knappen **Förhandsgranskningsfunktioner** i notebook-verktygsfältet för att aktivera den. Tabellen nedan visar funktionsjämförelse mellan befintliga notebook-datorer (så kallade "klassiska notebook-datorer") med den nya förhandsversionen.  

|Funktion|Klassisk notebook-dator|Notebook-förhandsgranskning|
|--|--|--|
|%run| Stöds inte | &#9745;|
|%-historik| Stöds inte |&#9745;
|%load| Stöds inte |&#9745;|
|%%html| Stöds inte |&#9745;|
|Dra och släpp för att flytta en cell| Stöds inte |&#9745;|
|Formatera textcell med verktygsfältsknappar|&#9745;| Inte tillgängligt |
|Ångra cellåtgärd| &#9745;| Inte tillgängligt |


## <a name="create-a-notebook"></a>Skapa en notebook-fil

Det finns två sätt att skapa en notebook-dator. Du kan skapa en ny notebook-dator eller importera en befintlig notebook-Azure Synapse en arbetsyta från **Object Explorer**. Azure Synapse Studio-notebook-filer kan identifiera IPYNB Jupyter Notebook standardfiler.

![skapa en import-notebook-dator](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Utveckla notebook-filer

Notebooks består av celler, som är enskilda kod- eller textblock som kan kördes oberoende av varandra eller som en grupp.

### <a name="add-a-cell"></a>Lägga till en cell

Det finns flera sätt att lägga till en ny cell i anteckningsboken.

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

1. Expandera den övre vänstra **knappen + Cell** och välj Lägg till **kodcell eller** Lägg till **textcell.**

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Hovra över utrymmet mellan två celler och välj Lägg **till kod eller** Lägg till **text.**

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Använd [genvägsnycklar under kommandoläget](#shortcut-keys-under-command-mode). Tryck **på A** för att infoga en cell ovanför den aktuella cellen. Tryck **på B** för att infoga en cell under den aktuella cellen.


# <a name="preview-notebook"></a>[Notebook-förhandsgranskning](#tab/preview)

1. Expandera den övre vänstra **knappen + Cell** och välj **kodcellen** eller **Markdown-cellen**.
    ![add-azure-notebook-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Välj plustecknet i början av en cell och välj **Kodcell eller** **Markdown-cell**.

    ![add-azure-notebook-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Använd [aznb-genvägsnycklarna i kommandoläget](#shortcut-keys-under-command-mode). Tryck **på A** för att infoga en cell ovanför den aktuella cellen. Tryck **på B** för att infoga en cell under den aktuella cellen.

---

### <a name="set-a-primary-language"></a>Ange ett primärt språk

Azure Synapse Studio Notebooks stöder fyra Apache Spark språk:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET för Apache Spark (C#)

Du kan ange det primära språket för nya tillagda celler från listrutan i det översta kommandofältet.

   ![standard-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Använda flera språk

Du kan använda flera språk i en notebook-dator genom att ange rätt magiska språkkommando i början av en cell. I följande tabell visas magiska kommandon för att växla cellspråk.

|Magiskt kommando |Språk | Description |  
|---|------|-----|
|%%pyspark| Python | Köra en **Python-fråga** mot Spark-kontext.  |
|%%spark| Scala | Köra en **Scala-fråga** mot Spark-kontext.  |  
|%%sql| SparkSQL | Köra en **SparkSQL-fråga** mot Spark-kontext.  |
|%%csharp | .NET för Spark C # | Kör en **.NET för Spark C#-fråga** mot Spark-kontext. |

Följande bild är ett exempel på hur du kan skriva en PySpark-fråga med hjälp av det magiska **kommandot %%pyspark** eller en SparkSQL-fråga med **%%sql** magiskt kommando i en **Notebook Spark(Scala).** Observera att det primära språket för notebook-filen är inställt på pySpark.

   ![Magiska Synapse Spark-kommandon](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages&quot;></a>Använda temporära tabeller för att referera till data mellan språk

Du kan inte referera till data eller variabler direkt över olika språk i en Synapse Studio notebook-dator. I Spark kan en tillfällig tabell refereras till på olika språk. Här är ett exempel på hur du läser en `Scala` DataFrame i `PySpark` och använder en `SparkSQL` Temporär Spark-tabell som en lösning.

1. I Cell 1 läser du en DataFrame från en SQL-poolanslutning med Hjälp av Scala och skapar en tillfällig tabell.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics(&quot;mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. I cell 2 frågar du efter data med Hjälp av Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. I Cell 3 använder du data i PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense i IDE-format

Azure Synapse Studio-notebook-datorer är integrerade med Monaco-redigeraren för att ta IntelliSense i IDE-format till cellredigeraren. Syntaxmarkering, felmarkör och automatiska kodslut hjälper dig att skriva kod och identifiera problem snabbare.

IntelliSense-funktionerna har olika mognadsnivåer för olika språk. Använd följande tabell för att se vad som stöds.

|Språk| Syntax markera | Syntaxfelmarkör  | Slutförande av syntaxkod | Slutförande av variabelkod| Slutförande av systemfunktionskod| Slutförande av användarfunktionskod| Smarta indrag | Koddekning|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Spark (Scala)|Ja|Ja|Ja|Ja|-|-|-|Ja|
|SparkSQL|Ja|Ja|-|-|-|-|-|-|
|.NET för Spark (C#)|Yes|-|-|-|-|-|-|-|



### <a name="code-snippets"></a>Kodfragment

Azure Synapse Studio-anteckningsböcker innehåller kodfragment som gör det enklare att ange vanliga kodmönster, till exempel konfigurera din Spark-session, läsa data som en Spark DataFrame eller rita diagram med matplotlib osv.

Kodfragment visas [i IntelliSense](#ide-style-intellisense) blandat med andra förslag. Innehållet i kodfragmenten överensstämmer med kodcellsspråket. Du kan se tillgängliga kodfragment genom att skriva **Kodfragment** eller om några nyckelord visas i kodfragmentrubriken i kodcellsredigeraren. Genom att till exempel skriva **läsa** kan du se en lista över kodfragment för att läsa data från olika datakällor.

![Synapse-kodfragment](./media/apache-spark-development-using-notebooks/synapse-code-snippets.gif#lightbox)



### <a name="format-text-cell-with-toolbar-buttons"></a>Formatera textcell med verktygsfältsknappar

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Du kan använda formatknapparna i verktygsfältet för textceller för att utföra vanliga markdown-åtgärder. Den innehåller fet text, kursiv stil, infogning av kodfragment, infogning av osorterad lista, infogning av ordnad lista och infogning av bild från URL.

  ![Verktygsfältet för Synapse-textcell](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Notebook-förhandsversion](#tab/preview)

Verktygsfältet För formateringsknappen är inte tillgängligt för notebook-förhandsversionen ännu. 

---

### <a name="undo-cell-operations"></a>Ångra cellåtgärder

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Välj knappen **Ångra** eller tryck på **Ctrl +Z för** att återkalla den senaste cellåtgärden. Nu kan du ångra de senaste 20 historiska cellåtgärderna. 

   ![Synapse ångra celler](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Notebook-förhandsversion](#tab/preview)

Åtgärden Ångra cell är inte tillgänglig för notebook-förhandsversionen ännu. 

---

### <a name="move-a-cell"></a>Flytta en cell

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Välj ellipserna (...) för att komma åt menyn för andra cellåtgärder längst till höger. Välj sedan **Flytta cellen uppåt eller** Flytta cellen nedåt **för** att flytta den aktuella cellen. 

Du kan också använda [kortkommandon i kommandoläget](#shortcut-keys-under-command-mode). Tryck **på Ctrl+Alt+ctrl för** att flytta upp den aktuella cellen. Tryck **på Ctrl+Alt+↓** för att flytta den aktuella cellen nedåt.

   ![flytta en cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Notebook-förhandsgranskning](#tab/preview)

Klicka på vänster sida av en cell och dra den till önskad position. 
    ![Synapse flyttar celler](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Ta bort en cell

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Om du vill ta bort en cell väljer du ellipserna (...) för att komma åt menyn för andra cellåtgärder längst till höger och väljer sedan **Ta bort cell.** 

Du kan också använda [kortkommandon i kommandoläget](#shortcut-keys-under-command-mode). Tryck **på D,D för** att ta bort den aktuella cellen.
  
   ![ta bort en cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Notebook-förhandsgranskning](#tab/preview)

Om du vill ta bort en cell väljer du knappen Ta bort till höger i cellen. 

Du kan också använda [kortkommandon i kommandoläget](#shortcut-keys-under-command-mode). Tryck **på Skift + D för** att ta bort den aktuella cellen. 

   ![azure-notebook-delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Komprimera en cellindata

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Välj pilknappen längst ned i den aktuella cellen för att dölja den. Om du vill expandera den väljer du pilknappen medan cellen är komprimerad.

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Notebook-förhandsversion](#tab/preview)

Välj **ellipserna** Fler kommandon (...) i cellverktygsfältet och **indata för** att minimera den aktuella cellens indata. Om du vill expandera den markerar **du de indata** som döljs när cellen döljs.

   ![azure-notebook-collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Minimera utdata från en cell

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Välj knappen **minimera utdata** längst upp till vänster i den aktuella cellens utdata för att minimera den. Om du vill expandera den väljer du **Visa cellutdata** medan cellutdata är komprimerade.

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Notebook-förhandsversion](#tab/preview)

Välj **ellipserna** Fler kommandon (...) i cellverktygsfältet och **utdata för** att minimera den aktuella cellens utdata. Om du vill expandera den väljer du samma knapp medan cellens utdata är dolda.

   ![azure-notebook-collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Köra notebook-filer

Du kan köra kodcellerna i anteckningsboken individuellt eller samtidigt. Status och förlopp för varje cell visas i anteckningsboken.

### <a name="run-a-cell"></a>Köra en cell

Det finns flera sätt att köra koden i en cell.

1. Hovra över den cell som du vill köra och välj **knappen Kör cell** eller tryck på **Ctrl+Retur.**

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. Använd [genvägsnycklar under kommandoläget](#shortcut-keys-under-command-mode). Tryck **på Skift+Retur** för att köra den aktuella cellen och markera cellen nedan. Tryck **på Alt + Retur** för att köra den aktuella cellen och infoga en ny cell nedan.

---

### <a name="run-all-cells"></a>Köra alla celler
Välj knappen **Kör alla** för att köra alla celler i den aktuella notebook-datorn i följd.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>Kör alla celler ovanför eller under

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Om du vill öppna menyn för andra cellåtgärder längst till höger väljer du ellipserna (**...**). Välj sedan **Kör celler ovan för** att köra alla celler ovanför den aktuella i följd. Välj **Kör celler nedan för** att köra alla celler under den aktuella i följd.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)

# <a name="preview-notebook"></a>[Notebook-förhandsgranskning](#tab/preview)

Expandera listrutan från knappen **Kör alla** och välj sedan Kör celler **ovan för** att köra alla celler ovanför den aktuella i följd. Välj **Kör celler nedan för** att köra alla celler under den aktuella i följd.

   ![azure-notebook-run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>Avbryt alla celler som körs

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)
Välj knappen **Avbryt alla** för att avbryta cellerna som körs eller cellerna som väntar i kön. 
   ![avbryt alla celler](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Notebook-förhandsversion](#tab/preview)

Välj knappen **Avbryt alla** för att avbryta cellerna som körs eller cellerna som väntar i kön. 
   ![azure-notebook-cancel-all-cells](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---



### <a name="notebook-reference"></a>Notebook-referens

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Stöds inte.

# <a name="preview-notebook"></a>[Notebook-förhandsversion](#tab/preview)

Du kan använda ```%run <notebook path>``` magic-kommandot för att referera till en annan notebook-dator i den aktuella notebook-kontexten. Alla variabler som definieras i referensanteckningsboken är tillgängliga i den aktuella notebook-datorn. ```%run``` magic-kommandot stöder kapslade anrop men har inte stöd för rekursiva anrop. Du får ett undantag om instruktionsdjupet är större än fem. ```%run``` -kommandot stöder för närvarande endast att skicka en notebook-sökväg som parameter. 

Exempel: ``` %run /path/notebookA ```.

> [!NOTE]
> Notebook-referens stöds inte i Synapse-pipeline.
>
>

---

### <a name="variable-explorer"></a>Variabelutforskaren

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Stöds inte.

# <a name="preview-notebook"></a>[Notebook-förhandsgranskning](#tab/preview)

Synapse Notebook tillhandahåller en inbyggd variabelutforskare där du kan se listan över variablernas namn, typ, längd och värde i den aktuella Spark-sessionen för PySpark-celler (Python). Fler variabler visas automatiskt när de definieras i kodcellerna. Om du klickar på varje kolumnrubrik sorteras variablerna i tabellen.

Du kan välja knappen **Variabler i** notebook-kommandofältet för att öppna eller dölja variabelutforskaren.

![azure-notebook-variable-explorer](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-variable-explorer.png)


---

### <a name="cell-status-indicator"></a>Cellstatusindikator

En stegvis cellkörningsstatus visas under cellen för att hjälpa dig att se dess aktuella förlopp. När cellkörningen är klar visas en körningssammanfattning med den totala varaktigheten och sluttiden och sparas där för framtida bruk.

![cellstatus](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Förloppsindikator för Spark

Azure Synapse Studio-notebook-datorn är helt Spark-baserad. Kodceller körs på den serverlösa Apache Spark fjärrpoolen. En förloppsindikator för Spark-jobb visas med en förloppsindikator i realtid som hjälper dig att förstå jobbets körningsstatus.
Antalet aktiviteter per jobb eller fas hjälper dig att identifiera den parallella nivån för spark-jobbet. Du kan också gå djupare in i Spark-användargränssnittet för ett specifikt jobb (eller fas) genom att välja länken för jobbets (eller fasens) namn.


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark-sessionskonfiguration

Du kan ange tidsgränsens varaktighet, antalet och storleken på utförare som ska ge till den aktuella Spark-sessionen i **Konfigurera session**. Starta om Spark-sessionen för att konfigurationsändringarna ska börja gälla. Alla cachelagrade notebook-variabler rensas.

[![sessionshantering](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Magic-kommando för Spark-sessionskonfiguration
Du kan också ange inställningar för Spark-sessionen via ett magiskt kommando **%%configure**. Spark-sessionen måste startas om för att inställningarna ska gälla. Vi rekommenderar att du kör **%%configure** i början av anteckningsboken. Här är ett exempel, se en https://github.com/cloudera/livy#request-body fullständig lista över giltiga parametrar 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```
> [!NOTE]
> Magic-kommandot för Spark-sessionskonfiguration stöds inte i Synapse-pipelinen.
>
>

## <a name="bring-data-to-a-notebook"></a>Hämta data till en notebook-dator

Du kan läsa in data Azure Blob Storage, Azure Data Lake Store Gen 2 och SQL-pool enligt kodexe exemplen nedan.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Läsa en CSV från Azure Data Lake Store Gen2 som en Spark DataFrame

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Läsa en CSV från Azure Blob Storage som en Spark DataFrame

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>Läsa data från det primära lagringskontot

Du kan komma åt data i det primära lagringskontot direkt. Du behöver inte ange de hemliga nycklarna. I Datautforskaren högerklickar du på en fil och väljer **Ny notebook-fil** för att se en ny notebook-fil med datautdrag som genererats automatiskt.

![data-till-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Spara notebook-filer

Du kan spara en enskild notebook-dator eller alla notebook-datorer på din arbetsyta.

1. Om du vill spara ändringar som du har gjort i en enskild notebook-dator väljer **du knappen** Publicera i notebook-kommandofältet.

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Om du vill spara alla notebook-datorer på arbetsytan väljer **du knappen Publicera** alla i arbetsytans kommandofält. 

   ![publicera alla](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

I notebook-egenskaperna kan du konfigurera om du vill inkludera cellutdata när du sparar.

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magiska kommandon
Du kan använda välbekanta Jupyter Magic-kommandon i Azure Synapse Studio-notebook-datorer. Granska följande lista som de aktuella tillgängliga magiska kommandona. Berätta dina [användningsfall på GitHub](https://github.com/MicrosoftDocs/azure-docs/issues/new) så att vi kan fortsätta att bygga ut fler magiska kommandon som uppfyller dina behov.

> [!NOTE]
> Endast följande magiska kommandon stöds i Synapse-pipeline: %%pyspark, %%spark, %%csharp, %%sql. 
>
>

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Tillgängliga rad magics: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Tillgängliga cell magics: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages),[%%configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[Notebook-förhandsgranskning](#tab/preview)

Tillgängliga line magics: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [%run](#notebook-reference), [%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Tillgängliga cell magics: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages), [%%html](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%%configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Integrera en notebook-dator

### <a name="add-a-notebook-to-a-pipeline"></a>Lägga till en notebook-dator i en pipeline

Välj knappen **Lägg till i pipeline** i det övre högra hörnet för att lägga till en notebook-dator i en befintlig pipeline eller skapa en ny pipeline.

![Lägga till notebook-dator i pipeline](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Ange en parametercell

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Om du vill parameterisera anteckningsboken väljer du ellipserna (...) för att komma åt den andra cellåtgärder-menyn längst till höger. Välj sedan **Växla parametercell för** att ange cellen som parametercell.

![toggle-parameter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Notebook-förhandsversion](#tab/preview)

Om du vill parameterisera notebook-datorn väljer du ellipserna (...) för att komma **åt fler** kommandon i cellverktygsfältet. Välj sedan **Växla parametercell för** att ange cellen som parametercell.

![azure-notebook-toggle-parameter](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory söker efter parametercellen och behandlar den här cellen som standardvärden för de parametrar som skickas vid körningen. Körningsmotorn lägger till en ny cell under parametercellen med indataparametrar för att skriva över standardvärdena. När en parametercell inte har angetts infogas den inmatade cellen överst i anteckningsboken.


### <a name="assign-parameters-values-from-a-pipeline"></a>Tilldela parametervärden från en pipeline

När du har skapat en notebook-dator med parametrar kan du köra den från en pipeline med Azure Synapse Notebook-aktiviteten. När du har lagt till aktiviteten på pipelinearbetsytan kan du ange parametervärdena under avsnittet **Basparametrar** på **fliken** Inställningar. 

![Tilldela en parameter](./media/apache-spark-development-using-notebooks/assign-parameter.png)

När du tilldelar parametervärden kan du använda [pipelineuttrycksspråket eller](../../data-factory/control-flow-expression-language-functions.md) [systemvariablerna](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Kortkommandon

Precis som Jupyter Notebooks har Azure Synapse Studio-notebook-datorer ett modalt användargränssnitt. Tangentbordet gör olika saker beroende på vilket läge notebook-cellen är i. Synapse Studio stöder följande två lägen för en viss kodcell: kommandoläge och redigeringsläge.

1. En cell är i kommandoläge när det inte finns någon textmarkör som uppmanar dig att skriva. När en cell är i kommandoläge kan du redigera anteckningsboken som helhet men inte skriva i enskilda celler. Ange kommandoläge genom att `ESC` trycka på eller använda musen för att välja utanför en cells redigeringsområde.

   ![kommandoläge](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. Redigeringsläget visas med en textmarkör som uppmanar dig att skriva i redigeringsområdet. När en cell är i redigeringsläge kan du skriva i cellen. Ange redigeringsläget genom att `Enter` trycka på eller använda musen för att markera i en cells redigeringsområde.
   
   ![redigerings-läge](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Kortkommandon under kommandoläge

# <a name="classical-notebook"></a>[Klassisk notebook-dator](#tab/classical)

Med hjälp av följande kortkommandon kan du enklare navigera och köra kod i Azure Synapse notebook-datorer.

| Action |Synapse Studio notebook-genvägar  |
|--|--|
|Kör den aktuella cellen och välj nedan | Skift+Retur |
|Kör den aktuella cellen och infoga nedan | Alt + Retur |
|Välj cell ovan| Upp |
|Välj cell nedan| Ned |
|Infoga cellen ovan| A |
|Infoga cellen nedan| B |
|Utöka markerade celler ovan| Skift+Upp |
|Utöka markerade celler nedan| Skift+Ned|
|Flytta cellen uppåt| Ctrl+Alt+ctrl+ |
|Flytta cellen nedåt| Ctrl+Alt+↓ |
|Ta bort markerade celler| D, D |
|Växla till redigeringsläge| Ange |

# <a name="preview-notebook"></a>[Notebook-förhandsversion](#tab/preview)

| Action |Synapse Studio notebook-genvägar  |
|--|--|
|Kör den aktuella cellen och välj nedan | Skift+Retur |
|Kör den aktuella cellen och infoga nedan | Alt + Retur |
|Kör aktuell cell| Ctrl+Retur |
|Markera cellen ovan| Upp |
|Markera cellen nedan| Ned |
|Markera föregående cell| K |
|Välj nästa cell| J |
|Infoga cellen ovan| A |
|Infoga cellen nedan| B |
|Ta bort markerade celler| Skift + D |
|Växla till redigeringsläge| Ange |

---

### <a name="shortcut-keys-under-edit-mode"></a>Kortkommandon under redigeringsläge


Med hjälp av följande kortkommandon kan du enklare navigera och köra kod i Azure Synapse notebook-filer i redigeringsläge.

| Action |Synapse Studio notebook-genvägar  |
|--|--|
|Flytta markören uppåt | Upp |
|Flytta markören nedåt|Ned|
|Ångra|Ctrl + Z|
|Gör om|Ctrl + Y|
|Kommentar/avkommentering|Ctrl + /|
|Ta bort ord före|Ctrl + Backsteg|
|Ta bort ord efter|Ctrl + Ta bort|
|Gå till cellstart|Ctrl + Start|
|Gå till cellslut |Ctrl + End|
|Gå ett ord åt vänster|Ctrl + vänster|
|Gå ett ord till höger|Ctrl + höger|
|Välj alla|Ctrl + A|
|Strecksatsen| Ctrl +]|
|Identifiera|Ctrl + [|
|Växla till kommandoläge| Esc |

---

## <a name="next-steps"></a>Nästa steg
- [Kolla in Synapse-exempelanteckningsböcker](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Snabbstart: Skapa en Apache Spark i en Azure Synapse Analytics med hjälp av webbverktyg](../quickstart-apache-spark-notebook.md)
- [Vad är Apache Spark i Azure Synapse Analytics](apache-spark-overview.md)
- [Använda .NET för Apache Spark med Azure Synapse Analytics](spark-dotnet.md)
- [.NET för Apache Spark dokumentation](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)