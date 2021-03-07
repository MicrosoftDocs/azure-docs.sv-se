---
title: Uppskatta kostnader med hjälp av Azure Cosmos DB Capacity Planner
description: Med Azure Cosmos DB kapacitets planeraren kan du uppskatta det data flöde (RU/s) som krävs och kostnaden för din arbets belastning. Den här artikeln beskriver hur du använder den nya versionen av kapacitets planeraren för att beräkna det data flöde och den kostnad som krävs.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: 829fb28577fbb89c031a3b972d3ee5e04c935e9f
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435009"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Beräkna RU/s med hjälp av Azure Cosmos DB Capacity Planner
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Det är viktigt att konfigurera dina Azure Cosmos-databaser och behållare med rätt mängd etablerade data flöde eller [enheter för programbegäran (ru/s)](request-units.md)för din arbets belastning för att optimera kostnader och prestanda. Den här artikeln beskriver hur du använder Azure Cosmos DB [Capacity Planner](https://cosmos.azure.com/capacitycalculator/) för att få en uppskattning av de nödvändiga ru/s och kostnaden för din arbets belastning. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Beräkna data flöde och kostnad med Azure Cosmos DB kapacitets planerare

Kapacitets planeraren kan användas i två lägen.

|**Läge**  |**Beskrivning**  |
|---------|---------|
|Grundläggande|Ger en snabb, hög nivå av RU/s och kostnads uppskattning. Det här läget förutsätter standardinställningarna Azure Cosmos DB inställningar för indexerings princip, konsekvens och andra parametrar. <br/><br/>Använd Basic-läget för en snabb och uppskattning på hög nivå när du utvärderar en potentiell arbets belastning att köras på Azure Cosmos DB.|
|Avancerat|Innehåller en mer detaljerad RU/s-och kostnads uppskattning med möjlighet att justera ytterligare inställningar – indexerings princip, konsekvens nivå och andra parametrar som påverkar kostnaden och data flödet. <br/><br/>Använd avancerat läge när du uppskattar RU/s för ett nytt projekt eller vill ha en mer detaljerad uppskattning. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Beräkna tillhandahållet data flöde och kostnad med Basic-läge
Om du vill få en snabb uppskattning för din arbets belastning med hjälp av Basic-läget går du till [kapacitets planeraren](https://cosmos.azure.com/capacitycalculator/). Ange följande parametrar utifrån din arbets belastning: 

|**Indata**  |**Beskrivning**  |
|---------|---------|
|Antal regioner|Azure Cosmos DB är tillgängligt i alla Azure-regioner. Välj antalet regioner som krävs för din arbets belastning. Du kan associera ett valfritt antal regioner med ditt Cosmos-konto. Mer information finns i [global distribution](distribute-data-globally.md) i Azure Cosmos dB.|
|Skrivningar för flera regioner|Om du aktiverar [flera region skrivningar](distribute-data-globally.md#key-benefits-of-global-distribution)kan ditt program läsa och skriva till alla Azure-regioner. Om du inaktiverar skrivningar i flera regioner kan ditt program skriva data till en enda region. <br/><br/> Aktivera flera regioner om du förväntar dig att ha en aktiv-aktiv arbets belastning som kräver skrivningar med låg latens i olika regioner. Till exempel en IOT-arbetsbelastning som skriver data till databasen på stora volymer i olika regioner. <br/><br/> Skrivningar i flera regioner garanterar 99,999% Läs-och skriv tillgänglighet. Skrivningar i flera regioner kräver mer data flöde jämfört med de enkla Skriv regionerna. Mer information finns i [hur ru: er skiljer sig åt i en artikel med en och flera Skriv regioner](optimize-cost-regions.md) .|
|Totalt antal lagrade data (per region)|Totalt antal beräknade data som lagras i GB i en enda region.|
|Objektstorlek|Den uppskattade storleken för dataobjektet (t. ex. dokument) mellan 1 KB och 2 MB. |
|Läsningar/SEK per region|Antalet läsningar som förväntas per sekund. |
|Skrivningar/s per region|Antal förväntade skrivningar per sekund. |

När du har fyllt i de nödvändiga uppgifterna väljer du **Beräkna**. På fliken **kostnads uppskattning** visas den totala kostnaden för lagring och allokerat data flöde. Du kan expandera länken **Visa information** på den här fliken för att få en uppdelning av data flödet som krävs för Läs-och skriv förfrågningar. Varje gång du ändrar värdet för ett fält väljer du **Beräkna** för att beräkna den uppskattade kostnaden igen. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode.png" alt-text="Basic-läge för Capacity Planner":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Beräkna tillhandahållet data flöde och kostnad med avancerat läge

Med avancerat läge kan du ange fler inställningar som påverkar RU/s-uppskattningen. Om du vill använda det här alternativet går du till [kapacitets planeraren](https://cosmos.azure.com/capacitycalculator/) och loggar in till verktyget med ett konto som du använder för Azure. Inloggnings alternativet är tillgängligt i det högra hörnet. 

När du har loggat in kan du se ytterligare fält jämfört med fälten i grundläggande läge. Ange ytterligare parametrar utifrån din arbets belastning. 

|**Indata**  |**Beskrivning**  |
|---------|---------|
|API|Azure Cosmos DB är en tjänst med flera modeller och flera API: er. För nya arbets belastningar väljer du SQL-API (Core). |
|Antal regioner|Azure Cosmos DB är tillgängligt i alla Azure-regioner. Välj antalet regioner som krävs för din arbets belastning. Du kan associera ett valfritt antal regioner med ditt Cosmos-konto. Mer information finns i [global distribution](distribute-data-globally.md) i Azure Cosmos dB.|
|Skrivningar för flera regioner|Om du aktiverar [flera region skrivningar](distribute-data-globally.md#key-benefits-of-global-distribution)kan ditt program läsa och skriva till alla Azure-regioner. Om du inaktiverar skrivningar i flera regioner kan ditt program skriva data till en enda region. <br/><br/> Aktivera flera regioner om du förväntar dig att ha en aktiv-aktiv arbets belastning som kräver skrivningar med låg latens i olika regioner. Till exempel en IOT-arbetsbelastning som skriver data till databasen på stora volymer i olika regioner. <br/><br/> Skrivningar i flera regioner garanterar 99,999% Läs-och skriv tillgänglighet. Skrivningar i flera regioner kräver mer data flöde jämfört med de enkla Skriv regionerna. Mer information finns i [hur ru: er skiljer sig åt i en artikel med en och flera Skriv regioner](optimize-cost-regions.md) .|
|Standard konsekvens|Azure Cosmos DB stöder 5 konsekvens nivåer för att tillåta utvecklare att balansera kompromissen mellan konsekvens, tillgänglighet och latens. Mer information finns i artikeln [konsekvens nivåer](consistency-levels.md) . <br/><br/> Som standard använder Azure Cosmos DB konsekvens för sessioner, vilket garanterar möjligheten att läsa dina egna skrivningar i en session. <br/><br/> Om du väljer stark eller begränsad föråldrad måste du dubblera nödvändiga RU/s för läsningar, jämfört med session, konsekvent prefix och eventuell konsekvens. Stark konsekvens med skrivningar i flera regioner stöds inte och används automatiskt som standard för skrivningar med en region med stark konsekvens. |
|Indexeringsprincip|Som standard [indexerar Azure Cosmos DB alla egenskaper](index-policy.md) i alla objekt för flexibla och effektiva frågor (mappas till den **automatiska** indexerings principen). <br/><br/> Om du väljer **av** är ingen av egenskaperna indexerad. Detta resulterar i lägsta avgift för skrivningar. Välj **av** princip om du förväntar dig att bara göra [punkt läsningar](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) (nyckel värdes uppslag) och/eller skrivningar, och inga frågor. <br/><br/> Med anpassad indexerings princip kan du ta med eller utesluta vissa egenskaper från indexet för lägre Skriv data flöde och lagring. Mer information finns i avsnittet [indexerings policy](index-overview.md) och [exempel på indexerings principer](how-to-manage-indexing-policy.md#indexing-policy-examples) artiklar.|
|Totalt antal lagrade data (per region)|Totalt antal beräknade data som lagras i GB i en enda region.|
|Arbets belastnings läge|Välj **stabil** om din arbets belastnings volym är konstant. <br/><br/> Välj **variabel** om din arbets belastnings volym ändras med tiden.  Till exempel under en angiven dag eller månad. <br/><br/> Följande inställningar är tillgängliga om du väljer alternativet variabel arbets belastning:<ul><li>Procent andel av tiden med hög belastning: procent andel av tiden i månaden där arbets belastningen kräver högsta data flöde. <br/><br/> Om du till exempel har en arbets belastning som har hög aktivitet under 9 –. 18:00 vardags arbets tid, är procent andelen av tiden på högsta: 45 timmar med hög/730 timmar/månad = ~ 6%.<br/><br/></li><li>Läsningar/SEK per region vid högsta antal läsningar som förväntas per sekund vid hög belastning.</li><li>Skrivningar/SEK per region vid hög belastning – antalet skrivningar som förväntas per sekund vid hög belastning.</li><li>Läsningar/SEK per region med hög belastning – antalet läsningar som förväntas per sekund under låg belastning.</li><li>Skrivningar/SEK per region med hög belastning – antal skrivningar som förväntas per sekund under låg belastning.</li></ul>Med intervall med hög belastning och låg belastning kan du optimera kostnaderna genom [att skala ditt etablerade data flöde program mässigt](set-throughput.md#update-throughput-on-a-database-or-a-container) och nedåt.|
|Objektstorlek|Storleken på dataobjektet (t. ex. dokument) mellan 1 KB och 2 MB. <br/><br/>Du kan också **Ladda upp exempel (JSON)** dokument för en mer korrekt uppskattning.<br/><br/>Om din arbets belastning har flera typer av objekt (med olika JSON-innehåll) i samma behållare kan du ladda upp flera JSON-dokument och få uppskattningen. Använd knappen **Lägg till nytt objekt** för att lägga till flera exempel JSON-dokument.|

Du kan också använda knappen **Spara uppskattning** för att ladda ned en CSV-fil som innehåller den aktuella uppskattningen. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode.png" alt-text="Avancerat läge för kapacitets planering":::

Priserna som visas i Azure Cosmos DB kapacitets planeraren beräknas utifrån de offentliga pris nivåerna för data flöde och lagring. Alla priser visas i amerikanska dollar. På [sidan Azure Cosmos DB priser](https://azure.microsoft.com/pricing/details/cosmos-db/) kan du se alla priser per region.  

## <a name="estimating-throughput-for-queries"></a>Beräkna data flöde för frågor

I kapacitets Kalkylatorn för Azure Cosmos antas punkt läsningar (en läsning av ett enskilt objekt, t. ex. dokument, efter ID och nyckel värde) och skrivningar för arbets belastningen. Om du vill uppskatta det data flöde som behövs för frågor kör du din fråga på en representativ data uppsättning i en Cosmos-behållare och [hämtar avgiften för ru](find-request-unit-charge.md). Multiplicera avgiften för RU med antalet frågor som du förväntar dig att köra per sekund för att hämta totalt antal RU/s som krävs. 

Om din arbets belastning t. ex. kräver en fråga, ``SELECT * FROM c WHERE c.id = 'Alice'`` som körs 100 gånger per sekund och om förfrågan är 10 ru: er, behöver du 100 fråga/SEK * 10 ru/Query = 1000 ru/s totalt för att kunna hantera dessa begär Anden. Lägg till de RU/s till RU/s som krävs för alla läsningar och skrivningar i din arbets belastning.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [pris sättnings modellen för Azure Cosmos DB](how-pricing-works.md).
* Skapa ett nytt [Cosmos-konto, en databas och en behållare](create-cosmosdb-resources-portal.md).
* Lär dig hur du [optimerar den etablerade data flödes kostnaden](optimize-cost-throughput.md).
* Lär dig hur du [optimerar kostnaderna med reserverad kapacitet](cosmos-db-reserved-capacity.md).