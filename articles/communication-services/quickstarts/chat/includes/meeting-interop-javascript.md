---
title: Snabbstart – Delta i ett Teams-möte
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 49f9bac40ae803f980a22c19fd5d44d85fa99e9e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564602"
---
## <a name="joining-the-meeting-chat"></a>Delta i möteschatten 

När Teams-samverkan har aktiverats kan en Communication Services ansluta till Teams-anropet som en extern användare med hjälp av anropande SDK. Om du ansluter till anropet lägger du även till dem som deltagare i möteschatten, där de kan skicka och ta emot meddelanden med andra användare under samtalet. Användaren har inte åtkomst till chattmeddelanden som skickades innan de anslöt till anropet. Om du vill delta i mötet och börja chatta kan du följa nästa steg.

## <a name="install-the-chat-packages"></a>Installera chattpaketen

Använd kommandot `npm install` för att installera nödvändiga Communication Services-SDK:er för JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

Alternativet `--save` visar biblioteket som ett beroende i dittpackage.js **på** filen.

## <a name="add-the-teams-ui-controls"></a>Lägga till ui-kontroller i Teams

Ersätt koden i index.html med följande kodavsnitt.
Textrutorna överst på sidan används för att ange Teams-möteskontext och tråd-ID för möte. Knappen Delta i Teams-mötet används för att ansluta till det angivna mötet.
Ett popup-fönster för chatt visas längst ned på sidan. Den kan användas för att skicka meddelanden i mötestråden och visas i realtid alla meddelanden som skickas i tråden medan ACS-användaren är medlem.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Aktivera gränssnittskontrollerna i Teams

Ersätt innehållet i client.js med följande kodavsnitt.

I kodfragmentet ersätter du 
- `SECRET CONNECTION STRING` med kommunikationstjänstens anslutningssträng 
- `ENDPOINT URL` med kommunikationstjänstens slutpunkts-URL

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
  const connectionString = "<SECRET CONNECTION STRING>";
  const endpointUrl = "<ENDPOINT URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(
    `\nCreated an identity with ID: ${identityResponse.communicationUserId}`
  );

  let tokenResponse = await identityClient.issueToken(identityResponse, [
    "voip",
    "chat",
  ]);
  const { token, expiresOn } = tokenResponse;
  console.log(
    `\nIssued an access token that expires at ${expiresOn}:`
  );
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationUserCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationUserCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
  call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = messages;
  
  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    
    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.content);
    }
    else {
       renderSentMessage(e.content);
    }
  });
  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
   messages += '<div class="container lighter">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
   messages += '<div class="container darker">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
      let message = messagebox.value;

      let sendMessageRequest = { content: message };
      let sendMessageOptions = { senderDisplayName : 'Jack' };
      let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
      let messageId = sendChatMessageResult.id;

      messagebox.value = '';
      console.log(`Message sent!, message id:${messageId}`);
  });
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Hämta en teams-möteschatttråd för en Communication Services användare

Teams-möteslänken och chatten kan hämtas med graph-API:er, som beskrivs i [Graph-dokumentationen.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) Den Communication Services anropande SDK accepterar en fullständig Teams-möteslänk. Den här länken returneras som en del av resursen som är tillgänglig under egenskapen `onlineMeeting` Med [ `joinWebUrl` ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) [Graph-API:erna](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)kan du också hämta `threadId` . Svaret har ett `chatInfo` -objekt som innehåller `threadID` . 

Du kan också hämta nödvändig mötesinformation och tråd-ID från URL:en **för kopplingsmöte** i själva Teams-mötesbjudan.
En Teams-möteslänk ser ut så här: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . `threadId`kommer att vara där finns i `meeting_chat_thread_id` länken. Se till att `meeting_chat_thread_id` är obildad före användning. Det bör ha följande format: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>Kör koden

Webpack-användare kan använda `webpack-dev-server` för att skapa och köra din app. Kör följande kommando för att paketa programvärden på en lokal webbserver:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Öppna webbläsaren och gå till http://localhost:8080/ . Du bör se följande:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Skärmbild av det färdiga JavaScript-programmet.":::

Infoga Teams-möteslänken och tråd-ID:t i textrutorna. Tryck *på Join Teams Meeting (Anslut till Teams-möte)* för att delta i Teams-mötet. När ACS-användaren har blivit intagen på mötet kan du chatta inifrån ditt Communication Services program. Gå till rutan längst ned på sidan för att börja chatta.

> [!NOTE] 
> För närvarande stöds endast sändning, mottagning och redigering av meddelanden för samverkansscenarier med Teams. Andra funktioner som att skriva indikatorer Communication Services användare som lägger till eller tar bort andra användare från Teams-mötet stöds inte ännu.  
