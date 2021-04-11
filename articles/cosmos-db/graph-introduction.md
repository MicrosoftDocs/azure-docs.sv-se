---
title: Introduktion till Azure Cosmos DB Gremlin API
description: Läs hur du kan använda Azure Cosmos DB för att lagra, fråga och bläddra igenom massiva diagram med låga svarstider med hjälp av Gremlin-diagramfrågespråket för Apache TinkerPop.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 03/22/2021
ms.author: chrande
ms.openlocfilehash: ffe11fafa6ba217572f9d12d40324a5201b62630
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801697"
---
# <a name="introduction-to-gremlin-api-in-azure-cosmos-db"></a>Introduktion till Gremlin-API i Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

[Azure Cosmos DB](introduction.md)   är den globalt distribuerade databas tjänsten för flera modeller från Microsoft för verksamhets kritiska program. Det är en databas med flera modeller och stöd för dokument, nyckel värdes-, graf-och kolumn serie data modeller. Azure Cosmos DB tillhandahåller en Graph Database-tjänst via Gremlin-API: et i en fullständigt hanterad databas tjänst utformad för alla skalor.  

:::image type="content" source="./media/graph-introduction/cosmosdb-graph-architecture.png" alt-text="Azure Cosmos DB-diagramarkitektur" border="false":::

Den här artikeln innehåller en översikt över API för Azure Cosmos DB Gremlin och förklarar hur du använder dem för att lagra enorma grafer med miljard tals hörn och kanter. Du kan fråga graferna med millisekunder och snabbt utveckla diagram strukturen. Azure Cosmos DBens Gremlin-API bygger på [Apache TinkerPop](https://tinkerpop.apache.org), ett ramverk för diagram bearbetning. Gremlin-API: et i Azure Cosmos DB använder Gremlin-frågespråket.

Azure Cosmos DBens Gremlin-API kombinerar kraften i graf Database-algoritmer med en mycket skalbar, hanterad infrastruktur för att tillhandahålla en unik, flexibel lösning för de vanligaste data problemen som är kopplade till bristande flexibilitet och Relations metoder.

> [!NOTE]
> Azure Cosmos DB diagram motor närmar sig följande Apache TinkerPop-specifikation. Det finns dock vissa skillnader i implementerings informationen som är speciell för Azure Cosmos DB. Vissa funktioner som stöds av Apache TinkerPop är inte tillgängliga i Azure Cosmos DB. mer information om de funktioner som inte stöds finns i [kompatibilitet med Apache TinkerPop](gremlin-support.md) -artikeln.

## <a name="features-of-azure-cosmos-dbs-gremlin-api"></a>Funktioner i Azure Cosmos DBs Gremlin-API

Azure Cosmos DB är en fullständigt hanterad grafdatabas med global distribution, elastisk skalbarhet av lagring och dataflöde, automatisk indexering och frågor, justerbara konsekvensnivåer och stöd för TinkerPop-standarden.

> [!NOTE]
> [Läget för Server lös kapacitet](serverless.md) är nu tillgängligt på Azure Cosmos DBens GREMLIN-API.

Följande är de differentierade funktionerna som Azure Cosmos DB Gremlin-API: er:

* **Elastiskt skalbara dataflöden och lagring**

  Diagram i verkligheten behöver skala utöver kapaciteten för en enskild server. Azure Cosmos DB stöder vågrätt skalbara diagram databaser som kan ha en i princip obegränsad storlek i termer av lagring och allokerat data flöde. I takt med att diagram databasen skalas, distribueras data automatiskt med hjälp av [diagram partitionering](./graph-partitioning.md).

* **Replikering för flera regioner**

  Azure Cosmos DB kan automatiskt replikera dina diagram data till alla Azure-regioner i hela världen. Global replikering fören klar utvecklingen av program som kräver global åtkomst till data. Förutom att minimera Läs-och skriv fördröjning var som helst över hela världen, tillhandahåller Azure Cosmos DB automatisk regional redundans som kan garantera programmets kontinuitet i sällsynta fall av ett tjänst avbrott i en region.

* **Snabba frågor och bläddringskontroll med den mest antagna standarden för kurv frågor**

  Lagra heterogena hörn och kanter och fråga dem genom en välbekant Gremlin-syntax. Gremlin är ett tvingande, funktionellt frågespråk som ger ett omfattande gränssnitt för att implementera vanliga graf-algoritmer.
  
  Azure Cosmos DB möjliggör omfattande frågor och bläddringskontroll i real tid utan att behöva ange schema tips, sekundära index eller vyer. Läs mer i [Fråga diagram med hjälp av Gremlin](gremlin-support.md).

* **Fullständigt hanterad diagram databas**

  Azure Cosmos DB eliminerar behovet av att hantera databasen och datorresurserna. De flesta befintliga Graph Database-plattformarna är kopplade till begränsningarna i infrastrukturen och kräver ofta en hög grad av underhåll för att säkerställa driften. 
  
  Som en helt hanterad tjänst tar Cosmos DB bort behovet av att hantera virtuella datorer, uppdatera runtime-programvaran, hantera horisontell partitionering eller replikering, eller hantera komplexa uppgraderingar av data nivåer. Varje databas säkerhetskopieras och skyddas automatiskt mot regionala fel. På så sätt kan utvecklare fokusera på att leverera program värde i stället för att hantera sina graf-databaser. 

* **Automatisk indexering**

  Som standard indexerar Azure Cosmos DB automatiskt alla egenskaper i noderna (kallas även hörn) och kanter i grafen och inte förväntar sig eller kräver något schema eller skapandet av sekundära index. Läs mer om [indexering i Azure Cosmos DB](./index-overview.md).

* **Kompatibilitet med Apache TinkerPop**

  Azure Cosmos DB stöder [Apache TinkerPop-standarden med öppen källkod](https://tinkerpop.apache.org/). Tinkerpop-standarden har ett stort eko system med program och bibliotek som enkelt kan integreras med Azure Cosmos DBens Gremlin-API.

* **Justerbara konsekvensnivåer**

  Azure Cosmos DB tillhandahåller fem väldefinierade konsekvens nivåer för att uppnå rätt kompromisser mellan konsekvens och prestanda för ditt program. Azure Cosmos DB erbjuder fem olika konsekvensnivåer för frågor och läsåtgärder: stark, bunden utgång, session, enhetligt prefix och slutlig. Med de här detaljerade, väldefinierade konsekvensnivåerna kan du själv avgöra balansen mellan konsekvens, tillgänglighet och svarstid. Läs mer i [Justerbara datakonsekvensnivåer i Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-use-gremlin-api"></a>Scenarier som använder Gremlin-API

Här följer några scenarier där diagram stöd för Azure Cosmos DB kan vara användbart:

* **Sociala nätverk/kund 365**

  Genom att kombinera data om dina kunder och deras interaktioner med andra personer kan du utveckla anpassade upplevelser, förutsäga kundbeteende eller ansluta personer med andra med liknande intressen. Azure Cosmos-DB kan användas för att hantera sociala nätverk och spåra kundinställningar och data.

* **Rekommendationsmotorer**

  Det här scenariot används ofta inom detaljhandeln. Genom att kombinera information om produkter, användare och användarinteraktioner som köp, bläddring eller klassificeringen av ett objekt, kan du skapa anpassade rekommendationer. Stöd för låg latens, elastisk skalning och inbyggd graf för Azure Cosmos DB är perfekt för dessa scenarier.

* **Geospatial**

  Många program inom telekommunikation, logistik och reseplanering måste hitta en plats av intresse inom ett område eller hitta den kortaste/optimala vägen mellan två platser. Azure Cosmos DB är en naturlig lösning på de här problemen.

* **Sakernas Internet**

  Med nätverket och anslutningarna mellan IoT-enheter som modellerats som ett diagram, kan du skapa en bättre förståelse för dina enheters och tillgångars tillstånd. Du kan också undersöka hur ändringar i en del av nätverket kan påverka en annan del.

## <a name="introduction-to-graph-databases"></a>Introduktion till diagram databaser

Data som de visas i verkligheten är naturligt sammanlänkade. Traditionell data modellering fokuserar på att definiera entiteter separat och att beräkna deras relationer vid körning. Även om den här modellen har sina fördelar kan data som är mycket anslutna vara svåra att hantera under sina begränsningar.  

En diagram databas metod förlitar sig på beständiga relationer i lagrings lagret i stället, vilket leder till mycket effektiva diagram hämtnings åtgärder. Azure Cosmos DBens Gremlin-API stöder [egenskaps diagrammets modell](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Egenskaps diagram objekt

Ett egenskaps [diagram](http://mathworld.wolfram.com/Graph.html) är en struktur som består av [hörn](http://mathworld.wolfram.com/GraphVertex.html) och [kanter](http://mathworld.wolfram.com/GraphEdge.html). Båda objekten kan ha ett godtyckligt antal nyckel/värde-par som egenskaper. 

* **Formhörn/noder** – hörn är diskreta entiteter, till exempel en person, en plats eller en händelse.

* **Kanter/Relationships** -kanter kännetecknar relationer mellan formhörn. En person kan till exempel känna till en annan person, vara inblandad i en händelse och nyligen ha varit på en plats.

* **Egenskaper** – Egenskaper uttrycker information om brytpunkterna och kanterna. Det kan finnas ett valfritt antal egenskaper i antingen formhörn eller kanter, och de kan användas för att beskriva och filtrera objekten i en fråga. Exempel egenskaperna innehåller ett hörn som har namn och ålder, eller en kant, som kan ha en tidstämpel och/eller en vikt.

* **Etikett** -en etikett är ett namn eller en identifierare för ett hörn eller en kant. Etiketter kan gruppera flera hörn eller kanter så att alla hörn/kanter i en grupp har en viss etikett. Ett diagram kan till exempel ha flera hörn av etikettyp "person".

Graf-databaser ingår ofta i NoSQL eller icke-relationell databas kategori eftersom det inte finns något beroende av ett schema eller en begränsad data modell. Detta brist på schema möjliggör modellering och lagring av anslutna strukturer naturligt och effektivt.

### <a name="graph-database-by-example"></a>Diagram databas enligt exempel

Nu ska vi använda ett exempeldiagram för att förstå hur frågor kan uttryckas i Gremlin. Följande bild visar ett affärsprogram som hanterar data om användare, intressen och enheter i form av ett diagram.  

:::image type="content" source="./media/gremlin-support/sample-graph.png" alt-text="Exempeldatabas som visar personer, enheter och intressen" border="false"::: 

Det här diagrammet har följande *hörn* typer (dessa kallas även "etikett" i Gremlin):

* **Personer**: grafen har tre personer, Robin, Thomas och ben
* **Intressen**: deras intressen, i det här exemplet spel på fotboll
* **Enheter**: enheterna som människor använder
* **Operativ system**: de operativ system som enheterna kör på
* **Plats**: platser som enheterna ska nås från

Vi representerar relationerna mellan dessa entiteter via följande *gräns* typer:

* **Känner**: till exempel "Thomas vet Robin"
* **Intresse rad**: att representera deltagarnas intressen i vårt diagram, till exempel "ben är intresserade av fotboll"
* **Körs**: den bärbara datorn kör Windows OS
* **Använder**: för att representera vilken enhet en person använder. Till exempel Robin använder en Motorola-telefon med serienummer 77
* **Finns**: för att representera platsen som enheterna används från

Gremlin-konsolen är en interaktiv Terminal som erbjuds av Apache TinkerPop och den här terminalen används för att interagera med Graf-data. Mer information finns i snabb starts dokumentet om [hur du använder Gremlin-konsolen](create-graph-gremlin-console.md). Du kan också utföra dessa åtgärder med hjälp av Gremlin-drivrutinerna i den plattform du vill (Java, Node.js, Python eller .NET). I följande exempel visas hur du kör frågor mot dessa diagram data med hjälp av Gremlin-konsolen.

Först ska vi titta på CRUD. Följande Gremlin-uttryck infogar Thomas-brytpunkten i diagrammet:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Därefter infogar följande Gremlin-uttryck en känner-kant mellan Thomas och Robin.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Följande fråga returnerar person-brytpunkten i fallande ordning efter deras förnamn:

```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Där diagram verkligen kommer till sin rätt är när det gäller att svara på frågor som Vilka operativsystem använder Thomas vänner? Du kan köra den här Gremlin-genom gången för att hämta informationen från grafen:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Nästa steg

Mer information om diagramstöd i Azure Cosmos DB finns i:

* Kom igång med [Självstudien Azure Cosmos DB-diagram](create-graph-dotnet.md).
* Läs mer om hur du [frågar diagram i Azure Cosmos DB med hjälp av Gremlin](gremlin-support.md).
