---
title: Check lista för prestanda och skalbarhet för Queue Storage-Azure Storage
description: En check lista över beprövade metoder för användning med Queue Storage för att utveckla program med höga prestanda.
author: tamram
services: storage
ms.author: tamram
ms.date: 10/10/2019
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 4040a81d5b509ddbdd355953e28721a7c9fccfb8
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585674"
---
<!-- docutune:casing "Timeout and Server Busy errors" -->

# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Check lista för prestanda och skalbarhet för Queue Storage

Microsoft har utvecklat ett antal beprövade metoder för att utveckla program med höga prestanda med Queue Storage. Den här check listan identifierar viktiga metoder som utvecklare kan följa för att optimera prestanda. Tänk på dessa metoder när du utformar ditt program och hela processen.

Azure Storage har skalbarhets-och prestanda mål för kapacitet, transaktions hastighet och bandbredd. Mer information om Azure Storage skalbarhets mål finns i [skalbarhets-och prestanda mål för standard lagrings konton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) och [skalbarhets-och prestanda mål för Queue Storage](scalability-targets.md).

## <a name="checklist"></a>Checklista

Den här artikeln ordnar beprövade metoder för prestanda i en check lista som du kan följa när du utvecklar ditt Queue Storage-program.

| Klart | Kategori | Design överväganden |
|--|--|--|
| &nbsp; | Skalbarhets mål | [Kan du utforma ditt program så att det inte använder fler än det högsta antalet lagrings konton?](#maximum-number-of-storage-accounts) |
| &nbsp; | Skalbarhets mål | [Undviker du att du närmar dig kapacitets-och transaktions gränserna?](#capacity-and-transaction-targets) |
| &nbsp; | Nätverk | [Har klient sidan enheter tillräckligt med hög bandbredd och låg latens för att uppnå den prestanda som krävs?](#throughput) |
| &nbsp; | Nätverk | [Har klient sidan enheter en nätverks länk med hög kvalitet?](#link-quality) |
| &nbsp; | Nätverk | [Är klient programmet i samma region som lagrings kontot?](#location) |
| &nbsp; | Direkt klient åtkomst | [Använder du signaturer för delad åtkomst (SAS) och resurs delning mellan ursprung (CORS) för att ge direkt åtkomst till Azure Storage?](#sas-and-cors) |
| &nbsp; | .NET-konfiguration | [Använder du .NET Core 2,1 eller senare för bästa prestanda?](#use-net-core) |
| &nbsp; | .NET-konfiguration | [Har du konfigurerat att klienten ska använda ett tillräckligt antal samtidiga anslutningar?](#increase-default-connection-limit) |
| &nbsp; | .NET-konfiguration | [Har du konfigurerat .NET att använda ett tillräckligt antal trådar för .NET-program?](#increase-the-minimum-number-of-threads) |
| &nbsp; | Parallellitet | [Har du säkerställt att parallellitet är lämpligt så att du inte överbelastar din klients funktioner eller använder skalbarhets målen?](#unbounded-parallelism) |
| &nbsp; | Verktyg | [Använder du de senaste versionerna av Microsoft-tillhandahållna klient bibliotek och verktyg?](#client-libraries-and-tools) |
| &nbsp; | Antal försök | [Använder du en princip för återförsök med en exponentiell backoff för begränsning av fel och tids gränser?](#timeout-and-server-busy-errors) |
| &nbsp; | Antal försök | [Kan programmet undvika nya försök för fel som inte kan återförsökas?](#non-retryable-errors) |
| &nbsp; | Konfiguration | [Har du stängt av Nagle-algoritmen för att förbättra prestanda för små begär Anden?](#disable-nagles-algorithm) |
| &nbsp; | Meddelandestorlek | [Är dina meddelanden komprimerade för att förbättra köns prestanda?](#message-size) |
| &nbsp; | Mass hämtning | [Hämtar du flera meddelanden i en enda hämtnings åtgärd?](#batch-retrieval) |
| &nbsp; | Avsöknings frekvens | [Avsöker du tillräckligt ofta för att minska den uppskattade svars tiden för ditt program?](#queue-polling-interval) |
| &nbsp; | Uppdatera meddelande | [Utför du en uppdaterings meddelande åtgärd för att lagra förloppet för bearbetning av meddelanden, så att du kan undvika att behöva bearbeta hela meddelandet om ett fel uppstår?](#perform-an-update-message-operation) |
| &nbsp; | Arkitektur | [Använder du köer för att göra hela programmet skalbart genom att hålla långvariga arbets belastningar från den kritiska vägen och skala sedan oberoende av varandra?](#application-architecture) |

## <a name="scalability-targets"></a>Skalbarhets mål

Om ditt program närmar sig eller överskrider något av skalbarhets målen kan det uppstå ökad transaktions fördröjning eller begränsning. När Azure Storage begränsar ditt program börjar tjänsten returnera `Server Busy` felkoderna 503 () eller 500 ( `Operation Timeout` ). Att undvika dessa fel genom att ligga kvar i gränserna för skalbarhets målen är en viktig del i att förbättra programmets prestanda.

Mer information om skalbarhets mål för Queue Storage finns i [Azure Storage skalbarhets-och prestanda mål](./scalability-targets.md#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maximalt antal lagrings konton

Om du närmar dig det maximala antalet lagrings konton som tillåts för en viss kombination av prenumerationer och regioner använder du flera lagrings konton för att Shard ska öka ingress, utgående, I/O-åtgärder per sekund (IOPS) eller kapacitet? I det här scenariot rekommenderar Microsoft att du utnyttjar ökade gränser för lagrings konton för att minska antalet lagrings konton som krävs för din arbets belastning om det är möjligt. Kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) för att begära ökade gränser för ditt lagrings konto. Mer information finns i avsnittet om att [presentera större och högre skalnings lagrings konton](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapacitets-och transaktions mål

Om ditt program närmar sig skalbarhets målen för ett enda lagrings konto bör du överväga att använda någon av följande metoder:

- Om skalbarhets målen för köerna inte är tillräckliga för ditt program, använder du flera köer och distribuerar meddelanden mellan dem.
- Ta hänsyn till arbets belastningen som gör att ditt program närmar sig eller överskrider skalbarhets målet. Kan du utforma det på ett annat sätt att använda mindre bandbredd eller kapacitet eller färre transaktioner?
- Om ditt program måste överskrida ett av skalbarhets målen skapar du flera lagrings konton och partitionerar dina program data på flera lagrings konton. Om du använder det här mönstret ser du till att utforma ditt program så att du kan lägga till fler lagrings konton i framtiden för belastnings utjämning. Själva lagrings konton har ingen annan kostnad än din användning i termer av lagrade data, transaktioner som har gjorts eller överförda data.
- Om ditt program närmar sig bandbredds målen bör du överväga att komprimera data på klient sidan för att minska den bandbredd som krävs för att skicka data till Azure Storage. När du komprimerar data kan du spara bandbredd och förbättra nätverks prestanda, men det kan också ha negativa effekter på prestanda. Utvärdera prestanda påverkan för de ytterligare bearbetnings kraven för data komprimering och dekomprimering på klient sidan. Tänk på att lagring av komprimerade data kan göra fel sökningen svårare eftersom det kan vara mer utmanande att visa data med hjälp av standard verktyg.
- Om ditt program närmar sig skalbarhets målen kontrollerar du att du använder en exponentiell backoff för återförsök. Det är bäst att försöka undvika att nå skalbarhets målen genom att implementera rekommendationerna som beskrivs i den här artikeln. Om du använder en exponentiell backoff för återförsök kan du dock förhindra att ditt program försöker igen, vilket kan göra att begränsningen blir sämre. Mer information finns i avsnittet [timeout-fel och Server upptagen fel](#timeout-and-server-busy-errors) .

## <a name="networking"></a>Nätverk

Det fysiska nätverkets begränsningar i programmet kan ha en betydande inverkan på prestanda. I följande avsnitt beskrivs vissa begränsningar som användarna kan stöta på.

### <a name="client-network-capability"></a>Klient nätverks funktion

Bandbredd och kvaliteten på nätverks länken spelar viktiga roller i program prestanda, enligt beskrivningen i följande avsnitt.

#### <a name="throughput"></a>Dataflöde

För bandbredd är problemet ofta klientens funktioner. Större Azure-instanser har nätverkskort med större kapacitet, så du bör överväga att använda en större instans eller flera virtuella datorer om du behöver högre nätverks gränser från en enda dator. Om du ansluter till Azure Storage från ett lokalt program gäller samma regel: förstå nätverks funktionerna hos klienten het och nätverks anslutningen till den Azure Storage platsen och förbättra dem efter behov eller utforma ditt program så att det fungerar inom sina funktioner.

#### <a name="link-quality"></a>Länk kvalitet

I takt med att nätverks användningen används bör du tänka på att nätverks förhållandena som resulterar i fel och paket förlust kommer att ta en långsam effektiv data flöde. Att använda wireshark eller Network Monitor kan hjälpa dig att diagnostisera det här problemet.

### <a name="location"></a>Plats

I alla distribuerade miljöer ger klienten nära-servern den bästa prestandan. För att få åtkomst till Azure Storage med den lägsta svars tiden är den bästa platsen för din klient i samma Azure-region. Om du till exempel har en Azure-webbapp som använder Azure Storage kan du söka efter dem i en enda region, t. ex. västra USA eller Sydostasien. Samplacering av resurser minskar svars tiden och kostnaden, eftersom bandbredds användningen i en enda region är kostnads fri.

Om klient program kommer åt Azure Storage men inte finns i Azure, till exempel appar för mobila enheter eller lokala företags tjänster, kan det minska svars tiden genom att leta upp lagrings kontot i en region nära dessa klienter. Om dina klienter är brett distribuerade (till exempel vissa i Nordamerika och några i Europa) kan du överväga att använda ett lagrings konto per region. Den här metoden är enklare att implementera om data som program arkivet är specifika för enskilda användare och inte behöver replikera data mellan lagrings konton.

## <a name="sas-and-cors"></a>SAS och CORS

Anta att du behöver auktorisera kod som till exempel java script som körs i användarens webbläsare eller i en mobil telefon-app för att komma åt data i Azure Storage. En metod är att bygga ett tjänst program som fungerar som en proxy. Användarens enhet autentiseras med tjänsten, som i sin tur ger åtkomst till Azure Storage resurser. På så sätt kan du undvika att exponera lagrings konto nycklar på oskyddade enheter. Den här metoden innebär dock en betydande belastning på tjänst programmet, eftersom alla data som överförs mellan användarens enhet och Azure Storage måste passera igenom tjänst programmet.

Du kan undvika att använda ett tjänst program som proxy för Azure Storage med hjälp av signaturer för delad åtkomst (SAS). Med SAS kan du göra det möjligt för användarens enhet att göra förfrågningar direkt till Azure Storage genom att använda en begränsad åtkomsttoken. Om en användare till exempel vill överföra ett foto till ditt program, kan tjänst programmet generera en SAS och skicka den till användarens enhet. SAS-token kan ge behörighet att skriva till en Azure Storage resurs under en angiven tids period, efter vilken SAS-token upphör att gälla. Mer information om SAS finns i [bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).

Normalt tillåter en webbläsare inte Java Script på en sida som finns på en webbplats på en domän för att utföra vissa åtgärder, till exempel Skriv åtgärder, till en annan domän. Den här principen kallas samma-ursprungs princip och förhindrar att ett skadligt skript på en sida får åtkomst till data på en annan webb sida. Samma ursprungs princip kan dock vara en begränsning när du skapar en lösning i molnet. Resurs delning mellan ursprung (CORS) är en webb läsar funktion som gör det möjligt för mål domänen att kommunicera med den webbläsare som den litar på begär Anden från käll domänen.

Anta till exempel att ett webb program som körs i Azure gör en begäran om en resurs till ett Azure Storage-konto. Webb programmet är käll domänen och lagrings kontot är mål domänen. Du kan konfigurera CORS för någon av de Azure Storage-tjänsterna för att kommunicera med den webbläsare som begär från käll domänen är betrodd av Azure Storage. Mer information om CORS finns i [stöd för resurs delning mellan ursprung (CORS) för Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

Både SAS och CORS kan hjälpa dig att undvika onödig belastning på ditt webb program.

## <a name="net-configuration"></a>.NET-konfiguration

Om du använder .NET Framework visar det här avsnittet flera snabb konfigurations inställningar som du kan använda för att göra betydande prestanda förbättringar. Om du använder andra språk, kontrollerar du om du vill se om liknande koncept gäller för det valda språket.

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

### <a name="increase-the-minimum-number-of-threads"></a>Öka det minsta antalet trådar

Om du använder synkrona anrop tillsammans med asynkrona uppgifter kanske du vill öka antalet trådar i trådpoolen:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Mer information finns i- [`ThreadPool.SetMinThreads`](/dotnet/api/system.threading.threadpool.setminthreads) metoden.

## <a name="unbounded-parallelism"></a>Obegränsad parallellitet

Parallellitet kan vara bra för prestanda, var noga med att använda obegränsad parallellitet, vilket innebär att det inte finns någon gräns för antalet trådar eller parallella begär Anden. Se till att begränsa parallella begär Anden för att ladda upp eller hämta data, för att få åtkomst till flera partitioner i samma lagrings konto, eller för att få åtkomst till flera objekt i samma partition. Om parallellitet är obundet kan ditt program överskrida klient enhetens funktioner eller lagrings kontots skalbarhets mål, vilket resulterar i längre latens och begränsning.

## <a name="client-libraries-and-tools"></a>Klient bibliotek och verktyg

Använd alltid de senaste klient biblioteken och verktygen från Microsoft för bästa prestanda. Azure Storage klient bibliotek är tillgängliga för flera olika språk. Azure Storage stöder också PowerShell och Azure CLI. Microsoft utvecklar aktivt dessa klient bibliotek och verktyg med prestanda i åtanke, håller dem uppdaterade med de senaste service versionerna och ser till att de hanterar många av de beprövade prestanda metoderna internt. Mer information finns i [referens dokumentationen för Azure Storage](./reference.md).

## <a name="handle-service-errors"></a>Hantera tjänst fel

Azure Storage returnerar ett fel när tjänsten inte kan bearbeta en begäran. Att förstå de fel som kan returneras av Azure Storage i ett specifikt scenario är användbart för att optimera prestanda.

### <a name="timeout-and-server-busy-errors"></a>Timeout-och Server upptagen-fel

Azure Storage kan begränsa ditt program om det närmar sig begränsningen för skalbarhet. I vissa fall kan Azure Storage inte hantera en begäran på grund av ett tillfälligt tillstånd. I båda fallen kan tjänsten returnera ett 503 ( `Server Busy` ) eller 500 ()- `Timeout` fel. Dessa fel kan också inträffa om tjänsten ombalanserar datapartitioner för att tillåta högre data flöde. Klient programmet bör normalt försöka utföra åtgärden igen som orsakar något av dessa fel. Men om Azure Storage begränsar ditt program eftersom det överskrider skalbarhets målen, eller även om tjänsten inte kunde hantera begäran av någon annan anledning, kan aggressiva återförsök göra problemet sämre. Det rekommenderas att du använder en exponentiell återförsöks princip och klient biblioteken använder detta beteende som standard. Ditt program kan till exempel försöka igen om 2 sekunder, sedan 4 sekunder, sedan 10 sekunder, sedan 30 sekunder, och sedan får upp helt. På så sätt minskar ditt program avsevärt sin belastning på tjänsten, i stället för exacerbating beteende som kan leda till begränsning.

Anslutnings fel kan göras omedelbart, eftersom de inte är resultatet av begränsningen och förväntas vara tillfälliga.

### <a name="non-retryable-errors"></a>Fel som inte går att försöka igen

Klient biblioteken hanterar nya försök med en medvetenhet om vilka fel som kan göras och vilka som inte kan utföras. Men om du anropar Azure Storage REST API direkt finns det vissa fel som du inte bör försöka igen. Till exempel anger ett 400 ( `Bad Request` )-fel att klient programmet skickade en begäran som inte kunde bearbetas eftersom det inte hade det förväntade formuläret. Om du skickar om den här begäran resulterar det i samma svar varje gång, så det finns ingen punkt för att försöka igen. Om du anropar Azure Storage REST API direkt bör du vara medveten om eventuella fel och om de bör göras om.

Mer information om Azure Storage felkoder finns i [status-och felkoder](/rest/api/storageservices/status-and-error-codes2).

## <a name="disable-nagles-algorithm"></a>Inaktivera algoritmen för Nagle

Nagle-algoritmen implementeras ofta i TCP/IP-nätverk som ett sätt att förbättra nätverks prestanda. Men det är inte optimalt i alla situationer (till exempel mycket interaktiva miljöer). Nagle-algoritmen har en negativ inverkan på prestanda för förfrågningar till Azure Table Storage och du bör inaktivera den om möjligt.

## <a name="message-size"></a>Meddelandestorlek

Öka prestanda och skalbarhet för köer när meddelande storleken ökar. Lägg endast till den information mottagaren behöver i ett meddelande.

## <a name="batch-retrieval"></a>Hämta batch

Du kan hämta upp till 32 meddelanden från en kö i en enda åtgärd. Med batch-hämtning kan du minska antalet tur och svar från klient programmet, vilket är särskilt användbart för miljöer, till exempel mobila enheter, med hög latens.

## <a name="queue-polling-interval"></a>Avsöknings intervall för kö

De flesta program söker efter meddelanden från en kö, som kan vara en av de största källorna till transaktioner för programmet. Välj avsöknings intervall: avsökningen för ofta kan leda till att ditt program använder skalbarhets målen för kön. Men vid 200 000 transaktioner för $0,01 (vid tidpunkten för skrivning) är en enskild processor avsökning en gång per sekund under en månad som kostar mindre än 15 cent så kostnaden är inte vanligt vis en faktor som påverkar ditt val av avsöknings intervall.

För uppdaterad kostnads information, se [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).

## <a name="perform-an-update-message-operation"></a>Utföra en uppdaterings meddelande åtgärd

Du kan utföra en uppdaterings åtgärd för att öka tids gränsen för insikter eller uppdatera statusinformation för ett meddelande. Den här metoden kan vara mer effektiv än att ha ett arbets flöde som skickar ett jobb från en kö till nästa, eftersom varje steg i jobbet har slutförts. Ditt program kan spara jobb status i meddelandet och sedan fortsätta att arbeta, i stället för att köa om meddelandet för nästa steg i jobbet varje gång ett steg slutförs. Tänk på att varje uppdaterings meddelande åtgärd räknas mot skalbarhets målet.

## <a name="application-architecture"></a>Programmets arkitektur

Använd köer för att göra din program arkitektur skalbar. Nedan visas några exempel på hur du kan använda köer för att göra programmet mer skalbart:

- Du kan använda köer för att skapa arbets loggar för bearbetning och smidiga arbets belastningar i ditt program. Du kan till exempel köa upp förfrågningar från användare för att utföra processor intensiva arbeten, till exempel ändra storlek på överförda bilder.
- Du kan använda köer för att koppla bort delar av ditt program så att du kan skala dem oberoende av varandra. En webb klient del kan till exempel placera undersöknings resultat från användare i en kö för senare analys och lagring. Du kan lägga till fler arbets Rolls instanser för att bearbeta tjänstedata vid behov.

## <a name="next-steps"></a>Nästa steg

- [Skalbarhets-och prestanda mål för Queue Storage](scalability-targets.md)
- [Skalbarhets-och prestanda mål för standard lagrings konton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Status och felkoder](/rest/api/storageservices/Status-and-Error-Codes2)
