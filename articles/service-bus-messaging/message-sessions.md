---
title: Azure Service Bus Message-sessioner | Microsoft Docs
description: Den här artikeln förklarar hur du använder sessioner för att aktivera gemensam och ordnad hantering av icke-bundna sekvenser av relaterade meddelanden.
ms.topic: article
ms.date: 04/12/2021
ms.openlocfilehash: c9a1c4fdccbbc8b38805e23d4895448959126f10
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308491"
---
# <a name="message-sessions"></a>Meddelandesessioner
Microsoft Azure Service Bus-sessioner möjliggör gemensam och ordnad hantering av icke-bundna sekvenser av relaterade meddelanden. Sessioner kan användas i de mönster som **först in, först ut (FIFO)** och **begär ande svar** . Den här artikeln visar hur du använder sessioner för att implementera dessa mönster när du använder Service Bus. 

> [!NOTE]
> Basic-nivån för Service Bus stöder inte sessioner. Standard-och Premium-nivåerna stöder sessioner. För skillnader mellan dessa nivåer, se [Service Bus prissättning](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>Mönster för första in, först ut (FIFO)
Om du vill inse en FIFO-garanti i Service Bus använder du sessioner. Service Bus är inte förenligt Relations typen för relationen mellan meddelandena och definierar inte heller en viss modell för att avgöra var en meddelandekö startar eller slutar.

En avsändare kan skapa en session när de skickar meddelanden till ett ämne eller en kö genom att ange **sessions-ID** -egenskapen till en viss programdefinierad identifierare som är unik för sessionen. På AMQP 1,0-protokoll nivån mappas detta värde till egenskapen *Group-ID* .

I sessionsbaserade köer eller prenumerationer kommer det att finnas sessioner när det finns minst ett meddelande med sessions-ID: t. När en session finns har ingen definierad tid eller API för när sessionen upphör att gälla eller försvinner. Teoretiskt sett kan ett meddelande tas emot för en session idag, nästa meddelande under årets tid, och om sessions-ID: t matchar är sessionen detsamma från Service Bus perspektivet.

Ett program har vanligt vis ett tydligt begrepp där en uppsättning relaterade meddelanden startar och slutar. Service Bus anger inte några speciella regler. Ditt program kan till exempel ange egenskapen **etikett** för det första meddelandet som ska **startas**, för mellanliggande meddelanden till **innehåll** och för det sista meddelandet som ska **avslutas**. Innehålls meddelandets relativa position kan beräknas som det aktuella meddelandet *SequenceNumber* delta från **Start** meddelandet *SequenceNumber*.

Du aktiverar funktionen genom att ställa in egenskapen [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) i kön eller prenumerationen via Azure Resource Manager eller genom att ställa in flaggan i portalen. Det krävs innan du försöker använda relaterade API-åtgärder.

I portalen kan du aktivera sessioner när du skapar en entitet (kö eller prenumeration) som du ser i följande exempel. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Aktivera sessionen när kön skapas":::

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Aktivera sessionen när prenumerationen skapas":::


> [!IMPORTANT]
> När sessioner är aktiverade i en kö eller en prenumeration kan klient programmen ***inte längre*** skicka/ta emot vanliga meddelanden. Alla meddelanden måste skickas som en del av en session (genom att ställa in sessions-ID) och tas emot genom att godkänna sessionen.

API: erna för sessioner finns i kö-och prenumerations klienter. Det finns en tvingande modell som styr när sessioner och meddelanden tas emot och en hanterare modell som döljer komplexiteten med att hantera Receive-loopen. 

För exempel kan du använda länkar i avsnittet [Nästa steg](#next-steps) . 

### <a name="session-features"></a>Session funktioner

Sessioner ger samtidiga indata strömmar av överlagrade meddelande strömmar samtidigt som de bevarar och garanterar beställda leveranser.

![Ett diagram som visar hur sessions-funktionen bevarar den beställda leveransen.][1]

En sessions-mottagare skapas av en klient som accepterar en session. När sessionen godkänns och hålls av en klient, innehåller klienten ett exklusivt lås på alla meddelanden med sessions **-ID: t** för sessionen i kön eller prenumerationen. Det kommer också att innehålla exklusiva lås på alla meddelanden med det **sessions-ID** som kommer att komma senare.

Låset släpps när du anropar de nära relaterade metoderna på mottagaren eller när låset upphör att gälla. Det finns metoder på mottagaren som även förnyar låsen. I stället kan du använda funktionen för automatisk lås förnyelse där du kan ange hur länge du vill att låset ska förnyas. Sessions låset bör behandlas som ett exklusivt lås på en fil, vilket innebär att programmet ska stänga sessionen så fort den inte längre behöver den och/eller inte förväntar sig några ytterligare meddelanden.

När flera samtidiga mottagare hämtar sig från kön skickas meddelanden som hör till en viss session till den specifika mottagare som för närvarande har låset för sessionen. Med den åtgärden kan en överlagrad meddelande ström i en kö eller prenumeration rensas helt och hållet i olika mottagare och dessa mottagare kan även aktive ras på olika klient datorer, eftersom lås hanteringen sker i Service Bus.

Föregående bild visar tre samtidiga sessioners mottagare. En session med `SessionId` = 4 har ingen aktiv, ägande klient, vilket innebär att inga meddelanden levereras från den här sessionen. En session fungerar på många sätt som en underkö.

Det fillås som innehas av sessionens mottagare är ett paraply för de meddelande lås som används i kvittnings läget för *Peek-lock* . Endast en mottagare kan låsa en session. En mottagare kan ha många meddelanden i flygningen, men meddelandena tas emot i rätt ordning. Om du överger ett meddelande skickas samma meddelande igen med nästa mottagnings åtgärd.

### <a name="message-session-state"></a>Sessionstillstånd för meddelande

När arbets flöden bearbetas i storskaliga moln system med hög tillgänglighet måste den arbets flödes hanterare som är associerad med en viss session kunna återställas från oväntade fel och kan återuppta delvis slutfört arbete i en annan process eller dator från vilken arbetet startade.

Funktionen session State aktiverar en programdefinierad anteckning för en Message-session i Broker, så att det registrerade bearbetnings läget i förhållande till den sessionen blir omedelbart tillgängligt när sessionen anskaffas av en ny processor.

Från Service Bus perspektiv är sessionens sessionstillstånd ett ogenomskinligt binärt objekt som kan lagra data i ett meddelande, vilket är 256 KB för Service Bus standard och 1 MB för Service Bus Premium. Bearbetnings läget i förhållande till en session kan hållas inne i sessionstillståndet, eller så kan sessionstillståndet peka på en lagrings plats eller databas post som innehåller sådan information.

Metoderna för att hantera sessionstillstånd, SetState och GetState, finns på objektet Session receiver. En session som tidigare inte har något sessionstillstånd returnerar en null-referens för GetState. Det tidigare angivna sessionstillståndet kan rensas genom att skicka null till SetState-metoden på mottagaren.

Sessionstillståndet förblir så länge det inte är rensat (returnerar **Null**), även om alla meddelanden i en session används.

Sessionstillståndet lagras i en kö eller i en prenumeration mot entitetens lagrings kvot. När programmet är klart med en session rekommenderar vi därför att programmet rensar sitt sparade tillstånd för att undvika extern hanterings kostnad.

### <a name="impact-of-delivery-count"></a>Påverkan av leverans antal

Definitionen av leverans antal per meddelande i samband med sessioner skiljer sig något från definitionen om det inte finns några sessioner. Här är en tabell som sammanfattas när leverans antalet ökar.

| Scenario | Är meddelandets leverans antal ökas |
|----------|---------------------------------------------|
| Sessionen godkänns, men sessions låset upphör att gälla (på grund av tids gräns) | Yes |
| Sessionen godkänns, meddelandena i sessionen slutförs inte (även om de är låsta) och sessionen stängs | No |
| Sessionen godkänns, meddelanden slutförs och sedan stängs sessionen explicit | Ej tillämpligt (det är standard flödet. Meddelanden tas bort från sessionen) |

## <a name="request-response-pattern"></a>Mönster för begäran-svar
[Fråge svars mönstret](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) är ett väletablerat integrerings mönster som gör det möjligt för avsändar programmet att skicka en begäran och ger mottagaren rätt att skicka ett svar tillbaka till avsändar programmet. Det här mönstret kräver vanligt vis en kort period i kö eller ett ämne som programmet ska skicka svar till. I det här scenariot tillhandahåller sessioner en enkel alternativ lösning med jämförbara semantik. 

Flera program kan skicka sina förfrågningar till en enskild begär ande kö, med en specifik huvud parameter inställd på att unikt identifiera avsändar programmet. Mottagar programmet kan bearbeta de begär Anden som kommer i kön och skicka svar på den session som är aktive rad, ställa in sessions-ID: t till den unika identifierare som avsändaren skickade till begär ande meddelandet. Det program som skickade begäran kan sedan ta emot meddelanden i det specifika sessions-ID: t och bearbeta svaren på rätt sätt.

> [!NOTE]
> Programmet som skickar de ursprungliga förfrågningarna bör veta om sessions-ID: t och använda det för att godkänna sessionen så att den session där svaret förväntas är låst. Det är en bra idé att använda en GUID som unikt identifierar instansen av programmet som ett sessions-ID. Det ska inte finnas någon sessions hanterare eller en timeout som angetts i sessionens mottagare för att säkerställa att svaren är tillgängliga för att låsas och bearbetas av specifika mottagare.

## <a name="next-steps"></a>Nästa steg

- [Azure. Messaging. Service Bus-exempel för .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [Azure Service Bus klient bibliotek för Java-exempel](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus klient bibliotek för python-exempel](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus klient bibliotek för JavaScript-exempel](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus klient bibliotek för TypeScript-exempel](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Microsoft. Azure. Service Bus-exempel för .net](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) (sessioner och sessionState-exempel)  

Mer information om Service Bus meddelanden finns i [Service Bus köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md).

[1]: ./media/message-sessions/sessions.png
