---
title: Jämföra Azure Storage-köer och Service Bus-köer
description: Analyserar skillnader och likheter mellan två typer av köer som erbjuds av Azure.
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 31992aa2012009c51cbeae78010ae8ced65fc872
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928315"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Storage-köer och Service Bus-köer – jämförelser och skillnader
I den här artikeln analyseras skillnaderna och likheter mellan de två typerna av köer som erbjuds av Microsoft Azure: lagrings köer och Service Bus köer. Med hjälp av den här informationen kan du fatta ett mer informerat beslut om vilken lösning som bäst passar dina behov.

## <a name="introduction"></a>Introduktion
Azure stöder två typer av köa mekanismer: **lagrings köer** och **Service Bus köer**.

**Lagrings köer** ingår i [Azure Storage](https://azure.microsoft.com/services/storage/) -infrastrukturen. De gör att du kan lagra stora mängder meddelanden. Du kommer åt meddelanden från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett Queue-meddelande kan vara upp till 64 KB stort. En kö kan innehålla miljon tals meddelanden, upp till den totala kapacitets gränsen för ett lagrings konto. Köer används ofta för att skapa en efter släpning av arbete som ska bearbetas asynkront. Mer information finns i [Vad är Azure Storage köer](../storage/queues/storage-queues-introduction.md).

**Service Bus köer** ingår i en bredare [Azure Messaging](https://azure.microsoft.com/services/service-bus/) -infrastruktur som har stöd för köer, publicera/prenumerera och fler avancerade integrations mönster. De är utformade för att integrera program eller program komponenter som kan omfatta flera kommunikations protokoll, data kontrakt, betrodda domäner eller nätverks miljöer. Mer information om Service Bus köer/ämnen/prenumerationer finns i [Service Bus köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md).


## <a name="technology-selection-considerations"></a>Överväganden vid val av teknik
Lagrings köer och Service Bus köer har en något annorlunda funktions uppsättning. Du kan välja antingen en eller båda, beroende på behoven i din specifika lösning.

När du bestämmer vilken kö-teknik som passar för en specifik lösning bör lösnings arkitekter och utvecklare överväga dessa rekommendationer. 

### <a name="consider-using-storage-queues"></a>Överväg att använda lagrings köer
Som lösnings arkitekt/-utvecklare **bör du överväga att använda lagrings köer** när:

* Ditt program måste lagra över 80 GB meddelanden i en kö.
* Ditt program vill spåra förloppet för att bearbeta ett meddelande i kön. Det är användbart om arbets belastningen som bearbetar ett meddelande kraschar. En annan anställd kan sedan använda den informationen för att fortsätta där den tidigare arbetaren slutade.
* Du behöver loggar på Server sidan för alla transaktioner som har utförts mot dina köer.

### <a name="consider-using-service-bus-queues"></a>Överväg att använda Service Bus köer
Som lösnings arkitekt/-utvecklare **bör du överväga att använda Service Bus köer** när:

* Din lösning måste ta emot meddelanden utan att behöva söka i kön. Med Service Bus kan du uppnå det genom att använda en lång avsöknings funktion med hjälp av TCP-baserade protokoll som Service Bus stöder.
* Lösningen kräver att kön tillhandahåller en garanterad leverans med först in-First (FIFO).
* Din lösning måste ha stöd för automatisk dubblettidentifiering.
* Du vill att ditt program ska bearbeta meddelanden som parallella tids krävande strömmar (meddelanden är associerade med en data ström med hjälp av egenskapen [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) i meddelandet). I den här modellen konkurrerar varje nod i förbrukar programmet om strömmar, i stället för meddelanden. När en data ström tilldelas till en förbrukad nod kan noden granska status för programmets ström tillstånd med transaktioner.
* Din lösning kräver transaktions beteende och Atomicitet när du skickar eller tar emot flera meddelanden från en kö.
* Ditt program hanterar meddelanden som kan överstiga 64 KB, men det är inte troligt att gränsen på 256 KB fungerar.
* Du hanterar ett krav för att tillhandahålla en rollbaserad åtkomst modell till köerna och olika rättigheter/behörigheter för avsändare och mottagare. Mer information finns i följande artiklar:
    - [Autentisera med hanterade identiteter](service-bus-managed-service-identity.md)
    - [Autentisera från ett program](authenticate-application.md)
* Din kös Tor lek växer inte större än 80 GB.
* Du vill använda AMQP 1,0-standardbaserat meddelande protokoll. Mer information om AMQP finns i [Översikt över Service Bus AMQP](service-bus-amqp-overview.md).
* Du Envision en eventuell migrering från gruppbaserad punkt-till-punkt-kommunikation till ett meddelande mönster för publicerings prenumerationer. Det här mönstret möjliggör integrering av ytterligare mottagare (prenumeranter). Varje mottagare får oberoende kopior av antingen vissa eller alla meddelanden som skickas till kön. 
* Din meddelande lösning måste ha stöd för leverans garantin "på högst en gång" utan att du behöver bygga ytterligare infrastruktur komponenter.
* Din lösning måste publicera och förbruka batchar av meddelanden.

## <a name="compare-storage-queues-and-service-bus-queues"></a>Jämför lagrings köer och Service Bus köer
Tabellerna i följande avsnitt innehåller en logisk gruppering av kös funktioner. De gör det möjligt att snabbt jämföra de funktioner som finns tillgängliga i både Azure Storage köer och Service Bus köer.

## <a name="foundational-capabilities"></a>Grundläggande funktioner
I det här avsnittet jämförs några av de grundläggande köernas funktioner som tillhandahålls av lagrings köer och Service Bus köer.

| Jämförelse villkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Beställ garanti |**Nej** <br/><br>Mer information finns i den första kommentaren i avsnittet [Ytterligare information](#additional-information) .</br> | **Ja-första-först-ut (FIFO)**<br/><br>(genom att använda [Message-sessioner](message-sessions.md)) |
| Leverans garanti |**Minst en gång** |Minst **en gång** (med PeekLock Receive-läge. Det är standard alternativet) <br/><br/>**Mycket-en gång** (med ReceiveAndDelete Receive-läge) <br/> <br/> Läs mer om olika [mottagnings lägen](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Stöd för atomiska åtgärder |**Nej** |**Ja**<br/><br/> |
| Mottagnings beteende |**Icke-blockerande**<br/><br/>(Slutför omedelbart om det inte finns något nytt meddelande) |**Blockera med eller utan tids gräns**<br/><br/>(erbjuder lång avsökning eller ["Comet-teknik"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Icke-blockerande**<br/><br/>(genom att endast använda .NET Managed API) |
| API för push-format |**Nej** |**Ja**<br/><br/>.NET API för [QueueClient. motringningen OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) och [MessageSessionHandler. motringningen OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) -sessioner. |
| Mottagnings läge |**Granska & lån** |**Läsa & lås**<br/><br/>**Ta emot & ta bort** |
| Exklusivt åtkomst läge |**Lease-baserad** |**Lås-baserad** |
| Varaktighet för lån/lås |**30 sekunder (standard)**<br/><br/>**7 dagar (maximalt)** (du kan förnya eller frigöra ett meddelande lån med [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) -API: et.) |**60 sekunder (standard)**<br/><br/>Du kan förnya ett meddelande lås med [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) -API: et. |
| Precision för lån/låsning |**Meddelande nivå**<br/><br/>Varje meddelande kan ha ett annat timeout-värde, som du sedan kan uppdatera efter behov när du bearbetar meddelandet med hjälp av [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) -API: et. |**Köa nivå**<br/><br/>(varje kö har en låsnings precision som tillämpas på alla sina meddelanden, men du kan förnya låset med [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) -API: et.) |
| Grupp mottagning |**Ja**<br/><br/>(ange antalet meddelanden när meddelanden hämtas, upp till högst 32 meddelanden) |**Ja**<br/><br/>(som implicit aktiverar en för hämtnings egenskap eller uttryckligen genom användning av transaktioner) |
| Skicka med batch |**Nej** |**Ja**<br/><br/>(genom användning av transaktioner eller klient sidans batch) |

### <a name="additional-information"></a>Ytterligare information
* Meddelanden i lagrings köer är vanligt vis först in-First, men ibland kan de vara i ordning. Till exempel när Synlighets-timeout-tiden för ett meddelande upphör att gälla eftersom ett klient program kraschade under bearbetningen av ett meddelande. När tids gränsen för synlighet går ut blir meddelandet synligt igen i kön för att en annan arbetare ska kunna ta den ur kön. Vid det här tillfället kan det nyligen synliga meddelandet placeras i kön för att tas i kö igen.
* Det garanterade FIFO-mönstret i Service Bus köer kräver att meddelande tjänsten används. Om programmet kraschar när det bearbetar ett meddelande som mottagits i **gransknings & lås** läge, kommer den att starta med det misslyckade meddelandet efter att MEDDELANDEts TTL-period (Time to Live) upphör att gälla nästa gång.
* Lagrings köer har utformats för att stödja standard scenarier för köer, till exempel följande:
    - Koppla från program komponenter för att öka skalbarheten och toleransen för problem
    - Belastnings utjämning
    - Skapa process arbets flöden.
* Inkonsekvenser avseende meddelande hantering i samband med Service Bus-sessioner kan undvikas genom att använda sessionstillstånd för att lagra programmets tillstånd i förhållande till förloppet för att hantera sessionens meddelandekö, och genom att använda transaktioner som rör att lösa mottagna meddelanden och uppdatera sessionstillståndet. Den här typen av konsekvens funktioner märks ibland *exakt efter bearbetning* i andra leverantörs produkter. Eventuella transaktions fel kommer självklart att orsaka att meddelanden levereras om och att det är så att termen inte är tillräckligt exakt.
* Lagrings köer ger en enhetlig och konsekvent programmerings modell mellan köer, tabeller och BLOBBAR – både för utvecklare och för drift team.
* Service Bus köer ger stöd för lokala transaktioner i kontexten för en enskild kö.
* **Mottagnings-och borttagnings** läget som stöds av Service Bus ger möjlighet att minska antalet meddelande åtgärder (och tillhör ande kostnad) i Exchange för sänkt leverans garanti.
* Lagrings köer ger lån möjlighet att utöka lånet för meddelanden. Med den här funktionen kan arbets processer upprätthålla korta lån för meddelanden. Så om en arbets krasch kraschar kan meddelandet snabbt bearbetas igen av en annan arbets tagare. Dessutom kan en anställd förlänga lånet för ett meddelande om det behöver bearbeta det längre än den aktuella låne tiden.
* Lagrings köer ger en Synlighets-timeout som du kan ange som ett meddelandes eller ett meddelandes timeout-värde. Du kan också uppdatera ett meddelande med olika värden för lån vid körning och uppdatera olika värden mellan meddelanden i samma kö. Service Bus lås tids gränser definieras i köns metadata. Du kan dock förnya låset genom att anropa [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) -metoden.
* Den maximala tids gränsen för en blockera mottagnings åtgärd i Service Bus köer är 24 dagar. REST-baserade tids gränser har dock ett högsta värde på 55 sekunder.
* Klient sidans batch som tillhandahålls av Service Bus gör det möjligt för en Queue-klient att köra flera meddelanden i en enda sändnings åtgärd. Batching är endast tillgängligt för asynkrona sändnings åtgärder.
* Funktioner som 200-TB tak för lagrings köer (mer när du virtualiserar konton) och obegränsade köer gör det till en idealisk plattform för SaaS-leverantörer.
* Lagrings köer ger en flexibel och presterande mekanism för begränsad åtkomst kontroll.

## <a name="advanced-capabilities"></a>Avancerade funktioner
I det här avsnittet jämförs avancerade funktioner som tillhandahålls av lagrings köer och Service Bus köer.

| Jämförelse villkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Schemalagd leverans |**Ja** |**Ja** |
| Automatisk obeställbara meddelanden |**Nej** |**Ja** |
| Ökande TTL-värde (Time to Live) för kö |**Ja**<br/><br/>(via uppdatering på plats av tids gräns för synlighet) |**Ja**<br/><br/>(tillhandahålls via en särskild API-funktion) |
| Stöd för Poison-meddelanden |**Ja** |**Ja** |
| Uppdatering på plats |**Ja** |**Ja** |
| Transaktions logg på Server Sidan |**Ja** |**Nej** |
| Lagrings mått |**Ja**<br/><br/>**Minut mått** ger real tids mått för tillgänglighet, TPS, API-anrop, antal fel och mer. De är i real tid, sammanställda per minut och rapporteras inom några minuter från vad som precis hänt i produktionen. Mer information finns i [om Lagringsanalys mått](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Ja**<br/><br/>(Mass frågor genom att anropa [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Tillståndshantering |**Nej** |**Ja**<br/><br/>[Microsoft. Service Bus. Messaging. EntityStatus. Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. Service Bus. Messaging. EntityStatus. disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. Service Bus. Messaging. EntityStatus. SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. Service Bus. Messaging. EntityStatus. ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Vidarebefordra meddelande |**Nej** |**Ja** |
| Funktionen rensa kö |**Ja** |**Nej** |
| Meddelande grupper |**Nej** |**Ja**<br/><br/>(genom att använda messaging-sessioner) |
| Program tillstånd per meddelande grupp |**Nej** |**Ja** |
| Dubblettidentifiering |**Nej** |**Ja**<br/><br/>(kan konfigureras på avsändar sidan) |
| Bläddra i meddelande grupper |**Nej** |**Ja** |
| Hämtar Message-sessioner efter ID |**Nej** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Med båda köerna kan ett meddelande schemaläggas för leverans vid ett senare tillfälle.
* Köa vidarebefordran gör att tusentals köer kan vidarebefordra sina meddelanden till en enskild kö, där det mottagande programmet förbrukar meddelandet. Du kan använda den här metoden för att uppnå säkerhet, kontroll flöde och isolera lagring mellan varje meddelande utgivare.
* Lagrings köer ger stöd för uppdatering av meddelande innehåll. Du kan använda den här funktionen för att spara statusinformation och stegvisa förlopps uppdateringar i meddelandet så att den kan bearbetas från den senaste kända kontroll punkten, i stället för att börja från början. Med Service Bus köer kan du aktivera samma scenario genom att använda Message-sessioner. Med sessioner kan du spara och hämta tillstånd för program bearbetning (med hjälp av [setState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) och [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* Service Bus köer stöder inaktive [brevning](service-bus-dead-letter-queues.md). Det kan vara användbart för att isolera meddelanden som uppfyller följande kriterier:
    - Meddelanden kan inte bearbetas av det mottagande programmet 
    - Meddelanden kan inte komma åt sitt mål på grund av en förfallen TTL-egenskap (Time-to-Live). TTL-värdet anger hur länge ett meddelande stannar kvar i kön. Med Service Bus flyttas meddelandet till en särskild kö som kallas $DeadLetterQueue när TTL-perioden går ut.
* Om du vill hitta "Poison"-meddelanden i lagrings köer, när du deköa ett meddelande, undersöker programmet [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) -egenskapen för meddelandet. Om **DequeueCount** är större än ett angivet tröskelvärde flyttas meddelandet till en programdefinierad kö för obeställbara meddelanden.
* Med lagrings köer kan du hämta en detaljerad logg över alla transaktioner som har utförts mot kön och aggregerade mått. Båda alternativen är användbara för fel sökning och förståelse för hur programmet använder lagrings köer. De är också användbara för prestanda justering av ditt program och minska kostnaderna för att använda köer.
* Message-sessioner som stöds av Service Bus aktivera meddelanden som tillhör en logisk grupp som ska associeras med en mottagare. Den skapar en session som liknar tillhörighet mellan meddelanden och deras respektive mottagare. Du kan aktivera den här avancerade funktionen i Service Bus genom att ange egenskapen [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) för ett meddelande. Mottagare kan sedan lyssna på ett visst sessions-ID och ta emot meddelanden som delar den angivna sessions-ID: t.
* Funktionen för dubblettidentifiering i Service Bus köer tar automatiskt bort dubbla meddelanden som skickas till en kö eller ett ämne, baserat på värdet för egenskapen [messageid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) .

## <a name="capacity-and-quotas"></a>Kapacitet och kvoter
I det här avsnittet jämförs lagrings köer och Service Bus köer från den [kapacitet och de kvoter](service-bus-quotas.md) som kan tillkomma.

| Jämförelse villkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Maximal kös Tor lek |**500 TB**<br/><br/>(begränsat till en [enda lagrings konto kapacitet](../storage/common/storage-introduction.md#queue-storage)) |**1 GB till 80 GB**<br/><br/>(definieras när du skapar en kö och [aktiverar partitionering](service-bus-partitioning.md) – se avsnittet "Ytterligare information") |
| Maximal meddelande storlek |**64 kB**<br/><br/>(48 KB vid användning av **base64** -kodning)<br/><br/>Azure stöder stora meddelanden genom att kombinera köer och blobbar – där du kan placera upp till 200 GB för ett enda objekt. |**256 KB** eller **1 MB**<br/><br/>(inklusive både sidhuvud och brödtext, maximal sidhuvud storlek: 64 KB).<br/><br/>Är beroende av [tjänst nivån](service-bus-premium-messaging.md). |
| Maximalt meddelande-TTL |**Oändlig** (API-version 2017-07-27 eller senare) |**TimeSpan. Max** |
| Maximalt antal köer |**Obegränsat** |**10 000**<br/><br/>(namn område per tjänst) |
| Maximalt antal samtidiga klienter |**Obegränsat** |**5 000** |

### <a name="additional-information"></a>Ytterligare information
* Service Bus tillämpar begränsningar för kös Tor lek. Den maximala kös Tor lek anges när du skapar en kö. Det kan vara mellan 1 GB och 80 GB. Om köns storlek når den här gränsen kommer ytterligare inkommande meddelanden att avvisas och anroparen får ett undantag. Mer information om kvoter i Service Bus finns [Service Bus kvoter](service-bus-quotas.md).
* Partitionering stöds inte på [Premium-nivån](service-bus-premium-messaging.md). På standard-meddelande nivån kan du skapa Service Bus köer och ämnen i 1 (standard), 2, 3, 4 eller 5 GB storlek. När partitionering har Aktiver ATS skapar Service Bus 16 kopior (16 partitioner) för entiteten, var och en av de samma storlek som anges. Om du skapar en kö som är 5 GB stor, med 16 partitioner, blir den maximala kös Tor lek (5 * 16) = 80 GB.  Du kan se den maximala storleken på din partitionerade kö eller ämne i [Azure Portal][Azure portal].
* Om innehållet i meddelandet inte är XML-säkert i lagrings köer måste det vara **base64** -kodat. Om du **base64**-kodar meddelandet kan användarens nytto Last vara upp till 48 KB, i stället för 64 kB.
* Med Service Bus köer består varje meddelande som lagras i en kö av två delar: ett sidhuvud och en brödtext. Meddelandets totala storlek får inte överskrida den maximala meddelande storleken som stöds av tjänst nivån.
* När klienter kommunicerar med Service Bus köer över TCP-protokollet, är det maximala antalet samtidiga anslutningar till en enda Service Bus-kö begränsad till 100. Numret delas mellan avsändare och mottagare. Om den här kvoten uppnås avvisas begär Anden om ytterligare anslutningar och ett undantag tas emot av den anropande koden. Den här begränsningen gäller inte för klienter som ansluter till köerna med hjälp av REST-baserade API: er.
* Om du behöver fler än 10 000 köer i ett enda Service Bus-namnområde kan du kontakta support teamet för Azure och begära en ökning. Om du vill skala bortom 10 000-köer med Service Bus kan du också skapa ytterligare namn områden med hjälp av [Azure Portal][Azure portal].

## <a name="management-and-operations"></a>Hantering och åtgärder
I det här avsnittet jämförs de hanterings funktioner som tillhandahålls av lagrings köer och Service Bus köer.

| Jämförelse villkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Hanterings protokoll |**REST över HTTP/HTTPS** |**REST över HTTPS** |
| Körnings protokoll |**REST över HTTP/HTTPS** |**REST över HTTPS**<br/><br/>**AMQP 1,0 standard (TCP med TLS)** |
| .NET-API |**Ja**<br/><br/>(API för .NET-lagrings klient) |**Ja**<br/><br/>(.NET Service Bus-API) |
| Ursprunglig C++ |**Ja** |**Ja** |
| Java-API |**Ja** |**Ja** |
| PHP-API |**Ja** |**Ja** |
| Node.js-API |**Ja** |**Ja** |
| Stöd för godtycklig metadata |**Ja** |**Nej** |
| Namngivnings regler för kön |**Upp till 63 tecken**<br/><br/>(Bokstäver i ett könamn måste vara i gemener.) |**Upp till 260 tecken**<br/><br/>(Kö Sök vägar och namn är Skift läges okänsliga.) |
| Hämta Kölängd-funktion |**Ja**<br/><br/>(Ungefärligt värde om meddelanden upphör att gälla efter TTL utan att tas bort.) |**Ja**<br/><br/>(Exakt värde för tidpunkt.) |
| Funktionen Peek |**Ja** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Lagrings köer ger stöd för godtyckliga attribut som kan tillämpas på köns beskrivning, i form av namn/värde-par.
* Båda köerna ger möjlighet att granska ett meddelande utan att behöva låsa det, vilket kan vara användbart när du implementerar ett Queue Explorer/webb läsar verktyg.
* Service Bus API: er för .NET-asynkrona meddelanden använder TCP-anslutningar med full duplex för bättre prestanda jämfört med REST över HTTP och de stöder AMQP 1,0 standard-protokollet.
* Namn på lagrings köer kan vara 3-63 tecken långa, får innehålla gemener, siffror och bindestreck. Mer information finns i [namnge köer och metadata](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Service Bus könamnet får innehålla upp till 260 tecken och ha mindre restriktiva namngivnings regler. Service Bus könamnet får innehålla bokstäver, siffror, punkter, bindestreck och under streck.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
I det här avsnittet beskrivs de funktioner för autentisering och auktorisering som stöds av lagrings köer och Service Bus köer.

| Jämförelse villkor | Lagringsköer | Service Bus-köer |
| --- | --- | --- |
| Autentisering |**Symmetrisk nyckel** |**Symmetrisk nyckel** |
| Säkerhetsmodell |Delegerad åtkomst via SAS-token. |SAS |
| Federation för identitets leverantör |**Nej** |**Ja** |

### <a name="additional-information"></a>Ytterligare information
* Varje begäran till någon av Queuing-teknikerna måste autentiseras. Offentliga köer med anonym åtkomst stöds inte. Med [SAS](service-bus-sas.md)kan du hantera det här scenariot genom att publicera en skrivskyddad SAS, skrivskyddad SAS eller till och med en full åtkomst SAS.
* Autentiseringsschemat som tillhandahålls av lagrings köer innebär att en symmetrisk nyckel används. Den här nyckeln är en hash-baserad Message Authentication Code (HMAC), beräknad med SHA-256-algoritmen och kodas som en **base64** -sträng. Mer information om respektive protokoll finns i [autentisering för Azure Storage-tjänsterna](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Service Bus köer har stöd för en liknande modell som använder symmetriska nycklar. Mer information finns i [autentisering med signatur för delad åtkomst med Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Slutsats
Genom att få en djupare förståelse för de två teknikerna kan du fatta ett mer informerat beslut om vilken kös teknik som ska användas och när. Beslutet om när du ska använda lagrings köer eller Service Bus köer är tydligt beroende av ett antal faktorer. Dessa faktorer kan vara beroende av programmets individuella behov och dess arkitektur. 

Du kanske föredrar att välja lagrings köer av följande orsaker:

- Om ditt program redan använder kärn funktionerna i Microsoft Azure 
- Om du behöver grundläggande kommunikation och meddelanden mellan tjänster 
- Behöver köer som kan vara större än 80 GB i storlek

Service Bus köer innehåller flera avancerade funktioner, till exempel följande. Det kan vara ett önskat val om du skapar ett hybrid program eller om ditt program annars kräver dessa funktioner.

- [Sessioner](message-sessions.md)
- [Transaktioner](service-bus-transactions.md)
- [Dubblettidentifiering](duplicate-detection.md)
- [Automatisk obeställbara meddelanden](service-bus-dead-letter-queues.md)
- [Funktioner för varaktig publicering och prenumeration](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 



## <a name="next-steps"></a>Nästa steg
Följande artiklar innehåller mer information om hur du använder lagrings köer eller Service Bus köer.

* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använda tjänsten Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Metod tips för prestanda förbättringar med Service Bus asynkrona meddelanden](service-bus-performance-improvements.md)
* [Introduktion till köer och ämnen i Azure Service Bus (blogg inlägg)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Utvecklarens guide till Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Använda tjänsten Queueing i Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

