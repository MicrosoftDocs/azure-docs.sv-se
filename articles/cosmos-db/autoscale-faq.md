---
title: Vanliga frågor om autoskalning av allokerat data flöde i Azure Cosmos DB
description: Vanliga frågor om autoskalning av allokerat data flöde för Azure Cosmos DB databaser och behållare
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/10/2020
ms.openlocfilehash: b398f739189232f39a2fee06fc6e6ff0d53348f0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656623"
---

# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Vanliga frågor om autoskalning av allokerat data flöde i Azure Cosmos DB

Med autoskalning av allokerat dataflöde kommer Azure Cosmos DB automatiskt att hantera och skala RU/s för din databas eller behållare baserat på användning. Den här artikeln besvarar vanliga frågor om autoskalning.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Vad är skillnaden mellan "autopilot" och "autoskalning" i Azure Cosmos DB?

"Autoskalning" eller "autoskalning av allokerat dataflöde" är det uppdaterade namnet för funktionen, tidigare kallat "autopilot". Med den aktuella versionen av AutoScale har vi lagt till nya funktioner, inklusive möjligheten att ange anpassade maximala RU/s-och programmässig support.

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>Vad händer med databaser eller behållare som skapats i föregående modell för autopilot-nivån?

Resurser som har skapats med den tidigare nivåmodellen stöds automatiskt med den nya anpassade Max-modellen RU/s. Den övre gränsen för nivån blir den nya maximala RU/s, vilket resulterar i samma skalningsintervall.

Om du till exempel tidigare valde nivån som skalades mellan 400 och 4000 RU/s visas nu databasen eller behållaren som har högst RU/s av 4000 RU/s, som skalar mellan 400 och 4000 RU/s. Härifrån kan du ändra det maximala antalet RU/s till ett anpassat värde som passar din arbetsbelastning.

### <a name="how-quickly-will-autoscale-scale-up-and-down-based-on-spikes-in-traffic"></a>Hur snabbt skalas dataflödet upp och ned automatiskt baserat på toppar i trafik?

Med autoskalning skalar systemet dataflödet (RU/s) `T` uppåt eller nedåt inom `0.1 * Tmax` `Tmax` intervallet och baserat på inkommande trafik. Eftersom skalningen är automatisk och momentan kan du när som helst använda upp till etableringen `Tmax` utan fördröjning.

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>Hur avgör jag vilken nivå RU/s är satt till för ett befintligt system?

Använd [Azure Monitor mått](how-to-choose-offer.md#measure-and-monitor-your-usage) för att övervaka både den allokerade autoskalning Max ru/s och det aktuella dataflödet (ru/s) som systemet skalas till.

### <a name="what-is-the-pricing-for-autoscale"></a>Vad är prissättningen för autoskalning?

Varje timme debiteras du för det högsta dataflödet som `T` systemet skalas till inom timmen. Om din resurs inte hade några förfrågningar under aktuell timme eller inte skalats längre, kommer du att faktureras för minimum av `0.1 * Tmax`. Mer information finns på [sidan](https://azure.microsoft.com/pricing/details/cosmos-db/) med Azure Cosmos DB priser.

### <a name="how-does-autoscale-show-up-on-my-bill"></a>Hur visas autoskalning på min faktura?

I ett enkelt huvudkonto är den automatiska skalningshastigheten per 100 RU/s 1,5 x frekvensen standard (manuellt) allokerat dataflöde. På din faktura visas den befintliga standard konfigurationen för dataflödesmätaren. Antalet för den här mätaren multipliceras med 1,5. Om till exempel det högsta RU/s-systemet skalas till inom en timme var 6000 RU/s faktureras du 60 \* 1,5 = 90 enheter för mätaren för den timmen.

I flera huvudkonton är autoskalning-priset per 100 RU/s samma som priset för standard (manuellt) allokerat multi-master-genomflöde. På din faktura visas den befintliga multi-master-mätaren. Eftersom priserna är desamma, kan du se samma kvantitet som med standard-dataflödet om du använder autoskalning.

### <a name="does-autoscale-work-with-reserved-capacity"></a>Skalar jag ut arbetet med reserverad kapacitet?

Ja. När du köper en reserverad primär kapacitet, tillämpas reservations rabatten för autoskalning av resurser i mätar användningen enligt förhållandet 1,5 \* [förhållandet för den angivna regionen](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region).

En reserverad kapacitet med flera överordnade fungerar på samma sätt för autoskalning och standard (manuellt) allokerat dataflöde. Se [Azure Cosmos DB reserverad kapacitet](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>Fungerar autoskalning med den kostnadsfria nivån?

Ja. På den kostnadsfria nivån kan du använda autoskalning genom strömning på en behållare. Stöd för autoskalning av delade dataflödes-databaser med anpassade Max RU/s är ännu inte tillgängligt. Se hur [fakturering på kostnads fri nivå fungerar med autoskalning](understand-your-bill.md#billing-examples-with-free-tier-accounts).

### <a name="is-autoscale-supported-for-all-apis"></a>Stöds autoskalning för alla API: er?

Ja, autoskalning stöds för alla API: er: Core (SQL), Gremlin, Table, Cassandra och API för MongoDB.

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>Stöds autoskalning för flera huvudkonton?

Ja. Det högsta antalet RU/s är tillgängliga i varje region som läggs till i Azure Cosmos DB-kontot.

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>Hur gör jag för att aktivera autoskalning på nya databaser eller behållare?

Se den här artikeln om [hur du aktiverar autoskalning](how-to-provision-autoscale-throughput.md).

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>Kan jag aktivera autoskalning på en befintlig databas eller behållare?

Ja. Du kan också växla mellan autoskalning och standard (manuell) allokerad data flöde vid behov. För närvarande kan du endast använda [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) för att utföra dessa åtgärder för alla API: er.

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>Hur fungerar migreringen mellan autoskalning och standard (manuell) allokerat data flöde?

Ett begrepp är att ändra dataflödes typen är en process i två steg. Först skickar du en begäran om att ändra data flödes inställningarna för att använda antingen autoskalning eller manuellt allokerat dataflöde. I båda fallen bestämmer systemet automatiskt och ställer in ett initialt RU/s-värde, baserat på de aktuella dataflödes inställningarna och lagringen. Under det här steget godkänns inget användardefinierat RU/s-värde. När uppdateringen har slutförts kan du [ändra ru/s](#can-i-change-the-max-rus-on-the-database-or-container) så att den passar din arbetsbelastning.

**Migrering från standard (manuellt) allokerat dataflöde till autoskalning**

För en behållare använder du följande formel för att beräkna den inledande autoskalning Max RU/s: `MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)` , avrundat till närmaste 1000 ru/s. Den faktiska inledande autoskalning Max RU/s kan variera beroende på din kontokonfiguration.

Exempel #1: anta att du har en behållare med 10 000 RU/s manuellt tillhandahållet data flöde och 25 GB lagringsutrymme. När du aktiverar autoskalning är den ursprungliga autoskalning Max ru/s: 10 000 ru/s, som kommer att skalas mellan 1000-10 000 ru/s.

Exempel #2: anta att du har en behållare med 50 000 RU/s manuellt tillhandahållet data flöde och 2500 GB lagringsutrymme. När du aktiverar autoskalning är den ursprungliga autoskalning Max ru/s: 250 000 ru/s, som kommer att skalas mellan 25 000-250 000 ru/s.

**Migrering från Autoskala till standard (manuellt) allokerat data flöde**

Det första manuella allokerade dataflödet kommer att vara detsamma som för den aktuella AutoScale-Maxen RU/s.

Exempel: anta att du har en autoskalning-databas eller container med max RU/s av 20 000 RU/s (skalas mellan 2000-20 000 RU/s). När du uppdaterar för att använda manuellt tillhandahållet dataflöde blir det första dataflödet 20 000 RU/s.

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>Finns det Azure-CLI eller PowerShell-stöd för att hantera databaser eller behållare med autoskalning?

För närvarande kan du bara skapa och hantera resurser med autoskalning från Azure Portal, .NET v3 SDK, Java v4 SDK och Azure Resource Manager. Stöd i Azure CLI, PowerShell och andra SDK: er är ännu inte tillgängligt.

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>Stöds autoskalning för delade dataflödes-databaser?

Ja, autoskalning stöds för delade dataflödes-databaser. Om du vill aktivera den här funktionen väljer du autoskalning och alternativet **etablera data flöde** när du skapar databasen.

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>Vad är antalet tillåtna behållare per delad dataflödes-databas när autoskalning är aktiverat?

Azure Cosmos DB använder högst 25 behållare i en delad dataflödes-databas som gäller för databaser med autoskalning eller standard (manuell) dataflöde.

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Vad är lagringsgränsen kopplad till varje Max RU/s-alternativ?

Lagringsgränsen i GB för varje högsta RU/s är: Max RU/s av databas eller container/100. Om t. ex. Max RU/s är 20 000 RU/s kan resursen stödja 200 GB lagrings utrymme. Se artikeln om [begränsningar för autoskalning](provision-throughput-autoscale.md#autoscale-limits) för de tillgängliga Max ru/s-och lagrings alternativen.

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Vad händer om jag överskrider lagrings gränsen som är kopplad till mitt maximala data flöde?

Om lagringsgränsen som är kopplad till databasens eller behållarens maximala dataflöde överskrids, ökar Azure Cosmos DB automatiskt det maximala data flödet till näst högsta RU/s som kan stödja lagringsnivån.

Om du till exempel börjar med en högsta RU/s av 50 000 RU/s (skalas mellan 5000-50 000 RU/s) kan du lagra upp till 500 GB data. Om du överskrider 500 GB, t.ex. lagringsutrymmet är 600 GB, blir de nya maximalt RU/s 60 000 RU/s (skalas mellan 6000-60 000 RU/s).

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>Kan jag ändra Max RU/s på databasen eller behållaren?

Ja. Se den här [artikeln](how-to-provision-autoscale-throughput.md) om hur du ändrar Max ru/s. När du ändrar Max RU/s, beroende på det begärda värdet, kan detta vara en asynkron åtgärd som kan ta lite tid att slutföra (kan vara upp till 4-6 timmar beroende på vald RU/s)

#### <a name="increasing-the-max-rus"></a>Ökar max. RU/s

När du skickar en begäran om att öka Max antalet RU/s `Tmax`, beroende på antalet ru/s har valts, etablerar tjänsten fler resurser för att stödja högsta antalet ru/s. Även om detta sker påverkas inte din befintliga arbetsbelastning och dina åtgärder. Systemet fortsätter att skala din databas eller behållare mellan föregående `0.1*Tmax` till `Tmax` tills det nya skalnings intervallet för `0.1*Tmax_new` `Tmax_new` är klart.

#### <a name="lowering-the-max-rus"></a>Sänker Max RU/s

När du sänker Max RU/s-värdet kan du ange det som: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`, avrundat till närmaste 1000 ru/s.

Exempel #1: anta att du har en behållare för autoskalning med högst RU/s av 20 000 RU/s (skalar mellan 2000-20 000 RU/s) och 50 GB lagrings utrymme. Det lägsta värdet som du kan ange max RU/s till är: MAX (4000, 20 000/10, **50 \* 100**) = 5000 ru/s (skalas mellan 500-5000 ru/s).

Exempel #2: anta att du har en behållare för autoskalning med högst RU/s av 100 000 RU/s och 100 GB lagrings utrymme. Nu skalar du Max RU/s till 150 000 RU/s (skalar mellan 15 000-150 000 RU/s). Det lägsta, lägsta värdet du kan nu ange max RU/s till: MAX (4000, **150 000/10**, 100 \* 100) = 15 000 ru/s (skalas mellan 1500-15 000 ru/s).

När du sänker Max-värdet RU/s för en delad dataflödes-databas kan du ange det som: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100, 4000 + (MAX(Container count - 25, 0) * 1000))`, avrundat till närmaste 1000 ru/s.

Ovanstående formler och exempel relaterar till den minsta autoskalning Max RU/s som du kan ange och skiljer sig från `0.1 * Tmax` området till, `Tmax` vilket skalar systemet automatiskt mellan. Oavsett vad som är Max RU/s, kommer systemet alltid att skalas mellan `0.1 * Tmax` till `Tmax` .

### <a name="how-does-ttl-work-with-autoscale"></a>Hur fungerar TTL med autoskalning?

Med automatisk skalning påverkar TTL-åtgärderna inte skalningen av RU/s. Alla ru:er som förbrukas på grund av TTL ingår inte i de fakturerade RU/s i den automatiska skalningsbehållaren.

Anta till exempel att du har en behållare för autoskalning med 400 – 4000 RU/s:

- Timme 1: T = 0: containern har ingen användning (inga TTL-eller arbets belastnings begär Anden). Fakturerbara RU/s är 400 RU/s.
- Timme 1: T = 1: TTL har Aktiver ATS.
- Timme 1: T = 2: behållaren börjar hämta begäranden, som förbrukar 1000 RU i 1 sekund. Det finns också 200 ru:er för TTL som måste utföras. Fakturerbara RU/s är fortfarande 1000 RU/s. Oavsett när TTLs inträffar kommer de inte att påverka logiken för automatisk skalning.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Vad är mappningen mellan de högsta RU/s och fysiska partitionerna?

Första gången du väljer Max RU/s är Azure Cosmos DB att etablera: Max RU/s/10 000 RU/s = antal fysiska partitioner. Varje [fysisk partition](partition-data.md#physical-partitions) har stöd för upp till 10 000 ru/s och 50 GB lagrings utrymme. När lagrings storleken ökar, delar Azure Cosmos DB automatiskt partitionerna för att lägga till fler fysiska partitioner för att hantera lagringsökningen, eller öka antalet RU/s om lagringen [överskrider den associerade gränsen](#what-is-the-storage-limit-associated-with-each-max-rus-option).

Max RU/s för databasen eller containern är jämnt fördelad över alla fysiska partitioner. Det totala dataflödet som en enskild fysisk partition kan skalas till är alltså: Max RU/s av databas eller behållare/# fysiska partitioner.

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Vad händer om inkommande begäranden överskrider max RU/s av databasen eller behållaren?

Om den totala förbrukade RU/s överskrider max RU/s av databasen eller behållaren, begränsas begäranden som överstiger antalet RU/s och returnerar en status kod för 429. Begäranden som resulterar i över 100% normaliserad användning kommer också att begränsas. Normaliserad användning definieras som Max för RU/s-användningen för alla fysiska partitioner. Anta till exempel att ditt maximala dataflöde är 20 000 RU/s och att du har två fysiska partitioner, P_1 och P_2, som var och en kan skalas till 10 000 RU/s. Om P_1 har använt 6000 ru: er och P_2 8000 ru: er är den normaliserade användningen MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

> [!NOTE]
> Azure Cosmos DB klient-SDK:er och dataimport-verktyg (Azure Data Factory, Mass utförar-bibliotek) försöker automatiskt igen på 429s, så tillfälliga 429s är okey. Ett varaktigt stort antal 429s kan tyda på att du behöver öka Max RU/s eller granska din partitioneringsstrategi för en [aktiv partition](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a>Är det fortfarande möjligt att se 429s (begränsning/hastighetsbegränsning) när autoskalning är aktiverat?

Ja. Det är möjligt att se 429s i två scenarier. Först när den totala förbrukade RU/s överskrider max RU/s av databasen eller behållaren, kommer tjänsten att begränsa begäranden enligt detta.

För det andra, om det finns en aktiv partition, d.v.s. ett nyckelvärde för logisk partition som har en oproportionerligt högre mängd begäranden jämfört med andra nyckelvärden, är det möjligt att den underliggande fysiska partitionen överskrider dess RU/s-budget. Som bästa praxis bör du [välja en bra partitionsnyckel](partitioning-overview.md#choose-partitionkey) som resulterar i en jämn fördelning av både lagring och dataflöde.

Om du till exempel väljer alternativet 20 000 RU/s max dataflöde och har 200 GB lagringsutrymme, med fyra fysiska partitioner, kan varje fysisk partition skalas upp till 5000 RU/s. Om det fanns en hot partition på en viss logisk partitionsnyckel visas 429s när den underliggande fysiska partitionen som den finns på överskrider 5000 RU/s, dvs. överskrider 100% normaliserad användning.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [aktiverar autoskalning på en Azure Cosmos DB databas eller behållare](how-to-provision-autoscale-throughput.md).
- Lär dig mer om [fördelarna med etablerad data flöde med autoskalning](provision-throughput-autoscale.md#benefits-of-autoscale).
- Läs mer om [logiska och fysiska partitioner](partition-data.md).
