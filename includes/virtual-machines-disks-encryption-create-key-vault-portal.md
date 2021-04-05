---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "88815478"
---
Om du ställer in Kundhanterade nycklar för dina diskar måste du skapa resurser i en viss ordning, om du gör det för första gången. Först måste du skapa och konfigurera en Azure Key Vault.

## <a name="set-up-your-azure-key-vault"></a>Konfigurera din Azure Key Vault

1. Logga in på [Azure-portalen](https://aka.ms/diskencryptionupdates).
1. Sök efter och välj **nyckel valv**.

    [![Skärm bild av Azure Portal med dialog rutan Sök expanderad.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Ditt Azure Key Vault, disk krypterings uppsättning, virtuell dator, diskar och ögonblicks bilder måste vara i samma region och prenumeration för att distributionen ska lyckas.

1. Välj **+ Lägg** till för att skapa en ny Key Vault.
1. Skapa en ny resursgrupp.
1. Ange ett nyckel valv namn, Välj en region och välj en pris nivå.

    > [!NOTE]
    > När du skapar Key Vault-instansen måste du aktivera mjuk borttagning och tömning av skydd. Mjuk borttagning garanterar att Key Vault innehåller en borttagen nyckel för en viss kvarhållningsperiod (90 dag standard). Med rensnings skyddet går det inte att ta bort en borttagen nyckel förrän kvarhållningsperioden upphörde. Dessa inställningar skyddar dig från att förlora data på grund av oavsiktlig borttagning. De här inställningarna är obligatoriska när du använder en Key Vault för att kryptera hanterade diskar.

1. Välj **Granska + skapa**, verifiera dina val och välj sedan **skapa**.

    ![Skärm bild av Azure Key Vault skapande upplevelsen. Visar de specifika värden som du skapar](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. När du har slutfört distributionen av nyckel valvet väljer du det.
1. Välj **nycklar** under **Inställningar**.
1. Välj **generera/importera**.

    ![Skärm bild av fönstret Key Vault resurs inställningar. Visar knappen generera/importera i inställningar.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Lämna båda **nyckel typerna** inställd på **RSA** och **RSA-nyckel storleken** inställd på **2048**.
1. Fyll i de återstående valen som du vill och välj sedan **skapa**.

    ![Skärm bild av bladet skapa en nyckel som visas när knappen generera/importera har valts](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>Konfigurera din disk krypterings uppsättning

1. Sök efter **disk krypterings uppsättningar** och välj den.
1. På bladet **disk krypterings uppsättningar** väljer du **+ Lägg till**.

    ![Skärm bild av disk krypterings portalens huvud skärm. Markera knappen Lägg till](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Välj din resurs grupp, namnge krypterings uppsättningen och välj samma region som nyckel valvet.
1. För **krypterings typ** väljer du **kryptering i vila med en kundhanterad nyckel**.

    > [!NOTE]
    > När du har skapat en disk krypterings uppsättning med en viss krypterings typ kan den inte ändras. Om du vill använda en annan krypterings typ måste du skapa en ny disk krypterings uppsättning.

1. Välj **en nyckel genom att klicka på Klicka**.
1. Välj nyckel valvet och nyckeln som du skapade tidigare, samt versionen.
1. Tryck på **Välj**.
1. Välj **Granska och skapa** och sedan **Skapa**.

    ![Skärm bild av bladet för att skapa disk kryptering. Visar prenumeration, resurs grupp, namn på disk krypterings uppsättning, region och nyckel valv + nyckel väljare.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. Öppna disk krypterings uppsättningen när den har skapats och välj den avisering som visas.

    ![Skärm bild av popup för avisering: "om du vill associera en disk, avbildning eller ögonblicks bild med en disk krypterings uppsättning måste du bevilja behörighet till nyckel valvet". Välj den här aviseringen om du vill fortsätta](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    Två meddelanden bör visas och lyckas. På så sätt kan du använda disk krypterings uppsättningen med nyckel valvet.

    ![Skärm bild av lyckad behörighet och roll tilldelning för ditt nyckel valv.](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)