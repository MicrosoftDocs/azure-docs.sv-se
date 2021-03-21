---
title: Snabb start – aktivera Azure automanage för virtuella datorer i Azure Portal
description: Lär dig hur du snabbt aktiverar autohantering för virtuella datorer på en ny eller befintlig virtuell dator i Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: 5f5f1e70d9ae309c90291ccac1e6dd61e7a9d056
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038431"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Snabb start: Aktivera Azure automanage för virtuella datorer i Azure Portal

Kom igång med Azure automanage för virtuella datorer med hjälp av Azure Portal för att aktivera autohantering på en ny eller befintlig virtuell dator.


## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> De kostnads fria utvärderings kontona har inte åtkomst till de virtuella datorerna som används i den här självstudien. Uppgradera till en prenumeration där du betalar per användning.

> [!IMPORTANT]
> Du måste ha **deltagar** rollen i resurs gruppen som innehåller dina virtuella datorer för att aktivera autohantering med ett befintligt konto för autohantering. Om du aktiverar automanage med ett nytt konto för autohantering behöver du följande behörigheter: **ägar** roll eller **deltagare** tillsammans med administratörs roller för **användar åtkomst** i din prenumeration.


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Aktivera autohantering för en enskild virtuell dator

1. Bläddra till den virtuella dator som du vill aktivera.

2. Klicka på posten **automanage (för hands version)** i innehålls förteckningen under **åtgärder**.

3. Välj **Kom igång**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="Kom igång med en enda virtuell dator.":::

4. Välj inställningar för autohantering (miljö, inställningar, autohantera konto) och tryck på **Aktivera**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="Aktivera på en enskild virtuell dator.":::

## <a name="enable-automanage-for-multiple-vms"></a>Aktivera autohantering för flera virtuella datorer

1. I Sök fältet söker du efter och väljer **autohantera – metod tips för Azure Machine**.

2. Välj **Aktivera på den befintliga virtuella datorn**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Aktivera på en befintlig virtuell dator.":::

3. På bladet **Välj datorer** :
    1. Filtrera listan med virtuella datorer efter din **prenumeration** och **resurs grupp**.
    1. Markera kryss rutan för varje virtuell dator som du vill publicera.
    1. Klicka på knappen **Välj** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Välj befintlig virtuell dator i listan över tillgängliga virtuella datorer.":::

4. Under **miljö** väljer du din miljö typ: **utveckling/testning** eller **produktion**. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Välj miljöer.":::

   Klicka på **Jämför miljö information** för att se skillnaderna mellan miljöerna.
    1. Välj en miljö i list rutan: *utveckling/testning* för testning, *produktion* för produktion.
    1. Klicka på **OK**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Bläddra i produktions miljö.":::

5. Som standard väljs inställningen **Azure Best Practices** för konfigurations inställningarna. Om du vill ändra detta skapar du en ny inställning eller väljer en befintlig. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="Skapa preferens.":::

6. Klicka på knappen **Aktivera**.


## <a name="enable-automanage-for-a-new-vm"></a>Aktivera automanage för en ny virtuell dator

Logga in på Azure Portal [här](https://aka.ms/AzureAutomanagePreview) om du vill skapa en ny virtuell dator och aktivera automanage.

1. Fyll i fliken **grundläggande** med din VM-information.

> [!NOTE]
> Kontrol lera de [regioner som stöds](automanage-virtual-machines.md#supported-regions) för att hantera och hantera [Linux-distributioner](automanage-linux.md#supported-linux-distributions-and-versions) och [Windows Server-versioner](automanage-windows-server.md#supported-windows-server-versions)som stöds.

2. Bläddra till fliken **hantering** och välj den **automatiska hanterings miljön**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="Aktivera fliken Hantera på hantering.":::

3. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.

4. När du ser meddelandet att valideringen har slutförts väljer du **skapa**.

## <a name="disable-automanage-for-vms"></a>Inaktivera autohantering för virtuella datorer

Sluta snabbt använda Azure automanagement för virtuella datorer genom att inaktivera automanagement.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Inaktiverar autohantering på en virtuell dator.":::

1. Gå till sidan för **metod tips för automatisk hantering – Azure virtuell dator** som visar en lista över alla dina automatiskt hanterade virtuella datorer.
1. Markera kryss rutan bredvid den virtuella dator som du vill inaktivera.
1. Klicka på knappen **inaktivera automanagent** .
1. Läs noggrant igenom meddelande tjänsten i det resulterande popup-meddelandet innan du godkänner **inaktive ring**.


## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resurs grupp för att testa Azure automanage för virtuella datorer och inte längre behöver den, kan du ta bort resurs gruppen. Om du tar bort gruppen raderas även den virtuella datorn och alla resurser i resurs gruppen.

Med Azure automanage skapas standard resurs grupper för lagring av resurser i. Kontrol lera resurs grupper som har namngivnings konventionen "DefaultResourceGroupRegionName" och "AzureBackupRGRegionName" för att rensa alla resurser.

1. Välj **resurs gruppen**.
1. Välj **ta bort** på sidan för resurs gruppen.
1. Bekräfta namnet på resurs gruppen när du uppmanas till det och välj sedan **ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här snabb starten aktiverade du Azure automanage för virtuella datorer.

Upptäck hur du kan skapa och använda anpassade inställningar när du aktiverar automatisk hantering på den virtuella datorn.

> [!div class="nextstepaction"]
> [Automatisk Azure-hantering för virtuella datorer – anpassade konfigurations inställningar](virtual-machines-custom-preferences.md)
