---
title: Arkitektur IoT Central Digital distributions Center | Microsoft Docs
description: En arkitektur för program mal len för digital distributions Center för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.openlocfilehash: f5f1a9a51c4aa68c11f1843424186a727be94621
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99834314"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Arkitektur för IoT Central app-mall för digital distributions Center



Partner och kunder kan använda appens mall & följande rikt linjer för att utveckla lösningar för att skapa slut punkt till slut punkt för **digital distribution Center** .

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Uppsättning IoT-sensorer som skickar telemetridata till en gateway-enhet
2. Gateway-enheter skickar telemetri och sammanställda insikter till IoT Central
3. Data dirigeras till önskad Azure-tjänst för manipulering
4. Azure-tjänster som ASA eller Azure Functions kan användas för att formatera om data strömmar och skicka till önskade lagrings konton
5. Bearbetade data lagras i frekvent lagring för nära real tids åtgärder eller kall lagring för ytterligare Insight-förbättringar som baseras på ML-eller batch-analys. 
6. Logic Apps kan användas för att driva olika arbets flöden i affärs program för slutanvändare

## <a name="details"></a>Information
I följande avsnitt beskrivs varje del av den konceptuella arkitekturen

## <a name="video-cameras"></a>Video kameror 
Video kameror är de viktigaste sensorerna i detta digitalt anslutna eko system i företags skala. Framsteg i maskin inlärning och artificiell intelligens som gör att video kan omvandlas till strukturerade data och bearbeta den på gränsen innan den skickas till molnet. Vi kan använda IP-kameror för att avbilda bilder, komprimera dem i kameran och sedan skicka den komprimerade data över Edge Compute för video analys pipelinen eller använda GigE vision-kameror för att avbilda avbildningar på sensorn och sedan skicka avbildningarna direkt till den Azure IoT Edge, som sedan komprimeras före bearbetningen i Video Analytics-pipeline. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge Gateway
"Kameror-som-sensorer" och Edge-arbetsbelastningar hanteras lokalt av Azure IoT Edge och kamera strömmen bearbetas av Analytics pipeline. Pipeline för video analys i Azure IoT Edge ger många fördelar, inklusive minskad svars tid, konsumtion med låg bandbredd, vilket resulterar i låg latens för snabb data bearbetning. Endast de viktigaste metadata, insikter eller åtgärder skickas till molnet för ytterligare åtgärder eller undersökning. 

## <a name="device-management-with-iot-central"></a>Enhets hantering med IoT Central 
Azure IoT Central är en plattform för lösnings utveckling som fören klar IoT-enheten & Azure IoT Edge Gateway-anslutning, konfiguration och hantering. Plattformen minskar avsevärt belastningen och kostnaderna för IoT-enhetens hantering, åtgärder och relaterad utveckling. Kunder & partner kan skapa en slut punkt för företags lösningar för att få en digital feedback-slinga i distributions Center.

## <a name="business-insights-and-actions-using-data-egress"></a>Affärs insikter och åtgärder som använder utgående data 
IoT Central Platform tillhandahåller omfattande utöknings alternativ via Center (kontinuerlig data export) och API: er. Affärs insikter som baseras på telemetri data behandling eller offline-telemetri exporteras vanligt vis till ett prioriterat branschspecifika program. Det kan uppnås genom webhook, Service Bus, händelsehubben eller Blob Storage för att skapa, träna och distribuera maskin inlärnings modeller och ytterligare förbättra insikter.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du distribuerar [mallen för digital distributions Center](./tutorial-iot-central-digital-distribution-center.md)
* Läs mer om [IoT Central mallar för butik](./overview-iot-central-retail.md)
* Läs mer om IoT Central referera till [IoT Central översikt](../core/overview-iot-central.md)
