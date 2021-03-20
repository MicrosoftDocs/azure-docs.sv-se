---
title: Aktivera och hantera BLOB-versioner
titleSuffix: Azure Storage
description: Lär dig hur du aktiverar BLOB-versioner i Azure Portal eller genom att använda en Azure Resource Manager mall.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5b6bd16eacf4b1bbb7b93f5500813e7fa9dc7eef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100095855"
---
# <a name="enable-and-manage-blob-versioning"></a>Aktivera och hantera BLOB-versioner

Du kan aktivera Blob Storage-versioner för att automatiskt underhålla tidigare versioner av en BLOB när den ändras eller tas bort. När BLOB-versioner har Aktiver ATS kan du återställa en tidigare version av en BLOB för att återställa dina data om de felaktigt ändras eller tas bort.

Den här artikeln visar hur du aktiverar eller inaktiverar BLOB-versioner för lagrings kontot med hjälp av Azure Portal eller en Azure Resource Manager mall. Mer information om BLOB-versioner finns i [BLOB-versioner](versioning-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Aktivera blobversionshantering

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Så här aktiverar du Blob-versioner för ett lagrings konto i Azure Portal:

1. Navigera till ditt lagrings konto i portalen.
1. Under **BLOB service** väljer du **data skydd**.
1. I avsnittet **versions hantering** väljer du **aktive rad**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Skärm bild som visar hur du aktiverar BLOB-versioner i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill aktivera BLOB-versioner för ett lagrings konto med PowerShell installerar du först [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) module version 2.3.0 eller senare. Anropa sedan kommandot [Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) för att aktivera versions hantering, som du ser i följande exempel. Kom ihåg att ersätta värdena i vinkelparenteser med dina egna värden:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill aktivera BLOB-versioner för ett lagrings konto med Azure CLI måste du först installera Azure CLI version 2.2.0 eller senare. Anropa sedan kommandot [AZ Storage Account BLOB-service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) för att aktivera versions hantering, som du ser i följande exempel. Kom ihåg att ersätta värdena i vinkelparenteser med dina egna värden:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Mall](#tab/template)

Om du vill aktivera BLOB-versioner med en mall skapar du en mall med egenskapen **IsVersioningEnabled** till **True**. Följande steg beskriver hur du skapar en mall i Azure Portal.

1. I Azure Portal väljer du **skapa en resurs**.
1. I **Sök på Marketplace** skriver du **mall distribution** och trycker sedan på **RETUR**.
1. Välj **malldistribution**, Välj **skapa** och välj sedan **skapa en egen mall i redigeraren**.
1. I redigeraren för mallar klistrar du in följande JSON. Ersätt platshållaren `<accountName>` med namnet på ditt lagringskonto.
1. Spara mallen.
1. Ange resurs gruppen för kontot och välj sedan knappen **köp** för att distribuera mallen och aktivera BLOB-versioner.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Mer information om hur du distribuerar resurser med mallar i Azure Portal finns i [distribuera resurser med Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Ändra en BLOB för att utlösa en ny version

Följande kod exempel visar hur du utlöser skapandet av en ny version med Azure Storage-klient biblioteket för .NET, version [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) eller senare. Kontrol lera att du har aktiverat versions hantering för ditt lagrings konto innan du kör det här exemplet.

Exemplet skapar en Block-Blob och uppdaterar sedan blobens metadata. Uppdatering av blobens metadata utlöser skapandet av en ny version. Exemplet hämtar den ursprungliga versionen och den aktuella versionen och visar att endast den aktuella versionen innehåller metadata.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Visa lista över BLOB-versioner

Om du vill visa BLOB-versioner eller ögonblicks bilder med klient biblioteket för .NET-V12 anger du parametern [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) med **versions** fältet.

I följande kod exempel visas hur du listar BLOB-versioner med Azure Storage-klient biblioteket för .NET, version [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) eller senare. Kontrol lera att du har aktiverat versions hantering för ditt lagrings konto innan du kör det här exemplet.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Nästa steg

- [BLOB-versioner](versioning-overview.md)
- [Mjuk borttagning för Azure Storage-blobar](./soft-delete-blob-overview.md)