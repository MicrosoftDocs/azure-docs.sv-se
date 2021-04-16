---
title: azcopy remove | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: bd221215d6be3c14ce1200e8bd374a97cb7608a0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503022"
---
# <a name="azcopy-remove"></a>azcopy ta bort

Ta bort blobar eller filer från ett Azure Storage-konto.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

## <a name="examples"></a>Exempel

Ta bort en enskild blob med hjälp av en SAS-token:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Ta bort en hel virtuell katalog med hjälp av en SAS-token:
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Ta bara bort blobarna i en virtuell katalog, men ta inte bort underkataloger eller blobar i dessa underkataloger:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Ta bort en delmängd av blobar i en virtuell katalog (till exempel ta bort endast jpg- och pdf-filer, eller om blobnamnet är `exactName` ):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Ta bort en hel virtuell katalog men undanta vissa blobar från omfånget (till exempel varje blob som börjar med foo eller slutar med stapel):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Ta bort specifika blobar och virtuella kataloger genom att placera deras relativa sökvägar (INTE URL-kodade) i en fil:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
Ta bort en enskild fil Blob Storage ett konto som har en hierarkisk namnrymd (inkludera/exkludera stöds inte):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Ta bort en enskild katalog från Blob Storage-konto som har en hierarkisk namnrymd (inkludera/exkludera stöds inte):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Alternativ

**--delete-snapshots-sträng** Som standard misslyckas borttagningsåtgärden om en blob har ögonblicksbilder. Ange för att ta bort rotbloben och alla dess ögonblicksbilder. Alternativt kan du ange att endast ögonblicksbilderna ska tas bort `include` `only` men behålla rotbloben.

**--exclude-path string** Exkludera dessa sökvägar när du tar bort. Det här alternativet stöder inte jokertecken (*). Kontrollerar relativt sökvägsprefix. Exempelvis: `myFolder;myFolder/subDirName/file.pdf`

**--exclude-pattern** sträng Exkludera filer där namnet matchar mönsterlistan. Till exempel: `*.jpg` `*.pdf` ; ;`exactName`

**--force-if-read-only**   När du tar Azure Files en fil eller mapp måste borttagningen fungera även om det befintliga objektet har sitt skrivskyddade attribut.

**--help**   help for remove.

**--include-path string** Inkludera endast dessa sökvägar när du tar bort. Det här alternativet stöder inte jokertecken (*). Kontrollerar relativt sökvägsprefix. Exempelvis: `myFolder;myFolder/subDirName/file.pdf`

**--include-pattern** string Inkludera endast filer där namnet matchar mönsterlistan. Till exempel: *`.jpg` ;* `.pdf` ;`exactName`

**--list-of-files sträng** definierar platsen för en fil, som innehåller listan över filer och kataloger som ska tas bort. De relativa sökvägarna ska avgränsas med radbrytningar och sökvägarna ska INTE vara URL-kodade. 

**Strängen --list-of-versions** Anger en fil där varje versions-ID visas på en separat rad. Se till att källan måste peka på en enskild blob och att alla versions-ID:er som anges i filen med den här flaggan endast tillhör källbloben. Angivna versions-ID:er för den angivna bloben tas bort från Azure Storage. 

**--log-level** string (Sträng på loggnivå) Definiera loggens verbositet för loggfilen. Tillgängliga nivåer är: `INFO` (alla begäranden/svar), `WARNING` (långsamma svar), `ERROR` (endast misslyckade begäranden) och `NONE` (inga utdataloggar). (standard `INFO` ) (standard `INFO` )

**--rekursiv**    Titta på underkataloger rekursivt när du synkroniserar mellan kataloger.

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--cap-mbps float|Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.|
|--output-type string|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|
|--trusted-microsoft-suffixes string   |Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som anges här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
