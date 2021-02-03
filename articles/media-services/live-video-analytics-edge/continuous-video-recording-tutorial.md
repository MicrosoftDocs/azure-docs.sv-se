---
title: Kontinuerlig video inspelning till molnet och uppspelningen från Cloud-självstudie – Azure
description: I den här självstudien får du lära dig hur du använder Azure Live Video Analytics på Azure IoT Edge för att kontinuerligt spela in video i molnet och strömma någon del av videon med hjälp av Azure Media Services.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 2db59b9c2b2893cad9a613257b4b7c1bafe3e333
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491612"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Självstudie: kontinuerlig video inspelning till molnet och uppspelningen från molnet

I den här självstudien får du lära dig hur du använder Azure Live Video Analytics på Azure IoT Edge för att utföra [kontinuerlig videoinspelning](continuous-video-recording-concept.md) (CVR) till molnet och strömma någon del av videon med hjälp av Azure Media Services. Den här funktionen är användbar för scenarier som säkerhet och efterlevnad om du behöver underhålla ett arkiv av filmen från en kamera i dagar eller veckor. 

I den här självstudien kommer du att:

> [!div class="checklist"]
> * Konfigurera relevanta resurser.
> * Granska koden som utför CVR.
> * Kör exempel koden.
> * Granska resultaten och visa videon.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Föreslagen för läsning  

Läs de här artiklarna innan du börjar:

* [Real tids analys av video i IoT Edge översikt](overview.md)
* [Video analys i real tid med IoT Edge terminologi](terminology.md)
* [Media Graph-begrepp](media-graph-concept.md) 
* [Scenarier för kontinuerlig video inspelning](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Förutsättningar

Krav för den här självstudien är:

* [Visual Studio Code](https://code.visualstudio.com/) på utvecklings datorn med [Azure IoT-verktygen](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) och [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) -tilläggen.

    > [!TIP]
    > Du kan uppmanas att installera Docker. Ignorera den här varningen.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) på din utvecklings dator.
* Slutför [installations skriptet för Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup).

I slutet av de här stegen har du relevanta Azure-resurser distribuerade i din Azure-prenumeration:

* Azure IoT Hub
* Azure-lagringskonto
* Azure Media Services konto
* Virtuella Linux-datorer i Azure med [IoT Edge runtime](../../iot-edge/how-to-install-iot-edge.md) installerat

> [!TIP]
> Om du stöter på problem med Azure-resurser som skapas, kan du läsa vår **[fel söknings guide](troubleshoot-how-to.md#common-error-resolutions)** för att lösa vanliga problem som uppstår.

## <a name="concepts"></a>Begrepp

Som det beskrivs i artikeln om [begrepp för medie diagram](media-graph-concept.md) kan du med ett medie diagram definiera:

- Var mediet ska samlas in från.
- Hur det ska bearbetas.
- Var resultaten ska levereras. 
 
 För att kunna utföra CVR måste du samla in videon från en RTSP-kompatibel kamera och kontinuerligt registrera den på en [Azure Media Services till gång](terminology.md#asset). Det här diagrammet visar en grafisk representation av det medie diagrammet.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="Mediegraf":::

I den här självstudien använder du en Edge-modul som skapats med hjälp av [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) för att simulera en RTSP-kamera. I medie grafen använder du en RTSP- [källnod](media-graph-concept.md#rtsp-source) för att hämta Live-flödet och skicka videon till [till gångs mottagarens nod](media-graph-concept.md#asset-sink), som registrerar videon till en till gång. Videon som används i den här självstudien är [ett exempel på en motorväg](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv).
<iframe src="https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Innan du börjar bör du kontrol lera att du har slutfört den tredje punkten i [krav](#prerequisites). När resurs installations skriptet har slutförts väljer du klammerparenteserna för att exponera mappstrukturen. Du ser några filer som skapats under katalogen ~/clouddrive/lva-Sample.

![Appinställningar](./media/quickstarts/clouddrive.png)

Av intresse i den här självstudien är filerna:

* **~/clouddrive/lva-Sample/Edge-Deployment/.env**: innehåller egenskaper som Visual Studio Code använder för att distribuera moduler till en Edge-enhet.
* **~/clouddrive/lva-sample/appsettings.jspå**: används av Visual Studio Code för att köra exempel koden.

Du behöver filerna för följande steg:

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
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Granska exempelfilerna

Öppna src/Edge/deployment.template.jspå i Visual Studio Code. Den här mallen definierar vilka Edge-moduler du distribuerar till gräns enheten (den virtuella Azure Linux-datorn). Det finns två poster under avsnittet **moduler** med följande namn:

* **lvaEdge**: det här är video analys i real tid i IoT Edge modul.
* **rtspsim**: Detta är RTSP-simulatorn.

Bläddra sedan till mappen src/Cloud-to-Device-console-app. Här ser du appsettings.jspå filen som du skapade tillsammans med några andra filer:

* **C2D-console-app. CSPROJ**: projekt filen för Visual Studio Code.
* **operations.jspå**: den här filen visar de olika åtgärder som du skulle köra.
* **Program.cs**: kod för exempel program som:
    * Läser in appinställningar.
    * Anropar direkta metoder som exponeras av direktsänd video analys i IoT Edge modul. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](direct-methods.md).
    * Pausar så att du kan granska utdata från programmet i **terminalfönstret** och de händelser som genererats av modulen i fönstret **utdata** .
    * Anropar direkta metoder för att rensa resurser.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Skapa och distribuera distributions manifestet för IoT Edge 

Distributions manifestet definierar vilka moduler som distribueras till en gräns enhet och konfigurations inställningarna för dessa moduler. Följ de här stegen för att generera ett manifest från mallfilen och distribuera det sedan till gräns enheten.

1. Starta Visual Studio Code.
1. Ange IoT Hub anslutnings sträng genom att välja ikonen **fler åtgärder** bredvid fönstret **Azure IoT Hub** i det nedre vänstra hörnet. Kopiera strängen från filen src/Cloud-to-Device-console-app/appsettings.jsi filen. 

    ![Ange IoT Hub anslutnings sträng](./media/quickstarts/set-iotconnection-string.png)
1. Högerklicka på filen src/Edge/deployment.template.jsoch välj **generera IoT Edge distributions manifest**. Visual Studio Code använder värdena från. kuvert-filen för att ersätta variablerna som finns i distributions mal Lav data. Den här åtgärden skapar en manifest fil i mappen src/Edge/config med namnet **deployment.amd64.jspå**.

   ![Generera IoT Edge distributions manifest](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Högerklicka på filen src/Edge/config/deployment.amd64.jsi filen och välj **skapa distribution för en enskild enhet**.

   ![Skapa distribution för en enskild enhet](./media/quickstarts/create-deployment-single-device.png)
1. Sedan uppmanas du att **välja en IoT Hub enhet**. Välj lva-Sample-Device i list rutan.
1. Om 30 sekunder uppdaterar du Azure-IoT Hub i det nedre vänstra avsnittet. Du bör se att Edge-enheten har följande moduler distribuerade:
    * Video analys i real tid för IoT Edge (Modulnamn **lvaEdge**)
    * RTSP Simulator (Modulnamn **rtspsim**)
 
    ![IoT Hub](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Förbered för att övervaka modulerna 

När du använder live video analys i IoT Edge-modulen för att spela in direktuppspelad video ström skickas händelser till IoT Hub. Följ dessa steg om du vill se de här händelserna:

1. Öppna Explorer-fönstret i Visual Studio Code och leta efter **Azure-IoT Hub** i det nedre vänstra hörnet.
1. Expandera noden **enheter** .
1. Högerklicka på filen lva-Sample-Device och välj **starta övervakning inbyggd händelse slut punkt**.

    ![Starta övervakning av inbyggd händelse slut punkt](./media/quickstarts/start-monitoring-iothub-events.png)

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
1. Gå till src/Cloud-to-Device-console-app/operations.jspå.
1. Under noden **GraphTopologySet** redigerar du följande:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. Se sedan till att värdet för **topologyName** matchar värdet för egenskapen **Name** i föregående graf-topologi under **GraphInstanceSet** -och **GraphTopologyDelete** -noderna:

    `"topologyName" : "CVRToAMSAsset"`  
1. Öppna [topologin](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json) i en webbläsare och titta på assetNamePattern. För att se till att du har en till gång med ett unikt namn kanske du vill ändra graf-instansnamnet i operations.jspå filen (från standardvärdet för Sample-Graph-1).

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Starta en felsökningssession genom att välja F5. Du ser vissa meddelanden som skrivs ut i **terminalfönstret** .
1. operations.jspå filen börjar med anrop till GraphTopologyList och GraphInstanceList. Om du har rensat resurser efter tidigare snabb starter eller självstudier returnerar den här åtgärden tomma listor och pausar sedan för att välja **RETUR**, som visas:

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
         "topologyName": "CVRToAMSAsset",
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
   * Ett anrop till GraphInstanceActivate för att starta graf-instansen och för att starta video flödet
   * Ett andra anrop till GraphInstanceList för att visa att graf-instansen är i körnings läge 
1. Utdata i **terminalfönstret** pausas nu vid en **tryck RETUR för att fortsätta** prompten. Välj inte **RETUR** för tillfället. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder du anropade.
1. Om du nu växlar till **utdatafönstret** i Visual Studio Code visas meddelanden som skickas till IoT Hub av video analysen i IoT Edge-modulen.

   Dessa meddelanden beskrivs i följande avsnitt.
1. Graf-instansen fortsätter att köra och spela in videon. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa inspelningen går du tillbaka till **terminalfönstret** och väljer **RETUR**. Nästa serie anrop görs för att rensa resurser genom att använda:

   * Ett anrop till GraphInstanceDeactivate för att inaktivera graf-instansen.
   * Ett anrop till GraphInstanceDelete för att ta bort instansen.
   * Ett anrop till GraphTopologyDelete för att ta bort topologin.
   * Ett sista anrop till GraphTopologyList för att visa att listan nu är tom.

## <a name="interpret-the-results"></a>Tolka resultatet 

När du kör medie diagrammet skickar live video analys i IoT Edge-modulen vissa diagnostik-och drift händelser till IoT Edge Hub. Dessa händelser är de meddelanden som visas i fönstret **utdata** i Visual Studio Code. De innehåller ett Body-avsnitt och ett applicationProperties-avsnitt. Information om vad dessa avsnitt representerar finns i [skapa och läsa IoT Hub meddelanden](../../iot-hub/iot-hub-devguide-messages-construct.md).

I följande meddelanden definieras program egenskaperna och innehållet i bröd texten av modulen live video analys.

## <a name="diagnostics-events"></a>Diagnostiska händelser 

### <a name="mediasession-established-event"></a>MediaSession-etablerad händelse

När graf-instansen aktive ras försöker RTSP-Källnoden att ansluta till RTSP-servern som körs i rtspsim-modulen. Om det lyckas skrivs den här händelsen ut:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* Meddelandet är en MediaSessionEstablished (Diagnostics event). Det anger att noden RTSP-källa (ämnet) upprättade en anslutning till RTSP-simulatorn och började ta emot en (simulerad) live-feed.
* Ämnes avsnittet i applicationProperties refererar till noden i den graf-topologi som meddelandet genererades från. I det här fallet kommer meddelandet från noden RTSP-källa.
* Avsnittet eventType i applicationProperties anger att det är en diagnostisk händelse.
* I avsnittet eventTime anges den tidpunkt då händelsen inträffade.
* Avsnittet brödtext innehåller information om den diagnostiska händelsen, som i det här fallet är [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -information.

## <a name="operational-events"></a>Drift händelser 

### <a name="recordingstarted-event"></a>RecordingStarted-händelse

När noden till gångs mottagare börjar spela in video, avger den här händelsen av typen **Microsoft. Media. Graph. Operational. RecordingStarted**:

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Ämnes avsnittet i applicationProperties refererar till noden till gångs mottagare i grafen, som skapade det här meddelandet.

Avsnittet brödtext innehåller information om platsen för utdata. I det här fallet är det namnet på Azure Media Services till gången som video spelas in i. Anteckna det här värdet.

### <a name="recordingavailable-event"></a>RecordingAvailable-händelse

Som namnet antyder skickas händelsen RecordingStarted när inspelningen har startat, men video data kanske inte har laddats upp till till gången ännu. När noden till gångs mottagare har överfört video data till till gången, genererar den här händelsen av typen **Microsoft. Media. Graph. Operational. RecordingAvailable**:

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Den här händelsen anger att tillräckligt med data skrevs till till gången för spelare eller klienter för att starta uppspelningen av videon.

Ämnes avsnittet i applicationProperties refererar till AssetSink-noden i grafen, som skapade det här meddelandet.

Avsnittet brödtext innehåller information om platsen för utdata. I det här fallet är det namnet på Azure Media Services till gången som video spelas in i.

### <a name="recordingstopped-event"></a>RecordingStopped-händelse

När du inaktiverar graf-instansen slutar noden till gångs mottagare att spela in video till till gången. Den genererar den här händelsen av typen **Microsoft. Media. Graph. Operational. RecordingStopped**:

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Den här händelsen indikerar att inspelningen har stoppats.

Ämnes avsnittet i applicationProperties refererar till AssetSink-noden i grafen, som skapade det här meddelandet.

Avsnittet brödtext innehåller information om platsen för utdata, som i det här fallet är namnet på Azure Media Services till gången som video spelas in i.

## <a name="media-services-asset"></a>Media Services till gång  

Du kan granska Media Services till gången som skapades av medie diagrammet genom att logga in på Azure Portal och visa videon.

1. Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.
1. Leta upp ditt Media Services-konto bland de resurser som du har i din prenumeration och öppna fönstret konto.
1. Välj **till gångar** i listan **Media Services** .

    ![Media Services till gångar](./media/continuous-video-recording-tutorial/assets.png)
1. Du hittar en till gång i listan med namnet sampleAsset-CVRToAMSAsset-Sample-Graph-1. Det här är det namn mönster som valts i din graf-Topology-fil.
1. Välj tillgången.
1. På sidan till gångs information väljer du **Skapa ny** under text rutan **strömmande URL** .

    ![Ny till gång](./media/continuous-video-recording-tutorial/new-asset.png)

1. I guiden som öppnas accepterar du standard alternativen och väljer **Lägg till**. Mer information finns i [videouppspelning](video-playback-concept.md).

    > [!TIP]
    > Kontrol lera att [slut punkten för direkt uppspelning körs](../latest/streaming-endpoint-concept.md).
1. Videon ska läsas in i spelaren. Välj **spela** för att visa den.

> [!NOTE]
> Eftersom källa för videon var en behållare som simulerar ett kamera flöde, är tidsstämplar i videon relaterade till när du aktiverade graf-instansen och när du inaktiverar den. Om du vill se hur du bläddrar i en multiday-inspelning och visar delar av det arkivet kan du läsa mer i [uppspelningen av flera dagars inspelnings](playback-multi-day-recordings-tutorial.md) guide. I den här självstudien kan du också se tidsstämplar i videon som visas på skärmen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker testa de andra självstudierna ska du hålla på de resurser som du har skapat. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp som du körde den här självstudien för och tar bort resurs gruppen.

## <a name="next-steps"></a>Nästa steg

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) med stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror med RTSP-stöd på [ONVIF-sidan produkter](https://www.onvif.org/conformant-products/) genom att söka efter enheter som uppfyller profilerna G, S eller T.
* Använd en AMD64-eller x64 Linux-enhet (jämfört med en virtuell Azure Linux-dator). Enheten måste finnas i samma nätverk som IP-kameran. Följ anvisningarna i [installera Azure IoT Edge runtime på Linux](../../iot-edge/how-to-install-iot-edge.md). Följ sedan anvisningarna i avsnittet [distribuera din första IoT Edge till en virtuell Linux-enhet](../../iot-edge/quickstart-linux.md) snabb start för att registrera enheten med Azure IoT Hub.