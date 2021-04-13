---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & resurser'
description: Lär dig allt om SQL Node.js API och SDK, inklusive lanseringsdatum, tillbakagångar och ändringar som görs mellan varje version av Azure Cosmos DB Node.js SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: a3e21abe2f4ed24726256689af16b48ed6721ce8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366155"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK för SQL API: Information och resurser
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Asynkron Java-SDK v2](sql-api-sdk-async-java.md)
> * [Synkron Java-SDK v2](sql-api-sdk-java.md)
> * [Spring-data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring-data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP Connector](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resursprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Masskörningsbibliotek – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Masskörningsbibliotek – Java](sql-api-sdk-bulk-executor-java.md)

|Resurs  |Länk  |
|---------|---------|
|Ladda ned SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API-dokumentation  |  [JavaScript SDK-referensdokumentation](/javascript/api/%40azure/cosmos/)
|Installationsanvisningar för SDK  |  [Installationsanvisningar](https://github.com/Azure/azure-sdk-for-js)
|Bidra till SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Exempel | [Node.js kodexempel](sql-api-nodejs-samples.md)
| Självstudiekurs för att komma igång | [Kom igång med JavaScript SDK](sql-api-nodejs-get-started.md)
| Självstudiekurs om webbapp | [Skapa en Node.js-webbapp med Azure Cosmos DB](sql-api-nodejs-application.md)
| Aktuell plattform som stöds | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) – SDK Version 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) – SDK Version 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) – SDK Version 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) – SDK Version 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)– SDK Version 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)– SDK Version 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)– SDK Version 1.x.x

## <a name="release-notes"></a>Viktig information

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* Ange standardvärdet ResponseContinuationTokenLimitInKB till 1 kB. Som standard begränsar vi detta till 1 kB för att undvika långa rubriker (Node.js har en global storleksgräns för sidhuvuden). En användare kan ange det här fältet för att tillåta längre rubriker, vilket kan hjälpa backend att optimera frågekörningen.
* Ta bort disableSSLVerification. Det här alternativet har nya alternativ som beskrivs [i #388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* Tillåt initialHeaders att uttryckligen ange partitionsnyckelrubrik
* Använd package.json#files för att förhindra att överflödiga filer publiceras
* Åtgärda sorteringsfel för routningskarta på äldre version av node+v8
* Åtgärdar fel när användaren tillhandahåller partiella återförsöksalternativ

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* Förhindra att webpack löser moduler med namnet med kräv

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* Åtgärdar en lång utestående bugg där RU:er alltid rapporterades som 0 för mängdfrågor

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 version v3! 🎉 Många nya funktioner, felkorrigeringar och några större ändringar. De främsta målen med den här versionen:

* Implementera viktiga nya funktioner
  * DISTINCT-frågor
  * LIMIT/OFFSET-frågor
  * Användaren kan avbryta begäranden
* Uppdatera till den senaste Cosmos REST API versionen där alla containrar har obegränsad skala
* Gör det enklare att använda Cosmos från webbläsaren
* Bättre anpassa dig till de nya riktlinjerna för Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Migreringsguide för större ändringar
##### <a name="improved-client-constructor-options"></a>Förbättrade alternativ för klientkonstruktorn

Konstruktoralternativen har förenklats:

* masterKey har bytt namn och flyttats till den översta nivån
* Egenskaper som tidigare fanns under options.auth har flyttats till den översta nivån

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>Förenklat QueryIterator-API
I v2 fanns det många olika sätt att iterera eller hämta resultat från en fråga. Vi har försökt förenkla v3-API:et och ta bort liknande eller duplicerade API:er:

* Ta bort iterator.next() och iterator.current(). Använd fetchNext() för att hämta sidor med resultat.
* Ta bort iterator.forEach(). Använd asynkrona iteratorer i stället.
* iterator.exenext() har bytt namn till iterator.fetchNext()
* iterator.toArray() har bytt namn till iterator.fetchAll()
* Sidor är nu rätt svarsobjekt i stället för vanliga JS-objekt
* const container = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Fasta containrar partitioneras nu
Cosmos-tjänsten stöder nu partitionsnycklar på alla containrar, inklusive de som tidigare har skapats som fasta containrar. v3 SDK uppdateras till den senaste API-versionen som implementerar den här ändringen, men den bryts inte. Om du inte tillhandahåller en partitionsnyckel för åtgärder kommer vi som standard att använda en systemnyckel som fungerar med alla dina befintliga containrar och dokument.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert har tagits bort för lagrade procedurer
Tidigare upsert tilläts för icke-partitionerade samlingar, men med API-versionsuppdateringen partitioneras alla samlingar så vi tog bort den helt.

##### <a name="item-reads-will-not-throw-on-404"></a>Objektläsningar kastar inte på 404
const container = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Standardskrivning för flera regioner
SDK:n skriver nu till flera regioner som standard om din Cosmos-konfiguration stöder det. Detta var tidigare inanmält beteende.

##### <a name="proper-error-objects"></a>Rätt felobjekt
Misslyckade begäranden ylar nu fel eller underklasser av fel. Tidigare utser de vanliga JS-objekt.

#### <a name="new-features"></a>Nya funktioner
##### <a name="user-cancelable-requests"></a>Begäranden som kan avbrytas av användaren
Flytten att hämta internt gör att vi kan använda webbläsarens AbortController-API för att stödja åtgärder som kan avbrytas av användaren. När det gäller åtgärder där flera begäranden eventuellt pågår (t.ex. frågor mellan partitioner) avbryts alla begäranden om åtgärden. Moderna webbläsaranvändare har redan AbortController. Node.js måste använda ett polyfill-bibliotek

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Ange dataflöde som en del av åtgärden för att skapa db/container
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Genereringen av huvudtoken delades upp i ett nytt bibliotek, @azure/cosmos-sign . Alla som anropar Cosmos REST API direkt kan använda detta för att signera rubriker med samma kod som vi anropar i @azure/cosmos .

##### <a name="uuid-for-generated-ids"></a>UUID för genererade ID:er
v2 hade anpassad kod för att generera objekt-ID:er. Vi har bytt till det välkända och underhållna community-biblioteket uuid.

##### <a name="connection-strings"></a>Anslutningssträngar
Nu är det möjligt att skicka en anslutningssträng som kopierats från Azure Portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Förbättrad webbläsarupplevelse
Även om det var möjligt att använda v2 SDK i webbläsaren var det inte en perfekt upplevelse. Du behövde polyfylla flera node.js inbyggda bibliotek och använda en paketerare som webpack eller Bundle. Med v3 SDK blir upplevelsen mycket bättre för webbläsaranvändare.

* Ersätt internt begärande med fetch (#245)
* Ta bort användningen av buffert (#330)
* Ta bort inbyggd nodanvändning till förmån för universella paket/API:er (#328)
* Växla till node-abort-controller (#294)

#### <a name="bug-fixes"></a>Felkorrigeringar
* Åtgärda test av erbjudande för läsning och återförsökserbjudande (#224)
* Åtgärda EnableEndpointDiscovery (#207)
* Åtgärda saknade RU:er på sidnumrerade resultat (#360)
* Expandera SQL-frågeparametertyp (#346)
* Lägg till ttl i ItemDefinition (#341)
* Åtgärda CP-frågemått (#311)
* Lägg till activityId i FeedResponse (#293)
* Växla _ts från sträng till tal (#252)(#295)
* Åtgärda aggregering av begärandeavgift (#289)
* Tillåt tomma partitionsnycklar för strängar (#277)
* Lägg till sträng till frågetypen konflikt (#237)
* Lägg till uniqueKeyPolicy i containern (#234)

#### <a name="engineering-systems"></a>Tekniksystem
Inte alltid de mest synliga ändringarna, men de hjälper vårt team att leverera bättre kod, snabbare.

* Använda sammanslagning för produktionsbyggen (#104)
* Uppdatera till Typescript 3.5 (#327)
* Konvertera till TS-projektreferenser. Extrahera testmappen (#270)
* Aktivera noUnusedLocals och noUnusedParameters (#275)
* Azure Pipelines YAML för CI-byggen (#298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* Inga kodändringar. Åtgärdar ett problem där vissa extra filer ingick i 2.1.4-paketet.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* Åtgärda regional redundans i återförsöksprincipen
* Åtgärda changeFeed-egenskapen hasMoreResults
* Uppdateringar av dev-beroenden
* Lägga till PolicheckExclusions.txt

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Växla _ts typ från sträng till tal
* Åtgärda standardindexeringstester
* Backport uniqueKeyPolicy till v2
* Demo- och demofelsökningskorrigeringar

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Korrigeringar av bakåtportserbjudande från v3-grenen
* Åtgärda bugg i signaturen executeNext()-typ
* Stavfelkorrigeringar

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Skapa omstrukturering. Gör det möjligt att hämta SDK-versionen vid byggtiden.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Nya funktioner
* Stöd för ChangeFeed (#196) har lagts till
* Datatypen MultiPolygon har lagts till för indexering (#191)
* Lägg till "nyckel"-egenskapen i konstruktorn som alias för masterKey (#202)

#### <a name="fixes"></a>Korrigeringar
* Åtgärda fel där next() returnerade fel värde på iteratorn

#### <a name="engineering-improvements"></a>Tekniska förbättringar
* Lägga till integrationstest för typescript-förbrukning (#199)
* Aktivera installation direkt från GitHub (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Lägger till gränssnitt för noden Agenttyp. Typescript-användare behöver inte längre installera @types/node som ett beroende
* Önskade platser respekteras nu korrekt
* Förbättringar av att bidra till utvecklardokumentationen
* Olika skrivfelkorrigeringar

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* Åtgärdar problem med typdefinition som introducerades i 2.0.3

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* Ta bort `big-integer` beroende
* Växla till referensdirektiv för typen AsyncIterable. Typescript-användare behöver inte längre anpassa sin "lib"-inställning.
* Stavfelkorrigeringar

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* Åtgärda readme-länkar

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Åtgärda implementering av återförsöksgränssnitt

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* GA för version 2.0.0 av JavaScript SDK
* Stöd har lagts till för skrivningar i flera regioner.

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* RC1 av version 2.0.0 av JavaScript SDK för offentlig förhandsversion.
* Ny objektmodell med CosmosClient på den översta nivån och metoder uppdelade över relevanta klasser för databas, container och objekt. 
* Stöd för [promises](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK har konverterats till TypeScript.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* npm-dokumentationen har åtgärdats.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* Stöd har lagts till för standardförsök vid anslutningsproblem.
* Stöd har lagts till för att läsa samlingsändringsflödet.
* Åtgärdade bugg för sessionskonsekvens som tillfälligt orsakade att "lässessionen inte var tillgänglig".
* Stöd för frågemått har lagts till.
* Ändrade http-agentens maximala antal anslutningar.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Uppdaterad dokumentation för att referera Azure Cosmos DB i stället för Azure DocumentDB.
* Stöd för proxyUrl-inställningen har lagts till i ConnectionPolicy.

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Mindre korrigering för ärendekänsliga filsystem.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Lägger till stöd för sessionskonsekvens.
* Den här SDK-versionen kräver den senaste versionen [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator).

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Split proofed cross partition queries (Dela upp proofed cross partition queries).
* Lägger till stöd för resurslänk med inledande och avslutande snedstreck (och motsvarande tester).

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    npm-dokumentationen har åtgärdats.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* En bugg har åtgärdats i executeStoredProcedure där dokument som var inblandade hade särskilda Unicode-tecken (LS, PS).
* En bugg i hanteringen av dokument med Unicode-tecken i partitionsnyckeln har åtgärdats.
* Stöd har åtgärdats för att skapa samlingar med namnmediet. GitHub-#114.
* Stöd för behörighetsauktoriseringstoken har åtgärdats. GitHub-#178.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Stöd har lagts till för en [ny konsekvensnivå](consistency-levels.md) som kallas ConsistentPrefix.
* Stöd för UriFactory har lagts till.
* Ett Unicode-stödfel har åtgärdats. GitHub-#171.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Stöd har lagts till för sammansättningsfrågor (COUNT, MIN, MAX, SUM och AVG).
* Alternativet för att kontrollera grad av parallellitet för frågor mellan partitioner har lagts till.
* Alternativet för att inaktivera TLS-verifiering vid körning mot en Azure Cosmos DB emulatorn har lagts till.
* Lägre minsta dataflöde för partitionerade samlingar från 10 100 RU/s till 2 500 RU/s.
* Buggen för fortsättningstoken för en enskild partitionssamling har åtgärdats. GitHub-#107.
* Buggen executeStoredProcedure i hantering 0 som enskild parameter har åtgärdats. GitHub-#155.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* Användaragentens sidhuvud har åtgärdats för att inkludera SDK-versionen.
* Mindre kodrensning.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* Inaktivera TLS-verifiering när du använder SDK för att rikta emulatorn (värdnamn = localhost).
* Stöd har lagts till för att aktivera skriptloggning under körning av lagrad procedur.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Stöd har lagts till för parallella frågor mellan partitioner.
* Stöd har lagts till för TOP/ORDER BY-frågor för partitionerade samlingar.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Stöd för återförsöksprincip för begränsade begäranden har lagts till. (Begränsade begäranden får ett undantag med för hög begärandefrekvens, felkod 429.) Som standard Azure Cosmos DB nio gånger för varje begäran när felkod 429 påträffas och respekterar retryAfter-tiden i svarshuvudet. En fast tid för återförsöksintervall kan nu anges som en del av retryOptions-egenskapen för ConnectionPolicy-objektet om du vill ignorera retryAfter-tiden som returneras av servern mellan återförsöken. Azure Cosmos DB väntar nu i högst 30 sekunder för varje begäran som begränsas (oavsett antal återförsök) och returnerar svaret med felkoden 429. Den här gången kan också åsidosättas i egenskapen RetryOptions på ConnectionPolicy-objektet.
* Cosmos DB returnerar nu x-ms-throttle-retry-count och x-ms-throttle-retry-wait-time-ms som svarshuvuden i varje begäran för att ange antalet omförsök för begränsning och den kumulativa tid då begäran väntade mellan återförsöken.
* Klassen RetryOptions har lagts till, vilket exponerar egenskapen RetryOptions i klassen ConnectionPolicy som kan användas för att åsidosätta några av standardalternativen för återförsök.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Stöd för databaskonton för flera regioner har lagts till.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Stöd för TTL-funktionen (Time To Live) för dokument har lagts till.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Implementerat [partitionerade samlingar](partitioning-overview.md) [och användardefinierade prestandanivåer](performance-levels.md).

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* Åtgärdade ett Fel i RangePartitionResolver.resolveForRead där det inte returnerade länkar på grund av en felaktig sammansning av resultaten.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* HashPartitionResolver resolveForRead(har åtgärdats): När ingen partitionsnyckel angavs utlöste undantaget, i stället för att returnera en lista över alla registrerade länkar.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* Åtgärdar [#100](https://github.com/Azure/azure-documentdb-node/issues/100) – Dedikerad HTTPS-agent: Undvik att ändra den globala agenten i Azure Cosmos DB syfte. Använd en dedikerad agent för alla lib-begäranden.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Åtgärdar [problem #81](https://github.com/Azure/azure-documentdb-node/issues/81) – hantera bindestreck i medie-ID:er korrekt.

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* Åtgärdar [problemet #95 –](https://github.com/Azure/azure-documentdb-node/issues/95) Varning om EventEmitter-lyssnarläcka.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Åtgärdar [problemet #92](https://github.com/Azure/azure-documentdb-node/issues/90) – byt namn på mappens hash till hash för fallkänsliga system.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Implementera stöd för horisontell partitionering genom att lägga till hash& partitions matchare för intervall.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementera Upsert. Nya upsertXXX-metoder på documentClient.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Överhoppad för att anpassa versionsnummer till andra SDK:er.

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* Dela Q promises wrapper till ny lagringsplats.
* Uppdatera till paketfilen för npm-registret.

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* Implementerar ID-baserad routning.
* Åtgärdar [problem #49](https://github.com/Azure/azure-documentdb-node/issues/49) – den aktuella egenskapen står i konflikt med metoden current().

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Stöd för geospatialt index har lagts till.
* Validerar ID-egenskapen för alla resurser. ID:er för resurser får inte innehålla ?, /, #,  &#47;&#47;, tecken eller sluta med blanksteg.
* Lägger till det nya huvudet "förlopp för indextransformering" i ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementerar V2-indexeringsprincip.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* Problem [#40](https://github.com/Azure/azure-documentdb-node/issues/40) – Implementerat konfigurationer för snint och grunt i kärn- och promise-SDK:n.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Problem [#45](https://github.com/Azure/azure-documentdb-node/issues/45) – Promises-wrapper innehåller inte rubrik med fel.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Implementerade möjligheten att fråga efter konflikter genom att lägga till readConflicts, readConflictAsync och queryConflicts.
* Uppdaterad API-dokumentation.
* Problem [#41](https://github.com/Azure/azure-documentdb-node/issues/41) – felet client.createDocumentAsync.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Lanseringsdatum & tillbaka

Microsoft tillhandahåller ett meddelande minst **12** månader innan ett SDK dras tillbaka för att underlätta övergången till en nyare version som stöds. Nya funktioner, verktyg och optimeringar läggs endast till i den aktuella SDK-versionen. Därför rekommenderar vi att du alltid uppgraderar till den senaste SDK-versionen så snart som möjligt.

| Version | Utgivningsdatum | Förfallodatum |
| --- | --- | --- |
| [3.1.0](#3.1.0) |Den 26 juli 2019 |--- |
| [3.0.4](#3.0.4) |Den 22 juli 2019 |--- |
| [3.0.3](#3.0.3) |Den 17 juli 2019 |--- |
| [3.0.2](#3.0.2) |Den 9 juli 2019 |--- |
| [3.0.0](#3.0.0) |Den 28 juni 2019 |--- |
| [2.1.5](#2.1.5) |Den 20 mars 2019 |--- |
| [2.1.4](#2.1.4) |Den 15 mars 2019 |--- |
| [2.1.3](#2.1.3) |Den 8 mars 2019 |--- |
| [2.1.2](#2.1.2) |Den 28 januari 2019 |--- |
| [2.1.1](#2.1.1) |Den 5 december 2018 |--- |
| [2.1.0](#2.1.0) |4 december 2018 |--- |
| [2.0.5](#2.0.5) |Den 7 november 2018 |--- |
| [2.0.4](#2.0.4) |Den 30 oktober 2018 |--- |
| [2.0.3](#2.0.3) |Den 30 oktober 2018 |--- |
| [2.0.2](#2.0.2) |Den 10 oktober 2018 |--- |
| [2.0.1](#2.0.1) |25 september 2018 |--- |
| [2.0.0](#2.0.0) |September 24, 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |Den 2 augusti 2018 |--- |
| [1.14.4](#1.14.4) |Den 3 maj 2018 |Den 30 augusti 2020 |
| [1.14.3](#1.14.3) |Den 3 maj 2018 |Den 30 augusti 2020 |
| [1.14.2](#1.14.2) |Den 21 december 2017 |Den 30 augusti 2020 |
| [1.14.1](#1.14.1) |Den 10 november 2017 |Den 30 augusti 2020 |
| [1.14.0](#1.14.0) |Den 9 november 2017 |Den 30 augusti 2020 |
| [1.13.0](#1.13.0) |Den 11 oktober 2017 |Den 30 augusti 2020 |
| [1.12.2](#1.12.2) |Den 10 augusti 2017 |Den 30 augusti 2020 |
| [1.12.1](#1.12.1) |Den 10 augusti 2017 |Den 30 augusti 2020 |
| [1.12.0](#1.12.0) |10 maj 2017 |Den 30 augusti 2020 |
| [1.11.0](#1.11.0) |Den 16 mars 2017 |Den 30 augusti 2020 |
| [1.10.2](#1.10.2) |Den 27 januari 2017 |Den 30 augusti 2020 |
| [1.10.1](#1.10.1) |Den 22 december 2016 |Den 30 augusti 2020 |
| [1.10.0](#1.10.0) |Den 3 oktober 2016 |Den 30 augusti 2020 |
| [1.9.0](#1.9.0) |Den 7 juli 2016 |Den 30 augusti 2020 |
| [1.8.0](#1.8.0) |Den 14 juni 2016 |Den 30 augusti 2020 |
| [1.7.0](#1.7.0) |26 april 2016 |Den 30 augusti 2020 |
| [1.6.0](#1.6.0) |Den 29 mars 2016 |Den 30 augusti 2020 |
| [1.5.6](#1.5.6) |Den 8 mars 2016 |Den 30 augusti 2020 |
| [1.5.5](#1.5.5) |Den 2 februari 2016 |Den 30 augusti 2020 |
| [1.5.4](#1.5.4) |Den 1 februari 2016 |Den 30 augusti 2020 |
| [1.5.2](#1.5.2) |Den 26 januari 2016 |Den 30 augusti 2020 |
| [1.5.2](#1.5.2) |Den 22 januari 2016 |Den 30 augusti 2020 |
| [1.5.1](#1.5.1) |Den 4 januari 2016 |Den 30 augusti 2020 |
| [1.5.0](#1.5.0) |Den 31 december 2015 |Den 30 augusti 2020 |
| [1.4.0](#1.4.0) |Den 6 oktober 2015 |Den 30 augusti 2020 |
| [1.3.0](#1.3.0) |Den 6 oktober 2015 |Den 30 augusti 2020 |
| [1.2.2](#1.2.2) |Den 10 september 2015 |Den 30 augusti 2020 |
| [1.2.1](#1.2.1) |Den 15 augusti 2015 |Den 30 augusti 2020 |
| [1.2.0](#1.2.0) |Den 5 augusti 2015 |Den 30 augusti 2020 |
| [1.1.0](#1.1.0) |Den 9 juli 2015 |Den 30 augusti 2020 |
| [1.0.3](#1.0.3) |Den 4 juni 2015 |Den 30 augusti 2020 |
| [1.0.2](#1.0.2) |Den 23 maj 2015 |Den 30 augusti 2020 |
| [1.0.1](#1.0.1) |Den 15 maj 2015 |Den 30 augusti 2020 |
| [1.0.0](#1.0.0) |Den 8 april 2015 |Den 30 augusti 2020 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Cosmos DB finns på sidan om tjänsten [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).