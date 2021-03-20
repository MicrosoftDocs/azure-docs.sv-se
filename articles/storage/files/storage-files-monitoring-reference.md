---
title: Azure Files Referens för övervaknings data | Microsoft Docs
description: Logg och statistik referens för övervakning av data från Azure Files.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 4575709a4fa7067b02228036fb2e2b4a60844e21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592323"
---
# <a name="azure-files-monitoring-data-reference"></a>Azure Files övervaknings data referens

Mer information om hur du samlar in och analyserar övervaknings data för Azure Files finns i [övervaknings Azure Files](storage-files-monitoring.md) .

## <a name="metrics"></a>Mått

I följande tabeller visas de plattforms mått som samlas in för Azure Files. 

### <a name="capacity-metrics"></a>Kapacitetsmått

Värdena för kapacitets värden uppdateras dagligen (upp till 24 timmar). Tids kornig het definierar tidsintervallet för vilka mått värden presenteras. Tids kornig het för alla kapacitets mått är en timme (PT1H).

Azure Files tillhandahåller följande kapacitets mått i Azure Monitor.

#### <a name="account-level"></a>Konto nivå

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Azure Files

I den här tabellen visas [Azure Files mått](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

| Metric | Beskrivning |
| ------------------- | ----------------- |
| FileCapacity | Mängden fil lagring som används av lagrings kontot. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| FileCount   | Antalet filer i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| FileShareCapacityQuota | Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024|
| FileShareCount | Antalet fil resurser i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| FileShareProvisionedIOPS | Antalet etablerade IOPS på en fil resurs. Det här måttet gäller endast Premium File Storage. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt |
| FileShareSnapshotCount | Antal ögonblicks bilder som finns på resursen i lagrings kontots Azure Files tjänst. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt | 
|FileShareSnapshotSize|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots Azure Files tjänst. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt|

### <a name="transaction-metrics"></a>Transaktionsmått

Transaktions mått genereras på varje begäran till ett lagrings konto från Azure Storage till Azure Monitor. Om det inte finns någon aktivitet på ditt lagrings konto kommer det inte att finnas några data om transaktions måtten under perioden. Alla transaktions mått är tillgängliga både på konto-och Azure Files service nivå. Tids kornig het definierar tidsintervallet som Mät värden presenteras. Tids korn som stöds för alla transaktions mått är PT1H och PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Files stöder följande dimensioner för mått i Azure Monitor.

> [!NOTE] 
> Fil resurs dimensionen är inte tillgänglig för standard fil resurser (endast Premium fil resurser). När du använder standard fil resurser är de angivna måtten för alla fil resurser i lagrings kontot. Om du vill få per delnings mått för standard fil resurser skapar du en fil resurs per lagrings konto.

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

- En beskrivning av övervaknings Azure Storage finns i [övervaknings Azure Files](storage-files-monitoring-reference.md) .
- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md) .