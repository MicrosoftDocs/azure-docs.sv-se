---
title: Arkitektur koncept i Azure IoT Central-sol panel | Microsoft Docs
description: I den här artikeln beskrivs viktiga begrepp som rör arkitekturen i Azure IoT Central solpanels övervakningsprogram.
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 1efb693e1ad9b9e9bfdbeafa27e9cc75c645baf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99834297"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>App-arkitektur för Azure IoT Central-solpanel

Den här artikeln innehåller en översikt över mallen för att övervaka appar i sol panelen. Diagrammet nedan visar en arkitektur för solpanel-appen på Azure som använder IoT Central plattform.

> [!div class="mx-imgBorder"]
> ![arkitektur för smart mätare](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Den här arkitekturen består av följande komponenter. Vissa program kanske inte kräver alla komponenter som anges här.

## <a name="solar-panels-and-connectivity"></a>Sol paneler och anslutningar

Sol-paneler är en av de betydande källorna till förnybar energi. Beroende på typ av sol panel och konfiguration kan du ansluta den antingen med hjälp av gatewayer eller andra mellanliggande enheter och patentskyddade system. Du kan behöva skapa IoT Central enhets brygga för att ansluta enheter som inte kan anslutas direkt. IoT Central Device Bridge är en lösning med öppen källkod och du hittar den fullständiga informationen [här](../core/howto-build-iotc-device-bridge.md). 

## <a name="iot-central-platform"></a>IoT Central plattform
Azure IoT Central är en plattform som fören klar skapandet av IoT-lösningen och hjälper till att minska belastningen och kostnaderna för IoT-hantering, åtgärder och utveckling. Med IoT Central kan du enkelt ansluta, övervaka och hantera dina Sakernas Internet (IoT)-till gångar i stor skala. När du har anslutit dina solpaneler till IoT Central, använder app-mallen inbyggda funktioner, till exempel enhets modeller, kommandon och instrument paneler. App-mallen använder också IoT Central lagring för varma sökvägar, till exempel data övervakning, analys, regler och visualisering i nära real tid.


## <a name="extensibility-options-to-build-with-iot-central"></a>Utöknings alternativ för att bygga med IoT Central
IoT Centrals plattformen innehåller två alternativ för utöknings barhet: kontinuerlig data export (CDE) och API: er. Kunder och partner kan välja mellan de här alternativen för att anpassa sina lösningar efter speciella behov. Till exempel är en av våra partner konfigurerade CDE med Azure Data Lake Storage (ADLS). De använder ADLS för långsiktig datakvarhållning och andra lagrings scenarier för kall vägar, t. ex. batchbearbetning, granskning och rapportering. 

## <a name="next-steps"></a>Nästa steg

* Nu när du har lärt dig om arkitekturen kan du [skapa en solpanel-app utan kostnad](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Mer information om IoT Central finns i [IoT Central översikt](../index.yml)
