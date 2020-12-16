---
title: Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)
description: Lär dig hur du skapar anpassade aktiviteter och använder dem i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-csharp
manager: anandsub
robots: noindex
ms.openlocfilehash: 0ef6c97f7924c890bb6665100259970372f1cd26
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606954"
---
# <a name="use-custom-activities-in-an-azure-data-factory-version-1-pipeline"></a>Använd anpassade aktiviteter i en Azure Data Factory version 1-pipeline
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-use-custom-activities.md)
> * [Version 2 (aktuell version)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [anpassade aktiviteter i v2](../transform-data-using-dotnet-custom-activity.md).

Det finns två typer av aktiviteter som du kan använda i en Azure Data Factory pipeline.

- [Data förflyttnings aktiviteter](data-factory-data-movement-activities.md) för att flytta data mellan [käll-och mottagar data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Data omvandlings aktiviteter](data-factory-data-transformation-activities.md) för att transformera data med hjälp av beräknings tjänster som Azure HDInsight, Azure Batch och Azure Machine Learning Studio (klassisk).

Om du vill flytta data till/från ett data lager som Data Factory inte stöder, skapar du en **anpassad aktivitet** med din egen data förflyttnings logik och använder aktiviteten i en pipeline. På samma sätt kan du skapa en anpassad aktivitet med din egen data omvandlings logik och använda aktiviteten i en pipeline för att transformera/bearbeta data på ett sätt som inte stöds av Data Factory.

Du kan konfigurera en anpassad aktivitet så att den körs på en **Azure Batch** pool av virtuella datorer. När du använder Azure Batch kan du använda en befintlig Azure Batch-pool.

Följande genom gång innehåller stegvisa instruktioner för hur du skapar en anpassad .NET-aktivitet och använder den anpassade aktiviteten i en pipeline. I genom gången används en **Azure Batch** länkad tjänst.

> [!IMPORTANT]
> - Det går inte att använda en Data Management Gateway från en anpassad aktivitet för att komma åt lokala data källor. För närvarande stöder [Data Management Gateway](data-factory-data-management-gateway.md) endast aktiviteten Kopiera aktivitet och lagrad procedur i Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Genom gång: skapa en anpassad aktivitet
### <a name="prerequisites"></a>Krav
* Visual Studio 2012/2013/2015/2017
* Ladda ned och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Azure Batch förutsättningar
I genom gången kör du dina anpassade .NET-aktiviteter med Azure Batch som en beräknings resurs. **Azure Batch** är en plattformstjänst för effektiv körning av storskaliga parallella program och HPC-program (databehandling med höga prestanda) i molnet. Azure Batch schemalägger beräknings intensiva arbete som ska köras på en hanterad **samling virtuella datorer** och kan automatiskt skala beräknings resurser för att uppfylla jobbens behov. En detaljerad översikt över tjänsten Azure Batch finns i artikeln [Azure Batch grundläggande][batch-technical-overview] information.

För självstudien skapar du ett Azure Batch-konto med en pool med virtuella datorer. Gör så här:

1. Skapa ett **Azure Batch konto** med hjälp av [Azure Portal](https://portal.azure.com). Instruktioner finns i artikeln [skapa och hantera en Azure Batch konto][batch-create-account] .
2. Anteckna Azure Batch konto namn, konto nyckel, URI och poolnamn. Du behöver dem för att skapa en Azure Batch länkad tjänst.
    1. På Start sidan för Azure Batch konto visas en **URL** i följande format: `https://myaccount.westus.batch.azure.com` . I det här exemplet är ditt **konto** namnet på Azure Batch kontot. URI som du använder i den länkade tjänst definitionen är URL: en utan namnet på kontot. Till exempel: `https://<region>.batch.azure.com`.
    2. Klicka på **nycklar** i den vänstra menyn och kopiera den **primära åtkomst nyckeln**.
    3. Om du vill använda en befintlig pool klickar du på **pooler** på menyn och noterar **ID** för poolen. Om du inte har en befintlig pool går du vidare till nästa steg.
2. Skapa en **Azure Batch pool**.

   1. I [Azure Portal](https://portal.azure.com)klickar du på **Bläddra** i den vänstra menyn och sedan på **batch-konton**.
   2. Välj ditt Azure Batch konto för att öppna bladet för **batch-kontot** .
   3. Klicka på **pooler** -panelen.
   4. På bladet **pooler** klickar du på knappen Lägg till i verktygsfältet för att lägga till en pool.
      1. Ange ett ID för poolen (pool-ID). Notera **poolens ID**. du behöver den när du skapar Data Factory-lösningen.
      2. Ange **Windows Server 2012 R2** som inställning för operativ system familj.
      3. Välj en **pris nivå för noden**.
      4. Ange **2** som värde för den **dedikerade mål** inställningen.
      5. Ange **2** som värde för inställningen **Max antal aktiviteter per nod** .
   5. Klicka på **OK** för att skapa poolen.
   6. Anteckna **ID** för poolen.

### <a name="high-level-steps"></a>Övergripande steg
Här följer två viktiga steg som du utför som en del av den här genom gången:

1. Skapa en anpassad aktivitet som innehåller enkel data omvandling/bearbetnings logik.
2. Skapa en Azure-datafabrik med en pipeline som använder den anpassade aktiviteten.

### <a name="create-a-custom-activity"></a>Skapa en anpassad aktivitet
Skapa ett **.NET-klass biblioteks** projekt med en klass som implementerar **IDotNetActivity** -gränssnittet för att skapa en anpassad .net-aktivitet. Det här gränssnittet har bara en metod: [execute](/dotnet/api/microsoft.azure.management.datafactories.runtime.idotnetactivity) och signaturen är:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

Metoden kräver fyra parametrar:

- **linkedServices**. Den här egenskapen är en enumerable lista över länkade tjänster för data lager som refereras till av data uppsättningar/utdata-datauppsättningar för aktiviteten.
- **data uppsättningar**. Den här egenskapen är en enumerable lista över data uppsättningar för indata/utdata för aktiviteten. Du kan använda den här parametern för att hämta de platser och scheman som definieras av data uppsättningar för indata och utdata.
- **aktivitet**. Den här egenskapen representerar den aktuella aktiviteten. Den kan användas för att få åtkomst till utökade egenskaper som är associerade med den anpassade aktiviteten. Mer information finns i [utökade egenskaper för åtkomst](#access-extended-properties) .
- **loggarna**. Med det här objektet kan du skriva fel söknings kommentarer som Surface i användar loggen för pipelinen.

Metoden returnerar en ord lista som kan användas för att kedja samman anpassade aktiviteter i framtiden. Den här funktionen har inte implementerats ännu, så returnera en tom ord lista från-metoden.

### <a name="procedure"></a>Procedur

1. Skapa ett **biblioteks** projekt för .NET-klass.
   
    <ol type="a">
     <li>Starta Visual Studio.</li>
     <li>Klicka på <b>Arkiv</b>, peka på <b>Nytt</b> och klicka på <b>Projekt</b>.</li>
     <li>Expandera <b>Mallar</b> och välj <b>Visual C#</b>. I den här genom gången använder du C#, men du kan använda valfritt .NET-språk för att utveckla den anpassade aktiviteten.</li>
     <li>Välj <b>klass bibliotek</b> i listan över projekt typer till höger. I Visual Studio väljer du <b>klass bibliotek (.NET Framework)</b> </li>
     <li>Ange <b>MyDotNetActivity</b> som <b>namn</b>.</li>
     <li>Välj <b>C:\ADFGetStarted</b> för <b>platsen</b>.</li>
     <li>Klicka på <b>OK</b> för att skapa projektet.</li>
   </ol>

2. Klicka på **verktyg**, peka på **NuGet Package Manager** och klicka på **Package Manager-konsolen**.

3. Kör följande kommando i Package Manager-konsolen för att importera **Microsoft. Azure. Management. DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```

4. Importera **Azure Storage** NuGet-paketet till projektet.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > För att Data Factory service Launcher krävs 4,3-versionen av WindowsAzure. Storage. Om du lägger till en referens i en senare version av Azure Storage sammansättning i ditt anpassade aktivitets projekt visas ett fel meddelande när aktiviteten körs. Information om hur du löser problemet finns i avsnittet [AppDomain-isolering](#appdomain-isolation) .
5. Lägg till följande **using** -uttryck i käll filen i projektet.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

6. Ändra namn **området** till **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```

7. Ändra namnet på klassen till **MyDotNetActivity** och Härled den från **IDotNetActivity** -gränssnittet som visas i följande kodfragment:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```

8. Implementera (Lägg till) metoden **execute** för **IDotNetActivity** -gränssnittet till klassen **MyDotNetActivity** och kopiera följande exempel kod till-metoden.

    I följande exempel räknas antalet förekomster av Sök termen ("Microsoft") i varje blob som är associerad med en data sektor.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.
    /// </summary>

    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

        // to log information, use the logger object
        // log all extended properties
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }

        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;

        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;

        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);

        // get the first Azure Storage linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;

        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;

        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.

        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

        // initialize the continuation token before using it in the do-while loop.
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

            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
            // with the data slice. definition of the method is shown in the next step.

            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

        } while (continuationToken != null);

        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;

        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path
        logger.Write("Writing blob to the folder: {0}", folderPath);

        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);

        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.

        return new Dictionary<string, string>();
    }
    ```

9. Lägg till följande hjälp metoder:

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

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the folder path found in the type properties
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

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
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

    Metoden GetFolderPath returnerar sökvägen till mappen som data uppsättningen pekar på och GetFileName-metoden returnerar namnet på den BLOB/fil som data uppsättningen pekar på. Om du har folderPath definierar användningen av variabler som {Year}, {Month}, {Day} osv., returnerar metoden strängen som det är utan att ersätta dem med körnings värden. Se avsnittet [åtkomst till utökade egenskaper](#access-extended-properties) för information om hur du kommer åt SliceStart, SliceEnd osv.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Metoden beräkna beräknar antalet instanser av nyckelordet Microsoft i indatafilerna (blobbar i mappen). Sök termen ("Microsoft") är hårdkodad i koden.

10. Kompilera projektet. Klicka på **build** på menyn och klicka på **build-lösning**.

    > [!IMPORTANT]
    > Ange 4.5.2-version av .NET Framework som mål ramverk för projektet: Högerklicka på projektet och klicka på **Egenskaper** för att ange mål ramverk. Data Factory stöder inte anpassade aktiviteter som kompilerats mot .NET Framework versioner senare än 4.5.2.

11. Starta **Utforskaren** och gå till mappen **bin\debug** eller **bin\release** beroende på typ av version.

12. Skapa en zip-fil **MyDotNetActivity.zip** som innehåller alla binärfiler i \<project folder\> mappen \bin\Debug Ta med **MyDotNetActivity. pdb** -filen så att du får ytterligare information, till exempel rad nummer i käll koden som orsakade problemet om det uppstod ett fel.

    > [!IMPORTANT]
    > Alla filer i zip-filen för den anpassade aktiviteten måste vara på den **översta nivån** utan undermappar.

    ![Filer för binär utdatafil](./media/data-factory-use-custom-activities/Binaries.png)

13. Skapa en BLOB-behållare med namnet **customactivitycontainer** om den inte redan finns.

14. Ladda upp MyDotNetActivity.zip som en blob till customactivitycontainer i en **allmän** Azure Blob Storage (inte varmt/cool Blob Storage) som kallas av AzureStorageLinkedService.

> [!IMPORTANT]
> Om du lägger till detta .NET-aktivitets projekt i en lösning i Visual Studio som innehåller ett Data Factory-projekt och lägger till en referens till .NET-aktivitets projekt från Data Factory-programprojektet, behöver du inte utföra de två sista stegen för att manuellt skapa zip-filen och ladda upp den till Azure Blob Storage i allmänt syfte. När du publicerar Data Factory entiteter med hjälp av Visual Studio utförs dessa steg automatiskt av publicerings processen. Mer information finns i avsnittet [Data Factory projekt i Visual Studio](#data-factory-project-in-visual-studio) .

## <a name="create-a-pipeline-with-custom-activity"></a>Skapa en pipeline med anpassad aktivitet

Du har skapat en anpassad aktivitet och överfört zip-filen med binärfiler till en BLOB-behållare i ett **allmänt** Azure Storage konto. I det här avsnittet skapar du en Azure-datafabrik med en pipeline som använder den anpassade aktiviteten.

Data uppsättningen för den anpassade aktiviteten representerar blobbar (filer) i mappen customactivityinput i adftutorial-behållaren i blob-lagringen. Data uppsättningen för utdata för aktiviteten representerar utgående blobbar i mappen customactivityoutput i adftutorial-behållaren i blob-lagringen.

Skapa **file.txt** -filen med följande innehåll och ladda upp den till **customactivityinput** -mappen i **adftutorial** -behållaren. Skapa behållaren adftutorial om den inte redan finns.

```
test custom activity Microsoft test custom activity Microsoft
```

Mappen indatafil motsvarar en sektor i Azure Data Factory även om mappen har två eller fler filer. När varje sektor bearbetas av pipelinen upprepas den anpassade aktiviteten genom alla blobbar i Indataporten för den sektorn.

Du ser en utdatafil med i mappen adftutorial\customactivityoutput med en eller flera rader (samma som antalet blobbar i indatafilen):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Här är de steg du utför i det här avsnittet:

1. Skapa en **data fabrik**.
2. Skapa **länkade tjänster** för den Azure Batch poolen med virtuella datorer som den anpassade aktiviteten körs på och Azure Storage som innehåller blobar för indata/utdata.
3. Skapa data **uppsättningar** för indata och utdata som representerar indata och utdata för den anpassade aktiviteten.
4. Skapa en **pipeline** som använder den anpassade aktiviteten.

> [!NOTE]
> Skapa **file.txt** och ladda upp den till en BLOB-behållare om du inte redan har gjort det. Se anvisningarna i föregående avsnitt.

### <a name="step-1-create-the-data-factory"></a>Steg 1: skapa data fabriken
1. När du har loggat in på Azure Portal utför du följande steg:
   1. Klicka på **skapa en resurs** på den vänstra menyn.
   2. Klicka på **data och analys** på det **nya** bladet.
   3. Klicka på **Data Factory** på bladet **Dataanalys**.

      ![Menyn ny Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. På bladet **ny data fabrik** anger du **CustomActivityFactory** som namn. Namnet på Azure Data Factory måste vara globalt unikt. Om du får felet: **data fabriks namnet "CustomActivityFactory" är inte tillgängligt**, ändrar du namnet på data fabriken (till exempel **yournameCustomActivityFactory**) och försöker skapa igen.

    ![Bladet ny Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Klicka på **resurs grupp namn** och välj en befintlig resurs grupp eller skapa en resurs grupp.
4. Kontrol lera att du använder rätt **prenumeration** och **region** där du vill att data fabriken ska skapas.
5. Klicka på **Skapa** på bladet **Ny datafabrik**.
6. Du ser att data fabriken skapas på **instrument panelen** för Azure Portal.
7. När data fabriken har skapats visas bladet Data Factory som visar innehållet i data fabriken.

    ![Bladet Datafabrik](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Steg 2: Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. I det här steget länkar du ditt Azure Storage konto och Azure Batch kontot till din data fabrik.

#### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
1. Klicka på panelen **författare och distribution** på bladet **Data Factory** för **CustomActivityFactory**. Du ser Data Factory Editor.
2. Klicka på **nytt data lager** i kommando fältet och välj **Azure Storage**. Du bör se JSON-skriptet för att skapa en länkad Azure-lagringstjänst i redigeraren.

    ![Nytt data lager – Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Ersätt `<accountname>` med namnet på ditt Azure Storage-konto och `<accountkey>` med åtkomst nyckeln för Azure Storage-kontot. Information om hur du hämtar din lagrings åtkomst nyckel finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).

    ![Azure Storage gilla tjänsten](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Klicka på **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

#### <a name="create-azure-batch-linked-service"></a>Skapa Azure Batch länkad tjänst
1. I Data Factory redigeraren klickar du på **... Mer information** om kommando fältet får du genom att klicka på **ny beräkning** och sedan välja **Azure Batch** på menyn.

    ![Ny beräkning – Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Gör följande ändringar i JSON-skriptet:

   1. Ange Azure Batch konto namn för egenskapen **accountName** . **URL: en** från **bladet Azure Batch konto** har följande format: `http://accountname.region.batch.azure.com` . För egenskapen **batchUri** i JSON måste du ta bort `accountname.` från URL: en och använda `accountname` for `accountName` JSON-egenskapen.
   2. Ange Azure Batch konto nyckeln för **accessKey** -egenskapen.
   3. Ange namnet på den pool som du skapade som en del av kraven för egenskapen **poolName** . Du kan också ange ID: t för poolen i stället för namnet på poolen.
   4. Ange Azure Batch-URI för egenskapen **batchUri** . Exempel: `https://westus.batch.azure.com`.
   5. Ange egenskapen **AzureStorageLinkedService** för egenskapen **linkedServiceName** .

        ```json
        {
          "name": "AzureBatchLinkedService",
          "properties": {
            "type": "AzureBatch",
            "typeProperties": {
              "accountName": "myazurebatchaccount",
              "batchUri": "https://westus.batch.azure.com",
              "accessKey": "<yourbatchaccountkey>",
              "poolName": "myazurebatchpool",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
        ```

       För egenskapen **poolName** kan du också ange ID: t för poolen i stället för namnet på poolen.

### <a name="step-3-create-datasets"></a>Steg 3: skapa data uppsättningar
I det här steget skapar du data uppsättningar som representerar indata och utdata.

#### <a name="create-input-dataset"></a>Skapa indatauppsättning
1. I **redigeraren** för Data Factory klickar du på **... Mer information** om kommando fältet får du genom att klicka på **ny data uppsättning** och sedan välja **Azure Blob Storage** på den nedrullningsbara menyn.
2. Ersätt JSON i den högra rutan med följande JSON-kodfragment:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/customactivityinput/",
                "format": {
                    "type": "TextFormat"
                }
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

   Du skapar en pipeline senare i den här genom gången med start tid: 2016-11-16T00:00:00Z och slut tid: 2016-11-16T05:00:00Z. Det är schemalagt att producera data varje timme, så det finns fem indata/utdata-segment (mellan **00**: 00:00-> **05**: 00:00).

   **Frekvensen** och **intervallet** för indata-datauppsättningen anges till **timme** och **1**, vilket innebär att indata sektorn är tillgänglig varje timme. I det här exemplet är det samma fil (file.txt) i intputfolder.

   Här är start tiderna för varje sektor, som representeras av SliceStart system Variable i ovanstående JSON-kodfragment.
3. Klicka på **distribuera** i verktygsfältet för att skapa och distribuera **InputDataset**. Kontrollera att du ser meddelandet **TABELLEN HAR SKAPATS** i namnlisten i redigeraren.

#### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
1. I **Data Factory redigeraren** klickar du på **... Mer information** om kommando fältet får du genom att klicka på **ny data uppsättning** och sedan välja **Azure Blob Storage**.
2. Ersätt JSON-skriptet i den högra rutan med följande JSON-skript:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
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

     Utmatnings platsen är **adftutorial/customactivityoutput/** och namnet på utdatafilen är yyyy-MM-dd-HH.txt där åååå-mm-dd-HH är året, månaden, datumet och timmen för den sektor som skapas. Mer information finns i [referens för utvecklare][adf-developer-reference] .

    En utgående BLOB/fil genereras för varje ingående sektor. Här är hur en utdatafil namnges för varje sektor. Alla utdatafiler genereras i en mapp för utdata: **adftutorial\customactivityoutput**.

   | Sektor | Starttid | Utdatafil |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Kom ihåg att alla filer i en mapp är en del av en sektor med de start tider som anges ovan. När den här sektorn bearbetas genomsöker den anpassade aktiviteten igenom varje fil och skapar en rad i utdatafilen med antalet förekomster av Sök termen ("Microsoft"). Om det finns tre filer i indata-mappen finns det tre rader i utdatafilen för varje timme-sektor: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt osv.
3. Klicka på **distribuera** i kommando fältet för att distribuera **OutputDataset**.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Skapa och kör en pipeline som använder den anpassade aktiviteten
1. I Data Factory redigeraren klickar du på **... Mer** och välj sedan **ny pipeline** i kommando fältet.
2. Ersätt JSON i den högra rutan med följande JSON-skript:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Observera följande punkter:

   * **Samtidighet** har angetts till **2** så att två sektorer bearbetas parallellt med två virtuella datorer i den Azure Batch poolen.
   * Det finns en aktivitet i avsnittet aktiviteter och är av typen: **DotNetActivity**.
   * **AssemblyName** har angetts till namnet på DLL-filen: **MyDotnetActivity.dll**.
   * **EntryPoint** är inställd på **MyDotNetActivityNS. MyDotNetActivity**.
   * **PackageLinkedService** är inställt på **AzureStorageLinkedService** som pekar på blob-lagringen som innehåller den anpassade aktivitetens zip-fil. Om du använder olika Azure Storage konton för in-och utdatafiler och den anpassade aktivitetens zip-fil, skapar du en annan Azure Storage länkad tjänst. Den här artikeln förutsätter att du använder samma Azure Storage-konto.
   * **PackageFile** har angetts till **customactivitycontainer/MyDotNetActivity.zip**. Den har formatet: containerforthezip/nameofthezip.zip.
   * Den anpassade aktiviteten tar **InputDataset** som indata och **OutputDataset** som utdata.
   * Egenskapen linkedServiceName för den anpassade aktiviteten pekar på **AzureBatchLinkedService**, som talar om för Azure Data Factory att den anpassade aktiviteten måste köras på Azure Batch virtuella datorer.
   * **isPaused** -egenskapen har angetts till **false** som standard. Pipelinen körs omedelbart i det här exemplet eftersom sektorerna börjar i det förflutna. Du kan ställa in den här egenskapen på True för att pausa pipelinen och återställa den till false för att starta om.
   * **Start** tid och **slut** tid är **fem** timmar isär och sektorerna skapas per timme, så fem sektorer skapas av pipelinen.
3. Klicka på **distribuera** i kommando fältet för att distribuera pipelinen.

### <a name="monitor-the-pipeline"></a>Övervaka pipeline
1. I bladet Data Factory i Azure Portal klickar du på **diagram**.

    ![Ikonen Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. I diagramvyn klickar du nu på OutputDataset.

    ![Diagramvy](./media/data-factory-use-custom-activities/diagram.png)
3. Du bör se att de fem Utdataportarna har statusen klar. Om de inte är i det färdiga läget har de inte skapats ännu.

   ![Utgående segment](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Kontrol lera att utdatafilerna genereras i blob-lagringen i behållaren **adftutorial** .

   ![utdata från anpassad aktivitet][image-data-factory-output-from-custom-activity]
5. Om du öppnar utdatafilen bör du se utdata som liknar följande utdata:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Använd [Azure Portal][azure-preview-portal] -eller Azure PowerShell-cmdletar för att övervaka data fabriken, pipeliner och data uppsättningar. Du kan se meddelanden från **ActivityLogger** i koden för den anpassade aktiviteten i loggarna (särskilt User-0. log) som du kan ladda ned från portalen eller använda cmdlets.

   ![Hämta loggar från anpassad aktivitet][image-data-factory-download-logs-from-custom-activity]

Se [övervaka och hantera pipelines](data-factory-monitor-manage-pipelines.md) för detaljerade steg för övervakning av data uppsättningar och pipeliner.

## <a name="data-factory-project-in-visual-studio"></a>Data Factory projekt i Visual Studio
Du kan skapa och publicera Data Factory entiteter genom att använda Visual Studio i stället för att använda Azure Portal. Detaljerad information om hur du skapar och publicerar Data Factory entiteter med hjälp av Visual Studio finns i [skapa din första pipeline med Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) och [Kopiera data från Azure blob till Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) -artiklar.

Utför följande steg om du skapar Data Factory projekt i Visual Studio:

1. Lägg till Data Factory-projektet i Visual Studio-lösningen som innehåller det anpassade aktivitets projektet.
2. Lägg till en referens till .NET-aktivitets projektet från Data Factory-projektet. Högerklicka på Data Factory projekt, peka på **Lägg till** och klicka sedan på **referens**.
3. I dialog rutan **Lägg till referens** väljer du projektet **MyDotNetActivity** och klickar på **OK**.
4. Bygg och publicera lösningen.

    > [!IMPORTANT]
    > När du publicerar Data Factory entiteter skapas en zip-fil automatiskt åt dig och överförs till BLOB-behållaren: customactivitycontainer. Om BLOB-behållaren inte finns skapas den automatiskt.

## <a name="data-factory-and-batch-integration"></a>Data Factory-och batch-integrering
Data Factorys tjänsten skapar ett jobb i Azure Batch med namnet: **ADF-PoolName: Job-XXX**. Klicka på **jobb** på den vänstra menyn.

![Azure Data Factory batch-jobb](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

En uppgift skapas för varje aktivitets körning i en sektor. Om det finns fem segment som är klara att bearbetas skapas fem uppgifter i det här jobbet. Om det finns flera Compute-noder i batch-poolen kan två eller fler sektorer köras parallellt. Om det maximala antalet aktiviteter per Compute-nod har angetts till > 1, kan du också ha mer än en sektor som körs på samma beräkning.

![Aktiviteter för Azure Data Factory batch-jobb](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Följande diagram illustrerar förhållandet mellan Azure Data Factory-och batch-aktiviteter.

![Data Factory & batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Felsökning
Fel sökning består av några grundläggande tekniker:

1. Om du ser följande fel kan du använda ett varmt/coolt Blob Storage i stället för att använda ett allmänt Azure Blob Storage. Överför zip-filen till ett **allmänt Azure Storage-konto**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Om du ser följande fel bekräftar du att namnet på klassen i CS-filen matchar det namn du angav för egenskapen **EntryPoint** i pipeline-JSON. I genom gången är namnet på klassen: MyDotNetActivity och EntryPoint i JSON är: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Om namnen matchar, bekräftar du att alla binärfiler finns i **rotmappen** för zip-filen. Det innebär att när du öppnar zip-filen bör du se alla filer i rotmappen, inte i några undermappar.
3. Om Indataporten inte är inställd på **klar**, kontrollerar du att strukturen för inmappen är korrekt och **file.txt** finns i inmapparna.
3. I **execute** -metoden för din anpassade aktivitet använder du **IActivityLogger** -objektet för att logga information som hjälper dig att felsöka problem. De loggade meddelandena visas i loggfilerna för användare (en eller flera filer med namnet: User-0. log, User-1. log, User-2. log osv.).

   På bladet **OutputDataset** klickar du på sektorn för att visa **data sektor** bladet för den sektorn. Du ser **aktivitets körningar** för den sektorn. Du bör se en aktivitets körning för sektorn. Om du klickar på Kör i kommando fältet kan du starta en annan aktivitets körning för samma sektor.

   När du klickar på aktivitets körningen visas bladet **AKTIVITETS körnings information** med en lista över loggfiler. Du ser loggade meddelanden i filen user_0. log. När ett fel inträffar visas tre aktivitets körningar eftersom antalet återförsök har angetts till 3 i pipeline/Activity-JSON. När du klickar på aktivitets körningen visas de loggfiler som du kan granska för att felsöka felet.

   I listan över loggfiler klickar du på filen **User-0. log**. I den högra panelen finns resultatet av att använda metoden **IActivityLogger. Write** . Om du inte ser alla meddelanden kontrollerar du om du har fler loggfiler med namnet: user_1. log, user_2. log osv. Annars kan koden ha misslyckats efter det senaste loggade meddelandet.

   Kontrol lera dessutom **system-0. log** för eventuella systemfel meddelanden och undantag.
4. Inkludera **PDB** -filen i zip-filen så att fel informationen innehåller information som **anrops stack** när ett fel uppstår.
5. Alla filer i zip-filen för den anpassade aktiviteten måste vara på den **översta nivån** utan undermappar.
6. Se till att **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS. MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) och **packageLinkedService** (ska peka på den **allmänna** Azure Blob-lagring som innehåller zip-filen) har angetts till korrekta värden.
7. Om du har korrigerat ett fel och vill bearbeta sektorn på nytt, högerklickar du på sektorn i **OutputDataset**-bladet och klickar på **Kör**.
8. Om du ser följande fel använder du Azure Storage-paketet med version > 4.3.0. För att Data Factory service Launcher krävs 4,3-versionen av WindowsAzure. Storage. Se avsnittet [AppDomain-isolering](#appdomain-isolation) för ett arbete – om du måste använda den senare versionen av Azure Storage Assembly.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Om du kan använda 4.3.0-versionen av Azure Storage-paketet, tar du bort den befintliga referensen till Azure Storage paketet med version > 4.3.0. Kör sedan följande kommando från NuGet Package Manager-konsolen.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Bygga projektet. Ta bort Azure. Storage-sammansättningen av version > 4.3.0 från mappen bin\Debug Skapa en zip-fil med binärfiler och PDB-filen. Ersätt den gamla zip-filen med den här i BLOB-behållaren (customactivitycontainer). Kör om de segment som misslyckades (Högerklicka på segment och klicka på Kör).
8. Den anpassade aktiviteten använder inte **app.config** -filen från ditt paket. Om din kod läser eventuella anslutnings strängar från konfigurations filen fungerar den inte vid körning. Den bästa metoden när du använder Azure Batch är att lagra alla hemligheter i ett **Azure-valv**, använda ett certifikat baserat tjänstens huvud namn för att skydda nyckel **valvet** och distribuera certifikatet till Azure Batch pool. Den anpassade .NET-aktiviteten kan sedan komma åt hemligheter i KeyVault vid körning. Den här lösningen är en allmän lösning som kan skalas till vilken typ av hemlighet som helst, inte bara anslutnings strängen.

   Det finns en enklare lösning (men inte bästa praxis): du kan skapa en **länkad Azure SQL-tjänst** med anslutnings Strängs inställningar, skapa en data uppsättning som använder den länkade tjänsten och kedja data uppsättningen som en dummy-datauppsättning till den anpassade .net-aktiviteten. Du kan sedan komma åt den länkade tjänstens anslutnings sträng i den anpassade aktivitets koden.

## <a name="update-custom-activity"></a>Uppdatera anpassad aktivitet
Om du uppdaterar koden för den anpassade aktiviteten skapar du den och laddar upp zip-filen som innehåller nya binärfiler till blob-lagringen.

## <a name="appdomain-isolation"></a>AppDomain-isolering
Se [exempel på Cross AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) som visar hur du skapar en anpassad aktivitet som inte är begränsad till sammansättnings versioner som används av Data Factory Launcher (exempel: windowsazure. Storage v 4.3.0, Newtonsoft.Jspå v 6.0. x osv.).

## <a name="access-extended-properties"></a>Åtkomst till utökade egenskaper
Du kan deklarera utökade egenskaper i aktivitets-JSON som visas i följande exempel:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```

I exemplet finns det två utökade egenskaper: **SliceStart** och **DataFactoryName**. Värdet för SliceStart baseras på SliceStart system-variabeln. Se [Systemvariabler](data-factory-functions-variables.md) för en lista över systemvariabler som stöds. Värdet för DataFactoryName är hårdkodat för CustomActivityFactory.

Använd kod som liknar följande kod för att komma åt dessa utökade egenskaper i **execute** -metoden:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Automatisk skalning av Azure Batch
Du kan också skapa en Azure Batch pool med funktionen för **autoskalning** . Du kan till exempel skapa en Azure Batch-pool med 0 dedikerade virtuella datorer och en formel för autoskalning baserat på antalet väntande aktiviteter.

Exempel formeln här uppnår följande beteende: när poolen skapas första gången börjar den med 1 virtuell dator. $PendingTasks måttet definierar antalet aktiviteter som körs och är aktiva (i kö).  Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och anger TargetDedicated i enlighet med detta. Det garanterar att TargetDedicated aldrig hamnar bortom 25 virtuella datorer. När nya uppgifter skickas, växer automatiskt poolen och när aktiviteterna har slutförts blir de virtuella datorerna gratis en i taget och den automatiska skalningen minskar de virtuella datorerna. startingNumberOfVMs och maxNumberofVMs kan anpassas efter dina behov.

Autoskalning-formel:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Se [automatisk skalning av Compute-noder i en Azure Batch pool](../../batch/batch-automatic-scaling.md) för mer information.

Om poolen använder standard- [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)kan batch-tjänsten ta 15-30 minuter att förbereda den virtuella datorn innan den anpassade aktiviteten körs.  Om poolen använder en annan autoScaleEvaluationInterval kan batch-tjänsten ta autoScaleEvaluationInterval + 10 minuter.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Skapa en anpassad aktivitet med hjälp av .NET SDK
I genom gången i den här artikeln skapar du en data fabrik med en pipeline som använder den anpassade aktiviteten med hjälp av Azure Portal. Följande kod visar hur du skapar en data fabrik med hjälp av .NET SDK i stället. Du hittar mer information om hur du använder SDK för att skapa pipelines i [skapa en pipeline med kopierings aktivitet med hjälp av .NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md) -artikeln.

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Felsöka anpassad aktivitet i Visual Studio
Exemplet [Azure Data Factory-lokal miljö](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) på GitHub innehåller ett verktyg som gör det möjligt att felsöka anpassade .net-aktiviteter i Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Exempel på anpassade aktiviteter på GitHub
| Exempel | Vad anpassad aktivitet gör |
| --- | --- |
| [Http-Datahämtare](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Hämtar data från en HTTP-slutpunkt till Azure Blob Storage använder anpassad C#-aktivitet i Data Factory. |
| [Exempel på Twitter-Attitydanalys](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Anropar en Azure Machine Learning Studio (klassisk) modell och utför sentiment analys, poängsättning, förutsägelser osv. |
| [Kör R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Anropar R-skript genom att köra RScript.exe på ditt HDInsight-kluster som redan har R installerat. |
| [Aktivitet i Cross AppDomain .NET](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Använder olika sammansättnings versioner än de som används i Data Factory starta |
| [Ombearbeta en modell i Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Ombearbeta en modell i Azure Analysis Services. |

[batch-net-library]: ../../batch/quick-run-dotnet.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]:../../azure-sql/database/sql-database-paas-overview.md
[batch-get-started]: ../../batch/quick-run-dotnet.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: /previous-versions/azure/mt125880(v=azure.100)
[new-azure-batch-pool]: /previous-versions/azure/mt125936(v=azure.100)
[azure-batch-blog]: /archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png