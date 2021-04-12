---
title: 'Snabb start: Azure Blob Storage Library V12-Java'
description: I den här snabb starten får du lära dig hur du använder Azure-Blob Storage klient bibliotek version 12 för Java för att skapa en behållare och en BLOB i blob-lagring (objekt). Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: twooley
ms.custom: devx-track-java
ms.author: twooley
ms.date: 12/01/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b6d42cf26bc128257aa5d0cd7ef01e8fe8749a51
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280640"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Snabb start: hantera blobbar med Java V12 SDK

I den här snabb starten lär du dig att hantera blobbar med hjälp av Java. Blobbar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och Arkiv data. Du laddar upp, laddar ned och listar blobbar och skapar och tar bort behållare.

Ytterligare resurser:

* [Referensdokumentation för API](/java/api/overview/azure/storage-blob-readme)
* [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob)
* [Paket (maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob)
* [Exempel](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto. [Skapa ett lagrings konto](../common/storage-account-create.md).
- [Java Development Kit (JDK)](/java/azure/jdk/) version 8 eller senare.
- [Apache Maven](https://maven.apache.org/download.cgi).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Konfigurera

Det här avsnittet beskriver hur du förbereder ett projekt för att arbeta med Azure Blob Storage-V12 för Java.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett Java-program med namnet *BLOB-snabb start-V12*.

1. I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du Maven för att skapa en ny konsol app med namnet *BLOB-snabb start-V12*. Skriv följande **mvn** -kommando för att skapa en "Hello World!" Java-projekt.

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.blobs.quickstart `
        --define artifactId=blob-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.blobs.quickstart \
        --define artifactId=blob-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

1. Resultatet från att generera projektet bör se ut ungefär så här:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. På sidan *BLOB-snabb start-V12-* katalogen skapar du en annan katalog med namnet *data*. Det är här som BLOB-datafilerna ska skapas och lagras.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installera paketet

Öppna *pom.xml* -filen i text redigeraren. Lägg till följande beroende element i gruppen med beroenden.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.6.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Från projekt katalogen:

1. Navigera till katalogen */src/main/Java/com/blobs/QuickStart*
1. Öppna filen *app. java* i redigeraren
1. Ta bort `System.out.println("Hello world!");` instruktionen
1. Lägg till `import` direktiv

Här är koden:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objekt modell

Azure Blob Storage optimeras för att lagra enorma mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagrings kontot
* En container på lagringskontot
* En BLOB i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande Java-klasser för att interagera med dessa resurser:

* [BlobServiceClient](/java/api/com.azure.storage.blob.blobserviceclient): `BlobServiceClient` klassen låter dig ändra Azure Storage-resurser och blob-behållare. Lagrings kontot innehåller namn området på den översta nivån för Blob Service.
* [BlobServiceClientBuilder](/java/api/com.azure.storage.blob.blobserviceclientbuilder): `BlobServiceClientBuilder` klassen innehåller en Fluent Builder-API som hjälper till att hjälpa till att konfigurera och instansiera `BlobServiceClient` objekt.
* [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient): `BlobContainerClient` klassen låter dig ändra Azure Storage behållare och deras blobbar.
* [BlobClient](/java/api/com.azure.storage.blob.blobclient): `BlobClient` klassen låter dig ändra Azure Storage blobbar.
* [BlobItem](/java/api/com.azure.storage.blob.models.blobitem): `BlobItem` klassen representerar enskilda blobbar som returneras från ett anrop till [listBlobs](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs).

## <a name="code-examples"></a>Kodexempel

I dessa exempel kods tycken visas hur du gör följande med Azure Blob Storage-klient biblioteket för java:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en container](#create-a-container)
* [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
* [Visa blobar i en container](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutnings strängen för lagrings kontot från den miljö variabel som skapades i avsnittet [Konfigurera din lagrings anslutnings sträng](#configure-your-storage-connection-string) .

Lägg till den här koden i- `Main` metoden:

```java
System.out.println("Azure Blob Storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Skapa en container

Välj ett namn för den nya behållaren. Koden nedan lägger till ett UUID-värde till behållar namnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Därefter skapar du en instans av klassen [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient) och anropar sedan [create](/java/api/com.azure.storage.blob.blobcontainerclient.create) -metoden för att skapa behållaren i ditt lagrings konto.

Lägg till den här koden i slutet av `Main` metoden:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodfragment:

1. Skapar en textfil i den lokala *data* katalogen.
1. Hämtar en referens till ett [BlobClient](/java/api/com.azure.storage.blob.blobclient) -objekt genom att anropa metoden [getBlobClient](/java/api/com.azure.storage.blob.blobcontainerclient.getblobclient) i behållaren från avsnittet [skapa en behållare](#create-a-container) .
1. Överför den lokala text filen till blobben genom att anropa metoden [uploadFromFile](/java/api/com.azure.storage.blob.blobclient.uploadfromfile) . Den här metoden skapar blobben om den inte redan finns, men den kommer inte att skriva över den om den gör det.

Lägg till den här koden i slutet av `Main` metoden:

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Visa en lista över blobarna i behållaren genom att anropa metoden [listBlobs](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs) . I det här fallet har endast en BLOB lagts till i behållaren, så List åtgärden returnerar bara den en blob.

Lägg till den här koden i slutet av `Main` metoden:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned den tidigare skapade blobben genom att anropa [downloadToFile](/java/api/com.azure.storage.blob.specialized.blobclientbase.downloadtofile) -metoden. Exempel koden lägger till suffixet "DOWNLOAD" i fil namnet så att du kan se båda filerna i det lokala fil systemet.

Lägg till den här koden i slutet av `Main` metoden:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar resurserna som skapats av appen genom att ta bort hela behållaren med [Delete](/java/api/com.azure.storage.blob.blobcontainerclient.delete) -metoden. Den tar också bort de lokala filer som skapats av appen.

Appen pausar indata från användaren genom `System.console().readLine()` att anropa innan den tar bort BLOB, container och lokala filer. Det här är en bra chans att verifiera att resurserna har skapats korrekt innan de tas bort.

Lägg till den här koden i slutet av `Main` metoden:

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en test fil i din lokala mapp och laddar upp den till Blob Storage. Exemplet visar sedan blobarna i behållaren och laddar ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna.

Navigera till den katalog som innehåller *pom.xml* -filen och kompilera projektet med hjälp av följande `mvn` kommando.

```console
mvn compile
```

Sedan skapar du paketet.

```console
mvn package
```

Kör följande `mvn` kommando för att köra appen.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Utdata från appen liknar följande exempel:

```output
Azure Blob Storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Innan du påbörjar rensnings processen kontrollerar du mappen *data* för de två filerna. Du kan öppna dem och se att de är identiska.

När du har verifierat filerna trycker du på **RETUR** -tangenten för att ta bort testfilerna och slutföra demon.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig att ladda upp, ladda ned och lista blobar med Java.

Om du vill se exempel appar för Blob Storage fortsätter du till:

> [!div class="nextstepaction"]
> [Java-exempel för Azure Blob Storage SDK V12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Läs mer i [Azure SDK för Java](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md).
* För självstudier, exempel, snabb starter och annan dokumentation går du till [Azure för Java-moln utvecklare](/azure/developer/java/).
