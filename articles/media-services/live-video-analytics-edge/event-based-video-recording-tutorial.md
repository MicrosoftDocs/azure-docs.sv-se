---
title: Händelsebaserade videoinspelningar till molnet och uppspelningen från moln guiden – Azure
description: I den här självstudien får du lära dig hur du använder Azure Live Video Analytics på Azure IoT Edge för att registrera en Event-baserad videoinspelning i molnet och spela upp den från molnet igen.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: ea98b4c8981be9fffe7911e4c8402a8f522976f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702325"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Självstudie: Event-baserad videoinspelning till molnet och uppspelningen från molnet

I den här självstudien får du lära dig hur du använder Azure Live Video Analytics på Azure IoT Edge för att selektivt registrera delar av en video källa i real tid för att Azure Media Services i molnet. Det här användnings fallet kallas för [Event-baserad videoinspelning](event-based-video-recording-concept.md) (EVR) i den här självstudien. Om du vill registrera delar av en live-video använder du en AI-modell för objekt identifiering för att söka efter objekt i videon och spela bara in video klipp när en viss typ av objekt identifieras. Du får också lära dig hur du spelar upp de inspelade videoklippen med hjälp av Media Services. Den här funktionen är användbar i en mängd olika scenarier där det finns ett behov av att hålla ett arkiv med videoklipp av intresse. 

I de här självstudierna får du:

> [!div class="checklist"]
> * Konfigurera relevanta resurser.
> * Granska koden som utför EVR.
> * Kör exempel koden.
> * Granska resultaten och visa videon.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Föreslagen för läsning  

Läs de här artiklarna innan du börjar:

* [Real tids analys av video i IoT Edge översikt](overview.md)
* [Video analys i real tid med IoT Edge terminologi](terminology.md)
* [Media Graph-begrepp](media-graph-concept.md) 
* [Händelsebaserad videoinspelning](event-based-video-recording-concept.md)
* [Självstudie: utveckla en IoT Edge-modul](../../iot-edge/tutorial-develop-for-linux.md)
* [Så här redigerar du Deployment. * .template.jspå](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Avsnitt om [hur du deklarerar vägar i IoT Edge distributions manifest](../../iot-edge/module-composition.md#declare-routes)

## <a name="prerequisites"></a>Förutsättningar

Krav för den här självstudien är:

* [Visual Studio Code](https://code.visualstudio.com/) på utvecklings datorn med [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) och [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) -tillägg.

    > [!TIP]
    > Du kan uppmanas att installera Docker. Ignorera den här varningen.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) på din utvecklings dator.
* Slutför [installations skriptet för Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)och [Konfigurera miljön](./detect-motion-emit-events-quickstart.md?pivots=programming-language-csharp#set-up-your-development-environment)

I slutet av de här stegen har du relevanta Azure-resurser distribuerade i din Azure-prenumeration:

* Azure IoT Hub
* Azure-lagringskonto
* Azure Media Services konto
* Virtuella Linux-datorer i Azure med [IoT Edge runtime](../../iot-edge/how-to-install-iot-edge.md) installerat

> [!TIP]
> Om du stöter på problem med Azure-resurser som skapas, kan du läsa vår **[fel söknings guide](troubleshoot-how-to.md#common-error-resolutions)** för att lösa vanliga problem som uppstår.

## <a name="concepts"></a>Begrepp

Händelsebaserade videoinspelningar syftar på hur du spelar in video som utlöses av en händelse. Händelsen kan genereras från:
- Bearbetning av själva video signalen, till exempel vid identifiering av ett rörligt objekt i videon.
- En oberoende källa, till exempel öppning av en dörr. 

Alternativt kan du bara utlösa registrering när en inferencing-tjänst identifierar att en speciell händelse har inträffat. I den här självstudien använder du en video om fordon som flyttas på en motor och spelar in videoklipp när en Last bil upptäcks.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="Mediegraf":::

Diagrammet är en bild representation av ett [medie diagram](media-graph-concept.md) och ytterligare moduler som utför det önskade scenariot. Fyra IoT Edge-moduler är involverade:

* Video analys i real tid i en IoT Edge-modul.
* En Edge-modul som kör en AI-modell bakom en HTTP-slutpunkt. Den här AI-modulen använder [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) -modellen, som kan identifiera många typer av objekt.
* En anpassad modul för att räkna och filtrera objekt, vilket kallas en objekt räknare i diagrammet. Du skapar en objekt räknare och distribuerar den i den här självstudien.
* En [RTSP Simulator-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) för att simulera en RTSP-kamera.
    
När diagrammet visas använder du en [RTSP-källmapp](media-graph-concept.md#rtsp-source) i medie grafen för att samla in den simulerade direktsänd videon om trafik på en väg och skicka videon till två sökvägar:

* Den första sökvägen är till en HTTP-tilläggsprovider. Noden samplar video bild rutorna till ett värde som anges av dig med hjälp av `samplingOptions` fältet och vidarebefordrar sedan bild rutorna, som bilder, till AI-modulen YOLOv3, som är en objekt detektor. Noden tar emot resultatet, som är de objekt (fordon i trafik) som identifieras av modellen. Noden HTTP-tillägg publicerar sedan resultaten via noden IoT Hub meddelande mottagare till IoT Edge Hub.
* ObjectCounter-modulen har kon figurer ATS för att ta emot meddelanden från IoT Edge Hub, bland annat objekt identifierings resultat (fordon i trafik). Modulen kontrollerar dessa meddelanden och söker efter objekt av en viss typ, som har kon figurer ATS via en inställning. När ett sådant objekt hittas skickar den här modulen ett meddelande till IoT Edge Hub. De "objekt som hittas"-Meddelanden dirigeras sedan till noden IoT Hub källa i medie diagrammet. När du tar emot ett sådant meddelande, utlöser noden för [signal grind](media-graph-concept.md#signal-gate-processor) av noden IoT Hub källa i medie diagrammet. Noden signal grind processor öppnas sedan under en konfigurerad tids period. Video flödar genom porten till till gångs mottagarens nod under den varaktigheten. Den delen av Live Stream registreras sedan via noden [till gångs mottagare](media-graph-concept.md#asset-sink) till en [till gång](terminology.md#asset) i ditt Azure Media Services konto.

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Innan du börjar bör du kontrol lera att du har slutfört den tredje punkten i [krav](#prerequisites). När resurs installations skriptet har slutförts väljer du klammerparenteserna för att exponera mappstrukturen. Du ser några filer som skapats under katalogen ~/clouddrive/lva-Sample.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="Appinställningar":::

Av intresse i den här självstudien är filerna:

* **~/clouddrive/lva-Sample/Edge-Deployment/.env**: innehåller egenskaper som Visual Studio Code använder för att distribuera moduler till en Edge-enhet.
* **~/clouddrive/lva-sample/appsetting.jspå**: används av Visual Studio Code för att köra exempel koden.

Du behöver filerna för de här stegen.

1. Klona lagrings platsen från GitHub-länken https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Starta Visual Studio Code och öppna mappen där du laddade ned lagrings platsen.
1. I Visual Studio Code bläddrar du till mappen src/Cloud-to-Device-console-app och skapar en fil med namnet **appsettings.jspå**. Den här filen innehåller de inställningar som krävs för att köra programmet.
1. Kopiera innehållet från ~/clouddrive/lva-Sample/-appsettings.jsi filen. Texten bör se ut så här:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    Med anslutnings strängen IoT Hub kan du använda Visual Studio Code för att skicka kommandon till Edge-modulerna via Azure IoT Hub.
    
1. Bläddra sedan till mappen src/Edge och skapa en fil med namnet **. kuvert**.
1. Kopiera innehållet från ~/clouddrive/lva-Sample/Edge-Deployment/.env-filen. Texten bör se ut så här:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaedgeuser/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Granska mallfilen 

I föregående steg startade du Visual Studio Code och öppnade mappen som innehåller exempel koden.

Bläddra till src/Edge i Visual Studio Code. Du ser den. kuvert-fil som du skapade och några filer för distributions mal len. Den här mallen definierar vilka Edge-moduler du distribuerar till gräns enheten (den virtuella Azure Linux-datorn). . Kuvert-filen innehåller värden för variablerna som används i dessa mallar, till exempel Media Services autentiseringsuppgifter.

Öppna src/Edge/deployment.objectCounter.template.jspå. Det finns fyra poster i avsnittet **moduler** som motsvarar de objekt som anges i föregående "begrepp"-avsnitt:

* **lvaEdge**: det här är video analys i real tid i IoT Edge modul.
* **yolov3**: det här är AI-modulen som skapats med hjälp av Yolo v3-modellen.
* **rtspsim**: Detta är RTSP-simulatorn.
* **objectCounter**: det här är den modul som söker efter vissa objekt i resultatet från yolov3.

För modulen objectCounter, se strängen ($ {MODULES. objectCounter}) som används för värdet "bild". Detta baseras på [självstudien](../../iot-edge/tutorial-develop-for-linux.md) om hur du utvecklar en IoT Edge-modul. Visual Studio Code känner automatiskt av att koden för objectCounter-modulen är under src/Edge/modules/objectCounter. 

Läs [det här avsnittet](../../iot-edge/module-composition.md#declare-routes) om hur du deklarerar vägar i IoT Edge distributions manifestet. Granska sedan vägarna i-mallens JSON-fil. Observera hur:

* LVAToObjectCounter används för att skicka vissa händelser till en angiven slut punkt i objectCounter-modulen.
* ObjectCounterToLVA används för att skicka en Utlös ande händelse till en speciell slut punkt (som bör vara noden IoT Hub källa) i lvaEdge-modulen.
* objectCounterToIoTHub används som ett fel söknings verktyg som hjälper dig att se resultatet från objectCounter när du kör den här självstudien.

> [!NOTE]
> Kontrol lera de önskade egenskaperna för objectCounter-modulen, som har ställts in för att leta efter objekt som är märkta som "Truck" med en konfidensnivå på minst 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Skapa och distribuera distributions manifestet för IoT Edge 

Distributions manifestet definierar vilka moduler som distribueras till en gräns enhet och konfigurations inställningarna för dessa moduler. Följ de här stegen för att generera ett manifest från mallfilen och distribuera det sedan till gräns enheten.

Använd Visual Studio Code och följ [anvisningarna](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) för att logga in på Docker. Välj sedan **build och Push IoT Edge-lösning**. Använd src/Edge/deployment.objectCounter.template.jspå för det här steget.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="Lösning för att bygga och push IoT Edge":::

Den här åtgärden skapar objectCounter-modulen för objekt inventering och skickar avbildningen till din Azure Container Registry.

* Kontrol lera att du har miljövariabler CONTAINER_REGISTRY_USERNAME_myacr och CONTAINER_REGISTRY_PASSWORD_myacr som definierats i. miljö-filen.

Det här steget skapar IoT Edge distributions manifestet i src/Edge/config/deployment.objectCounter.amd64.jspå. Högerklicka på filen och välj **skapa distribution för en enskild enhet**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="Skapa distribution för en enskild enhet":::

Om det här är din första själv studie kurs med real tids analys på IoT Edge, kommer Visual Studio Code att ange IoT Hub anslutnings strängen. Du kan kopiera den från appsettings.jspå filen.

> [!NOTE]
> Du kan bli ombedd att ange information om inbyggd slut punkt för IoT Hub. Om du vill hämta den informationen går du till IoT Hub i Azure Portal och letar efter **inbyggda slut punkts** alternativ i det vänstra navigerings fönstret. Klicka där och leta efter den **Event Hub-kompatibla slut punkten** under avsnittet **händelsehubben-kompatibel slut punkt** . Kopiera och Använd texten i rutan. Slut punkten kommer att se ut ungefär så här:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Sedan ber Visual Studio Code dig att välja en IoT Hub enhet. Välj din IoT Edge enhet som ska vara lva-Sample-Device.

I det här skedet har distributionen av Edge-moduler till din IoT Edges enhet startats.
Om 30 sekunder uppdaterar du Azure-IoT Hub i det nedre vänstra avsnittet i Visual Studio Code. Du bör se att det finns fyra moduler som har distribuerats med namnet lvaEdge, rtspsim, yolov3 och objectCounter.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="Fyra moduler har distribuerats":::

## <a name="prepare-for-monitoring-events"></a>Förbereda för övervaknings händelser

Följ dessa steg om du vill visa händelserna från modulen objectCounter och från live video analys på IoT Edge-modulen:

1. Öppna Explorer-fönstret i Visual Studio Code och leta efter **Azure-IoT Hub** i det nedre vänstra hörnet.
1. Expandera noden **enheter** .
1. Högerklicka på filen lva-Sample-Device och välj **starta övervakning inbyggd händelse slut punkt**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="Starta övervakning av inbyggd händelse slut punkt":::

    > [!NOTE]
    > Du kan bli ombedd att ange information om inbyggd slut punkt för IoT Hub. Om du vill hämta den informationen går du till IoT Hub i Azure Portal och letar efter **inbyggda slut punkts** alternativ i det vänstra navigerings fönstret. Klicka där och leta efter den **Event Hub-kompatibla slut punkten** under avsnittet **händelsehubben-kompatibel slut punkt** . Kopiera och Använd texten i rutan. Slut punkten kommer att se ut ungefär så här:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    
## <a name="run-the-program"></a>Köra programmet

1. Öppna fliken **tillägg** i Visual Studio Code (eller tryck på Ctrl + Shift + X) och Sök efter Azure-IoT Hub.
1. Högerklicka och välj **Inställningar för tillägg**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Tilläggs inställningar":::
1. Sök och aktivera "Visa utförligt meddelande".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Visa utförligt meddelande":::
1. <!--In Visual Studio Code, go-->Gå till src/Cloud-to-Device-console-app/operations.jspå.
1. Under noden **GraphTopologySet** redigerar du följande:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json"`
    
1. Sedan går du till **GraphInstanceSet** -och **GraphTopologyDelete** -noderna och redigerar:

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Starta en felsökningssession genom att välja F5. Du ser vissa meddelanden som skrivs ut i **terminalfönstret** .
1. operations.jspå filen börjar med anrop till GraphTopologyList och GraphInstanceList. Om du har rensat resurser efter tidigare snabb starter eller självstudier, returnerar den här åtgärden tomma listor och pausar så att du kan välja **RETUR**, som visas:

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
1. När du har valt **Ange** i **terminalfönstret** görs nästa uppsättning direkta metod anrop:
   * Ett anrop till GraphTopologySet med föregående topologyUrl
   * Ett anrop till GraphInstanceSet med hjälp av följande text
     
        ```
        {
          "@apiVersion": "2.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
            "description": "Sample graph description",
            "parameters": [
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
              },
              {
                "name": "rtspUserName",
                "value": "testuser"
              },
              {
                "name": "rtspPassword",
                "value": "testpassword"
              }
            ]
          }
        }
        ```
    
   * Ett anrop till GraphInstanceActivate för att starta graf-instansen och starta video flödet
   * Ett andra anrop till GraphInstanceList för att visa att graf-instansen är i körnings läge
     
1. Utdata i **terminalfönstret** pausas nu vid en **tryck RETUR för att fortsätta** prompten. Välj inte **RETUR** för tillfället. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder du anropade.
1. Om du nu växlar till **utdatafönstret** i Visual Studio Code visas meddelanden som skickas till IoT Hub av video analysen i IoT Edge-modulen.

   Dessa meddelanden beskrivs i följande avsnitt.
1. Graf-instansen fortsätter att köra och spela in videon. RTSP-simulatorn håller på att upprepa käll videon. Granska meddelandena enligt beskrivningen i följande avsnitt. Stoppa sedan instansen genom att gå tillbaka till **terminalfönstret** och välja **RETUR**. Nästa serie anrop görs för att rensa resurser genom att använda:

   * Ett anrop till GraphInstanceDeactivate för att inaktivera graf-instansen.
   * Ett anrop till GraphInstanceDelete för att ta bort instansen.
   * Ett anrop till GraphTopologyDelete för att ta bort topologin.
   * Ett sista anrop till GraphTopologyList för att visa att listan nu är tom.

## <a name="interpret-the-results"></a>Tolka resultatet 

När du kör medie diagrammet skickar live video analys i IoT Edge-modulen vissa diagnostik-och drift händelser till IoT Edge Hub. Dessa händelser är de meddelanden som visas i fönstret **utdata** i Visual Studio Code. De innehåller ett Body-avsnitt och ett applicationProperties-avsnitt. Information om vad dessa avsnitt representerar finns i [skapa och läsa IoT Hub meddelanden](../../iot-hub/iot-hub-devguide-messages-construct.md).

I följande meddelanden definieras program egenskaperna och innehållet i bröd texten av modulen live video analys.

## <a name="diagnostics-events"></a>Diagnostiska händelser

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse 

När ett medie diagram instansieras försöker RTSP-Källnoden att ansluta till RTSP-servern som körs på RTSP Simulator-behållaren. Om det lyckas skrivs den här händelsen ut. Händelse typen är **Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished**.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Meddelandet är en MediaSessionEstablished (Diagnostics event). Det anger att noden RTSP-källa (ämnet) upprättade en anslutning till RTSP-simulatorn och började ta emot en (simulerad) live-feed.
* Ämnes avsnittet i applicationProperties refererar till noden i den graf-topologi som meddelandet genererades från. I det här fallet kommer meddelandet från noden RTSP-källa.
* Avsnittet eventType i applicationProperties anger att det är en diagnostisk händelse.
* I avsnittet eventTime anges den tidpunkt då händelsen inträffade. Detta är den tidpunkt då trafik videon (MKV-filen) började komma in i modulen som en Live-dataström.
* Avsnittet brödtext innehåller information om den diagnostiska händelsen, som i det här fallet är [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -information.


## <a name="operational-events"></a>Drift händelser

När medie grafen har körts ett tag kan du få en händelse från objectCounter-modulen. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

ApplicationProperties-avsnittet innehåller händelse tiden. Detta är den tidpunkt då objectCounter-modulen observerade att resultaten från yolov3-modulen innehöll objekt av intresse (Truck).

Du kan se att fler av de här händelserna visas som andra Last bilar identifieras i videon.

### <a name="recordingstarted-event"></a>RecordingStarted-händelse

Nästan omedelbart efter att objekt räknaren har skickat händelsen visas en händelse av typen **Microsoft. Media. Graph. Operational. RecordingStarted**:

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

Ämnes avsnittet i applicationProperties refererar till noden till gångs mottagare i grafen, som skapade det här meddelandet. Avsnittet brödtext innehåller information om platsen för utdata. I det här fallet är det namnet på Azure Media Services till gången som video spelas in i. Anteckna det här värdet.

### <a name="recordingavailable-event"></a>RecordingAvailable-händelse

När noden till gångs mottagare har laddat upp video till till gången, genererar den här händelsen av typen **Microsoft. Media. Graph. Operational. RecordingAvailable**:

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Den här händelsen anger att tillräckligt med data skrevs till till gången för spelare eller klienter för att starta uppspelningen av videon. Ämnes avsnittet i applicationProperties refererar till AssetSink-noden i grafen, som skapade det här meddelandet. Avsnittet brödtext innehåller information om platsen för utdata. I det här fallet är det namnet på Azure Media Services till gången som video spelas in i.

### <a name="recordingstopped-event"></a>RecordingStopped-händelse

Om du granskar aktiverings inställningarna (maximumActivationTime) för noden signal grind processor i [topologin](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)ser du att porten är inställd på att stänga efter att 30 sekunders video har skickats. Ungefär 30 sekunder efter RecordingStarted-händelsen bör du se en händelse av typen **Microsoft. Media. Graph. Operational. RecordingStopped**. Den här händelsen anger att noden till gångs mottagare har stoppat inspelningen av videon till till gången.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Den här händelsen indikerar att inspelningen har stoppats. Ämnes avsnittet i applicationProperties refererar till AssetSink-noden i grafen, som skapade det här meddelandet. Avsnittet brödtext innehåller information om platsen för utdata. I det här fallet är det namnet på Azure Media Services till gången som video spelas in i.

## <a name="media-services-asset"></a>Media Services till gång  

Du kan granska Media Services till gången som skapades av grafen genom att logga in på Azure Portal och visa videon.

1. Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.
1. Leta upp ditt Media Services-konto bland de resurser som du har i din prenumeration. Öppna fönstret konto.
1. Välj **till gångar** i listan **Media Services** .

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="Kontinuerlig videoinspelning":::
1. Du hittar en till gång i listan med namnet sampleAssetFromEVR-LVAEdge-{DateTime}. Detta är det namn som anges i egenskapen outputLocation för händelsen RecordingStarted. AssetNamePattern i topologin avgör hur det här namnet genereras.
1. Välj tillgången.
1. På sidan till gångs information väljer du **Skapa ny** under text rutan **strömmande URL** .

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="Ny till gång":::
1. I guiden som öppnas accepterar du standard alternativen och väljer **Lägg till**. Mer information finns i [videouppspelning](video-playback-concept.md).

    > [!TIP]
    > Kontrol lera att [slut punkten för direkt uppspelning körs](../latest/streaming-endpoint-concept.md).
1. Videon ska läsas in i spelaren. Välj **spela** för att visa den.

> [!NOTE]
> Eftersom källa för videon var en behållare som simulerar ett kamera flöde, är tidsstämplar i videon relaterade till när du aktiverade graf-instansen och när du inaktiverar den. Om du använder uppspelnings kontrollerna som är inbyggda i [uppspelnings guiden för flera dagars inspelningar](playback-multi-day-recordings-tutorial.md) kan du se tidsstämplar i videon som visas på skärmen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker testa de andra självstudierna ska du hålla på de resurser som du har skapat. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp som du körde den här självstudien för och tar bort resurs gruppen.

## <a name="next-steps"></a>Nästa steg

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) med stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror med RTSP-stöd på [ONVIF-sidan produkter](https://www.onvif.org/conformant-products/) genom att söka efter enheter som uppfyller profilerna G, S eller T.
* Använd en AMD64-eller x64 Linux-enhet (jämfört med en virtuell Azure Linux-dator). Enheten måste finnas i samma nätverk som IP-kameran. Följ anvisningarna i [installera Azure IoT Edge runtime på Linux](../../iot-edge/how-to-install-iot-edge.md). Följ sedan anvisningarna i avsnittet [distribuera din första IoT Edge till en virtuell Linux-enhet](../../iot-edge/quickstart-linux.md) snabb start för att registrera enheten med Azure IoT Hub.