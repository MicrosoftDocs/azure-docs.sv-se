---
title: Arkitektur IoT-ansluten logistik | Microsoft Docs
description: En arkitektur för IoT Connected logistik-Programmall för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d1e17dce80c313bf726451c36ec06dd393549600
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832427"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Arkitektur för IoT Central ansluten logistik-Programmall



Partner & kunden kan använda appens mall & följande rikt linjer för att utveckla **anslutna logistik lösningar** från slut punkt till slut punkt.

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Uppsättning IoT-taggar som skickar telemetridata till en gateway-enhet
2. Gateway-enheter skickar telemetri och sammanställda insikter till IoT Central
3. Data dirigeras till önskad Azure-tjänst för manipulering
4. Azure-tjänster som ASA eller Azure Functions kan användas för att formatera om data strömmar och skicka till önskade lagrings konton
5. Olika affärs arbets flöden kan användas av affärs program för slutanvändare

## <a name="details"></a>Information
I följande avsnitt beskrivs varje del av den konceptuella arkitekturen för telemetri från IoT-Taggar & gateways

## <a name="iot-tags"></a>IoT-Taggar
IoT-Taggar tillhandahåller funktioner för fysisk, omgivande och miljö sensor, till exempel temperatur, fuktighet, stöt, lutning &ljus. IoT-Taggar ansluter vanligt vis till en gateway-enhet via ZigBee (802.15.4). Taggar är billigare sensorer; de kan därför tas bort i slutet av en typisk logistik resa för att undvika utmaningar med omvänd logistik.

## <a name="gateway"></a>Gateway
Gatewayer kan också fungera som IoT-Taggar med deras funktioner för att kunna hantera omgivningen. Gatewayen möjliggör överordnad Azure IoT Cloud-anslutning (MQTT) med hjälp av mobil nät, Wi-Fi kanaler.  Lägena Bluetooth, NFC och 802.15.4 för trådlösa sensor nätverk (WSN) används för underordnad kommunikation med IoT-taggar. Gatewayer ger slut punkt till slut punkt för säker moln anslutning, IoT-taggning, sammanslagning av sensor data, datakvarhållning och möjligheten att konfigurera larm tröskelvärden.

## <a name="device-management-with-iot-central"></a>Enhets hantering med IoT Central 
Azure IoT Central är en plattform för lösnings utveckling som gör det enklare att ansluta, konfigurera och hantera IoT-enheter. Plattformen minskar avsevärt belastningen och kostnaderna för IoT-enhetens hantering, åtgärder och relaterad utveckling. Kunder & partner kan skapa ett heltäckande företags lösningar för att få en digital feedback-slinga i logistik.

## <a name="business-insights-and-actions-using-data-egress"></a>Affärs insikter och åtgärder som använder utgående data 
IoT Central Platform tillhandahåller omfattande utöknings alternativ via Center (kontinuerlig data export) och API: er. Affärs insikter baserade på telemetri data behandling eller rå telemetri exporteras vanligt vis till ett prioriterat branschspecifika program. Det kan åstadkommas med hjälp av webhook, Service Bus, Event Hub eller Blob Storage för att skapa, träna och distribuera maskin inlärnings modeller & ytterligare förbättra insikter.

## <a name="next-steps"></a>Nästa steg
* Lär dig att distribuera en [mall för kopplad logistik lösning](./tutorial-iot-central-connected-logistics.md)
* Läs mer om [IoT Central mallar för butik](./overview-iot-central-retail.md)
* Läs mer om IoT Central referera till [IoT Central översikt](../core/overview-iot-central.md)
