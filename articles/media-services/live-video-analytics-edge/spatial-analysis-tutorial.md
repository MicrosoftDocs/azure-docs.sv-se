---
title: Analysera direktsänd video med Visuellt innehåll för rums analys – Azure
description: Den här självstudien visar hur du använder real tids analys tillsammans med Visuellt innehåll AI-funktionen för spatial analys från Azure Cognitive Services för att analysera en Live-videofeed från en (simulerad) IP-kamera.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 1c6fe6e10a91034d794437f31d495b85ef086848
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632950"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Analysera direktsänd video med Visuellt innehåll för rums analys (för hands version)

I den här självstudien får du se hur du använder real tids analys tillsammans med [visuellt innehåll för AI-tjänsten för spatial analys från Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/computer-vision/) för att analysera en Live-videofeed från en (simulerad) IP-kamera. Du kommer att se hur den här härlednings servern kan användas för att analysera den strömmande videon för att förstå olika avstånd mellan personer och rörelser i det fysiska utrymmet.  En delmängd av bild rutorna i video matningen skickas till den här härlednings servern och resultaten skickas till IoT Edge hubben och när vissa villkor uppfylls registreras videoklipp och lagras som Azure Media Services till gångar.

I den här självstudien kommer du att:

> [!div class="checklist"]
> * Konfigurera resurser.
> * Granska koden.
> * Kör exempel koden.
> * Övervaka händelser.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
  > [!NOTE]
  > Du behöver en Azure-prenumeration med behörigheter för att skapa tjänstens huvud namn (**ägar rollen** ger detta). Om du inte har rätt behörighet kan du kontakta konto administratören för att ge dig rätt behörigheter. 
## <a name="suggested-pre-reading"></a>Föreslagen för läsning

Läs de här artiklarna innan du börjar:

* [Real tids analys av video i IoT Edge översikt](overview.md)
* [Video analys i real tid med IoT Edge terminologi](terminology.md)
* [Media Graph-begrepp](media-graph-concept.md)
* [Händelsebaserad videoinspelning](event-based-video-recording-concept.md)
* [Självstudie: utveckla en IoT Edge-modul](../../iot-edge/tutorial-develop-for-linux.md)
* [Distribuera video analys i real tid på Azure Stack Edge](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Förutsättningar

Följande är förutsättningar för att ansluta modulen för spatial analys till modulen för video analys i real tid.

* [Visual Studio Code](https://code.visualstudio.com/) på din utvecklings dator. Kontrol lera att du har [tillägget Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
    * Kontrol lera att nätverket som utvecklings datorn är anslutet till tillåter Avancerat meddelandekö-protokoll över Port 5671. Med den här inställningen kan Azure IoT-verktyg kommunicera med Azure IoT Hub.
* [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) med GPU-acceleration.  
    Vi rekommenderar att du använder Azure Stack Edge med GPU-acceleration, men behållaren körs på andra enheter med en [NVIDIA Tesla T4-GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/). 
* [Azure kognitiva Service visuellt innehåll behållare](https://azure.microsoft.com/services/cognitive-services/computer-vision/) för rums analys.  
    För att du ska kunna använda den här behållaren måste du ha en Visuellt innehåll-resurs för att hämta den associerade **API-nyckeln** och en **slut punkts-URI**. API-nyckeln finns på Azure Portal Visuellt innehåll översikt och nycklar sidor. Nyckeln och slut punkten krävs för att starta behållaren.

## <a name="overview"></a>Översikt

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Översikt över rums analys":::
 
Det här diagrammet visar hur signal flödet i den här självstudien. En [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulerar en IP-kamera som är värd för en RTSP-server (Real-Time Streaming Protocol). En [RTSP-källmapp](media-graph-concept.md#rtsp-source) hämtar video flödet från den här servern och skickar video ramar till `MediaGraphCognitiveServicesVisionExtension` noden processor.

MediaGraphCognitiveServicesVisionExtension-noden spelar rollen för en proxy. Den konverterar video bild rutorna till den angivna bild typen. Sedan vidarebefordrar den avbildningen över **delat minne** till en annan Edge-modul som kör AI-åtgärder bakom en gRPC-slutpunkt. I det här exemplet är modulen för spatial analys. MediaGraphCognitiveServicesVisionExtension processor-noden gör två saker:

* Den samlar in resultaten och publicerar händelser till noden [IoT Hub mottagare](media-graph-concept.md#iot-hub-message-sink) . Noden skickar sedan händelserna till [IoT Edge Hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). 
* Det fångar också ett 30 sekunders videoklipp från RTSP-källan med en [signal grind processor](media-graph-concept.md#signal-gate-processor) och lagrar den som en Media Services till gång.

## <a name="create-the-computer-vision-resource"></a>Skapa Visuellt innehåll resursen

Du måste skapa en Azure-resurs av typen Visuellt innehåll antingen på [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) eller via Azure CLI. Du kommer att kunna skapa resursen när din begäran om åtkomst till behållaren har godkänts och ditt Azure-prenumerations-ID har registrerats. Gå till https://aka.ms/csgate för att skicka in ditt användnings fall och ditt Azure-prenumerations-ID.  Du måste skapa Azure-resursen med samma Azure-prenumeration som har angetts i formuläret för begäran om åtkomst.

### <a name="gathering-required-parameters"></a>Nödvändiga parametrar samlas in

Det finns tre primära parametrar för alla Cognitive Services behållare som krävs, inklusive behållaren för spatial analys. Licens avtalet för slutanvändare (EULA) måste finnas med värdet accept. Dessutom behövs både en slut punkts-URI och API-nyckel.

### <a name="keys-and-endpoint-uri"></a>Nycklar och slut punkts-URI

En nyckel används för att starta behållaren för spatial analys och är tillgänglig på Azure Portalens `Keys and Endpoint` sida för motsvarande kognitiva tjänst resurs. Navigera till sidan och hitta nycklarna och slut punkts-URI: n.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Slut punkts-URI":::

## <a name="set-up-azure-stack-edge"></a>Konfigurera Azure Stack Edge

Följ [dessa steg](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) för att ställa in Azure Stack Edge och fortsätta följa stegen nedan för att distribuera video analys och moduler för spatial analys.

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

1. Klona lagrings platsen från den här platsen: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Öppna mappen där lagrings platsen har hämtats i Visual Studio Code.
1. I Visual Studio Code går du till mappen src/Cloud-to-Device-console-app. Där skapar du en fil och namnger den appsettings.js. Den här filen innehåller de inställningar som krävs för att köra programmet.
1. Hämta IotHubConnectionString från Azure Stack Edge genom att följa dessa steg:

    * Gå till IoT Hub i Azure Portal och klicka på `Shared access policies` i det vänstra navigerings fönstret.
    * Klicka på `iothubowner` Hämta nycklar för delad åtkomst.
    * Kopiera  `Connection String – primary key` och klistra in den i rutan inmatare på VSCode.
    
        Anslutnings strängen ser ut så här: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Kopiera innehållet nedan till filen. Se till att du ersätter variablerna.
    
    ```json
    {
        "IoThubConnectionString" : "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Gå till mappen src/Edge och skapa en fil med namnet. kuvert.
1. Kopiera innehållet i/clouddrive/lva-Sample/Edge-Deployment/.env-filen. Texten bör se ut som i följande kod.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Konfigurera distributions mal len  

Sök efter distributions filen i/src/Edge/deployment.spatialAnalysis.template.jspå. Från mallen finns det lvaEdge-modul, rtspsim-modul och vår spatial Analysis-modul.

Det finns några saker som du måste tänka på när du ska betala i mallen för distributions mal len:

1. Ange port bindning.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. `IpcMode` i lvaEdge-och spatial Analysis-modulen createOptions ska vara samma och inställd på Host.
1. Se till att du har konfigurerat volym gränserna för att RTSP-simulatorn ska fungera. Mer information finns i [Konfigurera Docker volym monteringar](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [Anslut till SMB-resursen](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) och kopiera [exempel filen bulldozer video](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) till den lokala resursen.  
        > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Mesi]  
    1. Se att rtspsim-modulen har följande konfiguration:
        ```
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
        

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generera och distribuera distributions manifestet

Distributions manifestet definierar vilka moduler som distribueras till en Edge-enhet. Den definierar också konfigurations inställningar för dessa moduler.

Följ de här stegen för att generera manifestet från mallfilen och distribuera den sedan till gräns enheten.

1. Öppna Visual Studio Code.
1. Bredvid fönstret AZURE IOT HUB väljer du ikonen fler åtgärder för att ange IoT Hub anslutnings sträng. Du kan kopiera strängen från `src/cloud-to-device-console-app/appsettings.json` filen.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Rums analys: anslutnings sträng":::
1. Högerklicka `src/edge/deployment.spatialAnalysis.template.json` och välj generera IoT Edge distributions manifest.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Rums analys: distribution av amd64 JSON":::
    
    Den här åtgärden ska skapa en manifest fil med namnet deployment.amd64.jspå i mappen src/Edge/config.
1. Högerklicka `src/edge/config/deployment.spatialAnalysis.amd64.json` , Välj Skapa distribution för en enskild enhet och välj sedan namnet på din Edge-enhet.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Rums analys: distributions mal Lav JSON":::   
1. När du uppmanas att välja en IoT Hub enhet väljer du Azure Stack kant namn på den nedrullningsbara menyn.
1. Efter cirka 30 sekunder, i det nedre vänstra hörnet i fönstret, uppdaterar du Azure IoT Hub. Gräns enheten visar nu följande distribuerade moduler:
    
    * Video analys i real tid för IoT Edge (Modulnamn lvaEdge).
    * RTSP-Simulator (Real-Time Streaming Protocol) (Modulnamn rtspsim).
    * Rums analys (Modulnamn spatialAnalysis).
    
Om du distribuerar utan problem visas ett meddelande som ser ut så här:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Sedan kan du söka `lvaEdge` efter `rtspsim` , `spatialAnalysis` och `rtspsim` moduler under enheter/moduler och deras status ska vara "körs".

## <a name="prepare-to-monitor-events"></a>Förbereda övervakning av händelser

Följ dessa steg om du vill se de här händelserna:

1. Öppna fliken **tillägg** i Visual Studio Code (eller tryck på Ctrl + Shift + X) och Sök efter Azure-IoT Hub.
1. Högerklicka och välj Inställningar för **tillägg**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Tilläggs inställningar":::
1. Sök och aktivera "Visa utförligt meddelande".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Visa utförligt meddelande":::
1. Öppna fönstret Utforskaren och leta efter Azure-IoT Hub i det nedre vänstra hörnet.
1. Expandera noden enheter.
1. Högerklicka på Azure Stack Edge och välj Starta övervakning inbyggd händelse slut punkt.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Rums analys: starta övervakning":::
     
## <a name="run-the-program"></a>Köra programmet

Det finns en program.cs som kommer att anropa direkta metoder i src/Cloud-to-Device-console-app/operations.jspå. Vi måste konfigurera operations.jspå och tillhandahålla en topologi för användning av Media diagram.  

I operations.jspå:

* Ange topologin så här:

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyUrl": "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json"
    }
},
```

* Skapa diagram förekomst som detta, ange parametrarna i topologin här:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
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
},
```

>[!Note]
Ta en titt på användningen av MediaGraphRealTimeComputerVisionExtension för att ansluta till modulen för spatial analys. Ange $ {grpcUrl} som **TCP://spatialAnalysis: <PORT_NUMBER>**, t. ex. TCP://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
        "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
        "url": "${grpcUrl}",
        "credentials": {
            "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
            "username": "${spatialanalysisusername}",
            "password": "${spatialanalysispassword}"
        }
    },
    "image": {
        "scale": {
            "mode": "pad",
            "width": "1408",
            "height": "786"
        },
        "format": {
            "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
            "pixelFormat": "bgr24"
        }
    },
    "samplingOptions": {
        "skipSamplesWithoutAnnotation": "false",
        "maximumSamplesPerSecond": "20"
    },
    "inputs": [
        {
            "nodeName": "rtspSource",
            "outputSelectors": [
                {
                    "property": "mediaType",
                    "operator": "is",
                    "value": "video"
                }
            ]
        }
    ]
}
```

Kör en felsökningssession **och följ installationsinstruktionerna** , den ställer in topologi, ställer in diagram instansen, aktiverar diagram instansen och slutligen tar bort resurserna.

## <a name="interpret-results"></a>Tolka resultaten

När ett medie diagram instansieras bör du se "MediaSessionEstablished"-händelsen, här är ett [exempel](detect-motion-emit-events-quickstart.md#mediasessionestablished-event)på en MediaSessionEstablished-händelse.

Modulen för spatial analys skickar också ut AI Insight-händelser till live video analys och sedan till IoTHub, visas även i **utdata**. ENTITETen är identifierings objekt och händelsen är spaceanalytics-händelser. De här utdata skickas till video analys i real tid.

Exempel på utdata för personZoneEvent (från cognitiveservices. vision. spatialanalysis-personcrossingpolygon. livevideoanalytics-åtgärd):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Nästa steg

Prova olika åtgärder som `spatialAnalysis` modulen erbjuder, till exempel **PersonCount** och **personDistance** , genom att växla flaggan "Enabled" i noden Graph i distributions manifest filen.
>[!Tip]
> Använd ett [exempel](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) på en videofil som har fler än en person i ramen.

> [!NOTE]
> Du kan bara köra en åtgärd i taget. Se därför till att endast en flagga har angetts till **True** och att de andra är inställda på **false**.