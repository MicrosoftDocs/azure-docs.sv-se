---
title: Diagnostisera och Felsök Azure Cosmos DB asynkron Java SDK v2
description: Använd funktioner som loggning på klient sidan och andra verktyg från tredje part för att identifiera, diagnostisera och felsöka Azure Cosmos DB problem i asynkron Java SDK v2.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: b39a74bd06f942cf21d201c8cef48bc6dfc57d46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548094"
---
# <a name="troubleshoot-issues-when-you-use-the-azure-cosmos-db-async-java-sdk-v2-with-sql-api-accounts"></a>Felsöka problem när du använder Azure Cosmos DB asynkron Java SDK v2 med SQL API-konton
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Asynkron Java-SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Detta är *inte* den senaste Java SDK: n för Azure Cosmos DB! Du bör uppgradera projektet till [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) och sedan läsa [fel söknings guiden](troubleshoot-java-sdk-v4-sql.md)för Azure Cosmos DB Java SDK v4. Följ anvisningarna i guiden [migrera till Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) -guide och [reaktor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) för att uppgradera. 
>
> Den här artikeln beskriver fel sökning för Azure Cosmos DB asynkron Java SDK v2. Mer information finns i avsnittet Azure Cosmos DB async Java SDK v2, [versions anmärkningar](sql-api-sdk-async-java.md), [maven-lagringsplats](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) och [prestanda tips](performance-tips-async-java.md) .
>

Den här artikeln beskriver vanliga problem, lösningar, diagnostiska steg och verktyg när du använder [Java ASYNC SDK](sql-api-sdk-async-java.md) med Azure Cosmos DB SQL API-konton.
Java Async SDK tillhandahåller logisk representation på klientsidan för att få åtkomst till Azure Cosmos DB SQL API. I den här artikeln beskrivs verktyg och metoder för att hjälpa dig om du stöter på problem.

Börja med den här listan:

* Ta en titt på avsnittet [vanliga problem och lösningar] i den här artikeln.
* Titta på SDK: n, som är tillgänglig [öppen källkod på GitHub](https://github.com/Azure/azure-cosmosdb-java). Avsnittet innehåller ett [problem](https://github.com/Azure/azure-cosmosdb-java/issues) som är aktivt övervakat. Kontrol lera om det finns liknande problem med en lösning som redan har arkiverats.
* Granska [prestanda tipsen](performance-tips-async-java.md)och följ rekommendationerna.
* Läs resten av den här artikeln om du inte hittar någon lösning. Ange sedan ett [GitHub-problem](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Vanliga fel och lösningar

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Nätverks problem, nett timeout-fel för läsning, låg genom strömning, hög latens

#### <a name="general-suggestions"></a>Allmänna förslag
* Kontrol lera att appen körs i samma region som ditt Azure Cosmos DB-konto. 
* Kontrol lera CPU-användningen på värden där appen körs. Om CPU-användningen är 90 procent eller mer kör du din app på en värd med en högre konfiguration. Eller så kan du distribuera belastningen på fler datorer.

#### <a name="connection-throttling"></a>Anslutnings begränsning
Anslutnings begränsning kan inträffa på grund av en [anslutnings gräns på en värddator] eller [Azure SNAT-port (Pat)].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Anslutnings gräns på en värddator
Vissa Linux-system, till exempel Red Hat, har en övre gräns för det totala antalet öppna filer. Sockets i Linux implementeras som filer, så det här antalet begränsar även det totala antalet anslutningar.
Kör följande kommando.

```bash
ulimit -a
```
Antalet tillåtna öppna filer, som identifieras som "nofile", måste ha minst dubbel storlek på anslutningspoolen. Mer information finns i [prestanda tips](performance-tips-async-java.md).

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Port överbelastning för Azure SNAT (PAT)

Om din app distribueras på Azure Virtual Machines utan en offentlig IP-adress, upprättar [Azure SNAT-portar](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) som standard anslutningar till en slut punkt utanför den virtuella datorn. Antalet anslutningar som tillåts från den virtuella datorn till Azure Cosmos DB slut punkten begränsas av [Azure SNAT-konfigurationen](../load-balancer/load-balancer-outbound-connections.md#preallocatedports).

 Azure SNAT-portar används bara när den virtuella datorn har en privat IP-adress och en process från den virtuella datorn försöker ansluta till en offentlig IP-adress. Det finns två lösningar för att undvika begränsning av Azure SNAT:

* Lägg till din Azure Cosmos DB tjänst slut punkt i under nätet för ditt Azure Virtual Machines virtuella nätverk. Mer information finns i [Azure Virtual Network Service-slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md). 

    När tjänstens slut punkt Aktiver ATS skickas inte längre begär Anden från en offentlig IP-adress till Azure Cosmos DB. I stället skickas det virtuella nätverket och under nätets identitet. Den här ändringen kan leda till att brand väggen släpper om bara offentliga IP-adresser är tillåtna. Om du använder en brand vägg, när du aktiverar tjänstens slut punkt, lägger du till ett undernät i brand väggen med hjälp av [Virtual Network ACL: er](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Tilldela en offentlig IP-adress till din virtuella Azure-dator.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Det går inte att kontakta tjänsten – brand väggen
``ConnectTimeoutException`` anger att SDK inte kan komma åt tjänsten.
Du kan få ett problem som liknar följande när du använder direkt läge:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Om du har en brand vägg som körs på din app-dator öppnar du Port intervallet 10 000 till 20 000 som används i direkt läge.
Följ även [anslutnings gränsen på en värddator](#connection-limit-on-host).

#### <a name="http-proxy"></a>HTTP-proxy

Om du använder en HTTP-proxy kontrollerar du att den har stöd för det antal anslutningar som kon figurer ATS i SDK `ConnectionPolicy` .
Annars är det problem med ansikts anslutning.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Ogiltigt kodnings mönster: blockerar nett IO-tråd

SDK: n använder ett [delnäts](https://netty.io/) -IO-bibliotek för att kommunicera med Azure Cosmos dB. SDK: n har asynkrona API: er och använder icke-blockerande IO-API: er för nettning. SDK: s i/o-arbete utförs i i/o-nettning. Antalet NetIO-nättråder har kon figurer ATS att vara samma som antalet CPU-kärnor på App-datorn. 

De Netta IO-trådarna är avsedda att endast användas för icke-blockerande av nett i/o-arbete. SDK: n returnerar API-anropets anrops resultat på en av de nett IO-trådarna till appens kod. Om appen utför en långvarig åtgärd när den tar emot resultat på den Netta tråden kanske SDK: n inte har tillräckligt många IO-trådar för att utföra sitt interna IO-arbete. Sådan app-kodning kan resultera i låg genom strömning, hög latens och `io.netty.handler.timeout.ReadTimeoutException` haverier. Lösningen är att byta tråd när du vet att åtgärden tar tid.

Ta till exempel en titt på följande kodfragment. Du kan utföra långvarigt arbete som tar mer än några millisekunder på den Netta tråden. I så fall kan du komma i ett tillstånd där det inte finns någon NetIO-tråd för att bearbeta i/o-arbete. Därför får du ett ReadTimeoutException-problem.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-readtimeout"></a>Asynkron Java SDK v2 (maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
Lösningen är att ändra den tråd som du utför för arbete som tar tid. Definiera en singleton-instans av Scheduler för din app.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>Asynkron Java SDK v2 (maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
```
Du kan behöva utföra arbete som tar tid, till exempel hårt belastat arbete eller blockera IO. I det här fallet byter du tråd till en arbets tagare som tillhandahålls av med `customScheduler` hjälp av `.observeOn(customScheduler)` API: et.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-applycustomscheduler"></a>Asynkron Java SDK v2 (maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Genom att använda `observeOn(customScheduler)` , släpper du den uppdelade IO-tråden och växlar till din egen anpassade tråd från den anpassade Schemaläggaren. Den här ändringen löser problemet. Du får inte `io.netty.handler.timeout.ReadTimeoutException` längre ett problem.

### <a name="connection-pool-exhausted-issue"></a>Uttömdt problem i anslutningspoolen

`PoolExhaustedException` är ett fel på klient sidan. Det här felet indikerar att din app-arbetsbelastning är högre än den som SDK-anslutningspoolen kan hantera. Öka storleken på anslutningspoolen eller distribuera belastningen på flera appar.

### <a name="request-rate-too-large"></a>Begäran kostar för stor
Det här felet är ett fel på Server sidan. Det anger att du har använt det etablerade data flödet. Försök igen senare. Om du får det här felet ofta kan du fundera på en ökning av insamlings data flödet.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Det gick inte att ansluta till Azure Cosmos DB emulator

Azure Cosmos DB-emulatorns HTTPS-certifikat är självsignerat. För att SDK ska fungera med emulatorn kan du importera emulator-certifikatet till en Java-TrustStore. Mer information finns i [exportera Azure Cosmos DB emulator-certifikat](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problem med beroende konflikter

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

Ovanstående undantag föreslår att du har ett beroende på en äldre version av RxJava-lib (t. ex. 1.2.2). Vår SDK är beroende av RxJava-1.3.8 som innehåller API: er som inte är tillgängliga i den tidigare versionen av RxJava. 

Lösningen för sådana problem är att identifiera vilket annat beroende som går i RxJava-1.2.2 och exkluderar det transitiva beroendet på RxJava-1.2.2 och tillåter CosmosDB SDK att ta den nya versionen.

För att identifiera vilket bibliotek som får i RxJava-1.2.2 kör du följande kommando bredvid din Project pom.xml-fil:
```bash
mvn dependency:tree
```
Mer information finns i maven- [beroende träd guide](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

När du har identifierat att RxJava-1.2.2 är transitivt beroende av vilket andra beroendet av ditt projekt, kan du ändra beroendet för denna lib i din Pom-fil och utesluta RxJava transitivt beroende av det:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Mer information finns i [undantags guiden exkludera transitivt beroende](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Aktivera klient-SDK-loggning

Java async SDK använder SLF4j som loggnings-fasad som stöder loggning till populära loggnings ramverk som log4j och logback.

Om du till exempel vill använda log4j som loggnings ramverk lägger du till följande libs i din Java-classpath.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Lägg även till en log4j-konfiguration.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Mer information finns i [hand boken för sfl4j-loggning](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Nätverks statistik för operativ system
Kör kommandot netstat för att få en uppfattning om hur många anslutningar som finns i tillstånd som `ESTABLISHED` och `CLOSE_WAIT` .

I Linux kan du köra följande kommando.
```bash
netstat -nap
```
Filtrera resultatet till endast anslutningar till Azure Cosmos DB slut punkten.

Antalet anslutningar till Azure Cosmos DB-slutpunkten i `ESTABLISHED` tillstånd kan inte vara större än den konfigurerade storleken på anslutningspoolen.

Många anslutningar till Azure Cosmos DB slut punkten kan vara i det här `CLOSE_WAIT` läget. Det kan finnas mer än 1 000. Ett tal som högt indikerar att anslutningar upprättas och avsluts snabbt. Den här situationen kan orsaka problem. Mer information finns i avsnittet [vanliga problem och lösningar] .

 <!--Anchors-->
[Vanliga fel och lösningar]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Anslutnings gräns på en värddator]: #connection-limit-on-host
[Port överbelastning för Azure SNAT (PAT)]: #snat
