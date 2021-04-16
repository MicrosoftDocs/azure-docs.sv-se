---
title: Vad är Azure IoT Edge för Linux i Windows | Microsoft Docs
description: Översikt över hur du kan köra Linux IoT Edge moduler på Windows 10 enheter
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 3c7fd6c842d465dd5af5257628044666f10f2ece
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538207"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Vad är Azure IoT Edge for Linux on Windows (förhandsversion)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge för Linux i Windows kan du köra Linux-arbetsbelastningar i containrar tillsammans med Windows-program i Windows IoT-distributioner. Företag som förlitar sig på Windows IoT för att driva sina gränsenheter kan nu dra nytta av de molnbaserade analyslösningar som byggs i Linux.

IoT Edge för Linux i Windows fungerar genom att köra en virtuell Linux-dator på en Windows-enhet. Den virtuella Linux-datorn är förinstallerad med IoT Edge runtime. Alla IoT Edge moduler som distribueras till enheten körs på den virtuella datorn. Under tiden kan Windows-program som körs på Windows-värdenheten kommunicera med de moduler som körs på den virtuella Linux-datorn.

[Kom igång](how-to-install-iot-edge-on-windows.md) med förhandsversionen idag.

>[!NOTE]
>Överväg att använda vår [produktundersökning för](https://aka.ms/AzEFLOW-Registration) att hjälpa oss att Azure IoT Edge för Linux på Windows baserat på din IoT Edge bakgrund och mål. Du kan också använda den här undersökningen för att registrera dig för framtida Azure IoT Edge för Linux på Windows-meddelanden.

## <a name="components"></a>Komponenter

IoT Edge för Linux i Windows använder följande komponenter för att linux- och Windows-arbetsbelastningar ska kunna köras tillsammans med varandra och kommunicera sömlöst:

* **En virtuell Linux-dator** som kör Azure IoT Edge: En virtuell Linux-dator, som baseras på Microsofts första parts [operativsystemet Havenr,](https://github.com/microsoft/CBL-Mariner) byggs med IoT Edge-körningen och valideras som en miljö på nivå 1 som stöds för IoT Edge arbetsbelastningar.

* **Windows Admin Center:** Ett IoT Edge-tillägg för Windows Admin Center underlättar installation, konfiguration och diagnostik av IoT Edge på den virtuella Linux-datorn. Windows Admin Center kan distribuera IoT Edge för Linux på Windows på den lokala enheten eller ansluta till målenheter och fjärrhantera dem.

* **Microsoft Update:** Integrering med Microsoft Update ser till att Windows-körningskomponenterna, den virtuella Linux-datorn Och den virtuella Linux IoT Edge datorn är uppdaterade.

![Windows och den virtuella Linux-datorn körs parallellt, medan Windows Admin Center styr båda komponenterna](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Dubbelriktad kommunikation mellan Windows-processen och den virtuella Linux-datorn innebär att Windows-processer kan tillhandahålla användargränssnitt eller maskinvaruproxier för arbetsbelastningar som körs i Linux-containrar.

## <a name="samples"></a>Exempel

IoT Edge för Linux i Windows betonar samverkan mellan Linux- och Windows-komponenterna.

Exempel som visar kommunikationen mellan Windows-program och IoT Edge-moduler finns [i EFLOW & Windows 10 IoT Samples](https://aka.ms/AzEFLOW-Samples).

## <a name="public-preview"></a>Offentlig förhandsversion

IoT Edge för Linux i Windows är för närvarande i [offentlig förhandsversion.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Installation och hanteringsprocesser kan vara annorlunda än för allmänt tillgängliga funktioner.

## <a name="support"></a>Support

Använd IoT Edge och feedbackkanaler för att få hjälp med IoT Edge för Linux i Windows.

**Rapportera buggar** – Buggar kan rapporteras på [problemsidan i](https://github.com/azure/iotedge/issues) IoT Edge projekt med öppen källkod. Buggar som rör Azure IoT Edge för Linux i Windows kan rapporteras på sidan [med problem med iotedge-eflow.](https://aka.ms/AzEFLOW-Issues)

**Microsofts kundsupport –** Användare som har en [supportplan](https://azure.microsoft.com/support/plans/) kan kontakta Microsofts kundsupport genom att skapa en supportbiljett direkt från [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funktionsbegäranden** – Azure IoT Edge spårar funktionsförfrågningar via produktens [User Voice-sida](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="next-steps"></a>Nästa steg

Titta [IoT Edge för Linux på Windows 10 IoT Enterprise](https://aka.ms/EFLOWPPC9) mer information och ett exempel i praktiken.

Följ stegen i Installera och etablera Azure IoT Edge för Linux på en [Windows-enhet för](how-to-install-iot-edge-on-windows.md) att konfigurera en enhet med IoT Edge för Linux på Windows.
