---
title: Analysera direktsänd video med hjälp av Open,™ Model Server – AI-tillägg från Intel
description: I den här självstudien använder du en AI-modell server som tillhandahålls av Intel för att analysera direktsänd video från en (simulerad) IP-kamera.
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: db018c5c8d8f3990fd465f4d586ef4dc70980542
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955723"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>Självstudie: analysera direktsänd video med hjälp av poly™ Model Server – AI-tillägg från Intel 

I den här självstudien får du lära dig hur du använder det Open™ modell Server – AI-tillägget från Intel för att analysera ett direktuppspelat video flöde från en (simulerad) IP-kamera. Du ser hur den här härlednings servern ger dig åtkomst till modeller för att identifiera objekt (en person, ett fordon eller en cykel) och en modell för att klassificera fordon. En delmängd av bild rutorna i direktsänd video-feed skickas till den här härlednings servern och resultaten skickas till IoT Edge Hub.

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

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

I den här snabb starten ska du använda video analys i real tid för IoT Edge tillsammans med Open-Poly-™ modell Server – AI-tillägget från Intel för att identifiera objekt som fordon, eller för att klassificera dem. Du publicerar de resulterande härlednings händelserna till IoT Edge Hub.

## <a name="overview"></a>Översikt

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.svg" alt-text="Översikt":::

Det här diagrammet visar hur signal flödet i den här snabb starten. En [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulerar en IP-kamera som är värd för en RTSP-server (Real-Time Streaming Protocol). En [RTSP-källmapp](media-graph-concept.md#rtsp-source) hämtar video flödet från den här servern och skickar video ramar till noden [http-tilläggsbegäranden](media-graph-concept.md#http-extension-processor) . 

Noden HTTP-tillägg spelar rollen för en proxy. Det samplar de inkommande video bild rutorna som angetts av ditt `samplingOptions` fält och konverterar även video bild rutorna till den angivna bild typen. Sedan vidarebefordrar avbildningen över REST till en annan Edge-modul som kör AI-modeller bakom en HTTP-slutpunkt. I det här exemplet är Edge-modulen Open™ modell Server – AI-tillägget från Intel. Noden för HTTP-tilläggsbegäranden samlar in identifierings resultaten och publicerar händelser till noden [IoT Hub mottagare](media-graph-concept.md#iot-hub-message-sink) . Noden skickar sedan händelserna till [IoT Edge Hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

I de här självstudierna får du:

1. Skapa och distribuera medie diagrammet och ändra det.
1. Tolka resultatet.
1. Rensa resurser.

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>Om Open™ modell Server – AI-tillägg från Intel

Intel®-fördelningen av poly- [™ Toolkit](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (öppen visuell härledning och neurala nätverks optimering) är ett kostnads fritt program varu paket som hjälper utvecklare och data forskare att påskynda arbetet med arbets belastningar, effektivisera djup inlärning och distributioner och möjliggör enkel, heterogen körning av Intel®-plattformar från gränsen till molnet. Den innehåller Intel® djup inlärnings distributions verktyg med modell optimering och drift sättnings motor och den [öppna modellen Zoo](https://github.com/openvinotoolkit/open_model_zoo) -lagringsplatsen som innehåller mer än 40 optimerade förtränade modeller.

För att kunna bygga komplexa video analys lösningar med höga prestanda bör live video analys på IoT Edge modul kombineras med en kraftfull härlednings motor som kan dra nytta av skalan i kanten. I den här självstudien skickas en härlednings förfrågan till [open™ modell Server – AI-tillägget från Intel](https://aka.ms/lva-intel-ovms), en Edge-modul som har utformats för att fungera med real tids analys på IoT Edge. Den här modulen för den här härlednings servern innehåller™ modell Server (OVMS) för, en utgångs punkt som drivs av poly-™ Toolkit, som är mycket optimerad för arbets belastningar för dator vision och som utvecklats för Intel®-arkitekturer. Ett tillägg har lagts till i OVMS för enkel uppspelning av video ramar och utöknings resultat mellan härlednings servern och video analys i IoT Edge modul, vilket ger dig möjlighet att köra valfri modell för openprodukts-™ Toolkit som stöds (du kan anpassa modulen för härlednings Server genom att ändra [koden](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)). Du kan välja fler än en mängd olika accelerations funktioner som tillhandahålls av Intel®-maskinvara. Detta inkluderar CPU: er (Atom, Core, Xeon), FPGAs, VPUs.

I den första versionen av den här härlednings servern har du åtkomst till följande [modeller](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models):

- Fordons identifiering (härlednings-URL: http://{Modulnamn-Name}: 4000/vehicleDetection)
- Person/vehikel/cykel identifiering (URL för härledning: http://{modul-Name}: 4000/personVehicleBikeDetection)
- Fordons klassificering (härlednings-URL: http://{Modulnamn-Name}: 4000/vehicleClassification)
- Ansiktsigenkänning (härlednings-URL: http://{Modulnamn-Name}: 4000/faceDetection)

> [!NOTE]
> Genom att hämta och använda Edge-modulen: Open,™ modell Server – AI-tillägg från Intel och den inkluderade program varan godkänner du villkoren i [licens avtalet](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Intel värnar om att respektera de mänskliga rättigheterna och undvika Complicity i mänskliga rättigheter. Se [Intels globala principer för Human rättigheter](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Intels produkter och program vara är endast avsedda att användas i program som inte orsakar eller bidrar till överträdelse av en internationellt erkänd mänsklig rättighet.

## <a name="create-and-deploy-the-media-graph"></a>Skapa och distribuera medie diagrammet

### <a name="examine-and-edit-the-sample-files"></a>Granska och redigera exempelfilerna

Som en del av förutsättningarna hämtade du exempel koden till en mapp. Följ dessa steg om du vill granska och redigera exempelfilerna.

1. I Visual Studio Code går du till *src/Edge*. Du ser din *. kuvert* -fil och några mallar för distributionsmall.

    Distributions mal len refererar till distributions manifestet för gräns enheten. Den innehåller några värden för plats hållare. *. Miljö* filen innehåller värdena för variablerna.

1. Gå till mappen *src/Cloud-to-Device-console-app* . Här ser du *appsettings.jspå* filen och några andra filer:

    * ***C2D-console-app. CSPROJ** _-projekt filen för Visual Studio Code.
    _ ***operations.jspå** _ – en lista över de åtgärder som du vill att programmet ska köra.
    _ ***Program.cs** _ – exempel koden. Den här koden:

        _ Läser in appinställningar.
        * Anropar direkta metoder som visar IoT Edge modulen för video analys i real tid. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](direct-methods.md).
        * Pausar så att du kan granska programmets utdata i **terminalfönstret** och granska de händelser som har genererats av modulen i fönstret **utdata** .
        * Anropar direkta metoder för att rensa resurser.


1. Redigera *operations.jspå* filen:
    * Ändra länken till graf-topologin:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json"`

    * Under `GraphInstanceSet` redigerar du namnet på diagram sto pol Ogin så att det matchar värdet i föregående länk:

      `"topologyName" : "InferencingWithOpenVINO"`

    * Under `GraphTopologyDelete` redigerar du namnet:

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Skapa och distribuera distributions manifestet för IoT Edge

1. Högerklicka på filen *src/Edge/deployment.openvino.template.js* och välj sedan **skapa IoT Edge distributions manifest**.

    ![Generera IoT Edge distributions manifest](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    Filen *deployment.yolov3.amd64.jspå* manifest filen skapas i mappen *src/Edge/config* .

1. Hoppa över det här steget om du har slutfört snabb starten för att [identifiera rörelse och generera händelser](detect-motion-emit-events-quickstart.md) . 

    Annars, nära **Azure IoT Hub** -fönstret i det nedre vänstra hörnet, väljer du ikonen **fler åtgärder** och väljer sedan **Ange IoT Hub anslutnings sträng**. Du kan kopiera strängen från *appsettings.jsi* filen. Eller, för att se till att du har konfigurerat rätt IoT-hubb i Visual Studio Code, använder du [kommandot SELECT IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Ange IoT-hubbens anslutningssträng](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Du kan bli ombedd att ange information om inbyggd slut punkt för IoT Hub. Om du vill hämta den informationen går du till IoT Hub i Azure Portal och letar efter **inbyggda slut punkts** alternativ i det vänstra navigerings fönstret. Klicka där och leta efter den **Event Hub-kompatibla slut punkten** under avsnittet **händelsehubben-kompatibel slut punkt** . Kopiera och Använd texten i rutan. Slut punkten kommer att se ut ungefär så här:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Högerklicka på *src/Edge/config/deployment.openvino.amd64.jspå* och välj **skapa distribution för en enskild enhet**. 

    ![Skapa distribution för en enskild enhet](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. När du uppmanas att välja en IoT Hub-enhet väljer du **lva-Sample-Device**.
1. Efter cirka 30 sekunder, i det nedre vänstra hörnet i fönstret, uppdaterar du Azure IoT Hub. Gräns enheten visar nu följande distribuerade moduler:

    * Video analys-modulen i real tid med namnet **lvaEdge**
    * Modulen **rtspsim** , som simulerar en RTSP-server och fungerar som källa för en Live-videofeed
    * Modulen **Open-Modulnamn,** som är Open-modul™ modell Server – AI-Tilläggsprovider från Intel 

### <a name="prepare-to-monitor-events"></a>Förbereda övervakning av händelser

Högerklicka på Live Video Analytics-enheten och välj **starta övervakning inbyggd händelse slut punkt**. Du behöver det här steget för att övervaka IoT Hub händelser i fönstret **utdata** i Visual Studio Code. 

![Starta övervakning](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>Kör exempel programmet för att identifiera fordon
Om du öppnar [diagram sto pol Ogin](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) för den här självstudien i en webbläsare, ser du att värdet för `inferencingUrl` har angetts till `http://openvino:4000/vehicleDetection` , vilket innebär att en härlednings server returnerar resultat när du har identifierat fordon, om det finns några, i Live-videon.

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
             "topologyName": "InferencingWithOpenVINO",
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
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
  }
}
```

Lägg märke till följande information i det här meddelandet:

* Meddelandet är en diagnostisk händelse. `MediaSessionEstablished` anger att RTSP-Källnoden (ämnet) är ansluten med RTSP-simulatorn och har börjat ta emot en (simulerad) live-feed.
* I `applicationProperties` `subject` anger att meddelandet genererades från noden RTSP-källa i medie diagrammet.
* I `applicationProperties` `eventType` anger att den här händelsen är en diagnostisk händelse.
* `eventTime`Anger den tidpunkt då händelsen inträffade.
* `body`Innehåller data om Diagnostics-händelsen. I det här fallet innehåller informationen information om [session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Händelsen härledning

Noden för HTTP-tilläggsbegäranden tar emot utöknings resultat från Open™ modell Server – AI-Tilläggsprovider. Den genererar sedan resultaten genom IoT Hub Sink-noden som utöknings händelser. 

I dessa händelser anges typen till `entity` för att ange att den är en enhet, till exempel en bil eller Truck. `eventTime`Värdet är UTC-tiden då objektet identifierades. 

I följande exempel upptäcktes två fordon, med ett konfidens värde över 0,9.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:43:18.1280000Z"
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
Om du vill använda en annan modell måste du ändra grafens topologi och `operations.json` fil.

Kopiera [graf-topologin](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) till en lokal fil, t `C:\TEMP\topology.json` . ex.. Öppna kopian och redigera värdet för `inferencingUrl` till `http://openvino:4000/personVehicleBikeDetection` .

Gå sedan till mappen *src/Cloud-to-Device-console-app* och öppna fil i Visual Studio Code `operations.json` . Redigera raden med `topologyUrl` till:

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
Nu kan du upprepa stegen ovan för att köra exempel programmet igen med den nya topologin. Utöknings resultatet är liknande (i schema) till det för fordons identifierings modellen, med bara `subtype` inställt på `personVehicleBikeDetection` .

## <a name="run-the-sample-program-to-classify-vehicles"></a>Kör exempel programmet för att klassificera fordon
Öppna den lokala kopian av från föregående steg i Visual Studio Code `topology.json` och redigera värdet för `inferencingUrl` till `http://openvino:4000/vehicleClassification` . Om du har kört föregående exempel för att identifiera personer eller bilar, behöver du inte ändra `operations.json` filen igen.

Nu kan du upprepa stegen ovan för att köra exempel programmet igen med den nya topologin. Ett exempel på klassificerings resultat är följande.

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>Kör exempel programmet för att identifiera ansikten
Öppna den lokala kopian av från föregående steg i Visual Studio Code `topology.json` och redigera värdet för `inferencingUrl` till `http://openvino:4000/faceDetection` . Om du har kört föregående exempel för att identifiera personer eller bilar, behöver du inte ändra `operations.json` filen igen.

Nu kan du upprepa stegen ovan för att köra exempel programmet igen med den nya topologin. Ett exempel på identifierings resultat är följande (Obs! det parkerings parti video som används ovan innehåller inte några identifierade ansikten – du bör ha en annan video för att testa den här modellen).

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker prova andra snabb starter eller självstudier ska du behålla de resurser du har skapat. Annars går du till Azure Portal, går till dina resurs grupper, väljer den resurs grupp där du körde den här självstudien och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

Granska ytterligare utmaningar för avancerade användare:

* Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) som har stöd för RTSP i stället för att använda RTSP-simulatorn. Du kan söka efter IP-kameror som stöder RTSP på sidan [ONVIF](https://www.onvif.org/conformant-products/) -produkter. Sök efter enheter som uppfyller profilerna G, S eller T.
* Använd en AMD64-eller x64 Linux-enhet i stället för en virtuell Azure Linux-dator. Enheten måste finnas i samma nätverk som IP-kameran. Du kan följa anvisningarna i [installera Azure IoT Edge runtime på Linux](../../iot-edge/how-to-install-iot-edge.md). Registrera sedan enheten med Azure IoT Hub genom att följa anvisningarna i [distribuera din första IoT Edge-modul till en virtuell Linux-enhet](../../iot-edge/quickstart-linux.md).