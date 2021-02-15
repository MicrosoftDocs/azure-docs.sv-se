---
title: Bearbeta data uppsättningar i stor skala med hjälp av Data Factory och batch
description: Beskriver hur du bearbetar stora mängder data i en Azure Data Factory pipelinen med hjälp av parallell bearbetnings funktionen i Azure Batch.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: c59e164d67a665a5e99cb5045b3b0cf38e33dc87
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364281"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Bearbeta data uppsättningar i stor skala med hjälp av Data Factory och batch
> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig. Om du använder den aktuella versionen av tjänsten Data Factory, se [anpassade aktiviteter i Data Factory](../transform-data-using-dotnet-custom-activity.md).

I den här artikeln beskrivs en arkitektur för en exempel lösning som flyttar och bearbetar storskaliga data uppsättningar på ett automatiskt och schemalagt sätt. Den innehåller också en slut punkt till slut punkts genom gång för att implementera lösningen med hjälp av Data Factory och Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här artikeln är längre än en typisk artikel, eftersom den innehåller en genom gång av en hel exempel lösning. Om du är nybörjare på batch och Data Factory kan du lära dig mer om de här tjänsterna och hur de fungerar tillsammans. Om du känner till något om tjänsterna och skapar en lösning kan du fokusera på arkitekturen i artikeln. Om du utvecklar en prototyp eller en lösning kanske du vill prova steg-för-steg-instruktionerna i genom gången. Vi bjuder in dina kommentarer om det här innehållet och hur du använder det.

Först ska vi titta på hur Data Factory och batch Services kan hjälpa dig att bearbeta stora data mängder i molnet.     


## <a name="why-azure-batch"></a>Varför Azure Batch?
 Du kan använda batch för att köra storskaliga parallella program och HPC-program (data behandling med höga prestanda) i molnet. Det är en plattforms tjänst som schemalägger beräknings intensiva arbete som ska köras på en hanterad samling virtuella datorer (VM). Det kan automatiskt skala beräknings resurser för att uppfylla dina jobbs behov.

Med Batch-tjänsten definierar du Azure-beräkningsresurser att köra dina program parallellt och i stor skala. Du kan köra på begäran eller schemalagda jobb. Du behöver inte manuellt skapa, konfigurera och hantera ett HPC-kluster, enskilda virtuella datorer, virtuella nätverk eller en komplex infrastruktur för jobb-och aktivitets schemaläggning.

 Om du inte är bekant med batch kan du använda följande artiklar för att förstå arkitekturen/implementeringen av den lösning som beskrivs i den här artikeln:   

* [Grunderna i batch](../../azure-sql/database/sql-database-paas-overview.md)
* [Översikt över Batch-funktionen](../../batch/batch-service-workflow-features.md)

Du kan också läsa mer om batch i batch- [dokumentationen](../../batch/index.yml).

## <a name="why-azure-data-factory"></a>Varför Azure Data Factory?
Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data. Du kan använda Data Factory för att skapa datapipelines med hanterade data som flyttar data från lokala och molnbaserade data lager till ett centraliserat data lager. Ett exempel är Azure Blob Storage. Du kan använda Data Factory för att bearbeta/transformera data med hjälp av tjänster som Azure HDInsight och Azure Machine Learning. Du kan också schemalägga datapipelines så att de körs på ett schemalagt sätt (till exempel varje timme, varje dag och varje vecka). Du kan övervaka och hantera pipelinen snabbt för att identifiera problem och vidta åtgärder.

  Om du inte är bekant med Data Factory kan du använda följande artiklar för att förstå arkitekturen/implementeringen av den lösning som beskrivs i den här artikeln:  

* [Introduktion till Data Factory](data-factory-introduction.md)
* [Bygg din första data pipeline](data-factory-build-your-first-pipeline.md)   

Du kan också läsa mer om Data Factory i Data Factory- [dokumentationen](/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Data Factory och batch tillsammans
Data Factory innehåller inbyggda aktiviteter. Kopierings aktiviteten används till exempel för att kopiera/flytta data från ett käll data lager till ett mål data lager. Hive-aktiviteten används för att bearbeta data med hjälp av Hadoop-kluster (HDInsight) på Azure. En lista över omvandlings aktiviteter som stöds finns i [data omvandlings aktiviteter](data-factory-data-transformation-activities.md).

Du kan också skapa anpassade .NET-aktiviteter för att flytta eller bearbeta data med din egen logik. Du kan köra dessa aktiviteter i ett HDInsight-kluster eller i en batch-pool med virtuella datorer. När du använder batch kan du konfigurera poolen till autoskalning (lägga till eller ta bort virtuella datorer baserat på arbets belastningen) baserat på en formel som du anger.     

## <a name="architecture-of-a-sample-solution"></a>Arkitektur för en exempel lösning
  Arkitekturen som beskrivs i den här artikeln är för en enkel lösning. Det är också relevant för komplexa scenarier, till exempel risk modellering av finansiella tjänster, bild behandling och åter givning och genomiks analys.

Diagrammet illustrerar hur Data Factory dirigerar data förflyttning och bearbetning. Den visar också hur batch bearbetar data på ett parallellt sätt. Hämta och skriv ut diagrammet för enkel referens (11 × 17 tum eller A3-storlek). För att få åtkomst till diagrammet så att du kan skriva ut det, se [HPC och data Orchestration med batch-och Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagram över data bearbetning i stor skala](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

I följande lista visas de grundläggande stegen i processen. Lösningen innehåller kod och förklaringar för att bygga lösningen från slut punkt till slut punkt.

* **Konfigurera batch med en pool av datornoder (VM).** Du kan ange antalet noder och storleken på varje nod.

* **Skapa en Data Factory instans** som är konfigurerad med entiteter som representerar Blob Storage, batch Compute service, in-/utdata-data och ett arbets flöde/pipeline med aktiviteter som flyttar och transformerar data.

* **Skapa en anpassad .NET-aktivitet i Data Factory pipelinen.** Aktiviteten är din användar kod som körs i batch-poolen.

* **Lagra stora mängder indata som blobbar i Azure Storage.** Data delas upp i logiska sektorer (vanligt vis efter tid).

* **Data Factory kopierar data som bearbetas parallellt** med den sekundära platsen.

* **Data Factory kör den anpassade aktiviteten genom att använda poolen allokerad av batch.** Data Factory kan köra aktiviteter samtidigt. Varje aktivitet bearbetar en data sektor. Resultaten lagras i lagrings utrymmen.

* **Data Factory flyttar de slutliga resultaten till en tredje plats,** antingen för distribution via en app eller för ytterligare bearbetning av andra verktyg.

## <a name="implementation-of-the-sample-solution"></a>Implementering av exempel lösningen
Exempel lösningen är avsiktligt enkel. Den är utformad för att visa hur du använder Data Factory och batch tillsammans för att bearbeta data uppsättningar. Lösningen räknar antalet förekomster av Sök termen "Microsoft" i indatafiler som är ordnade i en tids serie. Den matar sedan ut antalet till utdatafiler.

**Tid:** Om du är van att lära dig grunderna i Azure, Data Factory och batch och har slutfört följande krav, tar den här lösningen en till två timmar att slutföra.

### <a name="prerequisites"></a>Förutsättningar
#### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte har någon Azure-prenumeration kan du snabbt skapa ett kostnads fritt utvärderings konto. Mer information finns i den [kostnads fria utvärderings versionen](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure Storage-konto
Du använder ett lagrings konto för att lagra data i den här självstudien. Om du inte har ett lagrings konto kan du läsa [skapa ett lagrings konto](../../storage/common/storage-account-create.md). Exempel lösningen använder Blob Storage.

#### <a name="azure-batch-account"></a>Azure Batch-konto
Skapa ett batch-konto med hjälp av [Azure Portal](https://portal.azure.com/). Mer information finns i [skapa och hantera ett batch-konto](../../batch/batch-account-create-portal.md). Notera batch-kontots namn och konto nyckeln. Du kan också använda cmdleten [New-AzBatchAccount](/powershell/module/az.batch/new-azbatchaccount) för att skapa ett batch-konto. Instruktioner för hur du använder den här cmdleten finns i [komma igång med PowerShell-cmdlets för batch](../../batch/batch-powershell-cmdlets-get-started.md).

Exempel lösningen använder batch (indirekt via en Data Factory-pipeline) för att bearbeta data på ett parallellt sätt i en pool av datornoder (en hanterad samling virtuella datorer).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch pool av virtuella datorer
Skapa en batch-pool med minst två Compute-noder.

1. I [Azure Portal](https://portal.azure.com)väljer du **Bläddra** i den vänstra menyn och väljer batch- **konton**.

1. Välj ditt batch-konto för att öppna bladet för **batch-kontot** .

1. Välj panelen **pooler** .

1. På bladet **pooler** väljer du knappen **Lägg till** i verktygsfältet för att lägga till en pool.

   a. Ange ett ID för poolen (**pool-ID**). Notera poolens ID. Du behöver den när du skapar data fabriks lösningen.

   b. Ange **Windows Server 2012 R2** som inställning för **operativ system familj** .

   c. Välj en **pris nivå för noden**.

   d. Ange **2** som värde för den **dedikerade mål** inställningen.

   e. Ange **2** som värde för inställningen **Max antal aktiviteter per nod** .

   f. Välj **OK** för att skapa poolen.

#### <a name="azure-storage-explorer"></a>Azure Storage Explorer
Du använder [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) eller [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (från ClumsyLeaf Software) för att granska och ändra data i dina lagrings projekt. Du kan också inspektera och ändra data i loggarna för dina molnbaserade program.

1. Skapa en behållare med **namnet $** med privat åtkomst (ingen anonym åtkomst).

1. Om du använder CloudXplorer kan du skapa mappar och undermappar med följande struktur:

   ![Mappstruktur för mappar och undermappar](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` och `outputfolder` är mappar på den översta nivån i `mycontainer` . `inputfolder`Mappen innehåller undermappar med datum-och tidsstämplar (åååå-mm-dd-HH).

   Om du använder Storage Explorer, i nästa steg, laddar du upp filer med följande namn: `inputfolder/2015-11-16-00/file.txt` , `inputfolder/2015-11-16-01/file.txt` och så vidare. I det här steget skapas automatiskt mapparna.

1. Skapa en textfil **file.txt** på datorn med innehåll som har nyckelordet **Microsoft**. Ett exempel är "testa anpassad aktivitet Microsoft-test anpassad aktivitet Microsoft".

1. Överför filen till följande mappar för indata i Blob Storage:

   ![Mappar för indatamängd](./media/data-factory-data-processing-using-batch/image4.png)

   Om du använder Storage Explorer laddar du upp **file.txt** -filen till den här typen av **behållare**. Välj **Kopiera** i verktygsfältet för att skapa en kopia av blobben. I dialog rutan **Kopiera BLOB** ändrar du **målets BLOB-namn** till `inputfolder/2015-11-16-00/file.txt` . Upprepa det här steget för att skapa,,, `inputfolder/2015-11-16-01/file.txt` `inputfolder/2015-11-16-02/file.txt` `inputfolder/2015-11-16-03/file.txt` `inputfolder/2015-11-16-04/file.txt` och så vidare. Den här åtgärden skapar automatiskt mapparna.

1. Skapa en annan behållare med namnet `customactivitycontainer` . Överför den anpassade aktivitetens zip-fil till den här behållaren.

#### <a name="visual-studio"></a>Visual Studio
Installera Visual Studio 2012 eller senare för att skapa en anpassad batch-aktivitet som ska användas i Data Factory-lösningen.

### <a name="high-level-steps-to-create-the-solution"></a>Steg på hög nivå för att skapa lösningen
1. Skapa en anpassad aktivitet som innehåller logiken för data bearbetning.

1. Skapa en data fabrik som använder den anpassade aktiviteten.

### <a name="create-the-custom-activity"></a>Skapa den anpassade aktiviteten
Data Factory anpassad aktivitet är hjärtat i den här exempel lösningen. Exempel lösningen använder batch för att köra den anpassade aktiviteten. Information om hur du utvecklar anpassade aktiviteter och använder dem i Data Factory-pipeliner finns i [använda anpassade aktiviteter i en Data Factory-pipeline](data-factory-use-custom-activities.md).

Om du vill skapa en anpassad .NET-aktivitet som du kan använda i en Data Factory-pipeline skapar du ett .NET-klass biblioteks projekt med en klass som implementerar IDotNetActivity-gränssnittet. Det här gränssnittet har bara en metod: EXECUTE. Här är signaturen för metoden:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Metoden har några viktiga komponenter som du behöver förstå:

* Metoden kräver fyra parametrar:

  * **linkedServices**. Den här parametern är en enumerable lista över länkade tjänster som länkar indata/utdata-datakällor (till exempel Blob Storage) till data fabriken. I det här exemplet finns det bara en länkad tjänst av typen Azure Storage som används för både indata och utdata.
  * **data uppsättningar**. Den här parametern är en enumerable lista över data uppsättningar. Du kan använda den här parametern för att hämta de platser och scheman som definieras av data uppsättningar för indata och utdata.
  * **aktivitet**. Den här parametern representerar den aktuella Compute-entiteten. I det här fallet är det en batch-tjänst.
  * **loggarna**. Du kan använda loggen för att skriva fel söknings kommentarer som ytan "användare" för pipelinen.
* Metoden returnerar en ord lista som kan användas för att kedja samman anpassade aktiviteter i framtiden. Den här funktionen har inte implementerats ännu, så du kan bara returnera en tom ord lista från-metoden.

#### <a name="procedure-create-the-custom-activity"></a>Procedur: skapa den anpassade aktiviteten
1. Skapa ett .NET-klass biblioteks projekt i Visual Studio.

   a. Starta Visual Studio 2012/2013/2015.

   b. Välj **Arkiv**  >  **nytt**  >  **projekt**.

   c. Expandera **mallar** och välj **Visual C \#**. I den här genom gången använder du C \# , men du kan använda valfritt .net-språk för att utveckla den anpassade aktiviteten.

   d. Välj **klass bibliotek** i listan över projekt typer till höger.

   e. Ange **MyDotNetActivity** som **namn**.

   f. Välj **C: \\ ADF** för **platsen**. Skapa mappen **ADF** om den inte finns.

   ex. Klicka på **OK** för att skapa projektet.

1. Välj **Verktyg** > **NuGet-pakethanteraren** > **Pakethanterarkonsolen**.

1. Kör följande kommando i Package Manager-konsolen för att importera Microsoft. Azure. Management. DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importera **Azure Storage** NuGet-paketet till projektet. Du behöver det här paketet eftersom du använder Blob Storage API i det här exemplet:

    ```powershell
    Install-Package Az.Storage
    ```
1. Lägg till följande med hjälp av direktiv i käll filen i projektet:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. Ändra namn området till **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Ändra namnet på klassen till **MyDotNetActivity** och Härled den från **IDotNetActivity** -gränssnittet som visas:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementera (Lägg till) metoden **execute** för **IDotNetActivity** -gränssnittet i **MyDotNetActivity** -klassen. Kopiera följande exempel kod till-metoden. En förklaring av logiken som används i den här metoden finns i avsnittet [köra metod](#execute-method) .

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {

       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;

       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);

       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;

       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.

       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

       // Initialize the continuation token before using it in the do-while loop.
       BlobContinuationToken continuationToken = null;
       do
       {   // get the list of input blobs from the input storage client object.
           BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                    true,
                                    BlobListingDetails.Metadata,
                                    null,
                                    continuationToken,
                                    null,
                                    null);

           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

       } while (continuationToken != null);

       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

       folderPath = GetFolderPath(outputDataset);

       logger.Write("Writing blob to the folder: {0}", folderPath);

       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);

       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. Lägg till följande hjälp metoder i-klassen. Dessa metoder anropas av metoden **execute** . Viktigast är att metoden **Beräkna** isolerar den kod som itererar genom varje blob.

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    private static string GetFolderPath(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FolderPath;
    }

    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>

    private static string GetFileName(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>

    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
       string output = string.Empty;
       logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
       foreach (IListBlobItem listBlobItem in Bresult.Results)
       {
           CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
           if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
           {
               string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
               logger.Write("input blob text: {0}", blobText);
               string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
               var matchQuery = from word in source
                                where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                select word;
               int wordCount = matchQuery.Count();
               output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
           }
       }
       return output;
    }
    ```
    Metoden GetFolderPath returnerar sökvägen till mappen som data uppsättningen pekar på och GetFileName-metoden returnerar namnet på den BLOB/fil som data uppsättningen pekar på.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Metoden beräkna beräknar antalet instanser av nyckelordet "Microsoft" i indatafilerna (blobbar i mappen). Sök termen "Microsoft" är hårdkodad i koden.

1. Kompilera projektet. Välj **build** på menyn och välj sedan build- **lösning**.

1. Starta Utforskaren och gå till mappen bin- **\\ Felsökning** eller **bin- \\ bin** . Valet av mapp beror på typen av version.

1. Skapa en zip-fil **MyDotNetActivity.zip** som innehåller alla binärfiler i mappen **\\ bin- \\ Felsökning** . Du kanske vill inkludera MyDotNetActivity. **PDB** -fil så att du får ytterligare information, till exempel rad numret i käll koden som orsakade problemet när ett fel uppstår.

   ![Mapplistan bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Ladda upp **MyDotNetActivity.zip** som en blob till BLOB-behållaren `customactivitycontainer` i blob-lagringen som den länkade tjänsten StorageLinkedService i ADFTutorialDataFactory använder. Skapa BLOB-behållaren `customactivitycontainer` om den inte redan finns.

#### <a name="execute-method"></a>Kör metod
Det här avsnittet innehåller mer information om koden i Execute-metoden.

1. Medlemmarna för att iterera genom indatamängden finns i namn området [Microsoft. windowsazure. Storage. blob](/java/api/com.microsoft.azure.storage.blob) . Om du vill iterera genom BLOB-samlingen måste du använda klassen **BlobContinuationToken** . I själva verket måste du använda en loop-loop med token som mekanismen för att avsluta slingan. Mer information finns i [använda Blob Storage från .net](../../storage/blobs/storage-quickstart-blobs-dotnet.md). En grundläggande slinga visas här:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,

                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.

     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

    } while (continuationToken != null);

    ```
   Mer information finns i dokumentationen för [ListBlobsSegmented](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented) -metoden.

1. Koden för att arbeta med en uppsättning blobbar logiskt behålls i slingan-while-slingan. I **execute** -metoden skickar sling-metoden listan över blobbar till en metod med namnet **Beräkna**. Metoden returnerar en String-variabel med namnet **output** som är resultatet av att ha itererat igenom alla blobbar i segmentet.

   Det returnerar antalet förekomster av Sök termen "Microsoft" i bloben som överförts till **beräknings** metoden.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. När **beräknings** metoden är färdig måste den skrivas till en ny blob. För varje uppsättning blobbar som bearbetas kan en ny BLOB skrivas med resultatet. Om du vill skriva till en ny BLOB måste du först hitta data uppsättningen för utdata.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. Koden anropar också hjälp metoden **GetFolderPath** för att hämta mappsökvägen (namnet på lagrings behållaren).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   GetFolderPath-metoden skickar DataSet-objektet till en AzureBlobDataSet som har en egenskap med namnet FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. Koden anropar metoden **GetFileName** för att hämta fil namnet (BLOB-namn). Koden liknar den tidigare koden som användes för att hämta mappsökvägen.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. Namnet på filen skrivs genom att skapa ett URI-objekt. URI-konstruktorn använder egenskapen **BlobEndpoint** för att returnera behållar namnet. Mappsökvägen och fil namnet läggs till för att skapa BLOB-URI: n för utgående data.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. När namnet på filen har skrivits kan du skriva den utgående strängen från metoden **Beräkna** till en ny BLOB:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Skapa data fabriken
I avsnittet [Skapa anpassad aktivitet](#create-the-custom-activity) har du skapat en anpassad aktivitet och överfört zip-filen med binärfiler och PDB-filen till en BLOB-behållare. I det här avsnittet skapar du en data fabrik med en pipeline som använder den anpassade aktiviteten.

Data uppsättningen för den anpassade aktiviteten representerar blobarna (filerna) i indatafilen ( `mycontainer\\inputfolder` ) i Blob Storage. Den utgående data uppsättningen för aktiviteten representerar utgående blobbar i mappen utdata ( `mycontainer\\outputfolder` ) i Blob Storage.

Ta bort en eller flera filer i mapparna för indatafiler:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Du kan till exempel släppa en fil (file.txt) med följande innehåll i var och en av mapparna:

```
test custom activity Microsoft test custom activity Microsoft
```

Varje mapp för indata motsvarar en sektor i data fabriken även om mappen har två eller fler filer. När varje sektor bearbetas av pipelinen upprepas den anpassade aktiviteten genom alla blobbar i Indataporten för den sektorn.

Du ser fem utdatafiler med samma innehåll. Till exempel har utdatafilen från bearbetning av filen i mappen 2015-11-16-00 följande innehåll:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Om du tar bort flera filer (file.txt, file2.txt, file3.txt) med samma innehåll i indatafilen visas följande innehåll i utdatafilen. Varje mapp (2015-11-16-00 osv.) motsvarar en sektor i det här exemplet även om mappen har flera indatafiler.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Utdatafilen har tre rader, en för varje indatafil (BLOB) i den mapp som är associerad med sektorn (2015-11-16-00).

En uppgift skapas för varje aktivitets körning. I det här exemplet finns det bara en aktivitet i pipelinen. När en sektor bearbetas av pipelinen körs den anpassade aktiviteten i batch för att bearbeta sektorn. Eftersom det finns fem sektorer (varje sektor kan ha flera blobbar eller filer) skapas fem uppgifter i batch. När en aktivitet körs i batch är det den anpassade aktivitet som körs.

Följande genom gång innehåller ytterligare information.

#### <a name="step-1-create-the-data-factory"></a>Steg 1: skapa data fabriken
1. När du har loggat in på [Azure Portal](https://portal.azure.com/)utför du följande steg:

   a. Välj **nytt** på den vänstra menyn.

   b. Välj **data och analys** på det **nya** bladet.

   c. Välj **Data Factory** på bladet **data analys** .

1. På bladet **ny data fabrik** anger du **CustomActivityFactory** som namn. Namnet på datafabriken måste vara globalt unikt. Om du får felet "Data Factory Name CustomActivityFactory är inte tillgängligt" ändrar du namnet på data fabriken. Använd till exempel yournameCustomActivityFactory och skapa data fabriken igen.

1. Välj **resurs grupp namn** och välj en befintlig resurs grupp eller skapa en resurs grupp.

1. Kontrol lera att den prenumeration och region där du vill att data fabriken ska skapas är korrekt.

1. Välj **skapa** på bladet **ny data fabrik** .

1. Data fabriken skapas i portalens instrument panel.

1. När data fabriken har skapats visas sidan **data fabrik** , som visar innehållet i data fabriken.

   ![Data Factory-sida](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Steg 2: Skapa länkade tjänster
Länkade tjänster länkar data lager eller beräknings tjänster till en data fabrik. I det här steget länkar du ditt lagrings konto och batch-konto till din data fabrik.

#### <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst
1. Välj panelen **författare och distribution** på bladet **Data Factory** för **CustomActivityFactory**. Data Factory redigeraren visas.

1. Välj **nytt data lager** i kommando fältet och välj **Azure Storage.** JSON-skriptet som du använder för att skapa en länkad lagrings tjänst i redigeraren visas.

   ![Nytt data lager](./media/data-factory-data-processing-using-batch/image7.png)

1. Ersätt **account name** med namnet på ditt lagringskonto. Ersätt **account key** med åtkomstnyckeln för lagringskontot. Information om hur du hämtar din lagrings åtkomst nyckel finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).

1. Välj **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

   ![Distribuera](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Skapa en Azure Batch länkad tjänst
I det här steget skapar du en länkad tjänst för batch-kontot som används för att köra den anpassade aktiviteten för Data Factory.

1. Välj **ny beräkning** i kommando fältet och välj **Azure Batch.** JSON-skriptet som du använder för att skapa en länkad batch-tjänst i redigeraren visas.

1. I JSON-skriptet:

   a. Ersätt **konto namn** med namnet på ditt batch-konto.

   b. Ersätt **åtkomst nyckeln** med åtkomst nyckeln för batch-kontot.

   c. Ange ID för poolen för egenskapen **poolName** . För den här egenskapen kan du ange antingen poolnamn eller pool-ID.

   d. Ange batch-URI för **batchUri** JSON-egenskapen.

      > [!IMPORTANT]
      > URL: en från **Batch-konto** -bladet har följande format: \<accountname\> . \<region\> . batch.azure.com. För egenskapen **batchUri** i JSON-skriptet måste du ta bort A88 "AccountName". * * från URL: en. Ett exempel är `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Bladet för batch-kontot](./media/data-factory-data-processing-using-batch/image9.png)

      För egenskapen **poolName** kan du också ange ID för poolen i stället för namnet på poolen.

      > [!NOTE]
      > Tjänsten Data Factory stöder inte ett alternativ på begäran för batch som det gör för HDInsight. Du kan bara använda din egen batch-pool i en data fabrik.
      >
      >

   e. Ange **StorageLinkedService** för egenskapen **linkedServiceName** . Du skapade den här länkade tjänsten i föregående steg. Den här lagringen används som ett mellanlagringsområde för filer och loggar.

1. Välj **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

#### <a name="step-3-create-datasets"></a>Steg 3: skapa data uppsättningar
I det här steget skapar du data uppsättningar som representerar indata och utdata.

#### <a name="create-the-input-dataset"></a>Skapa indatauppsättningen
1. I redigerings programmet för Data Factory väljer du knappen **ny data uppsättning** i verktygsfältet. Välj **Azure Blob Storage** i den nedrullningsbara listan.

1. Ersätt JSON-skriptet i den högra rutan med följande JSON-kodfragment:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           },
           "external": true,
           "policy": {}
       }
    }
    ```

    Du skapar en pipeline senare i den här genom gången med start tiden 2015-11-16T00:00:00Z och slut tiden 2015-11-16T05:00:00Z. Det är schemalagt att producera data varje timme, så det finns fem indata/utdata-segment (mellan **00**: 00:00- \> **05**: 00:00).

    **Frekvensen** och **intervallet** för indata-datauppsättningen anges till **timme** och **1**, vilket innebär att indata sektorn är tillgänglig varje timme.

    Start tiden för varje sektor representeras av **SliceStart** system-variabel i föregående JSON-kodfragment. Här är start tiderna för varje sektor.

    | **Sektor** | **Start tid**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T **00**: 00:00 |
    | 2         | 2015-11-16T **01**: 00:00 |
    | 3         | 2015-11-16T **02**: 00:00 |
    | 4         | 2015-11-16T **03**: 00:00 |
    | 5         | 2015-11-16T **04**: 00:00 |

    **FolderPath** beräknas med hjälp av året, månaden, dagen och Tim delen av sektor start tiden (**SliceStart**). Så här mappas en inmapp till en sektor.

    | **Sektor** | **Start tid**          | **Mapp för indatamängd**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T **00**: 00:00 | 2015-11-16 –**00** |
    | 2         | 2015-11-16T **01**: 00:00 | 2015-11-16 –**01** |
    | 3         | 2015-11-16T **02**: 00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T **03**: 00:00 | 2015-11-16 –**03** |
    | 5         | 2015-11-16T **04**: 00:00 | 2015-11-16-**04** |

1. Välj **distribuera** i verktygsfältet för att skapa och distribuera **InputDataset** -tabellen.

#### <a name="create-the-output-dataset"></a>Skapa datauppsättningen för utdata
I det här steget skapar du en annan data uppsättning av typen AzureBlob för att representera utdata.

1. I redigerings programmet för Data Factory väljer du knappen **ny data uppsättning** i verktygsfältet. Välj **Azure Blob Storage** i den nedrullningsbara listan.

1. Ersätt JSON-skriptet i den högra rutan med följande JSON-kodfragment:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
               "partitionedBy": [
                   {
                       "name": "slice",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy-MM-dd-HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           }
       }
    }
    ```

    En utgående BLOB/fil genereras för varje ingående sektor. Här är hur en utdatafil namnges för varje sektor. Alla utdatafiler genereras i en mapp för utdata `mycontainer\\outputfolder` .

    | **Sektor** | **Start tid**          | **Utdatafil**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T **00**: 00:00 | 2015-11-16 –**00.txt** |
    | 2         | 2015-11-16T **01**: 00:00 | 2015-11-16 –**01.txt** |
    | 3         | 2015-11-16T **02**: 00:00 | 2015-11-16 –**02.txt** |
    | 4         | 2015-11-16T **03**: 00:00 | 2015-11-16 –**03.txt** |
    | 5         | 2015-11-16T **04**: 00:00 | 2015-11-16 –**04.txt** |

    Kom ihåg att alla filer i en mapp (till exempel 2015-11-16-00) ingår i en sektor med start tiden 2015-11-16-00. När den här sektorn bearbetas genomsöker den anpassade aktiviteten igenom varje fil och skapar en rad i utdatafilen med antalet förekomster av Sök termen "Microsoft". Om det finns tre filer i mappen 2015-11-16-00 finns det tre rader i utdatafilen 2015-11-16-00.txt.

1. Välj **distribuera** i verktygsfältet för att skapa och distribuera **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Steg 4: skapa och kör pipelinen med en anpassad aktivitet
I det här steget skapar du en pipeline med en aktivitet, den anpassade aktivitet som du skapade tidigare.

> [!IMPORTANT]
> Om du inte har laddat upp **file.txt** för in-mappar i BLOB-behållaren gör du det innan du skapar pipelinen. **IsPaused** -egenskapen har angetts till false i pipeline-JSON, så att pipelinen körs omedelbart eftersom **Start** datumet redan har passerat.
>
>

1. I redigerings programmet för Data Factory väljer du **ny pipeline** i kommando fältet. Om du inte ser kommandot väljer du ellipsknappen för att visa det.

1. Ersätt JSON-skriptet i den högra rutan med följande JSON-kodfragment:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Observera följande punkter:

   * Endast en aktivitet finns i pipelinen och är av typen **DotNetActivity**.
   * **AssemblyName** har angetts till namnet på DLL- **MyDotNetActivity.dll**.
   * **EntryPoint** är inställd på **MyDotNetActivityNS. MyDotNetActivity**. Det är i princip \<namespace\> .\<classname\> i din kod.
   * **PackageLinkedService** är inställt på **StorageLinkedService**, som pekar på blob-lagringen som innehåller den anpassade aktivitetens zip-fil. Om du använder olika lagrings konton för in-och utdatafiler och den anpassade aktivitetens zip-fil måste du skapa en annan länkad lagrings tjänst. I den här artikeln förutsätter vi att du använder samma lagrings konto.
   * **PackageFile** har angetts till **customactivitycontainer/MyDotNetActivity.zip**. Formatet är i formatet \<containerforthezip\> / \<nameofthezip.zip\> .
   * Den anpassade aktiviteten tar **InputDataset** som indata och **OutputDataset** som utdata.
   * Egenskapen **linkedServiceName** för den anpassade aktiviteten pekar på **AzureBatchLinkedService**, som talar om för Data Factory att den anpassade aktiviteten måste köras i batch.
   * **Samtidighets** inställningen är viktig. Om du använder standardvärdet, vilket är 1, även om du har två eller fler Compute-noder i batch-poolen bearbetas sektorerna ett efter ett annat. Därför drar du inte nytta av den parallella bearbetnings funktionen i batch. Om du ställer in **samtidighet** till ett högre värde innebär det att två sektorer (motsvarar två aktiviteter i batch) kan bearbetas på samma gång. I det här fallet används båda de virtuella datorerna i batch-poolen. Ställ in samtidighets egenskapen på rätt sätt.
   * Endast en aktivitet (sektor) körs på en virtuell dator när som helst som standard. Som standard har **maximalt antal aktiviteter per virtuell dator** angetts till 1 för en batch-pool. Som en del av kraven har du skapat en pool med den här egenskapen inställd på 2. Därför kan två data fabriks segment köras på en virtuell dator på samma tidpunkt.
     - Egenskapen **isPaused** har angetts till false som standard. Pipelinen körs omedelbart i det här exemplet eftersom sektorerna börjar i det förflutna. Du kan ställa in den här egenskapen på **True** för att pausa pipelinen och återställa den till **false** för att starta om.
     -   **Start** -och **slut** tiderna är fem timmar isär. Sektorerna skapas per timme, så fem sektorer skapas av pipelinen.

1. Välj **Distribuera** i kommandofältet för att distribuera pipelinen.

#### <a name="step-5-test-the-pipeline"></a>Steg 5: testa pipelinen
I det här steget ska du testa pipelinen genom att släppa filer i inmapparna. Börja med att testa pipelinen med en fil för varje mapp för indata.

1. På bladet **data fabrik** i Azure Portal väljer du **diagram**.

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

1. I **diagramvyn** dubbelklickar du på data uppsättningen **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. Bladet **InputDataset** visas med alla fem segmenten klara. Lägg märke till **sektorens start tid** och **sektor slut tiden** för varje sektor.

   ![Start-och slut tider för ingående sektor](./media/data-factory-data-processing-using-batch/image12.png)

1. I **diagramvyn** väljer du **OutputDataset**.

1. De fem utmatnings sektorerna visas i **klart** läge om de producerades.

   ![Start-och slut tider för utgående sektor](./media/data-factory-data-processing-using-batch/image13.png)

1. Använd portalen för att visa de uppgifter som är associerade med sektorerna och se vilken virtuell dator varje sektor kördes på. Mer information finns i avsnittet [Data Factory och batch-integrering](#data-factory-and-batch-integration) .

1. Utdatafilerna visas under `mycontainer` i i `outputfolder` Blob Storage.

   ![Utdatafiler i lagring](./media/data-factory-data-processing-using-batch/image15.png)

   Fem utdatafiler visas, en för varje ingående sektor. Var och en av utdatafilerna har innehåll som liknar följande utdata:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Följande diagram illustrerar hur Data Factory-segmenten mappas till aktiviteter i batch. I det här exemplet har en sektor bara en körning.

   ![Diagram över segment mappning](./media/data-factory-data-processing-using-batch/image16.png)

1. Prova nu med flera filer i en mapp. Skapa filerna **file2.txt**, **file3.txt**, **file4.txt** och **file5.txt** med samma innehåll som i file.txt i mappen **2015-11-06-01**.

1. I mappen utdata tar du bort utdatafilen **2015-11-16-01.txt**.

1. På bladet **OutputDataset** högerklickar du på sektorn med **sektor start tid** inställd på **11/16/2015 01:00:00**. Välj **Kör** för att köra om eller ombearbeta sektorn. Sektorn har nu fem filer i stället för en fil.

    ![Kör](./media/data-factory-data-processing-using-batch/image17.png)

1. När sektorn har körts och statusen är **klar** kontrollerar du innehållet i utdatafilen för den här sektorn (**2015-11-16-01.txt**). Utdatafilen visas under `mycontainer` i i `outputfolder` Blob Storage. Det ska finnas en rad för varje fil i sektorn.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Om du inte har tagit bort utdatafilen 2015-11-16-01.txt innan du försökte med fem indatafiler, kan du se en rad från den föregående sektor körningen och fem rader från den aktuella sektor körningen. Som standard läggs innehållet till i utdatafilen om det redan finns.
>
>

#### <a name="data-factory-and-batch-integration"></a>Data Factory-och batch-integrering
Data Factorys tjänsten skapar ett jobb i batch med namnet `adf-poolname:job-xxx` .

![Batchjobb](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

En uppgift i jobbet skapas för varje aktivitets körning i en sektor. Om 10 sektorer är redo att bearbetas skapas 10 aktiviteter i jobbet. Du kan ha mer än en sektor som körs parallellt om du har flera Compute-noder i poolen. Om det maximala antalet aktiviteter per datornod är inställt på större än ett, kan mer än en sektor köras i samma beräkning.

I det här exemplet finns det fem segment, så det finns fem uppgifter i batch. Med **samtidighet** inställt på **5** i pipeline-JSON i data fabriken och **maximalt antal aktiviteter per virtuell dator** inställd på **2** i batch-poolen med **2** virtuella datorer körs aktiviteterna snabbt. (Kontrol lera start-och slut tiderna för aktiviteter.)

Använd portalen för att Visa batch-jobbet och de uppgifter som är associerade med sektorerna och se vilken virtuell dator varje sektor kördes på.

![Batch-jobb-aktiviteter](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Felsöka pipeline
Fel sökning består av några grundläggande tekniker.

1. Om Indataporten inte är inställd på **klar**, kontrollerar du att strukturen för inmappen är korrekt och att file.txt finns i inmapparna.

   ![Struktur för inmatade mappar](./media/data-factory-data-processing-using-batch/image3.png)

1. I **execute** -metoden för din anpassade aktivitet använder du **IActivityLogger** -objektet för att logga information som hjälper dig att felsöka problem. De loggade meddelandena visas i filen användare \_ 0. log.

   På bladet **OutputDataset** väljer du sektorn för att se bladet **data sektor** för den sektorn. Under **aktivitets körningar** visas en aktivitets körning för sektorn. Om du väljer **Kör** i kommando fältet kan du starta en annan aktivitets körning för samma sektor.

   När du väljer aktivitets körningen visas bladet **aktivitets körnings information** med en lista över loggfiler. Du ser loggade meddelanden i \_ filen användare 0. log. När ett fel inträffar visas tre aktivitets körningar eftersom antalet återförsök har angetts till 3 i pipeline/Activity-JSON. När du väljer aktivitets körningen visas de loggfiler som du kan granska för att felsöka felet.

   ![Blad för OutputDataset och data sektor](./media/data-factory-data-processing-using-batch/image18.png)

   I listan över loggfiler väljer du **User-0. log**. I den högra panelen visas resultatet av att använda metoden **IActivityLogger. Write** .

   ![Bladet körnings information för aktivitet](./media/data-factory-data-processing-using-batch/image19.png)

   Kontrol lera system-0. log för eventuella systemfel meddelanden och undantag.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Inkludera **PDB** -filen i zip-filen så att fel informationen innehåller information som anrops stack när ett fel uppstår.

1. Alla filer i zip-filen för den anpassade aktiviteten måste finnas på den översta nivån utan undermappar.

   ![Lista över zip-filer för anpassad aktivitet](./media/data-factory-data-processing-using-batch/image20.png)

1. Se till att **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS. MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) och **packageLinkedService** (ska peka på blob-lagringen som innehåller zip-filen) har angetts till rätt värden.

1. Om du har åtgärdat ett fel och vill bearbeta om sektorn högerklickar du på sektorn i bladet **OutputDataset** och väljer **Kör**.

   ![OutputDataset-bladets körnings alternativ](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > En behållare finns i blob-lagringen med namnet `adfjobs` . Den här behållaren tas inte bort automatiskt, men du kan ta bort den på ett säkert sätt när du har testat lösningen. På samma sätt skapar Data Factory-lösningen ett batch-jobb med namnet `adf-\<pool ID/name\>:job-0000000001` . Du kan ta bort det här jobbet när du har testat lösningen om du vill.
   >
   >
1. Den anpassade aktiviteten använder inte **app.config** -filen från ditt paket. Om din kod läser eventuella anslutnings strängar från konfigurations filen fungerar det därför inte vid körning. Den bästa metoden när du använder batch är att lagra alla hemligheter i Azure Key Vault. Använd sedan ett certifikat baserat tjänstens huvud namn för att skydda nyckel valvet och distribuera certifikatet till batch-poolen. Den anpassade .NET-aktiviteten kan komma åt hemligheter från nyckel valvet vid körning. Denna generiska lösning kan skalas till vilken typ av hemlighet som helst, inte bara en anslutnings sträng.

    Det finns en enklare lösning, men det är inte en bra metod. Du kan skapa en SQL Database länkad tjänst med inställningar för anslutnings strängen. Sedan kan du skapa en data uppsättning som använder den länkade tjänsten och kedja data uppsättningen som en exempel data data uppsättning till den anpassade .NET-aktiviteten. Du kan sedan komma åt den länkade tjänstens anslutnings sträng i den anpassade aktivitets koden. Den bör fungera bra vid körning.  

#### <a name="extend-the-sample"></a>Utöka exemplet
Du kan utöka det här exemplet för att lära dig mer om Data Factory-och batch-funktioner. För att till exempel bearbeta sektorer i ett annat tidsintervall, utför följande steg:

1. Lägg till följande undermappar i `inputfolder` : 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 och 2015-11-16-09. Placera indatafilerna i dessa mappar. Ändra slut tiden för pipelinen från `2015-11-16T05:00:00Z` till `2015-11-16T10:00:00Z` . I **diagramvyn** dubbelklickar du på **InputDataset** och bekräftar att de angivna segmenten är klara. Dubbelklicka på **OutputDataset** för att se statusen för de utgående segmenten. Om de har statusen **klar** kontrollerar du mappen utdata för utdatafilerna.

1. Öka eller minska **samtidighets** inställningen för att förstå hur den påverkar lösningens prestanda, särskilt bearbetningen i batch. Mer information om **samtidiga** inställningar finns i "steg 4: skapa och köra pipelinen med en anpassad aktivitet".

1. Skapa en pool med högre/lägre **maximala uppgifter per virtuell dator**. Om du vill använda den nya poolen som du skapade uppdaterar du den länkade batch-tjänsten i Data Factory-lösningen. Mer information om **högsta antal aktiviteter per VM** -inställning finns i "steg 4: skapa och köra pipelinen med en anpassad aktivitet".

1. Skapa en batch-pool med funktionen för **autoskalning** . Automatisk skalning av Compute-noder i en batch-pool är den dynamiska justeringen av bearbetnings kraft som används av ditt program.

    Exempel formeln här uppnår följande beteende. När poolen skapas första gången börjar den med en virtuell dator. $PendingTasks måttet definierar antalet aktiviteter i tillståndet som körs och är aktiva (i kö). Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och anger TargetDedicated i enlighet med detta. Det garanterar att TargetDedicated aldrig hamnar bortom 25 virtuella datorer. När nya uppgifter skickas växer automatiskt poolen. När aktiviteterna har slutförts blir de virtuella datorerna en i taget och den automatiska skalningen minskar de virtuella datorerna. Du kan anpassa startingNumberOfVMs och maxNumberofVMs efter behov.

    Autoskalning-formel:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Mer information finns i [automatisk skalning av Compute-noder i en batch-pool](../../batch/batch-automatic-scaling.md).

   Om poolen använder standard- [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)kan batch-tjänsten ta 15 till 30 minuter att förbereda den virtuella datorn innan du kör den anpassade aktiviteten. Om poolen använder en annan autoScaleEvaluationInterval kan batch-tjänsten ta autoScaleEvaluationInterval plus 10 minuter.

1. I exempel lösningen anropar metoden **execute** den **beräknings** metod som bearbetar en indata-sektor för att skapa en utgående data sektor. Du kan skriva en egen metod för att bearbeta indata och ersätta metoden **Beräkna** Metod anrop i metoden **execute** med ett anrop till metoden.

### <a name="next-steps-consume-the-data"></a>Nästa steg: använda data
När du har bearbetat data kan du använda det med online-verktyg som Power BI. Här följer länkar som hjälper dig att förstå Power BI och hur du använder det i Azure:

* [Utforska en data uppsättning i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Kom igång med Power BI Desktop](/power-bi/fundamentals/desktop-getting-started)
* [Uppdatera data i Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure och Power BI: grundläggande översikt](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referenser
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introduktion till Data Factory tjänsten](data-factory-introduction.md)
  * [Kom igång med Data Factory](data-factory-build-your-first-pipeline.md)
  * [Använd anpassade aktiviteter i en Data Factory pipeline](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Grunderna i batch](../../azure-sql/database/sql-database-paas-overview.md)
  * [Översikt över batch-funktioner](../../batch/batch-service-workflow-features.md)
  * [Skapa och hantera ett batch-konto i Azure Portal](../../batch/batch-account-create-portal.md)
  * [Kom igång med batch-klient biblioteket för .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: /archive/blogs/windowshpc/azure-batch-explorer-sample-walkthrough