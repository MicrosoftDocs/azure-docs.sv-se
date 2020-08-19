---
title: Azure Relay Hybridanslutningar protokoll guide | Microsoft Docs
description: I den här artikeln beskrivs interaktionen på klient sidan med Hybridanslutningar relä för att ansluta klienterna i lyssnings-och avsändarens roller.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: fec021d961a17102f8d979c61ee46af6b938f073
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272017"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay Hybridanslutningar protokoll

Azure Relay är en av de viktigaste kapacitets pelarna i Azure Service Buss plattformen. Den nya _hybridanslutningar_ kapaciteten för relä är en säker utveckling med öppen protokoll som baseras på http och WebSockets. Den ersätter den tidigare, samma namngivna _BizTalk Services_ -funktion som bygger på en patentskyddad protokoll bas. Integreringen av Hybridanslutningar i Azure App Services kommer att fortsätta att fungera i befintligt skick.

Hybridanslutningar möjliggör dubbelriktad, binär data Ströms kommunikation och enkelt datagram flöde mellan två nätverksanslutna program. En eller båda parter kan finnas bakom NAT eller brand väggar.

I den här artikeln beskrivs interaktionen på klient sidan med Hybridanslutningar relä för att ansluta klienterna i lyssnings-och avsändarens roller. Det beskriver också hur lyssnare accepterar nya anslutningar och begär Anden.

## <a name="interaction-model"></a>Interaktions modell

Hybridanslutningar reläet ansluter två parter genom att tillhandahålla en plats för Rendezvous i Azure-molnet som parterna kan upptäcka och ansluta till från sitt eget nätverks perspektiv. Rendezvous-punkten kallas "hybrid anslutning" i den här och annan dokumentation, i API: erna och även i Azure Portal. Den Hybridanslutningar tjänstens slut punkt kallas "tjänst" för resten av den här artikeln.

Tjänsten tillåter vidarebefordran av WebSocket-anslutningar och HTTP (S)-förfrågningar och-svar.

Interaktions modellen är Lean i nomenklaturen som upprättats av många andra nätverks-API: er. Det finns en lyssnare som först anger beredskap för att hantera inkommande anslutningar och sedan acceptera dem när de tas emot. På den andra sidan ansluter en klient till lyssnaren och förväntar dig att anslutningen ska godkännas för att upprätta en dubbelriktad kommunikations väg. "Anslut", "lyssna" och "acceptera" är samma termer som du hittar i de flesta socket-API: er.

En vidarebefordrad kommunikations modell har antingen part som gör utgående anslutningar till en tjänst slut punkt. Detta gör att "lyssnare" även en "klient" i colloquial-användning och kan också orsaka andra terminologier. Den exakta terminologin som används för Hybridanslutningar är följande:

Programmen på båda sidor av en anslutning kallas "klienter" eftersom de är klienter till tjänsten. Klienten som väntar på och accepterar anslutningar är "lyssnare" eller sägs vara i "Listener Role". Klienten som initierar en ny anslutning till en lyssnare via tjänsten kallas "Sender" eller är i "avsändarens roll".

### <a name="listener-interactions"></a>Lyssnare-interaktioner

Lyssnaren har fem interaktioner med tjänsten. all lednings information beskrivs senare i den här artikeln i avsnittet referens.

Lyssnar-, accept-och Request-meddelanden tas emot från tjänsten. Åtgärderna förnya och ping skickas av lyssnaren.

#### <a name="listen-message"></a>Lyssna meddelande

För att indikera beredskap för tjänsten att en lyssnare är redo att godkänna anslutningar skapas en utgående WebSocket-anslutning. Hand skakningen för anslutningen har namnet på en hybrid anslutning som kon figurer ATS i relä namn området och en säkerhetstoken som ger "lyssna"-behörighet för det namnet.

När websocketen accepteras av tjänsten är registreringen klar och den etablerade websocketen hålls aktiv som "kontroll kanal" för att aktivera alla efterföljande interaktioner. Tjänsten tillåter upp till 25 samtidiga lyssnare för en hybrid anslutning. Kvoten för AppHooks ska fastställas.

För Hybridanslutningar, om det finns två eller flera aktiva lyssnare, fördelas inkommande anslutningar över dem i slumpmässig ordning. den verkliga fördelningen görs med bästa möjliga ansträngning.

#### <a name="accept-message"></a>Acceptera meddelande

När en avsändare öppnar en ny anslutning i tjänsten, väljer tjänsten och meddelar en av de aktiva lyssnarna på Hybrid anslutningen. Det här meddelandet skickas till lyssnaren över öppna kontroll kanalen som ett JSON-meddelande. Meddelandet innehåller webb adressen till WebSocket-slutpunkten som lyssnaren måste ansluta till för att godkänna anslutningen.

URL: en kan och måste användas direkt av lyssnaren utan extra arbete.
Kodad information är bara giltig under en kort tids period, i stort sett så länge som avsändaren är villig att vänta tills anslutningen upprättas från slut punkt till slut punkt. Det högsta värdet för att anta är 30 sekunder. URL: en kan bara användas för ett lyckat anslutnings försök. Så snart WebSocket-anslutningen med URL: en för Rendezvous har upprättats, vidarebefordras all ytterligare aktivitet på websocketen från och till avsändaren. Detta sker utan någon åtgärd eller tolkning av tjänsten.

### <a name="request-message"></a>Begär ande meddelande

Förutom WebSocket-anslutningar kan lyssnaren även ta emot HTTP-begäranden från en avsändare, om den här funktionen uttryckligen aktive ras på Hybrid anslutningen.

Lyssnare som bifogas till Hybridanslutningar med HTTP-stöd måste hantera `request` gesten. En lyssnare som inte hanterar `request` och orsakar därför upprepade timeout-fel vid anslutning kan vara svartlistad av tjänsten i framtiden.

Metadata för HTTP-bildruta-huvuden översätts till JSON för enklare hantering av lyssnar ramverket, även på grund av att bibliotek för HTTP-huvudparsning är rarer än JSON-tolkare. HTTP-metadata som endast är relevanta för relationen mellan avsändaren och relä-HTTP-gatewayen, inklusive auktoriseringsinformation, vidarebefordras inte. HTTP-begärans organ överförs transparent som binär WebSocket-ramar.

Lyssnaren kan svara på HTTP-begäranden med en motsvarande svars-gest.

I flödet för begäran/svar används kontroll kanalen som standard, men den kan uppgraderas till en distinkt WebSocket för Rendezvous när det behövs. Distinkta WebSocket-anslutningar förbättrar data flödet för varje klient konversation, men de tar över lyssnaren med fler anslutningar som behöver hanteras, vilket kanske inte kan vara möjligt för lätta klienter.

På kontroll kanalen är begäran och svars kroppar begränsade till högst 64 kB. Metadata för HTTP-huvuden är begränsade till totalt 32 kB. Om antingen begäran eller svaret överstiger det tröskelvärdet måste lyssnaren uppgradera till en Rendezvous-WebSocket med en gest som motsvarar hanteringen av [Accepten](#accept-message).

För förfrågningar bestämmer tjänsten om begär Anden ska dirigeras över kontroll kanalen. Detta inkluderar, men kan inte begränsas till fall där en begäran överskrider 64 kB (huvuden och brödtext) till höger, eller om begäran skickas med ["segmenterad" överförings kodning](https://tools.ietf.org/html/rfc7230#section-4.1) och tjänsten har anledning att vänta på att begäran ska överskrida 64 KB eller att det inte går att läsa begäran. Om tjänsten väljer att leverera begäran över Rendezvous, skickar den bara Rendezvous-adressen till lyssnaren.
Sedan måste du upprätta en webbsocket för Rendezvous och tjänsten som ber om den fullständiga begäran, inklusive kroppar, via webbsocketen Rendezvous. Svaret måste också använda websocketen Rendezvous.

För förfrågningar som kommer över kontroll kanalen bestämmer lyssnaren om du vill svara på kontroll kanalen eller via Rendezvous. Tjänsten måste innehålla en Rendezvous-adress med varje begäran dirigerad över kontroll kanalen. Den här adressen är bara giltig för uppgradering från den aktuella begäran.

Om lyssnaren väljer att uppgradera, ansluter den och begär svaret via den etablerade Rendezvous-sockeln.

När den Rendezvous-websocketen har upprättats bör lyssnaren underhålla den för att hantera begär Anden och svar från samma klient. Tjänsten upprätthåller websocketen så länge HTTPS socket-anslutningen med avsändaren kvarstår och dirigerar alla efterföljande förfrågningar från den avsändaren över den underhållna websocketen. Om lyssnaren väljer att ta bort webbsocketen Rendezvous från sin sida, tar tjänsten även bort anslutningen till avsändaren, oavsett om en efterföljande begäran redan är pågående.

#### <a name="renew-operation"></a>Förnya åtgärd

Säkerhetstoken som måste användas för att registrera lyssnaren och underhålla kontroll kanalen kan gå ut när lyssnaren är aktiv. Giltighets tiden för token påverkar inte pågående anslutningar, men den gör att kontroll kanalen släpps av tjänsten vid eller strax efter att det gått ut. Åtgärden "förnya" är ett JSON-meddelande om att lyssnaren kan skicka för att ersätta den token som är associerad med kontroll kanalen, så att kontroll kanalen kan hanteras under längre perioder.

#### <a name="ping-operation"></a>Ping-åtgärd

Om kontroll kanalen är inaktiv under en längre tid, kan mellanhänder på väg, till exempel belastningsutjämnare eller NAT släppa TCP-anslutningen. Med ping-åtgärden undviker du att skicka en liten mängd data på den kanal som påminner alla på den nätverks väg som anslutningen är avsedd att vara aktiv och den fungerar även som ett "Live"-test för lyssnaren. Om pingen Miss lyckas bör kontroll kanalen anses vara oanvändbar och lyssnaren bör återansluta.

### <a name="sender-interaction"></a>Avsändarens interaktion

Avsändaren har två interaktioner med tjänsten: den ansluter en webbsocket eller skickar begär Anden via HTTPS. Begär Anden kan inte skickas via en webb-socket från avsändarens roll.

#### <a name="connect-operation"></a>Anslutnings åtgärd

Åtgärden Connect (Anslut) öppnar en WebSocket på tjänsten, och anger namnet på Hybrid anslutningen och (valfritt, men krävs som standard) en säkerhetstoken som ger behörigheten "Send" i frågesträngen. Tjänsten interagerar sedan med lyssnaren på det sätt som beskrivs ovan, och lyssnaren skapar en Rendezvous-anslutning som är kopplad till websocketen. När websocketen har accepterats är alla ytterligare interaktioner i WebSocket med en ansluten lyssnare.

#### <a name="request-operation"></a>Begär åtgärd

För Hybridanslutningar för vilka funktionen har Aktiver ATS kan avsändaren skicka stora HTTP-begäranden till lyssnare.

Med undantag för en Relay-åtkomsttoken som antingen är inbäddad i frågesträngen eller i ett HTTP-huvud i begäran, är reläet helt transparent för alla HTTP-åtgärder på relä adressen och alla suffix för relä adress Sök vägen, och lämnar lyssnaren fullständig kontroll över autentisering från slut punkt till slut punkt och till och med HTTP-tilläggsfunktioner som [CORS](https://www.w3.org/TR/cors/).

Avsändarens auktorisering med Relay-slutpunkten är aktive rad som standard, men är valfritt. Ägaren till hybrid anslutningen kan välja att tillåta anonyma avsändare. Tjänsten kommer att avlyssna, inspektera och utesluta auktoriseringsinformation enligt följande:

1. Om frågesträngen innehåller ett `sb-hc-token` uttryck kommer uttrycket alltid att tas bort från frågesträngen. Den kommer att utvärderas om relä auktorisering är aktiverat.
2. Om begärandehuvuden innehåller ett huvud är `ServiceBusAuthorization` huvud uttrycket alltid kvar från huvud samlingen.
   Den kommer att utvärderas om relä auktorisering är aktiverat.
3. Endast om Relay-auktorisering har Aktiver ATS, och om begärandehuvuden innehåller ett `Authorization` huvud, och inget av de tidigare uttrycken finns, kommer rubriken att utvärderas och tas bort. Annars `Authorization` skickas alltid den som den är.

Om det inte finns någon aktiv lyssnare returnerar tjänsten fel koden 502 "Felaktig gateway". Om tjänsten inte verkar hantera begäran returnerar tjänsten en 504 "Gateway-tidsgräns" efter 60 sekunder.

### <a name="interaction-summary"></a>Interaktions Sammanfattning

Resultatet av den här interaktions modellen är att avsändarens klient kommer från hand skakningen med en "ren" WebSocket, som är ansluten till en lyssnare och som inte behöver någon ytterligare inledning eller förberedelse. Den här modellen möjliggör praktiskt taget alla befintliga WebSocket-serverimplementeringar för att kunna dra nytta av den Hybridanslutningar tjänsten genom att tillhandahålla en korrekt konstruerad URL till sitt WebSocket-klient skikt.

Websocketen Rendezvous-anslutning som lyssnaren erhåller genom accept-interaktionen är också ren och kan göras till alla befintliga implementeringar av WebSocket-servrar med vissa minimala abstraktioner som skiljer mellan "acceptera"-åtgärder i deras Ramverks lokala nätverks lyssnare och Hybridanslutningar fjärr"Accept"-åtgärder.

HTTP-begäran/svar-modellen ger avsändaren ett stort obegränsat protokoll för HTTP-protokoll med ett valfritt Authorization-skikt. Lyssnaren hämtar ett förparsat rubrik avsnitt för HTTP-begäran som kan återställas till en underordnad HTTP-begäran eller hanteras som det, med binära ramar som bär HTTP-organ. Svar använder samma format. Interaktioner med mindre än 64 KB av begäran och svars text kan hanteras över en enskild webb-socket som delas för alla avsändare. Större förfrågningar och svar kan hanteras med hjälp av Rendezvous-modellen.

## <a name="protocol-reference"></a>Protokoll referens

I det här avsnittet beskrivs information om de protokoll interaktioner som beskrivs ovan.

Alla WebSocket-anslutningar görs på port 443 som en uppgradering från HTTPS 1,1, som vanligt vis är abstrakta av vissa WebSocket-ramverk eller API. Beskrivningen här är en neutral implementering utan att föreslå ett bestämt ramverk.

### <a name="listener-protocol"></a>Lyssnar protokoll

Lyssnar protokollet består av två anslutnings-gester och tre meddelande åtgärder.

#### <a name="listener-control-channel-connection"></a>Lyssnar kontroll kanal anslutning

Kontroll kanalen öppnas med att skapa en WebSocket-anslutning till:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

`namespace-address`Är det fullständigt kvalificerade domän namnet för Azure Relay namn området som är värd för Hybrid anslutningen, vanligt vis av formuläret `{myname}.servicebus.windows.net` .

Parameter alternativen för frågesträng är följande.

| Parameter        | Krävs | Beskrivning
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Ja      | Parametern måste vara **SB-HC-Action = avlyssna** för Listener-rollen
| `{path}`         | Ja      | Den URL-kodade namn områdets sökväg för den förkonfigurerade hybrid anslutningen för att registrera lyssnaren på. Det här uttrycket läggs till i den fasta `$hc/` Sök vägs delen.
| `sb-hc-token`    | Ja\*    | Lyssnaren måste ange en giltig URL-kodad Service Bus-token för delad åtkomst för namn området eller hybrid anslutningen som ger **lyssnings** rättigheten.
| `sb-hc-id`       | Nej       | Det här tillhandahållna valfria ID: t för klienten möjliggör diagnostisk spårning från slut punkt till slut punkt.

Om WebSocket-anslutningen Miss lyckas på grund av att hybrid anslutnings Sök vägen inte är registrerad, eller om en token är ogiltig eller saknas, eller om något annat fel inträffar, så anges fel återkopplings modellen med vanlig HTTP 1,1-status feedback. Status beskrivningen innehåller ett fel spårnings-ID som kan förmedlas till support personal för Azure:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Hittades inte      | Sökvägen till hybrid anslutningen är ogiltig eller också är bas-URL: en felaktig.
| 401  | Behörighet saknas   | Säkerhetstoken saknas eller är felaktig eller ogiltig.
| 403  | Förbjudet      | Säkerhetstoken är inte giltig för den här sökvägen för den här åtgärden.
| 500  | Internt fel | Något gick fel i tjänsten.

Om WebSocket-anslutningen avsiktligt stängs av tjänsten efter det att den ursprungligen konfigurerades, så kommuniceras skälet för att göra detta med hjälp av en lämplig WebSocket-protokoll fel kod tillsammans med ett beskrivande fel meddelande som också innehåller ett spårnings-ID. Tjänsten stänger inte av kontroll kanalen utan att åtgärda ett fel tillstånd. En ren avstängning är klient kontrollerad.

| WS-status | Beskrivning
| --------- | -------------------------------------------------------------------------------
| 1001      | Sökvägen till hybrid anslutningen har tagits bort eller inaktiverats.
| 1008      | Säkerhetstoken har upphört att gälla, vilket innebär att auktoriseringsprincipen överskrids.
| 1011      | Något gick fel i tjänsten.

#### <a name="accept-handshake"></a>Acceptera hand skakning

Meddelandet "accepterar" skickas av tjänsten till lyssnaren över den tidigare etablerade kontroll kanalen som ett JSON-meddelande i en WebSocket-textram. Det finns inget svar på det här meddelandet.

Meddelandet innehåller ett JSON-objekt med namnet "Accept" som definierar följande egenskaper för tillfället:

* **address** – den URL-sträng som ska användas för att upprätta websocketen till tjänsten för att acceptera en inkommande anslutning.
* **ID** – den unika identifieraren för den här anslutningen. Om ID: t angavs av avsändar klienten är det värdet för avsändaren, annars är det ett systemgenererat värde.
* **connectHeaders** – alla HTTP-huvuden som har levererats till relä slut punkten av avsändaren, som även innehåller rubrikerna SEC-WebSocket-Protocol och SEC-WebSocket-Extensions.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

Adress-URL: en som anges i JSON-meddelandet används av lyssnaren för att upprätta WebSocket för att godkänna eller avvisa avsändar-socketen.

##### <a name="accepting-the-socket"></a>Accepterar socketen

För att godkänna upprättar lyssnaren en WebSocket-anslutning till den angivna adressen.

Om meddelandet "acceptera" `Sec-WebSocket-Protocol` har ett sidhuvud förväntas det att lyssnaren bara accepterar Websocketen om den stöder det protokollet. Dessutom anges rubriken när websocketen upprättas.

Samma sak gäller för `Sec-WebSocket-Extensions` sidhuvudet. Om ramverket har stöd för ett tillägg ska det ställa in huvudet på Server sidans svar av den krävda `Sec-WebSocket-Extensions` hand skakningen för tillägget.

URL: en måste användas för att upprätta en socket för godkännande, men innehåller följande parametrar:

| Parameter      | Krävs | Beskrivning
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | För att acceptera en socket måste parametern vara `sb-hc-action=accept`
| `{path}`       | Ja      | (se följande stycke)
| `sb-hc-id`     | Nej       | Se tidigare beskrivning av **ID**.

`{path}` är sökvägen till URL-kodad namnrymd för den förkonfigurerade hybrid anslutning som den här lyssnaren ska registreras på. Det här uttrycket läggs till i den fasta `$hc/` Sök vägs delen.

`path`Uttrycket kan utökas med ett suffix och ett frågesträngs uttryck som följer det registrerade namnet efter ett snedstreck.
Detta gör att avsändar klienten kan skicka sändnings argument till den godkända lyssnaren när det inte går att inkludera HTTP-huvuden. Förväntat är att lyssnar ramverket tolkar den fasta Sök vägs delen och det registrerade namnet från sökvägen och gör resten, eventuellt utan några frågesträngs argument som har fastställts av, som är `sb-` tillgängliga för programmet för att avgöra om anslutningen ska godkännas eller inte.

Mer information finns i avsnittet "avsändar protokoll".

Om det uppstår ett fel kan tjänsten svara på följande sätt:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -----------------------------------
| 403  | Förbjudet      | Webbadressen är inte giltig.
| 500  | Internt fel | Något gick fel i tjänsten

 När anslutningen har upprättats stänger servern websocketen när avsändarens WebSocket stängs av eller med följande status:

| WS-status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Avsändarens klient stänger anslutningen.                                    |
| 1001      | Sökvägen till hybrid anslutningen har tagits bort eller inaktiverats.                        |
| 1008      | Säkerhetstoken har upphört att gälla, vilket innebär att auktoriseringsprincipen överskrids. |
| 1011      | Något gick fel i tjänsten.                                            |

##### <a name="rejecting-the-socket"></a>Avvisar socketen

 Att avvisa socketen efter att ha kontrollerat att `accept` meddelandet kräver en liknande hand skakning så att status koden och status beskrivningen som skickar orsaken till avvisningen kan flöda tillbaka till avsändaren.

 Protokoll design valet här är att använda en WebSocket-handskakning (som har utformats för att sluta i ett definierat fel tillstånd) så att lyssnar klientens implementeringar kan fortsätta att förlita sig på en WebSocket-klient och inte behöver använda en extra HTTP-klient.

 För att avvisa socketen tar klienten adress-URI från `accept` meddelandet och lägger till två parametrar för frågesträngar i den, enligt följande:

| Param                   | Krävs | Beskrivning                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB-HC-statusCode        | Ja      | Numerisk HTTP-statuskod.                |
| SB-HC-statusDescription | Ja      | Läslig anledning för avvisningen. |

Den resulterande URI: n används sedan för att upprätta en WebSocket-anslutning.

När den här hand skakningen är korrekt, Miss lyckas den avsiktligt med en HTTP-felkod 410, eftersom ingen WebSocket har upprättats. Om något går fel beskriver följande koder felet:

| Kod | Fel          | Beskrivning                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Förbjudet      | Webbadressen är inte giltig.                |
| 500  | Internt fel | Något gick fel i tjänsten. |

#### <a name="request-message"></a>Begär ande meddelande

`request`Meddelandet skickas av tjänsten till lyssnaren över kontroll kanalen. Samma meddelande skickas också via webbsocketen Rendezvous när det har upprättats.

`request`Består av två delar: en rubrik och en binär text ram.
Om det inte finns någon brödtext utelämnas bröd text ramarna. Indikatorn för om en brödtext är närvarande är den booleska `body` egenskapen i begär ande meddelandet.

För en begäran med en begär ande text kan strukturen se ut så här:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

Lyssnaren måste hantera att ta emot begär ande texten delat över flera binära ramar (se [WebSocket-fragment](https://tools.ietf.org/html/rfc6455#section-5.4)).
Begäran avslutas när en binär ram med flaggan RÄNTETRANS har tagits emot.

För en begäran utan brödtext finns det bara en textram.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

JSON-innehållet för `request` ser ut så här:

* **adress** -URI-sträng. Detta är den Rendezvous-adress som ska användas för den här begäran. Om den inkommande begäran är större än 64 kB lämnas resten av det här meddelandet tomt och klienten måste initiera en Rendezvous-handskakning som motsvarar den `accept` åtgärd som beskrivs nedan. Tjänsten kommer sedan att placeras hela den `request` etablerade webbsocketen. Om svaret kan förväntas överstiga 64 kB måste lyssnaren även initiera en Rendezvous-handskakning och sedan överföra svaret över den etablerade webbsocketen.
* **ID** – sträng. Den unika identifieraren för den här begäran.
* **requestHeaders** – det här objektet innehåller alla HTTP-huvuden som har angetts till slut punkten av avsändaren, med undantag för auktoriseringsinformation enligt beskrivningen [ovan](#request-operation), och huvuden som är strikt kopplade till anslutningen med gatewayen. Mer specifikt, alla huvuden som definieras eller reserveras i [RFC7230](https://tools.ietf.org/html/rfc7230), förutom `Via` , tas bort och vidarebefordras inte:

  * `Connection` (RFC7230, avsnitt 6,1)
  * `Content-Length`  (RFC7230, avsnitt 3.3.2)
  * `Host`  (RFC7230, avsnitt 5,4)
  * `TE`  (RFC7230, avsnitt 4,3)
  * `Trailer`  (RFC7230, avsnitt 4,4)
  * `Transfer-Encoding`  (RFC7230, avsnitt 3.3.1)
  * `Upgrade` (RFC7230, avsnitt 6,7)
  * `Close`  (RFC7230, avsnitt 8,1)

* **requestTarget** – sträng. Den här egenskapen innehåller  ["mål för begäran" (RFC7230, Section 5,3)](https://tools.ietf.org/html/rfc7230#section-5.3) i begäran. Detta inkluderar frågesträngen, som inte är lika med alla prefix som har `sb-hc-` fastställts för fasta parametrar.
* **metod** -sträng. Detta är metoden för begäran, per [RFC7231, avsnitt 4](https://tools.ietf.org/html/rfc7231#section-4). `CONNECT`Metoden får inte användas.
* **Body** – boolesk. Anger om en eller flera förekomster av en binär textram följer.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Svara på begär Anden

Mottagaren måste svara. Upprepade försök att svara på begär Anden medan anslutningen upprätthålls kan resultera i att lyssnaren blir svartlistad.

Svar kan skickas i vilken ordning som helst, men varje begäran måste besvaras inom 60 sekunder eller leveransen rapporteras som misslyckad. Tids gränsen på 60 sekund räknas tills `response` ramen har tagits emot av tjänsten. Ett pågående svar med flera binära ramar kan inte bli inaktivt i mer än 60 sekunder eller avslutas.

Om begäran tas emot via kontroll kanalen måste svaret antingen skickas på kontroll kanalen från den plats där begäran togs emot eller skickas via en samling med flera Rendezvous-kanaler.

Svaret är ett JSON-objekt med namnet "Response". Reglerna för att hantera bröd text innehåll är precis som i `request` meddelandet och baserat på `body` egenskapen.

* **RequestId** – sträng. Kunna. `id`Egenskap svärdet för det `request` meddelande som besvaras.
* **StatusCode** – nummer. Kunna. en numerisk HTTP-statuskod som visar resultatet av meddelandet. Alla status koder för [RFC7231, avsnitt 6](https://tools.ietf.org/html/rfc7231#section-6) tillåts, förutom [502 "Felaktig gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.3) och [504 "Gateway-tidsgräns"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** -sträng. Valfritt. HTTP-status-kod orsaks fras per [RFC7230, avsnitt 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – HTTP-huvuden som ska anges i ett externt http-svar.
  Som med `request` måste RFC7230-definierade huvuden inte användas.
* **Body** – boolesk. Anger om de binära bröd text ramarna följer (s).

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Svara via Rendezvous

För svar som överstiger 64 kB måste svaret levereras via en Rendezvous-socket. Om begäran överskrider 64 kB och `request` endast innehåller adress fältet, måste en socket för Rendezvous upprättas för att hämta `request` . När en Rendezvous-socket har upprättats måste svar på respektive klient och efterföljande begär Anden från den respektive klienten levereras via Rendezvous-socketen när den behålls.

`address`URL: en i `request` måste användas som-är för att upprätta en Rendezvous-socket, men innehåller följande parametrar:

| Parameter      | Krävs | Beskrivning
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | För att acceptera en socket måste parametern vara `sb-hc-action=request`

Om det uppstår ett fel kan tjänsten svara på följande sätt:

| Kod | Fel           | Beskrivning
| ---- | --------------- | -----------------------------------
| 400  | Ogiltig begäran | Okänd åtgärd eller URL är ogiltig.
| 403  | Förbjudet       | URL: en har upphört att gälla.
| 500  | Internt fel  | Något gick fel i tjänsten

 När anslutningen har upprättats stänger servern websocketen när klientens HTTP-socket stängs av eller med följande status:

| WS-status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Avsändarens klient stänger anslutningen.                                    |
| 1001      | Sökvägen till hybrid anslutningen har tagits bort eller inaktiverats.                        |
| 1008      | Säkerhetstoken har upphört att gälla, vilket innebär att auktoriseringsprincipen överskrids. |
| 1011      | Något gick fel i tjänsten.                                            |


#### <a name="listener-token-renewal"></a>Förnyelse av lyssnings-token

När token-token håller på att gå ut kan den ersätta den genom att skicka ett text Rute meddelande till tjänsten via den etablerade kontroll kanalen. Meddelandet innehåller ett JSON `renewToken` -objekt med namnet, som definierar följande egenskap för tillfället:

* **token** – en giltig URL-kodad Service Bus delad åtkomsttoken för namn området eller hybrid anslutningen som ger **lyssnings** rättigheten.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Om verifieringen av token Miss lyckas nekas åtkomst och moln tjänsten stänger websocketen för kontroll kanalen med ett fel. Annars finns det inget svar.

| WS-status | Beskrivning                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Säkerhetstoken har upphört att gälla, vilket innebär att auktoriseringsprincipen överskrids. |

### <a name="web-socket-connect-protocol"></a>WebSocket Connect-protokoll

Avsändar protokollet är på ett effektivt sätt identiskt med hur en lyssnare upprättas.
Målet är maximal genomskinlighet för slutpunkt-till-slutpunkt-WebSocket. Adressen som ska anslutas till är samma som för lyssnaren, men "åtgärden" skiljer sig åt och token behöver en annan behörighet:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sbc-hc-token=...
```

_Namn områdets adress_ är det fullständigt kvalificerade domän namnet för Azure Relay namn området som är värd för Hybrid anslutningen, vanligt vis av formuläret `{myname}.servicebus.windows.net` .

Begäran kan innehålla godtyckligt extra HTTP-huvuden, inklusive programdefinierade. Alla angivna huvuden-flöden till lyssnaren och finns på `connectHeader` objektet i **godkännande** meddelandet.

Parameter alternativen för frågesträngen är följande:

| Param          | Obligatoriskt? | Beskrivning
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Ja       | Parametern måste vara för avsändarens roll `sb-hc-action=connect` .
| `{path}`       | Ja       | (se följande stycke)
| `sb-hc-token`  | Ja\*     | Lyssnaren måste ange en giltig URL-kodad Service Bus delad åtkomsttoken för namn området eller hybrid anslutningen som ger **send** -rättigheten.
| `sb-hc-id`     | Nej        | Ett valfritt ID som möjliggör diagnostisk spårning från slut punkt till slut punkt och görs tillgängligt för lyssnaren under godkännande hand skakningen.

 `{path}`Är sökvägen till URL-kodad namnrymd för den förkonfigurerade hybrid anslutning som den här lyssnaren ska registreras på. `path`Uttrycket kan utökas med ett suffix och ett frågeuttryck för att kommunicera vidare. Om hybrid anslutningen är registrerad under sökvägen `hyco` `path` kan uttrycket `hyco/suffix?param=value&...` följas av de parametrar för frågesträng som definierats här. Ett fullständigt uttryck kan sedan vara följande:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sbc-hc-token=...
```

`path`Uttrycket skickas till lyssnaren i adress-URI: n som finns i kontroll meddelandet "acceptera".

Om WebSocket-anslutningen Miss lyckas på grund av att hybrid anslutnings Sök vägen inte registreras, om en token är ogiltig eller saknas, eller om något annat fel inträffar, anges fel återkopplings modellen med den vanliga HTTP 1,1-status feedback-modellen. Status beskrivningen innehåller ett fel söknings-ID som kan kommunicera till support personalen för Azure:

| Kod | Fel          | Beskrivning
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Hittades inte      | Sökvägen till hybrid anslutningen är ogiltig eller också är bas-URL: en felaktig.
| 401  | Behörighet saknas   | Säkerhetstoken saknas eller är felaktig eller ogiltig.
| 403  | Förbjudet      | Säkerhetstoken är inte giltig för den här sökvägen och för den här åtgärden.
| 500  | Internt fel | Något gick fel i tjänsten.

Om WebSocket-anslutningen avsiktligt stängs av tjänsten efter det att den har kon figurer ATS, så skickas orsaken till att du gör detta med hjälp av en lämplig WebSocket-protokoll fel kod tillsammans med ett beskrivande fel meddelande som också innehåller ett spårnings-ID.

| WS-status | Beskrivning
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Lyssnaren stänger av socketen.
| 1001      | Sökvägen till hybrid anslutningen har tagits bort eller inaktiverats.
| 1008      | Säkerhetstoken har upphört att gälla, vilket innebär att auktoriseringsprincipen överskrids.
| 1011      | Något gick fel i tjänsten.

### <a name="http-request-protocol"></a>Protokoll för HTTP-begäran

HTTP Request-protokollet tillåter godtyckliga HTTP-begäranden, förutom protokoll uppgraderingar.
HTTP-begäranden anges i entitetens reguljära körnings adress utan den $hc infix som används för Hybrid anslutningar WebSocket-klienter.

```
https://{namespace-address}/{path}?sbc-hc-token=...
```

_Namn områdets adress_ är det fullständigt kvalificerade domän namnet för Azure Relay namn området som är värd för Hybrid anslutningen, vanligt vis av formuläret `{myname}.servicebus.windows.net` .

Begäran kan innehålla godtyckligt extra HTTP-huvuden, inklusive programdefinierade. Alla angivna huvuden, förutom de som är direkt definierade i RFC7230 (se [begär ande meddelande](#request-message)) flödar till lyssnaren och finns på `requestHeader` objektet i **begär ande** meddelandet.

Parameter alternativen för frågesträngen är följande:

| Param          | Obligatoriskt? | Beskrivning
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Ja\*     | Lyssnaren måste ange en giltig URL-kodad Service Bus delad åtkomsttoken för namn området eller hybrid anslutningen som ger **send** -rättigheten.

Token kan också transporteras i antingen `ServiceBusAuthorization` -eller `Authorization` http-huvudet. Token kan utelämnas om hybrid anslutningen har kon figurer ATS för att tillåta anonyma begär Anden.

Eftersom tjänsten fungerar effektivt som en proxy, även om den inte är en sann HTTP-proxy, lägger den till ett `Via` sidhuvud eller kommenterar den befintliga `Via` rubriken som är kompatibel med [RFC7230, avsnitt 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
Tjänsten lägger till relä namn områdets värdnamn i `Via` .

| Kod | Meddelande  | Beskrivning                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | Begäran har hanterats av minst en lyssnare.  |
| 202  | Har godkänts | Begäran har accepterats av minst en lyssnare. |

Om det uppstår ett fel kan tjänsten svara på följande sätt. Huruvida svaret kommer från tjänsten eller från lyssnaren kan identifieras genom närvaron av `Via` huvudet. Om rubriken finns är svaret från lyssnaren.

| Kod | Fel           | Beskrivning
| ---- | --------------- |--------- |
| 404  | Hittades inte       | Sökvägen till hybrid anslutningen är ogiltig eller också är bas-URL: en felaktig.
| 401  | Behörighet saknas    | Säkerhetstoken saknas eller är felaktig eller ogiltig.
| 403  | Förbjudet       | Säkerhetstoken är inte giltig för den här sökvägen och för den här åtgärden.
| 500  | Internt fel  | Något gick fel i tjänsten.
| 503  | Felaktig gateway     | Det gick inte att dirigera begäran till någon lyssnare.
| 504  | Gateway-timeout | Begäran skickades till en lyssnare, men lyssnaren bekräftade inte kvittot på den begärda tiden.

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar om Relay](relay-faq.md)
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Kom igång med .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)
