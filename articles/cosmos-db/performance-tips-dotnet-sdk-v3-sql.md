---
title: Azure Cosmos DB prestanda tips för .NET SDK v3
description: Lär dig hur du kan använda klient konfigurations alternativ för att förbättra Azure Cosmos DB .NET v3 SDK-prestanda.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: jawilley
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 06fb087744ff4ecd96bee7a26e4a796e87866322
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433683"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Prestandatips för Azure Cosmos DB och .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Asynkron Java-SDK v2](performance-tips-async-java.md)
> * [Synkron Java-SDK v2](performance-tips-java.md)

Azure Cosmos DB är en snabb och flexibel distribuerad databas som skalar sömlöst med garanterade latens-och data flödes nivåer. Du behöver inte göra större ändringar i arkitekturen eller skriva komplex kod för att skala databasen med Azure Cosmos DB. Att skala upp och ned är lika enkelt som att göra ett enda API-anrop. Läs mer i [etablera data](how-to-provision-container-throughput.md) flöde för behållare eller [etablera databas data flöde](how-to-provision-database-throughput.md). 

Eftersom Azure Cosmos DB nås via nätverks anrop kan du göra optimeringar på klient sidan för att uppnå högsta prestanda när du använder [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Om du försöker förbättra databasens prestanda bör du tänka på vilka alternativ som visas i följande avsnitt.

## <a name="hosting-recommendations"></a>Värd rekommendationer

**Använd Windows 64-bitar i stället för Linux-eller Windows 32-bitars värd bearbetning för frågor intensiva arbets belastningar**

Vi rekommenderar Windows 64-bitars värd bearbetning för bättre prestanda. SQL-SDK: n innehåller en intern ServiceInterop.dll för att analysera och optimera frågor lokalt. ServiceInterop.dll stöds endast på Windows x64-plattformen. 

För Linux och andra plattformar som inte stöds, där ServiceInterop.dll inte är tillgänglig, görs ytterligare ett nätverks anrop till gatewayen för att hämta den optimerade frågan. 

De fyra program typerna som anges här använder 32-bitars värd bearbetning som standard. Om du vill ändra värd bearbetningen till 64-bitars bearbetning för din program typ gör du följande:

- **För körbara program**: i fönstret **projekt egenskaper** går du till fönstret **skapa** och anger [plattforms målet](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) till **x64**.

- **För VSTest-baserade test projekt**: på **test** -menyn i Visual Studio väljer du **testa**  >  **test inställningar** och anger sedan **standard processor arkitekturen** till **x64**.

- **För lokalt distribuerade ASP.net-webb program**: Välj **verktyg**  >  **alternativ**  >  **projekt och lösningar**  >  **webb projekt** och välj sedan **Använd 64-bitars versionen av IIS Express för webbplatser och projekt**.

- **För ASP.net webb program som distribueras på Azure**: i Azure Portal väljer du **64-bitars** plattformen i **program inställningar**.

> [!NOTE] 
> Som standard har nya Visual Studio-projekt angetts till **valfri processor**. Vi rekommenderar att du ställer in projektet på **x64** så att det inte växlar till **x86**. Ett projekt som är inställt på **valfri CPU** kan enkelt växla till **x86** om ett x86-enda beroende läggs till.<br/>
> ServiceInterop.dll-filen måste finnas i den mapp som SDK-DLL-filen körs från. Detta bör endast vara en risk om du manuellt kopierar dll: er eller har anpassade bygg-och distributions system.
    
**Aktivera skräp insamling på Server Sidan**

Att minska frekvensen skräp insamling kan i vissa fall hjälpa till. I .NET anger du [gcServer](/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) till `true` .

**Skala ut din klient arbets belastning**

Om du testar med höga data flödes nivåer eller priser som är större än 50 000 enheter för programbegäran per sekund (RU/s) kan klient programmet bli en Flask hals i arbets belastningen. Detta beror på att datorn kan leda till processor-eller nätverks användning. Om du når den här punkten kan du fortsätta att skicka det Azure Cosmos DB kontot ytterligare genom att skala ut dina klient program över flera servrar.

> [!NOTE] 
> Hög CPU-användning kan orsaka ökad latens och begär timeout-undantag.

## <a name="networking"></a>Nätverk
<a id="direct-connection"></a>

**Anslutnings princip: Använd direkt anslutnings läge**

.NET v3 SDK standard anslutnings läge är direkt. Du konfigurerar anslutnings läget när du skapar `CosmosClient` instansen i `CosmosClientOptions` .  Mer information om olika anslutnings alternativ finns i artikeln [anslutnings lägen](sql-sdk-connection-modes.md) .

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

**Tillfällig portöverbelastning**

Om du ser en hög anslutnings volym eller hög port användning på dina instanser måste du först kontrol lera att klient instanserna är singleton. Med andra ord bör klient instanserna vara unika för programmets livs längd.

När den körs på TCP-protokollet optimeras klienten för svars tider med hjälp av anslutningar med lång livs längd. Detta är i motsats med HTTPS-protokollet, vilket avslutar anslutningarna efter två minuters inaktivitet.

I scenarier där du har sparse-åtkomst, och om du ser att ett högre antal anslutningar jämfört med åtkomst till gateway-läge, kan du:

* Konfigurera egenskapen [CosmosClientOptions. PortReuseMode](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) till `PrivatePortPool` (gällande Framework-versioner 4.6.1 och senare och .net Core-versioner 2,0 och senare). Med den här egenskapen kan SDK använda en liten pool av tillfälliga portar för olika Azure Cosmos DB slut punkter för mål.
* Konfigurera egenskapen [CosmosClientOptions. IdleConnectionTimeout](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) som är större än eller lika med 10 minuter. De rekommenderade värdena är från 20 minuter till 24 timmar.

<a id="same-region"></a>

**Samordna-klienter i samma Azure-region för prestanda.**

Placera eventuella program som anropar Azure Cosmos DB i samma region som Azure Cosmos DBs databasen när det är möjligt. Här är en ungefärlig jämförelse: anrop till Azure Cosmos DB inom samma region slutar inom 1 millisekund (MS) till 2 MS, men fördröjningen mellan västra USA och östra kust är över 50 ms. Svars tiden kan variera från begäran till begäran, beroende på den väg som tas av begäran när den skickas från klienten till Azure Data Center-gränser. 

Du kan få lägsta möjliga fördröjning genom att se till att det anropande programmet finns i samma Azure-region som den etablerade Azure Cosmos DB slut punkten. En lista över tillgängliga regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Samordna-klienter i samma region." border="false":::

   <a id="increase-threads"></a>

**Öka antalet trådar/aktiviteter**

Eftersom anrop till Azure Cosmos DB görs via nätverket kan du behöva variera graden av samtidighet för dina begär Anden så att klient programmet ägnar lång tid åt att vänta mellan begär Anden. Om du till exempel använder ett [parallellt .net-aktivitets bibliotek](/dotnet/standard/parallel-programming/task-parallel-library-tpl)skapar du i ordningen på hundratals aktiviteter som läser från eller skriver till Azure Cosmos dB.

**Aktivera accelererat nätverk**
 
För att minska svars tid och CPU-Darr rekommenderar vi att du aktiverar accelererat nätverk på de virtuella klient datorerna. Mer information finns i [skapa en virtuell Windows-dator med accelererat nätverk](../virtual-network/create-vm-accelerated-networking-powershell.md) eller [skapa en virtuell Linux-dator med accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK-användning

**Installera den senaste SDK: n**

Azure Cosmos DB SDK: er har ständigt förbättrats för att ge bästa möjliga prestanda. Se [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md)för att fastställa de senaste SDK: er och gransknings förbättringarna.

**Använda Stream-API: er**

[.NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3) innehåller stream-API: er som kan ta emot och returnera data utan att serialiseras. 

Program på mellan nivå som inte konsumerar svar direkt från SDK, men som vidarebefordrar dem till andra program nivåer kan dra nytta av data Ströms-API: erna. Exempel på data Ströms hantering finns i exemplen för [objekt hantering](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) .

**Använd en singleton Azure Cosmos DB-klient för programmets livs längd**

Varje `CosmosClient` instans är tråd säker och utför effektiv anslutnings hantering och cachelagring av adresser när den körs i direkt läge. För att möjliggöra effektiv anslutnings hantering och bättre prestanda för SDK-klienten, rekommenderar vi att du använder en enda instans per `AppDomain` för programmets livs längd.

När du arbetar med Azure Functions bör instanserna också följa de befintliga [rikt linjerna](../azure-functions/manage-connections.md#static-clients) och underhålla en enda instans.

<a id="max-connection"></a>

**Inaktivera innehålls svar vid Skriv åtgärder**

För arbets belastningar som har tung Create-nyttolaster anger du `EnableContentResponseOnWrite` alternativet för begäran till `false` . Tjänsten kommer inte längre att returnera den skapade eller uppdaterade resursen till SDK: n. Normalt, eftersom programmet har det objekt som skapas, behöver inte tjänsten returnera det. Huvud värden är fortfarande tillgängliga, t. ex. en begär ande avgift. Att inaktivera innehålls svaret kan förbättra prestandan, eftersom SDK inte längre behöver allokera minne eller serialisera bröd texten i svaret. Det minskar också nätverkets bandbredds användning för att ytterligare hjälpa till med prestanda.  

```csharp
ItemRequestOptions requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**Aktivera Mass optimering för data flöde i stället för svars tid**

Aktivera *Mass bearbetning* för scenarier där arbets belastningen kräver stor mängd data flöde och svars tiden är inte lika viktig. Mer information om hur du aktiverar Mass funktionen och lär dig vilka scenarier den ska användas för finns i [Introduktion till Mass support](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk).

**Öka System.Net MaxConnections per värd när du använder Gateway-läge**

Azure Cosmos DB begär Anden görs via HTTPS/REST när du använder Gateway-läge. De är underkastade standard anslutnings gränsen per värdnamn eller IP-adress. Du kan behöva ange `MaxConnections` ett högre värde (från 100 till 1 000) så att klient biblioteket kan använda flera samtidiga anslutningar till Azure Cosmos dB. I .NET SDK-1.8.0 och senare är standardvärdet för [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) 50. Om du vill ändra värdet kan du ange [`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) ett högre värde.

**Justera parallella frågor för partitionerade samlingar**

SQL .NET SDK stöder parallella frågor, vilket gör att du kan fråga en partitionerad behållare parallellt. Mer information finns i [kod exempel](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs) för att arbeta med SDK: er. Parallella frågor är utformade för att ge bättre svars tid och data flöde än deras serie motsvarighet. 

Parallella frågor ger två parametrar som du kan justera för att passa dina behov: 

- **MaxConcurrency**: styr det högsta antalet partitioner som kan frågas parallellt.

   Parallell fråga fungerar genom att fråga flera partitioner parallellt. Men data från en enskild partition hämtas seriellt i förhållande till frågan. Inställningen `MaxConcurrency` i [SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3) till antalet partitioner har bästa möjliga chans att nå den mest utförda frågan, förutsatt att alla andra system villkor är desamma. Om du inte vet antalet partitioner kan du ange graden av parallellitet till ett högt tal. Systemet väljer det lägsta (antal partitioner, indata från användaren) som graden av parallellitet.

    Parallella frågor ger flest fördelar om data är jämnt fördelade över alla partitioner med avseende på frågan. Om den partitionerade samlingen är partitionerad, så att alla eller de flesta data som returneras av en fråga är koncentrerade i några partitioner (en partition är det värsta fallet), kommer dessa partitioner att översätta prestandan hos frågan.
   
- **MaxBufferedItemCount**: styr antalet förhämtade resultat.

   Parallell fråga är utformad för att hämta resultat när den aktuella gruppen med resultat bearbetas av klienten. Den här för hämtningen hjälper till att förbättra den övergripande svars tiden för en fråga. `MaxBufferedItemCount`Parametern begränsar antalet i förväg hämtade resultat. Ange `MaxBufferedItemCount` till det förväntade antalet returnerade resultat (eller ett högre antal) för att tillåta att frågan tar emot den maximala fördelen med för hämtning.

   För hämtning fungerar på samma sätt oavsett graden av parallellitet och det finns en enda buffert för data från alla partitioner.  

**Implementera backoff med RetryAfter-intervall**

Under prestanda testningen bör du öka belastningen tills en låg frekvens av begär Anden begränsas. Om förfrågningarna är begränsade bör klient programmet inaktivera begränsning för det Server-angivna återförsöksintervallet. Genom att respektera backoff ser du till att du ägnar en liten stund åt att vänta mellan återförsök. 

Mer information finns i [RetryAfter](/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter#Microsoft_Azure_Cosmos_CosmosException_RetryAfter).
    
Det finns en mekanism för att logga ytterligare diagnostikinformation och felsöka latens problem, som du ser i följande exempel. Du kan logga den diagnostiska strängen för förfrågningar som har en högre Läs fördröjning. Den infångade diagnostikprogrammet hjälper dig att förstå hur många gånger du fick ett *429* -fel för en specifik begäran.

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**Öka antalet trådar/aktiviteter**

Se [öka antalet trådar/aktiviteter](#increase-threads) i avsnittet nätverk i den här artikeln.

## <a name="indexing-policy"></a>Indexeringsprincip
 
**Utesluta sökvägar som inte används från indexering för att få snabbare skrivning**

Med Azure Cosmos DB indexerings principen kan du också ange vilka dokument Sök vägar som ska tas med eller undantas från indexering genom att använda indexerings Sök vägar (IndexingPolicy. IncludedPaths och IndexingPolicy. ExcludedPaths). 

Om du bara indexerar de sökvägar du behöver kan du förbättra skriv prestanda, minska RU-avgifterna för Skriv åtgärder och minska index lagringen för scenarier där fråge mönstren är kända i förväg. Detta beror på att indexerings kostnader korrelerar direkt med antalet unika sökvägar. Följande kod visar till exempel hur du undantar en hel del av dokumenten (ett under träd) från indexering med hjälp av jokertecknet "*":

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

Mer information finns i [Azure Cosmos DB indexerings principer](index-policy.md).

## <a name="throughput"></a>Dataflöde
<a id="measure-rus"></a>

**Mått och justering för lägre RU/s-användning**

Azure Cosmos DB erbjuder en omfattande uppsättning databas åtgärder. Dessa åtgärder omfattar Relations-och hierarkiska frågor med UDF-filer (Universal disk format), lagrade procedurer och utlösare, allt som körs på dokumenten i en databas samling. 

Kostnaderna för var och en av dessa åtgärder varierar beroende på processor, IO och minne som krävs för att slutföra åtgärden. I stället för att tänka på och hantera maskin varu resurser kan du tänka på en enhet för begäran som ett enda mått för de resurser som krävs för att utföra olika databas åtgärder och betjäna en programbegäran.

Data flödet har allokerats baserat på antalet enheter för [programbegäran](request-units.md) som angetts för varje behållare. Den begärda enhets förbrukningen utvärderas som en enhet per sekund. Program som överskrider den allokerade enhets hastigheten för deras behållare är begränsade tills hastigheten sjunker under den allokerade nivån för behållaren. Om ditt program kräver en högre data flödes nivå kan du öka data flödet genom att tillhandahålla ytterligare enheter för programbegäran.

En frågas komplexitet påverkar hur många enheter för programbegäran som används för en åtgärd. Antalet predikat, typen av predikat, antalet UDF-filer och storleken på käll data uppsättningen påverkar alla frågor om åtgärder.

Om du vill mäta omkostnaderna för en åtgärd (skapa, uppdatera eller ta bort) kan du kontrol lera huvudet [x-MS-Request-avgift](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (eller motsvarande `RequestCharge` egenskap i `ResourceResponse\<T>` eller `FeedResponse\<T>` i .NET SDK) för att mäta antalet enheter för programbegäran som används av åtgärderna:

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Den begär ande avgiften som returneras i den här rubriken är en bråkdel av ditt allokerade data flöde (d.v.s. 2 000 RU/s). Om exempelvis föregående fråga returnerar 1 000 1 KB-dokument, är kostnaden för åtgärden 1 000. Det innebär att servern i en sekund endast besitter två sådana begär Anden innan den bedöms senare. Mer information finns i [enheter för programbegäran](request-units.md) och [Kalkylatorn för begär ande](https://www.documentdb.com/capacityplanner)enheter.
<a id="429"></a>

**Hastighets begränsning/begär ande frekvens för stor**

När en klient försöker överskrida det reserverade data flödet för ett konto, finns det ingen prestanda försämring på servern och ingen användning av data flödes kapaciteten utöver den reserverade nivån. Server-förebyggande syfte avslutar begäran med RequestRateTooLarge (HTTP-status kod 429). Den returnerar ett [x-MS-retry-efter-MS-](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) huvud som anger hur lång tid i millisekunder som användaren måste vänta innan begäran försöker igen.

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

SDK: erna fångar alla implicita dessa svar, med den server-angivna återförsöket-efter-rubriken och gör om begäran. Om ditt konto inte kan nås samtidigt av flera klienter kommer nästa försök att lyckas.

Om du har mer än en klient ackumulerad på ett konsekvent sätt över begär ande frekvensen, kanske standard antalet nya försök som för närvarande är inställt på 9 internt av klienten inte räcker. I det här fallet genererar klienten en CosmosException med status kod 429 i programmet. 

Du kan ändra standard antalet nya försök genom att ställa in `RetryOptions` på `CosmosClientOptions` instansen. Som standard returneras CosmosException med status kod 429 efter en ackumulerad vänte tid på 30 sekunder om begäran fortsätter att köras över begär ande frekvensen. Det här felet returneras även om det aktuella antalet nya försök är mindre än det högsta antalet omförsök, om det aktuella värdet är standardvärdet 9 eller ett användardefinierat värde.

Det automatiska återförsöket hjälper till att förbättra återhämtning och användbarhet för de flesta program. Men det kanske inte är det bästa beteendet när du utför prestandatest, särskilt när du mäter svars tid. Den klientbaserade svars tiden kommer att inaktive ras om experimentet träffar Server begränsningen och gör att klient-SDK: n kan försöka tyst igen. För att undvika fördröjningar vid prestanda experiment kan du mäta den avgift som returneras av varje åtgärd och se till att förfrågningarna fungerar under den reserverade begär ande frekvensen. 

Mer information finns i [enheter för programbegäran](request-units.md).

**För högre data flöde kan du utforma för mindre dokument**

Begär ande avgiften (dvs. bearbetnings kostnaden för bearbetning) för en angiven åtgärd motsvarar direkt till storleken på dokumentet. Åtgärder i stora dokument kostar mer än åtgärder på små dokument.

## <a name="next-steps"></a>Nästa steg
Ett exempel program som används för att utvärdera Azure Cosmos DB för scenarier med hög prestanda på ett fåtal klient datorer finns i [prestanda-och skalnings testning med Azure Cosmos DB](performance-testing.md).

Mer information om hur du utformar programmet för skalning och höga prestanda finns i [partitionering och skalning i Azure Cosmos DB](partitioning-overview.md).