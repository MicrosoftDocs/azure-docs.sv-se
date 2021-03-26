---
title: Utveckla och distribuera en gRPC-härlednings Server – Azure
description: Den här artikeln innehåller rikt linjer för hur du utvecklar och distribuerar en gRPC-härlednings Server.
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: dbf46a26626a4143d76385968d092c4f238729da
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034868"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>Instruktions guide – utveckla och distribuera en gRPC-härlednings Server

## <a name="overview"></a>Översikt

Den här artikeln visar hur du kan figursätta AI-modeller som du väljer inom en gRPC-härlednings Server, så att den kan integreras med Live Video Analytics (LVA) via Graph-tillägget. 

## <a name="suggested-pre-reading"></a>Föreslagen för läsning

* [Media Graph-tillägg](media-graph-extension-concept.md)
* [Protokoll för gRPC-tillägg](grpc-extension-protocol.md)
* [Schema för härledning av metadata](inference-metadata-schema.md)
* [Introduktion till gRPC](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [Språk guide för proto3](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>Förutsättningar

* En x86-64-eller ARM64-enhet som kör ett av de [Linux-operativsystem som stöds](../../iot-edge/support.md#operating-systems) eller en Windows-dator.
* [Installera Docker](https://docs.docker.com/desktop/#download-and-install) på datorn.
* Installera [IoT Edge runtime](../../iot-edge/how-to-install-iot-edge.md?tabs=linux).

## <a name="grpc-implementation-steps"></a>gRPC implementerings steg

Om du vill skapa en gRPC-härlednings Server och implementera den som ett tillägg med Real video analys används följande steg:

### <a name="setup"></a>Installation:

Utför de steg som krävs för att köra [Live Video Analytics-modulen som distribueras och fungerar på en IoT Edge enhet](deploy-iot-edge-device.md).

### <a name="high-level-implementation-steps"></a>Implementerings steg på hög nivå:

1. Välj ett av många språk som stöds av gRPC: C#, C++, Dart, go, Java, Node, mål-C, PHP, python, Ruby.
1. Implementera en gRPC-server som ska kommunicera med real tids analys med hjälp av [proto3-filerna](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc).

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="gRPC-server som ska kommunicera med Real video analys med hjälp av proto3-filerna":::

    I den här tjänsten:
    1. Hantera sessions Beskrivning meddelande utbyte mellan servern och klienten.
    1. Hantera [medie exempel meddelanden](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) och returnera resultat.

        1. Anropa din inferencing-motor som använder en tränad modell för att göra inferences på inkommande meddelanden.
        1. Ta emot inferencing-resultat från motorn, paketera dem igen som ett medie exempel och skicka tillbaka till video analys med hjälp av [inferencing. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) -filen.

            Du kan också anropa en medie omvandlings funktion till medie exemplet.
1. Distribuera gRPC-serverns implementering. Det finns två sätt att göra detta:

    1. Distribuera som en IoT-modul samplacerad med modulen för video analys
    1. Distribuera som en IoT-modul till en tillgänglig nod i nätverket (lokalt eller i molnet) som kan utbyta data med modulen för video analys i real tid.
1. Konfigurera en Graph med real tids video analys med modulen för video analys och peka den mot gRPC-servern.

### <a name="recommendation"></a>Rekommenderade

När collocating på samma nod, kan delat minne användas för bästa prestanda. Detta kräver att du använder funktioner för delade Linux-minnen som exponeras av programmerings språket/miljön.

1. Öppna den klusterdelade minnes referensen i Linux.
1. När du får en ram får du åtkomst till adress förskjutningen i det delade minnet.
1. Bekräfta slut för ande av ram bearbetning så att dess minne kan återtas av video analys i real tid.

## <a name="create-a-grpc-inference-server"></a>Skapa en gRPC-härlednings Server

Nu ska du bygga en IoT Edge modul (extern AI) som tar emot video bild rutor från video analys med hjälp av [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -meddelanden via delat minne, klassificera bild rutorna som "mörk" eller "Light" och returnera utöknings tillbaka till IoT Hub meddelande mottagare i Real video analys med hjälp av [schemat för härledning av metadata](inference-metadata-schema.md).

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text="Bygg en IoT Edge-modul (extern AI)":::

Den här gRPC-protobuf är ett .NET Core-konsolprogram som skapats hantera [](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -meddelanden som skickas mellan live video analys och din anpassade AI. Nedan visas meddelande flödet mellan live video analys och gRPC-härlednings servern:

1. Live Video Analytics skickar en Media Stream-Beskrivning (se [Extension. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)) som definierar den medie strömmande information som ska skickas följt av video bild rutor till servern som ett [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -meddelande över gRPC Stream-sessionen. 
1. Servern verifierar och bekräftar Stream-beskrivningen och konfigurerar den önskade data överförings metoden.
1. Live video analys börjar sedan skicka MediaSample-filerna som innehåller video bild rutorna.
1. -Servern analyserar video bild rutorna när den tar emot och börjar bearbeta dem med hjälp av en avbildnings processor som definierats av dig.
1. Servern returnerar sedan utöknings resultat som [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) meddelanden så snart de är tillgängliga. 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="Skapa en gRPC-härlednings Server":::

Video bild rutorna kan överföras antingen via [delat minne](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) eller bäddas in i protobuf-meddelandet. Data överförings läget kan konfigureras i LVA Graph-topologi för att fastställa hur ramar ska överföras. Detta uppnås genom att konfigurera **dataTransfer** -elementet för egenskapen MediaGraphGrpcExtension enligt nedan:

Inbäddning

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

Delat minne:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> När du kommunicerar över delat minne ska värdet för IpcMode anges till **shareable** och i modulen gRPC angav du värdet för IpcMode till **container: {CONTAINER_NAME}**. De här inställningarna görs i distributions manifest filen som används för att distribuera modulerna till Azure-IoT Hub. Nedan visas ett exempel på de behållar alternativ som används när du konfigurerar IoT Edge-moduler.

Modulen för video analys i real tid:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

modul för gRPC-tillägg:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> Se till att du har åtkomst till det delade minnes området för "container: lvaEdge" i grpcExtension.

## <a name="sample-grpc-server"></a>Exempel på gRPC-Server

Vi går igenom vårt kod exempel om du vill veta mer om hur gRPC-servern utvecklas.

1. Klona lagrings platsen från GitHub-länken [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) .
1. Starta VSCode och navigera till mappen/src/edge/modules/grpcExtension.
1. Vi gör en snabb genom gång av filerna:

    1. **Program. cs**: Detta är start punkten för programmet. Den ansvarar för att initiera och hantera gRPC-servern, som fungerar som en värd. I vårt exempel är porten för att lyssna efter inkommande gRPC-meddelanden från en gRPC-klient (till exempel Real Video Analytics) på som anges av grpcBindings konfigurations element i AppConfig.jspå.
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\MediaGraphExtensionService.cs**: den här klassen ansvarar för hantering av [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) -meddelanden. Den läser ramen i meddelandet, anropar ImageProcessor och skriver resultatet av härledningen.
Nu när vi har konfigurerat och initierat portarna för gRPC-servern, ska vi titta på hur vi kan bearbeta inkommande gRPC-meddelanden.

        * När en gRPC-session har upprättats är det allra första meddelandet som gRPC-servern kommer att ta emot från klienten (video analys) är en MediaStreamDescriptor som definieras i filen [Extension. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) . 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * I vår server implementering `ProcessMediaStreamDescriptor` kommer metoden att validera MediaStreamDescriptor-egenskapen MediaDescriptor för en videofil och sedan konfigurera data överförings läget (som antingen använder delat minne eller med inbäddad ram överförings läge) beroende på vad du anger i topologin och mallen för distributions mal len som används. 
        * När du tar emot meddelandet och har konfigurerat data överförings läget, returnerar gRPC-servern MediaStreamDescriptor-meddelandet tillbaka till klienten som en bekräftelse och därmed upprättar en anslutning mellan servern och klienten.    
        * När live video analys tar emot bekräftelsen börjar den överföra medie strömmar till gRPC-servern. I vår server implementering kommer metoden att `ProcessMediaStream` bearbeta inkommande MediaStreamMessage. MediaStreamMessage definieras också i filen [Extension. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto).

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * Beroende på värdet för batchSize i Appconfig.jspå, kommer vår server att fortsätta att ta emot meddelanden och lagra video bild rutorna i en lista. När gränsen för batchSize har nåtts anropar funktionen funktionen eller filen som ska bearbeta avbildningen. I vårt fall anropar metoden en fil med namnet BatchImageProcessor. CS
    1. **Processors\BatchImageProcessor.cs**: den här klassen ansvarar för bearbetning av avbildningarna. Vi har använt en bild klassificerings modell i det här exemplet. Algoritmen som används för varje bild som ska bearbetas är följande:

        1. Konvertera avbildningen i en byte mat ris för bearbetning. Se metod: `GetBytes(Bitmap image)`
        
            Exempel processorn som vi använder stöder bara JPG-kodad bild ram och ingen som bild punkts format. Om din anpassade processor har stöd för en annan kodning och/eller format uppdaterar du `IsMediaFormatSupported` metoden för processor klassen.
        1. Konvertera bilden till grå skala med hjälp av [klassen ColorMatrix](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1). Se metod: `ToGrayScale(Image source)` .
        1. När vi får den grå skalnings bilden beräknar vi sedan medelvärdet av de grå skalans byte.
        1. Om det genomsnittliga värdet < 127, klassificeras bilden som "mörk", annars klassificeras de som "Light" med konfidens värdet 1,0. Se metod: `ProcessImage(List<Image> images)` .

    Du kan lägga till din egen processor logik genom att antingen ändra den här klassen eller genom att lägga till en ny klass och implementera metoden:

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    När du har lagt till den nya klassen måste du uppdatera MediaGraphExtensionService. cs så att den instansierar klassen och anropar metoden ProcessImage på den för att köra bearbetnings logiken. 

## <a name="connect-with-live-video-analytics-module"></a>Ansluta med Live Video Analytics-modulen

Nu när du har skapat din gRPC-utöknings modul kommer vi nu att skapa och distribuera medie diagram sto pol Ogin.

1. Använd Visual Studio Code och följ [anvisningarna](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) för att logga in på Docker.
1. I Visual Studio Code går du till src/Edge. Du ser din. kuvert-fil och några mallar för distributionsmall.

    Distributions mal len refererar till distributions manifestet för gräns enheten. Den innehåller några värden för plats hållare. . Miljö filen innehåller värdena för variablerna.
    
    Välj sedan build och push IoT Edge-lösning. Använd src/Edge/deployment.grpc.template.jspå för det här steget.
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="Ansluta med Live Video Analytics-modulen":::
    
    Den här åtgärden skapar modulen grpc och skickar avbildningen till din Azure Container Registry.
    Kontrol lera att du har miljövariabler CONTAINER_REGISTRY_USERNAME_myacr och CONTAINER_REGISTRY_PASSWORD_myacr som definierats i. miljö-filen.
1. Gå till mappen src/Cloud-to-Device-console-app. Här ser du appsettings.jspå filen och några andra filer:

    * C2D-console-app. CSPROJ – projekt filen för Visual Studio Code.
    * operations.jsen lista över de åtgärder som du vill att programmet ska köra.
    * Program. cs-kod för exempel program. Den här koden:

        * Läser in appinställningar.
        * Anropar direkta metoder som visar IoT Edge modulen för video analys i real tid. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](direct-methods.md).
        * Pausar så att du kan granska programmets utdata i TERMINALFÖNSTRET och granska de händelser som har genererats av modulen i fönstret utdata.
        * Anropar direkta metoder för att rensa resurser.
1. Redigera operations.jspå filen:

    * Ändra länken till graf-topologin:

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * Under GraphInstanceSet redigerar du namnet på diagram sto pol Ogin så att den matchar värdet i föregående länk:<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * Under GraphTopologyDelete redigerar du namnet:<br/>`"name": "InferencingWithGrpcExtension"`

            Topologin (till exempel `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json` ) måste definiera en tilläggs adress:
    * Tilläggs adress parameter

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * Konfiguration

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Skapa och distribuera distributions manifestet för IoT Edge

Distributions manifestet definierar vilka moduler som distribueras till en gräns enhet och konfigurations inställningarna för dessa moduler. Följ de här stegen för att generera ett manifest från mallfilen och distribuera det sedan till gräns enheten.
Det här steget skapar IoT Edge distributions manifestet i src/Edge/config/deployment.grpc.amd64.jspå. Högerklicka på filen och välj **skapa distribution för en enskild enhet**.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="Skapa och distribuera distributions manifestet för IoT Edge":::

Sedan ber Visual Studio Code dig att välja en IoT Hub enhet. Välj din IoT Edge enhet som ska vara lva-Sample-Device.
I det här skedet har distributionen av Edge-moduler till din IoT Edges enhet startats. Om 30 sekunder uppdaterar du Azure-IoT Hub i det nedre vänstra avsnittet i Visual Studio Code. Du bör se att en ny modul har distribuerats med namnet lvaExtension.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="En ny modul har distribuerats med namnet lvaExtension":::

## <a name="next-steps"></a>Nästa steg

Följ anvisningarna i **förbereda för övervakning av händelser** som nämns i snabb starten av [din modell](use-your-model-quickstart.md) för att köra exemplet och tolka resultatet. Kolla också våra exempel gRPC-topologier: [gRPCExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json), [CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension och [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json).