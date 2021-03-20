---
title: Säker åtkomst till program data
titleSuffix: Azure Storage
description: Skydda ditt programs data i molnet med SAS-token, kryptering och HTTPS.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: tamram
ms.reviewer: ozgun
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: f7c5dbaf30965fdd5f438f0351cfa2cd60e05b70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746564"
---
# <a name="secure-access-to-application-data"></a>Säker åtkomst till program data

Den här självstudiekursen är den tredje delen i en serie. Du lär dig hur du skyddar åtkomsten till lagringskontot. 

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * Använder SAS-token för att komma åt miniatyrbilder
> * Aktiverar kryptering på serversidan
> * Aktiverar endast HTTPS-transport

[Azure Blob Storage](../common/storage-introduction.md#blob-storage) tillhandahåller en robust tjänst för att lagra filer för program. Den här självstudiekursen kompletterar [det föregående avsnittet ][previous-tutorial] och visar hur du skyddar åtkomsten till ditt lagringskonto från ett webbprogram. När du är klar är bilderna krypterade och i webbappen används säkra SAS-token för att få åtkomst till miniatyrbilderna.

## <a name="prerequisites"></a>Förutsättningar

För att kunna utföra den här kursen måste du ha slutfört den föregående Storage-självstudiekursen: [Automatisera storleksändring av överförda bilder med Event Grid][previous-tutorial].

## <a name="set-container-public-access"></a>Ange offentlig åtkomst till containrar

I den här delen av kursserien används SAS-token för att få åtkomst till miniatyrbilder. I det här steget anger du offentlig åtkomst för containern med *miniatyrer* till `off`.

```bash
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

```powershell
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission `
    --account-name $blobStorageAccount `
    --account-key $blobStorageAccountKey `
    --name thumbnails `
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>Konfigurera SAS-token för miniatyrbilder

I den första delen i den här kursserien visade webbprogrammet bilder från en offentlig container. I den här delen av serien använder du SAS-token (Shared Access Signatures) för att hämta miniatyr bilderna. SAS-token ger dig begränsad åtkomst till en container eller blob baserat på IP-protokoll, tidsintervall eller rättigheter. Mer information om SAS finns i [bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).

I det här exemplet använder lagringsplatsen för källkod `sasTokens`-grenen, som har ett uppdaterat kodexempel. Ta bort den befintliga GitHub-distributionen med [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Konfigurera sedan GitHub-distributionen till webbappen med kommandot [az webapp deployment source config](/cli/azure/webapp/deployment/source).

I följande kommando är `<web-app>` namnet på din webbapp.

```bash
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> `
    --resource-group myResourceGroup --branch sasTokens --manual-integration `
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

`sasTokens`-grenen av lagringsplatsen uppdaterar filen `StorageHelper.cs`. Den ersätter aktiviteten `GetThumbNailUrls` med kodexemplet nedan. Den uppdaterade aktiviteten hämtar miniatyr webb adresser genom att använda en [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) för att ange start tid, förfallo tid och behörigheter för SAS-token. När webbappen har distribuerats hämtas nu miniatyrbilderna med en URL med hjälp av en SAS-token. Den uppdaterade aktiviteten visas i följande exempel:

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

Följande klasser, egenskaper och metoder används i den föregående aktiviteten:

| Klass | Egenskaper | Metoder |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [URI](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Finns](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [Namn](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [Query](/dotnet/api/system.uribuilder.query) |  |
|[Lista](/dotnet/api/system.collections.generic.list-1) | | [Lägg till](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="azure-storage-encryption"></a>Azure Storage-kryptering

[Azure Storage kryptering](../common/storage-service-encryption.md) hjälper dig att skydda och skydda dina data genom att kryptera data i vila och genom att hantera kryptering och dekryptering. Alla data krypteras med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), ett av de starkaste blockchiffer som finns.

Du kan välja att låta Microsoft hantera krypterings nycklar, eller så kan du ta med dina egna nycklar med Kundhanterade nycklar som lagras i Azure Key Vault eller Key Vault-hanterad maskin varu säkerhets modell (HSM) (för hands version). Mer information finns i [kund hanterade nycklar för Azure Storage kryptering](../common/customer-managed-keys-overview.md).

Azure Storage kryptering krypterar automatiskt data i alla prestanda nivåer (standard och Premium), alla distributions modeller (Azure Resource Manager och klassisk) och alla Azure Storage-tjänster (BLOB, kö, tabell och fil).

## <a name="enable-https-only"></a>Aktivera endast HTTPS

Du kan se till att begäranden om data till och från ett lagringskonto skyddas genom att begäranden begränsas till endast HTTPS. Uppdatera protokollet som krävs för lagringskontot med kommandot [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Testa anslutningen med hjälp av `curl` med protokollet `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Nu när säker överföring krävs får du följande meddelande:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Nästa steg

I den tredje delen i serien fick du lära dig hur du skyddar åtkomsten till lagringskontot, till exempel hur du:

> [!div class="checklist"]
> * Använder SAS-token för att komma åt miniatyrbilder
> * Aktiverar kryptering på serversidan
> * Aktiverar endast HTTPS-transport

Gå vidare till den fjärde delen i serien där du får lära dig hur du övervakar och felsöker ett molnlagringsprogram.

> [!div class="nextstepaction"]
> [Övervaka och felsöka molnlagringsprogram](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
