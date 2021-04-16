---
title: azcopy jobs remove | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot azcopy jobs remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c6a4745c4059c81384448deba37495030c4bf3a3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503379"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Ta bort alla filer som är associerade med det angivna jobb-ID:t.

> [!NOTE] 
> Du kan anpassa platsen där logg- och planfilerna sparas. Mer information [finns i kommandot azcopy env.](storage-ref-azcopy-env.md)

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

## <a name="examples"></a>Exempel

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Alternativ

**--help**                Hjälp för att ta bort.

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**Flyttal på --cap-mbps**      Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.

**--output-type** strängformat för kommandots utdata. Alternativen är: text, json. Standardvärdet är `text`. (standard `text` )

**--trusted-microsoft-suffixesträng** Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som anges här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.

## <a name="see-also"></a>Se även

- [azcopy jobb](storage-ref-azcopy-jobs.md)
