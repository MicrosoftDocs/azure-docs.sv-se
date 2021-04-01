---
title: Referens arkitektur för ansluten avfalls hanterings lösning som skapats med Azure IoT Central | Microsoft Docs
description: Lär dig begrepp för en ansluten avfalls hanterings lösning som skapats med Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2d6c8c693db42789a965ee896a6d913d6ee20ae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831679"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Referensarkitektur för ansluten avfallsövervakning 

En ansluten lösning för avfalls hantering kan skapas med hjälp av **Azure IoT Central app-mallen** som ett program starts-IoT-program. Den här artikeln innehåller en översikt över referens arkitektur på hög nivå om hur du skapar en lösning från slut punkt till slut punkt. 

![Arkitektur för ansluten avfalls hantering](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Koncept:

1. Enheter och anslutningar  
1. IoT Central 
1. Utökning och integrering
1. Affärs program

Låt oss ta en titt på viktiga komponenter som vanligt vis spelar en del i en övervaknings lösning för vatten förbrukning.

## <a name="devices-and-connectivity"></a>Enheter och anslutningar 
Enheter som används i öppna miljöer, t. ex. avfalls lager platser, kan anslutas via låg Energis LPWAN (Wide Area Network) via en nätverks operatör från tredje part. För dessa typer av enheter kan du använda [azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md) för att skicka enhets data till IoT-programmet i Azure IoT Central. Du kan också ha enhets-gatewayer som är IP-kompatibla och kan ansluta direkt till IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central är en IoT app-plattform som hjälper dig att snabbt komma igång med din IoT-lösning. Du kan märka, anpassa och integrera din lösning med tjänster från tredje part.
När du ansluter dina Smart vatten enheter till IoT Central får du enhets kommando och kontroll, övervakning och avisering, användar gränssnitt med inbyggda RBAC-och konfigurerbara insikter-instrument paneler och alternativ för utöknings barhet. 

## <a name="extensibility-and-integrations"></a>Utökning och integrering
Du kan utöka IoT-programmet i IoT Central och om du vill:
* transformera och integrera IoT-data för avancerad analys, till exempel utbildning av maskin inlärnings modeller, genom kontinuerlig data export från IoT Central program.
* Automatisera arbets flöden i andra system genom att utlösa åtgärder med hjälp av energi automatisering eller Webhooks från IoT Central program
* program mässigt har åtkomst till ditt IoT-program i IoT Central via IoT Central-API: er.

## <a name="business-applications"></a>Affärs program 
IoT-data kan användas för att driva en mängd olika affärs program i ett verktyg för avlopps verktyg. Om du vill lära dig hur du ansluter IoT Central anslutna hanterings program för avfalls hantering med fält tjänster följer du själv studie kursen om [hur du integrerar med Dynamics 365 Field Services](./how-to-configure-connected-field-services.md) 

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapar ett anslutet hanterings](./tutorial-connected-waste-management.md) IoT Central program
* Läs mer om [mallar för IoT Central myndigheter](./overview-iot-central-government.md)
* Mer information om IoT Central finns i [IoT Central översikt](../core/overview-iot-central.md)
