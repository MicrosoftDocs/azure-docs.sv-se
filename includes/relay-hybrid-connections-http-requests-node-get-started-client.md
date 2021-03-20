---
title: ta med fil
description: ta med fil
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 3c18efa7eb520b765c9bb3c2aff00104f971f5a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187581"
---
### <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Om du inaktiverade alternativet ”Klientauktorisering krävs” när du skapade reläet kan du skicka förfrågningar till URL:en för Hybridanslutningar i valfri webbläsare. För att komma åt skyddade slutpunkter måste du skapa och skicka en token i `ServiceBusAuthorization`-huvudet, som du ser här.

Starta genom att skapa en ny JavaScript-fil med namnet `sender.js`.

### <a name="add-the-relay-npm-package"></a>Lägg till Relay NPM-paketet

Kör `npm install hyco-https` från Kommandotolken för en nod i projektmappen. Med det här paketet importeras även det vanliga `https`-paketet. För klientsidan är den viktigaste skillnaden att paketet innehåller funktioner för att skapa Relay-URI:er och token.

### <a name="write-some-code-to-send-messages"></a>Skriva kod för att skicka meddelanden

1. Lägg till följande `constants` överst i `sender.js`-filen.
   
    ```js
    const https = require('hyco-https');
    ```

2. Lägg till följande konstanter i filen `sender.js` för hybridanslutningsinformationen. Ersätt platshållarna inom hakparentes med de värden du erhöll när du skapade hybridanslutningen.
   
   1. `const ns` – Relay-namnområde. Se till att du använder det fullständiga namnområdesnamnet, till exempel `{namespace}.servicebus.windows.net`.
   2. `const path` – Namnet på hybridanslutningen.
   3. `const keyrule` – Namnet på SAS-nyckeln.
   4. `const key` – SAS-nyckelvärdet.

3. Lägg till följande kod i `sender.js`-filen: Du kommer att märka att koden inte skiljer sig avsevärt från den vanliga användningen av HTTPS-klienten i Node.js. Den lägger bara till auktoriseringshuvudet.
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    Så här bör din sender.js-fil se ut:
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

