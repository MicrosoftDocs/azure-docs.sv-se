---
title: AzCopy-synkronisering | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: dc3451a4b46a317dccda0e4292dcb1712b4171f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878315"
---
# <a name="azcopy-sync"></a>azcopy synkronisering

Replikerar käll platsen till mål platsen.

## <a name="synopsis"></a>Sammanfattning

De senast ändrade tiderna används för jämförelse. Filen hoppas över om den senaste ändrings tiden i målet är nyare.

De par som stöds är:

- lokal <-> Azure-Blob (SAS-eller OAuth-autentisering kan användas)
- Azure Blob <-> Azure-Blob (källan måste innehålla en SAS eller är offentligt tillgänglig, antingen SAS-eller OAuth-autentisering kan användas för mål)
- Azure File <-> Azure-fil (källan måste innehålla en SAS eller är offentligt tillgänglig. SAS-autentisering ska användas för mål)

Kommandot Sync skiljer sig från kommandot Copy på flera sätt:

1. Som standard är den rekursiva flaggan True och Sync kopierar alla under kataloger. Sync kopierar endast filer på den översta nivån i en katalog om den rekursiva flaggan är falsk.
2. När du synkroniserar mellan virtuella kataloger lägger du till ett avslutande snedstreck till sökvägen (se exemplen) om det finns en blob med samma namn som en av de virtuella katalogerna.
3. Om `deleteDestination` flaggan är inställd på True eller prompt kommer synkronisering att ta bort filer och blobbar på det mål som inte finns på källan.

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Avancerat

Om du inte anger något fil namns tillägg identifierar AzCopy automatiskt filernas innehålls typ vid överföring från den lokala disken, baserat på fil namns tillägget eller innehållet (om inget tillägg har angetts).

Den inbyggda uppslags tabellen är liten, men på UNIX är den förstärkt av det lokala systemets MIME. typ fil (er) om de är tillgängliga under ett eller flera av följande namn:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

I Windows extraheras MIME-typer från registret.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Exempel

Synkronisera en enskild fil:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Samma som ovan, men beräkna även en MD5-hash av fil innehållet och spara sedan MD5-hash som blobens Content-MD5-egenskap. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synkronisera en hel katalog inklusive dess under kataloger (Observera att rekursivt är aktiverat som standard):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

eller

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synkronisera endast filerna inuti en katalog, men inte under kataloger eller filer i under kataloger:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synkronisera en delmängd av filer i en katalog (till exempel: endast jpg-och PDF-filer, eller om fil namnet är `exactName` ):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Synkronisera en hel katalog men undanta vissa filer från omfånget (till exempel: varje fil som börjar med foo eller slutar med bar):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

Synkronisera en enskild BLOB:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Synkronisera en virtuell katalog:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Synkronisera en virtuell katalog som har samma namn som en BLOB (Lägg till ett avslutande snedstreck till sökvägen i ordningen till disambiguate):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Synkronisera en Azure-fil katalog (samma syntax som BLOB):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Om inkludera/utelämna-flaggor används tillsammans, kommer endast filer som matchar include-mönster att titta på, men de som matchar de uteslutna mönstren skulle alltid ignoreras.

## <a name="options"></a>Alternativ

**--block-size-MB** float Använd den här block storleken (anges i MIB) vid överföring till Azure Storage eller nedladdning från Azure Storage. Standardvärdet beräknas automatiskt baserat på fil storlek. Decimal tal tillåts (till exempel: `0.25` ).

**--kontrol lera-MD5-** sträng anger hur strikt MD5-hashar ska verifieras vid hämtning. Det här alternativet är endast tillgängligt vid hämtning. Tillgängliga värden är: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (standard `FailIfDifferent` ). (standard `FailIfDifferent` )

**--Delete-destinations** sträng anger om du vill ta bort extra filer från målet som inte finns på källan. Kan anges till `true` , `false` , eller `prompt` . Om detta är inställt på `prompt` , uppmanas användaren att ange en fråga innan de schemalägger filer och blobbar för borttagning. (standard `false` ). (standard `false` )

**--sträng för exkludera attribut** (endast Windows) utesluter filer vars attribut matchar attributlistan. Exempelvis: `A;S;R`

**--sträng med exkludera Sök** vägar utesluter dessa sökvägar när källan jämförs mot målet. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefix för relativ sökväg (till exempel: `myFolder;myFolder/subDirName/file.pdf` ).

**--exkludera-mönster** sträng exkludera filer där namnet matchar mönster listan. Exempelvis: `*.jpg;*.pdf;exactName`

**--Hjälp**    för synkronisering.

**--include-attribut** sträng (endast Windows) innehåller bara filer vars attribut matchar attributlistan. Exempelvis: `A;S;R`

**--Inkludera-mönster** sträng inkludera bara filer där namnet matchar mönster listan. Exempelvis: `*.jpg;*.pdf;exactName`

**--sträng på loggnivå** definierar loggens utförlighet för logg filen, tillgängliga nivåer: `INFO` (alla begär Anden och svar) `WARNING` (långsamma svar), `ERROR` (endast misslyckade förfrågningar) och `NONE` (inga utgående loggar). (standard `INFO` ). 

**--Behåll-SMB-info**   Falskt som standard. Bevarar information om SMB-egenskaper (senaste skrivnings tid, skapande tid, attribut bitar) mellan SMB-medvetna resurser (Windows och Azure Files). Den här flaggan gäller för både filer och mappar, om inte ett fil filter anges (till exempel include-Pattern). Informationen som överförs för mappar är samma som för filer, förutom senaste skrivnings tid som inte bevaras för mappar.

**--bevara-SMB-Permissions**   Falskt som standard. Bevarar SMB ACL: er mellan medvetna resurser (Windows och Azure Files). Den här flaggan gäller för både filer och mappar, om inte ett fil filter anges (till exempel `include-pattern` ).

**--Skicka-MD5**     Skapa en MD5-hash av varje fil och spara hashen som Content-MD5-egenskapen för Målmatrisen eller-filen. (Som standard skapas inte hashen.) Endast tillgängligt vid uppladdning.

**--rekursivt** `True` som standard tittar du i under kataloger rekursivt vid synkronisering mellan kataloger.     (standard `True` ). 

**--S2S-konserver-Access-Tier**  Bevara åtkomst nivån under tjänst-till-tjänst-kopiering. Se [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](../blobs/storage-blob-storage-tiers.md) lag rings nivå för att säkerställa att mål lagrings kontot har stöd för att ange åtkomst nivå. I de fall då det inte finns stöd för att ange åtkomst nivå kan du använda s2sPreserveAccessTier = false för att kringgå kopiering av åtkomst nivå. (standard `true` ). 

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|
|--sträng för betrodd-Microsoft-suffix   |Anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [AzCopy](storage-ref-azcopy.md)
