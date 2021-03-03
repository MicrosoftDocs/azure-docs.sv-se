---
title: Snabb start – ansluta till ett team möte från en webbapp
description: I den här självstudien får du lära dig hur du ansluter till ett team möte med Azure Communication Services som anropar klient bibliotek för Java Script
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d022d90aa4558ef5a4973668d4966a30f477a1da
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660096"
---
I den här snabb starten får du lära dig hur du ansluter till ett team möte med Azure Communication Services som anropar klient bibliotek för Java Script.

## <a name="prerequisites"></a>Förutsättningar

- En fungerande [kommunikations tjänst som anropar en webbapp](../getting-started-with-calling.md).
- En [team distribution](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls"></a>Lägg till Team UI-kontroller

Ersätt Code i index.html med följande kodfragment.
Text rutan används för att ange team Mötes kontexten och knappen kommer att användas för att ansluta till det angivna mötet:

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

## <a name="enable-the-teams-ui-controls"></a>Aktivera UI-kontroller för team

Ersätt innehållet i client.js-filen med följande kodfragment.

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

## <a name="get-the-teams-meeting-link"></a>Hämta team Mötes länken

Länken Teams möte kan hämtas med Graph API: er. Detta beskrivs i [Graph-dokumentationen](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).
Kommunikations tjänsterna som anropar SDK accepterar en länk till en fullständig Teams möte. Den här länken returneras som en del av `onlineMeeting` resursen, som är tillgänglig under [ `joinWebUrl` egenskapen](/graph/api/resources/onlinemeeting?view=graph-rest-beta) . du kan också hämta nödvändig Mötes information från URL: en **till kopplings mötet** i team mötets inbjudan.

## <a name="run-the-code"></a>Kör koden

WebPack-användare kan använda `webpack-dev-server` för att skapa och köra din app. Kör följande kommando för att paketera din program värd på en lokal webserver:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Öppna webbläsaren och gå till http://localhost:8080/ . Du bör se följande:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Skärm bild av det färdiga JavaScript-programmet.":::

Infoga team-kontexten i text rutan och tryck på *Anslut Teams möte* för att ansluta till teamen i kommunikations tjänst programmet.
