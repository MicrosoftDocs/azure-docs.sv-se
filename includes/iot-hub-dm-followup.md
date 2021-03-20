---
title: ta med fil
description: ta med fil
services: iot-hub
ms.service: iot-hub
author: robinsh
ms.topic: include
ms.date: 02/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7f1f7d6f9ab6036fbcfcd1d19e175302bbd1a2a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87298703"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Anpassa och utöka enhets hanterings åtgärderna

Dina IoT-lösningar kan utöka den definierade uppsättningen enhets hanterings mönster eller aktivera anpassade mönster genom att använda enhetens dubbla och moln-till-enhet-metoden. Andra exempel på enhets hanterings åtgärder är fabriks återställning, uppdatering av inbyggd program vara, program uppdatering, energispar funktioner, nätverks-och anslutnings hantering och data kryptering.

## <a name="device-maintenance-windows"></a>Enhets underhålls fönster

Normalt konfigurerar du enheter för att utföra åtgärder i taget som minimerar avbrott och stillestånds tid. Enhets underhålls fönster är ett vanligt mönster för att definiera den tidpunkt då en enhet ska uppdatera konfigurationen. Dina backend-lösningar kan använda enhetens önskade egenskaper för att definiera och aktivera en princip på din enhet som aktiverar en underhålls period. När en enhet tar emot underhålls fönster principen kan den använda enhetens rapporterade egenskap för att rapportera status för principen. Backend-appen kan sedan använda enhets dubbla frågor för att intyga efterlevnaden av enheter och varje princip.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du en direkt metod för att utlösa en fjärran sluten omstart på en enhet. Du använde de rapporterade egenskaperna för att rapportera den senaste omstarts tiden från enheten och frågade enheten med en enhets gräns för att identifiera den senaste omstarts tiden för enheten från molnet.

Om du vill fortsätta att komma igång med IoT Hub-och enhets hanterings mönster, till exempel fjärran sluten av den inbyggda program varan, kan du läsa mer i [Uppdatera](../articles/iot-hub/tutorial-firmware-update.md)

Information om hur du utökar din IoT-lösning och schemalägger metod anrop på flera enheter finns i [schema-och sändnings jobb](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).