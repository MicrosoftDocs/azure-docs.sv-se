---
title: Ta emot händelser med hjälp av händelse processor värden – Azure Event Hubs | Microsoft Docs
description: I den här artikeln beskrivs händelse bearbetnings värden i Azure Event Hubs, vilket fören klar hanteringen av kontroll punkter, leasing och läsning av händelser Jon parallellt.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: de5d8f0f8bf9f64a473b18a50434cac83e8e38c3
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622070"
---
# <a name="event-processor-host"></a>Värd för händelsebearbetning
> [!NOTE]
> Den här artikeln gäller för den gamla versionen av Azure Event Hubs SDK. För aktuell version av SDK, se [belastningsutjämna partition belastning över flera instanser av programmet](event-processor-balance-partition-load.md). Information om hur du migrerar din kod till den nya versionen av SDK finns i dessa guider för migrering. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java-skript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)

Azure Event Hubs är en kraftfull tjänst för telemetri-inmatning som kan användas för att strömma miljon tals händelser till låg kostnad. I den här artikeln beskrivs hur du använder insamlade händelser med *händelse bearbetnings värden* (EPH). en intelligent konsument agent som fören klar hanteringen av kontroll punkter, leasing och parallella händelse läsare.  

Den nyckel som ska skalas för Event Hubs är en uppfattning om partitionerade konsumenter. Med hjälp av mönstret för [konkurrerande konsumenter](/previous-versions/msp-n-p/dn568101(v=pandp.10)) möjliggör det partitionerade konsument mönstret hög skalning genom att ta bort Flask halsen och under lätta slut punkt till slut punkt.

## <a name="home-security-scenario"></a>Start säkerhets scenario

Ett exempel scenario är att tänka på ett hem säkerhets företag som övervakar 100 000 hus. Varje minut hämtar den data från olika sensorer, till exempel rörelse detektor, dörr-/fönster sensor, glas brytnings detektor osv. som installeras i varje hem. Företaget tillhandahåller en webbplats för invånare för att övervaka aktivitetens start i nära real tid.

Varje sensor skickar data till en händelsehubben. Händelsehubben är konfigurerad med 16 partitioner. På så sätt behöver du en mekanism som kan läsa de här händelserna, konsolidera dem (filter, mängd osv.) och dumpa mängden till en lagrings-blob som sedan projiceras till en användarvänlig webb sida.

## <a name="write-the-consumer-application"></a>Skriv konsument programmet

När du designar konsumenten i en distribuerad miljö måste scenariot hantera följande krav:

1. **Skala:** Skapa flera konsumenter, där varje konsument tar över ägande rätten till att läsa från några få Event Hubs partitioner.
2. **Belastnings utjämning:** Öka eller minska konsumenterna dynamiskt. Till exempel när en ny sensor typ (till exempel en kolmonoxids detektor) läggs till i varje hem, ökar antalet händelser. I så fall ökar operatören (en mänsklig) antalet konsument instanser. Sedan kan poolen med konsumenter balansera om antalet partitioner som de äger, för att dela belastningen med de nyligen tillagda kunderna.
3. **Sömlös återgång vid haverier:** Om en konsument (**konsument a**) Miss lyckas (till exempel den virtuella datorn som är värd för konsumenten plötsligt kraschar), måste andra konsumenter kunna hämta de partitioner som ägs av **konsument a** och fortsätta. Fortsättnings punkten, som kallas för en *kontroll punkt* eller *förskjutning*, bör också vara i den exakta punkt där **konsumenten** misslyckades, eller något tidigare.
4. **Använda händelser:** De tidigare tre punkterna hanterar konsumentens hantering, men det måste finnas en kod för att använda händelserna och göra något användbart med den. Du kan till exempel aggregera den och ladda upp den till Blob Storage.

I stället för att skapa en egen lösning för detta ger Event Hubs den här funktionen genom [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -gränssnittet och [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -klassen.

## <a name="ieventprocessor-interface"></a>IEventProcessor-gränssnitt

Först implementerar program som använder  [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -gränssnittet, som har fyra metoder: [openAsync, CloseAsync, ProcessErrorAsync och ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor#methods). Det här gränssnittet innehåller den faktiska koden för att förbruka de händelser som Event Hubs skickar. Följande kod visar en enkel implementering:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Sedan instansierar du en [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -instans. Beroende på överbelastningen används följande parametrar när du skapar [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -instansen i konstruktorn:

- **värdnamn:** namnet på varje konsument instans. Varje instans av **EventProcessorHost** måste ha ett unikt värde för den här variabeln i en konsument grupp, så du behöver inte ange det här värdet.
- **eventHubPath:** Namnet på händelsehubben.
- **consumerGroupName:** Event Hubs använder **$default** som namnet på standard konsument gruppen, men det är en bra idé att skapa en konsument grupp för din speciella aspekt av bearbetningen.
- **eventHubConnectionString:** Anslutnings strängen till händelsehubben, som kan hämtas från Azure Portal. Den här anslutnings strängen ska ha **avlyssnings** behörigheter för händelsehubben.
- **storageConnectionString:** Det lagrings konto som används för intern resurs hantering.

Slutligen registrerar konsumenterna [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -instansen med Event Hubs tjänsten. Att registrera en händelse processor klass med en instans av EventProcessorHost startar händelse bearbetning. Registrering instruerar Event Hubss tjänsten att förväntar sig att konsument appen förbrukar händelser från några av dess partitioner, och för att anropa implementerings koden för [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) när den skickar händelser till förbrukning. 

> [!NOTE]
> ConsumerGroupName är Skift läges känsligt.  Ändringar i consumerGroupName kan leda till att alla partitioner läses in från strömmens start.

### <a name="example"></a>Exempel

Anta till exempel att det finns fem virtuella datorer (VM) som är dedikerade för att konsumera händelser och ett enkelt konsol program i varje virtuell dator, vilket gör att den faktiska förbrukningen fungerar. Varje konsol program skapar sedan en [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -instans och registrerar den med tjänsten Event Hubs.

I det här exempel scenariot antar vi att 16 partitioner har allokerats till 5 **EventProcessorHost** -instanserna. Vissa **EventProcessorHost** -instanser kan äga fler partitioner än andra. För varje partition som en **EventProcessorHost** -instans äger skapas en instans av `SimpleEventProcessor` klassen. Det finns därför 16 instanser av `SimpleEventProcessor` övergripande, med en tilldelad till varje partition.

I följande lista sammanfattas följande exempel:

- 16 Event Hubs partitioner.
- 5 virtuella datorer, 1 konsument app (till exempel Consumer.exe) i varje virtuell dator.
- 5 EPH instanser registrerade, 1 i varje virtuell dator med Consumer.exe.
- 16 `SimpleEventProcessor` objekt som skapats av 5 EPH-instanser.
- 1 Consumer.exe app kan innehålla 4 `SimpleEventProcessor` objekt, eftersom 1 EPH-instansen kan äga fyra partitioner.

## <a name="partition-ownership-tracking"></a>Spåra ägarskap för partition

Ägarskapet av en partition till en EPH-instans (eller en konsument) spåras via det Azure Storage konto som anges för spårning. Du kan visualisera spårningen som en enkel tabell enligt följande. Du kan se den faktiska implementeringen genom att undersöka blobarna under det angivna lagrings kontot:

| **Konsumentgruppens namn** | **Partitions-ID** | **Värdnamn (ägare)** | **Förvärvad tid för lån (eller ägarskap)** | **Förskjutning i partition (kontroll punkt)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Konsument \_ VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Konsument \_ VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Konsument \_ VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Konsument \_ VM3 | 2018-04-15T01:22:56 | 976 |

Här skaffar varje värd ägarskapet för en partition under en viss varaktighet (låne tiden). Om en värd Miss lyckas (den virtuella datorn stängs av) upphör lånet att gälla. Andra värdar försöker bli ägare till partitionen och en av värdarna lyckas. Den här processen återställer lånet på partitionen med en ny ägare. På så sätt kan bara en enda läsare i taget läsa från en viss partition i en konsument grupp.

## <a name="receive-messages"></a>Ta emot meddelanden

Varje anrop till [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) levererar en samling händelser. Det är ditt ansvar att hantera dessa händelser. Om du vill se till att processor värden bearbetar varje meddelande minst en gång måste du skriva ett eget försök att använda kod. Men var försiktig med giftade meddelanden.

Vi rekommenderar att du gör saker relativt snabba, det vill säga så lite bearbetning som möjligt. Använd i stället konsument grupper. Om du behöver skriva till lagring och göra en del routning, är det bättre att använda två konsument grupper och ha två [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementeringar som körs separat.

Vid en viss tidpunkt under bearbetningen kanske du vill hålla reda på vad du har läst och slutfört. Det är viktigt att hålla koll på om du måste starta om läsningen, så du kommer inte tillbaka till början av data strömmen. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) fören klar den här spårningen med hjälp av *kontroll punkter*. En kontroll punkt är en plats, eller förskjutning, för en viss partition, i en viss konsument grupp, där du är nöjd med att du har bearbetat meddelandena. Att markera en kontroll punkt i **EventProcessorHost** utförs genom att anropa metoden [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) på [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) -objektet. Den här åtgärden utförs inom [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) -metoden men kan också göras i [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Kontrollpunkter

Metoden [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) har två överbelastningar: det första, utan parametrar, kontroll punkter till den högsta händelse förskjutningen i den samling som returneras av [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Den här förskjutningen är ett "högt vatten"-märke. Det förutsätter att du har bearbetat alla senaste händelser när du anropar det. Om du använder den här metoden på det här sättet bör du vara medveten om att du förväntas anropa den när din andra händelse bearbetnings kod har returnerats. Den andra överbelastningen gör att du kan ange en [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) -instans för kontroll punkten. Med den här metoden kan du använda en annan typ av vattenstämpel för kontroll punkten. Med den här vattenstämpeln kan du implementera ett "lågt vatten"-märke: den lägsta sekvenserade händelsen som du är säker har bearbetats. Den här överbelastningen tillhandahålls för att möjliggöra flexibilitet i hantering av offset.

När kontroll punkten utförs skrivs en JSON-fil med partitionerad information (särskilt förskjutningen) till det lagrings konto som anges i konstruktorn för att [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Den här filen uppdateras kontinuerligt. Det är viktigt att tänka på kontroll punkter i kontexten – det skulle vara klokt att ta kontroll punkter varje meddelande. Det lagrings konto som används för kontroll punkter skulle förmodligen inte hantera den här belastningen, men mer betydelsefulla kontroll punkter i varje enskild händelse är en indikation på ett köade meddelande mönster som en Service Bus kö kan vara ett bättre alternativ än en Event Hub. Idén bakom Event Hubs är att du får "minst en gång"-leverans i stor skala. Genom att göra dina underordnade system idempotenta, är det enkelt att återställa från fel eller starta om, vilket innebär att samma händelser tas emot flera gånger.

## <a name="thread-safety-and-processor-instances"></a>Tråd säkerhet och processor instanser

Som standard är [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) tråd säker och fungerar på ett synkront sätt med avseende på instansen av [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). När händelser kommer till en partition, anropas [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) på **IEventProcessor** -instansen för den partitionen och blockerar ytterligare anrop till **ProcessEventsAsync** för partitionen. Efterföljande meddelanden och anrop till **ProcessEventsAsync** i kö i bakgrunden när meddelande pumpen fortsätter att köras i bakgrunden på andra trådar. Den här tråd säkerheten tar bort behovet av tråd säkra samlingar och ökar dramatiskt prestanda avsevärt.

## <a name="shut-down-gracefully"></a>Stäng av på ett smidigt sätt

Slutligen gör [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) en ren avstängning av alla partitionsuppsättningar och bör alltid anropas när en instans av [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)stängs ned. Om du inte gör det kan det orsaka fördröjningar när du startar andra instanser av **EventProcessorHost** på grund av lånets förfallo datum och epok-konflikter. Den Epoka hanteringen beskrivs i detalj i avsnittet [epoker](#epoch) i artikeln. 

## <a name="lease-management"></a>Hantering av lån
Att registrera en händelse processor klass med en instans av EventProcessorHost startar händelse bearbetning. Värd instansen erhåller lån på vissa partitioner i Händelsehubben, eventuellt från andra värd instanser, på ett sätt som konvergerar till en jämn fördelning av partitioner över alla värd instanser. För varje lånad partition skapar värd instansen en instans av den angivna händelse processor klassen och tar sedan emot händelser från den partitionen och skickar dem till händelse processor instansen. I takt med att fler instanser läggs till och fler lån fångas, kan EventProcessorHost slutligen utjämna belastningen bland alla konsumenter.

Som förklarat tidigare fören klar spårnings tabellen den automatiska skalnings typen för [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). När en instans av **EventProcessorHost** startar, erhåller den så många lån som möjligt och börjar läsa händelser. När lånet upphör snart försöker **EventProcessorHost** förnya dem genom att placera en reservation. Om lånet är tillgängligt för förnyelse fortsätter processorn att läsa, men om den inte är det stängs läsaren och [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) anropas. **CloseAsync** är en bra tid att utföra eventuell slut rensning för den partitionen.

**EventProcessorHost** innehåller en [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) -egenskap. Den här egenskapen aktiverar kontroll över låne hantering. Ange de här alternativen innan du registrerar [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementeringen.

## <a name="control-event-processor-host-options"></a>Styr värd alternativ för händelse bearbetning

Dessutom tar en överlagring av [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) ett [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) -objekt som en parameter. Använd den här parametern för att styra beteendet för [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) . [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definierar fyra egenskaper och en händelse:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): den maximala storleken på den samling som du vill ta emot i ett anrop till [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Den här storleken är inte minimal, bara den maximala storleken. Om det finns färre meddelanden att ta emot körs **ProcessEventsAsync** med så många som var tillgängliga.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): ett värde som används av den underliggande AMQP-kanalen för att fastställa den övre gränsen för hur många meddelanden klienten ska ta emot. Värdet måste vara större än eller lika med [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): om den här parametern är **True** anropas [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) när det underliggande anropet att ta emot händelser på en tids gräns för partitionen. Den här metoden är användbar för att ta tidsbaserade åtgärder under perioder av inaktivitet på partitionen.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): aktiverar en funktions pekare eller ett lambda-uttryck som ska anges, vilket anropas för att ange den inledande förskjutningen när en läsare börjar läsa en partition. Utan att ange den här förskjutningen startar läsaren vid den äldsta händelsen, om inte en JSON-fil med en förskjutning redan har sparats i det lagrings konto som har angetts för [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -konstruktorn. Den här metoden är användbar när du vill ändra beteendet för läsarens start. När den här metoden anropas innehåller parametern Object det partitions-ID som läsaren startas för.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): gör att du kan få aviseringar om eventuella underliggande undantag som inträffar i [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Om saker inte fungerar som du förväntar dig, är den här händelsen en bra plats att börja titta på.

## <a name="epoch"></a>Epok

Så här fungerar den mottagna epoken:

### <a name="with-epoch"></a>Med epok
Epok är en unik identifierare (värde värde) som tjänsten använder för att framtvinga ägande mellan partitioner och lån. Du skapar en epok-baserad mottagare med metoden [CreateEpochReceiver](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver) . Den här metoden skapar en epok-baserad mottagare. Mottagaren skapas för en specifik Event Hub-partition från den angivna konsument gruppen.

Funktionen epok ger användarna möjlighet att se till att det bara finns en mottagare på en konsument grupp vid en viss tidpunkt, med följande regler:

- Om det inte finns någon befintlig mottagare i en konsument grupp, kan användaren skapa en mottagare med ett värde för värde.
- Om det finns en mottagare med ett värde av värde: E1 och en ny mottagare skapas med ett värde för epoker, där E1 <= E2, kommer mottagaren med E1 att kopplas från automatiskt, mottagare med E2 skapas korrekt.
- Om det finns en mottagare med värdet ett värde av värde E1 och en ny mottagare skapas med ett värde på epoken E2 där E1 > E2, och sedan skapa E2 med fel: det finns redan en mottagare med epok E1.

### <a name="no-epoch"></a>Ingen epok
Du skapar en icke-epok-baserad mottagare med hjälp av metoden [CreateReceiver](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver) . 

Det finns vissa scenarier i Stream-bearbetningen där användare vill skapa flera mottagare i en enda konsument grupp. För att stödja sådana scenarier har vi möjlighet att skapa en mottagare utan epok och i det här fallet tillåter vi upp till 5 samtidiga mottagare på konsument gruppen.

### <a name="mixed-mode"></a>Blandat läge
Vi rekommenderar inte program användning där du skapar en mottagare med epok och sedan byter till No-epok eller vice versa i samma konsument grupp. Men när det här problemet uppstår hanterar tjänsten den med följande regler:

- Om det redan finns en mottagare med den nya epoken E1 och du tar emot händelser och en ny mottagare skapas utan epok, kommer skapandet av den nya mottagaren inte att fungera. Värdefulla mottagare har alltid företräde i systemet.
- Om en mottagare redan har skapats med värdet epok E1 och kopplats från, och en ny mottagare skapas utan epok på en ny MessagingFactory, kommer skapandet av den nya mottagaren att lyckas. Det finns ett villkor för att vårt system ska identifiera "mottagar från koppling" efter ~ 10 minuter.
- Om det finns en eller flera mottagare som har skapats utan epoker och en ny mottagare skapas med epok E1, kommer alla gamla mottagare att kopplas från.


> [!NOTE]
> Vi rekommenderar att du använder olika konsument grupper för program som använder epoker och för de som inte använder epoker för att undvika fel. 


## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med händelsens processor värd kan du läsa mer om Event Hubs i följande artiklar:

- Kom igång med händelsehubbar
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Programmerings guide för Event Hubs](event-hubs-programming-guide.md)
* [Tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Event Hubs exempel på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)
