---
title: Kvot för plats för virtuell dator
description: Öka kvot gränserna för virtuella datorer med virtuella datorer, vilket ger en modell av Azure-användning som låter dig ta med lägre kostnader i Exchange för att låta Azure ta bort virtuella datorer vid behov.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745493"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Punkt kvot: öka gränserna för alla VM-serier

Virtuella datorer (VM) tillhandahåller en annan modell för Azure-användning. De låter dig säga lägre kostnader i Exchange för att låta Azure ta bort virtuella datorer efter behov för distributioner med betala per användning eller reserverad VM-instans. Mer information om virtuella datorer finns i [Azure-virtuella datorer för skalnings uppsättningar för virtuella datorer](../../virtual-machine-scale-sets/use-spot.md).

Azure Resource Manager stöder två typer av vCPU-kvoter för virtuella datorer:

* *Virtuella datorer med betala per* användning och *reserverade VM-instanser* omfattas av en *standard vCPU-kvot*.
* *Virtuella datorer för virtuella datorer* omfattas av en *vCPU kvot*.

För kvot typen vCPU används Resource Manager vCPU kvoter i alla tillgängliga serier för virtuella datorer som en enda regional gräns.

När du distribuerar en ny virtuell dator, får den totala nya och befintliga vCPU-användningen för alla VM-instanser för virtuella datorer inte överskrida den godkända kvot gränsen för vCPU. Om kvoten överskrids tillåts inte distributionen av virtuella datorer.

Den här artikeln beskriver hur du begär en ökning av kvot gränsen för vCPU med hjälp av Azure Portal.

Mer information om standard kvoter för vCPU finns i [vCPU-kvoter för virtuella datorer](../../virtual-machines/windows/quotas.md) och [begränsningar för Azure-prenumerationer, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Mer information om hur du ökar vCPU-gränsen per region finns i [standard kvot: öka gränserna efter region](regional-quota-requests.md).

## <a name="request-a-quota-limit-increase-from-help--support"></a>Begär en kvot gräns öka från hjälp + support

Så här begär du en ökning av plats kvoten för alla serie nummer för virtuella datorer med **Hjälp + Support**:

> [!NOTE]
> Du kan också begära en ökad kvot gräns för flera regioner genom ett enda support ärende. Mer information finns i steg 8.

1. Från [Azure Portal](https://portal.azure.com) -menyn väljer du **Hjälp + Support**.

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

1. I **kvot information**, utför följande steg:

   1. För **distributions modell** väljer du lämplig modell och för **platser** väljer du en plats.

      ![Ange ytterligare kvot information](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Välj **dekor** under **typer** i **Välj en typ** för den valda platsen.

      ![Välj dekor typ](./media/resource-manager-core-quotas-request/select-spot-type.png)

       Under **typer** kan du begära både standard-och plats kvot typer från ett enda support ärende via stöd för flera markeringar.

       Mer information finns i [standard kvot: öka gränserna efter VM-serien](per-vm-quota-requests.md).

   1. Ange den nya kvot gränsen som du vill använda för den här prenumerationen.

      ![Välj en ny kvot för VM-VM](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Om du vill begära en kvot ökning för mer än en plats väljer du en annan plats i **platser** och väljer sedan en lämplig VM-typ. Du kan sedan ange en gräns som gäller för den ytterligare platsen.

   ![Ange ytterligare platser i kvot information](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Välj **Spara och fortsätt** att skapa support förfrågan.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Begär en kvot gräns ökning från fönstret prenumerationer

Så här begär du en ökning av plats kvoten för alla VM-serier från fönstret **prenumerationer** :

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

1. Välj **Nästa: lösningar** för att öppna **problem information**. Välj **Ange information** om du vill ange ytterligare information. I **kvot information** anger du följande information:

   1. För **distributions modell** väljer du lämplig modell och för **platser** väljer du en plats.

      ![Ange kvot information](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. Välj **dekor** under **typer** i **Välj en typ** för den valda platsen.

      ![Välj dekor typ](./media/resource-manager-core-quotas-request/select-spot-type.png)

      Mer information finns i [standard kvot: öka gränserna efter VM-serien](per-vm-quota-requests.md).

   1. Ange den nya kvot gränsen som du vill använda för den här prenumerationen.

      ![Ange ett nytt värde för vCPU-gränsen](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Om du vill begära en kvot ökning för mer än en plats väljer du en annan plats i **platser** och väljer sedan en lämplig VM-typ. Du kan sedan ange en gräns som gäller för den ytterligare platsen.

   ![Välj ytterligare platser i kvot information](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Välj **Spara och fortsätt** att skapa support förfrågan.
