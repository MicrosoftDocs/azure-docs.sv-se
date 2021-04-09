---
title: Optimera kostnaden för dina begär anden i Azure Cosmos DB
description: Den här artikeln förklarar hur du optimerar kostnaderna när du skickar förfrågningar på Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 36ecef007e10f9a090dbabc8b5a91fd473930141
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102633860"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>Optimera begär ande kostnader i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Den här artikeln beskriver hur Läs-och skriv förfrågningar översätts till [enheter för programbegäran](request-units.md) och hur du optimerar kostnaden för dessa begär Anden. Läs åtgärder innehåller punkt läsningar och frågor. Skriv åtgärder omfattar Infoga, Ersätt, ta bort och upsert av objekt.

Azure Cosmos DB erbjuder en omfattande uppsättning databas åtgärder som fungerar med objekten i en behållare. Den kostnad som hör till var och en av dessa operationer varierar baserat på vilken CPU, vilka IO-resurser och hur mycket minne som krävs för att slutföra operationen. I stället för att tänka på och hantera maskin varu resurser kan du tänka på en enhet för begäran (RU) som ett enda mått för de resurser som krävs för att utföra olika databas åtgärder för att betjäna en begäran.

## <a name="measuring-the-ru-charge-of-a-request"></a>Mäta RU-avgiften för en begäran

Det är viktigt att mäta RU-avgiften för dina förfrågningar för att förstå deras faktiska kostnad och även utvärdera effektiviteten i dina optimeringar. Du kan hämta den här kostnaden genom att använda Azure Portal eller granska svaret som skickas tillbaka från Azure Cosmos DB via en av SDK: erna. Mer information om hur du uppnår finns [i hitta enhets avgiften för begäran i Azure Cosmos DB](find-request-unit-charge.md) .

## <a name="reading-data-point-reads-and-queries"></a>Läser data: punkt läsningar och frågor

Läs åtgärder i Azure Cosmos DB beställs vanligt vis från snabbast/mest effektiva till långsammare/mindre effektiva i termer av RU-förbrukningen enligt följande:  

* Punkt läsningar (nyckel/värde-sökning för ett enskilt objekt-ID och partitionsnyckel).
* Fråga med en filter-sats inom en enskild partitionsnyckel.
* Fråga utan en likhets-eller Range filter-sats för en egenskap.
* Fråga utan filter.

### <a name="role-of-the-consistency-level"></a>Rollen för konsekvens nivån

När du använder antingen **starka** eller begränsade **inaktuella** [konsekvens nivåer](consistency-levels.md), är ru-kostnaden för alla Läs åtgärder (punkt läsning eller fråga) dubbelt.

### <a name="point-reads"></a>Punkt läsningar

Den enda faktorn som påverkar RU-avgiften för en punkt som läses (förutom den konsekvens nivå som används) är storleken på det hämtade objektet. I följande tabell visas värdet för RU-kostnaden för objekt som är 1 KB och 100 KB i storlek.

| **Objekt storlek** | **Kostnad för en poäng läsning** |
| --- | --- |
| 1 kB | 1 RU |
| 100 kB | 10 RU:er |

Eftersom punkt läsningar (nyckel/värde-uppslag i objekt-ID) är den mest effektiva typen av läsning bör du se till att ditt objekt-ID har ett meningsfullt värde så att du kan hämta dina objekt med en punkt läsning (i stället för en fråga) när det är möjligt.

### <a name="queries"></a>Frågor

Enheter för programbegäran för frågor är beroende av ett antal faktorer. Till exempel antalet inlästa/returnerade Azure Cosmos-objekt, antalet uppslag mot indexet, tiden för frågans kompilering osv. Azure Cosmos DB garanterar att samma fråga när den körs på samma data alltid kommer att förbruka samma antal enheter för programbegäran även med upprepad körning. Du får en bra uppfattning om hur enheter för programbegäran används i frågan profil med hjälp av körnings mått för frågor.  

I vissa fall kan du se en sekvens med 200-och 429-svar, och variabla enheter för programbegäran i en växlad körning av frågor, det vill säga att frågor körs så snabbt som möjligt baserat på tillgängliga ru: er. Du kan se att en frågekörningen bryts i flera sidor/avrunda resor mellan server och klient. Till exempel kan 10 000 objekt returneras som flera sidor, varje debiteras baserat på den beräkning som utförs för den sidan. När du summerar över dessa sidor bör du få samma antal ru: er som du skulle få för hela frågan.

#### <a name="metrics-for-troubleshooting-queries"></a>Mått för fel söknings frågor

Prestanda och data flöde som används av frågor (inklusive användardefinierade funktioner) är huvudsakligen beroende av funktions texten. Det enklaste sättet att ta reda på hur lång tid det tar att köra frågekörningen i UDF och antalet förbrukade ru: er, är att aktivera frågans mått. Om du använder .NET SDK är här exempel frågans mått som returneras av SDK:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

#### <a name="best-practices-to-cost-optimize-queries"></a>Bästa metoder för att kostnads optimera frågor 

Tänk på följande rekommendationer när du optimerar frågor för kostnad:

* **Samplacera flera typer av enheter**

   Försök att samplacera flera entitetstyper inom ett enda eller mindre antal behållare. Den här metoden ger förmåner inte bara från ett pris perspektiv, utan även för frågekörning och transaktioner. Frågor är begränsade till en enda behållare. och atomiska transaktioner över flera poster via lagrade procedurer/utlösare är begränsade till en partitionsnyckel inom en enda behållare. Att placera entiteter i samma behållare kan minska antalet nätverks fördröjningar för att lösa relationer mellan poster. Det ökar prestandan från slut punkt till slut punkt, som möjliggör atomiska transaktioner över flera poster för en större data uppsättning och därmed lägre kostnader. Om samplacering av flera entitetstyper inom ett enda eller mindre antal behållare är svårt för ditt scenario, vanligt vis på grund av att du migrerar ett befintligt program och inte vill göra några kod ändringar, bör du överväga att konfigurera data flödet på databas nivå.  

* **Mått och justering för lägre enheter för programbegäran/andra användning**

   Komplexiteten i en fråga påverkar hur många ru: er (Request units) som används för en åtgärd. Antalet predikat, typen av predikat, antalet UDF: er och storleken på käll data uppsättningen. Alla dessa faktorer påverkar kostnaderna för frågor. 

Azure Cosmos DB tillhandahåller förutsägbar prestanda med avseende på data flöde och svars tid med hjälp av en etablerad data flödes modell. Det data flöde som tillhandahålls är representerat i [enheter för programbegäran](request-units.md) per sekund eller ru/s. En enhet för programbegäran (RU) är en logisk abstraktion över beräknings resurser, till exempel processor, minne, IO, osv. som krävs för att utföra en begäran. Det etablerade data flödet (ru: er) har tagits ur bruk och är dedikerat för din behållare eller databas för att tillhandahålla förutsägbart data flöde och svars tider. Med det etablerade data flödet kan Azure Cosmos DB tillhandahålla förutsägbara och konsekventa prestanda, garanterad låg latens och hög tillgänglighet i valfri skala. Enheter för programbegäran representerar den normaliserade valutan som fören klar orsaken till hur många resurser ett program behöver.

Begär ande avgift som returneras i rubriken för begäran visar kostnaden för en specifik fråga. Om en fråga till exempel returnerar 1000 1 KB-objekt är kostnaden för åtgärden 1000. I och med den här servern bevarar servern bara två sådana begär Anden innan hastigheten begränsar efterföljande begär Anden. Mer information finns i artikeln om [enheter för programbegäran](request-units.md) och Kalkylatorn för begär ande enheter.

## <a name="writing-data"></a>Skriver data

RU-kostnaden för att skriva ett objekt beror på:

- Objektets storlek.
- Antalet egenskaper som omfattas av [indexerings principen](index-policy.md) och som krävs för att indexeras.

Infogar ett 1 KB-objekt utan att indexera kostnaderna runt 5,5 ru: er. Om du ersätter ett objekt kostar det två gånger den kostnad som krävs för att infoga samma objekt.

### <a name="optimizing-writes"></a>Optimera skrivningar

Det bästa sättet att optimera RU-kostnaden för Skriv åtgärder är att hitta rätt storlek dina objekt och antalet egenskaper som indexeras.

- Att lagra mycket stora objekt i Azure Cosmos DB resulterar i höga RU-kostnader och kan betraktas som ett antimönster. Lagra särskilt inte binärt innehåll eller stora delar av text som du inte behöver fråga på. Ett bra tips är att placera den här typen av data i [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) och lagra en referens (eller länk) till blobben i det objekt som du skriver till Azure Cosmos dB.
- Optimering av indexerings principen för att endast indexera egenskaperna som dina frågor filtrerar på kan göra en stor skillnad i ru: er som används av dina Skriv åtgärder. När du skapar en ny behållare indexerar standard indexerings principen var och en av de alla egenskaper som finns i objekten. Även om det här är en bra standard för utvecklings aktiviteter, rekommenderar vi starkt att du förnyar och [anpassar din indexerings princip](how-to-manage-indexing-policy.md) när du går till produktion eller när din arbets belastning börjar ta emot betydande trafik.

När du utför Mass inmatning av data rekommenderar vi också att du använder [Azure Cosmos DB bulk utförar-biblioteket](bulk-executor-overview.md) eftersom det är utformat för att optimera ru-förbrukningen för sådana åtgärder. Om du vill kan du också använda [Azure Data Factory](../data-factory/introduction.md) som är inbyggd i samma bibliotek.

## <a name="next-steps"></a>Nästa steg

Härnäst kan du fortsätta med att lära dig mer om kostnads optimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Lär dig mer om [att förstå din Azure Cosmos DB faktura](understand-your-bill.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Läs mer om hur [du optimerar kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)
* Läs mer om [Azure Cosmos DB reserverad kapacitet](cosmos-db-reserved-capacity.md)
