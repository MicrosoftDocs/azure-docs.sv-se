---
title: Tolka ditt styrkort | Microsoft Docs
description: Lär dig hur du tolkar ditt styrkort. Fliken styrkort innehåller sammanställda och analyserade resultat från dina tester.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f43d094193fb266d1ecec7089b44d8b3fd5e9b43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91330221"
---
# <a name="interpreting-your-scorecard"></a>Tolka ditt styrkort

Fliken styrkort innehåller sammanställda och analyserade resultat från dina tester. Varje test har sina egna styrkort. Styrkort ger snabba och meningsfulla sammanfattningar av mät resultaten för att tillhandahålla data drivna resultat för dina nätverks krav. Internet Analyzer tar hand om analysen, så att du kan fokusera på beslutet.

Fliken styrkort hittar du på menyn för Internet Analyzer-resurs. 


## <a name="filters"></a>Filter

* ***Test:*** Välj det test som du vill visa resultaten för – varje test har sitt eget styrkort. Test data visas när det finns tillräckligt med data för att slutföra analysen – i de flesta fall bör detta vara inom 24 timmar. 
* ***Tids period & slutdatum:*** Tre styrkort genereras dagligen – varje styrkort visar en annan sammanställnings period – 24 timmar före (dag), sju dagar före (vecka) och 30 dagar före (månad). Använd filtret "avsluta datum" för att välja den sista dagen i tids perioden som du vill se. 
* ***Land:*** Ett styrkort skapas för varje land som du har slutanvändare. Det globala filtret innehåller alla slutanvändare.

## <a name="measurement-count"></a>Antal mätningar

Antalet mätningar påverkar analysens tillförlitlighet. Ju högre antal desto bättre blir resultatet. Som minimum bör testerna riktas mot minst 100 mätningar per slut punkt per dag. Om antalet mätningar är för lågt konfigurerar du JavaScript-klienten så att den körs oftare i ditt program. Mätnings antalet för slut punkter A och B bör vara mycket likt även om små skillnader förväntas och OK. Om det finns stora skillnader bör resultatet inte vara betrott.

## <a name="percentiles"></a>Percentiler

Svars tid, mätt i millisekunder, är ett populärt mått för att mäta hastigheten mellan en källa och ett mål på Internet. Latens-data distribueras vanligt vis inte (dvs. följer inte en "Bell-kurva") eftersom det finns en "lång ände" av stora latens-värden som skevar resultat när du använder statistik som det aritmetiska medelvärdet. Alternativt ger percentiler ett distributions fritt sätt att analysera data. Som exempel, median eller 50: e percentilen, sammanfattar mitten av fördelningen – hälften av värdena ovanför och halva. Ett 75th: e percentils värde innebär att det är större än 75% av alla värden i distributionen. Internet Analyzer refererar till percentiler i kort skrift som P50, p75 och P95.

Internet Analyzer-percentiler är _exempel mått_. Detta är i motsats till det faktiska _populations måttet_. Till exempel är den dagliga sanna populationens svars tid mellan studenter vid University of södra Kalifornien och Microsoft är median värdet för svars tid för alla förfrågningar under den dagen. I praktiken är det opraktiskt att mäta värdet för alla förfrågningar, så vi antar att ett rimligt stort exempel är representativt för den faktiska populationen.

I analys syfte är P50 (median) användbart som ett förväntat värde för en latens distribution. Högre percentiler, till exempel P95, är användbara för att identifiera hur hög latens är i värsta fall. Om du är intresse rad av att förstå kund fördröjning i allmänhet är P50 rätt mått att fokusera på. Om du är orolig för att förstå prestanda för de sämsta kunderna bör P95 vara fokus. P75 är ett saldo mellan dessa två.


## <a name="deltas"></a>Deltan

En förändring är skillnaden i metriska värden för slut punkter A och B. delta beräknas för att visa fördelarna med B över ett. positiva värden anger att B har genomförts bättre än A, medan negativa värden visar att B-prestanda är sämre. Delta kan vara absoluta (t. ex. 10 millisekunder) eller relativa (5%).

## <a name="confidence-interval"></a>Konfidensintervall 

Konfidens intervall (CI) är ett intervall med värden som har sannolikhet att innehålla populations måttet, till exempel median, p75 eller Average. Vi följer den gemensamma statistik konventionen för att använda 95% CI.

För Internet Analyzer är ett smalt konfidens intervall bra eftersom det visar exempel måttet är förmodligen mycket nära det faktiska populations måttet. Ett brett konfidens intervall innebär mindre säkerhet att vårt exempel mått återspeglar det faktiska populations måttet. Det bästa sättet att förbättra CI är att öka antalet mått.

## <a name="time-series"></a>Tidsserier 

En tids serie visar hur ett mått förändras över tid. På Internet finns det många temporala faktorer som påverkar prestandan, till exempel högsta trafik perioder, veckodag-veckosluts skillnader och helgdagar.


## <a name="next-steps"></a>Nästa steg

Läs mer i [Översikt över Internet Analyzer](internet-analyzer-overview.md).
