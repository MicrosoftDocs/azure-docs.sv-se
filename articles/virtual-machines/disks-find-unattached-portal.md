---
title: Identifiera frånkopplade Azure-diskar – Azure Portal
description: Så här hittar du icke anslutna Azure-hanterade och ohanterade (VHD/Page blobbar) diskar med hjälp av Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/26/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4d31ce4b6086c44de913afd1083bae25fa3d44cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898163"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>Hitta och ta bort frånkopplade Azure-hanterade och ohanterade diskar – Azure Portal

När du tar bort en virtuell dator (VM) i Azure tas som standard alla diskar som är kopplade till den virtuella datorn bort. Detta bidrar till att förhindra data förlust på grund av oavsiktlig borttagning av virtuella datorer. När en virtuell dator har tagits bort fortsätter du att betala för ej anslutna diskar. Den här artikeln visar hur du hittar och tar bort alla frånkopplade diskar med hjälp av Azure Portal och minskar onödiga kostnader. Borttagningar är permanenta. du kommer inte att kunna återställa data när du har tagit bort en disk.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Hanterade diskar: hitta och ta bort ej anslutna diskar

Om du har frånkopplade hanterade diskar och inte längre behöver data på dem, förklarar följande process hur du hittar dem från Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Sök efter och välj **diskar**.

    På bladet **diskar** visas en lista över alla dina diskar. Alla diskar som har " **-** " i kolumnen **ägare** är en icke-kopplad disk.

    [![Skärm bild av bladet hanterade diskar, om en disk har-i kolumnen ägare, är det en frånkopplad disk.](media/disks-find-unattached-portal/managed-disk-unattached-owner.png)](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. Välj den icke-anslutna disk som du vill ta bort. Då öppnas bladet disk.
1. På disk bladet kan du kontrol lera att disk statusen är frånkopplad och sedan **ta bort**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="Skärm bild av bladet enskilda hanterade diskar. Det här bladet visar unattached i disk tillstånd om det inte är kopplat. Du kan ta bort disken om du inte behöver bevara dess data längre":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Ohanterade diskar: hitta och ta bort ej anslutna diskar

Ohanterade diskar är VHD-filer som lagras som [Page blobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) i [Azure Storage-konton](../storage/common/storage-account-overview.md).

Om du har ohanterade diskar som inte är kopplade till en virtuell dator behöver du inte längre använda data på dem och vill ta bort dem, förklarar följande process hur du gör det från Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Sök efter och välj **diskar (klassisk)**.

    En lista över alla ohanterade diskar visas. Alla diskar som har " **-** " i kolumnen **bifogad till** är en icke-ansluten disk.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="Skärm bild av bladet ej hanterade diskar. Diskar i det här bladet som är kopplade till kolumnen är inte kopplade.":::

1. Välj den icke-anslutna disken som du vill ta bort. Då öppnas bladet disk.

1. På diskens blad kan du bekräfta att det är frånkopplat, eftersom det fortfarande är **kopplat till** **-** .

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="Skärm bild av ett enskilt ohanterat disk blad. Den har-som bifogad till-värde om den är frånkopplad. Om du inte längre behöver dessa disk data kan du ta bort den.":::

1. Välj **Ta bort**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="Skärm bild av ett enskilt ohanterat disk blad, markera ta bort.":::

## <a name="next-steps"></a>Nästa steg

Om du vill ha ett automatiserat sätt att hitta och ta bort ej anslutna lagrings konton, Se våra [CLI](linux/find-unattached-disks.md) -eller [PowerShell](windows/find-unattached-disks.md) -artiklar.

Mer information finns i [ta bort ett lagrings konto](../storage/common/storage-account-create.md#delete-a-storage-account) och [identifiera överblivna diskar med PowerShell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)
