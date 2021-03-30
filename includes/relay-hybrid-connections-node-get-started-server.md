---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 531866ece1c4acacb926c9e9c6598158c1aa077f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187933"
---
### <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Skapa en ny JavaScript-fil som kallas `listener.js`.

### <a name="add-the-relay-npm-package"></a>Lägg till Relay NPM-paketet

Kör `npm install hyco-ws` från Kommandotolken för en nod i projektmappen.

### <a name="write-some-code-to-receive-messages"></a>Skriva kod för att ta emot meddelanden

1. Lägg till följande konstant överst i filen `listener.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Lägg till följande konstanter i filen `listener.js` för hybridanslutningsinformationen. Ersätt platshållarna inom hakparentes med de värden du erhöll när du skapade hybridanslutningen.
   
   1. `const ns` – Relay-namnområde. Se till att du använder det fullständiga namnområdesnamnet, till exempel `{namespace}.servicebus.windows.net`.
   2. `const path` – Namnet på hybridanslutningen.
   3. `const keyrule` – Namnet på SAS-nyckeln.
   4. `const key` – SAS-nyckelvärdet.

3. Lägg till följande kod i `listener.js`-filen:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    Så här bör din listener.js-fil se ut:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

