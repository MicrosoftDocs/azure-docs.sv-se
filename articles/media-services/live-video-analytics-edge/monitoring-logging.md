---
title: Övervakning och loggning – Azure
description: Den här artikeln innehåller en översikt över övervakning och loggning i real tids analys av IoT Edge.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 08b2f5cce80581d71ce73e97ab30900aa8957c77
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564491"
---
# <a name="monitoring-and-logging"></a>Övervakning och loggning

I den här artikeln får du lära dig hur du tar emot händelser för fjärrövervakning från live video analys i IoT Edge modul. 

Du lär dig också hur du styr de loggar som genereras av modulen.

## <a name="taxonomy-of-events"></a>Händelsetaxonomi

Real tids analys av IoT Edge avger händelser eller telemetridata, enligt följande taxonomi:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Diagram som visar taxonomin för händelser.":::

* Drift: händelser som genererats av åtgärder för en användare eller under körningen av ett [medie diagram](media-graph-concept.md)
   
   * Volym: förväntas vara låg (några gånger om en minut eller ännu mindre)
   * Exempel:

      - Inspelningen startades (visas i följande exempel)
      - Inspelningen stoppades
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnostik: händelser som hjälper till att diagnostisera problem med prestanda

   * Volym: kan vara hög (flera gånger per minut)
   * Exempel:
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -information (visas i följande exempel) 
      - Luckor i inkommande video flöden

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analys: händelser som genereras som en del av video analysen

   * Volym: kan vara hög (flera gånger per minut eller mer)
   * Exempel:
      
      - Rörelse identifierad (visas i följande exempel) 
      - Resultat av härledning

   ```      
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
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

De händelser som genereras av modulen skickas till [IoT Edge Hub](../../iot-edge/iot-edge-runtime.md#iot-edge-hub). De kan dirigeras därifrån till andra destinationer. 

### <a name="timestamps-in-analytic-events"></a>Tidsstämplar i analys händelser

Som tidigare nämnts har händelser som genererats som en del av video analysen en tidsstämpel som är kopplad till dem. Om du har [spelat in Live-videon](video-recording-concept.md) som en del av din graf-topologi, hjälper de här tidsstämpeln dig att hitta var i den inspelade videon att händelsen inträffade. Nedan följer rikt linjer för hur du mappar tidsstämpeln i en analys händelse till tids linjen för videon som spelas in i en [Azure Media Services till gång](terminology.md#asset).

Extrahera först `eventTime` värdet. Använd det här värdet i ett [tids intervall filter](playback-recordings-how-to.md#time-range-filters) för att hämta en lämplig del av inspelningen. Till exempel kanske du vill hämta video som börjar 30 sekunder innan `eventTime` och slutar 30 sekunder efter. I föregående exempel, där `eventTime` är 2020-05-12T23:33:09.381 z, en begäran om ett HLS-manifest för 30 sekunder innan och efter, `eventTime` skulle se ut så här:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

Föregående URL skulle returnera en [huvud-spelnings lista](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) som innehåller URL: er för medie spelnings listor. Medie spelnings listan skulle innehålla poster som den här:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
Föregående post rapporterar att ett video fragment är tillgängligt som börjar med `timestamp` värdet `143039375031270` . `timestamp`Värdet i analys händelsen använder samma tids skala som medie spelnings listan. Den kan användas för att identifiera relevant video fragment och söka efter rätt ram.

Mer information finns i de här [artiklarna om bildruta-korrekt sökning](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) i HLS.

## <a name="controlling-events"></a>Kontrollera händelser

Du kan använda följande moduls dubbla egenskaper för att kontrol lera de operativa och diagnostiska händelser som publiceras av video analysen i IoT Edge modul. Dessa egenskaper dokumenteras i [modulens dubbla JSON-schema](module-twin-configuration-schema.md).

- `diagnosticsEventsOutputName`: Om du vill hämta diagnostiska händelser från modulen tar du med den här egenskapen och anger ett värde för den. Utelämna det eller lämna det tomt om du vill stoppa modulen från att publicera diagnostiska händelser.
   
- `operationalEventsOutputName`: Om du vill hämta drift händelser från modulen tar du med den här egenskapen och anger ett värde för den. Utelämna det eller lämna det tomt om du vill stoppa modulen från att publicera drift händelser.
   
Analytics-händelser genereras av noder som rörelse identifierings processor eller HTTP-tilläggs processor. IoT Hub-sinken används för att skicka dem till IoT Edge Hub. 

Du kan styra [operationsföljden för alla föregående händelser](../../iot-edge/module-composition.md#declare-routes) genom att använda `desired` egenskapen i `$edgeHub` modulen, dubbel i distributions manifestet:

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

I föregående JSON `lvaEdge` är namnet på live video analys i IoT Edge-modulen. Regeln för routning följer schemat som definierats i [deklarerar vägar](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> För att säkerställa att Analytics-händelser når IoT Edge hubben måste du ha en mottagare av IoT Hub-noden intill en nod i en nod för rörelse identifiering och/eller valfri HTTP-tilläggsprovider.

## <a name="event-schema"></a>Händelseschema

Händelser kommer från gräns enheten och kan förbrukas på gränsen eller i molnet. Händelser som genererats av video analys i real tid IoT Edge överensstämma med det [mönster för strömnings meddelande](../../iot-hub/iot-hub-devguide-messages-construct.md) som upprättats av Azure IoT Hub. Mönstret består av system egenskaper, program egenskaper och brödtext.

### <a name="summary"></a>Sammanfattning

Varje händelse, vid observation via IoT Hub, har en uppsättning gemensamma egenskaper:

|Egenskap   |Egenskaps typ| Datatyp   |Beskrivning|
|---|---|---|---|
|`message-id`   |säker |guid|  Unikt händelse-ID.|
|`topic`|   applicationProperty |sträng|    Azure Resource Manager sökväg för Azure Media Servicess kontot.|
|`subject`| applicationProperty |sträng|    Under Sök väg till den enhet som avger händelsen.|
|`eventTime`|   applicationProperty|    sträng| Tiden då händelsen skapades.|
|`eventType`|   applicationProperty |sträng|    Händelse typ identifierare. (Se följande avsnitt.)|
|`body`|body    |objekt|    Specifika händelse data.|
|`dataVersion`  |applicationProperty|   sträng  |{Major}. Del|

### <a name="properties"></a>Egenskaper

#### <a name="message-id"></a>meddelande-ID

En globalt unik identifierare (GUID) för händelsen.

#### <a name="topic"></a>ämne

Representerar det Azure Media Servicess konto som är kopplat till grafen.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>Ämne

Den enhet som avger händelsen:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

Med `subject` egenskapen kan du mappa allmänna händelser till den genererade modulen. För ett ogiltigt RTSP-användar namn eller lösen ord finns den genererade händelsen `Microsoft.Media.Graph.Diagnostics.ProtocolError` på `/graphInstances/myGraph/sources/myRtspSource` noden.

#### <a name="event-types"></a>Händelsetyper

Händelse typer tilldelas till ett namn område enligt schemat:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Händelseklasser

|Klassnamn|Description|
|---|---|
|Analys  |Händelser som genereras som en del av innehålls analysen.|
|Diagnostik    |Händelser som hjälper dig att diagnostisera problem och prestanda.|
|Operativ    |Händelser som genererats som en del av resurs åtgärden.|

Händelse typerna är speciella för varje händelse klass.

Exempel:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>Händelsetid

Händelse tiden är formaterad i en ISO 8601-sträng. Den representerar den tidpunkt då händelsen inträffade.

### <a name="azure-monitor-collection-via-telegraf"></a>Azure Monitor samling via teleympkvistar

Dessa mått rapporteras från live video analys i IoT Edge modul:  

|Måttnamn|Typ|Etikett|Description|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Mätare|iothub, edge_device, module_name, graph_topology|Totalt antal aktiva diagram per topologi.|
|lva_received_bytes_total|Räknare|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Totalt antal byte som tagits emot av en nod. Stöds endast för RTSP-källor.|
|lva_data_dropped_total|Räknare|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|Räknare för data som släppts (händelser, medier och så vidare).|

> [!NOTE]
> En [Prometheus-slutpunkt](https://prometheus.io/docs/practices/naming/) exponeras i Port 9600 för behållaren. Om du namnger din video analys i IoT Edge-modulen "lvaEdge" kommer de att kunna komma åt mått genom att skicka en GET-begäran till http://lvaEdge:9600/metrics .   

Följ dessa steg om du vill aktivera insamling av mått från Live Video Analytics i IoT Edge modul:

1. Skapa en mapp på din utvecklings dator och gå till mappen.

1. I mappen skapar du en `telegraf.toml` fil som innehåller följande konfigurationer:
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Se till att ersätta variablerna i. toml-filen. Variablerna betecknas med klammerparenteser ( `{}` ).

1. I samma mapp skapar du en Dockerfile som innehåller följande kommandon:
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Använd Docker CLI-kommandon för att bygga Docker-filen och publicera avbildningen i Azure Container Registry.
    
   Mer information om hur du använder Docker CLI för att skicka till ett behållar register finns i [push-och pull-avbildningar](../../container-registry/container-registry-get-started-docker-cli.md). Mer information om Azure Container Registry finns i [dokumentationen](../../container-registry/index.yml).


1. När push-installationen till Azure Container Registry är klar lägger du till följande nod i distributions manifest filen:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > Se till att ersätta variablerna i manifest filen. Variablerna betecknas med klammerparenteser ( `{}` ).


   Azure Monitor kan [autentiseras via tjänstens huvud namn](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   Plugin-programmet Azure Monitorin ympkvistar visar [flera autentiseringsmetoder](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). 

  1. Om du vill använda tjänstens huvud namns autentisering anger du följande miljövariabler:  
     `AZURE_TENANT_ID`: Anger den klient som ska autentiseras.  
     `AZURE_CLIENT_ID`: Anger det app-klient-ID som ska användas.  
     `AZURE_CLIENT_SECRET`: Anger den app-hemlighet som ska användas.  
     
     >[!TIP]
     > Du kan ge tjänsten huvud rollen **övervaknings mått utgivar** rollen. Följ stegen i **[skapa tjänstens huvud namn](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** för att skapa tjänstens huvud namn och tilldela rollen.

1. När modulerna har distribuerats visas måtten i Azure Monitor under ett enda namn område. Mått namnen matchar de som har spridits av Prometheus. 

   I det här fallet går du till IoT-hubben i Azure Portal och väljer **mått** i det vänstra fönstret. Du bör se måtten där.

### <a name="log-analytics-metrics-collection"></a>Log Analytics mått samling
Med hjälp av [Prometheus-slutpunkten](https://prometheus.io/docs/practices/naming/) tillsammans med [Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md)kan du generera och [övervaka mått](../../azure-monitor/essentials/metrics-supported.md) som använda CPUPercent, MemoryUsedPercent osv.   

> [!NOTE]
> Konfigurationen nedan samlar inte in loggar, **endast mått**. Det är möjligt att utöka insamlarens modul så att den även samlar in och laddar upp loggar.

[![Diagram som visar insamling av mått med hjälp av Log Analytics.](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. Lär dig hur du [samlar in mått](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector)
1. Använd Docker CLI-kommandon för att bygga [Docker-filen](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux) och publicera avbildningen i Azure Container Registry.
    
   Mer information om hur du använder Docker CLI för att skicka till ett behållar register finns i [push-och pull-avbildningar](../../container-registry/container-registry-get-started-docker-cli.md). Mer information om Azure Container Registry finns i [dokumentationen](../../container-registry/index.yml).

1. När push-installationen till Azure Container Registry har slutförts infogas följande i distributions manifestet:
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > Modulerna `edgeHub` `edgeAgent` och `lvaEdge` är namnen på de moduler som definierats i distributions manifest filen. Se till att namnen på modulerna matchar.   

    Du kan hämta dina `LogAnalyticsWorkspaceId` och `LogAnalyticsSharedKey` värden genom att följa dessa steg:
    1. Gå till Azure Portal
    1. Sök efter dina Log Analytics arbets ytor
    1. När du har hittat din Log Analytics arbets yta navigerar du till `Agents management` alternativet i det vänstra navigerings fönstret.
    1. Du hittar arbetsyte-ID: t och de hemliga nycklar som du kan använda.

1. Skapa sedan en arbets bok genom att klicka på `Workbooks` fliken i det vänstra navigerings fönstret.
1. Med hjälp av Kusto frågespråk kan du skriva frågor som nedan och få processor procent som används av IoT Edge modulerna.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [![Diagram som visar måtten med Kusto-frågan.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Loggning

Precis som med andra IoT Edge moduler kan du också [Granska behållar loggarna](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) på gräns enheten. Du kan konfigurera den information som skrivs till loggarna genom att använda [Följande modul, dubbla](module-twin-configuration-schema.md) egenskaper:

* `logLevel`

   * Tillåtna värden är `Verbose` , `Information` , `Warning` , `Error` och `None` .
   * Standardvärdet är `Information`. Loggarna innehåller fel-, varnings-och informations meddelanden.
   * Om du ställer in värdet på `Warning` kommer loggarna att innehålla fel och varnings meddelanden.
   * Om du ställer in värdet på `Error` innehåller loggarna endast fel meddelanden.
   * Om du ställer in värdet på `None` kommer inga loggar att genereras. (Vi rekommenderar inte den här konfigurationen.)
   * Använd `Verbose` endast om du behöver dela loggar med Azure-Support för att diagnosticera ett problem.

* `logCategories`

   * En kommaavgränsad lista över ett eller flera av dessa värden: `Application` , `Events` , `MediaPipeline` .
   * Standardvärdet är `Application, Events`.
   * `Application`: Information på hög nivå från modulen, t. ex. startmeddelanden, miljö fel och direkta metod anrop.
   * `Events`: Alla händelser som beskrevs tidigare i den här artikeln.
   * `MediaPipeline`: Lågnivå loggar som kan ge insikt när du felsöker problem, t. ex. problem med att upprätta en anslutning till en RTSP-kompatibel kamera.
   
### <a name="generating-debug-logs"></a>Genererar fel söknings loggar

I vissa fall kan du behöva skapa mer detaljerade loggar än de som beskrivs ovan för att hjälpa Azure-supporten att lösa ett problem. Så här genererar du loggarna:

1. [Länka modulen lagring till enhets lagringen](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) via `createOptions` . Om du tittar på en [mall för distributions manifest](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) från snabb starterna visas den här koden:

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   Med den här koden kan Edge-modulen skriva loggar till enhetens lagrings Sök väg `/var/local/mediaservices/` . 

 1. Lägg till följande `desired` egenskap i modulen:

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Modulen kommer nu att skriva fel söknings loggar i binärformat till enhetens lagrings Sök väg `/var/local/mediaservices/debuglogs/` . Du kan dela dessa loggar med Azure-support.

## <a name="faq"></a>Vanliga frågor

Om du har frågor kan du läsa [vanliga frågor och svar om övervakning och mått](faq.md#monitoring-and-metrics).

## <a name="next-steps"></a>Nästa steg

[Kontinuerlig videoinspelning](continuous-video-recording-tutorial.md)