---
title: 'Snabb start: klient biblioteket för Azure Blob Storage-v10 för Java Script'
description: Skapa, ladda upp och ta bort blobbar och behållare i Node.js med Azure Storage klient bibliotek v10 för Java Script
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 2371c789b9e4a9fc70f4207fd8a634e419c97912
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599451"
---
# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-nodejs"></a>Snabb start: hantera blobbar med Java Script v10 SDK i Node.js

I den här snabb starten lär du dig att hantera blobbar med hjälp av Node.js. Blobbar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och Arkiv data. Du överför, laddar ned, listar och tar bort blobbar och hanterar behållare.

> [!NOTE]
> I den här snabb starten används en äldre version av klient biblioteket för Azure Blob Storage. Information om hur du kommer igång med den senaste versionen finns i [snabb start: hantera blobbar med Java Script V12 SDK i Node.js](storage-quickstart-blobs-nodejs.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto. [Skapa ett lagrings konto](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

[Exempelprogrammet](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git) som används i snabbstarten är ett enkelt Node.js- konsolprogram. Börja genom att klona lagringsplatsen till din dator med följande kommando:

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Nu ska du ändra mappar för programmet:

```bash
cd azure-storage-js-v10-quickstart
```

Öppna mappen i önskad kodredigeringsmiljö.

## <a name="configure-your-storage-credentials"></a>Konfigurera dina inloggningsuppgifter för lagring

Innan du kör programmet måste du ange säkerhetsreferenserna för ditt lagringskonto. Exempellagringsplatsen innehåller en fil med namnet *.env.example*. Byt namn på filen genom att ta bort filnamnstillägget *.example*, vilket resulterar i en fil med namnet *.env*. I filen *.env* lägger du till ditt kontonamn och åtkomstnycklelvärdena efter nycklarna *AZURE_STORAGE_ACCOUNT_NAME* och *AZURE_STORAGE_ACCOUNT_ACCESS_KEY*.

## <a name="install-required-packages"></a>Installera de paket som krävs

I programkatalogen kör du *npm install* för att installera de nödvändiga paketen för programmet.

```bash
npm install
```

## <a name="run-the-sample"></a>Kör exemplet

Nu när beroendena har installerats kan du köra exemplet med följande kommando:

```bash
npm start
```

Appens utdata bör likna följande exempel:

```bash
Container "demo" is created
Containers:
 - container-one
 - container-two
 - demo
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Om du använder ett nytt lagrings konto för den här snabb starten kan du bara se *demo* behållaren som visas under etiketten "*behållare:*".

## <a name="understanding-the-code"></a>Förstå koden

Exemplet börjar med att importera ett antal klasser och funktioner från Azure Blob Storage-namnområdet. Vart och ett av de importerade objekten diskuteras i sin kontext så som de används i exemplet.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

Autentiseringsuppgifter läses från miljövariabler baserat på relevant kontext.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

Modulen *dotenv* läser in miljövariabler när appen körs lokalt för felsökning. Värden definieras i en fil med namnet *.env* och läses in i den aktuella körningskontexten. I produktionsmiljön tillhandahåller serverkonfigurationen dessa värden, vilket är anledningen till att den här koden endast körs när skriptet *inte* körs i en ”produktionsmiljö”.

Nästa block med moduler importeras för att skapa ett gränssnitt mot filsystemet.

```javascript
const fs = require('fs');
const path = require('path');
```

Syftet med dessa moduler är följande: 

- *FS* är den inbyggda Node.js-modulen som används för att arbeta med filsystemet

- *path* krävs för att fastställa den absoluta sökvägen till filen, som används när du överför en fil till Blob Storage

Därefter läses miljövariablernas värden och placeras i konstanter.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
Nästa uppsättning konstanter hjälper till att visa syftet med filstorleksberäkningar under uppladdningar.

```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```

Begär Anden som görs av API: et kan ställas in på timeout efter ett angivet intervall. Klassen [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-legacy&preserve-view=true) ansvarar för att hantera tidsgränser för begäranden och den efterföljande konstanten används för att definiera tidsgränser som används i det här exemplet.

```javascript
const ONE_MINUTE = 60 * 1000;
```

### <a name="calling-code"></a>Anropa kod

För att ge stöd för *async/await*-syntaxen i JavaScript innesluts anropskoden i en funktion med namnet *execute*. Därefter anropas *execute* och hanteras som ett löfte.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```

All följande kod körs inuti execute-funktionen där kommentaren `// commands...` har lagts till.

Först deklareras de relevanta variablerna för att tilldela namn och exempelinnehåll och för att peka på den lokala filen som ska laddas upp till Blob Storage.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

Autentiseringsuppgifter används för att skapa en pipeline som ansvarar för att hantera hur begäranden skickas till REST-API:et. Pipelines är trådsäkra och anger logik för återförsöksprinciper, loggning, deserialiseringsregler för HTTP-svar med mera.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```

Följande klasser används i det här kodblocket:

- Klassen [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-legacy&preserve-view=true) ansvarar för att omsluta autentiseringsuppgifterna för lagringskontot och skicka dem till en pipeline som begär dem.

- Klassen [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-legacy&preserve-view=true) ansvarar för att skapa en ny pipeline.

- [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-legacy&preserve-view=true) modellerar en URL som används i REST-API:et. Med instanser av den här klassen kan du utföra åtgärder som att lista containrar och tillhandahålla kontextinformation för att generera container-URL:er.

*ServiceURL*-instansen används med instanserna [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-legacy&preserve-view=true) och [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-legacy&preserve-view=true) för att hantera containrar och blobar i ditt lagringskonto.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```

Variablerna *containerURL* och *blockBlobURL* återanvänds i exemplet för att hantera lagringskontot. 

I detta läge finns inte containern i lagringskontot. *ContainerURL*-instansen representerar en URL som du kan hantera. Du kan skapa och ta bort containern med hjälp av den här instansen. Platsen för den här containern motsvarar en plats som följande:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

*blockBlobURL* används för att hantera enskilda objekt, så att du kan ladda upp, hämta och ta bort blobinnehåll. URL:en här liknar den här platsen:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```

Precis som med containern finns inte blockbloben än. *blockBlobURL*-variabeln används senare för att skapa bloben genom att överföra innehåll.

### <a name="using-the-aborter-class"></a>Använda klassen Aborter

Begär Anden som görs av API: et kan ställas in på timeout efter ett angivet intervall. *Avbrotts* klassen ansvarar för att hantera hur tids gränsen för tids gränsen uppnåddes. Följande kod skapar en kontext där en uppsättning begär Anden får 30 minuter att köra.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```

En Aborter ger dig kontroll över begäranden genom att låta dig:

- ange hur lång tid som ska tilldelas en grupp med begäranden
- ange hur länge en enskild begäran får köra i batchen
- avbryta begäranden
- använda den statiska medlemmen *Aborter.none* för att helt förhindra att begäranden stoppas på grund av en timeout

### <a name="create-a-container"></a>Skapa en container

Metoden *create* för *ContainerURL* används för att skapa en container.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```

Eftersom namnet på containern definieras när *ContainerURL.fromServiceURL(serviceURL, containerName)* anropas, behöver du bara anropa metoden *create* för att skapa containern.

### <a name="show-container-names"></a>Visa namn på containrar

Konton kan lagra ett stort antal containrar. Följande kod visar hur du listar containrar på ett segmentbaserat sätt, vilket gör att du kan gå igenom ett stort antal containrar. Instanser av *ServiceURL* och *Aborter* skickas till funktionen *showContainerNames*.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```

Funktionen *showContainerNames* använder metoden *listContainersSegment* för att begära batchar med namn på containrar från lagringskontot.

```javascript
async function showContainerNames(aborter, serviceURL) {
    let marker = undefined;

    do {
        const listContainersResponse = await serviceURL.listContainersSegment(aborter, marker);
        marker = listContainersResponse.nextMarker;
        for(let container of listContainersResponse.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```

När svaret returneras itereras *containerItems* för att logga namnet på konsolen. 

### <a name="upload-text"></a>Ladda upp text

Du överför text till bloben genom att använda metoden *upload*.

```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```

Här skickas texten och dess längd till metoden.

### <a name="upload-a-local-file"></a>Ladda upp en lokal fil

För att ladda upp en lokal fil till containern behöver du en container-URL och sökvägen till filen.

```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```

Funktionen *uploadLocalFile* anropar funktionen *uploadFileToBlockBlob*, som använder sökvägen till filen och en instans av målet för blockbloben som argument.

```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```

### <a name="upload-a-stream"></a>Ladda upp en dataström

Det går också att ladda upp dataströmmar. Det här exemplet öppnar en lokal fil som en dataström som ska skickas till metoden upload.

```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```

Funktionen *uploadStream* anropar *uploadStreamToBlockBlob* för att ladda upp dataströmmen till lagringscontainern.

```javascript
async function uploadStream(aborter, containerURL, filePath) {
    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```

Under en uppladdning allokerar *uploadStreamToBlockBlob* buffertar för att cachelagra data från dataströmmen om ett nytt försök skulle bli nödvändigt. *maxBuffers*-värdet anger maximalt antal buffertar som används eftersom varje buffert skapar en separat överföringsförfrågan. Fler buffertar innebär högre hastigheter, men på bekostnad av högre minnesanvändning. Uppladdningshastigheten avstannar när antalet buffertar är tillräckligt högt för att flaskhalsen ska övergå till nätverket eller disken i stället för klienten.

### <a name="show-blob-names"></a>Visa blobnamn

Precis som konton kan innehålla många containrar kan varje container potentiellt innehålla ett stort antal blobar. Åtkomsten till varje blob i en container är tillgänglig via en instans av klassen *ContainerURL*.

```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```

Funktionen *showBlobNames* anropar *listBlobFlatSegment* för att begäran batchar med blobar från containern.

```javascript
async function showBlobNames(aborter, containerURL) {
    let marker = undefined;

    do {
        const listBlobsResponse = await containerURL.listBlobFlatSegment(Aborter.none, marker);
        marker = listBlobsResponse.nextMarker;
        for (const blob of listBlobsResponse.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```

### <a name="download-a-blob"></a>Ladda ned en blob

När en blob har skapats kan du ladda ned innehållet med hjälp av metoden *download*.

```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = await streamToString(downloadResponse.readableStreamBody);
console.log(`Downloaded blob content: "${downloadedContent}"`);
```

Svaret returneras som en dataström. I det här exemplet konverteras strömmen till en sträng med hjälp av följande *streamToString* Helper-funktion.

```javascript
// A helper method used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", data => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
}
```

### <a name="delete-a-blob"></a>Ta bort en blob

Metoden *delete* från en *BlockBlobURL*-instans tar bort en blob från containern.

```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```

### <a name="delete-a-container"></a>Ta bort en container

Metoden *delete* från en *ContainerURL*-instans tar bort en container från lagringskontot.

```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Rensa resurser

Alla data som skrivits till lagringskontot tas bort automatiskt i slutet av kodexemplet. 

## <a name="next-steps"></a>Nästa steg

Den här snabbstarten visar hur du hanterar blobar och containrar i Azure Blob Storage med hjälp av Node.js. Mer information om hur du arbetar med detta SDK finns på GitHub-lagringsplatsen.

> [!div class="nextstepaction"]
> [Azure Storage v10 SDK för Java Script-lagringsplats](https://github.com/Azure/azure-storage-js) 
>  [Azure Storage Java Script API-referens](/javascript/api/overview/azure/storage-overview)
