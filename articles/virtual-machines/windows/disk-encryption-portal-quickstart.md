---
title: Skapa och kryptera en virtuell Windows-dator med Azure-portalen
description: I den här snabb starten får du lära dig hur du använder Azure Portal för att skapa och kryptera en virtuell Windows-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 7857a037d8e48c8c6ae8d44cf77c863bec91d9d3
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510655"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Snabb start: skapa och kryptera en virtuell Windows-dator med Azure Portal

Det går att skapa virtuella Azure-datorer via Azure Portal. Azure-portalen är ett webbläsarbaserat användargränssnitt för att skapa virtuella datorer och alla relaterade resurser. I den här snabb starten ska du använda Azure Portal för att distribuera en virtuell Windows-dator, skapa ett nyckel valv för lagringen av krypterings nycklar och kryptera den virtuella datorn.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).


## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. På sidan ny under populär väljer du **Windows Server 2016 Data Center**.
1. På fliken grundläggande, under projekt information, se till att rätt prenumeration är markerad.
1. För "resurs grupp" väljer du **Skapa ny**. Ange *myResourceGroup* som namn och välj **OK**.
1. Ange *MyVM*som **namn på virtuell dator**.
1. För **region**väljer du *(US) USA, östra*.
1. Kontrol lera att **storleken** är *standard D2s v3*.
1. Under **administratörs konto**väljer du **lösen ord**. Ange ett användar namn och ett lösen ord.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-windows-vm-creation.png" alt-text="Skärmen Skapa virtuell dator i Windows":::

    > [!WARNING]
    > Fliken "diskar" innehåller fältet "krypterings typ" under **disk alternativ**. Det här fältet används för att ange krypterings alternativ för [Managed disks](managed-disks-overview.md) + CMK, inte för Azure Disk Encryption.
    >
    > För att undvika förvirring rekommenderar vi att du hoppar över fliken *diskar* helt när du har slutfört den här självstudien.

1. Välj fliken "hantering" och kontrol lera att du har ett konto för diagnostik-lagring. Om du inte har några lagrings konton väljer du "Skapa ny", ger ditt nya konto ett namn och väljer OK

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="Skärmen skapa ResourceGroup":::

1. Klicka på granska + skapa.
1. På sidan **Skapa en virtuell dator** kan du se information om den virtuella datorn som du håller på att skapa. När du är klar väljer du **Skapa**.

Det tar några minuter för den virtuella datorn att distribueras. När distributionen är klar kan du gå vidare till nästa avsnitt.

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

1. När den virtuella dator distributionen är klar väljer **du gå till resurs**.
1. På den vänstra sid panelen väljer du **diskar**.
1. I det översta fältet väljer du **ytterligare inställningar** .
1. Under **krypterings inställningar**  >  **diskar som ska krypteras väljer du** **OS och data diskar**.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="Val av diskar och kryptering":::

1. Under **krypterings inställningar**väljer du **Välj ett nyckel valv och nyckel för kryptering**.
1. På skärmen **Välj nyckel från Azure Key Vault** väljer du **Skapa ny**.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="Val av diskar och kryptering":::

1. Till vänster om **nyckel valv och nyckel**väljer **du klicka för att välja en nyckel**.
1. Välj **Skapa nytt**under fältet **Key Vault** på **Välj nyckel från Azure Key Vault**.
1. På skärmen **skapa nyckel valv** kontrollerar du att resurs gruppen är *myResourceGroup*och ger ditt nyckel valv ett namn.  Varje nyckel valv i Azure måste ha ett unikt namn.
1. På fliken **åtkomst principer** markerar du rutan **Azure Disk Encryption för volym kryptering** .

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="Val av diskar och kryptering":::

1. Välj **Granska + skapa**.  
1. När nyckel valvet har klarat valideringen väljer du **skapa**. Du kommer tillbaka till skärmen **Välj nyckel från Azure Key Vault** .
1. Lämna fältet **nyckel** tomt och välj **Välj**.
1. Klicka på **Spara**överst på krypterings skärmen. En popup varnar dig om att den virtuella datorn startas om. Klicka på **Ja**.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra detta väljer du resursgruppen för den virtuella datorn. Välj sedan Ta bort och kontrollera namnet på resursgruppen som du vill ta bort.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Key Vault som aktiverades för krypterings nycklar, skapat en virtuell dator och aktiverat den virtuella datorn för kryptering.  

> [!div class="nextstepaction"]
> [Översikt över Azure Disk Encryption](disk-encryption-overview.md)
