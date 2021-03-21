---
ms.openlocfilehash: 8bcef40dad9c67e9e2c6d6c4a051045999487027
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99531202"
---
När du kör medie diagrammet passerar resultatet från noden för rörelse detektorn genom IoT Hub Sink-noden till IoT Hub. De meddelanden som visas i fönstret **utdata** i Visual Studio Code innehåller ett `body` avsnitt och ett `applicationProperties` avsnitt. Mer information finns i [skapa och läsa IoT Hub meddelanden](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

I följande meddelanden definierar modulen live video analys program egenskaperna och innehållet i bröd texten.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse

När ett medie diagram instansieras försöker RTSP-Källnoden ansluta till RTSP-servern som körs i behållaren rtspsim-live555. Om anslutningen lyckas skrivs följande händelse ut.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

I föregående utdata: 

* Meddelandet är en Diagnostics-händelse `MediaSessionEstablished` . Det anger att RTSP-Källnoden (ämnet) är ansluten med RTSP-simulatorn och har börjat ta emot en (simulerad) live-feed.
* I `applicationProperties` `subject` refererar refererar noden i den diagram sto pol Ogin från vilken meddelandet genererades. I det här fallet kommer meddelandet från noden RTSP-källa.
* I `applicationProperties` `eventType` anger att den här händelsen är en diagnostisk händelse.
* `eventTime`Värdet anger den tidpunkt då händelsen inträffade.
* `body`Avsnittet innehåller information om Diagnostics-händelsen. I det här fallet innehåller informationen information om [session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="motiondetection-event"></a>MotionDetection-händelse

När motion identifieras skickar live video analys i IoT Edge-modulen en händelse för nedgradering. `type`Anges till `motion` för att indikera att det är ett resultat från processen för identifiering av rörelser. `eventTime`Värdet visar när rörelsen (i UTC) ägde rum. 

Här är ett exempel på det här meddelandet:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  } 
}  
```

I det här exemplet: 

* `eventTime`Värdet är den tidpunkt då händelsen inträffade.
* `body`Värdet är data om Analytics-händelsen. I det här fallet är händelsen en utfalls händelse, så texten innehåller `timestamp` och `inferences` data.
* `inferences`Data indikerar att `type` är `motion` . Den innehåller ytterligare information om `motion` händelsen.
* `box`Avsnittet innehåller koordinaterna för en avgränsnings ruta runt det flyttade objektet. Värdena normaliseras av videons bredd och höjd i bild punkter. Till exempel är bredden 1920 och höjden är 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
