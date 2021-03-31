---
title: Skapa en anpassad inställning i Azure automanage för virtuella datorer
description: Lär dig hur du anpassar miljö konfigurationen i Azure automanage och anger dina egna inställningar.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 584a3503bf736fcf727a169611e6c79e0c374c90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647974"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Skapa en anpassad inställning i Azure automanage för virtuella datorer

Azure automanage för den virtuella datorns bästa praxis har standard miljöer som kan justeras vid behov. Den här artikeln förklarar hur du kan ställa in egna inställningar när du aktiverar automanagement på en ny eller befintlig virtuell dator.

Vi stöder för närvarande anpassningar på [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) och [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


> [!NOTE]
> Du kan inte ändra miljön eller preferensen på den virtuella datorn när den automatiska hanteringen är aktive rad. Du måste inaktivera automatisk hantering för den virtuella datorn och sedan återaktivera automatisk hantering med önskad konfigurations miljö och inställningar.


## <a name="prerequisites"></a>Förutsättningar

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> De kostnads fria utvärderings kontona har inte åtkomst till de virtuella datorerna som används i den här självstudien. Uppgradera till en prenumeration där du betalar per användning.

> [!IMPORTANT]
> Följande Azure RBAC-behörighet krävs för att aktivera autohantering: **ägar** rollen eller **deltagare** tillsammans med **Administratörs roller för användar åtkomst** .


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Aktivera autohantering för virtuella datorer på en befintlig virtuell dator

1. I Sök fältet söker du efter och väljer **autohantera – metod tips för Azure Machine**.

2. Välj **Aktivera på den befintliga virtuella datorn**.

3. På bladet **Välj datorer** :
    1. Filtrera listan med virtuella datorer efter din **prenumeration** och **resurs grupp**.
    1. Markera kryss rutan för varje virtuell dator som du vill publicera.
    1. Klicka på knappen **Välj** .

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Välj befintlig virtuell dator i listan över tillgängliga virtuella datorer.":::

    > [!NOTE]
    > Klicka på **Visa ej kvalificerade datorer** om du vill se en lista över datorer som inte stöds och orsaken. 

4. Under **konfiguration** klickar du på **Jämför miljöer**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Jämför miljöer.":::

5. På bladet **miljö information** väljer du en miljö på den nedrullningsbara menyn: *utveckling/testning* för testning, *Prod* för produktion och klicka på **OK**

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Bläddra i produktions miljö.":::

6. När du har valt din miljö kan du välja **konfigurations inställningar**. Som standard används inställningen Azure Best Practices. Den här inställningen innehåller de rekommenderade inställningarna för varje tjänst. Ändra inställningarna genom att skapa en anpassad inställning: 
    1. Klicka på **skapa nya inställningar**.
    1. På bladet **skapa en konfigurations inställning** fyller du i fliken grundläggande:
        1. Prenumeration
        1. Resursgrupp
        1. Preference-namn
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Fyll i konfigurations inställningar.":::

7. Justera de konfigurations inställningar som du vill använda.
        
    > [!NOTE]
    > Det är bara justeringar som fortfarande passar i våra bästa praxis övre och nedre gränser som tillåts när du ändrar miljö konfigurationerna.

8. Granska konfigurations informationen.
9. Klicka på knappen **Skapa**.

10. Klicka på knappen **Aktivera**.


## <a name="disable-automanage-for-vms"></a>Inaktivera autohantering för virtuella datorer

Sluta snabbt använda Azure automanagement för virtuella datorer genom att inaktivera automanagement.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Inaktiverar autohantering på en virtuell dator.":::

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

Få de vanligaste frågorna i vanliga frågor och svar. 

> [!div class="nextstepaction"]
> [Vanliga frågor och svar](faq.md)