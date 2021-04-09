---
title: Check lista för prestanda och skalbarhet för Blob Storage – Azure Storage
description: En check lista över beprövade metoder för användning med Blob Storage i utveckla program med höga prestanda.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 14da8b6cb695703f1881b6b0b9858772bde386c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544759"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Check lista för prestanda och skalbarhet för Blob Storage

Microsoft har utvecklat ett antal beprövade metoder för att utveckla program med höga prestanda med Blob Storage. Den här check listan identifierar viktiga metoder som utvecklare kan följa för att optimera prestanda. Tänk på dessa metoder när du utformar ditt program och hela processen.

Azure Storage har skalbarhets-och prestanda mål för kapacitet, transaktions hastighet och bandbredd. Mer information om Azure Storage skalbarhets mål finns i [skalbarhets-och prestanda mål för standard lagrings konton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) och [skalbarhets-och prestanda mål för Blob Storage](scalability-targets.md).

## <a name="checklist"></a>Checklista

Den här artikeln ordnar beprövade metoder för prestanda i en check lista som du kan följa när du utvecklar ditt Blob Storage-program.

| Klart | Kategori | Design överväganden |
| --- | --- | --- |
| &nbsp; |Skalbarhets mål |[Kan du utforma ditt program så att det inte använder fler än det högsta antalet lagrings konton?](#maximum-number-of-storage-accounts) |
| &nbsp; |Skalbarhets mål |[Undviker du att du närmar dig kapacitets-och transaktions gränserna?](#capacity-and-transaction-targets) |
| &nbsp; |Skalbarhets mål |[Är ett stort antal klienter som har åtkomst till en enskild BLOB samtidigt?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Skalbarhets mål |[Ligger ditt program i skalbarhets målen för en enda BLOB?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Partitionering |[Är namngivnings konventionen utformad för att möjliggöra bättre belastnings utjämning?](#partitioning) |
| &nbsp; |Nätverk |[Har klient sidan enheter tillräckligt med hög bandbredd och låg latens för att uppnå den prestanda som krävs?](#throughput) |
| &nbsp; |Nätverk |[Har klient sidan enheter en nätverks länk med hög kvalitet?](#link-quality) |
| &nbsp; |Nätverk |[Är klient programmet i samma region som lagrings kontot?](#location) |
| &nbsp; |Direkt klient åtkomst |[Använder du signaturer för delad åtkomst (SAS) och resurs delning mellan ursprung (CORS) för att ge direkt åtkomst till Azure Storage?](#sas-and-cors) |
| &nbsp; |Cachelagring |[Är ditt program caching-data som används ofta och ändras sällan?](#reading-data) |
| &nbsp; |Cachelagring |[Är ditt program batch-uppdateringar att cachelagra dem på klienten och sedan ladda upp dem i större mängder?](#uploading-data-in-batches) |
| &nbsp; |.NET-konfiguration |[Använder du .NET Core 2,1 eller senare för bästa prestanda?](#use-net-core) |
| &nbsp; |.NET-konfiguration |[Har du konfigurerat att klienten ska använda ett tillräckligt antal samtidiga anslutningar?](#increase-default-connection-limit) |
| &nbsp; |.NET-konfiguration |[Har du konfigurerat .NET att använda ett tillräckligt antal trådar för .NET-program?](#increase-minimum-number-of-threads) |
| &nbsp; |Parallellitet |[Har du säkerställt att parallellitet är lämpligt så att du inte överbelastar din klients funktioner eller använder skalbarhets målen?](#unbounded-parallelism) |
| &nbsp; |Verktyg |[Använder du de senaste versionerna av Microsoft-tillhandahållna klient bibliotek och verktyg?](#client-libraries-and-tools) |
| &nbsp; |Antal försök |[Använder du en princip för återförsök med en exponentiell backoff för begränsning av fel och tids gränser?](#timeout-and-server-busy-errors) |
| &nbsp; |Antal försök |[Kan programmet undvika nya försök för fel som inte kan återförsökas?](#non-retryable-errors) |
| &nbsp; |Kopiera blobbar |[Kopierar du blobbar på det mest effektiva sättet?](#blob-copy-apis) |
| &nbsp; |Kopiera blobbar |[Använder du den senaste versionen av AzCopy för Mass kopierings åtgärder?](#use-azcopy) |
| &nbsp; |Kopiera blobbar |[Använder du Azure Data Box-familjen för att importera stora mängder data?](#use-azure-data-box) |
| &nbsp; |Innehålls distribution |[Använder du ett CDN för innehålls distribution?](#content-distribution) |
| &nbsp; |Använd metadata |[Lagrar du ofta använda metadata om blobbar i deras metadata?](#use-metadata) |
| &nbsp; |Ladda upp snabbt |[Kommer du att ladda upp block parallellt när du försöker ladda upp en BLOB snabbt?](#upload-one-large-blob-quickly) |
| &nbsp; |Ladda upp snabbt |[Kommer du att ladda upp blobar parallellt när du snabbt försöker ladda upp många blobbar?](#upload-many-blobs-quickly) |
| &nbsp; |Blobtyp |[Använder du Page blobbar eller block-blobar när det är lämpligt?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Skalbarhets mål

Om ditt program närmar sig eller överskrider något av skalbarhets målen kan det uppstå ökad transaktions fördröjning eller begränsning. När Azure Storage begränsar ditt program börjar tjänsten returnera 503 (servern är upptagen) eller 500 (åtgärds tids gräns) fel koder. Att undvika dessa fel genom att ligga kvar i gränserna för skalbarhets målen är en viktig del i att förbättra programmets prestanda.

Mer information om skalbarhets mål för Kötjänst finns i [Azure Storage skalbarhets-och prestanda mål](../queues/scalability-targets.md#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maximalt antal lagrings konton

Om du närmar dig det maximala antalet lagrings konton som tillåts för en viss kombination av prenumerationer och regioner ska du utvärdera ditt scenario och avgöra om något av följande villkor gäller:

- Använder du lagrings konton för att lagra ohanterade diskar och lägga till diskarna till dina virtuella datorer? I det här scenariot rekommenderar Microsoft att hanterade diskar används. Managed disks skalas automatiskt och utan behov av att skapa och hantera enskilda lagrings konton. Mer information finns i [Introduktion till Azure Managed disks](../../virtual-machines/managed-disks-overview.md)
- Använder du ett lagrings konto per kund, i syfte att isolera data? I det här scenariot rekommenderar Microsoft att du använder en BLOB-behållare för varje kund, i stället för ett helt lagrings konto. Med Azure Storage kan du nu tilldela Azure-roller per container-basis. Mer information finns i [använda Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../common/storage-auth-aad-rbac-portal.md).
- Använder du flera lagrings konton för att Shard för att öka ingress, utgående, I/O-åtgärder per sekund (IOPS) eller kapacitet? I det här scenariot rekommenderar Microsoft att du utnyttjar ökade gränser för lagrings konton för att minska antalet lagrings konton som krävs för din arbets belastning om det är möjligt. Kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) för att begära ökade gränser för ditt lagrings konto. Mer information finns i avsnittet om att [presentera större och högre skalnings lagrings konton](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapacitets-och transaktions mål

Om ditt program närmar sig skalbarhets målen för ett enda lagrings konto bör du överväga att använda någon av följande metoder:  

- Om ditt program träffar transaktions målet bör du överväga att använda Block Blob Storage-konton, som är optimerade för höga transaktions hastigheter och låg och konsekvent svars tid. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).
- Ta hänsyn till arbets belastningen som gör att ditt program närmar sig eller överskrider skalbarhets målet. Kan du utforma det på ett annat sätt att använda mindre bandbredd eller kapacitet eller färre transaktioner?
- Om ditt program måste överskrida ett av skalbarhets målen skapar du flera lagrings konton och partitionerar dina program data på flera lagrings konton. Om du använder det här mönstret ser du till att utforma ditt program så att du kan lägga till fler lagrings konton i framtiden för belastnings utjämning. Själva lagrings konton har ingen annan kostnad än din användning i termer av lagrade data, transaktioner som har gjorts eller överförda data.
- Om ditt program närmar sig bandbredds målen bör du överväga att komprimera data på klient sidan för att minska den bandbredd som krävs för att skicka data till Azure Storage.
    När du komprimerar data kan du spara bandbredd och förbättra nätverks prestanda, men det kan också ha negativa effekter på prestanda. Utvärdera prestanda påverkan för de ytterligare bearbetnings kraven för data komprimering och dekomprimering på klient sidan. Tänk på att lagring av komprimerade data kan göra fel sökningen svårare eftersom det kan vara mer utmanande att visa data med hjälp av standard verktyg.
- Om ditt program närmar sig skalbarhets målen kontrollerar du att du använder en exponentiell backoff för återförsök. Det är bäst att försöka undvika att nå skalbarhets målen genom att implementera rekommendationerna som beskrivs i den här artikeln. Om du använder en exponentiell backoff för återförsök kan du dock förhindra att ditt program försöker igen, vilket kan göra att begränsningen blir sämre. Mer information finns i avsnittet [timeout-fel och servern är upptagen](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Flera klienter som har åtkomst till en enskild BLOB samtidigt

Om du har ett stort antal klienter som använder en enda BLOB samtidigt måste du överväga båda skalbarhets målen per blob och per lagrings konto. Det exakta antalet klienter som kan komma åt en enda BLOB varierar beroende på faktorer som antalet klienter som begär BLOB samtidigt, storleken på blobben och nätverks förhållanden.

Om blobben kan distribueras via ett CDN, till exempel bilder eller videor som hanteras från en webbplats, kan du använda ett CDN. Mer information finns i avsnittet [innehålls distribution](#content-distribution).

I andra scenarier, till exempel vetenskapliga simuleringar där data är konfidentiella, har du två alternativ. Det första är att sprida arbets Belastningens åtkomst så att bloben kan nås under en viss tids period, jämfört med att nås samtidigt. Alternativt kan du tillfälligt kopiera blobben till flera lagrings konton för att öka det totala antalet IOPS per blob och över lagrings konton. Resultaten varierar beroende på programmets beteende, så se till att testa samtidiga samtidighets mönster under designen.

### <a name="bandwidth-and-operations-per-blob"></a>Bandbredd och åtgärder per BLOB

En enda BLOB stöder upp till 500 begär Anden per sekund. Om du har flera klienter som behöver läsa samma blob och du kan överskrida den här gränsen kan du överväga att använda ett Block Blob Storage-konto. Ett Block-Blob Storage-konto ger högre frekvens för begär Anden, eller I/O-åtgärder per sekund (IOPS).

Du kan också använda ett Content Delivery Network (CDN) som Azure CDN för att distribuera åtgärder på blobben. Mer information om Azure CDN finns i [Azure CDN översikt](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Partitionering

Att förstå hur Azure Storage partitioner dina BLOB-data är användbara för att förbättra prestandan. Azure Storage kan hantera data på en enda partition snabbare än data som sträcker sig över flera partitioner. Genom att namnge Blobbarna på rätt sätt kan du förbättra effektiviteten hos Läs begär Anden.

Blob Storage använder ett intervall beroende partitionerings schema för skalning och belastnings utjämning. Varje Blob har en partitionsnyckel som består av det fullständiga BLOB-namnet (konto + container + BLOB). Partitionsnyckel används för att partitionera BLOB-data i intervall. Intervallen är sedan belastningsutjämnade i Blob Storage.

Intervallbaserade partitionering innebär att namngivnings konventioner som använder lexikal sortering (till exempel *dislöneering*, *prestanda*, mina *anställda* osv.) eller tidsstämplar (*log20160101*, *log20160102*, *log20160102* osv.) är mer sannolika i att partitionerna är samplacerade på samma partitions Server. , tills den ökade belastningen kräver att de delas upp i mindre intervall. Att samplacera blobbar på samma partition Server ger bättre prestanda, så en viktig del av prestanda förbättringen innebär namngivning av blobbar på ett sätt som organiserar dem effektivt.

Till exempel kan alla blobbar i en behållare hanteras av en enda server tills belastningen på dessa blobbar kräver ytterligare ombalansering av partitionens intervall. På samma sätt kan en grupp med lätt inlästa konton med namn ordnade i lexikal ordning hanteras av en enda server tills belastningen på ett eller alla dessa konton kräver att de delas upp på flera partitioner.

Varje belastnings Utjämnings åtgärd kan påverka svars tiden för lagrings anrop under åtgärden. Tjänstens möjlighet att hantera en plötslig trafik trafik till en partition begränsas av skalbarheten för en enda partitions Server tills belastnings Utjämnings åtgärden körs i och balanserar om partitionens nyckel intervall.

Du kan följa vissa metod tips för att minska frekvensen för sådana åtgärder.  

- Använd om möjligt BLOB-eller block storlekar som är större än 4 MiB för standard lagrings konton och större än 256 KiB för Premium Storage-konton. Större blob-eller block storlekar aktiverar automatiskt block blobbar med hög genomflöde. Block blobbar med höga data flöden ger högpresterande inmatningar som inte påverkas av namngivning av partitioner.
- Undersök namngivnings konventionen som du använder för konton, behållare, blobbar, tabeller och köer. Överväg att förkorrigera konton, behållare eller BLOB-namn med en tresiffrig hash med en hash-funktion som passar dina behov bäst.
- Om du ordnar dina data med hjälp av tidsstämplar eller numeriska identifierare ser du till att du inte använder ett trafik mönster med enbart tillägg (eller lägga). Dessa mönster är inte lämpliga för ett intervallbaserade partitionerings system. Dessa mönster kan leda till all trafik som går till en enda partition och begränsa systemet från effektiv belastnings utjämning.

    Om du till exempel har dagliga åtgärder som använder en blob med en tidsstämpel som *ÅÅÅÅMMDD*, dirigeras all trafik för den dagliga åtgärden till en enda BLOB, som hanteras av en enda partitions Server. Överväg om gränserna per blob och per partition uppfyller dina behov och Överväg att dela upp den här åtgärden i flera blobbar om det behövs. På samma sätt kan all trafik dirigeras till den sista delen av nyckel namn rummet om du lagrar tids serie data i dina tabeller. Om du använder numeriska ID: n ska du använda prefixet ID med en tresiffrig hash. Om du använder tidsstämplar, ska du använda ett prefix för tidsstämpeln med värdet sekunder, till exempel *ssyyyymmdd*. Om ditt program rutinmässigt utför lista och fråga åtgärder, väljer du en hash-funktion som begränsar antalet frågor. I vissa fall kan ett slumpmässigt prefix vara tillräckligt.
  
- Mer information om partitionerings schema som används i Azure Storage finns i [Azure Storage: en moln lagrings tjänst med hög tillgänglighet med stark konsekvens](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

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

För bred distribution av BLOB-innehåll använder du ett nätverk för innehålls leverans som Azure CDN. Mer information om Azure CDN finns i [Azure CDN](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS och CORS

Anta att du behöver auktorisera kod som till exempel java script som körs i användarens webbläsare eller i en mobil telefon-app för att komma åt data i Azure Storage. En metod är att bygga ett tjänst program som fungerar som en proxy. Användarens enhet autentiseras med tjänsten, som i sin tur ger åtkomst till Azure Storage resurser. På så sätt kan du undvika att exponera lagrings konto nycklar på oskyddade enheter. Den här metoden innebär dock en betydande belastning på tjänst programmet, eftersom alla data som överförs mellan användarens enhet och Azure Storage måste passera igenom tjänst programmet.

Du kan undvika att använda ett tjänst program som proxy för Azure Storage med hjälp av signaturer för delad åtkomst (SAS). Med SAS kan du göra det möjligt för användarens enhet att göra förfrågningar direkt till Azure Storage genom att använda en begränsad åtkomsttoken. Om en användare till exempel vill överföra ett foto till ditt program, kan tjänst programmet generera en SAS och skicka den till användarens enhet. SAS-token kan ge behörighet att skriva till en Azure Storage resurs under en angiven tids period, efter vilken SAS-token upphör att gälla. Mer information om SAS finns i [bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md).  

Normalt tillåter en webbläsare inte Java Script på en sida som finns på en webbplats på en domän för att utföra vissa åtgärder, till exempel Skriv åtgärder, till en annan domän. Den här principen kallas samma-ursprungs princip och förhindrar att ett skadligt skript på en sida får åtkomst till data på en annan webb sida. Samma ursprungs princip kan dock vara en begränsning när du skapar en lösning i molnet. Resurs delning mellan ursprung (CORS) är en webb läsar funktion som gör det möjligt för mål domänen att kommunicera med den webbläsare som den litar på begär Anden från käll domänen.

Anta till exempel att ett webb program som körs i Azure gör en begäran om en resurs till ett Azure Storage-konto. Webb programmet är käll domänen och lagrings kontot är mål domänen. Du kan konfigurera CORS för någon av de Azure Storage-tjänsterna för att kommunicera med den webbläsare som begär från käll domänen är betrodd av Azure Storage. Mer information om CORS finns i [stöd för resurs delning mellan ursprung (CORS) för Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Både SAS och CORS kan hjälpa dig att undvika onödig belastning på ditt webb program.  

## <a name="caching"></a>Cachelagring

Cachelagring spelar en viktig roll i prestandan. I följande avsnitt beskrivs hur du cachelagrar bästa praxis.

### <a name="reading-data"></a>Läsa data

I allmänhet är det bättre att läsa data en gång för att läsa den två gånger. Överväg exemplet på ett webb program som har hämtat en 50 MiB-BLOB från Azure Storage som ska fungera som innehåll till en användare. Vi rekommenderar att programmet cachelagrar blobben lokalt på disk och hämtar sedan den cachelagrade versionen för efterföljande användar förfrågningar.

Ett sätt att undvika att hämta en BLOB om den inte har ändrats sedan den cachelagrades är att kvalificera åtgärden Hämta med ett villkors huvud för ändrings tiden. Om den senaste ändrings tiden är efter den tidpunkt då blobben cachelagrades, hämtas bloben och cachelagras på nytt. Annars hämtas cachelagrad BLOB för optimala prestanda.

Du kan också välja att utforma ditt program för att anta att blobben förblir oförändrad under en kort period efter att det har hämtats. I det här fallet behöver inte programmet kontrol lera om blobben har ändrats under intervallet.

Konfigurations data, uppslags data och andra data som ofta används av programmet är lämpliga kandidater för cachelagring.  

Mer information om hur du använder villkorliga huvuden finns i [Ange villkors rubriker för BLOB service åtgärder](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Laddar upp data i batchar

I vissa fall kan du samla in data lokalt och överföra dem regelbundet i en batch i stället för att ladda upp varje data direkt. Anta till exempel att ett webb program innehåller en loggfil med aktiviteter. Programmet kan antingen ladda upp information om varje aktivitet när det sker i en tabell (vilket kräver många lagrings åtgärder), eller så kan du spara aktivitets information till en lokal loggfil och sedan regelbundet ladda upp all aktivitets information som en avgränsad fil till en blob. Om varje loggpost är 1 KB kan du ladda upp tusentals poster i en enda transaktion. En enskild transaktion stöder överföring av en BLOB på upp till 64 MiB i storlek. Programutvecklaren måste utforma för möjligheten för klient enheter eller överförings problem. Om aktivitets data måste hämtas under ett tidsintervall i stället för en enskild aktivitet, rekommenderas att du använder Blob Storage över Table Storage.

## <a name="net-configuration"></a>.NET-konfiguration

Om du använder .NET Framework visar det här avsnittet flera snabb konfigurations inställningar som du kan använda för att göra betydande prestanda förbättringar.  Om du använder andra språk kan du kontrol lera om liknande koncept gäller för det valda språket.  

### <a name="use-net-core"></a>Använd .NET Core

Utveckla dina Azure Storage-program med .NET Core 2,1 eller senare för att dra nytta av prestanda förbättringar. Att använda .NET Core 3. x rekommenderas när det är möjligt.

Mer information om prestanda förbättringar i .NET Core finns i följande blogg inlägg:

- [Prestanda förbättringar i .NET Core 3,0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Prestanda förbättringar i .NET Core 2,1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Öka standard anslutnings gränsen

I .NET ökar följande kod standard anslutnings gränsen (vanligt vis två i en klient miljö eller tio i en Server miljö) till 100. Normalt bör du ange värdet till ungefär så många trådar som används av ditt program. Ange anslutnings gränsen innan du öppnar några anslutningar.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

För andra programmeringsspråk, se dokumentationen för att avgöra hur du anger anslutnings gränsen.  

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

## <a name="copying-and-moving-blobs"></a>Kopiera och flytta blobbar

Azure Storage innehåller ett antal lösningar för att kopiera och flytta blobbar inom ett lagrings konto, mellan lagrings konton och mellan lokala system och molnet. I det här avsnittet beskrivs några av de här alternativen i förhållande till deras påverkan på prestanda. Information om hur du effektivt överför data till eller från Blob Storage finns i [Välj en Azure-lösning för data överföring](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="blob-copy-apis"></a>BLOB Copy-API: er

Om du vill kopiera blobbar över lagrings konton använder du åtgärden för att [blockera från URL](/rest/api/storageservices/put-block-from-url) . Den här åtgärden kopierar data synkront från en URL-källa till en Block-Blob. Att använda `Put Block from URL` åtgärden kan avsevärt minska nödvändig bandbredd när du migrerar data mellan lagrings konton. Eftersom kopierings åtgärden sker på tjänst sidan behöver du inte hämta och ladda upp data igen.

Använd åtgärden [Kopiera BLOB](/rest/api/storageservices/Copy-Blob) för att kopiera data inom samma lagrings konto. Att kopiera data inom samma lagrings konto slutförs vanligt vis snabbt.  

### <a name="use-azcopy"></a>Använda AzCopy

Kommando rads verktyget AzCopy är ett enkelt och effektivt alternativ för Mass överföring av blobbar till, från och över lagrings konton. AzCopy är optimerat för det här scenariot och kan uppnå höga överföringshastigheter. AzCopy version 10 använder `Put Block From URL` åtgärden för att kopiera BLOB-data mellan lagrings konton. Mer information finns i [Kopiera eller flytta data till Azure Storage med hjälp av AzCopy v10](../common/storage-use-azcopy-v10.md).  

### <a name="use-azure-data-box"></a>Använd Azure Data Box

För att importera stora mängder data till Blob Storage bör du överväga att använda Azure Data Box-serien för offline-överföringar. Microsoft-tillhandahållna Data Box-enhet enheter är ett bra alternativ för att flytta stora mängder data till Azure när du är begränsad till tid, nätverks tillgänglighet eller kostnader. Mer information finns i dokumentationen för [Azure Data](../../databox/index.yml)Center.

## <a name="content-distribution"></a>Innehålls distribution

Ibland måste ett program hantera samma innehåll för många användare (till exempel en produkt demonstrations video som används på Start sidan för en webbplats), som finns i samma eller flera regioner. I det här scenariot använder du en Content Delivery Network (CDN) som Azure CDN för att distribuera BLOB-innehåll geografiskt. Till skillnad från ett Azure Storage-konto som finns i en enda region och som inte kan leverera innehåll med låg latens till andra regioner, Azure CDN använder servrar i flera data center runtom i världen. Dessutom kan ett CDN vanligt vis stödja mycket högre utgående gränser än ett enda lagrings konto.  

Mer information om Azure CDN finns i [Azure CDN](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Använd metadata

Blob Service stöder HEAD-begäranden som kan innehålla BLOB-egenskaper eller metadata. Om ditt program till exempel behöver EXIF-data (det går att byta bild format) från ett foto, kan det Hämta fotot och extrahera det. För att spara bandbredd och förbättra prestanda kan ditt program lagra EXIF-data i blobens metadata när programmet överför fotot. Du kan sedan hämta EXIF-data i metadata med bara en HEAD-begäran. Hämtning av endast metadata och inte det fullständiga innehållet i blobben sparar stor bandbredd och minskar bearbetnings tiden som krävs för att extrahera EXIF-data. Kom ihåg att 8 KiB metadata kan lagras per blob.  

## <a name="upload-blobs-quickly"></a>Ladda upp blobbar snabbt

Om du snabbt vill ladda upp blobar måste du först avgöra om du ska ladda upp en BLOB eller många. Använd anvisningarna nedan för att avgöra vilken metod som ska användas beroende på ditt scenario.  

### <a name="upload-one-large-blob-quickly"></a>Ladda upp en stor BLOB snabbt

Om du snabbt vill överföra en enda stor BLOB kan ett klient program ladda upp sina block eller sidor parallellt, som mindful av skalbarhets målen för enskilda blobbar och lagrings kontot som helhet. Azure Storage klient bibliotek stöder överföring parallellt. Du kan till exempel använda följande egenskaper för att ange antalet samtidiga begär Anden som tillåts i .NET eller Java. Klient bibliotek för andra språk som stöds ger liknande alternativ.

- För .NET anger du egenskapen [BlobRequestOptions. ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) .
- För Java/Android, anropa metoden [BlobRequestOptions. setConcurrentRequestCount (Final Integer concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount) .

### <a name="upload-many-blobs-quickly"></a>Ladda upp många blobbar snabbt

Ladda upp flera blobbar snabbt genom att ladda upp blobar parallellt. Det går fortare att ladda upp parallellt än att ladda upp enskilda blobbar i taget med parallella block överföringar eftersom den sprider uppladdning över flera partitioner i lagrings tjänsten. AzCopy utför överföringar parallellt som standard och rekommenderas för det här scenariot. Mer information finns i [Kom igång med AZCopy](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Välj rätt typ av BLOB

Azure Storage stöder block-blobbar, tillägg av blobbar och sid-blobar. För ett angivet användnings scenario kommer ditt val av Blob-typ att påverka lösningens prestanda och skalbarhet.

Block blobbar är lämpliga när du vill överföra stora mängder data effektivt. Till exempel är ett klient program som överför foton eller video till Blob Storage mål block-blobar.

Lägg till blobar liknar block-blobar på så sätt att de består av block. När du ändrar en tilläggs-BLOB läggs blocken till i slutet av blobben. Tillägg av blobbar är användbara för scenarier som loggning, när ett program behöver lägga till data i en befintlig blob.

Page blobbar är lämpliga om programmet behöver utföra slumpmässiga skrivningar på data. Till exempel lagras virtuella Azure-datorer som Page BLOB-enheter. Mer information finns i [förstå block-blobbar, bifoga blobbar och sid-blobar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Nästa steg

- [Skalbarhets-och prestanda mål för Blob Storage](scalability-targets.md)
- [Skalbarhets-och prestanda mål för standard lagrings konton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Status och felkoder](/rest/api/storageservices/Status-and-Error-Codes2)