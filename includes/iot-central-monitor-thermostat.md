---
title: ta med fil
description: ta med fil
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017514"
---
Som operatör i ditt Azure IoT Central-program kan du:

* Visa telemetri som skickats av enheten på sidan **Översikt** :

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Visa enhetstelemetri":::

* Visa enhets egenskaperna på sidan **om** :

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Visa enhets egenskaper":::

## <a name="customize-the-device-template"></a>Anpassa enhets mal len

Som en lösnings utvecklare kan du anpassa enhets mal len som IoT Central skapas automatiskt när termostat-enheten är ansluten.

För att lägga till en moln egenskap för att lagra kund namnet som är associerat med enheten:

1. I ditt IoT Central-program navigerar du till enhets mal len **termostat** på sidan **enhets mallar** .

1. I enhets mal len **termostat** väljer du **moln egenskaper**.

1. Välj **Lägg till moln egenskap**. Ange *kundens namn* som **visnings namn** och välj **sträng** som **schema**. Välj sedan **Spara**.

Om du vill anpassa hur kommandot **hämta Max-Min rapport** visas i IoT Central-programmet väljer du **Anpassa** i enhets mal len. Ersätt **hämta Max-Min-rapport.** med *Hämta status rapport*. Välj sedan **Spara**.

**Termostat** -modellen innehåller den skrivbara egenskapen för **mål temperatur** , enhets mal len innehåller egenskapen för namn molnet för **kunden** . Skapa en vy en operatör kan använda för att redigera dessa egenskaper:

1. Välj **vyer** och välj sedan panelen **Redigera enhet och moln data** .

1. Ange _Egenskaper_ som formulär namn.

1. Välj egenskaperna för **mål temperatur** och **kund namn** . Välj sedan **Lägg till avsnitt**.

1. Spara ändringarna.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Vy för uppdatering av egenskaps värden":::

## <a name="publish-the-device-template"></a>Publicera enhetsmallen

Innan en operatör kan se och använda de anpassningar som du har gjort måste du publicera enhets mal len.

Välj **publicera** i enhets mal len **termostat** . I **mallen publicera den här enheten i program** panelen väljer du **publicera**.

En operatör kan nu använda **egenskapsvyn** för att uppdatera egenskapsvärdena och anropa ett kommando med namnet **Hämta status rapport** på sidan enhets kommandon:

* Uppdatera skrivbara egenskaps värden på sidan **Egenskaper** :

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Uppdatera enhets egenskaperna":::

* Anropa kommandona från **kommando** sidan:

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Anropa kommandot":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Visa kommandots svar":::
