---
title: Snabb start – Anslut till ett team möte
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: c191da32444c3eb0315373780c8037f1b45be423
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96992987"
---
## <a name="prerequisites"></a>Krav

- En fungerande [kommunikations tjänst som anropar en app](../getting-started-with-calling.md).
- En [team distribution](/deployoffice/teams-install).

## <a name="enable-teams-interoperability"></a>Aktivera team samverkan

Funktionen Teams driftskompatibilitet finns för närvarande i privat för hands version. Om du vill aktivera den här funktionen för din kommunikations tjänst resurs, kan du skicka e-post [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) till:

1. Prenumerations-ID för den Azure-prenumeration som innehåller kommunikations tjänst resursen.
2. Ditt teams klient-ID. Det enklaste sättet att skaffa detta är att [Hämta och dela en länk till teamet](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f).

Du måste vara medlem i den ägande organisationen av båda entiteterna för att kunna använda den här funktionen.

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
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential);
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
    
    // set display name in the meeting
    callAgent.updateDisplayName('ACS user');
    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
    })
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
