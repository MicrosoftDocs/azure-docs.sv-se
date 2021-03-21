---
title: Prestanda tips för Azure Cosmos DB Sync Java SDK v2
description: Lär dig mer om klient konfigurations alternativ för att förbättra prestanda för Azure Cosmos Database för synkronisering av Java SDK v2
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6e7b01ae88645d8b16c3a43e21e40b53d242fdde
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96549250"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Prestanda tips för Azure Cosmos DB Sync Java SDK v2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Asynkron Java-SDK v2](performance-tips-async-java.md)
> * [Synkron Java-SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> Detta är *inte* den senaste Java SDK: n för Azure Cosmos DB! Du bör uppgradera projektet till [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) och sedan läsa guiden Azure Cosmos DB Java SDK v4- [prestanda tips](performance-tips-java-sdk-v4-sql.md). Följ anvisningarna i guiden [migrera till Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) -guide och [reaktor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) för att uppgradera. 
> 
> Dessa prestanda tips är endast för Azure Cosmos DB Sync Java SDK v2. Mer information finns i Azure Cosmos DB Sync Java SDK v2- [versions information](sql-api-sdk-java.md) och [maven-lagringsplats](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) .
>

Azure Cosmos DB är en snabb och flexibel distribuerad databas som skalar sömlöst med garanterad svars tid och data flöde. Du behöver inte göra större ändringar i arkitekturen eller skriva komplex kod för att skala databasen med Azure Cosmos DB. Att skala upp och ned är lika enkelt som att göra ett enda API-anrop. Mer information finns i [så här etablerar du behållar data flöde](how-to-provision-container-throughput.md) eller [hur du etablerar databas data flöde](how-to-provision-database-throughput.md). Men eftersom Azure Cosmos DB nås via nätverks anrop finns det en optimering på klient sidan som du kan göra för att uppnå högsta prestanda när du använder [Azure Cosmos DB synkronisera Java SDK v2](./sql-api-sdk-java.md).

Så om du frågar "Hur kan jag förbättra min databas prestanda?" Överväg följande alternativ:

## <a name="networking"></a>Nätverk
<a id="direct-connection"></a>

1. **Anslutnings läge: Använd DirectHttps**

    Hur en klient ansluter till Azure Cosmos DB har stor inverkan på prestandan, särskilt de svarstider som uppstår på klientsidan. Det finns en inställning för nyckel konfiguration som är tillgänglig för konfigurering av klientens [ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) – [ConnectionMode](/java/api/com.microsoft.azure.documentdb.connectionmode).  De två tillgängliga ConnectionModes är:

   1. [Gateway (standard)](/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](/java/api/com.microsoft.azure.documentdb.connectionmode)

      Gateway-läget stöds på alla SDK-plattformar och är det konfigurerade standardvärdet.  Om ditt program körs i ett företags nätverk med strikta brand Väggs begränsningar är Gateway det bästa valet eftersom det använder standard-HTTPS-porten och en enda slut punkt. Prestanda kompromissen är dock att Gateway-läget omfattar ytterligare nätverks hopp varje gång data läses eller skrivs till Azure Cosmos DB. Därför erbjuder DirectHttps-läget bättre prestanda på grund av färre nätverks hopp. 

      Azure Cosmos DB Sync Java SDK v2 använder HTTPS som transport protokoll. HTTPS använder TLS för inledande autentisering och kryptering av trafik. När du använder Azure Cosmos DB Sync Java SDK v2 måste endast HTTPS-port 443 vara öppen. 

      ConnectionMode konfigureras under konstruktion av DocumentClient-instansen med parametern ConnectionPolicy. 

    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-connectionpolicy"></a>Synkronisera Java SDK v2 (maven com. Microsoft. Azure:: Azure-DocumentDB)

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      :::image type="content" source="./media/performance-tips-java/connection-policy.png" alt-text="Diagrammet visar anslutnings principen för Azure Cosmos D B." border="false":::

   <a id="same-region"></a>
2. **Samordna klienter i samma Azure-region för prestanda**

    När det är möjligt kan du placera alla program som anropar Azure Cosmos DB i samma region som Azure Cosmos-databasen. För en ungefärlig jämförelse kan anrop till Azure Cosmos DB inom samma region slutföras inom 1-2 MS, men svars tiden mellan västra USA och östra kust är >50 ms. Den här fördröjningen kan troligt vis variera från begäran till begäran beroende på den väg som tas av begäran när den skickas från klienten till Azure Data Center-gränser. Den lägsta möjliga fördröjningen uppnås genom att se till att det anropande programmet finns i samma Azure-region som den etablerade Azure Cosmos DB slut punkten. En lista över tillgängliga regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/#services).

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Diagrammet visar begär Anden och svar i två regioner där datorer ansluter till ett Cosmos D B-konto via tjänster på mellan nivå." border="false":::
   
## <a name="sdk-usage"></a>SDK-användning
1. **Installera den senaste SDK: n**

    Azure Cosmos DB SDK: er har ständigt förbättrats för att ge bästa möjliga prestanda. Se de [Azure Cosmos DB SDK](./sql-api-sdk-java.md) -sidorna för att fastställa de senaste SDK: er och gransknings förbättringarna.
2. **Använd en singleton Azure Cosmos DB-klient för programmets livs längd**

    Varje [DocumentClient](/java/api/com.microsoft.azure.documentdb.documentclient) -instans är tråd säker och utför effektiv anslutnings hantering och cachelagring av adresser när du arbetar i direkt läge. För att möjliggöra effektiv anslutnings hantering och bättre prestanda av DocumentClient rekommenderar vi att du använder en enda instans av DocumentClient per AppDomain för programmets livs längd.

   <a id="max-connection"></a>
3. **Öka MaxPoolSize per värd när du använder Gateway-läge**

    Azure Cosmos DB begär Anden görs via HTTPS/REST vid användning av Gateway-läge och omfattas av standard anslutnings gränsen per värdnamn eller IP-adress. Du kan behöva ange MaxPoolSize till ett högre värde (200-1000) så att klient biblioteket kan använda flera samtidiga anslutningar till Azure Cosmos DB. I Azure Cosmos DB Sync Java SDK v2 är standardvärdet för [ConnectionPolicy. getMaxPoolSize](/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) 100. Använd [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) för att ändra värdet.

4. **Justera parallella frågor för partitionerade samlingar**

    Azure Cosmos DB Sync Java SDK-version 1.9.0 och senare stöder parallella frågor, vilket gör att du kan fråga en partitionerad samling parallellt. Mer information finns i [kod exempel](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) för att arbeta med SDK: er. Parallella frågor är utformade för att förbättra svars tid och data flöde för deras serie motsvarighet.

    (a) ***fin justering \: av setMaxDegreeOfParallelism*** -parallella frågor fungerar genom att fråga flera partitioner parallellt. Data från en enskild partitionerad samling hämtas dock i serie med avseende på frågan. Använd [setMaxDegreeOfParallelism](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) för att ställa in antalet partitioner som har maximal chans att uppnå den mest utförda frågan, förutsatt att alla andra system villkor är desamma. Om du inte känner till antalet partitioner kan du använda setMaxDegreeOfParallelism för att ange ett högt antal, och systemet väljer det lägsta (antal partitioner, indata från användaren) som den högsta graden av parallellitet. 

    Det är viktigt att Observera att parallella frågor ger de bästa fördelarna om data är jämnt fördelade över alla partitioner med avseende på frågan. Om den partitionerade samlingen är partitionerad, så att alla eller en majoritet av de data som returneras av en fråga är koncentrerade i några partitioner (en partition i värsta fall), skulle prestandan för frågan bli Flask hals av dessa partitioner.

    (b) ***justering av \: setMaxBufferedItemCount*** parallell fråga är utformad för att hämta resultat när den aktuella gruppen med resultat bearbetas av klienten. För hämtning bidrar till den totala tids fördröjnings förbättringen av en fråga. setMaxBufferedItemCount begränsar antalet i förväg hämtade resultat. Genom att ange [setMaxBufferedItemCount](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) till det förväntade antalet returnerade resultat (eller ett högre tal) gör detta att frågan får maximal nytta av för hämtning.

    För hämtning fungerar på samma sätt oavsett MaxDegreeOfParallelism, och det finns en enda buffert för data från alla partitioner.  

5. **Implementera backoff med getRetryAfterInMilliseconds-intervall**

    Under prestanda testningen bör du öka belastningen tills en låg frekvens av begär Anden blir begränsad. Om detta är begränsat bör klient programmet backoff vid begränsningen för det Server-angivna återförsöksintervallet. Genom att respektera backoff garanterar du att du tillbringar minimal tid på att vänta mellan återförsök. Princip support för återförsök ingår i version 1.8.0 och senare av [Azure Cosmos DB Sync Java SDK](./sql-api-sdk-java.md). Mer information finns i [getRetryAfterInMilliseconds](/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Skala ut din klient arbets belastning**

    Om du testar med höga data flödes nivåer (>50 000 RU/s) kan klient programmet bli Flask halsen på grund av att datorn capping ut på processor-eller nätverks användning. Om du når den här punkten kan du fortsätta att skicka det Azure Cosmos DB kontot ytterligare genom att skala ut dina klient program över flera servrar.

7. **Använd namn baserat adressering**

    Använd namnbaserade adressering, där länkar har formatet `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` , i stället för SelfLinks ( \_ Self), som har formatet `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` för att undvika att hämta ResourceIds för alla resurser som används för att skapa länken. Som de här resurserna kommer att återskapas (eventuellt med samma namn) kan cachelagring av dem inte vara till hjälp.

   <a id="tune-page-size"></a>
8. **Justera sid storleken för frågor/läsa feeds för bättre prestanda**

    När du utför en Mass läsning av dokument med hjälp av funktionen för att läsa feeds (till exempel [readDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) eller när du utfärdar en SQL-fråga, returneras resultatet i ett segment om resultat mängden är för stor. Som standard returneras resultaten i segment om 100 objekt eller 1 MB, beroende på vilken gräns som nåtts först.

    För att minska antalet nätverks fördröjningar som krävs för att hämta alla tillämpliga resultat kan du öka sid storleken med rubriken [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) till upp till 1000. I de fall där du bara behöver visa några få resultat, till exempel om ditt användar gränssnitt eller ditt program-API bara returnerar 10 resultat en gång, kan du också minska sid storleken till 10 för att minska data flödet som används för läsningar och frågor.

    Du kan också ange sid storleken med SetPageSize- [metoden](/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize).

## <a name="indexing-policy"></a>Indexeringsprincip
 
1. **Utesluta sökvägar som inte används från indexering för att få snabbare skrivning**

    Med Azure Cosmos DB indexerings principen kan du ange vilka dokument Sök vägar som ska tas med eller undantas från indexering genom att använda indexerings Sök vägar ([setIncludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) och [setExcludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)). Användningen av indexerings Sök vägar kan ge bättre skriv prestanda och lägre index lagring för scenarier där fråge mönstren är kända i förväg, eftersom indexerings kostnader direkt korreleras med antalet unika sökvägar som indexeras.  Följande kod visar till exempel hur du undantar ett helt avsnitt (Delträd) av dokumenten från indexering med jokertecknet "*".


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-indexing"></a>Synkronisera Java SDK v2 (maven com. Microsoft. Azure:: Azure-DocumentDB)

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

## <a name="throughput"></a>Dataflöde
<a id="measure-rus"></a>

1. **Mått och justering för lägre enheter för programbegäran/andra användning**

    Azure Cosmos DB erbjuder en omfattande uppsättning databas åtgärder, inklusive relationella och hierarkiska frågor med UDF: er, lagrade procedurer och utlösare – allt som körs på dokumenten i en databas samling. Den kostnad som hör till var och en av dessa operationer varierar baserat på vilken CPU, vilka IO-resurser och hur mycket minne som krävs för att slutföra operationen. I stället för att tänka på och hantera maskin varu resurser kan du tänka på en enhet för begäran (RU) som ett enda mått för de resurser som krävs för att utföra olika databas åtgärder och betjäna en programbegäran.

    Data flödet har allokerats baserat på antalet enheter för [programbegäran](request-units.md) som angetts för varje behållare. Den begärda enhets förbrukningen utvärderas som en taxa per sekund. Program som överskrider den allokerade enhets hastigheten för deras behållare är begränsade tills hastigheten sjunker under den allokerade nivån för behållaren. Om ditt program kräver en högre data flödes nivå kan du öka data flödet genom att tillhandahålla ytterligare enheter för programbegäran. 

    Komplexiteten i en fråga påverkar hur många enheter för programbegäran som används för en åtgärd. Antalet predikat, typen av predikat, antalet UDF: er och storleken på käll data uppsättningen påverkar kostnaden för frågor.

    Om du vill mäta omkostnaderna för en åtgärd (skapa, uppdatera eller ta bort) kan du kontrol lera huvudet [x-MS-Request-avgift](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (eller motsvarande RequestCharge-egenskap i [ResourceResponse \<T> ](/java/api/com.microsoft.azure.documentdb.resourceresponse) eller [FeedResponse \<T> ](/java/api/com.microsoft.azure.documentdb.feedresponse) för att mäta antalet enheter för programbegäran som används av dessa åtgärder.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>Synkronisera Java SDK v2 (maven com. Microsoft. Azure:: Azure-DocumentDB)

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Begär ande avgiften som returnerades i den här rubriken är en bråkdel av ditt etablerade data flöde. Om du till exempel har 2000 RU/s etablerad, och om föregående fråga returnerar 1000 1 KB-dokument, är kostnaden för åtgärden 1000. I och med den här servern bevarar servern bara två sådana begär Anden innan hastigheten begränsar efterföljande begär Anden. Mer information finns i [enheter för programbegäran](request-units.md) och [Kalkylatorn för begär ande](https://www.documentdb.com/capacityplanner)enheter.
   <a id="429"></a>
1. **Hastighets begränsning/begär ande frekvens för stor**

    När en klient försöker överskrida det reserverade data flödet för ett konto, finns det ingen prestanda försämring på servern och ingen användning av data flödes kapaciteten utöver den reserverade nivån. Servern kommer att förebyggande syfte avsluta begäran med RequestRateTooLarge (HTTP-statuskod 429) och returnera huvudet [x-MS-retry-efter-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) som anger hur lång tid i millisekunder som användaren måste vänta innan begäran försöker igen.
    
    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```
    SDK: erna fångar alla implicita dessa svar, med den server-angivna återförsöket-efter-rubriken och gör om begäran. Om ditt konto inte kan nås samtidigt av flera klienter kommer nästa försök att lyckas.

    Om du har mer än en klient ackumulerad på ett konsekvent sätt över begär ande frekvensen är standard antalet nya försök som för närvarande är 9 internt av klienten inte tillräckligt. i det här fallet genererar klienten en [DocumentClientException](/java/api/com.microsoft.azure.documentdb.documentclientexception) med status kod 429 i programmet. Standard antalet återförsök kan ändras genom att använda [setRetryOptions](/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) på [ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) -instansen. Som standard returneras DocumentClientException med status kod 429 efter en ackumulerad vänte tid på 30 sekunder om begäran fortsätter att köras över begär ande frekvensen. Detta inträffar även om det aktuella antalet återförsök är mindre än max antalet försök, måste det vara standardvärdet 9 eller ett användardefinierat värde.

    Även om det automatiserade återförsöket hjälper till att förbättra återhämtning och användbarhet för de flesta program, kan det komma på strider när du gör prestanda mått, särskilt när du mäter svars tid.  Den klientbaserade svars tiden kommer att inaktive ras om experimentet träffar Server begränsningen och gör att klient-SDK: n kan försöka tyst igen. För att undvika fördröjningar vid prestanda experiment kan du mäta den avgift som returneras av varje åtgärd och se till att förfrågningarna fungerar under den reserverade begär ande frekvensen. Mer information finns i [enheter för programbegäran](request-units.md).
3. **Utforma för mindre dokument för högre dataflöde**

    Begär ande avgiften (bearbetnings kostnaden för begäran) för en specifik åtgärd är direkt korrelerad med dokumentets storlek. Åtgärder i stora dokument kostar mer än åtgärder för små dokument.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du utformar programmet för skalning och höga prestanda finns i [partitionering och skalning i Azure Cosmos DB](partitioning-overview.md).