---
title: Programmerings guide för .NET – Azure Event Hubs (bakåtkompatibelt) | Microsoft Docs
description: Den här artikeln innehåller information om hur du skriver kod för Azure Event Hubs med hjälp av Azure .NET SDK.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f95abe3668bb400d84e354c3bca9eac289c5795
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108695"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>Programmerings guide för .NET för Azure Event Hubs (äldre Microsoft. Azure. EventHubs-paket)
I den här artikeln beskrivs några vanliga scenarier när du skriver kod med hjälp av Azure Event Hubs. Den förutsätter att du har en grundläggande förståelse av händelsehubbar. En konceptuell översikt av händelsehubbar finns på [Översikt av händelsehubbar](./event-hubs-about.md).

> [!WARNING]
> Den här guiden gäller för det gamla **Microsoft. Azure. EventHubs** -paketet. Vi rekommenderar att du [migrerar](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) koden så att den använder det senaste [Azure. Messaging. EventHubs](event-hubs-dotnet-standard-getstarted-send.md) -paketet.  


## <a name="event-publishers"></a>Händelseutfärdare

Du skickar händelser till en händelsehubben antingen via HTTP POST eller via en AMQP 1,0-anslutning. Valet av som ska användas och när är beroende av det speciella scenariot som åtgärdas. AMQP 1.0-anslutningar är avgiftsbelagda som asynkrona anslutningar i Service Bus och är mer lämpliga i scenarier med ofta högre meddelandevolymer och lägre svarstidskrav, eftersom de tillhandahåller en permanent meddelandekanal.

När du använder de .NET-hanterade API:erna är de primära konstruktionerna för att publicera data i händelsehubbar klasserna [EventHubClient][] och [EventData][]. [EventHubClient][] tillhandahåller den AMQP kommunikations kanal som händelser skickas till Event Hub. [EventData][] -klassen representerar en händelse och används för att publicera meddelanden till en Event Hub. Den här klassen innehåller bröd texten, vissa metadata (egenskaper) och rubrik information (SystemProperties) om händelsen. Andra egenskaper läggs till i [EventData][] -objektet när det passerar en händelsehubben.

## <a name="get-started"></a>Kom igång
De .NET-klasser som stöder Event Hubs finns i [Microsoft. Azure. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet-paketet. Du kan installera med hjälp av Visual Studio Solution Explorer eller [Package Manager-konsolen](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) i Visual Studio. Om du vill göra det kör du följande kommando i [Package Manager-konsol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)-fönstret:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan använda Azure Portal, Azure PowerShell eller Azure CLI för att skapa Event Hubs. Mer information finns i [skapa ett Event Hubs-namnområde och en händelsehubben med hjälp av Azure Portal](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Skapa en händelsehubbklient

Den primära klassen för att interagera med Event Hubs är [Microsoft. Azure. EventHubs. EventHubClient][EventHubClient]. Du kan skapa en instans av den här klassen med metoden [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) , som visas i följande exempel:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Skicka händelser till en händelsehubben

Du skickar händelser till en Event Hub genom att skapa en [EventHubClient][] -instans och skicka den asynkront via [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) -metoden. Den här metoden tar en enskild [EventData][] -instans parameter och skickar asynkront den till en Event Hub.

## <a name="event-serialization"></a>Händelseserialisering

Klassen [EventData][] har [två överbelastade konstruktorer](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) som tar en rad olika parametrar, byte eller en byte mat ris som representerar händelse data nytto lasten. När du använder JSON med [EventData][] kan du använda **Encoding.UTF8.GetBytes()** för att hämta bytematrisen för en JSON-kodad sträng. Till exempel:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Partitionsnyckel

> [!NOTE]
> Om du inte är bekant med partitioner kan du läsa [den här artikeln](event-hubs-features.md#partitions). 

När du skickar händelse data kan du ange ett värde som är hashat för att skapa en partitions tilldelning. Du anger partitionen med egenskapen [PartitionSender. PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) . Beslutet att använda partitioner innebär dock ett val mellan tillgänglighet och konsekvens. 

### <a name="availability-considerations"></a>Överväganden för tillgänglighet

Det är valfritt att använda en partitionsnyckel, och du bör överväga att noga överväga om du vill använda en enda nyckel. Om du inte anger en partitionsnyckel när du publicerar en händelse, används en tilldelning enligt resursallokeringsmodellen. I många fall är det en bra idé att använda en partitionsnyckel om händelse ordningen är viktig. När du använder en partitionsnyckel kräver dessa partitioner tillgänglighet på en enda nod och avbrott kan ske över tid. till exempel när Compute-noder startas om och korrigeras. Om du anger ett partitions-ID och den partitionen blir otillgänglig av någon anledning, kommer ett försök att komma åt data i den partitionen att Miss lyckas. Om hög tillgänglighet är viktigast, ska du inte ange någon partitionsnyckel. i så fall skickas händelser till partitioner med hjälp av den resursallokering-modell som beskrivs ovan. I det här scenariot gör du ett explicit val mellan tillgänglighet (inget partitions-ID) och konsekvens (fäster händelser till ett partitions-ID).

Ett annat övervägande är att hantera fördröjningar vid bearbetning av händelser. I vissa fall kan det vara bättre att släppa data och försöka igen än att försöka fortsätta med bearbetningen, vilket kan leda till ytterligare framtida bearbetnings fördröjningar. Till exempel är det bättre att vänta på att slutföra aktuella data, men i ett Live-chatt-eller VOIP-scenario kan du snabbt få data, även om de inte är slutförda.

Med hänsyn till dessa tillgänglighets överväganden kan du välja någon av följande strategier för fel hantering:

- Stoppa (sluta läsa från Event Hubs tills saker har åtgärd ATS)
- Släpp (meddelanden är inte viktiga, släpp dem)
- Försök igen (försök igen när du ser anpassningen)

Mer information och en diskussion om kompromisser mellan tillgänglighet och konsekvens finns [i tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Åtgärder för att skicka batchhändelser

Att skicka händelser i batchar kan öka data flödet. Du kan använda [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) -API: et för att skapa en batch som data objekt kan läggas till senare för ett [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) -anrop.

En enskild batch får inte överskrida gränsen på 1 MB för en händelse. Dessutom använder varje meddelande i batchen samma utgivaridentitet. Det är avsändarens ansvar att se till att batchen inte överskrider den maximala händelsestorleken. Om den gör det genereras ett **Skicka**-felmeddelande för klienten. Du kan använda hjälp metoden [EventHubClient. CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) för att se till att batchen inte överskrider 1 MB. Du får en tom [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) från [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) -API: et och använder sedan [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) för att lägga till händelser för att skapa batchen. 

## <a name="send-asynchronously-and-send-at-scale"></a>Skicka asynkront och skicka i skala

Du skickar händelser till en Event Hub asynkront. Om du skickar asynkront ökar den hastighet som en klient kan skicka händelser till. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) returnerar ett [aktivitets](/dotnet/api/system.threading.tasks.task?view=netcore-3.1) objekt. Du kan använda [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) -klassen på klienten för att kontrol lera alternativen för klient återförsök.

## <a name="event-consumers"></a>Händelsekonsumenter
Klassen [EventProcessorHost][] bearbetar data från händelsehubbar. Du bör använda den här implementeringen när du skapar händelseläsare på .NET-plattformen. [EventProcessorHost][] ger en trådsäker, flerprocessig, säker körningsmiljö för implementeringar av händelseprocessorer som också ger hantering av kontrollpunkter och hantering av partitionsleasing.

Om du vill använda klassen [EventProcessorHost][] kan du implementera [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Det här gränssnittet innehåller fyra metoder:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Starta händelse bearbetningen genom att instansiera [EventProcessorHost][], med lämpliga parametrar för händelsehubben. Till exempel:

> [!NOTE]
> EventProcessorHost och dess relaterade klasser finns i paketet **Microsoft. Azure. EventHubs. processor** . Lägg till paketet i Visual Studio-projektet genom att följa anvisningarna i [den här artikeln](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) eller genom att utfärda följande kommando i fönstret [Package Manager-konsol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) : `Install-Package Microsoft.Azure.EventHubs.Processor` .

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Anropa sedan [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) för att registrera [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementeringen med körningen:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Vid det här tillfället försöker värden skaffa ett lån på varje partition i händelsehubben med hjälp av en "girig"-algoritm. Dessa lån är sist under ett angivet tidsintervall och måste sedan förnyas. Allteftersom nya noder, i det här fallet arbetarinstanser, kommer ut online, placerar den ut leasingreservationer och med tiden skiftar arbetsbelastningen mellan noderna då varje nod försöker skaffa fler leasingar.

![Värd för händelsebearbetning](./media/event-hubs-programming-guide/IC759863.png)

Med tiden etableras ett jämviktsläge. Den här dynamiska funktionen gör att processorbaserad autoskalning kan tillämpas på konsumenter för både uppskalning och nedskalning. Eftersom Event Hubs inte har ett direkt begrepp för antal meddelanden, är den genomsnittliga processor användningen ofta den bästa mekanismen för att mäta Server dels-eller konsument skala. Om utgivare börjar publicera fler händelser än konsumenterna kan bearbeta kan processorökningen för konsumenterna användas för att autoskala efter antalet arbetsinstanser.

Klassen [EventProcessorHost][] implementerar också en mekanism för Azure storage-baserade kontrollpunkter. Den här mekanismen lagrar offseten på en per partition-basis, så att varje konsument kan fastställa vilken den senaste kontrollpunkten från den föregående konsumenten var. Eftersom partitioner övergår mellan noder via leasingar är det här den synkroniseringsmekanism som gör det lättare att skifta belastningar.

## <a name="publisher-revocation"></a>Återkallande av utgivare

Utöver de avancerade körnings funktionerna i händelse bearbetnings värden, aktiverar Event Hubs tjänsten [återkallning av utgivare](/rest/api/eventhub/revoke-publisher) för att blockera vissa utgivare från att skicka händelser till en Event Hub. Dessa funktioner är användbara om en utgivar-token har komprometterats eller om en program uppdatering gör att de beter sig felaktigt. I sådana situationer kan utgivarens identitet, vilken är en del av deras SAS-token, blockeras från att publicera händelser.

> [!NOTE]
> För närvarande stöds endast REST API som stöder den här funktionen ([återkallning av utgivare](/rest/api/eventhub/revoke-publisher)).


## <a name="next-steps"></a>Nästa steg

Mer information om scenarier i händelsehubbar finns i följande länkar:

* [Event Hubs API-översikt](./event-hubs-samples.md)
* [Vad är Event Hubs](./event-hubs-about.md)
* [Tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md)
* [Händelsebearbetningsvärd API-referens](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
