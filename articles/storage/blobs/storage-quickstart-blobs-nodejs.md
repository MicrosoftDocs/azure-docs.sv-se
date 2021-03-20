---
title: 'Snabb start: Azure Blob Storage-bibliotek V12 – Java Script'
description: I den här snabb starten får du lära dig hur du använder Azure Blob Storage klient bibliotek version 12 för Java Script för att skapa en behållare och en BLOB i blob-lagring (objekt). Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 0883fd1546ac3b55ec4211e5048569556858de29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179926"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Snabb start: hantera blobbar med Java Script V12 SDK i Node.js

I den här snabb starten lär du dig att hantera blobbar med hjälp av Node.js. Blobbar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och Arkiv data. Du laddar upp, laddar ned och listar blobbar och skapar och tar bort behållare.

Ytterligare resurser:

* [Referensdokumentation för API](/javascript/api/@azure/storage-blob)
* [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Paket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-blob)
* [Exempel](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto. [Skapa ett lagrings konto](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Konfigurera

Det här avsnittet beskriver hur du förbereder ett projekt så att det fungerar med Azure Blob Storage-V12 för Java Script.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett JavaScript-program med namnet *BLOB-snabb start-V12*.

1. Skapa en ny katalog för projektet i ett konsol fönster (till exempel cmd, PowerShell eller bash).

    ```console
    mkdir blob-quickstart-v12
    ```

1. Växla till den nyss skapade *V12-katalogen för BLOB-snabb start* .

    ```console
    cd blob-quickstart-v12
    ```

1. Skapa en ny textfil med namnet *package.jspå*. Den här filen definierar Node.js-projektet. Spara filen i katalogen *BLOB-snabb start-V12* . Här är filens innehåll:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Du kan ange ett eget namn för `author` fältet, om du vill.

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i *BLOB-snabb start-V12-* katalogen, installerar du Azure Blob Storage-klient biblioteket för JavaScript-paket med hjälp av `npm install` kommandot. Det här kommandot läser *package.jspå* filen och installerar Azure Blob Storage-V12 för JavaScript-paket och alla bibliotek som det är beroende av.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Från projekt katalogen:

1. Öppna en annan ny textfil i kod redigeraren
1. Lägg till `require` anrop för att läsa in Azure och Node.js moduler
1. Skapa strukturen för programmet, inklusive grundläggande undantags hantering

    Här är koden:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const { v1: uuidv1} = require('uuid');

    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Spara den nya filen som *blob-quickstart-v12.js* i katalogen *BLOB-snabb start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objekt modell

Azure Blob Storage är optimerat för att lagra enorma mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagrings kontot
* En container på lagringskontot
* En BLOB i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande JavaScript-klasser för att interagera med dessa resurser:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` klassen låter dig ändra Azure Storage-resurser och blob-behållare.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` klassen låter dig ändra Azure Storage behållare och deras blobbar.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): `BlobClient` klassen låter dig ändra Azure Storage blobbar.

## <a name="code-examples"></a>Kodexempel

I de här exempel kods tycken visas hur du gör följande med klient biblioteket för Azure Blob Storage för Java Script:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en container](#create-a-container)
* [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
* [Visa blobar i en container](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutnings strängen för lagrings kontot från den miljö variabel som skapades i avsnittet [Konfigurera din lagrings anslutnings sträng](#configure-your-storage-connection-string) .

Lägg till den här koden inuti `main` funktionen:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Skapa en container

Välj ett namn för den nya behållaren. Koden nedan lägger till ett UUID-värde till behållar namnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa en instans av klassen [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) genom att anropa [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) -metoden. Anropa sedan metoden [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) för att hämta en referens till en behållare. [Skapa slutligen ett anrop för](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) att skapa behållaren i ditt lagrings konto.

Lägg till den här koden i slutet av `main` funktionen:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodfragment:

1. Skapar en text sträng som ska överföras till en blob.
1. Hämtar en referens till ett [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) -objekt genom att anropa metoden [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) på [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) från avsnittet [skapa en behållare](#create-a-container) .
1. Överför text Strängs data till blobben genom att anropa [överförings](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) metoden.

Lägg till den här koden i slutet av `main` funktionen:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Visa en lista över blobarna i behållaren genom att anropa metoden [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) . I det här fallet har endast en BLOB lagts till i behållaren, så List åtgärden returnerar bara den en blob.

Lägg till den här koden i slutet av `main` funktionen:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Ladda ned blobbar

Hämta den tidigare skapade blobben genom att anropa [nedladdnings](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) metoden. Exempel koden innehåller en hjälp funktion `streamToString` som kallas, som används för att läsa en Node.js läsbar data ström i en sträng.

Lägg till den här koden i slutet av `main` funktionen:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Lägg till den här hjälp  funktionen efter `main` funktionen:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar resurserna som skapats av appen genom att ta bort hela behållaren med [Delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) -metoden. Du kan också ta bort de lokala filerna, om du vill.

Lägg till den här koden i slutet av `main` funktionen:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en text sträng och laddar upp den till Blob Storage. I exemplet visas sedan en lista över blobarna i behållaren, laddar ned bloben och visar hämtade data.

Gå till den katalog som innehåller *blob-quickstart-v12.js* -filen från en konsol tolk och kör sedan följande `node` kommando för att köra appen.

```console
node blob-quickstart-v12.js
```

Utdata från appen liknar följande exempel:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Gå igenom koden i fel söknings programmet och kontrol lera [Azure Portal](https://portal.azure.com) under hela processen. Kontrol lera att behållaren håller på att skapas. Du kan öppna blobben i behållaren och visa innehållet.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig att ladda upp, ladda ned och lista blobar med hjälp av Java Script.

För självstudier, exempel, snabb starter och annan dokumentation går du till:

> [!div class="nextstepaction"]
> [Azure för Java Script Developer Center](/azure/developer/javascript/)

* Information om hur du distribuerar en webbapp som använder Azure Blob Storage finns i [Självstudier: Ladda upp avbildnings data i molnet med Azure Storage](./storage-upload-process-images.md?preserve-view=true&tabs=javascript)
* Om du vill se exempel appar för Blob Storage kan du fortsätta till [Azure Blob Storage-V12 JavaScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
* Mer information finns i [klient biblioteket för Azure Blob Storage för Java Script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
