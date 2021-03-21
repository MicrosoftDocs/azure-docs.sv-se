---
title: ta med fil
description: ta med fil
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92517010"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>Vad kan jag uppnå med ett kluster?

För ett Event Hubs kluster, hur mycket du kan mata in och strömma beror på olika faktorer, till exempel producenter, konsumenter, den hastighet som du matar in och bearbetar, och mycket mer. 

I följande tabell visas de benchmark-resultat som vi uppnått vid testningen:

| Nytto Last form | Mottagare | Ingress bandbredd| Ingress meddelanden | Utgående bandbredd | Utgående meddelanden | Totalt antal antal | Antal per CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batchar av 100x1KB | 2 | 400 MB/SEK | 400k meddelanden/SEK | 800 MB/SEK | 800k meddelanden/SEK | 400 antal | 100 antal | 
| Batchar av 10x10KB | 2 | 666 MB/SEK | 66.6 k meddelanden/SEK | 1,33 GB/SEK | 133k meddelanden/SEK | 666 antal | 166 antal |
| Batchar av 6x32KB | 1 | 1,05 GB/SEK | 34k meddelanden/SEK | 1,05 GB/SEK | 34k meddelanden/SEK | 1000 antal | 250 antal |

I testningen användes följande kriterier:

- Ett dedikerat nivå Event Hubs kluster med fyra kapacitets enheter (CUs) användes. 
- Händelsehubben som används för inmatning hade 200 partitioner. 
- De data som matats in togs emot av två mottagar program som tar emot från alla partitioner.

### <a name="can-i-scale-updown-my-cluster"></a>Kan jag skala upp/ned mitt kluster?

Efter skapandet faktureras kluster för minst 4 timmars användning. I för hands versionen av den självbetjänings upplevelsen kan du skicka en [supportbegäran](https://ms.portal.azure.com/#create/Microsoft.Support) till Event Hubss gruppen under **teknisk**  >  **kvot**  >  **förfrågan för att skala upp eller ned ett dedikerat kluster** för att skala upp eller ned ett dedikerat kluster. Det kan ta upp till 7 dagar att slutföra begäran om att skala upp klustret. 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>Hur fungerar geo-DR med mitt kluster?

Du kan geo-para ett namn område under ett dedikerat-nivå kluster med ett annat namn område under ett dedikerat skikt kluster. Vi uppmuntrar inte att para ihop ett dedikerat namn område med ett namn område i vårt standard erbjudande eftersom data flödes gränsen är inkompatibel och resulterar i fel. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Kan jag migrera mina standard namn rymder så att de tillhör ett dedikerat kluster?
Vi stöder för närvarande inte en automatiserad migreringsprocessen för att migrera dina Event Hub-data från ett standard namn område till en dedikerad. 
