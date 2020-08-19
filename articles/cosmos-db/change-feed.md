---
title: Arbeta med stöd för ändrings flöden i Azure Cosmos DB
description: Använd Azure Cosmos DB ändra feed-stöd för att spåra ändringar i dokument, händelsebaserade bearbetningar som utlösare och hålla cacheminnen och analys system uppdaterade
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 4cd0ad1553f04a781349a4664fbb408108015632
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510291"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Ändringsflöde i Azure Cosmos DB

Stödet för ändringsflöde i Azure Cosmos DB fungerar genom att lyssna efter ändringar på en Azure Cosmos-container. Funktionen returnerar sedan den sorterade listan över dokument som ändrats i den ordning de ändrades. Ändringarna är beständiga, kan bearbetas asynkront och inkrementellt, och utdata kan distribueras bland en eller flera konsumenter för parallell bearbetning.

Lär dig mer om [design mönster för att ändra feed](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>API: er och klient-SDK som stöds

Den här funktionen stöds för närvarande av följande Azure Cosmos DB-API: er och klient-SDK: er.

| **Klientdrivrutiner** | **SQL-API** | **Azure Cosmos DB s API för Cassandra** | **API för Azure Cosmos DB för MongoDB** | **Gremlin-API**|**Tabell-API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Ja | Ja | Ja | Ja | Nej |
|Java|Ja|Ja|Ja|Ja|Nej|
|Python|Ja|Ja|Ja|Ja|Nej|
|Node/JS|Ja|Ja|Ja|Ja|Nej|

## <a name="change-feed-and-different-operations"></a>Ändra feed och olika åtgärder

Idag visas alla infogningar och uppdateringar i ändrings flödet. Det går inte att filtrera ändrings flödet för en speciell typ av åtgärd. Ett möjligt alternativ är att lägga till en "mjuk markör" på objektet för uppdateringar och filter baserat på det vid bearbetning av objekt i ändrings flödet.

Den aktuella ändrings flödet loggar inte borttagningar. Precis som i föregående exempel kan du lägga till en mjuk markör för de objekt som tas bort. Du kan till exempel lägga till ett attribut i objektet som kallas "borttaget" och ange det som "true" och ange ett TTL-värde för objektet, så att det kan tas bort automatiskt. Du kan läsa ändrings flödet för historiska objekt (den senaste ändringen som motsvarar objektet, det innehåller inte mellanliggande ändringar), till exempel objekt som har lagts till fem år sedan. Du kan läsa ändrings flödet så långt tillbaka som dess ursprung, men om ett objekt tas bort tas det bort från ändrings flödet.

### <a name="sort-order-of-items-in-change-feed"></a>Sorterings ordning för objekt i ändrings flöde

Ändra feed-objekt levereras i rätt ordning efter ändrings tiden. Den här sorterings ordningen garanteras per logisk partitionsnyckel.

### <a name="consistency-level"></a>Konsekvensnivå

När du förbrukar ändrings flödet i en eventuell konsekvens nivå, kan det finnas dubbla händelser i-mellan efterföljande Läs åtgärder för ändrings flöden (den sista händelsen i en Läs åtgärd visas som första av nästa).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Ändra feed i Azure Cosmos-konton med flera regioner

I ett Azure Cosmos-konto med flera regioner, om en skrivnings region växlar över, kommer ändrings flödet att fungera i den manuella redundansväxlingen och det kommer att vara sammanhängande.

### <a name="change-feed-and-time-to-live-ttl"></a>Ändra feed och Time to Live (TTL)

Om en egenskap för ett TTL-värde (Time to Live) har angetts för ett objekt till-1 sparas ändrings flödet för alltid. Om data inte tas bort finns de kvar i ändrings flödet.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Ändra feed och _etag, _lsn eller _ts

_Etags formatet är internt och du bör inte ta beroende på det, eftersom det kan ändras när som helst. _ts är en ändring eller en tidsstämpel för skapande. Du kan använda _ts för kronologisk jämförelse. _lsn är ett batch-ID som endast läggs till för ändrings flöde. den representerar transaktions-ID: t. Många objekt kan ha samma _lsn. ETag på FeedResponse skiljer sig från _etag som visas på objektet. _etag är en intern identifierare som används för samtidighets kontroll. Egenskapen _etag visar om versionen av objektet, medan ETag-egenskapen används för att sekvensera feeden.

## <a name="working-with-change-feed"></a>Arbeta med ändrings flöde

Du kan arbeta med ändrings flöden med följande alternativ:

* [Använda ändra feed med Azure Functions](change-feed-functions.md)
* [Använda Change feed med Change feed processor](change-feed-processor.md) 

Ändrings flödet är tillgängligt för varje logisk partitionsnyckel i behållaren, och den kan distribueras över en eller flera konsumenter för parallell bearbetning som visas i bilden nedan.

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Distribuerad bearbetning av Azure Cosmos DB ändra feed" border="false":::

## <a name="features-of-change-feed"></a>Funktioner i ändrings flöde

* Ändra feed är aktiverat som standard för alla Azure Cosmos-konton.

* Du kan använda ditt [etablerade data flöde](request-units.md) för att läsa från ändrings flödet, precis som andra Azure Cosmos DB åtgärder, i någon av de regioner som är kopplade till din Azure Cosmos-databas.

* Ändrings flödet innehåller infogningar och uppdaterings åtgärder som gjorts för objekt i behållaren. Du kan samla in borttagningar genom att ange flaggan "mjuk borttagning" i dina objekt (till exempel dokument) i stället för borttagningar. Alternativt kan du ange en begränsad förfallo period för dina objekt med TTL- [funktionen](time-to-live.md). Till exempel 24 timmar och Använd värdet för den egenskapen för att avbilda borttagningar. Med den här lösningen måste du bearbeta ändringarna inom ett kortare tidsintervall än förfallo tiden för TTL.

* Varje ändring av ett objekt visas exakt en gång i ändrings flödet och klienterna måste hantera kontroll punkts logiken. Om du vill undvika komplexiteten med att hantera kontroll punkter, tillhandahåller Change feed-processorn automatisk kontroll punkt och "minst en gång"-semantik. Se [använda ändra feed med Change feed processor](change-feed-processor.md).

* Endast den senaste ändringen för ett angivet objekt ingår i ändrings loggen. Mellanliggande ändringar kanske inte är tillgängliga.

* Ändrings flödet sorteras efter ändrings ordningen i varje nyckel värde för logisk partition. Det finns ingen garanterad ordning för nyckel värden i partitionen.

* Ändringar kan synkroniseras från vilken tidpunkt som helst, eftersom det inte finns några fasta data lagrings perioder för vilka ändringar är tillgängliga.

* Ändringarna är tillgängliga parallellt för alla logiska partitionsnyckel för en Azure Cosmos-behållare. Den här funktionen gör det möjligt att bearbeta ändringar från stora behållare parallellt av flera konsumenter.

* Program kan begära flera ändrings flöden på samma behållare samtidigt. ChangeFeedOptions. StartTime kan användas för att ange en start punkt. Om du till exempel vill hitta en fortsättnings-token som motsvarar en angivet klock slag. ContinuationToken, om det anges, prioriteras framför värdena StartTime och StartFromBeginning. Precisionen för ChangeFeedOptions. StartTime är ~ 5 sekunder.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Ändra feed i API: er för Cassandra och MongoDB

Funktionen ändra feed fungerar som en ändrings ström i MongoDB API och fråga med predikat i API för Cassandra. Mer information om implementerings informationen för MongoDB-API finns i [ändrings strömmar i Azure Cosmos DB API för MongoDB](mongodb-change-streams.md).

Inbyggd Apache Cassandra tillhandahåller registrering av ändrings data (CDC), en mekanism för att flagga specifika tabeller för arkivering och avvisning av skrivningar till dessa tabeller när en konfigurerbar storlek-på disk för CDC-loggen nås. Funktionen ändra feed i Azure Cosmos DB API för Cassandra förbättrar möjligheten att fråga ändringarna med predikat via CQL. Mer information om implementerings informationen finns i [ändra feed i Azure Cosmos DB API för Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med att lära dig mer om ändrings flöden i följande artiklar:

* [Alternativ för att läsa ändrings flöde](read-change-feed.md)
* [Använda ändra feed med Azure Functions](change-feed-functions.md)
* [Använda Change feed processor](change-feed-processor.md)
