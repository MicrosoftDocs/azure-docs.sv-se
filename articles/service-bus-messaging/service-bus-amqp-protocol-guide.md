---
title: AMQP 1.0 i Azure Service Bus och Event Hubs protokollguide | Microsoft Docs
description: Protokollguide till uttryck och beskrivning av AMQP 1.0 i Azure Service Bus och Event Hubs
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 8d346aeef74e1f67d3d525c061d40314ee5342aa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531005"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 i Azure Service Bus och Event Hubs protokollguide

Advanced Message Queueing Protocol 1.0 är ett standardiserat inramnings- och överföringsprotokoll för asynkront, säkert och tillförlitligt överföring av meddelanden mellan två parter. Det är det primära protokollet för Azure Service Bus messaging och Azure Event Hubs.  

AMQP 1.0 är resultatet av ett brett branschsamarbete som förde samman mellanprogramsleverantörer, till exempel Microsoft och Red Hat, med många meddelande mellanprogramsanvändare som JP Morgan Jp som representerar finanstjänstbranschen. Forum för teknisk standardisering för AMQP-protokollet och tilläggsspecifikationerna är OASIS, och det har fått formellt godkännande som en internationell standard som ISO/IEC 19494:2014. 

## <a name="goals"></a>Mål

Den här artikeln sammanfattar huvudbegreppen i amqp 1.0-meddelandespecifikationen tillsammans med tilläggsspecifikationer som utvecklats av DEN tekniska nämnden [för OASIS AMQP](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) och förklarar hur Azure Service Bus implementerar och bygger på dessa specifikationer.

Målet är att alla utvecklare som använder en befintlig AMQP 1.0-klientstack på valfri plattform ska kunna interagera med Azure Service Bus via AMQP 1.0.

Vanliga AMQP 1.0-stackar för generell användning, till exempel [Apache Qpid Proton](https://qpid.apache.org/proton/index.html) eller [AMQP.NET Lite,](https://github.com/Azure/amqpnetlite)implementerar alla kärn-AMQP 1.0-protokollelement som sessioner eller länkar. Dessa grundläggande element är ibland omslutna med ett API på högre nivå. Apache Proton erbjuder till och med två, imperativt Api för Api:et för api:et för api:et för den reaktiva reactorn.

I följande diskussion förutsätter vi att hanteringen av AMQP-anslutningar, sessioner och länkar samt hanteringen av ramöverföringar och flödeskontroll hanteras av respektive stack (till exempel Apache Proton-C) och inte kräver mycket om någon särskild uppmärksamhet från programutvecklare. Vi antar abstrakt att det finns några API-primitiver som möjligheten att  ansluta och att skapa någon form av avsändar- och mottagarabstraheringsobjekt, som sedan har viss form av och  `send()` `receive()` åtgärder.

När du diskuterar avancerade funktioner i Azure Service Bus, till exempel bläddring av meddelanden eller hantering av sessioner, förklaras dessa funktioner i AMQP-termer, men även som en pseudoimplementering i flera lager ovanpå den här förmodade API-abstraktionen.

## <a name="what-is-amqp"></a>Vad är AMQP?

AMQP är ett inramnings- och överföringsprotokoll. Inramning innebär att den ger struktur för binära dataströmmar som flödar i båda riktningarna för en nätverksanslutning. Strukturen ger en avgränsare för distinkta datablock, som kallas *ramar,* som ska utbytas mellan de anslutna parterna. Överföringsfunktionerna ser till att båda kommunicerande parter kan upprätta en delad förståelse för när bildrutor ska överföras och när överföringarna ska anses vara slutförda.

Till skillnad från tidigare utgångna utkastversioner som producerats av AMQP-arbetsgruppen och som fortfarande används av några få a brokers för meddelanden, tillåter inte arbetsgruppens slutliga och standardiserade AMQP 1.0-protokoll förekomsten av en autjämnare för meddelanden eller någon viss topologi för entiteter i en autjämnare för meddelanden.

Protokollet kan användas för symmetrisk peer-to-peer-kommunikation, för interaktion med a brokers för meddelanden som stöder köer och publicera/prenumerera entiteter, som Azure Service Bus gör. Det kan också användas för interaktion med meddelandeinfrastruktur där interaktionsmönstren skiljer sig från vanliga köer, vilket är fallet med Azure Event Hubs. En händelsehubb fungerar som en kö när händelser skickas till den, men fungerar mer som en seriell lagringstjänst när händelser läses från den. den liknar en bandenhet något. Klienten väljer en förskjutning i den tillgängliga dataströmmen och betjänas sedan alla händelser från den förskjutningen till den senaste tillgängliga.

AMQP 1.0-protokollet är utformat för att vara utökningsbart, vilket möjliggör ytterligare specifikationer för att förbättra dess funktioner. De tre tilläggsspecifikationer som beskrivs i det här dokumentet illustrerar detta. För kommunikation över befintlig HTTPS/WebSockets-infrastruktur kan det vara svårt att konfigurera de interna AMQP TCP-portarna. En bindningsspecifikation definierar hur AMQP ska skikta över WebSockets. För att interagera med meddelandeinfrastrukturen på ett begäran-/svars-sätt i hanteringssyfte eller för att tillhandahålla avancerade funktioner definierar AMQP-hanteringsspecifikationen de grundläggande interaktionsprimiterna som krävs. För integrering av federerad auktoriseringsmodell definierar AMQP-specifikationen anspråksbaserad säkerhet hur auktoriseringstoken som är associerade med länkar associeras och förnyas.

## <a name="basic-amqp-scenarios"></a>Grundläggande AMQP-scenarier

I det här avsnittet beskrivs den grundläggande användningen av AMQP 1.0 med Azure Service Bus, vilket innefattar att skapa anslutningar, sessioner och länkar och överföra meddelanden till och från Service Bus-entiteter som köer, ämnen och prenumerationer.

Den mest auktoritativa källan för att lära sig mer om hur AMQP fungerar är [AMQP 1.0-specifikationen,](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html)men specifikationen skrevs för att exakt vägleda implementeringen och inte för att lära protokollet. Det här avsnittet fokuserar på att introducera så mycket terminologi som behövs för att beskriva hur Service Bus använder AMQP 1.0. En mer omfattande introduktion till AMQP, samt en bredare diskussion om AMQP 1.0, finns i den [här videokursen.][this video course]

### <a name="connections-and-sessions"></a>Anslutningar och sessioner

AMQP anropar containrarna för *kommunikationsprogram*; de innehåller *noder*, som är de kommunicerande entiteterna inuti dessa containrar. En kö kan vara en sådan nod. AMQP tillåter multiplexering, så en enda anslutning kan användas för många kommunikationsvägar mellan noder. En programklient kan till exempel samtidigt ta emot från en kö och skicka till en annan kö via samma nätverksanslutning.

![Diagram som visar sessioner och anslutningar mellan containrar.][1]

Nätverksanslutningen är därför fäst på containern. Den initieras av containern i klientrollen och gör en utgående TCP-socketanslutning till en container i mottagarrollen, som lyssnar efter och accepterar inkommande TCP-anslutningar. Handskakningen för anslutningar omfattar att förhandla protokollversionen, deklarera eller förhandla användningen av TLS/SSL (Transport Level Security) och en handskakning för autentisering/auktorisering i anslutningsomfånget som baseras på SASL.

Azure Service Bus kräver alltid användning av TLS. Den stöder anslutningar via TCP-port 5671, där TCP-anslutningen först överlagras med TLS innan den förs in i HANDskakningen för AMQP-protokollet, och stöder även anslutningar via TCP-port 5672 där servern omedelbart erbjuder en obligatorisk uppgradering av anslutningen till TLS med hjälp av den AMQP-föreskrivna modellen. AMQP WebSockets-bindningen skapar en tunnel via TCP-port 443 som sedan motsvarar AMQP 5671-anslutningar.

När du har konfigurerat anslutningen och TLS Service Bus två alternativ för SASL-mekanism:

* SASL PLAIN används ofta för att skicka autentiseringsuppgifter för användarnamn och lösenord till en server. Service Bus inte några konton, men med namnet [Shared Access Security rules](service-bus-sas.md), som ger rättigheter och är associerade med en nyckel. Namnet på en regel används som användarnamn och nyckeln (som base64-kodad text) används som lösenord. De rättigheter som är associerade med den valda regeln styr de åtgärder som tillåts för anslutningen.
* SASL ANONYMOUS används för att kringgå SASL-auktorisering när klienten vill använda modellen för anspråksbaserad säkerhet (CBS) som beskrivs senare. Med det här alternativet kan en klientanslutning upprättas anonymt under en kort tid då klienten bara kan interagera med CBS-slutpunkten och CBS-handskakningen måste slutföras.

När transportanslutningen har upprättats deklarerar containrarna den maximala ramstorlek som de är villiga att hantera, och efter en tidsgräns för inaktivitet kopplar de automatiskt från om det inte finns någon aktivitet i anslutningen.

De deklarerar också hur många samtidiga kanaler som stöds. En kanal är en enkelriktad, utgående virtuell överföringssökväg ovanpå anslutningen. En session använder en kanal från var och en av de sammankopplade containrarna för att bilda en dubbelriktad kommunikationsväg.

Sessioner har en fönsterbaserad flödeskontrollmodell. När en session skapas deklarerar varje part hur många bildrutor den är beredd att acceptera i sitt mottagningsfönster. När parterna byter bildrutor fyller överförda bildrutor det fönstret och överföringarna stoppas när fönstret är fullt och tills fönstret återställs eller expanderas med hjälp av flödets *performativa* (*performativ* är AMQP-termen för gester på protokollnivå som utbyts mellan de två parterna).

Den här fönsterbaserade modellen är ungefär detsamma som TCP-begreppet fönsterbaserad flödeskontroll, men på sessionsnivå inuti socketen. Protokollets koncept för att tillåta flera samtidiga sessioner finns så att trafik med hög prioritet kan snidas förbi begränsad normal trafik, till exempel på ett expressfält på en motorväg.

Azure Service Bus använder för närvarande exakt en session för varje anslutning. Den Service Bus ramstorleken är 262 144 byte (256 000 byte) för Service Bus Standard. Det är 1 048 576 (1 MB) för Service Bus Premium och Event Hubs. Service Bus inte några särskilda begränsningsfönster på sessionsnivå, men återställer fönstret regelbundet som en del av flödeskontrollen på länknivå (se [nästa avsnitt](#links)).

Anslutningar, kanaler och sessioner är tillfälliga. Om den underliggande anslutningen minimeras måste anslutningar, TLS-tunnel, SASL-auktoriseringskontext och sessioner återupprättas.

### <a name="amqp-outbound-port-requirements"></a>Krav för utgående AMQP-portar

Klienter som använder AMQP-anslutningar via TCP kräver att portarna 5671 och 5672 öppnas i den lokala brandväggen. Tillsammans med dessa portar kan det vara nödvändigt att öppna ytterligare portar om [EnableLinkRedirect-funktionen](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect) är aktiverad. `EnableLinkRedirect` är en ny meddelandefunktion som hjälper till att hoppa över ett hopp när meddelanden tas emot, vilket bidrar till att öka dataflödet. Klienten skulle börja kommunicera direkt med backend-tjänsten via portintervallet 104XX enligt följande bild. 

![Lista över målportar][4]

En .NET-klient misslyckas med en SocketException ("Ett försök gjordes att komma åt en socket på ett sätt som förbjuds av åtkomstbehörigheterna") om dessa portar blockeras av brandväggen. Funktionen kan inaktiveras genom att ange i anslutningssträngen, som tvingar klienterna att kommunicera med `EnableAmqpLinkRedirect=false` fjärrtjänsten via port 5671.


### <a name="links"></a>Länkar

AMQP överför meddelanden via länkar. En länk är en kommunikationssökväg som skapas över en session som gör det möjligt att överföra meddelanden i en riktning. överföringens statusförhandling är via länken och dubbelriktad mellan de anslutna parterna.

![Skärmbild som visar en session med en länkanslutning mellan två containrar.][2]

Länkar kan skapas av antingen containern när som helst och över en befintlig session, vilket gör att AMQP skiljer sig från många andra protokoll, inklusive HTTP och MQTT, där initiering av överföringar och överföringssökväg är en exklusiv behörighet för den part som skapar socketanslutningen.

Containern som initierar länken ber den motsatta containern att godkänna en länk och väljer antingen avsändarens eller mottagarens roll. Därför kan antingen containern initiera skapandet av enkelriktade eller dubbelriktade kommunikationssökvägar, med den senare modellerad som par med länkar.

Länkar namnges och associeras med noder. Som vi angav i början är noder de kommunicerande entiteterna i en container.

I Service Bus motsvarar en nod direkt en kö, ett ämne, en prenumeration eller en underkö för en kö eller prenumeration. Nodnamnet som används i AMQP är därför det relativa namnet på entiteten inuti Service Bus namnområdet. Om en kö heter `myqueue` är det också dess AMQP-nodnamn. En ämnesprenumeration följer HTTP API-konventionen genom att sorteras  i en resurssamling för "prenumerationer" och därför har en prenumerationsundergrupp i ett ämne **mytopic** AMQP-nodnamnet **mytopic/subscriptions/sub**.

Den anslutande klienten krävs också för att använda ett lokalt nodnamn för att skapa länkar. Service Bus är inte normativt om dessa nodnamn och tolkar dem inte. AMQP 1.0-klientstackar använder vanligtvis ett schema för att säkerställa att dessa tillfälliga nodnamn är unika i omfånget för klienten.

### <a name="transfers"></a>Överföringar

När en länk har upprättats kan meddelanden överföras via länken. I AMQP körs en överföring med en explicit  protokollgest (den performativa överföringen) som flyttar ett meddelande från avsändaren till mottagaren via en länk. En överföring slutförs när den "regleras", vilket innebär att båda parterna har upprättat en delad förståelse för resultatet av överföringen.

![Ett diagram som visar ett meddelandes överföring mellan avsändaren och mottagaren och dispositionen som är resultatet av det.][3]

I det enklaste fallet kan avsändaren välja att skicka meddelanden som "förbestämt", vilket innebär att klienten inte är intresserad av resultatet och att mottagaren inte ger någon feedback om resultatet av åtgärden. Det här läget stöds av Service Bus på AMQP-protokollnivå, men exponeras inte i något av klient-API:erna.

Det vanliga fallet är att meddelanden skickas oreglerade och att mottagaren sedan anger godkännande eller avvisande med hjälp av *dispositionen* performativt. Avvisande inträffar när mottagaren inte kan acceptera meddelandet av någon anledning och avvisningsmeddelandet innehåller information om orsaken, vilket är en felstruktur som definieras av AMQP. Om meddelanden avvisas på grund av interna fel i Service Bus returnerar tjänsten extra information i den strukturen som kan användas för att ge diagnostiktips till supportpersonal om du skickar in supportförfrågningar. Du lär dig mer om fel senare.

En särskild typ av  avvisande är det frisläppta tillståndet, vilket indikerar att mottagaren inte har några tekniska stötanden på överföringen, men inte heller något intresse av att reglera överföringen. Det fallet gäller till exempel när ett meddelande levereras till en Service Bus-klient och klienten väljer att "avbryta" meddelandet eftersom den inte kan utföra det arbete som är resultatet av bearbetningen av meddelandet. själva meddelandeleveransen är inte felaktig. En variant av det tillståndet är *det ändrade* tillståndet, som tillåter ändringar i meddelandet när det släpps. Det tillståndet används inte av Service Bus för närvarande.

AMQP 1.0-specifikationen definierar ett ytterligare dispositionstillstånd som kallas *mottagen*, som specifikt hjälper till att hantera länkåterställning. Med länkåterställning kan du omkonfigurera tillståndet för en länk och eventuella väntande leveranser ovanpå en ny anslutning och session när den tidigare anslutningen och sessionen förlorades.

Service Bus stöder inte länkåterställning. Om klienten förlorar anslutningen till Service Bus med en väntande oreglerad meddelandeöverföring går meddelandeöverföringen förlorad och klienten måste återansluta, återupprätta länken och försöka överföra igen.

Därför har Service Bus och Event Hubs stöd för "minst en gång"-överföring där avsändaren kan vara säker på att meddelandet har lagrats och godkänts, men inte stöder "exakt en gång"-överföring på AMQP-nivå, där systemet försöker återställa länken och fortsätta att förhandla leveranstillståndet för att undvika duplicering av meddelandeöverföringen.

För att kompensera för möjliga dubbletter Service Bus du dubblettidentifiering som en valfri funktion i köer och ämnen. Dubblettidentifiering registrerar meddelande-ID:erna för alla inkommande meddelanden under ett användardefinierat tidsfönster och släpper sedan tyst alla meddelanden som skickas med samma meddelande-ID under samma fönster.

### <a name="flow-control"></a>Flödeskontroll

Förutom flödeskontrollmodellen på sessionsnivå som beskrivits tidigare har varje länk en egen flödeskontrollmodell. Flödeskontroll på sessionsnivå skyddar containern från att behöva hantera för många bildrutor samtidigt. Flödeskontroll på länknivå styr programmets hantering av hur många meddelanden som ska hanteras från en länk och när.

![Skärmbild av en logg som visar Källa, Mål, Källport, Målport och Protokollnamn. På den första raden anges Målport 10401 (0x28 A 1) i svart.][4]

På en länk kan överföringar endast ske när avsändaren har tillräckligt med *länkkredit.* Länkkredit är en räknare som anges av mottagaren *med* hjälp av flödets performativa, som är begränsad till en länk. När avsändaren tilldelas länkkredit försöker den använda upp krediten genom att leverera meddelanden. Varje meddelandeleverans avtar den återstående länkkrediten med 1. När länkkrediten är slut stoppas leveranser.

När Service Bus är i mottagarrollen ger den omedelbart avsändaren gott om länkkredit, så att meddelanden kan skickas omedelbart. När länkkredit används skickar Service Bus ibland ett *flöde* performativt till avsändaren för att uppdatera länkens kreditsaldo.

I avsändarrollen skickar Service Bus meddelanden för att använda upp eventuell utestående länkkredit.

Ett "mottagnings"-anrop på API-nivå omvandlas till ett flödes performativt som skickas till Service Bus av klienten och Service Bus förbrukar krediten genom att ta det första tillgängliga, olåsta meddelandet från kön, låsa den och överföra den.  Om det inte finns något meddelande tillgängligt för leverans registreras eventuell utestående kredit via någon länk som upprättats med den specifika enheten i ankomstordning och meddelanden låses och överförs när de blir tillgängliga för användning av utestående krediter.

Låset för ett meddelande släpps när överföringen regleras till ett av terminalens tillstånd som *godkänns,* *avvisas* eller *släpps*. Meddelandet tas bort från Service Bus när terminaltillståndet *godkänns*. Den finns kvar Service Bus och levereras till nästa mottagare när överföringen når något av de andra tillstånden. Service Bus automatiskt meddelandet till entitetens deadletter-kö när det når det högsta tillåtna leveransantalet för entiteten på grund av upprepade avvisanden eller versioner.

Även om Service Bus-API:erna inte direkt exponerar ett sådant alternativ i dag, kan en AMQP-protokollklient på lägre nivå använda länkkreditmodellen för att omvandla interaktionen "pull-style" för att utfärda en kreditenhet för varje mottagningsbegäran till en "push-stil"-modell genom att utfärda ett stort antal länkkrediter och sedan ta emot meddelanden när de blir tillgängliga utan ytterligare interaktion. Push stöds via [egenskapsinställningarna MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) eller [MessageReceiver.PrefetchCount.](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) När de inte är noll använder AMQP-klienten det som länkkredit.

I det här sammanhanget är det viktigt att förstå att klockan för förfallodatumet för meddelandet i entiteten börjar när meddelandet tas från entiteten, inte när meddelandet läggs till i kabeln. När klienten visar att den är redo att ta emot meddelanden genom att utfärda länkkredit förväntas den därför aktivt hämta meddelanden över nätverket och vara redo att hantera dem. Annars kan meddelandelåset ha upphört att gälla innan meddelandet ens levereras. Användningen av flödeskontroll för länkkredit bör direkt återspegla den omedelbara beredskapen att hantera tillgängliga meddelanden som skickas till mottagaren.

Sammanfattningsvis ger följande avsnitt en schematisk översikt över det performativa flödet under olika API-interaktioner. Varje avsnitt beskriver en annan logisk åtgärd. Vissa av dessa interaktioner kan vara "lata", vilket innebär att de bara kan utföras när det behövs. Att skapa en avsändare av ett meddelande kan inte orsaka en nätverksinteraktion förrän det första meddelandet skickas eller begärs.

Pilarna i följande tabell visar den performativa flödesriktningen.

#### <a name="create-message-receiver"></a>Skapa meddelandemottagare

| Klient | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numerisk referens},<br/>role=**receiver**,<br/>source={entity name},<br/>target={client link ID}<br/>) |Klienten ansluter till entiteten som mottagare |
| Service Bus svarar och bifogar länkens slut |<- attach(<br/>name={link name},<br/>handle={numerisk referens},<br/>role=**avsändare**,<br/>source={entity name},<br/>target={client link ID}<br/>) |

#### <a name="create-message-sender"></a>Skapa meddelandeavsändare

| Klient | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numerisk referens},<br/>role=**avsändare**,<br/>source={client link ID},<br/>target={entity name}<br/>) |Ingen åtgärd |
| Ingen åtgärd |<– anslut(<br/>name={link name},<br/>handle={numerisk referens},<br/>role=**receiver**,<br/>source={client link ID},<br/>target={entity name}<br/>) |

#### <a name="create-message-sender-error"></a>Skapa meddelandeavsändare (fel)

| Klient | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numerisk referens},<br/>role=**sender**,<br/>source={client link ID},<br/>target={entity name}<br/>) |Ingen åtgärd |
| Ingen åtgärd |<– anslut(<br/>name={link name},<br/>handle={numerisk referens},<br/>role=**receiver**,<br/>source=null,<br/>target=null<br/>)<br/><br/><-- koppla från(<br/>handle={numerisk referens},<br/>closed=**true**,<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>Stäng meddelandemottagare/avsändare

| Klient | Service Bus |
| --- | --- |
| --> från(<br/>handle={numerisk referens},<br/>closed=**true**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<-- koppla från(<br/>handle={numerisk referens},<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>Skicka (lyckades)

| Klient | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numerisk referens},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>) |

#### <a name="send-error"></a>Skicka (fel)

| Klient | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numerisk referens},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Ingen åtgärd |
| Ingen åtgärd |<- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**rejected**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>Ta emot

| Klient | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=1<br/>) |Ingen åtgärd |
| Ingen åtgärd |< transfer(<br/>delivery-id={numerisk referens},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=**receiver**,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Ingen åtgärd |

#### <a name="multi-message-receive"></a>Ta emot flera meddelanden

| Klient | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=3<br/>) |Ingen åtgärd |
| Ingen åtgärd |< transfer(<br/>delivery-id={numerisk referens},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Ingen åtgärd |< transfer(<br/>delivery-id={numerisk referens+1},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Ingen åtgärd |< transfer(<br/>delivery-id={numerisk referens+2},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID+2},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Ingen åtgärd |

### <a name="messages"></a>Meddelanden

I följande avsnitt förklaras vilka egenskaper från standardavsnitten för AMQP-meddelanden som används av Service Bus och hur de mappar till Service Bus API-uppsättningen.

Alla egenskap som programmet behöver definiera ska mappas till AMQP:s `application-properties` karta.

#### <a name="header"></a>sidhuvud

| Fältnamn | Användning | API-namn |
| --- | --- | --- |
| Hållbara |- |- |
| prioritet |- |- |
| ttl |Time to live för det här meddelandet |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| first-acquirer |- |- |
| leveransantal |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Fältnamn | Användning | API-namn |
| --- | --- | --- |
| meddelande-id |Programdefinierad, friformsidentifierare för det här meddelandet. Används för dubblettidentifiering. |[Messageid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| användar-id |Programdefinierad användaridentifierare, tolkas inte av Service Bus. |Inte tillgängligt via Service Bus-API:et. |
| på |Programdefinierad målidentifierare, tolkas inte av Service Bus. |[Till](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Ämne |Programdefinierad identifierare för meddelandesyfte, tolkas inte av Service Bus. |[Etikett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| svara på |Programdefinierad indikator för svarssökväg, tolkas inte av Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| korrelations-id |Programdefinierad korrelationsidentifierare, tolkas inte av Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-type |Programdefinierad indikator för innehållstyp för brödtexten, tolkas inte av Service Bus. |[Contenttype](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-encoding |Programdefinierad indikator för innehållskodning för brödtexten, tolkas inte av Service Bus. |Inte tillgängligt via Service Bus-API:et. |
| absolut förfallotid |Deklarerar vid vilken absolut omedelbar meddelande upphör att gälla. Ignoreras vid indata (huvud-TTL observeras), auktoritativt för utdata. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| skapandetid |Deklarerar när meddelandet skapades. Används inte av Service Bus |Inte tillgängligt via Service Bus-API:et. |
| grupp-id |Programdefinierad identifierare för en relaterad uppsättning meddelanden. Används för Service Bus sessioner. |[Sessionid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| gruppsekvens |Räknaren identifierar det relativa sekvensnumret för meddelandet i en session. Ignoreras av Service Bus. |Inte tillgängligt via Service Bus-API:et. |
| svara på grupp-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Meddelandeanteckningar

Det finns några andra egenskaper för Service Bus-meddelanden, som inte är en del av AMQP-meddelandeegenskaperna, och som skickas `MessageAnnotations` vidare som i meddelandet.

| Kartnyckel för anteckning | Användning | API-namn |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | Deklarerar vid vilken tidpunkt meddelandet ska visas på entiteten |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc) |
| x-opt-partition-key | Programdefinierad nyckel som avgör vilken partition meddelandet ska hamnar i. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey) |
| x-opt-via-partition-key | Programdefinierat partitionsnyckelvärde när en transaktion ska användas för att skicka meddelanden via en överföringskö. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey) |
| x-opt-enqueued-time | Tjänstdefinierad UTC-tid som representerar den faktiska tiden då meddelandet står i meddelandeklockor. Ignoreras vid indata. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) |
| x-opt-sequence-number | Tjänstdefinierat unikt nummer som tilldelats till ett meddelande. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) |
| x-opt-offset | Tjänstdefinierat sekvensnummer för meddelandet. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber) |
| x-opt-locked-until | Tjänstdefinierad. Datum och tid tills meddelandet kommer att låsas i kön/prenumerationen. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc) |
| x-opt-deadletter-source | Tjänstdefinierad. Om meddelandet tas emot från kön för dead letter är källan till det ursprungliga meddelandet. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource) |

### <a name="transaction-capability"></a>Transaktionsfunktion

En transaktion grupperar två eller flera åtgärder tillsammans i en körning. En sådan transaktion måste till sin natur säkerställa att alla åtgärder som hör till en viss grupp av åtgärder antingen lyckas eller misslyckas gemensamt.
Åtgärderna grupperas efter en identifierare `txn-id` .

För transaktionell interaktion fungerar klienten som en `transaction controller` , som styr de åtgärder som ska grupperas tillsammans. Service Bus-tjänsten fungerar som `transactional resource` en och utför arbete som begärs av `transaction controller` .

Klienten och tjänsten kommunicerar via `control link` en , som upprättas av klienten. Meddelandena och skickas av kontrollanten via kontrolllänken för att allokera respektive slutföra transaktioner (de representerar inte avgränsningen `declare` `discharge` av transaktionellt arbete). Den faktiska skicka/ta emot utförs inte på den här länken. Varje transaktionell åtgärd som begärs identifieras uttryckligen med önskat `txn-id` och kan därför inträffa på valfri länk i anslutningen. Om kontrolllänken stängs när det finns icke-fördrångade transaktioner som den har skapat återställs alla sådana transaktioner omedelbart och försök att utföra ytterligare transaktionsarbete på dem kommer att leda till fel. Meddelanden på kontrolllänken får inte vara förinstallerade.

Varje anslutning måste initiera en egen kontrolllänk för att kunna starta och avsluta transaktioner. Tjänsten definierar ett särskilt mål som fungerar som en `coordinator` . Klienten/kontrollanten upprättar en kontrolllänk till det här målet. Kontrolllänken ligger utanför gränsen för en entitet, det vill säga att samma kontrolllänk kan användas för att initiera och dra igång transaktioner för flera entiteter.

#### <a name="starting-a-transaction"></a>Starta en transaktion

För att påbörja transaktionellt arbete. kontrollanten måste hämta en från `txn-id` koordinatorn. Den gör detta genom att skicka ett `declare` typmeddelande. Om deklarationen lyckas svarar koordinatorn med ett dispositionsresultat som innehåller det tilldelade `txn-id` .

| Klient (styrenhet) | Riktning | Service Bus (Koordinator) |
| :--- | :---: | :--- |
| attach(<br/>name={link name},<br/>... ,<br/>role=**sender**,<br/>target=**Koordinator**<br/>) | ------> |  |
|  | <------ | attach(<br/>name={link name},<br/>... ,<br/>target=Coordinator()<br/>) |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=**Declared**(<br/>**txn-id**={transaktions-ID}<br/>))|

#### <a name="discharging-a-transaction"></a>Ta bort en transaktion

Kontrollanten avslutar transaktionsarbetet genom att skicka ett meddelande `discharge` till koordinatorn. Kontrollanten anger att den vill genomföra eller återställa det transaktionella arbetet genom att ange `fail` flaggan i brödtexten. Om koordinatorn inte kan slutföra koordinatorn avvisas meddelandet med resultatet `transaction-error` .

> Obs! fail=true refererar till Återställning av en transaktion och fail=false refererar till Genomför.

| Klient (styrenhet) | Riktning | Service Bus (koordinator) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaktions-ID}<br/>))|
| | . . . <br/>Transaktionsarbete<br/>på andra länkar<br/> . . . |
| transfer(<br/>delivery-id=57, ...)<br/>{ AmqpValue (<br/>**Förlopp(txn-id=0, <br/> fail=false)**)}| ------> |  |
| | <------ | disposition( <br/> first=57, last=57, <br/>state=**Accepted()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Skicka ett meddelande i en transaktion

Allt transaktionellt arbete utförs med `transactional-state` transaktionsleveranstillståndet som innehåller txn-id. Vid sändning av meddelanden utförs transaktionstillståndet av meddelandets överföringsram. 

| Klient (styrenhet) | Riktning | Service Bus (Koordinator) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaktions-ID}<br/>))|
| transfer(<br/>handle=1,<br/>delivery-id=1, <br/>**state= <br/> TransactionalState( <br/> txn-id=0)**)<br/>{ nyttolast }| ------> |  |
| | <------ | disposition( <br/> first=1, last=1, <br/>state=**TransactionalState( <br/> txn-id=0, <br/> outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Ta bort ett meddelande i en transaktion

Meddelandedispositionen innehåller åtgärder som `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer` . Om du vill utföra dessa åtgärder inom en transaktion skickar `transactional-state` du med dispositionen.

| Klient (styrenhet) | Riktning | Service Bus (koordinator) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaktions-ID}<br/>))|
| | <------ |transfer(<br/>handle=2,<br/>delivery-id=11, <br/>state=null)<br/>{ nyttolast }|  
| disposition( <br/> first=11, last=11, <br/>state=**TransactionalState( <br/> txn-id=0, <br/> outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Avancerade Service Bus funktioner

I det här avsnittet går vi Azure Service Bus funktioner som baseras på utkast till amqp-tillägg, som för närvarande utvecklas i DEN tekniska miljöavdelningen på AMQP. Service Bus implementerar de senaste versionerna av dessa utkast och inför ändringar som införs när dessa utkast når standardstatus.

> [!NOTE]
> Service Bus avancerade åtgärder för meddelanden stöds via ett mönster för begäran/svar. Informationen om dessa åtgärder beskrivs i artikeln [AMQP 1.0 i Service Bus: request-response-based operations](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>AMQP-hantering

AMQP-hanteringsspecifikationen är den första av de utkasttillägg som beskrivs i den här artikeln. Den här specifikationen definierar en uppsättning protokoll ovanpå AMQP-protokollet som tillåter hanteringsinteraktioner med meddelandeinfrastrukturen via AMQP. Specifikationen definierar allmänna åtgärder som att  *skapa,* *läsa,* uppdatera och ta bort för att hantera entiteter i en meddelandeinfrastruktur och en uppsättning frågeåtgärder.

Alla dessa gester kräver en interaktion mellan begäran/svar mellan klienten och meddelandeinfrastrukturen, och därför definierar specifikationen hur interaktionsmönstret ska modellera ovanpå AMQP: klienten ansluter till meddelandeinfrastrukturen, initierar en session och skapar sedan ett par länkar. På en länk fungerar klienten som avsändare och på den andra fungerar den som mottagare, vilket skapar ett par länkar som kan fungera som en dubbelriktad kanal.

| Logisk åtgärd | Klient | Service Bus |
| --- | --- | --- |
| Skapa sökväg för begärandesvar |--> attach(<br/>name={*link name*},<br/>handle={*numerisk referens*},<br/>role=**sender**,<br/>source=**null**,<br/>target="myentity/$management"<br/>) |Ingen åtgärd |
| Skapa sökväg för begärandesvar |Ingen åtgärd |\<-- attach(<br/>name={*link name*},<br/>handle={*numerisk referens*},<br/>role=**receiver**,<br/>source=null,<br/>target="myentity"<br/>) |
| Skapa sökväg för begärandesvar |--> attach(<br/>name={*link name*},<br/>handle={*numerisk referens*},<br/>role=**receiver**,<br/>source="myentity/$management",<br/>target="myclient$id"<br/>) | |
| Skapa sökväg för begärandesvar |Ingen åtgärd |\<-- attach(<br/>name={*link name*},<br/>handle={*numeriska handtag*},<br/>role=**avsändare**,<br/>source="myentity",<br/>target="myclient$id"<br/>) |

Med det här länkparet på plats är implementeringen av begäran/svar enkel: en begäran är ett meddelande som skickas till en entitet i meddelandeinfrastrukturen som förstår det här mönstret. I det begärandemeddelandet anges *svarsfältet* i avsnittet  egenskaper till målidentifieraren för länken som svaret ska levereras till.  Hanteringsentiteten bearbetar begäran och levererar sedan svaret  via länken vars målidentifierare matchar den angivna *svars-till-identifieraren.*

Mönstret kräver naturligtvis att klientcontainern och den klientgenererade identifieraren för svarsmålet är unika för alla klienter och av säkerhetsskäl också svåra att förutsäga.

De meddelandeutbyten som används för hanteringsprotokollet och för alla andra protokoll som använder samma mönster sker på programnivå. de definierar inte nya gester på AMQP-protokollnivå. Det är avsiktligt, så att program kan dra omedelbar nytta av dessa tillägg med kompatibla AMQP 1.0-stackar.

Service Bus implementerar för närvarande inte några av huvudfunktionerna i hanteringsspecifikationen, men mönstret för begäran/svar som definieras av hanteringsspecifikationen är grundläggande för den anspråksbaserade säkerhetsfunktionen och för nästan alla avancerade funktioner som beskrivs i följande avsnitt:

### <a name="claims-based-authorization"></a>Anspråksbaserad auktorisering

Specifikationsutkastet för AMQP-anspråksbaserad auktorisering (CBS) bygger på mönstret för begäran/svar för hanteringsspecifikation och beskriver en generaliserad modell för hur du använder federerade säkerhetstoken med AMQP.

Standardsäkerhetsmodellen för AMQP som beskrivs i introduktionen baseras på SASL och integreras med AMQP-anslutningshandskakningen. Att använda SASL har fördelen att det ger en utökningsbar modell där en uppsättning mekanismer har definierats från vilka protokoll som helst som formellt lutar åt SASL kan dra nytta av. Bland dessa mekanismer finns "PLAIN" för överföring av användarnamn och lösenord, "EXTERNAL" för att binda till säkerhet på TLS-nivå, "ANONYMOUS" för att uttrycka avsaknad av explicit autentisering/auktorisering och en mängd olika ytterligare mekanismer som tillåter överföring av autentiserings- och/eller autentiseringsuppgifter eller token.

SASL-integreringen i AMQP har två nackdelar:

* Alla autentiseringsuppgifter och token är begränsade till anslutningen. En meddelandeinfrastruktur kanske vill tillhandahålla differentierad åtkomstkontroll per entitet. till exempel att låta en tokens -användare skicka till kö A men inte till kö B. När auktoriseringskontexten är fäst vid anslutningen går det inte att använda en enda anslutning och ändå använda olika åtkomsttoken för kö A och kö B.
* Åtkomsttoken är vanligtvis bara giltiga under en begränsad tid. Den här giltigheten kräver att användaren regelbundet kräver tokens och ger en möjlighet för tokenutfärdaren att neka utfärdande av en ny token om användarens åtkomstbehörigheter har ändrats. AMQP-anslutningar kan vara långa tidsperioder. SASL-modellen ger bara möjlighet att ange en token vid anslutningstid, vilket innebär att meddelandeinfrastrukturen antingen måste koppla från klienten när token upphör att gälla eller måste acceptera risken för att tillåta fortsatt kommunikation med en klient som har åtkomsträttigheter kan ha återkallats under tiden.

AMQP CBS-specifikationen, som implementeras av Service Bus, ger en elegant lösning för båda dessa problem: Det gör att en klient kan associera åtkomsttoken med varje nod och uppdatera dessa token innan de upphör att gälla, utan att avbryta meddelandeflödet.

CBS definierar en virtuell hanteringsnod *med $cbs* som ska tillhandahållas av meddelandeinfrastrukturen. Hanteringsnoden accepterar token för alla andra noder i meddelandeinfrastrukturen.

Protokollgesten är ett begäran-/svarsutbyte enligt definitionen i hanteringsspecifikationen. Det innebär att klienten upprättar  ett par länkar med $cbs-noden och skickar sedan en begäran på den utgående länken och sedan väntar på svaret på den inkommande länken.

Begärandemeddelandet har följande programegenskaper:

| Nyckel | Valfritt | Värdetyp | Värdeinnehåll |
| --- | --- | --- | --- |
| operation |No |sträng |**put-token** |
| typ |No |sträng |Typen av token som läggs till. |
| name |No |sträng |Den "målgrupp" som token gäller för. |
| Förfallodagen |Yes |timestamp |Tokens förfallotid. |

Namnegenskapen identifierar den entitet som token ska associeras med.  I Service Bus är det sökvägen till kön eller ämnet/prenumerationen. Typegenskapen identifierar tokentypen: 

| Tokentyp | Tokenbeskrivning | Brödtexttyp | Kommentarer |
| --- | --- | --- | --- |
| Jwt |JSON Web Token (JWT) |AMQP-värde (sträng) | |
| servicebus.windows.net:sastoken |Service Bus SAS-token |AMQP-värde (sträng) |- |

Tokens har rättigheter. Service Bus känner till tre grundläggande rättigheter: "Skicka" gör det möjligt att skicka, "Lyssna" möjliggör mottagning och "Hantera" möjliggör manipulering av entiteter. Service Bus SAS-token refererar till regler som konfigurerats på namnområdet eller entiteten, och dessa regler är konfigurerade med rättigheter. Om token signeras med nyckeln som är associerad med den regeln uttrycks tokens respektive rättigheter. Den token som är associerad med en entitet som använder *put-token* tillåter att den anslutna klienten interagerar med entiteten enligt tokenrättigheterna. En länk där klienten får rollen *avsändare* kräver rättigheten "Skicka". för att få *rollen* som mottagare krävs rättigheten "Lyssna".

Svarsmeddelandet har följande *värden för programegenskaper*

| Nyckel | Valfritt | Värdetyp | Värdeinnehåll |
| --- | --- | --- | --- |
| statuskod |No |int |**HTTP-svarskod [RFC2616]**. |
| status-description |Yes |sträng |Beskrivning av statusen. |

Klienten kan anropa *put-token upprepade gånger* och för alla entiteter i meddelandeinfrastrukturen. Token är begränsade till den aktuella klienten och är fästa vid den aktuella anslutningen, vilket innebär att servern släpper alla kvarhållna token när anslutningen sjunker.

Den aktuella Service Bus endast CBS tillsammans med SASL-metoden "ANONYMOUS". En SSL/TLS-anslutning måste alltid finnas före SASL-handskakningen.

ANONYM-mekanismen måste därför stödjas av den valda AMQP 1.0-klienten. Anonym åtkomst innebär att den första handskakningen för anslutningen, inklusive skapandet av den första sessionen, sker utan att Service Bus vet vem som skapar anslutningen.

När anslutningen och sessionen har upprättats är det bara tillåtna *att koppla $cbs* till noden $cbs skicka *put-token-begäran.* En giltig token måste anges korrekt med hjälp av en *put-token-begäran* för en entitetsnod inom 20 sekunder efter att anslutningen har upprättats, annars tas anslutningen bort av Service Bus.

Klienten ansvarar därefter för att hålla reda på förfallodatum för token. När en token upphör att gälla Service Bus du snabbt alla länkar i anslutningen till respektive entitet. För att förhindra problem kan klienten ersätta token för noden med en  ny när som helst via den virtuella $cbs-hanteringsnoden med samma *put-token-gest,* och utan att komma i vägen för nyttolasttrafiken som flödar på olika länkar.

### <a name="send-via-functionality"></a>Funktionen Skicka via

[Skicka via/överför avsändare är en](service-bus-transactions.md#transfers-and-send-via) funktion som gör att Service Bus kan vidarebefordra ett visst meddelande till en målentitet via en annan entitet. Den här funktionen används för att utföra åtgärder över entiteter i en enda transaktion.

Med den här funktionen skapar du en avsändare och upprättar länken till `via-entity` . När länken upprättas skickas ytterligare information för att fastställa det sanna målet för meddelanden/överföringar på den här länken. När kopplingen har lyckats vidarebefordras alla meddelanden som skickas på den här länken automatiskt till *målentiteten* via *via entiteten*. 

> Obs! Autentisering måste utföras för både via entitet *och målentitet* *innan* du upprättar den här länken.

| Klient | Riktning | Service Bus |
| :--- | :---: | :--- |
| attach(<br/>name={link name},<br/>role=sender,<br/>source={client link ID},<br/>target=**{via-entity}**,<br/>**properties=map [( <br/> com.microsoft:transfer-destination-address= <br/> {destination-entity} )]** ) | ------> | |
| | <------ | attach(<br/>name={link name},<br/>role=receiver,<br/>source={client link ID},<br/>target={via-entity},<br/>properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )] ) |

## <a name="next-steps"></a>Nästa steg

Mer information om AMQP finns på följande länkar:

* [Service Bus översikt över AMQP]
* [AMQP 1.0-stöd för Service Bus partitionerade köer och ämnen]
* [AMQP i Service Bus för Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus översikt över AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-stöd för Service Bus partitionerade köer och ämnen]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)