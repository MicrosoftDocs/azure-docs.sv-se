---
title: Planer och fakturering
description: Läs mer om planer och fakturering för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "78898478"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Planer och fakturering för Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras [tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du konfigurerar i Scheduler, [migrera till Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) så snart som möjligt. 
>
> Scheduler är inte längre tillgänglig i Azure Portal, men [PowerShell-cmdletarna](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) och Azure Scheduler är tillgängliga just nu så att du kan hantera jobb och jobb samlingar.

## <a name="job-collection-plans"></a>Jobb samlings planer

I Azure Scheduler innehåller en jobb samling ett särskilt antal jobb. Jobb samlingen är den fakturerbara entiteten och ingår i standard-, P10 Premium-och P20 Premium-planerna, som beskrivs här: 

| Jobb samlings plan | Maximalt antal jobb per samling | Max upprepning | Högsta antal jobb samlingar per prenumeration | Gränser | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 jobb per samling | En per minut. Det går inte att köra jobb oftare än ett per minut. | Varje Azure-prenumeration kan ha upp till 100 standard jobb samlingar. | Åtkomst till fullständig funktions uppsättning i Scheduler | 
| **P10 Premium** | 50 jobb per samling | En per minut. Det går inte att köra jobb oftare än ett per minut. | Varje Azure-prenumeration kan ha upp till 10 000 P10 Premium-jobb samlingar. <a href="mailto:wapteams@microsoft.com">Kontakta oss</a>om du vill ha fler samlingar. | Åtkomst till fullständig funktions uppsättning i Scheduler |
| **P20 Premium** | 1000 jobb per samling | En per minut. Det går inte att köra jobb oftare än ett per minut. | Varje Azure-prenumeration kan ha upp till 5 000 P20 Premium-jobb samlingar. <a href="mailto:wapteams@microsoft.com">Kontakta oss</a>om du vill ha fler samlingar. | Åtkomst till fullständig funktions uppsättning i Scheduler |
|||||| 

## <a name="pricing"></a>Prissättning

Pris information finns i [priser för schemaläggare](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Uppgradera eller nedgradera planer

Du kan när som helst uppgradera eller nedgradera en jobb samlings plan över standard-, P10 Premium-och P20 Premium-planerna.

## <a name="active-status-and-billing"></a>Aktiv status och fakturering

Jobb samlingar är alltid aktiva om hela Azure-prenumerationen hamnar i ett tillfälligt inaktiverat tillstånd på grund av fakturerings problem. Även om du kan inaktivera alla jobb i en jobb samling via en enda åtgärd, ändrar inte den här åtgärden jobb samlingens fakturerings status, så jobb samlingen faktureras *fortfarande* . Tomma jobb samlingar betraktas som aktiva och faktureras.

För att se till att en jobb samling inte faktureras måste du ta bort jobb samlingen.

## <a name="standard-billable-units"></a>Standard fakturerbara enheter

En standardinställd fakturerings enhet kan ha upp till 10 standard jobb samlingar. Eftersom en standard jobb samling kan ha upp till 50 jobb per samling, kan en standard fakturerings enhet låta din Azure-prenumeration ha upp till 500 jobb eller upp till nästan 22 *miljoner* jobb körningar per månad. I den här listan förklaras hur du faktureras baserat på olika antal standard jobb samlingar:

* Om du har mellan 1 och 10 standard jobb samlingar debiteras du för en standard fakturerings enhet. 

* Om du har mellan 11 och 20 standard jobb samlingar debiteras du för två standard fakturerings enheter. 

* Om du har mellan 21 och 30 standard jobb samlingar debiteras du för tre standard fakturerings enheter och så vidare.

## <a name="p10-premium-billable-units"></a>P10 Premium fakturerbara enheter

En P10 Premium-enhet kan ha upp till 10 000 P10 Premium-jobb samlingar. Eftersom en P10 Premium-jobb samling kan ha upp till 50 jobb per samling, kan en P10 Premium-enhet för Azure-prenumerationen ha upp till 500 000 jobb eller upp till nästan 22 *miljarder* jobb körningar per månad. 

P10 Premium-jobb samlingar ger samma funktioner som standard jobb samlingar men erbjuder en pris rast för appar som kräver många jobb samlingar och ger större skalbarhet. I den här listan förklaras hur du faktureras baserat på olika antal P10 Premium-jobb samlingar:

* Om du har mellan 1 och 10 000 P10 Premium Job Collections debiteras du för en P10 Premium-fakturerings enhet. 

* Om du har mellan 10 001 och 20 000 P10 Premium Job Collections debiteras du för 2 P10 Premium-fakturerings enheter och så vidare.

## <a name="p20-premium-billable-units"></a>P20 Premium fakturerbara enheter

En P20 Premium-enhet kan ha upp till 5 000 P20 Premium-jobb samlingar. Eftersom en P20 Premium-jobb samling kan ha upp till 1 000 jobb per jobb samling, kan en P20 Premium-enhet för Azure-prenumerationen ha upp till 5 000 000 jobb eller upp till nästan 220 *miljarder* jobb körningar per månad.

P20 Premium-jobb samlingar ger samma funktioner som P10 Premium-jobb samlingar, men har även stöd för ett större antal jobb per samling och ett större antal jobb generellt än P10 Premium, vilket ger större skalbarhet.

## <a name="plan-comparison"></a>Planera jämförelse

* Om du har fler än 100 standard jobb samlingar (10 standard fakturerings enheter) kan du få ett bättre avtal genom att ha alla jobb samlingar i en Premium plan.

* Om du har en standard jobb samling och en Premium jobb samling debiteras du för en standard fakturerings enhet *och* en Premium fakturerings enhet.

  Scheduler service faktureras baserat på antalet aktiva jobb samlingar som antingen är standard eller Premium.

## <a name="next-steps"></a>Nästa steg

* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)