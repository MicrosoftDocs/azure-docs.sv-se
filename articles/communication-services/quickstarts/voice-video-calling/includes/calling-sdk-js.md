---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: f20099943d3cfa3dd4afc161c26e5582e467ca8d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589906"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- En distribuerad Communication Services resurs. [Skapa en Communication Services resurs](../../create-communication-resource.md).
- En användaråtkomsttoken för att aktivera den anropande klienten. Mer information finns i [Skapa och hantera åtkomsttoken.](../../access-tokens.md)
- Valfritt: Slutför snabbstarten för [att lägga till röstsamtal till ditt program](../getting-started-with-calling.md).

## <a name="install-the-sdk"></a>Installera SDK:n

> [!NOTE]
> Det här dokumentet använder ACS-anropande webb-SDK.

Använd kommandot `npm install` för att installera Azure Communication Services anropar och vanliga SDK:er för JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services Anropa SDK:

| Name                             | Beskrivning                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Den huvudsakliga startpunkten för anropande SDK.                                                                       |
| `CallAgent`                        | Används för att starta och hantera anrop.                                                                                            |
| `DeviceManager`                    | Används för att hantera medieenheter.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementerar `CommunicationTokenCredential` -gränssnittet, som används för att instansiera `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Initiera en CallClient-instans, skapa en CallAgent-instans och få åtkomst till deviceManager

Skapa en ny `CallClient` instans. Du kan konfigurera den med anpassade alternativ som en Logger-instans.

När du har en `CallClient` instans kan du skapa en instans genom att anropa metoden på `CallAgent` `createCallAgent` `CallClient` instansen. Detta returnerar asynkront ett `CallAgent` instansobjekt.

Metoden `createCallAgent` använder som ett `CommunicationTokenCredential` argument. Den accepterar en token [för användaråtkomst.](../../access-tokens.md)

Du kan använda metoden `getDeviceManager` på instansen `CallClient` för att komma åt `deviceManager` .

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Ringa ett anrop

Om du vill skapa och starta ett anrop använder du något av API:erna på och anger en användare som du har skapat `callAgent` via Communication Services identity SDK.

Anropet som skapas och startas är synkrona. Med anropsinstansen kan du prenumerera på anropshändelser.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Gör ett 1:n-anrop till en användare eller PSTN

Om du vill Communication Services en annan användare använder du metoden på och skickar mottagarens som du skapade med `startCall` `callAgent` `CommunicationUserIdentifier` [Communication Services-administrationsbiblioteket](../../access-tokens.md).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Om du vill ringa ett offentligt telefonnätverk (PSTN) använder `startCall` du metoden på och skickar `callAgent` mottagarens `PhoneNumberIdentifier` . Din Communication Services måste konfigureras för att tillåta PSTN-anrop.

När du anropar ett PSTN-nummer anger du ditt alternativa nummerpresentation. Ett alternativt nummerpresentation är ett telefonnummer (baserat på standarden E.164) som identifierar anroparen i ett PSTN-samtal. Det är det telefonnummer som mottagaren ser för ett inkommande samtal.

> [!NOTE]
> PSTN-anrop är för närvarande i privat förhandsversion. För åtkomst gäller [för det tidiga adopter-programmet](https://aka.ms/ACS-EarlyAdopter).

Använd följande kod för ett 1:1-anrop:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Använd följande kod för ett 1:n-anrop:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Ringa ett 1:1-samtal med videokamera

> [!IMPORTANT]
> Det kan för närvarande inte finnas mer än en utgående lokal videoström.

För att kunna ringa ett videosamtal måste du räkna upp lokala kameror med hjälp av `getCameras()` metoden i `deviceManager` .

När du har valt en kamera använder du den för att skapa en `LocalVideoStream` instans. Skicka in den `videoOptions` som ett objekt i `localVideoStream` matrisen till metoden `startCall` .


```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

När ditt samtal ansluter börjar det automatiskt skicka en videoström från den valda kameran till den andra deltagaren. Detta gäller även för `Call.Accept()` videoalternativen och `CallAgent.join()` videoalternativen.

### <a name="join-a-group-call"></a>Ansluta till ett gruppsamtal

> [!NOTE]
> Parametern `groupId` betraktas som systemmetadata och kan användas av Microsoft för åtgärder som krävs för att köra systemet. Inkludera inte personliga data i `groupId` värdet. Microsoft behandlar inte den här parametern som personuppgifter och dess innehåll kan vara synligt för Microsoft-anställda eller lagrat långsiktigt.
>
> Parametern `groupId` kräver att data är i GUID-format. Vi rekommenderar att du använder slumpmässigt genererade GUID:er som inte betraktas som personliga data i dina system.
>

Om du vill starta ett nytt gruppsamtal eller ansluta till ett pågående gruppsamtal använder du `join` metoden och skickar ett -objekt med en `groupId` -egenskap. Värdet `groupId` måste vara ett GUID.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Delta i ett Teams-möte
> [!NOTE]
> Detta API tillhandahålls som en förhandsversion för utvecklare och kan komma att ändras utifrån den feedback vi får. Använd inte detta API i en produktionsmiljö. Om du vill använda det här API:et använder du betaversionen av ACS Calling Web SDK

Om du vill delta i ett Teams-möte använder du metoden och skickar `join` en möteslänk eller ett mötes koordinater.

Anslut med hjälp av en möteslänk:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Anslut med hjälp av möteskoordinater:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Ta emot ett inkommande anrop

Instansen `callAgent` skickar en händelse när den `incomingCall` inloggade identiteten tar emot ett inkommande anrop. Om du vill lyssna på den här händelsen prenumererar du med något av följande alternativ:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 
    // Get incoming call ID
    var incomingCallId = incomingCall.id
    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

Händelsen `incomingCall` innehåller en instans som du kan godkänna eller `incomingCall` avvisa.

## <a name="manage-calls"></a>Hantera anrop

Under ett anrop kan du komma åt samtalsegenskaper och hantera video- och ljudinställningar.

### <a name="check-call-properties"></a>Kontrollera anropsegenskaper

Hämta det unika ID:t (strängen) för ett anrop:

   ```js
    const callId: string = call.id;
   ```
Hämta information om anropet:
> [!NOTE]
> Detta API tillhandahålls som en förhandsversion för utvecklare och kan komma att ändras utifrån den feedback vi får. Använd inte detta API i en produktionsmiljö. Om du vill använda det här API:et använder du betaversionen av ACS Calling Web SDK
   ```js
   const callInfo = call.info;
   ```

Lär dig mer om andra deltagare i anropet genom att `remoteParticipants` granska samlingen på anropsinstansen:

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Identifiera anroparen för ett inkommande anrop:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` är en av `CommunicationIdentifier` typerna.

Hämta tillståndet för ett anrop:

   ```js
   const callState = call.state;
   ```

   Detta returnerar en sträng som representerar det aktuella tillståndet för ett anrop:

  - `None`: Inledande anropstillstånd.
  - `Connecting`: Inledande övergångstillstånd när ett anrop placeras eller godkänns.
  - `Ringing`: För ett utgående samtal anger att ett anrop ringer för fjärranslutna deltagare. Det är `Incoming` på deras sida.
  - `EarlyMedia`: Anger ett tillstånd där ett meddelande spelas upp innan anropet ansluts.
  - `Connected`: Anger att anropet är anslutet.
  - `LocalHold`: Anger att anropet har stoppats av en lokal deltagare. Inga media flödar mellan den lokala slutpunkten och fjärranslutna deltagare.
  - `RemoteHold`: Anger att anropet har satts i is av fjärrdeltagaren. Inga media flödar mellan den lokala slutpunkten och fjärranslutna deltagare.
  - `InLobby`: Anger att användaren är i etta.
  - `Disconnecting`: Övergångstillstånd innan anropet förs till ett `Disconnected` tillstånd.
  - `Disconnected`: Slutligt anropstillstånd. Om nätverksanslutningen går förlorad ändras tillståndet till `Disconnected` efter två minuter.

Ta reda på varför ett anrop avslutades genom att granska `callEndReason` egenskapen:

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

Lär dig om det aktuella anropet är inkommande eller utgående genom att granska `direction` egenskapen . Den returnerar `CallDirection` .

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Kontrollera om den aktuella mikrofonen är avstängd. Den returnerar `Boolean` .

   ```js
   const muted = call.isMuted;
   ```

Ta reda på om skärmdelningsströmmen skickas från en viss slutpunkt genom att kontrollera `isScreenSharingOn` egenskapen . Den returnerar `Boolean` .

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Granska aktiva videoströmmar genom att kontrollera `localVideoStreams` samlingen. Den returnerar `LocalVideoStream` objekt.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```




### <a name="mute-and-unmute"></a>Stänga av och slå på ljudet

Om du vill stänga av eller slå på ljudet på den lokala slutpunkten kan du använda och `mute` `unmute` asynkrona API:er:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Starta och sluta skicka lokal video

För att starta en video måste du räkna upp kameror med `getCameras` hjälp av metoden på `deviceManager` objektet. Skapa sedan en ny instans `LocalVideoStream` av med önskad kamera och skicka sedan objektet till metoden `LocalVideoStream` `startVideo` :

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

När du har börjat skicka video läggs en `LocalVideoStream` instans till i samlingen på en `localVideoStreams` anropsinstans.

```js
call.localVideoStreams[0] === localVideoStream;
```

Om du vill stoppa lokal video skickar `localVideoStream` du den instans som är tillgänglig i `localVideoStreams` samlingen:

```js
await call.stopVideo(localVideoStream);
```

Du kan växla till en annan kameraenhet medan en video skickar genom att använda `switchSource` en `localVideoStream` instans:

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>Hantera fjärrdeltagare

Alla fjärrdeltagare representeras av typen `RemoteParticipant` och är tillgängliga via en samling på en `remoteParticipants` anropsinstans.

### <a name="list-the-participants-in-a-call"></a>Visa en lista över deltagarna i ett samtal

Samlingen `remoteParticipants` returnerar en lista över fjärranslutna deltagare i ett anrop:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Få åtkomst till egenskaper för fjärrdeltagare

Fjärranslutna deltagare har en uppsättning associerade egenskaper och samlingar:

- `CommunicationIdentifier`: Hämta identifieraren för en fjärrdeltagare. Identiteten är en av `CommunicationIdentifier` typerna:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Det kan vara någon av följande `CommunicationIdentifier` typer:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Objekt som representerar ACS-användaren.
  - `{ phoneNumber: '<E.164>' }`: Objekt som representerar telefonnumret i E.164-format.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Objekt som representerar Teams-användaren.
  - `{ id: string }`: identifierare för att identifiera ett objekt som inte passar någon av de andra identifierartyperna

- `state`: Hämta tillståndet för en fjärransluten deltagare.

  ```js
  const state = remoteParticipant.state;
  ```

  Tillståndet kan vara:

  - `Idle`: Ursprungligt tillstånd.
  - `Connecting`: Övergångstillstånd när en deltagare ansluter till anropet.
  - `Ringing`: Deltagaren ringer.
  - `Connected`: Deltagaren är ansluten till anropet.
  - `Hold`: Deltagaren är i rummet.
  - `EarlyMedia`: Meddelande som spelas upp innan en deltagare ansluter till anropet.
  - `InLobby`: Anger att fjärrdeltagaren är på plats.
  - `Disconnected`: Sluttillstånd. Deltagaren kopplas bort från anropet. Om fjärrdeltagaren förlorar sin nätverksanslutning ändras tillståndet till `Disconnected` efter två minuter.

- `callEndReason`: Om du vill ta reda på varför en deltagare lämnade anropet kontrollerar du `callEndReason` egenskapen:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` status: Om du vill ta reda på om en fjärrdeltagare är avstängd kontrollerar du `isMuted` egenskapen . Den returnerar `Boolean` .

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` status: Om du vill ta reda på om en fjärransluten deltagare talar kontrollerar du `isSpeaking` egenskapen . Den returnerar `Boolean` .

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Om du vill kontrollera alla videoströmmar som en viss deltagare skickar i det här anropet kontrollerar du `videoStreams` samlingen. Den innehåller `RemoteVideoStream` objekt.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: Om du vill hämta visningsnamnet för den här fjärrdeltagaren kontrollerar du `displayName` egenskapen som returnerar strängen. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Lägga till en deltagare i ett anrop

Om du vill lägga till en deltagare (antingen en användare eller ett telefonnummer) i ett samtal kan du använda `addParticipant` . Ange en av `Identifier` typerna. Den returnerar instansen `remoteParticipant` synkront. Händelsen `remoteParticipantsUpdated` från Anrop utlöses när en deltagare läggs till i anropet.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Ta bort en deltagare från ett anrop

Om du vill ta bort en deltagare (antingen en användare eller ett telefonnummer) från ett samtal kan du anropa `removeParticipant` . Du måste skicka en av `Identifier` typerna. Detta löses asynkront när deltagaren tas bort från anropet. Deltagaren tas också bort från `remoteParticipants` samlingen.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Rendera fjärrdeltagares videoströmmar

Om du vill visa en lista över videoströmmar och skärmdelningsströmmar för fjärranslutna deltagare kan du granska `videoStreams` samlingarna:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

För att `RemoteVideoStream` rendera måste du prenumerera på `isAvailableChanged` händelsen. Om egenskapen `isAvailable` ändras till skickar en `true` fjärrdeltagare en dataström. Därefter skapar du en ny instans av `VideoStreamRenderer` och skapar sedan en ny instans med hjälp av den `VideoStreamRendererView` asynkrona `createView` metoden.  Du kan sedan koppla till `view.target` val annat användargränssnittselement.

När tillgängligheten för en fjärrström ändras kan du välja att förstöra hela , en specifik eller behålla dem, men detta leder till att `VideoStreamRenderer` `VideoStreamRendererView` en tom videobild visas.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Egenskaper för fjärrvideoström

Fjärrvideoströmmar har följande egenskaper:

- `id`: ID för en fjärrvideoström.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: Kan vara `Video` eller `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Om en fjärrdeltagares slutpunkt aktivt skickar en dataström.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer-metoder och egenskaper
Skapa en instans som kan kopplas i programmets användargränssnitt för att rendera den fjärranslutna `VideoStreamRendererView` videoströmmen, använda asynkron metod, matchas när dataströmmen är redo att återges och returnerar ett objekt med en egenskap som representerar element som kan läggas till var som helst i `createView()` `target` `video` DOM-trädet

  ```js
  videoStreamRenderer.createView()
  ```

Ta bort `videoStreamRenderer` och alla `VideoStreamRendererView` associerade instanser:

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererVisa metoder och egenskaper

När du skapar `VideoStreamRendererView` en kan du ange egenskaperna och `scalingMode` `isMirrored` . `scalingMode` kan vara `Stretch` `Crop` , eller `Fit` . Om `isMirrored` anges, vändas den renderade dataströmmen lodrätt.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Varje `VideoStreamRendererView` instans har en egenskap som representerar `target` renderingsytan. Koppla den här egenskapen i programmets användargränssnitt:

```js
htmlElement.appendChild(view.target);
```

Du kan uppdatera `scalingMode` genom att använda metoden `updateScalingMode` :

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Enhetshantering

I `deviceManager` kan du räkna upp lokala enheter som kan överföra dina ljud- och videoströmmar i ett anrop. Du kan också använda den för att begära behörighet att komma åt den lokala enhetens mikrofoner och kameror.

Du kan komma `deviceManager` åt genom att anropa metoden `callClient.getDeviceManager()` :

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Hämta lokala enheter

Om du vill komma åt lokala enheter kan du använda uppräkningsmetoder på `deviceManager` . Uppräkning är en asynkron åtgärd

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Ange standard mikrofon och talare

I `deviceManager` kan du ange en standardenhet som du ska använda för att starta ett anrop. Om klientens standardvärden inte anges Communication Services standardinställningar för operativsystemet.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Förhandsversion av lokal kamera

Du kan använda `deviceManager` och för att börja återge strömmar från din lokala `VideoStreamRenderer` kamera. Den här strömmen skickas inte till andra deltagare. Det är en lokal förhandsgranskningsfeed.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Begära behörighet till kamera och mikrofon

Be en användare att bevilja kamera- och mikrofonbehörigheter:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Detta matchas med ett -objekt som anger om `audio` och `video` behörigheter har beviljats:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Registrera anrop
> [!NOTE]
> Detta API tillhandahålls som en förhandsversion för utvecklare och kan komma att ändras utifrån den feedback vi får. Använd inte detta API i en produktionsmiljö. Om du vill använda det här API:et använder du betaversionen av ACS Calling Web SDK

Samtalsinspelning är en utökad funktion i `Call` kärn-API:et. Du måste först hämta inspelningsfunktionens API-objekt:

```js
const callRecordingApi = call.api(Features.Recording);
```

Kontrollera sedan om anropet registreras genom att kontrollera `isRecordingActive` egenskapen för `callRecordingApi` . Den returnerar `Boolean` .

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

Du kan också prenumerera på att registrera ändringar:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="transfer-calls"></a>Överföringssamtal
> [!NOTE]
> Detta API tillhandahålls som en förhandsversion för utvecklare och kan komma att ändras utifrån den feedback vi får. Använd inte detta API i en produktionsmiljö. Om du vill använda det här API:et använder du betaversionen av ACS Calling Web SDK

Anropsöverföring är en utökad funktion i `Call` kärn-API:et. Du måste först hämta API-objektet för överföringsfunktionen:

```js
const callTransferApi = call.api(Features.Transfer);
```

Samtalsöverföringar involverar tre parter:

- *Överförare:* Den person som initierar överföringsbegäran.
- *Överföringsmottagare:* Den person som överförs.
- *Överföringsmål:* Den person som överförs till.

Överföringarna följer dessa steg:

1. Det finns redan ett anslutet anrop mellan *överföraren* och *mottagaren*. Överföraren *bestämmer* sig för att överföra anropet från *mottagaren* till *överföringsmålet*.
1. Överföraren *anropar* `transfer` API:et.
1. Mottagaren *bestämmer om* överföringsbegäran till `accept` `reject` överföringsmålet ska *eller med* hjälp av en `transferRequested` händelse.
1. *Överföringsmålet* tar bara emot ett inkommande anrop *om överföringsmottagaren* accepterar överföringsbegäran.

Om du vill överföra ett aktuellt anrop kan du använda `transfer` API:et. `transfer` tar den `transferCallOptions` valfria , vilket gör att du kan ange en `disableForwardingAndUnanswered` flagga:

- `disableForwardingAndUnanswered = false`: Om *överföringsmålet* inte svarar på överföringsanropet följer överföringen överföringsmålsöverföringen och obesvarade inställningar. 
- `disableForwardingAndUnanswered = true`: Om *överföringsmålet* inte svarar på överföringssamtalet avslutas överföringsförsöket.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Med `transfer` API:et kan du prenumerera på `transferStateChanged` händelser och `transferRequested` . En `transferRequested` händelse kommer från en `call` instans, en händelse och överföring och `transferStateChanged` kommer från en `state` `error` `transfer` instans.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

*Överföringsmottagaren* kan godkänna eller avvisa överföringsbegäran som initierats *av överföraren* i `transferRequested` händelsen med hjälp av eller i `accept()` `reject()` `transferRequestedEventArgs` . Du kan komma `targetParticipant` åt information och metoder i `accept` `reject` `transferRequestedEventArgs` .

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="learn-about-eventing-models"></a>Lär dig mer om händelsemodeller

Granska aktuella värden och prenumerera på uppdateringshändelser för framtida värden.

### <a name="properties"></a>Egenskaper

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Samlingar

```js
// Inspect the current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});

// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
