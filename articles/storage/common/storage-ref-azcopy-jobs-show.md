---
title: azcopy jobs show | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot azcopy jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: a14546d8bfc50531902b5150d38ee5ce8b8b5769
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503362"
---
# <a name="azcopy-jobs-show"></a>azcopy jobbvisning

Visar detaljerad information för det angivna jobb-ID:t.

## <a name="synopsis"></a>Synopsis

Om endast jobb-ID:t anges utan en flagga returneras förloppssammanfattningen för jobbet.

Antalet byte och procent som visas när du kör det här kommandot återspeglar endast filer som har slutförts i jobbet. De återspeglar inte delvis slutförda filer.

Om `with-status` flaggan har angetts visas listan över överföringar i jobbet med det angivna värdet.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h, --help|Visar hjälpinnehåll för kommandot show.|
|--with-status string|Lista endast överföringar av jobb med den här statusen, tillgängliga värden: Startad, Lyckades, Misslyckades|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--cap-mbps float|Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.|
|--output-type string|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|
|--trusted-microsoft-suffixes string   |Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som anges här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [azcopy jobb](storage-ref-azcopy-jobs.md)
