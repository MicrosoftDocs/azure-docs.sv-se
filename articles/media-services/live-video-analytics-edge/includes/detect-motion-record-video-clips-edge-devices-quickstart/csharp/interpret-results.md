---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88682239"
---
När du kör medie diagrammet passerar resultatet från noden för rörelse detektorn genom IoT Hub Sink-noden till IoT Hub. De meddelanden som visas i fönstret **utdata** i Visual Studio Code innehåller ett `body` avsnitt och ett `applicationProperties` avsnitt. Mer information finns i [skapa och läsa IoT Hub meddelanden](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

I följande meddelanden definierar modulen live video analys program egenskaperna och innehållet i bröd texten.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse

När ett medie diagram instansieras försöker RTSP-Källnoden ansluta till RTSP-servern som körs i behållaren rtspsim-live555. Om anslutningen lyckas skrivs följande händelse ut.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

I föregående utdata: 

* Meddelandet är en Diagnostics-händelse `MediaSessionEstablished` . Det anger att noden RTSP-källa (ämnet) upprättade en anslutning till RTSP-simulatorn och har börjat ta emot en (simulerad) live-feed.
* I `applicationProperties` `subject` refererar den nod i den graf-topologi från vilken meddelandet genererades. I det här fallet kommer meddelandet från noden RTSP-källa.
* I `applicationProperties` `eventType` anger att den här händelsen är en diagnostisk händelse.
* `eventTime`Värdet är den tidpunkt då händelsen inträffade.
* `body`Avsnittet innehåller information om Diagnostics-händelsen. I det här fallet innehåller informationen information om [session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="recordingstarted-event"></a>RecordingStarted-händelse

När motion identifieras aktive ras noden signal grind processor och noden fil mottagare i medie diagrammet börjar skriva en MP4-fil. Filen Sink-noden skickar en drifts händelse. `type`Anges till `motion` för att indikera att det är ett resultat från processen för identifiering av rörelser. `eventTime`Värdet är den UTC-tid då rörelsen ägde rum. Mer information om den här processen finns i avsnittet [Översikt](#overview) i den här snabb starten.

Här är ett exempel på det här meddelandet:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

I föregående meddelande: 

* I `applicationProperties` `subject` refererar den nod i medie diagrammet som meddelandet genererades från. I det här fallet kommer meddelandet från noden fil mottagare.
* I `applicationProperties` , `eventType` anger att den här händelsen fungerar.
* `eventTime`Värdet är den tidpunkt då händelsen inträffade. Den här tiden är 5 till 6 sekunder efter `MediaSessionEstablished` och efter att videon har börjat flöda. Den här tiden motsvarar den 5-till-6-andra markeringen när [bilen började flytta](#review-the-sample-video) till parkerings partiet.
* `body`Avsnittet innehåller information om drift händelsen. I det här fallet består data `outputType` och `outputLocation` .
* `outputType`Variabeln anger att den här informationen avser fil Sök vägen.
* `outputLocation`Värdet är platsen för MP4-filen i Edge-modulen.

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped-och RecordingAvailable-händelser

Om du undersöker egenskaperna för noden signal grind processor i [graf-topologin](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json)ser du att aktiverings tiderna är inställda på 5 sekunder. Så cirka 5 sekunder efter att `RecordingStarted` händelsen har tagits emot får du:

* En `RecordingStopped` händelse som anger att inspelningen har stoppats.
* En `RecordingAvailable` händelse som anger att MP4-filen nu kan användas för visning.

De två händelserna genereras vanligt vis inom några sekunder.
