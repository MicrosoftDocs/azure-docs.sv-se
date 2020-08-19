---
title: Aktivera mjuk borttagning – Azure-filresurser
description: Lär dig hur du aktiverar mjuk borttagning (för hands version) på Azure-filresurser för data återställning och förhindrar oavsiktlig borttagning.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 2d2a000879a95f86a6cdda3324add5b692476eee
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590123"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Aktivera mjuk borttagning på Azure-filresurser

Azure Storage erbjuder mjuk borttagning för fil resurser (för hands version) så att du enkelt kan återställa dina data när de tas bort av misstag av ett program eller annan lagrings konto användare. Mer information om mjuk borttagning finns i [så här förhindrar du oavsiktlig borttagning av Azure-filresurser](storage-files-prevent-file-share-deletion.md).

I följande avsnitt visas hur du aktiverar och använder mjuk borttagning för Azure-filresurser på ett befintligt lagrings konto:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Komma igång

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Navigera till ditt lagrings konto och välj **mjuk borttagning** under **fil tjänst**.
1. Välj **aktive rad** för **fil resurs mjuk borttagning**.
1. Välj **fil resursens kvarhållningsperiod i dagar** och ange ett antal som du väljer.
1. Välj **Spara** för att bekräfta dina inställningar för datakvarhållning.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Skärm bild av fönstret inställningar för mjuk borttagning av lagrings konto. Markera avsnittet fil resurser, aktivera växling, ange en kvarhållningsperiod och spara. Detta aktiverar mjuk borttagning för alla fil resurser i ditt lagrings konto.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Förutsättning

Cmdlets för mjuk borttagning är för närvarande endast tillgängliga i [2.1.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) och [2.3.1-Preview-](https://www.powershellgallery.com/packages/Az.Storage/2.3.1-preview) versioner av modulen AZ. Storage. 

## <a name="getting-started"></a>Komma igång

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets för mjuk borttagning är tillgängliga i 2.1.1-Preview-versionen av modulen AZ. Storage. Om du vill återställa en mjuk borttagen fil resurs använder du följande kommando:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Inaktivera mjuk borttagning

Om du vill sluta använda mjuk borttagning eller ta bort en fil resurs permanent, följer du dessa anvisningar:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigera till ditt lagrings konto och välj **mjuk borttagning** under **Inställningar**.
1. Under **fil resurser** väljer du **inaktive rad** för **mjuk borttagning av fil resurser**.
1. Välj **Spara** för att bekräfta dina inställningar för datakvarhållning.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Om du inaktiverar mjuk borttagning kan du omedelbart och permanent ta bort alla fil resurser i ditt lagrings konto.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets för mjuk borttagning är tillgängliga i 2.1.1-Preview-versionen av modulen AZ. Storage. Du kan använda följande kommando för att inaktivera mjuk borttagning på ditt lagrings konto:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Nästa steg

Information om en annan typ av data skydd och återställning finns i vår artikel [Översikt över resurs ögonblicks bilder för Azure Files](storage-snapshots-files.md).
