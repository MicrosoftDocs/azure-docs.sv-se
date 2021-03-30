---
title: Identifiera rörelse & spela in video på gräns enheter – Azure
description: Den här snabb starten visar hur du använder real tids analys på IoT Edge för att analysera Live-videofeeden från en (simulerad) IP-kamera, identifiera om någon rörelse finns tillgänglig och spela in ett MP4-videoklipp i det lokala fil systemet på gräns enheten.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: e4334cdd14242337a2a870f31886606020654685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498361"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Snabb start: identifiera rörelse-och inspelnings video på gräns enheter
 
Den här snabb starten visar hur du använder video analys i real tid för IoT Edge för att analysera direktsända video flöden från en (simulerad) IP-kamera. Det visar hur du kan identifiera om en rörelse finns tillgänglig och spela in ett MP4-videoklipp i det lokala fil systemet på gräns enheten. Snabb starten använder en virtuell Azure-dator som en IoT Edge-enhet och använder också en simulerad direktuppspelad video ström. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Förutsättningar

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Granska exempel videon

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Översikt

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/overview.md)]
::: zone-end

## <a name="examine-and-edit-the-sample-files"></a>Granska och redigera exempelfilerna

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/examine-edit-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/examine-edit-sample-files.md)]
::: zone-end

## <a name="review---check-the-modules-status"></a>Granska-kontrol lera modulernas status

::: zone pivot="programming-language-csharp"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/check-modules-status.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/check-modules-status.md)]
::: zone-end

## <a name="review---prepare-for-monitoring-events"></a>Granska – förbereda för övervaknings händelser

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prepare-monitoring-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prepare-monitoring-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>Kör exempel programmet

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>Tolka resultaten 

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="play-the-mp4-clip"></a>Spela upp MP4-klippet

::: zone pivot="programming-language-csharp"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/play-mp4-clip.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/play-mp4-clip.md)]
::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker testa de andra snabb starterna ska du behålla de resurser som du har skapat. Annars går du till dina resurs grupper i Azure Portal, väljer den resurs grupp där du körde den här snabb starten och tar sedan bort alla resurser.

## <a name="next-steps"></a>Nästa steg

* Följ snabb starten för [video analys med din egen modell](use-your-model-quickstart.md) snabb start för att applicera AI på direktsända video flöden.
* Granska ytterligare utmaningar för avancerade användare:

    * Använd en [IP-kamera](https://en.wikipedia.org/wiki/IP_camera) som stöder RTSP i stället för att använda RTSP-simulatorn. Du hittar IP-kameror som har stöd för RTSP på sidan [ONVIF-produkter](https://www.onvif.org/conformant-products) . Sök efter enheter som uppfyller profilerna G, S eller T.
    * Använd en AMD64-eller x64 Linux-enhet i stället för att använda en virtuell Linux-dator i Azure. Enheten måste finnas i samma nätverk som IP-kameran. Följ anvisningarna i [installera Azure IoT Edge runtime på Linux](../../iot-edge/how-to-install-iot-edge.md). Följ sedan anvisningarna i [distribuera din första IoT Edge-modul till en virtuell Linux-enhet](../../iot-edge/quickstart-linux.md) för att registrera enheten med Azure IoT Hub.