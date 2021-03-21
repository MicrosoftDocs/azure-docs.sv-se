---
title: Analysera direktsänd video genom att använda Intel Open™ DL-strömmar – Edge AI-tillägg via gRPC
description: I den här självstudien får du lära dig att använda Intel Open-™ DL streamer – Edge AI-tillägget från Intel för att analysera ett direktuppspelat video flöde från en (simulerad) IP-kamera.
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: 20a22d11973f5bb01e2c1345538d5d94ce311dc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465767"
---
# <a name="tutorial-analyze-live-video-by-using-intel-openvino-dl-streamer--edge-ai-extension"></a>Självstudie: analysera direktsänd video genom att använda Intel Open™ DL-dataströmmar – Edge AI-tillägg 

I den här självstudien får du lära dig att använda Intel Open-™ DL streamer – Edge AI-tillägget från Intel för att analysera ett direktuppspelat video flöde från en (simulerad) IP-kamera. Du ser hur den här härlednings servern ger dig åtkomst till olika modeller för att identifiera objekt (en person, ett fordon eller en cykel), objekt klassificering (fordons egenskaper) och en modell för objekt spårning (person, fordon och cykel). Med modulen gRPC kan du skicka video bild rutor till AI-servern för AI. Resultaten skickas sedan till IoT Edge Hub. När du kör den här härlednings tjänsten på samma Compute-nod som live video analys kan du dra nytta av att skicka video data via delat minne. På så sätt kan du köra inferencing med bild frekvensen för Live-videofeeden (t. ex. 30 bild rutor per sekund). 

Den här självstudien använder en virtuell Azure-dator som en IoT Edge enhet och använder en simulerad direktuppspelad video ström. Den baseras på exempel kod som skrivits i C# och bygger på snabb starten för att [identifiera rörelse och generera händelser](detect-motion-emit-events-quickstart.md) .

> [!NOTE]
> Den här självstudien kräver att en x86-64-dator används som din gräns enhet.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto som innehåller en aktiv prenumeration. [Skapa ett konto utan kostnad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) om du inte redan har ett.
  > [!NOTE]
  > Du behöver en Azure-prenumeration med behörigheter för att skapa tjänstens huvud namn (**ägar rollen** ger detta). Om du inte har rätt behörighet kan du kontakta konto administratören för att ge dig rätt behörigheter. 
* [Visual Studio Code](https://code.visualstudio.com/)med följande tillägg:
    * [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Om du inte slutför snabb starten för att [identifiera rörelse-och genererar händelser](detect-motion-emit-events-quickstart.md) måste du slutföra stegen för att [Konfigurera Azure-resurser](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> När du installerar Azure IoT-verktyg kan du uppmanas att installera Docker. Du kan ignorera prompten.

## <a name="review-the-sample-video"></a>Granska exempel videon

När du konfigurerar Azure-resurserna kopieras en kort video från ett parkerings parti till den virtuella Linux-datorn i Azure som du använder som IoT Edge enhet. I den här snabb starten används video filen för att simulera en Live-dataström.

Öppna ett program som [VLC Media Player](https://www.videolan.org/vlc/). Välj CTRL + N och klistra sedan in en länk till [videon](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) för att starta uppspelningen. Du ser tagningarna av fordon i ett parkerings parti, de flesta av dem parkerade och ett flyttal.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

I den här snabb starten använder du video analys i real tid för IoT Edge tillsammans med Intel Open,™ DL-dataströmer – Edge AI-tillägget från Intel för att identifiera objekt, till exempel fordon, för att klassificera fordon eller spåra fordon, personer eller cyklar. Du publicerar de resulterande härlednings händelserna till IoT Edge Hub.

## <a name="overview"></a>Översikt

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="Översikt över LVA-MediaGraph":::

Det här diagrammet visar hur signal flödet i den här snabb starten. En [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulerar en IP-kamera som är värd för en RTSP-server (Real-Time Streaming Protocol). En [RTSP-källmapp](media-graph-concept.md#rtsp-source) hämtar video flödet från den här servern och skickar video ramar till gRPC- [tillägget](media-graph-concept.md#grpc-extension-processor) . 

GRPC Extension processor-noden tar avkodade video ramar som inmatade och vidarebefordrar sådana ramar till en [gRPC](terminology.md#grpc) -slutpunkt som exponeras av en GRPC-Server. Noden stöder överföring av data med hjälp av [delat minne](https://en.wikipedia.org/wiki/Shared_memory) eller bädda in innehållet direkt i bröd texten i gRPC-meddelanden. Dessutom har noden en inbyggd avbildnings-Formatter för skalning och kodning av video bild rutor innan de vidarebefordras till gRPC-slutpunkten. Skalaren har alternativ för bild-höjd-förhållandet som ska bevaras, utfylls eller sträckas ut. Bild kodaren stöder JPEG-, PNG-eller BMP-format. Läs mer om processorn [här](media-graph-extension-concept.md#grpc-extension-processor).

I de här självstudierna får du:

1. Distribuera medie diagrammet.
1. Tolka resultatet.
1. Rensa resurser.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Om Intel openprodukt-™ DL-streamer – Edge AI-Tilläggsprovider


™ DL Stream-modulen för DL-Stream är en mikrotjänst baserad på Intels video analys (VA-hantering) som hanterar pipeline för video analys som skapats med openmodulnamn™ DL Stream. Utvecklare kan skicka avkodade video bild rutor till AI-tilläggsprovider som utför identifiering, klassificering eller spårning och returnerar resultatet. Modulen AI-tillägg visar gRPC-API: er som är kompatibla med video analys plattformar som direktsända video analyser på IoT Edge från Microsoft. 

För att kunna bygga komplexa video analys lösningar med höga prestanda bör live video analys på IoT Edge modul kombineras med en kraftfull härlednings motor som kan dra nytta av skalan i kanten. I den här självstudien skickas en härlednings förfrågan till [tillägget Intel open™ dl-streamer – Edge AI](https://aka.ms/lva-intel-openvino-dl-streamer), en Edge-modul som har utformats för att fungera med real tids analys på IoT Edge. 

I den första versionen av den här härlednings servern har du åtkomst till följande [modeller](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options):

- object_detection för att ![ identifiera person_vehicle_bike_detection objekt för fordon](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification för vehicle_attributes_recognition ![ objekt klassificering för fordon](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking för att person_vehicle_bike_tracking ![ objekt spårning för person fordon](./media/use-intel-openvino-tutorial/object-tracking.png)

Den använder förinlästa objekt identifiering, objekt klassificering och pipeliner för objekt spårning för att komma igång snabbt. Dessutom ingår den förinstallerade [person-vehikel-Bike-Detection-Crossroad-0078-](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/description/person-vehicle-bike-detection-crossroad-0078.md) och [vehikel-barriär-0039-modeller](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/description/vehicle-attributes-recognition-barrier-0039.md).

> [!NOTE]
> Genom att ladda ned och använda Edge-modulen: Poly™ dl streamer – Edge AI-tillägg från Intel och den medföljande program varan godkänner du villkoren i [licens avtalet](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Intel värnar om att respektera de mänskliga rättigheterna och undvika Complicity i mänskliga rättigheter. Se [Intels globala principer för Human rättigheter](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Intels produkter och program vara är endast avsedda att användas i program som inte orsakar eller bidrar till överträdelse av en internationellt erkänd mänsklig rättighet.

Du kan använda flexibiliteten för de olika pipelinerna för ditt speciella användnings fall genom att helt enkelt ändra miljö variablerna för pipelinen i distributions mal len. På så sätt kan du snabbt ändra Pipeline-modellen och när den kombineras med live video analys är det en del av några sekunder att ändra medie pipelinen och en utgångs modell.  

## <a name="create-and-deploy-the-media-graph"></a>Skapa och distribuera medie diagrammet

### <a name="examine-and-edit-the-sample-files"></a>Granska och redigera exempelfilerna

Som en del av förutsättningarna hämtade du exempel koden till en mapp. Följ dessa steg om du vill granska och redigera exempelfilerna.

1. I Visual Studio Code går du till *src/Edge*. Du ser din *. kuvert* -fil och några mallar för distributionsmall.

    Distributions mal len refererar till distributions manifestet för gräns enheten. Den innehåller några värden för plats hållare. *. Miljö* filen innehåller värdena för variablerna.

1. Gå till mappen *src/Cloud-to-Device-console-app* . Här ser du *appsettings.jspå* filen och några andra filer:

    * ***C2D-console-app. CSPROJ*** – projekt filen för Visual Studio Code.
    * ***operations.js*** en lista över de åtgärder som du vill att programmet ska köra.
    * ***Program. cs*** -kod för exempel program. Den här koden:

        * Läser in appinställningar.
        * Anropar direkta metoder som visar IoT Edge modulen för video analys i real tid. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](direct-methods.md).
        * Pausar så att du kan granska programmets utdata i **terminalfönstret** och granska de händelser som har genererats av modulen i fönstret **utdata** .
        * Anropar direkta metoder för att rensa resurser.


1. Redigera *operations.jspå* filen:
    * Ändra länken till graf-topologin:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * Under `GraphInstanceSet` redigerar du namnet på diagram sto pol Ogin så att det matchar värdet i föregående länk:

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * Under `GraphTopologyDelete` redigerar du namnet:

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Skapa och distribuera distributions manifestet för IoT Edge

1. Högerklicka på filen *src/Edge/deployment.openvino.grpc.cpu.template.js* och välj sedan **skapa IoT Edge distributions manifest**.

    ![Generera IoT Edge distributions manifest](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    Filen *deployment.openvino.grpc.cpu.amd64.jspå* manifest filen skapas i mappen *src/Edge/config* .

> [!NOTE]
> Vi har också lagt *till endeployment.openvino.grpc.gpu.template.jspå* en mall som möjliggör GPU-stöd för en Stream-modul för AI-tillägg för Intel openprodukts dl. Dessa mallar pekar på Intels Docker Hub-avbildning.

De ovan nämnda mallarna pekar på Intel Docker Hub-avbildningen. Om du hellre vill vara värd för en kopia på din egen Azure Container Registry kan du följa steg 1 och 2 nedan:
1. SSH till en enhet med Docker CLI-verktyg installerade (d.v.s. din Edge-enhet) och hämta/tagga/skicka behållaren med följande steg:
    * Hämta Intel-avbildningen från Docker Hub:

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Tagga Intel-avbildningen med ditt eget Azure Container Registry namn. Ersätt {ditt ACR namn} med ditt ACR-namn som du hittar i. miljö filen:

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * Skicka den taggade bilden till din Azure Container Registry:

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. Nu måste du redigera mallarna för att referera till den nya avbildningen som finns på Azure Container Registry.
    * Högerklicka på *deployment.openvino.grpc.cpu.template.js* och navigera till *lavExtension* -modulens del och Ersätt:

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        med:

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * Upprepa steg 2 för *deployment.openvino.grpc.gpu.template.jspå*


3. Hoppa över det här steget om du har slutfört snabb starten för att [identifiera rörelse och generera händelser](detect-motion-emit-events-quickstart.md) . 

    Annars, nära **Azure IoT Hub** -fönstret i det nedre vänstra hörnet, väljer du ikonen **fler åtgärder** och väljer sedan **Ange IoT Hub anslutnings sträng**. Du kan kopiera strängen från *appsettings.jsi* filen. Eller, för att se till att du har konfigurerat rätt IoT-hubb i Visual Studio Code, använder du [kommandot SELECT IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Ange IoT-hubbens anslutningssträng](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Du kan bli ombedd att ange information om inbyggd slut punkt för IoT Hub. Om du vill hämta den informationen går du till IoT Hub i Azure Portal och letar efter **inbyggda slut punkts** alternativ i det vänstra navigerings fönstret. Klicka där och leta efter den **Event Hub-kompatibla slut punkten** under avsnittet **händelsehubben-kompatibel slut punkt** . Kopiera och Använd texten i rutan. Slut punkten kommer att se ut ungefär så här:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Högerklicka på *src/Edge/config/deployment.openvino.grpc.cpu.template.jspå* och välj **skapa distribution för en enskild enhet**. 

    ![Skapa distribution för en enskild enhet](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. När du uppmanas att välja en IoT Hub-enhet väljer du **lva-Sample-Device**.
1. Efter cirka 30 sekunder, i det nedre vänstra hörnet i fönstret, uppdaterar du Azure IoT Hub. Gräns enheten visar nu följande distribuerade moduler:

    * Video analys-modulen i real tid med namnet **lvaEdge**
    * Modulen **rtspsim** , som simulerar en RTSP-server och fungerar som källa för en Live-videofeed
    * **LvaExtension** -modulen, som är intels Open™ dl-dataströmmar – Edge AI-tillägg 

### <a name="prepare-to-monitor-events"></a>Förbereda övervakning av händelser

Högerklicka på Live Video Analytics-enheten och välj **starta övervakning inbyggd händelse slut punkt**. Du behöver det här steget för att övervaka IoT Hub händelser i fönstret **utdata** i Visual Studio Code. 

![Starta övervakning](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Kör exempel programmet för att identifiera fordon, personer eller cykel
Om du öppnar [Graph-topologin](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) för den här självstudien i en webbläsare, ser du att värdet för `grpcExtensionAddress` har angetts till `tcp://lvaExtension:5001` , jämfört med *httpExtensionOpenVINO* -exemplet att du inte behöver ändra URL: en till gRPC-servern. I stället instruerar du modulen att köra en speciell pipeline av miljövariablerna som nämnts tidigare. I standardmallen har vi angett detta som: "object_detection" för "person_vehicle_bike_detection". Du kan experimentera med andra pipeliner som stöds. 

1. Öppna fliken **tillägg** i Visual Studio Code (eller tryck på Ctrl + Shift + X) och Sök efter Azure-IoT Hub.
1. Högerklicka och välj **Inställningar för tillägg**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Tilläggs inställningar":::
1. Sök och aktivera "Visa utförligt meddelande".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Visa utförligt meddelande":::
1. Om du vill starta en felsökningssession väljer du F5-tangenten. Du ser meddelanden som skrivs ut i **terminalfönstret** .
1. *operations.jsvid* kod börjar med anrop till direkta metoder `GraphTopologyList` och `GraphInstanceList` . Om du har rensat resurser efter att du har slutfört tidigare snabb starter, returnerar den här processen tomma listor och pausar sedan. Fortsätt genom att välja retur nyckeln.

    **Terminalfönstret** visar nästa uppsättning med direkta metod anrop:

     * Ett anrop till `GraphTopologySet` som använder föregående `topologyUrl`
     * Ett anrop till `GraphInstanceSet` som använder följande text:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINOgRPC",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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

     * Ett anrop till `GraphInstanceActivate` som startar graf-instansen och video flödet
     * Ett andra anrop till `GraphInstanceList` som visar att graf-instansen är i körnings tillstånd
1. Utdata i **terminalfönstret** pausas vid en `Press Enter to continue` prompt. Välj inte retur än. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder du anropade.
1. Växla till fönstret **utdata** i Visual Studio Code. Du ser meddelanden om att live video analys på IoT Edge modul skickas till IoT Hub. I följande avsnitt i den här snabb starten beskrivs dessa meddelanden.
1. Medie diagrammet fortsätter att köra och skriva ut resultat. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa medie diagrammet går du tillbaka till **terminalfönstret** och väljer RETUR. 

    Nästa serie anrop rensar resurser:
      * Ett anrop för att `GraphInstanceDeactivate` inaktivera graf-instansen.
      * Ett anrop för att `GraphInstanceDelete` ta bort instansen.
      * Ett anrop för att `GraphTopologyDelete` ta bort topologin.
      * Ett sista anrop till `GraphTopologyList` visar att listan är tom.

## <a name="interpret-results"></a>Tolka resultaten

När du kör medie diagrammet passerar resultatet från noden för HTTP-tillägget processor genom noden IoT Hub mottagare till IoT Hub. De meddelanden som visas i fönstret **utdata** innehåller ett `body` avsnitt och ett `applicationProperties` avsnitt. Mer information finns i [skapa och läsa IoT Hub meddelanden](../../iot-hub/iot-hub-devguide-messages-construct.md).

I följande meddelanden definierar modulen live video analys program egenskaperna och innehållet i bröd texten. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-händelse

När ett medie diagram instansieras försöker RTSP-Källnoden ansluta till RTSP-servern som körs i behållaren rtspsim-live555. Om anslutningen lyckas skrivs följande händelse ut. Händelse typen är **Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "sdp": "SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

Lägg märke till följande information i det här meddelandet:

* Meddelandet är en diagnostisk händelse. `MediaSessionEstablished` anger att RTSP-Källnoden (ämnet) är ansluten med RTSP-simulatorn och har börjat ta emot en (simulerad) live-feed.
* I `applicationProperties` `subject` anger att meddelandet genererades från noden RTSP-källa i medie diagrammet.
* I `applicationProperties` `eventType` anger att den här händelsen är en diagnostisk händelse.
* `eventTime`Anger den tidpunkt då händelsen inträffade.
* `body`Innehåller data om Diagnostics-händelsen. I det här fallet innehåller informationen information om [session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Händelsen härledning

GRPC Extension processor-noden tar emot utöknings resultat från Intel Open™ DL-dataströmer – Edge AI-tillägget. Den genererar sedan resultaten genom IoT Hub Sink-noden som utöknings händelser. 

I dessa händelser anges typen till `entity` för att ange att den är en enhet, till exempel en bil eller Truck. `eventTime`Värdet är UTC-tiden då objektet identifierades. 

I följande exempel ser du att den har identifierat ett fordon, fordons typ (van) och färg (vit), allt med en konfidensnivå över 0,9, som också har tilldelats ett ID till entiteten när vi använder objekt spårnings modellen.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id": "1"
      }
    }
}
```

Observera följande information i meddelandena:

* I `applicationProperties` `subject` refererar den nod i den graf-topologi från vilken meddelandet genererades. 
* I `applicationProperties` `eventType` anger att den här händelsen är en analys händelse.
* `eventTime`Värdet är den tidpunkt då händelsen inträffade.
* `body`Avsnittet innehåller information om Analytics-händelsen. I det här fallet är händelsen en utmatnings händelse, så att texten innehåller `inferences` data.
* `inferences`Avsnittet visar att `type` är `entity` . Det här avsnittet innehåller ytterligare information om entiteten.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Kör exempel programmet för att identifiera personer eller fordon eller cyklar
Om du vill använda en annan modell måste du ändra distributions mal len. Om du vill växla mellan modeller som stöds kan du ändra miljövariablerna som finns i lvaExtenstion-modulen. Se det här [dokumentet på GitHub](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) för de värden och kombinationer som stöds för modeller.

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP]
> Kopiera mallen och lagra den under ett nytt namn för varje möjlig pipeline. På så sätt kan du växla mellan modeller genom att skapa en ny distribution baserat på en av dessa mallar.

När du har ändrat variablerna kan du distribuera mallen igen till enheten. Nu kan du upprepa stegen ovan för att köra exempel programmet igen, med den nya pipelinen. Utöknings resultatet är liknande (i schema) men visar mer eller mindre information beroende på den pipeline-modell som du har valt.

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker prova andra snabb starter eller självstudier ska du behålla de resurser du har skapat. Annars går du till Azure Portal, går till dina resurs grupper, väljer den resurs grupp där du körde den här självstudien och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

Granska ytterligare utmaningar för avancerade användare:

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) som har stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror som stöder RTSP på sidan [ONVIF](https://www.onvif.org/conformant-products/) -produkter. Sök efter enheter som uppfyller profilerna G, S eller T.
* Använd en Intel x64 Linux-enhet i stället för en virtuell Azure Linux-dator. Enheten måste finnas i samma nätverk som IP-kameran. Du kan följa anvisningarna i [installera Azure IoT Edge runtime på Linux](../../iot-edge/how-to-install-iot-edge.md). Registrera sedan enheten med Azure IoT Hub genom att följa anvisningarna i [distribuera din första IoT Edge-modul till en virtuell Linux-enhet](../../iot-edge/quickstart-linux.md).
