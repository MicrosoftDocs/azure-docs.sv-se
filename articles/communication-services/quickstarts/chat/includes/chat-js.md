---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 0805537fe0791a622eb1814cc233c04d914dbecd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612665"
---
## <a name="prerequisites"></a>Förutsättningar
Innan du börjar ska du se till att:

- Skapa ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installera [Node.js](https://nodejs.org/en/download/) Active LTS och underhåll LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
- Skapa en Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../create-communication-resource.md). Du måste **Registrera resurs slut punkten** för den här snabb starten.
- Skapa *tre* ACS-användare och utfärda dem till användar åtkomst [token](../../access-tokens.md)för användar åtkomst. Var noga med att ange omfånget till **chatten** och **notera token-strängen och userId-strängen**. Den fullständiga demon skapar en tråd med två första deltagare och lägger sedan till en tredje deltagare i tråden.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-web-application"></a>Skapa ett nytt webb program

Först öppnar du terminalen eller kommando fönstret för att skapa en ny katalog för din app och navigerar till den.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Kör `npm init -y` för att skapa en **package.jspå** en fil med standardinställningar.

```console
npm init -y
```

### <a name="install-the-packages"></a>Installera paketen

Använd `npm install` kommandot för att installera klient biblioteken nedan för kommunikations tjänster för Java Script.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

I det här `--save` alternativet visas biblioteket som ett beroende i **package.jsi** filen.

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

I den här snabb starten används WebPack för att paketera program till gångarna. Kör följande kommando för att installera WebPack-, WebPack-CLI-och WebPack-dev-Server NPM-paket och lista dem som utvecklings beroenden i din **package.jspå**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Skapa en `webpack.config.js` fil i rot katalogen. Kopiera följande konfiguration till den här filen:

```
module.exports = {
  entry: "./client.js",
  output: {
    filename: "bundle.js"
  },
  devtool: "inline-source-map",
  mode: "development"
}
```

Lägg till ett `start` skript till din `package.json` , vi använder detta för att köra appen. Inuti `scripts` avsnittet i `package.json` Lägg till följande:

```
"scripts": {
  "start": "webpack serve --config ./webpack.config.js"
}
```

Skapa en **index.html** -fil i projektets rot Katalog. Vi använder den här filen som en mall för att lägga till chatt-funktioner med hjälp av klient biblioteket för Azure Communication Chat för Java Script.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Skapa en fil i projektets rot Katalog som kallas **client.js** som innehåller program logiken för den här snabb starten.

### <a name="create-a-chat-client"></a>Skapa en Chat-klient

Om du vill skapa en chatt-klient i din webbapp använder du SIP- **slutpunkten** för kommunikation **och den åtkomsttoken** som genererades som en del av de nödvändiga stegen.

Med token för användar åtkomst kan du skapa klient program som direkt autentiserar till Azure Communication Services. Den här snabb starten omfattar inte att skapa en tjänst nivå för att hantera tokens för chatt-programmet. Se [chatt-koncept](../../../concepts/chat/concepts.md) för mer information om chatt-arkitektur och [åtkomsttoken för användar åtkomst](../../access-tokens.md) för mer information om åtkomsttoken.

Inuti **client.js** använder du slut punkten och åtkomsttoken i koden nedan för att lägga till chatt-funktioner med hjälp av klient biblioteket för Azure Communication Chat för Java Script.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- Ersätt **endpointUrl** med resurs slut punkten för kommunikations tjänster, se [skapa en Azure Communication-resurs](../../create-communication-resource.md) om du inte redan har gjort det.
- Ersätt **userAccessToken** med den token som du utfärdade.


### <a name="run-the-code"></a>Kör koden

Kör följande kommando för att paketera program värden i på en lokal webserver:
```console
npm run start
```
Öppna webbläsaren och gå till http://localhost:8080/ .
I konsolen för utvecklarverktyg i webbläsaren bör du se följande:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Objekt modell
Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Servicess Chat-klient bibliotek för Java Script.

| Name                                   | Beskrivning                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Den här klassen krävs för chatt-funktionen. Du instansierar den med din prenumerations information och använder den för att skapa, hämta och ta bort trådar. |
| ChatThreadClient | Den här klassen krävs för chatt-trådens funktion. Du får en instans via ChatClient och använder den för att skicka/ta emot/uppdatera/ta bort meddelanden, lägga till/ta bort/hämta användare, skicka meddelanden och läsa kvitton, prenumerera på chatt-händelser. |


## <a name="start-a-chat-thread"></a>Starta en chatt-tråd

Använd `createThread` metoden för att skapa en chatt-tråd.

`createThreadRequest` används för att beskriva tråd förfrågan:

- Används `topic` för att ge ett ämne till den här chatten. Ämnen kan uppdateras när chatt-tråden har skapats med hjälp av `UpdateThread` funktionen.
- Används `participants` för att visa en lista över deltagare som ska läggas till i chatt-tråden.

Vid åtgärdat `createChatThread` returnerar metoden en `CreateChatThreadResult` . Den här modellen innehåller en `chatThread` egenskap där du kan komma åt den `id` nya tråd som skapats. Du kan sedan använda `id` för att hämta en instans av en `ChatThreadClient` . `ChatThreadClient`Kan sedan användas för att utföra åtgärder i tråden, t. ex. skicka meddelanden eller lista deltagare.

```JavaScript
async function createChatThread() {
    let createThreadRequest = {
        topic: 'Preparation for London conference',
        participants: [{
                    id: { communicationUserId: '<USER_ID_FOR_JACK>' },
                    displayName: 'Jack'
                }, {
                    id: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                    displayName: 'Geeta'
                }]
    };
    let createChatThreadResult = await chatClient.createChatThread(createThreadRequest);
    let threadId = createChatThreadResult.chatThread.id;
    return threadId;
    }

createChatThread().then(async threadId => {
    console.log(`Thread created:${threadId}`);
    // PLACEHOLDERS
    // <CREATE CHAT THREAD CLIENT>
    // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
    // <SEND MESSAGE TO A CHAT THREAD>
    // <LIST MESSAGES IN A CHAT THREAD>
    // <ADD NEW PARTICIPANT TO THREAD>
    // <LIST PARTICIPANTS IN A THREAD>
    // <REMOVE PARTICIPANT FROM THREAD>
    });
```

Ersätt **USER_ID_FOR_JACK** och **USER_ID_FOR_GEETA** med de användar-ID: n som har hämtats från att skapa användare och token ([åtkomsttoken för användar åtkomst](../../access-tokens.md))

När du uppdaterar din webb läsar flik bör du se följande i-konsolen:
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>Hämta en klient för chatt-tråd

`getChatThreadClient`Metoden returnerar en `chatThreadClient` för en tråd som redan finns. Den kan användas för att utföra åtgärder på den skapade tråden: Lägg till deltagare, skicka meddelande, osv. threadId är det unika ID: t för den befintliga chatt tråden.

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
Lägg till den här koden i stället för `<CREATE CHAT THREAD CLIENT>` kommentaren i **client.js**, uppdatera din webbläsare-flik och kontrol lera konsolen. du bör se:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Skicka ett meddelande till en chatt-tråd

Använd `sendMessage` metoden för att skicka ett meddelande till en tråd som identifieras av threadId.

`sendMessageRequest` används för att beskriva meddelande förfrågan:

- Används `content` för att tillhandahålla chatt-meddelandets innehåll.

`sendMessageOptions` används för att beskriva de valfria parametrarna för åtgärden:

- Används `senderDisplayName` för att ange visnings namnet på avsändaren.
- Används `type` för att ange meddelande typen, till exempel text eller HTML.

`SendChatMessageResult` är svaret som returnerades från att skicka ett meddelande, det innehåller ett ID, som är det unika ID: t för meddelandet.

```JavaScript
let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    senderDisplayName : 'Jack',
    type: 'text'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
```

Lägg till den här koden i stället för `<SEND MESSAGE TO A CHAT THREAD>` kommentaren i **client.js**, uppdatera din webbläsare-flik och kontrol lera konsolen.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ta emot Chat-meddelanden från en chatt-tråd

Med real tids signalering kan du prenumerera på att lyssna efter nya inkommande meddelanden och uppdatera aktuella meddelanden i minnet. Azure Communication Services har stöd för en [lista över händelser som du kan prenumerera på](../../../concepts/chat/concepts.md#real-time-signaling).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
Lägg till den här koden i stället för `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` kommentaren i **client.js**.
Uppdatera din webbläsare-flik visas i konsolen ett meddelande `Notification chatMessageReceived` .

Du kan också hämta Chat-meddelanden genom att avsöka `listMessages` metoden vid angivna intervall.

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
    while (!nextMessage.done) {
        let chatMessage = nextMessage.value;
        console.log(`Message :${chatMessage.content}`);
        // your code here
        nextMessage = await pagedAsyncIterableIterator.next();
    }

```
Lägg till den här koden i stället för `<LIST MESSAGES IN A CHAT THREAD>` kommentaren i **client.js**.
Uppdatera fliken i-konsolen ska du hitta listan över meddelanden som skickas i den här chatt-tråden.


`listMessages` Returnerar den senaste versionen av meddelandet, inklusive eventuella ändringar eller borttagningar som hände i meddelandet med hjälp av `updateMessage` och `deleteMessage` .
För borttagna meddelanden `chatMessage.deletedOn` returnerar ett datetime-värde som anger när meddelandet togs bort. För redigerade meddelanden `chatMessage.editedOn` returnerar en datetime som anger när meddelandet redigerades. Det går att få åtkomst till den ursprungliga tiden för att skapa meddelanden med `chatMessage.createdOn` som kan användas för att ordna meddelandena.

`listMessages` returnerar olika typer av meddelanden som kan identifieras av `chatMessage.type` . Dessa typer är:

- `Text`: Vanligt chatt-meddelande som skickas av en tråd deltagare.

- `ThreadActivity/TopicUpdate`: System meddelande som anger att ämnet har uppdaterats.

- `ThreadActivity/AddParticipant`: System meddelande som anger att en eller flera deltagare har lagts till i chatt-tråden.

- `ThreadActivity/RemoveParticipant`: System meddelande som anger att en deltagare har tagits bort från chatt-tråden.

Mer information finns i [meddelande typer](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Lägg till en användare som deltagare i chatt-tråden

När en chatt-tråd har skapats kan du lägga till och ta bort användare från den. Genom att lägga till användare ger du dem åtkomst till att skicka meddelanden till chatt-tråden och lägga till/ta bort andra deltagare.

Innan du anropar `addParticipants` -metoden kontrollerar du att du har skaffat en ny åtkomsttoken och identitet för användaren. Användaren måste ha denna åtkomsttoken för att kunna initiera sin Chat-klient.

`addParticipantsRequest` Beskriver objektet begär ande där `participants` visar en lista över deltagare som ska läggas till i chatt-tråden.
- `id`, krävs, är den kommunikations identifierare som ska läggas till i chatt-tråden.
- `displayName`, valfritt är visnings namnet för tråd deltagaren.
- `shareHistoryTime`, valfritt, är den tid från vilken chatt-historiken delas med deltagaren. Om du vill dela historiken på grund av att chatten är i gång, anger du den här egenskapen till ett datum som är lika med eller mindre än tiden för tråd skapande. Om du vill dela ingen Historik tidigare till när deltagaren lades in, ställer du in den på det aktuella datumet. Om du vill dela delar av historiken anger du det datum som du önskar.

```JavaScript

let addParticipantsRequest =
{
    participants: [
        {
            id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
Ersätt **NEW_PARTICIPANT_USER_ID** med ett [nytt användar-ID](../../access-tokens.md) Lägg till den här koden i stället för `<ADD NEW PARTICIPANT TO THREAD>` kommentaren i **client.js**

## <a name="list-users-in-a-chat-thread"></a>Lista användare i en chatt-tråd
```JavaScript
async function listParticipants() {
   let pagedAsyncIterableIterator = await chatThreadClient.listParticipants();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listParticipants();
```
Lägg till den här koden i stället för `<LIST PARTICIPANTS IN A THREAD>` kommentaren i **client.js**, uppdatera din webbläsare-flik och kontrol lera konsolen. du bör se information om användare i en tråd.

## <a name="remove-user-from-a-chat-thread"></a>Ta bort användare från en chatt-tråd

På samma sätt som du lägger till en deltagare kan du ta bort deltagare från en chatt-tråd. För att kunna ta bort måste du följa ID: na för de deltagare som du har lagt till.

Använd `removeParticipant` metoden där `participant` är den kommunikations användare som ska tas bort från tråden.

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
Ersätt **PARTICIPANT_ID** med ett användar-ID som användes i föregående steg (<NEW_PARTICIPANT_USER_ID>).
Lägg till den här koden i stället för `<REMOVE PARTICIPANT FROM THREAD>` kommentaren i **client.js**,
