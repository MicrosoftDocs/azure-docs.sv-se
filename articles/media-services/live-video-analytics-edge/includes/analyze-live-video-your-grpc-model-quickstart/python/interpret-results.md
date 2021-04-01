---
ms.openlocfilehash: fdc7360911e37babdb830b7d67ad7224ea84c774
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92038637"
---
När du kör medie diagrammet passerar resultatet från noden för HTTP-tillägget processor genom noden IoT Hub mottagare till IoT Hub. De meddelanden som visas i fönstret **utdata** innehåller ett text avsnitt och ett applicationProperties-avsnitt. Mer information finns i [skapa och läsa IoT Hub meddelanden](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

I följande meddelanden definierar modulen live video analys program egenskaperna och innehållet i bröd texten.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse

När ett medie diagram instansieras försöker RTSP-Källnoden ansluta till RTSP-servern som körs i behållaren rtspsim-live555. Om anslutningen lyckas skrivs följande händelse ut. Händelse typen är `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished` .

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

Lägg märke till följande information i det här meddelandet:
* Meddelandet är en diagnostisk händelse. `MediaSessionEstablished` anger att RTSP-Källnoden (ämnet) är ansluten med RTSP-simulatorn och har börjat ta emot en (simulerad) live-feed.
* I `applicationProperties` anger ämnet att meddelandet genererades från NODEN RTSP-källa i medie diagrammet.
* I `applicationProperties` `eventType` anger att den här händelsen är en diagnostisk händelse.
* `eventTime`Anger den tidpunkt då händelsen inträffade.
* Texten innehåller information om Diagnostics-händelsen. I det här fallet innehåller informationen information om [session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Händelsen härledning

GRPC Extension processor-noden tar emot härlednings resultat från lvaExtension-modulen. Den genererar sedan resultaten genom IoT Hub Sink-noden som utöknings händelser.
I dessa händelser anges typen till entitet för att ange att den är en enhet, till exempel en bil eller en Last bil. `eventTime`Värdet är UTC-tiden då objektet identifierades.
I följande exempel upptäcktes tre bilar i samma video RAM, med varierande nivåer av förtroende.

```
[IoTHubMonitor] [7:52:57 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "timestamp": 143802500954716,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.86707145
          },
          "attributes": [],
          "box": {
            "l": 0.7294476,
            "t": 0.567829,
            "w": 0.031738576,
            "h": 0.027762715
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.8634398
          },
          "attributes": [],
          "box": {
            "l": 0.4765757,
            "t": 0.59559196,
            "w": 0.05597749,
            "h": 0.048316106
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.67120105
          },
          "attributes": [],
          "box": {
            "l": 0.7247387,
            "t": 0.49816906,
            "w": 0.032748587,
            "h": 0.030686663
          }
        },
        "extensions": {},
        "valueCase": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/grpcExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-19T02:52:57.174Z",
    "dataVersion": "1.0"
  }
}
```

Observera följande information i meddelandena:

* I `applicationProperties` refererar ämnet till noden i den Graph-topologi från vilken meddelandet genererades.
* I `applicationProperties` anger eventType att händelsen är en analys händelse.
* `eventTime`Värdet är den tidpunkt då händelsen inträffade.
* `body`Avsnittet innehåller information om Analytics-händelsen. I det här fallet är händelsen en utfalls händelse, så texten innehåller inferences data.
* `inferences`Avsnittet visar att typen är entitet. Det här avsnittet innehåller ytterligare information om entiteten.