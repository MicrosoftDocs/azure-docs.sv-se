---
title: Övervaka flera tids serier i en enda mått varnings regel
description: Avisering i skala med hjälp av en enda varnings regel för flera tids serier
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 01/11/2021
ms.subservice: alerts
ms.openlocfilehash: 47a2fb9240f6826ed862f7a857264c09b79de9bc
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071093"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>Övervaka flera tids serier i en enda mått varnings regel

En regel för en enda mått kan användas för att övervaka en eller flera tids serier, vilket gör det enklare att övervaka resurser i stor skala.

## <a name="metric-time-series"></a>Metric Time-serien

En Metric Time-serien är en serie mätningar (eller "mått värden") som har samlats in under en viss tids period. 

Till exempel:

- CPU-användning för en virtuell dator
- Inkommande byte (ingress) till ett lagrings konto
- Antalet misslyckade begär Anden för ett webb program



## <a name="alert-rule-on-a-single-time-series"></a>Varnings regel på en enskild tids serie
En varnings regel övervakar en enda tids serie när den uppfyller följande villkor:
-   Regeln övervakar en enskild mål resurs 
-   Innehåller ett enstaka villkor
-   Utvärderar mått utan att välja dimensioner (förutsatt att måttet har stöd för dimensioner)

Ett exempel på en sådan aviserings regel (med endast relevanta egenskaper som visas):
-   Mål resurs: *myVM1*
-   Mått: *procent andel CPU*
-   Operator: *större än*
-   Tröskel: *70*


För den här varnings regeln övervakas en enda metrisk tids serie:
-   Procent CPU där *Resource*= ' myVM1 ' > 70%

![En varnings regel på en enskild tids serie](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>Varnings regel på flera tids serier
En varnings regel övervakar flera tids serier om den använder minst en av följande funktioner: 
-   Flera resurser
-   Flera villkor 
-   Flera dimensioner


## <a name="multiple-resources-multi-resource"></a>Flera resurser (flera resurser)

En regel för en enda mått kan övervaka flera resurser, förutsatt att resurserna är av samma typ och finns i samma Azure-region. Med den här typen av regel minskar komplexiteten och det totala antalet aviserings regler som du måste underhålla. 

Ett exempel på en sådan aviserings regel:
-   Mål resurs: *myVM1, myVM2*
-   Mått: *procent andel CPU*
-   Operator: *större än*
-   Tröskel: *70*

För den här varnings regeln övervakas två metriska tids serier separat:
-   Procent CPU där *Resource*= ' myVM1 ' > 70%
-   Procent CPU där *Resource*= ' myVM2 ' > 70%

![En varnings regel för flera resurser](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
I en varnings regel för flera resurser utvärderas villkoret **separat** för var och en av resurserna (eller mer korrekt, för var och en av måttets tids serier motsvarar varje resurs). Det innebär att aviseringar också utlöses för varje resurs separat.

Anta till exempel att vi har ställt in varnings regeln ovan för att övervaka processor över 70%. Under den utvärderade tids perioden (det vill säga de senaste 5 minuterna)
-   *Procent andelen* av *myVM1* är större än 70% 
-   *Procent andelen* av *myVM2* är på 50%

Varnings regeln utlöses på *myVM1*, men inte *myVM2*. Dessa utlösta aviseringar är oberoende av varandra. De kan också matcha vid olika tidpunkter, beroende på de enskilda virtuella datorernas beteende.

Mer information om aviserings regler för flera resurser och de resurs typer som stöds för den här funktionen finns i [övervakning i skala med hjälp av mått varningar i Azure Monitor](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

> [!NOTE] 
> I en regel för mått varningar som övervakar flera resurser, tillåts bara ett enda villkor.

## <a name="multiple-conditions-multi-condition"></a>Flera villkor (flera villkor)

En regel för en enda mått kan också övervaka upp till fem villkor per aviserings regel. 

Till exempel:

- Mål resurs: *myVM1*
- Condition1
  - Mått: *procent andel CPU*
  - Operator: *större än*
  - Tröskel: *70*
- Condition2
  - Mått: *totalt nätverk*
  - Operator: *större än*
  - Tröskel: *20 MB*

För den här varnings regeln övervakas två metriska tids serier:

- Procent CPU där *Resource*= ' myVM1 ' > 70%
- Nätverk totalt där *Resource*= ' myVM1 ' > 20 MB

![En varnings regel för flera villkor](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
Operatorn "och" används mellan villkoren. Varnings regeln utlöses när **alla** villkor är uppfyllda. Den utlöst aviseringen löses om minst ett av villkoren inte längre uppfylls. 

> [!NOTE]
> Det finns begränsningar när du använder dimensioner i en varnings regel med flera villkor. Mer information finns i [begränsningar när du använder dimensioner i en regel för mått varningar med flera villkor](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions).


## <a name="multiple-dimensions-multi-dimension"></a>Flera dimensioner (flera dimensioner)

En regel för en enda mått kan också övervaka flera dimensions värden för ett mått. Måtten för ett mått är namn/värde-par som innehåller ytterligare data för att beskriva måttets värde. Till exempel har **transaktions** måttet för ett lagrings konto en dimension med namnet **API-namn** som beskriver namnet på API: et som anropas av varje transaktion (till exempel GetBlob, DeleteBlob, PutPage). Användningen av dimensioner är valfri, men det gör det möjligt att filtrera måttet och bara övervaka vissa tids serier, i stället för att övervaka måttet som en mängd av alla dimensions värden som samlas in. 

Du kan till exempel välja att en avisering ska aktive ras när antalet transaktioner är högt över alla API-namn (vilket är de sammanställda data) eller att ytterligare bryta ned den i aviseringen när antalet transaktioner är högt för vissa API-namn. 

Ett exempel på en varnings regel som övervakar flera dimensioner är:

- Mål resurs: *myStorage1*
- Mått: *transaktioner*
- Dimensioner
  * API-namn = *GetBlob, DeleteBlob, PutPage*
- Operator: *större än*
- Tröskel: *70*

För den här varnings regeln övervakas tre metriska tids serier:

- Transaktioner där *Resource*= ' myStorage1 ' och *API Name*= ' GetBlob ' > 70
- Transaktioner där *Resource*= ' myStorage1 ' och *API Name*= ' DeleteBlob ' > 70
- Transaktioner där *Resource*= ' myStorage1 ' och *API Name*= ' PutPage ' > 70

![En varnings regel med flera dimensioner med värden från en dimension](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

En varnings regel för flera dimensioner kan också övervaka flera dimensions värden från **olika** mått för ett mått. I det här fallet övervakar varnings regeln **separat** alla dimensions värde kombinationer för de valda dimensions värdena.

Ett exempel på den här typen av aviserings regel:

- Mål resurs: *myStorage1*
- Mått: *transaktioner*
- Dimensioner
  * API-namn = *GetBlob, DeleteBlob, PutPage*
  * Autentisering = *SAS, AccountKey*
- Operator: *större än*
- Tröskel: *70*

För den här varnings regeln övervakas sex meters tids serier separat:

- Transaktioner där *Resource*= ' myStorage1 ' och *API Name*= ' GetBlob ' och *Authentication*= ' SAS ' > 70
- Transaktioner där *Resource*= ' myStorage1 ' och *API Name*= ' GetBlob ' och *Authentication*= ' AccountKey ' > 70
- Transaktioner där *Resource*= ' myStorage1 ' och *API Name*= ' DeleteBlob ' och *Authentication*= ' SAS ' > 70
- Transaktioner där *Resource*= ' myStorage1 ' och *API Name*= ' DeleteBlob ' och *Authentication*= ' AccountKey ' > 70
- Transaktioner där *Resource*= ' myStorage1 ' och *API Name*= ' PutPage ' och *Authentication*= ' SAS ' > 70
- Transaktioner där *Resource*= ' myStorage1 ' och *API Name*= ' PutPage ' och *Authentication*= ' AccountKey ' > 70

![En varnings regel med flera dimensioner och värden från flera dimensioner](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>Avancerade funktioner i flera dimensioner

1.  **Välja alla aktuella och framtida dimensioner** – du kan välja att övervaka alla möjliga värden för en dimension, inklusive framtida värden. En sådan varnings regel skalar automatiskt för att övervaka alla värden i dimensionen utan att du behöver ändra varnings regeln varje gång ett dimensions värde läggs till eller tas bort.
2.  **Förutom dimensioner** – att välja operatorn ' ≠ ' (exkludera) för ett dimensions värde motsvarar att välja alla andra värden för dimensionen, inklusive framtida värden.
3.  **Nya och anpassade dimensioner** – dimensions värden som visas i Azure Portal baseras på mått data som samlas in den senaste dagen. Om det dimensions värde som du söker efter inte har spridits, kan du lägga till ett anpassat dimensions värde.
4. **Matcha dimensioner med ett prefix** – du kan välja att övervaka alla dimensions värden som börjar med ett särskilt mönster genom att välja operatorn "börjar med" och ange ett anpassat prefix.

![Avancerade funktioner i flera dimensioner](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>Prissättning för mått varningar

Prissättningen av mått för mått finns på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

När du skapar en regel för mått varningar baseras den angivna pris beräkningen på de valda funktionerna och antalet övervakade tids serier, som bestäms av regel konfigurationen och aktuella mått värden. Månads kostnaden baseras dock på faktiska utvärderingar av tids serien och kan därför skilja sig från den ursprungliga uppskattningen om vissa tids serier inte har några data att utvärdera, eller om aviserings regeln använder funktioner som kan göra att de kan skalas dynamiskt.

En varnings regel kan till exempel Visa en hög pris uppskattning om den använder funktionen flera dimensioner, och ett stort antal kombinationer av dimensions värden väljs, vilket resulterar i övervakningen av många tids serier. Men den faktiska avgiften för den aviserings regeln kan vara lägre om inte alla tids serier som uppstår i kombination med dimensions värden verkligen har data att utvärdera.

## <a name="number-of-time-series-monitored-by-a-single-alert-rule"></a>Antal tids serier som övervakas av en enda varnings regel

För att förhindra överskotts kostnader kan varje aviserings regel övervaka upp till 5000 Time-serien som standard. Öppna ett support ärende om du vill lyfta upp den här gränsen från din prenumeration.


## <a name="next-steps"></a>Nästa steg

Lär dig mer om att övervaka i skala med hjälp av mått varningar och [dynamiska tröskelvärden](alerts-dynamic-thresholds.md).
