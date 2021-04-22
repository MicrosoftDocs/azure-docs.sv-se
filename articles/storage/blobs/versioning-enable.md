---
title: Aktivera och hantera versionshantering av blobar
titleSuffix: Azure Storage
description: Lär dig hur du aktiverar blobversionshantering i Azure Portal eller med hjälp av en Azure Resource Manager mall.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f6a1d315342ea98ccaf1382630eccca876ada3f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870201"
---
# <a name="enable-and-manage-blob-versioning"></a>Aktivera och hantera versionshantering av blobar

Du kan aktivera versionshantering för Blob Storage för att automatiskt underhålla tidigare versioner av en blob när den ändras eller tas bort. När versionshantering av blobar är aktiverat kan du återställa en tidigare version av en blob för att återställa dina data om de har ändrats eller tagits bort felaktigt.

Den här artikeln visar hur du aktiverar eller inaktiverar blobversionshantering för lagringskontot med hjälp av Azure Portal eller en Azure Resource Manager mall. Mer information om versionshantering av blobar finns i [Blobversionshantering.](versioning-overview.md)

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Aktivera blobversionshantering

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

Så här aktiverar du blobversionshantering för ett lagringskonto i Azure Portal:

1. Gå till ditt lagringskonto i portalen.
1. Under **Blob Service** väljer du **Dataskydd.**
1. I avsnittet **Versionshantering** väljer du **Aktiverad.**

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Skärmbild som visar hur du aktiverar blobversionshantering i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill aktivera blobversionshantering för ett lagringskonto med PowerShell installerar du först [Az.Storage-modul](https://www.powershellgallery.com/packages/Az.Storage) version 2.3.0 eller senare. Anropa sedan [kommandot Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) för att aktivera versionshantering, som du ser i följande exempel. Kom ihåg att ersätta värdena inom vinkelparenteser med dina egna värden:

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

Om du vill aktivera blobversionshantering för ett lagringskonto med Azure CLI installerar du först Azure CLI version 2.2.0 eller senare. Anropa sedan kommandot [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) för att aktivera versionshantering, som du ser i följande exempel. Kom ihåg att ersätta värdena inom vinkelparenteser med dina egna värden:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Mall](#tab/template)

Om du vill aktivera blobversionshantering med en mall skapar du en mall med egenskapen **IsVersioningEnabled** till **true**. Följande steg beskriver hur du skapar en mall i Azure Portal.

1. I Azure Portal du Skapa **en resurs**.
1. I **Sök på Marketplace** skriver du **malldistribution och** trycker sedan på **RETUR.**
1. Välj **Malldistribution**, välj **Skapa** och välj sedan Skapa en egen mall **i redigeraren**.
1. Klistra in följande JSON i mallredigeraren. Ersätt platshållaren `<accountName>` med namnet på ditt lagringskonto.
1. Spara mallen.
1. Ange resursgruppen för kontot och välj sedan knappen Köp **för** att distribuera mallen och aktivera versionshantering av blobar.

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

Mer information om hur du distribuerar resurser med mallar i Azure Portal finns i [Distribuera resurser med Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Ändra en blob för att utlösa en ny version

I följande kodexempel visas hur du utlöser skapandet av en ny version med Azure Storage-klientbiblioteket för .NET, version [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) eller senare. Innan du kör det här exemplet kontrollerar du att du har aktiverat versionshantering för ditt lagringskonto.

Exemplet skapar en blockblob och uppdaterar sedan blobens metadata. När du uppdaterar blobens metadata skapas en ny version. Exemplet hämtar den ursprungliga versionen och den aktuella versionen och visar att endast den aktuella versionen innehåller metadata.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Visa en lista över blobversioner

Om du vill visa en lista över blobversioner eller ögonblicksbilder med .NET v12-klientbiblioteket anger du parametern [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) med **fältet** Version.

I följande kodexempel visas hur du visar en lista över blobar med Azure Storage klientbibliotek för .NET, version [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) eller senare. Innan du kör det här exemplet kontrollerar du att du har aktiverat versionshantering för ditt lagringskonto.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Nästa steg

- [Versionshantering av blobar](versioning-overview.md)
- [Mjuk borttagning för Azure Storage-blobar](./soft-delete-blob-overview.md)