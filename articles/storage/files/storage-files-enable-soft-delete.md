---
title: Aktivera mjuk borttagning – Azure-filresurser
description: Lär dig hur du aktiverar mjuk borttagning på Azure-filresurser för dataåterställning och förhindrar oavsiktlig borttagning.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: a0dff310ce4a40b7a66cc548f3c77213f4a10e00
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717031"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Aktivera mjuk borttagning på Azure-filresurser

Azure Storage mjuk borttagning för filresurser så att du enklare kan återställa data när de tas bort av misstag av ett program eller en annan lagringskontoanvändare. Mer information om mjuk borttagning finns i Så [här förhindrar du oavsiktlig borttagning av Azure-filresurser.](storage-files-prevent-file-share-deletion.md)

Följande avsnitt visar hur du aktiverar och använder mjuk borttagning för Azure-filresurser på ett befintligt lagringskonto:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Komma igång

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Gå till ditt lagringskonto och välj **Filresurser** under **Datalagring.**
1. Välj **Aktiverad** bredvid **Mjuk borttagning.**
1. Välj **Aktiverad** för **Mjuk borttagning för alla filresurser**.
1. Välj **Kvarhållningsperiod för filresurs i** dagar och ange ett antal som du väljer.
1. Välj **Spara för** att bekräfta inställningarna för datalagring.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="Skärmbild av inställningsfönstret för mjuk borttagning av lagringskonto. Markera avsnittet för mjuk borttagning av filresurser, aktivera växling, ange en kvarhållningsperiod och spara. Detta aktiverar mjuk borttagning för alla filresurser i ditt lagringskonto.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cmdlets för mjuk borttagning är tillgängliga i version 2.1.3 och senare av [Azure CLI-modulen](/cli/azure/install-azure-cli).

## <a name="getting-started-with-cli"></a>Komma igång med CLI

Om du vill aktivera mjuk borttagning måste du uppdatera en filklients tjänstegenskaper. I följande exempel aktiveras mjuk borttagning för alla filresurser i ett lagringskonto:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

Du kan kontrollera om mjuk borttagning är aktiverat och visa dess bevarandeprincip med följande kommando:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Förutsättning

Cmdlets för mjuk borttagning är tillgängliga i 4.8.0 och senare versioner av Modulen Az.Storage. 

## <a name="getting-started-with-powershell"></a>Komma igång med PowerShell

Om du vill aktivera mjuk borttagning måste du uppdatera en filklients tjänstegenskaper. I följande exempel aktiveras mjuk borttagning för alla filresurser i ett lagringskonto:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Du kan kontrollera om mjuk borttagning är aktiverat och visa dess kvarhållningsprincip med följande kommando:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Återställa mjuk borttagning av filresurs

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här återställer du en mjukt borttagna filresurs:

1. Gå till ditt lagringskonto och välj **Filresurser**.
1. På filresursbladet aktiverar du **Visa borttagna resurser för** att visa alla resurser som har mjuk borttagning.

    Då visas alla resurser som för närvarande är i **ett borttagna** tillstånd.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Om statuskolumnen, kolumnen bredvid namnkolumnen, är inställd på Borttagna är filresursen i ett mjukt borttagna tillstånd. Och tas bort permanent efter den angivna kvarhållningsperioden.":::

1. Välj resursen och välj **undelete**. Då återställs resursen.

    Du kan bekräfta att resursen har återställts eftersom dess status växlar till **Aktiv.**

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Om statuskolumnen, kolumnen bredvid namnkolumnen, är inställd på Aktiv, har filresursen återställts.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cmdlets för mjuk borttagning är tillgängliga i 2.1.3-versionen av Azure CLI. Om du vill återställa en mjukt borttagna filresurs måste du först `--deleted-version` hämta värdet för resursen. Om du vill hämta det värdet använder du följande kommando för att visa en lista över alla borttagna resurser för ditt lagringskonto:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

När du har identifierat resursen som du vill återställa kan du använda den med följande kommando för att återställa den:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets för mjuk borttagning är tillgängliga i 4.8.0 och senare versioner av Modulen Az.Storage. Om du vill återställa en mjukt borttagna filresurs måste du först `-DeletedShareVersion` hämta värdet för resursen. Om du vill hämta det värdet använder du följande kommando för att visa en lista över alla borttagna resurser för ditt lagringskonto:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

När du har identifierat resursen som du vill återställa kan du använda den med följande kommando för att återställa den:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Inaktivera mjuk borttagning

Om du vill sluta använda mjuk borttagning följer du de här instruktionerna. Om du vill ta bort en filresurs som har mjuk borttagning permanent måste du ta bort den, inaktivera mjuk borttagning och sedan ta bort den igen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till ditt lagringskonto och välj **Filresurser** under **Datalagring.**
1. Välj länken bredvid Mjuk **borttagning.**
1. Välj **Inaktiverad** **för Mjuk borttagning för alla filresurser**.
1. Välj **Spara för** att bekräfta inställningarna för datalagring.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="Om du inaktiverar mjuk borttagning kan du omedelbart och permanent ta bort alla filresurser på ditt lagringskonto.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cmdlets för mjuk borttagning är tillgängliga i 2.1.3-versionen av Azure CLI. Du kan använda följande kommando för att inaktivera mjuk borttagning på ditt lagringskonto:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets för mjuk borttagning är tillgängliga i 4.8.0 och senare versioner av modulen Az.Storage. Du kan använda följande kommando för att inaktivera mjuk borttagning på ditt lagringskonto:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Nästa steg

Mer information om en annan form av dataskydd och återställning finns i vår artikel [Översikt över resursögonblicksbilder för Azure Files](storage-snapshots-files.md).