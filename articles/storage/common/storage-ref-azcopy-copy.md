---
title: AzCopy-kopia | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 03/08/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c676b92fd07c6e444aa22f25c48fdb1b1957ca7a
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493772"
---
# <a name="azcopy-copy"></a>azcopy kopiera

Kopierar käll data till en målplats.

## <a name="synopsis"></a>Sammanfattning

Kopierar käll data till en målplats. De vägvisningar som stöds är:

  - lokal <-> Azure-Blob (SAS eller OAuth-autentisering)
  - lokal <-> Azure Files (autentisering med ASSOCIATIONer för delade/kataloger)
  - lokala <-> Azure Data Lake Storage gen 2 (SAS, OAuth eller delad nyckel-autentisering)
  - Azure Blob (SAS eller offentlig) – > Azure Blob (SAS eller OAuth-autentisering)
  - Azure Blob (SAS eller offentlig) – > Azure Files (SAS)
  - Azure Files (SAS) – > Azure Files (SAS)
  - Azure Files (SAS) – > Azure Blob (SAS eller OAuth-autentisering)
  - Amazon Web Services (AWS) S3 (åtkomst nyckel) – > Azure Block Blob (SAS eller OAuth-autentisering)
  - Google Cloud Storage (tjänst konto nyckel) – > Azure Block Blob (SAS eller OAuth-autentisering) [för hands version]

Mer information finns i avsnittet exempel i den här artikeln.

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Avancerat

AzCopy identifierar automatiskt filernas innehålls typ när du laddar upp dem från den lokala disken. AzCopy identifierar innehålls typen baserat på fil namns tillägget eller innehållet (om inget tillägg har angetts).

Den inbyggda uppslags tabellen är liten, men på UNIX är den förstärkt av det lokala systemets `mime.types` filer om de är tillgängliga under ett eller flera av följande namn:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

I Windows extraheras MIME-typer från registret. Den här funktionen kan inaktive ras med hjälp av en flagga. Mer information finns i avsnittet flagga i den här artikeln.

Om du anger en miljö variabel med hjälp av kommando raden, kommer den variabeln att läsas i kommando rads historiken. Överväg att ta bort variabler som innehåller autentiseringsuppgifter från kommando rads historiken. Om du vill att variablerna ska visas i historiken kan du använda ett skript för att uppmana användaren att ange sina autentiseringsuppgifter och ange miljövariabeln.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exempel

Överför en enda fil med OAuth-autentisering. Om du ännu inte har loggat in på AzCopy kör du `azcopy login` kommandot innan du kör följande kommando.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Samma som ovan, men den här gången kan du också beräkna MD5-hashen för fil innehållet och spara den som blobens Content-MD5-egenskap:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Ladda upp en enstaka fil med hjälp av en SAS-token:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Ladda upp en enstaka fil med hjälp av en SAS-token och-rör (endast block-blobbar):
  
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

Ladda upp en uppsättning filer med hjälp av en SAS-token och jokertecken (*):
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Ladda upp filer och kataloger med hjälp av en SAS-token och jokertecken (*):

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Ladda upp filer och kataloger för att Azure Storage konto och ange taggar för frågesträngar som har kodats i bloben. 

- Om du vill ange Taggar {Key = "bort bort", val = "foo"} och {Key = "bort bort 2", val = "bar"}, använder du följande syntax: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- Nycklar och värden är URL-kodade och nyckel/värde-par avgränsas med ett et-tecken (&)

- När du anger taggar på Blobbarna, finns det ytterligare behörigheter (t för taggar) i SAS utan vilken tjänsten ger auktoriseringsfel tillbaka.

Hämta en enda fil med hjälp av OAuth-autentisering. Om du ännu inte har loggat in på AzCopy kör du `azcopy login` kommandot innan du kör följande kommando.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Hämta en enda fil med hjälp av en SAS-token:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Hämta en enda fil med hjälp av en SAS-token och sedan dirigera utdata till en fil (endast block-blobbar):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Ladda ned en hel katalog med hjälp av en SAS-token:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

En kommentar om att använda jokertecken (*) i URL: er:

Det finns bara två sätt som stöds för att använda jokertecken i en URL. 

- Du kan använda en precis efter det sista snedstrecket (/) för en URL. Användningen av jokertecknet kopierar alla filer i en katalog direkt till målet utan att placera dem i en under katalog. 

- Du kan också ett jokertecken i namnet på en behållare så länge URL: en endast refererar till en behållare och inte till en blob. Du kan använda den här metoden för att hämta filer från en delmängd behållare. 

Hämta innehållet i en katalog utan att kopiera den innehåller själva katalogen.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Ladda ned ett helt lagrings konto.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Hämta en delmängd av behållare i ett lagrings konto med hjälp av en symbol för jokertecken (*) i behållar namnet.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Kopiera en enskild blob till en annan BLOB med hjälp av en SAS-token.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopiera en enskild blob till en annan BLOB med hjälp av en SAS-token och en autentiseringstoken. Du måste använda en SAS-token i slutet av käll kontots URL, men mål kontot behöver inte vara ett om du loggar in i AzCopy med hjälp av `azcopy login` kommandot. 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Kopiera en virtuell BLOB-katalog till en annan med hjälp av en SAS-token:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Kopiera alla BLOB-behållare, kataloger och blobbar från lagrings kontot till ett annat med hjälp av en SAS-token:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopiera ett enskilt objekt till Blob Storage från Amazon Web Services (AWS) S3 med hjälp av en åtkomst nyckel och en SAS-token. Ange först miljövariabeln `AWS_ACCESS_KEY_ID` och `AWS_SECRET_ACCESS_KEY` för AWS S3-källan.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopiera en hel katalog till Blob Storage från AWS S3 med hjälp av en åtkomst nyckel och en SAS-token. Ange först miljövariabeln `AWS_ACCESS_KEY_ID` och `AWS_SECRET_ACCESS_KEY` för AWS S3-källan.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Se https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html för att bättre förstå [Folder]-plats hållaren.

Kopiera alla buckets till Blob Storage från Amazon Web Services (AWS) med hjälp av en åtkomst nyckel och en SAS-token. Ange först miljövariabeln `AWS_ACCESS_KEY_ID` och `AWS_SECRET_ACCESS_KEY` för AWS S3-källan.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopiera alla buckets till Blob Storage från ett Amazon Web Services (AWS)-region med hjälp av en åtkomst nyckel och en SAS-token. Ange först miljövariabeln `AWS_ACCESS_KEY_ID` och `AWS_SECRET_ACCESS_KEY` för AWS S3-källan.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopiera en delmängd av buckets med hjälp av en symbol för jokertecken (*) i Bucket-namnet. Precis som i föregående exempel behöver du en åtkomst nyckel och en SAS-token. Se till att ställa in miljövariabeln `AWS_ACCESS_KEY_ID` och `AWS_SECRET_ACCESS_KEY` för AWS S3-källan.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Överför filer och kataloger till Azure Storage kontot och ange de angivna taggarna för frågesträngen i bloben. 

- Om du vill ange Taggar {Key = "bort bort", val = "foo"} och {Key = "bort bort 2", val = "bar"}, använder du följande syntax: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- Nycklar och värden är URL-kodade och nyckel/värde-par avgränsas med ett et-tecken (&)
    
- När du anger taggar på Blobbarna, finns det ytterligare behörigheter (t för taggar) i SAS utan vilken tjänsten ger auktoriseringsfel tillbaka.

Kopiera ett enskilt objekt till Blob Storage från Google Cloud Storage med hjälp av en tjänst konto nyckel och en SAS-token. Ställ först in miljövariabeln GOOGLE_APPLICATION_CREDENTIALS för Google Cloud Storage-källan.
  
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopiera en hel katalog till Blob Storage från Google Cloud Storage med hjälp av en tjänst konto nyckel och en SAS-token. Ställ först in miljövariabeln GOOGLE_APPLICATION_CREDENTIALS för Google Cloud Storage-källan.
 
```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Kopiera en hel Bucket till Blob Storage från Google Cloud Storage med hjälp av en tjänst konto nyckel och en SAS-token. Ställ först in miljövariabeln GOOGLE_APPLICATION_CREDENTIALS för Google Cloud Storage-källan.

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Kopiera alla buckets till Blob Storage från Google Cloud Storage med hjälp av en tjänst konto nyckel och en SAS-token. Ange först miljövariabler GOOGLE_APPLICATION_CREDENTIALS och GOOGLE_CLOUD_PROJECT =<projekt-ID> för GC generationer-källa

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Kopiera en delmängd av buckets med hjälp av en jokertecken (*) i Bucket-namnet från Google Cloud Storage med hjälp av en tjänst konto nyckel och en SAS-token som mål. Ställ först in miljövariabler GOOGLE_APPLICATION_CREDENTIALS och GOOGLE_CLOUD_PROJECT =<projekt-ID> för källan Google Cloud Storage.
 
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>Alternativ

**--säkerhets kopiering** Aktiverar Windows-SeBackupPrivilege för uppladdningar eller SeRestorePrivilege för hämtningar för att tillåta AzCopy att se och läsa alla filer, oavsett deras fil Systems behörigheter och för att återställa alla behörigheter. Kräver att kontot som kör AzCopy redan har dessa behörigheter (till exempel har administratörs rättigheter eller är medlem i `Backup Operators` gruppen). Den här flaggan aktiverar privilegier som kontot redan har.

**--BLOB-Taggar** sträng uppsättnings taggar på blobbar för att kategorisera data i ditt lagrings konto.

**--BLOB-Type** -strängen definierar BLOB-typen vid målet. Detta används för att ladda upp blobar och när du kopierar mellan konton (standard `Detect` ). Giltiga värden är `Detect` , `BlockBlob` , `PageBlob` och `AppendBlob` . När du kopierar mellan konton `Detect` använder värdet AzCopy för att fastställa typen av käll-BLOB för att fastställa typen av BLOB för målet. När du laddar upp en fil, `Detect` fastställer om filen är en VHD-eller VHDX-fil baserat på fil namns tillägget. Om filen är en VHD-eller VHDX-fil behandlar AzCopy filen som en Page blob. (standard "identifiering")

**--Block-Blob-Tier** sträng uppladdnings Block Blob för att Azure Storage använda denna BLOB-nivå. (standard "ingen")

**--block-size-MB** float Använd den här block storleken (anges i MIB) vid överföring till Azure Storage och nedladdning från Azure Storage. Standardvärdet beräknas automatiskt baserat på fil storlek. Decimal tal tillåts (till exempel: 0,25).

**--Cache-Control** -sträng ange Cache-Control-huvudet. Returnerades vid nedladdning.

**--kontrol lera längd** Kontrol lera längden på en fil på målet efter överföringen. Om det finns ett matchnings fel mellan källan och målet markeras överföringen som misslyckad. (Standardvärdet är `true` )

**--kontrol lera-MD5-** sträng anger hur strikt MD5-hashar ska verifieras vid hämtning. Endast tillgängligt vid hämtning. Tillgängliga alternativ: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (standard `FailIfDifferent` ) (standard "FailIfDifferent")

**--innehålls-dispositions** sträng ange innehålls-dispositions rubriken. Returnerades vid nedladdning.

**--innehålls kodnings** sträng anger innehålls kodnings huvudet. Returnerades vid nedladdning.

**--innehålls språk** sträng anger innehålls-språkets rubrik. Returnerades vid nedladdning.

**--innehålls typ** sträng anger filens innehålls typ. Innebär No-gissning-MIME-type. Returnerades vid nedladdning.

**--Decompress** Expandera automatiskt filer vid hämtning, om deras innehålls kodning visar att de är komprimerade. Innehålls kodnings värden som stöds är `gzip` och `deflate` . Fil namns tilläggen `.gz` / `.gzip` eller `.zz` är inte nödvändiga, men kommer att tas bort om det är tillgängligt.

**--sträng för exkludera attribut** (endast Windows) utesluter filer vars attribut matchar attributlistan. Till exempel: A; Na R

**--exkludera-Blob-typ** sträng anger alternativt vilken typ av BLOB ( `BlockBlob` /  `PageBlob` /  `AppendBlob` ) som ska undantas vid kopiering av blobbar från behållaren eller kontot. Det går inte att använda den här flaggan för att kopiera data från tjänster som inte är från Azure till tjänsten. Mer än en BLOB ska separeras av `;` . 

**--sträng för exkluderings Sök väg** undantar dessa sökvägar vid kopiering. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefix för relativ sökväg (till exempel: `myFolder;myFolder/subDirName/file.pdf` ). När de används i kombination med konto Traversal inkluderar inte sökvägar namnet på behållaren.

**--exkludera-Pattern-** sträng exkludera dessa filer vid kopiering. Det här alternativet stöder jokertecken (*).

**--Följ-symlinks**  Följ symboliska länkar när du laddar upp från lokalt fil system.

**--tvång-skrivskyddad** När du skriver över en befintlig fil i Windows eller Azure Files tvingar du överskrivning att fungera även om den befintliga filen har en skrivskyddad attribut uppsättning.

**--från-till-** sträng (valfritt) anger källans mål kombination. Exempel: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--Hjälp**  för kopiering.

**--include-efter** sträng inkluderar endast de filer som har ändrats på eller efter det datum/tid som angetts. Värdet ska vara i ISO8601-format. Om ingen tidszon anges antas värdet vara i den lokala tids zonen på den dator som kör AzCopy. till exempel `2020-08-19T15:04:00Z` för en UTC-tid eller `2020-08-19` för midnatt (00:00) i den lokala tids zonen. Som vid AzCopy 10,5 gäller den här flaggan bara för filer, inte mappar, så att mappegenskaper inte kopieras när du använder den här flaggan med `--preserve-smb-info` eller `--preserve-smb-permissions` .

 **--include-före-** sträng inkluderar endast de filer som har ändrats före eller efter angivet datum/tid. Värdet ska vara i ISO8601-format. Om ingen tidszon anges antas värdet vara i den lokala tids zonen på den dator som kör AzCopy. T.ex. `2020-08-19T15:04:00Z` för en UTC-tid, eller `2020-08-19` för midnatt (00:00) i den lokala tids zonen. Från och med AzCopy 10,7 gäller den här flaggan endast filer, inte mappar, så att mappegenskaper inte kopieras när du använder den här flaggan med `--preserve-smb-info` eller `--preserve-smb-permissions` .

**--include-attribut** sträng (endast Windows) inkluderar filer vars attribut matchar attributlistan. Till exempel: A; Na R

**--include-Path-** sträng innehåller bara dessa sökvägar vid kopiering. Det här alternativet stöder inte jokertecken (*). Kontrollerar prefix för relativ sökväg (till exempel: `myFolder;myFolder/subDirName/file.pdf` ).

**--Inkludera-mönster** sträng inkludera endast dessa filer vid kopiering. Det här alternativet stöder jokertecken (*). Separera filer med hjälp av en `;` .

**--list-of-versions** -sträng anger en fil där varje VERSIONS-ID visas på en separat rad. Se till att källan måste peka på en enskild blob och att alla versions-ID: n som anges i filen med den här flaggan endast tillhör käll-bloben. AzCopy kommer att hämta de angivna versionerna i den angivna målmappen. Mer information finns i [Hämta tidigare versioner av en BLOB](./storage-use-azcopy-v10.md#transfer-data).

**--sträng på loggnivå** definierar loggens utförlighet för logg filen, tillgängliga nivåer: info (alla begär Anden/svar), varning (långsamma svar), fel (endast misslyckade förfrågningar) och ingen (inga utgående loggar). (standard `INFO` ). 

**--** sträng överföring av metadata till Azure Storage med dessa nyckel/värde-par som metadata.

**--no-gissning-MIME-type**  Förhindrar AzCopy från att identifiera innehålls typ baserat på fil namns tillägget eller innehållet i filen.

**--Skriv över** sträng skriver över konfliktskapande filer och blobbar på målet om den här flaggan har angetts till true. (standard `true` ) Möjliga värden är `true` , `false` , `prompt` och `ifSourceNewer` . För destinationer som har stöd för mappar skrivs den motstridiga egenskaperna på mappnivå över den här flaggan `true` eller om ett positivt svar anges för prompten. (standard "true")

**--sid-BLOB-skiktets** sträng överförings-blob till Azure Storage att använda denna BLOB-nivå. (standard `None` ). (standard "ingen")

**--bevara-senaste ändrings tid**  Endast tillgängligt om målet är fil system.

**--Behåll-ägare**    Har endast en inverkan på hämtningar och endast när `--preserve-smb-permissions` används. Om värdet är sant (standardvärdet) bevaras fil ägaren och gruppen i hämtningar. Om värdet är false `--preserve-smb-permissions` bevarar fortfarande ACL: er men ägare och grupp baseras på den användare som kör AzCopy (standard sant)

**--Behåll-SMB-info**   Falskt som standard. Bevarar information om SMB-egenskaper (senaste skrivnings tid, skapande tid, attribut bitar) mellan SMB-medvetna resurser (Windows och Azure Files). Endast de BITS-attribut som stöds av Azure Files kommer att överföras. andra kommer att ignoreras. Den här flaggan gäller för både filer och mappar, om inte ett fil filter anges (till exempel include-Pattern). Informationen som överförs för mappar är samma som för filer, förutom senaste skrivnings tid som aldrig bevaras för mappar.

**--bevara-SMB-Permissions**   Falskt som standard. Bevarar SMB ACL: er mellan medvetna resurser (Windows och Azure Files). För hämtningar behöver du också `--backup` flagga för att återställa behörigheter där den nya ägaren inte kommer att vara den användare som kör AzCopy. Den här flaggan gäller för både filer och mappar, om inte ett fil filter anges (till exempel `include-pattern` ).

**--Skicka-MD5**    Skapa en MD5-hash av varje fil och spara hashen som Content-MD5-egenskapen för Målmatrisen eller-filen. (Som standard skapas inte hashen.) Endast tillgängligt vid uppladdning.

**--rekursivt**    Sök i under kataloger rekursivt vid överföring från lokalt fil system.

**--S2S-detect-källa-ändrad**   Identifiera om käll filen/blobben ändras när den läses. (Den här parametern gäller endast för tjänst-till-tjänst-kopior, eftersom motsvarande kontroll är permanent aktive rad för uppladdning och nedladdning.)

**--S2S-handle-ogiltig-metadata** -sträng anger hur ogiltiga nycklar för nycklar hanteras. Tillgängliga alternativ: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (standard `ExcludeIfInvalid` ). (standard "ExcludeIfInvalid")

**--S2S-konserver-Access-Tier**   Bevara åtkomst nivån under tjänst-till-tjänst-kopiering. Se [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](../blobs/storage-blob-storage-tiers.md) lag rings nivå för att säkerställa att mål lagrings kontot har stöd för att ange åtkomst nivå. I de fall då det inte finns stöd för att ange åtkomst nivå kan du använda s2sPreserveAccessTier = false för att kringgå kopiering av åtkomst nivå. (standard `true` ).  (standard "true")

**--S2S-bevara-egenskaper**   Behåll fullständiga egenskaper under tjänst-till-tjänst-kopiering. För AWS S3 och Azure File non-EnFile Source, returnerar List-åtgärden inte fullständiga egenskaper för objekt och filer. För att bevara fullständig egenskaper måste AzCopy skicka ytterligare en begäran per objekt eller fil. (standard sant)

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**--Cap-Mbit/s float**   CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.

**--Skriv** sträng format för kommandots utdata. Alternativen är: text, JSON. Standardvärdet är `text`. (standard text)

**--sträng för betrodd-Microsoft-suffix** anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.

## <a name="see-also"></a>Se även

- [AzCopy](storage-ref-azcopy.md)
