---
title: Smart identifiering i Azure Application Insights | Microsoft Docs
description: Application Insights utför automatisk djup analys av din app-telemetri och varnar dig om potentiella problem.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 974795e853416ff7a4c051530b050fc663c9423d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585655"
---
# <a name="smart-detection-in-application-insights"></a>Smart identifiering i Application Insights
 Smart identifiering varnar automatiskt om potentiella prestanda problem och fel avvikelser i ditt webb program. Den utför proaktiv analys av Telemetrin som din app skickar till [Application Insights](./app-insights-overview.md). Om det uppstår plötsliga ökningar i felfrekvenser eller onormala mönster i klient-eller server prestanda får du en avisering. Den här funktionen behöver ingen konfiguration. Det fungerar om ditt program skickar tillräckligt med telemetri.

Du kan komma åt identifieringar som utfärdats av Smart identifiering både från e-postmeddelanden som du får och från bladet Smart igenkänning.

## <a name="review-your-smart-detections"></a>Granska dina smarta identifieringar
Du kan identifiera identifieringar på två sätt:

* **Du får ett e-postmeddelande** från Application Insights. Här är ett vanligt exempel:
  
    ![E-postavisering](./media/proactive-diagnostics/03.png)
  
    Klicka på knappen Big för att öppna mer information i portalen.
* **Bladet Smart igenkänning** i Application Insights. Välj **Smart identifiering** under menyn **Undersök** om du vill se en lista över de senaste identifieringarna.

![Visa senaste identifieringar](./media/proactive-diagnostics/04.png)

Välj en identifiering om du vill se information om den.

## <a name="what-problems-are-detected"></a>Vilka problem har upptäckts?
Smart identifiering identifierar och meddelar om en rad olika problem, till exempel:

* [Smart identifiering – fel avvikelser](./proactive-failure-diagnostics.md). Vi använder Machine Learning för att ange den förväntade antalet misslyckade förfrågningar för din app, som korrelerar med belastning och andra faktorer. Om felen hamnar utanför det förväntade kuvertet skickar vi en avisering.
* [Smart identifiering-prestanda avvikelser](./proactive-performance-diagnostics.md). Du får meddelanden om svars tiden för en åtgärd eller beroende varaktighet är långsammare jämfört med historisk bas linje eller om vi identifierar ett avvikande mönster i svars tid eller sid inläsnings tid.   
* Allmän försämring och problem, som [spårnings försämring](./proactive-trace-severity.md), [minnes läcka](./proactive-potential-memory-leak.md), [onormal ökning av undantags volym](./proactive-exception-volume.md) och [säkerhets mönster för säkerhet](./proactive-application-security-detection-pack.md).

(Hjälp länkar i varje meddelande tar dig till relevanta artiklar.)

## <a name="smart-detection-email-notifications"></a>E-postmeddelanden för smart identifiering

Alla regler för smart identifiering, förutom regler som är markerade som för _hands version_, konfigureras som standard för att skicka e-postaviseringar när identifieringar hittas.

Du kan konfigurera e-postaviseringar för en speciell Smart identifierings regel genom att öppna bladet **Inställningar** för smart identifiering och välja regeln, vilket öppnar bladet **Redigera regel** .

Alternativt kan du ändra konfigurationen med Azure Resource Manager mallar. Mer information [finns i hantera Application Insights regler för smart identifiering med Azure Resource Manager mallar](./proactive-arm-config.md) .

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
De här diagnostikverktygen hjälper dig att inspektera Telemetrin från din app:

* [Metric Explorer](../essentials/metrics-charts.md)
* [Sök Utforskaren](./diagnostic-search.md)
* [Analys-kraftfullt frågespråk](../logs/log-analytics-tutorial.md)

Smart identifiering är helt automatisk. Men kanske vill du konfigurera ytterligare aviseringar?

* [Manuellt konfigurerade mått varningar](../alerts/alerts-log.md)
* [Webbtester för tillgänglighet](./monitor-web-app-availability.md)