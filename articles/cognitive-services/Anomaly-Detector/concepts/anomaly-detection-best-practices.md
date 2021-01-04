---
title: Metodtips när du använder API:et för avvikelseidentifiering
titleSuffix: Azure Cognitive Services
description: 'Lär dig mer om metod tips när du identifierar avvikelser med API: t för avvikelse identifiering.'
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: mbullwin
ms.openlocfilehash: 9457c610b256dd4602ef0dc51a47eeffb3c63b49
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705157"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Metod tips för att använda API: t för avvikelse detektor

API: t för avvikelse detektor är en tillstånds lös tjänst för avvikelse identifiering. Noggrannheten och prestandan för dess resultat kan påverkas av:

* Hur tids serie data förbereds.
* API-parametrarna för avvikelse detektor som användes.
* Antalet data punkter i din API-begäran. 

Använd den här artikeln om du vill veta mer om metod tips för att använda API: et för att få bästa möjliga resultat för dina data. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>När du ska använda batch (hela) eller den senaste (sista) punkten avvikelse identifiering

Med hjälp av API: et för avvikelse detektorns API kan du identifiera avvikelser via hela tids serie data. I det här identifierings läget skapas och används en enda statistisk modell för varje punkt i data uppsättningen. Om tids serien har nedanstående egenskaper rekommenderar vi att du använder batch-identifiering för att förhandsgranska dina data i ett API-anrop.

* En säsongs tids serie med tillfällig avvikelse.
* En plan med fasta trender, med tillfälliga toppar/DIP. 

Vi rekommenderar inte att du använder avvikelse identifiering i real tid för data övervakning i real tid eller använder det på tids serie data som inte har mer än egenskaper. 

* Med batch-identifiering skapas och används bara en modell, identifieringen för varje punkt görs i hela serien. Om tids serie data trender uppåt och nedåt utan säsongs beroende kan vissa ändrings punkter (DIP och toppar i data) missas av modellen. På samma sätt kan vissa ändringar som är mindre viktiga än de senare i data uppsättningen inte räknas som tillräckligt stora för att införlivas i modellen.

* Batch-identifiering är långsammare än att identifiera avvikelse statusen för den senaste punkten vid data övervakning i real tid, på grund av antalet poäng som analyseras.

För data övervakning i real tid rekommenderar vi att du bara identifierar avvikelse statusen för den senaste data punkten. Genom att kontinuerligt använda den senaste identifieringen av punkter kan data övervakning för strömning göras effektivare och korrekt.

I exemplet nedan beskrivs de konsekvenser som de här identifierings lägena kan ha på prestanda. Den första bilden visar resultatet av att kontinuerligt identifiera avvikelse status den senaste punkten utmed 28 tidigare visade data punkter. De röda punkterna är avvikelser.

![En bild som visar avvikelse identifiering med den senaste punkten](../media/last.png)

Nedan finns samma data uppsättning med hjälp av batch-avvikelse identifiering. Modellen som skapats för åtgärden har ignorerat flera avvikelser, markerade med rektanglar.

![En bild som visar avvikelse identifiering med hjälp av batch-metoden](../media/entire.png)

## <a name="data-preparation"></a>Förberedelse av data

API: t för avvikelse detektor accepterar Time Series-data som är formaterade i ett JSON-Request-objekt. En tids serie kan vara alla numeriska data som registreras över tid i nummerordning. Du kan skicka Windows av dina Time Series-data till API-slutpunkten för avvikelse detektorn för att förbättra prestandan för API: et. Det minsta antalet data punkter som du kan skicka är 12 och det maximala värdet är 8640 punkter. [Granularitet](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) definieras som den hastighet som data samplas på. 

Data punkter som skickas till API: t för avvikelse detektor måste ha en giltig tidsstämpel för UTC-tid (Coordinated Universal Time) och numeriskt värde. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Om dina data samplas med ett tidsintervall som inte är standard kan du ange det genom att lägga till `customInterval` attributet i din begäran. Om din serie exempelvis samplas var 5: e minut kan du lägga till följande i din JSON-begäran:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Data punkter saknas

Data punkter som saknas är vanliga i data uppsättningar med jämnt distribuerade tids serier, särskilt med en fin kornig het (ett litet samplings intervall. Exempel på data som samplas med några minuter). Färre än 10% av det förväntade antalet punkter i dina data får inte ha någon negativ inverkan på dina identifierings resultat. Överväg att fylla luckor i dina data baserat på egenskaperna som att ersätta data punkter från en tidigare period, linjär interpolation eller ett glidande medelvärde.

### <a name="aggregate-distributed-data"></a>Sammanställda distribuerade data

API: t för avvikelse detektor fungerar bäst på en jämnt distribuerad tids serie. Om dina data är slumpmässigt distribuerade, ska du samla in dem med en tidsenhet, till exempel per minut, varje timme eller varje dag.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Avvikelse identifiering på data med säsongs mönster

Om du vet att tids serie data har ett säsongs mönster (en som inträffar med jämna mellanrum) kan du förbättra precisions-och API-svars tiden. 

`period`Om du anger en när du skapar en JSON-begäran kan du minska avvikelse identifierings fördröjningen med upp till 50%. `period`Är ett heltal som anger ungefär hur många data punkter tids serien tar för att upprepa ett mönster. En tids serie med en data punkt per dag skulle till exempel ha en `period` as `7` -serie och en tids serie med en punkt per timme (med samma vecko mönster) som har en `period`  `7*24` . Om du är osäker på dina data mönster behöver du inte ange den här parametern.

För bästa resultat ger du 4 en `period` data punkt, plus ytterligare en. Till exempel, data för varje timme med ett vecko mönster enligt beskrivningen ovan bör tillhandahålla 673 data punkter i begär ande texten ( `7 * 24 * 4 + 1` ).

### <a name="sampling-data-for-real-time-monitoring"></a>Samplings data för övervakning i real tid

Om dina strömmande data samplas med ett kort intervall (till exempel sekunder eller minuter) kan det hända att sändningen av det rekommenderade antalet data punkter överskrider det högsta tillåtna antalet för avvikelse detektor (8640 data punkter). Om dina data visar ett stabilt säsongs mönster kan du överväga att skicka ett exempel på dina Time Series-data till ett större tidsintervall, som timmar. Genom att sampla dina data på det här sättet kan du också märkbart förbättra API-svars tiden. 

## <a name="next-steps"></a>Nästa steg

* [Vad är API:et för avvikelseidentifiering?](../overview.md)
* [Snabb start: identifiera avvikelser i dina tids serie data med hjälp av avvikelse detektorn](../quickstarts/client-libraries.md)