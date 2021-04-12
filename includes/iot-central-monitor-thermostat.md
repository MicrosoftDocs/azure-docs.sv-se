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
ms.openlocfilehash: 77fdaf297fff0e145b1dd53908887bc14f9d3f14
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491132"
---
<!-- All needs updating -->
Som operatör i ditt Azure IoT Central-program kan du:

* Visa telemetri som skickats av de två termostat-komponenterna på **översikts** sidan:

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Visa enhetstelemetri":::

* Visa enhets egenskaperna på sidan **om** . Den här sidan visar egenskaperna från komponenten enhets information och de två termostat-komponenterna:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Visa enhets egenskaper":::

## <a name="customize-the-device-template"></a>Anpassa enhets mal len

Som en lösnings utvecklare kan du anpassa enhets mal len som IoT Central skapas automatiskt när temperatur styrenhets enheten är ansluten.

För att lägga till en moln egenskap för att lagra kund namnet som är associerat med enheten:

1. I ditt IoT Central-program går du till enhets mal len för **temperatur styrenheten** på sidan **mallar för enheter** .

1. I enhets mal len för **temperatur styrenhet** väljer du **moln egenskaper**.

1. Välj **Lägg till moln egenskap**. Ange *kundens namn* som **visnings namn** och välj **sträng** som **schema**. Välj sedan **Spara**.

Så här anpassar du hur kommandot **hämta Max-Min rapport** visas i ditt IoT Central-program:

1. Välj **Anpassa** i enhets mal len.

1. För **getMaxMinReport (thermostat1)** ersätter du *Hämta Max-Min rapporten.* med *rapporten Hämta thermostat1 status*.

1. För **getMaxMinReport (thermostat2)** ersätter du *Hämta Max-Min rapporten.* med *rapporten Hämta thermostat2 status*.

1. Välj **Spara**.

Så här anpassar du hur den skrivbara egenskaperna för **mål temperaturen** visas i ditt IoT Central-program:

1. Välj **Anpassa** i enhets mal len.

1. För **targetTemperature (thermostat1)** ersätter du *mål temperatur* med *mål temperatur (1)*.

1. För **targetTemperature (thermostat2)** ersätter du *mål temperatur* med *mål temperatur (2)*.

1. Välj **Spara**.

Termostat-komponenterna i **temperatur styrenhets** modellen inkluderar den skrivbara egenskapen för **mål temperatur** , enhets mal len innehåller egenskapen för namn molnet för **kunden** . Skapa en vy en operatör kan använda för att redigera dessa egenskaper:

1. Välj **vyer** och välj sedan panelen **Redigera enhet och moln data** .

1. Ange _Egenskaper_ som formulär namn.

1. Välj **mål temperatur (1)**,  **mål temperatur (2)** och egenskaper för **kund namn** . Välj sedan **Lägg till avsnitt**.

1. Spara ändringarna.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Vy för uppdatering av egenskaps värden":::

## <a name="publish-the-device-template"></a>Publicera enhetsmallen

Innan en operatör kan se och använda de anpassningar som du har gjort måste du publicera enhets mal len.

Välj **publicera** i enhets mal len **termostat** . I **mallen publicera den här enheten i program** panelen väljer du **publicera**.

En operatör kan nu använda **egenskapsvyn** för att uppdatera egenskapsvärdena och anropa kommandon med namnet **Hämta thermostat1 status rapport** och **Hämta thermostat2 status rapport** på sidan med enhets kommandon:

* Uppdatera skrivbara egenskaps värden på sidan **Egenskaper** :

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Uppdatera enhets egenskaperna":::

* Anropa kommandona från **kommando** sidan:

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Anropa kommandot":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Visa kommandots svar":::
