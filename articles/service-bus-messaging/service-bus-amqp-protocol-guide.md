---
title: AMQP 1,0 i Azure Service Bus-och Event Hubss protokoll guide | Microsoft Docs
description: Protokoll guide till uttryck och beskrivning av AMQP 1,0 i Azure Service Bus och Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2154221ebfe69b659ff83100ed614133e178ccdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624497"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1,0 i Azure Service Bus-och Event Hubs-protokoll guide

Det avancerade meddelandekö-protokollet 1,0 är ett standardiserat ram-och överförings protokoll för asynkront, säkert och tillförlitligt överföring av meddelanden mellan två parter. Det är det primära protokollet för Azure Service Bus meddelande tjänst och Azure Event Hubs.  

AMQP 1,0 är resultatet av ett brett samarbete mellan företag som samlats mellan leverantörer, till exempel Microsoft och Red Hat, med många meddelande funktioner mellan olika användare, till exempel JP Morgan-förbrukare som representerar finans Services-branschen. Det tekniska standardiserings forumet för AMQP-protokoll och tillägg är OASIS och har uppnått formell godkännande som internationell standard som ISO/IEC 19494:2014. 

## <a name="goals"></a>Mål

Den här artikeln sammanfattar huvud begreppen i AMQP 1,0-meddelande specifikationen tillsammans med tilläggs specifikationer som har utvecklats av [Oasis AMQP Technical Committee](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) och förklarar hur Azure Service Bus implementerar och bygger på dessa specifikationer.

Målet är för alla utvecklare som använder en befintlig AMQP 1,0-klients tack på valfri plattform för att kunna interagera med Azure Service Bus via AMQP 1,0.

Vanliga AMQP 1,0-stackar, till exempel [Apache qpid Proton](https://qpid.apache.org/proton/index.html) eller [AMQP.net lite](https://github.com/Azure/amqpnetlite), implementera alla Core AMQP 1,0-protokoll element som sessioner eller länkar. Dessa grundläggande element omsluts ibland med ett API på en högre nivå. Apache Proton erbjuder även två, det tvingande Messenger API och det omaktiva reaktor-API: et.

I följande diskussion förutsätter vi att hanteringen av AMQP-anslutningar, sessioner och länkar samt hantering av ram överföringar och flödes kontroll hanteras av respektive stack (t. ex. apache Proton-C) och inte kräver mycket särskild uppmärksamhet från programutvecklare. Vi är abstrakta över förekomsten av några API-primitiver som möjligheten att ansluta, och för att skapa någon form av *avsändare* *och abstraktions* objekt, som sedan har någon form av respektive `send()` `receive()` verksamhet.

När du diskuterar avancerade funktioner i Azure Service Bus, till exempel meddelande visning eller hantering av sessioner, förklaras dessa funktioner i AMQP-termer, men också som en överordnad pseudo-implementering ovanpå denna förmodade API-abstraktion.

## <a name="what-is-amqp"></a>Vad är AMQP?

AMQP är ett ram-och överförings protokoll. Ram koppling innebär att den tillhandahåller struktur för binära data strömmar som flödar i båda riktningarna för en nätverks anslutning. Strukturen tillhandahåller deposition för distinkta block med data, som kallas *ramar*, som ska bytas mellan de anslutna parterna. Överförings funktionerna säkerställer att båda kommunicerande parterna kan upprätta en delad förståelse för när ramar ska överföras och när överföringarna skall anses vara fullständiga.

Till skillnad från tidigare utgångna utkast versioner som skapats av AMQP-arbetsgruppen och som fortfarande används av några meddelande utlösare, anger arbets gruppens slutgiltiga och standardiserade AMQP 1,0-protokoll inte förekomsten av en meddelande koordinator eller en viss topologi för entiteter i en meddelande koordinator.

Protokollet kan användas för symmetrisk peer-to-peer-kommunikation, för interaktion med meddelande hanterare som stöder köer och publicera/prenumerera-entiteter, som Azure Service Bus gör. Det kan också användas för interaktion med meddelande infrastruktur där interaktions mönstren skiljer sig från vanliga köer, precis som i Azure Event Hubs. En Event Hub fungerar som en kö när händelser skickas till den, men fungerar som en seriell lagrings tjänst när händelser läses från den. Det liknar en band enhet. Klienten väljer en förskjutning i den tillgängliga data strömmen och betjänar sedan alla händelser från den förskjutningen till den senaste tillgängliga.

AMQP 1,0-protokollet är utformat för att kunna utökas, vilket möjliggör ytterligare specifikationer för att förbättra dess funktioner. De tre tilläggs specifikationerna som diskuteras i det här dokumentet illustrerar detta. För kommunikation över befintlig HTTPS/WebSockets-infrastruktur kan det vara svårt att konfigurera interna AMQP TCP-portar. En bindnings specifikation definierar hur man skiktar AMQP över WebSockets. För att interagera med infrastruktur för meddelanden i en begäran/svars tid i hanterings syfte eller för att tillhandahålla avancerade funktioner definierar AMQP-hanterings specifikationen de nödvändiga bas interaktions primitiverna. För integrering av federerade AMQP definierar anspråksbaserad-Security-specifikationen hur du kopplar och förnyar tokens som är associerade med länkar.

## <a name="basic-amqp-scenarios"></a>Grundläggande AMQP-scenarier

I det här avsnittet beskrivs den grundläggande användningen av AMQP 1,0 med Azure Service Bus, vilket innefattar att skapa anslutningar, sessioner och länkar och överföra meddelanden till och från Service Bus entiteter, till exempel köer, ämnen och prenumerationer.

Den mest auktoritativa källan för att lära dig om hur AMQP fungerar är [AMQP 1,0-specifikationen](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html), men specifikationen skrevs till en exakt guide och inte att lära sig protokollet. Det här avsnittet fokuserar på att introducera så mycket terminologi som behövs för att beskriva hur Service Bus använder AMQP 1,0. För en mer omfattande introduktion till AMQP, samt en bredare diskussion av AMQP 1,0, kan du läsa [den här video kursen][this video course].

### <a name="connections-and-sessions"></a>Anslutningar och sessioner

AMQP anropar *behållare* för program som kommunicerar. de innehåller *noder*, som är de kommunicerande enheterna inuti dessa behållare. En kö kan vara en sådan nod. AMQP tillåter multiplexering, så en enda anslutning kan användas för många kommunikations vägar mellan noderna. till exempel kan en program klient samtidigt tas emot från en kö och skickas till en annan kö över samma nätverks anslutning.

![Diagram som visar sessioner och anslutningar mellan behållare.][1]

Nätverks anslutningen är därmed fäst på behållaren. Den initieras av behållaren i klient rollen och upprättar en utgående TCP-socketanslutning till en behållare i mottagar rollen, som lyssnar efter och accepterar inkommande TCP-anslutningar. Hand skakningen för anslutningen innehåller förhandling av protokoll versionen, deklarationen eller förhandlingen av användningen av TLS/SSL (Transport Level Security) och en hand skakning för autentisering/auktorisering i anslutnings omfånget som baseras på SASL.

Azure Service Bus kräver att TLS används hela tiden. Den stöder anslutningar via TCP-port 5671, där TCP-anslutningen först översätts med TLS innan du anger AMQP-protokoll hand skakningen och även stöder anslutningar via TCP-port 5672, vilket innebär att servern omedelbart erbjuder en obligatorisk uppgradering av anslutningen till TLS med hjälp av den AMQP modellen. AMQP WebSockets-bindningen skapar en tunnel över TCP-port 443 som motsvarar AMQP 5671-anslutningar.

När du har konfigurerat anslutningen och TLS erbjuder Service Bus två alternativ för SASL-mekanismen:

* SASL PLAIN används ofta för att skicka autentiseringsuppgifter för användar namn och lösen ord till en server. Service Bus saknar konton, utan namngivna [säkerhets regler för delad åtkomst](service-bus-sas.md)som ger rättigheter och associeras med en nyckel. Namnet på en regel används som användar namn och nyckeln (som Base64-kodad text) används som lösen ord. De rättigheter som är associerade med den valda regeln styr vilka åtgärder som tillåts på anslutningen.
* SASL anonym används för att kringgå SASL-auktorisering när klienten vill använda den anspråksbaserada säkerhets rollen (CBS) som beskrivs senare. Med det här alternativet kan en klient anslutning upprättas anonymt under en kort tid under vilken klienten bara kan interagera med CBS-slutpunkten och den CBS-handskakningen måste slutföras.

När transport anslutningen har upprättats, bevarar behållarna var och en deklarerar den maximala ram storleken som de är villiga att hantera, och efter en tids gräns för inaktivitet kopplas de bort om det inte finns någon aktivitet på anslutningen.

De deklarerar också hur många samtidiga kanaler som stöds. En kanal är en enkelriktad, utgående, virtuell överförings Sök väg ovanpå anslutningen. En session tar en kanal från var och en av de sammankopplade behållarna för att bilda en dubbelriktad kommunikations väg.

Sessioner har en Window-baserad flödes kontroll modell; När en session skapas, förklarar varje part hur många ramar som det är villig att ta emot i mottagnings fönstret. Som parter som Exchange-ramar fyller de överförda ramarna i fönstret och överföringarna stoppas när fönstret är fullt och tills fönstret återställs eller expanderas med *Flow-Performative* (*Performative* är AMQP-termen för gester på protokoll nivå som utbyts mellan de två parterna).

Den här Window-baserade modellen är ungefär densamma som TCP-konceptet för Window-baserad flödes kontroll, men på sessions nivån i socketen. Protokollets koncept för att tillåta flera samtidiga sessioner finns så att hög prioritets trafik kan leda till att trafiken går förbi tidigare begränsad trafik, t. ex. på en väg Express-Lane.

Azure Service Bus använder för närvarande exakt en session för varje anslutning. Den Service Bus maximala ram storleken är 262 144 byte (256-K byte) för Service Bus standard. Det är 1 048 576 (1 MB) för Service Bus Premium och Event Hubs. Service Bus tillhandahåller inte några begränsade Windows-begränsningar, men återställer fönstret regelbundet som en del av flödes kontrollen på länk nivå (se [Nästa avsnitt](#links)).

Anslutningar, kanaler och sessioner är tillfälliga. Om den underliggande anslutningen komprimeras, anslutningar, TLS-tunnel, SASL-auktoriseringsarkiv och sessioner måste återupprättas.

### <a name="amqp-outbound-port-requirements"></a>AMQP utgående port krav

Klienter som använder AMQP-anslutningar via TCP kräver portarna 5671 och 5672 för att kunna öppnas i den lokala brand väggen. Tillsammans med dessa portar kan du behöva öppna ytterligare portar om [EnableLinkRedirect](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect) -funktionen är aktive rad. `EnableLinkRedirect` är en ny meddelande funktion som hjälper dig att hoppa över ett hopp medan du tar emot meddelanden, vilket hjälper till att öka data flödet. Klienten börjar kommunicera direkt med backend-tjänsten över Port intervallet 104XX, vilket visas i följande bild. 

![Lista över mål portar][4]

En .NET-klient skulle Miss lyckas med en SocketException ("ett försök gjordes att få åtkomst till en socket på ett sätt som tillåts av dess åtkomst behörigheter") om dessa portar blockeras av brand väggen. Funktionen kan inaktive ras genom att ställa in `EnableAmqpLinkRedirect=false` anslutnings strängen, som tvingar klienterna att kommunicera med fjärrtjänsten via port 5671.


### <a name="links"></a>Länkar

AMQP överför meddelanden via länkar. En länk är en kommunikations Sök väg som skapats via en session som möjliggör överföring av meddelanden i en riktning. förhandlingen om överförings status är över länken och dubbelriktad mellan de anslutna parterna.

![Skärm bild som visar en session som driver en länk anslutning mellan två behållare.][2]

Länkar kan skapas antingen av en behållare när som helst och över en befintlig session, vilket gör att AMQP skiljer sig från flera andra protokoll, inklusive HTTP och MQTT, där överföringen av överföring och överförings Sök väg är ett exklusivt privilegium hos parten som skapar socket-anslutningen.

Behållaren för att initiera den sammanlänkade behållaren ber den motsatta behållaren att acceptera en länk och väljer en roll för antingen Sender eller mottagare. En behållare kan därför starta skapandet av enkelriktade och dubbelriktade kommunikations vägar, med de senare modellerade som länkar till länkar.

Länkar har namnet och associeras med noder. Som anges i början är noderna de kommunicerande enheterna i en behållare.

I Service Bus motsvarar en nod direkt till en kö, ett ämne, en prenumeration eller en obeställbara meddelanden kön-underkö i en kö eller prenumeration. Det nodnamn som används i AMQP är därför det relativa namnet på entiteten i namn området Service Bus. Om en kö har namnet `myqueue` , det är även dess AMQP-nodnamn. En ämnes prenumeration följer HTTP API-konventionen genom att sorteras i en resurs samling med prenumerationer och **därför har** en **prenumeration på ett ämne i ämnet** AMQP Node-namn ( **ämne/prenumerationer/sub**).

Den anslutande klienten måste också använda ett lokalt nodnamn för att skapa länkar. Service Bus är inte förvarad om dessa nodnamn och tolkar dem inte. AMQP 1,0-klient stackar använder vanligt vis ett schema för att säkerställa att dessa tillfälliga nodnamn är unika i klientens omfattning.

### <a name="transfers"></a>Överlåtelse

När en länk har upprättats kan meddelanden överföras via den länken. I AMQP körs en överföring med en explicit protokoll-gest ( *överförings* -Performative) som flyttar ett meddelande från Sender till mottagare över en länk. En överföring är slutförd när den är "kvittad", vilket innebär att båda parter har upprättat en delad förståelse för resultatet av överföringen.

![Ett diagram som visar överföring av ett meddelande mellan avsändaren och mottagaren och dispositions resultatet från det.][3]

I det enklaste fallet kan avsändaren välja att skicka meddelanden "förbetalt", vilket innebär att klienten inte är intresse rad av resultatet och mottagaren inte ger någon feedback om resultatet av åtgärden. Det här läget stöds av Service Bus på AMQP-protokollets nivå, men inte exponeras i någon av klient-API: erna.

Det vanliga fallet är att meddelanden skickas unkvittas, och mottagaren anger sedan godkännande eller avvisande med *dispositionen* Performative. Avslag sker när mottagaren inte kan godkänna meddelandet av någon anledning, och avvisnings meddelandet innehåller information om orsaken, som är en fel struktur som definieras av AMQP. Om meddelanden avvisas på grund av interna fel i Service Bus, returnerar tjänsten extra information i den strukturen som kan användas för att tillhandahålla diagnos tips som stöd för personal om du skickar support förfrågningar. Du får mer information om felen senare.

En särskild form av avslag är den *släpp* statusen, som anger att mottagaren inte har någon teknisk inblandning i överföringen, men inte heller något som är intresse rad av överföringen. Detta gäller till exempel när ett meddelande levereras till en Service Bus-klient och klienten väljer att överge meddelandet, eftersom det inte kan utföra arbetet som härrör från bearbetning av meddelandet. själva meddelande leveransen är inte ett fel. En variant av detta tillstånd är det *ändrade* tillstånd, vilket gör det möjligt att ändra meddelandet som det släpps. Detta tillstånd används inte av Service Bus för närvarande.

AMQP 1,0-specifikationen definierar ett ytterligare dispositions tillstånd som kallas *mottaget*, som specifikt hjälper till att hantera länk återställning. Med länk återställning kan du skapa en länks tillstånd och eventuella väntande leveranser ovanpå en ny anslutning och session, när den tidigare anslutningen och sessionen förlorades.

Service Bus stöder inte länk återställning. om klienten förlorar anslutningen till Service Bus med en avbruten överföring av meddelanden, förloras meddelande överföringen och klienten måste återansluta, återupprätta länken och sedan försöka överföra igen.

I sådana fall, Service Bus och Event Hubs support "minst en gång", där avsändaren kan vara säker för att meddelandet har lagrats och godkänts, men inte stöder "exakt en gång" på AMQP-nivån, där systemet försöker återställa länken och fortsätta att förhandla om leverans status för att undvika att meddelande överföringen ska ske.

För att kompensera för eventuella dubbla sändningar kan Service Bus stödja dubblettidentifiering som en valfri funktion i köer och ämnen. Dubblettidentifiering registrerar meddelande-ID för alla inkommande meddelanden under en användardefinierad tids period och släpper sedan tyst alla meddelanden som skickas med samma meddelande-ID under samma fönster.

### <a name="flow-control"></a>Flödeskontroll

Förutom den flödes kontroll modell på sessionshantering som tidigare diskuterats, har varje länk sin egen flödes kontroll modell. Flödes kontroll på sessions nivå skyddar behållaren från att behöva hantera för många ramar samtidigt, flödes kontroll på länknivå ger programmet en kostnad för hur många meddelanden den vill hantera från en länk och när.

![Skärm bild av en logg som visar källa, mål, källport, målport och protokoll namn. På den första raden anges mål porten 10401 (0x28 A 1) i svart.][4]

På en länk kan överföringar bara ske när avsändaren har tillräckligt med *länk kredit*. Länk kredit är en räknare som anges av mottagaren med hjälp av *Flow* -Performative, som är begränsad till en länk. När avsändaren tilldelas länk kredit försöker den använda krediten genom att leverera meddelanden. Varje meddelande leverans minskar den återstående länk krediten med 1. När länk krediten används, stoppas leveranser.

När Service Bus finns i mottagarens roll, ger den omedelbart avsändaren med gott länk kredit, så att meddelanden kan skickas omedelbart. När länk kredit används skickar Service Bus ibland en *Flow* -Performative till avsändaren för att uppdatera länk kredit balansen.

I avsändarens roll Service Bus skickar meddelanden för att använda en utestående länk kredit.

Ett "ta emot"-anrop på API-nivå översätts till en *Flow* -Performative som skickas till Service Bus av klienten, och Service Bus använder den krediten genom att ta det första tillgängliga, olåsta meddelandet från kön, låsa det och överföra det. Om det inte finns något meddelande tillgängligt för leverans blir eventuell utestående kredit av en länk som upprättats med den specifika entiteten inloggad i den mottagande ordningen, och meddelanden låses och överförs när de blir tillgängliga, för att använda valfri utestående kredit.

Låset på ett meddelande släpps när överföringen är uppdelad i något av Terminal-tillstånden *accepteras*, *avvisas* eller *släpps*. Meddelandet tas bort från Service Bus när Terminal-statusen *accepteras*. Den finns kvar i Service Bus och levereras till nästa mottagare när överföringen når något av de andra tillstånden. Service Bus flyttar automatiskt meddelandet till entitetens obeställbara meddelanden kön-kö när det når maximalt antal tillåtna leveranser för entiteten på grund av upprepade avslag eller uppdateringar.

Även om Service Bus-API: er inte direkt exponerar ett sådant alternativ idag, kan en AMQP-protokoll på lägre nivå använda länk kredit modellen för att göra "pull-Style"-interaktionen att skicka en kredit enhet för varje Receive-begäran till en "push-Style"-modell genom att utfärda ett stort antal länk krediter och sedan ta emot meddelanden när de blir tillgängliga utan ytterligare interaktion Push-teknik stöds via egenskaps inställningarna [MessagingFactory. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) eller [MessageReceiver. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) . När de inte är noll använder AMQP-klienten den som länk kredit.

I det här sammanhanget är det viktigt att förstå att klockan för att stoppa låset på meddelandet i entiteten startar när meddelandet tas från entiteten, inte när meddelandet placeras i kabeln. När klienten anger att den är redo att ta emot meddelanden genom att skicka länk kredit, förväntas det därför vara aktivt att ta emot meddelanden i nätverket och vara redo att hantera dem. Annars kan meddelande låset ha gått ut innan meddelandet har levererats. Användning av länk kredit flödes kontroll bör direkt avspegla den omedelbara beredskap för att hantera tillgängliga meddelanden som skickas till mottagaren.

I sammanfattningen ger följande avsnitt en schematisk översikt över Performative-flödet under olika API-interaktioner. I varje avsnitt beskrivs en annan logisk åtgärd. Vissa av dessa interaktioner kan vara "Lazy", vilket innebär att de bara kan utföras när de behövs. Att skapa en meddelande avsändare kanske inte orsakar en nätverks interaktion förrän det första meddelandet skickas eller begärs.

Pilarna i följande tabell visar Performative flödes riktning.

#### <a name="create-message-receiver"></a>Skapa meddelande mottagare

| Klient | Service Bus |
| --- | --- |
| --> Anslut (<br/>namn = {Link Name},<br/>referens = {numerisk referens},<br/>roll =**mottagare**,<br/>Källa = {entitetsnamn},<br/>Target = {klient länk-ID}<br/>) |Klienten ansluter till entiteten som mottagare |
| Service Bus svar med kopplingens slut |<--attach (<br/>namn = {Link Name},<br/>referens = {numerisk referens},<br/>roll =**avsändare**,<br/>Källa = {entitetsnamn},<br/>Target = {klient länk-ID}<br/>) |

#### <a name="create-message-sender"></a>Skapa meddelande avsändare

| Klient | Service Bus |
| --- | --- |
| --> Anslut (<br/>namn = {Link Name},<br/>referens = {numerisk referens},<br/>roll =**avsändare**,<br/>Källa = {klient länk-ID},<br/>Target = {entitetsnamn}<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--attach (<br/>namn = {Link Name},<br/>referens = {numerisk referens},<br/>roll =**mottagare**,<br/>Källa = {klient länk-ID},<br/>Target = {entitetsnamn}<br/>) |

#### <a name="create-message-sender-error"></a>Skapa meddelande avsändare (fel)

| Klient | Service Bus |
| --- | --- |
| --> Anslut (<br/>namn = {Link Name},<br/>referens = {numerisk referens},<br/>roll =**avsändare**,<br/>Källa = {klient länk-ID},<br/>Target = {entitetsnamn}<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--attach (<br/>namn = {Link Name},<br/>referens = {numerisk referens},<br/>roll =**mottagare**,<br/>Källa = null,<br/>Target = null<br/>)<br/><br/><--loss (<br/>referens = {numerisk referens},<br/>Closed =**True**,<br/>fel = {Error info}<br/>) |

#### <a name="close-message-receiversender"></a>Stäng meddelande mottagare/avsändare

| Klient | Service Bus |
| --- | --- |
| --> koppla från (<br/>referens = {numerisk referens},<br/>stängt =**Sant**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--loss (<br/>referens = {numerisk referens},<br/>stängt =**Sant**<br/>) |

#### <a name="send-success"></a>Skicka (lyckades)

| Klient | Service Bus |
| --- | --- |
| --> överföring (<br/>leverans-ID = {numerisk referens},<br/>leverans-tag = {binär referens},<br/>Kvittat =**falskt**,, mer =**falskt**,<br/>State =**Null**,<br/>Resume =**false**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--disposition (<br/>roll = mottagare,<br/>första = {Delivery ID},<br/>senaste = {Delivery ID},<br/>Kvittat =**Sant**,<br/>State =**accepterad**<br/>) |

#### <a name="send-error"></a>Skicka (fel)

| Klient | Service Bus |
| --- | --- |
| --> överföring (<br/>leverans-ID = {numerisk referens},<br/>leverans-tag = {binär referens},<br/>Kvittat =**falskt**,, mer =**falskt**,<br/>State =**Null**,<br/>Resume =**false**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<--disposition (<br/>roll = mottagare,<br/>första = {Delivery ID},<br/>senaste = {Delivery ID},<br/>Kvittat =**Sant**,<br/>State =**nekad**(<br/>fel = {Error info}<br/>)<br/>) |

#### <a name="receive"></a>Ta emot

| Klient | Service Bus |
| --- | --- |
| --> flöde (<br/>länk – kredit = 1<br/>) |Ingen åtgärd |
| Ingen åtgärd |< överföring (<br/>leverans-ID = {numerisk referens},<br/>leverans-tag = {binär referens},<br/>Kvittat =**falskt**,<br/>More =**false**,<br/>State =**Null**,<br/>Resume =**false**<br/>) |
| --> disposition (<br/>roll =**mottagare**,<br/>första = {Delivery ID},<br/>senaste = {Delivery ID},<br/>Kvittat =**Sant**,<br/>State =**accepterad**<br/>) |Ingen åtgärd |

#### <a name="multi-message-receive"></a>Mottagning av flera meddelanden

| Klient | Service Bus |
| --- | --- |
| --> flöde (<br/>länk – kredit = 3<br/>) |Ingen åtgärd |
| Ingen åtgärd |< överföring (<br/>leverans-ID = {numerisk referens},<br/>leverans-tag = {binär referens},<br/>Kvittat =**falskt**,<br/>More =**false**,<br/>State =**Null**,<br/>Resume =**false**<br/>) |
| Ingen åtgärd |< överföring (<br/>Delivery-ID = {numeric-referens + 1},<br/>leverans-tag = {binär referens},<br/>Kvittat =**falskt**,<br/>More =**false**,<br/>State =**Null**,<br/>Resume =**false**<br/>) |
| Ingen åtgärd |< överföring (<br/>Delivery-ID = {numeric-referens + 2},<br/>leverans-tag = {binär referens},<br/>Kvittat =**falskt**,<br/>More =**false**,<br/>State =**Null**,<br/>Resume =**false**<br/>) |
| --> disposition (<br/>roll = mottagare,<br/>första = {Delivery ID},<br/>senaste = {Delivery ID + 2},<br/>Kvittat =**Sant**,<br/>State =**accepterad**<br/>) |Ingen åtgärd |

### <a name="messages"></a>Meddelanden

I följande avsnitt beskrivs vilka egenskaper som finns i standard avsnittet för AMQP-meddelanden som används av Service Bus och hur de mappar till Service Bus API-uppsättningen.

Alla egenskaper som programmet måste definiera ska mappas till AMQP- `application-properties` kartan.

#### <a name="header"></a>sidhuvud

| Fältnamn | Användning | API-namn |
| --- | --- | --- |
| konsumtion |- |- |
| prioritet |- |- |
| ttl |TTL-tid för det här meddelandet |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| första hämtnings bara |- |- |
| leverans antal |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Fältnamn | Användning | API-namn |
| --- | --- | --- |
| meddelande-ID |Programdefinierad, fri format identifierare för det här meddelandet. Används för dubblettidentifiering. |[Meddelande](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| användar-id |Programdefinierad användar identifierare, tolkas inte av Service Bus. |Inte tillgänglig via Service Bus API. |
| på |Programdefinierad mål identifierare, tolkas inte av Service Bus. |[Till](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Ämne |Programdefinierad identifierare för meddelande syfte, tolkas inte av Service Bus. |[Etikett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| svar till |Programdefinierad svars Sök vägs indikator, tolkas inte av Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| korrelations-id |Programdefinierad korrelations identifierare, tolkas inte av Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| innehålls typ |Programdefinierad innehålls typ indikator för bröd texten, tolkas inte av Service Bus. |[Innehålls](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| innehålls kodning |Programdefinierad innehålls kodnings indikator för bröd texten, tolkas inte av Service Bus. |Inte tillgänglig via Service Bus API. |
| absolut-förfallo tid |Deklarerar vid vilken absolut som den absoluta meddelandet upphör att gälla. Ignorerades vid indata (sidhuvud-TTL observeras), auktoritativ på utdata. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| skapande tid |Deklarerar vid vilken tidpunkt meddelandet skapades. Används inte av Service Bus |Inte tillgänglig via Service Bus API. |
| grupp-ID |Programdefinierad identifierare för en relaterad uppsättning meddelanden. Används för Service Bus-sessioner. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| grupp-sekvens |Räknare som identifierar meddelandets relativa ordnings nummer i en session. Ignoreras av Service Bus. |Inte tillgänglig via Service Bus API. |
| svar till grupp-ID |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Meddelande anteckningar

Det finns några andra egenskaper för Service Bus-meddelanden, som inte är en del av egenskaperna för AMQP-meddelanden och skickas tillsammans som `MessageAnnotations` i meddelandet.

| Antecknings kart nyckel | Användning | API-namn |
| --- | --- | --- |
| x-opt-Scheduled-Queue-Time-Time | Deklarerar vid vilken tidpunkt meddelandet ska visas i entiteten |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc) |
| x-opt-partition-nyckel | Programdefinierad nyckel som avgör vilken partition som meddelandet ska landa i. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey) |
| x-opt-Drive-partition-Key | Programdefinierat partitions nyckel värde när en transaktion ska användas för att skicka meddelanden via en överförings kön. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey) |
| x-opt-queued-Time | Tjänstedefinierad UTC-tid som representerar den faktiska tiden för att köa meddelandet. Ignoreras vid indatamängd. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) |
| x-opt-Sequence-Number | Det användardefinierade unika numret som tilldelats ett meddelande. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) |
| x-opt-offset | Tjänstedefinierat ordnings nummer i kön för meddelandet. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber) |
| x-opt-Locked-fram till | Tjänst definierad. Datum och tid till vilket meddelandet kommer att låsas i kön/prenumerationen. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc) |
| x-opt-obeställbara meddelanden kön-source | Tjänst definierad. Källan till det ursprungliga meddelandet om meddelandet tas emot från kön för obeställbara meddelanden. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource) |

### <a name="transaction-capability"></a>Transaktions kapacitet

En transaktion grupperar två eller flera åtgärder tillsammans i en körning. En sådan transaktion måste efter beskaffenhet säkerställa att alla åtgärder som hör till en specifik grupp av åtgärder antingen lyckas eller Miss lyckas gemensamt.
Åtgärderna grupperas efter en identifierare `txn-id` .

Vid transaktionell interaktion fungerar klienten som en `transaction controller` , som styr vilka åtgärder som ska grupperas tillsammans. Service Bus-tjänsten fungerar som en `transactional resource` och utför arbete som begärt av `transaction controller` .

Klienten och tjänsten kommunicerar via en `control link` , som upprättas av-klienten. `declare`-Och `discharge` -meddelandena skickas av kontrollanten över kontroll länken för att allokera och slutföra transaktioner (de representerar inte avgränsningen av transaktions arbetet). Själva skicka/ta emot utförs inte på den här länken. Varje transaktions åtgärd som begärs identifieras explicit med det önskade `txn-id` och kan därför uppstå på en länk i anslutningen. Om kontroll länken stängs samtidigt som det finns icke-avladdade transaktioner som skapas, återställs alla sådana transaktioner omedelbart och försök att utföra ytterligare transaktions arbete på dem leder till ett haveri. Meddelanden på kontroll länken får inte förlösas.

Varje anslutning måste initiera en egen kontroll länk för att kunna starta och avsluta transaktioner. Tjänsten definierar ett särskilt mål som fungerar som en `coordinator` . Klienten/styrenheten upprättar en kontroll länk till målet. Kontroll länken är utanför gränserna för en entitet, det vill säga samma kontroll länk kan användas för att initiera och avsluta transaktioner för flera entiteter.

#### <a name="starting-a-transaction"></a>Starta en transaktion

För att starta transaktions arbetet. kontrollanten måste hämta en `txn-id` från koordinatorn. Det gör detta genom att skicka ett `declare` typ meddelande. Om deklarationen lyckas svarar koordinatorn med ett dispositions resultat som har tilldelats `txn-id` .

| Klient (Controller) | Riktning | Service Bus (koordinator) |
| :--- | :---: | :--- |
| Fäst<br/>namn = {Link Name},<br/>... ,<br/>roll =**avsändare**,<br/>Target =**koordinator**<br/>) | ------> |  |
|  | <------ | Fäst<br/>namn = {Link Name},<br/>... ,<br/>Target = koordinator ()<br/>) |
| Gireringsblankett<br/>leverans-ID = 0,...)<br/>{AmqpValue (**Declare ()**)}| ------> |  |
|  | <------ | disposition <br/> första = 0, senaste = 0, <br/>State =**deklarerad**(<br/>**TXN-ID**= {TRANSAKTIONS-ID}<br/>))|

#### <a name="discharging-a-transaction"></a>Avladda en transaktion

Styrenheten avslutar transaktions arbetet genom att skicka ett `discharge` meddelande till koordinatorn. Kontrollanten anger att det vill spara eller återställa transaktions arbetet genom att ange `fail` flaggan på ansvars texten. Om koordinatorn inte kan slutföra ansvaret, avvisas meddelandet med det här resultatet som bär `transaction-error` .

> Obs!: misslyckande = sant refererar till återställning av en transaktion och redundansväxla = falskt refererar till genomför.

| Klient (Controller) | Riktning | Service Bus (koordinator) |
| :--- | :---: | :--- |
| Gireringsblankett<br/>leverans-ID = 0,...)<br/>{AmqpValue (declare ())}| ------> |  |
|  | <------ | disposition <br/> första = 0, senaste = 0, <br/>State = deklarerad (<br/>TXN-ID = {transaktions-ID}<br/>))|
| | . . . <br/>Transaktions arbete<br/>på andra länkar<br/> . . . |
| Gireringsblankett<br/>leverans-ID = 57,...)<br/>{ AmqpValue (<br/>**Urladdning (TXN-ID = 0, <br/> misslyckande = falskt)**)}| ------> |  |
| | <------ | disposition <br/> första = 57, sista = 57, <br/>State =**Accepted ()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Skicka ett meddelande i en transaktion

Alla transaktions arbeten görs med det transaktions leverans tillstånd `transactional-state` som bär TXN-ID: t. Vid sändning av meddelanden utförs transaktions statusen av meddelandets överförings ram. 

| Klient (Controller) | Riktning | Service Bus (koordinator) |
| :--- | :---: | :--- |
| Gireringsblankett<br/>leverans-ID = 0,...)<br/>{AmqpValue (declare ())}| ------> |  |
|  | <------ | disposition <br/> första = 0, senaste = 0, <br/>State = deklarerad (<br/>TXN-ID = {transaktions-ID}<br/>))|
| Gireringsblankett<br/>Referens = 1,<br/>leverans-ID = 1, <br/>**tillstånd = <br/> TransactionalState ( <br/> TXN-ID = 0)**)<br/>innehållet| ------> |  |
| | <------ | disposition <br/> första = 1, senaste = 1, <br/>State =**TransactionalState ( <br/> TXN-ID = 0, <br/> utfall = Accepted ()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Att avyttra ett meddelande i en transaktion

Meddelande dispositionen innehåller åtgärder som `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer` . Om du vill utföra dessa åtgärder i en transaktion måste du skicka `transactional-state` med dispositionen.

| Klient (Controller) | Riktning | Service Bus (koordinator) |
| :--- | :---: | :--- |
| Gireringsblankett<br/>leverans-ID = 0,...)<br/>{AmqpValue (declare ())}| ------> |  |
|  | <------ | disposition <br/> första = 0, senaste = 0, <br/>State = deklarerad (<br/>TXN-ID = {transaktions-ID}<br/>))|
| | <------ |Gireringsblankett<br/>referens = 2,<br/>leverans-ID = 11, <br/>State = null)<br/>innehållet|  
| disposition <br/> första = 11, senaste = 11, <br/>State =**TransactionalState ( <br/> TXN-ID = 0, <br/> utfall = Accepted ()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Avancerade Service Bus funktioner

I det här avsnittet beskrivs avancerade funktioner i Azure Service Bus som baseras på utkast till tillägg till AMQP, som för närvarande utvecklas i OASIS tekniska kommitté för AMQP. Service Bus implementerar de senaste versionerna av dessa utkast och antar ändringar som gjorts när dessa utkast når standard status.

> [!NOTE]
> Service Bus avancerade åtgärder för meddelande hantering stöds via ett fråge-/svars mönster. Informationen om dessa åtgärder beskrivs i artikeln [AMQP 1,0 i Service Bus: Request-Response-based Operations](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>Hantering av AMQP

Hanterings specifikationen för AMQP är den första av de utkast tillägg som beskrivs i den här artikeln. Den här specifikationen definierar en uppsättning protokoll som ligger ovanpå AMQP-protokollet som tillåter hantering av kommunikation med meddelande infrastrukturen över AMQP. Specifikationen definierar generiska åtgärder som att *skapa*, *läsa*, *Uppdatera* och *ta bort* för att hantera entiteter i en meddelande infrastruktur och en uppsättning frågor.

Alla dessa gester kräver en förfrågan/svar-interaktion mellan klienten och meddelande infrastrukturen, och därför definierar specifikationen hur interaktions mönstret ska modelleras över AMQP: klienten ansluter till meddelande infrastrukturen, initierar en session och skapar sedan ett länkat länk. På en länk fungerar klienten som avsändare och på den andra fungerar den som mottagare, vilket skapar ett länk par som kan fungera som en dubbelriktad kanal.

| Logisk åtgärd | Klient | Service Bus |
| --- | --- | --- |
| Skapa sökväg för svar på begäran |--> Anslut (<br/>namn = {*Link Name*},<br/>referens = {*numerisk referens*},<br/>roll =**avsändare**,<br/>källa =**Null**,<br/>Target = "min entitet/$management"<br/>) |Ingen åtgärd |
| Skapa sökväg för svar på begäran |Ingen åtgärd |\<-- attach(<br/>namn = {*Link Name*},<br/>referens = {*numerisk referens*},<br/>roll =**mottagare**,<br/>Källa = null,<br/>Target = "min entitet"<br/>) |
| Skapa sökväg för svar på begäran |--> Anslut (<br/>namn = {*Link Name*},<br/>referens = {*numerisk referens*},<br/>roll =**mottagare**,<br/>källa = "min entitet/$management",<br/>Target = "klientens $ ID"<br/>) | |
| Skapa sökväg för svar på begäran |Ingen åtgärd |\<-- attach(<br/>namn = {*Link Name*},<br/>referens = {*numerisk referens*},<br/>roll =**avsändare**,<br/>källa = "min entitet",<br/>Target = "klientens $ ID"<br/>) |

Med det här paret länkar kan implementeringen av begäran/svar vara enkel: en begäran är ett meddelande som skickas till en entitet inuti meddelande infrastrukturen som förstår det här mönstret. I det här meddelandet är *svars* fältet i avsnittet *Egenskaper* inställt på *mål* -ID: t för den länk som svaret ska skickas till. Hanterings enheten bearbetar begäran och levererar sedan svaret över länken vars *mål* -ID matchar den angivna *svars* identifieraren.

Mönstret uppenbart kräver att klient behållaren och den identifierade klient-ID: t för svars målet är unika för alla klienter och, av säkerhets skäl, också svårt att förutse.

De meddelande utbyten som används för hanterings protokollet och för alla andra protokoll som använder samma mönster sker på program nivå. de definierar inte nya AMQP-gester på protokoll nivå. Detta är avsiktligt, så att program kan dra nytta av dessa tillägg med kompatibla AMQP 1,0-stackar.

Service Bus implementerar för närvarande inte någon av kärn funktionerna i hanterings specifikationen, men mönstret för begäran/svar som definieras av hanterings specifikationen är grundläggande för den anspråksbaserad säkerhets funktionen och för nästan alla avancerade funktioner som beskrivs i följande avsnitt:

### <a name="claims-based-authorization"></a>Anspråksbaserad auktorisering

AMQP för anspråk-baserad auktorisering (CBS) bygger på hanterings specifikationens fråge-och svars mönster och beskriver en generaliserad modell för hur du använder federerade säkerhetstoken med AMQP.

Standard säkerhets modellen för AMQP som diskuteras i introduktionen baseras på SASL och integreras med AMQP-handskakningen. Att använda SASL har fördelen att den tillhandahåller en utöknings bar modell för vilken en uppsättning mekanismer har definierats från vilka alla protokoll som formellt lutar SASL kan dra nytta av. Bland dessa mekanismer är "PLAIn" för överföring av användar namn och lösen ord, "externt" för att binda till säkerhet på TLS-nivå, "Anonym" för att uttrycka frånvaron av explicit autentisering/auktorisering, samt en mängd olika ytterligare metoder som tillåter att autentiseringsuppgifter för autentisering och/eller auktorisering skickas eller tokens.

AMQP SASL-integrering har två nack delar:

* Alla autentiseringsuppgifter och tokens är begränsade till anslutningen. En infrastruktur för meddelanden kan vilja tillhandahålla differentierad åtkomst kontroll per entitet. Du kan till exempel tillåta Bearer för en token att skicka till kö A men inte till kö B. Med behörighets kontexten fäst vid anslutningen går det inte att använda en enda anslutning och använder sedan olika åtkomsttoken för kö A och kö B.
* Åtkomsttoken är vanligt vis bara giltiga under en begränsad tid. Den här giltighets tiden kräver att användaren regelbundet återhämtar tokens och ger en möjlighet till token Issuer för att neka utfärdande av en ny token om användarens åtkomst behörigheter har ändrats. AMQP-anslutningar kan ha varit efter långa tids perioder. SASL-modellen ger bara möjlighet att ange en token vid anslutnings tiden, vilket innebär att meddelande infrastrukturen antingen måste koppla från klienten när token går ut eller om den måste acceptera risken för att kommunikationen med en klient som har åtkomst behörighet kan ha återkallats i Interim.

AMQP CBS-specifikationen, som implementeras av Service Bus, aktiverar en elegant lösning för båda dessa problem: det gör att en klient kan koppla åtkomsttoken till varje nod och uppdatera dessa token innan de upphör att gälla, utan att avbryta meddelande flödet.

CBS definierar en virtuell hanterings nod med namnet *$CBS* som ska tillhandahållas av meddelande infrastrukturen. Noden hantering accepterar tokens på uppdrag av andra noder i meddelande infrastrukturen.

Protokoll-gesten är ett fråge-/svars utbyte som definieras av hanterings specifikationen. Det innebär att klienten upprättar ett länk par med *$CBS* -noden och sedan skickar en begäran på den utgående länken och väntar sedan på svar på den inkommande länken.

Begär ande meddelandet har följande program egenskaper:

| Nyckel | Valfritt | Värdetyp | Värde innehåll |
| --- | --- | --- | --- |
| operation |Inga |sträng |**Skicka token** |
| typ |Inga |sträng |Typ av token som ska beställas. |
| name |Inga |sträng |Mål gruppen som token gäller. |
| dag |Ja |timestamp |Utgångs tiden för token. |

Egenskapen *Name* identifierar den entitet som token ska associeras med. I Service Bus är det sökvägen till kön, eller ämnet/prenumerationen. *Typ* egenskapen identifierar tokentypen:

| Tokentyp | Beskrivning av token | Typ av brödtext | Kommentarer |
| --- | --- | --- | --- |
| AMQP: JWT |JSON Web Token (JWT) |AMQP-värde (sträng) |Ännu inte tillgängligt. |
| AMQP: SWT |Enkel webb-token (SWT) |AMQP-värde (sträng) |Stöds endast för SWT-token som utfärdats av AAD/ACS |
| Service Bus. Windows. net: sastoken |Service Bus SAS-token |AMQP-värde (sträng) |- |

Tokens ger behörighet. Service Bus vet om tre grundläggande rättigheter: "Skicka" aktiverar sändning, "lyssna" aktiverar mottagning och "hantera" aktiverar manipulering av entiteter. SWT-token som utfärdas av AAD/ACS inkluderar uttryckligen de rättigheterna som anspråk. Service Bus SAS-token refererar till regler som kon figurer ATS i namn området eller entiteten och dessa regler har kon figurer ATS med behörighet. Att signera token med nyckeln som är kopplad till regeln gör att token uttrycker respektive rättigheter. Den token som är associerad med en entitet med hjälp av *-token* tillåter att den anslutna klienten interagerar med entiteten per token-rättigheter. En länk där klienten tar *avsändarens* roll kräver rättigheten "Send". att ta på *mottagar* rollen kräver rättigheten "lyssna".

Svarsmeddelandet har följande *program egenskaps* värden

| Nyckel | Valfritt | Värdetyp | Värde innehåll |
| --- | --- | --- | --- |
| status kod |Inga |int |HTTP-svarskod **[RFC2616]**. |
| status-Beskrivning |Ja |sträng |Beskrivning av status. |

Klienten kan anropa in *-token* upprepade gånger och för alla entiteter i meddelande infrastrukturen. Token är begränsade till den aktuella klienten och fästs på den aktuella anslutningen, vilket innebär att servern släpper alla kvarhållna token när anslutningen uppkommer.

Den aktuella Service Bus-implementeringen tillåter endast CBS tillsammans med SASL-metoden "ANONYMOUS". En SSL/TLS-anslutning måste alltid finnas innan SASL-handskakningen.

Den ANONYMa mekanismen måste därför stödjas av den valda AMQP 1,0-klienten. Anonym åtkomst innebär att den första anslutningen till hand skakningen, inklusive att skapa den första sessionen, sker utan att Service Bus veta vem som skapar anslutningen.

När anslutningen och sessionen har upprättats kan du koppla länkarna till *$CBS* -noden och *skicka begäran om utfärdande av begäran* är de enda tillåtna åtgärderna. Du måste ange en giltig token med en *begäran om* en begäran för en entitet med en entitet inom 20 sekunder efter att anslutningen har upprättats, annars släpps anslutningen ensidigt av Service Bus.

Klienten ansvarar sedan för att hålla reda på förfallo datum för token. När en token upphör att gälla tar Service Bus med att ignorera alla länkar på anslutningen till respektive entitet. För att förhindra att problem uppstår kan klienten ersätta token för noden med en ny när som helst via noden för hantering av virtuella *$CBS* med samma push *-token-* gest, och utan att komma till det sätt på nytto Last trafik som flödar på olika länkar.

### <a name="send-via-functionality"></a>Skicka via-funktioner

[Skicka via/överför sändare](service-bus-transactions.md#transfers-and-send-via) är en funktion som gör det möjligt för Service Bus att vidarebefordra ett angivet meddelande till en målentitet via en annan entitet. Den här funktionen används för att utföra åtgärder mellan entiteter i en enskild transaktion.

Med den här funktionen skapar du en avsändare och upprättar länken till `via-entity` . När du upprättar länken skickas ytterligare information för att fastställa det sanna målet för meddelandena/överföringarna på den här länken. När kopplingen har slutförts vidarebefordras alla meddelanden som skickas på den här länken automatiskt till *mål-entiteten* via *-entiteten*. 

> Obs! autentisering måste utföras för både *via-och-* målentiteten *innan* den här länken upprättas.

| Klient | Riktning | Service Bus |
| :--- | :---: | :--- |
| Fäst<br/>namn = {Link Name},<br/>roll = avsändare,<br/>Källa = {klient länk-ID},<br/>mål =**{via-Entity}**,<br/>**Egenskaper = mappa [( <br/> com. Microsoft: Transfer-Address-address = <br/> {destination-entitet})]** ) | ------> | |
| | <------ | Fäst<br/>namn = {Link Name},<br/>roll = mottagare,<br/>Källa = {klient länk-ID},<br/>mål = {via-Entity},<br/>egenskaper = Map [(<br/>com. Microsoft: Transfer-destination-Address =<br/>{destination – entitet})] ) |

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om AMQP kan du gå till följande länkar:

* [Översikt över Service Bus AMQP]
* [AMQP 1,0-stöd för Service Bus partitionerade köer och ämnen]
* [AMQP i Service Bus för Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1,0-stöd för Service Bus partitionerade köer och ämnen]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)
