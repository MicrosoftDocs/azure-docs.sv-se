---
title: Överföra avbildningsdata i molnet med Azure Storage | Microsoft Docs
description: Använd Azure Blob Storage med Web Apps för att lagra appdata till ett lagrings konto. Den här självstudien skapar en webbapp som lagrar och visar bilder från Azure Storage.
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b50aadfa16ed95bacb5247187c15489a1b017d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676598"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Självstudie: Ladda upp bilddata i molnet med Azure Storage

Den här självstudien ingår i en serie. I den här självstudien får du lära dig hur du distribuerar en webbapp. Webbapp använder klient biblioteket för Azure Blob Storage för att ladda upp avbildningar till ett lagrings konto. När du är klar har du en webbapp som lagrar och visar bilder från Azure Storage.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

![Bild storleks program i .NET](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

![Bild storleks program i Java Script](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]

> * Skapa ett lagringskonto
> * Skapar en container och anger behörigheter
> * Hämta en åtkomstnyckel
> * Distribuera en webbapp till Azure
> * Konfigurera appinställningar
> * Interagera med webbappen

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen. Skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Installera och Använd CLI lokalt genom att köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.  

I följande exempel skapas en resursgrupp med namnet `myResourceGroup`.

```azurecli
az group create --name myResourceGroup --location southeastasia
```

```powershell
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Exemplet överför avbildningar till en BLOB-behållare i ett Azure Storage-konto. Ett Azure-lagringskonto tillhandahåller en unik namnrymd där du kan lagra och få åtkomst till dina Azure-lagringdataobjekt. Skapa ett lagringskonto i resursgruppen du skapade med hjälp av kommandot [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> I del 2 i självstudien använder du Azure Event Grid med Blob Storage. Se till att skapa ditt lagringskonto i en Azure-region som har stöd för Event Grid. En lista över regioner som stöds finns i [Azure-produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

I följande kommando ersätter du ditt globalt unika namn för det Blob Storage-konto där platshållaren `<blob_storage_account>` visas.

```azurecli
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

```powershell
$blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia `
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Skapa Blob Storage-containrar

Appen använder två containrar i bloblagringskontot. Containrar liknar mappar och lagrar blobar. Det är till containern *avbildningar* som appen överför högupplösta bilder. I en senare del av självstudierna överför en funktionsapp i Azure ändrade miniatyrbilder till containern för *miniatyrer*.

Hämta nyckeln till lagringskontot med kommandot [az storage account keys list](/cli/azure/storage/account/keys). Sedan använder du den här nyckeln för att skapa två containrar med kommandot [az storage container create](/cli/azure/storage/container).

Offentlig åtkomst för containern *avbildningar* har angetts till `off`. Offentlig åtkomst för containern *miniatyrer* har angetts till `container`. Inställningen för offentlig åtkomst för `container` tillåter att användare som besöker webbplatsen visar miniatyrbilderna.

```bash
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey --public-access container
```

Anteckna namnet och nyckeln till ditt Blob Storage-konto. Exempelappen använder dessa inställningar för att ansluta till lagringskontot för att ladda upp bilderna. 

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

En [App Service-plan](../../app-service/overview-hosting-plans.md) anger plats, storlek och funktioner för webbservergruppen som är värd för din app.

Skapa en App Service-plan med kommandot [az appservice plan create](/cli/azure/appservice/plan).

I följande exempel skapas en App Service-plan med namnet `myAppServicePlan` på prisnivån **Kostnadsfri**:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

```powershell
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Skapa en webbapp

Webbappen tillhandahåller ett värdutrymme för exempelappens kod som distribueras på GitHub-exempellagringsplatsen. Skapa en [webbapp](../../app-service/overview.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp).  

I följande kommando ersätter du `<web_app>` med ett unikt namn. Giltiga tecken är `a-z`, `0-9` och `-`. Om `<web_app>` inte är unikt får du ett fel meddelande: det *`<web_app>` finns redan en webbplats med det namnet.* Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`.  

```azurecli
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

```powershell
$webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Distribuera exempelappen från GitHub-lagringsplatsen

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

App Service har stöd för flera olika sätt att distribuera innehåll till en webbapp. I de här självstudierna distribuerar du webbappen från en [offentlig GitHub exempellagringsplats](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Konfigurera lokal Git-distribution till webbappen med kommandot [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source).

Exempelprojektet innehåller en [ASP.NET MVC](https://www.asp.net/mvc)-app. Appen accepterar en bild, sparar den till ett lagringskonto och visar bilder från en container med miniatyrer. Webbappen använder [Azure. Storage](/dotnet/api/azure.storage), [Azure. Storage. blobar](/dotnet/api/azure.storage.blobs)och [Azure. Storage. blobar.](/dotnet/api/azure.storage.blobs.models) namn rymder för modeller för att interagera med den Azure Storage tjänsten.

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

App Service har stöd för flera olika sätt att distribuera innehåll till en webbapp. I de här självstudierna distribuerar du webbappen från en [offentlig GitHub exempellagringsplats](https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs). Konfigurera lokal Git-distribution till webbappen med kommandot [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source).

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

---

## <a name="configure-web-app-settings"></a>Konfigurera inställningarna för webbappen

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

Webbappens exempel använder [Azure Storage-API: er för .net](/dotnet/api/overview/azure/storage) för att ladda upp avbildningar. Autentiseringsuppgifterna för lagrings kontot anges i app-inställningarna för webbappen. Lägg till appinställningar till den distribuerade appen med kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AzureStorageConfig__AccountName=$blobStorageAccount `
    AzureStorageConfig__ImageContainer=images `
    AzureStorageConfig__ThumbnailContainer=thumbnails `
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Exempel webb programmet använder [Azure Storage klient bibliotek för Java Script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage) för att ladda upp bilder. Autentiseringsuppgifterna för lagrings kontot anges i app-inställningarna för webbappen. Lägg till appinställningar till den distribuerade appen med kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

När du har distribuerat och konfigurerat webbappen kan du testa funktionen för bilduppladdning i appen.

## <a name="upload-an-image"></a>Ladda upp en avbildning

Om du vill testa webbappen bläddrar du till URL-adressen till din publicerade app. Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

Välj region för att **Ladda upp foton** för att ange och ladda upp en fil eller dra en fil till regionen. Bilden försvinner om överföringen lyckas. Avsnittet **genererade miniatyrer** förblir tomt tills vi testar det senare i den här självstudien.

![Ladda upp foton i .NET](media/storage-upload-process-images/figure1.png)

I exempel koden `UploadFileToStorage` används uppgiften i filen *Storagehelper. cs* för att ladda upp avbildningarna till behållaren *avbildningar* i lagrings kontot med hjälp av [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) -metoden. Följande kodexempel innehåller aktiviteten `UploadFileToStorage`.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

Följande klasser och metoder som används i den föregående aktiviteten:

| Klass | Metod |
|-------|--------|
| [URI](/dotnet/api/system.uri) | [URI-konstruktor](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential (sträng, sträng) konstruktor](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

Välj **Välj fil** för att välja en fil, och klicka sedan på **Ladda upp bild**. Avsnittet **genererade miniatyrer** förblir tomt tills vi testar det senare i den här självstudien.

![Ladda upp foton i Node.js](media/storage-upload-process-images/upload-app-nodejs.png)

I exempelkoden ansvarar vägen `post` för att ladda upp bilden till en blob-container. Flödet använder moduler för att bearbeta uppladdningen:

- [multer](https://github.com/expressjs/multer) implementerar överförings strategin för väg hanteraren.
- [i-Stream](https://github.com/sindresorhus/into-stream) konverterar bufferten till en ström som krävs av [uploadStream](/javascript/api/%40azure/storage-blob/blockblobclient#uploadstream-readable--number--number--blockblobuploadstreamoptions-).

När filen skickas till vägen förblir filens innehåll i minnet tills filen har laddats upp till blob-containern.

> [!IMPORTANT]
> Om stora filer läses in i minnet kan det ha en negativ inverkan på webbappens prestanda. Om du förväntar dig att användare publicerar stora filer kan du överväga att mellanlagra filer i webbserverns filsystem och sedan schemalägga uppladdningar till blob-lagringen. När filerna finns i blob-lagringen kan du ta bort dem från serverfilsystemet.

```javascript
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}

const {
  BlobServiceClient,
  StorageSharedKeyCredential,
  newPipeline
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const containerName1 = 'thumbnails';
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName2 = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };

const sharedKeyCredential = new StorageSharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = newPipeline(sharedKeyCredential);

const blobServiceClient = new BlobServiceClient(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, '');
  return `${identifier}-${originalName}`;
};

router.get('/', async (req, res, next) => {

  let viewData;

  try {
    const containerClient = blobServiceClient.getContainerClient(containerName1);
    const listBlobsResponse = await containerClient.listBlobFlatSegment();

    for await (const blob of listBlobsResponse.segment.blobItems) {
      console.log(`Blob: ${blob.name}`);
    }

    viewData = {
      title: 'Home',
      viewName: 'index',
      accountName: process.env.AZURE_STORAGE_ACCOUNT_NAME,
      containerName: containerName1
    };

    if (listBlobsResponse.segment.blobItems.length) {
      viewData.thumbnails = listBlobsResponse.segment.blobItems;
    }
  } catch (err) {
    viewData = {
      title: 'Error',
      viewName: 'error',
      message: 'There was an error contacting the blob storage container.',
      error: err
    };
    res.status(500);
  } finally {
    res.render(viewData.viewName, viewData);
  }
});

router.post('/', uploadStrategy, async (req, res) => {
  const blobName = getBlobName(req.file.originalname);
  const stream = getStream(req.file.buffer);
  const containerClient = blobServiceClient.getContainerClient(containerName2);;
  const blockBlobClient = containerClient.getBlockBlobClient(blobName);

  try {
    await blockBlobClient.uploadStream(stream,
      uploadOptions.bufferSize, uploadOptions.maxBuffers,
      { blobHTTPHeaders: { blobContentType: "image/jpeg" } });
    res.render('success', { message: 'File uploaded to Azure Blob storage.' });
  } catch (err) {
    res.render('error', { message: err.message });
  }
});

module.exports = router;
```

---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Kontrollera att avbildningen visas på lagringskontot

Logga in på [Azure-portalen](https://portal.azure.com). I den vänstra menyn väljer du **Lagringskonton** och sedan namnet på ditt lagringskonto. Välj **behållare** och välj sedan behållaren **avbildningar** .

Kontrollera att avbildningen visas i containern.

![Azure Portal lista över avbildnings behållare](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Testa miniatyrvisning

Om du vill testa miniatyrvisning laddar du upp en bild till containern **miniatyrer** för att kontrollera huruvida appen kan läsa containern **miniatyrer**.

Logga in på [Azure-portalen](https://portal.azure.com). I den vänstra menyn väljer du **Lagringskonton** och sedan namnet på ditt lagringskonto. Välj **behållare** och välj sedan behållaren **miniatyrer** . Välj **Överför** för att öppna fönstret **Överför blobb**.

Välj en fil med filväljaren och välj **Ladda upp**.

Gå tillbaka till din app för att kontrollera att avbildningen som har överförts till containern **Miniatyrer** syns.

# <a name="net-v12"></a>[\.NET-V12](#tab/dotnet)

![.NET Image höjder-appen med ny bild visas](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript-V12](#tab/javascript)

![Node.js bild storleks ändrings app med ny bild som visas](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

I del två i serien automatiserar du skapandet av miniatyrbilden så du inte behöver den här bilden. I behållaren **miniatyrer** väljer du den avbildning du överförde och väljer **ta bort** för att ta bort avbildningen.

Du kan aktivera Content Delivery Network (CDN) för att cachelagra innehåll från ditt Azure Storage-konto. Mer information finns i [integrera ett Azure Storage-konto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Nästa steg

I del ett av serien lärde du dig att konfigurera en webbapp för att interagera med lagring.

Gå vidare till del två i serien och lär dig använda Event Grid för att utlösa en Azure-funktion för att ändra storlek på en bild.

> [!div class="nextstepaction"]
> [Använd Event Grid för att utlösa en Azure-funktion för att ändra storlek på en överförd avbildning](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
