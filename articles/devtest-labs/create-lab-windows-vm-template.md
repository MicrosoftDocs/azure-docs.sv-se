---
title: Skapa ett labb med Azure DevTest Labs och Azure Resource Manager mall
description: I den här snabb starten skapar du ett labb i Azure DevTest Labs med hjälp av en Azure Resource Manager-mall (ARM-mall). En labb administratör konfigurerar ett labb, skapar virtuella datorer i labbet och konfigurerar principer.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 2b825b4d4485f401199556b6faaef0017f583cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91461197"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>Snabb start: Konfigurera ett labb med hjälp av Azure DevTest Labs ARM-mall
I den här snabb starten skapar du ett labb med en dator med Windows Server 2019 datacenter med hjälp av en Azure Resource Manager mall (ARM-mall). 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

I den här snabb starten utför du följande åtgärder:

> [!div class="checklist"]
> * Granska mallen 
> * Distribuera mallen
> * Kontrollera mallen
> * Rensa resurser

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/).

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json":::

De resurser som definieras i mallen omfattar:

- [**Microsoft. DevTestLab/Labs**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft. DevTestLab Labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft. DevTestLab Labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

Mer information om mallar finns i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Distribuera mallen
Om du vill köra distributionen automatiskt klickar du på följande knapp. 

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Skapa en **Ny resurs grupp** så att det är enkelt att rensa upp senare.
1. Välj en **plats** för resurs gruppen. 
1. Ange ett **namn på labbet**. 
1. Ange ett **namn för den virtuella datorn**. 
1. Ange ett **användar namn** som har åtkomst till den virtuella datorn. 
1. Ange **lösenord** för användaren. 
1. Välj **Jag accepterar de villkor som anges ovan**. 
1. Välj sedan **köp**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Sidan distribuera mall":::

## <a name="validate-the-deployment"></a>Verifiera distributionen
1. Välj **meddelanden** högst upp för att se status för distributionen och klicka på **distribution pågår** länk.

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Distributionsmeddelande":::
2. På sidan **distribution – översikt** väntar du tills distributionen är klar. Den här åtgärden (särskilt att skapa en virtuell dator) tar lite tid att slutföra. Välj sedan din **gå till resurs gruppen** eller **namnet på resurs gruppen** enligt följande bild: 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Gå till resurs grupp":::
3. På sidan **resurs grupp** visas en lista över resurser i resurs gruppen. Bekräfta att du ser ditt labb av typen: `DevTest Lab` i resurserna. Du ser även de beroende resurserna, till exempel virtuella nätverk och virtuella datorer i resurs gruppen. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="Resurs gruppens start sida":::
4. Välj ditt labb i listan över resurser för att se start sidan för ditt labb. Bekräfta att du ser den virtuella Windows Server 2019 datacenter-datorn i listan **mina virtuella datorer** . I följande bild minimeras avsnittet **Essentials** . 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Start sida för labbet":::

    > [!IMPORTANT] 
    > Se till att sidan är öppen och följ anvisningarna i nästa avsnitt för att rensa resurser för att undvika kostnader för att köra labbet och den virtuella datorn på Azure. Om du vill gå igenom nästa självstudie för att testa att komma åt den virtuella datorn i labbet kan du rensa resurserna när du har gått igenom självstudierna. 

## <a name="clean-up-resources"></a>Rensa resurser

1. Ta först bort labbet så att du kan ta bort resurs gruppen. Du kommer inte att kunna ta bort resurs gruppen med ett labb i den. Om du vill ta bort labbet väljer du **ta bort** i verktygsfältet. 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Knappen Ta bort labb":::
 2. Skriv **labb namnet** på sidan bekräftelse och välj **ta bort**. 
 3. Vänta tills labbet har tagits bort. Välj **klock** ikonen för att se meddelandet från borttagnings åtgärden. Den här processen tar lite tid. Bekräfta borttagningen av labbet och välj sedan **resurs gruppen** på menyn för dynamiska länkar. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Bekräfta borttagning av virtuell dator i meddelanden":::
 1. På sidan **resurs grupp** väljer du **ta bort resurs grupp** i verktygsfältet. Skriv **namnet på resurs gruppen** på sidan bekräftelse och välj **ta bort**. Kontrol lera meddelandena för att bekräfta att resurs gruppen har tagits bort.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Knappen Ta bort resurs grupp":::

## <a name="next-steps"></a>Nästa steg
I den här snabb starten skapade du ett labb med en virtuell dator. Om du vill veta mer om hur du kommer åt labbet går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Självstudie: Åtkomst till labb](tutorial-use-custom-lab.md)
