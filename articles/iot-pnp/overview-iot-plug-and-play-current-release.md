---
title: IoT Plug and Play aktuell utgåva | Microsoft Docs
description: Lär dig vad som ingår i den aktuella IoT Plug and Play-versionen.
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7b6669ca55dc9b94dc5d702e54b42011120b8812
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831883"
---
# <a name="what-is-in-the-current-iot-plug-and-play-release"></a>Vad finns i den aktuella IoT Plug and Play-versionen?

I den här artikeln sammanfattas de verktyg, SDK: er och API: er som har stöd för den aktuella IoT Plug and Play-versionen. Versions nummer som visas återspeglar versions numret vid tidpunkten för att IoT Plug and Play skulle vara allmänt tillgängligt. Versions nummer kan öka efter versionen.

## <a name="modeling-language"></a>Modellerings språk

[Digitals definitions språk (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Läs mer om hur IoT Plug and Play-enheter fungerar med DTDL i [IoT plug and Play-konventioner](concepts-convention.md).

## <a name="tools-and-utilities"></a>Verktyg och hjälpmedel

- 0.12.0 för Azure IoT Explorer.

    Mer information finns i [Installera och använda Azure IoT Explorer](howto-use-iot-explorer.md).

- VS Code-tillägg 1.0.0.

    Mer information finns i [Installera och använda redigerings verktygen för DTDL](howto-use-dtdl-authoring-tools.md).

- 1.0.0 för Visual Studio 2019-tillägg.

    Mer information finns i [Installera och använda redigerings verktygen för DTDL](howto-use-dtdl-authoring-tools.md).

- 0.10.0 för Azure CLI IoT-tillägg.

    Azure IoT-tillägget innehåller kommandon som hjälper dig att certifiera enheter. Se `az iot product -h`.

## <a name="libraries-and-sdks"></a>Bibliotek och SDK:er

Mer information om bibliotek och SDK: er finns i [Microsoft SDK: er för IoT plug and Play](libraries-sdks.md).

- C vcpkg- [1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) för enhets-SDK
- [GitHub](https://github.com/Azure/azure-sdk-for-c/) för Embedded C-enhets-SDK
- [NuGet-1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) för .net-enhets-SDK
- [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) för Java-enhets-SDK
- 2.3.0 för python Device SDK [pip](https://pypi.org/project/azure-iot-device/)
- [NPM-1.17.2](https://www.npmjs.com/package/azure-iot-device) för enhets-SDK Node.js
- .NET-IoT Hub [NuGet-1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- [Maven-1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) för Java-IoT Hub
- Node.js-IoT Hub tjänst [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python – IoT Hub/Digitals dubblare service [pip 2.2.3](https://pypi.org/project/azure-iot-hub)
- DTDL Model parser [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

## <a name="rest-apis"></a>REST API:er

REST API [2020-09-30](/rest/api/iothub).

Läs mer i [IoT plug and Play Developer Guide](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>IoT Hub

IoT Plug and Play stöds av IoT Hub i alla regioner. IoT Plug and Play stöds endast av IoT-hubbar av standard eller kostnads fri nivå.

## <a name="announcements"></a>Meddelanden

För aktuella och tidigare IoT Plug and Play-meddelanden, se följande blogg inlägg:

- [Offentlig för hands versions uppdatering (publicerades den 29 augusti 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Förbered och certifiera dina enheter för IoT Plug and Play (publiceras den 26 augusti 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [IoT Plug and Play finns nu i för hands version (publicerades den 22 augusti 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Utveckla med Azure IoT Central och IoT Plug and Play (publicerades den 7 maj 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

## <a name="next-steps"></a>Nästa steg

Det föreslagna nästa steg är att läsa [Vad är IoT plug and Play?](overview-iot-plug-and-play.md).