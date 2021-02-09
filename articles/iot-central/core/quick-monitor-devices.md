---
title: Snabb start – övervaka dina enheter i Azure IoT Central
description: Snabb start – som en operatör lär du dig att använda ditt Azure IoT Central-program för att övervaka dina enheter.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4c63a9833e6b9a9b243d289d79428ddef1468253
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833889"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Snabb start: Använd Azure IoT Central för att övervaka dina enheter

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

Den här snabb starten visar dig, som en operatör, hur du använder ditt Microsoft Azure IoT Central-program för att övervaka dina enheter och ändra inställningar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du slutföra de tre föregående snabb starterna [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md), [lägga till en simulerad enhet i IoT Central programmet](./quick-create-simulated-device.md) och [Konfigurera regler och åtgärder för enheten](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Få ett meddelande

Azure IoT Central skickar meddelanden om enheter via e-post. Som ett verktyg lade du till en regel för att skicka ett meddelande till en operatör när fuktigheten i en ansluten enhets sensor överskred ett tröskelvärde. Som operatör kontrollerar du dina e-postmeddelanden för meddelanden.

Öppna e-postmeddelandet som du fick i slutet av [Konfigurera reglerna och åtgärderna för](quick-configure-rules.md) snabb starten av enheten. I e-postmeddelandet väljer du länken till enheten:

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="Skärm bild som visar e-postavisering":::

**Översikts** visningen för den simulerade enheten som du skapade i föregående snabb starter öppnas i webbläsaren:

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="Skärm bild som visar en översikt över enhet som utlöste meddelandet":::

## <a name="investigate-an-issue"></a>Undersöka ett problem

Som operatör kan du Visa information om enheten i vyerna **Översikt**, **om** och **kommandon** . Verktyget skapade en **hanterad enhets** vy där du kan redigera enhets information och ange enhets egenskaper.

Diagrammet på instrument panelen visar ett område med enhetens fuktighet. Du bestämmer att enhetens fuktighet är för hög.

## <a name="remediate-an-issue"></a>Lösa ett problem

Använd sidan **Hantera enhet** om du vill göra en ändring i enheten.

Ändra **mål temperatur** till 80 för att värma enheten och minska fuktigheten. Välj **Spara** för att uppdatera enheten. När enheten bekräftar inställningarna ändras statusen för egenskapen till **synkroniserad**:

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="Skärm bild som visar den uppdaterade mål temperatur inställningen för enheten":::

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Få ett meddelande
* Undersöka ett problem
* Lösa ett problem

Nu när du vet att du nu kan övervaka enheten är det föreslagna nästa steg att:

> [!div class="nextstepaction"]
> [Bygg och hantera en enhets mall](howto-set-up-template.md).
