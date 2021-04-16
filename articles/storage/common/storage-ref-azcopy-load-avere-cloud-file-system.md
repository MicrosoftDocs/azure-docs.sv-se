---
title: azcopy load clfs | Microsoft Docs
titleSuffix: Azure Storage
description: Den här artikeln innehåller referensinformation för kommandot azcopy load clfs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebf04531f29e18f9d120ca2efa17244c4282084c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503277"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Överför lokala data till en container och lagrar dem i Microsofts CLFS-format (Avere Cloud FileSystem).

## <a name="synopsis"></a>Synopsis

Inläsningskommandot kopierar data till Azure Blob Storage-containrar och lagrar sedan dessa data i Microsofts CLFS-format (Avere Cloud FileSystem). Det upphovsrättsskyddade CLFS-formatet används av Azure HPC Cache och Avere vFXT for Azure produkter.

Om du vill använda det här kommandot installerar du det nödvändiga tillägget via: pip3 install clfsload~=1.0.23. Kontrollera att CLFSLoad.py finns i sökvägen. Mer information om det här steget finns i [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

Det här kommandot är ett enkelt alternativ för att flytta befintliga data till molnlagring för användning med specifika Microsofts cacheprodukter för databehandling med höga prestanda. 

Eftersom dessa produkter använder ett egenutvecklat molnfilsystemformat för att hantera data kan dessa data inte läsas in via det interna kopieringskommandot. 

I stället måste data läsas in via själva cacheprodukten eller via det här inläsningskommandot, som använder rätt upphovsrättsskyddat format.
Med det här kommandot kan du överföra data utan att använda cachen. Till exempel för att fylla i lagring i förväg eller lägga till filer i en arbetsuppsättning utan att öka cachebelastningen.

Målet är en tom Azure Storage Container. När överföringen är klar kan målcontainern användas med en Azure HPC Cache instans eller Avere vFXT for Azure kluster.

> [!NOTE] 
> Det här är en förhandsversion av inläsningskommandot. Rapportera eventuella problem på Github-lagringsplatsen AzCopy.

```
azcopy load clfs [local dir] [container URL] [flags]
```

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

**Strängen --compression-type** anger vilken komprimeringstyp som ska användas för överföringarna. Tillgängliga värden är: `DISABLED` , `LZ4` . (standard `LZ4` )

**--help**    help for the `azcopy load clfs` command.

**--log-level** string (Sträng på loggnivå) Definiera loggens verbositet för loggfilen, tillgängliga nivåer: `DEBUG` , , , `INFO` `WARNING` `ERROR` . (standard `INFO` )

**--max-errors** uint32 Ange det maximala antalet överföringsfel som ska tolereras. Stoppa jobbet omedelbart om det uppstår tillräckligt många fel.

**--new-session**   Starta ett nytt jobb i stället för att fortsätta med ett befintligt jobb vars spårningsinformation sparas på `--state-path` . (standardvärdet är true)

**--preserve-hardlinks**    Bevara hårda länkrelationer.

**--state-path string** Required path to a local directory for job state tracking. Sökvägen måste peka på en befintlig katalog för att kunna återuppta ett jobb. Det måste vara tomt för ett nytt jobb.

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|Flyttal på --cap-mbps|Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.|
|--output-type string|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|
|--trusted-microsoft-suffixes string   | Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net.* core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som visas här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
