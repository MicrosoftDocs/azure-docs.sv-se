---
title: Guide till service Developer – IoT Plug and Play | Microsoft Docs
description: Beskrivning av IoT-Plug and Play för tjänst utvecklare
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: a5889be88dfd0870a2eee868c97787ff354cff68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582739"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT Plug and Play service Developer Guide

Med IoT-Plug and Play kan du bygga smarta enheter som annonserar sina funktioner i Azure IoT-program. IoT Plug and Play-enheter kräver ingen manuell konfiguration när en kund ansluter dem till IoT Plug and Play-aktiverade program.

Med IoT-Plug and Play kan du använda enheter som har meddelat sitt modell-ID med IoT-hubben. Du kan till exempel komma åt egenskaperna och kommandona för en enhet direkt.

För att använda en IoT Plug and Play-enhet som är ansluten till din IoT-hubb, en av IoT-tjänstens SDK: er:

## <a name="service-sdks"></a>Tjänst-SDK:er

Använd Azure IoT service SDK: er i din lösning för att interagera med enheter och moduler. Du kan till exempel använda tjänst-SDK: erna för att läsa och uppdatera dubbla egenskaper och anropa kommandon. Språk som stöds är C#, Java, Node.js och python.

Med tjänst-SDK: er kan du komma åt enhets information från en lösning, till exempel ett skriv bord eller ett webb program. Tjänst-SDK: erna innehåller två namn rymder och objekt modeller som du kan använda för att hämta modell-ID:

- IoT Hub-tjänstens klient. I den här tjänsten exponeras modell-ID: t som en enhet med dubbla egenskaper.

- Digitals klient med dubbla klienter. Det nya digitala interformat-API: et fungerar på DTDL-modell konstruktioner [(Digitals definitions språk)](concepts-digital-twin.md) som komponenter, egenskaper och kommandon. De digitala dubbla API: erna gör det enklare för lösnings byggare att skapa IoT Plug and Play-lösningar.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om enhets modellering är här några ytterligare resurser:

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (enhets-SDK)](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](/rest/api/iothub/device)
- [IoT Plug and Play modellerings guide](concepts-modeling-guide.md)
- [Installera och Använd DTDL redigerings verktyg](howto-use-dtdl-authoring-tools.md)