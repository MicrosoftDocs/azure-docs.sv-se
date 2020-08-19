---
title: Lägga till en virtuell dator i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att lägga till en virtuell dator i ett labb i Azure DevTest Labs. Du kan välja en bas som antingen är en anpassad bild eller formel.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c61e6e685a52ac3658cb9b9ca37f64fc51f3ce1e
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270724"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Lägga till en virtuell dator i ett labb i Azure DevTest Labs
Om du redan har [skapat din första virtuella dator](tutorial-create-custom-lab.md#add-a-vm-to-the-lab), gjorde du troligt vis från en förinstallerad [Marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Om du nu vill lägga till efterföljande virtuella datorer i labbet kan du också välja en *bas* som antingen är en [anpassad bild](devtest-lab-create-template.md) eller [formel](devtest-lab-manage-formulas.md). Den här självstudien vägleder dig genom att använda Azure Portal för att lägga till en virtuell dator i ett labb i DevTest Labs.

Den här artikeln visar också hur du hanterar artefakter för en virtuell dator i labbet.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Steg för att lägga till en virtuell dator i ett labb i Azure DevTest Labs
1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**och välj sedan **DevTest Labs** i avsnittet **DEVOPS** . Om du väljer * (Star) bredvid **DevTest Labs** i avsnittet **DEVOPS** . Den här åtgärden lägger till **DevTest Labs** i den vänstra navigerings menyn så att du enkelt kan komma åt dem nästa gång. Sedan kan du välja **DevTest Labs** i den vänstra navigerings menyn.

    ![Alla tjänster – Välj DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. I listan med labb väljer du det labb som du vill skapa den virtuella datorn i.
2. På sidan **Översikt** för labb väljer du **+ Lägg till**.

    ![Knappen Lägg till virtuell dator](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. På sidan **Välj en bassida** väljer du en Marketplace-avbildning för den virtuella datorn.
1. På fliken **grundläggande inställningar** på sidan **virtuell dator** gör du följande:
    1. Ange ett namn för den virtuella datorn i text rutan **namn på virtuell dator** . Text rutan fylls i automatiskt med ett unikt namn som skapats automatiskt. Namnet motsvarar användar namnet i din e-postadress följt av ett unikt 3-siffrigt tal. Den här funktionen sparar tid för att tänka på ett dator namn och ange det varje gång du skapar en dator. Du kan åsidosätta det här automatiskt ifyllda fältet med ett valfritt namn om du vill. Om du vill åsidosätta det automatiskt ifyllda namnet för den virtuella datorn anger du ett namn i text rutan **namn på virtuell dator** .
    2. Ange ett **användar namn** som har behörighet för administratörs behörighet på den virtuella datorn. Datorns **användar namn** är i förväg ifyllt med ett unikt namn som skapats automatiskt. Namnet motsvarar användar namnet i din e-postadress. Med den här funktionen kan du välja ett användar namn varje gång du skapar en ny dator. Återigen kan du åsidosätta det automatiskt ifyllda fältet med ett användar namn som du väljer om du vill. Om du vill åsidosätta det automatiskt fyllda värdet för användar namn anger du ett värde i text rutan **användar namn** . Den här användaren beviljas **Administratörs** behörighet på den virtuella datorn.
    3. Om du skapar den första virtuella datorn i labbet anger du ett **lösen ord** för användaren. Om du vill spara lösen ordet som ett standard lösen ord i Azure Key Vault som är associerat med labbet väljer du **Spara som standard lösen ord**. Standard lösen ordet sparas i nyckel valvet med namnet: **VmPassword**. När du försöker skapa efterföljande virtuella datorer i labbet väljs **VmPassword** automatiskt för **lösen ordet**. Om du vill åsidosätta värdet avmarkerar du kryss rutan **Använd en sparad hemlighet** och anger ett lösen ord.

        ![Välja en bas](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        Du kan också spara hemligheter i nyckel valvet först och sedan använda det när du skapar en virtuell dator i labbet. Mer information finns i [lagra hemligheter i ett nyckel valv](devtest-lab-store-secrets-in-key-vault.md). Om du vill använda det lösen ord som lagras i nyckel valvet väljer du **Använd en sparad hemlighet**och anger ett nyckel värde som motsvarar ditt hemliga värde (lösen ord).
    4. I avsnittet **fler alternativ** väljer du **ändra storlek**. Välj ett av de fördefinierade objekten som anger processor kärnor, RAM-storlek och hård disk storlek för den virtuella dator som ska skapas.
    5. Välj **Lägg till eller ta bort artefakter**. Välj och konfigurera de artefakter som du vill lägga till i bas avbildningen.
    **Obs:** Om du inte har använt DevTest Labs eller konfigurerat artefakter går du till avsnittet [Lägg till en befintlig artefakt till en virtuell dator](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) och återgår hit när du är klar.
2. Växla till fliken **Avancerade inställningar** längst upp och utför följande åtgärder:
    1. Om du vill ändra det virtuella nätverk som den virtuella datorn finns i väljer du **ändra VNet**.
    2. Om du vill ändra under nätet väljer du **ändra undernät**.
    3. Ange om IP-adressen för den virtuella datorn är **offentlig, privat eller delad**.
    4. Om du vill ta bort den virtuella datorn automatiskt anger du **förfallo datum och-tid**.
    5. Om du vill göra den virtuella datorn valbar för en labb användare väljer du **Ja** för alternativet **gör datorn anspråks** bara.
    6. Ange antalet **instanser av den virtuella datorn** som du vill göra tillgängliga för dina labb användare.

        ![Välja en bas](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Välj **skapa** för att lägga till den angivna virtuella datorn i labbet.

   På sidan labb visas statusen för den virtuella datorns skapande – först som du **skapar**, sedan som **körs** när den virtuella datorn har startats.

    ![Skapandestatus för virtuell dator](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Lägga till en befintlig artefakt till en virtuell dator
När du skapar en virtuell dator kan du lägga till befintliga artefakter. Varje labb innehåller artefakter från den offentliga DevTest Labs artefakt-lagringsplats samt artefakter som du har skapat och lagt till i din egen artefakt lagrings plats.

* Med Azure DevTest Labs *artefakter* kan du ange *åtgärder* som utförs när den virtuella datorn är etablerad, till exempel köra Windows PowerShell-skript, köra bash-kommandon och installera program vara.
* Med artefakt *parametrar* kan du anpassa artefakten för ditt specifika scenario

Information om hur du skapar artefakter finns i artikeln [Lär dig hur du skapar dina egna artefakter för användning med DevTest Labs](devtest-lab-artifact-author.md).

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**och välj sedan **DevTest Labs** i listan.
1. I listan med labb väljer du det labb som innehåller den virtuella dator som du vill arbeta med.
1. Välj **mina virtuella datorer**.
1. Välj önskad virtuell dator.
1. Välj **Hantera artefakter**.
1. Välj **tillämpa artefakter**.
1. I fönstret **Använd artefakter** väljer du den artefakt som du vill lägga till i den virtuella datorn.
1. I fönstret **Lägg till artefakt** anger du de parameter värden som krävs och eventuella valfria parametrar som du behöver.
1. Välj **Lägg till** för att lägga till artefakten och gå tillbaka till fönstret **Använd artefakter** .
1. Fortsätt att lägga till artefakter efter behov för den virtuella datorn.
1. När du har lagt till dina artefakter kan du [ändra i vilken ordning artefakterna ska köras](#change-the-order-in-which-artifacts-are-run). Du kan också gå tillbaka om du vill [Visa eller ändra en artefakt](#view-or-modify-an-artifact).
1. När du är klar med att lägga till artefakter väljer du **tillämpa**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Ändra i vilken ordning artefakter körs
Som standard körs aktiviteterna i artefakterna i den ordning som de läggs till i den virtuella datorn.
Följande steg illustrerar hur du ändrar i vilken ordning artefakterna körs.

1. Längst upp i fönstret **Använd artefakter** väljer du den länk som anger antalet artefakter som har lagts till den virtuella datorn.

    ![Antal artefakter som har lagts till i den virtuella datorn](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. I fönstret **valda artefakter** drar du och släpper artefakterna i önskad ordning. **Obs:** Om du har problem med att dra artefakten ser du till att du drar från den vänstra sidan av artefakten.
1. Välj **OK** när du är klar.

## <a name="view-or-modify-an-artifact"></a>Visa eller ändra en artefakt
Följande steg visar hur du visar eller ändrar parametrarna för en artefakt:

1. Längst upp i fönstret **Använd artefakter** väljer du den länk som anger antalet artefakter som har lagts till den virtuella datorn.

    ![Antal artefakter som har lagts till i den virtuella datorn](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. I fönstret **valda artefakter** väljer du den artefakt som du vill visa eller redigera.
1. I fönstret **Lägg till artefakt** gör du nödvändiga ändringar och väljer **OK** för att stänga fönstret **Lägg till artefakt** .
1. Klicka på **OK** för att stänga fönstret **valda artefakter** .

## <a name="save-azure-resource-manager-template"></a>Spara Azure Resource Manager mall
En Azure Resource Manager-mall är ett deklarativ sätt att definiera en repeterbar distribution.
Följande steg beskriver hur du sparar Azure Resource Manager-mallen för den virtuella datorn som skapas.
När du har sparat kan du använda Azure Resource Manager-mallen för att [distribuera nya virtuella datorer med Azure PowerShell](../azure-resource-manager/templates/overview.md).

1. I fönstret **virtuell dator** väljer du **Visa Azure Resource Manager mall**.
2. I fönstret **visa Azure Resource Manager mall** väljer du mal len text.
3. Kopiera den markerade texten till Urklipp.
4. Välj **OK** för att stänga **fönstret vy Azure Resource Manager mall**.
5. Öppna en textredigerare.
6. Klistra in mal Lav bildtext från Urklipp.
7. Spara filen för senare användning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* När den virtuella datorn har skapats kan du ansluta till den virtuella datorn genom att välja **Anslut** i den virtuella datorns fönster.
* Lär dig hur du [skapar anpassade artefakter för den virtuella DevTest Labs](devtest-lab-artifact-author.md)-datorn.
* Utforska [DevTest Labs Azure Resource Manager snabb starts galleriet](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
