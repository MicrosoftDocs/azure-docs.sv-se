---
title: Övervakning och loggning – Azure
description: Den här artikeln innehåller en översikt över video analys på IoT Edge övervakning och loggning.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 8ae455a4157cd649f610620e486323ac2c0a5744
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401057"
---
# <a name="monitoring-and-logging"></a>Övervakning och loggning

I den här artikeln får du lära dig mer om hur du kan ta emot händelser från direktsänd video analys i IoT Edge modul för fjärrövervakning. 

Du får också lära dig hur du kan kontrol lera vilka loggar som genereras av modulen.

## <a name="taxonomy-of-events"></a>Händelsetaxonomi

Real tids analys på IoT Edge avger händelser eller telemetridata enligt följande taxonomi.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Händelsetaxonomi":::

* Drift: händelser som genereras som en del av åtgärder som utförs av en användare eller under körningen av ett [medie diagram](media-graph-concept.md).
   
   * Volym: förväntas vara låg (några gånger om en minut eller till och med lägre pris).
   * Exempel:

      Inspelningen startade (nedan), inspelningen stoppades
      
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
* Diagnostik: händelser som hjälper till att diagnostisera problem och/eller problem med prestanda.

   * Volym: kan vara hög (flera gånger per minut).
   * Exempel:
   
      RTSP [-](https://en.wikipedia.org/wiki/Session_Description_Protocol) information (nedan) eller luckor i det inkommande video flödet.

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
* Analys: händelser som genereras som en del av video analysen.

   * Volym: kan vara hög (flera gånger per minut eller oftare).
   * Exempel:
      
      Rörelse identifierad (nedan), resultat för nedgradering.

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

De händelser som genereras av modulen skickas till [IoT Edge Hub](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)och därifrån kan de dirigeras till andra mål. 

### <a name="timestamps-in-analytic-events"></a>Tidsstämplar i analys händelser

Som anges ovan har händelser som genererats som en del av video analysen en tidsstämpel som är kopplad till dem. Om du har [spelat in direktsänd video](video-recording-concept.md) som en del av din graf-topologi, hjälper den här tidsstämpeln dig att hitta var i den inspelade videon att en viss händelse inträffade. Nedan följer rikt linjerna för hur du mappar tidsstämpeln i en analys händelse till tids linjen för videon som spelas in i en [Azure Media service-till gång](terminology.md#asset).

Extrahera först `eventTime` värdet. Använd det här värdet i ett [tids intervall filter](playback-recordings-how-to.md#time-range-filters) för att hämta en lämplig del av inspelningen. Till exempel kanske du vill hämta video som börjar 30 sekunder innan `eventTime` och slutar 30 sekunder efteråt. Med ovanstående exempel, där `eventTime` är 2020-05-12T23:33:09.381 z, skulle en begäran om ett HLS-manifest för fönstret +/-30 s se ut så här:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

URL: en ovan skulle returnera en så kallad [huvud-spelnings lista](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)med URL: er för medie spelnings listor. Medie spelnings listan skulle innehålla poster som följande:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
I ovanstående rapport rapporterar posten att ett video fragment är tillgängligt som startar vid ett tidsstämpel-värde för `143039375031270` . `timestamp`Värdet i analys händelsen använder samma tids skala som medie spelnings listan och kan användas för att identifiera relevanta videofragment och söka efter rätt ram.

För mer information, kan du läsa en av de många [artiklarna](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) i den ram som är korrekta i HLS.

## <a name="controlling-events"></a>Kontrollera händelser

Du kan använda följande modul dubbla egenskaper, enligt beskrivningen i [modulens dubbla JSON-schema](module-twin-configuration-schema.md), för att styra de operativa och diagnostiska händelser som publiceras av video analysen i IoT Edge modul.

`diagnosticsEventsOutputName` – inkludera och ange (valfritt) värde för den här egenskapen för att hämta diagnostiska händelser från modulen. Utelämna det eller lämna det tomt om du vill stoppa modulen från att publicera diagnostiska händelser.
   
`operationalEventsOutputName` – inkludera och ange (valfritt) värde för den här egenskapen för att hämta drift händelser från modulen. Utelämna det eller lämna det tomt om du vill stoppa modulen från att publicera drift händelser.
   
Analytics-händelserna genereras av noder som rörelse identifierings processor, eller HTTP-tilläggsbegäranden, och IoT Hub-mottagaren används för att skicka dem till IoT Edge Hub. 

Du kan styra [operationsföljden för alla ovanstående händelser](../../iot-edge/module-composition.md#declare-routes) via en önskad egenskap i $edgeHub modul, dubbla (i distributions manifestet):

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

I det här exemplet är lvaEdge namnet på den direktsända video analysen i IoT Edge modul, och regeln för routning följer schemat som definierats i [deklarerar vägar](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> För att säkerställa att Analytics-händelser når IoT Edge hubben måste det finnas en IoT Hub-nod för en nod för avkänning av rörelse avkänning och/eller valfri HTTP-tilläggsprovider.

## <a name="event-schema"></a>Händelseschema

Händelser kommer från gräns enheten och kan förbrukas på gränsen eller i molnet. Händelser som genereras av real tids analys på IoT Edge överensstämmer med det [mönster för strömnings meddelande](../../iot-hub/iot-hub-devguide-messages-construct.md) som upprättats av Azure IoT Hub, med system egenskaper, program egenskaper och en brödtext.

### <a name="summary"></a>Sammanfattning

Varje händelse, vid observation via IoT Hub, har en uppsättning gemensamma egenskaper enligt beskrivningen nedan.

|Egenskap   |Egenskapstyp| Datatyp   |Beskrivning|
|---|---|---|---|
|meddelande-ID |säker |guid|  Unikt händelse-ID.|
|ämne| applicationProperty |sträng|    Azure Resource Manager sökväg för Media Servicess kontot.|
|Ämne|   applicationProperty |sträng|    Under Sök väg till den enhet som avger händelsen.|
|Händelsetid| applicationProperty|    sträng| Tiden då händelsen skapades.|
|Händelsetyp| applicationProperty |sträng|    Händelse typ identifierare (se nedan).|
|body|body  |objekt|    Specifika händelse data.|
|Dataversion    |applicationProperty|   sträng  |{Major}. Del|

### <a name="properties"></a>Egenskaper

#### <a name="message-id"></a>meddelande-ID

Händelse globalt unik identifierare (GUID)

#### <a name="topic"></a>ämne

Representerar Azure Media Service-kontot som är associerat med grafen.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>Ämne

Enhet som avger händelsen:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

Egenskapen subject gör det möjligt för allmänna händelser att mappas till den genererade modulen. Om t. ex. ogiltigt RTSP-användarnamn eller lösen ord finns den genererade händelsen finns `Microsoft.Media.Graph.Diagnostics.ProtocolError` på `/graphInstances/myGraph/sources/myRtspSource` noden.

#### <a name="event-types"></a>Händelsetyper

Händelse typer tilldelas en namnrymd enligt följande schema:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Händelseklasser

|Klassnamn|Beskrivning|
|---|---|
|Analytics  |Händelser som genereras som en del av innehålls analysen.|
|Diagnostik    |Händelser som är till hjälp vid diagnostik av problem och prestanda.|
|Operativ    |Händelser som genererats som en del av resurs åtgärden.|

Händelse typerna är speciella för varje händelse klass.

Exempel:

* Microsoft. Media. Graph. Analytics. härledning
* Microsoft. Media. Graph. Diagnostics. AuthorizationError
* Microsoft. Media. Graph. Operational. GraphInstanceStarted

### <a name="event-time"></a>Tid för händelsen

Händelse tiden beskrivs i ISO8601-strängen och den tidpunkt då händelsen inträffade.

### <a name="azure-monitor-collection-using-telegraf"></a>Azure Monitor samling med hjälp av teleympkvistar

De här måtten kommer att rapporteras direkt video analys i IoT Edge modul:  

|Måttnamn|Typ|Etikett|Beskrivning|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Mätare|iothub, edge_device, module_name, graph_topology|Totalt antal aktiva diagram per topologi.|
|lva_received_bytes_total|Räknare|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Det totala antalet byte som tagits emot av en nod. Stöds endast för RTSP-källor|
|lva_data_dropped_total|Räknare|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|Räknare för data som släppts (händelser, medier osv.)|

> [!NOTE]
> En [Prometheus-slutpunkt](https://prometheus.io/docs/practices/naming/) exponeras i Port **9600** för behållaren. Om du namnger din video analys i IoT Edge-modulen "lvaEdge" skulle de kunna komma åt mått genom att skicka en GET-begäran till http://lvaEdge:9600/metrics .   

Följ dessa steg om du vill aktivera insamling av mått från Live Video Analytics i IoT Edge modul:

1. Skapa en mapp på din utvecklings dator och navigera till den mappen

1. I mappen skapar du `telegraf.toml` en fil med följande innehåll
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = ""
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Se till att du ersätter variablerna (markerade med `{ }` ) i innehålls filen

1. I den mappen skapar du en `.dockerfile` med följande innehåll
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Nu kan du använda Docker CLI-kommandot för att **bygga Docker-filen** och publicera avbildningen till Azure Container Registry.
    1. Lär dig att [skicka och hämta Docker-avbildningar – Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).  Mer Azure Container Registry (ACR) hittar du [här](https://docs.microsoft.com/azure/container-registry/).


1. När push-ACR har slutförts lägger du till följande nod i distributions manifest filen:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{ACR_LINK_TO_YOUR_TELEGRAF_IMAGE}"
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
    > Se till att du ersätter variablerna (markerade med `{ }` ) i innehålls filen


1. **Autentisering**
    1. Azure Monitor kan [autentiseras av tjänstens huvud namn](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        1. Azure Monitorin ympkvistar-plugin-programmet visar [flera metoder för autentisering](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). Följande miljövariabler måste anges för att använda tjänstens huvud namns autentisering.  
            • AZURE_TENANT_ID: anger den klient som ska autentiseras.  
            • AZURE_CLIENT_ID: anger det app-klient-ID som ska användas.  
            • AZURE_CLIENT_SECRET: anger den app-hemlighet som ska användas.  
    >[!TIP]
    > Tjänstens huvud namn kan tilldelas rollen "**övervakning av mått utfärdare**".

1. När modulerna har distribuerats visas måtten i Azure Monitor under ett enda namn område med mått namn som matchar de som har spridits av Prometheus. 
    1. I det här fallet navigerar du till IoT Hub i Azure Portal och klickar på länken "**mått**" i det vänstra navigerings fönstret. Du bör se måtten där.
## <a name="logging"></a>Loggning

Precis som med andra IoT Edge moduler kan du också [Granska behållar loggarna](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) på gräns enheten. Informationen som skrivs till loggarna kan styras av [Följande modul, dubbla](module-twin-configuration-schema.md) egenskaper:

* logLevel

   * Tillåtna värden är utförlig, information, varning, fel, ingen.
   * Standardvärdet är information – loggarna innehåller fel, varning och information. Kömeddelanden.
   * Om du ställer in värdet på varning kommer loggarna att innehålla fel och varnings meddelanden
   * Om du ställer in värdet på fel kommer loggarna bara att innehålla fel meddelanden.
   * Om du anger värdet Ingen skapas inga loggar (detta rekommenderas inte).
   * Du bör endast använda verbose om du behöver dela loggar med Azure-Support för att diagnosticera ett problem.
* logCategories

   * En kommaavgränsad lista över ett eller flera av följande: program, händelser, MediaPipeline.
   * Standard: program, händelser.
   * Program – det här är en hög nivå information från modulen, t. ex. start meddelanden i moduler, miljö fel och direkta metod anrop.
   * Händelser – dessa är alla händelser som beskrevs tidigare i den här artikeln.
   * MediaPipeline – dessa är några lågnivå loggar som kan ge insikt vid fel sökning av problem, till exempel problem med att upprätta en anslutning med en RTSP-kompatibel kamera.
   
### <a name="generating-debug-logs"></a>Genererar fel söknings loggar

I vissa fall kan du behöva generera mer detaljerade loggar än de som beskrivs ovan, för att hjälpa Azure-supporten att lösa ett problem. Det finns två steg för att göra detta.

Först länkar du [modulens lagring till enhets lagringen](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) via createOptions. Om du undersöker en [mall för distributions manifest](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) från snabb starterna visas:

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

Ovan kan Edge-modulen skriva loggar till lagrings Sök vägen för (enhet) "/var/Local/MediaServices/". Om du lägger till följande önskade egenskap i modulen:

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Sedan skriver modulen fel söknings loggar i binärformat till lagrings Sök vägen (enhet)/var/Local/MediaServices/debuglogs/, som du kan dela med Azure-supporten.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[Vanliga frågor och svar](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Nästa steg

[Kontinuerlig videoinspelning](continuous-video-recording-tutorial.md)
