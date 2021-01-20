---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: c51316b445471c58ae88237b370f5a25fdedf373
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98605151"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- En distribuerad kommunikations tjänst resurs. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- A `User Access Token` för att aktivera anrops klienten. Mer information om [hur du hämtar en `User Access Token` ](../../access-tokens.md)
- Valfritt: Slutför snabb starten för att [komma igång med att lägga till samtal till ditt program](../getting-started-with-calling.md)

## <a name="setting-up"></a>Konfigurera

### <a name="install-the-client-library"></a>Installera klient biblioteket

Använd `npm install` kommandot för att installera Azure Communication Services-anrop och vanliga klient bibliotek för Java Script.

```console
npm install @azure/communication-common --save

npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services som anropar klient biblioteket:

| Name                             | Beskrivning                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient är den huvudsakliga start punkten för det anropande klient biblioteket.                                                                       |
| CallAgent                        | CallAgent används för att starta och hantera samtal.                                                                                            |
| AzureCommunicationUserCredential | AzureCommunicationUserCredential-klassen implementerar CommunicationUserCredential-gränssnittet som används för att instansiera CallAgent. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Initiera CallClient, skapa CallAgent och komma åt DeviceManager

Instansiera en ny `CallClient` instans. Du kan konfigurera den med anpassade alternativ som en loggad instans.
När en `CallClient` instansieras kan du skapa en `CallAgent` instans genom att anropa `createCallAgent` metoden på `CallClient` instansen. Ett instans objekt returneras asynkront `CallAgent` .
`createCallAgent`Metoden tar ett `CommunicationUserCredential` som argument, som godkänner en åtkomsttoken för [användare](../../access-tokens.md).
För att få åtkomst till `DeviceManager` en callAgent-instans måste först skapas. Du kan sedan använda `getDeviceManager` metoden på `CallClient` instansen för att hämta devicemanager.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationUserCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Placera ett utgående samtal

Om du vill skapa och starta ett samtal måste du använda ett av API: erna på CallAgent och tillhandahålla en användare som du har skapat via administrations klient biblioteket för kommunikations tjänster.

Skapande av anrop och start är synkront. Med anrops instansen kan du prenumerera på samtals händelser.

## <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Placera ett 1:1-anrop till en användare eller 1: n Anrop till användare och PSTN

Om du vill ringa till en annan kommunikations tjänst användare, anropar du `call` metoden på `callAgent` och skickar CommunicationUser som du har [skapat med administrations biblioteket för kommunikations tjänster](../../access-tokens.md).

```js
const oneToOneCall = callAgent.call([CommunicationUser]);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Placera ett 1: n-anrop med användare och PSTN

Om du vill placera ett 1: n Anrop till en användare och ett PSTN-nummer måste du ange en CommunicationUser och ett telefonnummer för båda callees.

Resursen för kommunikations tjänster måste konfigureras för att tillåta PSTN-anrop.
```js

const userCallee = { communicationUserId: <ACS_USER_ID> };
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const groupCall = callAgent.call([userCallee, pstnCallee], placeCallOptions);

```

### <a name="place-a-11-call-with-video-camera"></a>Placera ett 1:1-samtal med video kameran
> [!WARNING]
> Det kan för närvarande inte finnas fler än en utgående lokal video ström.
Om du vill placera ett video samtal måste du räkna upp lokala kameror med deviceManager- `getCameraList` API: et.
När du har valt den önskade kameran använder du den för att konstruera en `LocalVideoStream` instans och skicka den `videoOptions` som ett objekt i `localVideoStream` matrisen till- `call` metoden.
När anropet ansluter börjar den automatiskt att skicka en video ström från den valda kameran till den eller de andra deltagarna.

Detta gäller även video alternativen Call. Accept () och CallAgent. JOIN ().
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="receiving-an-incoming-call"></a>Tar emot ett inkommande samtal
```js
callAgent.on('callsUpdated', e => {
    e.added.forEach(addedCall => {
        if(addedCall.isIncoming) {
        addedCall.accept();
    }
    });
})
```

### <a name="join-a-group-call"></a>Anslut till ett grupp anrop
Om du vill starta ett nytt grupp anrop eller ansluta ett pågående grupp anrop använder du metoden Join och skickar ett objekt med en `groupId` egenskap. Värdet måste vara ett GUID.
```js

const locator = { groupId: <GUID>}
const call = callAgent.join(locator);

```

### <a name="join-a-teams-meeting"></a>Delta i ett team möte
Om du vill ansluta till ett team möte använder du Join-metoden och skickar en Mötes länk eller ett mötes koordinater
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="call-management"></a>Anrops hantering

Du kan komma åt anrops egenskaper och utföra olika åtgärder under ett anrop för att hantera inställningar som rör video och ljud.

### <a name="call-properties"></a>Anrops egenskaper
* Hämta unikt ID (sträng) för det här anropet.
```js

const callId: string = call.id;

```

* Om du vill veta mer om andra deltagare i anropet kan du granska `remoteParticipant` samlingen på `call` instansen. Matrisen innehåller List `RemoteParticipant` objekt
```js
const remoteParticipants = call.remoteParticipants;
```

* Anroparens identitet om samtalet är inkommande. Identiteten är en av `Identifier` typerna
```js

const callerIdentity = call.callerIdentity;

```

* Hämta status för anropet.
```js

const callState = call.state;

```
Detta returnerar en sträng som representerar det aktuella status för ett anrop:
* Ingen-första anrops status
* ' Inkommande ' – anger att ett samtal är inkommande, måste antingen godkännas eller avvisas
* Ansluter – ursprungligt över gångs tillstånd när anropet har placerats eller godkänts
* ' Ringing ' – för ett utgående anrop – indikerar att samtal rings upp för fjärranslutna deltagare, det är "inkommande" på deras sida
* ' EarlyMedia ' – anger ett tillstånd där ett meddelande spelas innan samtalet ansluts
* Ansluten – anropet är anslutet
* Hold '-anropet är spärrat, inget medium flödar mellan lokal slut punkt och fjärran sluten deltagare
* "Kopplar från"-över gångs läget innan anropet går till frånkopplat läge
* ' Frånkopplat ' – slutligt anrops tillstånd.
   * Om nätverks anslutningen förloras går tillstånd till "frånkopplad" efter ungefär 2 minuter.


* Om du vill se varför ett angivet samtal avslutades, kontrollerar du `callEndReason` egenskapen.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Om du vill veta om det aktuella anropet är ett inkommande samtal kontrollerar du att `isIncoming` egenskapen returneras `Boolean` .
```js
const isIncoming = call.isIncoming;
```

* Om du vill kontrol lera om anropet registreras kontrollerar du `isRecordingActive` egenskapen, den returneras `Boolean` .
```js
const isResordingActive = call.isRecordingActive;
```

*  Om du vill kontrol lera om den aktuella mikrofonen är avstängd, kontrollerar du att `muted` egenskapen returneras `Boolean` .
```js

const muted = call.isMicrophoneMuted;

```

* Om du vill se om skärm delnings strömmen skickas från en specifik slut punkt, kontrollerar du att `isScreenSharingOn` egenskapen returneras `Boolean` .
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Om du vill kontrol lera aktiva video strömmar kontrollerar du att `localVideoStreams` samlingen innehåller `LocalVideoStream` objekt
```js

const localVideoStreams = call.localVideoStreams;

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


Om du vill starta en video måste du räkna upp kameror med hjälp av `getCameraList` metoden på `deviceManager` objektet. Skapa sedan en ny instans för `LocalVideoStream` att skicka den önskade kameran till- `startVideo` metoden som ett argument:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

När du har startat sändningen kommer en `LocalVideoStream` instans att läggas till i `localVideoStreams` samlingen på en anrops instans.

```js

call.localVideoStreams[0] === localVideoStream;

```

Stoppa den lokala videon genom att skicka den `localVideoStream` tillgängliga instansen i `localVideoStreams` samlingen:

```js

await call.stopVideo(localVideoStream);

```

Du kan växla till en annan kamera enhet medan video skickas genom att anropa `switchSource` en `localVideoStream` instans:

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```
### <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
 * Om nätverks anslutningen förloras ändras anrops läget till "frånkopplad"?
    * Ja, om nätverks anslutningen går förlorad i mer än två minuter, övergår anropet till frånkopplat läge och anropet avslutas.

## <a name="remote-participants-management"></a>Hantering av fjärranslutna deltagare

Alla fjärranslutna deltagare representeras av `RemoteParticipant` typ och är tillgängliga via `remoteParticipants` samling på en samtals instans.

### <a name="list-participants-in-a-call"></a>Lista deltagare i ett samtal
`remoteParticipants`Samlingen returnerar en lista över fjärranslutna deltagare i angivet anrop:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Egenskaper för fjärran sluten part
Fjärran sluten part har en uppsättning egenskaper och samlingar som är associerade med den

* Hämta ID: t för den här fjärran deltagaren.
Identiteten är en av Identifier-typerna:
```js
const identifier = remoteParticipant.identifier;
//It can be one of:
// { communicationUserId: '<ACS_USER_ID'> } - object representing ACS User
// { phoneNumber: '<E.164>' } - object representing phone number in E.164 format
```

* Hämta tillstånd för denna fjärran sluten deltagare.
```js

const state = remoteParticipant.state;
```
Tillstånd kan vara en av
* Passiv-initial status
* ' Connect ' – över gångs tillstånd medan deltagare ansluter till anropet
* Ansluten – deltagaren är ansluten till anropet
* Hold '-deltagare är stoppad
* ' EarlyMedia ' – meddelandet spelas upp innan deltagaren ansluts till anropet
* ' Frånkopplad ' – slutligt tillstånd-deltagare är frånkopplat från anropet.
   * Om fjärrparten förlorar sin nätverks anslutning går fjärr anslutnings status till "frånkopplad" efter ungefär 2 minuter.

Om du vill veta varför deltagaren lämnade anropet, kontrollerar du `callEndReason` egenskapen:
```js

const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Om du vill kontrol lera om den här fjärrparten är avstängd eller inte, kontrollerar `isMuted` du att egenskapen returneras `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```

* Om du vill kontrol lera om den här fjärrdeltagaren pratar eller inte, kontrollerar `isSpeaking` du att egenskapen returneras `Boolean`
```js

const isSpeaking = remoteParticipant.isSpeaking;

```

* Om du vill kontrol lera alla video strömmar som en viss deltagare skickar i det här anropet markerar `videoStreams` du samlingen, den innehåller `RemoteVideoStream` objekt
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Lägg till en deltagare i ett samtal

Om du vill lägga till en deltagare till ett samtal (antingen en användare eller ett telefonnummer) kan du anropa `addParticipant` .
Ange en av typerna Identifier.
Fjärran sluten instans skickas synkront.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>};
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier);
```

### <a name="remove-participant-from-a-call"></a>Ta bort deltagare från ett samtal

Om du vill ta bort en deltagare från ett samtal (antingen en användare eller ett telefonnummer) kan du anropa `removeParticipant` .
Du måste skicka en av "Identifier"-typerna detta kommer att lösa asynkront när deltagaren tas bort från anropet.
Deltagaren tas också bort från `remoteParticipants` samlingen.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>};
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Återge video strömmar för fjärranslutna deltagare

För att visa en lista över video strömmar och skärm delnings strömmar av fjärranslutna deltagare, inspektera `videoStreams` samlingarna:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
För att kunna återge en `RemoteVideoStream` måste du prenumerera på en `isAvailableChanged` händelse.
Om `isAvailable` egenskapen ändras till `true` skickar en fjärran sluten deltagare en ström.
När det händer skapar du en ny instans av `Renderer` och skapar sedan en ny `RendererView` instans med hjälp av den asynkrona `createView` metoden.  Du kan sedan koppla `view.target` till ett gränssnitts element.
När tillgängligheten för en fjärrström ändras kan du välja att förstöra hela åter givningen, en speciell `RendererView` eller behålla dem, men det leder till att tomma video rutor visas.

```js
let renderer: Renderer;
const displayVideo = () => {
    renderer = new Renderer(remoteParticipantStream);
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>Egenskaper för fjärran sluten video ström
Fjärranslutna video strömmar har följande egenskaper:

* `Id` -ID för en fjärran sluten video ström
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` -storlek (bredd/höjd) för en fjärran sluten video ström
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` -kan vara video eller ScreenSharing
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable` -Anger om slut punkten för den fjärranslutna parten skickar ström
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Åter givnings metoder och egenskaper

* Skapa en `RendererView` instans som senare kan bifogas i programmets användar gränssnitt för att rendera den fjärranslutna video strömmen.
```js
renderer.createView()
```

* Ta bort åter givningen och alla associerade `RendererView` instanser.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>Metoder och egenskaper för RendererView
När du skapar en `RendererView` kan du ange `scalingMode` och `mirrored` Egenskaper.
Skalnings läget kan vara "Sträck ut", "Beskär" eller "passa" om `Mirrored` har angetts, så vänds den återgivna strömmen till lodrätt.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
En specifik `RendererView` instans har en `target` egenskap som representerar åter givnings ytan. Detta måste bifogas i programmets användar gränssnitt:
```js
document.body.appendChild(rendererView.target);
```

Du kan uppdatera skalnings läget senare genom att anropa- `updateScalingMode` metoden.
```js
view.updateScalingMode('Crop')
```
### <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
* Om en fjärran sluten part förlorar sin nätverks anslutning, ändrar deras tillstånd till "frånkopplad"?
    * Ja, om en fjärran sluten deltagare förlorar sin nätverks anslutning under mer än två minuter, kommer deras tillstånd att övergå till frånkopplat och de tas bort från anropet.
## <a name="device-management"></a>Enhetshantering

`DeviceManager` gör att du kan räkna upp lokala enheter som kan användas i ett anrop för att överföra ljud-och video strömmar. Du kan också begära behörighet från en användare för att få åtkomst till mikrofonen och kameran med hjälp av det inbyggda webb läsar-API: et.

Du kan komma åt `deviceManager` genom att anropa- `callClient.getDeviceManager()` metoden.
> [!WARNING]
> För närvarande `callAgent` måste ett objekt först instansieras för att få åtkomst till devicemanager

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Räkna upp lokala enheter

Du kan använda uppräknings metoder på Enhetshanteraren för att komma åt lokala enheter. Uppräkning är en synkron åtgärd.

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Ange standard mikrofon/talare

Med enhets hanteraren kan du ange en standardenhet som ska användas när ett samtal startas.
Om klientens standardvärden inte har angetts, kommer kommunikations tjänsterna att återgå till standardinställningarna för operativ systemet.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceMicrophone.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Lokal kamera för hands version

Du kan använda `DeviceManager` och `Renderer` för att börja rendera strömmar från den lokala kameran. Den här data strömmen skickas inte till andra deltagare. Det är en lokal förhands gransknings matning. Detta är en asynkron åtgärd.

```js
const localVideoDevice = deviceManager().getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Begär behörighet till kamera/mikrofon

Begär att en användare ska ge kamera-/mikrofon behörigheter med följande:

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
Detta kommer att lösa asynkront med ett objekt som anger om `audio` och `video` behörigheter har beviljats:
```js
console.log(result.audio);
console.log(result.video);
```

Du kan kontrol lera det aktuella behörighets läget för en specifik typ genom att anropa `getPermissionState` :

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="eventing-model"></a>Händelse modell

Du kan prenumerera på de flesta egenskaper och samlingar för att få ett meddelande när värdena ändras.

### <a name="properties"></a>Egenskaper
Så här prenumererar du på `property changed` händelser:

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>Samlingar
Så här prenumererar du på `collection updated` händelser:

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
