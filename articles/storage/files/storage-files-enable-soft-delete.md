---
title: Aktivera mjuk borttagning – Azure-filresurser
description: Lär dig hur du aktiverar mjuk borttagning på Azure-filresurser för data återställning och förhindrar oavsiktlig borttagning.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 13e6668337b82ea4be86eadcbc6f7797a72771c6
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023491"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Aktivera mjuk borttagning på Azure-filresurser

Azure Storage erbjuder mjuk borttagning för fil resurser så att du enkelt kan återställa dina data när de tas bort av misstag av ett program eller annan lagrings konto användare. Mer information om mjuk borttagning finns i [så här förhindrar du oavsiktlig borttagning av Azure-filresurser](storage-files-prevent-file-share-deletion.md).

I följande avsnitt visas hur du aktiverar och använder mjuk borttagning för Azure-filresurser på ett befintligt lagrings konto:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Komma igång

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Navigera till ditt lagrings konto och välj **fil resurser** under **fil tjänst**.
1. Välj **aktive rad** för **mjuk borttagning för alla fil resurser**.
1. Välj **fil resursens kvarhållningsperiod i dagar** och ange ett antal som du väljer.
1. Välj **Spara** för att bekräfta dina inställningar för datakvarhållning.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Skärm bild av fönstret inställningar för mjuk borttagning av lagrings konto. Markera avsnittet fil resurser mjuk borttagning, aktivera växling, ange en kvarhållningsperiod och spara. Detta aktiverar mjuk borttagning för alla fil resurser i ditt lagrings konto.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cmdlets för mjuk borttagning är tillgängliga i version 2.1.3 och senare av [Azure CLI-modulen](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="getting-started-with-cli"></a>Komma igång med CLI

Om du vill aktivera mjuk borttagning måste du uppdatera en fil klients tjänst egenskaper. I följande exempel aktive ras mjuk borttagning för alla fil resurser i ett lagrings konto:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

Du kan kontrol lera om mjuk borttagning är aktiverat och visa dess bevarande princip med följande kommando:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Förutsättning

Cmdlets för mjuk borttagning är tillgängliga i 4.8.0 och senare versioner av modulen AZ. Storage. 

## <a name="getting-started-with-powershell"></a>Komma igång med PowerShell

Om du vill aktivera mjuk borttagning måste du uppdatera en fil klients tjänst egenskaper. I följande exempel aktive ras mjuk borttagning för alla fil resurser i ett lagrings konto:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Du kan kontrol lera om mjuk borttagning är aktiverat och visa dess bevarande princip med följande kommando:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Återställa mjuk borttagen fil resurs

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här återställer du en mjuk borttagen fil resurs:

1. Navigera till ditt lagrings konto och välj **fil resurser**.
1. På bladet fil resurs aktiverar du **Visa borttagna resurser** för att visa alla resurser som har varit mjuka borttagna.

    Då visas alla resurser som är i ett **Borttaget** tillstånd.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Om kolumnen Status är kolumnen bredvid kolumnen namn inställt på borttagen, är fil resursen i ett tyst borttaget tillstånd. Och kommer att tas bort permanent efter den angivna kvarhållningsperioden.":::

1. Välj resursen och välj **ångra borttagning**. resursen återställs då.

    Du kan bekräfta att resursen har återställts eftersom dess status växlar till **aktiv**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Om kolumnen Status är kolumnen bredvid kolumnen namn inställt på aktiv, har fil resursen återställts.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cmdlets för mjuk borttagning är tillgängliga i 2.1.3-versionen av Azure CLI. Om du vill återställa en mjuk borttagen fil resurs måste du först hämta `--deleted-version` värdet för resursen. Använd följande kommando för att visa alla borttagna resurser för ditt lagrings konto för att hämta värdet:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

När du har identifierat resursen som du vill återställa kan du använda den med följande kommando för att återställa den:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets för mjuk borttagning är tillgängliga i 4.8.0 och senare versioner av modulen AZ. Storage. Om du vill återställa en mjuk borttagen fil resurs måste du först hämta `-DeletedShareVersion` värdet för resursen. Använd följande kommando för att visa alla borttagna resurser för ditt lagrings konto för att hämta värdet:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

När du har identifierat resursen som du vill återställa kan du använda den med följande kommando för att återställa den:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Inaktivera mjuk borttagning

Följ dessa instruktioner om du vill sluta använda mjuk borttagning. Om du vill ta bort en fil resurs som har varit mjuk borttagning permanent måste du ta bort den, inaktivera mjuk borttagning och sedan ta bort den igen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigera till ditt lagrings konto och välj **fil resurser** under **fil tjänst**.
1. Välj **inaktive rad** för **mjuk borttagning för alla fil resurser**.
1. Välj **Spara** för att bekräfta dina inställningar för datakvarhållning.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Om du inaktiverar mjuk borttagning kan du omedelbart och permanent ta bort alla fil resurser i ditt lagrings konto.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cmdlets för mjuk borttagning är tillgängliga i 2.1.3-versionen av Azure CLI. Du kan använda följande kommando för att inaktivera mjuk borttagning på ditt lagrings konto:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets för mjuk borttagning är tillgängliga i 4.8.0 och senare versioner av modulen AZ. Storage. Du kan använda följande kommando för att inaktivera mjuk borttagning på ditt lagrings konto:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Nästa steg

Information om en annan typ av data skydd och återställning finns i vår artikel [Översikt över resurs ögonblicks bilder för Azure Files](storage-snapshots-files.md).