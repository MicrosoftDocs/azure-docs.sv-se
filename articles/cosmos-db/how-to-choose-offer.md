---
title: Hur du väljer mellan manuell och autoskalning på Azure Cosmos DB
description: Lär dig hur du väljer mellan standard (manuell) etablerad data flöde och autoskalning av allokerat data flöde för din arbets belastning.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: d8a6471d53ad4b2428504f9c53cbec6bc1967c49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93089649"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Välja mellan standard (manuell) och autoskalning av allokerat data flöde 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB stöder två typer eller erbjudanden för etablerade data flöden: standard (manuell) och autoskalning. Båda data flödes typerna är lämpliga för verksamhets kritiska arbets belastningar som kräver höga prestanda och skalbarhet, och som backas upp av samma Azure Cosmos DB service avtal för data flöde, tillgänglighet, svars tid och konsekvens.

I den här artikeln beskrivs hur du väljer mellan standard (manuell) och autoskalning av allokerat data flöde för din arbets belastning. 

## <a name="overview-of-provisioned-throughput-types"></a>Översikt över etablerade data flödes typer
Innan simhopp i differensen mellan standard (manuell) och autoskalning är det viktigt att först förstå hur det etablerade data flödet fungerar i Azure Cosmos DB. 

När du använder ett tillhandahållet data flöde ställer du in data flödet, mätt i enheter för programbegäran per sekund (RU/s) som krävs för din arbets belastning. Tjänsten tillhandahåller den kapacitet som krävs för att stödja data flödes kraven. Databas åtgärder mot tjänsten, till exempel läsningar, skrivningar och frågor använder en viss mängd ru: er (Request units). Läs mer om [enheter för programbegäran](request-units.md).

Följande tabell visar en jämförelse på hög nivå mellan standard (manuell) och autoskalning.

|Beskrivning|Standard (manuell)|Automatisk skalning|
|-------------|------|-------|
|Passar bäst för|Arbets belastningar med konstant eller förutsägbar trafik|Arbets belastningar med varierande eller oförutsägbar trafik. Se [användnings fall för autoskalning](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Så här fungerar det|Du etablerar en angiven mängd RU/s `T` som är statisk över tid, om du inte ändrar dem manuellt. Varje sekund kan du använda upp till `T` ru/s-dataflöde. <br/><br/>Om du till exempel anger standard (manuell) 400 RU/s, stannar data flödet på 400 RU/s.|Du anger högsta eller högsta RU/s `Tmax` som du inte vill att systemet ska överskrida. Systemet skalar automatiskt genom strömningen `T` , till exempel `0.1* Tmax <= T <= Tmax` . <br/><br/>Om du till exempel ställer in maximalt antal RU/s av 4000 RU/s, kommer systemet att skalas mellan 400-4000 RU/s.|
|När du ska använda detta|Du vill hantera din data flödes kapacitet manuellt (RU/s) och skala dig själv.<br/><br/>Du har hög, konsekvent användning av etablerade RU/s. För alla timmar under en månad, om du ställer in etablerade RU/s `T` och använder hela beloppet för 66% av timmarna eller mer, beräknas du att spara med standard (manuellt) etablerade ru/s.<br/><br/>Detta baseras på en jämförelse mellan inställningen `T` i standard (manuell) och samma mängd `Tmax` i autoskalning. |Du vill Azure Cosmos DB hantera din data flödes kapacitet (RU/s) och skala utifrån användning.<br/><br/>Du har RU/s-användning som är variabel eller svår att förutsäga. För alla timmar under en månad, om du ställer in högsta antal RU/s `Tmax` och använder hela beloppet `Tmax` för 66% av timmarna eller mindre, beräknas du att spara med autoskalning.<br/><br/>Detta baseras på en jämförelse mellan att ställa in autoskalning `Tmax` och samma belopp `T` i standard (manuell) data flöde.|
|Faktureringsmodell|Faktureringen görs per timme för RU/s etablerad, oavsett hur många ru: er som för bruk ATS.<br/><br/>Exempel: <li>Etablera 400 RU/s</li><li>Timme 1: inga förfrågningar</li><li>Timme 2:400 RU/s värd för begär Anden</li><br/><br/>För både timmar 1 och 2 faktureras du 400 RU/s för båda timmarna enligt [standard priserna (manuell)](https://azure.microsoft.com/pricing/details/cosmos-db/).|Faktureringen görs per timme för de högsta RU/s-systemet skalas till i timmen. <br/><br/>Exempel: <li>Etablera autoskalning Max RU/s av 4000 RU/s (skalar mellan 400-4000 RU/s)</li><li>Timme 1: systemet skalas upp till det högsta värdet på 3500 RU/s</li><li>Timme 2: systemet skalas ned till minst 400 RU/s (alltid 10% av `Tmax` ), på grund av ingen användning</li><br/><br/>Du debiteras för 3500 RU/s på timme 1 och 400 RU/s på timme 2 vid de [kostnader för autoskalning av allokerade data flöden](https://azure.microsoft.com/pricing/details/cosmos-db/). Autoskalning per RU/s är 1,5 * standard priset (manuell).
|Vad händer om du överskrider etablerade RU/s|RU/s förblir statisk vid vad som är etablerad. Alla begär Anden som förbrukar sig bortom den etablerade ru: er i en sekund kommer att begränsas, med ett svar som rekommenderar en tid att vänta innan den försöker igen. Du kan manuellt öka eller minska RU/s vid behov.| Systemet skalar RU/s upp till den Autoskalade Max RU/s. Alla begär Anden som förbrukar sig utanför den automatiska skalningen RU/s i en sekund kommer att begränsas med ett svar som rekommenderar en tid att vänta innan den försöker igen.|

## <a name="understand-your-traffic-patterns"></a>Förstå dina trafik mönster

### <a name="new-applications"></a>Nya program ###

Om du skapar ett nytt program och inte känner till ditt trafik mönster än, kanske du vill starta vid start punkten RU/s (eller minst RU/s) för att undvika överetablering i början. Eller, om du har ett litet program som inte behöver hög skala, kanske du vill etablera bara den minsta start punkten RU/s för att optimera kostnaderna. För små program med låg förväntade trafik kan du också ta hänsyn till [Server](throughput-serverless.md) utan kapacitets läge.

Oavsett om du planerar att använda standard (manuell) eller autoskalning bör du tänka på följande:

Om du etablerar standard (manuell) RU/s vid start punkten 400 RU/s, kommer du inte att kunna använda över 400 RU/s, om du inte manuellt ändrar data flödet. Du debiteras för 400 RU/s vid standard (manuell) allokerad data flödes hastighet, per timme.

Om du etablerar autoskalning genom strömning vid start punkten för max RU/s av 4000 RU/s, kommer resursen att skalas mellan 400 och 4000 RU/s. Eftersom den automatiska skalningen av data flödet per RU/s är 1,5 x av standard priset (manuell), i timmar där systemet har skalats ned till minst 400 RU/s, kommer fakturan att vara högre än om du etablerade 400 RU/s manuellt. Men med autoskalning kan du när som helst använda upp till 4000 RU/s utan någon användar åtgärd. I allmänhet bör du väga fördelarna med att kunna förbruka upp till max. RU/s när som helst med 1,5 x-frekvensen för autoskalning.

Använd Azure Cosmos DB [kapacitets kalkylatorn](estimate-ru-with-capacity-planner.md) för att beräkna dina data flödes krav. 

### <a name="existing-applications"></a>Befintliga program ###

Om du har ett befintligt program med standard (manuellt) tillhandahållet data flöde kan du använda [Azure Monitor mått](../azure-monitor/insights/cosmosdb-insights-overview.md) för att avgöra om ditt trafik mönster är lämpligt för autoskalning. 

Börja med att hitta den [normaliserade förbruknings måttet för begär ande enheter](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) för din databas eller behållare. Normaliserad användning är ett mått på hur mycket du för närvarande använder ditt standard (manuellt) allokerade data flöde. Närmare talet är 100%, desto mer är du helt använder dina etablerade RU/s. [Läs mer](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) om måttet.

Sedan fastställer du hur den normaliserade användningen varierar över tid. Hitta den högsta normaliserade användningen för varje timme. Beräkna sedan den genomsnittliga normaliserade användningen för alla timmar. Om du ser att den genomsnittliga användningen är lägre än 66 % kan du överväga att aktivera autoskalning för databasen eller containern. Om den genomsnittliga användningen å andra sidan är högre än 66 % rekommenderar vi att du fortsätter med manuellt etablerat dataflöde.

> [!TIP]
> Om ditt konto har kon figurer ATS för att använda flera regioner och har mer än en region, är priset per 100 RU/s detsamma för både manuell och autoskalning. Det innebär att aktivering av autoskalning medför ingen ytterligare kostnad oavsett användning. Därför rekommenderar vi alltid att du använder autoskalning med flera regioner när du har mer än en region, så att du kan dra nytta av besparingarna från att bara betala för RU/s ditt program skalas till. Om du har skrivningar i flera regioner och en region använder du den genomsnittliga användningen för att avgöra om autoskalning leder till kostnads besparingar. 

#### <a name="examples"></a>Exempel

Låt oss ta en titt på två olika exempel arbets belastningar och analysera om de är lämpliga för manuell eller automatisk skalning av data flöde. För att illustrera den allmänna metoden analyserar vi tre timmars historik för att fastställa kostnads skillnaden mellan att använda manuell och autoskalning. För produktions arbets belastningar rekommenderar vi att du använder 7 till 30 dagars historik (eller längre om tillgängligt) för att upprätta ett mönster för RU/s-användning.

> [!NOTE]
> Alla exempel som visas i det här dokumentet baseras på priset för ett Azure Cosmos-konto som distribueras i en icke-myndighets region i USA. Prissättningen och beräkningen varierar beroende på vilken region du använder, se [sidan Azure Cosmos DB prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/) för den senaste pris informationen.

Antaganden:
- Anta att vi för närvarande har manuellt genomflöde av 30 000 RU/s. 
- Vår region har kon figurer ATS med en enda regions skrivning, med en region. Om vi hade flera regioner skulle vi multiplicera Tim kostnaden med antalet regioner.
- Använd priser för offentliga priser för manuell ($0,008 USD per 100 RU/s per timme) och autoskalning av data flöde ($0,012 USD per 100 RU/s per timme) i ett konto med en regions skrivning. Mer information finns på [sidan med pris](https://azure.microsoft.com/pricing/details/cosmos-db/) information. 

#### <a name="example-1-variable-workload-autoscale-recommended"></a>Exempel 1: variabel arbets belastning (Autoskala rekommenderas)

Först tittar vi på den normaliserade RU-förbrukningen. Den här arbets belastningen har varierande trafik, med normaliserad RU-förbrukning mellan 6 och 100%. Det finns tillfälliga toppar till 100% som är svåra att förutsäga, men många timmar med låg belastning. 

:::image type="content" source="media/how-to-choose-offer/variable-workload_use_autoscale.png" alt-text="Arbets belastning med variabel trafik – normaliserad RU-förbrukning mellan 6% och 100% för alla timmar":::

Nu ska vi jämföra kostnaden för etablering av 30 000 RU/s manuellt data flöde, jämfört med att ange max. RU/s till 30 000 (skalas mellan 3000-30 000 RU/s). 

Nu ska vi analysera historiken. Anta att vi har den användning som beskrivs i följande tabell. Den genomsnittliga användningen för dessa tre timmar är 39%. Eftersom den normaliserade RU-förbrukningen är mindre än 66% sparar vi med hjälp av autoskalning. 

Observera att när det finns 6% användning i timmen 1 faktureras autoskalning för RU/s för 10% av Max RU/s, vilket är minimivärdet per timme. Även om kostnaden för autoskalning kan vara högre än manuellt genom strömning under vissa timmar, blir den automatiska skalningen billigare övergripande, förutsatt att den genomsnittliga användningen är mindre än 66% över hela tiden.

|Tidsperiod  | Användning |Fakturerad autoskalning av RU/s  |Alternativ 1: manuell 30 000 RU/s  | Alternativ 2: autoskalning mellan 3000-30 000 RU/s |
|---------|---------|---------|---------|---------|
|Timme 1  | 6 %  |     3000  |  30 000 * 0,008/100 = $2,40        |   3000 * 0,012/100 = $0,36      |
|Timme 2  | 100 %  |     30 000    |  30 000 * 0,008/100 = $2,40       |  30 000 * 0,012/100 = $3,60      |
|Timme 3 |  11.3  |     3300    |  30 000 * 0,008/100 = $2,40       |    3300 * 0,012/100 = $0,40     |
|**Totalt**   |  |        |  $7,20       |    $4,36 (39% besparingar)    |

#### <a name="example-2-steady-workload-manual-throughput-recommended"></a>Exempel 2: stabil arbets belastning (manuellt data flöde rekommenderas)

Den här arbets belastningen har stadig trafik, med normaliserad RU-förbrukning mellan 72% och 100%. Med 30 000 RU/s etablerad innebär det att vi tar mellan 21 600 och 30 000 RU/s.

:::image type="content" source="media/how-to-choose-offer/steady_workload_use_manual_throughput.png" alt-text="Arbets belastning med konstant trafik – normaliserad RU-förbrukning mellan 72% och 100% för alla timmar":::

Nu ska vi jämföra kostnaden för etablering av 30 000 RU/s manuellt data flöde, jämfört med att ange max. RU/s till 30 000 (skalas mellan 3000-30 000 RU/s).

Anta att vi har användnings historiken enligt beskrivningen i tabellen. Den genomsnittliga användningen för dessa tre timmar är 88%. Eftersom den normaliserade RU-förbrukningen är större än 66% sparar vi med hjälp av manuellt data flöde.

I allmänhet kommer vi att spara med hjälp av manuellt data flöde om den genomsnittliga användningen för alla 730 timmar i en månad är större än 66%. 

| Tidsperiod | Användning |Fakturerad autoskalning av RU/s  |Alternativ 1: manuell 30 000 RU/s  | Alternativ 2: autoskalning mellan 3000-30 000 RU/s |
|---------|---------|---------|---------|---------|
|Timme 1  | 72%  |     21 600   |  30 000 * 0,008/100 = $2,40        |   21600 * 0,012/100 = $2,59      |
|Timme 2  | 93 %  |     28 000    |  30 000 * 0,008/100 = $2,40       |  28 000 * 0,012/100 = $3,36       |
|Timme 3 |  100 %  |     30 000    |  30 000 * 0,008/100 = $2,40       |    30 000 * 0,012/100 = $3,60     |
|**Totalt**   |  |        |  $7,20       |    $9,55     |

> [!TIP]
> Med standard-genomflödet (manuell) kan du använda det normaliserade användnings måttet för att beräkna de faktiska RU/s som du kan använda om du växlar till autoskalning. Multiplicera den normaliserade användningen vid en tidpunkt genom den aktuella etablerade standarden (manuell) RU/s. Om du till exempel har etablerade 5000 RU/s och den normaliserade användningen är 90% är RU/s-användningen 0,9 * 5000 = 4500 RU/s. Om du ser att ditt trafik mönster är variabelt, men du är över eller under etablerad, kanske du vill aktivera autoskalning och sedan ändra inställningen för autoskalning Max RU/s.

#### <a name="how-to-calculate-average-utilization"></a>Beräkna genomsnittlig användning
Autoskalning av fakturor för flest RU/s skalas till i en timme. När du analyserar normaliserad RU-förbrukning över tid är det viktigt att använda den högsta användningen per timme när du beräknar genomsnittet. 

Beräkna medelvärdet för den högsta användningen för alla timmar:
1. Ställ in **agg regeringen** på Noramlized ru-förbruknings måttet till **Max**.
1. Välj **tids kornig het** till 1 timme.
1. Navigera till **diagram alternativ**.
1. Välj alternativet stapeldiagram. 
1. Under **dela** väljer du alternativet **Ladda ned till Excel** . Beräkna den genomsnittliga användningen för alla timmar från det genererade kalkyl bladet. 

:::image type="content" source="media/how-to-choose-offer/variable-workload-highest-util-by-hour.png" alt-text="Om du vill se normaliserad RU-förbrukning per timme, 1) väljer du tids kornig het till 1 timme. 2) redigera diagram inställningar; 3) Välj stapeldiagram, alternativ; 4) under dela väljer du alternativet Ladda ned till Excel för att beräkna genomsnitt för alla timmar. ":::

## <a name="measure-and-monitor-your-usage"></a>Mäta och övervaka din användning
Med tiden bör du övervaka ditt program och göra justeringar efter behov när du har valt typ av data flöde. 

När du använder autoskalning använder du Azure Monitor för att se den allokerade autoskalning Max RU/s (**autoskalning Max-genomflöde**) och ru/s-systemet skalas för närvarande till (**etablerings data flöde**). Nedan visas ett exempel på en variabel eller oförutsägbar arbets belastning med hjälp av autoskalning. Observera att när det inte finns någon trafik skalar systemet RU/s till minst 10% av Max RU/s, som i det här fallet är 5000 RU/s respektive 50 000 RU/s respektive. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Exempel på arbets belastning med autoskalning med autoskalning Max RU/s av 50 000 RU/s och data flöde mellan 5000-50 000 RU/s":::

> [!NOTE]
> När du använder standard (manuellt) tillhandahållet data flöde, refererar det **tillhandahållna data flödes** måttet till det som användaren har angett. När du använder autoskalning av data flöde refererar detta mått till RU/s-systemet är för närvarande skalat till.

## <a name="next-steps"></a>Nästa steg
* Använd [ru-kalkylatorn](https://cosmos.azure.com/capacitycalculator/) för att beräkna data flödet för nya arbets belastningar.
* Använd [Azure Monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) för att övervaka dina befintliga arbets belastningar.
* Lär dig hur du [etablerar autoskalning genom strömning på en Azure Cosmos-databas eller-behållare](how-to-provision-autoscale-throughput.md).
* Läs [vanliga frågor och svar om autoskalning](autoscale-faq.md).