---
title: Begär en ökning av kvot gränserna för regionala vCPU i Azure
description: Så här begär du en ökning av kvot gränsen för vCPU för en region i Azure Portal.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745425"
---
# <a name="standard-quota-increase-limits-by-region"></a>Standard kvot: öka gränserna efter region

Azure Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer:

* *Virtuella datorer med betala per* användning och *reserverade VM-instanser* omfattas av en *standard vCPU-kvot*.
* *Virtuella datorer för virtuella datorer* omfattas av en *vCPU kvot*.

Standard kvoten för vCPU för att betala per användning och reserverade instanser av virtuella datorer tillämpas på två nivåer för varje prenumeration i varje region:

* Den första nivån är den *totala regionala virtuella processorer-gränsen* för alla VM-serier.
* Den andra nivån är *virtuella processorer-gränsen per VM-serien*, till exempel D-seriens virtuella processorer.

När du distribuerar en ny virtuell dator, får den totala nya och befintliga vCPU-användningen för VM-serien inte överskrida den godkända vCPU-kvoten för den aktuella VM-serien. Dessutom får det totala antalet nya och befintliga virtuella processorer som distribueras över alla VM-serien inte överstiga den totala godkända regionala vCPU-kvoten för prenumerationen. Om någon av dessa kvoter överskrids tillåts inte VM-distributionen.

Du kan begära en ökning av vCPU kvot gräns för VM-serien med hjälp av Azure Portal. En ökning av kvoten för VM-serien ökar automatiskt den totala regionala vCPU-gränsen med samma belopp.

När du skapar en ny prenumeration kanske det totala antalet regionala virtuella processorer inte är lika med den totala standard kvoten för vCPU för alla enskilda VM-serier. Den här avvikelsen kan resultera i en prenumeration med tillräcklig kvot för varje enskild VM-serie som du vill distribuera. Men det kanske inte finns tillräckligt med kvot för att hantera den totala regionala virtuella processorer för alla distributioner. I så fall måste du skicka en begäran om att uttryckligen öka gränsen för det totala antalet regionala virtuella processorer. Den totala regionala vCPU-gränsen får inte överskrida den totala godkända kvoten för alla VM-serier för regionen.

Mer information om standard kvoter för vCPU finns i [vCPU-kvoter för virtuella datorer](../../virtual-machines/windows/quotas.md) och [begränsningar för Azure-prenumerationer, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Mer information om hur du ökar vCPU-gränser för virtuella datorer finns i [punkt kvot: öka gränserna för alla VM-serier](low-priority-quota.md).

Du kan begära en ökning av vCPU standard kvot gräns per region på två sätt.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>Begär en kvot ökning per region från hjälp + support

Så här begär du en vCPU kvot ökning per region från **Hjälp + Support**:

1. Från [Azure Portal](https://portal.azure.com) -menyn väljer du **Hjälp + Support**.

   ![Länken "Help + support"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. I **Hjälp + Support** väljer du **ny supportbegäran**.

    ![Ny supportbegäran](./media/resource-manager-core-quotas-request/new-support-request.png)

1. För **typ av problem** väljer du **begränsningar för tjänsten och prenumerationen (kvoter)**.

   ![Välj en typ av problem](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. För **prenumeration** väljer du den prenumeration vars kvot du vill öka.

   ![Välj en prenumeration](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. För **typ av kvot** väljer du **andra begär Anden**.

   ![Välj en kvot typ](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Välj **Nästa: lösningar** för att öppna **problem information**. I **Beskrivning** anger du följande information:

    1. För **distributions modell** anger du **Resource Manager**.  
    1. För **region** anger du den region som krävs, till exempel **USA, östra 2**.  
    1. För **ny gräns** anger du en ny vCPU-gräns för regionen. Värdet får inte överstiga summan av de godkända kvoterna för enskilda SKU-serier för den här prenumerationen.

    ![Ange information om kvot förfrågan](./media/resource-manager-core-quotas-request/regional-details.png)

1. Välj **Granska + skapa** för att fortsätta skapa support förfrågan.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>Begär en kvot ökning per region från prenumerationer

Så här begär du en vCPU kvot ökning per region från **prenumerationer**:

1. Sök efter och välj **prenumerationer** i [Azure Portal](https://portal.azure.com).

   ![Gå till prenumerationer i Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Välj den prenumeration vars kvot du vill öka.

   ![Välj en prenumeration som ska ändras](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. I det vänstra fönstret väljer du **användning + kvoter**.

   ![Länken Följ användning och kvoter](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Välj **begär ökning** längst upp till höger.

   ![Välj för att öka kvoten](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Välj **andra begär Anden** från **typ av kvot**.

   ![Välj typ av kvot](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. Välj **Nästa: lösningar** för att öppna **problem information**. I rutan **Beskrivning** anger du följande ytterligare information:

    1. För **distributions modell** anger du **Resource Manager**.  
    1. För **region** anger du den region som krävs, till exempel **USA, östra 2**.  
    1. För **ny gräns** anger du en ny vCPU-gräns för regionen. Värdet får inte överstiga summan av de godkända kvoterna för enskilda SKU-serier för den här prenumerationen.

    ![Ange information i information](./media/resource-manager-core-quotas-request/regional-details.png)

1. Välj **Granska + skapa** för att fortsätta skapa support förfrågan.
