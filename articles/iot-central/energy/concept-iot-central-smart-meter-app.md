---
title: Arkitektur koncept i Azure IoT Central – smart mätare | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp som rör arkitekturen i Azure IoT Central Energy app-mall
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 48dd95acf725e57d63f85c54c97b641935b049b9
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832376"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central – arkitektur för smart avläsning av program vara

Den här artikeln innehåller en översikt över arkitekturen för appar för smart avläsning av övervakning. Diagrammet nedan visar en arkitektur för smart avläsning av appar på Azure med hjälp av IoT Centrals plattform.

> [!div class="mx-imgBorder"]
> ![arkitektur för smart mätare](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Den här arkitekturen består av följande komponenter. Vissa lösningar kanske inte kräver varje komponent som visas här.

## <a name="smart-meters-and-connectivity"></a>Smarta mätare och anslutningar 

En smart mätare är en av de viktigaste enheterna bland alla energi till gångar. Den registrerar och kommunicerar energi förbruknings data till verktyg för övervakning och andra användnings fall, till exempel fakturerings-och svars tid för begäran. Baserat på mätar typen kan den ansluta till IoT Central antingen med hjälp av gatewayer eller andra mellanliggande enheter eller system, t. ex. enheter och huvud system. Skapa IoT Central enhets brygga för att ansluta enheter, som inte kan anslutas direkt. IoT Central Device Bridge är en lösning med öppen källkod och du hittar den fullständiga informationen [här](../core/howto-build-iotc-device-bridge.md). 

## <a name="iot-central-platform"></a>IoT Central plattform

Azure IoT Central är en plattform som fören klar skapandet av IoT-lösningen och hjälper till att minska belastningen och kostnaderna för IoT-hantering, åtgärder och utveckling. Med IoT Central kan du enkelt ansluta, övervaka och hantera dina Sakernas Internet (IoT)-till gångar i stor skala. När du har anslutit dina smart-mätare till IoT Central använder app-mallen inbyggda funktioner, till exempel enhets modeller, kommandon och instrument paneler. App-mallen använder också IoT Central lagring för varma sökvägar, till exempel data övervakning, analys, regler och visualisering i nära real tid. 

## <a name="extensibility-options-to-build-with-iot-central"></a>Utöknings alternativ för att bygga med IoT Central
IoT Centrals plattformen innehåller två alternativ för utöknings barhet: kontinuerlig data export (CDE) och API: er. Kunder och partner kan välja mellan de här alternativen för att anpassa sina lösningar efter speciella behov. Till exempel är en av våra partner konfigurerade CDE med Azure Data Lake Storage (ADLS). De använder ADLS för långsiktig datakvarhållning och andra lagrings scenarier för kall vägar, t. ex. batch-bearbetning, granskning och rapportering. 

## <a name="next-steps"></a>Nästa steg

* Nu när du har lärt dig om arkitekturen skapar du en [app för smart avläsning utan kostnad](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Mer information om IoT Central finns i [IoT Central översikt](../index.yml)
