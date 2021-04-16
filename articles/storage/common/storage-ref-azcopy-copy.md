---
title: azcopy copy| Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot azcopy copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 03/08/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7c1e265f473c1c6fb70fd97416722e7b863c429b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503566"
---
# <a name="azcopy-copy"></a>azcopy kopiera

Kopierar källdata till en målplats.

## <a name="synopsis"></a>Synopsis

Kopierar källdata till en målplats. Anvisningarna som stöds är:

  - lokal <-> Azure Blob (SAS- eller OAuth-autentisering)
  - lokal <-> Azure Files (SAS-autentisering för resurs/katalog)
  - lokal <-> Azure Data Lake Storage Gen 2 (SAS, OAuth eller autentisering med delad nyckel)
  - Azure Blob (SAS eller offentlig) – > Azure Blob (SAS- eller OAuth-autentisering)
  - Azure Blob (SAS eller offentlig) – > Azure Files (SAS)
  - Azure Files (SAS) -> Azure Files (SAS)
  - Azure Files (SAS) – > Azure Blob (SAS- eller OAuth-autentisering)
  - Amazon Web Services (AWS) S3 (åtkomstnyckel) – > Azure Block Blob (SAS- eller OAuth-autentisering)
  - Google Cloud Storage (tjänstkontonyckel) – > Azure Block Blob (SAS- eller OAuth-autentisering) [förhandsversion]

Mer information finns i exempelavsnittet i den här artikeln.

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

## <a name="advanced"></a>Avancerat

AzCopy identifierar automatiskt innehållstypen för filerna när du laddar upp dem från den lokala disken. AzCopy identifierar innehållstypen baserat på filnamnstillägget eller innehållet (om inget tillägg har angetts).

Den inbyggda uppslagstabellen är liten, men i Unix utökas den av det lokala systemets filer om de är tillgängliga under ett eller flera av `mime.types` dessa namn:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

I Windows extraheras MIME-typer från registret. Den här funktionen kan stängas av med hjälp av en flagga. Mer information finns i flaggavsnittet i den här artikeln.

Om du anger en miljövariabel med hjälp av kommandoraden kan variabeln läsas i kommandoradshistoriken. Överväg att rensa variabler som innehåller autentiseringsuppgifter från kommandoradshistoriken. Om du inte vill att variabler ska visas i historiken kan du använda ett skript för att uppmana användaren att ange sina autentiseringsuppgifter och för att ange miljövariabeln.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exempel

Ladda upp en enskild fil med OAuth-autentisering. Om du ännu inte har loggat in på AzCopy kör `azcopy login` du kommandot innan du kör följande kommando.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Samma som ovan, men den här gången kan du även beräkna MD5-hashen för filinnehållet och spara det som blobens Content-MD5-egenskap:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Ladda upp en enskild fil med hjälp av en SAS-token:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Ladda upp en enskild fil med hjälp av en SAS-token och piping (endast blockblobar):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Ladda upp en hel katalog med hjälp av en SAS-token:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

eller

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Ladda upp en uppsättning filer med hjälp av en SAS-token och jokertecken (*) tecken:
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Ladda upp filer och kataloger med hjälp av en SAS-token och jokertecken (*) tecken:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Ladda upp filer och kataloger Azure Storage ett konto och ange kodade taggar för frågesträngar på bloben. 

- Om du vill ange taggarna {key = "bla bla", val = "foo"} och {key = "bla bla 2", val = "bar"}, använder du följande syntax: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- Nycklar och värden är URL-kodade och nyckel/värde-paren avgränsas med ett et-&)

- När du anger taggar för blobarna finns det ytterligare behörigheter (inte för taggar) i SAS utan vilka tjänsten kommer att ge auktoriseringsfel tillbaka.

Ladda ned en enskild fil med hjälp av OAuth-autentisering. Om du ännu inte har loggat in på AzCopy kör `azcopy login` du kommandot innan du kör följande kommando.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Ladda ned en enskild fil med hjälp av en SAS-token:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Ladda ned en enskild fil med hjälp av en SAS-token och överför sedan utdata till en fil (endast blockblobar):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Ladda ned en hel katalog med hjälp av en SAS-token:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

En anteckning om att använda jokertecken (*) i URL:er:

Det finns bara två sätt att använda jokertecken i en URL. 

- Du kan använda ett precis efter det slutliga snedstrecket (/) för en URL. Den här användningen av jokertecknet kopierar alla filer i en katalog direkt till målet utan att placera dem i en underkatalog. 

- Du kan också ett jokertecken i namnet på en container så länge URL:en endast refererar till en container och inte till en blob. Du kan använda den här metoden för att hämta filer från en delmängd av containrar. 

Ladda ned innehållet i en katalog utan att kopiera själva katalogen som innehåller.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Ladda ned ett helt lagringskonto.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Ladda ned en delmängd containrar i ett lagringskonto med hjälp av ett jokertecken (*) i containernamnet.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Kopiera en enskild blob till en annan blob med hjälp av en SAS-token.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopiera en enskild blob till en annan blob med hjälp av en SAS-token och en autentiseringstoken. Du måste använda en SAS-token i slutet av källkontots URL, men målkontot behöver inte en om du loggar in på AzCopy med hjälp av `azcopy login` kommandot . 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Kopiera en virtuell blob-katalog till en annan med hjälp av en SAS-token:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Kopiera alla blobcontainrar, kataloger och blobar från lagringskontot till ett annat med hjälp av en SAS-token:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopiera ett enskilt objekt till Blob Storage från Amazon Web Services (AWS) S3 med hjälp av en åtkomstnyckel och en SAS-token. Ange först miljövariabeln `AWS_ACCESS_KEY_ID` och `AWS_SECRET_ACCESS_KEY` för AWS S3-källan.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopiera en hel katalog för att Blob Storage från AWS S3 med hjälp av en åtkomstnyckel och en SAS-token. Ange först miljövariabeln `AWS_ACCESS_KEY_ID` och `AWS_SECRET_ACCESS_KEY` för AWS S3-källan.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Läs mer https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html om platshållaren [folder].

Kopiera alla buckets till Blob Storage från Amazon Web Services (AWS) med hjälp av en åtkomstnyckel och en SAS-token. Ange först miljövariabeln `AWS_ACCESS_KEY_ID` och `AWS_SECRET_ACCESS_KEY` för AWS S3-källan.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopiera alla buckets till Blob Storage från en Amazon Web Services (AWS)-region med hjälp av en åtkomstnyckel och en SAS-token. Ange först miljövariabeln `AWS_ACCESS_KEY_ID` och `AWS_SECRET_ACCESS_KEY` för AWS S3-källan.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopiera en delmängd av bucketar med hjälp av ett jokertecken (*) i bucketnamnet. Precis som i föregående exempel behöver du en åtkomstnyckel och en SAS-token. Se till att ange miljövariabeln `AWS_ACCESS_KEY_ID` och `AWS_SECRET_ACCESS_KEY` för AWS S3-källan.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Överför filer och kataloger till Azure Storage konto och ange de angivna frågesträngskodade taggarna på bloben. 

- Om du vill ange taggarna {key = "bla bla", val = "foo"} och {key = "bla bla 2", val = "bar"}, använder du följande syntax: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- Nycklar och värden är URL-kodade och nyckel/värde-paren avgränsas med ett et-&)
    
- När du anger taggar för blobarna finns det ytterligare behörigheter (inte för taggar) i SAS utan vilka tjänsten kommer att ge auktoriseringsfel tillbaka.

Kopiera ett enskilt objekt till Blob Storage från Google Cloud Storage med hjälp av en tjänstkontonyckel och en SAS-token. Börja med att ange miljövariabeln GOOGLE_APPLICATION_CREDENTIALS för Google Cloud Storage-källa.
  
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopiera en hel katalog till Blob Storage från Google Cloud Storage med hjälp av en nyckel för tjänstkontot och en SAS-token. Börja med att ange miljövariabeln GOOGLE_APPLICATION_CREDENTIALS för Google Cloud Storage-källa.
 
```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Kopiera en hel bucket för att Blob Storage från Google Cloud Storage med hjälp av en tjänstkontonyckel och en SAS-token. Börja med att ange miljövariabeln GOOGLE_APPLICATION_CREDENTIALS för Google Cloud Storage-källa.

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Kopiera alla bucketar till Blob Storage från Google Cloud Storage med hjälp av en tjänstkontonyckel och en SAS-token. Börja med att ange miljövariablerna GOOGLE_APPLICATION_CREDENTIALS och GOOGLE_CLOUD_PROJECT=<projekt-id> för GCS-källa

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Kopiera en delmängd av bucketar med ett jokertecken (*) i bucketnamnet från Google Cloud Storage med hjälp av en tjänstkontonyckel och en SAS-token för målet. Börja med att ange miljövariablerna GOOGLE_APPLICATION_CREDENTIALS och GOOGLE_CLOUD_PROJECT=<projekt-id> för Google Cloud Storage-källan.
 
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>Alternativ

**--backup** Aktiverar Windows SeBackupPrivilege för uppladdningar, eller SeRestorePrivilege för nedladdningar, så att AzCopy kan se och läsa alla filer, oavsett deras filsystembehörigheter, och återställa alla behörigheter. Kräver att kontot som kör AzCopy redan har dessa behörigheter (till exempel har administratörsrättigheter eller är medlem i `Backup Operators` gruppen). Den här flaggan aktiverar behörigheter som kontot redan har.

**--blob-tags string** Ange taggar för blobar för att kategorisera data i ditt lagringskonto.

**--blob-type** string Definierar typen av blob vid målet. Detta används för att ladda upp blobar och vid kopiering mellan konton (standard `Detect` ). Giltiga värden är `Detect` , `BlockBlob` , och `PageBlob` `AppendBlob` . Vid kopiering mellan konton gör värdet att AzCopy använder typen av källblob för att `Detect` fastställa typen av målblob. När du laddar upp en fil `Detect` avgör om filen är en VHD- eller VHDX-fil baserat på filnamnstillägget. Om filen är en VHD- eller VHDX-fil behandlar AzCopy filen som en sidblob. (standard "Identifiera")

**--block-blob-tier string** Upload block blob to Azure Storage using this blob tier. (standardvärdet "Ingen")

**--block-size-mb** float Använd den här blockstorleken (anges i MiB) när du laddar upp till Azure Storage och laddar ned från Azure Storage. Standardvärdet beräknas automatiskt baserat på filstorleken. Decimaler tillåts (till exempel: 0,25).

**--cache-control sträng** Ange cache-control-huvudet. Returneras vid nedladdning.

**--check-length** Kontrollera längden på en fil på målet efter överföringen. Om det finns ett matchningsfel mellan källa och mål markeras överföringen som misslyckad. (standardvärdet är `true` )

**--check-md5-sträng** Anger hur strikt MD5-hash-värden ska verifieras vid nedladdning. Endast tillgängligt vid nedladdning. Tillgängliga alternativ: `NoCheck` , `LogOnly` , , `FailIfDifferent` `FailIfDifferentOrMissing` . (standard `FailIfDifferent` ) (standardvärdet "FailIfDifferent")

**--content-disposition** sträng Ange rubriken innehållsdisposition. Returneras vid nedladdning.

**--content-encoding sträng** Ange content-encoding rubrik. Returneras vid nedladdning.

**--content-language string** Ange innehållsspråkrubriken. Returneras vid nedladdning.

**--content-type** string Anger filens innehållstyp. Innebär no-guess-mime-type. Returneras vid nedladdning.

**--dekomprimera** Dekomprimera automatiskt filer vid nedladdning, om deras innehållskodning indikerar att de är komprimerade. De innehållskodningsvärden som stöds är `gzip` och `deflate` . Filnamnstillägg `.gz` / `.gzip` för eller `.zz` är inte nödvändiga, men tas bort om de finns.

**--exclude-attributes string** (endast Windows) Exkluderar filer vars attribut matchar attributlistan. Exempel: A; S; R

**Strängen --exclude-blob-type** (Valfritt) anger vilken typ av blob ( ) som ska undantas vid kopiering av blobar från `BlockBlob` /  `PageBlob` /  `AppendBlob` containern eller kontot. Användning av den här flaggan gäller inte för kopiering av data från icke-Azure-tjänst till tjänst. Mer än en blob ska avgränsas med `;` . 

**--exclude-path string** Undanta dessa sökvägar vid kopiering. Det här alternativet stöder inte jokertecken (*). Kontrollerar relativt sökvägsprefix (till `myFolder;myFolder/subDirName/file.pdf` exempel: ). När sökvägarna används i kombination med konto-traversal inkluderar sökvägarna inte containernamnet.

**--exclude-pattern string** Exkludera dessa filer vid kopiering. Det här alternativet stöder jokertecken (*).

**--follow-symlinks**  Följ symboliska länkar vid uppladdning från det lokala filsystemet.

**--force-if-read-only** När du skriver över en befintlig fil i Windows eller Azure Files måste du tvinga överskrivningen att fungera även om den befintliga filen har sina skrivskyddade attribut inställda.

**--from-to sträng** Om du vill kan du ange kombinationen av källmålet. Till exempel: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--help**  help for copy.

**--include-after sträng** Inkludera endast de filer som ändrats på eller efter det angivna datumet/tiden. Värdet ska vara i ISO8601-format. Om ingen tidszon anges antas värdet finnas i den lokala tidszonen för den dator som kör AzCopy. till exempel `2020-08-19T15:04:00Z` för en UTC-tid eller `2020-08-19` för midnatt (00:00) i den lokala tidszonen. Som på AzCopy 10.5 gäller den här flaggan endast för filer, inte mappar, så mappegenskaper kopieras inte när du använder den här flaggan med `--preserve-smb-info` eller `--preserve-smb-permissions` .

 **--include-before sträng** Inkludera endast de filer som ändrats före eller på det angivna datumet/tiden. Värdet ska vara i ISO8601-format. Om ingen tidszon anges antas värdet finnas i den lokala tidszonen på den dator som kör AzCopy. T.ex. `2020-08-19T15:04:00Z` för en UTC-tid eller `2020-08-19` vid midnatt (00:00) i den lokala tidszonen. Från och med AzCopy 10.7 gäller den här flaggan endast för filer, inte mappar, så mappegenskaper kopieras inte när du använder den här flaggan med `--preserve-smb-info` eller `--preserve-smb-permissions` .

**--include-attributes string** (endast Windows) Innehåller filer vars attribut matchar attributlistan. Till exempel: A; S; R

**--include-path string** Inkludera endast dessa sökvägar vid kopiering. Det här alternativet stöder inte jokertecken (*). Kontrollerar relativt sökvägsprefix (till `myFolder;myFolder/subDirName/file.pdf` exempel: ).

**--include-pattern sträng** Inkludera endast dessa filer vid kopiering. Det här alternativet stöder jokertecken (*). Avgränsa filer med hjälp av `;` en .

**Strängen --list-of-versions** Anger en fil där varje versions-ID visas på en separat rad. Se till att källan måste peka på en enskild blob och att alla versions-ID:er som anges i filen med den här flaggan endast tillhör källbloben. AzCopy laddar ned de angivna versionerna i målmappen. Mer information finns i Ladda [ned tidigare versioner av en blob](./storage-use-azcopy-v10.md#transfer-data).

**--log-level** string (Sträng på loggnivå) Definiera loggens verbositet för loggfilen, tillgängliga nivåer: INFO(alla begäranden/svar), WARNING(långsamma svar), ERROR (endast misslyckade begäranden) och NONE (inga utdataloggar). (standard `INFO` ). 

**--metadata string** Upload to Azure Storage with these key-value pairs as metadata .-metadata string Upload to Azure Storage with these key-value pairs as metadata.

**--no-guess-mime-type**  Förhindrar att AzCopy identifierar innehållstypen baserat på filnamnstillägget eller innehållet i filen.

**--overwrite** string Overwrite the conflicting files and blobs at the destination if this flag is set to true. (standard `true` ) Möjliga värden är `true` , `false` , och `prompt` `ifSourceNewer` . För mål som stöder mappar skrivs motstridiga egenskaper på mappnivå över den här flaggan eller om ett positivt `true` svar ges till prompten. (standardvärdet "true")

**--page-blob-tier string** Upload page blob to Azure Storage using this blob tier. (standard `None` ). (standardvärdet "Ingen")

**--preserve-last-modified-time**  Endast tillgängligt när målet är filsystem.

**--preserve-owner**    Har bara en effekt i nedladdningar och endast `--preserve-smb-permissions` när används. Om sant (standard) bevaras filens ägare och grupp i nedladdningar. Om det är inställt `--preserve-smb-permissions` på false bevarar fortfarande ACL:er, men Ägare och Grupp baseras på användaren som kör AzCopy (standard sant)

**--preserve-smb-info**   Falskt som standard. Bevarar SMB-egenskapsinformation (senaste skrivtid, skapandetid, attributbit) mellan SMB-medvetna resurser (Windows och Azure Files). Endast attributbitarna som stöds av Azure Files överförs. alla andra ignoreras. Den här flaggan gäller för både filer och mappar, såvida inte ett filter för endast filer har angetts (till exempel include-pattern). Den information som överförs för mappar är samma som för filer, förutom senaste skrivtiden som aldrig bevaras för mappar.

**--preserve-smb-permissions**   Falskt som standard. Bevarar SMB-ACL:er mellan medvetna resurser (Windows och Azure Files). För nedladdningar behöver du också flaggan för att återställa behörigheter där den `--backup` nya ägaren inte är den användare som kör AzCopy. Den här flaggan gäller för både filer och mappar, såvida inte ett filter för endast filer har angetts (till exempel `include-pattern` ).

**--put-md5**    Skapa en MD5-hash för varje fil och spara hashen som Content-MD5-egenskapen för målbloben eller målfilen. (Hashen skapas INTE som standard.) Endast tillgängligt vid uppladdning.

**--rekursiv**    Titta på underkataloger rekursivt vid uppladdning från det lokala filsystemet.

**--s2s-detect-source-changed**   Identifiera om källfilen/bloben ändras medan den läses. (Den här parametern gäller endast för tjänst-till-tjänst-kopior, eftersom motsvarande kontroll är permanent aktiverad för uppladdningar och nedladdningar.)

**--s2s-handle-invalid-metadata-sträng** Anger hur ogiltiga metadatanycklar hanteras. Tillgängliga alternativ: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (standard `ExcludeIfInvalid` ). (standardvärdet "ExcludeIfInvalid")

**--s2s-preserve-access-tier**   Bevara åtkomstnivån under tjänst-till-tjänstkopiering. Se [åtkomstnivåer för Azure Blob Storage: hot, cool och archive](../blobs/storage-blob-storage-tiers.md) för att se till att mållagringskontot stöder inställning av åtkomstnivå. Om det inte finns stöd för att ange åtkomstnivå använder du s2sPreserveAccessTier=false för att kringgå kopiering av åtkomstnivå. (standard `true` ).  (standardvärdet "true")

**--s2s-preserve-properties**   Bevara fullständiga egenskaper under tjänst-till-tjänstkopiering. För AWS S3 och Azure File som inte är en enskild filkälla returnerar liståtgärden inte fullständiga egenskaper för objekt och filer. För att bevara fullständiga egenskaper måste AzCopy skicka ytterligare en begäran per objekt eller fil. (standard true)

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**--cap-mbps float**   Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.

**--output-type** strängformat för kommandots utdata. Alternativen är: text, json. Standardvärdet är `text`. (standard "text")

**--trusted-microsoft-suffixesträng** Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Alla som anges här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
