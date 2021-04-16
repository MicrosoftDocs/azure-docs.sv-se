---
title: azcopy jobs clean | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot azcopy jobs clean.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 01bb7e37965abacac8105689bcb5ae52c548d647
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503430"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Ta bort alla logg- och planfiler för alla jobb

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

## <a name="examples"></a>Exempel

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Alternativ

**--help**                Hjälp för ren.

**--with-status string** Ta endast bort jobb med den här statusen, tillgängliga värden: `Canceled` , , , ( standard `Completed` `Failed` `InProgress` `All` `All` )

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**--cap-mbps float**      Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.

**--output-type** strängformat för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text". (standardtext)

**--trusted-microsoft-suffixesträng** Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net.* core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som visas här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.

## <a name="see-also"></a>Se även

- [azcopy jobb](storage-ref-azcopy-jobs.md)
