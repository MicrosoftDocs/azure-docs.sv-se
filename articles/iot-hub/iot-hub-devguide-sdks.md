---
title: 'Förstå Azure IoT SDK: er | Microsoft Docs'
description: 'Guide för utvecklare – information om och länkar till de olika Azure IoT-enheter och tjänst-SDK: er som du kan använda för att bygga enhets appar och backend-appar.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 8a65619d65e1a3655d3ef8a295749a705e6a79e1
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431286"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Förstå och använda SDK:er för Azure IoT Hub

Det finns två kategorier av Software Development Kits (SDK: er) för att arbeta med IoT Hub:

* Med **IoT Hub enhets-SDK** : er kan du skapa appar som körs på dina IoT-enheter med hjälp av enhets klienten eller klientens modul Dessa appar skickar telemetri till din IoT-hubb och kan ta emot meddelanden, jobb, metoder eller tvillinguppdateringar från din IoT-hubb. Du kan använda dessa SDK: er för att bygga enhets program som använder [Azure IoT plug and Play](../iot-pnp/overview-iot-plug-and-play.md) konventioner och modeller för att annonsera sina funktioner till IoT plug and Play-aktiverade program. Du kan också använda module-klienten för att redigera [moduler](../iot-edge/iot-edge-modules.md) för [Azure IoT Edge runtime](../iot-edge/about-iot-edge.md).

* Med **IoT Hub tjänst-SDK** : er kan du skapa backend-program för att hantera din IoT-hubb och skicka meddelanden, schemalägga jobb, anropa direkta metoder eller skicka önskade egenskaps uppdateringar till dina IoT-enheter eller-moduler.

Dessutom tillhandahåller vi också en uppsättning SDK: er för att arbeta med [enhets etablerings tjänsten](../iot-dps/about-iot-dps.md).

* Genom att **tillhandahålla enhets-SDK** : er kan du skapa appar som körs på dina IoT-enheter för att kommunicera med enhets etablerings tjänsten.

* Med **tjänst-SDK** : er för etablering kan du skapa backend-program för att hantera dina registreringar i Device Provisioning-tjänsten.

Lär dig mer om [fördelarna med att utveckla med Azure IoT SDK](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/): er.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="os-platform-and-hardware-compatibility"></a>Operativ system plattform och maskinvarukompatibilitet

Plattformar som stöds för SDK: er finns i [plattforms stöd för Azure IoT SDK](iot-hub-device-sdk-platform-support.md): er.

Mer information om SDK-kompatibilitet med specifika maskin varu enheter finns i [enhets katalogen för Azure Certified för IoT](https://catalog.azureiotsolutions.com/) eller i den enskilda lagrings platsen.

## <a name="azure-iot-hub-device-sdks"></a>SDK: er för Azure IoT Hub-enheter

Microsoft Azure IoT-enhetens SDK: er innehåller kod som underlättar skapandet av program som ansluter till och hanteras av Azure IoT Hub-tjänster.

Azure IoT Hub Device SDK för .NET: 

* Ladda ned från [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Namn området är Microsoft. Azure. devices. clients, som innehåller IoT Hub enhets klienter (DeviceClient, ModuleClient).
* [Källkod](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-referens](/dotnet/api/microsoft.azure.devices)
* [Modulreferens](/dotnet/api/microsoft.azure.devices.client.moduleclient)


Azure IoT Hub Device SDK för Embedded C (ANSI C-C99):
* [Bygg den inbäddade C SDK: n](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Källkod](https://github.com/Azure/azure-sdk-for-c)
* [Storleks diagram](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart) för begränsade enheter.
* [API-referens](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Identity/1.0.0/api/index.html)


Azure IoT Hub Device SDK för C (ANSI C-C99):

* Installera från [apt-get, MBED, ARDUINO IDE eller iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Källkod](https://github.com/Azure/azure-iot-sdk-c)
* [Kompilera C-enhetens SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API-referens](/azure/iot-hub/iot-c-sdk-ref/)
* [Modulreferens](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Porting C SDK till andra plattformar](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Dokumentation för utvecklare](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) som vill ha information om att korsa kompilering, komma igång på olika plattformar osv.
* [Information om resurs förbrukning för Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Azure IoT Hub Device SDK för java:

* Lägg till i [maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) -projekt
* [Källkod](https://github.com/Azure/azure-iot-sdk-java)
* [API-referens](/java/api/com.microsoft.azure.sdk.iot.device)
* [Modulreferens](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

Azure IoT Hub Device SDK för Node.js:

* Installera från [NPM](https://www.npmjs.com/package/azure-iot-device)
* [Källkod](https://github.com/Azure/azure-iot-sdk-node)
* [API-referens](/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest&preserve-view=true)
* [Modulreferens](/javascript/api/azure-iot-device/moduleclient)

Azure IoT Hub Device SDK för python:

* Installera från [pip](https://pypi.org/project/azure-iot-device/)
* [Källkod](https://github.com/Azure/azure-iot-sdk-python)
* [API-referens](/python/api/azure-iot-device)

Azure IoT Hub Device SDK för iOS:

* Installera från [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Exempel](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API-referens: se [C API-referens](/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>SDK: er för Azure IoT Hub-tjänst

SDK: er för Azure IoT-tjänsten innehåller kod för att under lätta skapandet av program som interagerar direkt med IoT Hub för att hantera enheter och säkerhet.

Azure IoT Hub service SDK för .NET:

* Ladda ned från [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Namn området är Microsoft. Azure. Devices, som innehåller IoT Hub tjänst klienter (RegistryManager, ServiceClients).
* [Källkod](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-referens](/dotnet/api/microsoft.azure.devices)

Azure IoT Hub service SDK för java:

* Lägg till i [maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) -projekt
* [Källkod](https://github.com/Azure/azure-iot-sdk-java)
* [API-referens](/java/api/com.microsoft.azure.sdk.iot.service)

Azure IoT Hub service SDK för Node.js:

* Ladda ned från [NPM](https://www.npmjs.com/package/azure-iothub)
* [Källkod](https://github.com/Azure/azure-iot-sdk-node)
* [API-referens](/javascript/api/azure-iothub/?view=azure-iot-typescript-latest&preserve-view=true)

Azure IoT Hub service SDK för python:

* Ladda ned från [pip](https://pypi.python.org/pypi/azure-iot-hub/)
* [Källkod](https://github.com/Azure/azure-iot-sdk-python/tree/master)
* [API-referens](/python/api/azure-iot-hub)

Azure IoT Hub service SDK för C:

Azure IoT service SDK för C är inte längre under aktiv utveckling.
Vi kommer att fortsätta att åtgärda viktiga buggar, till exempel krascher, skadade data och säkerhets problem. Vi kommer inte att lägga till någon ny funktion eller åtgärda fel som inte är kritiska, men.

Support för Azure IoT service SDK är tillgängligt på högre nivå språk ([C#](https://github.com/Azure/azure-iot-sdk-csharp), [Java](https://github.com/Azure/azure-iot-sdk-java), [Node](https://github.com/Azure/azure-iot-sdk-node), [python](https://github.com/Azure/azure-iot-sdk-python)).

* Ladda ned från [apt-get, MBED, ARDUINO IDE eller NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Källkod](https://github.com/Azure/azure-iot-sdk-c)

Azure IoT Hub service SDK för iOS:

* Installera från [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Exempel](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> I README-filerna i GitHub-lagringsplatserna hittar du information om hur du använder språk-och plattformsspecifik paket hanterare för att installera binärfiler och beroenden på din utvecklings dator.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure etablering av SDK: er

Med **SDK: erna för Microsoft Azure etablering** kan du etablera enheter till din IoT Hub med [enhets etablerings tjänsten](../iot-dps/about-iot-dps.md).

Azure-etablering av enhets-och tjänst-SDK: er för C#:

* Ladda ned från [Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) och [service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) från NuGet.
* [Källkod](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API-referens](/dotnet/api/microsoft.azure.devices.provisioning.client)

Azure-etablering av enhets-och tjänst-SDK: er för C:

* Installera från [apt-get, MBED, ARDUINO IDE eller iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Källkod](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API-referens](/azure/iot-hub/iot-c-sdk-ref/)

Azure-etablering av enheter och tjänst-SDK: er för java:

* Lägg till i [maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) -projekt
* [Källkod](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API-referens](/java/api/com.microsoft.azure.sdk.iot.provisioning.device)

Azure-etablering av enhets-och tjänst-SDK: er för Node.js:

* [Källkod](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API-referens](/javascript/api/overview/azure/iothubdeviceprovisioning)
* Hämta [enhets-SDK](https://badge.fury.io/js/azure-iot-provisioning-device) och [service SDK](https://badge.fury.io/js/azure-iot-provisioning-service) från NPM

Azure-etablering av enhets-och tjänst-SDK: er för python:

* [Källkod](https://github.com/Azure/azure-iot-sdk-python)
* Hämta [enhets-SDK](https://pypi.org/project/azure-iot-device/) och [service SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) från pip

## <a name="next-steps"></a>Nästa steg

Azure IoT SDK: er tillhandahåller också en uppsättning verktyg som hjälper dig att utveckla:

* [iothub-diagnostik](https://github.com/Azure/iothub-diagnostics): ett kommando rads verktyg för flera plattformar för att diagnostisera problem som rör anslutning till IoT Hub.
* [Azure-IoT-Explorer](https://github.com/Azure/azure-iot-explorer): ett plattforms oberoende Skriv bords program för att ansluta till din IoT Hub och lägga till/hantera/kommunicera med IoT-enheter.

Relevanta dokument som rör utveckling med Azure IoT-SDK: er:

* Lär dig mer om [hur du hanterar anslutningar och Reliable Messaging](iot-hub-reliability-features-in-sdks.md) med hjälp av IoT Hub SDK: er.
* Lär dig mer om hur du [utvecklar för mobila plattformar](iot-hub-how-to-develop-for-mobile-devices.md) som iOS och Android.
* [Stöd för Azure IoT SDK-plattformen](iot-hub-device-sdk-platform-support.md)

Andra referens ämnen i den här IoT Hub Developer Guide är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md)
* [IoT Hub frågespråk för enhets dubbla, jobb och meddelanderoutning](iot-hub-devguide-query-language.md)
* [Kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-stöd](iot-hub-mqtt-support.md)
* [IoT Hub REST API referens](/rest/api/iothub/)
