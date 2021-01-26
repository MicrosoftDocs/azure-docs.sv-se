---
title: Diagnostik och övervakning av Azure ServiceFabric
description: Den här artikeln beskriver prestanda övervaknings funktionerna i Service Fabric Reliable ServiceRemoting-körningsmiljön, t. ex. prestanda räknare som släpps av den.
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 9c7d466d6e8fd36b4445966b92ee753becf96c64
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791769"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostik-och prestanda övervakning för Reliable service Remoting
Den Reliable ServiceRemoting-körningen genererar  [prestanda räknare](/dotnet/api/system.diagnostics.performancecounter). Dessa ger insikter om hur ServiceRemoting fungerar och hjälper till med fel sökning och prestanda övervakning.


## <a name="performance-counters"></a>Prestandaräknare
Den Reliable ServiceRemoting-körningen definierar följande kategorier för prestanda räknare:

| Kategori | Beskrivning |
| --- | --- |
| Service Fabric tjänst |Räknare som är speciella för fjärr kommunikation med Azure Service Fabric-tjänster, till exempel genomsnittlig tid det tar att bearbeta begäran |
| Service Fabric tjänst metod |Räknare som är speciella för metoder som implementeras av Service Fabric fjärr kommunikations tjänst, till exempel hur ofta en tjänst metod anropas |

Var och en av de föregående kategorierna har en eller flera räknare.

[Windows prestanda övervaknings](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) programmet som är tillgängligt som standard i Windows-operativsystemet kan användas för att samla in och Visa prestanda räknar data. [Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics.md) är ett annat alternativ för att samla in prestanda räknar data och ladda upp den till Azure-tabeller.

### <a name="performance-counter-instance-names"></a>Instans namn för prestanda räknare
Ett kluster som har ett stort antal ServiceRemoting-tjänster eller partitioner har ett stort antal prestanda räknar instanser. Instans namn för prestanda räknaren kan hjälpa till att identifiera den angivna partitionen och tjänst metoden (om tillämpligt) som prestanda räknar instansen är associerad med.

#### <a name="service-fabric-service-category"></a>Service Fabric tjänste kategori
För kategorin `Service Fabric Service` är räknar instans namnen i följande format:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* är sträng representationen av Service Fabric partitions-ID: t som prestanda räknar instansen är associerad med. Partitions-ID: t är ett GUID och dess sträng representation genereras genom [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) metoden med format specificeraren "D".

*ServiceReplicaOrInstanceId* är sträng representationen av Service Fabric replik/instans-ID som prestanda räknar instansen är associerad med.

*ServiceRuntimeInternalID* är en sträng representation av ett 64-bitars heltal som genereras av Fabric service runtime för intern användning. Detta ingår i prestanda räknarens instans namn för att säkerställa dess unika värde och undvika konflikter med andra instans namn för prestanda räknaren. Användare bör inte försöka tolka den här delen av prestanda räknarens instans namn.

Följande är ett exempel på ett räknar instans namn för en räknare som tillhör `Service Fabric Service` kategorin:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

I föregående exempel `2740af29-78aa-44bc-a20b-7e60fb783264` är sträng representationen av Service Fabric partitions-ID: t en `635650083799324046` sträng representation av replik/InstanceID och `5008379932` är 64-bitars-ID som genereras för körningens interna användning.

#### <a name="service-fabric-service-method-category"></a>Service Fabric tjänst metod kategori
För kategorin `Service Fabric Service Method` är räknar instans namnen i följande format:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* är namnet på den tjänst metod som prestanda räknar instansen är associerad med. Formatet på metod namnet bestäms baserat på en del av logiken i körnings miljön för infrastruktur resurser som balanserar läsbarheten hos namnet med begränsningar för den maximala längden på instans namnen för prestanda räknarna i Windows.

*ServiceRuntimeMethodId* är en sträng representation av ett 32-bitars heltal som genereras av Fabric service runtime för intern användning. Detta ingår i prestanda räknarens instans namn för att säkerställa dess unika värde och undvika konflikter med andra instans namn för prestanda räknaren. Användare bör inte försöka tolka den här delen av prestanda räknarens instans namn.

*ServiceFabricPartitionID* är sträng representationen av Service Fabric partitions-ID: t som prestanda räknar instansen är associerad med. Partitions-ID: t är ett GUID och dess sträng representation genereras genom [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) metoden med format specificeraren "D".

*ServiceReplicaOrInstanceId* är sträng representationen av Service Fabric replik/instans-ID som prestanda räknar instansen är associerad med.

*ServiceRuntimeInternalID* är en sträng representation av ett 64-bitars heltal som genereras av Fabric service runtime för intern användning. Detta ingår i prestanda räknarens instans namn för att säkerställa dess unika värde och undvika konflikter med andra instans namn för prestanda räknaren. Användare bör inte försöka tolka den här delen av prestanda räknarens instans namn.

Följande är ett exempel på ett räknar instans namn för en räknare som tillhör `Service Fabric Service Method` kategorin:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

I föregående exempel `ivoicemailboxservice.leavemessageasync` är metod namnet, `2` det 32-bitars ID som genereras för körningens interna användning, `89383d32-e57e-4a9b-a6ad-57c6792aa521` sträng representationen av Service Fabric partitions-ID, `635650083804480486` är sträng representationen av Service Fabric REPLIKENS/instans-ID och `5008380` är 64-bitars-ID som genereras för körningens interna användning.

## <a name="list-of-performance-counters"></a>Lista över prestanda räknare
### <a name="service-method-performance-counters"></a>Prestanda räknare för tjänst metod

Den Reliable service runtime publicerar följande prestanda räknare relaterade till körningen av tjänst metoder.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric tjänst metod |Anrop/SEK |Antal gånger som tjänst metoden anropas per sekund |
| Service Fabric tjänst metod |Genomsnittligt antal millisekunder per anrop |Åtgången tid för att köra tjänst metoden i millisekunder |
| Service Fabric tjänst metod |Genererade undantag/SEK |Antal gånger som tjänst metoden utlöste ett undantag per sekund |

### <a name="service-request-processing-performance-counters"></a>Bearbetnings prestanda räknare för tjänstbegäran
När en klient anropar en metod via ett proxyobjekt, resulterar det i att ett begär ande meddelande skickas via nätverket till fjärr kommunikations tjänsten. Tjänsten bearbetar begär ande meddelandet och skickar tillbaka ett svar till klienten. Den tillförlitliga ServiceRemoting-körningen publicerar följande prestanda räknare relaterade till behandling av tjänstbegäran.

| Kategorinamn | Räknarens namn | Beskrivning |
| --- | --- | --- |
| Service Fabric tjänst |antal utestående begär Anden |Antal begär Anden som bearbetas i tjänsten |
| Service Fabric tjänst |Genomsnittligt antal millisekunder per begäran |Åtgången tid (i millisekunder) av tjänsten för att bearbeta en begäran |
| Service Fabric tjänst |Genomsnittligt antal millisekunder för avserialisering av begäran |Tids åtgång (i millisekunder) för att deserialisera meddelandet om tjänstbegäran när det tas emot på tjänsten |
| Service Fabric tjänst |Genomsnittligt antal millisekunder för svars serialisering |Åtgången tid (i millisekunder) för att serialisera tjänst svars meddelandet i tjänsten innan svaret skickas till klienten |

## <a name="next-steps"></a>Nästa steg
* [Exempel kod](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [EventSource-providers i PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
