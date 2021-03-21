---
title: Aktörernas diagnostik och övervakning
description: I den här artikeln beskrivs funktionerna för diagnostik-och prestanda övervakning i Service Fabric Reliable Actors runtime, inklusive händelser och prestanda räknare som genereras av den.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: ebaedb5369f3b39372262bfde526706e8d069418
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789624"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostik- och prestandaövervakning för Reliable Actors
Reliable Actors körningen genererar [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) -händelser och [prestanda räknare](/dotnet/api/system.diagnostics.performancecounter). Dessa ger insikter om hur körningen fungerar och hjälper till med fel sökning och prestanda övervakning.

## <a name="eventsource-events"></a>EventSource-händelser
EventSource Provider-namnet för Reliable Actors runtime är "Microsoft-ServiceFabric-skådespelare". Händelser från den här händelse källan visas i fönstret [diagnostik-händelser](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) när aktörs programmet [felsöks i Visual Studio](service-fabric-debugging-your-application.md).

Exempel på verktyg och tekniker som hjälper dig att samla in och/eller Visa EventSource-händelser är [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics.md), [semantisk loggning](/previous-versions/msp-n-p/dn774980(v=pandp.10))och [Microsoft TraceEvent-biblioteket](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Nyckelord
Alla händelser som tillhör Reliable Actors EventSource är associerade med ett eller flera nyckelord. Detta möjliggör filtrering av händelser som samlas in. Följande nyckelords bitar har definierats.

| Bitmask | Beskrivning |
| --- | --- |
| 0x1 |En uppsättning viktiga händelser som sammanfattar driften av körningen av Fabric-aktörer. |
| 0x2 |Uppsättning händelser som beskriver aktörs metod anrop. Mer information finns i [introduktions avsnittet om aktörer](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Uppsättning händelser som rör aktörs status. Mer information finns i avsnittet om hantering av [aktörs tillstånd](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Uppsättning händelser relaterade till turn samtidighet i aktören. Mer information finns i avsnittet om [samtidighet](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Prestandaräknare
Reliable Actors runtime definierar följande prestanda räknar kategorier.

| Kategori | Beskrivning |
| --- | --- |
| Service Fabric skådespelare |Räknare som är speciella för Azure Service Fabric-aktörer, t. ex. tid för att spara aktörs tillstånd |
| Service Fabric aktörs metod |Räknare som är speciella för metoder som implementerats av Service Fabric-aktörer, t. ex. hur ofta en aktörs metod anropas |

Var och en av kategorierna ovan har en eller flera räknare.

[Windows prestanda övervaknings](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) programmet som är tillgängligt som standard i Windows-operativsystemet kan användas för att samla in och Visa prestanda räknar data. [Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in prestanda räknar data och ladda upp den till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Instans namn för prestanda räknare
Ett kluster som har ett stort antal aktörs tjänster eller aktörs tjänst partitioner kommer att ha ett stort antal instanser av prestanda räknaren för aktör. Instans namn för prestanda räknaren kan hjälpa till att identifiera den angivna [partitionen](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) och aktörs metoden (om tillämpligt) som prestanda räknar instansen är associerad med.

#### <a name="service-fabric-actor-category"></a>Kategorin Service Fabric aktör
För kategorin `Service Fabric Actor` är räknar instans namnen i följande format:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* är sträng representationen av Service Fabric partitions-ID: t som prestanda räknar instansen är associerad med. Partitions-ID: t är ett GUID och dess sträng representation genereras genom [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) metoden med format specificeraren "D".

*ActorRuntimeInternalID* är en sträng representation av ett 64-bitars heltal som genereras av Fabric-aktörernas kör tid för intern användning. Detta ingår i prestanda räknarens instans namn för att säkerställa dess unika värde och undvika konflikter med andra instans namn för prestanda räknaren. Användare bör inte försöka tolka den här delen av prestanda räknarens instans namn.

Följande är ett exempel på ett räknar instans namn för en räknare som tillhör `Service Fabric Actor` kategorin:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

I exemplet ovan `2740af29-78aa-44bc-a20b-7e60fb783264` är sträng representationen av Service Fabric partitions-ID och `635650083799324046` är 64-bitars-ID som genereras för körningens interna användning.

#### <a name="service-fabric-actor-method-category"></a>Metod kategori för Service Fabric aktör
För kategorin `Service Fabric Actor Method` är räknar instans namnen i följande format:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* är namnet på aktörs metoden som prestanda räknar instansen är associerad med. Formatet på metod namnet bestäms utifrån en del av logiken i körnings miljön för infrastruktur resurser som balanserar läsbarheten hos namnet med begränsningar på den maximala längden för prestanda räknar instans namnen i Windows.

*ActorsRuntimeMethodId* är en sträng representation av ett 32-bitars heltal som genereras av Fabric-aktörernas kör tid för intern användning. Detta ingår i prestanda räknarens instans namn för att säkerställa dess unika värde och undvika konflikter med andra instans namn för prestanda räknaren. Användare bör inte försöka tolka den här delen av prestanda räknarens instans namn.

*ServiceFabricPartitionID* är sträng representationen av Service Fabric partitions-ID: t som prestanda räknar instansen är associerad med. Partitions-ID: t är ett GUID och dess sträng representation genereras genom [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) metoden med format specificeraren "D".

*ActorRuntimeInternalID* är en sträng representation av ett 64-bitars heltal som genereras av Fabric-aktörernas kör tid för intern användning. Detta ingår i prestanda räknarens instans namn för att säkerställa dess unika värde och undvika konflikter med andra instans namn för prestanda räknaren. Användare bör inte försöka tolka den här delen av prestanda räknarens instans namn.

Följande är ett exempel på ett räknar instans namn för en räknare som tillhör `Service Fabric Actor Method` kategorin:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

I exemplet ovan `ivoicemailboxactor.leavemessageasync` är metod namnet, `2` det 32-bitars ID som genereras för körningens interna användning, `89383d32-e57e-4a9b-a6ad-57c6792aa521` är sträng representationen för Service Fabric partitions-id och `635650083804480486` är 64-bitars-ID som genereras för körningens interna användning.

## <a name="list-of-events-and-performance-counters"></a>Lista över händelser och prestandaräknare
### <a name="actor-method-events-and-performance-counters"></a>Aktörs metod händelser och prestanda räknare
Reliable Actors runtime genererar följande händelser som rör [aktörs metoder](service-fabric-reliable-actors-introduction.md).

| Händelse namn | Händelse-ID | Nivå | Följt | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Verbose |0x2 |Körningar av aktörer är på väg att anropa en aktörs metod. |
| ActorMethodStop |8 |Verbose |0x2 |En aktörs metod har slutfört körningen. Det vill säga körningens asynkrona anrop till aktörs metoden har returnerat och uppgiften som returnerades av aktörs metoden har slutförts. |
| ActorMethodThrewException |9 |Varning |0x3 |Ett undantag uppstod under körningen av en aktörs metod, antingen under körningens asynkrona anrop till aktörs metoden eller under körningen av den uppgift som returnerades av aktörs metoden. Den här händelsen indikerar viss typ av haveri i aktörs koden som behöver undersökning. |

Reliable Actors runtime publicerar följande prestanda räknare relaterade till körningen av aktörs metoder.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric aktörs metod |Anrop/SEK |Antal gånger som aktörs tjänst metoden anropas per sekund |
| Service Fabric aktörs metod |Genomsnittligt antal millisekunder per anrop |Åtgången tid för att köra aktörs tjänst metoden i millisekunder |
| Service Fabric aktörs metod |Genererade undantag/SEK |Antal gånger som aktörs tjänst metoden utlöste ett undantag per sekund |

### <a name="concurrency-events-and-performance-counters"></a>Samtidiga händelser och prestanda räknare
Reliable Actors runtime genererar följande händelser relaterade till [samtidighet](service-fabric-reliable-actors-introduction.md#concurrency).

| Händelse namn | Händelse-ID | Nivå | Följt | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Verbose |0x8 |Den här händelsen skrivs i början av varje ny tur i en aktör. Det innehåller antalet väntande aktörs anrop som väntar på att erhålla lås per aktör som tillämpar fast samtidighet. |

Reliable Actors runtime publicerar följande prestanda räknare relaterade till samtidighet.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric skådespelare |antal aktörs anrop som väntar på att aktörs låset |Antal aktörs anrop som väntar på att erhålla lås per aktör som tillämpar fast samtidighet |
| Service Fabric skådespelare |Genomsnittlig vänte tid i millisekunder per lås |Åtgången tid (i millisekunder) för att hämta låset per aktör som framtvingar Turn-baserad samtidighet |
| Service Fabric skådespelare |Genomsnittligt antal millisekunder för aktörs lås |Tid (i millisekunder) för vilken låset per aktör hålls |

### <a name="actor-state-management-events-and-performance-counters"></a>Aktörs tillstånds hanterings händelser och prestanda räknare
Reliable Actors runtime genererar följande händelser relaterade till hantering av [aktörs tillstånd](service-fabric-reliable-actors-state-management.md).

| Händelse namn | Händelse-ID | Nivå | Följt | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Verbose |0x4 |Aktörernas kör tid är på väg att spara aktörs tillstånd. |
| ActorSaveStateStop |11 |Verbose |0x4 |Aktörs körningen har slutat att aktörens status har sparats. |

Reliable Actors runtime publicerar följande prestanda räknare relaterade till hantering av aktörs tillstånd.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric skådespelare |Genomsnittligt antal millisekunder per Save-tillstånd |Åtgången tid för att spara aktörs tillstånd i millisekunder |
| Service Fabric skådespelare |Genomsnittligt antal millisekunder per inläsnings tillstånd |Åtgången tid för att läsa in aktörs tillstånd i millisekunder |

### <a name="events-related-to-actor-replicas"></a>Händelser relaterade till aktörs repliker
Reliable Actors runtime genererar följande händelser relaterade till [aktörens repliker](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Händelse namn | Händelse-ID | Nivå | Följt | Beskrivning |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Information |0x1 |Aktörens replik ändrade rollen till primär. Detta innebär att skådespelare för den här partitionen skapas i den här repliken. |
| ReplicaChangeRoleFromPrimary |2 |Information |0x1 |Aktörens replik ändrade rollen till icke-primär. Detta innebär att skådespelare för den här partitionen inte längre skapas i den här repliken. Inga nya förfrågningar skickas till aktörer som redan har skapats i den här repliken. Aktörerna förstörs efter att alla pågående begär Anden har slutförts. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Aktivering och inaktive ring av aktör, händelser och prestanda räknare
Reliable Actors runtime genererar följande händelser som rör [aktivering och inaktive](service-fabric-reliable-actors-lifecycle.md)ring av aktör.

| Händelse namn | Händelse-ID | Nivå | Följt | Beskrivning |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Information |0x1 |En aktör har Aktiver ATS. |
| ActorDeactivated |6 |Information |0x1 |En aktör har inaktiverats. |

Reliable Actors runtime publicerar följande prestanda räknare relaterade till aktivering och inaktive ring av aktör.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric skådespelare |Genomsnittlig OnActivateAsync millisekunder |Åtgången tid för att köra OnActivateAsync-metoden i millisekunder |

### <a name="actor-request-processing-performance-counters"></a>Aktör begär behandling prestanda räknare
När en klient anropar en metod via ett aktörs objekt, resulterar det i ett meddelande som skickas via nätverket till aktörs tjänsten. Tjänsten bearbetar begär ande meddelandet och skickar tillbaka ett svar till klienten. Reliable Actors runtime publicerar följande prestanda räknare relaterade till bearbetning av aktörs förfrågningar.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric skådespelare |antal utestående begär Anden |Antal begär Anden som bearbetas i tjänsten |
| Service Fabric skådespelare |Genomsnittligt antal millisekunder per begäran |Åtgången tid (i millisekunder) av tjänsten för att bearbeta en begäran |
| Service Fabric skådespelare |Genomsnittligt antal millisekunder för avserialisering av begäran |Åtgången tid (i millisekunder) för att deserialisera aktör begär ande meddelande när det tas emot på tjänsten |
| Service Fabric skådespelare |Genomsnittligt antal millisekunder för svars serialisering |Åtgången tid (i millisekunder) för att serialisera aktörens svars meddelande vid tjänsten innan svaret skickas till klienten |

## <a name="next-steps"></a>Nästa steg
* [Hur Reliable Actors använder Service Fabrics plattformen](service-fabric-reliable-actors-platform.md)
* [Dokumentation om aktörs-API-referens](/previous-versions/azure/dn971626(v=azure.100))
* [Exempel kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [EventSource-providers i PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
