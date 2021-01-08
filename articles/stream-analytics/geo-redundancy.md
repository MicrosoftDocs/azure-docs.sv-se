---
title: Uppnå GEO-redundans för Azure Stream Analytics jobb
description: Den här artikeln beskriver hur du uppnår GEO-redundans för Azure Stream Analytics jobb i stället för GEO-redundans.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 86a90cbcbb3381e853dcea766364642a89dafd7b
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015529"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Uppnå GEO-redundans för Azure Stream Analytics jobb

Azure Stream Analytics tillhandahåller inte automatisk geo-redundans, men du kan åstadkomma GEO-redundans genom att distribuera identiska Stream Analytics jobb i flera Azure-regioner. Varje jobb ansluts till lokala data källor och lokala utdata. Det är programmets ansvar att både skicka indata till två regionala indata och stämma av mellan de två regionala utmatningarna. Stream Analyticss jobb är två separata entiteter.

Följande diagram illustrerar ett exempel på en Geo-redundant Stream Analytics jobb distribution med Event Hub-indata och Azure Database-utdata.

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="diagram över geo-redundanta Stream Analytics-jobb":::

## <a name="primarysecondary-strategy"></a>Primär/sekundär strategi

Ditt program måste hantera vilken region som den utgående databasen betraktas som primär och som betraktas som sekundär. På grund av ett primärt regions haveri växlar programmet till den sekundära databasen och börjar läsa uppdateringar från databasen. Den faktiska mekanismen som gör det möjligt att minimera dubblerade läsningar beror på ditt program.Du kan förenkla den här processen genom att skriva ytterligare information till utdata. Du kan till exempel lägga till en tidstämpel eller ett sekvens-ID för varje utdata för att hoppa över dubblettrader i en trivial operation. När den primära regionen återställs, fångas den upp med den sekundära databasen med liknande Mechanics.

Även om olika indata-och utdatatyper tillåter olika alternativ för geo-replikering, rekommenderar vi att du använder det mönster som beskrivs i den här artikeln för att uppnå GEO-redundans eftersom det ger flexibilitet och kontroll över både event-och händelse konsumenter.

## <a name="next-steps"></a>Nästa steg

* [Övervaka och hantera Azure Stream Analytics jobb med PowerShell](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Data driven fel sökning i Azure Stream Analytics](stream-analytics-job-diagram-with-metrics.md)