---
title: 'Självstudie: Azure Data Lake Storage Gen2, Azure Databricks & Spark | Microsoft Docs'
description: Den här självstudien visar hur du kör Spark-frågor på ett Azure Databricks-kluster för att komma åt data i ett Azure Data Lake Storage Gen2 lagrings konto.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.custom: devx-track-python
ms.openlocfilehash: 232e28d3cc8b0bc7427dd035d51743f623e54259
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564311"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>Självstudie: Azure Data Lake Storage Gen2, Azure Databricks & Spark

Den här självstudien visar hur du ansluter ditt Azure Databricks-kluster till data som lagras i ett Azure-lagringskonto som har Azure Data Lake Storage Gen2 aktiverat. Med den här anslutningen kan du internt köra frågor och analyser från klustret på dina data.

I de här självstudierna får du:

> [!div class="checklist"]
> * Skapa ett Databricks-kluster
> * Mata in ostrukturerade data i ett lagringskonto
> * Köra analyser på data i Blob Storage

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Skapa ett Azure Data Lake Storage Gen2-konto.

  Se [skapa ett lagrings konto som ska användas med Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

* Se till att ditt användarkonto har tilldelats rollen [Storage Blob Data-deltagare](../common/storage-auth-aad-rbac-portal.md).

* Installera AzCopy v10. Läs mer i [Överföra data med AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Skapa ett huvudnamn för tjänsten. Se [så här gör du: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md).

  Det finns några saker som du måste göra när du utför stegen i den här artikeln.

  : heavy_check_mark: när du utför stegen i avsnittet [tilldela programmet till en roll](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) i artikeln, se till att tilldela rollen **Storage BLOB data Contributor** till tjänstens huvud namn.

  > [!IMPORTANT]
  > Se till att tilldela rollen i omfånget för Data Lake Storage Gen2-lagringskontot. Du kan tilldela en roll till den överordnade resursgruppen eller prenumerationen, men du får behörighetsrelaterade fel tills de rolltilldelningarna propageras till lagringskontot.

  : heavy_check_mark: när du utför stegen i avsnittet [Hämta värden för signering i](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) artikeln klistrar du in klient-ID, app-ID och klientens hemliga värden i en textfil. Du kommer att behöva dem snart.

### <a name="download-the-flight-data"></a>Ladda ned flygdata

I den här självstudien används flygdata från Bureau of Transportation Statistics för att demonstrera hur du utför en ETL-åtgärd. Du måste hämta dessa data för att kunna gå självstudien.

1. Gå till [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?gnoyr_VQ=FGJ).

2. Markera kryssrutan **Prezipped file** (Förkomprimerad fil) för att markera alla datafält.

3. Klicka på knappen **Ladda ned** och spara resultaten lokalt. 

4. Packa upp innehållet i den komprimerade filen och anteckna filnamnet och sökvägen. Du behöver den här informationen i ett senare steg.

## <a name="create-an-azure-databricks-service"></a>Skapa en Azure Databricks-tjänst

I det här avsnittet skapar du en Azure Databricks-tjänst i Azure Portal.

1. I Azure Portal väljer du **skapa en resurs**  >  **analys**  >  **Azure Databricks**.

    ![Databricks på Azure Portal](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks på Azure Portal")

2. Ange följande värden under **Azure Databricks-tjänst** för att skapa en Databricks-tjänst:

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |**Namn på arbetsyta**     | Ange ett namn för Databricks-arbetsytan.  |
    |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
    |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../../azure-resource-manager/management/overview.md). |
    |**Plats**     | Välj **USA, västra 2**. För andra tillgängliga regioner läser du informationen om [Azure-tjänsttillgänglighet per region](https://azure.microsoft.com/regions/services/).       |
    |**Prisnivå**     |  Välj **standard**.     |

    ![Skapa en Azure Databricks-arbetsyta](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Skapa en Azure Databricks-tjänst")

3. Det tar några minuter att skapa kontot. Du kan övervaka åtgärdsstatusen i förloppsindikatorn längst upp.

4. Välj **Fäst på instrumentpanelen** och välj sedan **Skapa**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Skapa ett Spark-kluster i Azure Databricks

1. Gå till Databricks-tjänsten du skapade i Azure Portal och välj **Starta arbetsyta**.

2. Du omdirigeras till Azure Databricks-portalen. I portalen väljer du **Kluster**.

    ![Databricks på Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

    Fyll i värden för följande fält och godkänn standardvärdena för de andra fälten:

    - Ange ett namn för klustret.
     
    - Se till att markera kryssrutan **Avsluta efter 120 minuters inaktivitet**. Ange en varaktighet (i minuter) för att avsluta klustret om klustret inte används.

4. Välj **skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

## <a name="ingest-data"></a>Mata in data

### <a name="copy-source-data-into-the-storage-account"></a>Kopiera källdata till lagringskontot

Använd AzCopy till att kopiera data från *.csv*-filen till Data Lake Storage Gen2-kontot.

1. Öppna en kommandotolk och ange följande kommandon för att logga in på lagringskontot.

   ```bash
   azcopy login
   ```

   Följ anvisningarna som visas i kommando tolkens fönster för att autentisera ditt användar konto.

2. Kopiera data från *.csv*-filen med följande kommando.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Ersätt `<csv-folder-path>` placeholder-värdet med sökvägen till *. csv* -filen.

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

   * Ersätt `<container-name>` plats hållaren med namnet på en behållare i ditt lagrings konto.

## <a name="create-a-container-and-mount-it"></a>Skapa en behållare och montera den

I det här avsnittet ska du skapa en behållare och en mapp i ditt lagrings konto.

1. Gå till Azure Databricks-tjänsten du skapade i [Azure Portal](https://portal.azure.com) och välj **Starta arbetsyta**.

2. Välj **Arbetsyta** till vänster. I listrutan **Arbetsyta** väljer du **Skapa** > **Anteckningsbok**.

    ![Skapa en antecknings bok i Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Skapa antecknings bok i Databricks")

3. Ge anteckningsboken ett namn i dialogrutan **Skapa anteckningsbok**. Välj **Python** som språk och välj sedan det Spark-kluster du skapade tidigare.

4. Välj **Skapa**.

5. Kopiera och klistra in följande kodblock i den första cellen, men kör inte den här koden än.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<clientSecret>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. I det här kodblocket ersätter du platshållarvärdena `appId`, `clientSecret`, `tenant` och `storage-account-name` i det här kodblocket med de värden som du hämtade när du slutförde förutsättningarna för den här självstudien. Ersätt `container-name` placeholder-värdet med namnet på behållaren.

19. Tryck på **SKIFT + RETUR** för att köra koden i det här blocket.

   Lämna den här anteckningsboken öppen eftersom du ska lägga till kommandon i den senare.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Använda Databricks-anteckningsboken för att konvertera CSV till Parquet

Lägg till en ny cell i anteckningsboken du skapade tidigare och klistra in följande kod i cellen. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Utforska data

Klistra in följande kod i en ny cell för att få en lista med CSV-filer som laddats upp via AzCopy.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Kör det här skriptet om du vill skapa en ny fil och visa en lista med filerna i mappen *parquet/flights*:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Med dessa kodexempel har du utforskat den hierarkiska strukturen för HDFS med hjälp av data som lagras i ett lagringskonto med Azure Data Lake Storage Gen2 aktiverat.

## <a name="query-the-data"></a>Fråga efter data

Därefter kan du börja fråga efter de data du har laddat upp till ditt lagringskonto. Ange vart och ett av följande kodblock i **Cmd 1** och tryck på **Cmd + Retur** för att köra Python-skriptet.

Skapa dataramar för dina datakällor med följande skript:

* Ersätt `<csv-folder-path>` placeholder-värdet med sökvägen till *. csv* -filen.

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

Med det här skriptet kör du några grundläggande analysfrågor mot data.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen och alla relaterade resurser när de inte längre behövs. Det gör du genom att välja resursgruppen för lagringskontot och sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Extrahera, transformera och läsa in data med Apache Hive på Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
