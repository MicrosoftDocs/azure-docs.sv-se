---
title: Översikt över Azures industriella IoT | Microsoft Docs
description: Den här artikeln ger en översikt över industriella IoT. Den förklarar den anslutna fabriken, fabrikens golv anslutning och säkerhets komponenter i IIoT.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3df10c9d7630e9db76994e8e508f30adb986e0d5
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105647052"
---
# <a name="what-is-industrial-iot-iiot"></a>Vad är industriella IoT (IIoT)

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

IIoT är den industriella Sakernas Internet. IIoT förbättrar bransch effektiviteten genom program varan för IoT i tillverknings branschen. 

## <a name="improve-industrial-efficiencies"></a>Förbättra industriella effektivitets vinster

Förbättra din operativa produktivitet och lönsamhet med en ansluten fabriks lösnings Accelerator. Anslut och övervaka din industriella utrustning och dina enheter i molnet, inklusive dina datorer som redan arbetar på fabriks golvet. Analysera dina IoT-data för insikter som hjälper dig att öka prestandan för hela fabriks golvet.

Minska tids krävande processen för att komma åt fabriks maskiner med OPC, och fokusera din tid på att bygga IIoT-lösningar. Effektivisera certifikat hanteringen och industriell till gångs integrering med OPC-valvet och var säker på att till gångs anslutningen är säker. Dessa mikrotjänster ger en REST-liknande API ovanpå [Azures industriella IoT-komponenter](https://github.com/Azure/Industrial-IoT). Med tjänst-API: t får du kontroll över funktionerna i Edge-modulen. 

![Översikt över branschanpassat IoT](media/overview-iot-industrial/overview.png)

> [!NOTE]
> Mer information om Azure industriella IoT-tjänster finns i GitHub- [lagringsplatsen](https://github.com/Azure/Industrial-IoT) och [dokumentationen](https://azure.github.io/Industrial-IoT/).
Om du inte är bekant med hur Azure IoT Edge moduler fungerar börjar du med följande artiklar:
- [Om Azure IoT Edge](../iot-edge/about-iot-edge.md)
- [Azure IoT Edge moduler](../iot-edge/iot-edge-modules.md)

## <a name="connected-factory"></a>Ansluten fabrik

[Ansluten fabrik](../iot-accelerators/iot-accelerators-connected-factory-features.md) är en implementering av Microsofts referens arkitektur för Azures industriella IoT som kan anpassas efter specifika affärs behov. Den fullständiga lösnings koden är öppen källkod och tillgänglig på den anslutna fabriken Solution Accelerator GitHub-lagringsplatsen. Du kan använda den som utgångs punkt för en kommersiell produkt och distribuera en fördefinierad lösning i din Azure-prenumeration på några minuter. 

## <a name="factory-floor-connectivity"></a>Fabriks anslutning på fabriken

OPC dubbla är en IIoT-komponent som automatiserar identifiering och registrering av enheter och ger fjärr styrning av industriella enheter via REST API: er. OPC är dubbla, använder Azure IoT Edge och IoT Hub för att ansluta molnet och fabriks nätverket. OPC med dubbla låter IIoT utvecklare fokusera på att skapa IIoT-program utan att behöva oroa dig för säker åtkomst till lokala datorer.

## <a name="security"></a>Säkerhet

OPC-valvet är en implementering av OPC UA global Discovery Server (GDS) som kan konfigurera, registrera och hantera certifikat livs cykeln för OPC UA-Server och klient program i molnet. OPC-valvet fören klar implementeringen och underhållet av säker till gångs anslutning i det industriella utrymmet. Genom att automatisera certifikat hanteringen frigör OPC-valvet fabriks operatörer från de manuella och komplexa processerna som är kopplade till anslutningen och certifikat hanteringen.

## <a name="next-steps"></a>Nästa steg

Nu när du har haft en introduktion till den industriella IoT-komponenten och dess komponenter, här är det föreslagna nästa steg:

[Vad är OPC-tvilling](overview-opc-twin.md)
