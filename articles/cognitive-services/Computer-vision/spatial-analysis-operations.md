---
title: Åtgärder för rumslig analys
titleSuffix: Azure Cognitive Services
description: Åtgärderna för rumslig analys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 37ac7573a1794c97c81fe5364204f85ff14d9fa6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538087"
---
# <a name="spatial-analysis-operations"></a>Åtgärder för rumslig analys

Rumslig analys möjliggör analys av strömmande video i realtid från kameraenheter. För varje kameraenhet som du konfigurerar genererar åtgärderna för rumslig analys en utdataström med JSON-meddelanden som skickas till din instans av Azure IoT Hub. 

Containern spatial analys implementerar följande åtgärder:

| Åtgärdsidentifierare| Description|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | Räknar personer i en angiven zon i kamerans visningsfält. Zonen måste vara helt täckt av en enda kamera för att PersonCount ska kunna registrera en korrekt totalsumma. <br> Sändar en _första personCountEvent-händelse_ och _sedan personCountEvent-händelser_ när antalet ändras.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | Spårar när en person passerar en angiven linje i kamerans visningsfält. <br>Sändar _en personLineEvent-händelse_ när personen går över linjen och ger riktningsinformation. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | Sändar en _personZoneEnterExitEvent-händelse_ när en person kommer in i eller lämnar zonen och ger riktningsinformation med numrerad sida av zonen som korsades. Skickar ut _en personZoneDwellTimeEvent_ när personen lämnar zonen och ger riktningsinformation samt antalet millisekunder som personen spenderade i zonen. |
| cognitiveservices.vision.spatialanalysis-persondistance | Spårar när personer bryter mot en avståndsregel. <br> Skickar regelbundet en _personDistanceEvent_ med platsen för varje avståndsöverträdelse. |
| cognitiveservices.vision.spatialanalysis | Allmän åtgärd som kan användas för att köra alla scenarier som nämns ovan. Det här alternativet är mer användbart när du vill köra flera scenarier på samma kamera eller använda systemresurser (t.ex. GPU) mer effektivt. |

Alla ovanstående åtgärder är också tillgängliga i -versionen, som har möjlighet att `.debug` visualisera bildrutorna när de bearbetas. Du måste köra på `xhost +` värddatorn för att aktivera visualisering av bildrutor och händelser.

| Åtgärdsidentifierare| Description|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | Räknar personer i en angiven zon i kamerans visningsfält. <br> Sändar en _första personCountEvent-händelse_ och _sedan personCountEvent-händelser_ när antalet ändras.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | Spårar när en person passerar en angiven linje i kamerans visningsfält. <br>Sändar _en personLineEvent-händelse_ när personen går över linjen och ger riktningsinformation. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | Sändar en _personZoneEnterExitEvent-händelse_ när en person kommer in i eller lämnar zonen och ger riktningsinformation med numrerad sida av zonen som korsades. Skickar ut _en personZoneDwellTimeEvent_ när personen lämnar zonen och ger riktningsinformation samt antalet millisekunder som personen spenderade i zonen. |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | Spårar när personer bryter mot en avståndsregel. <br> Skickar regelbundet en _personDistanceEvent_ med platsen för varje avståndsöverträdelse. |
| cognitiveservices.vision.spatialanalysis.debug | Allmän åtgärd som kan användas för att köra alla scenarier som nämns ovan. Det här alternativet är mer användbart när du vill köra flera scenarier på samma kamera eller använda systemresurser (t.ex. GPU) mer effektivt. |

Rumslig analys kan också köras med [Live Video Analytics](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) som video-AI-modul. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Åtgärdsidentifierare| Description|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | Räknar personer i en angiven zon i kamerans visningsfält. <br> Sändar en _första personCountEvent-händelse_ och _sedan personCountEvent-händelser_ när antalet ändras.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | Spårar när en person passerar en angiven linje i kamerans visningsfält. <br>Sändar _en personLineEvent-händelse_ när personen går över linjen och ger riktningsinformation. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | Sändar en _personZoneEnterExitEvent-händelse_ när en person kommer in i eller lämnar zonen och ger riktningsinformation med numrerad sida av zonen som korsades. Skickar ut _en personZoneDwellTimeEvent_ när personen lämnar zonen och ger riktningsinformation samt antalet millisekunder som personen spenderade i zonen.  |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | Spårar när personer bryter mot en avståndsregel. <br> Skickar regelbundet en _personDistanceEvent_ med platsen för varje avståndsöverträdelse. |
| cognitiveservices.vision.spatialanalysis.livevideoanalytics | Allmän åtgärd som kan användas för att köra alla scenarier som nämns ovan. Det här alternativet är mer användbart när du vill köra flera scenarier på samma kamera eller använda systemresurser (t.ex. GPU) mer effektivt. |

Live Video Analytics-åtgärder är också tillgängliga i versionen `.debug` (t.ex. cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug) som har möjlighet att visualisera bildrutorna som bearbetade. Du måste köra på `xhost +` värddatorn för att aktivera visualiseringen av bildrutor och händelser

> [!IMPORTANT]
> AI-modeller för datorseende identifierar och hittar mänsklig närvaro i videosekvenser och -utdata med hjälp av en avgränsare runt en människokropp. AI-modellerna försöker inte identifiera identiteter eller demografi för individer.

Det här är de parametrar som krävs för var och en av dessa åtgärder för rumslig analys.

| Åtgärdsparametrar| Description|
|---------|---------|
| Åtgärds-ID | Åtgärdsidentifieraren från tabellen ovan.|
| enabled | Booleskt: sant eller falskt|
| VIDEO_URL| RTSP-URL:en för kameraenheten (exempel: `rtsp://username:password@url` ). Rumslig analys stöder H.264-kodad dataström via RTSP, http eller mp4. Video_URL kan anges som ett fördrövat base64-strängvärde med hjälp av AES-kryptering, och om video-URL:en är fördedd så måste den anges som `KEY_ENV` `IV_ENV` miljövariabler. Exempelverktyg för att generera nycklar och kryptering finns [här](/dotnet/api/system.security.cryptography.aesmanaged). |
| VIDEO_SOURCE_ID | Ett eget namn för kameraenheten eller videoströmmen. Detta returneras med händelsens JSON-utdata.|
| VIDEO_IS_LIVE| Sant för kameraenheter; falskt för inspelade videor.|
| VIDEO_DECODE_GPU_INDEX| Vilken GPU som ska avkodas för videoramen. Som standard är det 0. Bör vara samma som i `gpu_index` andra nodkonfigurationer som `VICA_NODE_CONFIG` , `DETECTOR_NODE_CONFIG` .|
| INPUT_VIDEO_WIDTH | Indatavideons/strömmens rambredd (t.ex. 1920). Det här är ett valfritt fält, och om det anges skalas ramen till den här dimensionen samtidigt som proportionerna bevaras.|
| DETECTOR_NODE_CONFIG | JSON som anger vilken GPU som detektornoden ska köras på. Det bör ha följande format: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | JSON-konfiguration för zon och rad enligt beskrivningen nedan.|
| ENABLE_FACE_MASK_CLASSIFIER | `True` för att aktivera identifiering av personer som bär ansiktsmasker i videoströmmen, `False` för att inaktivera det. Som standard är detta inaktiverat. Ansiktsmaskidentifiering kräver att parametern för videobredd för indata är 1920 `"INPUT_VIDEO_WIDTH": 1920` . Ansiktsmaskattributet returneras inte om identifierade personer inte är riktade mot kameran eller är för långt från den. Mer information finns [i guiden för](spatial-analysis-camera-placement.md) kameraplacering |

### <a name="detector-node-parameter-settings"></a>Inställningar för detektornodparameter
Det här är ett exempel på DETECTOR_NODE_CONFIG för alla åtgärder för rumslig analys.

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000
}
```

| Namn | Typ| Description|
|---------|---------|---------|
| `gpu_index` | sträng| GPU-indexet som åtgärden ska köras på.|
| `do_calibration` | sträng | Anger att kalibrering är aktiverat. `do_calibration` måste vara sant för **att cognitiveservices.vision.spatialanalysis-persondistance** ska fungera korrekt. do_calibration är inställt på Sant som standard. |
| `enable_recalibration` | boolesk | Anger om automatisk omcalibrering är aktiverat. Standardvärdet är `true`.|
| `calibration_quality_check_frequency_seconds` | int | Minsta antal sekunder mellan varje kvalitetskontroll för att avgöra om om en omcalibrering krävs eller inte. Standardvärdet `86400` är (24 timmar). Används endast när `enable_recalibration=True` .|
| `calibration_quality_check_sample_collect_frequency_seconds` | int | Minsta antal sekunder mellan insamling av nya dataexempel för omcalibrering och kvalitetskontroll. Standardvärdet `300` är (5 minuter). Används endast när `enable_recalibration=True` .|
| `calibration_quality_check_one_round_sample_collect_num` | int | Minsta antal nya dataexempel som ska samlas in per omgång av exempelinsamling. Standardvärdet är `10`. Används endast när `enable_recalibration=True` .|
| `calibration_quality_check_queue_max_size` | int | Maximalt antal dataexempel som ska lagras när kameramodellen kalibreras. Standardvärdet är `1000`. Används endast när `enable_recalibration=True` .|
| `enable_breakpad`| boolesk | Anger om du vill aktivera breakpad, som används för att generera kraschdump för felsökning. Det är `false` som standard. Om du ställer in den `true` på måste du också lägga till i delen av `"CapAdd": ["SYS_PTRACE"]` `HostConfig` containern `createOptions` . Som standard laddas kraschdumpen upp till [Appen RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter. Om du vill att kraschdumpar ska laddas upp till din egen AppCenter-app kan du åsidosätta miljövariabeln med `RTPT_APPCENTER_APP_SECRET` appens apphemlighet.

## <a name="spatial-analysis-operations-configuration-and-output"></a>Konfiguration och utdata för åtgärder för rumslig analys
### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Zonkonfiguration för cognitiveservices.vision.spatialanalysis-personcount

 Det här är ett exempel på JSON-indata för SPACEANALYTICS_CONFIG som konfigurerar en zon. Du kan konfigurera flera zoner för den här åtgärden.

```json
{
"zones":[{
       "name": "lobbycamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
              "type": "count",
              "config":{
                     "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
       }]
}
```

| Namn | Typ| Description|
|---------|---------|---------|
| `zones` | lista| Lista över zoner. |
| `name` | sträng| Eget namn för den här zonen.|
| `polygon` | lista| Varje värdepar representerar x,y för hörn i en polygon. Polygonen representerar de områden där personer spåras eller räknas och polygonpunkter baseras på normaliserade koordinater (0–1), där det övre vänstra hörnet är (0,0, 0,0) och det nedre högra hörnet är (1.0, 1.0).   
| `threshold` | flyt| Händelser utgående när personen är större än det här antalet bildpunkter i zonen. |
| `type` | sträng| För **cognitiveservices.vision.spatialanalysis-personcount** ska detta vara `count` .|
| `trigger` | sträng| Typ av utlösare för att skicka en händelse. Värden som stöds är för att skicka händelser när antalet ändras eller för att skicka händelser med `event` `interval` jämna mellanrum, oavsett om antalet har ändrats eller inte.
| `output_frequency` | int | Den hastighet med vilken händelser utgående. När `output_frequency` = X, utgående varje X-händelse, t.ex. `output_frequency` = 2 innebär att alla andra händelser är utdata. `output_frequency`gäller för både och `event` `interval` . |
| `focus` | sträng| Punktplatsen i personens begränsningsruta som används för att beräkna händelser. Fokusvärdet kan vara (fotavtrycket för personen), (det nedre mitten av personens `footprint` `bottom_center` begränsningsruta) (mitten av `center` personens begränsningsruta).|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Linjekonfiguration för cognitiveservices.vision.spatialanalysis-personcrossingline

Det här är ett exempel på JSON-indata för SPACEANALYTICS_CONFIG som konfigurerar en rad. Du kan konfigurera flera korsande linjer för den här åtgärden.

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| Namn | Typ| Description|
|---------|---------|---------|
| `lines` | lista| Lista över rader.|
| `name` | sträng| Eget namn för den här raden.|
| `line` | lista| Definitionen av raden. Det här är en riktningslinje som gör att du kan förstå "entry" kontra "exit".|
| `start` | värdepar| x, y-koordinater för linjens startpunkt. Flyttalsvärdena representerar positionen för hörnet i förhållande till det övre vänstra hörnet. Om du vill beräkna de absoluta x- och y-värdena multiplicerar du dessa värden med ramstorleken. |
| `end` | värdepar| x, y-koordinater för linjens slutpunkt. Flyttalsvärdena representerar positionen för hörnet i förhållande till det övre vänstra hörnet. Om du vill beräkna de absoluta x- och y-värdena multiplicerar du dessa värden med ramstorleken. |
| `threshold` | flyt| Händelser utgående när personen är större än det här antalet bildpunkter i zonen. Standardvärdet är 16. Det här är det rekommenderade värdet för att uppnå maximal noggrannhet. |
| `type` | sträng| För **cognitiveservices.vision.spatialanalysis-personcrossingline** bör detta vara `linecrossing` .|
|`trigger`|sträng|Typ av utlösare för att skicka en händelse.<br>Värden som stöds: "event": fire when someone crosses the line.|
| `focus` | sträng| Punktplatsen i personens begränsningsruta som används för att beräkna händelser. Fokusvärdet kan vara (fotavtrycket för personen), (det nedre mitten av personens `footprint` `bottom_center` begränsningsruta) (mitten av `center` personens begränsningsruta). Standardvärdet är fotavtryck.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Zonkonfiguration för cognitiveservices.vision.spatialanalysis-personcrossingpolygon

Det här är ett exempel på JSON-indata för SPACEANALYTICS_CONFIG som konfigurerar en zon. Du kan konfigurera flera zoner för den här åtgärden.

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| Namn | Typ| Description|
|---------|---------|---------|
| `zones` | lista| Lista över zoner. |
| `name` | sträng| Eget namn för den här zonen.|
| `polygon` | lista| Varje värdepar representerar x,y för polygonhörn. Polygonen representerar de områden där personer spåras eller räknas. Flyttalsvärdena representerar positionen för hörnet i förhållande till det övre vänstra hörnet. Om du vill beräkna de absoluta x- och y-värdena multiplicerar du dessa värden med ramstorleken. 
| `threshold` | flyt| Händelser utgående när personen är större än det här antalet bildpunkter i zonen. Standardvärdet är 48 när typen är zonecrossing och 16 när tid är Förtid. Det här är de rekommenderade värdena för att uppnå maximal noggrannhet.  |
| `type` | sträng| För **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** bör detta vara `zonecrossing` eller `zonedwelltime` .|
| `trigger`|sträng|Typ av utlösare för att skicka en händelse<br>Värden som stöds: "event": fire when someone enters or exits the zone.: fire when someone enters or exits the zone.|
| `focus` | sträng| Punktplatsen i personens begränsningsruta som används för att beräkna händelser. Fokusvärdet kan vara (fotavtrycket för personen), (det nedre mitten av personens `footprint` `bottom_center` begränsningsruta) (mitten av `center` personens begränsningsruta). Standardvärdet är fotavtryck.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Zonkonfiguration för cognitiveservices.vision.spatialanalysis-persondistance

Det här är ett exempel på en JSON-indata för SPACEANALYTICS_CONFIG-parametern som konfigurerar en zon för **cognitiveservices.vision.spatialanalysis-persondistance**. Du kan konfigurera flera zoner för den här åtgärden.

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
       "type": "persondistance",
       "config":{
           "trigger": "event",
           "output_frequency":1,
           "minimum_distance_threshold":6.0,
           "maximum_distance_threshold":35.0,
        "aggregation_method": "average"
           "threshold": 16.00,
           "focus": "footprint"
                   }
          }]
   }]
}
```

| Namn | Typ| Description|
|---------|---------|---------|
| `zones` | lista| Lista över zoner. |
| `name` | sträng| Eget namn för den här zonen.|
| `polygon` | lista| Varje värdepar representerar x,y för polygonhörn. Polygonen representerar de områden där människor räknas och avståndet mellan människor mäts. Flyttalsvärdena representerar positionen för brytpunkt i förhållande till det övre vänstra hörnet. Om du vill beräkna de absoluta x- och y-värdena multiplicerar du dessa värden med ramstorleken. 
| `threshold` | flyt| Händelser utgående när personen är större än det här antalet bildpunkter i zonen. |
| `type` | sträng| För **cognitiveservices.vision.spatialanalysis-persondistance** ska detta vara `people_distance` .|
| `trigger` | sträng| Typ av utlösare för att skicka en händelse. Värden som stöds är för att skicka händelser när antalet ändras eller för att `event` `interval` skicka händelser regelbundet, oavsett om antalet har ändrats eller inte.
| `output_frequency` | int | Den hastighet med vilken händelser utgående. När `output_frequency` = X, är varje X-händelse utgående, t.ex. `output_frequency` = 2 innebär att alla andra händelser är utdata. `output_frequency`gäller för både och `event` `interval` .|
| `minimum_distance_threshold` | flyt| Ett avstånd i fot som utlöser en "TooClose"-händelse när personer är mindre än avståndet ifrån varandra.|
| `maximum_distance_threshold` | flyt| Ett avstånd i fot som utlöser en "TooFar"-händelse när personer är större än avståndet.|
| `aggregation_method` | sträng| Metoden för aggregerat persondistance-resultat. Den aggregation_method gäller för både `mode` och `average` .|
| `focus` | sträng| Punktplatsen i personens begränsningsruta som används för att beräkna händelser. Fokusvärdet kan vara (personens fotavtryck) (längst ned i mitten av personens `footprint` `bottom_center` begränsningsruta) (mitten av `center` personens begränsningsruta).|

### <a name="configuration-for-cognitiveservicesvisionspatialanalysis"></a>Konfiguration för cognitiveservices.vision.spatialanalysis
Det här är ett exempel på JSON-indata för SPACEANALYTICS_CONFIG som konfigurerar en linje och zon för **cognitiveservices.vision.spatialanalysis**. Du kan konfigurera flera rader/zoner för den här åtgärden och varje rad/zon kan ha olika händelser.

 ```
{
  "lines": [
    {
      "name": "doorcamera",
      "line": {
        "start": {
          "x": 0,
          "y": 0.5
        },
        "end": {
          "x": 1,
          "y": 0.5
        }
      },
      "events": [
        {
          "type": "linecrossing",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ],
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3, 0.3],[0.3, 0.9],[0.6, 0.9],[0.6, 0.3],[0.3, 0.3]],
      "events": [
        {
          "type": "persondistance",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "minimum_distance_threshold": 6.0,
            "maximum_distance_threshold": 35.0,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonecrossing",
          "config": {
            "threshold": 48.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonedwelltime",
          "config": {
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```
## <a name="camera-configuration"></a>Kamerakonfiguration

I riktlinjerna [för kameraplacering](spatial-analysis-camera-placement.md) kan du läsa mer om hur du konfigurerar zoner och linjer.

## <a name="spatial-analysis-operation-output"></a>Utdata för rumslig analysåtgärd

Händelserna från varje åtgärd utgående till Azure IoT Hub JSON-format.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>JSON-format för cognitiveservices.vision.spatialanalysis-personcount Alla insikter

Exempel på JSON för en händelseutdata med den här åtgärden.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadata":{
            "attributes": {
            "face_nomask": 0.99
            }
            }
       }
    ],
    "schemaVersion": "1.0"
}
```

| Namn på händelsefält | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Händelse-ID|
| `type` | sträng| Händelsetyp|
| `detectionsId` | matris| Matris med storlek 1 med unik identifierare för den personidentifiering som utlöste händelsen|
| `properties` | samling| Samling med värden|
| `trackinId` | sträng| Unik identifierare för den person som har identifierats|
| `zone` | sträng | Fältet "namn" för polygonen som representerar den zon som korsades|
| `trigger` | sträng| Utlösartypen är "event" eller "interval" beroende på värdet för `trigger` i SPACEANALYTICS_CONFIG|

| Namn på identifieringsfält | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Identifierings-ID|
| `type` | sträng| Identifieringstyp|
| `region` | samling| Samling med värden|
| `type` | sträng| Typ av region|
| `points` | samling| Övre vänstra och nedre högra hörnet när regiontypen är REKTANGEL |
| `confidence` | flyt| Algoritmförtroende|
| `face_mask` | flyt | Attributets konfidensvärde med intervall (0–1) anger att den identifierade personen använder en ansiktsmask |
| `face_nomask` | flyt | Förtroendevärdet för attributet med intervallet (0–1) anger att den identifierade personen inte **använder** en ansiktsmask |

| Fältnamn för SourceInfo | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Kamera-ID|
| `timestamp` | date| UTC-datum då JSON-nyttolasten släpptes|
| `width` | int | Bredd på videoram|
| `height` | int | Höjd på videoram|
| `frameId` | int | Bildruteidentifierare|
| `cameraCallibrationInfo` | samling | Samling värden|
| `status` | sträng | Status för kalibreringen i formatet `state[;progress description]` . Tillståndet kan vara `Calibrating` , `Recalibrating` (om omcalibration är aktiverat) eller `Calibrated` . Förloppsbeskrivningsdelen är endast giltig när den är i och `Calibrating` `Recalibrating` -tillstånd, som används för att visa förloppet för den aktuella kalibreringsprocessen.|
| `cameraHeight` | flyt | Kamerans höjd över marken i fot. Detta här härledas från automatisk kalibrering. |
| `focalLength` | flyt | Kamerans fokuslängd i bildpunkter. Detta här härledas från automatisk kalibrering. |
| `tiltUpAngle` | flyt | Kamerans lutningsvinkel från vertikal. Detta här härledas från automatisk kalibrering.|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>JSON-format för cognitiveservices.vision.spatialanalysis-personcrossingline Alla insikter

JSON-exempel för identifieringar av utdata med den här åtgärden.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| Namn på händelsefält | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Händelse-ID|
| `type` | sträng| Händelsetyp|
| `detectionsId` | matris| Matris med storlek 1 med unik identifierare för den personidentifiering som utlöste händelsen|
| `properties` | samling| Samling med värden|
| `trackinId` | sträng| Unik identifierare för den person som har identifierats|
| `status` | sträng| Riktning för linjekorsningar, antingen "CrossLeft" eller "CrossRight". Riktningen baseras på att du föreställer dig att den står vid "start"-radens "slut". CrossRight går från vänster till höger. CrossLeft passerar från höger till vänster.|
| `zone` | sträng | Fältet "namn" på den rad som korsades|

| Namn på identifieringsfält | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Identifierings-ID|
| `type` | sträng| Identifieringstyp|
| `region` | samling| Samling med värden|
| `type` | sträng| Typ av region|
| `points` | samling| Övre vänstra och nedre högra hörnet när regiontypen är REKTANGEL |
| `confidence` | flyt| Algoritmförtroende|
| `face_mask` | flyt | Attributets konfidensvärde med intervallet (0–1) anger att den identifierade personen använder en ansiktsmask |
| `face_nomask` | flyt | Attributets konfidensvärde med intervallet (0–1) anger att den identifierade personen inte **använder en** ansiktsmask |

| SourceInfo-fältnamn | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Kamera-ID|
| `timestamp` | date| UTC-datum då JSON-nyttolasten släpptes|
| `width` | int | Bredd på videoram|
| `height` | int | Höjd på videoram|
| `frameId` | int | Bildruteidentifierare|


> [!IMPORTANT]
> AI-modellen identifierar en person oavsett om personen är riktad mot eller bort från kameran. AI-modellen kör inte ansiktsigenkänning och ger inte någon biometrisk information. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>JSON-format för cognitiveservices.vision.spatialanalysis-personcrossingpolygon Alla insikter

JSON-exempel för identifieringar av utdata från den här `zonecrossing` åtgärden med typen SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
        "metadata": {
        "attributes": {
        "face_mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

JSON-exempel för identifieringar av utdata från den här `zonedwelltime` åtgärden med typen SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
              "durationMs": 7132.0
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Namn på händelsefält | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Händelse-ID|
| `type` | sträng| Händelsetyp. Värdet kan vara antingen _personZoneDwellTimeEvent_ eller _personZoneEnterExitEvent_|
| `detectionsId` | matris| Matris med storlek 1 med unik identifierare för den personidentifiering som utlöste händelsen|
| `properties` | samling| Samling med värden|
| `trackinId` | sträng| Unik identifierare för den person som har identifierats|
| `status` | sträng| Riktning för polygonövergångar, antingen Enter eller Exit|
| `side` | int| Antalet på sidan av polygonen som personen korsade. Varje sida är en numrerad kant mellan de två hörnen i polygonen som representerar din zon. Kanten mellan de två första hörnen i polygonen representerar den första sidan. "Sida" är tom när händelsen inte är associerad med en specifik sida på grund av ocklusion. Till exempel inträffade en avslut när en person försvinner men inte sågs korsa en sida av zonen eller när en person uppstod i zonen men inte sågs korsa en sida.|
| `durationMs` | flyt | Antalet millisekunder som representerar den tid som personen spenderade i zonen. Det här fältet anges när händelsetypen är _personZoneDwellTimeEvent_|
| `zone` | sträng | Fältet "namn" för polygonen som representerar den zon som korsades|

| Fältnamn för identifieringar | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Identifierings-ID|
| `type` | sträng| Identifieringstyp|
| `region` | samling| Samling värden|
| `type` | sträng| Typ av region|
| `points` | samling| Övre vänstra och nedre högra punkter när regiontypen är REKTANGEL |
| `confidence` | flyt| Algoritmförtroende|
| `face_mask` | flyt | Attributets konfidensvärde med intervall (0–1) anger att den identifierade personen använder en ansiktsmask |
| `face_nomask` | flyt | Attributets konfidensvärde med intervall (0–1) anger att den identifierade personen inte **använder** en ansiktsmask |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>JSON-format för cognitiveservices.vision.spatialanalysis-persondistance Alla insikter

JSON-exempel för identifieringar av utdata med den här åtgärden.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Calibrated",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Namn på händelsefält | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Händelse-ID|
| `type` | sträng| Händelsetyp|
| `detectionsId` | matris| Matris med storlek 1 med unik identifierare för den personidentifiering som utlöste händelsen|
| `properties` | samling| Samling värden|
| `personCount` | int| Antal personer som identifierades när händelsen utges|
| `averageDistance` | flyt| Det genomsnittliga avståndet mellan alla identifierade personer i fot|
| `minimumDistanceThreshold` | flyt| Avståndet i fot som utlöser en "TooClose"-händelse när personer är mindre än avståndet.|
| `maximumDistanceThreshold` | flyt| Avståndet i fot som utlöser en "TooFar"-händelse när personer är större än avståndet.|
| `eventName` | sträng| Händelsenamnet `TooClose` är med `minimumDistanceThreshold` den som har brutits, `TooFar` när den `maximumDistanceThreshold` överträds eller `unknown` när automatisk kalibrering inte har slutförts|
| `distanceViolationPersonCount` | int| Antal personer som har identifierats i brott mot `minimumDistanceThreshold` eller `maximumDistanceThreshold`|
| `zone` | sträng | Fältet "namn" för den polygon som representerar zonen som övervakades för att ta avstånd mellan personer|
| `trigger` | sträng| Utlösartypen är "event" eller "interval" beroende på värdet för `trigger` i SPACEANALYTICS_CONFIG|

| Namn på identifieringsfält | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Identifierings-ID|
| `type` | sträng| Identifieringstyp|
| `region` | samling| Samling med värden|
| `type` | sträng| Typ av region|
| `points` | samling| Övre vänstra och nedre högra hörnet när regiontypen är REKTANGEL |
| `confidence` | flyt| Algoritmförtroende|
| `centerGroundPoint` | 2 flyttalsvärden| `x`, `y` värden med koordinaterna för personens härdade plats på marken i fot. `x` och `y` är koordinaterna på golvplanet, förutsatt att golvnivån är nivå. Kamerans plats är ursprunget. |

När du beräknar är avståndet från kameran till personen längs en linje som är formad till `centerGroundPoint` `x` kamerans bildplan. `y` är avståndet från kameran till personen längs en linje parallellt med kamerans bildplan. 

![Exempel på mittpunkt](./media/spatial-analysis/x-y-chart.png) 

I det här exemplet `centerGroundPoint` är `{x: 4, y: 5}` . Det innebär att det finns en person 4 meter från kameran och 5 fot till höger som tittar på rummet uppifrån och ned.


| SourceInfo-fältnamn | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Kamera-ID|
| `timestamp` | date| UTC-datum då JSON-nyttolasten släpptes|
| `width` | int | Bredd på videoram|
| `height` | int | Höjd på videoram|
| `frameId` | int | Bildruteidentifierare|
| `cameraCallibrationInfo` | samling | Samling med värden|
| `status` | sträng | Status för kalibreringen i formatet `state[;progress description]` . Tillståndet kan vara `Calibrating` , `Recalibrating` (om omcalibration är aktiverat) eller `Calibrated` . Förloppsbeskrivningsdelen är endast giltig när den är i tillståndet och , som `Calibrating` används för att visa `Recalibrating` förloppet för den aktuella kalibreringsprocessen.|
| `cameraHeight` | flyt | Höjden på kameran ovanför marken i fot. Detta här härledas från automatisk kalibrering. |
| `focalLength` | flyt | Kamerans fokuslängd i bildpunkter. Detta här härledas från automatisk kalibrering. |
| `tiltUpAngle` | flyt | Kamerans lutningsvinkel från vertikal. Detta här härledas från automatisk kalibrering.|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-ai-insights"></a>JSON-format för cognitiveservices.vision.spatialanalysis Alla insikter

Utdata för den här åtgärden beror på konfigurerad , till exempel om det finns en händelse som konfigurerats för den här åtgärden kommer utdata `events` att vara samma som `zonecrossing` `cognitiveservices.vision.spatialanalysis-personcrossingpolygon` .

## <a name="use-the-output-generated-by-the-container"></a>Använd de utdata som genererats av containern

Du kanske vill integrera identifiering eller händelser för rumslig analys i ditt program. Här är några metoder att överväga: 

* Använd Azure Event Hub SDK för ditt valda programmeringsspråk för att ansluta Azure IoT Hub slutpunkten och ta emot händelserna. Mer information [finns i Läsa meddelanden från enhet till moln från den inbyggda](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) slutpunkten. 
* Konfigurera **Meddelanderoutning** på din Azure IoT Hub att skicka händelserna till andra slutpunkter eller spara händelserna i datalagringen. Se [IoT Hub för mer](../../iot-hub/iot-hub-devguide-messages-d2c.md) information. 
* Konfigurera ett Azure Stream Analytics jobb för att bearbeta händelserna i realtid när de tas emot och skapa visualiseringar. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Distribuera åtgärder för rumslig analys i stor skala (flera kameror)

För att få bästa möjliga prestanda och användning av GPU:erna kan du distribuera alla åtgärder för rumslig analys på flera kameror med hjälp av grafinstanser. Nedan visas ett exempel för att köra `cognitiveservices.vision.spatialanalysis-personcrossingline` åtgärden på 15 kameror.

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
                }
            }
        },
        "parameters": {
            "VIDEO_DECODE_GPU_INDEX": 0,
            "VIDEO_IS_LIVE": true
        },
        "instances": {
            "1": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| Namn | Typ| Description|
|---------|---------|---------|
| `batch_size` | int | Om alla kameror har samma upplösning anger du till antalet kameror som ska användas i den åtgärden. Annars anger du till 1 eller lämnar det som standard `batch_size` (1), vilket anger att ingen `batch_size` batch stöds. |

## <a name="next-steps"></a>Nästa steg

* [Distribuera en webbapp för att räkna personer](spatial-analysis-web-app.md)
* [Loggning och felsökning](spatial-analysis-logging.md)
* [Guide för kameraplacering](spatial-analysis-camera-placement.md)
* [Guide för zon- och radplacering](spatial-analysis-zone-line-placement.md)
