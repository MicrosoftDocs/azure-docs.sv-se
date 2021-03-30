---
title: Vad är Azure Scheduler?
description: Skapa schema och kör automatiserade jobb som anropar tjänster i eller utanför Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 66ec285554299214122a4093837d3506bf642b13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368136"
---
# <a name="what-is-azure-scheduler"></a>Vad är Azure Scheduler?

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras [tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du konfigurerar i Scheduler, [migrera till Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) så snart som möjligt. 
>
> Scheduler är inte längre tillgänglig i Azure Portal, men [PowerShell-cmdletarna](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) och Azure Scheduler är tillgängliga just nu så att du kan hantera jobb och jobb samlingar.

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) hjälper dig att skapa [jobb](../scheduler/scheduler-concepts-terms.md) som körs i molnet genom att deklarativt beskriva åtgärder. Tjänsten schemalägger och kör sedan dessa åtgärder automatiskt. Du kan till exempel anropa tjänster inuti och utanför Azure, till exempel anropa HTTP- eller HTTPS-slutpunkter, och också publicera meddelanden till Azure Storage-köer och Azure Service Bus-köer eller ämnen. Du kan köra jobb direkt eller vid ett senare tillfälle. Scheduler kan enkelt hantera [komplexa scheman och avancerade upprepningar](../scheduler/scheduler-advanced-complexity.md). Scheduler anger när jobb ska köras och sparar en historik över jobbresultaten. Du kan sedan granska dessa och förutsägbart och säkert schemalägga arbetsbelastningar som ska köras.

Andra schemaläggningsfunktioner i Azure använder också Scheduler i bakgrunden, till exempel [Azure WebJobs](../app-service/webjobs-create.md), som är en [Web Apps](https://azure.microsoft.com/services/app-service/web/)-funktion i Azure App Service. Du kan hantera kommunikationen för dessa åtgärder med hjälp av [scheduler REST API](/rest/api/scheduler/), som hjälper dig att hantera kommunikationen för dessa åtgärder.

Det här är några scenarier där Scheduler kan hjälpa dig att göra följande:

* Köra regelbundna appåtgärder: till exempel regelbunden insamling av data från Twitter till ett flöde.

* Utföra dagligt underhåll: som att rensa loggar dagligen, utföra säkerhetskopieringar och andra underhållsåtgärder.

  Som administratör kanske du vill säkerhetskopiera din databas kl 01.00 varje dag under de kommande nio månaderna.

Du kan använda Scheduler för att skapa, underhålla och köra schemalagda arbetsbelastningar, men Scheduler är inte värd för arbetsbelastningarna och kör heller inte kod. Tjänsten *anropar* endast tjänster eller kod som finns på en annan plats, till exempel i Azure, lokalt eller hos en annan provider. Scheduler kan anropa via HTTP, HTTPS, en Storage-kö, en Service Bus-kö eller ett Service Bus-ämne.

Du kan skapa, schemalägga, hantera, uppdatera eller ta bort jobb och [jobb samlingar](../scheduler/scheduler-concepts-terms.md)genom att använda kod, [Scheduler REST API](/rest/api/scheduler/)eller [Azure schedulers PowerShell-cmdletar](scheduler-powershell-reference.md).

## <a name="next-steps"></a>Nästa steg

* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Planer och fakturering för Azure Scheduler](scheduler-plans-billing.md)
* [Bygg komplexa scheman och avancerad upprepning med Azure Scheduler](scheduler-advanced-complexity.md)
* [Referens för REST-API:et för Azure Scheduler](/rest/api/scheduler)
* [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)