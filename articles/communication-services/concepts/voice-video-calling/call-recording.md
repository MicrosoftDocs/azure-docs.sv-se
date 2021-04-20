---
title: översikt Azure Communication Services samtalsinspelning
titleSuffix: An Azure Communication Services concept document
description: Innehåller en översikt över funktionen För samtalsinspelning och API:er.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/13/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df9638588b4d677a7ceb80bafbe7157bb5c2df42
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730607"
---
# <a name="calling-recording-overview"></a>Översikt över anrop av inspelning

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

> [!NOTE]
> Många länder och delstater har lagar och föreskrifter som gäller för inspelning av PSTN-, röst- och videosamtal, som ofta kräver att användarna samtycker till inspelning av sin kommunikation. Det är ditt ansvar att använda funktionerna för samtalsinspelning i enlighet med lagen. Du måste få medgivande från parterna om den registrerade kommunikationen på ett sätt som uppfyller de lagar som gäller för varje deltagare.

> [!NOTE]
> Regler för underhåll av personuppgifter kräver möjligheten att exportera användardata. För att stödja dessa krav inkluderar registrering av metadatafiler deltagar-ID för varje anropsdeltagare i `participants` matrisen. Du kan korsreferenser till MRI:erna i `participants` matrisen med dina interna användaridentiteter för att identifiera deltagarna i ett anrop. Ett exempel på en inspelningsmetadatafil finns nedan som referens.

Samtalsinspelning innehåller en uppsättning API:er för att starta, stoppa, pausa och återuppta inspelningen. Dessa API:er kan nås från affärslogik på serversidan eller via händelser som utlöses av användaråtgärder. Inspelade medieutdata är `MP4 Audio+Video` i format, vilket är samma format som Teams använder för att spela in media. Meddelanden som rör media och metadata genereras via Event Grid. Inspelningar lagras i 48 timmar på inbyggd tillfällig lagring för hämtning och förflyttning till en långsiktig lagringslösning som du väljer. 

## <a name="run-time-control-apis"></a>API:er för körningskontroll
API:er för körningskontroll kan användas för att hantera registrering via interna affärslogikutlösare, till exempel ett program som skapar ett gruppsamtal och spelar in konversationen, eller från en användarutlöst åtgärd som talar om för serverprogrammet att börja spela in. I båda scenarierna `<conversation-id>` krävs för att registrera ett specifikt möte eller anrop. 

#### <a name="getting-conversation-id-from-a-server-initiated-call"></a>Hämta konversations-ID från ett serverinitierade anrop

En `ConversationId` returneras via `Microsoft.Communication.CallLegStateChanged` händelsen. Det här händelsemeddelandet genereras när ett anrop har upprättats. Du hittar den i `data.ConversationId` fältet . Det här värdet kan användas direkt som `{conversationId}` parameter i körningskontroll-API:er:
```
      {
        "id": null,
        "topic": null,
        "subject": "callLeg/<callLegId>/callState",
        "data": {
---->       "ConversationId": "<conversation-id>",    <----
            "CallLegId": "<callLegId>",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-04-14T16:32:34.1115003Z",
        "metadataVersion": null,
        "dataVersion": null
    }
```
                                                            
#### <a name="getting-the-conversation-id-from-a-user-triggered-event-on-the-client"></a>Hämta konversations-ID:t från en användarutlöst händelse på klienten

Från JavaScript-biblioteket när du har upprättat ett anrop anropa för att hämta , kodar sedan Base64Url för att hämta för användning i `@azure/communication-calling` `let result = call.info.getConversationUrl()` `conversationUrl` **`conversationUrl` `{conversationId}` körningskontroll-API:erna**. Kodning kan göras antingen på klienten innan händelsen skickas till servern eller på serversidan.

Observera att måste vara Base64Url-kodad, inte ska förväxlas med bara `conversationUrl`  Base64-kodning (d.v.s. btoa).                                                            

### <a name="start-recording"></a>Starta inspelning

#### <a name="request"></a>Förfrågan

**HTTP**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```
POST /conversations/{conversationId}/Recordings
Content-Type: application/json

{
  "operationContext": "string", // developer provided string for correlation context on each operation
  "recordingStateCallbackUri": "string"
}
```
**C#-SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// start call recording
StartRecordingResponse startRecordingResponse = await conversationClient.StartRecordingAsync(
    conversationId: "<conversation-id>"
    operationContext: "<operation-context>", /// developer provided string for correlation context on each operation
    recordingStateCallbackUri: "<recording-state-callback-uri>").ConfigureAwait(false);

string recordingId = startRecordingResponse.RecordingId;
```

#### <a name="response"></a>Svarsåtgärder

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 404 Not found
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="get-call-recording-state"></a>Hämta samtalsinspelningstillstånd

#### <a name="request"></a>Förfrågan

**HTTP**
<!-- {
  "blockType": "request",
  "name": "get-recording-state"
}-->
```http
GET /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
}
```
**C#-SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// get recording state
GetCallRecordingStateResponse recordingState = await conversationClient.GetRecordingStateAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>).ConfigureAwait(false);
```
#### <a name="response"></a>Svarsåtgärder

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingState": "active"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="stop-recording"></a>Stoppa inspelning
#### <a name="request"></a>Förfrågan
**HTTP**
<!-- {
  "blockType": "request",
  "name": "stop-recording"
}-->
```
DELETE /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C#-SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// stop recording
StopRecordingResponse response = conversationClient.StopRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Svarsåtgärder
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="pause-recording"></a>Pausa inspelning
När du pausar och återupptar en samtalsinspelning kan du hoppa över inspelningen av en del av ett samtal eller ett möte och återuppta inspelningen till en enda fil. 
#### <a name="request"></a>Förfrågan
**HTTP**
<!-- {
  "blockType": "request",
  "name": "pause-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Pause
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C#-SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// pause recording
PauseRecordingResponse response = conversationClient.PauseRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Svarsåtgärder
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="resume-recording"></a>Återuppta inspelning
#### <a name="request"></a>Förfrågan
**HTTP**
<!-- {
  "blockType": "request",
  "name": "resume-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Resume
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C#-SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// resume recording
ResumeRecordingResponse response = conversationClient.ResumeRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Svarsåtgärder
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

## <a name="media-output-types"></a>Medieutdatatyper
Samtalsinspelning stöder för närvarande mp4-utdataformat med blandat ljud och video. Utdatamediet matchar mötesinspelningar som produceras via Microsoft Teams-inspelning.

| Kanaltyp | Innehållsformat | Video | Ljud |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | mp4 | 1920x1080 8 VIDEOS-video med alla deltagare i standardpanelsordningen | 16kHz mp4a blandat ljud för alla deltagare |

## <a name="event-grid-notifications"></a>Event Grid meddelanden
Ett Event Grid-meddelande publiceras när en inspelning är redo för hämtning, vanligtvis 1–2 minuter efter att inspelningsprocessen har slutförts (t.ex. när mötet avslutats, inspelningen har `Microsoft.Communication.RecordingFileStatusUpdated` stoppats). Registrering av händelsemeddelanden omfattar ett dokument-ID som kan användas för att hämta både inspelade medier och en inspelningsmetadatafil:

- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}
- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}/metadata

Exempelkod för hantering av Event Grid-meddelanden och nedladdning av inspelnings- och metadatafiler finns [här](../../quickstarts/voice-video-calling/download-recording-file-sample.md). 

### <a name="notification-schema"></a>Meddelandeschema
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="file-download"></a>Filhämtning

> Azure Communication Services tillhandahåller kortsiktig medielagring för inspelningar. **Exportera inspelat innehåll som du vill bevara inom 48 timmar.** Efter 48 timmar är inspelningarna inte längre tillgängliga.

### <a name="download-recording"></a>Ladda ned inspelning
#### <a name="request"></a>Förfrågan
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording"
}-->
```http
GET /recording/download/{documentId}
Content-Type: application/json

{
}
```
#### <a name="response"></a>Svarsåtgärder
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```
HTTP/1.1 200 Success
Content-Type: video/mp4

{
string // Recording file bytes
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
### <a name="download-recording-metadata"></a>Ladda ned inspelningsmetadata
#### <a name="request"></a>Förfrågan
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording-metadata"
}-->
```http
GET /recording/download/{documentId}/metadata
Content-Type: application/json

{
}
```
#### <a name="response"></a>Svarsåtgärder
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "resourceId": "string",
  "callId": "string",
  "chunkDocumentId": "string",
  "chunkIndex": 0,
  "chunkStartTime": "string",
  "chunkDuration": 0,
  "pauseResumeIntervals": [
    {
      "startTime": "string",
      "duration": 0
    }
  ],
  "recordingInfo": {
    "contentType": "string",
    "channelType": "string",
    "format": "string",
    "audioConfiguration": {
      "sampleRate": "string",
      "bitRate": 0,
      "channels": 0
    },
    "videoConfiguration": {
      "longerSideLength": 0,
      "shorterSideLength": 0,
      "framerate": 0,
      "bitRate": 0
    }
  },
  "participants": [
    {
      "participantId": "string"
    }
  ]
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
