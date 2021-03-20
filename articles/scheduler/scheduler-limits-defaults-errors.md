---
title: Begränsningar, kvoter och tröskelvärden i Azure Scheduler
description: Läs mer om begränsningar, kvoter, standardvärden och begränsnings tröskelvärden för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "78898532"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Begränsningar, kvoter och begränsnings gränser i Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras [tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du konfigurerar i Scheduler, [migrera till Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) så snart som möjligt. 
>
> Scheduler är inte längre tillgänglig i Azure Portal, men [PowerShell-cmdletarna](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) och Azure Scheduler är tillgängliga just nu så att du kan hantera jobb och jobb samlingar.

## <a name="limits-quotas-and-thresholds"></a>Gränser, kvoter och tröskelvärden

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>rubrik för x-MS-Request-ID

Varje begäran som görs mot Scheduler-tjänsten returnerar ett svars huvud med namnet **x-MS-Request-ID**. Den här rubriken innehåller ett ogenomskinligt värde som unikt identifierar begäran. Om en begäran däremot Miss lyckas och du har bekräftat att begäran är korrekt formaterad, kan du rapportera felet till Microsoft genom att ange värdet för svars huvudet **x-MS-Request-ID** och inkludera dessa uppgifter: 

* Värdet **x-MS-Request-ID**
* Ungefärlig tid då begäran gjordes 
* Identifierare för Azure-prenumerationen, jobb samlingen och jobbet 
* Den typ av åtgärd som begäran försökte utföra

## <a name="next-steps"></a>Nästa steg

* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Planer och fakturering för Azure Scheduler](scheduler-plans-billing.md)
* [Referens för REST-API:et för Azure Scheduler](/rest/api/scheduler)
* [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)