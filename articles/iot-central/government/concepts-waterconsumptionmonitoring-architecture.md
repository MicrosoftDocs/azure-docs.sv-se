---
title: Referens arkitektur för övervakning av vatten konsumtions lösning som skapats med Azure IoT Central | Microsoft Docs
description: Lär dig begrepp för en övervaknings lösning för vatten konsumtion som skapats med Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da1eafee719919d7f3086a059d8d2c70ef8d8cb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831662"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Referensarkitektur för övervakning av vattenförbrukning 

Lösningar för övervakning av vattenförbrukning kan skapas med **Azure IoT Central app-mallen** som ett program starts-IoT-program. Den här artikeln innehåller en översikt över referens arkitektur på hög nivå om hur du skapar en lösning från slut punkt till slut punkt. 

![Arkitektur för övervakning av vatten förbrukning](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Koncept:

1. Enheter och anslutningar  
1. IoT Central 
1. Utökning och integrering
1. Affärs program

Låt oss ta en titt på viktiga komponenter som vanligt vis spelar en del i en övervaknings lösning för vatten förbrukning.

## <a name="devices-and-connectivity"></a>Enheter och anslutningar 
Det här avsnittet avser enheter som används för smarta vatten lösningar, till exempel övervakning av vatten kvalitet eller övervakning av vattenförbrukning, vanligt vis som smarta vatten enheter. Smarta vatten enheter kan vara Flow-mätare, vatten kvalitets övervakare, smarta ventiler, läckage för identifiering och så vidare.

Enheter som används i smarta vatten lösningar är vanligt vis anslutna via låg Energis LPWAN (Wide Area Network) via en nätverks operatör från tredje part. För dessa typer av enheter kan du använda [azure IoT Central Device Bridge](../core/howto-build-iotc-device-bridge.md) för att skicka enhets data till IoT-programmet i Azure IoT Central. Du kan också ha enhets-gatewayer som är IP-kompatibla och kan ansluta direkt till IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central är en IoT app-plattform som hjälper dig att snabbt komma igång med din IoT-lösning. Du kan märka, anpassa och integrera din lösning med tjänster från tredje part.
När du ansluter dina Smart vatten enheter till IoT Central får du enhets kommando och kontroll, övervakning och avisering, användar gränssnitt med inbyggda RBAC-och konfigurerbara insikter-instrument paneler och alternativ för utöknings barhet. 


## <a name="extensibility-and-integrations"></a>Utökning och integrering
Du kan utöka IoT-programmet i IoT Central och om du vill:
* transformera och integrera IoT-data för avancerad analys, till exempel utbildning av maskin inlärnings modeller, genom kontinuerlig data export från IoT Central program
* Automatisera arbets flöden i andra system genom att utlösa åtgärder med hjälp av energi automatisering eller Webhooks från IoT Central program
* program mässigt åtkomst till ditt IoT-program i IoT Central via IoT Central-API: er

## <a name="business-applications"></a>Företagsprogram 
IoT-data kan användas för att driva olika typer av affärs program i ett vatten verktyg. Om du vill lära dig hur du ansluter appen IoT Central vattenförbrukning med fält tjänster följer du själv studie kursen om [hur du integrerar med Dynamics 365 Field Services](./how-to-configure-connected-field-services.md) 


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar ett IoT Central program för [vatten förbrukning](./tutorial-water-consumption-monitoring.md)
* Läs mer om [mallar för IoT Central myndigheter](./overview-iot-central-government.md)
* Mer information om IoT Central finns i [IoT Central översikt](../core/overview-iot-central.md)
