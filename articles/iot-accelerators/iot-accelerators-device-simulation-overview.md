---
title: Översikt över enhetssimulering – Azure | Microsoft Docs
description: En beskrivning av lösningsacceleratorn för enhetssimulering och dess funktioner.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 27a23ff924c2fa9e9e35fec010ca2a177868eacc
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713920"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Översikt över lösningsacceleratorn för enhetssimulering

I en molnbaserad IoT-lösning ansluter dina enheter till en molnslutpunkt för att skicka telemetri som temperatur, plats och status. Din lösning använder den här telemetrin så att du kan vidta åtgärder eller härleda insikter från den.

När du utvecklar en IoT-lösning är experimentering och testning viktiga delar av processen. Simulering är ett viktigt verktyg i den här processen. Med enhetssimulering kan du:

* Få snabbt igång en prototyp och iterera genom att justera simulerat enhetsbeteende i farten. Med den här processen kan du bevisa idén innan du investerar i dyr maskinvara. Du kan skapa anpassade enheter via webbgränssnittet för att generera en prototypenhet på några sekunder.
* Validera att lösningen fungerar som förväntat från enhet till lösning genom att simulera verkliga enhetsbeteenden. Du kan skapa skript för komplexa enhetsbeteenden med Hjälp av JavaScript för att generera realistisk simulerad telemetri.
* Skala testa din lösning genom att simulera normala, högsta och bortom belastningstoppar. Skalningstester hjälper dig också att anpassa storleken på de Azure-resurser som behövs för att köra din lösning.

![Exempel på drönarsimulering](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Med enhetssimulering kan du definiera enhetsmodeller för att simulera dina verkliga enheter. Den här modellen innehåller meddelandeformat, tvillingegenskaper och metoder. Du kan också simulera komplexa enhetsbeteenden med JavaScript.

Du kan köra simuleringar för en till tusentals enheter som ansluter till valfri IoT-hubb. För att hjälpa till med testning kan du distribuera en IoT-hubb tillsammans med enhetssimulering för en fristående miljö.

Enhetssimulering är kostnadsfritt. Enhetssimulering distribueras dock till din Azure-prenumeration i molnet och förbrukar Azure-resurser. Om enhetssimuleringen inte uppfyller dina krav är [källkoden också tillgänglig på GitHub](https://github.com/Azure/azure-iot-pcs-device-simulation) där du kan kopiera och ändra den.

## <a name="sample-simulations"></a>Exempelsimuleringar

När du distribuerar enhetssimulering får du några exempelsimuleringar och exempelenheter. Du kan använda de här exemplen för att lära dig hur du använder enhetssimulering. Kom igång genom att köra en [exempelsimulering](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md). Du kan också [skapa en egen simulering med någon av de många exempelenheter som tillhandahålls.](iot-accelerators-device-simulation-create-simulation.md)

![Simuleringskonfiguration](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Anpassade simulerade enheter

Du kan använda enhetssimuleringen för [att skapa anpassade enhetsmodeller](iot-accelerators-device-simulation-create-custom-device.md) som ska användas i dina simuleringar. Du kan till exempel definiera en ny kylskåpsenhetsmodell som skickar telemetri om temperatur och luftfuktighet. Anpassade simulerade enheter är idealiska för enkla enhetsbeteenden med slumpmässiga, stegvisa eller minskande telemetrivärden.

![Skapa enhetsmodell](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Avancerade simulerade enheter

När du behöver mer kontroll över telemetrivärden som en enhet skickar kan du använda en avancerad enhetsmodell. Avancerade enhetsmodeller gör det möjligt för JavaScript-stöd att ändra de skickade telemetrivärdena. Du kan till exempel simulera den inre temperaturen för en bil som står på en varm solig dag – när temperaturen ökar ökar den inre temperaturen exponentiellt.

Med avancerade enhetsmodeller kan [du skapa och ladda upp egna enhetsmodeller](iot-accelerators-device-simulation-advanced-device.md) som består av en JSON-enhetsdefinitionsfil och motsvarande JavaScript-filer.

Med avancerade enhetsmodeller kan du:

* Ange det meddelandeformat som skickas från enheten tillsammans med telemetrityperna.
* Använd anpassade skript för att generera telemetrivärden som upprätthåller enhetens tillstånd över tid.
* Använd anpassade skript för att ange hur den simulerade enheten svarar på metoder.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om lösningsacceleratorn för enhetssimulering och dess funktioner. Om du vill distribuera lösningsacceleratorn går du till GitHub-lagringsplatsen:

> [!div class="nextstepaction"]
> [Distribuera och köra en IoT-enhetssimulering i Azure](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md)
