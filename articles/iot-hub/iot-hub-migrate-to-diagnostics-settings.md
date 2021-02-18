---
title: Migrera Azure IoT Hub Operations Monitoring till IoT Hub resurs loggar i Azure Monitor | Microsoft Docs
description: Så här uppdaterar du Azure IoT Hub att använda Azure Monitor i stället för drift övervakning för att övervaka status för åtgärder i IoT Hub i real tid.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 48b646881b12047b28490999a96326f6076af2c1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591836"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>Migrera IoT Hub från Operations Monitor till Azure Monitor resurs loggar

Kunder som använder [drift övervakning](iot-hub-operations-monitoring.md) för att spåra statusen för åtgärder i IoT Hub kan migrera det arbets flödet till [Azure Monitor resurs loggar](../azure-monitor/essentials/platform-logs-overview.md), en funktion i Azure Monitor. Resurs loggar tillhandahåller diagnostisk information på resurs nivå för många Azure-tjänster.

**Funktionerna för drift övervakning av IoT Hub är inaktuella** och har tagits bort från portalen. Den här artikeln innehåller steg för steg hur du flyttar dina arbets belastningar från Operations Monitoring till Azure Monitor resurs loggar. Mer information om tids linjen för tids linjen finns i [övervaka dina Azure IoT-lösningar med Azure Monitor och Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Uppdatera IoT Hub

Om du vill uppdatera din IoT Hub i Azure Portal ska du först skapa en diagnostisk inställning och sedan inaktivera åtgärds övervakning.  

### <a name="create-a--diagnostic-setting"></a>Skapa en diagnostisk inställning

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb.

1. I den vänstra rutan under **övervakning** väljer du **diagnostikinställningar**. Välj sedan **Lägg till diagnostisk inställning**.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="Skärm bild som visar diagnostikinställningar i avsnittet övervakning.":::

1. I fönstret **diagnostikinställningar** ger du diagnostisk inställning ett namn.

1. Under **kategori Detaljer** väljer du kategorier för de åtgärder som du vill övervaka. Mer information om vilka kategorier av åtgärder som är tillgängliga med IoT Hub finns i [resurs loggar](monitor-iot-hub-reference.md#resource-logs).

1. Under **mål information** väljer du var du vill skicka loggarna. Du kan välja valfri kombination av dessa mål:

   * Arkivera till ett lagringskonto
   * Strömma till en händelsehubb
   * Skicka till Azure Monitor loggar via en Log Analytics arbets yta

   Följande skärm bild visar en diagnostisk inställning som dirigerar åtgärder i kategorierna anslutningar och telemetri till en Log Analytics arbets yta:

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="Skärm bild som visar en slutförd diagnostisk inställning.":::

1. Spara inställningarna genom att klicka på **Spara**.

Nya inställningar börjar gälla om 10 minuter. Därefter visas loggar i det konfigurerade målet. Mer information om hur du konfigurerar diagnostik finns i [samla in och använda loggdata från dina Azure-resurser](../azure-monitor/essentials/platform-logs-overview.md).

Mer detaljerad information om hur du skapar diagnostikinställningar, inklusive med PowerShell och Azure CLI finns i [diagnostikinställningar](../azure-monitor/essentials/diagnostic-settings.md) i Azure Monitor-dokumentationen.

### <a name="turn-off-operations-monitoring"></a>Inaktivera drift övervakning

> [!NOTE]
> Från och med den 11 mars 2019 tas funktionen för Operations övervakning bort från IoT Hub Azure Portal-gränssnittet. Stegen nedan gäller inte längre. För att migrera, se till att rätt kategorier dirigeras till ett mål med en Azure Monitor diagnostisk inställning ovan.

När du har testat de nya diagnostikinställningar i ditt arbets flöde kan du inaktivera funktionen för drifts övervakning. 

1. I IoT Hub-menyn väljer du **Åtgärds övervakning**.

2. Under varje övervaknings kategori väljer du **ingen**.

3. Spara ändringarna i Operations Monitoring.

## <a name="update-applications-that-use-operations-monitoring"></a>Uppdatera program som använder Operations Monitoring

Scheman för drift övervakning och resurs loggar skiljer sig något. Det är viktigt att du uppdaterar de program som använder drift övervakning idag för att mappa till schemat som används av resurs loggar.

Dessutom erbjuder IoT Hub resurs loggar fem nya kategorier för spårning. När du har uppdaterat program för det befintliga schemat lägger du även till de nya kategorierna:

* Dubbla åtgärder från moln till enhet
* Dubbla åtgärder från enhet till moln
* Dubbla frågor
* Jobbåtgärder
* Direkta metoder

Information om de olika schema strukturerna finns i [resurs loggar](monitor-iot-hub-reference.md#resource-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Övervaka enheten Anslut och koppla från händelser med låg latens

För att övervaka enhets anslutning och från koppling av händelser i produktion, rekommenderar vi att du prenumererar på [händelsen **frånkopplad av enheten**](iot-hub-event-grid.md#event-types) på Event Grid för att få aviseringar och övervaka enhetens anslutnings tillstånd. I den här [självstudien](iot-hub-how-to-order-connection-state-events.md) får du lära dig hur du integrerar enhet anslutna och avkopplade händelser från IoT Hub i din IoT-lösning.

## <a name="next-steps"></a>Nästa steg

[Övervaka IoT Hub](monitor-iot-hub.md)