---
ms.openlocfilehash: 8d18b2e72a2a9e787f69d6adaeae8ebc3ca162b8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582634"
---
![Översikt](../../../media/quickstarts/gRPC-extension.svg)

Det här diagrammet visar hur signal flödet i den här snabb starten. En [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simulerar en IP-kamera som är värd för en RTSP-server (Real-Time Streaming Protocol). En [RTSP-källmapp](../../../media-graph-concept.md#rtsp-source) hämtar video flödet från den här servern och skickar video bild rutor till noden för [avkänning av rörelser](../../../media-graph-concept.md#motion-detection-processor) . Den här processorn identifierar rörelse och när identifieringen ska skickas, skickas video bild rutor till [gRPC-tillägget processor](../../../media-graph-concept.md#grpc-extension-processor) .

Noden gRPC-tillägg spelar rollen som en proxy. Den konverterar video bild rutorna till den angivna bild typen. Sedan vidarebefordrar den avbildningen över gRPC till en annan Edge-modul som kör en AI-modell bakom en gRPC-slutpunkt över ett [delat minne](https://en.wikipedia.org/wiki/Shared_memory). I det här exemplet skapas en Edge-modul med hjälp av [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) -modellen, som kan identifiera många typer av objekt. GRPC Extension processor-noden samlar in identifierings resultaten och publicerar händelser till noden [IoT Hub mottagare](../../../media-graph-concept.md#iot-hub-message-sink) . Noden skickar sedan händelserna till [IoT Edge Hub](../../../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

I den här snabb starten kommer du att:

1. Skapa och distribuera medie diagrammet.
1. Tolka resultatet.
1. Rensa resurser.