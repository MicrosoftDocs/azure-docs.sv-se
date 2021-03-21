---
title: Klassisk Azure-distributions modell
description: Den klassiska distributions modellen, som nu ersatts av Resource Manager-modellen, tillämpar en global vCPU kvot gräns för virtuella datorer och skalnings uppsättningar för virtuella datorer.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b5b5e8c11bfe164aaa3539742dac8c4d267c69e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745612"
---
# <a name="classic-deployment-model"></a>Klassisk distributionsmodell

Den klassiska distributions modellen är den äldre generationens Azure-distributions modell. Den tillämpar en global vCPU kvot gräns för virtuella datorer och skalnings uppsättningar för virtuella datorer. Den klassiska distributions modellen rekommenderas inte längre och har nu ersatts av Resource Manager-modellen.

Mer information om de här två distributions modellerna och fördelarna med att använda Resource Manager finns i [Resource Manager och klassisk distribution](../../azure-resource-manager/management/deployment-models.md).

När en ny prenumeration skapas, tilldelas en standard kvot för virtuella processorer. När en ny virtuell dator ska distribueras med den klassiska distributions modellen, får summan av nya och befintliga vCPU-användning i alla regioner inte överskrida vCPU-kvoten som har godkänts för den klassiska distributions modellen.

Mer information om kvoter finns i [begränsningar, kvoter och begränsningar för Azure-prenumerationer och tjänster](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Du kan begära en ökning av kvot gränsen för vCPU för den klassiska distributions modellen. Använd antingen **Hjälp + Support** eller **användning + kvoter** i Azure Portal.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Begäran per VM-serien vCPU kvot ökning på prenumerations nivå med hjälp + support

Följ instruktionerna nedan om du vill skapa en supportbegäran med **Hjälp + Support** i Azure Portal.

1. Från [Azure Portal](https://portal.azure.com) -menyn väljer du **Hjälp + Support**.

   ![Välj hjälp + support i Azure Portal](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Välj **Ny supportbegäran**.

   ![Skapa en ny supportbegäran i Azure Portal](./media/resource-manager-core-quotas-request/new-support-request.png)

1. I **typ av ärende** väljer du **tjänst-och prenumerations gränser (kvoter)**.

   ![Välj kvoter som typ av problem](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Välj den prenumeration vars kvot du vill öka.

   ![Välj den prenumeration som du vill öka en kvot för](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. För **typ av kvot**, Välj **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar**.

   ![Välj den typ av kvot som ska ökas](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Välj **Nästa: lösningar** för att öppna **problem information**. Välj **Ange information** för att ange ytterligare information.

   ![Ange information för att hjälpa din begäran tillsammans](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. I **kvot information** väljer du **klassisk** och väljer en **plats**.

   ![Lägg till information inklusive distributions modell och plats](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. För **SKU-serien** väljer du en eller flera SKU-familjer att öka.

   ![Ange SKU-serien som ska ökas](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Ange de nya gränser som du vill ha i prenumerationen. Om du vill ta bort en rad avmarkerar du SKU: n från **SKU-serien** eller väljer ikonen Ta bort X. När du har angett en kvot för varje SKU-familj väljer du **Spara och fortsätter** med **kvot informationen** för att fortsätta med support förfrågan.

   ![Begär nya gränser](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Begäran per VM-serien vCPU kvot ökning på prenumerations nivå med användning + kvoter

Följ anvisningarna nedan om du vill skapa en supportbegäran med hjälp av **användnings** -och kvoter i Azure Portal.

1. Sök efter och välj **prenumerationer** i [Azure Portal](https://portal.azure.com).

   ![Gå till prenumerationer i Azure Portal](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Välj den prenumeration vars kvot du vill öka.

   ![Välj den prenumeration som ska ändras](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Välj **användning + kvoter**.

   ![Välj användning och kvoter för en prenumeration](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. I det övre högra hörnet väljer du **begär ökning**.

   ![Välj för att öka kvoten](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Välj **Compute-VM (kärnor-virtuella processorer) prenumerations gränsen ökar** med **kvot typen**.

   ![Välj typ av kvot](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Välj **Nästa: lösningar** för att öppna **problem information**. Välj **Ange information** för att ange ytterligare information.

   ![Ange information om din begäran](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. I **kvot information** väljer du **klassisk** och en **plats**.

   ![Välj kvot information, inklusive distributions modell och plats](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Välj en eller flera SKU-familjer för en ökning.

   ![Välj SKU-familjen för ökning](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Ange de nya gränser som du vill ha i prenumerationen. Om du vill ta bort en rad avmarkerar du SKU: n från **SKU-serien** eller väljer ikonen Ta bort X. När du har angett en kvot för varje SKU-familj väljer du **Spara och fortsätter** med **kvot informationen** för att fortsätta med support förfrågan.

   ![Ange ny kvot](./media/resource-manager-core-quotas-request/new-limits-classic.png)

