---
title: Övervaka IoT Edge distributioner – Azure IoT Edge
description: Övervakning på hög nivå, inklusive egenskaper för edgeHub och edgeAgent och mått för automatisk distribution.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 39e7bb5c151d490e79ef111589f52f260c3e6c7a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483171"
---
# <a name="monitor-iot-edge-deployments"></a>Övervaka IoT Edge-distributioner

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge innehåller rapporter som gör att du kan övervaka realtidsinformation om de moduler som distribueras till dina IoT Edge enheter. Tjänsten IoT Hub hämtar statusen från enheterna och gör dem tillgängliga för operatören. Övervakning är också viktigt för [distributioner i stor skala](module-deployment-monitoring.md) som omfattar automatiska distributioner och lagerdistributioner.

Både enheter och moduler har liknande data, till exempel anslutning, så värden hämtas enligt enhets-ID eller modul-ID.

Tjänsten IoT Hub samlar in data som rapporteras av enhets- och modultvillingarna och visar antalet olika tillstånd som enheter kan ha. Tjänsten IoT Hub organiserar dessa data i fyra grupper med mått:

| Typ | Beskrivning |
| --- | ---|
| Riktad | Visar IoT Edge enheter som matchar distributionsmålvillkoret. |
| Tillämpat | Visar målenheterna IoT Edge som inte är mål för en annan distribution med högre prioritet. |
| Rapporteringen lyckades | Visar IoT Edge enheter som har rapporterat att modulerna har distribuerats. |
| Rapporteringsfel | Visar IoT Edge enheter som har rapporterat att en eller flera moduler inte har distribuerats. Du kan undersöka felet ytterligare genom att fjärransluta till enheterna och visa loggfilerna. |

Tjänsten IoT Hub gör dessa data tillgängliga för dig att övervaka i Azure Portal och i Azure CLI.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Övervaka en distribution i Azure Portal

Använd följande steg för att visa information om en distribution och övervaka de enheter som kör den:

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till IoT Hub.
1. Välj **IoT Edge** på menyn till vänster.
1. Välj fliken **IoT Edge distributioner.**
1. Granska distributionslistan. För varje distribution kan du visa följande information:

    | Kolumn | Beskrivning |
    | --- | --- |
    | ID | Namnet på distributionen. |
    | Typ | Typen av distribution, antingen **Distribution** eller **Skiktad distribution**. |
    | Målvillkor | Taggen som används för att definiera målenheter. |
    | Prioritet | Det prioritetsnummer som tilldelats distributionen. |
    | Systemmått | Antalet enhetstvillingar i IoT Hub matchar målvillkoret. **Tillämpad** anger antalet enheter som har haft distributionsinnehållet tillämpat på sina modultvillingar i IoT Hub. |
    | Enhetsmått | Antalet enheter IoT Edge rapporterar lyckade eller fel från IoT Edge klientkörning. |
    | Anpassade mått | Antalet enheter IoT Edge som rapporterar data för mått som du har definierat för distributionen. |
    | Skapandetid | Tidsstämpeln från när distributionen skapades. Den här tidsstämpeln används för att bryta oavgjort när två distributioner har samma prioritet. |

1. Välj den distribution som du vill övervaka.  
1. På sidan **Distributionsinformation** rullar du ned till avsnittet längst ned och väljer **fliken Målvillkor.** Välj **Visa** för att visa de enheter som matchar målvillkoret. Du kan ändra villkoret och även **prioriteten**. Välj **Spara** om du har gjort ändringar.

   ![Visa målenheter för en distribution](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Välj **fliken** Mått. Om du väljer ett mått i **listrutan Välj** mått visas **knappen Visa** så att du kan visa resultatet. Du kan också välja **Redigera mått om** du vill justera villkoren för anpassade mått som du har definierat. Välj **Spara** om du har gjort ändringar.

   ![Visa mått för en distribution](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Information om hur du gör ändringar i distributionen finns [i Ändra en distribution.](how-to-deploy-at-scale.md#modify-a-deployment)

## <a name="monitor-a-deployment-with-azure-cli"></a>Övervaka en distribution med Azure CLI

Använd kommandot [az iot edge deployment show](/cli/azure/iot/edge/deployment) för att visa information om en enskild distribution:

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Kommandot deployment show tar följande parametrar:

* **--deployment-id** – Namnet på distributionen som finns i IoT-hubben. Obligatorisk parameter.
* **--hub-name** – Namnet på den IoT-hubb där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot `az account set -s [subscription name]`

Granska distributionen i kommandofönstret. Egenskapen **metrics** visar ett antal för varje mått som utvärderas av varje hubb:

* **targetedCount** – Ett systemmått som anger antalet enhetstvillingar i IoT Hub som matchar målvillkoret.
* **appliedCount** – Ett systemmått anger antalet enheter som har haft distributionsinnehållet tillämpat på sina modultvillingarna i IoT Hub.
* **reportedSuccessfulCount** – Ett enhetsmått som anger antalet IoT Edge enheter i distributionen som rapporterar att IoT Edge från klientkörningen.
* **reportedFailedCount** – Ett enhetsmått som anger antalet IoT Edge enheter i distributionsrapporteringsfelet från IoT Edge klientkörningen.

Du kan visa en lista över enhets-ID:er eller objekt för vart och ett av måtten med [kommandot az iot edge deployment show-metric:](/cli/azure/iot/edge/deployment)

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Kommandot för att visa mått för distribution använder följande parametrar:

* **--deployment-id** – Namnet på distributionen som finns i IoT-hubben.
* **--metric-id** – Namnet på måttet som du vill visa listan över enhets-ID:n för, till exempel `reportedFailedCount` .
* **--hub-name** – Namnet på den IoT-hubb där distributionen finns. Hubben måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot `az account set -s [subscription name]` .

Information om hur du gör ändringar i distributionen finns [i Ändra en distribution.](how-to-deploy-cli-at-scale.md#modify-a-deployment)

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du](how-to-monitor-module-twins.md)övervakar modultvillingarna , främst modulerna IoT Edge Agent och IoT Edge Hub runtime, för anslutning och hälsa för dina IoT Edge-distributioner.
