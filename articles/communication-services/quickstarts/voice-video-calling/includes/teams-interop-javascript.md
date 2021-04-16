---
title: Snabbstart – Delta i ett Teams-möte från en webbapp
description: I den här självstudien lär du dig hur du ansluter till ett Teams-möte med hjälp Azure Communication Services Anropa SDK för JavaScript
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6747d1d3cfba1c9e2bee7a8a7a48d67d6bed9f8e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564740"
---
I den här snabbstarten lär du dig hur du ansluter till ett Teams-möte med hjälp Azure Communication Services Anropa SDK för JavaScript.

## <a name="prerequisites"></a>Förutsättningar

- En fungerande [Communication Services anropar webbappen](../getting-started-with-calling.md).
- En [Teams-distribution](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls"></a>Lägga till ui-kontroller i Teams

Ersätt koden i index.html med följande kodfragment.
Textrutan används för att ange Kontext för Teams-möte och knappen används för att ansluta till det angivna mötet:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
        <p><span style="font-weight: bold" id="recording-state"></span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Aktivera gränssnittskontrollerna i Teams

Ersätt innehållet i client.js med följande kodfragment.

```javascript
import { CallClient } from "@azure/communication-calling";
import { Features } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');
const recordingStateElement = document.getElementById('recording-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })

    call.api(Features.Recording).on('isRecordingActiveChanged', () => {
        if (call.api(Features.Recording).isRecordingActive) {
            recordingStateElement.innerText = "This call is being recorded";
        }
        else {
            recordingStateElement.innerText = "";
        }
    });
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Hämta länken för Teams-mötet

Teams-möteslänken kan hämtas med Graph API:er. Detta beskrivs i [Graph-dokumentationen.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)
Den Communication Services anropande SDK accepterar en fullständig Teams-möteslänk. Den här länken returneras som en del av resursen och är tillgänglig under egenskapen . Du kan också hämta nödvändig mötesinformation från URL:en för kopplingsmöte i `onlineMeeting` själva [ `joinWebUrl` ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) Teams-mötesbjudan. 

## <a name="run-the-code"></a>Kör koden

Webpack-användare kan använda `webpack-dev-server` för att skapa och köra din app. Kör följande kommando för att paketa programvärden på en lokal webbserver:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Öppna webbläsaren och gå till http://localhost:8080/ . Du bör se följande:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Skärmbild av det färdiga JavaScript-programmet.":::

Infoga Teams-kontexten i textrutan och tryck på *Anslut till Teams-möte* för att ansluta till Teams-mötet från ditt Communication Services program.
