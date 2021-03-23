---
title: Översikt över Azures industriella IoT
description: Den här artikeln ger en översikt över industriella IoT. Den beskriver arbets styrning och säkerhets komponenter i IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787953"
---
# <a name="what-is-industrial-iot-iiot"></a>Vad är industriella IoT (IIoT)?

IIoT (industriella Sakernas Internet) förbättrar bransch effektiviteten genom program varan för IoT i tillverknings branschen.

![Industriell IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>Förbättra industriella effektivitets vinster
Förbättra din operativa produktivitet och lönsamhet med Azures industriella IoT. Minska tids krävande processen för åtkomst till till gångar på plats. Anslut och övervaka din industriella utrustning och dina enheter i molnet, inklusive dina datorer som redan arbetar på fabriks golvet. Analysera dina IoT-data för insikter som hjälper dig att öka prestandan för hela webbplatsen.

## <a name="industrial-iot-components"></a>Industriella IoT-komponenter

**IoT Edge enheter** En IoT Edge enhet består av Edge Runtime och Edge-moduler. 
- *Edge-moduler* är Docker-behållare, som är den minsta beräknings enheten, t. ex. OPC PUBLISHER och OPC, dubbla. 
- *Gräns enheten* används för att distribuera sådana moduler, som fungerar som MEDIATOR mellan OPC UA-server och IoT Hub i molnet. Mer information om IoT Edge finns [här](https://azure.microsoft.com/services/iot-edge/).

**IoT Hub** IoT Hub fungerar som en central meddelande hubb för dubbelriktad kommunikation mellan IoT-programmet och de enheter som hanteras av. Det är en öppen och flexibel moln plattform som en tjänst som stöder SDK: er med öppen källkod och flera protokoll. Läs mer om IoT Hub [här](https://azure.microsoft.com/services/iot-hub/).

**Industriella Edge-moduler**
- *OPC Publisher*: OPC-utgivaren körs i IoT Edge. Den ansluter till OPC UA-servrar och publicerar JSON-kodade telemetridata från dessa servrar i OPC UA "pub/sub"-format till Azure IoT Hub. Alla transport protokoll som stöds av Azure IoT Hub client SDK kan användas, t. ex. HTTPS, AMQP och MQTT.
- *OPC*: OPC, dubbla består av mikrotjänster som använder Azure IoT Edge och IoT Hub för att ansluta molnet och fabriks nätverket. OPC dubbla ger identifiering, registrering och fjärrkontroll av industriella enheter via REST-API: er. OPC-dubbla kräver inte en OPC UA-SDK (OPC Unified Architecture). Det är programmerings språk oberoende och kan ingå i ett Server lös arbets flöde.
- *Identifiering*: identifierings modulen som representeras av identifierings identiteten tillhandahåller identifierings tjänster på gränsen, som inkluderar OPC UA Server Discovery. Om identifiering har kon figurer ATS och Aktiver ATS skickar modulen resultatet av en skannings avsökning via IoT Edge och IoT Hub telemetri till onboarding-tjänsten. Tjänsten bearbetar resultaten och uppdaterar alla relaterade identiteter i registret.


**Identifiera, registrera och hantera dina industriella till gångar med Azure** Med Azures industriella IoT kan fabriks operatörer identifiera OPC UA-aktiverade servrar i ett fabriks nätverk och registrera dem i Azure IoT Hub. Drift personal kan prenumerera på och reagera på händelser på fabriks golvet från var som helst i världen. REST-API: erna för mikrotjänster speglar OPC UA Services Edge-Side. De skyddas med OAUTH-autentisering och auktorisering som backas upp av Azure Active Directory (AAD). Detta gör det möjligt för dina moln program att söka i server adress utrymmen eller läsa/skriva variabler och köra metoder med hjälp av HTTPS och enkla OPC UA JSON-nyttolaster.

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig vilken industriella IoT är kan du läsa om den industriella IoT-plattformen och OPC-utgivaren:

> [!div class="nextstepaction"]
> [Vad är den industriella IoT-plattformen?](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [Vad är OPC-utgivaren?](overview-what-is-opc-publisher.md)