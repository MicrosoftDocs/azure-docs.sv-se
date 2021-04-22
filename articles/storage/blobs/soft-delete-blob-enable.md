---
title: Aktivera mjuk borttagning för blobar
titleSuffix: Azure Storage
description: Aktivera mjuk borttagning för blobar för att skydda blobdata från oavsiktliga borttagningar eller överwrites.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4a8d1f872ca042429276b8f0e1112bc5837d8e38
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869283"
---
# <a name="enable-soft-delete-for-blobs"></a>Aktivera mjuk borttagning för blobar

Mjuk borttagning av blob skyddar en enskild blob och dess versioner, ögonblicksbilder och metadata från oavsiktliga borttagningar eller skriver över genom att behålla borttagna data i systemet under en angiven tidsperiod. Under kvarhållningsperioden kan du återställa bloben till dess tillstånd vid borttagning. När kvarhållningsperioden har gått ut tas bloben bort permanent. Mer information om mjuk borttagning av blobar finns i [Mjuk borttagning för blobar.](soft-delete-blob-overview.md)

Mjuk borttagning av blobar är en del av en omfattande dataskyddsstrategi för blobdata. Mer information om Microsofts rekommendationer för dataskydd finns i [Översikt över dataskydd.](data-protection-overview.md)

## <a name="enable-blob-soft-delete"></a>Aktivera mjuk borttagning av blob

Mjuk borttagning av blobar är inaktiverat som standard för ett nytt lagringskonto. Du kan aktivera eller inaktivera mjuk borttagning för ett lagringskonto när som helst med hjälp av Azure Portal, PowerShell eller Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill aktivera mjuk borttagning av blobar för ditt Azure Portal med hjälp av Azure Portal här:

1. Navigera till ditt lagringskonto på [Azure-portalen](https://portal.azure.com/).
1. Leta upp **alternativet Dataskydd** under **Blob Service**.
1. I avsnittet **Återställning** väljer du **Aktivera mjuk borttagning för blobar.**
1. Ange en kvarhållningsperiod mellan 1 och 365 dagar. Microsoft rekommenderar en minsta kvarhållningsperiod på sju dagar.
1. Spara ändringarna.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Skärmbild som visar hur du aktiverar mjuk borttagning i Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill aktivera mjuk borttagning av blobar med PowerShell anropar du kommandot [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) och anger kvarhållningsperioden i dagar.

I följande exempel aktiveras mjuk borttagning av blobar och kvarhållningsperioden anges till sju dagar. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Om du vill kontrollera de aktuella inställningarna för mjuk borttagning av blob anropar du kommandot [Get-AzStorageBlobServiceProperty:](/powershell/module/az.storage/get-azstorageblobserviceproperty)

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Om du vill aktivera mjuk borttagning av blobar med Azure CLI anropar du kommandot [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) och anger kvarhållningsperioden i dagar.

I följande exempel aktiveras mjuk borttagning av blobar och kvarhållningsperioden anges till sju dagar. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Om du vill kontrollera de aktuella inställningarna för mjuk borttagning av blob anropar du kommandot [az storage account blob-service-properties show:](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show)

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Nästa steg

- [Mjuk borttagning för blobar](soft-delete-blob-overview.md)
- [Hantera och återställa mjukt borttagna blobar](soft-delete-blob-manage.md)
