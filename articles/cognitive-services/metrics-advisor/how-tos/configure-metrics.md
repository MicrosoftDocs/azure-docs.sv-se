---
title: Konfigurera din Metric Advisor-instans med hjälp av webb portalen
titleSuffix: Azure Cognitive Services
description: Så här konfigurerar du din Metrics Advisor-instans och finjusterar resultaten för avvikelse identifiering.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 7923dad3d47122c0ceb04d1240736e2b66a0dd64
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92048261"
---
# <a name="how-to-configure-metrics-and-fine-tune-detecting-configuration"></a>Gör så här: Konfigurera mått och finjustera identifiering av konfiguration

Använd den här artikeln för att börja konfigurera mått Advisor-instansen med hjälp av webb portalen. Om du vill bläddra bland måtten för en speciell datafeed går du till sidan **data matningar** och väljer en av feeds. Då visas en lista över mått som är kopplade till den.

:::image type="content" source="../media/metrics/select-metric.png" alt-text="Välj ett mått" lightbox="../media/metrics/select-metric.png":::

Klicka på ett av mått namnen för att se information om det. I den här detaljerade vyn kan du växla till ett annat mått i samma datafeed med hjälp av list rutan i det övre högra hörnet på skärmen.

När du först visar en mått information kan du läsa in en tids serie genom att låta mått Advisor välja en åt dig, eller genom att ange värden som ska ingå i varje dimension. 

Du kan också välja tidsintervall och ändra sidans layout.

> [!NOTE]
> - Start tiden är inkluderad.
> - Slut tiden är exklusiv. 

Du kan klicka på fliken **incidenter** om du vill visa avvikelser och hitta en länk till [incident Hub](diagnose-incident.md).

## <a name="tune-the-detecting-configuration"></a>Justera konfigurationen för identifiering

Ett mått kan använda en eller flera identifierings konfigurationer. Det finns en standard konfiguration för varje mått, som du kan redigera eller lägga till i enligt dina övervaknings behov.

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>Justera konfigurationen för alla serier i aktuellt mått

Den här konfigurationen används för alla serier i det här måttet, förutom för dem med en separat konfiguration. En mått nivå konfiguration används som standard när data registreras och visas i den vänstra panelen. Användare kan redigera Metric-konfigurationen på mått sidan direkt. 

Det finns ytterligare parametrar som **riktning** och **giltig avvikelse** som kan användas för att ytterligare finjustera konfigurationen. Du kan också kombinera olika identifierings metoder. 

:::image type="content" source="../media/configuration-combination.png" alt-text="Konfigurations kombination" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>Justera konfigurationen för en bestämd serie eller grupp

Klicka på **Avancerad konfiguration** under konfigurations alternativen på mått nivå för att se grupp nivå konfigurationen. Du kan lägga till en konfiguration för en enskild serie eller grupp med serier genom att klicka på **+** ikonen i det här fönstret. Parametrarna liknar konfigurations parametrarna på mått nivå, men du kan behöva ange minst ett dimensions värde för en grupp nivå konfiguration för att identifiera en grupp med serier. Och ange alla dimensions värden för konfiguration på serie nivå för att identifiera en speciell serie. 

Den här konfigurationen kommer att tillämpas på grupp serien eller i vissa serier i stället för på mått nivå konfigurationen. När du har angett villkoren för den här gruppen sparar du den.

:::image type="content" source="../media/advanced-configuration.png" alt-text="Avancerad konfiguration" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>Metoder för avvikelse identifiering

Mått Advisor innehåller flera metoder för avvikelse identifiering. Du kan använda en eller kombinera dem med logiska operatorer genom att klicka på **+** knappen. 

**Smart identifiering** 

Smart identifiering drivs av maskin inlärning som lär sig mönster från historiska data och använder dem för framtida identifiering. När den här metoden används är **känslighet** den viktigaste parametern för att justera identifierings resultatet. Du kan dra det till ett mindre eller större värde för att påverka visualiseringen på höger sida av sidan. Välj en som passar dina data och spara den. 


I läget för smart identifiering används känslighets-och gränser versions parametrarna för att finjustera resultatet av avvikelse identifiering.

Känslighet kan påverka bredden på det förväntade värde intervallet för varje punkt. Vid ökningen blir det förväntade värde intervallet tätare och fler avvikelser rapporteras:

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="Smart identifiering med hög känslighet":::

När känslighets värdet är inaktiverat blir det förväntade värde intervallet större, och färre avvikelser rapporteras:

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="Smart identifiering med låg känslighet":::

**Ändra tröskel** 

Ändrings tröskeln används vanligt vis när mått data normalt hålls runt ett visst intervall. Tröskelvärdet anges enligt **ändrings procent**. **Ändrings tröskel** läget kan identifiera avvikelser i scenarierna:

* Dina data är vanligt vis stabila och smidiga. Du vill få ett meddelande när det finns fluktuationer.
* Dina data är normalt ganska instabila och varierar mycket. Du vill få ett meddelande när det blir för stabilt eller fast.

Använd följande steg för att använda det här läget:

1. Välj **ändra tröskel** som metod för avvikelse identifiering när du ställer in konfigurationerna för avvikelse identifiering för dina mått eller tids serier.
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="ändra tröskel":::

2. Välj **utanför intervallet** eller **i parametern Range** baserat på ditt scenario.

    Om du vill identifiera variationer väljer du **utanför intervallet**. Med inställningarna nedan identifieras till exempel alla data punkter som ändras över 10% jämfört med den tidigare en avvikare.
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="out-of-Range-parameter":::

    Om du vill identifiera fasta linjer i dina data väljer du **i intervallet**. Med inställningarna nedan identifieras till exempel alla data punkter som ändras inom 0,01% jämfört med den tidigare en avvikare. Eftersom tröskelvärdet är så litet (0,01%) identifieras fasta linjer i data som avvikare.

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="I intervall parameter":::

3. Ange procent andelen av ändring som räknas som en avvikelse och som tidigare fångade data punkter ska användas för jämförelse. Den här jämförelsen är alltid mellan den aktuella data punkten och en enda data punkt är N punkter före.
    
    **Riktningen** är bara giltig om du använder **utanför intervallet** :
    
    * **Konfigurera identifiering** för att bara identifiera avvikelser när (aktuell data punkt)-(jämförelse data punkt) > **+** tröskel procent.
    * **Konfigurera** identifieringen för att endast identifiera avvikelser när (aktuell data punkt)-(jämför data punkt) < **-** tröskel procent.
 
**Hård tröskel**

 Hård tröskel är en grundläggande metod för avvikelse identifiering. Du kan ange en övre och/eller lägre gränser för att fastställa det förväntade värde intervallet. De punkter som faller utanför gränsen kommer att identifieras som en avvikelse. 


## <a name="preset-events"></a>Förinställda händelser

Ibland kan förväntade händelser och förekomster (t. ex. helgdagar) generera avvikande data. Med förinställda händelser kan du lägga till flaggor i utdata för avvikelse identifiering under angivna tider. Den här funktionen ska konfigureras efter att datafeeden har publicerats. Varje mått kan bara ha en förinställd händelse konfiguration.

> [!Note]
> Den förinställda händelse konfigurationen tar helgdagar vid avvikelse identifiering och kan ändra resultatet. Den kommer att användas för de data punkter som matas in när du har sparat konfigurationen. 

Klicka på knappen **Konfigurera Förvals händelse** bredvid List rutan mått på varje mått informations sida.
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="knappen för förinställt händelse":::

I fönstret som visas konfigurerar du alternativen enligt din användning. Se till att **Aktivera högtids händelse** har valts för att använda konfigurationen. 

Avsnittet **högtids händelse** hjälper dig att förhindra onödiga avvikelser som upptäckts under helger. Det finns två alternativ för det **strategi** alternativ som du kan använda:

* **Utelämna helgdagar**: förhindrar alla avvikelser och varningar i resultat av avvikelse identifiering under semester period.
* **Helgdag som helg**: beräknar det genomsnittliga förväntade värdet för flera motsvarande helger före semestern och baserar avvikelse statusen från dessa värden.

Det finns flera andra värden som du kan konfigurera:

|Alternativ  |Beskrivning  |
|---------|---------|
|**Välj en dimension som land**     | Välj en dimension som innehåller information om land. Till exempel en landskod.         |
|**Kod mappning för land**     | Mappningen mellan en standard [lands kod](https://wikipedia.org/wiki/ISO_3166-1_alpha-2)och vald dimensions lands data.        |
|**Helgdags alternativ**    | Om alla semestrar ska beaktas, endast PTO (betald ledighet) eller endast icke-PTO semester.         |
|**Dagar som ska expanderas**    |  De påverkade dagarna före och efter en helgdag.        |


Avsnittet **cykel händelse** kan användas i vissa scenarier för att minska onödig aviseringar genom att använda cykliska mönster i data. Exempel: 

- Mått som har flera mönster eller cykler, till exempel både veckovis och månatligt mönster. 
- Mått som inte har ett tydligt mönster, men data är jämförbara år över året (årsbasis), månad över månad (MoM), veckan över veckan (WoW) eller Day-Day (DoD).
 
Alla alternativ är inte valbara för varje kornig het. De tillgängliga alternativen per granularitet är nedan:

| Precision | Årsbasis | Modulen | WoW | DoD |
|:-|:-|:-|:-|:-|
| Varje år | X | X | X | X |
| Varje månad | X | X | X | X |
| Varje vecka | ✔ | X | X | X |
| Varje dag | ✔ | ✔ | ✔ | X |
| Varje timma | ✔ | ✔ | ✔ | ✔ |
| Varje minut | X | X | X | X |
| Andra | X | X | X | X |
| Bild | ✔ | ✔ | ✔ | ✔ |

X-otillgänglig.  
✔ – Tillgängligt.
  
\* När du använder en anpassad kornig het i sekunder, är endast tillgänglig om måttet är längre än en timme och mindre än en dag.

Cykel händelsen används för att minska avvikelser om de följer ett cykliskt mönster, men det rapporterar en avvikelse om flera data punkter inte följer mönstret. **Strikt läge** används för att aktivera avvikelse rapportering om inte ens en data punkt följer mönstret. 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="konfiguration av förinställda händelser":::

## <a name="view-recent-incidents"></a>Visa senaste incidenter

Mått Advisor identifierar avvikelser i alla dina tids serie data när de matas in. Men alla avvikelser behöver inte eskaleras, eftersom de kanske inte har stor påverkan. Agg regeringen kommer att utföras på avvikelser för att gruppera relaterade i incidenter. Du kan visa dessa incidenter från fliken **incident** på sidan med mått information. 

Klicka på en incident för att gå till sidan **incident analys** där du kan se mer information om den. Klicka på **hantera incidenter i New incident Hub** för att hitta sidan för [incident Hub](diagnose-incident.md) där du kan hitta alla incidenter under det aktuella måttet. 

## <a name="subscribe-anomalies-for-notification"></a>Prenumerations avvikelser för avisering

Om du vill bli meddelad när en avvikelse identifieras kan du prenumerera på aviseringar för måttet med hjälp av en Hook. Mer information finns i [Konfigurera aviseringar och få meddelanden med hjälp av en Hook](alerts.md) .


## <a name="next-steps"></a>Nästa steg 
- [Konfigurera aviseringar och få meddelanden via en hook](alerts.md)
- [Justera avvikelseidentifiering med hjälp av feedback](anomaly-feedback.md)
- [Diagnostisera en incident](diagnose-incident.md).

