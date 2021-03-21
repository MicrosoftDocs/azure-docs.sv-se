---
title: Översikt över enhets simulering – Azure | Microsoft Docs
description: En beskrivning av lösnings Accelerator för enhets simulering och dess funktioner.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 7be0278fd8870395b8495168eb9723780eba4b26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852378"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Översikt över lösningsacceleratorn för enhetssimulering

I en molnbaserad IoT-lösning ansluter dina enheter till en moln slut punkt för att skicka telemetri, till exempel temperatur, plats och status. Din lösning förbrukar denna telemetri, så att du kan vidta åtgärder eller härleda insikter från den.

När du utvecklar en IoT-lösning är experimentering och testning viktiga delar av processen. Simulering är ett viktigt verktyg i hela den här processen. Med enhets simulering kan du:

* Snabbt få en prototyp igång och upprepa genom att justera det simulerade enhets beteendet i farten. Med den här processen kan du bevisa att du har en bra idé att investera i kostsam maskin vara. Du kan skapa anpassade enheter via webb gränssnittet för att generera en prototyp enhet på några sekunder.
* Verifiera att lösningen fungerar som förväntat från enhet till lösning genom att simulera verkliga enhets beteenden. Du kan skripta komplexa enhets beteenden med hjälp av Java Script för att skapa realistiska simulerad telemetri.
* Skala testet till din lösning genom att simulera normala, högsta och bortom belastnings belastnings förhållanden. Med skalnings test kan du också få rätt storlek på de Azure-resurser som behövs för att köra din lösning.

![Simulering av exempel drönare](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Med enhets simulering kan du definiera enhets modeller för att simulera dina riktiga enheter. Den här modellen innehåller meddelande format, dubbla egenskaper och metoder. Du kan också simulera komplexa enhets beteenden med Java Script.

Du kan köra simuleringar för en till tusentals enheter som ansluter till en IoT-hubb. För att hjälpa till med testningen kan du distribuera en IoT-hubb tillsammans med enhets simulering för en fristående miljö.

Enhets simuleringen är kostnads fri. Men enhets simuleringen distribueras till din Azure-prenumeration i molnet och använder Azure-resurser. Om enhets simuleringen inte uppfyller dina krav finns [även käll koden på GitHub](https://github.com/Azure/device-simulation-dotnet) så att du kan kopiera och ändra.

## <a name="sample-simulations"></a>Exempel simuleringar

När du distribuerar enhets simulering får du några exempel på simuleringar och exempel enheter. Du kan använda de här exemplen för att lära dig hur du använder enhets simulering. Kom igång genom att köra en [simulering](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)av ett exempel. Du kan också [skapa en egen simulering med någon av de många exempel enheter som tillhandahålls](iot-accelerators-device-simulation-create-simulation.md).

![Simuleringskonfiguration](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Anpassade simulerade enheter

Du kan använda enhets simuleringen för att [skapa anpassade enhets modeller](iot-accelerators-device-simulation-create-custom-device.md) som du kan använda i dina simuleringar. Du kan till exempel definiera en ny enhets modell för kyl skåp som skickar telemetri för temperatur och fuktighet. Anpassade simulerade enheter är idealiska för enkla enhets beteenden med värden för slumpmässiga, ökande eller decrementing telemetri.

![Skapa enhetsmodell](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Avancerade simulerade enheter

När du behöver mer kontroll över telemetri-värden som en enhet skickar, kan du använda en avancerad enhets modell. Med avancerade enhets modeller kan du hantera JavaScript-stöd för att manipulera de skickade telemetridata. Du kan till exempel simulera invändig temperatur för en parkerad bil på en varm solig dag – när den yttre temperaturen stiger ökar den inre temperaturen exponentiellt.

Med avancerade enhets modeller kan du [skapa och ladda upp dina egna enhets modeller](iot-accelerators-device-simulation-advanced-device.md) som består av en JSON-enhets definitions fil och motsvarande JavaScript-filer.

Med avancerade enhets modeller kan du:

* Ange meddelande formatet som skickas från enheten tillsammans med telemetri typerna.
* Använd anpassade skript för att generera telemetri värden som upprätthåller enhetens status över tid.
* Använd anpassat skript för att ange hur den simulerade enheten svarar på metoder.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om enhets simuleringens lösnings Accelerator och dess funktioner. Om du vill distribuera Solution Accelerator går du till GitHub-lagringsplatsen:

> [!div class="nextstepaction"]
> [Distribuera och köra en IoT-enhetssimulering i Azure](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)
