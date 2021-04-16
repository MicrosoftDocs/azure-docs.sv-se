---
title: azcopy list | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot azcopy list.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ca436ee440ad7c1dd2caee3a65790f8b630fd949
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503294"
---
# <a name="azcopy-list"></a>azcopy lista

Visar entiteterna i en viss resurs.

## <a name="synopsis"></a>Synopsis

Endast blobcontainrar stöds i den aktuella versionen.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

## <a name="examples"></a>Exempel

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h, --help|Visa hjälpinnehåll för listkommandot.|
|--machine-readable|Visar en lista över filstorlekar i byte.|
|--mega-units|Visar enheter i order om 1 000, inte 1 024.|
|--running-tally|Räknar det totala antalet filer och deras storlekar.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|Flyttal på --cap-mbps|Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.|
|--output-type string|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|
|--trusted-microsoft-suffixes string   |Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net.* core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som anges här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
