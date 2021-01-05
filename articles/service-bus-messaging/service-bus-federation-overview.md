---
title: Meddelanderoutning och över-region Federation-Azure Service Bus | Microsoft Docs
description: Den här artikeln innehåller en översikt över replikering av händelser och över flera regioner med Azure Service Bus.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d5153820feab8b7901356838ec435d992aa82116
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803280"
---
# <a name="message-replication-and-cross-region-federation"></a>Meddelandereplikering och federation mellan regioner

I namn områden, Azure Service Bus har stöd för att [skapa topologier för kedjade köer och ämnes prenumerationer med hjälp av vidarebefordran](service-bus-auto-forwarding.md) för att möjliggöra implementering av olika vägvals mönster. Du kan till exempel ge partner med dedikerade köer som de har behörighet att skicka eller ta emot och som tillfälligt kan inaktive ras vid behov och flexibelt ansluta dem till andra entiteter som är privata för programmet. Du kan också skapa komplexa routningsmetoder för flera steg, eller så kan du skapa köer för post lådor, vilket tömmer kön som prenumerationer på ämnen och tillåter mer lagrings kapacitet per prenumerant. 

Många avancerade lösningar kräver också att meddelanden replikeras mellan namn områdes gränser för att kunna implementera dessa och andra mönster. Meddelanden kan behöva flöda mellan namn områden som är kopplade till flera, olika program innehavare eller mellan flera olika Azure-regioner. 

Lösningen kommer att underhålla flera Service Bus namn områden i olika regioner och replikera meddelanden mellan köer och ämnen och/eller att du kommer att utbyta meddelanden med källor och mål som [Azure Event Hubs](../event-hubs/event-hubs-about.md), [azure IoT Hub](../iot-fundamentals/iot-introduction.md)eller [Apache Kafka](https://kafka.apache.org). 

De här scenarierna är fokus i den här artikeln. 

## <a name="federation-patterns"></a>Federations mönster

Det finns många tänkbara orsaker till varför du kanske vill flytta meddelanden mellan Service Bus entiteter som köer eller ämnen, eller mellan Service Bus och andra källor och mål. 

Jämfört med den liknande uppsättningen mönster för [Event Hubs](../service-bus-messaging/service-bus-federation-overview.md)är federationen för köade entiteter mer komplexa eftersom meddelande köerna lovar sina konsumenters exklusiva ägarskap över ett enda meddelande, förväntas bevara införsel order i meddelande leverans och för Service Broker för att samordna rättvis fördelning av meddelanden mellan [konkurrerande konsumenter](/azure/architecture/patterns/competing-consumers). 

Det finns praktiska hinder, inklusive begränsningarna i [Cap-satsen](https://en.wikipedia.org/wiki/CAP_theorem), som gör det svårt att tillhandahålla en enhetlig vy över en kö som är tillgänglig samtidigt i flera regioner och som gör det möjligt för regionalt distribuerade, [konkurrerande konsumenter](/azure/architecture/patterns/competing-consumers) att ta ensam ägande av meddelanden. En sådan geo-distribuerad kö kräver helt konsekvent replikering, inte bara för meddelanden, utan även av leverans status för varje meddelande innan meddelanden kan göras tillgängliga för konsumenter. Målet med fullständig konsekvens för en hypotetisk, regional distribuerad kö är direkt i konflikt med det viktiga mål som nästan alla Azure Service Bus kunder har när de överväger Federations scenarier: maximal tillgänglighet och tillförlitlighet för deras lösningar. 

De mönster som presenteras här fokuserar därför på tillgänglighet och tillförlitlighet, samtidigt som vi strävar efter att undvika både informations förlust och dubbel hantering av meddelanden. 

### <a name="resiliency-against-regional-availability-events"></a>Återhämtning mot regionala tillgänglighets händelser 

Även om maximal tillgänglighet och pålitlighet är de viktigaste drifts prioriteterna för Service Bus, finns det dock många sätt på vilka en producent eller konsument kan förhindras från att kommunicera med den tilldelade primära Service Bus på grund av nätverks-eller namn matchnings problem, eller där Service Bus entiteten kanske inte svarar eller returnerar fel. Den angivna meddelande processorn kan också bli otillgänglig.

Sådana villkor är inte "katastrofal", så att du vill överge den regionala distributionen helt på samma sätt som du kan göra i en katastrof återställnings situation, men affärs scenariot för vissa program kanske redan påverkas av tillgänglighets händelser som senast är några minuter eller till och med sekunder. Azure Service Bus används ofta i hybrid moln miljöer och med klienter som finns på nätverks gränsen, till exempel i butiker, restaurang, bank filialer, tillverknings platser, logistik anläggningar och flyg platser. Det är möjligt att ett nätverks Dirigerings-eller överbelastnings problem påverkar en plats möjlighet att komma åt den tilldelade Service Bus slut punkten medan en sekundär slut punkt i en annan region kan bli nåbar. På samma gång kan system som bearbetar meddelanden som kommer från dessa platser fortfarande ha nekad åtkomst till både den primära och den sekundära Service Bus slut punkterna. 

Det finns många praktiska exempel på sådana hybrid moln-och gräns program med låg företags tolerans för påverkan på problem med nätverks routning eller övergående tillgänglighets problem för en Service Bus entitet. De omfattar bearbetning av betalningar på detaljist platser, bordning på flyg plats portar och mobila telefon beställningar på restauranger, alla som kommer till en omedelbar och komplett kontakt när den pålitliga kommunikations Sök vägen inte är tillgänglig.

I den här kategorin diskuterar vi tre olika distribuerade mönster: "All-Active" replikering, "aktiv-passiv" replikering och "spillover"-replikering. 

#### <a name="all-active-replication"></a>All-Active replikering

Med det "alla aktiva" replik mönstret kan en aktiv replik av samma logiska ämne (eller kö) vara tillgänglig i flera namn områden (och regioner) och för att alla meddelanden ska bli tillgängliga i alla repliker, oavsett var de har placerats i kö. Mönstret bevarar vanligt vis ordning på meddelanden i relation till en utgivare. 

![Alla aktiva mönster](media/service-bus-federation-overview/mirrored-topics.jpg)

Som du ser i bilden lutar mönstret vanligt vis på Service Bus ämnen. Ett ämne för varje namn område som ska ingå i schemat för replikering. Vart och ett av dessa avsnitt har en "replikeringspartner" för alla andra ämnen som meddelanden ska replikeras till. I bilden ovan har vi bara ett par med ämnen och därför en prenumeration på en enda replikering för respektive andra ämne. I ett scenario med tre namn rymder *{N1, N2, N3}*, skulle ett ämne i namn område *N1* ha två prenumerationer för replikering, en för motsvarande ämne i *N2* och en för motsvarande ämne i *N3*. 

Varje replikeringspartner har en regel som kombinerar ett SQL-filter uttryck ( `replicated <> 1` ) och en SQL-åtgärd ( `set replicated = 1` ). Filtret för regeln säkerställer att endast meddelanden där den anpassade egenskapen inte har `replication` angetts eller inte har värdet `1` blir berättigat för den här prenumerationen, och åtgärden anger att den exakta egenskapen till värdet `1` för varje markerat meddelande stämmer efteråt. Resultatet är att när meddelandet kopieras till motsvarande ämne, är det inte längre kvalificerat för replikering i motsatt riktning och vi undviker därför bouncing av meddelanden mellan repliker.

En prenumeration med en respektive regel kan enkelt läggas till i alla ämnen som använder Azure CLI som detta.

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

För att modellera en kö, är varje ämne begränsat till bara en vanlig prenumeration (förutom prenumerationerna för replikering) som alla konsumenter delar.

> Med modellen all-Active Replication placeras en kopia av varje meddelande som skickas till något av ämnena i vart och ett av ämnena. Det innebär att din program kod i varje region kommer att se och bearbeta alla meddelanden.
> Det här mönstret är lämpligt för scenarier där data delas i flera regioner eller om redundant bearbetning vanligt vis önskas. Om du behöver bearbeta alla meddelanden en gång, precis som med en vanlig kö, måste du överväga något av följande två mönster.  

#### <a name="active-passive-replication"></a>Active-Passive replikering

Mönstret "Active-passivt" är en variant av det tidigare mönstret där endast ett av ämnena ("primär") används aktivt av programmet för att skicka och ta emot meddelanden och meddelanden replikeras till ett sekundärt ämne när det primära ämnet kan bli otillgängligt eller inte kan nås. 

![Aktivt passivt mönster](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

Den viktigaste skillnaden mellan det här mönstret och det tidigare mönstret är att replikeringen är enkelriktad från det primära ämnet till det sekundära ämnet. Det sekundära avsnittet blir aldrig primärt, men är ett alternativ för säkerhets kopiering för när det primära avsnittet är tillfälligt oanvändbart. 

En knapp som använder det här mönstret är att det försöker hjälpa till att minimera dubbel bearbetning. Under replikeringen `TimeToLive` anges meddelande egenskapen till en varaktighet för de replikerade meddelanden som visar den förväntade tiden under vilken ett fel i den primära orsaken leder till en redundansväxling. Om ditt användnings fall till exempel kräver en omflyttning av konsumenten till den sekundära inom 1 minut då hämtningen av meddelanden från den primära starten visar problem, bör den sekundära servern ha alla meddelanden som du inte har åtkomst till i den primära, men ett minimalt antal meddelanden som du redan har bearbetat från den primära datorn innan problemen visades. Om vi ställer in `TimeToLive` till två gånger denna period, 2 minuter, under replikering ( `set sys.TimeToLive = '0:2:0'` i regel åtgärden), kommer den sekundära bara att behålla meddelanden i 2 minuter och kommer att ta bort äldre. Det innebär att när mottagaren växlar över till den sekundära kan den snabbt läsa igenom och ta bort meddelanden som är äldre än den senaste som bearbetas och sedan bearbeta från det första meddelandet som den ännu inte har sett. Den faktiska Retentions tiden beror på det specifika användnings fallet och snabbt som du vill och kan växla över till den sekundära i ditt program. `TimeToLive`Inställningen är i intervallet från några sekunder till dagar. 

Även om programmet använder den sekundära, kan det också publiceras direkt till det sekundära ämnet, som sedan fungerar som ett vanligt ämne. Efter över gången till den sekundära kommer konsumenten därför att se en blandning av replikerade meddelanden och meddelanden som publiceras direkt till den sekundära. Programmet bör därför först byta publicering tillbaka till den primära och fortfarande tillåta tömning av lokalt publicerade meddelanden innan du växlar tillbaka till den sekundära konsumenten. På grund av att replikeringen återupptas automatiskt när den primära tiden är tillgänglig igen, kommer konsumenten också att få nya meddelanden som publicerats till primär under den tiden, med lite högre latens. 

> Det här mönstret är lämpligt för scenarier där meddelanden endast ska bearbetas en gång. Programmet behöver samar beta för att hålla koll på meddelanden som den har bearbetat från den primära eftersom det hittar dubbletter för varaktigheten för redundansväxlingen i den sekundära, och det kommer återigen att hitta dubbletter när de växlar tillbaka.
> Det avduplicerade kriteriet bör vara ett program som tillhandahålls `MessageId` . `EnqueuedTimeUtc`Värdet är också lämpligt som en vattenstämpel-indikator, men programmet måste tillåta en mängd klock frekvenser (flera sekunder) mellan primär och sekundär som med alla distribuerade system.


#### <a name="spillover-replication"></a>Spillover-replikering

Med spillover kan du använda ett aktivt/aktivt användning av flera Service Bus entiteter i flera regioner för att hantera scenariot där Service Bus är felfritt, men *konsumenten* blir överbelastad med antalet väntande meddelanden eller är inte tillgänglig. En orsak till detta kan vara att en databas som kan användas för att återställa konsument processen kan vara långsam eller otillgänglig. Det här mönstret fungerar med vanliga köer och med ämnes prenumerationer.  

![Spillover-replikering](media/service-bus-federation-overview/spillover.jpg)  

Som du ser i bilden replikerar mönstret för spillover-replikering meddelanden från köns eller prenumerationens associerade kö för [obeställbara](service-bus-dead-letter-queues.md) meddelanden till en länkad kö eller ett ämne i en annan namnrymd. 

Om du inte har någon fel situation används de två namn områdena parallellt, vart och ett tar emot vissa delmängd av den övergripande meddelande trafiken och deras associerade konsumenter som hanterar den del mängden. När en av de konsumenter börjar Visa hög felgrader eller slutar att fungera, kommer respektive meddelanden att visas i kön för obeställbara meddelanden, antingen genom att överskrida leverans antalet eller på grund av att den upphör att gälla. Replikeringen kommer sedan att välja dem och sedan köa om dem i den kopplade kön, där de visas för den förmodade felfria konsumenten. 

Om bearbetningen måste ske inom en viss tids gräns, `TimeToLive` ska för kön och/eller meddelanden anges, så att bearbetningen fortfarande kan inträffa i tid av den sekundära spillover, till exempel `TimeToLive` kan vara inställt på hälften av den tillåtna tiden.

Precis som med [alla-aktiva mönster](#all-active-replication), kan programmet lägga till en indikator i meddelandet om meddelandet redan har repliker ATS, så att de inte studsar mellan köernas par, utan är i stället anslagna i en extra kö som fungerar som kön för obeställbara meddelanden i det sammansatta mönstret.

> Det här mönstret är lämpligt för scenarier där det främsta fallet är att försvara sig mot tillgänglighets problem i konsumenter eller resurser som förlitar sig på, och även för att distribuera om trafik toppar i en av de kopplade köerna. Det ger även skydd mot en av namn områdena som blir otillgängliga om konsumenterna läser från båda köerna, men den fördröjning som påbörjas av `TimeToLive` förfallo datumet kan orsaka att meddelanden inom denna tids period visas i den otillgängliga namn rymden. 

### <a name="latency-optimization"></a>Optimering av latens 

Ämnen används för att distribuera information till flera konsumenter. I vissa fall, särskilt konsumenter med bred geografisk distribution, kan det vara bra att replikera meddelanden från ett ämne till ett ämne i ett sekundärt namn område som är närmare för konsumenterna.

![Optimering av latens](media/service-bus-federation-overview/latency-optimization.jpg)  

När du till exempel delar data mellan regionala, kontinentala hubbar, är det mer effektivt att överföra information en gång mellan hubbar och låta konsumenterna hämta sin kopia av data från dessa nav. 

Överföring av replikering kan göras i batchar, vilka konsumenter ofta hämtar och löser meddelanden en i taget. Med en bas nätverks fördröjning på 100 MS mellan, t. ex. Nordamerika och Europa, tar varje meddelande med 200 MS längre tid att bearbeta för de två turerna till en fjärran sluten entitet för att förvärva och reglera meddelandena, jämfört med en entitet i samma region. 

### <a name="validation-reduction-and-enrichment"></a>Validering, minskning och berikning

Meddelanden kan skickas till en Service Bus kö eller ett ämne från klienter som är externa i din egen lösning.

![Validering, minskning, berikning](media/service-bus-federation-overview/validation.jpg)  

Sådana meddelanden kan kräva kontroll av kompatibilitet med ett angivet schema och för icke-kompatibla meddelanden som ska släppas eller avbrytas. Vissa meddelanden kan behöva reduceras i komplexitet genom att utelämna data och vissa kan behöva beskrivas genom att lägga till data baserat på sökning av referens data. Åtgärderna kan utföras med anpassade funktioner i aktiviteten replikering. 

### <a name="stream-to-queue-replication"></a>Strömma till Queue-replikering

Azure Event Hubs är en perfekt lösning för hantering av extrema volymer av inkommande händelser. Men varken Event Hubs eller liknande motorer som Apache Kafka tillhandahåller en hanterad [konkurrerande konsument](/azure/architecture/patterns/competing-consumers) modell där flera användare kan hantera meddelanden från samma källa samtidigt utan att riskera att de bearbetas, och slutligen lösa dessa meddelanden när de har bearbetats. 

![Integrering](media/service-bus-federation-overview/hub-to-queue.jpg)

En ström till Queue-replikering överför innehållet i en enskild händelsehubben partition eller innehållet i en komplett Händelsehubben till en Service Bus kö, från vilken meddelanden kan bearbetas på ett säkert sätt, med beaktande av och med konkurrerande konsumenter. Den här replikeringen gör det också möjligt att använda alla andra Service Bus-funktioner för dessa meddelanden, inklusive routning med ämnen och sessionsbaserade demultiplexing. 

### <a name="consolidation-and-normalization"></a>Konsolidering och normalisering 

Globala lösningar består ofta av regionala delar som är mycket oberoende, inklusive deras egna bearbetnings kapacitet, men Supra-regionala och globala perspektiv kräver data integrering och därför en central konsolidering av samma meddelande data som utvärderas i respektive regionala anpassningar för det lokala perspektivet. 

![Ställning](media/service-bus-federation-overview/merge.jpg)

Normalisering är en smak av konsoliderings scenariot, där två eller flera inkommande sekvenser av meddelanden har samma typ av information, men med olika strukturer eller olika kodningar, och meddelandena måste vara kodade eller transformeras innan de kan förbrukas. 

Normalisering kan även innehålla kryptografiskt arbete, till exempel dekryptering av krypterade nytto laster från slut punkt till slut punkt och omkrypterar den med olika nycklar och algoritmer för den efterföljande konsument mål gruppen. 

### <a name="splitting-and-routing"></a>Dela och dirigera

Service Bus ämnen och deras prenumerations regler används ofta för att filtrera en data ström med meddelanden för en viss mål grupp, och den mål gruppen hämtade den filtrerade uppsättningen från en prenumeration. 

![Delar upp](media/service-bus-federation-overview/split.jpg)

I ett globalt system där mål gruppen för dessa meddelanden globalt distribueras eller tillhör olika program kan replikeringen användas för att överföra meddelanden från en sådan prenumeration till en kö eller ett ämne i ett annat namn område än där de används.

### <a name="replication-applications-in-azure-functions"></a>Program för replikering i Azure Functions

Implementering av mönster ovan kräver en skalbar och tillförlitlig körnings miljö för de replikeringsalternativ som du vill konfigurera och köra. I Azure är körnings miljön som är bäst lämpad för tillstånds lösa aktiviteter [Azure Functions](../azure-functions/functions-overview.md). 

Azure Functions kan köras under en [Azure-hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) , så att replikerings aktiviteterna kan integreras med de rollbaserade åtkomst kontroll reglerna för käll-och mål tjänsterna utan att du behöver hantera hemligheter längs sökvägen för replikering. För replikerings källor och mål som kräver explicita autentiseringsuppgifter kan Azure Functions innehålla konfigurations värden för dessa autentiseringsuppgifter i tätt skrivskyddad lagring i [Azure Key Vault](../key-vault/general/overview.md).

Azure Functions också tillåta att aktiviteterna integreras direkt med virtuella Azure-nätverk och [tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) för alla Azure Messaging Services, och det är enkelt att integrera med [Azure Monitor](../azure-monitor/overview.md).

Det viktigaste är att Azure Functions har färdiga, skalbara utlösare och utgående bindningar för [azure Event Hubs](../azure-functions/functions-bindings-service-bus.md), [azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md)och [Azure Queue Storage](/azure/azure-functions/functions-bindings-storage-queue), anpassade tillägg för [rabbitmq](https://github.com/azure/azure-functions-rabbitmq-extension)och [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). De flesta utlösare kan anpassas dynamiskt efter data flödes behoven genom att skala antalet körnings instanser som körs samtidigt, baserat på dokumenterade mått. 

Med Azure Functions-förbruknings planen kan de förinställda utlösarna till och med skala ned till noll medan inga meddelanden är tillgängliga för replikering, vilket innebär att du inte får några kostnader för att hålla konfigurationen redo att skala upp. Den viktigaste nack delen med användningen av förbruknings planen är att svars tiden för replikerings aktiviteterna som aktive ras från det här läget är betydligt högre än med värd avtalen där infrastrukturen behålls.  

Till skillnad från allt detta kräver de flesta vanliga MirrorMaker för meddelanden och händelser, till exempel Apache Kafkaens [](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) , att du tillhandahåller en värd miljö och skalar själva replikeringstopologin själv. Det innefattar att konfigurera och integrera säkerhets-och nätverksfunktioner och under lätta flödet av övervaknings data, och sedan har du fortfarande ingen möjlighet att mata in aktiviteter för anpassad replikering i flödet. 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har vi utforskat en rad Federations mönster och förklarat rollen Azure Functions som event-och Messaging Replication runtime i Azure. 

Härnäst kanske du vill läsa mer om hur du konfigurerar ett replikerande program med Azure Functions och hur du replikerar händelse flöden mellan Event Hubs och olika händelse-och meddelande system:

- [Program för replikering i Azure Functions](service-bus-federation-replicator-functions.md)
- [Replikera händelser mellan Service Bus entiteter](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [Dirigera händelser till Azure Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [Hämta händelser från Azure Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif 
