---
title: Vanliga frågor och svar om mått rådgivare
titleSuffix: Azure Cognitive Services
description: Vanliga frågor och svar om Metrics Advisor-tjänsten.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420967"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Vanliga frågor och svar om mått rådgivare

### <a name="what-is-the-cost-of-my-instance"></a>Vad kostar min instans?

Det finns för närvarande ingen kostnad för att använda din instans under för hands versionen.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Varför kan jag inte skapa resursen? Pris nivån är inte tillgänglig och säger att du redan har skapat 1 S0 för den här prenumerationen?

:::image type="content" source="media/pricing.png" alt-text="Meddelande när det redan finns en F0-resurs":::

Under en offentlig för hands version kan endast en instans av Metrics Advisor skapas per region under en prenumeration.

Om du redan har en instans som skapats i samma region med samma prenumeration kan du prova med en annan region eller en annan prenumeration för att skapa en ny instans. Du kan också ta bort en befintlig instans om du vill skapa en ny.

Om du redan har tagit bort den befintliga instansen men ändå ser felet, vänta i ungefär 20 minuter efter att resursen tagits bort innan du skapar en ny instans.

## <a name="basic-concepts"></a>Grundläggande begrepp

### <a name="what-is-multi-dimensional-time-series-data"></a>Vad är flerdimensionella Time Series-data?

Se definitionen av [flerdimensionell mått](glossary.md#multi-dimensional-metric)  i ord listan.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Hur mycket data behövs för att Metric Advisor ska starta avvikelse identifiering?

En data punkt kan som minimum utlösa avvikelse identifiering. Detta ger dock inte bästa möjliga noggrannhet. Tjänsten kommer att anta ett fönster med tidigare data punkter med det värde som du har angett som "fyllnings Lucke"-regel när datafeed skapas.

Vi rekommenderar att du har några data före den tidstämpel som du vill identifiera.
Baserat på data precisionen varierar den rekommenderade data mängden enligt nedan.

| Precision | Rekommenderat data belopp för identifiering |
| ----------- | ------------------------------------- |
| Mindre än 5 minuter | 4 dagars data |
| 5 minuter till 1 dag | 28 dagars data |
| Mer än 1 dag, till 31 dagar | 4 års data |
| Mer än 31 dagar | 48 års data |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Varför är metric Advisor inte identifiera avvikelser från historiska data?

Metrics Advisor är utformad för att identifiera direkt strömmande data. Det finns en begränsning av den maximala längden för historiska data som tjänsten kommer att se tillbaka och köra avvikelse identifiering. Det innebär att endast data punkter efter en viss tidigaste tidstämpel har avvikelse identifierings resultat. Den tidigaste tidsstämpeln beror på datans granularitet.

Baserat på data granularitet är längden på historiska data som har avvikelse identifierings resultat som nedan.

| Precision | Maximal längd på historiska data för avvikelse identifiering |
| ----------- | ------------------------------------- |
| Mindre än 5 minuter | Drift tid – 13 timmar |
| 5 minuter till mindre än 1 timme | Onboard Time-4 dagar  |
| 1 timme till mindre än 1 dag | Drift tid – 14 dagar  |
| 1 dag | Onboard Time – 28 dagar  |
| Mer än 1 dag, mindre än 31 dagar | Onboard Time – 2 år  |
| Mer än 31 dagar | Drift tid – 24 år   |

### <a name="more-concepts-and-technical-terms"></a>Fler begrepp och tekniska villkor

Se även [ord](glossary.md) listan för mer information.

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Hur gör jag för att skriva en giltig fråga för att mata in mina data?  

För att mått Advisor ska mata in dina data måste du skapa en fråga som returnerar dimensionerna för dina data med en enda tidsstämpel. Mått Advisor kommer att köra frågan flera gånger för att hämta data från varje tidsstämpel. 

Observera att frågan ska returnera högst en post för varje dimensions kombination vid en specifik tidsstämpel. Alla poster som returneras måste ha samma tidsstämpel. Det får inte finnas några dubbla poster som returneras av frågan.

Anta till exempel att du skapar frågan nedan för ett dagligt mått: 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Se till att använda rätt granularitet för din tids serie. För ett Tim mått använder du: 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Observera att dessa frågor bara returnerar data i en enda tidsstämpel och innehåller alla dimensions kombinationer som ska matas in av Metrics Advisor. 

:::image type="content" source="media/query-result.png" alt-text="Ett frågeresultat med en tidsstämpel" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Hur gör jag för att identifiera toppar & DIP som avvikelser?

Om du har hård tröskelvärden fördefinierade kan du faktiskt ange "hård tröskel" i konfigurationer för [avvikelse identifiering](how-tos/configure-metrics.md#anomaly-detection-methods).
Om det inte finns några tröskelvärden kan du använda "Smart identifiering" som drivs av AI. Mer information finns i [finjustera konfigurationen för identifiering](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Hur gör jag för att identifiera inkonsekvenser med regelbundna (säsongs) mönster som avvikelser?

"Smart identifiering" kan lära sig mönstret för dina data, inklusive säsongs mönster. Den identifierar sedan de data punkter som inte uppfyller vanliga mönster som avvikelser. Mer information finns i [finjustera konfigurationen för identifiering](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Hur gör jag för att identifiera enkla linjer som avvikelser?

Om dina data normalt är instabila och varierar mycket, och du vill bli aviserad om det blir för stabilt eller till och med en plan, kan "ändra tröskel" konfigureras för att identifiera sådana data punkter när ändringen är för liten.
Mer information finns i [konfigurationer för avvikelse identifiering](how-tos/configure-metrics.md#anomaly-detection-methods) .

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>Hur konfigurerar jag e-postinställningar och aktiverar aviseringar via e-post?

1.  En användare med administratörs behörighet för prenumeration eller resurs grupp måste gå till den Metric Advisor-resurs som skapades i Azure Portal och välja fliken **åtkomst kontroll (IAM)** . 
2.  Välj **Lägg till roll tilldelningar**
3.  Välj en roll för **Cognitive Services Metrics Advisor-administratör**, Välj ditt konto som i bilden nedan.
4.  Klicka på knappen **Spara** , sedan har du lagt till som administratör för Metrics Advisor-resursen. Observera att alla åtgärder ovan måste utföras av prenumerations administratören eller resurs grupps administratören. 

:::image type="content" source="media/access-control.png" alt-text="Meny sidan åtkomst kontroll (IAM) med Lägg till en roll tilldelning markerad, följt av en ruta med tilldela åtkomst till vald användare som visas med en åtkomst roll för Cognitive Services Metrics Advisor-administratör, följt av knappen Spara för det användar gränssnitt som valts för att illustrera stegen för att söka efter en användare och lägga till en viss nivå av åtkomst behörighet." lightbox="media/access-control.png":::


5.  Det kan ta upp till en minut innan behörigheterna har spridits. Välj sedan arbets ytan mått rådgivare och välj alternativet **e-postinställning** i den vänstra navigerings panelen. Fyll i de obligatoriska objekten, särskilt SMTP-relaterad information. 
6.  Välj **Spara**. sedan är allt konfigurerat med e-postkonfigurationen. Du kan skapa nya krokar och prenumerera på mått avvikelser för nära real tids aviseringar. 

## <a name="advanced-concepts"></a>Avancerade koncept

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>Hur skapar Metric Advisor ett incident träd för flerdimensionella mått?

Mått kan delas upp i flera tids serier per dimension. Till exempel `Response latency` övervakas måttet för alla tjänster som ägs av teamet. `Service`Kategorin kan användas som en dimension för att utöka måttet, så vi ska `Response latency` dela med `Service1` , `Service2` och så vidare. Varje tjänst kan distribueras på olika datorer i flera data Center, så måttet kan delas av `Machine` och `Data center` .

|Tjänst| Data Center| Dator  | 
|----|------|----------------   |
| S1 |  DC1 |   M |
| S1 |  DC1 |   = |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M |
| S2 |  DC1 |   = |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

Från och med summan `Response latency` kan vi öka detalj nivån i måttet av `Service` `Data center` och `Machine` . Det kan dock vara mer meningsfullt för tjänste ägare att använda sökvägen `Service`  ->  `Data center`  ->  `Machine` , eller kanske är det mer begripligt för infrastruktur tekniker att använda sökvägen `Data Center`  ->  `Machine`  ->  `Service` . Det beror helt på de enskilda användarnas affärs behov. 

I Metric Advisor kan användarna ange vilken sökväg de vill öka detalj nivån eller sammanslagningen från en nod i den hierarkiska topologin. Mer exakt är den hierarkiska topologin ett riktat acykliska diagram i stället för en träd struktur. Det finns en fullständig hierarkisk topologi som består av alla möjliga dimensions kombinationer, som detta: 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="diagram över hierarkiska topologier som består av flera sammankopplade hörn och kanter med flera dimensioner märkta S, DC och M med motsvarande nummer mellan 1 och 6" lightbox="media/dimension-combinations-view.png":::

I teorin, om dimensionen `Service` har `Ls` distinkta värden, `Data center` har dimensionen `Ldc` distinkta värden och dimensionen `Machine` har `Lm` distinkta värden, så det kan finnas `(Ls + 1) * (Ldc + 1) * (Lm + 1)` Dimensions kombinationer i den hierarkiska topologin. 

Men vanligt vis är inte alla dimensions kombinationer giltiga, vilket kan minska komplexiteten avsevärt. För närvarande om användarna sammanställer själva måttet begränsar vi inte antalet dimensioner. Om du behöver använda sammanslagnings funktionen som tillhandahålls av Metrics Advisor bör antalet dimensioner vara större än 6. Vi begränsar dock antalet tids serier som utökats med dimensioner för ett mått till mindre än 10 000.

**Incident träd** verktyget på sidan diagnostik visar bara noder där en avvikelse har upptäckts, i stället för hela topologin. Detta är att hjälpa dig att fokusera på det aktuella problemet. Det kan också hända att alla avvikelser inte visas i måttet. i stället visas de vanligaste avvikelserna baserat på bidrag. På så sätt kan vi snabbt ta reda på konsekvenserna, omfattningen och spridnings vägen för onormala data. Vilket avsevärt minskar antalet avvikelser som vi behöver fokusera på och hjälper användarna att förstå och hitta sina viktiga problem. 
 
Till exempel när en avvikelse inträffar på påverkar avvikelsen `Service = S2 | Data Center = DC2 | Machine = M5` av avvikelsen den överordnade noden `Service= S2` som också har identifierat avvikelsen, men avvikelsen påverkar inte hela data centret på `DC2` och alla tjänster på `M5` . Incident trädet skulle skapas som i skärm bilden nedan, den översta avvikelsen samlas in `Service = S2` och rotor saken kan analyseras i två sökvägar som båda leder till `Service = S2 | Data Center = DC2 | Machine = M5` .

 :::image type="content" source="media/root-cause-paths.png" alt-text="5 märkta hörn med två distinkta sökvägar sammankopplade med en gemensam nod med etiketten S2. Den främsta avvikelsen samlas in på tjänsten = S2, och rotor saken kan analyseras av de två Sök vägarna som båda leder till service = S2 | Data Center = DC2 | Machine = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>Nästa steg
- [Översikt över Metrics Advisor](overview.md)
- [Använda webbportalen](quickstarts/web-portal.md)