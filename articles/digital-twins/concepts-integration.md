---
title: Integrering med andra tjänster
titleSuffix: Azure Digital Twins
description: Förstå ingångs-och utgångs kraven när du distribuerar Azure Digital-dubbla.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 118b02ab694d27dbe4e13cbfa1a617a56b052772
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043076"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Integrera Azure Digitals dubbla med andra tjänster

Azure Digitals, används vanligt vis tillsammans med andra tjänster för att skapa flexibla, anslutna lösningar som använder dina data på flera olika sätt.

Med hjälp av [**händelse vägar**](concepts-route-events.md)kan Azure Digitals dubbla, ta emot data från överordnade tjänster som [IoT Hub](../iot-hub/about-iot-hub.md) eller [Logic Apps](../logic-apps/logic-apps-overview.md)som används för att leverera telemetri och meddelanden. 

Digitala Azure-enheter kan också dirigera data till underordnade tjänster, till exempel [Azure Maps](../azure-maps/about-azure-maps.md) och [Time Series Insights](../time-series-insights/overview-what-is-tsi.md), för lagring, arbets flödes integrering, analys med mera. 

## <a name="data-ingress"></a>Inkommande data

Azures digitala dubblare kan drivas med data och händelser från vilken tjänst som helst –[IoT Hub](../iot-hub/about-iot-hub.md), [Logic Apps](../logic-apps/logic-apps-overview.md), din egen anpassade tjänst och mer. På så sätt kan du samla in telemetri från fysiska enheter i din miljö och bearbeta dessa data med hjälp av Azures digitala dubbla diagram i molnet.

I stället för att ha en inbyggd IoT Hub bakom kulisserna kan du med hjälp av Azure Digitals med hjälp av att "ta med din egen" IoT Hub att använda med tjänsten. Du kan använda en befintlig IoT Hub som du för närvarande har i produktion eller distribuera en ny som ska användas för detta ändamål. Detta ger dig fullständig åtkomst till alla enhets hanterings funktioner i IoT Hub.

Använd en [**Azure-funktion**](../azure-functions/functions-overview.md)för att mata in data från vilken källa som helst i Azure Digitals dubbla. Lär dig mer om det här mönstret i [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md)eller prova själv i självstudien om Azure Digitals dubblare [*: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md). 

Du kan också lära dig hur du ansluter Azure Digitals dubbla till en Logic Apps-utlösare i [*How-to: integration med Logic Apps*](how-to-integrate-logic-apps.md).

## <a name="data-egress-services"></a>Utgående tjänster för data

Azure Digitals dubbla, kan skicka data till anslutna **slut punkter**. Slut punkter som stöds kan vara:
* [Händelsehubb](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Slutpunkter är kopplade till Azure Digital Twins via hanterings-API:er eller Azure Portal. Lär dig mer om hur du ansluter en slut punkt till Azure Digitals dubbla i [*anvisningar: hantera slut punkter och vägar*](how-to-manage-routes-apis-cli.md).

Det finns många andra tjänster där du kanske vill dirigera dina data i slutändan, till exempel [Azure Storage](../storage/common/storage-introduction.md), [Azure Maps](../azure-maps/about-azure-maps.md)eller [Time Series Insights](../time-series-insights/overview-what-is-tsi.md). Om du vill skicka dina data till tjänster som de här kan du koppla mål tjänsten till en slut punkt.

Om du till exempel också använder Azure Maps och vill korrelera platsen med dina Azure Digitals dubbla [grafer](concepts-twins-graph.md)kan du använda Azure Functions med event Grid för att upprätta kommunikation mellan alla tjänster i din distribution. Lär dig mer om det här i [ *How-to: använda Azure Digitals flätas för att uppdatera en Azure Maps inomhus karta*](how-to-integrate-maps.md)

Du kan också lära dig hur du dirigerar data på ett liknande sätt som Time Series Insights, i [*How-to: integration with Time Series Insights*](how-to-integrate-time-series-insights.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om slut punkter och vidarebefordran av händelser till externa tjänster:
* [*Koncept: routning av Azure Digitals dubbla händelser*](concepts-route-events.md)

Mer information finns i så här konfigurerar du Azure Digital-dubblare för att mata in data från IoT Hub:
* [*How-to: intag av telemetri från IoT Hub*](how-to-ingest-iot-hub-data.md)