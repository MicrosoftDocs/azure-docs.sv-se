---
title: återuppta AzCopy-jobb | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Jobs Resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 30c0a31cc58ee6f1bbe78af017be42ae7d410fe0
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878417"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobb-CV

Återupptar det befintliga jobbet med angivet jobb-ID.

## <a name="synopsis"></a>Sammanfattning

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|--mål – SAS-sträng|Mål-SAS för målet för angivet jobb-ID.|
|--Exkludera sträng|Filtrera: exkludera de här misslyckade överföringarna när jobbet återupptas. Filerna ska avgränsas med ";".|
|-h,--hjälp|Visa hjälp innehåll för kommandot Resume.|
|--inkludera sträng|Filter: ta bara med de här misslyckade överföringarna när jobbet återupptas. Filerna ska avgränsas med ";".|
|--Källa – SAS-sträng |käll-SAS för källan för angivet jobb-ID.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s Float|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|
|--sträng för betrodd-Microsoft-suffix   |Anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [azcopy jobb](storage-ref-azcopy-jobs.md)
