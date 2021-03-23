---
title: Azures industriella IoT-plattform
description: Den här artikeln innehåller en översikt över den industriella IoT-plattformen och dess komponenter.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 9908c020fb987e951f150fa27bce7c1eba973f7b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787934"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>Vad är IIoT-plattformen (industriella IoT)?

Azures industriella IoT-plattform är en Microsoft-Svit med moduler och tjänster som distribueras i Azure. Dessa moduler och tjänster har fullt underutnyttjat öppenhet. Mer specifikt tillämpar vi Azure: s hanterade PaaS-erbjudande (Platform as a Service), program vara med öppen källkod licensierad via MIT-licens, öppnar internationella standarder för kommunikation (OPC UA, AMQP, MQTT, HTTP) och Interfaces (Open API) och öppnar industriella data modeller (OPC UA) på gränsen och i molnet.

## <a name="enabling-shopfloor-connectivity"></a>Aktivera shopfloor-anslutning 

Azures industriella IoT-plattform täcker industriell anslutning av shopfloor-tillgångar (inklusive identifiering av OPC UA-aktiverade till gångar), normaliserar data till OPC UA-format och överför till gångs telemetridata till Azure i OPC UA PubSub-format. Där lagras telemetridata i en moln databas. Dessutom ger plattformen säker åtkomst till shopfloor-tillgångar via OPC UA från molnet. Enhets hanterings funktioner (inklusive säkerhets konfiguration) är också inbyggda. OPC UA-funktionen har skapats med hjälp av Docker-behållar teknik för enkel distribution och hantering. För icke-OPC UA-aktiverade till gångar har vi samarbetat med ledande leverantörer av industriella anslutningar och hjälpte dem att kontakta sina OPC-program för program vara från till Azure IoT Edge. Dessa kort är tillgängliga på Azure Marketplace.

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>Industriella IoT-komponenter: IoT Edge moduler och mikrotjänster i molnet

Edge-tjänsterna implementeras som Azure IoT Edge moduler och körs lokalt. Moln mikrotjänster implementeras som ASP.NET mikrotjänster med ett REST-gränssnitt och körs på hanterade Azure Kubernetes-tjänster eller fristående på Azure App Service. För både Edge-och Cloud Services har vi tillhandahållit färdiga Docker-behållare i Microsoft Container Registry (MCR) och tar bort det här steget för kunden. Edge-och Cloud-tjänsterna utnyttjar varandra och måste användas tillsammans. Vi har även tillhandahållit lättanvända distributions skript som gör att en kan distribuera hela plattformen med ett enda kommando.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig vad Azures industriella IoT-plattform är, kan du lära dig mer om OPC-utgivaren:

> [!div class="nextstepaction"]
> [Vad är OPC-utgivaren?](overview-what-is-opc-publisher.md)