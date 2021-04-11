---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 45a772b4a1d65b67f918107fd33135a56f6302f2
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073101"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]


## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- En distribuerad kommunikations tjänst resurs. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- A `User Access Token` för att aktivera anrops klienten. Mer information om [hur du hämtar en `User Access Token` ](../../access-tokens.md)
- Valfritt: Slutför snabb starten för att [komma igång med att lägga till samtal till ditt program](../getting-started-with-calling.md)

## <a name="setting-up"></a>Konfigurera

### <a name="install-the-package"></a>Installera paketet

> [!NOTE]
> Det här dokumentet använder version 1.0.0 – beta. 8 av anrops-SDK: n.

Leta upp build. gradle på projekt nivå och se till att lägga till `mavenCentral()` i listan över databaser under `buildscript` och `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Sedan lägger du till följande rader i avsnittet beroenden i din modulnivå build. gradle

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}

```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services som anropar SDK:

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient är den huvudsakliga start punkten för den anropande SDK: n.|
| CallAgent | CallAgent används för att starta och hantera samtal. |
| CommunicationTokenCredential | CommunicationTokenCredential används som token-autentiseringsuppgifter för att instansiera CallAgent.|
| CommunicationIdentifier | CommunicationIdentifier används som en annan typ av deltagare som skulle kunna vara en del av ett samtal.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Initiera CallClient, skapa en CallAgent och få åtkomst till DeviceManager

Om du vill skapa en `CallAgent` instans måste du anropa- `createCallAgent` metoden på en `CallClient` instans. Ett instans objekt returneras asynkront `CallAgent` .
`createCallAgent`Metoden tar ett `CommunicationUserCredential` as-argument som kapslar in en [åtkomsttoken](../../access-tokens.md).
För att få åtkomst till `DeviceManager` måste en callAgent-instans skapas först och sedan kan du använda `CallClient.getDeviceManager` metoden för att hämta devicemanager.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager().get();
```
Använd den här alternativa metoden för att ange ett visnings namn för anroparen:

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
DeviceManager deviceManager = callClient.getDeviceManager().get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Placera ett utgående samtal och delta i ett grupp anrop

Om du vill skapa och starta ett samtal måste du anropa `CallAgent.startCall()` -metoden och ange `Identifier` för-eller-anropen.
Om du vill ansluta till ett grupp anrop måste du anropa- `CallAgent.join()` metoden och ange ett välliggande metod nummer. Grupp-ID: n måste vara i GUID-eller UUID-format.

Skapande av anrop och start är synkront. Anrops instansen gör att du kan prenumerera på alla händelser på anropet.

### <a name="place-a-11-call-to-a-user"></a>Placera ett 1:1-anrop till en användare
För att placera ett anrop till en annan kommunikations tjänst användare, anropa `call` metoden på `callAgent` och skicka ett objekt med `communicationUserId` nyckeln.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Placera ett 1: n-anrop med användare och PSTN
> [!WARNING]
> För närvarande är PSTN-anrop inte tillgängligt

Om du vill placera ett 1: n-Anrop till en användare och ett PSTN-nummer måste du ange telefonnumret för den här anrops mottagaren.
Resursen för kommunikations tjänster måste konfigureras för att tillåta PSTN-anrop:
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>Placera ett 1:1-samtal med video kameran
> [!WARNING]
> För närvarande stöds endast en utgående lokal video ström för att placera ett samtal med video som du måste räkna upp lokala kameror med hjälp av `deviceManager` `getCameras` API: et.
När du har valt en önskad kamera använder du den för att skapa en `LocalVideoStream` instans och skickar den till `videoOptions` som ett objekt i `localVideoStream` matrisen till en `call` metod.
När anropet ansluter börjar den automatiskt att skicka en video ström från den valda kameran till andra deltagare.

> [!NOTE]
> På grund av integritets problem kommer videon inte att delas till samtalet om den inte har för hands visats lokalt.
Mer information finns i för [hands versionen av lokal kamera](#local-camera-preview) .
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Anslut till ett grupp anrop
Om du vill starta ett nytt grupp anrop eller ansluta ett pågående grupp anrop måste du anropa Join-metoden och skicka ett objekt med en `groupId` egenskap. Värdet måste vara ett GUID.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

### <a name="accept-a-call"></a>Acceptera ett samtal
Om du vill acceptera ett anrop anropar du metoden accept på ett Call-objekt.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

Så här accepterar du ett samtal med video kamera på:

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

Det inkommande anropet kan erhållas genom att prenumerera på `onIncomingCall` händelsen på `callAgent` objektet:

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```


## <a name="push-notifications"></a>Push-meddelanden

### <a name="overview"></a>Översikt
Mobila push-meddelanden är de popup-meddelanden som visas på mobila enheter. För att anropa ska vi fokusera på VoIP (Voice-of-Internet Protocol) push-meddelanden. Vi registrerar dig för push-meddelanden, hanterar push-meddelanden och avregistrerar push-meddelanden.

### <a name="prerequisites"></a>Förutsättningar

Ett Firebase-konto som har kon figurer ATS med Cloud Messaging (FCM) aktiverat och med din Firebase-moln meddelande tjänst ansluten till en Azure Notification Hub-instans. Mer information finns i [meddelanden om kommunikations tjänster](../../../concepts/notifications.md) .
Dessutom förutsätter du själv studie kursen att du använder Android Studio version 3,6 eller senare för att bygga ditt program.

En uppsättning behörigheter krävs för att Android-programmet ska kunna ta emot meddelanden från Firebase Cloud Messaging. I `AndroidManifest.xml` filen lägger du till följande behörighets uppsättning direkt efter *<manifestet... >* eller under *</application>* taggen

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Registrera dig för push-meddelanden

För att registrera för push-meddelanden måste programmet anropa `registerPushNotification()` en *CallAgent* -instans med en token för enhets registrering.

Du kan hämta token för enhets registrering genom att lägga till Firebase SDK i programmodulens *build. gradle* -fil genom att lägga till följande rader i `dependencies` avsnittet om den inte redan finns där:

```
    // Add the SDK for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

På projekt nivåns *build. gradle* -fil lägger du till följande i `dependencies` avsnittet om det inte redan finns:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Lägg till följande plugin-program till början av filen om det inte redan finns:

```
apply plugin: 'com.google.gms.google-services'
```

Välj *Synkronisera nu* i verktygsfältet. Lägg till följande kodfragment för att hämta token för enhets registrering som genereras av Firebase Cloud Messaging SDK för klient program instansen. se till att lägga till nedanstående import i huvud aktiviteten för instansen. De krävs för att kodfragmentet ska kunna hämta token:

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Lägg till det här kodfragmentet för att hämta token:

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
Registrera Device Registration-token med anrops tjänsterna SDK för push-meddelanden för inkommande samtal:

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Hantering av push-meddelanden

Om du vill ta emot push-meddelanden för inkommande samtal anropar du *handlePushNotification ()* på en *CallAgent* -instans med en nytto Last.

Du hämtar nytto lasten från Firebase Cloud Messaging genom att börja med att skapa en ny tjänst (fil > ny > tjänst > tjänst) som utökar SDK-klassen *FirebaseMessagingService* Firebase och åsidosätter `onMessageReceived` metoden. Den här metoden är händelse hanteraren som anropas när Firebase Cloud Messaging levererar push-meddelanden till programmet.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
Lägg till följande tjänst definition i `AndroidManifest.xml` filen, inuti <application> taggen:

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- När nytto lasten har hämtats kan den skickas till *kommunikations tjänst* -SDK: n för att parsas ut till ett internt *IncomingCallInformation* -objekt som ska hanteras genom att anropa *HandlePushNotification* -metoden på en *CallAgent* -instans. En `CallAgent` instans skapas genom att anropa- `createCallAgent(...)` metoden i `CallClient` klassen.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

När hanteringen av push-meddelandet lyckas och alla händelse hanterare registreras korrekt, kommer programmet att ringas.

### <a name="unregister-push-notifications"></a>Avregistrera push-meddelanden

Program kan avregistrera push-meddelanden när som helst. Anropa `unregisterPushNotification()` metoden på callAgent för avregistrering.

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Anrops hantering
Du kan komma åt anrops egenskaper och utföra olika åtgärder under ett anrop för att hantera inställningar som rör video och ljud.

### <a name="call-properties"></a>Anrops egenskaper

Hämta unikt ID för det här anropet:

```java
String callId = call.getId();
```

Lär dig mer om andra deltagare i anrops inspektions `remoteParticipant` samlingen på `call` instansen:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

Anroparens identitet om samtalet är inkommande:

```java
CommunicationIdentifier callerId = call.getCallerId();
```

Hämta status för anropet: 

```java
CallState callState = call.getState();
```

Den returnerar en sträng som representerar det aktuella status för ett anrop:
* Ingen-första anrops status
* Ansluter – ursprungligt över gångs tillstånd när anropet har placerats eller godkänts
* ' Ringing ' – för ett utgående anrop – indikerar att samtal rings för fjärranslutna deltagare
* ' EarlyMedia ' – anger ett tillstånd där ett meddelande spelas upp innan anropet ansluts
* Ansluten – anropet är anslutet
* "LocalHold"-anropet spärras av lokal deltagare, inget medium flödar mellan lokal slut punkt och fjärran sluten deltagare
* ' RemoteHold ' – anropet spärras av en fjärran sluten deltagare, inget medium flödar mellan lokal slut punkt och fjärran sluten deltagare
* "Kopplar från"-över gångs läget innan anrop övergår till frånkopplat läge
* Frånkopplat-avslutande anrops tillstånd


Om du vill veta varför ett anrop avslutat, kontrollerar du `callEndReason` egenskapen. Den innehåller kod/under kod: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

För att se om det aktuella anropet är ett inkommande eller utgående anrop, kontrollerar du `callDirection` egenskapen:

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.Incoming for incoming call
// callDirection == CallDirection.Outgoing for outgoing call
```

För att se om den aktuella mikrofonen är avstängd, kontrollerar du `muted` egenskapen:

```java
boolean muted = call.getIsMicrophoneMuted();
```

Om du vill se om det aktuella anropet registreras kontrollerar du `isRecordingActive` egenskapen:

```java
boolean recordinggActive = call.getIsRecordingActive();
```

Om du vill kontrol lera aktiva video strömmar kontrollerar du `localVideoStreams` samlingen:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Ljud av och på

Om du vill stänga av eller stänga av den lokala slut punkten kan du använda de `mute` `unmute` asynkrona API: erna:

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>Starta och stoppa sändning av lokal video

Om du vill starta en video måste du räkna upp kameror med hjälp av `getCameraList` API: et för `deviceManager` objektet. Skapa sedan en ny instans av `LocalVideoStream` att skicka önskad kamera och skicka den till `startVideo` API: et som ett argument:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(currentLocalVideoStream);
startVideoFuture.get();
```

När du har startat sändningen kommer en `LocalVideoStream` instans att läggas till i `localVideoStreams` samlingen på anrops instansen.

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

Stoppa den lokala videon genom att skicka den `LocalVideoStream` tillgängliga instansen i `localVideoStreams` samlingen:

```java
call.stopVideo(currentLocalVideoStream).get();
```

Du kan växla till en annan kamera enhet medan video skickas genom att anropa `switchSource` en `LocalVideoStream` instans:
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Hantering av fjärranslutna deltagare

Alla fjärranslutna deltagare representeras av `RemoteParticipant` Type och är tillgängliga via `remoteParticipants` samlingen på en samtals instans.

### <a name="list-participants-in-a-call"></a>Lista deltagare i ett samtal
`remoteParticipants`Samlingen returnerar en lista över fjärranslutna deltagare i angivet anrop:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Egenskaper för fjärran sluten part
En viss fjärran sluten deltagare har en uppsättning egenskaper och samlingar som är associerade med den:

* Hämta ID: t för den här fjärran deltagaren.
Identiteten är en av Identifier-typerna
```java
CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
```

* Hämta tillstånd för denna fjärran sluten deltagare.
```java
ParticipantState state = remoteParticipant.getState();
```
Tillstånd kan vara en av
* Passiv-initial status
* ' EarlyMedia ' – meddelandet spelas upp innan deltagaren ansluts till anropet
* Ring samtal-deltagare ringer
* ' Connect ' – över gångs tillstånd medan deltagare ansluter till anropet
* Ansluten – deltagaren är ansluten till anropet
* Hold '-deltagare är stoppad
* "In"-deltagare väntar i lobbyn att bli upptagna. Används för närvarande endast i team interop-scenario
* ' Frånkopplad ' – slutligt tillstånd-deltagare är frånkopplat från anropet


* Om du vill veta varför en deltagare lämnat samtalet, kontrollerar du `callEndReason` egenskapen:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Kontrol lera om den här fjärrparten är avstängd eller inte genom att kontrol lera `isMuted` egenskapen:
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* Om du vill kontrol lera om den här fjärrparten är intalad eller inte kontrollerar du `isSpeaking` egenskapen:
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* Om du vill kontrol lera alla video strömmar som en viss deltagare skickar i det här anropet kontrollerar du `videoStreams` samlingen:
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>Lägg till en deltagare i ett samtal

Om du vill lägga till en deltagare till ett samtal (antingen en användare eller ett telefonnummer) kan du anropa `addParticipant` . Fjärran sluten instans skickas synkront.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>Ta bort deltagare från ett samtal
Om du vill ta bort en deltagare från ett samtal (antingen en användare eller ett telefonnummer) kan du anropa `removeParticipant` .
Detta kommer att lösa asynkront när deltagaren tas bort från anropet.
Deltagaren tas också bort från `remoteParticipants` samlingen.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

## <a name="render-remote-participant-video-streams"></a>Återge video strömmar för fjärranslutna deltagare
För att visa en lista över video strömmar och skärm delnings strömmar av fjärranslutna deltagare, inspektera `videoStreams` samlingarna:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Om du vill rendera en `RemoteVideoStream` från en fjärran sluten deltagare måste du prenumerera på en `OnVideoStreamsUpdated` händelse.

I händelse `isAvailable` indikerar egenskapen Change of True att fjärrparten för närvarande skickar en ström. När det händer skapar du en ny instans av a `Renderer` och skapar sedan en ny `RendererView` med asynkron `createView` API och kopplar `view.target` var som helst i användar gränssnittet för ditt program.

När tillgängligheten för en fjärrström ändras kan du välja att förstöra hela åter givningen, en speciell `RendererView` eller behålla dem, men det leder till att tomma video rutor visas.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Egenskaper för fjärran sluten video ström
Fjärran sluten video strömmar har ett par egenskaper

* `Id` -ID för en fjärran sluten video ström
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` -Kan vara video eller ScreenSharing
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` -Anger om slut punkten för den fjärranslutna parten skickar ström
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>Åter givnings metoder och egenskaper
Åter givnings objekt följande API: er

* Skapa en `RendererView` instans som senare kan bifogas i programmets användar gränssnitt för att rendera en fjärran sluten video ström.
```java
// Create a view for a video stream
renderer.createView()
```
* Ta bort åter givning och alla `RendererView` associerade med den här åter givningen. Anropas när du har tagit bort alla associerade vyer från användar gränssnittet.
```java
renderer.dispose()
```

* `StreamSize` -storlek (bredd/höjd) för en fjärran sluten video ström
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>Metoder och egenskaper för RendererView
När du skapar en `RendererView` kan du ange vilka `scalingMode` och `mirrored` egenskaper som ska gälla för den här vyn: skalnings läget kan vara antingen "Sträck ut" | Beskär | Passa om `mirrored` är inställt på `true` , vänds den renderade strömmen lodrätt.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

De skapade RendererView kan sedan kopplas till program gränssnittet med hjälp av följande kodfragment:
```java
layout.addView(rendererView);
```

Du kan senare uppdatera skalnings läget genom att anropa `updateScalingMode` API på RendererView-objektet med en av ScalingMode. utsträckt | ScalingMode. beskärning | ScalingMode. passa som ett argument.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>Enhetshantering

`DeviceManager` gör att du kan räkna upp lokala enheter som kan användas i ett anrop för att överföra ljud-och video strömmar. Du kan också begära behörighet från en användare för att få åtkomst till mikrofonen och kameran med hjälp av det inbyggda webb läsar-API: et.

Du kan komma åt `deviceManager` genom att anropa `callClient.getDeviceManager()` metoden.
> [!WARNING]
> För närvarande `callAgent` måste ett objekt först instansieras för att få åtkomst till devicemanager

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>Räkna upp lokala enheter

Du kan använda uppräknings metoder på Enhetshanteraren för att komma åt lokala enheter. Uppräkning är en synkron åtgärd.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>Ange standard mikrofon/talare

Med enhets hanteraren kan du ange en standardenhet som ska användas när ett samtal startas.
Om klientens standardvärden inte har angetts, kommer kommunikations tjänsterna att återgå till standardinställningarna för operativ systemet.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophones().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakers().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>Lokal kamera för hands version

Du kan använda `DeviceManager` och `Renderer` för att börja rendera strömmar från den lokala kameran. Den här data strömmen skickas inte till andra deltagare. Det är en lokal förhands gransknings matning. Detta är en asynkron åtgärd.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>Händelse modell
Du kan prenumerera på de flesta egenskaper och samlingar för att få ett meddelande när värdena ändras.

### <a name="properties"></a>Egenskaper
Så här prenumererar du på `property changed` händelser:

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Samlingar
Så här prenumererar du på `collection updated` händelser:

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
