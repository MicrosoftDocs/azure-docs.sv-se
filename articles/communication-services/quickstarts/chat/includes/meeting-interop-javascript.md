---
title: Snabb start – Anslut till ett team möte
author: askaur
ms.author: askaur
ms.date: 02/17/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 93cbc693e340174cf92aa2515d5e09f1ff3c1b31
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656133"
---
## <a name="joining-the-meeting-chat"></a>Ansluta till mötes chatten 

När team samverkan är aktiverat kan en kommunikations tjänst användare ansluta till Team anropet som en extern användare med hjälp av det anropande klient biblioteket. När du ansluter till samtalet läggs de till som deltagare till mötes chatten, där de kan skicka och ta emot meddelanden med andra användare på anropet. Användaren har inte åtkomst till chatt meddelanden som har skickats innan de anslöt till anropet. Om du vill ansluta till mötet och börja chatta kan du följa anvisningarna nedan.

## <a name="install-the-chat-packages"></a>Installera chatt-paketen

Använd `npm install` kommandot för att installera de nödvändiga klient biblioteken för kommunikations tjänster för Java Script.

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

I det här `--save` alternativet visas biblioteket som ett beroende i **package.jsi** filen.

## <a name="add-the-teams-ui-controls"></a>Lägg till Team UI-kontroller

Ersätt koden i index.html med följande kodfragment.
Text rutorna överst på sidan används för att ange team Mötes kontext och mötes-ID. Knappen Anslut till Teams möte kommer att användas för att ansluta till det angivna mötet.
Popup-fönstret chat visas längst ned på sidan. Den kan användas för att skicka meddelanden i Mötes tråden och den visas i real tid av meddelanden som skickas i tråden när ACS-användaren är medlem.

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

## <a name="enable-the-teams-ui-controls"></a>Aktivera UI-kontroller för team

Ersätt innehållet i client.js-filen med följande kodfragment.

I kodfragmentet ersätter du 
- `SECRET CONNECTION STRING` med anslutnings strängen för kommunikations tjänsten 
- `ENDPOINT URL` med din kommunikations tjänsts slut punkts-URL

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-administration";
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

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Skaffa en team Meeting Chat-tråd för en kommunikations tjänst användare

Team Mötes länken och chatten kan hämtas med Graph API: er, som beskrivs i [Graph-dokumentationen](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta). Kommunikations tjänsterna som anropar SDK accepterar en länk till en fullständig Teams möte. Den här länken returneras som en del av `onlineMeeting` resursen, som är tillgänglig under [ `joinWebUrl` egenskapen](/graph/api/resources/onlinemeeting?view=graph-rest-beta) med [Graph API: er](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta), men du kan också hämta `threadId` . Svaret kommer att ha ett- `chatInfo` objekt som innehåller `threadID` . 

Du kan också hämta nödvändig Mötes information och tråd-ID från URL: en **till kopplings mötet** i de team som bjuder in sig själva.
Länken Teams möte ser ut så här: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . `threadId`Kommer att finnas `meeting_chat_thread_id` i länken. Kontrol lera att `meeting_chat_thread_id` är avbrotts ur bruk. Den bör ha följande format: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>Kör koden

WebPack-användare kan använda `webpack-dev-server` för att skapa och köra din app. Kör följande kommando för att paketera din program värd på en lokal webserver:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Öppna webbläsaren och gå till http://localhost:8080/ . Du bör se följande:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Skärm bild av det färdiga JavaScript-programmet.":::

Infoga Mötes länken för team och tråd-ID i text rutorna. Tryck på *Anslut till Team* om du vill delta i teamen och chatta i kommunikations tjänst programmet. Gå till slutet av sidan för att börja chatta.

**Obs!** endast för att skicka, ta emot och redigera meddelanden stöds för samverkan scenarier med team. Andra funktioner som att skriva indikatorer och kommunikations tjänster användare som lägger till eller tar bort andra användare från Team-mötet stöds inte ännu.  
