---
title: Service Bus asynkrona meddelanden | Microsoft Docs
description: Lär dig hur Azure Service Bus stöder asynchronism via en metod för lagring och vidarebefordran med köer, ämnen och prenumerationer.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e37c18b95bca7ef1e6e8f0d74976bb73b214624a
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500639"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynkrona meddelande mönster och hög tillgänglighet

Asynkrona meddelanden kan implementeras på flera olika sätt. Med köer, ämnen och prenumerationer stöder Azure Service Bus asynchronism via en mekanism för lagring och vidarebefordran. I normal (synkron) åtgärd, skickar du meddelanden till köer och ämnen och tar emot meddelanden från köer och prenumerationer. Program som du skriver är beroende av att dessa entiteter alltid är tillgängliga. När enhetens hälso tillstånd ändras, på grund av en rad olika omständigheter, behöver du ett sätt att tillhandahålla en enhet med begränsad kapacitet som kan uppfylla de flesta behov.

Program använder vanligt vis asynkrona meddelande mönster för att möjliggöra ett antal kommunikations scenarier. Du kan bygga program där klienter kan skicka meddelanden till tjänster, även när tjänsten inte körs. För program som upplever burst-kommunikation kan en kö hjälpa till att utjämna belastningen genom att tillhandahålla en plats för att buffra kommunikation. Slutligen kan du få en enkel, men effektiv belastningsutjämnare för att distribuera meddelanden över flera datorer.

För att upprätthålla tillgänglighet för någon av dessa entiteter bör du fundera på ett antal olika sätt som dessa entiteter kan visas i för ett hållbart meddelande system. I allmänhet ser vi att entiteten blir otillgänglig för program som vi skriver på följande sätt:

* Det gick inte att skicka meddelanden.
* Det gick inte att ta emot meddelanden.
* Det går inte att hantera entiteter (skapa, Hämta, uppdatera eller ta bort entiteter).
* Det gick inte att kontakta tjänsten.

För var och en av dessa avbrott finns det olika fellägen som gör att ett program kan fortsätta att utföra arbete på viss nivå av begränsad kapacitet. Till exempel kan ett system som kan skicka meddelanden men inte ta emot dem fortfarande ta emot beställningar från kunder, men inte bearbeta dessa order. I det här avsnittet beskrivs möjliga problem som kan uppstå och hur problemen begränsas. Service Bus har infört ett antal åtgärder som du måste välja, och i det här avsnittet beskrivs även de regler som styr användningen av dessa deltagande åtgärder.

## <a name="reliability-in-service-bus"></a>Tillförlitlighet i Service Bus
Det finns flera sätt att hantera problem med meddelanden och enheter, och det finns rikt linjer för hur du kan använda dessa lösningar på rätt sätt. För att förstå rikt linjerna måste du först förstå vad som kan uppstå i Service Bus. På grund av utformningen av Azure-system tenderar alla dessa problem att vara kort livs längd. På hög nivå visas olika orsaker till otillgängligheten enligt följande:

* Begränsning från ett externt system som Service Bus är beroende av. Begränsning sker från interaktioner med lagrings-och beräknings resurser.
* Problem för ett system där Service Bus är beroende av. Till exempel kan en specifik del av lagringen drabbas av problem.
* Det gick inte att Service Bus på ett enskilt under system. I den här situationen kan en Compute-nod komma in i ett inkonsekvent tillstånd och måste starta om sig själv, vilket gör att alla entiteter kan belastningsutjämna till andra noder. Detta kan orsaka en kort period av långsam meddelande bearbetning.
* Det gick inte att Service Bus i ett Azure-datacenter. Detta är ett "oåterkalleligt haveri" som innebär att systemet inte kan kontaktas i flera minuter eller några timmar.

> [!NOTE]
> Termen **Storage** kan betyda både Azure Storage och SQL Azure.
> 
> 

Service Bus innehåller ett antal begränsningar för problemen. I följande avsnitt beskrivs varje problem och deras respektive åtgärder.

### <a name="throttling"></a>Begränsning
Med Service Bus aktiverar begränsningen hantering av kooperativa meddelande frekvens. Varje enskild Service Bus nod hus många entiteter. Var och en av dessa entiteter gör krav på systemet vad gäller CPU, minne, lagring och andra ansikte. Om någon av dessa ansikte upptäcker användning som överskrider definierade tröskelvärden kan Service Bus neka en viss begäran. Anroparen tar emot en [ServerBusyException][ServerBusyException] och försöker igen efter 10 sekunder.

Som en minskning måste koden läsa felet och stoppa alla återförsök i meddelandet i minst 10 sekunder. Eftersom felet kan inträffa mellan olika delar av kundens program, förväntas det att varje enskild enhet kör logiken igen. Koden kan minska sannolikheten för att begränsas genom att aktivera partitionering i en kö eller ett ämne.

### <a name="issue-for-an-azure-dependency"></a>Problem för ett Azure-beroende
Andra komponenter i Azure kan ibland ha problem med tjänsten. Till exempel när ett system som Service Bus använder uppgraderas, kan systemet tillfälligt uppleva lägre funktioner. För att lösa dessa typer av problem Service Bus regelbundet undersöka och implementerar begränsningar. Sido effekter av de här lösningarna visas. Om du till exempel vill hantera tillfälliga problem med lagringen implementerar Service Bus ett system som gör att meddelande överförings åtgärder fungerar konsekvent. På grund av typen av minskning kan ett skickat meddelande ta upp till 15 minuter visas i den berörda kön eller prenumerationen och vara redo för en mottagnings åtgärd. De flesta entiteter kommer i allmänhet inte att uppleva det här problemet. Men med tanke på antalet entiteter i Service Bus i Azure behövs den här lösningen ibland för en liten del av Service Bus kunderna.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Service Bus-problem på ett enskilt under system
I alla program kan omständigheter orsaka att en intern komponent i Service Bus blir inkonsekvent. När Service Bus identifierar detta samlar det in data från programmet för att hjälpa till att diagnostisera vad som hände. När data har samlats in startas programmet om i ett försök att returnera det till ett konsekvent tillstånd. Den här processen sker ganska snabbt, och det visas en entitet som visar sig vara otillgänglig i upp till några minuter, även om vanliga tider är mycket kortare.

I dessa fall genererar klient programmet ett [system. TimeoutException][System.TimeoutException] -eller [MessagingException][MessagingException] -undantag. Service Bus innehåller en minskning av det här problemet i form av logik för automatisk omprövning av klienter. När återförsöks perioden är slut och meddelandet inte levereras kan du utforska med andra som nämns i artikeln om [hantering av avbrott och haverier][handling outages and disasters].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om asynkrona meddelanden i Service Bus kan du läsa mer om hur du [hanterar avbrott och haverier][handling outages and disasters].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: /dotnet/api/system.timeoutexception
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: /dotnet/api/system.timespan.zero
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: /dotnet/api/system.unauthorizedaccessexception
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[handling outages and disasters]: service-bus-outages-disasters.md