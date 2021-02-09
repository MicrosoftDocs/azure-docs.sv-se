---
title: Store Analytics-arkitektur
description: Lär dig att skapa ett program för analys i butiken med hjälp av mall för utcheckning i IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 972ab8eaaf7c0b9fe8beb446d74bc8d2e89bbc55
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832206"
---
# <a name="in-store-analytics-architecture"></a>Arkitektur för i Store Analytics


Med lagrings analys lösningar kan du övervaka olika villkor i detalj handels miljön. Dessa lösningar kan skapas med hjälp av en av Programmallarna i IoT Central och arkitekturen nedan som vägledning.


![Azure IoT Central Store Analytics](./media/architecture/store-analytics-architecture-frame.png)

- Uppsättning IoT-sensorer som skickar telemetridata till en gateway-enhet
- Gateway-enheter skickar telemetri och sammanställda insikter till IoT Central
- Kontinuerlig data export till önskad Azure-tjänst för manipulering
- Data kan struktureras i det önskade formatet och skickas till en lagrings tjänst
- Företags program kan fråga data och generera insikter som Power Retail-åtgärder
 
Låt oss ta en titt på viktiga komponenter som vanligt vis spelar en del i en analys lösning i butiken.

## <a name="condition-monitoring-sensors"></a>Villkor för övervakning av sensorer

En IoT-lösning börjar med en uppsättning sensorer som fångar in meningsfulla signaler inifrån en åter försäljare miljö. Den återspeglas av olika typer av sensorer längst till vänster i arkitektur diagrammet ovan.

## <a name="gateway-devices"></a>Gateway-enheter

Många IoT-sensorer kan mata ut RAW-signaler direkt till molnet eller till en gateway-enhet som finns nära dem. Gateway-enheten utför data agg regering på gränsen innan den skickar sammanfattnings information till ett IoT Central-program. Gateway-enheterna är också ansvariga för att vidarebefordra kommando-och kontroll åtgärder till sensor enheterna vid behov. 

## <a name="iot-central-application"></a>IoT Central program

Azure IoT Central-programmet matar in data från olika typer av IoT-sensorer som bra gateway-enheter i detalj handels miljön och genererar en uppsättning meningsfulla insikter.

Azure IoT Central ger också en skräddarsydd upplevelse för Store-operatören som gör det möjligt för dem att fjärrövervaka och hantera infrastruktur enheter.

## <a name="data-transform"></a>Datatransformering
Azure IoT Central-programmet i en lösning kan konfigureras för att exportera rå eller aggregerade insikter till en uppsättning Azure PaaS-tjänster (Platform-as-a-Service) som kan utföra data manipulation och förbättra dessa insikter innan de anropar dem i ett affärs program. 

## <a name="business-application"></a>Affärs program
IoT-data kan användas för att driva olika typer av affärs program som distribueras i en detalj handels miljö. En butiks chef eller personal medlem kan använda dessa program för att visualisera affärs insikter och vidta meningsfulla åtgärder i real tid. Om du vill lära dig hur du skapar en instrument panel i real tid Power BI för din butiks grupp följer du [själv studie kursen](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Nästa steg
* Kom igång med att [checka in-Store Analytics](https://aka.ms/checkouttemplate) och [in-Store Analytics-villkor för övervakning](https://aka.ms/conditiontemplate) . 
* Ta en titt på [kursen om slut punkt till slut punkt](https://aka.ms/storeanalytics-tutorial) som vägleder dig genom hur du skapar en lösning med hjälp av en av In-Store Analytics-Programmallarna.
