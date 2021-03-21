---
title: 'Översikt över API: er för Azure Relay Node | Microsoft Docs'
description: Den här artikeln innehåller en översikt över Node.js API för tjänsten Azure Relay. Det visar också hur du använder Hyco-WS-Node-paketet.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 558f49c09203192ff4cbb1af392eaeef8d705c94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91263495"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Översikt över API för relä Hybridanslutningar Node

## <a name="overview"></a>Översikt

[`hyco-ws`](https://www.npmjs.com/package/hyco-ws)Node-paketet för Azure Relay hybridanslutningar bygger på och utökar [WS](https://www.npmjs.com/package/ws) -NPM-paketet. Det här paketet exporterar om alla exporter av det här bas paketet och lägger till nya exporter som möjliggör integrering med funktionen Azure Relay service Hybridanslutningar. 

Befintliga program som `require('ws')` kan använda det här paketet i `require('hyco-ws')` stället, som också möjliggör hybrid scenarier där ett program kan lyssna efter WebSocket-anslutningar lokalt från "inuti brand väggen" och via hybridanslutningar, samtidigt.
  
## <a name="documentation"></a>Dokumentation

API: erna [dokumenteras i huvud paketet WS](https://github.com/websockets/ws/blob/master/doc/ws.md). I den här artikeln beskrivs hur det här paketet skiljer sig från den bas linjen. 

De viktigaste skillnaderna mellan bas paketet och detta ' Hyco-ws ' är att lägga till en ny server klass, exporterad via `require('hyco-ws').RelayedServer` och några hjälp metoder.

### <a name="package-helper-methods"></a>Hjälp metoder för paket

Det finns flera verktygs metoder som är tillgängliga för paket exporten som du kan referera till på följande sätt:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Hjälp metoder är för användning med det här paketet, men det kan också användas av en nod Server för att aktivera webb-eller enhets klienter för att skapa lyssnare eller sändare. Servern använder de här metoderna genom att skicka dem till URI: er som bäddar in korta livs längds-token. Dessa URI: er kan också användas med vanliga WebSocket-stackar som inte har stöd för att ange HTTP-huvuden för WebSocket-handskakningen. Inbäddning av autentiseringstoken i URI: n stöds främst för dessa bibliotek – externa användnings scenarier. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Skapar en giltig Azure Relay lyssnar-URI för Hybrid anslutning för det angivna namn området och sökvägen. Denna URI kan sedan användas med relä versionen av klassen WebSocketServer.

- `namespaceName` (obligatoriskt) – det domän-kvalificerade namnet på Azure Relay namn området som ska användas.
- `path` (obligatoriskt) – namnet på en befintlig Azure Relay hybrid anslutning i det namn området.
- `token` (valfritt) – en tidigare utfärdad Relay-åtkomsttoken som är inbäddad i lyssnar-URI: n (se följande exempel).
- `id` (valfritt) – ett spårnings-ID som gör det möjligt att spåra begär Anden från slut punkt till slut punkt.

`token`Värdet är valfritt och bör endast användas när det inte går att skicka HTTP-huvuden tillsammans med WebSocket-handskakning, som är fallet med W3C WebSocket-stacken.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Skapar en giltig Azure Relay hybrid anslutning som skickar URI för det angivna namn området och sökvägen. Denna URI kan användas med valfri WebSocket-klient.

- `namespaceName` (obligatoriskt) – det domän-kvalificerade namnet på Azure Relay namn området som ska användas.
- `path` (obligatoriskt) – namnet på en befintlig Azure Relay hybrid anslutning i det namn området.
- `token` (valfritt) – en tidigare utfärdad Relay-åtkomsttoken som är inbäddad i sändnings-URI (se följande exempel).
- `id` (valfritt) – ett spårnings-ID som gör det möjligt att spåra begär Anden från slut punkt till slut punkt.

`token`Värdet är valfritt och bör endast användas när det inte går att skicka HTTP-huvuden tillsammans med WebSocket-handskakning, som är fallet med W3C WebSocket-stacken.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Skapar en Azure Relay signatur för signatur för delad åtkomst (SAS) för den angivna mål-URI: n, SAS-regeln och SAS-regelsamlingen som är giltig för det angivna antalet sekunder eller en timme från det aktuella omedelbart om argumentet förfallo datum utelämnas.

- `uri` (krävs) – den URI för vilken token ska utfärdas. URI: n är normaliserad att använda HTTP-schemat och information om frågesträngen tas bort.
- `ruleName` (obligatoriskt) – SAS-regelsamlingen för antingen entiteten som representeras av angiven URI eller för namn området som representeras av URI-värdservern.
- `key` (obligatoriskt) – giltig nyckel för SAS-regeln. 
- `expirationSeconds` (valfritt) – antalet sekunder tills den genererade token ska upphöra att gälla. Om inget värde anges är standardvärdet 1 timme (3600).

Utfärdad token ger de rättigheter som är associerade med den angivna SAS-regeln för angiven varaktighet.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Den här metoden fungerar som likvärdig med `createRelayToken` metoden som dokumenterats tidigare, men returnerar token som är korrekt tillagd i Indataporten.

### <a name="class-wsrelayedserver"></a>Klass ws. RelayedServer

`hycows.RelayedServer`Klassen är ett alternativ till den `ws.Server` klass som inte lyssnar på det lokala nätverket, men som delegerar lyssning till Azure Relay tjänsten.

De två klasserna är främst kontrakts kompatibla, vilket innebär att ett befintligt program som använder `ws.Server` klassen enkelt kan ändras för att använda den vidarebefordrade versionen. De huvudsakliga skillnaderna finns i konstruktorn och i de tillgängliga alternativen.

#### <a name="constructor"></a>Konstruktor  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

`RelayedServer`Konstruktorn har stöd för en annan uppsättning argument än `Server` , eftersom den inte är en fristående lyssnare eller kan bäddas in i ett befintligt http Listener Framework. Det finns också färre tillgängliga alternativ eftersom WebSocket-hanteringen är i stort sett delegerad till relä tjänsten.

Argument för konstruktorn:

- `server` (obligatoriskt) – den fullständigt kvalificerade URI: n för ett hybrid anslutnings namn som du vill lyssna på, vanligt vis konstruerad med hjälp av WebSocket. createRelayListenUri ().
- `token` (obligatoriskt) – det här argumentet innehåller antingen en tidigare Utfärdad token-sträng eller en callback-funktion som kan anropas för att hämta en sådan token-sträng. Alternativet motringning är att föredra, eftersom det möjliggör förnyelse av token.

#### <a name="events"></a>Händelser

`RelayedServer` instanser genererar tre händelser som gör att du kan hantera inkommande begär Anden, upprätta anslutningar och identifiera fel villkor. Du måste prenumerera på `connect` händelsen för att hantera meddelanden. 

##### <a name="headers"></a>sidhuvud

```JavaScript 
function(headers)
```

`headers`Händelsen höjs precis innan en inkommande anslutning godkänns, vilket möjliggör ändring av huvudena som ska skickas till klienten. 

##### <a name="connection"></a>anslutning

```JavaScript
function(socket)
```

Genereras när en ny WebSocket-anslutning godkänns. Objektet är av typen `ws.WebSocket` , samma som med bas paketet.


##### <a name="error"></a>fel

```JavaScript
function(error)
```

Om den underliggande servern avger ett fel vidarebefordras den här.  

#### <a name="helpers"></a>Hjälp

För att förenkla starten av en vidarebefordrad Server och prenumerera direkt på inkommande anslutningar, visar paketet en enkel hjälp funktion, som också används i exemplen, enligt följande:

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Den här metoden anropar konstruktorn för att skapa en ny instans av RelayedServer och prenumererar sedan på den angivna återanropet till "Connection"-händelsen.
 
##### <a name="relayedconnect"></a>relayedConnect

Att spegla `createRelayedServer` hjälpen i-funktionen `relayedConnect` skapar en klient anslutning och prenumererar på händelsen "Open" på den resulterande socketen.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Azure Relay går du till följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Tillgängliga relä-API: er](relay-api-overview.md)
