---
title: Använda händelsehubben från Apache Kafka app – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om Apache Kafka support av Azure Event Hubs.
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: b0f0da76bba68f8a66695700d530e871cbd35e3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97861336"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Använda Azure Event Hubs från Apache Kafka-program
Event Hubs tillhandahåller en slut punkt som är kompatibel med Apache Kafka® tillverkare och konsument-API: er som kan användas av de flesta befintliga Apache Kafka klient program som ett alternativ till att köra ditt eget Apache Kafka-kluster. Event Hubs stöder Apache Kafkas tillverkare och klient-API-klienter i version 1,0 och senare.

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Vad innebär Event Hubs för Kafka?

Funktionen Event Hubs för Apache Kafka tillhandahåller ett protokoll huvud ovanpå Azure-Event Hubs som är kompatibelt med Apache Kafka klienter som skapats för Apache Kafka Server version 1,0 och senare och som har stöd för att både läsa från och skriva till Event Hubs, som motsvarar Apache Kafka ämnen. 

Du kan ofta använda Event Hubs Kafka-slutpunkten från dina program utan kod ändringar jämfört med din befintliga Kafka-installation och bara ändra konfigurationen: uppdatera anslutnings strängen i konfigurationer så att den pekar på den Kafka-slutpunkt som exponeras av händelsehubben i stället för att peka på ditt Kafka-kluster. Sedan kan du starta strömmande händelser från dina program som använder Kafka-protokollet i Event Hubs. 

Kafka och Event Hubs är mycket lika likartade: de har båda partitionerade loggar som skapats för strömmande data, där klienten styr vilken del av den kvarhållna loggen som den vill läsa. Följande tabell mappar begrepp mellan Kafka och Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Konceptuell mappning för Kafka och Event Hub

| Kafka-koncept | Event Hubs koncept|
| --- | --- |
| Kluster | Namnområde |
| Avsnitt | Händelsehubb |
| Partition | Partition|
| Konsument grupp | Konsument grupp |
| Offset | Offset|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Viktiga skillnader mellan Apache Kafka och Event Hubs

Även om [Apache Kafka](https://kafka.apache.org/) är program vara som du vanligt vis behöver installera och använda, Event Hubs är en helt hanterad moln intern tjänst. Det finns inga servrar, diskar eller nätverk för att hantera och övervaka och inga utjämnare att överväga eller konfigurera, någonsin. Du skapar ett namn område, som är en slut punkt med ett fullständigt kvalificerat domän namn och sedan skapar du Event Hubs (ämnen) inom det namn området. 

Mer information om Event Hubs och namn områden finns i [Event Hubs funktioner](event-hubs-features.md#namespace). Som en moln tjänst använder Event Hubs en enda stabil virtuell IP-adress som slut punkten, så att klienterna inte behöver känna till utlösarna eller datorerna i ett kluster. Trots att Event Hubs implementerar samma protokoll innebär den här skillnaden att all Kafka-trafik för alla partitioner går att förutsäga genom den här slut punkten i stället för att kräva brand Väggs åtkomst för alla utjämnare i ett kluster.   

Skala i Event Hubs styrs av hur många data flödes enheter du köper, med varje data flödes enhet, vilket innebär att du har 1 megabyte per sekund eller 1000 händelser per sekund i ingressen och dubbelt så mycket som den utgående volymen. Event Hubs kan automatiskt skala upp data flödes enheter när du når data flödes gränsen om du använder funktionen för [Automatisk](event-hubs-auto-inflate.md) ökning. den här funktionen fungerar också med stöd för Apache Kafka-protokollet.  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Är Apache Kafka rätt lösning för din arbets belastning?

När du skapar program med hjälp av Apache Kafka, är det också användbart att förstå att Azure Event Hubs ingår i en flottan av tjänster som också omfattar [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)och [Azure Event Grid](../event-grid/overview.md). 

Vissa leverantörer av kommersiella distributioner av Apache Kafka kan föreslå att Apache Kafka är en ett-avbrott-shoppa för alla dina meddelande plattforms behov, verkligheten är att Apache Kafka inte implementera, t. ex. kön med [konkurrerande konsument](/azure/architecture/patterns/competing-consumers) mönster, har inte stöd för  [publicerings prenumeration](/azure/architecture/patterns/publisher-subscriber) på en nivå som gör det möjligt för prenumeranter att komma åt inkommande meddelanden baserat på andra regler än vanliga förskjutningar, och det har inga anläggningar för att spåra livs cykeln för ett jobb som initierats av ett meddelande eller sidelining fel meddelanden till en kö för obeställbara meddelanden, som är grundläggande för många företags meddelande scenarier.

Om du vill förstå skillnaderna mellan mönster och vilket mönster som passar bäst för tjänsten, kan du gå igenom [alternativen för asynkrona meddelanden i Azures](/azure/architecture/guide/technology-choices/messaging) vägledning. Som Apache Kafka användare kanske du upptäcker att kommunikations Sök vägar som du hittills har realiserat med Kafka, kan realiseras med mycket mindre grundläggande komplexitet och ännu mer kraftfulla funktioner med hjälp av antingen Event Grid eller Service Bus. 

Om du behöver specifika funktioner i Apache Kafka som inte är tillgängliga via Event Hubs för Apache Kafka gränssnittet eller om ditt implementerings mönster överskrider [Event Hubs kvoter](event-hubs-quotas.md), kan du också köra ett [inbyggt Apache Kafka-kluster i Azure HDInsight](../hdinsight/kafka/apache-kafka-introduction.md).  

## <a name="security-and-authentication"></a>Säkerhet och autentisering
Varje gång du publicerar eller använder händelser från en Event Hubs för Kafka försöker klienten komma åt Event Hubs resurserna. Du vill se till att resurserna nås med hjälp av en auktoriserad entitet. När du använder Apache Kafka protokoll med dina klienter kan du ange konfigurationen för autentisering och kryptering med hjälp av SASL-mekanismer. När du använder Event Hubs för Kafka kräver TLS-kryptering (som alla data i överföring med Event Hubs är TLS-krypterat). Det kan du göra genom att ange SASL_SSL alternativet i konfigurations filen. 

Azure Event Hubs tillhandahåller flera alternativ för att ge åtkomst till dina säkra resurser. 

- OAuth 2.0
- Signatur för delad åtkomst (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
Event Hubs integreras med Azure Active Directory (Azure AD), vilket ger en **OAuth 2,0** -kompatibel Central Authorization-Server. Med Azure AD kan du använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge detaljerade behörigheter till dina klient identiteter. Du kan använda den här funktionen med Kafka-klienter genom att ange **SASL_SSL** för protokollet och  **OAUTHBEARER** för mekanismen. Mer information om Azure-roller och-nivåer för omfångs åtkomst finns i [ge åtkomst till Azure AD](authorize-access-azure-active-directory.md).

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Signatur för delad åtkomst (SAS)
Event Hubs tillhandahåller även **signaturer för delad åtkomst (SAS)** för delegerad åtkomst till Event Hubs för Kafka-resurser. Att auktorisera åtkomst med OAuth 2,0-tokenbaserad mekanism ger överlägsen säkerhet och enkel användning över SAS. De inbyggda rollerna kan också eliminera behovet av ACL-baserad auktorisering, som måste underhållas och hanteras av användaren. Du kan använda den här funktionen med Kafka-klienter genom att ange **SASL_SSL** för protokollet och **plait** för mekanismen. 

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Ersätt `{YOUR.EVENTHUBS.CONNECTION.STRING}` med anslutnings strängen för din Event Hubs-namnrymd. Anvisningar om hur du hämtar anslutnings strängen finns i [Hämta en Event Hubs anslutnings sträng](event-hubs-get-connection-string.md). Här är ett exempel på en konfiguration: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

> [!NOTE]
> När du använder SAS-autentisering med Kafka-klienter kopplas inte etablerade anslutningar bort när SAS-nyckeln återskapas. 


#### <a name="samples"></a>Exempel 
En **själv studie kurs** med stegvisa instruktioner för att skapa en händelsehubben och komma åt den med SAS eller OAuth finns i [snabb start: Data strömning med Event Hubs med Kafka-protokollet](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Fler **exempel** som visar hur du använder OAuth med Event Hubs för Kafka finns i [exempel på GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features"></a>Andra Event Hubs funktioner 

Event Hubs för Apache Kafka-funktionen är ett av tre protokoll som är samtidigt tillgängligt på Azure-Event Hubs, vilket kompletterar HTTP-och AMQP. Du kan skriva med något av dessa protokoll och läsa med varandra, så att dina nuvarande Apache Kafka producenter kan fortsätta att publicera via Apache Kafka, men läsaren kan dra nytta av den inbyggda integrationen med Event Hubs AMQP-gränssnittet, till exempel Azure Stream Analytics eller Azure Functions. Du kan enkelt integrera Azure-Event Hubs i AMQP-vägvals nätverk som en mål slut punkt och läsa data ännu via Apache Kafka-integration.  

Dessutom kan Event Hubs funktioner som [Capture](event-hubs-capture-overview.md), som möjliggör extremt mycket kostnads effektiv lagring via Azure Blob Storage och Azure Data Lake Storage och [geo haveri beredskap](event-hubs-geo-dr.md) också arbeta med Event Hubs för Kafka-funktionen.

## <a name="apache-kafka-feature-differences"></a>Skillnader mellan Apache Kafka funktioner 

Målet med Event Hubs för Apache Kafka är att ge till gång till Azure Event Hub-funktioner till program som är låsta i Apache Kafka API och som annars måste säkerhets kopie ras av ett Apache Kafka kluster. 

Som beskrivs [ovan](#is-apache-kafka-the-right-solution-for-your-workload)tillhandahåller Azure Messaging-flottan omfattande och robusta täcknings möjligheter för många meddelande scenarier, och även om följande funktioner för närvarande inte stöds via Event Hubs "stöd för Apache Kafka API, pekar vi där och hur den önskade kapaciteten är tillgänglig.

### <a name="transactions"></a>Transaktioner

[Azure Service Bus](../service-bus-messaging/service-bus-transactions.md) har robust transaktions stöd som gör det möjligt att ta emot och lösa meddelanden och sessioner samtidigt som de skickar utgående meddelanden från meddelande behandling till flera mål enheter under konsekvens skyddet av en transaktion. Funktionen set tillåter inte bara för exakt en bearbetning av varje meddelande i följd, men förhindrar också risken för att en annan konsument bearbetar om samma meddelanden, eftersom det skulle vara fallet med Apache Kafka. Service Bus är den rekommenderade tjänsten för arbets belastningar för transaktions meddelanden.

### <a name="compression"></a>Komprimering

[Komprimerings](https://cwiki.apache.org/confluence/display/KAFKA/Compression) funktionen på klient sidan i Apache Kafka komprimerar en batch med flera meddelanden till ett enda meddelande på producent sidan och expanderar batchen på konsument sidan. Apache Kafka Broker behandlar batchen som ett särskilt meddelande.

Den här funktionen är grundläggande på strider med Azure Event Hubs Multiprotocol-modellen, som tillåter meddelanden, även de som skickas i batchar, som kan hämtas individuellt från Service Broker och via valfritt protokoll. 

Nytto lasten för händelse NAV är en byte-dataström och innehållet kan komprimeras med en algoritm som du väljer. Kodnings formatet Apache Avro stöder komprimering internt.

### <a name="log-compaction"></a>Logg komprimering

Apache Kafka-komprimering är en funktion som gör att du kan ta bort alla utom den sista posten i varje nyckel från en partition, vilket i praktiken sätter ett Apache Kafka ämne i ett nyckel/värde-lager där det sista värdet som lagts till åsidosätter föregående. Den här funktionen är för närvarande inte implementerad av Azure Event Hubs. Mönstret för nyckel värdes lagring, även vid frekventa uppdateringar, stöds mycket bättre av databas tjänster som [Azure Cosmos DB](../cosmos-db/introduction.md). Mer information finns i avsnittet om [logg projektion](event-hubs-federation-overview.md#log-projections) i Event Hubs Federations vägledning. 

### <a name="kafka-streams"></a>Kafka strömmar

Kafka strömmar är ett klient bibliotek för Stream Analytics som är en del av det Apache Kafka projektet med öppen källkod, men är skilt från Apache Kafka Event Stream Broker. 

De vanligaste orsakerna till att Azure Event Hubs-kunder ber om stöd för Kafka-strömmar är att de är intresserade av "ksqlDB"-produkten. "ksqlDB" är ett patentskyddat delat käll projekt som är [licensierat så](https://github.com/confluentinc/ksql/blob/master/LICENSE) att ingen leverantör erbjuder program vara som en tjänst, plattforms-som-tjänst, infrastruktur som en tjänst eller liknande onlinetjänster som konkurrerar med samprodukter eller tjänster av typen "ksqlDB". Praktiskt taget, om du använder ksqlDB, måste du antingen använda Kafka själv eller så måste du använda samverkans moln erbjudanden. Licens villkoren kan också påverka Azure-kunder som erbjuder tjänster för ett ändamål som undantas av licensen.

Fristående och utan ksqlDB har Kafka strömmar färre funktioner än många alternativa ramverk och tjänster, varav de flesta har inbyggda SQL-gränssnitt för strömning och alla som integreras med Azure Event Hubs idag:

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Azure Synapse Analytics (via Event Hubs avbildning)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Akka strömmar](event-hubs-kafka-akka-streams-tutorial.md)

De listade tjänsterna och ramverken kan vanligt vis hämta händelse strömmar och referens data direkt från en mängd olika källor via kort. Kafka-strömmar kan bara hämta data från Apache Kafka och dina analys projekt är därför låsta till Apache Kafka. Om du vill använda data från andra källor måste du först importera data till Apache Kafka med Kafka Connect Framework.

Om du måste använda Kafka Streams Framework på Azure kommer [Apache Kafka i HDInsight](../hdinsight/kafka/apache-kafka-introduction.md) att förse dig med det alternativet. Apache Kafka i HDInsight ger fullständig kontroll över alla konfigurations aspekter av Apache Kafka, samtidigt som de är helt integrerade med olika aspekter av Azure-plattformen, från fel-/uppdaterings domän placering till nätverks isolering för att övervaka integrering. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln angav vi en introduktion till Event Hubs för Kafka. Mer information finns i [Apache Kafka Developer Guide for Azure Event Hubs](apache-kafka-developer-guide.md).