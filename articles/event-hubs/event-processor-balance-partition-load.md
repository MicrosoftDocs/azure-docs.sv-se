---
title: Balansera partition belastning över flera instanser – Azure Event Hubs | Microsoft Docs
description: Beskriver hur du balanserar partition belastningen över flera instanser av ditt program med hjälp av en händelse processor och Azure Event Hubs SDK.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: af307058d0eda6b96c0811bccc245c09e2bdd27d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025052"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Utjämna belastningen på partitionen över flera instanser av programmet
Om du vill skala ditt händelse bearbetnings program kan du köra flera instanser av programmet och utjämna belastningen sinsemellan. I de äldre versionerna har [EventProcessorHost](event-hubs-event-processor-host.md) gett dig möjlighet att balansera belastningen mellan flera instanser av ditt program och kontroll punkts händelser när de tar emot. I de nyare versionerna (5,0 och senare), **EventProcessorClient** (.net och Java) eller **EventHubConsumerClient** (python och Java Script) kan du göra samma sak. Utvecklings modellen blir enklare med hjälp av händelser. Du prenumererar på de händelser som du är intresse rad av genom att registrera en händelse hanterare. Om du använder den gamla versionen av klient biblioteket kan du läsa följande guider för migrering: [.net](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/migration-guide.md), [python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/servicebus/azure-servicebus/migration_guide.md)och [Java Script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/migrationguide.md).

I den här artikeln beskrivs ett exempel scenario för att använda flera instanser för att läsa händelser från en händelsehubben och ger dig information om funktioner i händelse processor klienten, som gör att du kan ta emot händelser från flera partitioner samtidigt och belastningsutjämna med andra konsumenter som använder samma Event Hub och konsument grupp.

> [!NOTE]
> Den nyckel som ska skalas för Event Hubs är en uppfattning om partitionerade konsumenter. Med hjälp av mönstret för [konkurrerande konsumenter](/previous-versions/msp-n-p/dn568101(v=pandp.10)) möjliggör det partitionerade konsument mönstret hög skalning genom att ta bort Flask halsen och under lätta slut punkt till slut punkt.

## <a name="example-scenario"></a>Exempelscenario

Ett exempel scenario är att tänka på ett hem säkerhets företag som övervakar 100 000 hus. Varje minut hämtar den data från olika sensorer, till exempel en rörelse detektor, dörr-/fönster öppen sensor, glas brytnings detektor och så vidare som installeras i varje hem. Företaget tillhandahåller en webbplats för invånare för att övervaka aktivitetens start i nära real tid.

Varje sensor skickar data till en händelsehubben. Händelsehubben är konfigurerad med 16 partitioner. På så sätt behöver du en mekanism som kan läsa de här händelserna, konsolidera dem (filtrera, sammanställa och så vidare) och dumpa mängden till en lagrings-BLOB, som sedan projiceras till en användarvänlig webb sida.

## <a name="write-the-consumer-application"></a>Skriv konsument programmet

När du designar konsumenten i en distribuerad miljö måste scenariot hantera följande krav:

1. **Skala:** Skapa flera konsumenter, där varje konsument tar över ägande rätten till att läsa från några få Event Hubs partitioner.
2. **Belastnings utjämning:** Öka eller minska konsumenterna dynamiskt. Till exempel när en ny sensor typ (till exempel en kolmonoxids detektor) läggs till i varje hem, ökar antalet händelser. I så fall ökar operatören (en mänsklig) antalet konsument instanser. Sedan kan poolen med konsumenter balansera om antalet partitioner som de äger, för att dela belastningen med de nyligen tillagda kunderna.
3. **Sömlös återgång vid haverier:** Om en konsument (**konsument a**) Miss lyckas (till exempel den virtuella datorn som är värd för konsumenten plötsligt kraschar), kan andra konsumenter hämta de partitioner som ägs av **konsument a** och fortsätta. Fortsättnings punkten, som kallas för en *kontroll punkt* eller *förskjutning*, bör också vara i den exakta punkt där **konsumenten** misslyckades, eller något tidigare.
4. **Använda händelser:** De tidigare tre punkterna hanterar konsumentens hantering, men det måste finnas en kod för att använda händelserna och göra något användbart med den. Du kan till exempel aggregera den och ladda upp den till Blob Storage.

## <a name="event-processor-or-consumer-client"></a>Händelse processor eller konsument klient

Du behöver inte bygga din egen lösning för att uppfylla dessa krav. Azure Event Hubs SDK: er tillhandahåller den här funktionen. I .NET eller Java SDK: er använder du en Event processor-klient (EventProcessorClient) och i python-och JavaScript-SDK: er använder du EventHubConsumerClient. I den tidigare versionen av SDK var det händelse bearbetnings värd (EventProcessorHost) som har stöd för dessa funktioner.

För de flesta produktions scenarier rekommenderar vi att du använder händelse processor klienten för att läsa och bearbeta händelser. Processor klienten är avsedd att tillhandahålla en robust upplevelse för bearbetning av händelser i alla partitioner i en Event Hub i en utförd och feltolerant metod, samtidigt som det ger ett sätt att ange en kontroll punkt för förloppet. Event processor-klienter kan samar beta inom kontexten för en konsument grupp för en specifik händelsehubben. Klienterna hanterar automatiskt distribution och balansering av arbete när instanser blir tillgängliga eller otillgängliga för gruppen.

## <a name="partition-ownership-tracking"></a>Spåra ägarskap för partition

En händelse processor instans äger vanligt vis och bearbetar händelser från en eller flera partitioner. Ägande rätt till partitioner distribueras jämnt mellan alla aktiva händelse processor instanser som är kopplade till en Event Hub-och konsument grupp kombination. 

Varje händelse processor får en unik identifierare och hävdar ägande rätt för partitioner genom att lägga till eller uppdatera en post i ett kontroll punkts arkiv. Alla Event processor instanser kommunicerar med det här arkivet regelbundet för att uppdatera sitt egna bearbetnings tillstånd samt för att lära dig om andra aktiva instanser. Dessa data används sedan för att utjämna belastningen mellan de aktiva processorerna. Nya instanser kan anslutas till den behandlande poolen för att skala upp. När instanser går ned, antingen på grund av problem eller för att skala ned, överförs partitionens ägarskap på ett smidigt sätt till andra aktiva processorer.

Partitionera ägarskaps poster i kontroll punkts arkivet Håll koll på Event Hubs namnrymd, namn på händelsehubben, konsument grupp, händelse processor identifierare (kallas även ägare), partitions-ID och senaste ändrings tid.



| Event Hubs-namnområde               | Namn på händelsehubb | **Konsument grupp** | Ägare                                | Partitions-ID | Tid för senaste ändring  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Varje händelse processor instans får ägarskapet för en partition och börjar bearbeta partitionen från den senaste kända [kontroll punkten](# Checkpointing). Om en processor slutar fungera (VM stängs av), identifierar andra instanser den genom att titta på den senast ändrade tiden. Andra instanser försöker bli ägare till de partitioner som tidigare ägdes av den inaktiva instansen och kontroll punkts arkivet garanterar att endast en av instanserna lyckas vid anspråk på ägarskapet för en partition. Därför finns det bara en processor som tar emot händelser från en partition vid en viss tidpunkt.

## <a name="receive-messages"></a>Ta emot meddelanden

När du skapar en händelse processor anger du de funktioner som ska bearbeta händelser och fel. Varje anrop till funktionen som bearbetar händelser levererar en enskild händelse från en viss partition. Det är ditt ansvar att hantera den här händelsen. Om du vill vara säker på att konsumenten bearbetar varje meddelande minst en gång måste du skriva din egen kod med logiken för omprövning. Men var försiktig med giftade meddelanden.

Vi rekommenderar att du gör saker relativt snabba. Det vill säga så lite bearbetning som möjligt. Om du behöver skriva till lagring och göra en del routning, är det bättre att använda två konsument grupper och ha två händelse processorer.

## <a name="checkpointing"></a>Kontrollpunkter

*Kontroll punkter* är en process där en händelse processor markerar eller genomför positionen för den senaste bearbetade händelsen i en partition. Att markera en kontroll punkt utförs vanligt vis i den funktion som bearbetar händelserna och inträffar per partition i en konsument grupp. 

Om en händelse processor kopplar från en partition kan en annan instans återuppta bearbetningen av partitionen vid kontroll punkten som tidigare har allokerats av den senaste processorn i den aktuella partitionen i den konsument gruppen. När processorn ansluter skickar den förskjutningen till händelsehubben för att ange den plats där du vill börja läsa. På så sätt kan du använda kontroll punkter för att båda Markera händelser som "slutförda" i underordnade program och för att ge återhämtning när en händelse processor slutar fungera. Det går att återgå till äldre data genom att ange en lägre offset från den här kontroll punkten. 

När kontroll punkten utförs för att markera en händelse som bearbetad läggs en post i kontroll punkts arkivet till eller uppdateras med händelsens förskjutning och sekvensnummer. Användarna bör bestämma frekvensen för att uppdatera kontroll punkten. Uppdatering efter att varje lyckad händelse har bearbetats kan ha prestanda-och kostnads konsekvenser när den utlöser en Skriv åtgärd till den underliggande kontroll punkts arkivet. Dessutom är kontroll punkter i varje enskild händelse indikationer på ett köat meddelande mönster som en Service Bus kö kan vara ett bättre alternativ än en Event Hub. Idén bakom Event Hubs är att du får "minst en gång"-leverans i stor skala. Genom att göra dina idempotenta i det underordnade systemet, är det enkelt att återställa från fel eller starta om, vilket innebär att samma händelser tas emot flera gånger.

> [!NOTE]
> Om du använder Azure Blob Storage som kontroll punkts Arkiv i en miljö som har stöd för en annan version av Storage BLOB SDK än vad som normalt är tillgängligt på Azure, måste du använda kod för att ändra Storage Service API-versionen till den version som stöds av den aktuella miljön. Om du till exempel kör [Event Hubs på en Azure Stack hubb version 2002](/azure-stack/user/event-hubs-overview)är den högsta tillgängliga versionen för lagrings tjänsten version 2017-11-09. I så fall måste du använda kod för att rikta Storage Service API-versionen till 2017-11-09. Ett exempel på hur du riktar in en speciell Storage API-version finns i följande exempel på GitHub: 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [Java Script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) eller  [typescript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>Tråd säkerhet och processor instanser

Som standard anropas den funktion som bearbetar händelserna i turordning för en viss partition. Efterföljande händelser och anrop till den här funktionen från samma partitionsuppsättning i kön i bakgrunden när händelse pumpen fortsätter att köras i bakgrunden på andra trådar. Händelser från olika partitioner kan bearbetas samtidigt och alla delade tillstånd som nås mellan partitioner måste synkroniseras.

## <a name="next-steps"></a>Nästa steg
Se följande snabb starter:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
