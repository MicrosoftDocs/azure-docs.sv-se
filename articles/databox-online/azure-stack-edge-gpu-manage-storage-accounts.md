---
title: Konto hantering för Azure Stack Edge Pro GPU Storage | Microsoft Docs
description: Beskriver hur du använder Azure Portal för att hantera lagrings kontot på din Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 2d9520c8f97171dbf2f46aa2c94b9e1e280ed86c
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201285"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro"></a>Använda Azure-portalen till att hantera Edge-lagringskonton i Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du hanterar Edge Storage-konton på Azure Stack Edge Pro. Du kan hantera Azure Stack Edge Pro via Azure Portal eller via det lokala webb gränssnittet. Använd Azure Portal för att lägga till eller ta bort gräns lagrings konton på enheten.

## <a name="about-edge-storage-accounts"></a>Om Edge Storage-konton

Du kan överföra data från din Azure Stack Edge Pro-enhet via SMB-, NFS-eller REST-protokollen. Om du vill överföra data till Blob Storage med hjälp av REST-API: er måste du skapa Edge Storage-konton på Azure Stack Edge Pro. 

De gräns lagrings konton som du lägger till på Azure Stack Edge Pro-enheten mappas till Azure Storage-konton. Alla data som skrivs till gräns lagrings kontona flyttas automatiskt till molnet.

Ett diagram som beskriver de två typerna av konton och hur data flödar från var och en av dessa konton till Azure visas nedan:

![Diagram för Blob Storage-konton](media/azure-stack-edge-gpu-manage-storage-accounts/ase-blob-storage.svg)

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Lägg till ett gräns lagrings konto
> * Ta bort ett gräns lagrings konto


## <a name="add-an-edge-storage-account"></a>Lägg till ett gräns lagrings konto

Gör så här för att skapa ett gräns lagrings konto:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Ta bort ett gräns lagrings konto

Utför följande steg för att ta bort ett gräns lagrings konto.

1. Gå till **konfiguration > lagrings konton** i din resurs. I listan över lagrings konton väljer du det lagrings konto som du vill ta bort. I det övre kommando fältet väljer du **ta bort lagrings konto**.

    ![Gå till listan över lagrings konton](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-1.png)

2. På bladet **ta bort lagrings konto** bekräftar du det lagrings konto som ska tas bort och väljer **ta bort**.

    ![Bekräfta och ta bort lagrings konto](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-2.png)

Listan över lagrings konton uppdateras för att återspegla borttagningen.


## <a name="add-delete-a-container"></a>Lägg till, ta bort en behållare

Du kan också lägga till eller ta bort behållarna för dessa lagrings konton.

Gör så här för att lägga till en behållare:

1. Välj det lagrings konto som du vill hantera. I det övre kommando fältet väljer du **+ Lägg till behållare**.

    ![Välj lagrings konto för att lägga till behållare](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-1.png)

2. Ange ett namn för din behållare. Den här behållaren skapas i ditt lagrings konto för lagring och det Azure Storage-konto som är kopplat till det här kontot. 

    ![Lägg till gräns behållare](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-2.png)

Listan över behållare uppdateras för att avspegla den nyligen tillagda behållaren.

![Uppdaterad lista över behållare](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-4.png)

Nu kan du välja en behållare från den här listan och välja **+ ta bort behållare** från det övre kommando fältet. 

![Ta bort en container](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>Synkronisera lagringsnycklar

Du kan synkronisera åtkomst nycklarna för kant (lokala) lagrings konton på enheten. 

Utför följande steg för att synkronisera lagrings kontots åtkomst nyckel:

1. I din resurs väljer du det lagrings konto som du vill hantera. I det övre kommando fältet väljer du **Synkronisera lagrings nyckel**.

    ![Välj synkronisera lagrings nyckel](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-1.png)

2. Välj **Ja** när du uppmanas att bekräfta.

    ![Välj Sync Storage-nyckel 2](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar användare via Azure-portalen](azure-stack-edge-gpu-manage-users.md).
