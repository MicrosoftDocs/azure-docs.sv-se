---
title: 'Snabb start: Azure Blob Storage för Java Script v10 i webbläsaren'
description: Lär dig att ladda upp, lista och ta bort blobar med Java Script v10 SDK på en HTML-sida.
services: storage
author: mhopkins-msft
ms.custom: mvc, devx-track-js
ms.service: storage
ms.author: mhopkins
ms.date: 07/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: d212029936bcd257ef5a78eeedc98c2d6e1df514
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012790"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Snabb start: hantera blobbar med Java Script v10 SDK i webbläsare

I den här snabb starten lär du dig att hantera blobbar med hjälp av JavaScript-kod som körs helt i webbläsaren. Blobbar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och Arkiv data. Du använder nödvändiga säkerhets åtgärder för att säkerställa skyddad åtkomst till ditt Blob Storage-konto.

> [!NOTE]
> I den här snabb starten används en äldre version av klient biblioteket för Azure Blob Storage. Information om hur du kommer igång med den senaste versionen finns i [snabb start: hantera blobbar med Java Script V12 SDK i en webbläsare](quickstart-blobs-javascript-browser.md).

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto. [Skapa ett lagrings konto](../common/storage-account-create.md).
- En lokal webb server. Den här artikeln använder [Node.js](https://nodejs.org) för att öppna en grundläggande Server.
- [Visual Studio Code](https://code.visualstudio.com).
- Ett VS Code-tillägg för fel sökning av webbläsare, till exempel [fel sökning för Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) eller [fel sökning för Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge).

## <a name="setting-up-storage-account-cors-rules"></a>Ställa in CORS-regler för lagringskonto

Innan ditt webb program kan komma åt en blob-lagring från klienten måste du konfigurera ditt konto för att aktivera [resurs delning mellan ursprung](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)eller CORS.

Återgå till Azure Portal och välj ditt lagringskonto. Om du vill definiera en ny CORS-regel går du till avsnittet **Inställningar** och klickar på **CORS** -länken. Klicka sedan på knappen **Lägg till** för att öppna fönstret **Lägg till CORS-regel**. För den här snabbstarten skapar du en öppen CORS-regel:

![CORS-inställningar för Azure Blob Storage-konto](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

I följande tabell beskrivs varje CORS-inställning och de värden som används för att definiera regeln.

|Inställning  |Värde  | Beskrivning |
|---------|---------|---------|
| Tillåtna ursprung | * | Accepterar en kommaavgränsad lista över domäner som är inställda som godkänt ursprung. Om du anger värdet till `*` ges alla domäner åtkomst till lagringskontot. |
| Tillåtna metoder     | delete, get, head, merge, post, options och put | Listar de HTTP-verb som kan köras mot lagringskontot. För den här snabbstarten väljer du alla tillgängliga alternativ. |
| Tillåtna rubriker | * | Definierar en lista över begäranderubriker (inklusive prefixrubriker) som tillåts av lagringskontot. Om du ställer in värdet på `*` får alla rubriker åtkomst. |
| Exponerade rubriker | * | Listar de svarsrubriker som tillåts av kontot. Om du ställer in värdet på `*` får kontot skicka alla rubriker.  |
| Högsta ålder (sekunder) | 86400 | Den maximala tidsperiod som webbläsaren cachelagrar den preliminära OPTIONS-begäran. Ett värde på *86400* innebär cachelagring under ett helt dygn. |

> [!IMPORTANT]
> Se till att alla inställningar som du använder i produktionen visar den lägsta mängd åtkomst som krävs för ditt lagrings konto för att upprätthålla säker åtkomst. De CORS-inställningar som beskrivs här är lämpliga för en snabbstart eftersom de innebär en flexibel säkerhetsprincip. Inställningarna rekommenderas dock inte i verkliga sammanhang.

Härnäst använder du Azure-molnskalet och skapar en säkerhetstoken.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Skapa en signatur för delad åtkomst

SAS, eller signatur för delad åtkomst, används av den kod som körs i webbläsaren för att auktorisera begäranden till bloblagring. Med hjälp av SAS kan klienten auktorisera åtkomst till lagringsresurser utan att ha åtkomstnyckel eller anslutningssträng för kontot. Mer information om SAS finns i [Använda signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).

Du kan skapa en SAS med hjälp av Azure CLI via Azure Cloud Shell eller med Azure Portal eller Azure Storage Explorer. I följande tabell beskrivs de parametrar du behöver ange värden för för att skapa en SAS med CLI.

| Parameter      |Beskrivning  | Platshållare |
|----------------|-------------|-------------|
| *förfallo*       | Förfallodatum för åtkomsttoken i formatet YYYY-MM-DD. Ange morgondagens datum under den här snabbstarten. | *FUTURE_DATE* |
| *konto namn* | Namnet på lagringskontot. Använd det namn som noterades i ett tidigare steg. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | Nyckeln till lagringskontot. Använd den nyckel som noterades i ett tidigare steg. | *YOUR_STORAGE_ACCOUNT_KEY* |

Använd följande CLI-kommando med faktiska värden för varje plats hållare för att skapa en SAS som du kan använda i JavaScript-koden.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

Du kan kanske uppleva serien med värden efter varje parameter som lite kryptisk. Dessa parametervärden hämtas från den första bokstaven i respektive behörighet. I följande tabell förklaras var värdena kommer från:

| Parameter        | Värde   | Beskrivning  |
|------------------|---------|---------|
| *åtkomst*    | racwdl  | Den här signaturen för delad åtkomst (SAS) kan *läsa*, *bifoga*, *skapa*, *skriva*, *ta bort* och *lista* funktioner. |
| *resource-types* | sco     | De resurser som påverkas av signaturen för delad åtkomst (SAS) är *tjänst*, *container* och *objekt*. |
| *Terminal*       | b       | Den tjänst som påverkas av signaturen för delad åtkomst är *blob*-tjänsten. |

Nu när SAS har skapats kopierar du returvärdet och sparar det någonstans för användning i ett kommande steg. Om du har genererat SAS med en annan metod än Azure CLI måste du ta bort den ursprungliga `?` om den finns. Det här tecknet är en URL-avgränsare som redan finns i URL-mallen senare i det här avsnittet där SAS används.

> [!IMPORTANT]
> I produktion ska du alltid skicka SAS-token med TLS. SAS-token bör dessutom genereras på servern och skickas till HTML-sidan för att återgå till Azure Blob Storage. En metod du kan överväga är att använda en serverlös funktion när du genererar SAS-tokens. I Azure-portalen finns funktionsmallar som ger möjligheten att generera en SAS med en JavaScript-funktion.

## <a name="implement-the-html-page"></a>Implementera HTML-sidan

I det här avsnittet ska du skapa en grundläggande webb sida och konfigurera VS Code för att starta och felsöka sidan. Innan du kan starta måste du dock använda Node.js för att starta en lokal webb server och betjäna sidan när webbläsaren begär det. Härnäst ska du lägga till JavaScript-kod för att anropa olika Blob Storage-API: er och visa resultaten på sidan. Du kan också se resultatet av dessa anrop i [Azure Portal](https://portal.azure.com), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer)och [Azure Storage tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) för vs Code.

### <a name="set-up-the-web-application"></a>Konfigurera webbprogrammet

Börja med att skapa en ny mapp med namnet *Azure-blobs-Java Script* och öppna den i vs Code. Skapa sedan en ny fil i VS Code, Lägg till följande HTML och spara den som *index.html* i mappen *Azure-blobs-Java Script* .

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>Konfigurera fel söknings programmet

Om du vill ställa in tillägget för fel sökning i VS Code väljer du **felsök > Lägg till konfiguration...** och väljer sedan **Chrome** eller **Edge**, beroende på vilket tillägg du installerade i avsnittet krav ovan. Den här åtgärden skapar en *launch.jspå* filen och öppnar den i redigeraren.

Ändra sedan *launch.jspå* filen så att `url` värdet inkluderar `/index.html` :

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

Den här konfigurationen talar om för VS Code vilken webbläsare som ska startas och vilken URL som ska läsas in.

### <a name="launch-the-web-server"></a>Starta webb servern

Starta den lokala Node.js webb servern genom att välja **visa > Terminal** för att öppna ett konsol fönster i vs Code, och ange sedan följande kommando.

```console
npx http-server
```

Med det här kommandot installeras *http-server* -paketet och servern startas, och den aktuella mappen blir tillgänglig via standard-URL: er, inklusive den som anges i föregående steg.

### <a name="start-debugging"></a>Starta fel sökning

Om du vill starta *index.html* i webbläsaren med vs Code-felsökaren, väljer du **Felsök > starta fel sökning** eller trycker på F5 i vs Code.

Det användar gränssnitt som visas gör inte något än, men du kommer att lägga till JavaScript-kod i följande avsnitt för att implementera varje funktion som visas. Du kan sedan ange Bryt punkter och interagera med fel söknings programmet när det har pausats på din kod.

När du gör ändringar i *index.html* måste du läsa in sidan igen för att se ändringarna i webbläsaren. I VS Code kan du också välja **felsök > starta om fel sökning** eller trycka på Ctrl + Shift + F5.

### <a name="add-the-blob-storage-client-library"></a>Lägg till klient biblioteket för Blob Storage

Om du vill aktivera anrop till Blob Storage-API: et måste [du först hämta klient biblioteket Azure Storage SDK för Java Script-BLOB](https://aka.ms/downloadazurestoragejsblob), extrahera innehållet i zip-filen och placera *azure-storage-blob.js* -filen i mappen *Azure-blobs-Java Script* .

Klistra sedan in följande HTML i *index.html* efter den `</body>` avslutande taggen och ersätt plats hållaren.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Den här koden lägger till en referens till skript filen och ger en plats för din egen JavaScript-kod. I den här snabb starten använder vi *azure-storage-blob.js* skript filen så att du kan öppna den i vs Code, läsa dess innehåll och ange Bryt punkter. I produktion bör du använda den mer kompakta *azure-storage.blob.min.js* -filen som också finns i zip-filen.

Du kan läsa mer om varje Blob Storage-funktion i [referens dokumentationen](/javascript/api/%40azure/storage-blob/index). Observera att vissa av funktionerna i SDK: n endast är tillgängliga i Node.js eller endast är tillgängliga i webbläsaren.

Koden i *azure-storage-blob.js* exporterar en global variabel som kallas `azblob` , som du kommer att använda i JavaScript-koden för att få åtkomst till API: erna för Blob Storage.

### <a name="add-the-initial-javascript-code"></a>Lägg till den inledande JavaScript-koden

Klistra sedan in följande kod i det `<script>` element som visas i det föregående kod blocket och ersätt plats hållaren.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

Den här koden skapar fält för varje HTML-element som följande kod kommer att använda och implementerar en `reportStatus` funktion för att visa utdata.

I följande avsnitt lägger du till varje nytt block med JavaScript-kod efter föregående block.

### <a name="add-your-storage-account-info"></a>Lägg till information om lagrings kontot

Lägg sedan till kod för att komma åt ditt lagrings konto och ersätt plats hållarna med ditt konto namn och de SAS som du skapade i föregående steg.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

I den här koden används konto information och SAS för att skapa en [ContainerURL](/javascript/api/@azure/storage-blob/ContainerURL) -instans, vilket är användbart för att skapa och ändra en lagrings behållare.

### <a name="create-and-delete-a-storage-container"></a>Skapa och ta bort en lagrings behållare

Lägg sedan till kod för att skapa och ta bort lagrings behållaren när du trycker på motsvarande knapp.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

Den här koden anropar funktionen ContainerURL [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) och [Delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) utan att använda en [avbrotts](/javascript/api/@azure/storage-blob/aborter) instans. För att det ska vara enkelt för den här snabb starten förutsätter den här koden att ditt lagrings konto har skapats och är aktiverat. I produktions kod använder du en avbrotts instans för att lägga till timeout-funktioner.

### <a name="list-blobs"></a>Lista blobar

Lägg sedan till kod för att visa innehållet i lagrings behållaren när du trycker på knappen **lista filer** .

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

Den här koden anropar funktionen [ContainerURL. listBlobFlatSegment](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) i en slinga för att säkerställa att alla segment hämtas. För varje segment loopar det över listan över BLOB-objekt som den innehåller och uppdaterar listan **filer** .

### <a name="upload-blobs"></a>Ladda upp blobbar

Lägg sedan till kod för att ladda upp filer till lagrings behållaren när du trycker på knappen **Välj och överför filer** .

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

Den här koden ansluter knappen **Välj och ladda upp filer** till det dolda `file-input` elementet. På så sätt `click` utlöser knapp händelsen fil indata `click` och visar fil väljaren. När du har valt filer och stängt dialog rutan `input` inträffar händelsen och `uploadFiles` funktionen anropas. Den här funktionen anropar den webbläsarbaserade [uploadBrowserDataToBlockBlob](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) -funktionen för varje fil som du har valt. Varje anrop returnerar ett löfte, som läggs till i en lista så att alla kan vänta samtidigt, vilket gör att filerna överförs parallellt.

### <a name="delete-blobs"></a>Ta bort blobar

Lägg sedan till kod för att ta bort filer från lagrings behållaren när du trycker på knappen **ta bort markerade filer** .

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

Den här koden anropar funktionen [BlobURL. Delete](/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) för att ta bort varje fil som marker ATS i listan. Sedan anropas `listFiles` funktionen som visas tidigare för att uppdatera innehållet i listan **filer** .

### <a name="run-and-test-the-web-application"></a>Kör och testa webb programmet

Nu kan du starta sidan och experimentera för att få en känsla för hur Blob Storage fungerar. Om några fel inträffar (till exempel när du försöker att lista filer innan du har skapat behållaren) visas fel meddelandet i fönstret **status** . Du kan också ange Bryt punkter i JavaScript-koden för att undersöka de värden som returneras av lagrings-API: erna.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa de resurser som skapades under den här snabb starten går du till [Azure Portal](https://portal.azure.com) och tar bort resurs gruppen som du skapade i avsnittet krav.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en enkel webbplats som har åtkomst till Blob Storage från webbläsarbaserat Java Script. Om du vill lära dig hur du kan vara värd för en webbplats på Blob Storage fortsätter du till följande självstudie:

> [!div class="nextstepaction"]
> [Var värd för en statisk webbplats på Blob Storage](./storage-blob-static-website-host.md)