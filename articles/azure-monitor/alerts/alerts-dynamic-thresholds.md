---
title: Skapa aviseringar med dynamiska tröskelvärden i Azure Monitor
description: Skapa aviseringar med maskin inlärnings beroende dynamiska tröskelvärden
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 9cf4caf77d14dd1296276ca48827c170ef265e9b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718071"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Mått aviseringar med dynamiska tröskelvärden i Azure Monitor

Mått varning med identifiering av dynamiskt tröskelvärden utnyttjar avancerad Machine Learning (ML) för att lära sig mått på historiska beteende, identifiera mönster och avvikelser som indikerar möjliga tjänst problem. Det ger stöd för både ett enkelt användar gränssnitt och åtgärder i skala genom att tillåta användare att konfigurera aviserings regler via Azure Resource Manager-API: et på ett helt automatiserat sätt.

När en varnings regel har skapats aktive ras den bara när det övervakade måttet inte fungerar som förväntat, baserat på dess anpassade tröskelvärden.

Vi vill gärna höra dina synpunkter, se till att de kommer <azurealertsfeedback@microsoft.com> .

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Varför och när använder dynamisk villkors typ rekommenderas?

1. **Skalbar avisering** – aviserings regler för dynamiskt tröskelvärden kan skapa skräddarsydda tröskelvärden för hundratals mått serier i taget, men samtidigt ge samma enklare för att definiera en varnings regel för ett enda mått. De ger dig färre aviseringar för att skapa och hantera. Du kan använda antingen Azure Portal eller Azure Resource Manager-API: et för att skapa dem. Den skalbara metoden är särskilt användbar när du hanterar mått dimensioner eller när du tillämpar på flera resurser, t. ex. för alla prenumerations resurser.  [Lär dig mer om hur du konfigurerar mått aviseringar med dynamiska tröskelvärden med hjälp av mallar](./alerts-metric-create-templates.md).

1. **Smart metrisk mönster igenkänning** – med vår ml-teknik kan vi automatiskt identifiera Mät mönster och anpassa sig till mått förändringar över tid, vilket ofta kan innefatta säsongs beroende (per timme/dag/vecka). Att anpassa sig till måttets beteende över tid och avisering baserat på avvikelser från dess mönster innebär att du kan känna till "höger"-tröskelvärdet för varje mått. Den ML-algoritm som används i dynamiska tröskelvärden är utformad för att förhindra störningar (låg precision) eller ett begränsat (låg återställnings) tröskelvärden som inte har ett förväntat mönster.

1. **Intuitiv konfiguration** – dynamiska tröskelvärden gör det möjligt att ställa in mått aviseringar med hjälp av koncept på hög nivå, vilket minskar behovet av att ha omfattande domän kunskaper om måttet.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Hur konfigurerar jag aviserings regler med dynamiska tröskelvärden?

Aviseringar med dynamiska tröskelvärden kan konfigureras via mått varningar i Azure Monitor. [Läs mer om hur du konfigurerar mått aviseringar](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Hur beräknas tröskelvärdena?

Dynamiska tröskelvärden lär sig kontinuerligt data i mått serien och försöker modellera den med en uppsättning algoritmer och metoder. Den identifierar mönster i data, t. ex. säsongs beroende (per timme/dag/vecka) och kan hantera brus mått (till exempel maskin-CPU eller minne) samt mått med låg spridning (till exempel tillgänglighet och fel frekvens).

Tröskelvärdena väljs på ett sådant sätt att avvikelser från dessa tröskelvärden indikerar en avvikelse i mått beteendet.

> [!NOTE]
> Dynamiska tröskelvärden kan identifiera säsongs beroende för varje timme, varje dag eller vecko mönster. Andra mönster som bi-eller halv vecko säsongs beroende kanske inte kan identifieras. För att upptäcka vecko säsongs beroende krävs minst tre veckors historiska data. 

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Vad betyder känslighets inställningen i dynamiska tröskelvärden?

Tröskelvärde för aviserings tröskel är ett koncept på hög nivå som styr mängden avvikelse från det mått som krävs för att utlösa en avisering.
Det här alternativet kräver inte domän kunskap om måttet, t. ex. statiskt tröskelvärde. De tillgängliga alternativen är:

- Hög – tröskelvärdena är nära och nära mått serie mönstret. En varnings regel kommer att utlösas på den minsta avvikelsen, vilket resulterar i fler aviseringar.
- Medel – mindre tätt och mer balanserade tröskelvärden, färre varningar än med hög känslighet (standard).
- Låg – tröskelvärdena kommer att lösas med mer avstånd från Mät seriens mönster. En varnings regel utlöses bara vid stora avvikelser, vilket resulterar i färre aviseringar.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Vad är inställnings alternativen "operatör" i dynamiska tröskelvärden?

Aviserings regeln för dynamiska tröskelvärden kan skapa skräddarsydda tröskelvärden utifrån mått beteendet för både övre och nedre gränser med samma varnings regel.
Du kan välja vilken avisering som ska utlösas på något av följande tre villkor:

- Större än det övre tröskelvärdet eller lägre än det nedre tröskelvärdet (standard)
- Större än det övre tröskelvärdet
- Lägre än det nedre tröskelvärdet.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Vad betyder de avancerade inställningarna i dynamiska tröskelvärden?

Om du felsöker **perioder** – dynamiska tröskelvärden gör det också möjligt att konfigurera "antal överträdelser för att utlösa aviseringen", ett minsta antal avvikelser som krävs inom ett visst tidsfönster för att systemet ska kunna generera en avisering (standardvärdet för tids perioden är fyra avvikelser om 20 minuter). Användaren kan konfigurera perioder som inte kan konfigureras och välja vad som ska aviseras om genom att ändra fönstret för misslyckade perioder och tids perioden. Den här funktionen minskar aviserings bruset som genereras av tillfälliga toppar. Exempel:

Om du vill utlösa en avisering när problemet är kontinuerligt i 20 minuter, 4 gånger i följd under en viss period grupper på 5 minuter, använder du följande inställningar:

![Misslyckade period inställningar för kontinuerliga problem i 20 minuter, 4 gånger i följd under en viss period grupperat med 5 minuter](media/alerts-dynamic-thresholds/0008.png)

Om du vill utlösa en avisering när det uppstod ett fel i ett dynamiskt tröskelvärde inom 20 minuter från de senaste 30 minuterna med en period på 5 minuter, använder du följande inställningar:

![Misslyckade period inställningar för problem i 20 minuter av de senaste 30 minuterna med period gruppering på 5 minuter](media/alerts-dynamic-thresholds/0009.png)

**Ignorera data före** – användare kan också välja att definiera ett start datum som systemet ska börja beräkna tröskelvärdena från. Ett vanligt användnings fall kan inträffa när en resurs kördes i ett test läge och nu befordras för att betjäna en produktions arbets belastning och därför bör beteendet för ett mått under test fasen ignoreras.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Hur tar du reda på varför en avisering om dynamiskt tröskelvärden utlöstes?

Du kan utforska Utlös ande aviserings instanser i vyn aviseringar genom att klicka på länken i e-postmeddelandet eller textmeddelandet eller webbläsaren för att se vyn aviseringar i Azure Portal. [Läs mer om vyn aviseringar](./alerts-overview.md#alerts-experience).

Aviserings visningen visar:

- All mått information vid den tidpunkt då aviseringen om dynamiskt tröskelvärden utlöses.
- Ett diagram över den period under vilken aviseringen utlöstes, inklusive de dynamiska tröskelvärden som användes vid den tidpunkten.
- Möjlighet att ge feedback om dynamiska tröskelvärden och aviseringar, vilket kan förbättra framtida identifieringar.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Kommer långsamma beteende ändringar i mått utlöser en avisering?

Förmodligen inte. Dynamiska tröskelvärden är bra för att upptäcka betydande avvikelser i stället för långsamma problem som utvecklas.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Hur mycket data används för att förhandsgranska och sedan beräkna tröskelvärden?

När en varnings regel först skapas, beräknas tröskelvärdena i diagrammet baserat på tillräckligt historiska data för att beräkna timmar eller dagliga säsongs mönster (10 dagar). När en varnings regel har skapats använder dynamiska tröskelvärden alla nödvändiga historiska data som är tillgängliga och kommer kontinuerligt att lära sig och anpassas baserat på nya data för att göra tröskelvärdena mer exakta. Det innebär att efter den här beräkningen visas även vecko mönster i diagrammet.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Hur mycket data behövs för att utlösa en avisering?

Om du har en ny resurs eller saknar Mät värden utlöser inte dynamiska tröskelvärden aviseringar innan tre dagar och minst 30 exempel på mått data är tillgängliga för att säkerställa korrekta tröskelvärden.
För befintliga resurser med tillräckliga mått data kan dynamiska tröskelvärden utlösa aviseringar direkt.

## <a name="dynamic-thresholds-best-practices"></a>Metod tips för dynamiska tröskelvärden

Dynamiska tröskelvärden kan tillämpas på de flesta plattformar och anpassade mått i Azure Monitor och de är också justerade för gemensamma program-och infrastruktur mått.
Följande objekt är metod tips för hur du konfigurerar aviseringar för vissa av dessa mått med hjälp av dynamiska tröskelvärden.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Dynamiska tröskelvärden för den virtuella datorns processor procents mått

1. Klicka på **övervaka** i [Azure Portal](https://portal.azure.com). Vyn övervakare konsoliderar alla övervaknings inställningar och data i en vy.

2. Klicka på **aviseringar** och klicka sedan på **+ ny varnings regel**.

    > [!TIP]
    > De flesta resurs blad innehåller **aviseringar** på resurs menyn under **övervakning**. du kan även skapa aviseringar därifrån.

3. Klicka på **Välj mål**, i kontext fönstret som läses in, väljer du en mål resurs som du vill Avisera om. Använd List rutorna **prenumeration** och **Virtual Machines resurs typ** för att hitta den resurs som du vill övervaka. Du kan också använda Sök fältet för att hitta din resurs.

4. När du har valt en mål resurs klickar du på **Lägg till villkor**.

5. Välj **CPU-procent**.

6. Alternativt kan du förfina måttet genom att justera **period** och **agg regering**. Det rekommenderas inte att använda den högsta agg regerings typen för den här mått typen eftersom den är mindre representativ för beteendet. För maximalt värde för agg regerings typ, kanske mer lämpligt.

7. Du ser ett diagram för måttet under de senaste 6 timmarna. Definiera aviserings parametrar:
    1. **Villkors typ** – Välj alternativet dynamisk.
    1. **Känslighet** – Välj medium/låg känslighet för att minska varnings bruset.
    1. **Operator** – Välj "större än" om beteendet inte representerar program användningen.
    1. **Frekvens** – Överväg att minska beroende på affärs påverkan av aviseringen.
    1. **Misslyckade perioder** (avancerat alternativ) – fönstret se tillbaka bör vara minst 15 minuter. Om till exempel perioden är inställd på fem minuter, ska perioderna för misslyckanden vara minst tre.

8. Mått diagrammet visar de beräknade tröskelvärdena baserat på senaste data.

9. Klicka på **Klar**.

10. Fyll i **aviserings information** som **aviserings regel namn**, **Beskrivning** och **allvarlighets grad**.

11. Lägg till en åtgärds grupp i aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.

12. Klicka på **Slutför** om du vill spara varnings regeln för mått.

> [!NOTE]
> Mått varnings regler som skapas via portalen skapas i samma resurs grupp som mål resursen.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Dynamiska tröskelvärden på Application Insights körnings tid för HTTP-begäran

1. Klicka på **övervaka** i [Azure Portal](https://portal.azure.com). Vyn övervakare konsoliderar alla övervaknings inställningar och data i en vy.

2. Klicka på **aviseringar** och klicka sedan på **+ ny varnings regel**.

    > [!TIP]
    > De flesta resurs blad innehåller **aviseringar** på resurs menyn under **övervakning**. du kan även skapa aviseringar därifrån.

3. Klicka på **Välj mål**, i kontext fönstret som läses in, väljer du en mål resurs som du vill Avisera om. Använd List rutorna **prenumeration** och **Application Insights resurs typ** för att hitta den resurs som du vill övervaka. Du kan också använda Sök fältet för att hitta din resurs.

4. När du har valt en mål resurs klickar du på **Lägg till villkor**.

5. Välj **körnings tid för http-begäran**.

6. Alternativt kan du förfina måttet genom att justera **period** och **agg regering**. Det rekommenderas inte att använda den högsta agg regerings typen för den här mått typen eftersom den är mindre representativ för beteendet. För maximalt värde för agg regerings typ, kanske mer lämpligt.

7. Du ser ett diagram för måttet under de senaste 6 timmarna. Definiera aviserings parametrar:
    1. **Villkors typ** – Välj alternativet dynamisk.
    1. **Operator** – Välj "större än" för att minska aviseringar som Aktiver ATS vid förbättring av varaktigheten.
    1. **Frekvens** – Överväg att minska beroende på affärs påverkan av aviseringen.

8. Mått diagrammet visar de beräknade tröskelvärdena baserat på senaste data.

9. Klicka på **Klar**.

10. Fyll i **aviserings information** som **aviserings regel namn**, **Beskrivning** och **allvarlighets grad**.

11. Lägg till en åtgärds grupp i aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.

12. Klicka på **Slutför** om du vill spara varnings regeln för mått.

> [!NOTE]
> Mått varnings regler som skapas via portalen skapas i samma resurs grupp som mål resursen.

## <a name="interpreting-dynamic-threshold-charts"></a>Tolka diagram för dynamiska tröskelvärden

Följande är ett diagram som visar ett Mät värde, dess dynamiska tröskel gränser och vissa aviseringar utlöses när värdet låg utanför de tillåtna tröskelvärdena.

![Läs mer om hur du konfigurerar mått varningar](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Använd följande information för att tolka föregående diagram.

- **Blå linje** – faktiskt uppmätt mått över tid.
- **Blått skuggat område** – visar det tillåtna intervallet för måttet. Så länge mått värdena ligger inom det här intervallet inträffar ingen avisering.
- **Blå punkter** – om du vänsterklickar på en del av diagrammet och sedan hovrar över den blå linjen visas en blå prick under markören som visar ett individuellt aggregerat mått värde.
- **Popup-fönster med blå punkt** – visar det uppmätta måttets värde (den blå punkten) och de övre och nedre värdena för tillåtet intervall.  
- **Röd punkt med en svart cirkel** – visar det första måttets värde utanför det tillåtna intervallet. Detta är värdet som utlöser en mått avisering och placerar den i ett aktivt tillstånd.
- **Röda punkter**– ange ytterligare uppmätta värden utanför det tillåtna intervallet. De kommer inte att utlösa ytterligare mått varningar, men aviseringen stannar kvar i den aktiva.
- **Rött område** – visar den tid då måttets värde var utanför det tillåtna intervallet. Aviseringen förblir i aktivt tillstånd så länge efterföljande uppmätta värden ligger utanför det tillåtna intervallet, men inga nya aviseringar utlöses.
- **Slutet av det röda utrymmet** – när den blå linjen är tillbaka i de tillåtna värdena stoppas det röda arean och den uppmätta värde linjen blir blå. Status för måttet som utlöses vid tidpunkten för den röda punkten med svart kon tur är inställd på löst.