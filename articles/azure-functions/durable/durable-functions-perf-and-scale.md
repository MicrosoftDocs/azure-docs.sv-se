---
title: Prestanda och skalning i Durable Functions – Azure
description: Lär dig mer om de unika skalnings egenskaperna för Durable Functions-tillägget för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 120335a7bce83bc3d4771ea64f665d67c7d1079a
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572807"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestanda och skalning i Durable Functions (Azure Functions)

För att optimera prestanda och skalbarhet är det viktigt att förstå de unika skalnings egenskaperna för [Durable Functions](durable-functions-overview.md).

För att förstå skalnings beteendet måste du förstå lite information om underliggande Azure Storage-providern.

## <a name="history-table"></a>Historik tabell

**Historik** tabellen är en Azure Storage tabell som innehåller historik händelser för alla Dirigerings instanser i en aktivitets hubb. Namnet på den här tabellen är i formatet *TaskHubName* historik. När instanser körs läggs nya rader till i den här tabellen. Partitionsnyckel för den här tabellen härleds från instans-ID: t för dirigeringen. Ett instans-ID är slumpmässigt i de flesta fall, vilket säkerställer optimal distribution av interna partitioner i Azure Storage.

När en Dirigerings instans måste köras läses rätt rader i historik tabellen in i minnet. Dessa *Historik händelser* spelas sedan upp i Orchestrator-funktions koden för att komma tillbaka till sitt tidigare tillstånd. Användningen av körnings historiken för att återskapa tillstånd på det här sättet påverkas av [mönstret för händelse källor](/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Instans tabell

**Instans** tabellen är en annan Azure Storage tabell som innehåller status för alla Dirigerings-och enhets instanser i en aktivitets hubb. När instanser skapas läggs nya rader till i den här tabellen. Partitionsnyckel för den här tabellen är Dirigerings instans-ID: t eller enhets nyckeln och rad nyckeln är en tom sträng. Det finns en rad per dirigering eller entitetsinstansen.

Den här tabellen används för att uppfylla instans förfrågningar från `GetStatusAsync` (.net) och `getStatus` (Java Script) API: er samt [status fråga http API](durable-functions-http-api.md#get-instance-status). Det hålls i enlighet med innehållet i **Historik** tabellen ovan. Användningen av en separat Azure Storage tabell för att effektivt uppfylla instanser av instans frågor på det här sättet påverkas av [mönstret uppdelning av kommando-och frågeåtgärder (CQRS)](/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Interna köade utlösare

Funktioner och aktivitets funktioner i Orchestrator utlöses båda av interna köer i Function-programmets aktivitets hubb. Med hjälp av köer på det här sättet får du en tillförlitlig garanti för meddelande leverans på minst en gång. Det finns två typer av köer i Durable Functions: **kontroll kön** och **arbets objekt kön**.

### <a name="the-work-item-queue"></a>Arbets objekts kön

Det finns en kö för arbets objekt per aktivitets hubb i Durable Functions. Det är en grundläggande kö och fungerar på samma sätt som andra `queueTrigger` köer i Azure Functions. Den här kön används för att utlösa tillstånds lösa *aktivitets funktioner* genom att köa ett enskilt meddelande i taget. Vart och ett av dessa meddelanden innehåller indata för aktivitets funktion och ytterligare metadata, till exempel vilken funktion som ska köras. När ett Durable Functions-program skalar ut till flera virtuella datorer, konkurrerar de virtuella datorerna med att förvärva arbete från arbets objekt kön.

### <a name="control-queues"></a>Kontroll köer

Det finns flera *kontroll köer* per aktivitets nav i Durable functions. En *kontroll kö* är mer avancerad än den enklare arbets objekts kön. Kontroll köer används för att utlösa tillstånds känsliga funktioner för Orchestrator och entitet. Eftersom Orchestrator-och enhets funktions instanserna är tillstånds känsliga singleton är det inte möjligt att använda en konkurrerande konsument modell för att distribuera belastningen mellan virtuella datorer. I stället är Orchestrator-och enhets meddelanden belastningsutjämnade i kontroll köerna. Mer information om det här problemet finns i följande avsnitt.

Kontroll köer innehåller olika typer av typer av Dirigerings-livs cykel meddelanden. Exempel på detta är [Orchestrator-kontrollmeddelanden](durable-functions-instance-management.md), meddelanden om aktivitets funktions *svar* och timer-meddelanden. Så många som 32 meddelanden tas i kö från en kontroll kö i en enda omröstning. Dessa meddelanden innehåller nytto Last data samt metadata, inklusive vilken Orchestration-instans den är avsedd för. Om flera meddelanden i kön är avsedda för samma Dirigerings instans kommer de att bearbetas som en batch.

### <a name="queue-polling"></a>Köa avsökning

Det varaktiga aktivitets tillägget implementerar en slumpmässig, exponentiell algoritm för att minska inaktive rad avsökningar på lagrings transaktions kostnader. När ett meddelande hittas söker körningen omedelbart efter ett annat meddelande. När inget meddelande hittas väntar den en stund innan den försöker igen. Efter efterföljande misslyckade försök att hämta ett Queue-meddelande fortsätter vänte tiden att öka tills den når den maximala vänte tiden, vilket är 30 sekunder.

Den maximala avsöknings fördröjningen kan konfigureras via `maxQueuePollingInterval` egenskapen i [host.jsi filen](../functions-host-json.md#durabletask). Om den här egenskapen ställs in på ett högre värde kan det resultera i högre meddelande fördröjning. Högre fördröjningar förväntas bara efter perioder av inaktivitet. Om du ställer in den här egenskapen till ett lägre värde kan högre lagrings kostnader uppstå på grund av ökade lagrings transaktioner.

> [!NOTE]
> När du kör i Azure Functions förbruknings-och Premium-planerna, avsöker [Azure Functions skalnings styrenheten](../event-driven-scaling.md) varje kontroll och arbets objekts kön var tionde sekund. Denna ytterligare avsökning är nödvändig för att avgöra när du ska aktivera Function App-instanser och fatta skalnings beslut. Vid tidpunkten för skrivning är det här 10 sekunder konstant och kan inte konfigureras.

### <a name="orchestration-start-delays"></a>Start fördröjningar för dirigering
Dirigerings instanser startas genom att placera ett `ExecutionStarted` meddelande i en av aktivitets hubbens kontroll köer. Under vissa omständigheter kan du se flera sekunders fördröjningar mellan när ett dirigering är schemalagt att köras och när det faktiskt börjar köras. Under det här tidsintervallet behålls Orchestration-instansen i `Pending` status. Det finns två möjliga orsaker till den här fördröjningen:

1. **Eftersläpande kontroll köer**: om kontroll kön för den här instansen innehåller ett stort antal meddelanden kan det ta tid innan `ExecutionStarted` meddelandet tas emot och bearbetas av körnings miljön. Meddelanden med loggfiler kan inträffa när dirigeringen behandlar mycket händelser samtidigt. Händelser som ingår i kontroll kön omfattar Dirigerings start händelser, aktivitetens slutdatum, varaktiga timers, avslutning och externa händelser. Om den här fördröjningen inträffar under normala omständigheter bör du överväga att skapa en ny aktivitetsvy med ett större antal partitioner. Om du konfigurerar fler partitioner kommer körningen att skapa fler kontroll köer för belastnings distribution.

2. **Inaktivera avsöknings fördröjningar**: en annan vanlig orsak till Dirigerings fördröjningar är den [tidigare beskrivna avsöknings beteendet för kontroll köer](#queue-polling). Denna fördröjning förväntas dock bara när en app skalas ut till två eller fler instanser. Om det bara finns en app-instans eller om den app-instans som startar dirigeringen också är samma instans som avsöker mål kontroll kön, kommer det inte att finnas någon fördröjning i kön. Du kan minska avsöknings fördröjningen genom att uppdatera **host.jspå** inställningar, enligt beskrivningen ovan.

## <a name="storage-account-selection"></a>Val av lagrings konto

Köer, tabeller och blobbar som används av Durable Functions skapas i ett konfigurerat Azure Storage konto. Kontot som ska användas kan anges med `durableTask/storageProvider/connectionStringName` inställningen (eller `durableTask/azureStorageConnectionStringName` inställningen i Durable Functions 1. x) i **host.js** filen.

### <a name="durable-functions-2x"></a>Durable Functions 2. x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Om inget värde anges används standard `AzureWebJobsStorage` lagrings kontot. För prestanda känsliga arbets belastningar rekommenderas dock inte att konfigurera ett lagrings konto som inte är standard. Durable Functions använder Azure Storage kraftigt och använder ett dedikerat lagrings konto för att isolera Durable Functions lagrings användningen från den interna användningen av Azure Functions-värden.

## <a name="orchestrator-scale-out"></a>Skalbarhet för Orchestrator

Aktivitets funktionerna är tillstånds lösa och skalas ut automatiskt genom att lägga till virtuella datorer. Orchestrator-funktioner och entiteter, å andra sidan, *partitioneras* i en eller flera kontroll köer. Antalet kontroll köer definieras i **host.js** i filen. I följande exempel host.jsi kodfragmentet anges `durableTask/storageProvider/partitionCount` egenskapen (eller `durableTask/partitionCount` i Durable Functions 1. x) till `3` .

### <a name="durable-functions-2x"></a>Durable Functions 2. x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

En aktivitets hubb kan konfigureras med mellan 1 och 16 partitioner. Om inget värde anges är standardpartitions antalet **4**.

Vid skalning till flera funktions värd instanser (vanligt vis på olika virtuella datorer) får varje instans ett lås i en av kontroll köerna. Dessa lås implementeras internt som Blob Storage-lån och säkerställer att en Dirigerings instans eller entitet bara körs på en enda värd instans i taget. Om en aktivitets hubb har kon figurer ATS med tre kontroll köer kan Dirigerings instanser och entiteter vara belastningsutjämnade i upp till tre virtuella datorer. Ytterligare virtuella datorer kan läggas till för att öka kapaciteten för körning av aktivitets funktionen.

Följande diagram illustrerar hur Azure Functions-värden interagerar med lagrings enheterna i en skalad miljö.

![Skala diagram](./media/durable-functions-perf-and-scale/scale-diagram.png)

Som du ser i föregående diagram konkurrerar alla virtuella datorer om meddelanden i arbets objekt kön. Men endast tre virtuella datorer kan hämta meddelanden från kontroll köer och varje virtuell dator låser en enskild kontroll kö.

Dirigerings instanser och entiteter distribueras över alla instanser i kontroll kön. Fördelningen görs genom hashing av instans-ID: t för dirigeringen eller enhets namnet och nyckel paret. Dirigerings instans-ID: n är som standard slumpmässiga GUID, vilket säkerställer att instanserna är jämnt fördelade över alla kontroll köer.

I allmänhet är Orchestrator-funktioner avsedda att vara lätta att vara lätta och bör inte kräva stora mängder data behandlings kraft. Därför är det inte nödvändigt att skapa ett stort antal Control-diskpartitioner för att få ett bra data flöde för dirigering. Det mesta av det tunga arbetet bör utföras i tillstånds lösa aktivitets funktioner som kan skalas ut oändligt.

## <a name="auto-scale"></a>Automatisk skalning

Precis som med alla Azure Functions som körs i förbruknings-och elastiska Premium-planerna stöder Durable Functions automatisk skalning via [Azure Functions skalnings styrenheten](../event-driven-scaling.md#runtime-scaling). Skalnings kontrollen övervakar svars tiden för alla köer genom att regelbundet skicka _gransknings_ kommandon. Baserat på fördröjningen hos de granskade meddelandena bestämmer skalnings styrenheten om du vill lägga till eller ta bort virtuella datorer.

Om skalnings styrenheten bestämmer att meddelande fördröjningarna för kontroll kön är för höga, lägger den till virtuella dator instanser tills svars tiden minskas till en acceptabel nivå eller når gränsen för kontroll köns partition. På samma sätt kommer skalnings styrenheten att kontinuerligt lägga till virtuella dator instanser om svars tiden för arbets objekt i kö är hög, oavsett antalet partitioner.

> [!NOTE]
> Från och med Durable Functions 2,0 kan Function-appar konfigureras för att köras i VNET-skyddade tjänst slut punkter i elastisk Premium-prenumerationen. I den här konfigurationen utlöser Durable Functions initierar skalnings begär anden i stället för skalnings styrenheten.

## <a name="thread-usage"></a>Tråd användning

Orchestrator-funktioner körs på en enda tråd för att säkerställa att körningen kan vara deterministisk över flera uppspelningar. På grund av den här enkla körningen är det viktigt att Orchestrator Function-trådar inte utför processor intensiva uppgifter, gör I/O eller blockera av någon anledning. Allt arbete som kan kräva I/O, blockera eller flera trådar bör flyttas till aktivitets funktioner.

Aktivitets funktioner har samma beteende som regelbundna köade funktioner. De kan på ett säkert sätt göra I/O, köra processor intensiva åtgärder och använda flera trådar. Eftersom aktivitets utlösare är tillstånds lösa kan de fritt skalas ut till ett obegränsat antal virtuella datorer.

Enhets funktioner körs också på en enda tråd och åtgärder bearbetas en i taget. Enhets funktioner har dock inga begränsningar för vilken typ av kod som kan köras.

## <a name="concurrency-throttles"></a>Samtidighets begränsningar

Azure Functions stöder körning av flera funktioner samtidigt inom en enda App-instans. Den här samtidiga körningen bidrar till att öka parallellitet och minimerar antalet "kall starter" som en typisk app kommer att uppleva över tid. Hög samtidighet kan dock användas för att avsluta system resurser per virtuell dator, t. ex. nätverks anslutningar eller tillgängligt minne. Beroende på behoven i Function-appen kan det vara nödvändigt att reglera per-instans-samtidigheten för att undvika möjligheten att få slut på minne i stora inläsnings situationer.

Samtidighets gränser för aktiviteter, Orchestrator och entiteter kan konfigureras i **host.js** i filen. De relevanta inställningarna är `durableTask/maxConcurrentActivityFunctions` för aktivitets funktioner och `durableTask/maxConcurrentOrchestratorFunctions` för både funktioner för Orchestrator och entitet.

### <a name="functions-20"></a>Functions 2,0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

I föregående exempel kan maximalt 10 funktioner för Orchestrator eller entitet och 10 aktivitets funktioner köras på en enskild virtuell dator samtidigt. Om detta inte anges, är antalet körningar av samtidiga aktiviteter och Orchestrator-eller Entity-funktionen ett tak på 10X antalet kärnor på den virtuella datorn.

> [!NOTE]
> De här inställningarna är användbara för att hantera minnes-och CPU-användning på en enskild virtuell dator. Men när de skalas ut över flera virtuella datorer har varje virtuell dator en egen uppsättning gränser. De här inställningarna kan inte användas för att kontrol lera samtidighet på global nivå.

## <a name="extended-sessions"></a>Utökade sessioner

Utökade sessioner är en inställning som skyddar dirigeringar och entiteter i minnet även när de har slutfört bearbetningen av meddelanden. Den normala påverkan av att aktivera utökade sessioner minskar I/O mot Azure Storage kontot och det övergripande data flödet.

Du kan aktivera utökade sessioner genom att ställa in på `durableTask/extendedSessionsEnabled` `true` i **host.jspå** filen. `durableTask/extendedSessionIdleTimeoutInSeconds`Inställningen kan användas för att styra hur länge en inaktiv session ska lagras i minnet:

**Functions 2,0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**Functions 1,0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Det finns två möjliga nack delen med den här inställningen för att vara medveten om:

1. Det finns en övergripande ökning av minnes användningen för Function-appar.
2. Det kan finnas en övergripande minskning i data flödet om det finns många samtidiga, kortsiktiga Orchestrator-eller enhets funktions körningar.

Exempel: om `durableTask/extendedSessionIdleTimeoutInSeconds` är inställt på 30 sekunder, kan ett avsnitt med en kort livs längd för Orchestrator-eller enhets funktioner som körs på mindre än 1 sekund fortfarande uppta minne i 30 sekunder. Det räknas även mot den `durableTask/maxConcurrentOrchestratorFunctions` kvot som anges ovan, vilket kan förhindra att andra Orchestrator-eller entitet-funktioner körs.

De olika effekterna av utökade sessioner i funktionerna för Orchestrator och entiteter beskrivs i nästa avsnitt.

> [!NOTE]
> Utökade sessioner stöds för närvarande bara på .NET-språk, t. ex. C# eller F #. Inställningen `extendedSessionsEnabled` till `true` för andra plattformar kan leda till körnings problem, t. ex. tyst misslyckad körning av aktivitet och Orchestration-utlösta funktioner.


### <a name="orchestrator-function-replay"></a>Uppspelning av Orchestrator-funktion

Som tidigare nämnts spelas Orchestrator-funktionerna om med hjälp av innehållet i **Historik** tabellen. Som standard spelas Orchestrator-funktions koden upp varje gång en grupp meddelanden tas ur kö från en kontroll kö. Även om du använder fläkten, fläkten och väntar på att alla aktiviteter ska slutföras (till exempel med `Task.WhenAll` i .net eller `context.df.Task.all` i Java Script), kommer det att spelas upp som inträffar när batchar av uppgifts svar bearbetas över tid. När utökade sessioner är aktiverade hålls Orchestrator Function-instanser i minnet längre och nya meddelanden kan bearbetas utan att en fullständig historik spelas upp.

Prestanda förbättringen av utökade sessioner observeras oftast i följande situationer:

* När ett begränsat antal Dirigerings instanser körs samtidigt.
* När dirigeringen har ett stort antal sekventiella åtgärder (t. ex. hundratals aktivitets funktions anrop) som slutförs snabbt.
* När Orchestration-utsättning och fläkt är i ett stort antal åtgärder som slutförs ungefär samma gång.
* När Orchestrator-funktioner behöver bearbeta stora meddelanden eller utföra all processor intensiv data bearbetning.

I alla andra situationer finns det vanligt vis ingen observerad prestanda förbättring för Orchestrator-funktioner.

> [!NOTE]
> De här inställningarna bör endast användas när en Orchestrator-funktion har utvecklats helt och testats. Standardvärdet för aggressiva omuppspelning kan vara användbart för att identifiera problem vid utveckling av [Orchestrator-funktioner](durable-functions-code-constraints.md) vid utvecklings tiden och är därför inaktiverat som standard.

### <a name="entity-function-unloading"></a>Ta bort enhets funktion

Enhets funktioner bearbetar upp till 20 åtgärder i en enda batch. Så fort en entitet har slutfört bearbetningen av en batch med åtgärder, behåller den sitt tillstånd och tas bort från minnet. Du kan fördröja inläsningen av entiteter från minnet med inställningen för utökade sessioner. Enheter fortsätter att spara sina tillstånds ändringar som tidigare, men finns kvar i minnet under den konfigurerade tids perioden för att minska antalet inläsningar från Azure Storage. Den här minskningen av inläsningar från Azure Storage kan förbättra det totala data flödet för entiteter som används ofta.

## <a name="performance-targets"></a>Prestanda mål

När du planerar att använda Durable Functions för ett produktions program är det viktigt att beakta prestanda kraven tidigt i planerings processen. I det här avsnittet beskrivs några grundläggande användnings scenarier och förväntade högsta data flödes nummer.

* **Sekventiell aktivitets körning**: det här scenariot beskriver en Orchestrator-funktion som kör en serie aktivitets funktioner en efter den andra. Det liknar närmast [funktionen kedje](durable-functions-sequence.md) exempel.
* **Körning av parallell aktivitet**: i det här scenariot beskrivs en Orchestrator-funktion som kör många aktivitets funktioner parallellt med [fläkt-och fläkt](durable-functions-cloud-backup.md) mönster.
* **Bearbetning av parallella svar**: det här scenariot är den andra halvan av det mönster som används för [fläkten](durable-functions-cloud-backup.md) . Den fokuserar på prestandan hos fläkten. Det är viktigt att tänka på att till skillnad från fläkt, som görs av en enda Orchestrator Function-instans och därför bara kan köras på en enda virtuell dator.
* **Extern händelse bearbetning**: det här scenariot representerar en enskild Orchestrator Function-instans som väntar på [externa händelser](durable-functions-external-events.md), en i taget.
* **Bearbetning av enhets åtgärd**: det här scenariot testar hur _snabbt en entitet med en_ [räknare](durable-functions-entities.md) kan bearbeta en konstant ström av åtgärder.

> [!TIP]
> Till skillnad från fläkt, är fläkt i-åtgärder begränsade till en enda virtuell dator. Om ditt program använder fläkten, fläkten och du är bekymrad om fläkt prestanda bör du överväga att dela upp aktivitetens fläkt [över flera](durable-functions-sub-orchestrations.md)underordningar.

I följande tabell visas de förväntade *högsta* data flödes numren för de scenarier som beskrivs ovan. "Instance" syftar på en enda instans av en Orchestrator-funktion som körs på en enskild liten ([a1](../../virtual-machines/sizes-previous-gen.md)) virtuell dator i Azure App Service. I samtliga fall förutsätts att [utökade sessioner](#orchestrator-function-replay) är aktiverade. De faktiska resultaten kan variera beroende på processor-eller I/O-arbete som utförs av funktions koden.

| Scenario | Maximalt dataflöde |
|-|-|
| Körning av sekventiell aktivitet | 5 aktiviteter per sekund, per instans |
| Körning av parallell aktivitet (fläkt ut) | 100 aktiviteter per sekund, per instans |
| Bearbetning av parallella svar (fläkt-in) | 150 svar per sekund, per instans |
| Extern händelse bearbetning | 50 händelser per sekund, per instans |
| Bearbetning av enhets åtgärd | 64 åtgärder per sekund |

> [!NOTE]
> De här talen är aktuella från och med v 1.4.0-versionen (GA) av Durable Functions-tillägget. De här talen kan ändras med tiden när funktionen är vuxen och som optimeringar.

Om du inte ser de data flödes nummer du förväntar dig och din CPU-och minnes användning verkar vara felfri, kontrollerar du om orsaken är relaterad till [ditt lagrings kontos hälso tillstånd](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Durable Functions-tillägget kan leda till betydande belastning på ett Azure Storage konto och tillräckligt stora belastningar kan resultera i begränsning av lagrings kontot.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om haveri beredskap och geo-distribution](durable-functions-disaster-recovery-geo-distribution.md)
