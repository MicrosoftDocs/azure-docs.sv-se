---
title: AzCopy load CLFS | Microsoft Docs
titleSuffix: Azure Storage
description: Den här artikeln innehåller referensinformation för kommandot AzCopy load CLFS.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b36ea25180c31fef199aaacb10e46b3caa20f807
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878383"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Överför lokala data till en behållare och lagrar dem i Microsofts CLFS-format (AVERT Cloud FileSystem).

## <a name="synopsis"></a>Sammanfattning

Load-kommandot kopierar data till Azure Blob Storage-behållare och lagrar dessa data i Microsofts CLFS-format (AVERT Cloud FileSystem). Det tillverkarspecifika CLFS-formatet används av Azure HPC cache och AVERT vFXT för Azure-produkter.

Om du vill utnyttja det här kommandot installerar du det nödvändiga tillägget via: PIP3 install clfsload ~ = 1.0.23. Kontrol lera att CLFSLoad.py finns i din sökväg. Mer information om det här steget finns i [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

Det här kommandot är ett enkelt alternativ för att flytta befintliga data till moln lagring för användning med vissa Microsoft-produkter med hög prestanda data behandling. 

Eftersom dessa produkter använder ett patentskyddat moln fil system format för att hantera data, kan dessa data inte läsas in via det inbyggda kopierings kommandot. 

I stället måste data läsas in via själva cache-produkten eller via det här inläsnings kommandot, som använder rätt patentskyddat format.
Med det här kommandot kan du överföra data utan att använda cachen. Om du till exempel vill fylla i lagrings utrymme eller lägga till filer i en arbets uppsättning utan att öka cache belastningen.

Målet är en tom Azure Storage-behållare. När överföringen är klar kan mål behållaren användas med en Azure HPC-instans eller AVERT vFXT för Azure-kluster.

> [!NOTE] 
> Detta är en för hands version av load-kommandot. Rapportera eventuella problem på AzCopy GitHub-lagrings platsen.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

Läs in en hel katalog till en behållare med SAS i CLFS-format:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Alternativ

**– komprimerings typ** sträng ange vilken komprimerings typ som ska användas för överföringarna. Tillgängliga värden är: `DISABLED` , `LZ4` . (standard `LZ4` )

**--Hjälp**    för `azcopy load clfs` kommandot.

**--sträng för loggnings nivå** definiera loggens utförlighet för logg filen, tillgängliga nivåer: `DEBUG` , `INFO` , `WARNING` , `ERROR` . (standard `INFO` )

**--Max-antal fel** som UInt32 anger det maximala antalet överförings fel som ska tolereras. Stoppa jobbet omedelbart om det inträffar tillräckligt många fel.

**--New-session**   Starta ett nytt jobb i stället för att fortsätta att fortsätta en befintlig person vars spårnings information hålls kvar `--state-path` . (standard sant)

**--Behåll-hardlinks**    Behåll hårda länk relationer.

**--tillstånds Sök vägs** sträng krävs sökväg till en lokal katalog för spårning av jobb tillstånd. Sökvägen måste peka på en befintlig katalog för att det ska gå att återuppta ett jobb. Det måste vara tomt för ett nytt jobb.

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s Float|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|
|--sträng för betrodd-Microsoft-suffix   | Anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [AzCopy](storage-ref-azcopy.md)
