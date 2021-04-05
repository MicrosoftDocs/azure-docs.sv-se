---
title: Ändra feed design mönster i Azure Cosmos DB
description: Översikt över design mönster för vanliga ändrings flöden
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 443d00e61e593daacca04a4451b90bb78cc7d854
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334620"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Ändra feed design mönster i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ändra feed möjliggör effektiv bearbetning av stora data uppsättningar med en stor mängd skrivningar. Ändra feed erbjuder också ett alternativ till att fråga en hel data uppsättning för att identifiera vad som har ändrats. Det här dokumentet fokuserar på vanliga design mönster för ändrings flöden, design kompromisser och ändra begränsningar för feed.

Azure Cosmos DB passar bra för IoT-, spel-, detalj-och drift loggnings program. Ett gemensamt design mönster i dessa program är att använda ändringar av data för att utlösa ytterligare åtgärder. Exempel på ytterligare åtgärder är:

* Utlösa ett meddelande eller ett anrop till ett API när ett objekt infogas eller uppdateras.
* Data ström bearbetning i real tid för IoT-eller real tids analys bearbetning av drift data.
* Data förflyttning, till exempel synkronisering med en cache, en sökmotor, ett informations lager eller en kall lagring.

Med ändrings flödet i Azure Cosmos DB kan du bygga effektiva och skalbara lösningar för var och en av dessa mönster, som du ser i följande bild:

:::image type="content" source="./media/change-feed/changefeedoverview.png" alt-text="Använda Azure Cosmos DB ändra feed till Power real tids analys och händelse drivna scenarier" border="false":::

## <a name="event-computing-and-notifications"></a>Händelse bearbetning och meddelanden

Azure Cosmos DB ändra feed kan förenkla scenarier som behöver utlösa ett meddelande eller skicka ett anrop till ett API baserat på en viss händelse. Du kan använda processen för att [ändra feed-processen](change-feed-processor.md) för att automatiskt avsöka din behållare efter ändringar och anropa ett externt API varje gång som det finns en skrivning eller uppdatering.

Du kan också selektivt utlösa ett meddelande eller skicka ett anrop till ett API baserat på vissa kriterier. Om du till exempel läser från ändra feed med [Azure Functions](change-feed-functions.md)kan du placera logik i funktionen för att endast skicka ett meddelande om ett angivet villkor har uppfyllts. Medan Azure Function-koden körs vid varje skrivning och uppdatering skulle meddelandet bara skickas om vissa kriterier hade uppfyllts.

## <a name="real-time-stream-processing"></a>Ström bearbetning i real tid

Azure Cosmos DB ändra feed kan användas för data ström bearbetning i real tid för IoT-eller real tids analys bearbetning av drift data.
Du kan till exempel ta emot och lagra händelse data från enheter, sensorer, infrastruktur och program och bearbeta dessa händelser i real tid med hjälp av [Spark](../hdinsight/spark/apache-spark-overview.md). Följande bild visar hur du kan implementera en lambda-arkitektur med hjälp av Azure Cosmos DB via ändra feed:

:::image type="content" source="./media/change-feed/lambda.png" alt-text="Azure Cosmos DB-baserad lambda-pipeline för inmatning och fråga" border="false":::

I många fall får implementationen av data bearbetningen först en stor mängd inkommande data till en tillfällig meddelandekö, till exempel Azure Event Hub eller Apache Kafka. Ändrings flödet är ett bra alternativ på grund av Azure Cosmos DBs möjlighet att stödja en varaktig hög takt med data inmatning med garanterad låg Läs-och skriv fördröjning. Fördelarna med Azure Cosmos DB ändra feed över en meddelandekö är:

### <a name="data-persistence"></a>Data persistens

Data som skrivs till Azure Cosmos DB visas i ändrings flödet och bevaras tills de tas bort. Meddelande köer har vanligt vis en högsta kvarhållningsperiod. Till exempel erbjuder [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) en maximal datakvarhållning på 90 dagar.

### <a name="querying-ability"></a>Fråga om kapacitet

Förutom att läsa från en Cosmos containers ändrings flöde kan du också köra SQL-frågor på data som lagras i Azure Cosmos DB. Ändrings flödet är inte en dubblett av data som redan finns i behållaren, men i stället bara en annan mekanism för att läsa data. Om du läser data från ändrings flödet, är det därför alltid konsekvent med frågor av samma Azure Cosmos DB-behållare.

### <a name="high-availability"></a>Hög tillgänglighet

Azure Cosmos DB erbjuder upp till 99,999% Läs-och skriv tillgänglighet. Till skillnad från många meddelande köer kan Azure Cosmos DB data enkelt globalt distribueras och konfigureras med ett [RTO (återställnings tid)](./consistency-levels.md#rto) på noll.

Efter bearbetning av objekt i ändrings flödet kan du bygga en materialiserad vy och bevara sammanställda värden i Azure Cosmos DB. Om du använder Azure Cosmos DB för att bygga ett spel kan du till exempel använda ändra feed för att implementera ranknings listor i real tid baserat på poängen från färdiga spel.

## <a name="data-movement"></a>Dataförflyttning

Du kan också läsa från ändra feed för data förflyttning i real tid.

Till exempel hjälper ändrings flödet dig att utföra följande uppgifter effektivt:

* Uppdatera en cache, ett Sök index eller ett informations lager med data som lagras i Azure Cosmos DB.

* Utför migreringen från noll till ett annat Azure Cosmos-konto eller till en annan Azure Cosmos-behållare med en annan logisk partitionsnyckel.

* Implementera data skiktning och arkivering på program nivå. Du kan till exempel lagra "heta data" i Azure Cosmos DB och åldras "kalla data" till andra lagrings system, till exempel [Azure Blob Storage](../storage/common/storage-introduction.md).

När du måste [normalisera data mellan partitioner och behållare](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)kan du läsa från behållarens ändrings flöde som källa för denna datareplikering. Data replikering i real tid med ändra feed kan endast garantera eventuell konsekvens. Du kan [övervaka hur långt processorn för byte av lags bakom](how-to-use-change-feed-estimator.md) bearbetar ändringar i din Cosmos-behållare.

## <a name="event-sourcing"></a>Händelse källa

[Mönstret för händelse källor](/azure/architecture/patterns/event-sourcing) innebär att du använder en lagrings plats för endast tillägg för att registrera hela serien med åtgärder för dessa data. Azure Cosmos DBens ändrings flöde är ett bra val som ett centralt data lager i händelse källor arkitekturer där all data inmatning modelleras som skrivningar (inga uppdateringar eller borttagningar). I det här fallet är varje skrivning till Azure Cosmos DB en "event" och du kommer att ha en fullständig post med tidigare händelser i ändrings flödet. Vanliga användnings områden för de händelser som publiceras av den centrala händelse lagringen är för att underhålla materialiserade vyer eller för integrering med externa system. Eftersom det inte finns någon tids gräns för kvarhållning i ändrings flödet kan du spela upp alla tidigare händelser genom att läsa från början av Cosmos-behållarens ändrings flöde.

Du kan ha [flera ändrings flöden som konsumenter prenumererar på samma behållares ändrings flöde](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Det finns inte någon kostnad för att använda ändrings flödet från [leasing behållarens](change-feed-processor.md#components-of-the-change-feed-processor) etablerade data flöde. Ändrings flödet är tillgängligt i varje behållare oavsett om den används eller inte.

Azure Cosmos DB är ett bra centralt permanent tilläggs data lager i händelse källan, på grund av dess styrkor i vågrät skalbarhet och hög tillgänglighet. Dessutom ger processor biblioteket för förändrings flödet en ["minst en gång"](change-feed-processor.md#error-handling) -garanti, vilket säkerställer att du inte bearbetar några händelser.

## <a name="current-limitations"></a>Aktuella begränsningar

Ändrings flödet har viktiga begränsningar som du bör känna till. Objekt i en Cosmos-behållare kommer alltid att finnas kvar i ändrings flödet, men ändrings flödet är inte en fullständig åtgärds logg. Det finns viktiga områden att tänka på när du skapar ett program som använder ändrings flödet.

### <a name="intermediate-updates"></a>Mellanliggande uppdateringar

Endast den senaste ändringen för ett angivet objekt ingår i ändrings flödet. När du bearbetar ändringar kommer du att läsa den senaste tillgängliga versionen av objektet. Om det finns flera uppdateringar av samma objekt under en kort tids period, är det möjligt att bearbeta mellanliggande uppdateringar. Om du vill spåra uppdateringar och kunna spela upp tidigare uppdateringar av ett objekt rekommenderar vi att du modellerar dessa uppdateringar som en serie skrivningar i stället.

### <a name="deletes"></a>Text

Ändrings flödet fångar inte in borttagningar. Om du tar bort ett objekt från din behållare, tas det även bort från ändra feed. Den vanligaste hanterings metoden är att lägga till en mjuk markör för de objekt som tas bort. Du kan lägga till en egenskap med namnet "borttagen" och ange den som "true" vid tidpunkten för borttagningen. Den här dokument uppdateringen visas i ändrings flödet. Du kan ange ett TTL-värde för det här objektet så att det kan tas bort automatiskt senare.

### <a name="guaranteed-order"></a>Garanterad ordning

Det finns en garanterad ordning i ändrings flödet inom ett nyckel värde för partitionen, men inte mellan nyckel värden. Du bör välja en partitionsnyckel som ger dig en meningsfull order garanti.

Anta till exempel ett detalj handels program med design mönstret för händelse källor. I det här programmet är olika användar åtgärder varje "händelser" som modelleras som skrivningar till Azure Cosmos DB. Tänk dig om några exempel händelser inträffar i följande ordning:

1. Kunden lägger till artikel A i sin shopping vagn
2. Kunden lägger till objekt B i kund vagnen
3. Kunden tar bort artikel A från kund vagnen
4. Kund checkar ut och shopping vagns innehåll levereras

En materialiserad vy över aktuell shopping vagns innehåll underhålls för varje kund. Det här programmet måste se till att dessa händelser bearbetas i den ordning som de inträffar. Om till exempel en kundvagn-utcheckning skulle bearbetas innan posten tas bort, är det troligt att kunden hade haft ett levererat objekt, i stället för den önskade artikeln B. För att garantera att dessa fyra händelser bearbetas i händelse av deras förekomst, ska de ligga inom samma partitionsnyckel. Om du väljer **användar namn** (varje kund har ett unikt användar namn) som partitionsnyckel kan du garantera att dessa händelser visas i ändrings flödet i samma ordning som de skrivs till Azure Cosmos dB.

## <a name="examples"></a>Exempel

Här följer några exempel på verkliga exempel kod exempel som sträcker sig utanför omfånget för de exempel som finns i Microsoft-dokument:

- [Introduktion till ändrings flödet](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Användnings fall för IoT centreras runt ändrings flödet](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Väska för detalj handels användning centrerad runt ändrings flödet](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Nästa steg

* [Översikt över ändringsflöde](change-feed.md)
* [Alternativ för att läsa ändrings flöde](read-change-feed.md)
* [Använda ändra feed med Azure Functions](change-feed-functions.md)