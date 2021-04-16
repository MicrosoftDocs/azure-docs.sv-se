---
title: azcopy make | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot azcopy make.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1e5b142d36266d5b6a333a0fa5cd233bac71849e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503039"
---
# <a name="azcopy-make"></a>azcopy skapa

Skapar en container eller filresurs.

## <a name="synopsis"></a>Synopsis

Skapa en container eller filresurs som representeras av den angivna resurs-URL:en.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

## <a name="examples"></a>Exempel

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h, --help|Visa hjälpinnehåll för kommandot make. |
|--quota-gb uint32|Anger den maximala storleken på resursen i GB, noll innebär att du accepterar filtjänstens standardkvot.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--cap-mbps float|Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.|
|--output-type string|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|
|--trusted-microsoft-suffixes string   |Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som anges här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
