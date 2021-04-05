---
title: Snabb start – Konfigurera regler och åtgärder i Azure IoT Central
description: Den här snabb starten visar dig som ett verktyg för att konfigurera telemetri-baserade regler och åtgärder i ditt Azure IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 90fc1385afb2ef921828465ba030674281e96ebf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833855"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Snabbstart: Konfigurera regler och åtgärder för enheten i Azure IoT Central

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

I den här snabb starten skapar du en regel som skickar ett e-postmeddelande när den fuktighet som rapporteras av en enhets sensor överskrider 55%.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra de två föregående snabb starterna [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) och [lägga till en simulerad enhet till ditt IoT Central-program](./quick-create-simulated-device.md) för att skapa **sensor styrenhetens** enhets mall för att arbeta med.

## <a name="create-a-telemetry-based-rule"></a>Skapa en telemetribaserad regel

1. Om du vill lägga till en ny telemetri-baserad regel i programmet väljer du **regler** i det vänstra fönstret.

1. Om du vill skapa en ny regel väljer du **+ ny**.

1. Ange **miljö fuktighet** som regel namn.

1. I avsnittet **mål enheter** väljer du **sensor styrenhet** som enhets mal len. Med det här alternativet filtreras de enheter som regeln gäller för efter typ av enhets mal len. Du kan lägga till fler filter villkor genom att välja **+ filter**.

1. I avsnittet **villkor** definierar du vad som utlöser din regel. Använd följande information för att definiera ett villkor baserat på temperatur telemetri:

    | Fält        | Värde            |
    | ------------ | ---------------- |
    | Mått  | SensorHumid      |
    | Operator     | är större än  |
    | Värde        | 55               |

    Om du vill lägga till fler villkor väljer du **+ villkor**.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Skärm bild som visar regel villkoret":::

1. Om du vill lägga till en e-poståtgärd som ska köras när regeln utlöses väljer du **+ e-post**.

1. Använd informationen i följande tabell för att definiera din åtgärd och välj sedan **slutförd**:

    | Inställning   | Värde                                             |
    | --------- | ------------------------------------------------- |
    | Visningsnamn | E-poståtgärd för operatör                          |
    | Om du vill        | Din e-postadress                                |
    | Kommentarer     | Miljöns fuktighet överskred tröskelvärdet. |

    > [!NOTE]
    > För att ett e-postmeddelande ska kunna tas emot måste e-postadressen vara ett [användar-ID i programmet](howto-administer.md), och användaren måste har loggat in i programmet minst en gång.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="Skärm bild som visar en e-poståtgärd som lagts till i regeln":::

1. Välj **Spara**. Din regel listas på sidan **Regler**.

## <a name="test-the-rule"></a>Testa regeln

Strax efter att du har sparat regeln blir den aktiv. När villkoren som definieras i regeln uppfylls skickar ditt program ett e-postmeddelande till den adress som du angav i åtgärden.

> [!NOTE]
> När testet är färdigt stänger du av regeln för att sluta få aviseringar i inkorgen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Skapa en telemetribaserad regel
* Lägga till en åtgärd

Om du vill veta mer om övervakning av enheter som är anslutna till ditt program fortsätter du till snabb starten:

> [!div class="nextstepaction"]
> [Använd Azure-IoT central för att övervaka dina enheter](quick-monitor-devices.md).
