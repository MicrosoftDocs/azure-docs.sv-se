---
title: Uppgradera video analys i real tid på IoT Edge från 1,0 till 2,0
description: Den här artikeln beskriver skillnaderna och de olika saker som du bör tänka på när du uppgraderar Live Video Analytics (LVA) i Azure IoT Edge-modulen.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 7904269a8ca87d3d801c7ff78def1b60f242b9ab
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425995"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>Uppgradera video analys i real tid på IoT Edge från 1,0 till 2,0

Den här artikeln beskriver skillnaderna och de olika saker som du bör tänka på när du uppgraderar Live Video Analytics (LVA) i Azure IoT Edge-modulen.

## <a name="change-list"></a>Ändra lista

> [!div class="mx-tdCol4BreakAll"]
> |Rubrik|Live video analys 1,0|Live video analys 2,0|Beskrivning|
> |-------------|----------|---------|---------|
> |Behållar avbildning|mcr.microsoft.com/media/live-video-analytics:1.0.0|mcr.microsoft.com/media/live-video-analytics:2.0.0|Microsoft-publicerade Docker-avbildningar för real tids video analys på Azure IoT Edge|
> |**MediaGraph-noder** |    |   |   |
> |Källor|:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP-källa </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub meddelande källa |:::image type="icon" source="./././media/upgrading-lva/check.png"::: RTSP-källa </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub meddelande källa | MediaGraph-noder som fungerar som källor för medie inmatning och meddelanden.|
> |Processorer|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processor för rörelse identifiering </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Filter processor för RAM hastighet </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processor för http-tillägg </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc förlängnings processor </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Signal grind processor |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processor för rörelse identifiering </br>:::image type="icon" source="./././media/upgrading-lva/remove.png":::**Filter processor för RAM hastighet**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Processor för http-tillägg </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Grpc förlängnings processor </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Signal grind processor | MediaGraph-noder som gör att du kan formatera mediet innan du skickar till AI-slutpunktsservrar.|
> |Mottagare|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Till gångs mottagare </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Fil mottagare </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub meddelande mottagare|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Till gångs mottagare </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Fil mottagare </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub meddelande mottagare| MediaGraph-noder som gör att du kan lagra det bearbetade mediet.|
> |**MediaGraphs som stöds** |    |   |   |
> |Topologier|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Kontinuerlig inspelning av video </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rörelse analys och kontinuerlig inspelning av video </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Extern analys och kontinuerlig inspelning av video </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Event-baserad inspelning på rörelse </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Event-baserad inspelning på AI</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Event-baserad inspelning på extern händelse </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rörelse analys </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rörelse analys följt av AI-inferencing |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Kontinuerlig inspelning av video </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rörelse analys och kontinuerlig inspelning av video </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Extern analys och kontinuerlig inspelning av video </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Event-baserad inspelning på rörelse </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Event-baserad inspelning på AI</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Event-baserad inspelning på extern händelse </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rörelse analys </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rörelse analys följt av AI-inferencing </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**AI-sammansättning** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Ljud-och video inspelning** </br>  | MediaGraph-topologier som gör att du kan definiera en grafs skiss med parametrar som plats hållare för värden.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>Uppgradera direktsänd video analys på IoT Edge modul från 1,0 till 2,0
När du uppgraderar video analysen i IoT Edge-modulen, se till att du uppdaterar följande information.
### <a name="container-image"></a>Behållar avbildning
I distributions mal len söker du efter din live video analys på IoT Edge modul under `modules` noden och uppdaterar `image` fältet som:
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
Om du inte har ändrat namnet på video analysen i IoT Edge-modulen tittar du `lvaEdge` under noden modul.

### <a name="topology-file-changes"></a>Ändringar i Topology-filen
Kontrol lera att **`apiVersion`** är inställt på 2,0 i dina Topology-filer

### <a name="mediagraph-node-changes"></a>MediaGraph-noden ändras


* Ljud från kamera källan kan nu skickas direkt tillsammans med video. Du kan bara skicka **ljud-** eller **video-** eller **ljud-och video filmer** med hjälp av **`outputSelectors`** till en graf-nod. Om du till exempel bara vill välja video från RTSP-källan och skicka den till den underordnade, lägger du till följande i noden RTSP-Källa:
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>**`outputSelectors`** Är en valfri egenskap. Om detta inte används skickar Media grafen ljudet (om det är aktiverat) och video från RTSP-kameran. 

* I `MediaGraphHttpExtension` och `MediaGraphGrpcExtension` -processorer noterar du följande ändringar:  
    * **Bild egenskaper**
        * `MediaGraphImageFormatEncoded` stöds inte längre. 
        * Använd i stället **`MediaGraphImageFormatBmp`** eller **`MediaGraphImageFormatJpeg`** **`MediaGraphImageFormatPng`** . Exempel:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * Använd om du vill använda RAW-avbildningar **`MediaGraphImageFormatRaw`** . Om du vill använda detta uppdaterar du noden avbildning som:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > Möjliga värden för pixelFormat är:,,,,,,,,, `yuv420p` `rgb565be` `rgb565le` `rgb555be` `rgb555le` `rgb24` `bgr24` `argb` `rgba` `abgr``bgra`  

    * **extensionConfiguration för Grpc Extension-processor**  
        * I `MediaGraphGrpcExtension` processor är en ny egenskap som heter **`extensionConfiguration`** tillgänglig, som är en valfri sträng som kan användas som en del av gRPC-kontraktet. Det här fältet kan användas för att skicka data till en härlednings Server och du kan definiera hur den här informationen ska användas i servern.  
        Ett användnings fall av den här egenskapen är när du har flera AI-modeller som paketeras i en enda härlednings Server. Med den här egenskapen behöver du inte exponera en nod för varje AI-modell. I stället, för en diagram förekomst, som förlängnings leverantör, kan du definiera hur du vill att de olika AI-modellerna ska **`extensionConfiguration`** användas med egenskapen och under körningen, och lva skickar strängen till inferencing-servern, som kan använda den för att anropa den önskade AI-modellen.  

    * **AI-sammansättning**
        * Live video analys 2,0 har nu stöd för användning av fler än en Media Graph-förlängningskabel i en topologi. Du kan skicka medie bild rutorna från RTSP-kameran till olika AI-modeller antingen i tur och ordning, parallellt eller i en kombination av båda. Se en exempel-topologi som visar två AI-modeller som används sekventiellt.


* I noden **fil mottagare** kan du nu ange hur mycket disk utrymme som video analysen i real tid på IoT Edge modul kan använda för att lagra de bearbetade bilderna. Det gör du genom att lägga till **`maximumSizeMiB`** fältet i FileSink-noden. En exempel på en fil mottagare är följande:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* I noden **till gångs mottagare** kan du ange hur mycket disk utrymme som direktsänd video analys på IoT Edge modul kan använda för att lagra de bearbetade bilderna. Det gör du genom att lägga till **`localMediaCacheMaximumSizeMiB`** fältet i noden till gångs mottagare. En nod för exempel på till gångs mottagare är följande:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  **Filens mottagar** Sök väg är uppdelad i sökvägen till bas katalogen och fil namnet, medan sökvägen **till till gångs mottagare** inkluderar sökvägen till bas katalogen.  

* **`MediaGraphFrameRateFilterProcessor`** är föråldrad i **Real video analys i IoT Edge 2,0** -modulen.
    * Om du vill testa den inkommande videon för bearbetning lägger du till **`samplingOptions`** egenskapen i MediaGraph-tilläggs processorerna ( `MediaGraphHttpExtension` eller `MediaGraphGrpcExtension` )  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Modulens mått i Prometheus-formatet med hjälp av teleympkvistar
I den här versionen kan du använda netympkvistar för att skicka mått till Azure Monitor. Därifrån kan måtten dirigeras till Log Analytics eller en händelsehubben.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="Händelsetaxonomi":::

Du kan skapa en teleympkvistar-avbildning med en anpassad konfiguration som enkelt använder Docker. Läs mer om det här på sidan [övervakning och loggning](monitoring-logging.md#azure-monitor-collection-using-telegraf) .

## <a name="next-steps"></a>Nästa steg

[Kom igång med live video analys på IoT Edge](get-started-detect-motion-emit-events-quickstart.md)