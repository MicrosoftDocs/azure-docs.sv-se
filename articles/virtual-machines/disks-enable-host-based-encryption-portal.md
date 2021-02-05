---
title: Aktivera kryptering från slut punkt till slut punkt med kryptering på värd Azure Portal-hanterade diskar
description: Använd kryptering på värden för att aktivera kryptering från slut punkt till slut punkt på dina Azure Managed disks-Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ba7d6d8deb2034f8b2a853cf74635687561c41ea
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573610"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Använd Azure Portal för att aktivera kryptering från slut punkt till slut punkt med kryptering på värden

När du aktiverar kryptering på värden krypteras data som lagras på den virtuella dator värden i vila och flöden krypteras till lagrings tjänsten. För konceptuell information om kryptering på värden, samt andra typer av hanterade disk krypterings typer, se:

* Linux: [kryptering på värd-från-slutpunkt-till-slutpunkt-kryptering för dina VM-data](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [kryptering på värd-från-slutpunkt-till-slutpunkt-kryptering för dina VM-data](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Regioner som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>VM-storlekar som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda kryptering på värden för dina virtuella datorer eller skalnings uppsättningar för virtuella datorer måste du få funktionen aktive rad i din prenumeration. Skicka ett e-postmeddelande till encryptionAtHost@microsoft.com med dina prenumerations-ID för att få funktionen aktive rad för dina prenumerationer.

Logga in på Azure Portal med den [angivna länken](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Du måste använda den [angivna länken](https://aka.ms/diskencryptionupdates) för att få åtkomst till Azure Portal. Kryptering på värden är för närvarande inte synligt i den offentliga Azure Portal utan att använda länken.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Skapa en Azure Key Vault och disk krypterings uppsättning

När funktionen är aktive rad måste du konfigurera en Azure Key Vault och en disk krypterings uppsättning, om du inte redan gjort det.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Distribuera en virtuell dator

Du måste distribuera en ny virtuell dator för att aktivera kryptering på värden, men den kan inte aktive ras på befintliga virtuella datorer.

1. Sök efter **Virtual Machines** och välj **+ Lägg** till för att skapa en virtuell dator.
1. Skapa en ny virtuell dator, Välj en lämplig region och en VM-storlek som stöds.
1. Fyll i de andra värdena på bladet **grundläggande** som du vill och fortsätt sedan till bladet **diskar** .

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Skärm bild av bladet grunderna för att skapa virtuella datorer, region och V M storlek är markerade.":::

1. På bladet **diskar** väljer du **Ja** för **kryptering på värden**.
1. Gör de återstående valen som du vill.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Skärm bild av bladet disk för skapande av virtuell dator, kryptering på värd är markerat.":::

1. Slutför distributions processen för den virtuella datorn och gör de val som passar din miljö.

Nu har du distribuerat en virtuell dator med kryptering på värden aktive rad, men alla tillhör ande diskar krypteras med kryptering på värden.

## <a name="next-steps"></a>Nästa steg

[Azure Resource Manager-mallexempel](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
