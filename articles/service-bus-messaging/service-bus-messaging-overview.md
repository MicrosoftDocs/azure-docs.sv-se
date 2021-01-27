---
title: Översikt över Azure Service Bus-meddelanden | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure Service Bus, en fullständigt hanterad meddelande Broker för företags integrering.
ms.topic: overview
ms.date: 11/20/2020
ms.openlocfilehash: 7453e8dd300ad754fb58489f059670af209314ab
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881609"
---
# <a name="what-is-azure-service-bus"></a>Vad är Azure Service Bus?
Microsoft Azure Service Bus är en fullständigt hanterad meddelande tjänst för företags meddelanden med meddelande köer och publicera prenumerations ämnen. Service Bus används för att frikoppla program och tjänster från varandra, vilket ger följande fördelar:

- Belastnings Utjämnings arbete mellan konkurrerande arbetare
- Routning och överföring av data och kontroll över tjänst-och program gränser på ett säkert sätt
- Koordinera transaktions arbete som kräver en hög Tillförlitlighets nivå 

## <a name="overview"></a>Översikt
Data överförs mellan olika program och tjänster med *meddelanden*. Ett meddelande är en behållare dekorerad med metadata och innehåller data. Data kan vara vilken typ av information som helst, inklusive strukturerade data som kodats med vanliga format, till exempel: JSON, XML, Apache Avro, oformaterad text.

Några vanliga scenarier för meddelanden är:

* *Meddelanden*. Överför affärs data, till exempel försäljnings-eller inköps order, journaler eller lager förflyttningar.
* Frikopplade *program*. Förbättra tillförlitligheten och skalbarheten för program och tjänster. Tillverkare och konsument behöver inte vara online eller tillgängliga på samma gång. [Belastningen är utjämnad](/azure/architecture/patterns/queue-based-load-leveling) så att trafik toppar inte overtax en tjänst. 
* *Belastnings utjämning*. Tillåt att flera [konkurrerande användare](/azure/architecture/patterns/competing-consumers) kan läsa från en kö på samma gång, och samtidigt få exklusiv ägande rätt till vissa meddelanden. 
* *Ämnen och prenumerationer*. Aktivera 1:*n* relationer mellan [utgivare och prenumeranter](/azure/architecture/patterns/publisher-subscriber), så att prenumeranter kan välja specifika meddelanden från en publicerad meddelande ström.
* *Transaktioner*. Gör att du kan utföra flera åtgärder, allt inom ramen för en atomisk transaktion. Följande åtgärder kan till exempel utföras i omfånget för en transaktion.  

    1. Hämta ett meddelande från en kö.
    2. Publicera resultat från bearbetning till en eller flera olika köer.
    3. Flytta Indataporten från den ursprungliga kön. 
    
    Resultatet blir bara synligt för underordnade konsumenter när det lyckas, inklusive den framgångna indatamängden, vilket gör det möjligt att bearbeta semantiken en gång. Den här transaktions modellen är en robust grund för mönstret för [kompenserande transaktioner](/azure/architecture/patterns/compensating-transaction) i den större lösnings kontexten. 
* *Message-sessioner*. Implementera storskalig samordning av arbets flöden och multiplex-överföringar som kräver strikt meddelande ordning eller meddelande uppskjutande.

Om du är van vid andra meddelande utjämnare som Apache ActiveMQ, liknar Service Bus koncept det du känner till. Eftersom Service Bus är ett PaaS-erbjudande (Platform-as-a-Service) är en viktig skillnad att du inte behöver oroa dig för följande åtgärder. Azure tar hand om dessa sysslor åt dig. 

- Placera loggar och hantera disk utrymme
- Hantera säkerhets kopior
- Hålla operativ systemen eller produkterna som korrigeras
- Bekymra dig om maskin varu problem 
- Växling till reserv dator

## <a name="compliance-with-standards-and-protocols"></a>Efterlevnad med standarder och protokoll

Det primära Wire-protokollet för Service Bus är [Advanced Messaging Queueing Protocol (AMQP) 1,0](service-bus-amqp-overview.md), en öppen ISO/IEC-standard. Det gör det möjligt för kunder att skriva program som fungerar mot Service Bus och lokala mäklare som ActiveMQ eller RabbitMQ. [AMQP-protokoll guiden](service-bus-amqp-protocol-guide.md) innehåller detaljerad information om du vill bygga en sådan abstraktion.

[Service Bus Premium](service-bus-premium-messaging.md) är helt kompatibelt med JMS (Java/Jakarta ee [Java Message Service) 2,0](how-to-use-java-message-service-20.md) API. Och Service Bus standard stöder JMS 1,1-delmängden som fokuserar på köer. JMS är en gemensam abstraktion för meddelande utjämnare och kan integreras med många program och ramverk, inklusive det populära våren-ramverket. Om du vill växla från andra utjämnare till Azure Service Bus behöver du bara återskapa topologin för köer och ämnen och ändra beroenden och konfigurationen för klient leverantören. Ett exempel finns i [migrations guiden för ActiveMQ](migrate-jms-activemq-to-servicebus.md).

## <a name="concepts-and-terminology"></a>Begrepp och terminologi 
I det här avsnittet beskrivs begrepp och terminologi för Service Bus.

### <a name="namespaces"></a>Namnrymder
Ett namn område är en behållare för alla meddelande komponenter. Flera köer och ämnen kan finnas i ett enda namn område och namn områden fungerar ofta som program behållare. 

Ett namn område kan jämföras med en "Server" i terminologin för andra utjämnare, men begreppen är inte direkt likvärdiga. Ett Service Bus-namnområde är din egen kapacitets sektor i ett stort kluster som består av dussin tals alla-aktiva virtuella datorer. Det kan eventuellt omfatta tre [tillgänglighets zoner i Azure](../availability-zones/az-overview.md). Så du får alla tillgänglighets-och robusta fördelar med att köra meddelande utjämningen i enorma skala. Och du behöver inte bekymra dig om underliggande komplexitet. Service Bus är "Server lös" meddelanden.

### <a name="queues"></a>Köer
Meddelanden skickas till och tas emot från *köer*. Köer lagrar meddelanden tills det mottagande programmet är tillgängligt för att ta emot och bearbeta dem.

![Kö](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Meddelanden i köer ordnas och tidsstämplas vid ankomsten. När tjänsten har godkänts av Service Broker hålls meddelandet alltid varaktigt i tredubbel redundant lagring, sprids över tillgänglighets zoner om namn området är aktiverat som zon. Service Bus lämnar aldrig meddelanden i minnet eller flyktig lagring efter att de rapporter ATS till klienten som accepterad.

Meddelanden levereras i *pull* -läge och levererar bara meddelanden när de begärs. Till skillnad från den upptagna-avsöknings modellen för några andra moln köer kan pull-åtgärden vara lång och bara slutföras när ett meddelande är tillgängligt. 

### <a name="topics"></a>Ämnen

Du kan också använda *ämnen* för att skicka och ta emot meddelanden. Medan en kö oftast används för kommunikation från punkt till punkt är ämnen användbara i scenarier med publicering/prenumeration.

![Avsnitt](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Ämnen kan ha flera, oberoende prenumerationer som ansluts till ämnet och fungerar på samma sätt som köer från mottagar sidan. En prenumerant på ett ämne får en kopia av varje meddelande. Prenumerationer kallas entiteter. Prenumerationer är beständiga som standard, men kan konfigureras att förfalla och sedan tas bort automatiskt. Med hjälp av JMS-API Service Bus: et kan du också skapa temporära prenumerationer som finns under anslutningens varaktighet.

Du kan definiera regler för en prenumeration. En prenumerations regel har ett *filter* för att definiera ett villkor för meddelandet som ska kopieras till prenumerationen och en valfri *åtgärd* som kan ändra metadata för meddelanden. Mer information finns i [avsnittet filter och åtgärder](topic-filters.md). Den här funktionen är användbar i följande scenarier:

- Du vill inte att en prenumeration ska ta emot alla meddelanden som skickas till ett ämne.
- Du vill markera meddelanden med extra metadata när de passerar en prenumeration.

## <a name="advanced-features"></a>Avancerade funktioner

Service Bus innehåller avancerade funktioner som gör att du kan lösa mer komplexa meddelande problem. I följande avsnitt beskrivs flera av de här funktionerna.

### <a name="message-sessions"></a>Meddelandesessioner

Om du vill skapa en första in-och utgångs-(FIFO) garanti i Service Bus använder du sessioner. Message-sessioner möjliggör exklusiv, ordnad hantering av icke-bundna sekvenser av relaterade meddelanden. För att kunna hantera sessioner i storskaliga system med hög tillgänglighet kan du också använda sessionen för att lagra sessionstillstånd, vilket gör att sessioner kan flyttas mellan hanterare på ett säkert sätt. Mer information finns i [meddelande sessioner: först in, först ut (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Vidarebefordrar

Funktionen vidarebefordran länkar en kö eller en prenumeration till en annan kö eller ett ämne i samma namnrymd. När du använder den här funktionen flyttas Service Bus automatiskt meddelanden från en kö eller en prenumeration till en målkö eller ett ämne. Alla sådana flyttningar genomförs med en transaktion. Mer information finns i [länkning Service Bus entiteter med vidarebefordran](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Kö för obeställbara meddelanden

Alla Service Bus köer och ämnes prenumerationer har en associerad kö för obeställbara meddelanden (DLQ). Ett DLQ innehåller meddelanden som uppfyller följande kriterier: 

- De kan inte levereras till någon mottagare.
- Tids gränsen uppnåddes.
- De är uttryckligen sidelined av det mottagande programmet. 

Meddelanden i kön för obeställbara meddelanden kommenteras med orsaken till varför de har placerats där. Kön för obeställbara meddelanden har en särskild slut punkt, men fungerar annars som en vanlig kö. Ett program eller ett verktyg kan bläddra i en DLQ eller ur kö. Du kan också vidarebefordra från en kö med obeställbara meddelanden. Mer information finns i [Översikt över Service Bus köer för obeställbara meddelanden](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Schemalagd leverans

Du kan skicka meddelanden till en kö eller ett ämne för fördröjd bearbetning, ange en tid då meddelandet blir tillgängligt för användning. Schemalagda meddelanden kan också avbrytas. Mer information finns i [schemalagda meddelanden](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Skjut upp meddelanden

En kö-eller prenumerations klient kan skjuta upp hämtningen av ett mottaget meddelande till ett senare tillfälle. Meddelandet kan ha skickats ut ur en förväntad ordning och klienten vill vänta tills den får ett annat meddelande. Uppskjutna meddelanden finns kvar i kön eller prenumerationen och måste återaktiveras med hjälp av deras tilldelade ordnings nummer. Mer information finns i [meddelande avstängning](message-deferral.md).

### <a name="batching"></a>Batchbearbetning

Med batching på klient sidan kan en kö-eller ämnes klient samla en uppsättning meddelanden och överföra dem tillsammans. Det är ofta möjligt att spara bandbredd eller öka data flödet. Mer information finns i [batching av klient sidan](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transaktioner

En transaktion grupper två eller flera åtgärder tillsammans i ett *körnings område*. Med Service Bus kan du gruppera åtgärder mot flera meddelande enheter inom omfånget för en enskild transaktion. En Message-entitet kan vara en kö, ett ämne eller en prenumeration. Mer information finns i [Översikt över Service Bus transaktions bearbetning](service-bus-transactions.md).

### <a name="autodelete-on-idle"></a>Ta bort vid inaktivitet

Vid borttagning vid inaktivitet kan du ange ett inaktivt intervall efter vilket en kö eller ämnes prenumeration tas bort automatiskt. Minimilängden är 5 minuter. Mer information finns i [egenskapen QueueDescription. AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Dubblettidentifiering
Funktionen för dubblettidentifiering gör att avsändaren kan skicka samma meddelande igen och för att koordinatorn ska kunna släppa en potentiell dubblett. Dubblettidentifiering baseras på spårning av `message-id` egenskapen för ett meddelande, vilket innebär att programmet måste ta hand om att använda samma värde när meddelandet skickas igen, vilket kan vara direkt härlett från vissa programspecifika sammanhang. Mer information finns i [dubblettidentifiering](duplicate-detection.md).

### <a name="geo-disaster-recovery"></a>Geohaveriberedskap

När en Azure-region upplever avbrott gör funktionen för haveri beredskap att data bearbetningen fortsätter att fungera i en annan region eller data Center. Funktionen sparar en strukturell spegling av ett namn område som är tillgängligt i den sekundära regionen och tillåter namn rymds identiteten att växla till det sekundära namn området. Redan skickade meddelanden finns kvar i det tidigare primära namn området för återställning när tillgänglighets avsnittet är under sidor. Mer information finns i [Azure Service Bus geo-Disaster Recovery](service-bus-geo-dr.md).

### <a name="security"></a>Säkerhet

Service Bus stöder standard [AMQP 1,0](service-bus-amqp-overview.md) [-och http-eller rest-](/rest/api/servicebus/) protokoll samt deras respektive säkerhetsfunktioner, inklusive TLS (Transport Level Security). Klienter kan auktoriseras för åtkomst med hjälp av [signaturen för delad åtkomst](service-bus-sas.md) eller [Azure Active Directory](service-bus-authentication-and-authorization.md) rollbaserad säkerhet. 

För skydd mot oönskad trafik tillhandahåller Service Bus [säkerhetsfunktioner](network-security.md) som IP-brandvägg och integrering med virtuella nätverk. 

## <a name="client-libraries"></a>Klientbibliotek

Fullständigt stöd för Service Bus klient bibliotek är tillgängliga via Azure SDK.

- [Azure Service Bus för .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus bibliotek för Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Azure Service Bus Provider för Java JMS 2,0](how-to-use-java-message-service-20.md)
- [Azure Service Bus moduler för Java Script och TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus bibliotek för python](/python/api/overview/azure/servicebus?preserve-view=true)

[Azure Service Bus primärt protokoll är AMQP 1,0](service-bus-amqp-overview.md) och kan användas från valfri AMQP 1,0-kompatibel protokoll klient. Flera AMQP-klienter med öppen källkod har exempel som tydligt demonstrerar Service Bus samverkan. Granska [AMQP 1,0-protokoll guiden](service-bus-amqp-protocol-guide.md) för att förstå hur du använder Service Bus-funktioner med AMQP 1,0-klienter direkt.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Integrering

Service Bus är fullständigt integrerat med många Microsoft-och Azure-tjänster, till exempel:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Power Platform](https://powerplatform.microsoft.com/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med Service Bus-meddelanden, kan du läsa följande artiklar:

* Information om hur du jämför Azure Messaging-tjänster finns i [jämförelse av tjänster](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Försök att använda snabb starterna för [.net](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md)eller [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Information om hur du hanterar Service Bus-resurser finns i [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Mer information om standard-och Premium nivåerna och deras priser finns i [Service Bus priser](https://azure.microsoft.com/pricing/details/service-bus/).
* Läs mer om prestanda och svars tider för Premium-nivån i [Premium-meddelanden](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).