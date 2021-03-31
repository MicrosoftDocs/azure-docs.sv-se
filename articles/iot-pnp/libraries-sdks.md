---
title: 'IoT Plug and Play-bibliotek och SDK: er'
description: Information om de enhets-och tjänst bibliotek som är tillgängliga för att utveckla IoT Plug and Play-aktiverade lösningar.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 31e06777a2f2e26f6ef546e60fd0bf4428d272c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503818"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK: er för IoT Plug and Play

IoT Plug and Play-bibliotek och SDK: er gör det möjligt för utvecklare att skapa IoT-lösningar med olika programmeringsspråk på flera plattformar. Följande tabell innehåller länkar till exempel och snabb starter som hjälper dig att komma igång:

## <a name="device-sdks"></a>Enhets-SDK: er

| Språk | Paket | Kod lagrings plats | Exempel | Snabbstart | Referens |
|---|---|---|---|---|---|
| C-enhet | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Exempel](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device.md) | [Referens](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-enhet | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Ansluta till IoT Hub](quickstart-connect-device.md) | [Referens](/dotnet/api/microsoft.azure.devices.client) |
| Java-enhet | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Ansluta till IoT Hub](quickstart-connect-device.md) | [Referens](/java/api/com.microsoft.azure.sdk.iot.device) |
| Python-enhet | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device.md) | [Referens](/python/api/azure-iot-device/azure.iot.device) |
| Nod-enhet | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Ansluta till IoT Hub](quickstart-connect-device.md) | [Referens](/javascript/api/azure-iot-device/) |
| Inbäddad C-enhet | Ej tillämpligt | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Exempel](howto-use-embedded-c.md#samples) | [Använda Embedded C](howto-use-embedded-c.md) | Ej tillämpligt

## <a name="service-sdks"></a>Tjänst-SDK:er

| Plattform  | Paket | Kod lagrings plats | Exempel | Snabbstart | Referens |
|---|---|---|---|---|---|
| .NET – IoT Hub tjänst | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | Ej tillämpligt | [Referens](/dotnet/api/microsoft.azure.devices) |
| Tjänsten Java-IoT Hub | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | Ej tillämpligt | [Referens](/java/api/com.microsoft.azure.sdk.iot.service) |
| Node-IoT Hub tjänst | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | Ej tillämpligt | [Referens](/javascript/api/azure-iothub/) |
| Python – Digitals dubbla tjänster | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interagera med IoT Hub digitala dubbla API: er](quickstart-service.md) | Ej tillämpligt |
| Nod-Digitals dubbla tjänster | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interagera med IoT Hub digitala dubbla API: er](quickstart-service.md) | Ej tillämpligt |

## <a name="next-steps"></a>Nästa steg

För att testa SDK: er och bibliotek, se  [Guide för utvecklare](concepts-developer-guide-device.md) och [snabb](quickstart-connect-device.md) Starter för enheter och [snabb starter för tjänsten](quickstart-service.md).