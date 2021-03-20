---
title: AzCopy ta bort | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d28a20a6afc78939491e56f46a32782b4c0844cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879011"
---
# <a name="azcopy-remove"></a>azcopy ta bort

Ta bort blobbar eller filer från ett Azure Storage-konto.

## <a name="synopsis"></a>Sammanfattning

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

Ta bort en enskild BLOB med hjälp av en SAS-token:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Ta bort en hel virtuell katalog med hjälp av en SAS-token:
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Ta bara bort blobarna i en virtuell katalog, men ta inte bort några under kataloger eller blobbar i dessa under kataloger:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Ta bort en delmängd av blobbar i en virtuell katalog (till exempel: ta endast bort jpg-och PDF-filer, eller om BLOB-namnet är `exactName` ):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Ta bort en hel virtuell katalog men undanta vissa blobbar från omfånget (till exempel: varje blob som börjar med foo eller slutar med bar):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Ta bort vissa blobbar och virtuella kataloger genom att placera deras relativa sökvägar (inte URL-kodade) i en fil:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
Ta bort en enskild fil från ett Blob Storage konto som har ett hierarkiskt namn område (inkludera/exkludera stöds inte):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Ta bort en enskild katalog från ett Blob Storage-konto som har ett hierarkiskt namn område (inkludera/exkludera stöds inte):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Alternativ

**--ta bort-ögonblicks bilder** sträng som standard Miss lyckas borttagnings åtgärden om en BLOB har ögonblicks bilder. Ange `include` om du vill ta bort rot-bloben och alla dess ögonblicks bilder. Alternativt kan du ange `only` att endast ögonblicks bilderna ska tas bort men behåll rot-bloben.

**--sträng för exkluderings Sök väg** utesluter dessa sökvägar när de tas bort. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefix för relativ sökväg. Exempelvis: `myFolder;myFolder/subDirName/file.pdf`

**--exkludera-mönster** sträng exkludera filer där namnet matchar mönster listan. Exempel: `*.jpg` ; `*.pdf` ;`exactName`

**--tvång-skrivskyddad**   När du tar bort en Azure Files-fil eller-mapp, tvinga borttagningen att fungera även om det befintliga objektet har en skrivskyddad attribut uppsättning.

**--Hjälp**   för att ta bort.

**--include-Path-** sträng innehåller bara dessa sökvägar när de tas bort. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefix för relativ sökväg. Exempelvis: `myFolder;myFolder/subDirName/file.pdf`

**--Inkludera-mönster** sträng inkludera bara filer där namnet matchar mönster listan. Exempel: *`.jpg` ;* `.pdf` ;`exactName`

**--** en sträng som anger en fil som innehåller en lista över filer och kataloger som ska tas bort. De relativa Sök vägarna ska avgränsas med rad brytningar och Sök vägarna får inte vara URL-kodade. 

**--list-of-versions** -sträng anger en fil där varje versions-ID visas på en separat rad. Se till att källan måste peka på en enskild blob och att alla versions-ID: n som anges i filen med den här flaggan endast tillhör käll-bloben. Angivet versions-ID för den angivna blobben tas bort från Azure Storage. 

**--sträng för logg nivå** definierar loggens utförlighet för logg filen. Tillgängliga nivåer är: `INFO` (alla begär Anden/svar), `WARNING` (långsamma svar), `ERROR` (endast misslyckade förfrågningar) och `NONE` (inga utgående loggar). (standard `INFO` ) (standard `INFO` )

**--rekursivt**    Titta i under kataloger rekursivt vid synkronisering mellan kataloger.

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s Float|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|
|--sträng för betrodd-Microsoft-suffix   |Anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [AzCopy](storage-ref-azcopy.md)
