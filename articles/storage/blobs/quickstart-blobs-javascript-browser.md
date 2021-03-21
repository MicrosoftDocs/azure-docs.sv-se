---
title: 'Snabb start: Azure Blob Storage-bibliotek V12 – java script i en webbläsare'
description: I den här snabb starten får du lära dig hur du använder Azure Blob Storage klient bibliotek version 12 för Java Script i en webbläsare. Du skapar en behållare och ett objekt i Blob Storage. Härnäst får du lära dig hur du visar alla blobar i en behållare. Slutligen får du lära dig hur du tar bort blobbar och tar bort en behållare.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 998d49e91d38a1f2fdc2503165ee99635e153027
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001906"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-a-browser"></a>Snabb start: hantera blobbar med Java Script V12 SDK i en webbläsare

Azure Blob Storage är optimerat för att lagra stora mängder ostrukturerade data. Blobbar är objekt som kan innehålla text eller binära data, inklusive bilder, dokument, strömmande media och Arkiv data. I den här snabb starten lär du dig att hantera blobbar med hjälp av Java Script i en webbläsare. Du överför och listar blobbar och skapar och tar bort behållare.

Ytterligare resurser:

* [Referensdokumentation för API](/javascript/api/@azure/storage-blob)
* [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Paket (NPM)](https://www.npmjs.com/package/@azure/storage-blob)
* [Exempel](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Förutsättningar

* [Ett Azure-konto med en aktiv prenumeration](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Ett Azure Storage konto](../common/storage-account-create.md)
* [Node.js](https://nodejs.org)
* [Microsoft Visual Studio Code](https://code.visualstudio.com)
* Ett Visual Studio Code-tillägg för fel sökning av webbläsare, till exempel:
    * [Fel sökare för Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge)
    * [Fel sökning för Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)
    * [Fel sökare för Firefox](https://marketplace.visualstudio.com/items?itemName=firefox-devtools.vscode-firefox-debug)


[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="object-model"></a>Objekt modell

I blobblagringen finns tre typer av resurser:

* Lagrings kontot
* En container på lagringskontot
* En BLOB i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

I den här snabb starten använder du följande JavaScript-klasser för att interagera med dessa resurser:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` klassen låter dig ändra Azure Storage-resurser och blob-behållare.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` klassen låter dig ändra Azure Storage behållare och deras blobbar.
* [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient): `BlockBlobClient` klassen låter dig ändra Azure Storage blobbar.

## <a name="setting-up"></a>Konfigurera

Det här avsnittet beskriver hur du förbereder ett projekt så att det fungerar med Azure Blob Storage-V12 för Java Script.

### <a name="create-a-cors-rule"></a>Skapa en CORS-regel

Innan ditt webb program kan komma åt Blob Storage från klienten måste du konfigurera ditt konto för att aktivera [resurs delning mellan ursprung](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)eller CORS.

I Azure Portal väljer du ditt lagrings konto. Om du vill definiera en ny CORS-regel går du till avsnittet **Inställningar** och väljer **CORS**. För den här snabbstarten skapar du en öppen CORS-regel:

![CORS-inställningar för Azure Blob Storage-konto](media/quickstart-blobs-javascript-browser/azure-blob-storage-cors-settings.png)

I följande tabell beskrivs varje CORS-inställning och de värden som används för att definiera regeln.

|Inställning  |Värde  | Beskrivning |
|---------|---------|---------|
| **TILLÅTNA URSPRUNG** | **\** _ | Accepterar en kommaavgränsad lista över domäner som är inställda som godkänt ursprung. Om du anger värdet till `_` ges alla domäner åtkomst till lagringskontot. |
| **TILLÅTNA METODER** | **Ta bort**, **Hämta**, **huvud**, **slå samman**, **anslå**, **alternativ** och **Placera** | Listar de HTTP-verb som kan köras mot lagringskontot. För den här snabbstarten väljer du alla tillgängliga alternativ. |
| **TILLÅTNA HUVUDEN** | **\** _ | Definierar en lista över begäranderubriker (inklusive prefixrubriker) som tillåts av lagringskontot. Om du ställer in värdet på `_` får alla rubriker åtkomst. |
| **EXPONERADE RUBRIKER** | **\** _ | Listar de svarsrubriker som tillåts av kontot. Om du ställer in värdet på `_` får kontot skicka alla rubriker. |
| **HÖGSTA ÅLDER** | **86400** | Den maximala tid som webbläsaren cachelagrar preflight OPTIONs-begäran på några sekunder. Ett värde på *86400* innebär cachelagring under ett helt dygn. |

När du har fyllt i fälten med värdena från den här tabellen klickar du på knappen **Spara** .

> [!IMPORTANT]
> Se till att alla inställningar som du använder i produktionen visar den lägsta mängd åtkomst som krävs för ditt lagrings konto för att upprätthålla säker åtkomst. De CORS-inställningar som beskrivs här är lämpliga för en snabbstart eftersom de innebär en flexibel säkerhetsprincip. Inställningarna rekommenderas dock inte i verkliga sammanhang.

### <a name="create-a-shared-access-signature"></a>Skapa en signatur för delad åtkomst

Signaturen för delad åtkomst (SAS) används av kod som körs i webbläsaren för att auktorisera Azure Blob Storage-begäranden. Med hjälp av SAS kan klienten auktorisera åtkomst till lagringsresurser utan att ha åtkomstnyckel eller anslutningssträng för kontot. Mer information om SAS finns i [Använda signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).

Följ dessa steg för att hämta Blob Service SAS-URL:

1. I Azure Portal väljer du ditt lagrings konto.
2. Navigera till avsnittet **Inställningar** och välj **signatur för delad åtkomst**.
3. Rulla nedåt och klicka på knappen **generera SAS och anslutnings sträng** .
4. Rulla nedåt och leta upp fältet **BLOB service SAS URL**
5. Klicka på knappen **Kopiera till Urklipp** längst ned till höger i fältet **BLOB service SAS URL** .
6. Spara den kopierade URL: en någonstans för användning i ett kommande steg.

### <a name="add-the-azure-blob-storage-client-library"></a>Lägg till klient biblioteket för Azure Blob Storage

På den lokala datorn skapar du en ny mapp med namnet *Azure-blobs-JS-webbläsare* och öppnar den i Visual Studio Code.

Välj **visa > Terminal** för att öppna ett konsol fönster i Visual Studio Code. Kör följande Node.js Package Manager (NPM)-kommando i terminalfönstret för att skapa en [package.jspå](https://docs.npmjs.com/files/package.json) en fil.

```console
npm init -y
```

Azure SDK består av många separata paket. Du kan välja vilka paket du behöver utifrån de tjänster som du vill använda. Kör följande `npm` kommando i terminalfönstret för att installera `@azure/storage-blob` paketet.

```console
npm install --save @azure/storage-blob
```

#### <a name="bundle-the-azure-blob-storage-client-library"></a>Paketera klient biblioteket för Azure Blob Storage

Om du vill använda Azure SDK-bibliotek på en webbplats konverterar du koden så att den fungerar i webbläsaren. Du gör detta med hjälp av ett verktyg som kallas för en insamlare. Sammanslagen använder JavaScript-kod som skrivits med [Node.js](https://nodejs.org) konventioner och konverterar den till ett format som förstås av webbläsare. I den här snabb starts artikeln används [paket](https://parceljs.org/) insamlingen.

Installera paket genom att köra följande `npm` kommando i terminalfönstret:

```console
npm install -g parcel-bundler
```

Öppna filen *package.jspå* fil i Visual Studio Code och Lägg till en `browserlist` mellan `license` posterna och `dependencies` . Detta `browserlist` riktar sig till den senaste versionen av tre populära webbläsare. Den fullständiga *package.jsi* filen bör nu se ut så här:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/package.json" highlight="12-16":::

Spara *package.jspå* filen.

### <a name="import-the-azure-blob-storage-client-library"></a>Importera klient biblioteket för Azure Blob Storage

Importera paketet om du vill använda Azure SDK-bibliotek i Java Script `@azure/storage-blob` . Skapa en ny fil i Visual Studio Code som innehåller följande JavaScript-kod.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ImportLibrary":::

Spara filen som *index.js* i katalogen *Azure-blobs – JS-Browser* .

### <a name="implement-the-html-page"></a>Implementera HTML-sidan

Skapa en ny fil i Visual Studio Code och Lägg till följande HTML-kod.

:::code language="html" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.html":::

Spara filen som *index.html* i mappen *Azure-Blob-JS-JS-Browser* .

## <a name="code-examples"></a>Kodexempel

Exempel koden visar hur du utför följande uppgifter med Azure Blob Storage-klient biblioteket för Java Script:

* [Deklarera fält för GRÄNSSNITTs element](#declare-fields-for-ui-elements)
* [Lägg till information om lagrings kontot](#add-your-storage-account-info)
* [Skapa klientobjekt](#create-client-objects)
* [Skapa och ta bort en lagrings behållare](#create-and-delete-a-storage-container)
* [Lista blobar](#list-blobs)
* [Ladda upp blobbar](#upload-blobs)
* [Ta bort blobar](#delete-blobs)

Du kör koden när du har lagt till alla kodfragment i *index.js* -filen.

### <a name="declare-fields-for-ui-elements"></a>Deklarera fält för GRÄNSSNITTs element

Lägg till följande kod i slutet av *index.js* -filen.

:::code language="JavaScript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeclareVariables":::

Spara *index.js* -filen.

Den här koden deklarerar fält för varje HTML-element och implementerar en `reportStatus` funktion för att visa utdata.

I följande avsnitt lägger du till varje nytt block med JavaScript-kod efter föregående block.

### <a name="add-your-storage-account-info"></a>Lägg till information om lagrings kontot

Lägg till kod för att komma åt ditt lagrings konto. Ersätt plats hållaren med din Blob Service SAS-URL som du skapade tidigare. Lägg till följande kod i slutet av *index.js* -filen.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_StorageAcctInfo":::

Spara *index.js* -filen.

### <a name="create-client-objects"></a>Skapa klientobjekt

Skapa [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) -och [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) -objekt för att interagera med Azure Blob Storage-tjänsten. Lägg till följande kod i slutet av *index.js* -filen.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateClientObjects":::

Spara *index.js* -filen.

### <a name="create-and-delete-a-storage-container"></a>Skapa och ta bort en lagrings behållare

Skapa och ta bort lagrings behållaren när du klickar på motsvarande knapp på webb sidan. Lägg till följande kod i slutet av *index.js* -filen.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateDeleteContainer":::

Spara *index.js* -filen.

### <a name="list-blobs"></a>Lista blobar

Ange innehållet i lagrings behållaren när du klickar på knappen **lista filer** . Lägg till följande kod i slutet av *index.js* -filen.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ListBlobs":::

Spara *index.js* -filen.

Den här koden anropar funktionen [ContainerClient. listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) och använder sedan en iterator för att hämta namnet på varje [BlobItem](/javascript/api/@azure/storage-blob/blobitem) som returneras. För varje `BlobItem` uppdateras **fil** listan med [namnet](/javascript/api/@azure/storage-blob/blobitem#name) egenskap svärdet.

### <a name="upload-blobs"></a>Ladda upp blobbar

Ladda upp filer till lagrings behållaren när du klickar på knappen **Välj och överför filer** . Lägg till följande kod i slutet av *index.js* -filen.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_UploadBlobs":::

Spara *index.js* -filen.

Den här koden ansluter knappen **Välj och ladda upp filer** till det dolda `file-input` elementet. Knapp `click` händelsen utlöser fil indata- `click` händelsen och visar fil väljaren. När du har valt filer och stängt dialog rutan `input` inträffar händelsen och `uploadFiles` funktionen anropas. Den här funktionen skapar ett [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) -objekt och anropar sedan funktionen endast [uploadBrowserData](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) för varje fil som du har valt. Varje anrop returnerar en `Promise` . Varje `Promise` läggs till i en lista så att de kan avvisas tillsammans, vilket gör att filerna överförs parallellt.

### <a name="delete-blobs"></a>Ta bort blobar

Ta bort filer från lagrings behållaren när du klickar på knappen **ta bort markerade filer** . Lägg till följande kod i slutet av *index.js* -filen.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeleteBlobs":::

Spara *index.js* -filen.

Den här koden anropar funktionen [ContainerClient. deleteBlob](/javascript/api/@azure/storage-blob/containerclient#deleteblob-string--blobdeleteoptions-) för att ta bort varje fil som marker ATS i listan. Sedan anropas `listFiles` funktionen som visas tidigare för att uppdatera innehållet i listan **filer** .

## <a name="run-the-code"></a>Kör koden

Om du vill köra koden i Visual Studio Code debugger konfigurerar du *launch.jspå* filen för din webbläsare.

### <a name="configure-the-debugger"></a>Konfigurera fel söknings programmet

Så här konfigurerar du tillägget för fel sökning i Visual Studio Code:

1. Välj **kör > Lägg till konfiguration**
2. Välj **Edge**, **Chrome** eller **Firefox**, beroende på vilket tillägg du installerade i avsnittet [krav](#prerequisites) ovan.

Om du lägger till en ny konfiguration skapas en *launch.jspå* filen och den öppnas i redigeraren. Ändra *launch.jspå* filen så att `url` värdet är `http://localhost:1234/index.html` , som du ser här:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/.vscode/launch.json" highlight="11":::

När du har uppdaterat sparar du *launch.jspå* filen. Den här konfigurationen visar Visual Studio Code vilken webbläsare som ska öppnas och vilken URL som ska läsas in.

### <a name="launch-the-web-server"></a>Starta webb servern

Starta den lokala utvecklings webb servern genom att välja **visa > Terminal** för att öppna ett konsol fönster i Visual Studio Code och ange sedan följande kommando.

```console
parcel index.html
```

Skiftena paketerar koden och startar en lokal utvecklings Server för din sida på `http://localhost:1234/index.html` . Ändringar som du gör i *index.js* skapas automatiskt och avspeglas på utvecklings servern när du sparar filen.

Om du får ett meddelande om att **det inte gick att använda den konfigurerade port 1234** kan du ändra porten genom att köra kommandot `parcel -p <port#> index.html` . I *launch.jspå* filen uppdaterar du porten i URL-sökvägen så att den matchar.

### <a name="start-debugging"></a>Starta fel sökning

Kör sidan i fel söknings programmet och få en känsla för hur Blob Storage fungerar. Om några fel inträffar visas fel meddelandet i **status** fönstret på webb sidan.

Öppna *index.html* i webbläsaren med Visual Studio Code-felsökaren ansluten genom att välja **Kör > starta fel sökning** eller tryck på F5 i Visual Studio Code.

### <a name="use-the-web-app"></a>Använda webbapp

I [Azure Portal](https://portal.azure.com)kan du verifiera resultatet av API-anropen när du följer stegen nedan.

#### <a name="step-1---create-a-container"></a>Steg 1 – Skapa en behållare

1. I webbapp väljer du **skapa behållare**. Statusen anger att en behållare har skapats.
2. Om du vill verifiera i Azure Portal väljer du ditt lagrings konto. Välj **containrar** under **Blob Service**. Kontrol lera att den nya behållaren visas. (Du kan behöva välja **Uppdatera**.)

#### <a name="step-2---upload-a-blob-to-the-container"></a>Steg 2 – Ladda upp en blob till behållaren

1. På den lokala datorn skapar du och sparar en test fil, till exempel *test.txt*.
2. I webbappen klickar du på **Välj och ladda upp filer**.
3. Bläddra till test filen och välj sedan **Öppna**. Statusen anger att filen laddades upp och att fil listan hämtades.
4. I Azure Portal väljer du namnet på den nya behållare som du skapade tidigare. Kontrol lera att test filen visas.

#### <a name="step-3---delete-the-blob"></a>Steg 3 – ta bort blobben

1. I webbapp väljer du test filen under **filer**.
2. Välj **ta bort markerade filer**. Statusen anger att filen har tagits bort och att behållaren inte innehåller några filer.
3. I Azure Portal väljer du **Uppdatera**. Kontrol lera att det **inte finns några blobbar**.

#### <a name="step-4---delete-the-container"></a>Steg 4 – ta bort behållaren

1. I webbapp väljer du **ta bort behållare**. Statusen anger att behållaren har tagits bort.
2. I Azure Portal väljer du **\<account-name\> | Behållare** -länk längst upp till vänster i Portal fönstret.
3. Välj **Uppdatera**. Den nya behållaren försvinner.
4. Stäng webb programmet.

### <a name="clean-up-resources"></a>Rensa resurser

Klicka på **Terminal** -konsolen i Visual Studio Code och tryck på CTRL + C för att stoppa webb servern.

Om du vill rensa de resurser som skapades under den här snabb starten går du till [Azure Portal](https://portal.azure.com) och tar bort resurs gruppen som du skapade i avsnittet [krav](#prerequisites) .

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig att ladda upp, lista och ta bort blobar med hjälp av Java Script. Du har också lärt dig hur du skapar och tar bort en Blob Storage-behållare.

För självstudier, exempel, snabb starter och annan dokumentation går du till:

> [!div class="nextstepaction"]
> [Dokumentation om Azure för Java Script](/azure/developer/javascript/)

* Mer information finns i [klient biblioteket för Azure Blob Storage för Java Script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
* Om du vill se exempel appar för Blob Storage kan du fortsätta till [Azure Blob Storage-V12 JavaScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).