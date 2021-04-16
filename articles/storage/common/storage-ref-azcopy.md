---
title: azcopy | Microsoft Docs
description: Den här artikeln innehåller referensinformation för azcopy-kommandot.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b3b4f7737320cc0359192f947271a0f4beb3c478
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503005"
---
# <a name="azcopy"></a>azcopy

AzCopy är ett kommandoradsverktyg som flyttar data till och från Azure Storage. I artikeln [Kom igång med AzCopy kan](storage-use-azcopy-v10.md) du ladda ned AzCopy och lära dig mer om hur du kan ange autentiseringsuppgifter för auktorisering till lagringstjänsten.

## <a name="synopsis"></a>Synopsis

Det allmänna formatet för kommandona är: `azcopy [command] [arguments] --[flag-name]=[flag-value]` .

Om du vill rapportera problem eller lära dig mer om verktyget kan du läsa [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy) .

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

## <a name="options"></a>Alternativ

**--cap-mbps** (float) Kapslar överföringshastigheten, i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.

**--help** Hjälp för azcopy
      
**--output-type**  (sträng) Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är `text`. (standard `text` )

**--trusted-microsoft-suffix (sträng)** Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som anges här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.

## <a name="see-also"></a>Se även

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [azcopy bench](storage-ref-azcopy-bench.md)
- [azcopy kopiera](storage-ref-azcopy-copy.md)
- [azcopy dok](storage-ref-azcopy-doc.md)
- [azcopy miljö](storage-ref-azcopy-env.md)
- [azcopy jobb](storage-ref-azcopy-jobs.md)
- [azcopy jobs clean](storage-ref-azcopy-jobs-clean.md)
- [azcopy jobblista](storage-ref-azcopy-jobs-list.md)
- [azcopy jobs remove](storage-ref-azcopy-jobs-remove.md)
- [azcopy jobb-CV](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobbvisning](storage-ref-azcopy-jobs-show.md)
- [azcopy lista](storage-ref-azcopy-list.md)
- [azcopy inloggning](storage-ref-azcopy-login.md)
- [azcopy utloggning](storage-ref-azcopy-logout.md)
- [azcopy skapa](storage-ref-azcopy-make.md)
- [azcopy ta bort](storage-ref-azcopy-remove.md)
- [azcopy synkronisering](storage-ref-azcopy-sync.md)
