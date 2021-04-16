---
title: Överföra data till eller från Azure Files med hjälp av AzCopy v10 | Microsoft Docs
description: Överföra data med AzCopy och fillagring. AzCopy är ett kommandoradsverktyg för att kopiera blobar eller filer till eller från ett lagringskonto. Använd AzCopy med Azure Files.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 04bf84c5dc8a63929900f1bc95d7274074ef9d5a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498364"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Överföra data med AzCopy och fillagring 

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera filer till eller från ett lagringskonto. Den här artikeln innehåller exempelkommandon som fungerar med Azure Files.

Innan du börjar kan du läsa [artikeln Kom igång med AzCopy](storage-use-azcopy-v10.md) för att ladda ned AzCopy och bekanta dig med verktyget.

> [!TIP]
> Exemplen i den här artikeln omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt förutom Windows Command Shell (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe), omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

## <a name="create-file-shares"></a>Skapa filresurser

Du kan använda kommandot [azcopy make](storage-ref-azcopy-make.md) för att skapa en filresurs. Exemplet i det här avsnittet skapar en filresurs med namnet `myfileshare` .

**Syntax**

`azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'`

**Exempel**

```azcopy
azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

Detaljerade referensdokument finns i [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Ladda upp filer

Du kan använda [kommandot azcopy copy](storage-ref-azcopy-copy.md) för att ladda upp filer och kataloger från den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Ladda upp en fil
> * Ladda upp en katalog
> * Ladda upp innehållet i en katalog
> * Ladda upp en specifik fil

> [!TIP]
> Du kan justera uppladdningsåtgärden med hjälp av valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera åtkomstkontrollistor (ACL: er) tillsammans med filerna.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Kopiera SMB-egenskapsinformation tillsammans med filerna.|**--preserve-smb-info** = \[ true \| false\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> AzCopy beräknar och lagrar inte filens md5-hashkod automatiskt. Om du vill att AzCopy ska göra det lägger du till `--put-md5` flaggan i varje kopieringskommando. När filen laddas ned beräknar AzCopy på så sätt en MD5-hash för nedladdade data och verifierar att MD5-hashen som lagras i filens -egenskap matchar den beräknade `Content-md5` hashen.

### <a name="upload-a-file"></a>Ladda upp en fil

**Syntax**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'`

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

Du kan också ladda upp en fil med ett jokertecken (*) var som helst i sökvägen eller filnamnet. Till exempel: `'C:\myDirectory\*.txt'` , eller `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Ladda upp en katalog

I det här exemplet kopieras en katalog (och alla filer i katalogen) till en filresurs. Resultatet är en katalog i filresursen med samma namn.

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

Om du vill kopiera till en katalog i filresursen anger du bara namnet på katalogen i kommandosträngen.

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

Om du anger namnet på en katalog som inte finns i filresursen skapar AzCopy en ny katalog med det namnet.

### <a name="upload-the-contents-of-a-directory"></a>Ladda upp innehållet i en katalog

Du kan ladda upp innehållet i en katalog utan att kopiera själva katalogen med jokertecknet (*).

**Syntax**

`azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>`

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"
```

> [!NOTE]
> Lägg till flaggan `--recursive` för att ladda upp filer i alla underkataloger.

### <a name="upload-specific-files"></a>Ladda upp specifika filer

Du kan ladda upp specifika filer med fullständiga filnamn, partiella namn med jokertecken (*), eller genom att använda datum och tider.

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga filnamn

Använd [kommandot azcopy copy](storage-ref-azcopy-copy.md) med `--include-path` alternativet . Avgränsa enskilda filnamn med semikolon ( `;` ).

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>`

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'
```

I det här exemplet överför AzCopy `C:\myDirectory\photos` katalogen och `C:\myDirectory\documents\myFile.txt` filen. Du måste inkludera alternativet `--recursive` för att överföra alla filer i `C:\myDirectory\photos` katalogen.

Du kan också undanta filer med hjälp av `--exclude-path` alternativet . Mer information finns i [azcopy copy](storage-ref-azcopy-copy.md) reference docs.

#### <a name="use-wildcard-characters"></a>Använda jokertecken

Använd [kommandot azcopy copy](storage-ref-azcopy-copy.md) med `--include-pattern` alternativet . Ange partiella namn som innehåller jokertecken. Avgränsa namn med semikolon ( `;` ).

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'
```

Du kan också undanta filer med hjälp av `--exclude-pattern` alternativet . Mer information finns i [azcopy copy](storage-ref-azcopy-copy.md) reference docs.

Alternativen `--include-pattern` och gäller endast för filnamn och inte för `--exclude-pattern` sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalogträd använder du alternativet för att hämta hela katalogträdet och använder sedan och anger för att hämta alla `--recursive` `--include-pattern` `*.txt` textfiler.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Ladda upp filer som har ändrats efter ett datum och en tid 

Använd [kommandot azcopy copy](storage-ref-azcopy-copy.md) med `--include-after` alternativet . Ange datum och tid i ISO 8601-format (till exempel: `2020-08-19T15:04:00Z` ). 

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>`

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'
```

Detaljerad referens finns i [referensdokumenten för azcopy](storage-ref-azcopy-copy.md) copy.

## <a name="download-files"></a>Ladda ned filer

Du kan använda [kommandot azcopy copy](storage-ref-azcopy-copy.md) för att ladda ned filer, kataloger och filresurser till den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Ladda ned en fil
> * Ladda ned en katalog
> * Ladda ned innehållet i en katalog
> * Ladda ned specifika filer

> [!TIP]
> Du kan justera nedladdningsåtgärden med hjälp av valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera åtkomstkontrollistor (ACL: er) tillsammans med filerna.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Kopiera SMB-egenskapsinformation tillsammans med filerna.|**--preserve-smb-info** = \[ true \| false\]|
> |Dekomprimera filer automatiskt.|**--dekomprimera**|
> 
> En fullständig lista finns i [alternativen](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Om egenskapsvärdet för en fil innehåller en hash beräknar AzCopy en MD5-hash för nedladdade data och verifierar att MD5-hashen som lagras i filens -egenskap matchar den beräknade `Content-md5` `Content-md5` hashen. Om dessa värden inte matchar misslyckas nedladdningen om du inte åsidosätter det här beteendet genom att lägga till `--check-md5=NoCheck` `--check-md5=LogOnly` eller till kopieringskommandot.

### <a name="download-a-file"></a>Ladda ned en fil

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'
```

### <a name="download-a-directory"></a>Ladda ned en katalog

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive
```

Det här exemplet resulterar i en katalog `C:\myDirectory\myFileShareDirectory` med namnet som innehåller alla nedladdade filer.

### <a name="download-the-contents-of-a-directory"></a>Ladda ned innehållet i en katalog

Du kan ladda ned innehållet i en katalog utan att kopiera själva katalogen med hjälp av jokertecknet (*).

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'
```

> [!NOTE]
> Lägg till `--recursive` flaggan för att ladda ned filer i alla underkataloger.

### <a name="download-specific-files"></a>Ladda ned specifika filer

Du kan ladda ned specifika filer med fullständiga filnamn, partiella namn med jokertecken (*), eller genom att använda datum och tider.

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga filnamn

Använd [kommandot azcopy copy](storage-ref-azcopy-copy.md) med `--include-path` alternativet . Avgränsa enskilda filnamn med semikolon ( `;` ).

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

I det här exemplet överför AzCopy `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` katalogen och `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` filen. Inkludera alternativet `--recursive` för att överföra alla filer i `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` katalogen.

Du kan också undanta filer med hjälp av `--exclude-path` alternativet . Mer information finns i [azcopy copy](storage-ref-azcopy-copy.md) reference docs.

#### <a name="use-wildcard-characters"></a>Använda jokertecken

Använd [kommandot azcopy copy](storage-ref-azcopy-copy.md) med `--include-pattern` alternativet . Ange partiella namn som innehåller jokertecken. Avgränsa namn med semikolon ( `;` ).

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

Du kan också undanta filer med hjälp av `--exclude-pattern` alternativet . Mer information finns i [azcopy copy](storage-ref-azcopy-copy.md) reference docs.

Alternativen `--include-pattern` och gäller endast för filnamn och inte för `--exclude-pattern` sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalogträd använder du alternativet för att hämta hela katalogträdet och använder sedan och anger för att hämta `--recursive` `--include-pattern` alla `*.txt` textfiler.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Ladda ned filer som har ändrats efter ett datum och en tid 

Använd [kommandot azcopy copy](storage-ref-azcopy-copy.md) med `--include-after` alternativet . Ange datum och tid i ISO-8601-format (till exempel: `2020-08-19T15:04:00Z` ). 

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'
```

Detaljerad referens finns i [referensdokumenten för azcopy](storage-ref-azcopy-copy.md) copy.

#### <a name="download-from-a-share-snapshot"></a>Ladda ned från en resursögonblicksbild

Du kan ladda ned en specifik version av en fil eller katalog genom att referera till **DateTime-värdet för** en resursögonblicksbild. Mer information om resursögonblicksbilder [finns i Översikt över resursögonblicksbilder för Azure Files](../files/storage-snapshots-files.md). 

**Syntax**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'`

**Exempel (ladda ned en fil)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**Exempel (Ladda ned en katalog)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive
```

## <a name="copy-files-between-storage-accounts"></a>Kopiera filer mellan lagringskonton

Du kan använda AzCopy för att kopiera filer till andra lagringskonton. Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats.

AzCopy använder [server-till-server-API:er,](/rest/api/storageservices/put-block-from-url) [](/rest/api/storageservices/put-page-from-url)så data kopieras direkt mellan lagringsservrar. Dessa kopieringsåtgärder använder inte datorns nätverksbandbredd. Du kan öka dataflödet för dessa åtgärder genom att ange värdet för `AZCOPY_CONCURRENCY_VALUE` miljövariabeln . Mer information finns i [Öka samtidighet.](storage-use-azcopy-optimize.md#increase-concurrency)

Du kan också kopiera specifika versioner av en fil genom att referera till **DateTime-värdet för** en resursögonblicksbild. Mer information om resursögonblicksbilder [finns i Översikt över resursögonblicksbilder för Azure Files](../files/storage-snapshots-files.md). 

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Kopiera en fil till ett annat lagringskonto
> * Kopiera en katalog till ett annat lagringskonto
> * Kopiera en filresurs till ett annat lagringskonto
> * Kopiera alla filresurser, kataloger och filer till ett annat lagringskonto

> [!TIP]
> Du kan justera kopieringsåtgärden med hjälp av valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera åtkomstkontrollistor (ACL: er) tillsammans med filerna.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Kopiera SMB-egenskapsinformation tillsammans med filerna.|**--preserve-smb-info** = \[ true \| false\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Kopiera en fil till ett annat lagringskonto

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'`

**Exempel**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

**Exempel (resursögonblicksbild)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiera en katalog till ett annat lagringskonto

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Exempel**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Exempel (resursögonblicksbild)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-a-file-share-to-another-storage-account"></a>Kopiera en filresurs till ett annat lagringskonto

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Exempel**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D
```

**Exempel (resursögonblicksbild)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Kopiera alla filresurser, kataloger och filer till ett annat lagringskonto

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'`

**Exempel**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Exempel (resursögonblicksbild)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

## <a name="synchronize-files"></a>Synkronisera filer

Du kan synkronisera innehållet i en filresurs med en annan filresurs. Du kan också synkronisera innehållet i en katalog i en filresurs med innehållet i en katalog som finns i en annan filresurs. Synkronisering är ett sätt. Med andra ord väljer du vilken av dessa två slutpunkter som är källan och vilken som är målet. Synkroniseringen använder också server-till-server-API:er.

> [!NOTE]
> Det här scenariot stöds för närvarande endast för konton som inte har ett hierarkiskt namnområde. Den aktuella versionen av AzCopy synkroniseras inte mellan Azure Files och Blob Storage.

[Synkroniseringskommandot](storage-ref-azcopy-sync.md) jämför filnamn och tidsstämplar för senaste ändring. Ange den valfria flaggan till värdet eller för att ta bort filer i målkatalogen om dessa `--delete-destination` filer inte längre finns i `true` `prompt` källkatalogen.

Om du anger `--delete-destination` flaggan till `true` tar AzCopy bort filer utan att ange någon uppmaning. Om du vill att en uppmaning ska visas innan AzCopy tar bort en fil anger du `--delete-destination` flaggan till `prompt` .

> [!TIP]
> Du kan justera synkroniseringsåtgärden med hjälp av valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera åtkomstkontrollistor (ACL: er) tillsammans med filerna.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Kopiera SMB-egenskapsinformation tillsammans med filerna.|**--preserve-smb-info** = \[ true \| false\]|
> |Undanta filer baserat på ett mönster.|**--exclude-path**|
> |Ange hur detaljerade du vill att dina synkroniseringsrelaterade loggposter ska vara.|**--log-level** = \[ VARNINGSFEL \| \| INFO \| NONE\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Uppdatera en filresurs med ändringar i en annan filresurs

Den första filresursen som visas i det här kommandot är källan. Det andra är målet.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Exempel**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Uppdatera en katalog med ändringar i en katalog i en annan filresurs

Den första katalogen som visas i det här kommandot är källan. Det andra är målet.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive`

**Exempel**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Uppdatera en filresurs så att den matchar innehållet i en resursögonblicksbild

Den första filresursen som visas i det här kommandot är källan. I slutet av URI:en lägger du till strängen `&sharesnapshot=` följt av **DateTime-värdet** för ögonblicksbilden.

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Exempel**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
``` 

Mer information om resursögonblicksbilder finns [i Översikt över resursögonblicksbilder för Azure Files](../files/storage-snapshots-files.md).

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i någon av dessa artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data](storage-use-azcopy-v10.md#transfer-data)

Se de här artiklarna för att konfigurera inställningar, optimera prestanda och felsöka problem:

- [AzCopy-konfigurationsinställningar](storage-ref-azcopy-configuration-settings.md)
- [Optimera prestanda för AzCopy](storage-use-azcopy-optimize.md)
- [Felsöka Problem med AzCopy V10 i Azure Storage med hjälp av loggfiler](storage-use-azcopy-configure.md)

