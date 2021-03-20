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
ms.openlocfilehash: fdd4e26839661ab9765b7d496e7f60c3686ba637
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187580"
---
### <a name="create-a-nodejs-application"></a>Skapa ett Node.js-program

Skapa en ny JavaScript-fil som kallas `listener.js`.

### <a name="add-the-relay-npm-package"></a>Lägg till Relay NPM-paketet

Kör `npm install hyco-https` från Kommandotolken för en nod i projektmappen.

### <a name="write-some-code-to-handle-requests"></a>Skriv kod för att hantera begäranden

1. Lägg till följande konstant överst i filen `listener.js`.

    ```js
    const https = require('hyco-https');
    ```
2. Lägg till följande konstanter i filen `listener.js` för hybridanslutningsinformationen. Ersätt platshållarna inom hakparentes med de värden du erhöll när du skapade hybridanslutningen.

   1. `const ns` – Relay-namnområde. Se till att du använder det fullständiga namnområdesnamnet, till exempel `{namespace}.servicebus.windows.net`.
   2. `const path` – Namnet på hybridanslutningen.
   3. `const keyrule` – Namnet på SAS-nyckeln.
   4. `const key` – SAS-nyckelvärdet.

3. Lägg till följande kod i `listener.js`-filen: :

    Du kommer att märka att koden inte skiljer sig avsevärt från andra enkla HTTP-serverexempel i Node.js-självstudierna för nybörjare, förutom användningen av `createRelayedServer` i stället för den typiska `createServer`-funktionen.

    ```js
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```
    Så här bör din listener.js-fil se ut:
   
    ```js
    const https = require('hyco-https');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```

