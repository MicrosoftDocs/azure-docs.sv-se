---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: af5ec07a8fb2db0bd4b9b8f1af556ef54199400d
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609419"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En distribuerad kommunikations tjänst resurs. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- En token för användar åtkomst för att aktivera den anropande klienten. Mer information finns i [skapa och hantera åtkomsttoken](../../access-tokens.md).
- Valfritt: Slutför snabb starten för att [lägga till röst samtal till ditt program](../getting-started-with-calling.md).

## <a name="install-the-sdk"></a>Installera SDK:n

> [!NOTE]
> Det här dokumentet använder version 1.0.0 – beta. 6 av anrops-SDK: n.

Använd `npm install` kommandot för att installera Azure Communication Services-anrop och vanliga SDK: er för Java Script.
Det här dokumentet refererar till typer i version 1.0.0-beta. 5 av anrops bibliotek.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services som anropar SDK:

| Name                             | Beskrivning                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Huvud start punkten för det anropande SDK: t.                                                                       |
| `CallAgent`                        | Används för att starta och hantera samtal.                                                                                            |
| `DeviceManager`                    | Används för att hantera medie enheter.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementerar `CommunicationTokenCredential` gränssnittet, som används för att instansiera `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Initiera en CallClient-instans, skapa en CallAgent-instans och få åtkomst till deviceManager

Skapa en ny `CallClient` instans. Du kan konfigurera den med anpassade alternativ som en loggad instans.

När du har en `CallClient` instans kan du skapa en `CallAgent` instans genom att anropa `createCallAgent` metoden på `CallClient` instansen. Ett instans objekt returneras asynkront `CallAgent` .

`createCallAgent`Metoden använder `CommunicationTokenCredential` som ett argument. Den accepterar en [token för användar åtkomst](../../access-tokens.md).

När du har skapat en `callAgent` instans kan du använda- `getDeviceManager` metoden på `CallClient` instansen för att komma åt `deviceManager` .

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Placera ett samtal

Om du vill skapa och starta ett anrop använder du ett av API: erna på `callAgent` och anger en användare som du har skapat via kommunikations tjänstens identitets-SDK.

Skapande av anrop och start är synkront. Med anrops instansen kan du prenumerera på samtals händelser.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Placera ett 1: n-Anrop till en användare eller PSTN

Om du vill anropa en annan kommunikations tjänst användare använder du `startCall` metoden på `callAgent` och skickar mottagarens `CommunicationUserIdentifier` som du [skapade med administrations biblioteket för kommunikations tjänster](../../access-tokens.md).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Om du vill placera ett samtal till ett delat telefonnät verk (PSTN) använder du `startCall` metoden på `callAgent` och skickar mottagarens `PhoneNumberIdentifier` . Resursen för kommunikations tjänster måste konfigureras för att tillåta PSTN-anrop.

När du anropar ett PSTN-nummer anger du ditt alternativa ID för anroparen. Ett alternativt nummer presentations-ID är ett telefonnummer (baserat på E. 164-standarden) som identifierar anroparen i ett PSTN-anrop. Det är telefonnumret som samtals mottagaren ser för ett inkommande samtal.

> [!NOTE]
> PSTN-anrop är för närvarande i privat för hands version. För åtkomst, [gäller det tidiga tillämpnings programmet](https://aka.ms/ACS-EarlyAdopter).

Använd följande kod för ett 1:1-anrop:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Använd följande kod för ett 1: n-Anrop:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Placera ett 1:1-samtal med video kameran

> [!IMPORTANT]
> Det kan för närvarande inte finnas fler än en utgående lokal video ström.

Om du vill placera ett video samtal måste du ange dina kameror genom att använda- `getCameras()` metoden i `deviceManager` .

När du har valt en kamera använder du den för att konstruera en `LocalVideoStream` instans. Skicka det i `videoOptions` som ett objekt i `localVideoStream` matrisen till- `startCall` metoden.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

När ditt samtal ansluter börjar den automatiskt att skicka en video ström från den valda kameran till den andra deltagaren. Detta gäller även `Call.Accept()` video alternativ och `CallAgent.join()` video alternativ.

### <a name="join-a-group-call"></a>Anslut till ett grupp anrop

> [!NOTE]
> `groupId`Parametern betraktas som systemmetadata och kan användas av Microsoft för åtgärder som krävs för att köra systemet. Ta inte med personliga data i `groupId` värdet. Microsoft behandlar inte den här parametern som personliga data och dess innehåll kan vara synligt för Microsoft-anställda eller lagras på lång sikt.
>
> `groupId`Parametern kräver att data är i GUID-format. Vi rekommenderar att du använder slumpmässigt genererade GUID som inte betraktas som personliga data i dina system.
>

Om du vill starta ett nytt grupp anrop eller ansluta ett pågående grupp anrop använder du `join` metoden och skickar ett objekt med en `groupId` egenskap. `groupId`Värdet måste vara ett GUID.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Delta i ett team möte

Använd- `join` metoden och skicka en Mötes länk eller koordinater för att ansluta till ett team möte.

Anslut med hjälp av en Mötes länk:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Anslut med hjälp av mötes koordinater:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Ta emot ett inkommande samtal

`callAgent`Instansen avger en `incomingCall` händelse när den inloggade identiteten tar emot ett inkommande samtal. Om du vill lyssna på den här händelsen kan du prenumerera med något av följande alternativ:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo

    //Accept the call
    var call = await incomingCall.accept();

    //Reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall`Händelsen innehåller en `incomingCall` instans som du kan acceptera eller avvisa.

## <a name="manage-calls"></a>Hantera samtal

Under ett anrop kan du komma åt samtals egenskaper och hantera inställningar för video och ljud.

### <a name="check-call-properties"></a>Kontrol lera anrops egenskaper

Hämta unikt ID (sträng) för ett anrop:

   ```js
    const callId: string = call.id;
   ```

Lär dig mer om andra deltagare i samtalet genom att inspektera `remoteParticipant` samlingen:

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Identifiera anroparen för ett inkommande samtal:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` är en av `CommunicationIdentifier` typerna.

Hämta status för ett anrop:

   ```js
   const callState = call.state;
   ```

   Detta returnerar en sträng som representerar det aktuella status för ett anrop:

  - `None`: Initialt anrops tillstånd.
  - `Incoming`: Anger att ett anrop är inkommande. Det måste antingen godkännas eller avvisas.
  - `Connecting`: Ursprungligt över gångs tillstånd när ett anrop läggs till eller godkänns.
  - `Ringing`: För ett utgående samtal indikerar det att ett anrop rings upp för fjärranslutna deltagare. Det finns `Incoming` på deras sida.
  - `EarlyMedia`: Anger ett tillstånd där ett meddelande spelas innan anropet ansluts.
  - `Connected`: Anger att anropet är anslutet.
  - `LocalHold`: Anger att anropet spärras av en lokal deltagare. Inget medium flödar mellan den lokala slut punkten och fjärranslutna deltagare.
  - `RemoteHold`: Anger att anropet var stoppad av en fjärran sluten deltagare. Inget medium flödar mellan den lokala slut punkten och fjärranslutna deltagare.
  - `Disconnecting`: Över gångs tillstånd innan anropet övergår till ett `Disconnected` tillstånd.
  - `Disconnected`: Slutligt anrops tillstånd. Om nätverks anslutningen förloras ändras statusen till `Disconnected` efter två minuter.

Ta reda på varför ett samtal avslutades genom att inspektera `callEndReason` egenskapen:

   ```js
   const callEndReason = call.callEndReason;
   // callEndReason.code (number) code associated with the reason
   // callEndReason.subCode (number) subCode associated with the reason
   ```

Lär dig om det aktuella anropet är inkommande eller utgående genom att granska `direction` egenskapen. Den returnerar `CallDirection` .

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Kontrol lera om den aktuella mikrofonen är avstängd. Den returnerar `Boolean` .

   ```js
   const muted = call.isMicrophoneMuted;
   ```

Ta reda på om skärm delnings strömmen skickas från en specifik slut punkt genom att kontrol lera `isScreenSharingOn` egenskapen. Den returnerar `Boolean` .

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Kontrol lera aktiva video strömmar genom att kontrol lera `localVideoStreams` samlingen. Den returnerar `LocalVideoStream` objekt.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```

### <a name="check-a-callended-event"></a>Kontrol lera en callEnded-händelse

`call`Instansen avger en `callEnded` händelse när anropet slutar. Om du vill lyssna på den här händelsen, prenumererar du med hjälp av följande kod:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Ljud av och på

Om du vill stänga av eller stänga av den lokala slut punkten kan du använda de `mute` `unmute` asynkrona API: erna:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Starta och stoppa sändning av lokal video

Om du vill starta en video måste du ange kameror genom att använda- `getCameras` metoden på `deviceManager` objektet. Skapa sedan en ny instans av `LocalVideoStream` genom att skicka önskad kamera till- `startVideo` metoden som ett argument:

```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);
```

När du har startat sändningen av en video `LocalVideoStream` läggs en instans till i `localVideoStreams` samlingen på en samtals instans.

```js
call.localVideoStreams[0] === localVideoStream;
```

Stoppa den lokala videon genom att skicka den `localVideoStream` instans som är tillgänglig i `localVideoStreams` samlingen:

```js
await call.stopVideo(localVideoStream);
```

Du kan växla till en annan kamera enhet medan en video skickas genom att anropa `switchSource` en `localVideoStream` instans:

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);
```

## <a name="manage-remote-participants"></a>Hantera fjärranslutna deltagare

Alla fjärranslutna deltagare representeras av `remoteParticipant` och är tillgängliga via `remoteParticipants` insamlingen på en samtals instans.

### <a name="list-the-participants-in-a-call"></a>Lista deltagarna i ett samtal

`remoteParticipants`Samlingen returnerar en lista över fjärranslutna deltagare i ett anrop:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Egenskaper för fjärråtkomst till fjärr deltagare

Fjärranslutna deltagare har en uppsättning tillhör ande egenskaper och samlingar:

- `CommunicationIdentifier`: Hämta ID för en fjärran sluten deltagare. Identiteten är en av `CommunicationIdentifier` typerna:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Det kan vara en av följande `CommunicationIdentifier` typer:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Ett objekt som representerar ACS-användaren.
  - `{ phoneNumber: '<E.164>' }`: Ett objekt som representerar telefonnumret i E. 164-format.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Ett objekt som representerar team användaren.

- `state`: Hämta status för en fjärran sluten deltagare.

  ```js
  const state = remoteParticipant.state;
  ```

  Status kan vara:

  - `Idle`: Initialt tillstånd.
  - `Connecting`: Över gångs tillstånd när en deltagare ansluter till anropet.
  - `Ringing`: Deltagaren ringer.
  - `Connected`: Deltagaren är ansluten till anropet.
  - `Hold`: Deltagare är stoppad.
  - `EarlyMedia`: Meddelande som spelas upp innan en deltagare ansluter till anropet.
  - `Disconnected`: Slutligt tillstånd. Deltagaren är frånkopplad från anropet. Om fjärran sluten deltagare förlorar sin nätverks anslutning ändras deras status till `Disconnected` efter två minuter.

- `callEndReason`: Om du vill veta varför en deltagare lämnat samtalet kontrollerar du `callEndReason` egenskapen:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  // callEndReason.code (number) code associated with the reason
  // callEndReason.subCode (number) subCode associated with the reason
  ```

- `isMuted` status: om du vill ta reda på om en fjärran sluten deltagare är avstängd, kontrollerar du `isMuted` egenskapen. Den returnerar `Boolean` .

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` status: om du vill ta reda på om en fjärran sluten deltagare pratar, kontrollerar du `isSpeaking` egenskapen. Den returnerar `Boolean` .

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Om du vill inspektera alla video strömmar som en viss deltagare skickar i det här anropet kontrollerar du `videoStreams` samlingen. Den innehåller `RemoteVideoStream` objekt.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```

### <a name="add-a-participant-to-a-call"></a>Lägg till en deltagare i ett samtal

Om du vill lägga till en deltagare (antingen en användare eller ett telefonnummer) till ett samtal kan du använda `addParticipant` . Ange en av `Identifier` typerna. Den returnerar `remoteParticipant` instansen.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Ta bort en deltagare från ett samtal

Om du vill ta bort en deltagare (antingen en användare eller ett telefonnummer) från ett samtal kan du anropa `removeParticipant` . Du måste skicka en av `Identifier` typerna. Detta löser asynkront efter att deltagaren har tagits bort från anropet. Deltagaren tas också bort från `remoteParticipants` samlingen.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Återge video strömmar för fjärranslutna deltagare

För att visa en lista över video strömmar och skärm delnings strömmar av fjärranslutna deltagare, inspektera `videoStreams` samlingarna:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

För att kunna återge måste `RemoteVideoStream` du prenumerera på en `isAvailableChanged` händelse. Om `isAvailable` egenskapen ändras till `true` skickar en fjärran sluten deltagare en ström. När detta har skett skapar du en ny instans av `Renderer` och skapar sedan en ny `RendererView` instans med hjälp av den asynkrona `createView` metoden.  Du kan sedan koppla `view.target` till ett gränssnitts element.

När tillgängligheten för en fjärrström ändras kan du förstöra `Renderer` , förstöra en `RendererView` instans eller behålla alla. Åter givningar som är kopplade till en otillgänglig ström leder till en tom video bild ruta.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Egenskaper för fjärran sluten video ström

Fjärranslutna video strömmar har följande egenskaper:

- `id`: ID för en fjärran sluten video ström.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `Stream.size`: Höjden och bredden på en fjärran sluten video ström.

  ```js
  const size: {width: number; height: number} = remoteVideoStream.size;
  ```

- `mediaStreamType`: Kan vara `Video` eller `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Om en slut punkt för en fjärran sluten part skickar aktivt en data ström.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="renderer-methods-and-properties"></a>Åter givnings metoder och egenskaper

Skapa en `rendererView` instans som kan kopplas till programmets användar gränssnitt för att återge den fjärranslutna video strömmen:

  ```js
  renderer.createView()
  ```

Ta bort `renderer` och alla associerade `rendererView` instanser:

  ```js
  renderer.dispose()
  ```

### <a name="rendererview-methods-and-properties"></a>Metoder och egenskaper för RendererView

När du skapar `rendererView` kan du ange `scalingMode` `isMirrored` egenskaperna och. `scalingMode` kan vara `Stretch` , `Crop` eller `Fit` . Om `isMirrored` har angetts vänds den renderade strömmen lodrätt.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```

Varje `RendererView` instans har en `target` egenskap som representerar åter givnings ytan. Bifoga den här egenskapen i programmets användar gränssnitt:

```js
document.body.appendChild(rendererView.target);
```

Du kan uppdatera `scalingMode` genom att anropa `updateScalingMode` metoden:

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Enhetshantering

I `deviceManager` kan du ange lokala enheter som kan överföra ljud-och video strömmar i ett samtal. Det hjälper dig också att begära behörighet att komma åt en annan användares mikrofon och kamera med hjälp av det inbyggda webb läsar-API: et

Du kan komma åt `deviceManager` genom att anropa `callClient.getDeviceManager()` metoden:

> [!IMPORTANT]
> Du måste ha ett `callAgent` objekt innan du kan komma åt det `deviceManager` .

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Hämta lokala enheter

Du kan använda uppräknings metoder på för att komma åt lokala enheter `deviceManager` .

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Ange standard mikrofon och högtalare

I `deviceManager` kan du ange en standardenhet som du ska använda för att starta ett samtal. Om klientens standardvärden inte har angetts använder kommunikations tjänsterna standardvärden för operativ system.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);
```

### <a name="local-camera-preview"></a>Lokal kamera för hands version

Du kan använda `deviceManager` och `Renderer` för att börja rendera strömmar från den lokala kameran. Den här data strömmen skickas inte till andra deltagare. Det är en lokal förhands gransknings matning.

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Begär behörighet till kamera och mikrofon

Meddela användaren om behörighet för kamera och mikrofon:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Detta löses med ett-objekt som anger om `audio` och `video` behörigheter har beviljats:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Registrera samtal

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

Anrops inspelning är en utökad funktion i Core `Call` API. Du måste först hämta API-objektet inspelnings funktioner:

```js
const callRecordingApi = call.api(Features.Recording);
```

Kontrol lera sedan om anropet registreras genom att kontrol lera `isRecordingActive` egenskapen för `callRecordingApi` . Den returnerar `Boolean` .

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

Du kan även prenumerera på inspelnings ändringar:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="transfer-calls"></a>Överförings anrop

Samtals överföring är en utökad funktion i Core `Call` API. Du måste först hämta API-objektet transfer Feature:

```js
const callTransferApi = call.api(Features.Transfer);
```

Samtals överföringar omfattar tre parter:

- *Överförings* ansvarig: personen som initierar överföringsbegäran.
- *Övertag* ande person: den person som överförs.
- *Överförings mål*: den person som överförs till.

Överföringar följer de här stegen:

1. Det finns redan ett anslutet samtal mellan *överförings parten* och den *övertag ande parten*. *Överförings* tjänsten bestämmer sig för att överföra anropet från *överföringsutleveransen* till *överförings målet*.
1. *Överförings* tjänsten anropar `transfer` API: et.
1. Den *övertag ande parten* bestämmer om `accept` eller `reject` överför begäran till *överförings målet* genom att använda en `transferRequested` händelse.
1. *Överförings målet* får bara ett inkommande samtal om *transfer* parten accepterar överföringsbegäran.

Om du vill överföra ett aktuellt anrop kan du använda `transfer` API: et. `transfer` tar det valfria `transferCallOptions` , vilket gör att du kan ange en `disableForwardingAndUnanswered` flagga:

- `disableForwardingAndUnanswered = false`: Om *överförings målet* inte besvarar överförings anropet följer överföringen överförings *målets* vidarebefordrande och inställningar för svar.
- `disableForwardingAndUnanswered = true`: Om *överförings målet* inte besvarar överförings anropet avslutas överförings försöket.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Med `transfer` API: et kan du prenumerera på `transferStateChanged` och `transferRequested` händelser. En `transferRequested` händelse kommer från en `call` instans, en `transferStateChanged` händelse och överföring `state` och `error` kommer från en `transfer` instans.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

Den *övertag* ande parten kan acceptera eller avvisa överföringsbegäran som initierats av *överlåtaren* i `transferRequested` händelsen genom att använda `accept()` eller `reject()` i `transferRequestedEventArgs` . Du kan komma åt `targetParticipant` information `accept` och `reject` metoder i `transferRequestedEventArgs` .

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

## <a name="learn-about-eventing-models"></a>Lär dig mer om händelse modeller

Granska aktuella värden och prenumerera på Uppdatera händelser för framtida värden.

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
