---
title: Azure IoT Hub MQTT 5-support (för hands version)
description: Läs mer om IoT Hub 5-support för MQTT
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603556"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>Översikt över IoT Hub MQTT 5 support (för hands version)

**Version:** 2,0 **API-version:** 2020-10-01 – för hands version

Det här dokumentet definierar IoT Hub data Plans-API över MQTT version 5,0-protokollet. Se [API-referens](iot-hub-mqtt-5-reference.md) för fullständiga definitioner i detta API.

## <a name="prerequisites"></a>Förutsättningar

- [Aktivera förhands gransknings läget](iot-hub-preview-mode.md) på en helt ny IoT Hub för att testa MQTT 5.
- Tidigare kunskaper om [MQTT 5-specifikation](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) krävs.

## <a name="level-of-support-and-limitations"></a>Support nivå och begränsningar

IoT Hub stöd för MQTT 5 är i för hands version och är begränsat på följande sätt (kommunicerat med klienten via `CONNACK` Egenskaper om inget annat anges):

- Inget officiellt stöd för [Azure IoT Hub Device SDK](iot-hub-devguide-sdks.md) ännu.
- Prenumerations-ID: n stöds inte.
- Delade prenumerationer stöds inte.
- `RETAIN` stöds inte.
- `Maximum QoS` är `1`.
- `Maximum Packet Size` är `256 KiB` (omfattas av ytterligare begränsningar per åtgärd).
- Tilldelade klient-ID: n stöds inte.
- `Keep Alive` är begränsad till `19 min` (maximal fördröjning för Live-kontroll – `28.5 min` ).
- `Topic Alias Maximum` är `10`.
- `Response Information` stöds inte; `CONNACK` returnerar inte `Response Information` egenskapen även om `CONNECT` innehåller `Request Response Information` egenskapen.
- `Receive Maximum` (maximalt antal tillåtna utestående `PUBLISH` ignorerade paket (i klient-server riktningen) med `QoS: 1` ) är `16` .
- En enskild klient kan inte ha fler än `50` prenumerationer.
  När gränsen har nåtts `SUBACK` returneras `0x97` (kvot överskriden) orsaks kod för prenumerationer.

## <a name="connection-lifecycle"></a>Anslutningens livs cykel

### <a name="connection"></a>Anslutning

Upprätta anslutning per MQTT 5-specifikation för att ansluta en klient till IoT Hub med hjälp av detta API.
Klienten måste skicka `CONNECT` paket inom 30 sekunder efter lyckad TLS-handskakning, eller så stänger servern anslutningen.
Här är ett exempel på `CONNECT` paket:

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` Egenskapen är obligatorisk och identifierar vilken autentiseringsmetod som används. Mer information om autentiseringsmetoder finns i [autentisering](#authentication).
- `Authentication Data` egenskaps hantering är beroende av `Authentication Method` . Om `Authentication Method` är inställt på `SAS` , `Authentication Data` krävs det och måste innehålla en giltig signatur. Mer information om autentiseringsdata finns i [autentisering](#authentication).
- `api-version` Egenskapen är obligatorisk och måste anges till API-version svärdet i denna Specifikations huvud för att denna specifikation ska gälla.
- `host` egenskap definierar klient organisationens värd namn. Det krävs om inte SNI-tillägget har presenter ATS i klient Hello-posten under TLS-handskakning
- `sas-at` definierar tid för anslutning.
- `sas-expiry` definierar förfallo tid för de angivna SAS: erna.
- `client-agent` kommunicerar eventuellt information om klienten som skapar anslutningen.

> [!NOTE]
> `Authentication Method` och andra egenskaper i specifikationen med inkapitaliserade namn är första klass egenskaperna i MQTT 5 – de beskrivs i informationen i MQTT 5-specifikationen. `api-version` och andra egenskaper i bindestreck är användar egenskaper som är speciella för IoT Hub API.

IoT Hub svarar med `CONNACK` ett paket när det är klart med autentisering och hämtning av data som stöd för anslutningen. Om anslutningen har upprättats, `CONNACK` ser ut så här:

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

Dessa `CONNACK` paket egenskaper följer [MQTT 5-specifikationen](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080). De återspeglar IoT Hub funktionerna.

### <a name="authentication"></a>Autentisering

`Authentication Method`Egenskapen på `CONNECT` klienten definierar vilken typ av autentisering som används för den här anslutningen:

- `SAS` – Signaturen för delad åtkomst tillhandahålls `CONNECT` i `Authentication Data` egenskapen,
- `X509` – klienten använder autentisering av klient certifikat.

 Autentiseringen Miss lyckas om autentiseringsmetoden inte matchar klientens konfigurerade metod i IoT Hub.

> [!NOTE]
> Detta API kräver `Authentication Method` att en egenskap anges i `CONNECT` paketet. Om `Authentication Method` egenskapen inte anges, Miss lyckas anslutningen med `Bad Request` Response.

Autentisering av användar namn/lösen ord som används i tidigare API-versioner stöds inte.

#### <a name="sas"></a>SAS

Med SAS-baserad autentisering måste klienten tillhandahålla signaturen för anslutnings kontexten. Detta bekräftar att MQTT-anslutningen är äkta. Signaturen måste baseras på en av två autentiseringsinställningar i klientens konfiguration i IoT Hub eller en av två delade åtkomst nycklar för en [princip för delad åtkomst](iot-hub-devguide-security.md).

Strängen som ska signeras måste skapas på följande sätt:

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` härleds antingen från SNI-tillägget (visas av klienten i klientens hello-post under TLS-handskakning) eller `host` användar egenskap i `CONNECT` paket.
- `Client Id` är klient identifierare i `CONNECT` paket.
- `sas-policy` – om det finns definierar IoT Hub åtkomst princip som används för autentisering. Den är kodad som användar egenskap för `CONNECT` paket. Valfritt: utelämnande innebär att autentiseringsinställningar i enhets registret används i stället.
- `sas-at` – om det är tillgängligt anger tiden för anslutningens aktuella tid. Den är kodad som användar egenskap av `time` typen i `CONNECT` paket.
- `sas-expiry` definierar förfallo tid för autentiseringen. Det är en `time` användar egenskap som anges i `CONNECT` paket. Den här egenskapen är obligatorisk.

För valfria parametrar, om det utelämnas, måste en tom sträng användas i stället i sträng för att signera.

HMAC-SHA256 används för att Hasha strängen baserat på en av enhetens symmetriska nycklar. Hash-värdet anges sedan som värde för `Authentication Data` egenskapen.

#### <a name="x509"></a>X509

Om `Authentication Method` egenskapen är inställd på `X509` , IoT Hub autentiserar anslutningen utifrån det angivna klient certifikatet.

#### <a name="reauthentication"></a>Omautentisering

Om SAS-baserad autentisering används rekommenderar vi att du använder token med kort livs längd. För att hålla anslutningen autentiserad och förhindra från koppling på grund av förfallo datum, måste klienten autentisera igen genom att skicka `AUTH` paket med `Reason Code: 0x19` (reautentisering):

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

Uttryck

- `Authentication Method` måste vara samma som det som används för inledande autentisering
- Om anslutningen ursprungligen autentiserades med SAS baserat på principen för delad åtkomst måste signaturen som används i reautentisering baseras på samma princip.

Om omautentiseringen lyckas skickar IoT Hub `AUTH` paketet med `Reason Code: 0x00` (lyckades). Annars skickar IoT Hub `DISCONNECT` paket med `Reason Code: 0x87` (inte auktoriserad) och stänger anslutningen.

### <a name="disconnection"></a>Från koppling

Servern kan inte koppla från klienten av några skäl:

- klienten är inte i ett sådant sätt att den inte kan svara på med negativ bekräftelse (eller svar) direkt,
- servern håller på att hålla nere anslutnings status,
- klienten med samma identitet har anslutits.

Servern kan koppla från alla orsaks koder som definieras i MQTT 5,0-specifikationen. Viktiga omnämnanden:

- `135` (Inte auktoriserad) när omautentiseringen Miss lyckas, den aktuella SAS-token upphör att gälla eller när enhetens autentiseringsuppgifter ändras
- `142` (Sessionen tog över) när en ny anslutning med samma klient identitet har öppnats.
- `159` (Anslutnings frekvensen överskreds) när anslutnings hastigheten för IoT-hubben överskrider  
- `131` (Implementations-Specific error) används för anpassade fel som definierats i detta API. `status` och `reason` egenskaper kommer att användas för att förmedla ytterligare information om orsaken till från koppling (se [svar](#response) för information).

## <a name="operations"></a>Operations

Alla funktioner i detta API uttrycks som åtgärder. Här är ett exempel på hur du skickar telemetri:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

Fullständig specifikation av åtgärder i det här API: et finns i [API-referens](iot-hub-mqtt-5-reference.md).

> [!NOTE]
> Alla exempel i den här specifikationen visas från klientens perspektiv. Signering `->` innebär att klienten skickar paket, `<-` -tar emot.

### <a name="message-topics-and-subscriptions"></a>Meddelande ämnen och prenumerationer

Ämnen som används i Operations-meddelanden i den här API: n börjar med `$iothub/` .
MQTT-Broker-semantik gäller inte för dessa åtgärder (se "[ämnen som \$ börjar med ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)" för information).
Ämnen som börjar med `$iothub/` som inte har definierats i detta API stöds inte:

- Att skicka meddelanden till odefinierade ämnen resulterar i `Not Found` svar (se [svar](#response) på information nedan).
- Prenumerera på odefinierat ämnes resultat i `SUBACK` med `Reason Code: 0x8F` (ämnes filtret är ogiltigt).

Ämnes namn och egenskaps namn är Skift läges känsliga och måste vara en exakt matchning. Till exempel `$iothub/telemetry/` stöds inte när `$iothub/telemetry` är.

> [!NOTE]
> Jokertecken i prenumerationer `$iothub/..` stöds inte. Det vill säga att en klient inte kan prenumerera på `$iothub/+` eller `$iothub/#` . Försöker göra detta i `SUBACK` with `Reason Code: 0xA2` (jokertecken stöds inte). Endast jokertecken () med ett enda segment `+` stöds i stället för Sök vägs parametrar i ämnes namn för åtgärder som har dem.

### <a name="interaction-types"></a>Interaktions typer

Alla åtgärder i detta API baseras på en av två interaktions typer:

- Meddelande med valfri bekräftelse (MessageAck)
- Request-Response (ReqRep)

Åtgärder varierar också efter riktning (bestäms av riktning för inledande meddelande i Exchange):

- Klient-till-Server (c2s)
- Server-till-klient (s2c)

Till exempel är skicka telemetri en klient-till-server-åtgärd av typen "meddelande med bekräftelse", medan den direkta metoden för att hantera Direct är en server-till-klient-åtgärd av Request-Response typ.

#### <a name="message-acknowledgement-interactions"></a>Meddelande – bekräftelse interaktion

Meddelande med en MessageAck-interaktion (valfritt bekräftelse) uttrycks som ett utbyte av `PUBLISH` och `PUBACK` paket i MQTT. Bekräftelsen är valfri och avsändaren kan välja att inte begära det genom att skicka `PUBLISH` paket med `QoS: 0` .

> [!NOTE]
> Om egenskaper i `PUBACK` paket måste trunkeras på grund av att `Maximum Packet Size` klienten deklareras, så behåller IoT Hub så många användar egenskaper som de får plats inom den angivna gränsen. De användar egenskaper som anges först har en högre chans att skickas än de som listas senare. `Reason String` egenskapen har minst prioritet.

##### <a name="example-of-simple-messageack-interaction"></a>Exempel på enkel MessageAck-interaktion

Meddelande:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

Bekräftelse (lyckades):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Request-Response interaktioner

I Request-Response (ReqRep)-interaktioner översätts både begäran och svar till `PUBLISH` paket med `QoS: 0` .

`Correlation Data` egenskapen måste anges i både och används för att matcha svars paketet för att begära paket.

Detta API använder enskilt svar `$iothub/responses` för alla ReqRep-åtgärder. Att prenumerera på/avbryta från det här avsnittet för klient-till-Server-åtgärder krävs inte – servern förutsätter att alla klienter måste prenumerera.

##### <a name="example-of-simple-reqrep-interaction"></a>Exempel på enkel ReqRep-interaktion

Begäran:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Svar (lyckades):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

ReqRep-interaktioner stöder inte `PUBLISH` paket med `QoS: 1` som begäran eller svarsmeddelanden. `PUBLISH`Resultat av begäran skickas som `Bad Request` svar.

Den maximala längd som stöds i `Correlation Data` egenskapen är 16 byte. Om `Correlation Data` egenskapen för `PUBLISH` paket har angetts till ett värde som är längre än 16 byte, IoT Hub skickas `DISCONNECT` med `Bad Request` resultat och anslutningen stängs. Detta beteende gäller endast paket som utbyts i detta API.

> [!NOTE]
> Korrelations data är en godtycklig byte-sekvens, t. ex. att det inte garanterar UTF-8-sträng.
>
> ReqRep använder fördefinierade ämnen för svar. Svars ämnes egenskapen i Request `PUBLISH` -paketet (om den anges av avsändaren) ignoreras.

IoT Hub prenumererar automatiskt på klient till svars ämnen för alla ReqRep-åtgärder från klient till server. Även om klienten avbryter prenumerationen från svars avsnittet återupprättar IoT Hub prenumerationen automatiskt. För ReqRep-interaktioner från server till klient är det fortfarande nödvändigt för att enheten ska kunna prenumerera.

### <a name="message-properties"></a>Meddelande egenskaper

Åtgärds egenskaper-system eller användardefinierad-anges som paket egenskaper i MQTT 5.

Användar egenskaps namn är Skift läges känsliga och måste stavas exakt som de definieras. Till exempel `Trace-ID` stöds inte när `trace-id` är.

Begär Anden med användar egenskaper utanför specifikationen och utan prefix `@` resulterar i fel.

System egenskaper kodas antingen som första klass egenskaper (till exempel `Content Type` ) eller som användar egenskaper. Specifikationen innehåller en fullständig lista över system egenskaper som stöds.
Alla första klass egenskaper ignoreras om inte stöd för dem uttryckligen anges i specifikationen.

När användardefinierade egenskaper är tillåtna måste deras namn följa formatet `@{property name}` . Användardefinierade egenskaper stöder endast giltiga UTF-8-sträng värden. till exempel `MyProperty1` måste egenskap med värde `15` vara kodat som användar egenskap med namn `@MyProperty` och värde `15` .

Om IoT Hub inte känner igen egenskapen användare, betraktas det som ett fel och IoT Hub svarar med `PUBACK` med `Reason Code: 0x83` (fel i implementationen) och `status: 0100` (felaktig begäran). Om bekräftelse inte begärdes (QoS: 0) `DISCONNECT` skickas paket med samma fel tillbaka och anslutningen avbryts.

I detta API definieras följande data typer förutom `string` :

- `time`: antal millisekunder sedan `1970-01-01T00:00:00.000Z` . till exempel `1600987195320` för `2020-09-24T22:39:55.320Z` ,
- `u32`: osignerat 32-bitars heltals nummer,
- `u64`: osignerat 64-bitars heltals nummer,
- `i32`: signerat 32-bitars heltals nummer.

### <a name="response"></a>Svarsåtgärder

Interaktioner kan resultera i olika resultat: `Success` ,, `Bad Request` `Not Found` och andra.
Resultat särskiljs från varandra av `status` användar egenskapen. `Reason Code` i `PUBACK` paket (för MessageAck-interaktioner) matchar `status` när det är möjligt.

> [!NOTE]
> Om klienten anger `Request Problem Information: 0` i Connect-paket skickas inga användar egenskaper `PUBACK` till paket för att uppfylla MQTT 5-specifikation, inklusive `status` egenskap. I det här fallet kan klienten fortfarande förlita sig på `Reason Code` att avgöra om bekräftelsen är positiv eller negativ. 

Varje interaktion har standardvärdet (eller lyckades). Den har `Reason Code` `0` och har `status` egenskapen "ej angivet". I annat fall:

- För MessageAck-interaktioner `PUBACK` får `Reason Code` andra än 0x0 (lyckades). `status` Egenskapen kan finnas för att ytterligare klargöra resultatet.
- För ReqRep-interaktioner `PUBLISH` hämtar svar `status` egenskaps uppsättningen.
- Eftersom det inte finns något sätt att svara på MessageAck-interaktioner med `QoS: 0` direkt, `DISCONNECT` skickas paket i stället för svars information, följt av koppla från.

Exempel:

Felaktig begäran (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

Inte auktoriserad (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

Inte auktoriserad (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

Vid behov ställer IoT Hub in följande användar egenskaper:

- `status` – IoT Hubs utökade kod för åtgärdens status. Den här koden kan användas för att särskilja resultat.
- `trace-id` – spårnings-ID för åtgärden. IoT Hub kan behålla ytterligare diagnostik för den åtgärd som kan användas för intern undersökning.
- `reason` – mänskligt läsbart meddelande som ger ytterligare information om varför åtgärden har slutförts i ett tillstånd som anges av `status` egenskap.

> [!NOTE]
> Om egenskapen client set `Maximum Packet Size` i Connect-paket till ett mycket litet värde, kan inte alla användar egenskaper passa och visas inte i paketet.
> 
> `reason` är endast avsett för personer och ska inte användas i klient logiken. Med det här API: et kan meddelanden ändras när som helst utan varning eller ändring av version.
>
> Om klienten skickar `RequestProblemInformation: 0` i Connect-paket tas användar egenskaper inte med i bekräftelser per [MQTT 5-specifikation](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053).

#### <a name="status-code"></a>Statuskod

`status` Egenskapen utför status kod för åtgärden. Den är optimerad för maskin läsnings effektivitet.
Det består av två byte med osignerat heltal kodat som hex i sträng som `0501` .
Kod struktur (bit karta):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

Första byte används för flaggor:

- bitarna 0 och 1 anger typ av resultat:
  - `00` -lyckades
  - `01` – klient fel
  - `10` – Server fel
- bit 2: `1` indikerar att felet går att försöka igen
- BITS 3 till 7 är reserverade och måste anges till `0`

Den andra byten innehåller den faktiska distinkta svars koden. Felkoder med olika flaggor kan ha samma andra byte värde. Det kan till exempel finnas `0001` ,, `0101` `0201` `0301` felkoder som har distinkt innebörd.

Till exempel `Too Many Requests` är en klient, ett nytt fel med egen kod för `1` . Värdet är `0000 0101 0000 0001` eller `0x0501` .

Klienter kan använda typ bitar för att identifiera om åtgärden har slutförts. Klienter kan också använda en försöks stund för att avgöra om det är lämpliga att försöka utföra åtgärden igen.

## <a name="recommendations"></a>Rekommendationer

### <a name="session-management"></a>Sessionshantering

`CONNACK` Packet bär `Session Present` egenskap för att ange om servern återställde en tidigare skapad session. Använd den här egenskapen för att ta reda på om du ska prenumerera på ämnen eller hoppa över prenumeration sedan prenumerationen skedde tidigare.

För att förlita dig `Session Present` måste klienten hålla koll på de prenumerationer som görs (det vill säga skickade `SUBSCRIBE` paket och tas emot `SUBACK` med uppföljnings koden) eller se till att prenumerera på alla ämnen i ett enda `SUBSCRIBE` / `SUBACK` utbyte. Annars, om klienten skickar två `SUBSCRIBE` paket och bara en av dem bearbetas av servern, kommer servern att kommunicera `Session Present: 1` i `CONNACK` samtidigt som endast en del av klientens prenumerationer har godkänts.

För att förhindra att en äldre version av klienten prenumererar på alla ämnen, är det bättre att prenumerera på problem när klientens beteende ändras (till exempel som en del av uppdatering av inbyggd program vara). För att se till att inga inaktuella prenumerationer ligger kvar bakom (från det högsta tillåtna antalet prenumerationer) kan du uttryckligen avbryta prenumerationen på prenumerationer som inte längre används.

### <a name="batching"></a>Batchbearbetning

Det finns inget särskilt format för att skicka en batch med meddelanden. För att minska kostnaderna för resurs intensiva åtgärder i TLS och nätverk, paket paket ( `PUBLISH` , `PUBACK` , `SUBSCRIBE` och så vidare) tillsammans innan du överlämnar dem till den underliggande TLS/TCP-stacken. Dessutom kan klienten göra ämnes Ali Aset enklare i "batch":

- Lägg till fullständigt ämnes namn i det första `PUBLISH` paketet för anslutningen och associera ämnes Ali Aset med det,
- Lägg till följande paket för samma ämne med ett tomt ämnes namn och egenskapen alias.

## <a name="migration"></a>Migrering

I det här avsnittet visas ändringarna i API jämfört med [tidigare MQTT-API](iot-hub-mqtt-support.md).

- Transport protokollet är MQTT 5. Tidigare-MQTT 3.1.1.
- Kontext information för SAS-autentisering finns i `CONNECT` paket direkt i stället för att kodas tillsammans med signaturen.
- Autentiseringsmetoden används för att indikera vilken autentiseringsmetod som används.
- Signaturen för delad åtkomst har lagts till i egenskapen Authentication data. Fältet för tidigare lösen ord användes.
- Ämnen för åtgärder skiljer sig åt:
  - Telemetri: `$iothub/telemetry` i stället för `devices/{Client Id}/messages/events` ,
  - Kommandon: `$iothub/commands` i stället för `devices/{Client Id}/messages/devicebound` ,
  - Snabb korrigeringen har rapporter ATS: `$iothub/twin/patch/reported` i stället för `$iothub/twin/PATCH/properties/reported` ,
  - Meddela att den dubbla önskade statusen har ändrats: `$iothub/twin/patch/desired` i stället för `$iothub/twin/PATCH/properties/desired` .
- Prenumerationen för svars avsnittet för Request-Response-åtgärder för klient-server krävs inte.
- Användar egenskaper används i stället för kodnings egenskaper i ämnes namn segment.
- egenskaps namn är rättstavade i namngivnings konventionen "bindestreck" i stället för förkortningar med särskilda prefix. För användardefinierade egenskaper krävs prefix i stället. Till exempel, `$.mid` är nu `message-id` , men `myProperty1` blir det `@myProperty1` .
- Korrelations data egenskapen används för att korrelera begär ande-och svars meddelanden för Request-Response-åtgärder i stället för `$rid` egenskap som är kodad i avsnittet.
- `iothub-connection-auth-method` Egenskapen stämplas inte längre för telemetri händelser.
- C2D-kommandon rensas inte eftersom det inte finns någon prenumeration på enheten. De förblir köade tills enheten prenumererar eller upphör att gälla.

## <a name="examples"></a>Exempel

### <a name="send-telemetry"></a>Skicka telemetri

Meddelande:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

Godkänd

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

Alternativ bekräftelse (begränsad):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>Skicka get till dubbla

Begäran:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

Svar (lyckades):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

Svar (tillåts inte):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>Hantera Direct-metod anrop

Begäran:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Svar (lyckades):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` är inte inställd – det är ett lyckat svar.

Ej tillgängligt enhets svar:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>Fel vid användning av QoS 0, del 1

Begäran:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Svar:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>Fel vid användning av QoS 0, del 2

Begäran:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

Svar:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>Nästa steg

- Information om hur du granskar MQTT 5 finns i [IoT Hub data plan MQTT 5 API-referens](iot-hub-mqtt-5-reference.md).
- Information om hur du följer ett C#-exempel finns i [GitHub Sample Central](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp).