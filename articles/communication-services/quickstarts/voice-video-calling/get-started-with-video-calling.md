---
title: Snabb start – lägga till video som anropar din app (Java Script)
titleSuffix: An Azure Communication Services quickstart
description: I den här snabb starten får du lära dig hur du lägger till video samtals funktioner i din app med Azure Communication Services.
author: xumo-95
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e7f74298b8bf8209a6b1473880b33d64bd17cfd9
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108102"
---
# <a name="quickstart-add-11-video-calling-to-your-app-javascript"></a>Snabb start: lägga till 1:1 video som anropar din app (Java Script)

## <a name="download-code"></a>Hämta kod

Hitta den färdiga koden för den här snabb starten på [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling)

## <a name="prerequisites"></a>Förutsättningar
- Skaffa ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/) Aktiva LTS och underhåll LTS-versioner (8.11.1 och 10.14.1)
- Skapa en Active Communication Services-resurs. [Skapa en kommunikations tjänst resurs](../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- Skapa en token för användar åtkomst för att instansiera anrops klienten. [Lär dig hur du skapar och hanterar användar åtkomst-token](../access-tokens.md?pivots=programming-language-csharp).

## <a name="setting-up"></a>Konfigurera
### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program
Öppna terminalen eller kommando fönstret Skapa en ny katalog för din app och navigera till den.
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>Installera paketet
Använd `npm install` kommandot för att installera Azure Communication Services som anropar SDK för Java Script.

> [!IMPORTANT]
> I den här snabb starten används Azure Communication Services som anropar SDK-versionen `1.0.0.beta-10` . 


```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

I den här snabb starten används WebPack för att paketera program till gångarna. Kör följande kommando för att installera `webpack` , `webpack-cli` och NPM- `webpack-dev-server` paket och lista dem som utvecklings beroenden i `package.json` :

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
Skapa en `index.html` fil i projektets rot Katalog. Vi använder den här filen för att konfigurera en grundläggande layout som gör det möjligt för användaren att placera ett 1:1-video samtal.

Här är koden:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

Skapa en fil i rot katalogen i projektet `client.js` som kallas för att innehålla program logiken för den här snabb starten. Lägg till följande kod för att importera den anropande klienten och hämta referenser till DOM-elementen.

```JavaScript
import { CallClient, CallAgent, VideoStreamRenderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services som anropar SDK:

| Name      | Beskrivning | 
| :---        |    :----   |
| CallClient  | CallClient är den huvudsakliga start punkten för den anropande SDK: n.      |
| CallAgent  | CallAgent används för att starta och hantera samtal.        |
| DeviceManager | DeviceManager används för att hantera medie enheter.    |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential-klassen implementerar CommunicationTokenCredential-gränssnittet som används för att instansiera CallAgent.        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>Autentisera klienten och komma åt DeviceManager

Du måste ersätta <USER_ACCESS_TOKEN> med en giltig åtkomsttoken för din resurs. Se dokumentationen för användar åtkomst-token om du inte redan har en tillgänglig token. Med hjälp av `CallClient` initierar du en `CallAgent` instans med en som gör det `CommunicationUserCredential` möjligt för oss att ringa och ta emot samtal. För att få åtkomst till `DeviceManager` en callAgent-instans måste först skapas. Du kan sedan använda- `getDeviceManager` metoden på `CallClient` instansen för att hämta `DeviceManager` .

Lägg till följande kod i `client.js`:

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}
init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>Placera ett 1:1 utgående video samtal till en användare

Lägg till en händelse lyssnare för att initiera ett anrop när `callButton` användaren klickar på den:

Först måste du räkna upp lokala kameror med deviceManager- `getCameraList` API: et. I den här snabb starten använder vi den första kameran i samlingen. När du har valt den önskade kameran skapas en LocalVideoStream-instans och skickas `videoOptions` som ett objekt inom LocalVideoStream-matrisen till anrops metoden. När samtalet ansluter börjar den automatiskt att skicka en video ström till den andra deltagaren. 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
Om du vill rendera en `LocalVideoStream` måste du skapa en ny instans av `VideoStreamRenderer` och sedan skapa en ny `VideoStreamRendererView` instans med hjälp av den asynkrona `createView` metoden. Du kan sedan koppla `view.target` till ett gränssnitts element. 

```JavaScript
async function localVideoView() {
    rendererLocal = new VideoStreamRenderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
Alla fjärranslutna deltagare är tillgängliga via `remoteParticipants` samlingen på en samtals instans. Du måste lyssna på händelsen för att `remoteParticipantsUpdated` bli meddelad när en ny fjärran sluten deltagare läggs till i anropet. Du måste också iterera `remoteParticipants` samlingen för att prenumerera på var och en av dem för att kunna prenumerera på deras video strömmar. 

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToParticipantVideoStreams(p);
        })
    }); 
    callInstance.remoteParticipants.forEach( p => {
        subscribeToParticipantVideoStreams(p);
    })
}
```
Du måste prenumerera på `videoStreamsUpdated` händelsen för att kunna hantera nya video strömmar av fjärranslutna deltagare. Du kan granska `videoStreams` samlingarna för att visa en lista över strömmar för varje deltagare samtidigt som du går igenom `remoteParticipants` samlingen av det aktuella anropet.

```JavaScript
function subscribeToParticipantVideoStreams(remoteParticipant) {
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
}
```
Du måste prenumerera på en `isAvailableChanged` händelse för att kunna återge `remoteVideoStream` . Om `isAvailable` egenskapen ändras till `true` skickar en fjärran sluten deltagare en ström. När tillgängligheten för en fjärrström ändras kan du välja att ta bort hela `Renderer` , en speciell `RendererView` eller behålla dem, men det leder till att tomma video rutor visas.
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
Om du vill rendera en `RemoteVideoStream` måste du skapa en ny instans av `VideoStreamRenderer` och sedan skapa en ny `VideoStreamRendererView` instans med hjälp av den asynkrona `createView` metoden. Du kan sedan koppla `view.target` till ett gränssnitts element. 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new VideoStreamRenderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```
## <a name="receive-an-incoming-call"></a>Ta emot ett inkommande samtal
För att hantera inkommande samtal måste du lyssna på `incomingCall` händelsen `callAgent` . När det finns ett inkommande samtal måste du räkna upp lokala kameror och skapa en `LocalVideoStream` instans för att skicka en video ström till den andra deltagaren. Du måste också prenumerera på `remoteParticipants` för att hantera fjärranslutna video strömmar. Du kan acceptera eller avvisa anropet via `incomingCall` instansen. 

Sätt implementeringen i `init()` för att hantera inkommande samtal. 

```JavaScript
callAgent.on('incomingCall', async e => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    localVideoView();

    stopVideoButton.disabled = false;
    callButton.disabled = true;
    hangUpButton.disabled = false;

    const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
    call = addedCall;

    subscribeToRemoteParticipantInCall(addedCall);  
});
```
## <a name="end-the-current-call"></a>Avsluta det aktuella anropet
Lägg till en händelse lyssnare för att avsluta det aktuella anropet när `hangUpButton` användaren klickar på den:
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
## <a name="subscribe-to-call-updates"></a>Prenumerera för att anropa uppdateringar
Du måste prenumerera på händelsen när den fjärranslutna deltagaren avslutar anropet för att ta bort video åter givningar och växla knapp status. 

Lägg till implementeringen i init () för att prenumerera på `callsUpdated` händelsen. 
 ```JavaScript 
callAgent.on('callsUpdated', e => {
    e.removed.forEach(removedCall => {
        // dispose of video renders
        rendererLocal.dispose();
        rendererRemote.dispose();
        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        stopVideoButton.disabled = true;
    })
})
```

## <a name="start-and-end-video-during-the-call"></a>Starta och avsluta video under samtalet
Du kan stoppa videon under det aktuella anropet genom att lägga till en händelse lyssnare till knappen Stoppa video för att ta bort åter givningen av `localVideoStream` . 
 ```JavaScript       
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
Du kan lägga till en händelse lyssnare på knappen Starta video för att aktivera videon igen när den stoppades under det aktuella anropet. 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>Kör koden
Använd `webpack-dev-server` för att skapa och köra din app. Kör följande kommando för att paketera program värden i på en lokal webserver:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Öppna webbläsaren och gå till http://localhost:8080/ . Du bör se följande:

:::image type="content" source="./media/javascript/1-on-1-video-calling.png" alt-text="1 på 1 video samtals sida":::

Du kan göra ett 1:1 utgående video samtal genom att ange ett användar-ID i fältet text och klicka på knappen starta samtal. 

## <a name="sample-code"></a>Exempelkod
Du kan hämta exempelappen från [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling).

## <a name="clean-up-resources"></a>Rensa resurser
Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources).

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande artiklar:

- Kolla i vårt [exempel på webb anrop](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Läs mer om hur du [anropar SDK-funktioner](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Läs mer om [hur du anropar Works](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)

