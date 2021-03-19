---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 9d4f7faa18ee7fae158afb42b8c42287e61dd103
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187930"
---
### <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Skapa en ny JavaScript-fil som kallas `sender.js`.

### <a name="add-the-relay-npm-package"></a>Lägg till Relay NPM-paketet

Kör `npm install hyco-ws` från Kommandotolken för en nod i projektmappen.

### <a name="write-some-code-to-send-messages"></a>Skriva kod för att skicka meddelanden

1. Lägg till följande `constants` överst i `sender.js`-filen.
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. Lägg till följande konstanter i filen `sender.js` för hybridanslutningsinformationen. Ersätt platshållarna inom hakparentes med de värden du erhöll när du skapade hybridanslutningen.
   
   1. `const ns` – Relay-namnområde. Se till att du använder det fullständiga namnområdesnamnet, till exempel `{namespace}.servicebus.windows.net`.
   2. `const path` – Namnet på hybridanslutningen.
   3. `const keyrule` – Namnet på SAS-nyckeln.
   4. `const key` – SAS-nyckelvärdet.

3. Lägg till följande kod i `sender.js`-filen:
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    Så här bör din sender.js-fil se ut:
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

