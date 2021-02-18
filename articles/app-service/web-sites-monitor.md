---
title: Övervakning av appar
description: Lär dig hur du övervakar appar i Azure App Service med hjälp av Azure Portal. Förstå de kvoter och mått som rapporteras.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 04/23/2020
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: bf230032afe80680dc392c2a74da2a5aef381983
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586099"
---
# <a name="monitor-apps-in-azure-app-service"></a>Övervaka appar i Azure App Service
[Azure App Service](./overview.md) innehåller inbyggda övervaknings funktioner för webbappar, mobil-och API-appar i [Azure Portal](https://portal.azure.com).

I Azure Portal kan du granska *kvoter* och *mått* för en app och App Service plan och ställa in *varningar* och regler för automatisk *skalning* som baseras på mått.

## <a name="understand-quotas"></a>Förstå kvoter

Appar som finns i App Service omfattas av vissa begränsningar för de resurser som de kan använda. Gränserna definieras av den App Service plan som är associerad med appen.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Om appen finns i en *kostnads fri* eller *delad* plan definieras gränserna för de resurser som appen kan använda definieras av kvoter.

Om appen finns i en *Basic*-, *standard*-eller *Premium* -plan anges gränserna för de resurser som de kan använda av *storlek* (liten, medel, stor) och *instans antal* (1, 2, 3 och så vidare) för App Service plan.

Kvoter för kostnads fria eller delade appar är:

| Kvot | Description |
| --- | --- |
| **PROCESSOR (kort)** | Mängden CPU som tillåts för den här appen i ett intervall om 5 minuter. Den här kvoten återställs var femte minut. |
| **PROCESSOR (dag)** | Den totala mängden processor som tillåts för den här appen under en dag. Den här kvoten återställs var 24: e timme vid midnatt UTC. |
| **Minne** | Den totala mängden minne som tillåts för den här appen. |
| **Bandbredd** | Den totala mängden utgående bandbredd som tillåts för den här appen under en dag. Den här kvoten återställs var 24: e timme vid midnatt UTC. |
| **Filsystem** | Den totala mängden lagrings utrymme som tillåts. |

Den enda kvot som gäller för appar som finns i fil systemet *Basic*, *standard* och *Premium* är filesystem.

Mer information om vilka kvoter, gränser och funktioner som är tillgängliga för de olika App Service SKU: er finns i [begränsningar för Azure-prenumerations tjänsten](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Kvot tillämpning

Om en app överskrider *processorns (kort)*, *CPU (dag)* eller *bandbredds* kvot stoppas appen tills kvoten återställs. Under den här tiden resulterar alla inkommande förfrågningar i ett HTTP 403-fel.

![403-fel meddelande][http403]

Om minnes kvoten för appen har överskridits stoppas appen tillfälligt.

Om kvoten för fil systemet överskrids Miss lyckas eventuella Skriv åtgärder. Skriv åtgärds felen innehåller alla skrivningar till loggarna.

Du kan öka eller ta bort kvoter från din app genom att uppgradera din App Service plan.

## <a name="understand-metrics"></a>Förstå mått

> [!NOTE]
> **Användning av fil system** är ett nytt mått som distribueras globalt, inga data förväntas om inte din app finns i en app service-miljön.
> 

> [!IMPORTANT]
> Den **genomsnittliga svars tiden** blir föråldrad för att undvika förvirring med mått agg regeringar. Använd **svars tid** som ersättning.

> [!NOTE]
> Mått för en app inkluderar begär anden till appens SCM-plats (kudu).  Detta inkluderar begär Anden för att Visa webbplatsens logstream med hjälp av kudu.  Logstream-begäranden kan omfatta flera minuter, vilket kommer att påverka tids måtten för begäran.  Användarna bör vara medvetna om den här relationen när de använder dessa mått med autoskalning av logik.
> 

Mått ger information om appen eller App Service plan beteendet.

För en app är tillgängliga mått:

| Metric | Beskrivning |
| --- | --- |
| **Svars tid** | Den tid det tar för appen att betjäna begär Anden, i sekunder. |
| **Genomsnittlig svars tid (inaktuell)** | Genomsnittlig tid det tar för appen att betjäna begär Anden, i sekunder. |
| **Genomsnittlig arbets mängd för minne** | Den genomsnittliga mängden minne som används av appen, i megabyte (MiB). |
| **Anslutningar** | Antalet kopplade socketar som är befintliga i sandbox (w3wp.exe och dess underordnade processer).  En bunden socket skapas genom att anropa BIND ()/Connect ()-API: er och förblir kvar tills socketen stängs med CloseHandle ()/Closesocket (). |
| **CPU-tid** | Mängden CPU som används av appen, i sekunder. Mer information om det här måttet finns i [CPU-tid jämfört med processor procent](#cpu-time-vs-cpu-percentage). |
| **Aktuella sammansättningar** | Det aktuella antalet sammansättningar som har lästs in i alla AppDomains i det här programmet. |
| **Data i** | Mängden inkommande bandbredd som används av appen, i MiB. |
| **Data ut** | Mängden utgående bandbredd som används av appen, i MiB. |
| **Fil system användning** | Mängden användning i byte per lagrings resurs. |
| **Skräp insamling för gen 0** | Antalet gånger som generation 0-objekt är skräp insamlat sedan program processen startades. Högre generations GC generationer omfattar all lägre generations-GC generationer.|
| **Skräp insamling för gen 1** | Antalet gånger som generation 1-objekten är skräp insamlat sedan program processen startades. Högre generations GC generationer omfattar all lägre generations-GC generationer.|
| **Gen 2 skräp insamling** | Antalet gånger som generation 2-objekt är skräp insamlat sedan program processen startades.|
| **Antal referenser** | Det totala antalet handles som för närvarande är öppna av app-processen.|
| **Hälso kontroll status** | Genomsnittlig hälso status för program instanser i App Service planen.|
| **Http-2xx** | Antalet förfrågningar som resulterar i en HTTP-statuskod ≥ 200 men < 300. |
| **Http-3xx** | Antalet förfrågningar som resulterar i en HTTP-statuskod ≥ 300 men < 400. |
| **Http 401** | Antal begär Anden som resulterar i status kod för HTTP 401. |
| **Http 403** | Antal begär Anden som resulterar i status kod för HTTP 403. |
| **Http 404** | Antal begär Anden som resulterar i status kod för HTTP 404. |
| **Http 406** | Antal begär Anden som resulterar i status kod för HTTP 406. |
| **Http-4xx** | Antalet förfrågningar som resulterar i en HTTP-statuskod ≥ 400 men < 500. |
| **Http-serverfel** | Antalet förfrågningar som resulterar i en HTTP-statuskod ≥ 500 men < 600. |
| **I/o andra byte per sekund** | Den hastighet med vilken app-processen utfärdar byte till I/O-åtgärder som inte omfattar data, till exempel kontroll åtgärder.|
| **Andra i/o-åtgärder per sekund** | Den hastighet med vilken app-processen utfärdar I/O-åtgärder som inte är Läs-eller Skriv åtgärder.|
| **IO-lästa byte per sekund** | Den hastighet med vilken appens process läser in byte från I/O-åtgärder.|
| **IO-Läs åtgärder per sekund** | Den hastighet med vilken app-processen utfärdar Läs-I/O-åtgärder.|
| **Skrivna byte i i/o per sekund** | Den hastighet med vilken appens process skriver byte till I/O-åtgärder.|
| **I/o-Skriv åtgärder per sekund** | Den hastighet med vilken program processen utfärdar Skriv-I/O-åtgärder.|
| **Minnes arbets mängd** | Den aktuella mängden minne som används av appen, i MiB. |
| **Privata byte** | Privata byte är den aktuella storleken i byte på minne som program processen har allokerat och som inte kan delas med andra processer.|
| **Begäranden** | Det totala antalet begär Anden oavsett den resulterande HTTP-statuskoden. |
| **Begär anden i program kön** | Antalet begär anden i program begär ande kön.|
| **Antal trådar** | Antalet trådar som för närvarande är aktiva i program processen.|
| **Totalt antal app-domäner** | Det aktuella antalet AppDomains som har lästs in i det här programmet.|
| **Totalt antal app-domäner som har inaktiverats** | Det totala antalet inaktiverade tillämpnings domäner sedan programmet startades.|


För en App Service plan är tillgängliga mått:

> [!NOTE]
> App Service plan mått är bara tillgängliga för planer på nivåerna *Basic*, *standard* och *Premium* .
> 

| Metric | Beskrivning |
| --- | --- |
| **CPU-procent** | Den genomsnittliga CPU som används för alla instanser av planen. |
| **Minnes procent** | Genomsnittligt minne som används för alla instanser av planen. |
| **Data i** | Den genomsnittliga inkommande bandbredd som används för alla instanser av planen. |
| **Data ut** | Den genomsnittliga utgående bandbredd som används för alla instanser av planen. |
| **Diskkölängd** | Genomsnittligt antal begär Anden om läsning och skrivning som ställts i kö för lagring. En hög diskkölängd är en indikation på en app som kan vara långsam på grund av överdriven disk-I/O. |
| **Längd på http-kö** | Genomsnittligt antal HTTP-begäranden som måste sitta i kön innan de kunde uppfyllas. En hög eller ökande HTTP-Kölängd är ett symtom på en plan som är tungt belastad. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU-tid jämfört med processor procent
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Det finns två mått som återspeglar CPU-användning:

**CPU-tid**: användbart för appar som finns i kostnads fria eller delade planer, eftersom en av deras kvoter definieras i CPU-minuter som används av appen.

**Processor procent**: användbart för appar som finns i Basic-, standard-och Premium-planer, eftersom de kan skalas ut. CPU-procent är en korrekt indikation på den övergripande användningen för alla instanser.

## <a name="metrics-granularity-and-retention-policy"></a>Mät precisions precision och bevarande princip
Mått för en app-och App Service-plan loggas och sammanställs av tjänsten och [bevaras enligt dessa regler](../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics).

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Övervaknings kvoter och mät värden i Azure Portal
Om du vill granska statusen för de olika kvoter och mått som påverkar en app går du till [Azure Portal](https://portal.azure.com).

![Kvot diagram i Azure Portal][quotas]

Välj **inställnings** kvoter för att hitta kvoter  >  . I diagrammet kan du granska: 
1. Kvotens namn.
1. Dess återställnings intervall.
1. Den aktuella gränsen.
1. Det aktuella värdet.

![Mått diagram i Azure Portal ][metrics] du kan komma åt mått direkt från resurs **översikts** sidan. Här ser du diagram som visar några av måtten för appar.

Om du klickar på någon av dessa diagram tas du till vyn mått där du kan skapa anpassade diagram, fråga olika mått och mycket mer. 

Mer information om mått finns i [övervaka tjänst mått](../azure-monitor/data-platform.md).

## <a name="alerts-and-autoscale"></a>Aviseringar och autoskalning
Mått för en app eller en App Service plan kan anslutas till aviseringar. Mer information om varningsaviseringar finns [här](../azure-monitor/alerts/alerts-classic-portal.md).

App Service appar som finns i Basic eller högre App Service planer stöder autoskalning. Med autoskalning kan du konfigurera regler som övervakar App Service plans måtten. Regler kan öka eller minska antalet instanser, vilket kan ge ytterligare resurser efter behov. Regler kan också hjälpa dig att spara pengar när appen är överetablerad.

Mer information om autoskalning finns i [skala](../azure-monitor/autoscale/autoscale-get-started.md) och [metod tips för Azure Monitor autoskalning](../azure-monitor/autoscale/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
