---
title: Prestanda tips för Azure Cosmos DB asynkron Java SDK v2
description: Lär dig mer om klient konfigurations alternativ för att förbättra prestanda för Azure Cosmos Database för asynkron Java SDK v2
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: bd009ae4909c8cb016a31323294df3a359eb7c51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033671"
---
# <a name="performance-tips-for-azure-cosmos-db-async-java-sdk-v2"></a>Prestanda tips för Azure Cosmos DB asynkron Java SDK v2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Asynkron Java-SDK v2](performance-tips-async-java.md)
> * [Synkron Java-SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)


> [!IMPORTANT]  
> Detta är *inte* den senaste Java SDK: n för Azure Cosmos DB! Du bör uppgradera projektet till [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) och sedan läsa guiden Azure Cosmos DB Java SDK v4- [prestanda tips](performance-tips-java-sdk-v4-sql.md). Följ anvisningarna i guiden [migrera till Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) -guide och [reaktor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) för att uppgradera. 
> 
> Prestanda tipsen i den här artikeln är endast för Azure Cosmos DB asynkron Java SDK v2. Mer information finns i [viktig](sql-api-sdk-async-java.md)information om Azure Cosmos DB ASYNC Java SDK v2, [maven-lagringsplatsen](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)och Azure Cosmos DB asynkron Java SDK v2- [felsöknings guide](troubleshoot-java-async-sdk.md) .
>

Azure Cosmos DB är en snabb och flexibel distribuerad databas som skalar sömlöst med garanterad svars tid och data flöde. Du behöver inte göra större ändringar i arkitekturen eller skriva komplex kod för att skala databasen med Azure Cosmos DB. Att skala upp och ned är lika enkelt som att göra ett enda API-anrop eller SDK-metod anrop. Men eftersom Azure Cosmos DB nås via nätverks anrop finns det en optimering på klient sidan som du kan göra för att uppnå högsta prestanda när du använder [Azure Cosmos DB asynkron Java SDK v2](sql-api-sdk-async-java.md).

Så om du frågar "Hur kan jag förbättra min databas prestanda?" Överväg följande alternativ:

## <a name="networking"></a>Nätverk

* **Anslutnings läge: Använd direkt läge**
    
  Hur en klient ansluter till Azure Cosmos DB har viktiga konsekvenser för prestanda, särskilt vad gäller svars tid på klient sidan. *ConnectionMode* är en nyckel konfigurations inställning som är tillgänglig för konfigurering av klientens *ConnectionPolicy*. För Azure Cosmos DB asynkron Java SDK v2 är de två tillgängliga ConnectionModes:  
      
  * [Gateway (standard)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
  * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)
  
  Gateway-läge stöds på alla SDK-plattformar och är det konfigurerade alternativet som standard. Om dina program körs i ett företags nätverk med strikta brand Väggs begränsningar är Gateway-läget det bästa valet eftersom det använder standard-HTTPS-porten och en enda slut punkt.   Prestanda kompromissen är dock att Gateway-läget omfattar ytterligare nätverks hopp varje gång data läses eller skrivs till Azure Cosmos DB. Därför erbjuder Direct-läget bättre prestanda på grund av färre nätverks hopp.
  
  *ConnectionMode* konfigureras under konstruktion av *DocumentClient* -instansen med parametern *ConnectionPolicy* .

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-connectionpolicy"></a>Asynkron Java SDK v2 (maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.Direct);
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
```

* **Samordna klienter i samma Azure-region för prestanda**

  När det är möjligt kan du placera alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos-databasen. För en ungefärlig jämförelse kan anrop till Azure Cosmos DB inom samma region slutföras inom 1-2 MS, men svars tiden mellan västra USA och östra kust är >50 ms. Den här fördröjningen kan troligt vis variera från begäran till begäran beroende på den väg som tas av begäran när den skickas från klienten till Azure Data Center-gränser. Den lägsta möjliga fördröjningen uppnås genom att se till att det anropande programmet finns i samma Azure-region som den etablerade Azure Cosmos DB slut punkten. En lista över tillgängliga regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/#services).

  :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Bild av Azure Cosmos DB anslutnings princip" border="false":::

## <a name="sdk-usage"></a>SDK-användning

* **Installera den senaste SDK: n**

  Azure Cosmos DB SDK: er har ständigt förbättrats för att ge bästa möjliga prestanda. Mer information om de senaste SDK: er och gransknings förbättringar finns på sidan Azure Cosmos DB asynkron Java SDK v2- [viktig information](sql-api-sdk-async-java.md) .

* **Använd en singleton Azure Cosmos DB-klient för programmets livs längd**

  Varje AsyncDocumentClient-instans är tråd säker och utför effektiv anslutnings hantering och cachelagring av adresser. För att möjliggöra effektiv anslutnings hantering och bättre prestanda av AsyncDocumentClient rekommenderar vi att du använder en enda instans av AsyncDocumentClient per AppDomain för programmets livs längd.

* **Justera ConnectionPolicy**

  Som standard görs direkt läge Cosmos DB begär Anden via TCP när Azure Cosmos DB asynkron Java SDK v2 används. Internt använder SDK en särskild arkitektur för direkt läge för att dynamiskt hantera nätverks resurser och få bästa möjliga prestanda.

  I Azure Cosmos DB asynkron Java SDK v2 är Direct-läget det bästa valet för att förbättra databasens prestanda med de flesta arbets belastningar. 

  * ***Översikt över direkt läge***

  :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Bild av arkitekturen för direkt läge" border="false":::
  
  Arkitekturen på klient sidan som används i direkt läge möjliggör förutsägbar nätverks användning och multiplex-åtkomst till Azure Cosmos DB repliker. Diagrammet ovan visar hur Direct-läge dirigerar klient begär anden till repliker i Cosmos DB-backend-servern. Arkitekturen för direkt läge allokerar upp till 10 **kanaler** på klient sidan per DB-replik. En kanal är en TCP-anslutning som föregås av en buffert för begäran, som är en djup på 30 begär Anden. Kanaler som tillhör en replik allokeras dynamiskt efter behov av replikens **tjänst slut punkt**. När användaren utfärdar en begäran i direkt läge dirigerar **TransportClient** begäran till rätt tjänst slut punkt utifrån partitionsnyckel. **Begär ande kön** buffrar begär Anden före tjänst slut punkten.

  * ***ConnectionPolicy konfigurations alternativ för direkt läge***

    I det första steget använder du följande rekommenderade konfigurations inställningar nedan. Kontakta Azure Cosmos DB- [teamet](mailto:CosmosDBPerformanceSupport@service.microsoft.com) om du stöter på problem på det här specifika ämnet.

    Om du använder Azure Cosmos DB som en referens databas (det vill säga databasen används för många punkt läsnings åtgärder och få Skriv åtgärder) kan det vara acceptabelt att ange *idleEndpointTimeout* till 0 (det vill säga ingen tids gräns).


    | Konfigurations alternativ       | Standardvärde    |
    | :------------------:       | :-----:    |
    | bufferPageSize             | 8192       |
    | connectionTimeout          | "PT1M"     |
    | idleChannelTimeout         | "PT0S"     |
    | idleEndpointTimeout        | "PT1M10S"  |
    | maxBufferCapacity          | 8388608    |
    | maxChannelsPerEndpoint     | 10         |
    | maxRequestsPerChannel      | 30         |
    | receiveHangDetectionTime   | "PT1M5S"   |
    | requestExpiryInterval      | "PT5S"     |
    | requestTimeout             | "PT1M"     |
    | requestTimerResolution     | "PT 0,5 S"   |
    | sendHangDetectionTime      | "PT10S"    |
    | shutdownTimeout            | "PT15S"    |

* ***Programmerings tips för direkt läge***

  Läs artikeln Azure Cosmos DB async Java SDK v2- [Felsökning](troubleshoot-java-async-sdk.md) som en bas linje för att lösa eventuella SDK-problem.
  
  Några viktiga programmerings tips när du använder direkt läge:
  
  * **Använd multitrådning i ditt program för effektiv TCP-dataöverföring** – när du har gjort en förfrågan bör ditt program prenumerera på att ta emot data i en annan tråd. Om du inte gör det tvingas oavsiktligt "halv duplex"-åtgärd och efterföljande förfrågningar blockeras i väntan på föregående begärans svar.
  
  * **Utför beräknings intensiva arbets belastningar på en dedikerad tråd** – av liknande skäl som i föregående tips, är åtgärder som komplex data bearbetning bäst placerade i en separat tråd. En begäran som hämtar data från ett annat data lager (till exempel om tråden använder Azure Cosmos DB och Spark-datalager samtidigt) kan öka svars tiden och vi rekommenderar att du skapar en ytterligare tråd som väntar på ett svar från det andra data lagret.
  
    * Den underliggande nätverks-i/o i Azure Cosmos DB asynkron Java SDK v2 hanteras av, se följande [tips för att undvika kodnings mönster som blockerar nett i/o-trådar](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).
  
  * **Data modellering** – Azure Cosmos DB SLA förutsätter att dokument storleken är mindre än 1 KB. Att optimera din data modell och programmering för att prioritera mindre dokument storlek kommer normalt att leda till minskad svars tid. Om du kommer att behöva lagring och hämtning av dokument som är större än 1 KB, är den rekommenderade metoden att dokument länkar till data i Azure Blob Storage.

* **Justera parallella frågor för partitionerade samlingar**

  Azure Cosmos DB asynkron Java SDK v2 stöder parallella frågor som gör att du kan fråga en partitionerad samling parallellt. Mer information finns i [kod exempel](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) för att arbeta med SDK: er. Parallella frågor är utformade för att förbättra svars tid och data flöde för deras serie motsvarighet.

  * ***Justera setMaxDegreeOfParallelism\:***
    
    Parallella frågor fungerar genom att fråga flera partitioner parallellt. Data från en enskild partitionerad samling hämtas dock i serie med avseende på frågan. Använd setMaxDegreeOfParallelism för att ställa in antalet partitioner som har maximal chans att uppnå den mest utförda frågan, förutsatt att alla andra system villkor är desamma. Om du inte känner till antalet partitioner kan du använda setMaxDegreeOfParallelism för att ange ett högt antal, och systemet väljer det lägsta (antal partitioner, indata från användaren) som den högsta graden av parallellitet.

    Det är viktigt att Observera att parallella frågor ger de bästa fördelarna om data är jämnt fördelade över alla partitioner med avseende på frågan. Om den partitionerade samlingen är partitionerad, så att alla eller en majoritet av de data som returneras av en fråga är koncentrerade i några partitioner (en partition i värsta fall), skulle prestandan för frågan bli Flask hals av dessa partitioner.

  * ***Justera setMaxBufferedItemCount\:***
    
    Parallell fråga är utformad för att hämta resultat när den aktuella gruppen med resultat bearbetas av klienten. För hämtning bidrar till den totala tids fördröjnings förbättringen av en fråga. setMaxBufferedItemCount begränsar antalet i förväg hämtade resultat. Om du anger setMaxBufferedItemCount till det förväntade antalet returnerade resultat (eller en högre siffra) kan frågan ta emot maximal nytta av för hämtning.

    För hämtning fungerar på samma sätt oavsett MaxDegreeOfParallelism, och det finns en enda buffert för data från alla partitioner.

* **Implementera backoff med getRetryAfterInMilliseconds-intervall**

  Under prestanda testningen bör du öka belastningen tills en låg frekvens av begär Anden blir begränsad. Om detta är begränsat bör klient programmet backoff för det Server-angivna återförsöksintervallet. Genom att respektera backoff garanterar du att du tillbringar minimal tid på att vänta mellan återförsök.

* **Skala ut din klient arbets belastning**

  Om du testar med höga data flödes nivåer (>50 000 RU/s) kan klient programmet bli Flask halsen på grund av att datorn capping ut på processor-eller nätverks användning. Om du når den här punkten kan du fortsätta att skicka det Azure Cosmos DB kontot ytterligare genom att skala ut dina klient program över flera servrar.

* **Använd namn baserat adressering**

  Använd namnbaserade adressering, där länkar har formatet `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` , i stället för SelfLinks ( \_ Self), som har formatet `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` för att undvika att hämta ResourceIds för alla resurser som används för att skapa länken. Som de här resurserna kommer att återskapas (eventuellt med samma namn) är det inte säkert att de cachelagrar dem.

* **Justera sid storleken för frågor/läsa feeds för bättre prestanda**

  När du utför en Mass läsning av dokument med hjälp av funktionen för att läsa feeds (till exempel readDocuments) eller när du utfärdar en SQL-fråga, returneras resultatet i ett segment om resultat mängden är för stor. Som standard returneras resultaten i segment om 100 objekt eller 1 MB, beroende på vilken gräns som nåtts först.

  För att minska antalet nätverks fördröjningar som krävs för att hämta alla tillämpliga resultat kan du öka sid storleken med rubriken [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) till upp till 1000. I de fall där du bara behöver visa några få resultat, till exempel om ditt användar gränssnitt eller ditt program-API bara returnerar 10 resultat en gång, kan du också minska sid storleken till 10 för att minska data flödet som används för läsningar och frågor.

  Du kan också ange sid storleken med setMaxItemCount-metoden.

* **Använd lämplig Scheduler (Undvik att stjäla händelse slingor i IO-nättrådar)**

  Azure Cosmos DB asynkron Java SDK v2 använder [nettning](https://netty.io/) för icke-blockerande i/o. SDK använder ett fast antal upprepnings trådar i IO-nettning (så många processor kärnor som datorn har) för att köra IO-åtgärder. Det observerbara som returneras av API: n genererar resultatet av en av de uppdelade IO-händelserna för den delade IO-händelsen. Det är därför viktigt att inte blockera den delade IO-händelsen av en loop. Att utföra processor intensiva arbeten eller blockera åtgärder i Netstore-tråden i IO-händelseloggen kan orsaka död läge eller avsevärt minska SDK-dataflödet.

  Följande kod kör till exempel ett processor intensivt arbete i-tråden för händelse slingor i IO:

  **Asynkron Java SDK v2 (maven com. Microsoft. Azure:: Azure-cosmosdb)**

  ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
  ```

  När resultatet har tagits emot, om du vill göra processor intensivt arbete på resultatet bör du undvika att göra det på händelse loop i/o-kontexten. Du kan i stället tillhandahålla din egen schemaläggare att tillhandahålla en egen tråd för att köra ditt arbete.

  **Asynkron Java SDK v2 (maven com. Microsoft. Azure:: Azure-cosmosdb)**

  ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
  ```

  Baserat på typen av arbete bör du använda den aktuella befintliga RxJava Scheduler för ditt arbete. Läs här [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html) .

  Mer information finns på [GitHub-sidan](https://github.com/Azure/azure-cosmosdb-java) för Azure Cosmos DB ASYNKRON Java SDK v2.

* **Inaktivera loggning av netttjänster**

    Loggning av Netstore-bibliotek är chatt och måste stängas av (under tryckning av inloggnings konfigurationen kanske inte räcker) för att undvika ytterligare processor kostnader. Om du inte är i fel söknings läge inaktiverar du nett loggning helt. Så om du använder Log4J för att ta bort de ytterligare CPU-kostnader som uppstår vid ``org.apache.log4j.Category.callAppenders()`` nettning lägger du till följande rad i kodbasen:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Resurs gräns för öppna filer i operativ systemet**
 
    Vissa Linux-system (till exempel Red Hat) har en övre gräns för antalet öppna filer och så det totala antalet anslutningar. Kör följande för att visa de aktuella gränserna:

    ```bash
    ulimit -a
    ```

    Antalet öppna filer (nofile) måste vara tillräckligt stort för att det ska finnas tillräckligt med plats för den konfigurerade anslutningspoolen och andra öppna filer av operativ systemet. Den kan ändras för att tillåta en större storlek på anslutningspoolen.

    Öppna filen Limits. conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Lägg till/ändra följande rader:

    ```
    * - nofile 100000
    ```

## <a name="indexing-policy"></a>Indexeringsprincip
 
* **Utesluta sökvägar som inte används från indexering för att få snabbare skrivning**

    Med Azure Cosmos DB indexerings principen kan du ange vilka dokument Sök vägar som ska tas med eller undantas från indexering genom att använda indexerings Sök vägar (setIncludedPaths och setExcludedPaths). Användningen av indexerings Sök vägar kan ge bättre skriv prestanda och lägre index lagring för scenarier där fråge mönstren är kända i förväg, eftersom indexerings kostnader direkt korreleras med antalet unika sökvägar som indexeras. Följande kod visar till exempel hur du undantar en hel del av dokumenten (kallas även ett under träd) från indexering med jokertecknet "*".

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-indexing"></a>Asynkron Java SDK v2 (maven com. Microsoft. Azure:: Azure-cosmosdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Mer information finns i [Azure Cosmos DB indexerings principer](./index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a>Dataflöde

* **Mått och justering för lägre enheter för programbegäran/andra användning**

    Azure Cosmos DB erbjuder en omfattande uppsättning databas åtgärder, inklusive relationella och hierarkiska frågor med UDF: er, lagrade procedurer och utlösare – allt som körs på dokumenten i en databas samling. Den kostnad som hör till var och en av dessa operationer varierar baserat på vilken CPU, vilka IO-resurser och hur mycket minne som krävs för att slutföra operationen. I stället för att tänka på och hantera maskin varu resurser kan du tänka på en enhet för begäran (RU) som ett enda mått för de resurser som krävs för att utföra olika databas åtgärder och betjäna en programbegäran.

    Data flödet har allokerats baserat på antalet enheter för [programbegäran](request-units.md) som angetts för varje behållare. Den begärda enhets förbrukningen utvärderas som en taxa per sekund. Program som överskrider den allokerade enhets hastigheten för deras behållare är begränsade tills hastigheten sjunker under den allokerade nivån för behållaren. Om ditt program kräver en högre data flödes nivå kan du öka data flödet genom att tillhandahålla ytterligare enheter för programbegäran.

    Komplexiteten i en fråga påverkar hur många enheter för programbegäran som används för en åtgärd. Antalet predikat, typen av predikat, antalet UDF: er och storleken på käll data uppsättningen påverkar kostnaden för frågor.

    Om du vill mäta omkostnaderna för en åtgärd (skapa, uppdatera eller ta bort) kan du kontrol lera huvudet [x-MS-Request-avgift](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) för att mäta antalet enheter för programbegäran som används av dessa åtgärder. Du kan också titta på motsvarande RequestCharge-egenskap i ResourceResponse \<T> eller FeedResponse \<T> .

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-requestcharge"></a>Asynkron Java SDK v2 (maven com. Microsoft. Azure:: Azure-cosmosdb)

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Begär ande avgiften som returnerades i den här rubriken är en bråkdel av ditt etablerade data flöde. Om du till exempel har 2000 RU/s etablerad, och om föregående fråga returnerar 1000 1 KB-dokument, är kostnaden för åtgärden 1000. I och med den här servern bevarar servern bara två sådana begär Anden innan hastigheten begränsar efterföljande begär Anden. Mer information finns i [enheter för programbegäran](request-units.md) och [Kalkylatorn för begär ande](https://www.documentdb.com/capacityplanner)enheter.

* **Hastighets begränsning/begär ande frekvens för stor**

    När en klient försöker överskrida det reserverade data flödet för ett konto, finns det ingen prestanda försämring på servern och ingen användning av data flödes kapaciteten utöver den reserverade nivån. Servern kommer att förebyggande syfte avsluta begäran med RequestRateTooLarge (HTTP-statuskod 429) och returnera huvudet [x-MS-retry-efter-MS](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) som anger hur lång tid i millisekunder som användaren måste vänta innan begäran försöker igen.

   ```xml
   HTTP Status 429,
   Status Line: RequestRateTooLarge
   x-ms-retry-after-ms :100
   ```

    SDK: erna fångar alla implicita dessa svar, med den server-angivna återförsöket-efter-rubriken och gör om begäran. Om ditt konto inte kan nås samtidigt av flera klienter kommer nästa försök att lyckas.

    Om du har mer än en klient ackumulerad på ett konsekvent sätt över begär ande frekvensen är standard antalet nya försök som för närvarande är 9 internt av klienten inte tillräckligt. i det här fallet genererar klienten en DocumentClientException med status kod 429 i programmet. Standard antalet återförsök kan ändras genom att använda setRetryOptions på ConnectionPolicy-instansen. Som standard returneras DocumentClientException med status kod 429 efter en ackumulerad vänte tid på 30 sekunder om begäran fortsätter att köras över begär ande frekvensen. Detta inträffar även om det aktuella antalet återförsök är mindre än max antalet försök, måste det vara standardvärdet 9 eller ett användardefinierat värde.

    Även om det automatiserade återförsöket hjälper till att förbättra återhämtning och användbarhet för de flesta program, kan det komma på strider när du gör prestanda mått, särskilt när du mäter svars tid. Den klientbaserade svars tiden kommer att inaktive ras om experimentet träffar Server begränsningen och gör att klient-SDK: n kan försöka tyst igen. För att undvika fördröjningar vid prestanda experiment kan du mäta den avgift som returneras av varje åtgärd och se till att förfrågningarna fungerar under den reserverade begär ande frekvensen. Mer information finns i [enheter för programbegäran](request-units.md).

* **Utforma för mindre dokument för högre dataflöde**

    Begär ande avgiften (bearbetnings kostnaden för begäran) för en specifik åtgärd är direkt korrelerad med dokumentets storlek. Åtgärder i stora dokument kostar mer än åtgärder för små dokument.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utformar programmet för skalning och höga prestanda finns i [partitionering och skalning i Azure Cosmos DB](partitioning-overview.md).