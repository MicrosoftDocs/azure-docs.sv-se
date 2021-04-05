---
title: Azure IoT Central Micro-uppfyllelse Center | Microsoft Docs
description: Lär dig att bygga ett program för Micro-uppfyllelse Center med hjälp av vår Programmall för Micro-uppfyllelse Center i IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 23e1880a3abac6ebda71eaa36276d2bf5815048e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833787"
---
# <a name="micro-fulfillment-center-architecture"></a>Arkitektur för Micro-uppfyllelse Center

Med lösningar för Micro-uppfyllelse Center kan du ansluta, övervaka och hantera alla aspekter av ett helt automatiserat uppfyllande Center för att minska kostnaderna genom att eliminera drift stoppet och öka säkerheten och den övergripande effektiviteten. Dessa lösningar kan skapas med hjälp av en av Programmallarna i IoT Central och arkitekturen nedan som vägledning.

![Azure IoT Central Store Analytics](./media/architecture/micro-fulfillment-center-architecture-frame.png)

1. Uppsättning IoT-sensorer som skickar telemetridata till en gateway-enhet
2. Gateway-enheter skickar telemetri och sammanställda insikter till IoT Central
3. Kontinuerlig data export till önskad Azure-tjänst för manipulering
4. Data kan struktureras i det önskade formatet och skickas till en lagrings tjänst
5. Företags program kan fråga data och generera insikter som Power Retail-åtgärder
 
Låt oss ta en titt på viktiga komponenter som vanligt vis spelar en del i en lösning för Micro-uppfyllelse Center.

## <a name="robotic-carriers"></a>Robot bärare

En lösning för Micro-uppfyllelse Center har troligen en stor uppsättning robot bärare som genererar olika typer av telemetri signaler. Dessa signaler kan matas in av en gateway-enhet, aggregeras och sedan skickas till IoT Central som återges av den vänstra sidan av arkitektur diagrammet.  

## <a name="condition-monitoring-sensors"></a>Villkor för övervakning av sensorer

En IoT-lösning börjar med en uppsättning sensorer som fångar in meningsfulla signaler från ditt uppfyllande Center. Den återspeglas av olika typer av sensorer längst till vänster i arkitektur diagrammet ovan.

## <a name="gateway-devices"></a>Gateway-enheter

Många IoT-sensorer kan mata ut RAW-signaler direkt till molnet eller till en gateway-enhet som finns nära dem. Gateway-enheten utför data agg regering på gränsen innan den skickar sammanfattnings information till ett IoT Central-program. Gateway-enheterna är också ansvariga för att vidarebefordra kommando-och kontroll åtgärder till sensor enheterna vid behov. 

## <a name="iot-central-application"></a>IoT Central program

Azure IoT Central-programmet matar in data från olika typer av IoT-sensorer, robots, som bra gateway-enheter inom den uppfyllande Center miljön och genererar en uppsättning meningsfulla insikter.

Azure IoT Central ger också en skräddarsydd upplevelse för Store-operatören som gör det möjligt för dem att fjärrövervaka och hantera infrastruktur enheter.

## <a name="data-transform"></a>Datatransformering
Azure IoT Central-programmet i en lösning kan konfigureras för att exportera obearbetade eller sammanställda insikter till en uppsättning med Azure PaaS-tjänster (Platform-as-a-Service) som kan utföra data manipulation och förbättra dessa insikter innan de informerar dem i ett affärs program. 

## <a name="business-application"></a>Affärs program
IoT-data kan användas för att driva olika typer av affärs program som distribueras i en detalj handels miljö. En ansvarig Center-hanterare eller medarbetare kan använda dessa program för att visualisera affärs insikter och vidta meningsfulla åtgärder i real tid. Om du vill lära dig hur du skapar en instrument panel i real tid Power BI för din butiks grupp följer du [själv studie kursen](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Nästa steg
* Kom igång med programmallen för [Micro-uppfyllelse Center](https://aka.ms/checkouttemplate) . 
* Ta en titt på [självstudien](https://aka.ms/mfc-tutorial) som vägleder dig genom hur du skapar en lösning med hjälp av app-mallen för Micro-utförande.
