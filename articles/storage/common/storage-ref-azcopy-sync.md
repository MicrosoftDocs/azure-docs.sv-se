---
title: azcopy sync | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot azcopy sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 83d10a7a6e9eb14379d32cc88800a2c443feac60
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503056"
---
# <a name="azcopy-sync"></a>azcopy synkronisering

Replikerar källplatsen till målplatsen. Den här artikeln innehåller en detaljerad referens för kommandot azcopy sync. Mer information om hur du synkroniserar blobar mellan käll- och målplatser finns i Synkronisera med Azure Blob Storage med [hjälp av AzCopy v10.](storage-use-azcopy-blobs-synchronize.md) Mer Azure Files i [Synkronisera filer.](storage-use-azcopy-files.md#synchronize-files)

## <a name="synopsis"></a>Synopsis

De senaste ändringstiderna används för jämförelse. Filen hoppas över om tiden för senaste ändring i målet är nyare.

De par som stöds är:

- lokal <-> Azure Blob (antingen SAS- eller OAuth-autentisering kan användas)
- Azure Blob <-> Azure Blob (Källan måste innehålla en SAS eller är offentligt tillgänglig. SAS- eller OAuth-autentisering kan användas för målet)
- Azure File <-> Azure File (Källan måste innehålla en SAS eller är offentligt tillgänglig; SAS-autentisering ska användas för målet)

Synkroniseringskommandot skiljer sig från kopieringskommandot på flera sätt:

1. Som standard är den rekursiva flaggan true och sync kopierar alla underkataloger. Sync kopierar endast filerna på den översta nivån i en katalog om den rekursiva flaggan är false.
2. När du synkroniserar mellan virtuella kataloger lägger du till ett avslutande snedstreck i sökvägen (se exempel) om det finns en blob med samma namn som en av de virtuella katalogerna.
3. Om flaggan är inställd på true eller prompt tar synkroniseringen bort filer och blobar på det `deleteDestination` mål som inte finns vid källan.

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

### <a name="advanced"></a>Avancerat

Om du inte anger något filtillägg identifierar AzCopy automatiskt innehållstypen för filerna vid uppladdning från den lokala disken, baserat på filnamnstillägget eller innehållet (om inget tillägg har angetts).

Den inbyggda uppslagstabellen är liten, men på Unix utökas den av det lokala systemets mime.types-filer om de är tillgängliga under ett eller flera av dessa namn:

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

Samma som ovan, men beräkna även en MD5-hash för filinnehållet och spara sedan MD5-hashen som blobens Content-MD5-egenskap. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synkronisera en hel katalog inklusive dess underkataloger (observera att rekursiv är som standard på):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

eller

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synkronisera endast filerna i en katalog, men inte underkataloger eller filerna inuti underkataloger:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synkronisera en delmängd av filer i en katalog (till exempel endast jpg- och pdf-filer, eller om filnamnet är `exactName` ):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Synkronisera en hel katalog men exkludera vissa filer från omfånget (till exempel: varje fil som börjar med foo eller slutar med stapel):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

Synkronisera en enskild blob:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Synkronisera en virtuell katalog:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Synkronisera en virtuell katalog som har samma namn som en blob (lägg till ett avslutande snedstreck i sökvägen för att undvika tvetydighet):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Synkronisera en Azure-filkatalog (samma syntax som blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Om include/exclude-flaggor används tillsammans skulle endast filer som matchar inkludningsmönstren tittas på, men de som matchar exkludingsmönstren ignoreras alltid.

## <a name="options"></a>Alternativ

**--block-size-mb** float Använd den här blockstorleken (anges i MiB) när du laddar upp till Azure Storage eller laddar ned från Azure Storage. Standardvärdet beräknas automatiskt baserat på filstorleken. Decimaler tillåts (till `0.25` exempel: ).

**--check-md5-sträng** Anger hur strikt MD5-hash-värden ska verifieras vid nedladdning. Det här alternativet är endast tillgängligt vid nedladdning. Tillgängliga värden är: `NoCheck` , `LogOnly` , , `FailIfDifferent` `FailIfDifferentOrMissing` . (standard `FailIfDifferent` ). (standard `FailIfDifferent` )

**--delete-destination** sträng definierar om du vill ta bort extra filer från målet som inte finns vid källan. Kan anges till `true` `false` , eller `prompt` . Om det är `prompt` inställt på ställs en fråga till användaren innan filer och blobar schemaläggers för borttagning. (standard `false` ). (standard `false` )

**Strängen --exclude-attributes** (endast Windows) Exkluderar filer vars attribut matchar attributlistan. Exempelvis: `A;S;R`

**--exclude-path string** Exkludera dessa sökvägar när du jämför källan med målet. Det här alternativet stöder inte jokertecken (*). Kontrollerar relativt sökvägsprefix (till exempel: `myFolder;myFolder/subDirName/file.pdf` ).

**--exclude-pattern** sträng Exkludera filer där namnet matchar mönsterlistan. Exempelvis: `*.jpg;*.pdf;exactName`

**--help**    help for sync.

**--include-attributes string** (endast Windows) Innehåller endast filer vars attribut matchar attributlistan. Exempelvis: `A;S;R`

**--include-pattern** sträng Inkludera endast filer där namnet matchar mönsterlistan. Exempelvis: `*.jpg;*.pdf;exactName`

**Sträng på loggnivå** Definiera loggens verbositet för loggfilen, tillgängliga nivåer: (alla begäranden och `INFO` svar), `WARNING` (långsamma svar), `ERROR` (endast misslyckade begäranden) och `NONE` (inga utdataloggar). (standard `INFO` ). 

**--preserve-smb-info**   Falskt som standard. Bevarar SMB-egenskapsinformation (senaste skrivtid, skapandetid, attributbit) mellan SMB-medvetna resurser (Windows och Azure Files). Den här flaggan gäller för både filer och mappar, såvida inte ett filter för endast filer har angetts (till exempel include-pattern). Den information som överförs för mappar är samma som för filer, förutom senaste skrivtiden som inte bevaras för mappar.

**--preserve-smb-permissions**   Falskt som standard. Bevarar SMB-ACL:er mellan medvetna resurser (Windows och Azure Files). Den här flaggan gäller för både filer och mappar, såvida inte ett filter för endast filer har angetts (till exempel `include-pattern` ).

**--put-md5**     Skapa en MD5-hash för varje fil och spara hashen som Content-MD5-egenskapen för målbloben eller målfilen. (Hashen skapas INTE som standard.) Endast tillgängligt vid uppladdning.

**--rekursiv** `True` Som standard kan du titta på underkataloger rekursivt när du synkroniserar mellan kataloger.     (standard `True` ). 

**--s2s-preserve-access-tier**  Bevara åtkomstnivån under tjänst-till-tjänstkopiering. Se [åtkomstnivåer för Azure Blob Storage: hot, cool och archive](../blobs/storage-blob-storage-tiers.md) för att se till att mållagringskontot stöder inställning av åtkomstnivå. Om inställningen av åtkomstnivå inte stöds använder du s2sPreserveAccessTier=false för att kringgå kopiering av åtkomstnivå. (standard `true` ). 

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--cap-mbps uint32|Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.|
|--output-type string|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|
|--trusted-microsoft-suffixes string   |Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som anges här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
