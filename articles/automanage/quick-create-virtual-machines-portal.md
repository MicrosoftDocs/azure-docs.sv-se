---
title: Snabbstart – Aktivera Azure Automanage för virtuella datorer i Azure Portal
description: Lär dig hur du snabbt aktiverar automanage för virtuella datorer på en ny eller befintlig virtuell dator i Azure Portal.
author: ju-shim
ms.author: jushiman
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.custom:
- mode-portal
ms.openlocfilehash: 7121d83f9401fe985966324afe6a61cf8396b2bb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534066"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Snabbstart: Aktivera Azure Automanage för virtuella datorer i Azure Portal

Kom igång med Azure Automanage för virtuella datorer med hjälp av Azure Portal för att aktivera automanagement på en ny eller befintlig virtuell dator.


## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> Konton för kostnadsfri utvärdering har inte åtkomst till de virtuella datorer som används i den här självstudien. Uppgradera till en prenumeration där du betalar per prenumeration.

> [!IMPORTANT]
> Du måste ha rollen **Deltagare i resursgruppen** som innehåller dina virtuella datorer för att aktivera Automanage med hjälp av ett befintligt automanagekonto. Om du aktiverar Automanage med ett nytt automanagekonto behöver du  följande **behörigheter:** Ägarroll eller Deltagare samt administratörsroller **för** användaråtkomst i din prenumeration.


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Aktivera automanage för en enskild virtuell dator

1. Bläddra till den virtuella dator som du vill aktivera.

2. Klicka på **posten Automanage (Preview) (Automanage (förhandsversion)** i innehållsförteckningen under **Åtgärder**.

3. Välj **Kom igång.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="Kom igång med en enskild virtuell dator.":::

4. Välj dina inställningar för automanage (miljö, inställningar, automanage-konto) och tryck på **Aktivera**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="Aktivera på en enskild virtuell dator.":::

## <a name="enable-automanage-for-multiple-vms"></a>Aktivera automanage för flera virtuella datorer

1. I sökfältet söker du efter och väljer Metodtips **för Automatisk hantering – Azure-dator.**

2. Välj Aktivera **på befintlig virtuell dator.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Aktivera på befintlig virtuell dator.":::

3. På **bladet Välj** datorer:
    1. Filtrera listan över virtuella datorer efter din **prenumeration** och **resursgrupp.**
    1. Markera kryssrutan för varje virtuell dator som du vill publicera.
    1. Klicka på **knappen** Välj.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Välj en befintlig virtuell dator i listan över tillgängliga virtuella datorer.":::

4. Under **Miljö** väljer du din miljötyp: **Dev/Test** eller **Produktion.** 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Välj miljöer.":::

   Klicka **på Jämför miljöinformation** för att se skillnaderna mellan miljöerna.
    1. Välj en miljö i listrutan: *Dev/Test* för testning, *Produktion* för produktion.
    1. Klicka på **OK**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Bläddra i produktionsmiljön.":::

5. Som standard är **azures bästa praxis** valt för konfigurationsinställningarna. Om du vill ändra detta skapar du en ny inställning eller väljer en befintlig. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="Skapa inställningar.":::

6. Klicka på knappen **Aktivera**.


## <a name="enable-automanage-for-a-new-vm"></a>Aktivera automanage för en ny virtuell dator

Logga in på Azure Portal [för](https://aka.ms/AzureAutomanagePreview) att skapa en ny virtuell dator och aktivera Automanage.

1. Fyll i **fliken Grundläggande med** information om den virtuella datorn.

> [!NOTE]
> Kontrollera de regioner som stöds [automatiskt och](automanage-virtual-machines.md#supported-regions) de Linux-distributioner som stöds automatiskt [och](automanage-linux.md#supported-linux-distributions-and-versions) [Windows Server-versioner.](automanage-windows-server.md#supported-windows-server-versions)

2. Bläddra till **fliken Hantering** och välj din **automanagemiljö.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="Aktivera automatisk hantering på fliken Hantering.":::

3. Låt de återstående standardinställningarna vara och välj sedan knappen **Granska + skapa** längst ned på sidan.

4. När du ser meddelandet att verifieringen har passerat väljer du **Skapa**.

## <a name="disable-automanage-for-vms"></a>Inaktivera automanage för virtuella datorer

Stoppa snabbt användningen Azure Automanage virtuella datorer genom att inaktivera automatisk distribution.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Inaktivera automanage på en virtuell dator.":::

1. Gå till sidan **Metodtips för Automatisk hantering – Virtuell Azure-dator** med en lista över alla dina automatiskt hanterade virtuella datorer.
1. Markera kryssrutan bredvid den virtuella dator som du vill inaktivera.
1. Klicka på knappen **Inaktivera automanagent.**
1. Läs igenom meddelandena noggrant i popup-fönster som visas innan du godkänner **Inaktivera**.


## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resursgrupp för Azure Automanage för virtuella datorer och inte längre behöver den kan du ta bort resursgruppen. Om du tar bort gruppen tas även den virtuella datorn och alla resurser i resursgruppen bort.

Azure Automanage skapar standardresursgrupper att lagra resurser i. Kontrollera resursgrupper som har namngivningskonventionen "DefaultResourceGroupRegionName" och "AzureBackupRGRegionName" för att rensa alla resurser.

1. Välj **resursgruppen**.
1. På sidan för resursgruppen väljer du Ta **bort**.
1. När du uppmanas till det bekräftar du namnet på resursgruppen och väljer sedan Ta **bort**.


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du Azure Automanage för virtuella datorer.

Upptäck hur du kan skapa och tillämpa anpassade inställningar när du aktiverar automanage på den virtuella datorn.

> [!div class="nextstepaction"]
> [Azure Automanage för virtuella datorer – anpassade konfigurationsinställningar](virtual-machines-custom-preferences.md)
