---
title: Vanliga frågor och svar – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en lista med vanliga frågor och svar (FAQ) för Azure Event Hubs och deras svar.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: e6fd4814e771d03827e51f1cd5ee182c9e432cc5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98696116"
---
# <a name="event-hubs-frequently-asked-questions"></a>Vanliga frågor och svar om Event Hubs

## <a name="general"></a>Allmänt

### <a name="what-is-an-event-hubs-namespace"></a>Vad är ett Event Hubs namn område?
Ett namn område är en omfattnings behållare för Event Hub/Kafka-ämnen. Det ger dig ett unikt [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Ett namn område fungerar som en program behållare som kan vara ett hus för flera Event Hub-och Kafka-ämnen. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>När skapar jag ett nytt namn område eller använder ett befintligt namn område?
Kapacitets tilldelningar ([data flödes enheter (antal)](#throughput-units)) debiteras på namn områdes nivå. En namnrymd är också kopplad till en region.

Du kanske vill skapa ett nytt namn område i stället för att använda ett befintligt i något av följande scenarier: 

- Du behöver en Event Hub som är associerad med en ny region.
- Du behöver en Event Hub som är associerad med en annan prenumeration.
- Du behöver en Event Hub med en distinkt kapacitets fördelning (det vill säga att kapacitets behovet för namn området med den tillagda händelsehubben skulle överskrida tröskelvärdet på 40 data flödes enheter och du inte vill gå till det dedikerade klustret)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Vad är skillnaden mellan Event Hubs Basic-och standard-nivåerna?

Standard nivån i Azure Event Hubs ger funktioner utöver vad som är tillgängligt på Basic-nivån. Följande funktioner ingår i standard:

* Längre kvarhållning av händelser
* Ytterligare Brokered Connections, med ett överbelastat värde för mer än det antal som ingår
* Mer än en enda [konsument grupp](event-hubs-features.md#consumer-groups)
* [Capture](event-hubs-capture-overview.md)
* [Kafka-integrering](event-hubs-for-kafka-ecosystem-overview.md)

Mer information om pris nivåer, inklusive Event Hubs Dedicated finns i [pris informationen för Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Var finns Azure Event Hubs?

Azure Event Hubs är tillgängligt i alla Azure-regioner som stöds. För en lista går du till sidan [Azure-regioner](https://azure.microsoft.com/regions/) .  

### <a name="can-i-use-a-single-advanced-message-queuing-protocol-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Kan jag använda en enda Advanced Message Queueing Protocol-anslutning (AMQP) för att skicka och ta emot från flera Event Hub?

Ja, så länge alla Event Hub finns i samma namnrymd.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Vad är den maximala kvarhållningsperioden för händelser?

Event Hubs standard nivån stöder för närvarande en maximal kvarhållningsperiod på sju dagar. Händelse hubbar är inte avsedda som permanenta data lager. Kvarhållningsperiod som är större än 24 timmar är avsedda för scenarier där det är lämpligt att spela upp en händelse ström i samma system. Till exempel för att träna eller verifiera en ny maskin inlärnings modell på befintliga data. Om du behöver kvarhålla meddelanden längre än sju dagar, kan du med hjälp av [Event Hubs Capture](event-hubs-capture-overview.md) på händelsehubben hämta data från Händelsehubben till lagrings kontot eller Azure Data Lake tjänst konto som du väljer. Om du aktiverar Capture debiteras du en avgift utifrån dina köpta data flödes enheter.

Du kan konfigurera kvarhållningsperioden för de infångade data på ditt lagrings konto. Funktionen för **livs cykel hantering** i Azure Storage erbjuder en omfattande, regelbaserade princip för generell användning v2-och Blob Storage-konton. Använd principen för att överföra data till lämpliga åtkomst nivåer eller förfaller i slutet av data livs cykeln. Mer information finns i [Hantera Azure Blob Storage-livscykeln](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Hur gör jag för att övervaka mina Event Hubs?
Event Hubs utvärderar uttömmande Mät värden som ger resursernas tillstånd att [Azure Monitor](../azure-monitor/overview.md). De gör det också möjligt att utvärdera den övergripande hälsan för tjänsten Event Hubs inte bara på namn områdes nivån, utan även på enhets nivå. Lär dig mer om vilken övervakning som erbjuds för [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="where-does-azure-event-hubs-store-data"></a><a name="in-region-data-residency"></a>Var lagrar Azure Event Hubs data?
Azure Event Hubs standard-och dedikerade nivåer lagrar metadata och data i regioner som du väljer. När geo-haveri beredskap har kon figurer ATS för ett Azure Event Hubs-namnområde kopieras metadata till den sekundära regionen som du väljer. Den här tjänsten uppfyller därför automatiskt placering-kraven för regions data, inklusive de som anges i [säkerhets Center](https://azuredatacentermap.azurewebsites.net/).

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

## <a name="apache-kafka-integration"></a>Apache Kafka-integrering

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Hur gör jag för att integrera mitt befintliga Kafka-program med Event Hubs?
Event Hubs tillhandahåller en Kafka-slutpunkt som kan användas av befintliga Apache Kafka baserade program. En konfigurations ändring är allt som krävs för att ha PaaS Kafka-upplevelsen. Det är ett alternativ till att köra ditt eget Kafka-kluster. Event Hubs stöder Apache Kafka 1,0 och nyare klient versioner och fungerar med dina befintliga Kafka-program, verktyg och ramverk. Mer information finns i [Event Hubs för Kafka-lagrings platsen](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Vilka konfigurations ändringar måste göras för mitt befintliga program för att prata med Event Hubs?
Om du vill ansluta till en Event Hub måste du uppdatera Kafka-klientens konfiguration. Det gör du genom att skapa en Event Hubs namnrymd och hämta [anslutnings strängen](event-hubs-get-connection-string.md). Ändra start-. servrarna så att de pekar på Event Hubs-FQDN och porten till 9093. Uppdatera sasl.jaas.config för att dirigera Kafka-klienten till din Event Hubs-slutpunkt (vilket är den anslutnings sträng som du har fått), med korrekt autentisering enligt nedan:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

Exempel:

```properties
bootstrap.servers=dummynamespace.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXX";
```
OBS! om sasl.jaas.config inte är en konfiguration som stöds i ramverket hittar du de konfigurationer som används för att ange SASL användar namn och lösen ord och använder dem i stället. Ange användar namnet $ConnectionString och lösen ordet till din Event Hubs anslutnings sträng.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Vad är storleken på meddelandet/händelsen för Event Hubs?
Den maximala meddelande storlek som tillåts för Event Hubs är 1 MB.

## <a name="throughput-units"></a>Genomflödesenheter

### <a name="what-are-event-hubs-throughput-units"></a>Vad är Event Hubs data flödes enheter?
Genom strömning i Event Hubs definieras mängden data i megabyte eller antal (i tusental) av 1 KB-händelser som intränger och utgångs genom Event Hubs. Detta data flöde mäts i data flödes enheter (antal). Köp antal innan du kan börja använda tjänsten Event Hubs. Du kan uttryckligen välja Event Hubs antal antingen med hjälp av portalen eller Event Hubs Resource Manager-mallar. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Gäller data flödes enheter för alla Event Hub i ett namn område?
Ja, data flödes enheter (antal) gäller för alla Event Hub i ett Event Hubs-namnområde. Det innebär att du köper antal på namn områdes nivån och delas mellan händelse hubbarna under denna namnrymd. Varje data flödes enheter bevarar namn området till följande funktioner:

- Upp till 1 MB ingångs händelser per sekund (händelser som skickats till en händelsehubben), men högst 1000 ingress händelser, hanterings åtgärder eller kontroll-API-anrop per sekund.
- Upp till 2 MB utgångs händelser per sekund (händelser som konsumeras från en händelsehubben), men högst 4096 utgående händelser.
- Upp till 84 GB händelse lagring (tillräckligt för standard lagrings perioden på 24 timmar).

### <a name="how-are-throughput-units-billed"></a>Hur faktureras data flödes enheter?
Data flödes enheter (antal) faktureras per timme. Faktureringen baseras på det maximala antalet enheter som valdes under den angivna timmen. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Hur kan jag optimera användningen av mina data flödes enheter?
Du kan börja så lite som en data flödes enhet (data flödes enheter) och aktivera [automatiskt](event-hubs-auto-inflate.md)öknings kraft. Med funktionen för automatisk ökning kan du utöka din antal när din trafik/nytto Last ökar. Du kan också ange en övre gräns för antalet antal.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Hur fungerar funktionen för automatisk ökning i Event Hubs fungera?
Med funktionen för automatisk ökning kan du skala upp dina data flödes enheter (antal). Det innebär att du kan börja med att köpa låg antal och att autoökningen automatiskt ska skala upp dina antal när ingångs ökningen ökar. Det ger dig ett kostnads effektivt alternativ och fullständig kontroll över antalet antal som ska hanteras. Den här funktionen är en funktion för automatisk **skalning** , och du kan helt styra nedskalning av antalet antal genom att uppdatera den. 

Du kanske vill börja med låg data flödes enheter (antal), till exempel 2 antal. Om du förutsäger att din trafik kan växa till 15 antal, aktiverar du funktionen för automatisk ökning på ditt namn område och anger Max gränsen till 15 antal. Nu kan du utöka dina antal automatiskt när trafiken växer.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Är kostnaden kopplad till funktionen för automatisk ökning?
Det finns **ingen kostnad** kopplad till den här funktionen. 

### <a name="how-are-throughput-limits-enforced"></a>Hur tvingas data flödes gränser?
Om den totala ingångs data flödet eller den totala ingångs händelse frekvensen för alla händelse hubbar i ett namn område överskrider den sammanlagda enhets tilldelningen för **data flödet,** begränsas avsändare och får fel meddelanden som anger att den inkommande kvoten har överskridits.

Om det totala **utgående data flödet eller** den totala utgående händelsen i ett namn område överskrider den sammanlagda tilldelningen av data flödes enheter, begränsas mottagarna men inga begränsnings fel genereras. 

Ingångs-och utgångs kvoter tillämpas separat, så att ingen avsändare kan orsaka att händelse förbrukningen saktas ned och att ingen mottagare kan förhindra att händelser skickas till en Event Hub.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-reservedselected"></a>Finns det en gräns för antalet data flödes enheter som kan reserveras/väljas?

När du skapar ett Basic-eller standard-nivå namn område i Azure Portal kan du välja upp till 20 antal för namn området. Skicka en [supportbegäran](../azure-portal/supportability/how-to-create-azure-support-request.md)för att öka den till **exakt** 40 antal.  

1. På sidan **Event Bus-namnrymd** väljer du **ny support förfrågan** på den vänstra menyn. 
1. Följ dessa steg på sidan **ny support förfrågan** :
    1. Beskriv problemet med några få ord för **Sammanfattning**. 
    1. Välj **kvot** för **typ av problem**. 
    1. Välj **begäran om ökning eller minskning av data flödes enhet** för **problem under typ**. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-throughput-units.png" alt-text="Supportbegäran sida":::

Utöver 40 antal erbjuder Event Hubs den resurs-/kapacitets modellen som kallas Event Hubs Dedicated-kluster. Dedikerade kluster säljs i kapacitets enheter (CUs). Mer information finns i [Event Hubs Dedicated-Overview](event-hubs-dedicated-overview.md).

## <a name="dedicated-clusters"></a>Dedikerade kluster

### <a name="what-are-event-hubs-dedicated-clusters"></a>Vad är Event Hubs dedikerade kluster?
Event Hubs Dedicated kluster erbjuder distributioner för enskilda klienter för kunder med de mest krävande kraven. Detta erbjudande skapar ett kapacitets baserat kluster som inte är kopplat till data flödes enheter. Det innebär att du kan använda klustret för att mata in och strömma data som styrs av klustrets processor-och minnes användning. Mer information finns i [Event Hubs Dedicated kluster](event-hubs-dedicated-overview.md).

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Hur gör jag för att skapa ett Event Hubs Dedicated-kluster?
Steg-för-steg-instruktioner och mer information om hur du konfigurerar ett Event Hubs dedikerat kluster finns i [snabb start: skapa ett dedikerat Event Hubs-kluster med Azure Portal](event-hubs-dedicated-cluster-create-portal.md). 


[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]


## <a name="partitions"></a>Partitioner

### <a name="how-many-partitions-do-i-need"></a>Hur många partitioner behöver jag?
Antalet partitioner anges när det skapas och måste vara mellan 1 och 32. Antalet partitioner kan inte ändras på alla nivåer förutom den [dedikerade nivån](event-hubs-dedicated-overview.md), så du bör överväga långsiktig skalning när du anger antalet partitioner. Partitioner är en mekanism för organisering av data som har att göra med vilken underordnad parallellitet som krävs i de program som används. Antalet partitioner i en händelsehubb är direkt kopplat till antalet samtidiga läsare som du förväntar dig. Mer information om partitioner finns i [partitioner](event-hubs-features.md#partitions).

Du kanske vill ange att den ska vara det högsta möjliga värdet, vilket är 32 vid tidpunkten för skapandet. Kom ihåg att om du har mer än en partition resulterar det i händelser som skickas till flera partitioner utan att behållas i ordningen, om du inte konfigurerar avsändare till att bara skicka till en enda partition av 32 som lämnar de återstående 31 partitionerna överflödiga. I det förra fallet måste du läsa händelser över alla 32-partitioner. I det senare fallet finns det ingen tydlig ytterligare kostnad utöver den extra konfiguration som du måste göra på händelse bearbetnings värden.

Event Hubs har utformats för att tillåta en enda partitions läsare per konsument grupp. I de flesta användnings fall räcker standardinställningen för fyra partitioner. Om du vill skala din händelse bearbetning kanske du vill överväga att lägga till ytterligare partitioner. Det finns inga särskilda data flödes gränser för en partition, men det sammanlagda data flödet i namn området begränsas av antalet data flödes enheter. När du ökar antalet data flödes enheter i namn området kan du behöva ytterligare partitioner för att tillåta samtidiga läsare att uppnå sitt eget maximala data flöde.

Men om du har en modell där ditt program har en tillhörighet till en viss partition, kanske det inte är någon förmån för att öka antalet partitioner. Mer information finns i [tillgänglighet och konsekvens](event-hubs-availability-and-consistency.md).

### <a name="increase-partitions"></a>Öka partitioner
Du kan begära att antalet partitioner ska höjas till 40 (exakt) genom att skicka in en support förfrågan. 

1. På sidan **Event Bus-namnrymd** väljer du **ny support förfrågan** på den vänstra menyn. 
1. Följ dessa steg på sidan **ny support förfrågan** :
    1. Beskriv problemet med några få ord för **Sammanfattning**. 
    1. Välj **kvot** för **typ av problem**. 
    1. Under **typ av problem** väljer du **begäran om ändrad partition**. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-increase-partitions.png" alt-text="Öka antalet partitioner":::

Antalet partitioner kan ökas till exakt 40. I det här fallet måste antalet antal också ökas till 40. Om du senare bestämmer dig för att sänka gränsen för data flödes enheter till <= 20, minskas även gränsen för högsta tillåtna partitioner till 32. 

Minskningen av partitioner påverkar inte befintliga händelse nav eftersom partitioner används på händelsens hubbnivå och de är oföränderliga när hubben har skapats. 

## <a name="pricing"></a>Priser

### <a name="where-can-i-find-more-pricing-information"></a>Var hittar jag mer pris information?

Fullständig information om Event Hubs priser finns i [pris informationen för Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Finns det någon avgift för att behålla Event Hubs händelser i mer än 24 timmar?

Event Hubs standard nivån tillåter att meddelande kvarhållning är längre än 24 timmar, i högst sju dagar. Om storleken på det totala antalet lagrade händelser överskrider lagrings tilldelningen för antalet valda data flödes enheter (84 GB per data flödes enhet) debiteras den storlek som överstiger tilldelningen för den publicerade Azure Blob Storage-priset. Lagrings tilldelningen i varje data flödes enhet omfattar alla lagrings kostnader för kvarhållningsperiod på 24 timmar (standard) även om data flödes enheten används upp till maximalt antal inkommande tilldelningar.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hur beräknas och debiteras lagrings storleken för Event Hubs?

Den totala storleken på alla lagrade händelser, inklusive eventuell intern kostnad för händelse rubriker eller disk lagrings strukturer i alla Event Hub, mäts under dagen. Vid slutet av dagen beräknas den högsta lagrings storleken. Den dagliga lagrings tilldelningen beräknas baserat på det minsta antalet data flödes enheter som valdes under dagen (varje data flödes enhet ger en förmån på 84 GB). Om den totala storleken överskrider den beräknade dagliga lagringen debiteras den överskjutande lagringen med hjälp av Azure Blob Storage-taxan (vid den **lokalt redundanta lagrings** takten).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hur beräknas Event Hubs ingress-händelser?

Varje händelse som skickas till en Event Hub räknas som ett fakturerbart meddelande. En *ingress-händelse* definieras som en enhet med data som är mindre än eller lika med 64 kB. En händelse som är mindre än eller lika med 64 KB anses vara en fakturerbar händelse. Om händelsen är större än 64 KB beräknas antalet fakturerbara händelser enligt händelse storleken i multipler av 64 KB. En 8 KB-händelse som skickas till händelsehubben faktureras exempelvis som en händelse, men ett meddelande på 96 KB som skickas till händelsehubben faktureras som två händelser.

Händelser som konsumeras från en Event Hub, och hanterings åtgärder och kontroll anrop, till exempel kontroll punkter, räknas inte som fakturerbara ingångs händelser, men påförs upp till enhets tilldelningen för data flödet.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Gäller Brokered Connection-avgifter för Event Hubs?

Anslutnings avgifterna gäller endast när AMQP-protokollet används. Det finns inga anslutnings avgifter för att skicka händelser med HTTP, oavsett antalet sändande system eller enheter. Om du planerar att använda AMQP (till exempel för att få mer effektiv händelse strömning eller för att aktivera dubbelriktad kommunikation i IoT-kommando och kontroll scenarier) finns information om hur många anslutningar som ingår i varje tjänst nivå på sidan [Event Hubs pris information](https://azure.microsoft.com/pricing/details/event-hubs/) .

### <a name="how-is-event-hubs-capture-billed"></a>Hur faktureras Event Hubs-avbildning?

Avbildning aktive ras när en Event Hub i namn området har alternativet Aktivera. Event Hubs inspelning faktureras per timme per köpt data flödes enhet. När antalet data flödes enheter ökar eller minskar, visar Event Hubs avbildnings faktureringen dessa ändringar i hela Tim ökningar. Mer information om hur du Event Hubs avbildnings fakturering finns i [Event Hubs pris information](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Debiteras jag för det lagrings konto jag väljer för Event Hubs avbildning?

Avbildningen använder ett lagrings konto som du anger när du aktiverar det i en Event Hub. Eftersom det är ditt lagrings konto faktureras alla ändringar för den här konfigurationen till din Azure-prenumeration.

## <a name="quotas"></a>Kvoter

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Finns det några kvoter som är associerade med Event Hubs?

En lista över alla Event Hubs kvoter finns i [kvoter](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Varför kan jag inte skapa ett namn område efter att ha tagit bort det från en annan prenumeration? 
När du tar bort ett namn område från en prenumeration väntar du i fyra timmar innan du skapar det igen med samma namn i en annan prenumeration. Annars kan du få följande fel meddelande: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Vad är några av undantagen som genereras av Event Hubs och deras föreslagna åtgärder?

En lista över möjliga Event Hubs undantag finns i [Översikt över undantag](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnostikloggar

Event Hubs stöder två typer av [diagnostikloggar](event-hubs-diagnostic-logs.md) – avbildning av fel loggar och drift loggar som båda representeras i JSON och som kan aktive ras via Azure Portal.

### <a name="support-and-sla"></a>Support och SLA

Teknisk support för Event Hubs finns på [sidan Microsoft Q&en fråga för Azure Service Bus](/answers/topics/azure-service-bus.html). Support för fakturering och prenumerations hantering ges utan kostnad.

Mer information om service avtal finns på sidan [service nivå avtal](https://azure.microsoft.com/support/legal/sla/) .

## <a name="azure-stack-hub"></a>Azure Stack Hub

### <a name="how-can-i-target-a-specific-version-of-azure-storage-sdk-when-using-azure-blob-storage-as-a-checkpoint-store"></a>Hur kan jag rikta en speciell version av Azure Storage SDK när du använder Azure Blob Storage som en kontroll punkts lagring?
Om du kör den här koden på Azure Stack Hub får du Körfel om du inte riktar in dig på en viss lagrings-API-version. Det beror på att Event Hubs SDK använder det senaste tillgängliga Azure Storage API som är tillgängligt i Azure och som kanske inte är tillgängligt på din Azure Stack Hub-plattform. Azure Stack Hub kan stödja en annan version av Storage BLOB SDK än vad som normalt är tillgängligt på Azure. Om du använder Azure blogg lagring som kontroll punkts Arkiv, kontrollerar du [Azure Storage API-versionen som stöds för din Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) -version och aktiverar den versionen i din kod. 

Om du till exempel kör på Azure Stack Hub version 2005 är den högsta tillgängliga versionen för lagrings tjänsten version 2019-02-02. Som standard använder klient biblioteket för Event Hubs SDK den senaste tillgängliga versionen på Azure (2019-07-07 vid tidpunkten för lanseringen av SDK: n). I det här fallet, förutom följande steg i det här avsnittet, måste du också lägga till kod som mål för Storage Service API-versionen 2019-02-02. Ett exempel på hur du kan rikta en speciell Storage API-version finns i följande exempel för C#, Java, python och Java Script/TypeScript.  

Ett exempel på hur du kan rikta en speciell lagrings-API-version från koden finns i följande exempel på GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)
- Python – [synkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [asynkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)
- [Java Script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) och [typescript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](./event-hubs-about.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Event Hubs automatiskt bred mitt](event-hubs-auto-inflate.md)
