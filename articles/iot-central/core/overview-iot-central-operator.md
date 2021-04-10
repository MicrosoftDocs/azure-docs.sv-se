---
title: Azure IoT Central-operatörs guide
description: Azure IoT Central är en IoT-programplattform som gör skapandet av IoT-lösningar enklare. Den här artikeln innehåller en översikt över operatörs rollen i IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b8692973f187743e282de6f8e54a55363cc3105b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669951"
---
# <a name="iot-central-operator-guide"></a>IoT Central operatörs guide

*Den här artikeln gäller för operatörer.*

Med ett IoT Central program kan du övervaka och hantera miljon tals enheter under deras livs cykel. Den här guiden är för operatörer som använder ett IoT Central program för att hantera IoT-enheter.

En operator:

- Övervakar och hanterar enheterna som är anslutna till programmet.
- Felsöker och åtgärdar problem med enheter.
- Etablerar nya enheter.

## <a name="monitor-and-manage-devices"></a>Övervaka och hantera enheter

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="Skärm bild som visar en enhets visning":::

För att övervaka enheter kan en operatör använda de enhetsspecifika vyer som definieras av Solution Builder som en del av enhets mal len. Dessa vyer kan visa enhetens telemetri och egenskaps värden. Ett exempel är vyn **Översikt** som visas på föregående skärm bild.

För mer detaljerad information kan en operatör använda enhets grupper och de inbyggda analys funktionerna. Mer information finns i [använda analyser för att analysera enhets data](howto-create-analytics.md).

För att kunna hantera enskilda enheter kan en operatör använda enhets visningar för att ange egenskaper för enhet och moln och anropa enhets kommandon. Exempel finns vyerna **Hantera enhet** och **kommandon** i föregående skärm bild.

För att hantera enheter i bulk kan en operatör skapa och schemalägga jobb. Jobb kan uppdatera egenskaper och köra kommandon på flera enheter. Mer information finns i [skapa och köra ett jobb i ditt Azure IoT Central-program](howto-run-a-job.md).

## <a name="troubleshoot-and-remediate-issues"></a>Felsöka och åtgärda problem

Operatören ansvarar för hälso tillståndet för programmet och dess enheter. [Fel söknings guiden](troubleshoot-connection.md) hjälper operatörer att diagnostisera och åtgärda vanliga problem. En operatör kan använda sidan **enheter** för att blockera enheter som verkar vara felaktiga tills problemet är löst.

## <a name="add-and-remove-devices"></a>Lägga till och ta bort enheter

Operatören kan lägga till och ta bort enheter i ditt IoT Central program individuellt eller i grupp. Mer information finns i [Hantera enheter i ditt Azure IoT Central-program](howto-manage-devices.md).

## <a name="personalize"></a>Anpassa

Operatörer kan skapa anpassade instrument paneler i ett IoT Central program som innehåller länkar till de resurser som de oftast använder. Mer information finns i [hantera instrument paneler](howto-create-personal-dashboards.md#manage-dashboards).

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om hur du använder IoT Central, är de föreslagna nästa steg som du behöver för att testa snabb starterna, från och med [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md).
