---
title: Skapa och hantera virtuella datorer som går att frigöra i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att lägga till en virtuell dator som kan frigöras i Azure DevTest Labs och se de processer som följer efter att anspråk/frigör en virtuell dator.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1fe949d7104fe6437087e77c403cc0d921ebd025
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270987"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Skapa och hantera virtuella datorer som går att frigöra i Azure DevTest Labs
Du lägger till en virtuell dator som kan frigöras i ett labb på liknande sätt som du [lägger till en virtuell standard-VM](devtest-lab-add-vm.md) – från en *bas* som är antingen en [anpassad avbildning](devtest-lab-create-template.md), [formel](devtest-lab-manage-formulas.md)eller [Marketplace-avbildning](devtest-lab-configure-marketplace-images.md). Den här självstudien vägleder dig genom att använda Azure Portal för att lägga till en virtuell dator som kan frigöras i ett labb i DevTest Labs, och visar de processer som en användare följer för att begära och frigöra den virtuella datorn.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Steg för att lägga till en virtuell dator med anspråk i ett labb i Azure DevTest Labs
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
3. Välj **skapa** för att lägga till den angivna virtuella datorn i labbet.

   På sidan labb visas statusen för den virtuella datorns skapande – först som du **skapar**, sedan som **körs** när den virtuella datorn har startats.

> [!NOTE]
> Om du distribuerar virtuella labb datorer via [Azure Resource Manager mallar](devtest-lab-create-environment-from-arm.md)kan du skapa virtuella datorer som kan krävas genom att ange egenskapen **allowClaim** till true i avsnittet Egenskaper.


## <a name="using-a-claimable-vm"></a>Använda en virtuell dator med anspråk

En användare kan göra anspråk på vilken virtuell dator som helst från listan över "anspråk bara virtuella datorer" genom att göra något av följande:

* I listan över "anspråk bara virtuella datorer" längst ned i labb rutan "Översikt" högerklickar du på en av de virtuella datorerna i listan och väljer **anspråks dator**.

  ![Begär en speciell virtuell dator med anspråk.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Överst i fönstret "Översikt" väljer du **anspråks alla**. En slumpmässig virtuell dator tilldelas från listan över virtuella datorer som kan krävas.

  ![Begär alla virtuella datorer som går att frigöra.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


När en användare hävdar en virtuell dator startar DevTest Labs datorn och flyttar den till labb användarens lista över "mina virtuella datorer". Det innebär att labb användaren nu har ägar behörighet på den här datorn. Tiden som krävs för det här steget kan variera beroende på Start tider samt eventuella andra anpassade åtgärder som utförs under anspråks händelsen. När det har begärts är datorn inte längre tillgänglig i den anspråk bara poolen.  

## <a name="unclaim-a-vm"></a>Frigöra en virtuell dator

När en användare är färdig med en virtuell dator som har en virtuell dator och vill göra den tillgänglig för någon annan, kan de returnera den virtuella datorn som omfattas av listan över virtuella datorer som kan frigöras genom att göra något av följande:

- I listan med "mina virtuella datorer" högerklickar du på en av de virtuella datorerna i listan, eller så väljer du dess ellips (...) – och väljer inte **anspråk**.

  ![Frigör en virtuell dator i listan över virtuella datorer.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- I listan med "mina virtuella datorer" väljer du en virtuell dator för att öppna hanterings fönstret och väljer sedan **frigör** från den översta meny raden.

  ![Frigör en virtuell dator i hanterings fönstret för den virtuella datorn.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

När en användare frånsäger sig en virtuell dator har han/hon inte längre ägar behörigheter för den aktuella virtuella datorn i labbet och det är tillgängligt att begäras av andra labb användare i det tillstånd som den returnerades till poolen. 

### <a name="transferring-the-data-disk"></a>Data disken överförs
Om en virtuell dator med en virtuell dator har en datadisk ansluten till den och en användare inte har gjort anspråk på den, stannar data disken med den virtuella datorn. När en annan användare sedan anspråk på den virtuella datorn anlitar den nya användaren data disken samt den virtuella datorn.

Detta kallas "överföring av data disk". Data disken blir sedan tillgänglig i den nya användarens lista över **mina data diskar** som de kan hantera.

![Frigör data diskar.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Nästa steg
* När den har skapats kan du ansluta till den virtuella datorn genom att välja **Anslut** i hanterings fönstret.
* Utforska [DevTest Labs Azure Resource Manager snabb starts galleriet](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
