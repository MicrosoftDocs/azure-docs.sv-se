---
title: Analysera direktsänd video med din egen gRPC-modell – Azure
description: I den här snabb starten ska du använda dator vision för att analysera direktsända video flöden från en (simulerad) IP-kamera.
ms.topic: quickstart
ms.date: 08/14/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 907d402a4b0ef5d5e437322d02431a0ccd2d8da3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89421549"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-grpc-model"></a>Snabb start: analysera direktsänd video med din egen gRPC-modell

Den här snabb starten visar hur du använder video analys i real tid för IoT Edge för att analysera ett direktuppspelat video flöde från en (simulerad) IP-kamera. Du får se hur du använder en modell för dator vision för att identifiera objekt. En delmängd av bild rutorna i direktsänd video-feed skickas till en tjänst för drift störningar. Resultaten skickas till IoT Edge Hub.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Förutsättningar

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Granska exempel videon

::: zone pivot="programming-language-csharp"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Översikt

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Skapa och distribuera medie diagrammet

::: zone pivot="programming-language-csharp"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Tolka resultaten

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker prova andra snabb starter behåller du de resurser som du har skapat. Annars går du till Azure Portal, går till dina resurs grupper, väljer den resurs grupp där du körde den här snabb starten och tar bort alla resurser.

## <a name="next-steps"></a>Nästa steg

* Prova att köra olika media Graph-topologier med gRPC-protokollet.
* **Skapa och kör exempel på Live Video Analytics (LVA)-tillägg**
<br/>Prova våra Jupyter-exempel Notebooks som gör att du kan bygga och köra [ONNX](http://onnx.ai/) -baserade Yolo-modeller som live video analys (lva)-tillägget.
    * [Exempel på YOLOv3-modell](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/readme.md)
    * [Exempel på YOLOv4-modell](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov4/yolov4-grpc-icpu-onnx/readme.md)

