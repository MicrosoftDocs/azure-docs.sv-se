---
title: Identifiera rörelse, spela in video till Azure Media Services
description: Den här snabb starten visar hur du använder real tids analys på IoT Edge för att identifiera rörelse i en video ström i real tid och spela in videoklipp i Azure Media Services.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 8872c9aefa0ed748cbed93d0f7376586859be9df
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511900"
---
# <a name="quickstart-detect-motion-record-video-to-media-services"></a>Snabb start: identifiera rörelse, spela in video till Media Services

Den här artikeln beskriver steg för steg hur du använder video analys i real tid för IoT Edge för [Event-baserad inspelning](event-based-video-recording-concept.md). Den använder en virtuell Linux-dator i Azure som en IoT Edge enhet och en simulerad direktuppspelad video ström. Den här video strömmen analyseras för förekomst av objekt som flyttas. När motion identifieras skickas händelser till Azure IoT Hub och relevant del av video strömmen registreras som en till gång i Azure Media Services.

Den här artikeln bygger ovanpå [komma igång snabb start](get-started-detect-motion-emit-events-quickstart.md).

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) på din dator med [Azure IoT tools-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Om du inte har slutfört [komma igång snabb](get-started-detect-motion-emit-events-quickstart.md) starten tidigare går du igenom följande steg:
    * [Ställa in Azure-resurser](get-started-detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Distribuera moduler](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)
    * [Konfigurera Visual Studio Code](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension)

## <a name="review-the-sample-video"></a>Granska exempel videon

Som en del av stegen ovan för att konfigurera Azure-resurserna kopieras en (kort) video från ett parkerings parti till den virtuella Linux-datorn i Azure som används som IoT Edge enhet. Den här video filen används för att simulera en Live-ström för den här självstudien.

Du kan använda ett program som [VLC Player](https://www.videolan.org/vlc/), starta det `Ctrl+N` och klistra in [en video exempel länk för parkerings parti](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) för att starta uppspelningen. Vid ungefär 5 sekunders märket flyttas en vit bil genom parkerings partiet.

När du har slutfört stegen nedan har du använt video analys i real tid för IoT Edge för att identifiera rörelsen i bilen och spela in ett videoklipp från och med det 5-andra märket. Diagrammet nedan är den visuella representationen av det övergripande flödet.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/topology.svg" alt-text="Event-baserad videoinspelning till till gångar baserat på rörelse händelser":::

## <a name="use-direct-method-calls"></a>Använd direkta metod anrop

Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa direkta metoder. Läs [direkta metoder för video analys på IoT Edge](direct-methods.md) för att förstå alla direkta metoder som tillhandahålls av modulen. 

1. Öppna fliken **tillägg** i Visual Studio Code (eller tryck på Ctrl + Shift + X) och Sök efter Azure-IoT Hub.
1. Högerklicka och välj **Inställningar för tillägg**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Tilläggs inställningar":::
1. Sök och aktivera "Visa utförligt meddelande".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Visa utförligt meddelande":::

### <a name="invoke-graphtopologylist"></a>Anropa GraphTopologyList
Det här steget räknar upp alla [Graph-topologier](media-graph-concept.md#media-graph-topologies-and-instances) i modulen.

1. Högerklicka på modulen "lvaEdge" och välj "anropa modul Direct metod" på snabb menyn.
1. En redigerings ruta visas i det översta mitten av Visual Studio Code-fönstret. Ange "GraphTopologyList" i redigerings rutan och tryck på RETUR.
1. Kopiera sedan och klistra in nedanstående JSON-nyttolast i redigerings rutan och tryck på RETUR.
    
```
{
    "@apiVersion" : "2.0"
}
```

Inom några sekunder visas UTDATAFÖNSTRET i Visual Studio Code-popup med följande svar
    
```
[DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```
    
Ovanstående svar förväntas eftersom inga diagram-topologier har skapats.

### <a name="invoke-graphtopologyset"></a>Anropa GraphTopologySet

Med samma steg som de som beskrivs för att anropa GraphTopologyList, kan du anropa GraphTopologySet för att ange en [graf-topologi](media-graph-concept.md#media-graph-topologies-and-instances) med följande JSON som nytto lasten. Du kommer att skapa en Graph-topologi med namnet "EVRtoAssetsOnMotionDetection".

```
{
    "@apiVersion": "2.0",
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
      "parameters": [
        {
            "name": "rtspUserName",
            "type": "String",
            "description": "rtsp source user name.",
            "default": "dummyUserName"
        },
        {
            "name": "rtspPassword",
            "type": "String",
            "description": "rtsp source password.",
            "default" : "dummyPassword"
        },
        {
            "name": "rtspUrl",
            "type": "String",
            "description": "rtsp Url"
        },
        {
            "name": "motionSensitivity",
            "type": "String",
            "description": "motion detection sensitivity",
            "default" : "medium"
        },
        {
            "name": "hubSinkOutputName",
            "type": "String",
            "description": "hub sink output name",
            "default" : "iothubsinkoutput"
        }                              
    ],         

      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "name": "motionDetection",
          "sensitivity": "${motionSensitivity}",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection"
            },
            {
              "nodeName": "rtspSource"
            }
          ],
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S"
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "name": "assetSink",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "segmentLength": "PT0M30S",
          "localMediaCacheMaximumSizeMiB": "2048",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "inputs": [
            {
              "nodeName": "signalGateProcessor"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "${hubSinkOutputName}",
          "inputs": [
            {
              "nodeName": "motionDetection"
            }
          ]
        }
      ]
    }
}
```

Ovanstående JSON-nyttolast resulterar i att en graf-topologi skapas som definierar fem parametrar (fyra av som har standardvärden). Topologin har en källnod ([RTSP-källa](media-graph-concept.md#rtsp-source)), två processorer ([motion-identifiering processor](media-graph-concept.md#motion-detection-processor) och [signal-grind](media-graph-concept.md#signal-gate-processor)och två Sink-noder (IoT Hub mottagare och [till gångs mottagare](media-graph-concept.md#asset-sink)). Den visuella representationen av topologin visas ovan.

Inom några sekunder visas följande svar i fönstret utdata.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt": "2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to assets based on motion events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default": "medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default": "iothubsinkoutput"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Statusen som returneras är 201, vilket indikerar att en ny graf-topologi har skapats. Prova följande direkta metoder som nästa steg:

* Anropa GraphTopologySet igen och kontrol lera att den returnerade status koden är 200. Status kod 200 anger att en befintlig graf-topologi har uppdaterats.
* Anropa GraphTopologySet igen men ändra beskrivnings strängen. Kontrol lera att status koden i svaret är 200 och att beskrivningen uppdateras till det nya värdet.
* Anropa GraphTopologyList enligt beskrivningen i föregående avsnitt och kontrol lera att du nu kan se diagram sto pol Ogin "EVRtoAssetsOnMotionDetection" i den returnerade nytto lasten.

### <a name="invoke-graphtopologyget"></a>Anropa GraphTopologyGet

Anropar nu GraphTopologyGet med följande nytto Last
```

{
    "@apiVersion" : "2.0",
    "name" : "EVRtoAssetsOnMotionDetection"
}
```

Inom några sekunder bör du se följande svar i fönstret utdata

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt": "2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default": "medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default": "iothubsinkoutput"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Observera följande egenskaper i nytto lasten för svar:

* Status koden är 200, vilket indikerar att det lyckades.
* Nytto lasten har skapats och tidsstämpeln "lastModified".

### <a name="invoke-graphinstanceset"></a>Anropa GraphInstanceSet

Sedan skapar du en diagram instans som hänvisar till diagram sto pol Ogin ovan. Som beskrivs [här](media-graph-concept.md#media-graph-topologies-and-instances)kan du med Graph-instanser analysera direktuppspelade video strömmar från flera kameror med samma graf-topologi.

Anropa nu metoden GraphInstanceSet Direct med följande nytto last:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2",
    "properties" : {
        "topologyName" : "EVRtoAssetsOnMotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/lots_015.mkv" }
        ]
    }
}
```

. Tänk på följande:

* I nytto lasten ovan anges grafens Topology-namn (EVRtoAssetsOnMotionDetection) som graf-instansen måste skapas för.
* Nytto lasten innehåller parametervärdet för "rtspUrl", som inte har något standardvärde i topologins nytto Last.

Inom några sekunder visas följande svar i fönstret utdata:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt": "2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Observera följande egenskaper i nytto lasten för svar:

* Status koden är 201, vilket indikerar att en ny instans skapades.
* State är "inaktiv", vilket indikerar att graf-instansen skapades men inte Aktiver ATS. Mer information finns i [Media Graph](media-graph-concept.md) -tillstånd.

Prova följande direkta metoder som nästa steg:

* Anropa GraphInstanceSet igen med samma nytto last och Observera att den returnerade status koden nu är 200.
* Anropa GraphInstanceSet igen men med en annan beskrivning och anteckna den uppdaterade beskrivningen i svarets nytto Last, vilket indikerar att graf-instansen har uppdaterats.
* Anropa GraphInstanceSet men ändra namnet till "Sample-Graph-3" och observera svarets nytto Last. Observera att en ny diagram instans skapas (dvs. status koden är 201). Kom ihåg att rensa sådana dubbla instanser när du är klar med snabb starten.

### <a name="prepare-for-monitoring-events"></a>Förbereda för övervaknings händelser

Medie diagrammet som du skapade använder noden motion-identifiering för att identifiera rörelse och sådana händelser vidarebefordras till din IoT Hub. Följ dessa steg för att förbereda för att följa dessa händelser

1. Öppna Explorer-fönstret i Visual Studio Code och leta efter Azure-IoT Hub längst ned till vänster.
1. Expandera noden enheter
1. Right-klinker på lva-Sample-Device och väljer alternativet "starta övervakning av inbyggd händelse övervakning"

    ![Starta övervakning av inbyggd händelse övervakning](./media/quickstarts/start-monitoring-iothub-events.png)
    
    Inom några sekunder visas följande meddelanden i fönstret utdata:

```
[IoTHubMonitor] Start monitoring message arrived in built-in endpoint for all devices ...
[IoTHubMonitor] Created partition receiver [0] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [1] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [2] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [3] for consumerGroup [$Default]
```

### <a name="invoke-graphinstanceactivate"></a>Anropa GraphInstanceActivate

Aktivera nu graf-instansen – som startar flödet av direktsänd video via modulen. Anropa den direkta metoden GraphInstanceActivate med följande nytto last:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Inom några sekunder bör du se följande svar i fönstret utdata

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden 200 i nytto lasten för svaret indikerar att graf-instansen har Aktiver ATS.

### <a name="invoke-graphinstanceget"></a>Anropa GraphInstanceGet

Anropa nu metoden GraphInstanceGet Direct med följande nytto last:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Inom några sekunder bör du se följande svar i fönstret utdata

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt": "2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Active",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Observera följande egenskaper i nytto lasten för svar:

* Status koden är 200, vilket indikerar att det lyckades.
* State är "aktivt", vilket indikerar att graf-instansen är i "aktivt" läge.

## <a name="observe-results"></a>Observera resultat

Graf-instansen som du skapade och aktiverade ovan använder noden motion-identifiering för att identifiera rörelse i inkommande real tids video ström och skickar händelser till IoT Hub mottagare. Dessa händelser vidarebefordras sedan till din IoT Hub, som nu kan observeras. Följande meddelanden visas i fönstret utdata

```
[IoTHubMonitor] [4:33:04 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-12T23:33:04.077Z",
    "dataVersion": "1.0"
  }
}
[IoTHubMonitor] [4:33:09 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "timestamp": 143039375044290,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.48954,
            "t": 0.140741,
            "w": 0.075,
            "h": 0.058824
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/processors/md",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-12T23:33:09.381Z",
    "dataVersion": "1.0"
  }
}
```

Observera följande egenskaper i ovanstående meddelanden

* Varje meddelande innehåller avsnittet "Body" och "applicationProperties". För att förstå vad dessa avsnitt representerar kan du läsa artikeln [skapa och läsa IoT Hub meddelande](../../iot-hub/iot-hub-devguide-messages-construct.md).
* Det första meddelandet är en diagnostisk händelse, MediaSessionEstablished, som anger att RTSP-Källnoden (subject) kunde upprätta en anslutning till RTSP-simulatorn och börja ta emot en (simulerad) live-feed.
* "Subject" i applicationProperties refererar till noden i den graf-topologi som meddelandet genererades från. I det här fallet kommer meddelandet från noden RTSP-källa.
* "eventType" i applicationProperties anger att det är en diagnostisk händelse.
* "eventTime" anger den tidpunkt då händelsen inträffade.
* "Body" innehåller data om Diagnostic-händelsen – det är [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -meddelandet.
* Det andra meddelandet är en analys händelse. Du kan kontrol lera att det skickas ungefär 5 sekunder efter MediaSessionEstablished-meddelandet, som motsvarar fördröjningen mellan videons början och när Car-enheterna passerar genom parkerings partiet.
* "Subject" i applicationProperties refererar till noden för motion-identifiering i grafen, som skapade det här meddelandet
* Händelsen är en Härlednings händelse och därför innehåller texten "timestamp" och "inferences".
* avsnittet "inferences" visar att "typ" är "rörelse" och innehåller ytterligare information om händelsen "Motion".

Nästa meddelande som visas är följande.

```
[IoTHubMonitor] [4:33:10 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-12T23:33:10.392Z",
    "dataVersion": "1.0"
  }
}
```

* Det tredje meddelandet är en drifts händelse. Du kan kontrol lera att det skickas nästan omedelbart efter meddelandet om motion-identifiering, vilket fungerar som utlösaren för att starta inspelningen.
* "Subject" i applicationProperties hänvisar till noden till gångs mottagare i grafen, som skapade det här meddelandet.
* Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i. Anteckna det här värdet – du kommer att använda det senare i snabb starten.

I topologin har signal porten processor noden kon figurer ATS med aktiverings tider på 30 sekunder, vilket innebär att graf-topologin kommer att registrera ungefär 30 sekunders video till till gången. Medan video spelas in fortsätter noden för motion-identifiering att generera utställnings händelser som visas i UTDATAFÖNSTRET. Efter en stund visas följande meddelande.

```
[IoTHubMonitor] [4:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-12T23:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

* Det här meddelandet är också en drifts händelse. Händelsen RecordingAvailable visar att tillräckligt med data har skrivits till till gången för att spelarna/klienter ska kunna starta uppspelningen av videon
* "Subject" i applicationProperties hänvisar till noden till gångs mottagare i grafen, som skapade det här meddelandet
* Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i.

Om du låter graf-instansen fortsätta att köras visas det här meddelandet.

```
[IoTHubMonitor] [4:33:40 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-12T23:33:40.014Z",
    "dataVersion": "1.0"
  }
}
```

* Det här meddelandet är också en drifts händelse. Händelsen RecordingStopped visar att inspelningen har stoppats.
* Observera att ungefär 30 sekunder har gått sedan RecordingStarted-händelsen matchar värdena för aktiverings tiderna i noden signal grind processor.
* "Subject" i applicationProperties hänvisar till noden till gångs mottagare i grafen, som skapade det här meddelandet.
* Texten innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media service-till gången som video spelas in i.

Om du låter graf-instansen fortsätta att köras, kommer RTSP-simulatorn att komma fram till slutet av video filen och stoppa/koppla från. RTSP-Källnoden återansluter sedan till simulatorn och processen upprepas.
    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>Anropa ytterligare direkta metod anrop för rensning

Starta nu direkta metoder för att inaktivera och ta bort graf-instansen (i den ordningen).

### <a name="invoke-graphinstancedeactivate"></a>Anropa GraphInstanceDeactivate

Anropa metoden GraphInstanceDeactivate Direct med följande nytto last:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Inom några sekunder bör du se följande svar i fönstret utdata.

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden 200 anger att graf-instansen har inaktiverats.

Prova följande, som nästa steg:

* Anropa GraphInstanceGet som anges i de tidigare avsnitten och observera "State"-värdet.

### <a name="invoke-graphinstancedelete"></a>Anropa GraphInstanceDelete

Anropa den direkta metoden GraphInstanceDelete med följande nytto Last

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Inom några sekunder bör du se följande svar i fönstret utdata:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden 200 i svaret indikerar att graf-instansen har tagits bort.

### <a name="invoke-graphtopologydelete"></a>Anropa GraphTopologyDelete

Anropa metoden GraphTopologyDelete Direct med följande nytto last:

```
{
    "@apiVersion" : "2.0",
    "name" : "EVRtoAssetsOnMotionDetection"
}
```

Inom några sekunder bör du se följande svar i fönstret utdata

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden 200 anger att MediaGraph-topologin har tagits bort.

Prova följande direkta metoder som nästa steg:

* Anropa GraphTopologyList och Observera att det inte finns några diagram-topologier i modulen.
* Anropa GraphInstanceList med samma nytto last som GraphTopologyList och Observera att inga graf-instanser räknas upp.

## <a name="playing-back-the-recorded-video"></a>Spela upp den inspelade videon

Sedan kan du använda Azure Portal för att spela upp den video som du har spelat in.

1. Logga in på [Azure Portal](https://portal.azure.com/), skriv "Media Services" i sökrutan.
1. Leta upp ditt Azure Media Services-konto och öppna det.
1. Leta upp och markera posten till gångar i Media Services listan.

    ![Till gångs poster i Media Servicess listan](./media/quickstarts/asset-entity.png)
1. Om den här snabb starten är din första användning av Azure Media Services visas bara de till gångar som genereras från den här snabb starten och du kan välja den äldsta.
1. Annars använder du namnet på den till gång som angavs som outputLocation i drift händelserna ovan.
1. På informations sidan som öppnas klickar du på länken "Skapa nytt" strax under text rutan för strömnings-URL.

    ![Strömnings-URL](./media/quickstarts/asset-streaming-url.png)
1. I fönstret som öppnas för "Lägg till strömmande positionerare", Godkänn standardvärdena och klicka på Lägg till längst ned.
1. På sidan till gångs information ska Videos pelaren nu laddas till den första bild rutan i videon och du kan trycka på uppspelnings knappen. Kontrol lera att du ser den del av videon där bilen rör sig i parkerings partiet.

    ![Spela](./media/quickstarts/asset-playback.png)

> [!NOTE]
> Eftersom den simulerade Live-videon startar när du aktiverar grafen, är tids periodens värden inte relevanta och visas inte via den här genvägen i Media Player. Självstudien om kontinuerlig videoinspelning och uppspelning visar hur du kan visa tidsstämplar.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort de resurser som skapats i den här snabb starten.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du anropar real tids video analys på IoT Edge [Direct-metoder](direct-methods.md) program mässigt.
* Läs mer om diagnostiska meddelanden.    
