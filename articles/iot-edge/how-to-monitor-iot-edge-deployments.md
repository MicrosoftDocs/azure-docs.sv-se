---
title: Övervaka IoT Edge distributioner – Azure IoT Edge
description: Övervakning på hög nivå, inklusive edgeHub och edgeAgent rapporterade egenskaper och automatiska distributions mått.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 45df700cc1772250e42a0e007fb4ea91b49471ba
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684211"
---
# <a name="monitor-iot-edge-deployments"></a>Övervaka IoT Edge-distributioner

Azure IoT Edge ger rapporter som gör att du kan övervaka real tids information i de moduler som distribueras till dina IoT Edge-enheter. Tjänsten IoT Hub hämtar status från enheterna och gör dem tillgängliga för operatören. Övervakning är också viktigt för [distributioner som görs i skala](module-deployment-monitoring.md) , inklusive automatiska distributioner och lager distributioner.

Både enheter och moduler har liknande data, till exempel anslutningar, så att värdena hämtas enligt enhets-ID eller modul-ID.

IoT Hubs tjänsten samlar in data som rapporter ATS av enhet och modul, och innehåller antalet olika tillstånd som enheterna kan ha. Tjänsten IoT Hub organiserar dessa data i fyra grupper av mått:

| Typ | Description |
| --- | ---|
| Riktad | Visar de IoT Edge enheter som matchar villkoret för distributions målet. |
| Tillämpat | Visar mål IoT Edge enheter som inte är riktade till en annan distribution med högre prioritet. |
| Rapporten lyckades | Visar de IoT Edge enheter som har rapporterat att modulerna har distribuerats korrekt. |
| Rapporterings problem | Visar de IoT Edge enheter som har rapporterat att en eller flera moduler inte har distribuerats korrekt. Du kan undersöka felet genom att fjärrans luta till dessa enheter och Visa loggfilerna. |

Den IoT Hub tjänsten gör dessa data tillgängliga för att övervaka i Azure Portal och i Azure CLI.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Övervaka en distribution i Azure Portal

Använd följande steg för att visa information om en distribution och övervaka de enheter som kör den:

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT Hub.
1. Välj **IoT Edge** på menyn i den vänstra rutan.
1. Välj fliken **IoT Edge distributioner** .
1. Granska distributions listan. För varje distribution kan du se följande information:

    | Kolumn | Description |
    | --- | --- |
    | ID | Namnet på distributionen. |
    | Typ | Distributions typ, antingen **distribution** eller **lager distribution**. |
    | Mål villkor | Den tagg som används för att definiera mål enheter. |
    | Prioritet | Prioritets numret som tilldelats distributionen. |
    | System mått | Antalet enheter är dubbla i IoT Hub som matchar mål villkoret. **Tillämpad** anger det antal enheter som har haft det distributions innehåll som applicerats på deras modul är dubbla i IoT Hub. |
    | Enhets mått | Antalet IoT Edge enheter som rapporterar lyckade eller felaktiga enheter från IoT Edge klient körning. |
    | Anpassade mått | Antalet IoT Edge enheter som rapporterar data för alla mät värden som du har definierat för distributionen. |
    | Skapande tid | Tidsstämpeln från när distributionen skapades. Den här tidsstämpeln används för att avbryta relationer när två distributioner har samma prioritet. |

1. Välj den distribution som du vill övervaka.  
1. Rulla ned till det nedersta avsnittet på sidan **distributions information** och välj fliken **mål villkor** . Välj **Visa** för att visa en lista över de enheter som matchar mål villkoret. Du kan ändra villkoret och även **prioriteten**. Välj **Spara** om du har gjort ändringar.

   ![Visa riktade enheter för en distribution](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Välj fliken **mått** . Om du väljer ett mått från List rutan **Välj mått** visas en **Visa** -knapp där du kan visa resultatet. Du kan också välja **Redigera mått** om du vill justera villkoren för anpassade mått som du har definierat. Välj **Spara** om du har gjort ändringar.

   ![Visa mått för en distribution](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Information om hur du gör ändringar i distributionen finns i [ändra en distribution](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Övervaka en distribution med Azure CLI

Använd kommandot [az IoT Edge Deployment show](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show) för att visa information om en enda distribution:

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Kommandot Deployment show tar följande parametrar:

* **--Deployment-ID** – namnet på den distribution som finns i IoT Hub. Obligatorisk parameter.
* **--hubb-Name** -namnet på den IoT-hubb som distributionen finns i. Navet måste finnas i den aktuella prenumerationen. Växla till önskad prenumeration med kommandot `az account set -s [subscription name]`

Granska distributionen i kommando fönstret. Egenskapen **mått** visar ett antal för varje mått som utvärderas av varje hubb:

* **targetedCount** – ett system mått som anger antalet enheter i IoT Hub som matchar mål villkoret.
* **appliedCount** – ett system mått anger det antal enheter som har haft det distributions innehåll som tillämpas på deras modul är dubbla i IoT Hub.
* **reportedSuccessfulCount** – ett enhets mått som anger antalet IoT Edge enheter i distributions rapporten som lyckas från IoT Edge klient körning.
* **reportedFailedCount** – ett enhets mått som anger antalet IoT Edge enheter i distributions rapporterings felen från IoT Edge klient körningen.

Du kan visa en lista över enhets-ID: n eller objekt för vart och ett av måtten med kommandot [az IoT Edge Deployment show-Metric](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show-metric) :

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Kommandot Deployment show-Metric använder följande parametrar:

* **--Deployment-ID** – namnet på den distribution som finns i IoT Hub.
* **--Metric-ID** – namnet på det mått som du vill se listan över enhets-ID: n för, till exempel `reportedFailedCount` .
* **--hubb-Name** -namnet på den IoT-hubb som distributionen finns i. Navet måste finnas i den aktuella prenumerationen. Växla till den önskade prenumerationen med kommandot `az account set -s [subscription name]` .

Information om hur du gör ändringar i distributionen finns i [ändra en distribution](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [övervakar modulerna](how-to-monitor-module-twins.md), i huvudsak IoT Edge-Agent och IoT Edge Hub runtime-moduler, för anslutning och hälsa för dina IoT Edge-distributioner.
