---
title: Snabbstart – Konfigurera regler och åtgärder i Azure IoT Central
description: Den här snabbstarten visar hur du som byggare konfigurerar telemetribaserade regler och åtgärder i ditt Azure IoT Central program.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: f65614de97e8ff6eed732e624ae30c3f2b70bd60
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589014"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Snabbstart: Konfigurera regler och åtgärder för enheten i Azure IoT Central

I den här snabbstarten skapar du en regel som skickar ett e-postmeddelande när luftfuktigheten som rapporteras av en enhetssensor överskrider 55 %.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra de två föregående snabbstarterna Skapa ett [Azure IoT Central-program](./quick-deploy-iot-central.md) och Lägga till en [simulerad](./quick-create-simulated-device.md) enhet i IoT Central-programmet för att skapa **sensorkontrollantenhetsmallen** att arbeta med.

## <a name="create-a-telemetry-based-rule"></a>Skapa en telemetribaserad regel

1. Om du vill lägga till en ny telemetribaserad regel i programmet väljer du Regler i den **vänstra rutan.**

1. Om du vill skapa en ny regel väljer du **+ Ny**.

1. Ange **Luftfuktighet för** miljö som regelnamn.

1. I avsnittet **Målenheter** väljer du **Sensor Controller** som enhetsmall. Det här alternativet filtrerar de enheter som regeln gäller för efter typ av enhetsmall. Du kan lägga till fler filtervillkor genom att **välja + Filtrera**.

1. I avsnittet **Villkor** definierar du vad som utlöser regeln. Använd följande information för att definiera ett villkor baserat på temperaturtelemetri:

    | Fält        | Värde            |
    | ------------ | ---------------- |
    | Mått  | SensorHumid      |
    | Operator     | är större än  |
    | Värde        | 55               |

    Om du vill lägga till fler villkor väljer **du + Villkor**.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Skärmbild som visar regelvillkoret":::

1. Om du vill lägga till en e-poståtgärd som ska köras när regeln utlöses väljer du **+ E-post**.

1. Använd informationen i följande tabell för att definiera åtgärden och välj sedan **Klar:**

    | Inställning   | Värde                                             |
    | --------- | ------------------------------------------------- |
    | Visningsnamn | E-poståtgärd för operatör                          |
    | Om du vill        | Din e-postadress                                |
    | Kommentarer     | Luftfuktigheten överskred tröskelvärdet. |

    > [!NOTE]
    > För att ett e-postmeddelande ska kunna tas emot måste e-postadressen vara ett [användar-ID i programmet](howto-administer.md), och användaren måste har loggat in i programmet minst en gång.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="Skärmbild som visar en e-poståtgärd som lagts till i regeln":::

1. Välj **Spara**. Din regel listas på sidan **Regler**.

## <a name="test-the-rule"></a>Testa regeln

Strax efter att du har sparat regeln blir den aktiv. När villkoren som definierats i regeln uppfylls skickar programmet ett e-postmeddelande till den adress som du angav i åtgärden.

> [!NOTE]
> När testet är färdigt stänger du av regeln för att sluta få aviseringar i inkorgen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Skapa en telemetribaserad regel
* Lägga till en åtgärd

Om du vill veta mer om övervakning av enheter som är anslutna till ditt program fortsätter du till snabbstarten:

> [!div class="nextstepaction"]
> [Använd Azure IoT Central för att övervaka dina enheter.](quick-monitor-devices.md)
