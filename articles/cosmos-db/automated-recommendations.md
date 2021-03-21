---
title: Automatiserad prestanda, kostnad, säkerhets rekommendationer för Azure Cosmos DB
description: Lär dig hur du visar anpassade prestanda, kostnader, säkerhet och andra rekommendationer för Azure Cosmos DB baserat på dina arbets belastnings mönster.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: d9eb55030c7ec52f9b2ac79fbab19944f0a3e190
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93087833"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Automatiserade rekommendationer för Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Alla moln tjänster, inklusive Azure Cosmos DB få frekventa uppdateringar med nya funktioner, funktioner och förbättringar. Det är viktigt att ditt program håller sig uppdaterad med de senaste prestanda-och säkerhets uppdateringarna. Azure Portal erbjuder anpassade rekommendationer som gör att du kan maximera programmets prestanda. Azure Cosmos DBs råd givande motor analyserar kontinuerligt användnings historiken för dina Azure Cosmos DB resurser och ger rekommendationer baserat på dina arbets belastnings mönster. Dessa rekommendationer motsvarar områden som partitionering, indexering, nätverk, säkerhet osv. Med dessa anpassade rekommendationer kan du förbättra programmets prestanda.

## <a name="view-recommendations"></a>Visa rekommendationer

Du kan visa rekommendationer för Azure Cosmos DB på följande sätt:

- Ett sätt att Visa rekommendationerna finns på fliken meddelanden. Om det finns nya rekommendationer visas ett meddelande fält. Logga in på din [Azure Portal](https://portal.azure.com) och gå till ditt Azure Cosmos-konto. Öppna fönstret **meddelanden** i ditt Azure Cosmos-konto och välj sedan fliken **rekommendationer** . Du kan välja meddelandet och Visa rekommendationer.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Visa rekommendationer från Azure Cosmos DBs fönstret":::

- Du kan också hitta rekommendationerna genom [Azure Advisor](../advisor/advisor-overview.md) i Kategoriserad med olika buckets, till exempel kostnader, säkerhet, tillförlitlighet, prestanda och drift kvalitet. Du kan välja vissa prenumerationer och filtrera efter resurs typ, som är **Azure Cosmos DB konton**.  När du väljer en speciell rekommendation visas de åtgärder som du kan vidta för att dra nytta av dina arbets belastningar.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Visa rekommendationer från Azure Advisors fönstret":::

Alla rekommendationer som visas i Azure Cosmos DBs fönstret är inte tillgängliga i Azure Advisor och vice versa. Det beror på vilken typ av rekommendation de får i antingen Azure Advisor fönstret, Azure Cosmos DBs fönstret eller både och.

För närvarande Azure Cosmos DB stöder rekommendationer i följande områden. Var och en av dessa rekommendationer innehåller en länk till det relevanta avsnittet i dokumentationen, så det är enkelt att ta nästa steg.

## <a name="sdk-usage-recommendations"></a>Rekommendationer för SDK-användning

I den här kategorin identifierar Advisor användningen av en gammal version av SDK: er och rekommenderar att du uppgraderar till en nyare version för att utnyttja de senaste fel korrigeringarna och prestanda förbättringarna. För närvarande finns följande SDK-/regionsspecifika rekommendationer:

|Name  |Beskrivning  |
|---------|---------|
| Gammal Spark-anslutning | Identifierar användningen av en äldre version av Spark-anslutningen och rekommenderar att du uppgraderar. |
| Gamla .NET SDK | Identifierar användningen av gamla versioner av .NET SDK och rekommenderar att du uppgraderar. |
| Gamla Java SDK | Identifierar användningen av gamla versioner av Java-anslutningen och rekommenderar att du uppgraderar. |

## <a name="indexing-recommendations"></a>Indexerings rekommendationer

I den här kategorin identifierar Advisor indexerings läget, indexerings principen, indexerade sökvägar och rekommenderar att du ändrar om den aktuella konfigurationen påverkar frågans prestanda. För närvarande är följande indexerings bara rekommendationer tillgängliga:

|Name  |Beskrivning  |
|---------|---------|
| Lazy-indexering | Identifierar användningen av Lazy Indexing-läge och rekommenderar att du använder konsekvent indexerings läge i stället. Syftet med Azure Cosmos DB läget för Lazy-indexering är begränsat och kan påverka uppdateringens aktualitet i vissa situationer så att ett konsekvent indexerings läge rekommenderas. |
| Sammansatt indexering| Identifierar konton där frågor kan dra nytta av sammansatta index och rekommendera att använda dem. Sammansatta index kan dramatiskt förbättra prestanda-och data flödes användningen av vissa frågor.|
| Standard indexerings princip med många indexerade sökvägar | Identifierar behållare som körs på standard indexering med många indexerade sökvägar och rekommenderar att du anpassar indexerings principen.|
| Sortera efter frågor med hög RU/s-avgift| Identifierar behållar utfärdande ORDER BY-frågor med hög RU/s-kostnad och rekommenderar att du undersöker sammansatta index.|
| MongoDB 3,6-konton utan index och hög RU/s-förbrukning| Identifierar Azure Cosmos DBs API för MongoDB med 3,6-versionen av behållare som utfärdar frågor med hög RU/s-avgift och rekommenderar att du lägger till index.|

## <a name="cost-optimization-recommendations"></a>Rekommendationer för kostnads optimering

I den här kategorin identifierar Advisor RU/s-användningen och avgör att du kan optimera priset genom att göra några ändringar i resurserna eller genom att använda en annan pris sättnings modell. För närvarande finns det tillgängliga rekommendationer för följande kostnads optimering:

|Name  |Beskrivning  |
|---------|---------|
| Reserverad kapacitet | Identifierar din RU/s-användning och rekommenderar reserverade instanser till användare som kan dra nytta av det. |
| Inaktiva behållare | Identifierar de behållare som inte har använts i mer än 30 dagar och rekommenderar att du minskar data flödet för sådana behållare eller tar bort dem.|
| Nya prenumerationer med högt data flöde | Identifierar nya prenumerationer med konto utgifter i allmänhet höga RU/s per dag och ger dem ett meddelande. Detta meddelande är särskilt avsett för nya kunder som Azure Cosmos DB arbetar med etablerade data flödes modeller och inte konsumtions modeller. |

## <a name="migration-recommendations"></a>Rekommendationer för migrering

I den här kategorin upptäcker Advisor att du använder äldre funktioner rekommenderar att du migrerar så att du kan utnyttja Azure Cosmos DBs enorma skalbarhet och andra fördelar. För närvarande finns följande migrations-/regionsspecifika rekommendationer:

|Name  |Beskrivning  |
|---------|---------|
| Icke-partitionerade behållare | Identifierar behållare med fast storlek som närmar sig den maximala lagrings gränsen och rekommenderar att du migrerar dem till partitionerade behållare.|

## <a name="query-usage-recommendations"></a>Frågor om användnings rekommendationer

I den här kategorin identifierar Advisor frågekörningen och identifierar att frågans prestanda kan justeras med vissa ändringar. Följande rekommendationer för användnings frågor är tillgängliga:

|Name  |Beskrivning  |
|---------|---------|
| Frågor med fast sid storlek | Identifierar frågor som utfärdats med en fast sid storlek och rekommenderar att du använder-1 (ingen begränsning på sid storleken) i stället för att definiera ett visst värde. Det här alternativet minskar antalet nätverks fördröjningar som krävs för att hämta alla resultat. |

## <a name="next-steps"></a>Nästa steg

* [Justera prestanda för frågor i Azure Cosmos DB](sql-api-query-metrics.md)
* [Felsök problem med frågor](troubleshoot-query-performance.md) när du använder Azure Cosmos DB
