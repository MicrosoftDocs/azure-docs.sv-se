---
title: Synapse Studio-anteckningsböcker
description: I den här artikeln får du lära dig hur du skapar och utvecklar Azure Synapse Studio-anteckningsböcker för förberedelse av data och visualisering.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 57999ce53e536d422e6502a77aaccdc66b4c5077
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898502"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Skapa, utveckla och underhålla Synapse Studio-anteckningsböcker i Azure Synapse Analytics

En Synapse Studio-anteckningsbok är ett webb gränssnitt där du kan skapa filer som innehåller Live-kod, visualiseringar och text. Bärbara datorer är en bra plats för att verifiera idéer och använda snabba experiment för att få insikter om dina data. Antecknings böcker används också ofta i förberedelse av data, data visualisering, maskin inlärning och andra stora data scenarier.

Med en Azure Synapse Studio-anteckningsbok kan du:

* Kom igång med noll setup-arbete.
* Skydda dina data med inbyggda funktioner för företags säkerhet.
* Analysera data över RAW-format (CSV, txt, JSON osv.), bearbetade fil format (Parquet, delta Lake, ORC osv.) och SQL-datafiler i tabell mot Spark och SQL.
* Var produktiv med förbättrade redigerings funktioner och inbyggd data visualisering.

Den här artikeln beskriver hur du använder antecknings böcker i Azure Synapse Studio.

## <a name="preview-of-the-new-notebook-experience"></a>För hands versionen av den nya bärbara datorn
Synapse-teamet har tilldelat den nya komponenten för bärbara datorer i Synapse Studio för att ge en enhetlig bärbar upplevelse för Microsoft-kunder och maximera identifiering, produktivitet, delning och samarbete. Den nya bärbara datorn är redo för för hands versionen. Markera knappen för **hands versions funktioner** i anteckningsbok-verktygsfältet för att aktivera den. I tabellen nedan samlas funktions jämförelser mellan befintliga antecknings böcker (så kallade "klassisk Notebook") med den nya för hands versionen.  

|Funktion|Klassisk klassiskt Notebook|För hands version av Notebook|
|--|--|--|
|% kör| Stöds inte | &#9745;|
|% historik| Stöds inte |&#9745;
|% belastning| Stöds inte |&#9745;|
|%% HTML| Stöds inte |&#9745;|
|Dra och släpp för att flytta en cell| Stöds inte |&#9745;|
|Beständig visning () utdata|&#9745;| Inte tillgängligt |
|Avbryt alla| &#9745;| Inte tillgängligt|
|Kör alla celler ovan|&#9745;| Inte tillgängligt |
|Kör alla celler nedan|&#9745;| Inte tillgängligt |
|Formatera en text cell med knappar i verktygsfältet|&#9745;| Inte tillgängligt |
|Ångra cell åtgärd| &#9745;| Inte tillgängligt |


## <a name="create-a-notebook"></a>Skapa en notebook-fil

Det finns två sätt att skapa en antecknings bok. Du kan skapa en ny antecknings bok eller importera en befintlig antecknings bok till en Azure Synapse-arbetsyta från **Object Explorer**. Bärbara Azure Synapse Studio-datorer kan känna igen standard Jupyter Notebook IPYNB-filer.

![Skapa import-anteckningsbok](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Utveckla notebook-filer

Antecknings böcker består av celler, som är enskilda kodblock eller text som kan köras oberoende av varandra eller som en grupp.

### <a name="add-a-cell"></a>Lägg till en cell

Det finns flera sätt att lägga till en ny cell i din bärbara dator.

# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

1. Expandera knappen uppe till vänster **+ cell** och välj **Lägg till kod cell** eller **Lägg till text cell**.

    ![Lägg till cell-med-cell-knapp](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Hovra över utrymmet mellan två celler och välj **Lägg till kod** eller **Lägg till text**.

    ![Lägg till cell-mellan-blank steg](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Använd [kortkommandon under kommando läge](#shortcut-keys-under-command-mode). Tryck på **a** för att infoga en cell ovanför den aktuella cellen. Tryck på **B** för att infoga en cell under den aktuella cellen.


# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

1. Expandera den övre vänstra **+ cell** -knappen och välj sedan **kod cell** eller **markdown cell**.
    ![Lägg till Azure-Notebook-cell-med-cell-knapp](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Välj plus tecknet i början av en cell och välj **tecken cell** eller **markdown cell**.

    ![Lägg till Azure-Notebook-cell-mellan-blank steg](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Använd [aznb-kortkommandon under kommando läge](#shortcut-keys-under-command-mode). Tryck på **a** för att infoga en cell ovanför den aktuella cellen. Tryck på **B** för att infoga en cell under den aktuella cellen.

---

### <a name="set-a-primary-language"></a>Ange ett primärt språk

Bärbara Azure Synapse Studio-datorer stöder fyra Apache Spark språk:

* pySpark (python)
* Spark (Scala)
* SparkSQL
* .NET för Apache Spark (C#)

Du kan ställa in det primära språket för nya tillagda celler i list rutan i det övre kommando fältet.

   ![standard – Synapse – språk](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Använd flera språk

Du kan använda flera språk i en bärbar dator genom att ange rätt språk-Magic-kommando i början av en cell. I följande tabell visas Magic-kommandon för att växla mellan cell språk.

|Magiskt kommando |Språk | Description |  
|---|------|-----|
|%% pyspark| Python | Kör en **python** -fråga mot Spark-kontext.  |
|%% Spark| Scala | Kör en **Scala** -fråga mot Spark-kontext.  |  
|%% SQL| SparkSQL | Kör en **SparkSQL** -fråga mot Spark-kontext.  |
|%% csharp | .NET för Spark C # | Kör en **.net for Spark C#** -fråga mot Spark-kontext. |

Följande bild är ett exempel på hur du kan skriva en PySpark-fråga med kommandot **%% PySpark** Magic eller en SparkSQL-fråga med **%% SQL** Magic-kommandot i en **Spark-anteckningsbok (Scala)** . Observera att det primära språket för antecknings boken är inställt på pySpark.

   ![Synapse Spark Magic-kommandon](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Använd temporära tabeller för att referera till data över olika språk

Du kan inte referera till data eller variabler direkt på olika språk i en Synapse Studio-anteckningsbok. I Spark kan en tillfällig tabell refereras mellan olika språk. Här är ett exempel på hur du läser en `Scala` DataFrame i `PySpark` och `SparkSQL` använder en spark-temporär tabell som en lösning.

1. I cell 1 läser du en DataFrame från en SQL-pool med hjälp av Scala och skapar en temporär tabell.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics("mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. Fråga data i cell 2 med hjälp av Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Använd data i PySpark i cell 3.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-stil, IntelliSense

Bärbara Azure Synapse Studio-datorer är integrerade med Monaco-redigeraren för att kunna använda IntelliSense i IDE-format i cell redigeraren. Syntaxkontroll, fel markör och automatisk kod komplettering hjälper dig att skriva kod och identifiera problem snabbare.

IntelliSense-funktionerna finns på olika förfallo nivåer för olika språk. Använd följande tabell för att se vad som stöds.

|Språk| Syntaxkontroll | Fel markör för syntaxfel  | Komplettering av syntax kod | Slut för ande av variabel kod| Slut för ande av system funktions kod| Slut för ande av användar funktions kod| Smarta indrag | Kod vikning|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Spark (Scala)|Ja|Ja|Ja|Ja|-|-|-|Ja|
|SparkSQL|Ja|Ja|-|-|-|-|-|-|
|.NET för Spark (C#)|Ja|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formatera en text cell med knappar i verktygsfältet

# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

Du kan använda format knapparna i verktygsfältet text celler för att utföra vanliga markdown-åtgärder. Den innehåller fet text, kursivering av text, infoga kodfragment, infoga osorterad lista, infoga sorterad lista och infoga bild från URL.

  ![Synapse text cells verktygsfält](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

Verktygsfältet format knapp är inte tillgängligt för för hands versionen av den bärbara datorn än. 

---

### <a name="undo-cell-operations"></a>Ångra cell åtgärder

# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

Klicka på knappen **Ångra** eller tryck på **CTRL + Z** för att återkalla den senaste cell åtgärden. Nu kan du ångra upp till de senaste 20 historiska cell åtgärderna. 

   ![Synapse ångra celler](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

Åtgärden för att ångra en cell är inte tillgänglig för för hands versionen av Notebook. 

---

### <a name="move-a-cell"></a>Flytta en cell

# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

Välj ellipserna (...) för att få åtkomst till menyn ytterligare cell åtgärder längst till höger. Välj sedan **Flytta cell uppåt** eller **Flytta cell nedåt** för att flytta den aktuella cellen. 

Du kan också använda [kortkommandon under kommando läge](#shortcut-keys-under-command-mode). Tryck på **CTRL + ALT + ↑** för att flytta upp den aktuella cellen. Tryck på **CTRL + ALT + ↓** för att flytta den aktuella cellen nedåt.

   ![flytta – a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

Klicka på den vänstra sidan i en cell och dra den till önskad position. 
    ![Synapse flytta celler](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Ta bort en cell

# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

Om du vill ta bort en cell väljer du ellipserna (...) för att komma åt menyn ytterligare cell åtgärder längst till höger och väljer sedan **ta bort cell**. 

Du kan också använda [kortkommandon under kommando läge](#shortcut-keys-under-command-mode). Tryck på **d, d** för att ta bort den aktuella cellen.
  
   ![ta bort a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

Om du vill ta bort en cell väljer du knappen Ta bort till höger om cellen. 

Du kan också använda [kortkommandon under kommando läge](#shortcut-keys-under-command-mode). Tryck på **SHIFT + D** för att ta bort den aktuella cellen. 

   ![Azure-Notebook-Delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Komprimera en cell Indatatyp

# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

Välj pilknappen längst ned i den aktuella cellen för att dölja den. Om du vill expandera den väljer du pilknappen när cellen är komprimerad.

   ![Komprimera-cell-Indatatyp](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

Välj **fler kommandon** (...) i verktygsfältet cell och **inmatade** för att komprimera den aktuella cellens indatatyper. För att expandera den, väljer du **inmatad dold** när cellen är komprimerad.

   ![Azure-Notebook-dold-cell-indatamängd](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Komprimera cell utdata

# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

Klicka på knappen **Komprimera utdata** längst upp till vänster i den aktuella cellens utdata för att dölja den. För att expandera den, väljer du **Visa cellens utdata** medan cellens utdata är komprimerad.

   ![Komprimera-cell-utdata](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

Välj **fler kommandon** (...) i cell verktygsfältet och **Spara** för att komprimera den aktuella cellens utdata. Om du vill expandera den väljer du samma knapp medan cellens utdata är dold.

   ![Azure-Notebook-komprimera-cell-utdata](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Köra notebook-filer

Du kan köra kod cellerna i din bärbara dator separat eller alla på en gång. Status och förlopp för varje cell representeras i antecknings boken.

### <a name="run-a-cell"></a>Köra en cell

Det finns flera sätt att köra koden i en cell.

1. Hovra över den cell som du vill köra och välj knappen **Kör cell** eller tryck på **CTRL + RETUR**.

   ![Kör-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. Använd [kortkommandon under kommando läge](#shortcut-keys-under-command-mode). Tryck på **SKIFT + RETUR** för att köra den aktuella cellen och markera cellen nedan. Tryck på **Alt + Retur** för att köra den aktuella cellen och infoga en ny cell nedan.

---

### <a name="run-all-cells"></a>Kör alla celler
Välj knappen **Kör alla** för att köra alla celler i den aktuella antecknings boken i följd.

   ![Kör alla – celler](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

### <a name="run-all-cells-above-or-below"></a>Kör alla celler ovanför eller under

Om du vill komma åt menyn ytterligare cell åtgärder längst till höger väljer du ellipserna (**...**). Välj sedan **Kör celler ovan** för att köra alla celler ovanför den aktuella i sekvensen. Välj **Kör celler nedan** om du vill köra alla celler under den aktuella sekvensen.

   ![Run-cellernas över-eller-under](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Avbryt alla celler som körs
Välj knappen **Avbryt alla** om du vill avbryta de celler eller celler som väntar i kön. 
   ![Avbryt – alla celler](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

Avbryt alla celler som körs är inte tillgängliga för för hands versionen av den bärbara datorn ännu. 

---



### <a name="reference-notebook"></a>Referens antecknings bok

# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

Stöds inte.

# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

Du kan använda ```%run <notebook path>``` kommandot Magic för att referera till en annan antecknings bok i den aktuella Notebook-kontexten. Alla variabler som definieras i referens antecknings boken är tillgängliga i den aktuella antecknings boken. ```%run``` kommandot Magic stöder kapslade anrop men har inte stöd för rekursiva anrop. Du får ett undantag om utdrags djupet är större än fem. ```%run``` kommandot har för närvarande endast stöd för att skicka en Notebook-sökväg som parameter. 

Exempel: ``` %run /path/notebookA ```.

---


### <a name="cell-status-indicator"></a>Cell status indikator

En steg-för-steg-cells körnings status visas under cellen för att hjälpa dig att se den aktuella förloppet. När cell körningen är klar visas en körnings översikt med den totala varaktigheten och slut tiden, där det finns en framtida referens.

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Status indikator för Spark

Azure Synapse Studio-anteckningsbok är enbart Spark-baserad. Kod celler körs på poolen utan server Apache Spark. En status indikator för Spark-jobbet visas med en förlopps indikator i real tid som hjälper dig att förstå jobb körnings statusen.
Antalet aktiviteter per jobb eller steg hjälper dig att identifiera parallell nivån för ditt Spark-jobb. Du kan också gå djupare till Spark-ANVÄNDARGRÄNSSNITTET för ett bestämt jobb (eller steg) genom att välja länken på jobbets (eller scenens) namn.


![Spark-Progress-Indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Konfiguration av Spark-session

Du kan ange tids längd, antal och storlek på körningar för att ge den aktuella Spark-sessionen i **Konfigurera session**. Starta om Spark-sessionen för att konfigurations ändringarna ska börja gälla. Alla cachelagrade Notebook-variabler rensas.

[![session-hantering](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Kommandot Spark session config Magic
Du kan också ange Spark-sessionsinställningar via ett magiskt kommando **%% Konfigurera**. Spark-sessionen måste startas om för att inställningen ska fungera. Vi rekommenderar att du kör **%%** i början av din antecknings bok. Här är ett exempel, se https://github.com/cloudera/livy#request-body för en fullständig lista över giltiga parametrar 

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


## <a name="bring-data-to-a-notebook"></a>Hämta data till en bärbar dator

Du kan läsa in data från Azure Blob Storage, Azure Data Lake Store gen 2 och SQL-poolen som visas i kod exemplen nedan.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Läs en CSV-fil från Azure Data Lake Store Gen2 som en spark-DataFrame

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Läs en CSV-fil från Azure Blob Storage som en spark-DataFrame

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

### <a name="read-data-from-the-primary-storage-account"></a>Läs data från det primära lagrings kontot

Du kan komma åt data i det primära lagrings kontot direkt. Du behöver inte ange de hemliga nycklarna. I Datautforskaren högerklickar du på en fil och väljer **ny antecknings** bok för att se en ny antecknings bok med autogenerated dataextractor.

![data till cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Spara antecknings böcker

Du kan spara en enskild bärbar dator eller alla antecknings böcker i din arbets yta.

1. Om du vill spara ändringar som du har gjort i en enskild antecknings bok väljer du knappen **publicera** i kommando fältet för antecknings boken.

   ![publicera – Notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Om du vill spara alla antecknings böcker i arbets ytan väljer du knappen **publicera alla** i arbets ytans kommando fält. 

   ![publicera – alla](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

I egenskaperna för antecknings boken kan du konfigurera om du vill ta med cellens utdata när du sparar.

   ![Notebook – egenskaper](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magic-kommandon
Du kan använda välkända Jupyter Magic-kommandon i Azure Synapse Studio-anteckningsböcker. Granska följande lista som de aktuella tillgängliga Magic-kommandona. Berätta för oss [dina användnings fall på GitHub](https://github.com/MicrosoftDocs/azure-docs/issues/new) så att vi kan fortsätta att bygga upp fler Magic-kommandon för att uppfylla dina behov.

# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

Tillgängliga rad Magic: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeIt](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Tillgängliga cell Magic: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeIt](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% csharp](#use-multiple-languages),[%% Konfigurera](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

Tillgängliga rad Magic: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% timeIt](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [% History](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [% Kör](#reference-notebook), [% load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Tillgängliga cell Magic: [%% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeIt](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% csharp](#use-multiple-languages), [%% HTML](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%% Konfigurera](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Integrera en bärbar dator

### <a name="add-a-notebook-to-a-pipeline"></a>Lägga till en antecknings bok i en pipeline

Välj knappen **Lägg till i** det övre högra hörnet för att lägga till en antecknings bok i en befintlig pipeline eller skapa en ny pipeline.

![Lägg till antecknings bok i pipeline](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Ange en parameter cell

# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

Om du vill Parameterisera din bärbara dator väljer du ellipserna (...) för att få åtkomst till menyn ytterligare cell åtgärder längst till höger. Välj sedan **Växla parameter cell** för att ange cellen som parameter cellen.

![Växla-parameter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

Om du vill Parameterisera din bärbara dator väljer du ellipserna (...) för att få åtkomst till **fler kommandon** i cell verktygsfältet. Välj sedan **Växla parameter cell** för att ange cellen som parameter cellen.

![Azure-Notebook-växlings parameter](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory letar efter parameter cellen och behandlar den här cellen som standard för de parametrar som skickas vid körningen. Körnings motorn lägger till en ny cell under parameter cellen med indataparametrar för att skriva över standardvärdena. När en parameter cell inte anges infogas den inmatade cellen längst upp i antecknings boken.


### <a name="assign-parameters-values-from-a-pipeline"></a>Tilldela parameter värden från en pipeline

När du har skapat en antecknings bok med parametrar kan du köra den från en pipeline med Azure Synapse Notebook-aktiviteten. När du har lagt till aktiviteten till din pipeline-arbetsyta kan du ange parametrar-värden under avsnittet **grundläggande parametrar** på fliken **Inställningar** . 

![Tilldela en parameter](./media/apache-spark-development-using-notebooks/assign-parameter.png)

När du tilldelar parameter värden kan du använda [uttrycks språket](../../data-factory/control-flow-expression-language-functions.md) eller [systemvariablerna](../../data-factory/control-flow-system-variables.md)för pipelinen.



## <a name="shortcut-keys"></a>Kortkommandon

I likhet med Jupyter-anteckningsböcker har Azure Synapse Studio-anteckningsbokar ett spärrat användar gränssnitt. Tangent bordet skiljer sig åt beroende på vilket läge som Notebook-cellen finns i. Bärbara datorer i Synapse Studio har stöd för följande två lägen för en specifik kod cell: kommando läge och redigerings läge.

1. En cell är i kommando läge när ingen text markör visas som anger att du skriver. När en cell är i kommando läge kan du redigera antecknings boken som helhet men inte skriva in enskilda celler. Ange kommando läge genom att trycka `ESC` eller använda musen för att välja utanför cellens redigerings området.

   ![kommando läge](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. Redigerings läget anges av en text markör där du ombeds ange redigerings ytan. När en cell är i redigerings läge kan du skriva i cellen. Ange redigerings läge genom att trycka `Enter` eller använda musen för att välja en cells redigerings yta.
   
   ![redigerings-läge](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Kortkommandon under kommando läge

# <a name="classical-notebook"></a>[Klassisk klassiskt Notebook](#tab/classical)

Med följande kortkommandon kan du enkelt navigera och köra kod i Azure Synapse Notebooks.

| Action |Genvägar till Synapse Studio-anteckningsbok  |
|--|--|
|Kör den aktuella cellen och välj nedan | SKIFT + RETUR |
|Kör den aktuella cellen och infoga den nedan | ALT + RETUR |
|Markera cell ovanför| Upp |
|Markera cell under| Ned |
|Infoga cell ovanför| A |
|Infoga cell under| B |
|Utöka markerade celler ovanför| Skift + upp |
|Utöka markerade celler nedan| Skift + ned|
|Flytta upp cell| Ctrl + Alt + ↑ |
|Flytta cell nedåt| Ctrl + Alt + ↓ |
|Ta bort markerade celler| D, D |
|Växla till redigerings läge| Ange |

# <a name="preview-notebook"></a>[För hands version av Notebook](#tab/preview)

| Action |Genvägar till Synapse Studio-anteckningsbok  |
|--|--|
|Kör den aktuella cellen och välj nedan | SKIFT + RETUR |
|Kör den aktuella cellen och infoga den nedan | ALT + RETUR |
|Kör aktuell cell| Ctrl+Retur |
|Markera cell ovanför| Upp |
|Markera cell under| Ned |
|Markera föregående cell| K |
|Markera nästa cell| J |
|Infoga cell ovanför| A |
|Infoga cell under| B |
|Ta bort markerade celler| Skift + D |
|Växla till redigerings läge| Ange |

---

### <a name="shortcut-keys-under-edit-mode"></a>Kortkommandon under redigerings läge


Med följande kortkommandon kan du enkelt navigera och köra kod i Azure Synapse Notebooks i redigerings läge.

| Action |Genvägar till Synapse Studio-anteckningsbok  |
|--|--|
|Flytta markören uppåt | Upp |
|Flytta markören nedåt|Ned|
|Ångra|Ctrl + Z|
|Gör om|Ctrl + Y|
|Kommentera/ta bort kommentar|CTRL +/|
|Ta bort ord före|CTRL + BACKSTEG|
|Ta bort ord efter|CTRL + Delete|
|Gå till cell start|Ctrl + Home|
|Gå till cell slut |CTRL + END|
|Gå ett ord till vänster|CTRL + vänster|
|Gå ett ord till höger|CTRL + höger|
|Välj alla|CTRL + A|
|Uppgiften| Ctrl +]|
|Dra ut|Ctrl + [|
|Växla till kommando läge| Esc |

---

## <a name="next-steps"></a>Nästa steg
- [Kolla Synapse-exempel Notebooks](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Snabb start: skapa en Apache Spark pool i Azure Synapse Analytics med hjälp av webb verktyg](../quickstart-apache-spark-notebook.md)
- [Vad är Apache Spark i Azure Synapse Analytics](apache-spark-overview.md)
- [Använda .NET för Apache Spark med Azure Synapse Analytics](spark-dotnet.md)
- [Dokumentation om .NET för Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](../index.yml)