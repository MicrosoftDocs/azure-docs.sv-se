---
title: Vad är Azure IoT Edge för Linux i Windows | Microsoft Docs
description: Översikt över du kan köra Linux IoT Edge-moduler på Windows 10-enheter
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: ebe0ac1151f3a1f43072f2832e2f433182ccc82d
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634370"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Vad är Azure IoT Edge för Linux på Windows (för hands version)

Med Azure IoT Edge för Linux i Windows kan du köra Linux-arbetsbelastningar i behållare tillsammans med Windows-program i Windows IoT-distributioner. Företag som är beroende av Windows IoT för att driva sina gräns enheter kan nu dra nytta av de molnbaserade analys lösningarna som skapats i Linux.

IoT Edge för Linux i Windows fungerar genom att köra en virtuell Linux-dator på en Windows-enhet. Den virtuella Linux-datorn kommer att vara förinstallerad med IoT Edge Runtime. Alla IoT Edge moduler som distribueras till enheten körs i den virtuella datorn. Under tiden kan Windows-program som körs på Windows-värd enheten kommunicera med moduler som körs i den virtuella Linux-datorn.

>[!NOTE]
>Överväg att ta med vår [produkt undersökning](https://aka.ms/AzEFLOW-Registration) för att hjälpa oss att förbättra Azure IoT Edge för Linux på Windows baserat på din IoT Edge bakgrund och mål. Du kan också använda den här undersökningen för att registrera dig för framtida Azure IoT Edge för Linux på Windows-meddelanden.

## <a name="components"></a>Komponenter

IoT Edge för Linux i Windows använder följande komponenter för att aktivera Linux-och Windows-arbetsbelastningar att köras tillsammans med varandra och kommunicera sömlöst:

* **En virtuell Linux-dator som kör Azure IoT Edge**: en virtuell Linux-dator, som baseras på Microsofts första parts [Mariner](https://github.com/microsoft/CBL-Mariner) -operativsystem, är byggd med IoT Edge Runtime och verifieras som en miljö som stöds av nivå 1 för IoT Edge arbets belastningar.

* **Windows administrations Center**: ett IoT Edge tillägg för Windows administrations Center underlättar installation, konfiguration och diagnostik av IoT Edge på den virtuella Linux-datorn. Windows administrations Center kan distribuera IoT Edge för Linux i Windows på den lokala enheten, eller så kan du ansluta till mål enheter och hantera dem via en fjärr anslutning.

* **Microsoft Update**: integrering med Microsoft Update behåller Windows Runtime-komponenterna, den virtuella Mariner Linux-datorn och IoT Edge uppdaterad.

![Windows och virtuella Linux-datorer körs parallellt, medan Windows administrations Center styr båda komponenterna](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Dubbelriktad kommunikation mellan Windows-processen och den virtuella Linux-datorn innebär att Windows-processer kan tillhandahålla användar gränssnitt eller maskinvarubaserade proxyservrar för arbets belastningar som körs i Linux-behållare.

## <a name="samples"></a>Exempel

IoT Edge för Linux i Windows betonar samverkan mellan Linux-och Windows-komponenterna.

Exempel som demonstrerar kommunikation mellan Windows-program och IoT Edge moduler finns i [Windows 10 IoT-exempel](https://github.com/microsoft/Windows-IoT-Samples).

## <a name="public-preview"></a>Offentlig för hands version

IoT Edge för Linux i Windows är för närvarande en [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Installations-och hanterings processer kan skilja sig från för allmänt tillgängliga funktioner.

För närvarande använder IoT Edge för Linux i Windows Windows Insider Preview-versionen av Windows administrations Center. Mer information om Windows Insider program och registrering finns i [Vad är Windows Insider program?](https://insider.windows.com/about-windows-insider-program).

## <a name="support"></a>Support

Använd IoT Edge support och feedback-kanaler för att få hjälp med IoT Edge för Linux i Windows.

**Rapporterings buggar** – buggar kan rapporteras på [sidan problem](https://github.com/azure/iotedge/issues) i projektet IoT Edge med projekt med öppen källkod. 

**Microsofts kund support team** – användare som har ett [support](https://azure.microsoft.com/support/plans/) avtal kan engagera Microsofts kund support team genom att skapa ett Support ärende direkt från [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funktions förfrågningar** – Azure IoT Edge produkten spårar funktions förfrågningar via produktens [röst sida](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="next-steps"></a>Nästa steg

Se [IoT Edge för Linux på Windows 10 IoT Enterprise](https://aka.ms/EFLOWPPC9) för mer information och ett exempel på åtgärder.

Följ stegen i [Installera och etablera Azure IoT Edge för Linux på en Windows-enhet](how-to-install-iot-edge-on-windows.md) för att konfigurera en enhet med IoT Edge för Linux i Windows.
