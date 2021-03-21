---
title: Data referens för Azure Queue Storage-övervakning
description: Logg och statistik referens för övervakning av data från Azure Queue Storage.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 10/02/2020
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 95f20737b044140fe12ea939e71cd2397cb4826d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576689"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Data referens för Azure Queue Storage-övervakning

Mer information om hur du samlar in och analyserar övervaknings data för Azure Storage finns i [övervaknings Azure Storage](monitor-queue-storage.md) .

## <a name="metrics"></a>Mått

I följande tabeller visas de plattforms mått som samlas in för Azure Storage.

### <a name="capacity-metrics"></a>Kapacitetsmått

Värdena för kapacitets värden uppdateras dagligen (upp till 24 timmar). Tids kornig het definierar tidsintervallet för vilka mått värden presenteras. Tids kornig het för alla kapacitets mått är en timme (PT1H).

Azure Storage tillhandahåller följande kapacitets mått i Azure Monitor.

#### <a name="account-level-capacity-metrics"></a>Kapacitets mått på konto nivå

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Queue Storage mått

I den här tabellen visas [Queue Storage mått](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices).

| Metric | Beskrivning |
| ------------------- | ----------------- |
| **QueueCapacity** | Mängden Queue Storage som används av lagrings kontot. <br><br> Processor `Bytes` <br> Sammansättnings typ: `Average` <br> Värde exempel: `1024` |
| **QueueCount** | Antalet köer i lagrings kontot. <br><br> Processor `Count` <br> Sammansättnings typ: `Average` <br> Värde exempel: `1024` |
| **QueueMessageCount** | Ungefärligt antal köa meddelanden i lagrings kontot. <br><br> Processor `Count` <br> Sammansättnings typ: `Average` <br> Värde exempel: `1024` |

### <a name="transaction-metrics"></a>Transaktionsmått

Transaktions mått genereras på varje begäran till ett lagrings konto från Azure Storage till Azure Monitor. Om det inte finns någon aktivitet på ditt lagrings konto kommer det inte att finnas några data om transaktions måtten under perioden. Alla transaktions mått är tillgängliga både på konto-och Queue Storage Service nivå. Tids kornig het definierar tidsintervallet som Mät värden presenteras. Tids korn som stöds för alla transaktions mått är PT1H och PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Storage stöder följande dimensioner för mått i Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Resurs loggar (förhands granskning)

> [!NOTE]
> Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. Den här för hands versionen aktiverar loggar för blobbar (inklusive Azure Data Lake Storage Gen2), filer, köer, tabeller, Premium Storage-konton i generell användning v1-och General-Purpose v2-lagrings konton. Klassiska lagrings konton stöds inte.

I följande tabell visas egenskaperna för Azure Storage resurs loggar när de samlas in i Azure Monitor loggar eller Azure Storage. Egenskaperna beskriver åtgärden, tjänsten och typen av auktorisering som användes för att utföra åtgärden.

### <a name="fields-that-describe-the-operation"></a>Fält som beskriver åtgärden

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Fält som beskriver hur åtgärden autentiserades

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Fält som beskriver tjänsten

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Se även

- Se [övervakning av Azure-Queue Storage](monitor-queue-storage.md) för en beskrivning av övervakning av azure-Queue Storage.
- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md) .
