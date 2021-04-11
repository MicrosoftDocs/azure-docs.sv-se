---
ms.openlocfilehash: 8124f31213de1bbb794968c616e6efb940ac36b2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582739"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="Signal flöde":::

Det här diagrammet visar hur signal flödet i den här snabb starten. En [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulerar en IP-kamera som är värd för en RTSP-server (Real-Time Streaming Protocol). En [RTSP-källmapp](../../../media-graph-concept.md#rtsp-source) hämtar video flödet från den här servern och skickar video ramar till noden [http-tilläggsbegäranden](../../../media-graph-concept.md#http-extension-processor) . 

Noden HTTP-tillägg spelar rollen för en proxy. Den samplar de inkommande video bild rutorna som du har angett genom att använda `samplingOptions` fältet och konverterar även video bild rutorna till den angivna bild typen. Sedan vidarebefordrar avbildningen över REST till en annan Edge-modul som kör en AI-modell bakom en HTTP-slutpunkt. I det här exemplet skapas en Edge-modul med hjälp av [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) -modellen, som kan identifiera många typer av objekt. Noden för HTTP-tilläggsbegäranden samlar in identifierings resultaten och publicerar händelser till noden [IoT Hub mottagare](../../../media-graph-concept.md#iot-hub-message-sink) . Noden skickar sedan händelserna till [IoT Edge Hub](../../../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

I den här snabb starten kommer du att:

1. Skapa och distribuera medie diagrammet.
1. Tolka resultatet.
1. Rensa resurser.