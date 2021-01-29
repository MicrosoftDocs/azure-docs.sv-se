---
title: Analysera direktsänd video med real tids video analys på IoT Edge och Azure Custom Vision
description: Lär dig hur du använder Azure Custom Vision för att bygga en behållar modell som kan identifiera en leksaks Truck och använda AI-utöknings möjligheter i Azure Live Video Analytics på Azure IoT Edge för att distribuera modellen på gränsen för att identifiera leksaks truckar från en real tids video ström.
ms.topic: tutorial
ms.date: 09/08/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 0b5d011ac7832436edf1f5c898b1fe1e239d0aea
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055372"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Självstudie: analysera direktsänd video med real tids video analys på IoT Edge och Azure Custom Vision

I den här självstudien får du lära dig hur du använder Azure [Custom vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) för att bygga en behållar modell som kan identifiera en leksaks Last bil och använda [AI-funktionen](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) i Azure Live video analys på Azure IoT Edge för att distribuera modellen på gränsen för att identifiera leksaks truckar från en live video ström.

Vi visar dig hur du sammanställer kraften hos Custom Vision att bygga och träna en modell för visuellt innehåll genom att ladda upp och etikettera några bilder. Du behöver ingen kunskap om data vetenskap, maskin inlärning eller AI. Du lär dig också om funktionerna i real tids analys för att enkelt distribuera en anpassad modell som en behållare på gränsen och analysera en simulerad Live-videofeed.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/custom-vision-tutorial/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/custom-vision-tutorial/python/header.md)]
::: zone-end

Självstudien visar hur du:

> [!div class="checklist"]
> * Konfigurera relevanta resurser.
> * Bygg en Custom Vision modell i molnet för att identifiera leksaks truckar och distribuera dem på gränsen.
> * Skapa och distribuera ett medie diagram med ett HTTP-tillägg till en Custom Vision modell.
> * Kör exempel koden.
> * Granska och tolka resultatet.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Föreslagen för läsning  

Läs igenom följande artiklar innan du börjar:

* [Real tids analys av video i IoT Edge översikt](overview.md)
* [Översikt över Azure Custom Vision](../../cognitive-services/custom-vision-service/overview.md)
* [Video analys i real tid med IoT Edge terminologi](terminology.md)
* [Media Graph-begrepp](media-graph-concept.md)
* [Videoanalys i realtid utan videoinspelning](analyze-live-video-concept.md)
* [Köra Live Video Analytics med din egen modell](use-your-model-quickstart.md)
* [Självstudie: utveckla en IoT Edge-modul](../../iot-edge/tutorial-develop-for-linux.md)
* [Så här redigerar du Deployment. * .template.jspå](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Förutsättningar

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/python/prerequisites.md)]
::: zone-end

> [!IMPORTANT]
> Den här Custom Vision modulen har endast stöd för **Intel x86-och amd64** -arkitekturer. Kontrol lera din gräns enhets arkitektur innan du fortsätter.

## <a name="review-the-sample-video"></a>Granska exempel videon

I den här självstudien används en video fil för [leksaks bilar](https://lvamedia.blob.core.windows.net/public/t2.mkv) för att simulera en Live-dataström. Du kan granska videon via ett program, till exempel [VLC Media Player](https://www.videolan.org/vlc/). Välj **CTRL + N** och klistra sedan in en länk till [videon för leksaks bilen](https://lvamedia.blob.core.windows.net/public/t2.mkv) för att starta uppspelningen. Observera att när du tittar på videon visas en leksaks Truck i videon vid den 36-andra markören. Den anpassade modellen har tränats för att identifiera denna speciella leksaks Truck. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LPwK]

I den här självstudien använder du video analys i real tid för IoT Edge för att identifiera sådana leksaks truckar och publicera associerade härlednings händelser till IoT Edge Hub.

## <a name="overview"></a>Översikt

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Diagram som visar en Custom Vision översikt.":::

Det här diagrammet visar hur signal flödet i den här självstudien. En [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulerar en IP-kamera som är värd för en RTSP-server (Real-Time Streaming Protocol). En [RTSP-källmapp](media-graph-concept.md#rtsp-source) hämtar video flödet från den här servern och skickar video ramar till noden [http-tilläggsbegäranden](media-graph-concept.md#http-extension-processor) .

Noden HTTP-tillägg spelar rollen för en proxy.  Den samplar de inkommande video bild rutorna som du har angett genom att använda `samplingOptions` fältet och konverterar även video bild rutorna till den angivna bild typen. Sedan vidarebefordrar avbildningen över REST till en annan Edge-modul som kör en AI-modell bakom en HTTP-slutpunkt. I det här exemplet är den Edge-modulen leksakens detektor modell som skapats med hjälp av Custom Vision. Noden för HTTP-tilläggsbegäranden samlar in identifierings resultaten och publicerar händelser till [Azure IoT Hub Sink](media-graph-concept.md#iot-hub-message-sink) -noden. Noden skickar sedan händelserna till [IoT Edge Hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Bygg och distribuera en identifierings modell för Custom Vision leksak 

Som namnet Custom Vision föreslår kan du använda det för att bygga en egen anpassad objekt detektor eller klassificerare i molnet. Det ger ett enkelt, lättanvänt och intuitivt gränssnitt för att bygga Custom Vision modeller som kan distribueras i molnet eller på gränsen via behållare.

För att bygga en leksaks Last bils detektor följer du stegen i [snabb start: bygga en objekt detektor med Custom vision webbplats](../../cognitive-services/custom-vision-service/get-started-build-detector.md).

> [!IMPORTANT]
> Den här Custom Vision modulen stöder bara endast **Intel x86-och amd64** -arkitekturer. Kontrol lera din gräns enhets arkitektur innan du fortsätter.

Ytterligare kommentarer:
 
* I den här självstudien ska du inte använda de exempel bilder som finns i [avsnittet krav](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites)för snabb starts artikeln. I stället har vi använt en viss avbildnings uppsättning för att skapa en leksaks detektor Custom Vision modell. Använd [de här bilderna](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) när du uppmanas att [välja dina utbildnings avbildningar](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) i snabb starten.
* I avsnittet tagga avbildning i snabb start ser du till att du taggar leksaks bilen som visas i bilden med taggen "Last bil".

När du är klar kan du exportera modellen till en Docker-behållare med hjälp av knappen **Exportera** på fliken **prestanda** . Se till att du väljer Linux som behållar plattforms typ. Detta är den plattform som containern ska köras på. Datorn som du hämtar behållaren på kan vara antingen Windows eller Linux. Anvisningarna nedan baseras på den behållar fil som hämtats till en Windows-dator.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Skärm bild som visar Dockerfile vald.":::
 
1. Du bör ha en zip-fil som hämtats till den lokala datorn med namnet `<projectname>.DockerFile.Linux.zip` . 
1. Kontrol lera om du har Docker installerat. Om inte, installerar du [Docker](https://docs.docker.com/get-docker/) för Windows-skrivbordet.
1. Zippa upp den hämtade filen på valfri plats. Använd kommando raden för att gå till den zippade katalogen.
    
    Kör följande kommandon:
    
    1. `docker build -t cvtruck` 
    
        Det här kommandot hämtar många paket, skapar Docker-avbildningen och taggar den som `cvtruck:latest` .
    
        > [!NOTE]
        > Om det lyckas bör du se följande meddelanden: `Successfully built <docker image id>` och `Successfully tagged cvtruck:latest` . Försök igen om kommandot build Miss lyckas. Ibland laddas inte beroende paket om första gången.
    1. `docker  image ls`

        Det här kommandot kontrollerar om den nya avbildningen finns i det lokala registret.
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        Det här kommandot bör publicera Docker-portens exponerade port (80) på den lokala datorns port (80).
    1. `docker container ls`
    
        Det här kommandot kontrollerar port mappningarna och om Docker-behållaren körs på datorn. Utdata ska vara något som liknar:

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Det här kommandot testar behållaren på den lokala datorn. Om avbildningen har samma Last bil när vi tränade modellen på, ska utdata vara något som liknar följande exempel. Det föreslår att leverans trucken har identifierats med 90,12% sannolikhet.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Granska exempelfilerna

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generera och distribuera distributions manifestet

1. I Visual Studio Code går du till src/Cloud-to-Device-console-app/operations.jspå.

1. Under `GraphTopologySet` kontrollerar du att följande stämmer:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`
1. Under `GraphInstanceSet` , se till att:
    1. `"topologyName" : "InferencingWithHttpExtension"`
    1. Lägg till följande överst i parameter mat ris: `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Ändra `rtspUrl` parametervärdet till `"rtsp://rtspsim:554/media/t2.mkv"` .
1. Under `GraphTopologyDelete` kontrollerar du `"name": "InferencingWithHttpExtension"` .
1. Högerklicka på filen src/Edge/deployment.customvision.template.jsoch välj **generera IoT Edge distributions manifest**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Skärm bild som visar generera IoT Edge distributions manifest.":::
  
    Den här åtgärden ska skapa en manifest fil i mappen src/Edge/config med namnet deployment.customvision.amd64.jspå.
1. Öppna filen src/Edge/deployment.customvision.template.jsoch leta upp `registryCredentials` JSON-blocket. I det här blocket hittar du adressen till ditt Azure Container Registry tillsammans med dess användar namn och lösen ord.
1. Skicka den lokala Custom Vision-behållaren till Azure Container Registry-instansen genom att följa dessa steg på kommando raden:

    1. Logga in i registret genom att köra följande kommando:
    
        `docker login <address>`
    
        Ange användar namn och lösen ord när du tillfrågas om autentisering.
        
        > [!NOTE]
        > Lösen ordet visas inte på kommando raden.
    1. Tagga avbildningen med hjälp av det här kommandot: <br/>`docker tag cvtruck   <address>/cvtruck`.
    1. Skicka avbildningen med hjälp av det här kommandot: <br/>`docker push <address>/cvtruck`.

        Om det är klart bör du se `Pushed` kommando raden tillsammans med SHA för avbildningen.
    1. Du kan också bekräfta genom att kontrol lera Azure Container Registry-instansen i Azure Portal. Här ser du namnet på lagrings platsen tillsammans med taggen.
1. Ange IoT Hub anslutnings sträng genom att välja ikonen **fler åtgärder** bredvid fönstret **Azure IoT Hub** i det nedre vänstra hörnet. Du kan kopiera strängen från appsettings.jsi filen. (Här är en annan metod som rekommenderas för att se till att du har rätt IoT Hub konfigurerat i Visual Studio Code via [kommandot Select IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).)

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Skärm bild som visar set IoT Hub-anslutningssträng.":::
1. Högerklicka sedan på src/Edge/config/deployment.customvision.amd64.jspå och välj **skapa distribution för en enskild enhet**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Skärm bild som visar skapa distribution för en enskild enhet.":::
1. Sedan uppmanas du att välja en IoT Hub enhet. Välj **lva-Sample-Device** i list rutan.
1. Om 30 sekunder uppdaterar du Azure IoT Hub i det nedre vänstra avsnittet. Du bör ha gräns enheten med följande moduler distribuerade:

    * Video analys i real tid på IoT Edge modul med namnet `lvaEdge` .
    * En modul med namnet `rtspsim` , som simulerar en RTSP-server som fungerar som källa till en Live-videofeed.
    * En modul med namnet `cv` , som namnet antyder, är den Custom vision leksakens identifierings modell som används Custom vision på avbildningarna och returnerar flera tag-typer. (Vår modell har tränats på endast en tagg, leverans bil.)

## <a name="prepare-for-monitoring-events"></a>Förbereda för övervaknings händelser

Högerklicka på Live Video Analytics-enheten och välj **starta övervakning inbyggd händelse slut punkt**. Du behöver det här steget för att övervaka IoT Hub händelser i fönstret **utdata** i Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Skärm bild som visar den inbyggda händelse slut punkten för start övervakning.":::

## <a name="run-the-sample-program"></a>Kör exempel programmet

Om du öppnar Graph-topologin för den här självstudien i en webbläsare ser du att värdet för `inferencingUrl` har angetts till `http://cv:80/image` . Den här inställningen innebär att en härlednings server returnerar resultat när du har identifierat eventuella leksaks truckar i Live-videon.

1. Öppna fliken **tillägg** i Visual Studio Code (eller Välj **CTRL + SHIFT + X**) och sök efter Azure-IoT Hub.
1. Högerklicka och välj Inställningar för **tillägg**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Skärm bild som visar inställningar för tillägg.":::
1. Sök och aktivera **Visa utförligt meddelande**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Skärm bild som visar Visa utförligt meddelande.":::
1. Om du vill starta en felsökningssession väljer du **F5** -tangenten. Du ser meddelanden som skrivs ut i **terminalfönstret** .
1. operations.jsvid kod börjar med anrop till direkta metoder `GraphTopologyList` och `GraphInstanceList` . Om du har rensat resurser efter att du har slutfört tidigare snabb starter, kommer den här processen att returnera tomma listor och sedan pausa. Fortsätt genom att välja **RETUR** nyckeln.
    
   **Terminalfönstret** visar nästa uppsättning med direkta metod anrop:
    
   * Ett anrop till `GraphTopologySet` som använder föregående `topologyUrl` .
   * Ett anrop till `GraphInstanceSet` som använder följande text:
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
   * Ett anrop till `GraphInstanceActivate` som startar graf-instansen och film flödet.
   * Ett andra anrop till `GraphInstanceList` som visar att graf-instansen är i körnings tillstånd.
    
1. Utdata i **terminalfönstret** pausas vid en **tryck RETUR för att fortsätta** prompten. Välj inte **RETUR** än. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder du anropade.
1. Växla till fönstret **utdata** i Visual Studio Code. Du ser meddelanden om att live video analys på IoT Edge modul skickas till IoT Hub. I följande avsnitt i den här självstudien beskrivs dessa meddelanden.
1. Medie diagrammet fortsätter att köra och skriva ut resultat. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa medie diagrammet går du tillbaka till **terminalfönstret** och väljer **RETUR**.
Nästa serie anrop rensar resurser:
    
   * Ett anrop för att `GraphInstanceDeactivate` inaktivera graf-instansen.
   * Ett anrop för att `GraphInstanceDelete` ta bort instansen.
   * Ett anrop för att `GraphTopologyDelete` ta bort topologin.
   * Ett sista anrop till `GraphTopologyList` visar att listan är tom.
    
## <a name="interpret-the-results"></a>Tolka resultatet

När du kör medie diagrammet passerar resultatet från noden för HTTP-tillägget processor genom noden IoT Hub mottagare till IoT Hub. De meddelanden som visas i **utdatafönstret** innehåller ett text avsnitt och ett `applicationProperties` avsnitt. Mer information finns i [skapa och läsa IoT Hub meddelanden](../../iot-hub/iot-hub-devguide-messages-construct.md).

I följande meddelanden definierar modulen live video analys program egenskaperna och innehållet i bröd texten.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse

När ett medie diagram instansieras försöker RTSP-Källnoden ansluta till RTSP-servern som körs i behållaren rtspsim-live555. Om anslutningen lyckas skrivs följande händelse ut. Händelse typen är **Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished**.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

Lägg märke till följande information i det här meddelandet:

* Meddelandet är en diagnostisk händelse. `MediaSessionEstablished` anger att noden RTSP-källa (ämnet) är ansluten med RTSP-simulatorn och har börjat ta emot en simulerad live-feed.
* I `applicationProperties` `subject` anger att meddelandet genererades från noden RTSP-källa i medie diagrammet.
* I `applicationProperties` anger händelse typen att händelsen är en diagnostisk händelse.
* Händelse tiden anger den tidpunkt då händelsen inträffade.
* Texten innehåller information om Diagnostics-händelsen. I det här fallet innehåller informationen information om [session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Händelsen härledning

Noden för HTTP-tilläggsbegäranden tar emot resultat från Custom Vision containern och genererar resultatet via noden IoT Hub mottagare som utöknings händelser.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Observera följande information i föregående meddelanden:

* Ämnet i `applicationProperties` refererar till noden i MediaGraph som meddelandet genererades från. I det här fallet kommer meddelandet från HTTP-tilläggs processorn.
* Händelse typen i `applicationProperties` anger att detta är en händelse för analys av en analys.
* Händelse tiden anger den tidpunkt då händelsen inträffade.
* Texten innehåller data om Analytics-händelsen. I det här fallet är händelsen en utfalls händelse, så texten innehåller en matris med inferences som kallas förutsägelser.
* Avsnittet förutsägelses innehåller en lista över förutsägelser där en leksaks Last bil (tag) påträffas i ramen. När du återkallar är "leverans Last bil" den anpassade tagg som du har angett för din anpassade utbildade modell för leksaks trucken. Inferences för modell och identifierar leksaks trucken i Indataporten med olika sannolikhets konfidens resultat.

## <a name="clean-up-resources"></a>Rensa resurser

Om du avser att testa de andra självstudierna eller snabb starterna, måste du hålla till de resurser som du har skapat. Annars går du till Azure Portal, bläddrar till resurs grupper, väljer den resurs grupp som du körde den här självstudien under och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

Granska ytterligare utmaningar för avancerade användare:

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) som har stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror som stöder RTSP på sidan [ONVIF](https://www.onvif.org/conformant-products/) -produkter. Sök efter enheter som uppfyller profilerna G, S eller T.
* Använd en AMD64-eller x64 Linux-enhet i stället för en virtuell Azure Linux-dator. Enheten måste finnas i samma nätverk som IP-kameran. Du kan följa anvisningarna i [installera Azure IoT Edge runtime på Linux](../../iot-edge/how-to-install-iot-edge.md).

Registrera sedan enheten med Azure IoT Hub genom att följa anvisningarna i [distribuera din första IoT Edge-modul till en virtuell Linux-enhet](../../iot-edge/quickstart-linux.md).
