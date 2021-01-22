---
title: Vanlig användning av kostnadsanalyser i Azure Cost Management
description: I den här artikeln beskrivs hur du hämtar resultat från vanliga kostnadsanalyser i Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 72b371fd1184c9c9e69593c5ad7e57310abf1de2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600025"
---
# <a name="common-cost-analysis-uses"></a>Vanlig användning av kostnadsanalyser

Azure Cost Management-användare vill ofta ha svar på samma frågor som många andra. Den här artikeln visar hur du hämtar resultat från vanliga kostnadsanalyser i Cost Management.

## <a name="view-forecasted-costs"></a>Visa prognostiserade kostnader

Prognostiserade kostnader visas i kostnadsanalysområden för vyerna för område och staplad kolumn. Prognosen baseras på din historiska resursanvändning. Ändringar i resursanvändningen påverkar prognostiserade kostnader.

Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.

I standardvyn innehåller det översta diagrammet de faktiska/amorterade kostnaderna och prognostiserade kostnadsavsnitt. Den färgade delen av diagrammet visar dina faktiska/amorterade kostnader. Den skuggade delen visar prognostiserade kostnader.

[![Prognostiserad kostnad](./media/cost-analysis-common-uses/enrollment-forecast.png)](./media/cost-analysis-common-uses/enrollment-forecast.png#lightbox)

## <a name="view-forecasted-costs-grouped-by-service"></a>Visa prognostiserade kostnader grupperade efter tjänst

Standardvärdet visar inte en tjänst med prognostiserade kostnader för en tjänst, så du måste lägga till ett urval att gruppera efter.

Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.

Välj **Gruppera efter** > **Tjänstnamn**.

I vyn visas dina kostnader grupperade för varje tjänst. Den prognostiserade kostnaden beräknas inte för varje tjänst. Den projiceras för dina **totala** tjänster.

[![Grupperad prognostiserad kostnad](./media/cost-analysis-common-uses/forecast-group-by-service.png)](./media/cost-analysis-common-uses/forecast-group-by-service.png#lightbox)

## <a name="view-forecasted-costs-for-a-service"></a>Visa prognostiserade kostnader för en tjänst

Du kan visa prognostiserade kostnader som är begränsade till en enda tjänst. Du kanske till exempel vill se prognostiserade kostnader för just de virtuella datorerna.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj **Lägg till filter** och sedan **Tjänstnamn**.
1. Välj en tjänst i listan **Välj**. Välj till exempel **virtuella datorer**.

Granska den faktiska kostnaden för ditt val och den prognostiserade kostnaden.

Du kan lägga till fler anpassningar i vyn.

1. Lägg till ett andra filter för **mätare** och välj ett värde att filtrera efter en enskild typ av mätare under det valda tjänstnamnet.
1. Gruppera efter **resurs** för att se de resurser som kostnaderna gäller. Den prognostiserade kostnaden beräknas inte för varje tjänst. Den projiceras för dina **totala** resurser.

[![Prognostiserade kostnader för en tjänst](./media/cost-analysis-common-uses/forecast-by-service.png)](./media/cost-analysis-common-uses/forecast-by-service.png#lightbox)

## <a name="view-your-azure-and-aws-costs-together"></a>Visa dina kostnader för Azure och AWS tillsammans  

Om du vill visa kostnader för Azure och AWS tillsammans använder du omfång för hanteringsgrupper i Azure.

1. Skapa en hanteringsgrupp eller välj en befintlig.
1. Tilldela befintliga Azure-prenumerationer som du behöver till hanteringsgruppen.
1. Tilldela *samma* hanteringsgrupp till det länkade kontot för anslutningsappen.
1. Gå till Kostnadsanalys och välj **Ackumulerade kostnader**.
1. Välj **Grupper efter** - **Leverantör**.

## <a name="view-cost-breakdown-by-azure-service"></a>Visa kostnadsuppdelning efter Azure-tjänst

Om du visar kostnaderna för en Azure-tjänst, blir det enklare att förstå vilka delar av infrastrukturen som kostar mest. Beräkningskostnaderna för virtuella datorer kan till exempel vara låga. Men du kan ändå ha avsevärda nätverkskostnader på grund av mängden information som genereras från de virtuella datorerna. Det är viktigt att förstå de primära kostnadsdrivkrafterna för dina Azure-tjänster, så att du kan anpassa tjänstanvändningen vid behov.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj **Kostnad efter tjänst** och gruppera sedan efter **Tjänstnivå**.
1. Ändra vyn till **Tabell**.

[![Kostnadsuppdelning efter Azure-tjänst](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="review-invoiced-charges-in-cost-analysis"></a>Granska fakturerade avgifter i Kostnadsanalys

Gå till kostnadsanalysen för omfattningen som är associerad med fakturan som du analyserar för att se information om den i Microsoft Azure-portalen. Välj vyn **Fakturainformation**. Fakturainformation visar avgifterna som visas på fakturan.

[![Exempel som visar fakturainformation](./media/cost-analysis-common-uses/invoice-details.png)](./media/cost-analysis-common-uses/invoice-details.png#lightbox)

När du granskar fakturainformationen kan du identifiera eventuella tjänster som har oväntade kostnader och avgöra vilka resurser som är direkt kopplade till resursen i kostnadsanalysen. Om du till exempel vill analysera avgifterna för tjänsten Virtual Machines går du till vyn **Ackumulerade kostnader**. Sedan ställer du in kornigheten på **Daglig** och filtrerar avgifterna efter **Tjänstnamn: Virtual Machines** samt grupperar avgifterna efter **Resurs**.

[![Exempel som visar ackumulerade kostnader för virtuella datorer](./media/cost-analysis-common-uses/virtual-machines.png)](./media/cost-analysis-common-uses/virtual-machines.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Visa kostnadsuppdelning efter Azure-resurs

Dina tjänster skapas med Azure-resurser. Att granska kostnader baserat på resurser kan hjälpa dig att snabbt identifiera dina primära kostnadsfaktorer. Om en tjänst har resurser som är för dyra, bör du överväga ändringar som minskar kostnaderna.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj **Kostnad efter resurs**.
1. Ändra vyn till **Tabell**.

[![Visa kostnadsuppdelning efter Azure-resurs](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Visa kostnadsuppdelning efter valda dimensioner

Med dimensionerna kan du organisera dina kostnader baserat på olika metadatavärden som visas i dina avgifter. Du kan till exempel gruppera dina kostnader efter plats.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj filtret **Gruppera efter**.  
    [![Välj ett objekt i Gruppera efter](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. Du kan även spara vyn för senare användning.
1. Klicka på ett cirkeldiagram under grafen om du vill se mer detaljerade data.  
    [![Visa kostnadsuppdelning efter valda dimensioner](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>Visa kostnader per dag eller per månad

Att titta på dagliga och månatliga kostnader kan hjälpa dig att se om det finns en tid i veckan eller under året när kostnaderna är högre. Leder det till en motsvarande ökning av dina Azure-kostnader om du har mer kundtrafik under en semesterperiod? Är fredag en dyrare dag än måndag?

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Ange **Kornighet** till **Varje månad** eller **Varje dag**.

[![Visa kostnader per dag](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>Visa dina avgifter för virtuella Spot-datorer

Virtuella Spot-datorer kan ge stora besparingar för arbetsbelastningar som kan hantera avbrott. Arbetsbelastningar körs på outnyttjad Azure-kapacitet. Eftersom de kan uteslutas när som helst får de virtuella Spot-datorerna en betydande rabatt. Använd följande steg för att visa dina kostnader för virtuella Spot-datorer.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Till exempel **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
2. Lägg till ett filter för **prismodell: Spot**.

![Exempel som visar filtret för virtuella Spot-datorer](./media/cost-analysis-common-uses/spot-vm-filter.png)

Prismodellsdimensionen används också för att visa på-begäran- och reservationsavgifter.

## <a name="view-your-reservation-charges"></a>Visa dina reservationsavgifter

Reserverade instanser är ett sätt för dig att spara pengar med Azure. Med reservationer betalar du i förskott för ett visst antal resurser över tid. Kostnadsanalyser visar kostnaderna som de visas på din faktura. Avgifterna visas som faktiska kostnader eller som avskrivningar under din reservationsperiod.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Till exempel **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Lägg till ett filter för **Prismodell: Reservation**.
1. Under **omfång** och bredvid den kostnad som visas klickar du på nedåtpilen och väljer antingen **Verklig kostnad** eller **Periodiserad kostnad** som mått.

![Välj ett kostnadsmått](./media/cost-analysis-common-uses/metric-cost.png)

Varje mått påverkar hur data visas för dina reservationsavgifter.

**Faktisk kostnad** – visar inköpet som det visas på fakturan. Om du till exempel köpte en reservation för ett år för 1200 USD i januari, så visar kostnadsanalysen en kostnad på 1200 USD i januari månad för reservationen. Den visar ingen reservationskostnad för årets övriga månader. Om du grupperar dina faktiska kostnader per virtuell dator skulle en virtuell dator som tog emot reservationsförmånen för en månad ha noll kostnader för den månaden.

**Periodiserad kostnad** – visar ett reservationsköp uppdelat som en periodiserad kostnad under reservationsperiodens varaktighet. I exemplet ovan visar kostnadsanalys en kostnad på 100 USD för varje månad under året, om du har köpt en reservation för ett år för 1200 USD i januari. Om du grupperar kostnader per virtuell dator i det här exemplet skulle du se kostnaden för varje virtuell dator som har tagit emot reservationsförmånen.

## <a name="view-your-reservation-utilization"></a>Visa reservationsförbrukningen

När du har köpt en reservation är det viktigt att spåra dess användning så att du får det du betalar för. Om du till exempel köpte 10 virtuella datorer för ett år och bara använder fem av dem, är halva köpet i stort sett bortkastat. Det finns två olika sätt att utvärdera din användning:

### <a name="view-unused-ri-costs-in-cost-analysis"></a>Visa oförbrukade RI-kostnader i kostnadsanalys

Följ stegen nedan för att identifiera vilken kostnad som för närvarande går till spillo varje månad av ditt reservationsköp.

1. Gå till Azure Portal och navigera till kostnadsanalys för omfånget där reservationen tillämpas. Till exempel **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Lägg till ett filter för **Prismodell: Reservation**.
1. Välj vyn **periodiserad kostnad**.
1. Ange Kornighet till **Månadsvis**.
1. Ange tidsperioden till det aktuella året eller din reservationsperiod.
1. Ange diagramtypen till **Kolumn (staplad)** .
1. Gruppera avgifter efter **avgiftstyp**.
1. Granska resultaten för `unusedreservation`-värden.

[![Exempel som visar reservationsanvändning](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>Visa användning i reservationer

Detaljerade anvisningar finns i [Optimera reservationanvändningen](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use).

## <a name="view-costs-for-a-specific-tag"></a>Visa kostnader för en specifik tagg

Många Azure-användare taggar sina resurser, till exempel ett kostnadsställe eller en utvecklingsmiljö (produktion och test) för att kategorisera sina kostnader på ett bättre sätt. Taggar visas som en dimension i kostnadsanalysen. Du kan använda dimensionen för att få insikter i dina anpassade taggningskategoriseringar.

Stödet för taggar gäller användning som rapporterats *efter* att taggen tillämpades på resursen. Taggar tillämpas inte retroaktivt på samlade kostnader.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj **Gruppera efter** för taggen.

[![Visa kostnader för en specifik tagg](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>Ladda ned din användningsinformation

Rapportfilen med användningsinformationen (i CSV-format) innehåller en uppdelning av alla avgifter på en faktura. Du kan använda rapporten för att jämföra den med och bättre förstå din faktura. Varje fakturerad avgift på fakturan motsvarar nedbrutna avgifter i användningsrapporten.

1. I Azure-portalen går du till fliken **Användning och avgifter** för ett faktureringskonto eller en prenumeration. Ett exempel: **Cost Management + Fakturering** > **Fakturering** > **Användning + avgifter**.
1. Välj det radobjekt som du vill ladda ned från och klicka sedan på nedladdningssymbolen.  
    [![Ladda ned användning och avgifter](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  Välj den användningsfil som ska laddas ned.  
    ![Välj en användningsfil som ska laddas ned](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Visa månatlig EA-kostnadsuppdelning

Din EA-registrering medför kostnader för hela organisationen. Att förstå hur kostnader uppstår och faktureras över tid hjälper dig att hitta lämpliga intressenter som kan säkerställa att kostnaderna hanteras på ett ansvarsfullt sätt.

Kostnaderna visas bara för din aktiva registrering. Om du överförde en registrering (inaktiv) till en ny (aktiv) visas inte kostnader för den föregående registreringen i Cost Management.


1. Gå till **Cost Management och fakturering** > **Översikt** i Azure-portalen.
1. Klicka på **Uppdelning** för den aktuella månaden så ser du detaljerad information om din Azure förskottsbetalning (kallades tidigare ekonomiskt åtagande).  
    [![Översikt över EA-kostnader – sammanfattning av uppdelning](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  Klicka på fliken **Användning och avgifter** och visa den föregående månadens uppdelning under det valda tidsintervallet.  
    [![Fliken Användning och avgifter](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>Visa registreringens månadskostnad per period

Använd en grafisk vy över registreringens månadskostnad för att förstå kostnadstrender och fakturerade belopp under en viss period.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj din registrering och ange registreringsperiod.
1. Ange kornigheten till varje månad och ställ sedan in vyn på **Kolumn     (staplad)** .

Du kan gruppera efter och filtrera dina data om du vill ha en mer detaljerad analys.

[![Månatlig registreringskostnad per period](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>Visa ackumulerade kostnader för EA-registrering

Se de nettoavgifter som har ackumulerats över tid för att förstå övergripande kostnader för din organisation under en viss period.

1. Gå till kostnadsanalysen för ditt omfång i Azure-portalen. Ett exempel: **Cost Management och fakturering** > **Cost Management** > **Kostnadsanalys**.
1. Välj din registrering och visa dina aktuella ackumulerade kostnader.

[![Ackumulerade registreringskostnader](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>Nästa steg
- Om du inte redan har slutfört den första snabbstarten för Cost Management, kan du läsa den i [Börja analysera kostnader](quick-acm-cost-analysis.md).
- Läs [Cost Management-dokumentationen](../index.yml).
