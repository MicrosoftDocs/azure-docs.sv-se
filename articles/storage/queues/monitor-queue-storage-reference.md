---
title: Azure Queue Storage för övervakningsdata
description: Logg- och måttreferens för övervakningsdata från Azure Queue Storage.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 04/20/2021
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: 506f5a46688f597b8ac5db341c5bbe5eb5fb67c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763153"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Azure Queue Storage för övervakningsdata

Se [Övervakning Azure Storage](monitor-queue-storage.md) för information om hur du samlar in och analyserar övervakningsdata för Azure Storage.

## <a name="metrics"></a>Mått

I följande tabeller visas de plattformsmått som samlats in för Azure Storage.

### <a name="capacity-metrics"></a>Kapacitetsmått

Kapacitetsmåttvärden uppdateras dagligen (upp till 24 timmar). Tidsintervallet definierar det tidsintervall för vilket måttvärden visas. Det tidsslag som stöds för alla kapacitetsmått är en timme (PT1H).

Azure Storage tillhandahåller följande kapacitetsmått i Azure Monitor.

#### <a name="account-level-capacity-metrics"></a>Kapacitetsmått på kontonivå

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Queue Storage mått

Den här tabellen [Queue Storage mått](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices).

| Metric | Beskrivning |
| ------------------- | ----------------- |
| **QueueCapacity** | Den mängd Queue Storage används av lagringskontot. <br><br> Enhet: `Bytes` <br> Sammansättningstyp: `Average` <br> Värdeexempel: `1024` |
| **QueueCount** | Antalet köer i lagringskontot. <br><br> Enhet: `Count` <br> Sammansättningstyp: `Average` <br> Värdeexempel: `1024` |
| **QueueMessageCount** | Antalet kömeddelanden som inte har utkommit på lagringskontot. <br><br> Enhet: `Count` <br> Sammansättningstyp: `Average` <br> Värdeexempel: `1024` |

### <a name="transaction-metrics"></a>Transaktionsmått

Transaktionsmått genereras vid varje begäran till ett lagringskonto från Azure Storage till Azure Monitor. Om det inte finns någon aktivitet på ditt lagringskonto kommer det inte att finnas några data om transaktionsmått under perioden. Alla transaktionsmått är tillgängliga på både konto- Queue Storage på tjänstnivå. Tidsintervallet definierar det tidsintervall som måttvärden visas. De tidsperioder som stöds för alla transaktionsmått är PT1H och PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Måttdimensioner

Azure Storage har stöd för följande dimensioner för mått i Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Resursloggar (förhandsversion)

> [!NOTE]
> Azure Storage loggar i Azure Monitor är i offentlig förhandsversion och är tillgänglig för förhandsversionstestning i alla offentliga molnregioner. Den här förhandsversionen aktiverar loggar för blobar (inklusive Azure Data Lake Storage Gen2), filer, köer, tabeller, premiumlagringskonton i allmänna v1- och general-purpose v2-lagringskonton. Klassiska lagringskonton stöds inte.

I följande tabell visas egenskaperna för Azure Storage resursloggar när de samlas in i Azure Monitor-loggar eller Azure Storage. Egenskaperna beskriver åtgärden, tjänsten och den typ av auktorisering som användes för att utföra åtgärden.

### <a name="fields-that-describe-the-operation"></a>Fält som beskriver åtgärden

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Fält som beskriver hur åtgärden autentiserades

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Fält som beskriver tjänsten

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Se även

- En [beskrivning av Azure Queue Storage](monitor-queue-storage.md) finns i Övervakning av Azure Queue Storage.
- Se [Övervaka Azure-resurser med Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md) information om övervakning av Azure-resurser.
