---
title: Ord lista för mått rådgivare
titleSuffix: Azure Cognitive Services
description: Viktiga idéer och begrepp för tjänsten Metrics Advisor
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 6c461983053a145dfda58b9e3d26b39db0c339e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92893428"
---
# <a name="metrics-advisor-glossary-of-common-vocabulary-and-concepts"></a>Ord lista för mått rådgivare i vanliga ord listor och koncept

Det här dokumentet beskriver de tekniska termer som används i Metric Advisor. Använd den här artikeln om du vill veta mer om vanliga koncept och objekt som du kan stöta på när du använder tjänsten.

## <a name="data-feed"></a>Datafeed

> [!NOTE]
> Flera mått kan dela samma data källa och till och med samma datafeed.

En datafeed är vilka mått som rådgivare matar in från din data källa, till exempel Cosmos DB eller en SQL-Server. En datafeed innehåller rader med:
* tidsstämplar
* noll eller flera dimensioner
* en eller flera mått. 

## <a name="metric"></a>Metric

Ett mått är ett mätbart mått som används för att övervaka och utvärdera status för en enskild affärs process. Det kan vara en kombination av flera tids serie värden indelade i dimensioner. Till exempel kan ett *webb hälso* mått innehålla dimensioner för *antal användare* och en *-US-marknad*.

## <a name="dimension"></a>Dimension

En dimension är ett eller flera kategoriska-värden. Kombinationen av dessa värden identifierar en viss univariate tids serie, till exempel: land, språk, klient organisation och så vidare.

## <a name="multi-dimensional-metric"></a>Flerdimensionellt mått

Vad är ett mått på flera dimensioner? Vi använder två exempel. 

**Intäkter för ett företag**

Anta att du har data för intäkterna för ditt företag. Tids serie data kan se ut ungefär så här:

| Timestamp | Kategori | Telefonförsäljning | Intäkter |
| ----------|----------|--------|----- |
| 2020-6-1 | Kost | USA | 1000 |
| 2020-6-1 | Beklädnads | USA | 2000 |
| 2020-6-2 | Kost | Storbritannien | 800 | 
| ...      | ...  |... | ... |

I det här exemplet är *kategorin* och *marknaden* dimensioner. *Intäkter* är KPI-indikatorn (Key Performance Indicator) som kan segmenteras i olika kategorier och/eller marknader, och kan också aggregeras. Till exempel intäkterna för *livsmedel* för alla marknader.
 
**Antal fel för ett komplext program**

Anta att du har data för antalet fel som loggats i ett program. Tids serie data kan se ut ungefär så här:

| Timestamp | Program komponent | Region | Antal fel |
| ----------|----------|--------|----- |
| 2020-6-1 | Medarbetare-databas | VÄSTRA EU | 9000 |
| 2020-6-1 | Meddelandekö | USA, ÖSTRA | 1000 |
| 2020-6-2 | Meddelandekö | USA, ÖSTRA | 8000| 
| ...      | ...           | ...     |  ...|

I det här exemplet är *program komponent* och *region* dimensioner. *Antal fel* är KPI: n som kan segmenteras i olika kategorier och/eller marknader och kan också aggregeras. Till exempel antalet fel i *meddelande kön* i alla regioner.

## <a name="measure"></a>Mått

Ett mått är ett grundläggande eller enhetsspecifika villkor och ett kvantifierbart värde för måttet.

## <a name="time-series"></a>Tidsserier

En tids serie är en serie data punkter som indexeras (eller visas i diagrammet) i kronologisk ordning. Oftast är en tids serie en sekvens som tas i efterföljande, lika stora punkter i tid. Det är en sekvens av diskreta tids data.

I mått Advisor kallas värdena för ett mått i en speciell dimensions kombination en serie.

## <a name="granularity"></a>Precision

Granularitet anger hur ofta data punkter ska genereras på data källan. Till exempel varje dag, varje timme.

## <a name="start-time"></a>Starttid

Start tid är den tid som du vill att mått Advisor ska börja mata in data från data källan. Data källan måste ha data på den angivna start tiden.

## <a name="confidence-boundaries"></a>Konfidens gränser

> [!NOTE]
> Konfidens gränser är inte det enda måttet som används för att hitta avvikelser. Det är möjligt för data punkter utanför den här gränser som flaggas som normala av identifierings modellen. 

I mått Advisor representerar konfidensts känslighet för algoritmen som används och används för att filtrera bort alltför känsliga avvikelser. På webb portalen visas konfidens gränser som ett transparent blått band. Alla punkter inom bandet behandlas som normala punkter.

Metrics Advisor innehåller verktyg för att justera känslighet för de algoritmer som används. Mer information finns i [så här gör du: Konfigurera mått och finjustera identifiering av konfiguration](how-tos/configure-metrics.md) .

![Konfidens intervall](media/confidence-bounds.png)


## <a name="hook"></a>Uttryckt

Med mått Advisor kan du skapa och prenumerera på aviseringar i real tid. Dessa aviseringar skickas via Internet [med hjälp av en Hook](how-tos/alerts.md).

## <a name="anomaly-incident"></a>Avvikelse incident

När en identifierings konfiguration har tillämpats på måtten genereras incidenter varje gång en serie i den har en avvikelse. I stora data mängder kan detta vara överbelastat, så Metrics Advisor grupperar serier om avvikelser i ett mått till en incident. Tjänsten kommer också att utvärdera allvarlighets graden och tillhandahålla verktyg för att [diagnosticera incidenten](how-tos/diagnose-incident.md).

### <a name="incident-tree"></a>Incident träd

I Metric Advisor kan du använda avvikelse identifiering på mått, och Metric Advisor övervakar automatiskt alla tids serier för alla dimensions kombinationer. När en avvikande avvikelse upptäcks, aggregerar Metric Advisor avvikelser i incidenter.
När en incident inträffar tillhandahåller Metric Advisor ett incident träd med en hierarki med bidrags avvikelser och identifierar dem med största påverkan. Varje incident har en rotor Saks avvikelse, som är den översta noden i trädet.

### <a name="anomaly-grouping"></a>Avvikelse gruppering

Metrics Advisor ger möjlighet att hitta relaterade tids serier med liknande mönster. Det kan också ge djupare insikter om påverkan på andra dimensioner och korrelera avvikelser.

### <a name="time-series-comparison"></a>Jämförelse av tids serier

Du kan välja flera tids serier för att jämföra trender i en enda visualisering. Detta ger ett tydligt och inblickat sätt att visa och jämföra relaterade serier.

## <a name="detection-configuration"></a>Identifierings konfiguration

>[!Note]
>Identifierings konfigurationerna tillämpas bara inom ett individuellt mått.

På sidan Metrics Advisor-webbportalen visas en identifierings konfiguration (till exempel känslighet, automatisk vilo läge och riktning) i den vänstra panelen när du visar ett mått. Parametrar kan justeras och appliceras på alla serier i måttet.

En identifierings konfiguration krävs för varje tids serie och avgör om en punkt i tids serien är en avvikelse. Metrics Advisor ställer in en standard konfiguration för hela måttet när du först säkerhetskopierade data. 

Du kan också förfina konfigurationen genom att använda justerings parametrar i en grupp serie eller en annan. Endast en konfiguration kommer att gälla för en tids serie:
* Konfigurationer som tillämpas på en angiven serie skriver över konfigurationer för en grupp
* Konfigurationer för en grupp skriver över de konfigurationer som tillämpas på hela måttet.

Metrics Advisor innehåller flera [identifierings metoder](how-tos/configure-metrics.md#anomaly-detection-methods)och du kan kombinera dem med logiska operatorer.

### <a name="smart-detection"></a>Smart identifiering

Avvikelse identifiering med hjälp av flera Machine Learning-algoritmer.

**Känslighet**: ett numeriskt värde för att justera toleransen för avvikelse identifiering. Ett visuellt värde är ju högre värde, desto smalare är de övre och nedre gränserna runt tids serien.

### <a name="hard-threshold"></a>Hård tröskel

Värden utanför övre eller nedre gränser är avvikelser.

**Min**: den nedre kanten

**Max**: den övre gränser

### <a name="change-threshold"></a>Ändra tröskel

Använd föregående punkt värde för att avgöra om den här punkten är en avvikelse.

**Ändrings procent**: jämfört med föregående punkt är den aktuella punkten en avvikelse om procent andelen av ändring är mer än den här parametern.

**Ändra över punkter**: hur många punkter du vill se tillbaka.

### <a name="common-parameters"></a>Vanliga parametrar

**Riktning**: en punkt är en avvikelse endast när avvikelsen inträffar i riktningen *upp*, *ned* eller *både* och.

**Ogiltig avvikelse tills**: en data punkt är bara en avvikelse om en angiven procent andel av föregående punkter också är avvikelser.

## <a name="alert-settings"></a>Aviserings inställningar

Aviserings inställningarna avgör vilka avvikelser som ska utlösa en avisering. Du kan ange flera aviseringar med olika inställningar. Du kan till exempel skapa en avisering för avvikelser med lägre påverkan på företaget och en annan för fler prioritets varningar.

Du kan också skapa en avisering över mått. Till exempel en avisering som bara utlöses om två angivna mått har avvikelser.

### <a name="alert-scope"></a>Aviserings omfång

Aviserings omfånget avser den omfattning som aviseringen gäller. Det finns fyra alternativ:

**Avvikelser i alla serier**: aviseringar utlöses för avvikelser i alla serier i måttet.

**Avvikelser i serie grupp**: aviseringar aktive ras endast för avvikelser i vissa dimensioner i serie gruppen. Antalet angivna dimensioner måste vara mindre än det totala antalet dimensioner.

**Avvikelser i favorit serien**: aviseringar aktive ras endast för avvikelser som läggs till som favoriter. Du kan välja en grupp med serier som favorit för varje identifierings konfiguration.

**Avvikelser i översta n i alla serier**: aviseringar utlöses bara för avvikelser i den översta n-serien. Du kan ange parametrar för att ange antalet tidsstämplar som ska tas med i beräkningen och hur många avvikelser som måste finnas i dem för att skicka aviseringen.

### <a name="severity"></a>Allvarlighetsgrad

Allvarlighets grad är en klass som Metrics Advisor använder för att beskriva allvarlighets graden för incidenten, inklusive *hög*, *medel* och *låg*.

För närvarande använder Metric Advisor följande faktorer för att mäta allvarlighets graden för aviseringen:
1. Värdets andel och kvantitetens andel avvikelser i måttet.
1. Förtroende för avvikelser.
1. Dina favorit inställningar bidrar också till allvarlighets grad.

### <a name="auto-snooze"></a>Automatisk vilo läge

Vissa avvikelser är tillfälliga problem, särskilt för små precisions mått. Du kan försätta en avisering i *vilo läge* under ett angivet antal tids punkter. Om avvikelser upptäcks inom det angivna antalet punkter utlöses ingen avisering. Beteendet för automatisk vilo läge kan ställas in på antingen mått-eller serie nivå.

Beteendet för vilo läge kan anges antingen på mått-eller serie nivå.

## <a name="data-feed-settings"></a>Inställningar för datafeed

### <a name="ingestion-time-offset"></a>Tids förskjutning för inmatning

Som standard matas data in enligt granularitet (till exempel *dagligen*). Genom att använda ett positivt heltal kan du fördröj inmatning av data med det angivna värdet. Genom att använda ett negativt tal kan du gå vidare med det angivna värdet.

### <a name="max-ingestion-per-minute"></a>Maximal inmatning per minut

Ange den här parametern om data källan har stöd för begränsad samtidighet. Lämna annars standardinställningarna.

### <a name="stop-retry-after"></a>Avbryt försök efter

Om data inmatningen Miss lyckas, görs ett nytt försök automatiskt efter en tids period. Början av perioden är den tidpunkt då den första data inmatningen skedde. Längden på perioden för återförsök definieras enligt granularitet. Om du använder standardvärdet ( `-1` ) bestäms återförsöks perioden enligt granularitet:

| Precision       | Avbryt försök efter           |
| :------------ | :--------------- |
| Daglig, anpassad (>= 1 dag), varje vecka, varje månad, varje år     | 7 dagar          |
| Varje timme, anpassad (< 1 dag)       | 72 timmar |

### <a name="min-retry-interval"></a>Minsta återförsöksintervall

Du kan ange det minsta intervallet när du försöker hämta data från källan igen. Om du använder standardvärdet ( `-1` ) bestäms intervallet för återförsök enligt granularitet:

| Precision       | Minsta återförsöksintervall           |
| :------------ | :--------------- |
| Dagligen, anpassat (>= 1 dag), varje vecka, varje månad     | 30 minuter          |
| Varje timme, anpassad (< 1 dag)      | 10 minuter |
| Varje år | 1 dag          |

### <a name="grace-period"></a>Respitperiod

> [!Note]
> Grace-perioden börjar med den normala Inhämtnings tiden, plus den angivna tids förskjutningen för inmatningen.
    
En respitperiod är en tids period då måtten Advisor fortsätter att hämta data från data källan, men kan inte utlösa några aviseringar. Om inga data har matats in efter respitperioden utlöses en avisering om att en *datafeed inte är tillgänglig* .

### <a name="snooze-alerts-in"></a>Aviseringar i vilo läge i

När det här alternativet är inställt på noll utlöser varje tidsstämpel med *inte tillgänglig* en avisering. När värdet har angetts till ett annat värde än noll blir det angivna antalet *ej tillgängliga* varningar i vilo läge om inga data hämtades.

## <a name="data-feed-permissions"></a>Behörigheter för data feed

Det finns två roller för att hantera feed-behörigheter: *administratör* och *visnings program*. 

* En *administratör* har fullständig kontroll över datafeeden och måtten i den. De kan aktivera, pausa, ta bort datafeeden och göra uppdateringar av feeds och konfigurationer. En *administratör* är vanligt vis ägare till måtten.

* Ett *visnings program* kan visa datafeeden eller måtten, men kan inte göra ändringar. 

## <a name="next-steps"></a>Nästa steg
- [Översikt över Metrics Advisor](overview.md)
- [Använda webbportalen](quickstarts/web-portal.md)