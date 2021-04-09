---
title: 'Självstudie: Implementera Data Lake Capture-mönstret för att uppdatera en Azure Databricks delta tabell | Microsoft Docs'
description: Den här självstudien visar hur du använder en Event Grid-prenumeration, en Azure-funktion och ett Azure Databricks-jobb för att infoga data rader i en tabell som lagras i Azure DataLake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.custom: devx-track-csharp
ms.openlocfilehash: f5fa4ad357e937fed7df5be24a1fc78409a0259b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100516404"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Självstudie: Implementera Data Lake Capture-mönstret för att uppdatera en Databricks delta tabell

Den här självstudien visar hur du hanterar händelser i ett lagrings konto som har ett hierarkiskt namn område.

Du skapar en liten lösning som gör att en användare kan fylla i en Databricks delta tabell genom att överföra en fil med kommaavgränsade värden (CSV) som beskriver en försäljnings order. Du skapar den här lösningen genom att ansluta samman en Event Grid prenumeration, en Azure-funktion och ett [jobb](/azure/databricks/jobs) i Azure Databricks.

I de här självstudierna får du:

> [!div class="checklist"]
> * Skapa en Event Grid-prenumeration som anropar en Azure-funktion.
> * Skapa en Azure-funktion som tar emot ett meddelande från en händelse och kör sedan jobbet i Azure Databricks.
> * Skapa ett Databricks-jobb som infogar en kund order i en Databricks delta-tabell som finns i lagrings kontot.

Vi bygger den här lösningen i omvänd ordning, från och med Azure Databricks arbets ytan.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Skapa ett lagrings konto med ett hierarkiskt namn område (Azure Data Lake Storage Gen2). I den här självstudien används ett lagrings konto med namnet `contosoorders` . Se till att ditt användarkonto har tilldelats rollen [Storage Blob Data-deltagare](../common/storage-auth-aad-rbac-portal.md).

   Se [skapa ett lagrings konto som ska användas med Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

* Skapa ett huvudnamn för tjänsten. Se [så här gör du: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md).

  Det finns några saker som du måste göra när du utför stegen i den här artikeln.

  : heavy_check_mark: när du utför stegen i avsnittet [tilldela programmet till en roll](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) i artikeln, se till att tilldela rollen **Storage BLOB data Contributor** till tjänstens huvud namn.

  > [!IMPORTANT]
  > Se till att tilldela rollen i omfånget för Data Lake Storage Gen2-lagringskontot. Du kan tilldela en roll till den överordnade resursgruppen eller prenumerationen, men du får behörighetsrelaterade fel tills de rolltilldelningarna propageras till lagringskontot.

  : heavy_check_mark: när du utför stegen i avsnittet [Hämta värden för signering i](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) artikeln klistrar du in klient-ID, app-ID och lösen ords värden i en textfil. Du behöver dessa värden senare.

## <a name="create-a-sales-order"></a>Skapa en försäljnings order

Skapa först en CSV-fil som beskriver en försäljnings order och överför sedan filen till lagrings kontot. Senare använder du data från den här filen för att fylla den första raden i vår Databricks delta-tabell.

1. Öppna Azure Storage Explorer. Gå sedan till ditt lagrings konto och skapa en ny behållare med namnet **data** i avsnittet **BLOB-behållare** .

   ![data-mapp](./media/data-lake-storage-events/data-container.png "data-mapp")

   Mer information om hur du använder Storage Explorer finns i [använda Azure Storage Explorer för att hantera data i ett Azure Data Lake Storage Gen2-konto](data-lake-storage-explorer.md).

2. Skapa en mapp med namnet **indata** i **data** containern.

3. Klistra in följande text i en text redigerare.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Spara filen på den lokala datorn och ge den namnet **data.csv**.

5. I Storage Explorer laddar du upp den här filen till mappen **indata** .  

## <a name="create-a-job-in-azure-databricks"></a>Skapa ett jobb i Azure Databricks

I det här avsnittet ska du utföra följande uppgifter:

* Skapa en Azure Databricks-arbetsyta.
* Skapa en anteckningsbok.
* Skapa och fyll i en Databricks delta tabell.
* Lägg till kod som infogar rader i Databricks delta-tabellen.
* Skapa ett jobb.

### <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

I det här avsnittet skapar du en Azure Databricks-arbetsyta med Azure-portalen.

1. I Azure Portal väljer du **skapa en resurs**  >  **analys**  >  **Azure Databricks**.

    ![Databricks på Azure Portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks på Azure Portal")

2. Under **Azure Databricks-tjänst** anger du värden för att skapa en Databricks-arbetsyta.

    ![Skapa en Azure Databricks-arbetsyta](./media/data-lake-storage-events/new-databricks-service.png "Skapa en Azure Databricks-arbetsyta")

    Det tar några minuter att skapa arbetsytan. Du kan övervaka åtgärdsstatusen i förloppsindikatorn längst upp.

### <a name="create-a-spark-cluster-in-databricks"></a>Skapa ett Spark-kluster i Databricks

1. I [Azure Portal](https://portal.azure.com)går du till arbets ytan Azure Databricks som du skapade och väljer sedan **Starta arbets yta**.

2. Du omdirigeras till Azure Databricks-portalen. Välj **nytt**  >  **kluster** från portalen.

    ![Databricks på Azure](./media/data-lake-storage-events/databricks-on-azure.png "Databricks på Azure")

3. På sidan **Nytt kluster** anger du värdena för att skapa ett kluster.

    ![Skapa Databricks Spark-kluster på Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Skapa Databricks Spark-kluster på Azure")

    Godkänn alla övriga standardvärden, förutom följande:

    * Ange ett namn för klustret.
    * Se till att markera kryssrutan **Avsluta efter 120 minuters inaktivitet**. Ange en varaktighet (i minuter) för att avsluta klustret om klustret inte används.

4. Välj **skapa kluster**. När klustret körs kan du ansluta anteckningsböcker till klustret och köra Spark-jobb.

Mer information om att skapa kluster finns i [Skapa ett Spark-kluster i Azure Databricks](/azure/databricks/clusters/create).

### <a name="create-a-notebook"></a>Skapa en notebook-fil

1. Välj **Arbetsyta** i det vänstra fönstret. I listrutan **Arbetsyta** väljer du **Skapa** > **Anteckningsbok**.

    ![Skapa antecknings bok i Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Skapa antecknings bok i Databricks")

2. Ge anteckningsboken ett namn i dialogrutan **Skapa anteckningsbok**. Välj **Python** som språk och välj sedan det Spark-kluster du skapade tidigare.

    ![Skärm bild som visar dialog rutan skapa antecknings bok och var du väljer python som språk.](./media/data-lake-storage-events/new-databricks-notebook.png "Skapa antecknings bok i Databricks")

    Välj **Skapa**.

### <a name="create-and-populate-a-databricks-delta-table"></a>Skapa och fylla i en Databricks delta tabell

1. I den antecknings bok som du har skapat kopierar du och klistrar in följande kodblock i den första cellen, men kör inte den här koden ännu.  

   Ersätt `appId` -, `password` , `tenant` placeholder-värden i det här kod blocket med de värden som du samlade in när du slutförde kraven i den här självstudien.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Den här koden skapar en widget med namnet **source_file**. Senare kommer du att skapa en Azure-funktion som anropar den här koden och skickar en fil Sök väg till widgeten.  Den här koden autentiserar också tjänstens huvud namn med lagrings kontot och skapar vissa variabler som du kommer att använda i andra celler.

    > [!NOTE]
    > I en produktionsinställning bör du överväga att lagra din autentiseringsnyckel i Azure Databricks. Sedan lägger du till en lookup-nyckel i kodblocket i stället för autentiseringsnyckeln. <br><br>I stället för att använda den här kodraden: `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")` använder du till exempel följande rad med kod: `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))` . <br><br>När du har slutfört den här kursen kan du se exempel på den här metoden i [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) artikeln på Azure Databricks webbplats.

2. Tryck på **SKIFT + RETUR** för att köra koden i det här blocket.

3. Kopiera och klistra in följande kod block i en annan cell och tryck sedan på **SKIFT + RETUR** för att köra koden i det här blocket.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Den här koden skapar Databricks delta-tabellen i ditt lagrings konto och läser sedan in några inledande data från den CSV-fil som du överförde tidigare.

4. När det här kod blocket har körts tar du bort det här kod blocket från antecknings boken.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Lägg till kod som infogar rader i Databricks delta-tabellen

1. Kopiera och klistra in följande kod block i en annan cell, men kör inte den här cellen.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Den här koden infogar data i en tillfällig tabellvy genom att använda data från en CSV-fil. Sökvägen till CSV-filen kommer från widgeten för indata som du skapade i ett tidigare steg.

2. Lägg till följande kod för att sammanfoga innehållet i den tillfälliga tabellen med Databricks delta-tabellen.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Skapa ett jobb

Skapa ett jobb som kör den antecknings bok som du skapade tidigare. Senare kommer du att skapa en Azure-funktion som kör det här jobbet när en händelse utlöses.

1. Klicka på **jobb**.

2. På sidan **jobb** klickar du på **skapa jobb**.

3. Ge jobbet ett namn och välj sedan `upsert-order-data` arbets boken.

   ![Skapa ett jobb](./media/data-lake-storage-events/create-spark-job.png "Skapa ett jobb")

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Skapa en Azure-funktion som kör jobbet.

1. I det övre hörnet av arbets ytan Databricks väljer du ikonen personer och väljer sedan **användar inställningar**.

   ![Hantera kontot](./media/data-lake-storage-events/generate-token.png "Användarinställningar")

2. Klicka på knappen **generera ny token** och sedan på knappen **generera** .

   Se till att kopiera token till en säker plats. Din Azure-funktion behöver denna token för att autentisera med Databricks så att den kan köra jobbet.
  
3. Välj knappen **skapa en resurs** i det övre vänstra hörnet av Azure Portal och välj sedan **Compute > Funktionsapp**.

   ![Skapa en Azure-funktion](./media/data-lake-storage-events/function-app-create-flow.png "Skapa Azure Function")

4. På sidan **skapa** i Funktionsapp, se till att välja **.net Core** för körnings stacken och se till att konfigurera en Application Insights instans.

   ![Konfigurera funktionsappen](./media/data-lake-storage-events/new-function-app.png "Konfigurera funktionsappen")

5. På sidan **Översikt** i Funktionsapp klickar du på **konfiguration**.

   ![Skärm bild som visar konfigurations alternativet under konfigurerade funktioner.](./media/data-lake-storage-events/configure-function-app.png "Konfigurera funktionsappen")

6. På sidan **program inställningar** väljer du knappen **ny program inställning** för att lägga till varje inställning.

   ![Lägg till konfigurations inställning](./media/data-lake-storage-events/add-application-setting.png "Lägg till konfigurations inställning")

   Lägg till följande inställningar:

   |Inställningsnamn | Värde |
   |----|----|
   |**DBX_INSTANCE**| Regionen för din databricks-arbetsyta. Exempelvis: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| Den personliga åtkomsttoken som du skapade tidigare. |
   |**DBX_JOB_ID**|Identifierare för det jobb som körs. I vårt fall är det här värdet `1` .|
7. På sidan Översikt i Function-appen klickar du på knappen **ny funktion** .

   ![Ny funktion](./media/data-lake-storage-events/new-function.png "Ny funktion")

8. Välj **Azure Event Grid utlösare**.

   Installera tillägget **Microsoft. Azure. WebJobs. Extensions. EventGrid** om du uppmanas att göra det. Om du måste installera den måste du välja **Azure Event Grid trigger** igen för att skapa funktionen.

   Fönstret **ny funktion** visas.

9. I fönstret **ny funktion** namnger du funktionen **UpsertOrder** och klickar sedan på knappen **skapa** .

10. Ersätt innehållet i kod filen med den här koden och klicka sedan på knappen **Spara** :

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Den här koden analyserar information om den lagrings händelse som har Aktiver ATS och skapar sedan ett begär ande meddelande med URL för den fil som utlöste händelsen. Som en del av meddelandet skickar funktionen ett värde till den **source_file** widget som du skapade tidigare. funktions koden skickar meddelandet till Databricks-jobbet och använder den token som du fick tidigare som autentisering.

## <a name="create-an-event-grid-subscription"></a>Skapa en Event Grid-prenumeration

I det här avsnittet ska du skapa en Event Grid-prenumeration som anropar Azure-funktionen när filer överförs till lagrings kontot.

1. På sidan funktions kod klickar du på knappen **Lägg till Event Grid prenumeration** .

   ![Skärm bild som visar knappen Lägg till Event Grid prenumeration.](./media/data-lake-storage-events/new-event-subscription.png "Ny händelse prenumeration")

2. På sidan **Skapa händelse prenumeration** namnger du prenumerationen och använder sedan fälten på sidan för att välja ditt lagrings konto.

   ![Ny händelse prenumeration](./media/data-lake-storage-events/new-event-subscription-2.png "Ny händelse prenumeration")

3. Välj den **blob som skapats** i list rutan **filtrera till händelse typer** och **ta bort BLOB** -händelser och klicka sedan på knappen **skapa** .

## <a name="test-the-event-grid-subscription"></a>Testa Event Grid prenumerationen

1. Skapa en fil med namnet `customer-order.csv` , klistra in följande information i filen och spara den på den lokala datorn.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. I Storage Explorer laddar du upp den här filen till mappen **indata** för ditt lagrings konto.

   Överföring av en fil meddelar händelsen **Microsoft. Storage. BlobCreated** . Event Grid meddelar alla prenumeranter om händelsen. I vårt fall är Azure-funktionen den enda prenumeranten. Azure-funktionen parsar händelse parametrarna för att avgöra vilken händelse som inträffade. Sedan skickas filens URL till Databricks-jobbet. Databricks-jobbet läser filen och lägger till en rad i Databricks delta-tabellen som finns på ditt lagrings konto.

3. Kontrol lera att jobbet har slutförts genom att öppna din databricks-arbetsyta, klicka på **jobb** -knappen och sedan öppna jobbet.

4. Välj jobbet för att öppna jobb sidan.

   ![Spark-jobb](./media/data-lake-storage-events/spark-job.png "Spark-jobb")

   När jobbet har slutförts visas en status för slut för ande.

   ![Jobbet har slutförts](./media/data-lake-storage-events/spark-job-completed.png "Jobbet har slutförts")

5. I en ny arbets boks cell kör du den här frågan i en cell för att se den uppdaterade delta tabellen.

   ```
   %sql select * from customer_data
   ```

   Den returnerade tabellen visar den senaste posten.

   ![Den senaste posten visas i tabellen](./media/data-lake-storage-events/final_query.png "Den senaste posten visas i tabellen")

6. Om du vill uppdatera posten skapar du en fil med namnet `customer-order-update.csv` , klistrar in följande information i filen och sparar den på den lokala datorn.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Den här CSV-filen är nästan identisk med den föregående, förutom antalet order som har ändrats från `228` till `22` .

7. I Storage Explorer laddar du upp den här filen till mappen **indata** för ditt lagrings konto.

8. Kör `select` frågan igen för att visa den uppdaterade delta tabellen.

   ```
   %sql select * from customer_data
   ```

   Den returnerade tabellen visar den uppdaterade posten.

   ![Den uppdaterade posten visas i tabellen](./media/data-lake-storage-events/final_query-2.png "Den uppdaterade posten visas i tabellen")

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen och alla relaterade resurser när de inte längre behövs. Det gör du genom att välja resursgruppen för lagringskontot och sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Reagera på Blob Storage-händelser](storage-blob-event-overview.md)