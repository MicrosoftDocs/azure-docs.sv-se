---
title: Begär en ökning av vCPU kvot gränser per Azure VM-serien
description: Så här begär du en ökning av vCPU-kvot gränsen för en VM-serie i Azure Portal, vilket ökar den totala regionala vCPU-gränsen med samma belopp.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5db3e538a64e275313e1e0ab01f6cc6350eabb77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745442"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Standardkvot: Öka gränserna med virtuella datorserier 

Azure Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer:

* *Virtuella datorer med betala per* användning och *reserverade VM-instanser* omfattas av en *standard vCPU-kvot*.
* *Virtuella datorer för virtuella datorer* omfattas av en *vCPU kvot*.

Standard kvoten för vCPU för att betala per användning och reserverade instanser av virtuella datorer tillämpas på två nivåer för varje prenumeration i varje region:

* Den första nivån är den *totala regionala virtuella processorer-gränsen* för alla VM-serier.
* Den andra nivån är *virtuella processorer-gränsen per VM-serien*, till exempel dv3-serien virtuella processorer.

När du distribuerar en ny virtuell dator, får den totala nya och befintliga vCPU-användningen för alla VM-instanser för virtuella datorer inte överskrida den godkända kvot gränsen för vCPU. Om kvoten överskrids tillåts inte distributionen av virtuella datorer.

Du kan begära en ökning av vCPU kvot gräns för VM-serien med hjälp av Azure Portal. En ökning av kvoten för VM-serien ökar automatiskt den totala regionala vCPU-gränsen med samma belopp.

Mer information om standard kvoter för vCPU finns i [vCPU-kvoter för virtuella datorer](../../virtual-machines/windows/quotas.md) och [begränsningar för Azure-prenumerationer och tjänster](./classic-deployment-model-quota-increase-requests.md).

Mer information om hur du ökar vCPU-gränsen per region för standard kvot finns i [standard kvot: öka gränserna efter region](regional-quota-requests.md).

Mer information om hur du ökar vCPU-gränser för virtuella datorer finns i [punkt kvot: öka gränserna för alla VM-serier](low-priority-quota.md).

Du kan begära en ökning av standard kvot gränser för vCPU per VM-serien på två sätt, enligt beskrivningen i följande avsnitt.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Begär en standard kvot ökning från hjälp + support

Så här begär du en ökad standard vCPU-kvot per VM-serien från **Hjälp + Support**:

> [!NOTE]
> Du kan också begära en ökad kvot gräns för flera regioner genom ett enda support ärende. Mer information finns i steg 8.

1. På [Azure Portal](https://portal.azure.com) -menyn väljer du **Hjälp + Support**.

   ![Länken Hjälp + Support](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. I **Hjälp + Support** väljer du **ny supportbegäran**.

    ![Skapa en ny supportbegäran](./media/resource-manager-core-quotas-request/new-support-request.png)

1. För **typ av problem** väljer du **begränsningar för tjänsten och prenumerationen (kvoter)**.

   ![Välj en typ av problem](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. För **prenumeration** väljer du den prenumeration vars kvot du vill öka.

   ![Välj en prenumeration för ökad kvot](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. För **typ av kvot**, Välj **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar**.

   ![Välj en kvot typ](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Välj **Nästa: lösningar** för att öppna **problem information**. Välj **Ange information** om du vill ange ytterligare information.

   ![Länken "Tillhandahåll information"](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. I **kvot informationen** utför du följande steg:

   ![TProvide ytterligare kvot information](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. För **distributions modell** väljer du lämplig modell.

   1. För **platser** väljer du en plats. Välj **standard** under **typer** i **Välj en typ** för den valda platsen.

      ![Kvot information – kvot typer](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Under **typer** kan du begära både standard-och plats kvot typer från ett enda support ärende via stöd för flera markeringar.

      Mer information om hur du ökar kvot gränserna finns i [Azure-virtuella datorer för skalnings uppsättningar för virtuella datorer](../../virtual-machine-scale-sets/use-spot.md).

   1. I **standard** väljer du SKU-serien för ökade kvoter.

      ![Kvot information – SKU-serien](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Ange de nya kvot gränser som du vill använda för den här prenumerationen. Om du vill ta bort en SKU från listan avmarkerar du kryss rutan bredvid SKU: n eller väljer ikonen Ta bort X.

      ![Välj en ny vCPU-gräns](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Om du vill begära en kvot ökning för mer än en plats väljer du en annan plats i **platser** och väljer sedan en lämplig VM-typ. Du kan sedan ange en gräns som gäller för den ytterligare platsen.

   ![Ange ytterligare platser i kvot information](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Välj **Spara och fortsätt** att skapa support förfrågan.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Begär en standard kvot ökning från prenumerationer

Så här begär du en kvot ökning för standard vCPU per VM-serien från **prenumerationer**:

> [!NOTE]
> Du kan också begära en ökad kvot gräns för flera regioner genom ett enda support ärende. Mer information finns i steg 7.

1. Sök efter och välj **prenumerationer** i [Azure Portal](https://portal.azure.com).

   ![Prenumerationer i Azure Portals sökning](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Välj den prenumeration vars kvot du vill öka.

   ![Prenumerationer att välja för ändringar](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. I det vänstra fönstret väljer du **användning + kvoter**.

   ![Länken "användning + kvoter"](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. Välj **begär ökning** längst upp till höger.

   ![Välj för att öka kvoten](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. För **typ av kvot**, Välj **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar**.

   ![Välj en kvot typ](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. I **kvot informationen** utför du följande steg:

   1. För **distributions modell** väljer du lämplig modell och för **platser** väljer du en plats.

      ![Ange kvot information](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. För den valda platsen, under **typer**, väljer du **Välj en typ** och väljer sedan **standard**.

      ![Välj standard typ](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      Under **typer** kan du begära både standard-och plats kvot typer från ett enda support ärende via stöd för flera markeringar.

      Mer information om hur du ökar kvot gränserna finns i [Azure-virtuella datorer för skalnings uppsättningar för virtuella datorer](../../virtual-machine-scale-sets/use-spot.md).

   1. För **standard** väljer du den SKU-serie vars kvoter du vill öka.

      ![Kvot information – SKU-serien](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Ange de nya kvot gränser som du vill använda för den här prenumerationen. Om du vill ta bort en SKU från listan avmarkerar du kryss rutan bredvid SKU: n eller väljer ikonen Ta bort X.

      ![Välj en ny vCPU-gräns](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Om du vill begära en kvot ökning för mer än en plats väljer du en annan plats i **platser** och väljer sedan en lämplig VM-typ.

   I det här steget förinstalleras den SKU-serie som du har valt för tidigare platser. Ange de kvot gränser som du vill använda för ytterligare serier.

   ![Välj ytterligare platser i kvot information](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Välj **Spara och fortsätt** att skapa support förfrågan.