---
title: Läs om anslutnings alternativ för Azure IoT Device-utvecklare
description: Läs om vanliga alternativ för enhets anslutning och verktyg för Azure IoT-utvecklare.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654562"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Översikt: anslutnings alternativ för Azure IoT Device-utvecklare
Som utvecklare som arbetar med enheter har du flera alternativ för att ansluta och hantera Azure IoT-enheter. Den här artikeln visar de vanligaste alternativen och verktygen som hjälper dig att ansluta och hantera enheter.

När du utvärderar alternativ för Azure IoT-anslutningar för dina enheter är det bra att jämföra följande objekt:
- Program plattformar för Azure IoT-enheter
- Verktyg för att ansluta och hantera enheter

## <a name="application-platforms-iot-central-and-iot-hub"></a>Programplattformar: IoT Central och IoT Hub
Azure IoT innehåller två tjänster som är plattformar för enhets aktiverade moln program: Azure IoT Central och Azure IoT Hub. Du kan använda antingen en för att vara värd för ett IoT-program och ansluta enheter.
- [IoT Central](../iot-central/core/overview-iot-central.md) har utformats för att minska komplexiteten och kostnaden för att arbeta med IoT-lösningar. Det är ett SaaS-program (Software-as-a-Service) som tillhandahåller en komplett plattform för IoT-program. Du kan använda IoT Central webb gränssnitt för att effektivisera hela livs cykeln för att skapa och hantera IoT-program. Webb gränssnittet fören klar aktiviteterna för att skapa program och att ansluta och hantera från några få upp till miljon tals enheter. IoT Central använder IoT Hub för att skapa och hantera program, men behåller informationen transparent för användaren. I allmänhet ger IoT Central minskad komplexitet och utvecklings arbete och förenklad enhets hantering via webb gränssnittet.
- [IoT Hub](../iot-hub/about-iot-hub.md) fungerar som en central meddelande hubb för dubbelriktad kommunikation mellan IoT-program och anslutna enheter. Det är ett PaaS-program (Platform-as-a-Service) som också är en plattform för att hantera IoT-program. Precis som IoT Central kan den skalas för att stödja miljon tals enheter. I allmänhet erbjuder IoT Hub bättre kontroll och anpassning över din program design och fler verktyg för utvecklarverktyg för att arbeta med tjänsten, till anskaffnings värde för viss ökning av utveckling och hantering.

## <a name="tools-to-connect-and-manage-devices"></a>Verktyg för att ansluta och hantera enheter
När du har valt IoT Hub eller IoT Central som värd för IoT-programmet har du flera alternativ för utvecklarverktyg. Du kan använda dessa verktyg för att ansluta till din valda IoT-programplattform och för att skapa och hantera program och enheter. I följande tabell sammanfattas vanliga verktygs alternativ. 

> [!NOTE]
> Förutom följande verktyg kan du program mässigt skapa och hantera IoT-program med hjälp av REST API, Azure-SDK: er eller Azure Resource Manager mallar. Du kan läsa mer i dokumentationen för [IoT Hub](../iot-hub/about-iot-hub.md) och [IoT Central](../iot-central/core/overview-iot-central.md) -tjänsten.

|Verktyg  |Stöder IoT-plattform &nbsp; &nbsp; &nbsp;&nbsp; |Dokumentation  |Description  |
|---------|---------|---------|---------|
|Centralt webb gränssnitt     | Centrala | [Central snabb start](../iot-central/core/quick-deploy-iot-central.md) | Webbläsarbaserad Portal för IoT Central. |
|Azure Portal     | Hubb, Central      | [Skapa en IoT-hubb med Azure Portal](../iot-hub/iot-hub-create-through-portal.md), [hantera IoT Central från Azure Portal](../iot-central/core/howto-manage-iot-central-from-portal.md)| Webbläsarbaserad Portal för IoT Hub och enheter. Fungerar även med andra Azure-resurser, inklusive IoT Central. |
|Azure CLI     | Hubb, Central          | [Skapa en IoT Hub med CLI](../iot-hub/iot-hub-create-using-cli.md), [Hantera IoT Central från Azure CLI](../iot-central/core/howto-manage-iot-central-from-cli.md) | Kommando rads gränssnitt för att skapa och hantera IoT-program. |
|Azure PowerShell     | Hubb, Central   | [Skapa en IoT-hubb med PowerShell](../iot-hub/iot-hub-create-using-powershell.md), [hantera IoT Central från Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | PowerShell-gränssnitt för att skapa och hantera IoT-program |
|Azure IoT Tools för VS Code  | Hubb | [Skapa en IoT-hubb med verktyg för VS-kod](../iot-hub/iot-hub-create-use-iot-toolkit.md) | VS Code-tillägg för IoT Hub program. |
|Azure IoT Explorer     | Hubb | [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) | Det går inte att skapa IoT-hubbar. Ansluter till en befintlig IoT-hubb för att hantera enheter. Används ofta med CLI eller Portal.|

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om alternativen för att ansluta enheter till Azure IoT kan du utforska följande snabb starter:
- IoT Central: [skapa ett Azure IoT Central-program](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [Skicka telemetri från en enhet till en IoT-hubb och övervaka den med Azure CLI](../iot-hub/quickstart-send-telemetry-cli.md)