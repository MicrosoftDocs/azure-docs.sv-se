---
title: Aktivera mjuk borttagning för blobar
titleSuffix: Azure Storage
description: Aktivera mjuk borttagning för blobbar för att skydda BLOB-data från oavsiktliga borttagningar eller Skriv över.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 11323f2aec05935b9dc45187ed54597e61af924d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554131"
---
# <a name="enable-soft-delete-for-blobs"></a>Aktivera mjuk borttagning för blobar

BLOB-mjuk borttagning skyddar en enskild blob och dess versioner, ögonblicks bilder och metadata från oavsiktliga borttagningar eller överskrivning genom att underhålla borttagna data i systemet under en angiven tids period. Under kvarhållningsperioden kan du återställa blobben till dess tillstånd vid borttagning. När kvarhållningsperioden har gått ut tas bloben bort permanent. Mer information om mjuk borttagning av BLOB finns i [mjuk borttagning för blobbar](soft-delete-blob-overview.md).

BLOB-mjuk borttagning är en del av en omfattande data skydds strategi för BLOB-data. Mer information om Microsofts rekommendationer för data skydd finns i [Översikt över data skydd](data-protection-overview.md).

## <a name="enable-blob-soft-delete"></a>Aktivera mjuk borttagning av BLOB

BLOB-mjuk borttagning är inaktiverat som standard för ett nytt lagrings konto. Du kan aktivera eller inaktivera mjuk borttagning för ett lagrings konto när som helst med hjälp av Azure Portal, PowerShell eller Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill aktivera mjuk borttagning av BLOB för ditt lagrings konto med hjälp av Azure Portal:

1. Navigera till ditt lagringskonto på [Azure-portalen](https://portal.azure.com/).
1. Leta upp alternativet **data skydd** under **BLOB service**.
1. I avsnittet **återställning** väljer du **Aktivera mjuk borttagning för blobbar**.
1. Ange en kvarhållningsperiod mellan 1 och 365 dagar. Microsoft rekommenderar en minsta Retentions period på sju dagar.
1. Spara ändringarna.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Skärm bild som visar hur du aktiverar mjuk borttagning i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill aktivera mjuk borttagning av BLOB med PowerShell anropar du kommandot [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) och anger kvarhållningsperioden i dagar.

I följande exempel aktive ras borttagning av BLOB-Soft och kvarhållningsperioden anges till sju dagar. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Om du vill kontrol lera de aktuella inställningarna för en mjuk borttagning av BLOB anropar du kommandot [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) :

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Om du vill aktivera BLOB-mjuk borttagning med Azure CLI anropar du kommandot [AZ Storage Account BLOB-service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) och anger kvarhållningsperioden i dagar.

I följande exempel aktive ras borttagning av BLOB-Soft och kvarhållningsperioden anges till sju dagar. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Om du vill kontrol lera de aktuella inställningarna för en mjuk borttagning av BLOB anropar du kommandot [AZ Storage Account BLOB-service-Properties show](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_show) :

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Nästa steg

- [Mjuk borttagning för blobar](soft-delete-blob-overview.md)
- [Hantera och återställa mjuka borttagna blobar](soft-delete-blob-manage.md)
