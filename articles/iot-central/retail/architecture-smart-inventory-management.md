---
title: Arkitektur IoT Smart Inventory Management | Microsoft Docs
description: En arkitektur för smart Inventory Management-mall i IoT för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d7939193b0dad1ed74dccd0ace0b0f0323b17124
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832648"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Arkitektur för IoT Central Mall för smart inventerings hantering

Partner och kunder kan använda appens mall och följande rikt linjer för att utveckla lösningar för **hantering av Smart inventering** från slut punkt till slut punkt.

> [!div class="mx-imgBorder"]
> ![hantering av smarta lager](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Uppsättning IoT-sensorer som skickar telemetridata till en gateway-enhet
2. Gateway-enheter skickar telemetri och sammanställda insikter till IoT Central
3. Data dirigeras till önskad Azure-tjänst för manipulering
4. Azure-tjänster som ASA eller Azure Functions kan användas för att formatera om data strömmar och skicka till önskade lagrings konton 
5. Bearbetade data lagras i frekvent lagring för nära real tids åtgärder eller kall lagring för ytterligare Insight-förbättringar som baseras på ML-eller batch-analys. 
6. Logic Apps kan användas för att driva olika arbets flöden i affärs program för slutanvändare

## <a name="details"></a>Information
I följande avsnitt beskrivs var och en del av den konceptuella arkitekturen för telemetri från Radio frekvens identifiering (RFID), Bluetooth låg energi (Bell)

## <a name="rfid-tags"></a>RFID-taggar
RFID-taggar överför data om ett objekt via radio vågor. RFID-taggar har vanligt vis inget batteri om inget annat anges. Taggar får energi från Radio vågor som genereras av läsaren och skickar tillbaka en signal till RFID-läsaren.

## <a name="ble-tags"></a>Bell-Taggar
Energy Beacon skickar paket med data med jämna mellanrum. Beacon-data identifieras av TABELLbaserade läsare eller installerade tjänster på smartphones och överför sedan till molnet.

## <a name="rfid--ble-readers"></a>RFID-& tabell läsare
RFID-läsaren konverterar radio vågor till en mer användbar data form. Information som samlas in från taggarna lagras sedan i den lokala Edge-servern eller skickas till molnet med JSON-RPC 2,0 över MQTT.
Bell Reader är även känt som åtkomst punkter (AP) liknar RFID-läsaren. Den används för att identifiera närliggande Bluetooth-signaler och vidarebefordra sitt meddelande till lokala Azure IoT Edge eller molnet med JSON-RPC 2,0 över MQTT.
Många läsare kan läsa RFID-och Beacon-signaler och tillhandahålla ytterligare sensor kapacitet för temperatur, fuktighet, accelerometer och Gyroscope.

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge Gateway
Azure IoT Edge Server är en plats för att Förbearbeta data lokalt innan de skickas till molnet. Vi kan även distribuera moln arbets belastningar, artificiell intelligens, Azure och tjänster från tredje part, affärs logik med hjälp av standard behållare.

## <a name="device-management-with-iot-central"></a>Enhets hantering med IoT Central 
Azure IoT Central är en plattform för lösnings utveckling som gör det enklare att ansluta, konfigurera och hantera IoT-enheter. Plattformen minskar avsevärt belastningen och kostnaderna för IoT-enhetens hantering, åtgärder och relaterad utveckling. Kunder & partner kan skapa en slut punkt för företags lösningar för att få en digital feedback-slinga i inventerings hanteringen.

## <a name="business-insights--actions-using-data-egress"></a>Affärs insikter & åtgärder med utgående data 
IoT Central Platform tillhandahåller omfattande utöknings alternativ via Center (kontinuerlig data export) och API: er. Affärs insikter baserade på telemetri data behandling eller rå telemetri exporteras vanligt vis till ett prioriterat branschspecifika program. Det kan åstadkommas med hjälp av webhook, Service Bus, Event Hub eller Blob Storage för att skapa, träna och distribuera maskin inlärnings modeller & ytterligare förbättra insikter.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du distribuerar [Smart Inventory Management-mall](./tutorial-iot-central-smart-inventory-management.md)
* Läs mer om [IoT Central mallar för butik](./overview-iot-central-retail.md)
* Läs mer om IoT Central referera till [IoT Central översikt](../core/overview-iot-central.md)
