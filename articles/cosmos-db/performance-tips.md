---
title: Azure Cosmos DB prestanda tips för .NET SDK v2
description: Lär dig mer om klient konfigurations alternativ för att förbättra Azure Cosmos DB .NET v2 SDK-prestanda.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/26/2020
ms.author: sngun
ms.openlocfilehash: bc73292d7ed01468fc31e5a6203a4ba53a6425a2
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505775"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Prestanda tips för Azure Cosmos DB och .NET SDK v2

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Asynkron Java-SDK v2](performance-tips-async-java.md)
> * [Synkron Java-SDK v2](performance-tips-java.md)

Azure Cosmos DB är en snabb och flexibel distribuerad databas som skalar sömlöst med garanterad svars tid och data flöde. Du behöver inte göra större ändringar i arkitekturen eller skriva komplex kod för att skala databasen med Azure Cosmos DB. Att skala upp och ned är lika enkelt som att göra ett enda API-anrop. Mer information finns i [så här etablerar du behållar data flöde](how-to-provision-container-throughput.md) eller [hur du etablerar databas data flöde](how-to-provision-database-throughput.md). Men eftersom Azure Cosmos DB nås via nätverks anrop finns det optimeringar på klient sidan som du kan göra för att uppnå högsta prestanda när du använder [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Om du försöker förbättra databasens prestanda bör du därför överväga följande alternativ:

## <a name="upgrade-to-the-net-v3-sdk"></a>Uppgradera till .NET v3 SDK

[.Net v3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) lanseras. Om du använder .NET v3 SDK, se [prestanda guiden för .net v3](performance-tips-dotnet-sdk-v3-sql.md) för följande information:

- Standardinställningar för direkt TCP-läge
- Stream API-stöd
- Stöd för anpassad serialisering för att tillåta System.Text.JSanvändning
- Integrerad batch och Mass stöd

## <a name="hosting-recommendations"></a>Värd rekommendationer

**Använd Windows 64-bitar i stället för Linux-eller Windows 32-bitars värd bearbetning för frågor intensiva arbets belastningar**

Vi rekommenderar Windows 64-bitars värd bearbetning för bättre prestanda. SQL-SDK: n innehåller en intern ServiceInterop.dll för att analysera och optimera frågor lokalt. ServiceInterop.dll stöds endast på Windows x64-plattformen. För Linux och andra plattformar som inte stöds, där ServiceInterop.dll inte är tillgänglig, görs ytterligare ett nätverks anrop till gatewayen för att hämta den optimerade frågan. Följande typer av program använder 32-bitars värd bearbetning som standard. Om du vill ändra värd bearbetningen till 64-bitars bearbetning följer du dessa steg baserat på typen av program:

- För körbara program kan du ändra värd bearbetningen genom att ange [plattforms målet](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) till **x64**  i fönstret **projekt egenskaper** på fliken **skapa** .

- För VSTest-baserade test projekt kan du ändra värd bearbetningen genom att välja **testa**  >  **standardinställningar**  >  **standard processor arkitektur som x64** på **test** menyn i Visual Studio.

- För lokalt distribuerade ASP.NET-webbprogram kan du ändra värd bearbetningen genom att välja **Använd 64-bitars versionen av IIS Express för webbplatser och projekt** under **verktyg**  >  **alternativ**  >  **projekt och**  >  **webb projekt**för lösningar.

- För ASP.NET-webbprogram som distribueras i Azure kan du ändra värd bearbetningen genom att välja **64-bitars** plattform i **program inställningar** i Azure Portal.

> [!NOTE] 
> Som standard har nya Visual Studio-projekt angetts till **valfri processor**. Vi rekommenderar att du ställer in projektet på **x64** så att det inte växlar till **x86**. Ett projekt som är inställt på **valfri processor** kan enkelt växla till **x86** om ett x86-endast beroende läggs till.<br/>
> ServiceInterop.dll måste finnas i mappen som SDK-DLL-filen körs från. Detta bör endast vara en risk om du manuellt kopierar dll: er eller har anpassade Bygg-/distributions system.
    
**Aktivera skräp insamling på Server sidan (GC)**

Att minska frekvensen skräp insamling kan i vissa fall hjälpa till. I .NET anger du [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) till `true` .

**Skala ut din klient arbets belastning**

Om du testar med höga data flödes nivåer (mer än 50 000 RU/s) kan klient programmet bli Flask halsen på grund av att datorn capping ut på processor-eller nätverks användning. Om du når den här punkten kan du fortsätta att skicka det Azure Cosmos DB kontot ytterligare genom att skala ut dina klient program över flera servrar.

> [!NOTE] 
> Hög CPU-användning kan orsaka ökad latens och begär timeout-undantag.

## <a name="networking"></a><a id="networking"></a> Nätverk

**Anslutnings princip: Använd direkt anslutnings läge**

Hur en klient ansluter till Azure Cosmos DB har viktiga prestanda effekter, särskilt för observerad svars tid på klient sidan. Det finns två nyckel konfigurations inställningar som är tillgängliga för konfigurering av klient anslutnings princip: anslutnings *läget* och anslutnings *protokollet*.  De två tillgängliga lägena är:

  * Gateway-läge (standard)
      
    Gateway-läge stöds på alla SDK-plattformar och är det konfigurerade standardvärdet för [Microsoft.Azure.DocUMENTDB SDK](sql-api-sdk-dotnet.md). Om ditt program körs i ett företags nätverk med strikta brand Väggs begränsningar är Gateway-läget det bästa valet eftersom det använder standard-HTTPS-porten och en enda DNS-slutpunkt. Prestanda kompromissen är dock att Gateway-läget omfattar ytterligare ett nätverks hopp varje gång data läses från eller skrivs till Azure Cosmos DB. Det direkta läget ger bättre prestanda eftersom det finns färre nätverks hopp. Vi rekommenderar också Gateway-anslutnings läge när du kör program i miljöer som har ett begränsat antal socketanslutningar.

    När du använder SDK i Azure Functions, i synnerhet i [förbruknings planen](../azure-functions/functions-scale.md#consumption-plan), var medveten om de aktuella [gränserna för anslutningar](../azure-functions/manage-connections.md). I så fall kan gateway-läget vara bättre om du även arbetar med andra HTTP-baserade klienter i ditt Azure Functions-program.

  * Direkt läge

    Direct-läget stöder anslutning via TCP-protokollet.
     
När du använder TCP i direkt läge måste du förutom Gateway-portarna se till att port intervallet är mellan 10000 och 20000 är öppen eftersom Azure Cosmos DB använder dynamiska TCP-portar. När du använder direkt läge på [privata slut punkter](./how-to-configure-private-endpoints.md)ska hela intervallet TCP-portar – från 0 till 65535 vara öppen. Om de här portarna inte är öppna och du försöker använda TCP-protokollet får du ett meddelande om att 503-tjänsten inte är tillgänglig. I följande tabell visas de anslutnings lägen som är tillgängliga för olika API: er och tjänst portar som används för varje API:

|Anslutningsläge  |Protokoll som stöds  |SDK: er som stöds  |API/tjänst-port  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Alla SDK: er    |   SQL (443), MongoDB (10250, 10255, 10256), tabell (443), Cassandra (10350), Graf (443) <br> Port 10250 mappar till ett standard-Azure Cosmos DB-API för MongoDB-instans utan geo-replikering. Portarna 10255 och 10256 mappar till den instans som har geo-replikering.   |
|Direct    |     TCP    |  .NET SDK    | När du använder offentliga/tjänst slut punkter: portar i intervallet 10000 till 20000<br>När du använder privata slut punkter: portar inom intervallet 0 till 65535 |

Azure Cosmos DB erbjuder en enkel, öppen RESTful programmerings modell över HTTPS. Dessutom erbjuder den ett effektivt TCP-protokoll, som också RESTful i sin kommunikations modell och är tillgängligt via .NET-klient-SDK: n. TCP-protokollet använder TLS för inledande autentisering och kryptering av trafik. Använd TCP-protokollet när det är möjligt för bästa prestanda.

För Microsoft.Azure.DocumentDB SDK konfigurerar du anslutnings läget under `DocumentClient` instansen av instansen med hjälp av `ConnectionPolicy` parametern. Om du använder direkt läge kan du också ställa in `Protocol` med hjälp av `ConnectionPolicy` parametern.

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Eftersom TCP endast stöds i direkt läge, om du använder Gateway-läge, används HTTPS-protokollet alltid för att kommunicera med gatewayen och `Protocol` värdet i `ConnectionPolicy` ignoreras.

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Azure Cosmos DB anslutnings princip" border="false":::

**Tillfällig portöverbelastning**

Om du ser en hög anslutnings volym eller hög port användning på dina instanser måste du först kontrol lera att klient instanserna är singleton. Med andra ord bör klient instanserna vara unika för programmets livs längd.

När den körs på TCP-protokollet optimerar klienten för svars tid genom att använda de långsamma anslutningarna i stället för HTTPS-protokollet, vilket avslutar anslutningarna efter 2 minuters inaktivitet.

I scenarier där du har sparse-åtkomst och om du upptäcker ett högre antal anslutningar jämfört med åtkomst till gateway-läge kan du:

* Konfigurera egenskapen [ConnectionPolicy. PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) till `PrivatePortPool` (gällande med framework-version>= 4.6.1 och .net core-version >= 2,0): med den här egenskapen kan SDK använda en liten pool av tillfälliga portar för olika Azure Cosmos DB mål slut punkter.
* Konfigurera egenskapen [ConnectionPolicy. IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) måste vara större än eller lika med 10 minuter. De rekommenderade värdena är mellan 20 minuter och 24 timmar.

**Anropa openAsync för att undvika start fördröjning för första begäran**

Som standard har den första begäran högre latens eftersom den måste hämta adress routningstabellen. När du använder [SDK v2](sql-api-sdk-dotnet.md)anropar du `OpenAsync()` en gång under initieringen för att undvika den här start fördröjningen för den första begäran. Anropet ser ut så här: `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` genererar begär Anden för att hämta adress routningstabellen för alla behållare i kontot. För konton som har många behållare men vars program har åtkomst till en delmängd av dem, `OpenAsync` genererar en onödig mängd trafik som gör initieringen långsam. Det `OpenAsync` kan vara bra att använda i det här scenariot eftersom det gör att program starten går långsamt.

**Samordna-klienter i samma Azure-region för prestanda.**

Placera eventuella program som anropar Azure Cosmos DB i samma region som Azure Cosmos DBs databasen när det är möjligt. Här är en ungefärlig jämförelse: anrop till Azure Cosmos DB inom samma region slutförs inom 1 ms till 2 MS, men fördröjningen mellan västra USA och östra kust är över 50 ms. Svars tiden kan variera från begäran till begäran, beroende på den väg som tas av begäran när den skickas från klienten till Azure Data Center-gränser. Du kan få lägsta möjliga fördröjning genom att se till att det anropande programmet finns i samma Azure-region som den etablerade Azure Cosmos DB slut punkten. En lista över tillgängliga regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB anslutnings princip" border="false":::

**Öka antalet trådar/aktiviteter**
<a id="increase-threads"></a>

Eftersom anrop till Azure Cosmos DB görs via nätverket kan du behöva variera graden av parallellitet för dina begär Anden så att klient programmet tillbringar minimal tid i väntan mellan begär Anden. Om du till exempel använder ett [parallellt .net-aktivitets bibliotek](https://msdn.microsoft.com//library/dd460717.aspx)skapar du i ordningen på hundratals aktiviteter som läser från eller skriver till Azure Cosmos dB.

**Aktivera accelererat nätverk**
 
För att minska svars tid och CPU-Darr rekommenderar vi att du aktiverar accelererat nätverk på virtuella klient datorer. Se [skapa en virtuell Windows-dator med accelererat nätverk](../virtual-network/create-vm-accelerated-networking-powershell.md) eller [skapa en virtuell Linux-dator med accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK-användning

**Installera den senaste SDK: n**

Azure Cosmos DB SDK: er har ständigt förbättrats för att ge bästa möjliga prestanda. Se de [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) -sidorna för att fastställa de senaste SDK: er och gransknings förbättringarna.

**Använd en singleton Azure Cosmos DB-klient för programmets livs längd**

Varje `DocumentClient` instans är tråd säker och utför effektiv anslutnings hantering och cachelagring av adresser när de körs i direkt läge. För att möjliggöra effektiv anslutnings hantering och bättre prestanda för SDK-klienten, rekommenderar vi att du använder en enda instans per `AppDomain` för programmets livs längd.

**Öka System.Net MaxConnections per värd när du använder Gateway-läge**

Azure Cosmos DB begär Anden görs via HTTPS/REST när du använder Gateway-läge. De är underkastade standard anslutnings gränsen per värdnamn eller IP-adress. Du kan behöva ange `MaxConnections` ett högre värde (100 till 1 000) så att klient biblioteket kan använda flera samtidiga anslutningar till Azure Cosmos dB. I .NET SDK-1.8.0 och senare är standardvärdet för [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 50. Om du vill ändra värdet kan du ange [Documents. client. ConnectionPolicy. MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) till ett högre värde.

**Justera parallella frågor för partitionerade samlingar**

SQL .NET SDK-1.9.0 och senare stöder parallella frågor som gör att du kan fråga en partitionerad samling parallellt. Mer information finns i [kod exempel](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) för att arbeta med SDK: er. Parallella frågor är utformade för att ge bättre svars tid och data flöde än deras serie motsvarighet. Parallella frågor ger två parametrar som du kan justera för att passa dina behov: 
- `MaxDegreeOfParallelism` kontrollerar det högsta antalet partitioner som kan frågas parallellt. 
- `MaxBufferedItemCount` styr antalet i förväg hämtade resultat.

***Justera graden av parallellitet***

Parallell fråga fungerar genom att fråga flera partitioner parallellt. Men data från en enskild partition hämtas seriellt i förhållande till frågan. Inställningen `MaxDegreeOfParallelism` i [SDK v2](sql-api-sdk-dotnet.md) till antalet partitioner har den bästa möjligheten att nå den mest utförda frågan, förutsatt att alla andra system villkor är desamma. Om du inte vet antalet partitioner kan du ange graden av parallellitet till ett högt tal. Systemet väljer det lägsta (antal partitioner, indata från användaren) som graden av parallellitet.

Parallella frågor ger flest fördelar om data är jämnt fördelade över alla partitioner med avseende på frågan. Om den partitionerade samlingen är partitionerad, så att alla eller de flesta data som returneras av en fråga är koncentrerade i några partitioner (en partition är det värsta fallet), kommer dessa partitioner att översätta prestandan hos frågan.

***Justera MaxBufferedItemCount***
    
Parallell fråga är utformad för att hämta resultat när den aktuella gruppen med resultat bearbetas av klienten. Den här för hämtningen hjälper till att förbättra den övergripande svars tiden för en fråga. `MaxBufferedItemCount`Parametern begränsar antalet i förväg hämtade resultat. Ange `MaxBufferedItemCount` till det förväntade antalet returnerade resultat (eller ett högre antal) för att tillåta att frågan tar emot den maximala fördelen med för hämtning.

För hämtning fungerar på samma sätt oavsett graden av parallellitet och det finns en enda buffert för data från alla partitioner.  

**Implementera backoff med RetryAfter-intervall**

Under prestanda testningen bör du öka belastningen tills en låg frekvens av begär Anden begränsas. Om begär Anden begränsas bör klient programmet stängas av vid begränsningen för det Server-angivna återförsöksintervallet. Att respektera backoff garanterar att du ägnar en liten stund åt att vänta mellan återförsök. 

Princip support för återförsök ingår i dessa SDK: er:
- Version 1.8.0 och senare av [.NET SDK för SQL](sql-api-sdk-dotnet.md) och [Java SDK för SQL](sql-api-sdk-java.md)
- Version 1.9.0 och senare av [Node.js SDK för SQL](sql-api-sdk-node.md) och [python SDK för SQL](sql-api-sdk-python.md)
- Alla versioner av [.net Core](sql-api-sdk-dotnet-core.md) SDK: er som stöds 

Mer information finns i [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
I version 1,19 och senare av .NET SDK finns det en mekanism för att logga ytterligare diagnostikinformation och felsöka latens problem, som du ser i följande exempel. Du kan logga den diagnostiska strängen för förfrågningar som har en högre Läs fördröjning. Den hämtade diagnostikloggar hjälper dig att förstå hur många gånger du fick 429 fel för en specifik begäran.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Cachelagra dokument-URI: er för lägre Läs fördröjning**

Cachelagra dokument-URI: er när det är möjligt för bästa möjliga Läs prestanda. Du måste definiera logik för att cachelagra resurs-ID när du skapar en resurs. Sökningar baserat på resurs-ID: n är snabbare än namnbaserade sökningar, så cachelagring av dessa värden ger bättre prestanda.

**Justera sid storleken för frågor/läsa feeds för bättre prestanda**

När du gör en Mass läsning av dokument med hjälp av funktionen för att läsa feeds (till exempel `ReadDocumentFeedAsync` ) eller när du skickar en SQL-fråga, returneras resultatet i ett segmenterat läge om resultat mängden är för stor. Som standard returneras resultaten i segment om 100 objekt eller 1 MB, beroende på vilken gräns som nåtts först.

För att minska antalet nätverks fördröjningar som krävs för att hämta alla tillämpliga resultat kan du öka sid storleken genom att använda [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) för att begära så många som 1 000 sidhuvud. Om du bara behöver visa några få resultat, till exempel om ditt användar gränssnitt eller ditt program-API bara returnerar 10 resultat i taget, kan du också minska sid storleken till 10 för att minska data flödet som används för läsningar och frågor.

> [!NOTE] 
> `maxItemCount`Egenskapen bör inte användas bara för sid brytning. Den används huvudsakligen för att förbättra prestandan för frågor genom att minska det maximala antalet objekt som returneras på en enda sida.  

Du kan också ange sid storlek med hjälp av tillgängliga Azure Cosmos DB SDK: er. Med egenskapen [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) i `FeedOptions` kan du ange det maximala antalet objekt som ska returneras i uppräknings åtgärden. När `maxItemCount` är inställt på-1 hittar SDK: n automatiskt det optimala värdet, beroende på dokumentets storlek. Ett exempel:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
När en fråga körs skickas resulterande data i ett TCP-paket. Om du anger för lågt värde för värdet `maxItemCount` är antalet resor som krävs för att skicka data i TCP-paketet hög, vilket påverkar prestanda. Så om du inte är säker på vilket värde som ska anges för `maxItemCount` egenskapen, är det bäst att ange det till-1 och låta SDK: n välja standardvärdet.

**Öka antalet trådar/aktiviteter**

Se [öka antalet trådar/aktiviteter](#increase-threads) i avsnittet nätverk i den här artikeln.

## <a name="indexing-policy"></a>Indexeringsprincip
 
**Utesluta sökvägar som inte används från indexering för att få snabbare skrivning**

Med Azure Cosmos DB indexerings principen kan du också ange vilka dokument Sök vägar som ska ingå i eller undantas från indexering genom att använda indexerings Sök vägar (IndexingPolicy. IncludedPaths och IndexingPolicy. ExcludedPaths). Indexering av sökvägar kan förbättra skriv prestanda och minska index lagringen för scenarier där fråge mönstren är kända i förväg. Detta beror på att indexerings kostnader korrelerar direkt med antalet unika sökvägar. Den här koden visar till exempel hur du undantar en hel del av dokumenten (ett under träd) från indexering med hjälp av jokertecknet "*":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

Mer information finns i [Azure Cosmos DB indexerings principer](index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a> Kapacitet

**Mått och justering för lägre enheter för programbegäran/andra användning**

Azure Cosmos DB erbjuder en omfattande uppsättning databas åtgärder. Dessa åtgärder omfattar relationella och hierarkiska frågor med UDF: er, lagrade procedurer och utlösare, allt som körs på dokumenten i en databas samling. Kostnaden som är kopplad till var och en av dessa åtgärder varierar beroende på processor, IO och minne som krävs för att slutföra åtgärden. I stället för att tänka på och hantera maskin varu resurser kan du tänka på en enhet för begäran (RU) som ett enda mått för de resurser som krävs för att utföra olika databas åtgärder och betjäna en programbegäran.

Data flödet har allokerats baserat på antalet enheter för [programbegäran](request-units.md) som angetts för varje behållare. Den begärda enhets förbrukningen utvärderas som en taxa per sekund. Program som överskrider den allokerade enhets hastigheten för deras behållare är begränsade tills hastigheten sjunker under den allokerade nivån för behållaren. Om ditt program kräver en högre data flödes nivå kan du öka data flödet genom att tillhandahålla ytterligare enheter för programbegäran.

En frågas komplexitet påverkar hur många enheter för programbegäran som används för en åtgärd. Antalet predikat, typen av predikat, antalet UDF: er och storleken på käll data uppsättningen påverkar hela kostnaden för frågor.

Om du vill mäta omkostnaderna för en åtgärd (skapa, uppdatera eller ta bort) kan du kontrol lera huvudet [x-MS-Request-avgift](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (eller motsvarande `RequestCharge` egenskap i `ResourceResponse\<T>` eller `FeedResponse\<T>` i .NET SDK) för att mäta antalet enheter för programbegäran som används av åtgärderna:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Begär ande avgiften som returnerades i den här rubriken är en bråkdel av ditt allokerade data flöde (dvs. 2 000 ru: er/sekund). Om exempelvis föregående fråga returnerar 1 000 1 KB-dokument, är kostnaden för åtgärden 1 000. Det innebär att servern i en sekund endast besitter två sådana begär Anden innan den hastighet som senare begärs begränsas. Mer information finns i [enheter för programbegäran](request-units.md) och [Kalkylatorn för begär ande](https://www.documentdb.com/capacityplanner)enheter.
<a id="429"></a>

**Hastighets begränsning/begär ande frekvens för stor**

När en klient försöker överskrida det reserverade data flödet för ett konto, finns det ingen prestanda försämring på servern och ingen användning av data flödes kapaciteten utöver den reserverade nivån. Servern kommer att förebyggande syfte avsluta begäran med RequestRateTooLarge (HTTP-status kod 429). Det returnerar ett [x-MS-retry-efter-MS-](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) huvud som anger hur lång tid i millisekunder som användaren måste vänta innan begäran försöker igen.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

SDK: erna fångar alla implicita dessa svar, med den server-angivna återförsöket-efter-rubriken och gör om begäran. Om ditt konto inte kan nås samtidigt av flera klienter kommer nästa försök att lyckas.

Om du har mer än en klient ackumulerad på ett konsekvent sätt över begär ande frekvensen är standard antalet nya försök som för närvarande är 9 internt av klienten inte tillräckligt. I det här fallet genererar klienten en DocumentClientException med status kod 429 i programmet. 

Du kan ändra standard antalet nya försök genom att ställa in `RetryOptions` på `ConnectionPolicy` instansen. Som standard returneras DocumentClientException med status kod 429 efter en ackumulerad vänte tid på 30 sekunder om begäran fortsätter att köras över begär ande frekvensen. Det här felet returnerar även när det aktuella antalet nya försök är mindre än det högsta antalet tillåtna försök, om det aktuella värdet är standardvärdet 9 eller ett användardefinierat värde.

Det automatiska återförsöket hjälper till att förbättra återhämtning och användbarhet för de flesta program. Men det kanske inte är det bästa beteendet när du utför prestandatest, särskilt när du mäter svars tid. Den klientbaserade svars tiden kommer att inaktive ras om experimentet träffar Server begränsningen och gör att klient-SDK: n kan försöka tyst igen. För att undvika fördröjningar vid prestanda experiment kan du mäta den avgift som returneras av varje åtgärd och se till att förfrågningarna fungerar under den reserverade begär ande frekvensen. Mer information finns i [enheter för programbegäran](request-units.md).

**För högre data flöde kan du utforma för mindre dokument**

Avgiften för begäran (det vill säga bearbetnings kostnaden för bearbetning) för en specifik åtgärd motsvarar direkt till dokumentets storlek. Åtgärder i stora dokument kostar mer än åtgärder på små dokument.

## <a name="next-steps"></a>Nästa steg

Ett exempel program som används för att utvärdera Azure Cosmos DB för scenarier med hög prestanda på ett fåtal klient datorer finns i [prestanda-och skalnings testning med Azure Cosmos DB](performance-testing.md).

Mer information om hur du utformar programmet för skalning och höga prestanda finns i [partitionering och skalning i Azure Cosmos DB](partition-data.md).
