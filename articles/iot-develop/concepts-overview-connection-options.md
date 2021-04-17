---
title: Läs mer om anslutningsalternativ för Azure IoT-enhetsutvecklare
description: Lär dig mer om vanliga alternativ och verktyg för enhetsanslutning för Azure IoT-enhetsutvecklare.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 8669919192b1e6394043842d7d23f8829ec7c71e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589558"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Översikt: Anslutningsalternativ för Azure IoT-enhetsutvecklare
Som utvecklare som arbetar med enheter har du flera alternativ för att ansluta och hantera Azure IoT-enheter. Den här artikeln innehåller en översikt över de vanligaste alternativen och verktygen som hjälper dig att ansluta och hantera enheter.

När du utvärderar Azure IoT-anslutningsalternativen för dina enheter är det bra att jämföra följande:
- Programplattformar för Azure IoT-enheter
- Verktyg för att ansluta och hantera enheter

## <a name="application-platforms-iot-central-and-iot-hub"></a>Programplattformar: IoT Central och IoT Hub
Azure IoT innehåller två tjänster som är plattformar för enhetsaktiverade molnprogram: Azure IoT Central och Azure IoT Hub. Du kan använda någon av dessa för att vara värd för ett IoT-program och ansluta enheter.
- [IoT Central](../iot-central/core/overview-iot-central.md) är utformat för att minska komplexiteten och kostnaden för att arbeta med IoT-lösningar. Det är ett SaaS-program (programvara som en tjänst) som tillhandahåller en komplett plattform för att vara värd för IoT-program. Du kan använda IoT Central för att effektivisera hela livscykeln för att skapa och hantera IoT-program. Webbgränssnittet förenklar arbetet med att skapa program och ansluter och hanterar från några upp till miljontals enheter. IoT Central använder IoT Hub för att skapa och hantera program, men ser till att informationen är transparent för användaren. I allmänhet ger IoT Central mindre komplexitet och utvecklingsarbete samt förenklad enhetshantering via webbgränssnittet.
- [IoT Hub](../iot-hub/about-iot-hub.md) fungerar som en central meddelandehubb för dubbelriktad kommunikation mellan IoT-program och anslutna enheter. Det är ett PaaS-program (plattform som en tjänst) som också tillhandahåller en plattform för att vara värd för IoT-program. Precis IoT Central kan den skalas för att stödja miljontals enheter. I allmänhet ger IoT Hub större kontroll och anpassning över din programdesign och fler alternativ för utvecklarverktyg för att arbeta med tjänsten, på bekostnad av ökad komplexitet för utveckling och hantering.

## <a name="tools-to-connect-and-manage-devices"></a>Verktyg för att ansluta och hantera enheter
När du har IoT Hub eller IoT Central värd för ditt IoT-program har du flera alternativ för utvecklingsverktyg. Du kan använda dessa verktyg för att ansluta till din valda IoT-programplattform och för att skapa och hantera program och enheter. I följande tabell sammanfattas vanliga verktygsalternativ. 

> [!NOTE]
> Förutom följande verktyg kan du programmatiskt skapa och hantera IoT-program med hjälp av REST API-, Azure-SDK:er eller Azure Resource Manager mallar. Mer information finns [](../iot-hub/about-iot-hub.md) i dokumentationen IoT Hub [IoT Central](../iot-central/core/overview-iot-central.md) tjänsten.

|Verktyg  |Stöder IoT-plattform &nbsp; &nbsp; &nbsp;&nbsp; |Dokumentation  |Description  |
|---------|---------|---------|---------|
|Centralt webbgränssnitt     | Centrala | [Central snabbstart](../iot-central/core/quick-deploy-iot-central.md) | Webbläsarbaserad portal för IoT Central. |
|Azure Portal     | Hub, Central      | [Skapa en IoT-hubb Azure Portal](../iot-hub/iot-hub-create-through-portal.md), [hantera IoT Central från Azure Portal](../iot-central/core/howto-manage-iot-central-from-portal.md)| Webbläsarbaserad portal för IoT Hub enheter. Fungerar även med andra Azure-resurser, inklusive IoT Central. |
|Azure IoT Explorer     | Hubb | [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer#azure-iot-explorer-preview) | Det går inte att skapa IoT-hubbar. Ansluter till en befintlig IoT-hubb för att hantera enheter. Används ofta med CLI eller portalen.|
|Azure CLI     | Hub, Central          | [Skapa en IoT-hubb med CLI](../iot-hub/iot-hub-create-using-cli.md), [hantera IoT Central från Azure CLI](../iot-central/core/howto-manage-iot-central-from-cli.md) | Kommandoradsgränssnitt för att skapa och hantera IoT-program. |
|Azure PowerShell     | Hub, Central   | [Skapa en IoT-hubb med PowerShell](../iot-hub/iot-hub-create-using-powershell.md), [hantera IoT Central från Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | PowerShell-gränssnitt för att skapa och hantera IoT-program |
|Azure IoT Tools för VS Code  | Hubb | [Skapa en IoT-hubb med Verktyg för VS Code](../iot-hub/iot-hub-create-use-iot-toolkit.md) | VS Code-tillägg för IoT Hub program. |

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om alternativen för att ansluta enheter till Azure IoT kan du utforska följande snabbstarter:
- IoT Central: Skapa [ett Azure IoT Central program](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [Skicka telemetri från en enhet till en IoT-hubb och övervaka den med Azure CLI](../iot-hub/quickstart-send-telemetry-cli.md)