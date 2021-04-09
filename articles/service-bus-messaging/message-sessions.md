---
title: Azure Service Bus Message-sessioner | Microsoft Docs
description: Den här artikeln förklarar hur du använder sessioner för att aktivera gemensam och ordnad hantering av icke-bundna sekvenser av relaterade meddelanden.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 6d316571d69d2e1e73ddca4ccca53c116ee8fa5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680761"
---
# <a name="message-sessions"></a>Meddelandesessioner
Microsoft Azure Service Bus-sessioner möjliggör gemensam och ordnad hantering av icke-bundna sekvenser av relaterade meddelanden. Sessioner kan användas i de mönster som **först in, först ut (FIFO)** och **begär ande svar** . Den här artikeln visar hur du använder sessioner för att implementera dessa mönster när du använder Service Bus. 

> [!NOTE]
> Basic-nivån för Service Bus stöder inte sessioner. Standard-och Premium-nivåerna stöder sessioner. För skillnader mellan dessa nivåer, se [Service Bus prissättning](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>Mönster för första in, först ut (FIFO)
Om du vill inse en FIFO-garanti i Service Bus använder du sessioner. Service Bus är inte förenligt Relations typen för relationen mellan meddelandena och definierar inte heller en viss modell för att avgöra var en meddelandekö startar eller slutar.

En avsändare kan skapa en session när de skickar meddelanden till ett ämne eller en kö genom att ange egenskapen [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) till en viss programdefinierad identifierare som är unik för sessionen. På AMQP 1,0-protokoll nivån mappas detta värde till egenskapen *Group-ID* .

I sessionsbaserade köer eller prenumerationer kommer det att finnas sessioner när det finns minst ett meddelande med sessionens [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). När en session finns har ingen definierad tid eller API för när sessionen upphör att gälla eller försvinner. Teoretiskt sett kan ett meddelande tas emot för en session idag, nästa meddelande under årets tid, och om **SessionID** matchar är sessionen detsamma från Service Bus perspektivet.

Ett program har vanligt vis ett tydligt begrepp där en uppsättning relaterade meddelanden startar och slutar. Service Bus anger inte några speciella regler.

Ett exempel på hur du kan avgränsa en sekvens för att överföra en fil är att ange egenskapen **etikett** för det första meddelandet som ska **startas**, för mellanliggande meddelanden till **innehåll** och för det sista meddelandet som ska **avslutas**. Innehålls meddelandets relativa position kan beräknas som det aktuella meddelandet *SequenceNumber* delta från **Start** meddelandet *SequenceNumber*.

Funktionen session i Service Bus aktiverar en enskild Receive-åtgärd i form av [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) i C#-och Java-API: er. Du aktiverar funktionen genom att ställa in egenskapen [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) i kön eller prenumerationen via Azure Resource Manager eller genom att ställa in flaggan i portalen. Det krävs innan du försöker använda relaterade API-åtgärder.

I portalen anger du flaggan med följande kryss ruta:

![Skärm bild av dialog rutan skapa kö med alternativet Aktivera sessioner markerat och anges i rött.][2]

> [!NOTE]
> När sessioner är aktiverade i en kö eller en prenumeration kan klient programmen ***inte längre*** skicka/ta emot vanliga meddelanden. Alla meddelanden måste skickas som en del av en session (genom att ställa in sessions-ID) och tas emot genom att ta emot sessionen.

API: erna för sessioner finns i kö-och prenumerations klienter. Det finns en tvingande modell som styr när sessioner och meddelanden tas emot, och en hanterare-baserad modell, som liknar *motringningen OnMessage*, som döljer komplexiteten med att hantera Receive-slingan.

### <a name="session-features"></a>Session funktioner

Sessioner ger samtidiga indata strömmar av överlagrade meddelande strömmar samtidigt som de bevarar och garanterar beställda leveranser.

![Ett diagram som visar hur sessions-funktionen bevarar den beställda leveransen.][1]

En [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -mottagare skapas av klienten som accepterar en session. Klienten anropar [QueueClient. AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) eller [QueueClient. AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) i C#. I den återaktiverade återanrops modellen registreras en sessions hanterare.

När [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -objektet godkänns och när det innehas av en klient, har klienten ett exklusivt lås på alla meddelanden med sessionens [SessionID](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) som finns i kön eller prenumerationen, och även på alla meddelanden med det **SessionID** som fortfarande anländer när sessionen hålls.

Låset släpps när **Close** eller **CloseAsync** anropas, eller när låset upphör att gälla i fall där programmet inte kan stängas. Sessions låset bör behandlas som ett exklusivt lås på en fil, vilket innebär att programmet ska stänga sessionen så fort den inte längre behöver den och/eller inte förväntar sig några ytterligare meddelanden.

När flera samtidiga mottagare hämtar sig från kön skickas meddelanden som hör till en viss session till den specifika mottagare som för närvarande har låset för sessionen. Med den åtgärden kan en överlagrad meddelande ström i en kö eller prenumeration rensas helt och hållet i olika mottagare och dessa mottagare kan även aktive ras på olika klient datorer, eftersom lås hanteringen sker i Service Bus.

Föregående bild visar tre samtidiga sessioners mottagare. En session med `SessionId` = 4 har ingen aktiv, ägande klient, vilket innebär att inga meddelanden levereras från den här sessionen. En session fungerar på många sätt som en underkö.

Det fillås som innehas av sessionens mottagare är ett paraply för de meddelande lås som används i kvittnings läget för *Peek-lock* . Endast en mottagare kan låsa en session. En mottagare kan ha många meddelanden i flygningen, men meddelandena tas emot i rätt ordning. Om du överger ett meddelande skickas samma meddelande igen med nästa mottagnings åtgärd.

### <a name="message-session-state"></a>Sessionstillstånd för meddelande

När arbets flöden bearbetas i storskaliga moln system med hög tillgänglighet måste den arbets flödes hanterare som är associerad med en viss session kunna återställas från oväntade fel och kan återuppta delvis slutfört arbete i en annan process eller dator från vilken arbetet startade.

Funktionen session State aktiverar en programdefinierad anteckning för en Message-session i Broker, så att det registrerade bearbetnings läget i förhållande till den sessionen blir omedelbart tillgängligt när sessionen anskaffas av en ny processor.

Från Service Bus perspektiv är sessionens sessionstillstånd ett ogenomskinligt binärt objekt som kan lagra data i ett meddelande, vilket är 256 KB för Service Bus standard och 1 MB för Service Bus Premium. Bearbetnings läget i förhållande till en session kan hållas inne i sessionstillståndet, eller så kan sessionstillståndet peka på en lagrings plats eller databas post som innehåller sådan information.

API: erna för hantering av sessionstillstånd, [setState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) och [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)finns på [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -objektet i både C#-och Java-API: erna. En session som tidigare inte har någon sessionstillstånd för sessionstillstånd returnerar en **Null** -referens för **GetState**. Det gick inte att rensa det tidigare angivna sessionstillstånd i [setState (null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Sessionstillståndet förblir så länge det inte är rensat (returnerar **Null**), även om alla meddelanden i en session används.

Alla befintliga sessioner i en kö eller prenumeration kan räknas upp med metoden **SessionBrowser** i Java-API: et och med [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) på [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) och [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) i .NET Framework-klienten.

Sessionstillståndet lagras i en kö eller i en prenumeration mot entitetens lagrings kvot. När programmet är klart med en session rekommenderar vi därför att programmet rensar sitt sparade tillstånd för att undvika extern hanterings kostnad.

### <a name="impact-of-delivery-count"></a>Påverkan av leverans antal

Definitionen av leverans antal per meddelande i samband med sessioner skiljer sig något från definitionen om det inte finns några sessioner. Här är en tabell som sammanfattas när leverans antalet ökar.

| Scenario | Är meddelandets leverans antal ökas |
|----------|---------------------------------------------|
| Sessionen godkänns, men sessions låset upphör att gälla (på grund av tids gräns) | Ja |
| Sessionen godkänns, meddelandena i sessionen slutförs inte (även om de är låsta) och sessionen stängs | Inga |
| Sessionen godkänns, meddelanden slutförs och sedan stängs sessionen explicit | Ej tillämpligt (det är standard flödet. Meddelanden tas bort från sessionen) |

## <a name="request-response-pattern"></a>Mönster för begäran-svar
[Fråge svars mönstret](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) är ett väletablerat integrerings mönster som gör det möjligt för avsändar programmet att skicka en begäran och ger mottagaren rätt att skicka ett svar tillbaka till avsändar programmet. Det här mönstret kräver vanligt vis en kort period i kö eller ett ämne som programmet ska skicka svar till. I det här scenariot tillhandahåller sessioner en enkel alternativ lösning med jämförbara semantik. 

Flera program kan skicka sina förfrågningar till en enskild begär ande kö, med en specifik huvud parameter inställd på att unikt identifiera avsändar programmet. Mottagar programmet kan bearbeta de begär Anden som kommer i kön och skicka svar på den session som är aktive rad, ställa in sessions-ID: t till den unika identifierare som avsändaren skickade till begär ande meddelandet. Det program som skickade begäran kan sedan ta emot meddelanden i det specifika sessions-ID: t och bearbeta svaren på rätt sätt.

> [!NOTE]
> Programmet som skickar de ursprungliga förfrågningarna bör veta om sessions-ID: t och använda `SessionClient.AcceptMessageSession(SessionID)` för att låsa den session där svaret förväntas. Det är en bra idé att använda en GUID som unikt identifierar instansen av programmet som ett sessions-ID. Det ska inte finnas någon sessionsbiljett eller `AcceptMessageSession(timeout)` i kön för att se till att svaren är tillgängliga för att låsas och bearbetas av vissa mottagare.

## <a name="next-steps"></a>Nästa steg

- Se antingen [Microsoft. Azure. Service Bus-exemplen](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) eller [Microsoft. Service Bus. Messaging-exemplen](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) för ett exempel som använder .NET Framework-klienten för att hantera sessionsbaserade meddelanden. 

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
