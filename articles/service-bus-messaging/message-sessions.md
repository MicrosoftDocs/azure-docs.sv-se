---
title: Azure Service Bus-meddelandesessioner | Microsoft Docs
description: Den här artikeln förklarar hur du använder sessioner för att aktivera gemensam och ordnad hantering av obundna sekvenser av relaterade meddelanden.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: e22dfb2aa7372a227f70fd2bfa8f72d2161cda17
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750760"
---
# <a name="message-sessions"></a>Meddelandesessioner
Microsoft Azure Service Bus-sessioner möjliggör gemensam och ordnad hantering av obundna sekvenser av relaterade meddelanden. Sessioner kan användas i **först in, först ut (FIFO) och** **begäran-svar-mönster.** Den här artikeln visar hur du använder sessioner för att implementera dessa mönster när du använder Service Bus. 

> [!NOTE]
> Basic-nivån för Service Bus stöder inte sessioner. Standard- och Premium-nivåerna stöder sessioner. Skillnader mellan dessa nivåer finns i [Service Bus priser.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="first-in-first-out-fifo-pattern"></a>FiFO-mönster (först in, först ut)
Om du vill realisera en FIFO-garanti Service Bus kan du använda sessioner. Service Bus är inte normativt om typen av relation mellan meddelandena och definierar inte heller en viss modell för att avgöra var en meddelandesekvens startar eller slutar.

Alla avsändare kan skapa en session när de skickar meddelanden till ett ämne eller en kö genom att ange egenskapen **för sessions-ID** till en programdefinierad identifierare som är unik för sessionen. På AMQP 1.0-protokollnivån mappar det här värdet till *egenskapen group-id.*

I sessionsmedvetna köer eller prenumerationer finns sessioner när det finns minst ett meddelande med sessions-ID:t. När det finns en session finns det ingen definierad tid eller något API för när sessionen upphör att gälla eller försvinner. Teoretiskt sett kan ett meddelande tas emot för en session i dag, nästa meddelande om ett år, och om sessions-ID:t matchar är sessionen densamma ur ett Service Bus perspektiv.

Normalt har dock ett program en tydlig uppfattning om var en uppsättning relaterade meddelanden startar och slutar. Service Bus anger inga specifika regler. Programmet kan till exempel ange egenskapen **Etikett** för det första meddelandet till att starta **,** för mellanliggande meddelanden till innehåll **och** för att det sista meddelandet ska **avsluta**. Innehållsmeddelandenas relativa position kan beräknas som det aktuella meddelandet *SequenceNumber* delta från **startmeddelandet** *SequenceNumber*.

> [!IMPORTANT]
> När Sessioner är aktiverade i en kö eller en prenumeration kan klientprogrammen ***inte längre*** skicka/ta emot vanliga meddelanden. Alla meddelanden måste skickas som en del av en session (genom att ange sessions-ID) och tas emot genom att sessionen accepteras.

API:erna för sessioner finns i kö- och prenumerationsklienter. Det finns en imperativ modell som styr när sessioner och meddelanden tas emot och en hanterarbaserad modell som döljer komplexiteten med att hantera mottagningsloopen. 

För exempel använder du länkar i [avsnittet Nästa](#next-steps) steg. 

### <a name="session-features"></a>Sessionsfunktioner

Sessioner ger samtidig de-multiplexering av överbevarande meddelandeströmmar samtidigt som den beställda leveransen bevaras och garanterars.

![Ett diagram som visar hur funktionen Sessioner bevarar ordnad leverans.][1]

En sessionsmottagare skapas av en klient som accepterar en session. När sessionen accepteras och hålls av en klient, har klienten ett exklusivt lås för alla meddelanden med sessionens **sessions-ID** i kön eller prenumerationen. Den kommer också att innehålla exklusiva lås för alla meddelanden med **sessions-ID:t** som kommer senare.

Låset släpps när du anropar stängningsrelaterade metoder på mottagaren eller när låset upphör att gälla. Det finns metoder på mottagaren för att förnya låsen också. I stället kan du använda funktionen för automatisk låsförnyelse där du kan ange hur länge du vill fortsätta att förnya låset. Sessionslåset bör behandlas som ett exklusivt lås på en fil, vilket innebär att programmet bör stänga sessionen så snart den inte längre behöver den och/eller inte förväntar sig några ytterligare meddelanden.

När flera samtidiga mottagare hämtar från kön skickas meddelanden som hör till en viss session till den specifika mottagare som för närvarande har låset för den sessionen. Med den åtgärden avkodas en sammanflätad meddelandeström i en kö eller prenumeration korrekt av multiplexerat till olika mottagare och dessa mottagare kan också vara live på olika klientdatorer, eftersom låshanteringen sker på tjänstsidan, inuti Service Bus.

Föregående bild visar tre samtidiga sessionsmottagare. En session med `SessionId` = 4 har ingen aktiv, äger klient, vilket innebär att inga meddelanden levereras från den här specifika sessionen. En session fungerar på många sätt som en underkö.

Sessionslåset som sessionsmottagaren har är ett samlingsparaply för de meddelandelås som används av *peek-lock-likvidläget.* Endast en mottagare kan ha ett lås på en session. En mottagare kan ha många flygmeddelanden, men meddelandena tas emot i ordning. Om du lämnar ett meddelande ser du samma meddelande igen med nästa mottagningsåtgärd.

### <a name="message-session-state"></a>Meddelandesessionstillstånd

När arbetsflöden bearbetas i storskaliga molnsystem med hög tillgänglighet måste arbetsflödeshanteraren som är associerad med en viss session kunna återställas från oväntade fel och kan återuppta delvis slutfört arbete på en annan process eller dator där arbetet började.

Sessionstillståndsfunktion möjliggör en programdefinierad anteckning av en meddelandesession i koordinatorn, så att det registrerade bearbetningstillståndet i förhållande till den sessionen blir omedelbart tillgängligt när sessionen förvärvas av en ny processor.

Ur Service Bus perspektivet är meddelandesessionstillståndet ett täckande binärt objekt som kan innehålla data med storleken ett meddelande, vilket är 256 kB för Service Bus Standard och 1 MB för Service Bus Premium. Bearbetningstillståndet i förhållande till en session kan hållas i sessionstillståndet, eller så kan sessionstillståndet peka på en lagringsplats eller databaspost som innehåller sådan information.

Metoderna för att hantera sessionstillståndet SetState och GetState finns i sessionsmottagareobjektet. En session som tidigare inte hade något sessionstillstånd returnerar en null-referens för GetState. Det tidigare inställda sessionstillståndet kan rensas genom att skicka null till metoden SetState på mottagaren.

Sessionstillståndet förblir så länge det inte rensas (null **returneras),** även om alla meddelanden i en session förbrukas.

Sessionstillståndet som lagras i en kö eller i en prenumeration räknas mot entitetens lagringskvot. När programmet är klart med en session rekommenderar vi därför att programmet rensar sitt kvarhållna tillstånd för att undvika externa hanteringskostnader.

### <a name="impact-of-delivery-count"></a>Påverkan av leveransantal

Definitionen av antal leveranser per meddelande i sessionssammanhang varierar något från definitionen i avsaknad av sessioner. Här är en tabell som sammanfattar när antalet leveranser ökar.

| Scenario | Ökas meddelandets leveransantal? |
|----------|---------------------------------------------|
| Sessionen godkänns, men sessionslåset upphör att gälla (på grund av tidsgräns) | Yes |
| Sessionen godkänns, meddelandena i sessionen slutförs inte (även om de är låsta) och sessionen stängs | No |
| Sessionen godkänns, meddelanden slutförs och sedan stängs sessionen uttryckligen | Ej a(det är standardflödet. Här tas meddelanden bort från sessionen) |

## <a name="request-response-pattern"></a>Mönster för begäran-svar
Mönstret [för begäran/svar](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) är ett väletablerat integreringsmönster som gör att avsändarprogrammet kan skicka en begäran och ger ett sätt för mottagaren att skicka ett svar tillbaka till avsändarprogrammet på rätt sätt. Det här mönstret behöver vanligtvis en kortlivad kö eller ett ämne som programmet kan skicka svar till. I det här scenariot tillhandahåller sessioner en enkel alternativ lösning med jämförbar semantik. 

Flera program kan skicka sina begäranden till en enda begärandekö, med en specifik rubrikparameter inställd på att unikt identifiera avsändarprogrammet. Mottagarprogrammet kan bearbeta begäranden som kommer i kön och skicka svar i den sessionsaktiverade kön, och ange sessions-ID:t till den unika identifierare som avsändaren skickade i begärandemeddelandet. Programmet som skickade begäran kan sedan ta emot meddelanden om det specifika sessions-ID:t och bearbeta svaren korrekt.

> [!NOTE]
> Programmet som skickar de första begärandena bör känna till sessions-ID:t och använda det för att acceptera sessionen så att den session där den förväntar sig att svaret är låst. Det är en bra idé att använda ett GUID som unikt identifierar instansen av programmet som ett sessions-ID. Det bör inte finnas någon sessionshanterare eller en tidsgräns angiven för sessionsmottagaren för kön för att säkerställa att svar är tillgängliga för att låsas och bearbetas av specifika mottagare.

## <a name="next-steps"></a>Nästa steg
Du kan aktivera meddelandesessioner när du skapar en kö med Azure Portal, PowerShell, CLI, Resource Manager mall, .NET, Java, Python och JavaScript. Mer information finns i Aktivera [meddelandesessioner.](enable-message-sessions.md) 

Prova exemplen på det språk du väljer för att utforska Azure Service Bus funktioner. 

- [Azure Service Bus klientbiblioteksexempel för Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus klientbiblioteksexempel för Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus klientbiblioteksexempel för JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus klientbiblioteksexempel för TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Azure.Messaging.ServiceBus-exempel för .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Hitta exempel för äldre .NET- och Java-klientbibliotek nedan:
- [Microsoft.Azure.ServiceBus-exempel för .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [azure-servicebus-exempel för Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

[1]: ./media/message-sessions/sessions.png

