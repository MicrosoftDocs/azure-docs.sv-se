---
title: 'Snabb start: klient biblioteket för Azure Blob Storage-V8 för Java'
description: Skapa ett lagringskonto och en container i objektlagring (Blob). Använd sedan Azure Storage klient bibliotek V8 för Java för att ladda upp en blob till Azure Storage, hämta en blob och lista blobarna i en behållare.
author: twooley
ms.custom: devx-track-java
ms.author: twooley
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 49c55a2b565100e370ce561537c96a96b896f355
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280606"
---
# <a name="quickstart-manage-blobs-with-java-v8-sdk"></a>Snabb start: hantera blobbar med Java V8 SDK

I den här snabb starten lär du dig att hantera blobbar med hjälp av Java. Blobbar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och Arkiv data. Du laddar upp, laddar ned och listar blobbar. Du ska också skapa, ange behörigheter för och ta bort behållare.

> [!NOTE]
> I den här snabb starten används en äldre version av klient biblioteket för Azure Blob Storage. Information om hur du kommer igång med den senaste versionen finns i [snabb start: hantera blobbar med Java V12 SDK](storage-quickstart-blobs-java.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto. [Skapa ett lagrings konto](../common/storage-account-create.md).
- En IDE som har maven-integrering. Den här guiden använder [Sol förmörkelse](https://www.eclipse.org/downloads/) med konfigurationen "Sol förmörkelse IDE för Java-utvecklare".

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

[Exempel programmet](https://github.com/Azure-Samples/storage-blobs-java-quickstart) är ett grundläggande konsol program.

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Det här kommandot klonar lagret till den lokala git-mappen. Öppna projektet genom att starta Eclipse och stänga välkomstskärmen. Välj **Arkiv** och sedan **Öppna projekt, under Filsystem**. Kontrollera att alternativet för att **identifiera och konfigurera projektegenskaper** har markerats. Välj **Katalog** och navigera till den plats där sparade den klonade lagringsplatsen. På den klonade lagringsplatsen väljer du mappen **blobAzureApp**. Kontrollera att projektet **blobAzureApp** visas som ett Eclipse-projekt och välj sedan **Slutför**.

När projektet har importer ATS öppnar du **AzureApp. java** (finns i **blobQuickstart. blobAzureApp** inuti **src/main/Java**) och ersätter `accountname` och `accountkey` inuti `storageConnectionString` strängen. Kör sedan programmet. Specifika anvisningar för att slutföra dessa uppgifter beskrivs i följande avsnitt.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Öppna filen **AzureApp.Java**. Hitta variabeln `storageConnectionString` och klistra in anslutningssträngens värde som du kopierade i föregående avsnitt. Variabeln `storageConnectionString` bör se ut ungefär som följande kodexempel:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Kör exemplet

Det här exempel programmet skapar en test fil i standard katalogen (*C:\Users \<user> \AppData\Local\Temp*, för Windows-användare), laddar upp den till Blob Storage, listar blobarna i behållaren och laddar ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna.

Kör exemplet med hjälp av Maven på kommandoraden. Öppna ett gränssnitt och gå till **blobAzureApp** i den klonade katalogen. Ange sedan `mvn compile exec:java`.

Följande exempel visar utdata om du kör programmet i Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

Innan du fortsätter kontrollerar du din standard katalog (*C:\Users \<user> \AppData\Local\Temp*, för Windows-användare) för exempel filen. Kopiera URL:en för blobben från konsolfönstret och klistra in den i en webbläsare om du vill se innehållet i filen i blobblagringen. Om du jämför exempelfilen i katalogen med det innehåll som lagras i bloblagringen märker du att de är likadana.

  >[!NOTE]
  >Du kan också använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för att visa filerna i blobblagringen. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som gör det möjligt att komma åt information på lagringskontot.

När du har verifierat filerna trycker du på **RETUR** -tangenten för att slutföra demon och ta bort testfilerna. Nu när du vet vad exemplet gör kan du öppna filen **AzureApp.java** och titta på koden.

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst går vi igenom exempelkoden så att du kan förstå hur den fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten

Det första du ska göra är att skapa referenser till objekten som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra – vart och ett används av nästa i listan.

* Skapa en instans av objektet [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) som pekar mot lagringskontot.

    Objektet **CloudStorageAccount** är en representation av lagringskontot och gör det möjligt att konfigurera och komma åt egenskaper för lagringskontot programmässigt. Med hjälp av objektet **CloudStorageAccount** kan du skapa en instans av klienten **CloudBlobClient**, som krävs för att komma åt blobbtjänsten.

* Skapa en instans av **CloudBlobClient** -objektet som pekar på [BLOB service](/java/api/com.microsoft.azure.storage.blob.cloudblobclient) i ditt lagrings konto.

    **CloudBlobClient** ger dig en åtkomstpunkt till blobtjänsten så att du kan konfigurera och komma åt egenskaper för bloblagring programmatiskt. Med hjälp av **CloudBlobClient** kan du skapa en instans av objektet **CloudBlobContainer**, som krävs för att skapa behållare.

* Skapa en instans av objektet [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer), som representerar den behållare du får åtkomst till. Använd containrar för att organisera blobar på samma sätt som du använder mappar på datorn för att organisera filer.

    När du har **CloudBlobContainer** kan du skapa en instans av [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob) -objektet som pekar på den angivna blobben som du är intresse rad av och utföra en uppladdning, hämtning, kopiering eller annan åtgärd.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om containrar finns i [Namnge och referera till containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Skapa en container

I det här avsnittet skapar du en instans av objekt, skapar en ny container och anger sedan behörigheter för containern så att blobarna är offentliga och går att komma åt med bara en URL. Containern heter **quickstartcontainer**.

Det här exemplet använder [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists) eftersom vi vill skapa en ny container varje gång exemplet körs. I en produktionsmiljö där du använder samma container i ett helt program är det bättre att bara anropa **CreateIfNotExists** en gång. Du kan också skapa containern i förväg så att du inte behöver skapa den i koden.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i containern

Hämta en referens till bloben i mål behållaren om du vill överföra en fil till en Block-Blob. När du har blobbreferensen kan du ladda upp data till den med hjälp av [CloudBlockBlob.Upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload). Den här åtgärden skapar bloben om den inte redan finns, eller skriver över bloben om den redan finns.

Exempelkoden skapar en lokal fil som ska användas för uppladdning och nedladdning, och lagrar den fil som ska laddas upp som **source** (källa) och namnet på bloben i **blob**. I följande exempel laddas filen upp till containern med namnet **quickstartcontainer**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Det finns flera metoder för `upload`, exempelvis [upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadstandardblobtier) och [uploadText](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadtext), som du kan använda med Blob Storage. Om du till exempel har en sträng kan du använda metoden `UploadText` i stället för metoden `Upload`.

Blockblobbar kan vara valfri texttyp eller binär fil. Sidblobar används främst för VHD-filer som används för att säkerhetskopiera virtuella IaaS-datorer. Använd tilläggsblobar för loggning, till exempel när du vill skriva till en fil och sedan fortsätta att lägga till mer information. De flesta objekt som lagras i Blob Storage är blockblobar.

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Du kan hämta en lista över filer i behållaren med hjälp av [CloudBlobContainer.ListBlobs](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobs). Följande kod hämtar listan över blobbar och går sedan igenom dem och visar URI för de blobbar som har påträffats. Du kan kopiera URI från kommandofönstret och klistra in den i en webbläsare om du vill visa filen.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned blobbar till den lokala disken med hjälp av [CloudBlob.DownloadToFile](/java/api/com.microsoft.azure.storage.blob.cloudblob.downloadtofile).

Följande kod hämtar blobben som överfördes i föregående avsnitt och lägger till ett suffix med "_DOWNLOADED" i blobbnamnet så att du kan se båda filerna på den lokala disken.

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and
// haven't changed the blob we're interested in, so we can reuse it.
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver de blobbar som du har överfört kan du ta bort hela behållaren med hjälp av [CloudBlobContainer. DeleteIfExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists). Denna metod tar även bort filerna i containern.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();

if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med Java. För att få mer information om hur du arbetar med Java fortsätter du till vår lagringsplats för GitHub-källkod.

> [!div class="nextstepaction"]
> [Java API-referens](/java/api/overview/azure/storage?view=azure-java-legacy&preserve-view=true) 
>  [Kod exempel för Java](../common/storage-samples-java.md)