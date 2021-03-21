---
title: Check lista för prestanda och skalbarhet för tabell lagring – Azure Storage
description: En check lista över beprövade metoder för användning med Table Storage i utveckla program med höga prestanda.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 71b1f3cfa1df86b417c468d56f67cd7fe8d71d73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004712"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Check lista för prestanda och skalbarhet för tabell lagring

Microsoft har utvecklat ett antal beprövade metoder för att utveckla program med höga prestanda med Table Storage. Den här check listan identifierar viktiga metoder som utvecklare kan följa för att optimera prestanda. Tänk på dessa metoder när du utformar ditt program och hela processen.

Azure Storage har skalbarhets-och prestanda mål för kapacitet, transaktions hastighet och bandbredd. Mer information om Azure Storage skalbarhets mål finns i [skalbarhets-och prestanda mål för standard lagrings konton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) och [skalbarhets-och prestanda mål för Table Storage](scalability-targets.md).

## <a name="checklist"></a>Checklista

Den här artikeln ordnar beprövade metoder för prestanda i en check lista som du kan följa när du utvecklar ditt tabell lagrings program.

| Klart | Kategori | Design överväganden |
| --- | --- | --- |
| &nbsp; |Skalbarhets mål |[Kan du utforma ditt program så att det inte använder fler än det högsta antalet lagrings konton?](#maximum-number-of-storage-accounts) |
| &nbsp; |Skalbarhets mål |[Undviker du att du närmar dig kapacitets-och transaktions gränserna?](#capacity-and-transaction-targets) |
| &nbsp; |Skalbarhets mål |[Närmar du dig skalbarhets målen för entiteter per sekund?](#targets-for-data-operations) |
| &nbsp; |Nätverk |[Har klient sidan enheter tillräckligt med hög bandbredd och låg latens för att uppnå den prestanda som krävs?](#throughput) |
| &nbsp; |Nätverk |[Har klient sidan enheter en nätverks länk med hög kvalitet?](#link-quality) |
| &nbsp; |Nätverk |[Är klient programmet i samma region som lagrings kontot?](#location) |
| &nbsp; |Direkt klient åtkomst |[Använder du signaturer för delad åtkomst (SAS) och resurs delning mellan ursprung (CORS) för att ge direkt åtkomst till Azure Storage?](#sas-and-cors) |
| &nbsp; |Batchbearbetning |[Är ditt program batch-uppdateringar med hjälp av enhets grupp transaktioner?](#batch-transactions) |
| &nbsp; |.NET-konfiguration |[Använder du .NET Core 2,1 eller senare för bästa prestanda?](#use-net-core) |
| &nbsp; |.NET-konfiguration |[Har du konfigurerat att klienten ska använda ett tillräckligt antal samtidiga anslutningar?](#increase-default-connection-limit) |
| &nbsp; |.NET-konfiguration |[Har du konfigurerat .NET att använda ett tillräckligt antal trådar för .NET-program?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallellitet |[Har du säkerställt att parallellitet är lämpligt så att du inte överbelastar din klients funktioner eller använder skalbarhets målen?](#unbounded-parallelism) |
| &nbsp; |Verktyg |[Använder du de senaste versionerna av Microsoft-tillhandahållna klient bibliotek och verktyg?](#client-libraries-and-tools) |
| &nbsp; |Antal försök |[Använder du en princip för återförsök med en exponentiell backoff för begränsning av fel och tids gränser?](#timeout-and-server-busy-errors) |
| &nbsp; |Antal försök |[Kan programmet undvika nya försök för fel som inte kan återförsökas?](#non-retryable-errors) |
| &nbsp; |Konfiguration |[Använder du JSON för dina tabell förfrågningar?](#use-json) |
| &nbsp; |Konfiguration |[Har du stängt av Nagle-algoritmen för att förbättra prestanda för små begär Anden?](#disable-nagle) |
| &nbsp; |Tabeller och partitioner |[Har du partitionerat dina data korrekt?](#schema) |
| &nbsp; |Varma partitioner |[Undviker du tilläggs mönster för tillägg och lägga?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Varma partitioner |[Är dina infogningar/uppdateringar spridda över flera partitioner?](#high-traffic-data) |
| &nbsp; |Frågeomfång |[Har du utformat ditt schema för att tillåta att punkt frågor används i de flesta fall och tabell frågor som ska användas sparsamt?](#query-scope) |
| &nbsp; |Frågans densitet |[Söker dina frågor normalt bara igenom och returnerar rader som programmet kommer att använda?](#query-density) |
| &nbsp; |Begränsa returnerade data |[Använder du filtrering för att undvika att returnera entiteter som inte behövs?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Begränsa returnerade data |[Använder du projektion för att undvika att returnera egenskaper som inte behövs?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalisering |[Har du avnormaliserat dina data så att du undviker ineffektiva frågor eller flera Läs begär anden när du försöker hämta data?](#denormalization) |
| &nbsp; |Infoga, uppdatera och ta bort |[Är du batch-begäranden som måste vara transaktionella eller som kan utföras samtidigt för att minska antalet turer?](#batching) |
| &nbsp; |Infoga, uppdatera och ta bort |[Undviker du att hämta en entitet bara för att avgöra om du vill anropa INSERT eller Update?](#upsert) |
| &nbsp; |Infoga, uppdatera och ta bort |[Har du funderat på att lagra data serier som ofta hämtas tillsammans i en enskild entitet som egenskaper i stället för flera entiteter?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Infoga, uppdatera och ta bort |[För entiteter som alltid ska hämtas tillsammans och som kan skrivas i batchar (till exempel Time Series-data), har du funderat på att använda blobbar i stället för tabeller?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Skalbarhets mål

Om ditt program närmar sig eller överskrider något av skalbarhets målen kan det uppstå ökad transaktions fördröjning eller begränsning. När Azure Storage begränsar ditt program börjar tjänsten returnera 503 (servern är upptagen) eller 500 (åtgärds tids gräns) fel koder. Att undvika dessa fel genom att ligga kvar i gränserna för skalbarhets målen är en viktig del i att förbättra programmets prestanda.

Mer information om skalbarhets mål för Table service finns i skalbarhets- [och prestanda mål för Table Storage](scalability-targets.md).

### <a name="maximum-number-of-storage-accounts"></a>Maximalt antal lagrings konton

Om du närmar dig det maximala antalet lagrings konton som tillåts för en viss kombination av prenumerationer och regioner använder du flera lagrings konton för att Shard ska öka ingress, utgående, I/O-åtgärder per sekund (IOPS) eller kapacitet? I det här scenariot rekommenderar Microsoft att du utnyttjar ökade gränser för lagrings konton för att minska antalet lagrings konton som krävs för din arbets belastning om det är möjligt. Kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) för att begära ökade gränser för ditt lagrings konto. Mer information finns i avsnittet om att [presentera större och högre skalnings lagrings konton](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapacitets-och transaktions mål

Om ditt program närmar sig skalbarhets målen för ett enda lagrings konto bör du överväga att använda någon av följande metoder:  

- Ta hänsyn till arbets belastningen som gör att ditt program närmar sig eller överskrider skalbarhets målet. Kan du utforma det på ett annat sätt att använda mindre bandbredd eller kapacitet eller färre transaktioner?
- Om ditt program måste överskrida ett av skalbarhets målen skapar du flera lagrings konton och partitionerar dina program data på flera lagrings konton. Om du använder det här mönstret ser du till att utforma ditt program så att du kan lägga till fler lagrings konton i framtiden för belastnings utjämning. Själva lagrings konton har ingen annan kostnad än din användning i termer av lagrade data, transaktioner som har gjorts eller överförda data.
- Om ditt program närmar sig bandbredds målen bör du överväga att komprimera data på klient sidan för att minska den bandbredd som krävs för att skicka data till Azure Storage.
    När du komprimerar data kan du spara bandbredd och förbättra nätverks prestanda, men det kan också ha negativa effekter på prestanda. Utvärdera prestanda påverkan för de ytterligare bearbetnings kraven för data komprimering och dekomprimering på klient sidan. Tänk på att lagring av komprimerade data kan göra fel sökningen svårare eftersom det kan vara mer utmanande att visa data med hjälp av standard verktyg.
- Om ditt program närmar sig skalbarhets målen kontrollerar du att du använder en exponentiell backoff för återförsök. Det är bäst att försöka undvika att nå skalbarhets målen genom att implementera rekommendationerna som beskrivs i den här artikeln. Om du använder en exponentiell backoff för återförsök kan du dock förhindra att ditt program försöker igen, vilket kan göra att begränsningen blir sämre. Mer information finns i avsnittet [timeout-fel och servern är upptagen](#timeout-and-server-busy-errors).

### <a name="targets-for-data-operations"></a>Mål för data åtgärder

Azure Storage belastnings utjämning när trafiken till ditt lagrings konto ökar, men om trafiken uppvisar plötsliga burst-överföringar kanske du inte kan hämta data flödet omedelbart. Vi räknar med att se begränsnings-och/eller tids gränser under burst när Azure Storage automatiskt belastningsutjämna tabellen. Att öka långsamt ger vanligt vis bättre resultat eftersom systemet har tid att belastningsutjämna på lämpligt sätt.

#### <a name="entities-per-second-storage-account"></a>Entiteter per sekund (lagrings konto)

Skalbarhets gränsen för att komma åt tabeller är upp till 20 000 entiteter (1 KB per sekund) per sekund för ett konto. I allmänhet räknas varje enhet som infogas, uppdateras, tas bort eller skannas mot målet. En batch-infogning som innehåller 100 entiteter räknas som 100 entiteter. En fråga som skannar 1000 entiteter och returnerar 5 räknas som 1000 entiteter.

#### <a name="entities-per-second-partition"></a>Entiteter per sekund (partition)

I en enda partition är skalbarhets målet för att komma åt tabeller 2 000 entiteter (1 KB per sekund) med samma inventering som beskrivs i föregående avsnitt.

## <a name="networking"></a>Nätverk

Det fysiska nätverkets begränsningar i programmet kan ha en betydande inverkan på prestanda. I följande avsnitt beskrivs vissa begränsningar som användarna kan stöta på.  

### <a name="client-network-capability"></a>Klient nätverks funktion

Bandbredd och kvaliteten på nätverks länken spelar viktiga roller i program prestanda, enligt beskrivningen i följande avsnitt.

#### <a name="throughput"></a>Dataflöde

För bandbredd är problemet ofta klientens funktioner. Större Azure-instanser har nätverkskort med större kapacitet, så du bör överväga att använda en större instans eller flera virtuella datorer om du behöver högre nätverks gränser från en enda dator. Om du ansluter till Azure Storage från ett lokalt program gäller samma regel: förstå nätverks funktionerna i klient enheten och nätverks anslutningen till den Azure Storage platsen och förbättra dem efter behov eller utforma ditt program så att det fungerar inom sina funktioner.

#### <a name="link-quality"></a>Länk kvalitet

I takt med att nätverks användningen används bör du tänka på att nätverks förhållandena som resulterar i fel och paket förlust kommer att ta en långsam effektiv data flöde.  Att använda WireShark eller NetMon kan hjälpa dig att diagnostisera det här problemet.  

### <a name="location"></a>Location

I alla distribuerade miljöer ger klienten nära-servern den bästa prestandan. För att få åtkomst till Azure Storage med den lägsta svars tiden är den bästa platsen för din klient i samma Azure-region. Om du till exempel har en Azure-webbapp som använder Azure Storage kan du söka efter dem i en enda region, till exempel västra USA eller Asien, sydöstra. Samplacering av resurser minskar svars tiden och kostnaden, eftersom bandbredds användningen i en enda region är kostnads fri.  

Om klient program kommer åt Azure Storage men inte finns i Azure, till exempel appar för mobila enheter eller lokala företags tjänster, kan det minska svars tiden genom att leta upp lagrings kontot i en region nära dessa klienter. Om dina klienter är brett distribuerade (till exempel vissa i Nordamerika och några i Europa) kan du överväga att använda ett lagrings konto per region. Den här metoden är enklare att implementera om data som program arkivet är specifika för enskilda användare och inte behöver replikera data mellan lagrings konton.

## <a name="sas-and-cors"></a>SAS och CORS

Anta att du behöver auktorisera kod som till exempel java script som körs i användarens webbläsare eller i en mobil telefon-app för att komma åt data i Azure Storage. En metod är att bygga ett tjänst program som fungerar som en proxy. Användarens enhet autentiseras med tjänsten, som i sin tur ger åtkomst till Azure Storage resurser. På så sätt kan du undvika att exponera lagrings konto nycklar på oskyddade enheter. Den här metoden innebär dock en betydande belastning på tjänst programmet, eftersom alla data som överförs mellan användarens enhet och Azure Storage måste passera igenom tjänst programmet.

Du kan undvika att använda ett tjänst program som proxy för Azure Storage med hjälp av signaturer för delad åtkomst (SAS). Med SAS kan du göra det möjligt för användarens enhet att göra förfrågningar direkt till Azure Storage genom att använda en begränsad åtkomsttoken. Om en användare till exempel vill överföra ett foto till ditt program, kan tjänst programmet generera en SAS och skicka den till användarens enhet. SAS-token kan ge behörighet att skriva till en Azure Storage resurs under en angiven tids period, efter vilken SAS-token upphör att gälla. Mer information om SAS finns i [bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).  

Normalt tillåter en webbläsare inte Java Script på en sida som finns på en webbplats på en domän för att utföra vissa åtgärder, till exempel Skriv åtgärder, till en annan domän. Den här principen kallas samma-ursprungs princip och förhindrar att ett skadligt skript på en sida får åtkomst till data på en annan webb sida. Samma ursprungs princip kan dock vara en begränsning när du skapar en lösning i molnet. Resurs delning mellan ursprung (CORS) är en webb läsar funktion som gör det möjligt för mål domänen att kommunicera med den webbläsare som den litar på begär Anden från käll domänen.

Anta till exempel att ett webb program som körs i Azure gör en begäran om en resurs till ett Azure Storage-konto. Webb programmet är käll domänen och lagrings kontot är mål domänen. Du kan konfigurera CORS för någon av de Azure Storage-tjänsterna för att kommunicera med den webbläsare som begär från käll domänen är betrodd av Azure Storage. Mer information om CORS finns i [stöd för resurs delning mellan ursprung (CORS) för Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Både SAS och CORS kan hjälpa dig att undvika onödig belastning på ditt webb program.  

## <a name="batch-transactions"></a>Batch-transaktioner

Table service stöder batch-transaktioner på entiteter som finns i samma tabell och tillhör samma partitionsuppsättning. Mer information finns i [utföra enhets grupp transaktioner](/rest/api/storageservices/performing-entity-group-transactions).

## <a name="net-configuration"></a>.NET-konfiguration

Om du använder .NET Framework visar det här avsnittet flera snabb konfigurations inställningar som du kan använda för att göra betydande prestanda förbättringar.  Om du använder andra språk kan du kontrol lera om liknande koncept gäller för det valda språket.  

### <a name="use-net-core"></a>Använd .NET Core

Utveckla dina Azure Storage-program med .NET Core 2,1 eller senare för att dra nytta av prestanda förbättringar. Att använda .NET Core 3. x rekommenderas när det är möjligt.

Mer information om prestanda förbättringar i .NET Core finns i följande blogg inlägg:

- [Prestanda förbättringar i .NET Core 3,0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Prestanda förbättringar i .NET Core 2,1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Öka standard anslutnings gränsen

I .NET ökar följande kod standard anslutnings gränsen (vanligt vis 2 i en klient miljö eller 10 i en Server miljö) till 100. Normalt bör du ange värdet till ungefär så många trådar som används av ditt program.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Ange anslutnings gränsen innan du öppnar några anslutningar.  

Information om andra programmeringsspråk finns i språk dokumentationen för att fastställa hur du ställer in anslutnings gränsen.  

Mer information finns i blogg inlägget [webb tjänster: samtidiga anslutningar](/archive/blogs/darrenj/web-services-concurrent-connections).  

### <a name="increase-minimum-number-of-threads"></a>Öka det minsta antalet trådar

Om du använder synkrona anrop tillsammans med asynkrona uppgifter kanske du vill öka antalet trådar i trådpoolen:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Mer information finns i metoden [trådpool. SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) .  

## <a name="unbounded-parallelism"></a>Obegränsad parallellitet

Parallellitet kan vara bra för prestanda, var noga med att använda obegränsad parallellitet, vilket innebär att det inte finns någon gräns för antalet trådar eller parallella begär Anden. Se till att begränsa parallella begär Anden för att ladda upp eller hämta data, för att få åtkomst till flera partitioner i samma lagrings konto, eller för att få åtkomst till flera objekt i samma partition. Om parallellitet är obundet kan ditt program överskrida klient enhetens funktioner eller lagrings kontots skalbarhets mål, vilket resulterar i längre latens och begränsning.  

## <a name="client-libraries-and-tools"></a>Klient bibliotek och verktyg

Använd alltid de senaste klient biblioteken och verktygen från Microsoft för bästa prestanda. Azure Storage klient bibliotek är tillgängliga för flera olika språk. Azure Storage stöder också PowerShell och Azure CLI. Microsoft utvecklar aktivt dessa klient bibliotek och verktyg med prestanda i åtanke, håller dem uppdaterade med de senaste service versionerna och ser till att de hanterar många av de beprövade prestanda metoderna internt.

## <a name="handle-service-errors"></a>Hantera tjänst fel

Azure Storage returnerar ett fel när tjänsten inte kan bearbeta en begäran. Att förstå de fel som kan returneras av Azure Storage i ett specifikt scenario är användbart för att optimera prestanda.

### <a name="timeout-and-server-busy-errors"></a>Timeout-och Server upptagen-fel

Azure Storage kan begränsa ditt program om det närmar sig begränsningen för skalbarhet. I vissa fall kan Azure Storage inte hantera en begäran på grund av ett tillfälligt tillstånd. I båda fallen kan tjänsten returnera ett 503-fel (Server upptagen) eller 500 (timeout). Dessa fel kan också inträffa om tjänsten ombalanserar datapartitioner för att tillåta högre data flöde. Klient programmet bör normalt försöka utföra åtgärden igen som orsakar något av dessa fel. Men om Azure Storage begränsar ditt program eftersom det överskrider skalbarhets målen, eller även om tjänsten inte kunde hantera begäran av någon annan anledning, kan aggressiva återförsök göra problemet sämre. Det rekommenderas att du använder en exponentiell återförsöks princip och klient biblioteken använder detta beteende som standard. Ditt program kan till exempel försöka igen om 2 sekunder, sedan 4 sekunder, sedan 10 sekunder, sedan 30 sekunder, och sedan får upp helt. På så sätt minskar ditt program avsevärt sin belastning på tjänsten, i stället för exacerbating beteende som kan leda till begränsning.  

Anslutnings fel kan göras omedelbart, eftersom de inte är resultatet av begränsningen och förväntas vara tillfälliga.  

### <a name="non-retryable-errors"></a>Fel som inte går att försöka igen

Klient biblioteken hanterar nya försök med en medvetenhet om vilka fel som kan göras och vilka som inte kan utföras. Men om du anropar Azure Storage REST API direkt finns det vissa fel som du inte bör försöka igen. Till exempel anger ett 400-fel (felaktig begäran) att klient programmet skickade en begäran som inte kunde bearbetas eftersom det inte hade det förväntade formuläret. Om du skickar om den här begäran resulterar det i samma svar varje gång, så det finns ingen punkt för att försöka igen. Om du anropar Azure Storage REST API direkt bör du vara medveten om eventuella fel och om de bör göras om.

Mer information om Azure Storage felkoder finns i [status-och felkoder](/rest/api/storageservices/status-and-error-codes2).

## <a name="configuration"></a>Konfiguration

Det här avsnittet innehåller flera snabb konfigurations inställningar som du kan använda för att göra betydande prestanda förbättringar i Table service:

### <a name="use-json"></a>Använd JSON

Från och med Storage Service version 2013-08-15 har Table service stöd för JSON i stället för det XML-baserade AtomPub-formatet för överföring av tabell data. Med JSON kan du minska nytto Last storlekarna med så mycket som 75% och kan förbättra programmets prestanda avsevärt.

Mer information finns i post [Microsoft Azures tabeller: Introduktion till JSON](/archive/blogs/windowsazurestorage/windows-azure-tables-introducing-json) och [nytto Last format för tabell tjänst åtgärder](/rest/api/storageservices/Payload-Format-for-Table-Service-Operations).

### <a name="disable-nagle"></a>Inaktivera Nagle

Nagle-algoritmen implementeras ofta i TCP/IP-nätverk som ett sätt att förbättra nätverks prestanda. Men det är inte optimalt i alla situationer (till exempel mycket interaktiva miljöer). Nagle-algoritmen har en negativ inverkan på prestanda för begär anden till Azure-Table service och du bör inaktivera den om möjligt.

## <a name="schema"></a>Schema

Hur du representerar och frågar dina data är den största enkla faktorn som påverkar Table serviceens prestanda. Även om alla program är olika beskriver det här avsnittet några allmänna beprövade metoder som är relaterade till:

- Tabell design
- Effektiva frågor
- Effektiva data uppdateringar

### <a name="tables-and-partitions"></a>Tabeller och partitioner

Tabellerna är indelade i partitioner. Varje enhet som lagras i en partition delar samma partitionsnyckel och har en unik rad nyckel för att identifiera den i den partitionen. Partitioner ger förmåner, men omfattar även skalbarhets gränser.

- Fördelar: du kan uppdatera entiteter i samma partition i en enskild, Atomic, batch-transaktion som innehåller upp till 100 separata lagrings åtgärder (högst 4 MB storlek). Om du antar samma antal entiteter som ska hämtas kan du även fråga efter data inom en enda partition än data som sträcker sig över partitioner (Läs vidare om du vill veta mer om att fråga tabell data).
- Skalbarhets gräns: åtkomst till entiteter som lagras i en enskild partition kan inte läsas in eftersom partitioner stöder atomiska batch-transaktioner. Därför är skalbarhets målet för en enskild tabell partition lägre än för Table service som helhet.

På grund av dessa egenskaper för tabeller och partitioner bör du införa följande design principer:

- Hitta data som klient programmet ofta uppdaterar eller frågar i samma logiska arbets enhet på samma partition. Du kan till exempel hitta data i samma partition om ditt program aggregerar skrivningar eller om du utför atomiska batch-åtgärder. Dessutom kan data i en enda partition bli mer effektiva i en enskild fråga än data mellan partitioner.
- Hitta data som klient programmet inte infogar, uppdaterar eller frågar i samma logiska arbets enhet (det vill säga i en enskild fråga eller uppdatering av en grupp) i separata partitioner. Tänk på att det inte finns någon gräns för antalet partitionstyper i en enskild tabell, så att det inte är något problem med miljon tals sessionsnycklar och inte påverkar prestandan. Om ditt program till exempel är en populär webbplats med användar inloggning, kan det vara ett bra val att använda användar-ID: t som partitionsnyckel.

#### <a name="hot-partitions"></a>Varma partitioner

En varm partition är en som tar emot en oproportionerlig procent andel av trafiken till ett konto och kan inte läsas in eftersom det är en enda partition. I allmänhet skapas varma partitioner på ett av två sätt:

#### <a name="append-only-and-prepend-only-patterns"></a>Endast tillägg och lägga mönster

Mönstret "Lägg till endast" är ett av alla (eller nästan alla) trafik till en specifik partitionsnyckel ökar och minskar i enlighet med aktuell tid. Anta till exempel att ditt program använder det aktuella datumet som en partitionsnyckel för loggdata. Den här designen resulterar i alla infogningar som kommer till den sista partitionen i tabellen och systemet kan inte belastningsutjämna korrekt. Om volymen för trafik till partitionen överskrider skalbarhets målet på partitions nivå, leder det till en begränsning. Det är bättre att se till att trafiken skickas till flera partitioner, så att du kan aktivera belastnings utjämning för förfrågningarna i din tabell.

#### <a name="high-traffic-data"></a>Data med hög trafik

Om ditt partitionerings schema resulterar i en enda partition som bara har data som är mycket mer använda än andra partitioner, kan du också se begränsning när den partitionen närmar sig skalbarhets målet för en enda partition. Det är bättre att se till att ditt partitionsschema resulterar i att det inte finns någon partition som närmar sig skalbarhets målen.

### <a name="querying"></a>Fråga

I det här avsnittet beskrivs beprövade metoder för att skicka frågor till Table service.

#### <a name="query-scope"></a>Frågeomfång

Det finns flera sätt att ange intervallet för entiteter att fråga. I följande lista beskrivs varje alternativ för fråge omfånget.

- **Punkt frågor:**-en punkt fråga hämtar exakt en entitet genom att ange både partitionsnyckel och rad nyckel för entiteten som ska hämtas. Dessa frågor är effektiva och du bör använda dem där det är möjligt.
- **Partitions frågor:** En partitions fråga är en fråga som hämtar en uppsättning data som delar en gemensam partitionsnyckel. Normalt anger frågan ett intervall med rad nyckel värden eller ett värde intervall för viss enhets egenskap utöver en partitionsnyckel. Dessa frågor är mindre effektiva än punkt frågor och bör användas sparsamt.
- **Tabell frågor:** En tabell fråga är en fråga som hämtar en uppsättning entiteter som inte delar en gemensam partitionsnyckel. Dessa frågor är inte effektiva och du bör undvika dem om möjligt.

I allmänhet bör du undvika genomsökningar (frågor som är större än en enskild entitet), men om du måste skanna kan du försöka organisera dina data så att dina sökningar hämtar de data du behöver utan att söka efter eller returnera större mängder entiteter som du inte behöver.

#### <a name="query-density"></a>Frågans densitet

En annan viktig faktor i frågans effektivitet är antalet entiteter som returnerades jämfört med antalet entiteter som genomsöktes för att hitta den returnerade uppsättningen. Om programmet utför en tabell fråga med ett filter för ett egenskaps värde som bara är 1% av data resurserna, genomsöker frågan 100 entiteter för varje entitet som returneras. De tabell skalbarhets mål som diskuterats tidigare relaterar till antalet genomsökta enheter och inte antalet returnerade enheter: en låg frågans densitet kan enkelt orsaka att Table service begränsar ditt program eftersom det måste söka igenom så många entiteter för att hämta den entitet som du letar efter. Mer information om hur du undviker begränsning finns i avsnittet med rubriken [denormalisering](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Begränsa mängden data som returneras

När du vet att en fråga kommer att returnera entiteter som du inte behöver i klient programmet kan du överväga att använda ett filter för att minska storleken på den returnerade uppsättningen. Även om entiteterna som inte returneras till klienten fortfarande räknas mot skalbarhets gränserna, förbättras programmets prestanda på grund av den minskade storleken på nätverkets nytto last och det reducerade antalet enheter som klient programmet måste bearbeta. Tänk på att skalbarhets målen avser antalet genomsökta entiteter, så en fråga som filtrerar ut många entiteter kan fortfarande leda till begränsning, även om få entiteter returneras. Mer information om hur du skapar frågor effektivt finns i avsnittet om [frågans densitet](#query-density).

Om klient programmet bara behöver en begränsad uppsättning egenskaper från entiteterna i tabellen kan du använda projektion för att begränsa storleken på den returnerade data uppsättningen. Som med filtrering bidrar projektionen till att minska belastningen på nätverket och klient bearbetningen.

#### <a name="denormalization"></a>Denormalisering

Till skillnad från att arbeta med relations databaser, är beprövade metoder för att effektivt fråga tabell data att avnormalisera dina data. Det innebär att duplicera samma data i flera entiteter (en för varje nyckel som du kan använda för att hitta data) för att minimera antalet entiteter som en fråga måste genomsökas för att hitta de data som klienten behöver, i stället för att behöva söka igenom ett stort antal entiteter för att hitta de data som dina program behöver. I en e-handelswebbplats kanske du till exempel vill hitta en order både i kund-ID: t (meddela mig till den här kundens order) och efter datumet (ge mig beställningar på ett datum). I Table Storage är det bäst att lagra entiteten (eller en referens till den) två gånger – en gång med tabell namn, PN och kund för att under lätta sökning efter kund-ID, en gång för att under lätta att hitta den med datumet.  

### <a name="insert-update-and-delete"></a>Infoga, uppdatera och ta bort

I det här avsnittet beskrivs beprövade metoder för att ändra entiteter som lagras i Table service.  

#### <a name="batching"></a>Batchbearbetning

Batch-transaktioner kallas för enhets grupp transaktioner i Azure Storage. Alla åtgärder inom en enhets grupp transaktion måste finnas på en enda partition i en enda tabell. Använd om möjligt enhets grupp transaktioner för att utföra infogningar, uppdateringar och borttagningar i batchar. Om du använder enhets grupp transaktioner minskar antalet tur och inkörningar från klient programmet till servern, vilket minskar antalet fakturerbara transaktioner (en enhets grupp transaktion räknas som en enskild transaktion för fakturerings syfte och kan innehålla upp till 100 lagrings åtgärder) och möjliggör atomiska uppdateringar (alla åtgärder lyckas eller Miss lyckas inom en enhets grupp transaktion). Miljöer med hög fördröjning, till exempel mobila enheter, kommer att ha stor nytta av att använda enhets grupp transaktioner.  

#### <a name="upsert"></a>Upsert

Använd Table **upsert** -åtgärder där det är möjligt. Det finns två typer av **upsert**, som båda kan vara mer effektiva än vanliga **insert** -och **uppdaterings** åtgärder:  

- **InsertOrMerge**: Använd den här åtgärden när du vill ladda upp en delmängd av entitetens egenskaper, men är osäker på om entiteten redan finns. Om entiteten finns uppdaterar det här anropet egenskaperna som ingår i **upsert** -åtgärden och lämnar alla befintliga egenskaper, om entiteten inte finns, infogar den nya entiteten. Detta liknar att använda projektion i en fråga, i att du bara behöver överföra de egenskaper som ändras.
- **InsertOrReplace**: Använd den här åtgärden när du vill ladda upp en helt ny entitet, men är osäker på om den redan finns. Använd den här åtgärden när du vet att den nyligen överförda entiteten är helt korrekt, eftersom den gamla entiteten skrivs över helt. Till exempel vill du uppdatera entiteten som lagrar en användares aktuella plats oavsett om programmet har lagrat plats data tidigare lagrat för användaren. entiteten ny plats har slutförts och du behöver inte någon information från någon tidigare entitet.

#### <a name="storing-data-series-in-a-single-entity"></a>Lagra data serier i en enskild entitet

Ibland lagrar ett program en serie data som ofta behöver hämtas samtidigt: ett program kan till exempel spåra CPU-användning över tid för att rita ett rullande diagram över data från de senaste 24 timmarna. En metod är att ha en tabell enhet per timme, där varje entitet representerar en viss timme och lagrar processor användningen för den timmen. För att kunna rita dessa data måste programmet hämta de entiteter som innehåller data från de 24 senaste timmarna.  

Alternativt kan ditt program lagra processor användningen för varje timme som en separat egenskap i en enskild enhet: om du vill uppdatera varje timme kan programmet använda ett enda **InsertOrMerge upsert** -anrop för att uppdatera värdet för den senaste timmen. För att kunna rita data behöver programmet bara hämta en enda entitet i stället för 24, vilket gör en effektiv fråga. Mer information om frågornas effektivitet finns i avsnittet med rubriken [frågans omfång](#query-scope).

#### <a name="storing-structured-data-in-blobs"></a>Lagra strukturerade data i blobbar

Om du utför batch-infogningar och sedan hämtar intervall av entiteter, bör du överväga att använda blobbar i stället för tabeller. Ett användbart exempel är en loggfil. Du kan gruppera flera minuters loggar och infoga dem och sedan hämta flera minuters loggar i taget. I det här fallet är prestanda bättre om du använder blobbar i stället för tabeller, eftersom du avsevärt kan minska antalet objekt som skrivs till eller läses, och även eventuellt hur många begär Anden som behöver göras.  

## <a name="next-steps"></a>Nästa steg

- [Skalbarhets-och prestanda mål för Table Storage](scalability-targets.md)
- [Skalbarhets-och prestanda mål för standard lagrings konton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Status och felkoder](/rest/api/storageservices/Status-and-Error-Codes2)