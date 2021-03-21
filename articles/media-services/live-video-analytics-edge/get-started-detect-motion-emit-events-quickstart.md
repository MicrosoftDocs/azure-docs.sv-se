---
title: Kom igång med live video analys på IoT Edge – Azure
description: Den här snabb starten visar hur du kommer igång med live video analys på IoT Edge. Lär dig hur du identifierar rörelser i en video ström i real tid.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 57edf1721249f839f5c781756b3e09bf59888dab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730294"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Snabb start: kom igång – direktsända video analyser på IoT Edge

Den här snabb starten vägleder dig genom stegen för att komma igång med real tids video analys på IoT Edge. Den använder en virtuell Azure-dator som en IoT Edge enhet. Den använder också en simulerad direktuppspelad video ström. 

När du har slutfört installations stegen kan du köra en simulerad real tids video ström genom ett medie diagram som identifierar och rapporterar en rörelse i den data strömmen. Följande diagram visar grafiskt det medie diagrammet.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Real video analys baserat på rörelse identifiering":::

Du kan visa följande video med detaljerade anvisningar om hur du kommer igång med real tids video analys på IoT Edge:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Hcax]

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto som har en aktiv prenumeration. [Skapa ett konto utan kostnad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) om du inte redan har ett.

  > [!NOTE]
  > Du behöver en Azure-prenumeration med behörigheter för att skapa tjänstens huvud namn (**ägar rollen** ger detta). Om du inte har rätt behörighet kan du kontakta konto administratören för att ge dig rätt behörigheter.  

* [Visual Studio Code](https://code.visualstudio.com/) på din utvecklings dator. Kontrol lera att du har [tillägget Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Kontrol lera att nätverket som utvecklings datorn är anslutet till tillåter avancerat protokoll för meddelandekö (AMQP) via port 5671 för utgående trafik. Med den här inställningen kan Azure IoT-verktyg kommunicera med Azure IoT Hub.

> [!TIP]
> Du kan uppmanas att installera Docker när du installerar tillägget Azure IoT-verktyg. Du kan ignorera prompten.

## <a name="set-up-azure-resources"></a>Ställa in Azure-resurser

I den här självstudien krävs följande Azure-resurser:

* IoT Hub
* Lagringskonto
* Azure Media Services konto
* En virtuell Linux-dator i Azure, med [IoT Edge runtime](../../iot-edge/how-to-install-iot-edge.md) installerat

I den här snabb starten rekommenderar vi att du använder [installations skriptet Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) för att distribuera de nödvändiga resurserna i din Azure-prenumeration. Det gör du på följande sätt:

1. Gå till [Azure Portal](https://portal.azure.com) och välj ikonen Cloud Shell.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Om du använder Cloud Shell för första gången uppmanas du att välja en prenumeration för att skapa ett lagrings konto och en Microsoft Azure fil resurs. Välj **skapa lagring** för att skapa ett lagrings konto för din Cloud Shell sessionsinformation. Det här lagrings kontot är skilt från kontot som skriptet skapar för användning med ditt Azure Media Services-konto.
1. I den nedrullningsbara menyn på vänster sida av Cloud Shells fönstret väljer du **bash** som din miljö.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/env-selector.png" alt-text="Miljö väljare":::
1. Kör följande kommando.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    När skriptet har slutförts bör du se alla nödvändiga resurser i din prenumeration. Totalt 12 resurser kommer att konfigureras med skriptet:
    1. **Slut punkt för direkt uppspelning** – det här hjälper dig att spela upp den registrerade AMS-till gången.
    1. **Virtuell dator** – det här är en virtuell dator som fungerar som din gräns enhet.
    1. **Disk** – det här är en lagrings disk som är ansluten till den virtuella datorn för att lagra medier och artefakter.
    1. **Nätverks säkerhets grupp** – detta används för att filtrera nätverks trafik till och från Azure-resurser i ett virtuellt Azure-nätverk.
    1. **Nätverks gränssnitt** – detta gör att en virtuell Azure-dator kan kommunicera med Internet, Azure och andra resurser.
    1. **Skydds-anslutning** – med det här alternativet kan du ansluta till din virtuella dator med webbläsaren och Azure Portal.
    1. **Offentlig IP-adress** – detta gör att Azure-resurser kan kommunicera med Internet och offentliga Azure-tjänster
    1. **Virtuellt nätverk** – Detta möjliggör många typer av Azure-resurser, till exempel den virtuella datorn, för att kommunicera på ett säkert sätt med varandra, Internet och lokala nätverk. Läs mer om [virtuella nätverk](../../virtual-network/virtual-networks-overview.md).
    1. **IoT Hub** – detta fungerar som en central meddelande hubb för dubbelriktad kommunikation mellan IoT-programmet, IoT Edge moduler och de enheter som hanteras.
    1. **Media Service-konto** – detta hjälper till att hantera och strömma medie innehåll i Azure.
    1. **Lagrings konto** – du måste ha ett primärt lagrings konto och du kan ha valfritt antal sekundära lagrings konton som är kopplade till ditt Media Services-konto. Mer information finns i [Azure Storage konton med Azure Media Services-konton](../latest/storage-account-concept.md).
    1. **Behållar register** – det hjälper till att lagra och hantera dina privata Docker-behållar avbildningar och relaterade artefakter.

I skriptets utdata visar en resurs tabell namnet på IoT Hub. Sök efter resurs typen **`Microsoft.Devices/IotHubs`** och Anteckna namnet. Du behöver det här namnet i nästa steg.  

> [!NOTE]
> Skriptet genererar också några konfigurationsfiler i katalogen ***~/clouddrive/lva-Sample/*** . Du behöver dessa filer senare i snabb starten.

> [!TIP]
> Om du stöter på problem med Azure-resurser som skapas, kan du läsa vår **[fel söknings guide](troubleshoot-how-to.md#common-error-resolutions)** för att lösa vanliga problem som uppstår.

## <a name="deploy-modules-on-your-edge-device"></a>Distribuera moduler på din Edge-enhet

Kör följande kommando från Cloud Shell.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Det här kommandot distribuerar följande moduler till gräns enheten, som är den virtuella Linux-datorn i det här fallet.

* Video analys i real tid för IoT Edge (Modulnamn `lvaEdge` )
* RTSP-Simulator (Real-Time Streaming Protocol) (modulens namn `rtspsim` )

RTSP Simulator-modulen simulerar en real tids video ström med hjälp av en videofil som kopierades till din Edge-enhet när du körde [installations skriptet för Live Video Analytics-resurser](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

Nu distribueras modulerna, men inga medie diagram är aktiva.

## <a name="configure-the-azure-iot-tools-extension"></a>Konfigurera tillägget Azure IoT-verktyg

Följ dessa anvisningar för att ansluta till din IoT Hub med hjälp av tillägget Azure IoT-verktyg.

1. Öppna fliken **tillägg** i Visual Studio Code (eller tryck på Ctrl + Shift + X) och Sök efter Azure-IoT Hub.
1. Högerklicka och välj Inställningar för **tillägg**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Tilläggs inställningar":::
1. Sök och aktivera "Visa utförligt meddelande".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Visa utförligt meddelande":::
1. Välj **Visa**  >  **Utforskaren**. Eller Välj CTRL + SKIFT + E.
1. I det nedre vänstra hörnet på fliken **Utforskaren** väljer du **Azure IoT Hub**.
1. Välj ikonen **fler alternativ** om du vill se snabb menyn. Välj sedan **ange IoT Hub anslutnings sträng**.
1. När en inmatad ruta visas anger du IoT Hub anslutnings strängen. I Cloud Shell kan du hämta anslutnings strängen från *~/clouddrive/lva-sample/appsettings.jspå*.

> [!NOTE]
> Du kan bli ombedd att ange information om inbyggd slut punkt för IoT Hub. Om du vill hämta den informationen går du till IoT Hub i Azure Portal och letar efter **inbyggda slut punkts** alternativ i det vänstra navigerings fönstret. Klicka där och leta efter den **Event Hub-kompatibla slut punkten** under avsnittet **händelsehubben-kompatibel slut punkt** . Kopiera och Använd texten i rutan. Slut punkten kommer att se ut ungefär så här:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Om anslutningen lyckas visas listan över gräns enheter. Du bör se minst en enhet med namnet **lva-Sample-Device**. Nu kan du hantera dina IoT Edge enheter och interagera med Azure IoT Hub via snabb menyn. Om du vill visa de moduler som har distribuerats på gräns enheten expanderar du noden **moduler** under **lva-Sample-Device**.

![lva-exempel-Device-nod](./media/quickstarts/lva-sample-device-node.png)

> [!TIP]
> Om du har [distribuerat video analys manuellt på IoT Edge](deploy-iot-edge-device.md) yourselves på en gräns enhet (till exempel en arm64-enhet) visas modulen som visas under den enheten under Azure-IoT Hub. Du kan välja den modulen och följa resten av stegen nedan.

## <a name="use-direct-method-calls"></a>Använd direkta metod anrop

Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa direkta metoder. Mer information finns i [direkta metoder för video analys på IoT Edge](direct-methods.md). 

### <a name="invoke-graphtopologylist"></a>Anropa GraphTopologyList

Räkna upp alla [grafer-topologier](media-graph-concept.md#media-graph-topologies-and-instances) i modulen:

1. Högerklicka på modulen **lvaEdge** i Visual Studio Code och välj **Invoke modul Direct-metod**.
1. I rutan som visas anger du *GraphTopologyList*.
1. Kopiera följande JSON-nyttolast och klistra sedan in den i rutan. Välj sedan Retur-tangenten.

    ```
    {
        "@apiVersion" : "2.0"
    }
    ```

    Inom några sekunder visas följande svar i fönstret **utdata** .

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
    
    Det här svaret förväntas eftersom inga diagram-topologier har skapats.
    

### <a name="invoke-graphtopologyset"></a>Anropa GraphTopologySet

Precis som vi gjorde tidigare, kan du nu anropa `GraphTopologySet` för att ange en [diagram sto pol Ogin](media-graph-concept.md#media-graph-topologies-and-instances). Använd följande JSON som nytto lasten.

```
{
    "@apiVersion": "2.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
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
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
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

Denna JSON-nyttolast skapar en Graph-topologi som definierar tre parametrar. Två av dessa parametrar har standardvärden. Topologin har en källa (RTSP-källa), en processor (process för rörelse identifiering) och en Sink-nod (IoT Hub Sink).

Inom några sekunder visas följande svar i fönstret **utdata** .

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
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
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
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

Returnerad status är 201. Den här statusen anger att en ny topologi har skapats. 

Försök med följande steg:

1. Anropa `GraphTopologySet` igen. Den returnerade status koden är 200. Den här koden anger att en befintlig topologi har uppdaterats.
1. Anropa `GraphTopologySet` igen, men ändra beskrivnings strängen. Den returnerade status koden är 200 och beskrivningen uppdateras till det nya värdet.
1. Anropa `GraphTopologyList` enligt beskrivningen i föregående avsnitt. Nu kan du se `MotionDetection` topologin i den returnerade nytto lasten.

### <a name="invoke-graphtopologyget"></a>Anropa GraphTopologyGet

Anropa `GraphTopologyGet` med hjälp av följande nytto Last.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Inom några sekunder visas följande svar i fönstret **utdata** :

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
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
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
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

Lägg märke till följande information i svarets nytto last:

* Status koden är 200, vilket indikerar att det lyckades.
* Nytto lasten inkluderar tidstämpeln `created` och `lastModified` tidsstämpeln.

### <a name="invoke-graphinstanceset"></a>Anropa GraphInstanceSet

Skapa en diagram instans som refererar till föregående graf-topologi. Med diagram förekomster kan du analysera direktuppspelade video strömmar från många kameror med samma graf-topologi. Mer information finns i [Media Graph-topologier och instanser](media-graph-concept.md#media-graph-topologies-and-instances).

Anropa metoden Direct `GraphInstanceSet` med hjälp av följande nytto Last.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Observera att denna nytto last:

* Anger det Topology-namn ( `MotionDetection` ) som instansen måste skapas för.
* Innehåller ett parameter värde för `rtspUrl` , som inte har något standardvärde i nytto lasten i graf-topologin. Det här värdet är en länk till exempel videon nedan:
    > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]
Inom några sekunder visas följande svar i fönstret **utdata** :

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

I nytto lasten för svar ser du till att:

* Status koden är 201, vilket indikerar att en ny instans skapades.
* Statusen är `Inactive` och anger att graf-instansen skapades men inte Aktiver ATS. Mer information finns i [Media Graph-tillstånd](media-graph-concept.md).

Försök med följande steg:

1. Anropa `GraphInstanceSet` igen med hjälp av samma nytto Last. Observera att den returnerade status koden är 200.
1. Anropa `GraphInstanceSet` igen, men Använd en annan beskrivning. Lägg märke till den uppdaterade beskrivningen i nytto lasten för svar, vilket indikerar att graf-instansen har uppdaterats.
1. Anropa `GraphInstanceSet` , men ändra namnet till `Sample-Graph-2` . Lägg märke till den nyligen skapade graf-instansen (dvs. status kod 201) i nytto lasten för svar.

### <a name="invoke-graphinstanceactivate"></a>Anropa GraphInstanceActivate

Aktivera nu graf-instansen för att starta flödet av direktsänd video genom modulen. Anropa metoden Direct `GraphInstanceActivate` med hjälp av följande nytto Last.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Inom några sekunder visas följande svar i fönstret **utdata** .

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden 200 anger att graf-instansen har Aktiver ATS.

### <a name="invoke-graphinstanceget"></a>Anropa GraphInstanceGet

Anropa nu den direkta metoden med `GraphInstanceGet` hjälp av följande nytto Last.

```
 {
     "@apiVersion" : "2.0",
     "name" : "Sample-Graph-1"
 }
 ```

Inom några sekunder visas följande svar i fönstret **utdata** .

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Observera följande information i svarets nytto last:

* Status koden är 200, vilket indikerar att det lyckades.
* Statusen är `Active` , vilket anger att graf-instansen nu är aktiv.

## <a name="observe-results"></a>Observera resultat

Graf-instansen som vi har skapat och aktiverat använder noden för motion-identifiering för att identifiera rörelse i inkommande video ström för Real tid. Den skickar händelser till noden IoT Hub mottagare. Dessa händelser vidarebefordras till IoT Edge Hub. 

Följ de här stegen om du vill följa resultatet.

1. Öppna fönstret **Utforskaren** i Visual Studio Code. I det nedre vänstra hörnet tittar du efter **Azure IoT Hub**.
2. Expandera noden **enheter** .
3. Högerklicka på **lva-Sample-Device** och välj **starta övervakning inbyggd händelse övervakning**.

    ![Börja övervaka IoT Hub-händelser](./media/quickstarts/start-monitoring-iothub-events.png)

    > [!NOTE]
    > Du kan bli ombedd att ange information om inbyggd slut punkt för IoT Hub. Om du vill hämta den informationen går du till IoT Hub i Azure Portal och letar efter **inbyggda slut punkts** alternativ i det vänstra navigerings fönstret. Klicka där och leta efter den **Event Hub-kompatibla slut punkten** under avsnittet **händelsehubben-kompatibel slut punkt** . Kopiera och Använd texten i rutan. Slut punkten kommer att se ut ungefär så här:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    
I fönstret **utdata** visas följande meddelande:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    }
}
```

Lägg märke till följande information:

* Meddelandet innehåller ett `body` avsnitt och ett `applicationProperties` avsnitt. Mer information finns i [skapa och läsa IoT Hub meddelanden](../../iot-hub/iot-hub-devguide-messages-construct.md).
* I `applicationProperties` `subject` refererar den nod som `MediaGraph` meddelandet skapades från. I det här fallet kommer meddelandet från processen för identifiering av rörelser.
* I `applicationProperties` `eventType` anger att den här händelsen är en analys händelse.
* `eventTime`Värdet är den tidpunkt då händelsen inträffade.
* `body`Avsnittet innehåller information om Analytics-händelsen. I det här fallet är händelsen en utfalls händelse, så texten innehåller `timestamp` och `inferences` data.
* `inferences`Avsnittet visar att `type` är `motion` . Den ger ytterligare information om `motion` händelsen.

Om du låter medie grafen köras ett tag visas följande meddelande i fönstret **utdata** .

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Observera följande information i det här meddelandet:

* I `applicationProperties` `subject` anger att meddelandet genererades från noden RTSP-källa i medie diagrammet.
* I `applicationProperties` `eventType` anger att den här händelsen är diagnostik.
* `body`Innehåller data om Diagnostic-händelsen. I det här fallet innehåller meddelandet bröd texten eftersom händelsen är `MediaSessionEstablished` .

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Starta ytterligare direkta metoder för att rensa

Anropa direkt metoder för att först inaktivera graf-instansen och ta sedan bort den.

### <a name="invoke-graphinstancedeactivate"></a>Anropa GraphInstanceDeactivate

Anropa metoden Direct `GraphInstanceDeactivate` med hjälp av följande nytto Last.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Inom några sekunder visas följande svar i fönstret **utdata** :

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden för 200 anger att graf-instansen har inaktiverats.

Försök sedan att anropa `GraphInstanceGet` som angivet tidigare i den här artikeln. Observera `state` värdet.

### <a name="invoke-graphinstancedelete"></a>Anropa GraphInstanceDelete

Anropa metoden Direct `GraphInstanceDelete` med hjälp av följande nytto Last.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Inom några sekunder visas följande svar i fönstret **utdata** :

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden 200 anger att graf-instansen har tagits bort.

### <a name="invoke-graphtopologydelete"></a>Anropa GraphTopologyDelete

Anropa metoden Direct `GraphTopologyDelete` med hjälp av följande nytto Last.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Inom några sekunder visas följande svar i fönstret **utdata** .

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Status koden 200 anger att graf sto pol Ogin har tagits bort.

Försök med följande steg:

1. Anropa `GraphTopologyList` och Observera att modulen inte innehåller några diagram-topologier.
1. Anropa `GraphInstanceList` med samma nytto last som `GraphTopologyList` . Observera att inga diagram instanser räknas upp.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort de resurser som du skapade i den här snabb starten.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [registrerar video med hjälp av real tids analys på IoT Edge](continuous-video-recording-tutorial.md).
* Läs mer om [diagnostiska meddelanden](monitoring-logging.md).