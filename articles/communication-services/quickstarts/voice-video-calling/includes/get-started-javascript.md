---
title: Snabbstart – Lägga till VOIP-anrop till en webbapp med hjälp av Azure Communication Services
description: I den här självstudien lär du dig att använda Azure Communication Services Calling SDK för JavaScript
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: a93fe6c6203140bfed3771da8353ea7843b7694f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327276"
---
I den här snabbstarten lär du dig hur du startar ett anrop med hjälp av Azure Communication Services Anropa SDK för JavaScript.

> [!NOTE]
> Det här dokumentet använder version 1.0.0-beta.10 av anropande SDK.


## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Versionerna Active LTS och Maintenance LTS (8.11.1 och 10.14.1 rekommenderas).
- En aktiv Communication Services resurs. [Skapa en Communication Services resurs](../../create-communication-resource.md).
- En token för användaråtkomst för att instansiera anropsklienten. Lär dig hur [du skapar och hanterar användaråtkomsttoken.](../../access-tokens.md)


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

Här är koden:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="token-input"
      type="text"
      placeholder="User access token"
      style="margin-bottom:1em; width: 200px;"
    />
    </div>
    <button id="token-submit" type="button">
        Submit
    </button>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px; display: block;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Skapa en fil i rotkatalogen för projektet med namnet **client.js** ska innehålla programlogiken för den här snabbstarten. Lägg till följande kod för att importera den anropande klienten och hämta referenser till DOM-elementen så att vi kan koppla vår affärslogik. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
let userTokenCredential = "";
const userToken = document.getElementById("token-input");
const calleeInput = document.getElementById("callee-id-input");
const submitToken = document.getElementById("token-submit");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services Anropa SDK:

| Name                             | Beskrivning                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient är den huvudsakliga startpunkten för anropande SDK.                                                                       |
| CallAgent                        | CallAgent används för att starta och hantera anrop.                                                                                            |
| AzureCommunicationTokenCredential | Klassen AzureCommunicationTokenCredential implementerar communicationTokenCredential-gränssnittet som används för att instansiera CallAgent. |


## <a name="authenticate-the-client"></a>Autentisera klienten

Du måste ange en giltig token för användaråtkomst för din resurs i textfältet och klicka på Skicka. Läs dokumentationen om [användaråtkomsttoken](../../access-tokens.md) om du inte redan har en tillgänglig token. Med `CallClient` hjälp av initierar du `CallAgent` en -instans med `CommunicationTokenCredential` en som gör att vi kan göra och ta emot anrop. Lägg till följande kod i **client.js**:

```javascript
submitToken.addEventListener("click", async () => {
  const callClient = new CallClient(); 
  const userTokenCredential = userToken.value;
    try {
      tokenCredential = new AzureCommunicationTokenCredential(userTokenCredential);
      callAgent = await callClient.createCallAgent(tokenCredential);
      callButton.disabled = false;
      submitToken.disabled = true;
    } catch(error) {
      window.alert("Please submit a valid token!");
    }
})
```

## <a name="start-a-call"></a>Starta ett anrop

Lägg till en händelsehanterare för att initiera ett anrop `callButton` när du klickar på :

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ id: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Avsluta ett anrop

Lägg till en händelselyssnare för att avsluta det aktuella `hangUpButton` anropet när du klickar på :

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
  submitToken.disabled = false;
});
```

Egenskapen `forEveryone` avslutar anropet för alla samtalsdeltagare.

## <a name="run-the-code"></a>Kör koden

Använd för `webpack-dev-server` att skapa och köra din app. Kör följande kommando för att paketa programvärden i på en lokal webbserver:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Öppna webbläsaren och gå till http://localhost:8080/ . Du bör se följande:

:::image type="content" source="../media/javascript/calling-javascript-app-2.png" alt-text="Skärmbild av det färdiga JavaScript-programmet.":::

Du kan göra ett utgående VOIP-anrop genom att ange ett användar-ID i textfältet och klicka på **knappen Starta anrop.** När du anropar ansluts du till en ekorobot. Det här är perfekt för att komma igång `8:echo123` och verifiera att dina ljudenheter fungerar.

## <a name="sample-code"></a>Exempelkod

Du kan ladda ned exempelappen från [Github](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-voice-calling).
