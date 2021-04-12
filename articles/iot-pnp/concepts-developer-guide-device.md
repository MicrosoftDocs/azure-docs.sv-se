---
title: Guide för enhets utvecklare (C) – IoT Plug and Play | Microsoft Docs
description: Beskrivning av IoT-Plug and Play för C-enhets utvecklare
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 0cca47269e632e1fcba1f8f9eb1c835f27e63059
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582837"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>IoT Plug and Play Device Developer Guide

Med IoT-Plug and Play kan du bygga smarta enheter som annonserar sina funktioner i Azure IoT-program. IoT Plug and Play-enheter kräver ingen manuell konfiguration när en kund ansluter dem till IoT Plug and Play-aktiverade program.

En smart enhet kan implementeras direkt, använda [moduler](../iot-hub/iot-hub-devguide-module-twins.md)eller använda [IoT Edge moduler](../iot-edge/about-iot-edge.md).

Den här guiden beskriver de grundläggande stegen som krävs för att skapa en enhet, modul eller IoT Edge modul som följer [IoT plug and Play-konventionerna](../iot-pnp/concepts-convention.md).

Följ dessa steg om du vill bygga en IoT Plug and Play-enhet, modul eller IoT Edge-modul:

1. Se till att enheten använder antingen MQTT-eller MQTT över WebSockets-protokollet för att ansluta till Azure IoT Hub.
1. Skapa en [DTDL-modell (Digital-definitions språk)](https://github.com/Azure/opendigitaltwins-dtdl) för att beskriva enheten. Mer information finns i [förstå komponenter i IoT plug and Play-modeller](concepts-modeling-guide.md).
1. Uppdatera din enhet eller modul för att tillkännage `model-id` som en del av enhets anslutningen.
1. Implementera telemetri, egenskaper och kommandon med [IoT plug and Play konventioner](concepts-convention.md)

När din enhet eller modul har implementerats kan du använda [Azure IoT Explorer](howto-use-iot-explorer.md) för att kontrol lera att enheten följer IoT plug and Play-konventionerna.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-c](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om IoT Plug and Play enhets utveckling kan du lägga till några ytterligare resurser:

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (enhets-SDK)](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](/rest/api/iothub/device)
- [Förstå komponenter i IoT Plug and Play-modeller](concepts-modeling-guide.md)
- [Installera och Använd DTDL redigerings verktyg](howto-use-dtdl-authoring-tools.md)
- [IoT Plug and Play service Developer Guide](concepts-developer-guide-service.md)