---
title: azcopy load | Microsoft Docs
titleSuffix: Azure Storage
description: Den här artikeln innehåller referensinformation för kommandot azcopy load.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4c2187f52503159c8efc199cb7d09a147c2c1455
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503260"
---
# <a name="azcopy-load"></a>azcopy load

Underkommandon som rör överföring av data i specifika format

## <a name="synopsis"></a>Synopsis

Underkommandon som rör överföring av data i specifika format, till exempel Microsofts CLFS-format (Avere Cloud FileSystem).

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

## <a name="examples"></a>Exempel

Läs in en hel katalog till en container med en SAS i CLFS-format:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h, --help|Visar hjälpinnehåll för inläsningskommandot.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|Flyttal på --cap-mbps|Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.|
|--output-type string|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|
|--trusted-microsoft-suffixes string   | Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net.* core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som visas här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
