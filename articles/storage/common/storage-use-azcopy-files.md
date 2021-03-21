---
title: Överföra data till eller från Azure Files med AzCopy v10 | Microsoft Docs
description: Överföra data med AzCopy och fil lagring. AzCopy är ett kommando rads verktyg för att kopiera blobbar eller filer till eller från ett lagrings konto. Använd AzCopy med Azure Files.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 2cca8a93330e5ddd965d27532895ed1d6702c123
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179654"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Överföra data med AzCopy och fillagring 

AzCopy är ett kommando rads verktyg som du kan använda för att kopiera filer till eller från ett lagrings konto. Den här artikeln innehåller exempel kommandon som fungerar med Azure Files.

Innan du börjar kan du läsa artikeln [Kom igång med AZCopy](storage-use-azcopy-v10.md) för att hämta AzCopy och bekanta dig med verktyget.

> [!TIP]
> I exemplen i den här artikeln omger du Sök vägs argumenten med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

## <a name="create-file-shares"></a>Skapa filresurser

Du kan använda kommandot [AzCopy make](storage-ref-azcopy-make.md) för att skapa en fil resurs. Exemplet i det här avsnittet skapar en fil resurs med namnet `myfileshare` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Exempel** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

För detaljerade referens dokument, se [AzCopy-fabrikat](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Ladda upp filer

Du kan använda kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) för att ladda upp filer och kataloger från den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Ladda upp en fil
> * Ladda upp en katalog
> * Ladda upp innehållet i en katalog
> * Ladda upp en fil

> [!TIP]
> Du kan ändra uppladdnings åtgärden med valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera åtkomst kontrol listor (ACL: er) tillsammans med filerna.|**--bevara-SMB-Permissions** = \[ Sant \| falskt\]|
> |Kopiera information om SMB-egenskaper tillsammans med filerna.|**--Behåll-SMB-info** = \[ Sant \| falskt\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> AzCopy beräknar och lagrar inte filens MD5-hash-kod automatiskt. Om du vill att AzCopy ska göra det lägger du till `--put-md5` flaggan i varje Copy-kommando. När filen har hämtats beräknar AzCopy en MD5-hash för hämtade data och verifierar att MD5-hashen som lagras i filens `Content-md5` egenskap matchar det beräknade hash-värdet.

### <a name="upload-a-file"></a>Ladda upp en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Exempel** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Du kan också ladda upp en fil med en jokertecken (*) var som helst i fil Sök vägen eller fil namnet. Till exempel: `'C:\myDirectory\*.txt'` , eller `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Ladda upp en katalog

I det här exemplet kopieras en katalog (och alla filer i katalogen) till en filresurs. Resultatet är en katalog i filresursen med samma namn.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Om du vill kopiera till en katalog i fil resursen anger du bara namnet på den katalogen i kommando strängen.

|    |     |
|--------|-----------|
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Om du anger namnet på en katalog som inte finns i fil resursen, skapar AzCopy en ny katalog med det namnet.

### <a name="upload-the-contents-of-a-directory"></a>Ladda upp innehållet i en katalog

Du kan ladda upp innehållet i en katalog utan att kopiera den innehåller själva katalogen med jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Exempel** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Lägg till `--recursive` flaggan för att ladda upp filer i alla under kataloger.

### <a name="upload-specific-files"></a>Ladda upp vissa filer

Du kan ladda upp vissa filer genom att använda fullständiga fil namn, partiella namn med jokertecken (*) eller med hjälp av datum och tider.

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga fil namn

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-path` alternativet. Separera enskilda fil namn genom att använda semikolon ( `;` ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

I det här exemplet överför AzCopy `C:\myDirectory\photos` katalogen och `C:\myDirectory\documents\myFile.txt` filen. Du måste inkludera `--recursive` alternativet för att överföra alla filer i `C:\myDirectory\photos` katalogen.

Du kan också utesluta filer med hjälp av `--exclude-path` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Använd jokertecken

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-pattern` alternativet. Ange partiella namn som innehåller jokertecken. Separera namn genom att använda semikolon ( `;` ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta filer med hjälp av `--exclude-pattern` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

`--include-pattern`Alternativen och `--exclude-pattern` gäller endast för fil namn och inte till sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalog träd, använder du `--recursive` alternativet för att hämta hela katalog trädet och använder sedan `--include-pattern` och anger `*.txt` för att hämta alla textfiler.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Ladda upp filer som har ändrats efter datum och tid 

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-after` alternativet. Ange ett datum och en tid i ISO 8601-format (till exempel: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Exempel** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'` |

Mer detaljerad information finns i referens dokument för [AzCopy-kopiering](storage-ref-azcopy-copy.md) .

## <a name="download-files"></a>Ladda ned filer

Du kan använda kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) för att ladda ned filer, kataloger och fil resurser till den lokala datorn.

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Ladda ned en fil
> * Ladda ned en katalog
> * Hämta innehållet i en katalog
> * Hämta vissa filer

> [!TIP]
> Du kan anpassa nedladdnings åtgärden genom att använda valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera åtkomst kontrol listor (ACL: er) tillsammans med filerna.|**--bevara-SMB-Permissions** = \[ Sant \| falskt\]|
> |Kopiera information om SMB-egenskaper tillsammans med filerna.|**--Behåll-SMB-info** = \[ Sant \| falskt\]|
> |Expandera filer automatiskt.|**--Decompress**|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Om `Content-md5` egenskap svärdet för en fil innehåller en hash, beräknar AzCopy en MD5-hash för hämtade data och kontrollerar att MD5-hashen som lagras i filens `Content-md5` egenskap matchar det beräknade hashvärdet. Om dessa värden inte matchar, Miss lyckas nedladdningen om du inte åsidosätter det här beteendet genom att lägga till `--check-md5=NoCheck` eller `--check-md5=LogOnly` till kommandot Kopiera.

### <a name="download-a-file"></a>Ladda ned en fil

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Ladda ned en katalog

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Det här exemplet resulterar i en katalog med namnet `C:\myDirectory\myFileShareDirectory` som innehåller alla hämtade filer.

### <a name="download-the-contents-of-a-directory"></a>Hämta innehållet i en katalog

Du kan ladda ned innehållet i en katalog utan att kopiera själva katalogen med hjälp av jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Lägg till `--recursive` flaggan för att ladda ned filer i alla under kataloger.

### <a name="download-specific-files"></a>Hämta vissa filer

Du kan hämta vissa filer genom att använda fullständiga fil namn, partiella namn med jokertecken (*) eller med hjälp av datum och tider.

#### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga fil namn

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-path` alternativet. Separera enskilda fil namn genom att använda semikolon ( `;` ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

I det här exemplet överför AzCopy `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` katalogen och `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` filen. Inkludera `--recursive` alternativet för att överföra alla filer i `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` katalogen.

Du kan också utesluta filer med hjälp av `--exclude-path` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Använd jokertecken

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-pattern` alternativet. Ange partiella namn som innehåller jokertecken. Separera namn genom att använda semikolon ( `;` ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta filer med hjälp av `--exclude-pattern` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

`--include-pattern`Alternativen och `--exclude-pattern` gäller endast för fil namn och inte till sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalog träd, använder du `--recursive` alternativet för att hämta hela katalog trädet och använder sedan `--include-pattern` och anger `*.txt` för att hämta alla textfiler.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Ladda ned filer som har ändrats efter datum och tid 

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-after` alternativet. Ange ett datum och en tid i ISO-8601-format (till exempel: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'` |


Mer detaljerad information finns i referens dokument för [AzCopy-kopiering](storage-ref-azcopy-copy.md) .

#### <a name="download-from-a-share-snapshot"></a>Ladda ned från en resurs ögonblicks bild

Du kan hämta en speciell version av en fil eller katalog genom att referera till **datetime** -värdet för en resurs ögonblicks bild. Mer information om hur du delar ögonblicks bilder finns i [Översikt över resurs ögonblicks bilder för Azure Files](../files/storage-snapshots-files.md). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'` |
| **Exempel** (Hämta en fil) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Exempel** (Hämta en katalog) | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive`|


## <a name="copy-files-between-storage-accounts"></a>Kopiera filer mellan lagringskonton

Du kan använda AzCopy för att kopiera filer till andra lagrings konton. Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats.

AzCopy använder [Server-till-Server-](/rest/api/storageservices/put-block-from-url) [API: er](/rest/api/storageservices/put-page-from-url), så data kopieras direkt mellan lagrings servrar. Dessa kopierings åtgärder använder inte datorns nätverks bandbredd. Du kan öka data flödet för dessa åtgärder genom att ange värdet för `AZCOPY_CONCURRENCY_VALUE` miljövariabeln. Mer information finns i [optimera data flöde](storage-use-azcopy-configure.md#optimize-throughput).

Du kan också kopiera vissa versioner av en fil genom att referera till **datetime** -värdet för en resurs ögonblicks bild. Mer information om hur du delar ögonblicks bilder finns i [Översikt över resurs ögonblicks bilder för Azure Files](../files/storage-snapshots-files.md). 

Det här avsnittet innehåller följande exempel:

> [!div class="checklist"]
> * Kopiera en fil till ett annat lagrings konto
> * Kopiera en katalog till ett annat lagrings konto
> * Kopiera en fil resurs till ett annat lagrings konto
> * Kopiera alla filresurser, kataloger och filer till ett annat lagringskonto

> [!TIP]
> Du kan ändra kopierings åtgärden genom att använda valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera åtkomst kontrol listor (ACL: er) tillsammans med filerna.|**--bevara-SMB-Permissions** = \[ Sant \| falskt\]|
> |Kopiera information om SMB-egenskaper tillsammans med filerna.|**--Behåll-SMB-info** = \[ Sant \| falskt\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Kopiera en fil till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |
| **Exempel** (resurs ögonblicks bild) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiera en katalog till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Exempel** (resurs ögonblicks bild) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-a-file-share-to-another-storage-account"></a>Kopiera en fil resurs till ett annat lagrings konto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Exempel** (resurs ögonblicks bild) | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Kopiera alla filresurser, kataloger och filer till ett annat lagringskonto

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |
| **Exempel** (resurs ögonblicks bild) | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |


## <a name="synchronize-files"></a>Synkronisera filer

Du kan synkronisera innehållet i en fil resurs med en annan fil resurs. Du kan också synkronisera innehållet i en katalog i en fil resurs med innehållet i en katalog som finns i en annan fil resurs. Synkronisering är ett sätt. Med andra ord kan du välja vilken av dessa två slut punkter som är källan och vilken som är målet. Synkronisering använder också server-till-Server-API: er.

> [!NOTE]
> För närvarande stöds det här scenariot endast för konton som inte har ett hierarkiskt namn område. Den aktuella versionen av AzCopy synkroniseras inte mellan Azure Files och Blob Storage.

Kommandot [Sync](storage-ref-azcopy-sync.md) jämför fil namn och senaste ändrade tidsstämplar. Ange `--delete-destination` värdet för valfri flagga `true` eller `prompt` ta bort filer i mål katalogen om filerna inte längre finns i käll katalogen.

Om du ställer in `--delete-destination` flaggan på `true` , tar AzCopy bort filer utan att ange någon prompt. Om du vill att en prompt ska visas innan AzCopy tar bort en fil, ställer du in `--delete-destination` flaggan på `prompt` .

> [!TIP]
> Du kan anpassa din synkronisering genom att använda valfria flaggor. Här är några exempel.
>
> |Scenario|Flagga|
> |---|---|
> |Kopiera åtkomst kontrol listor (ACL: er) tillsammans med filerna.|**--bevara-SMB-Permissions** = \[ Sant \| falskt\]|
> |Kopiera information om SMB-egenskaper tillsammans med filerna.|**--Behåll-SMB-info** = \[ Sant \| falskt\]|
> |Undanta filer baserat på ett mönster.|**--Exkludera-sökväg**|
> |Ange hur detaljerad du vill att dina Sync-relaterade logg poster ska vara.|**--inloggnings nivå** = \[ VARNINGs \| fel \| information \| ingen\]|
> 
> En fullständig lista finns i [alternativ](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Uppdatera en fil resurs med ändringar i en annan fil resurs

Den första fil resursen som visas i det här kommandot är källan. Den andra är målet.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Uppdatera en katalog med ändringar i en katalog i en annan fil resurs

Den första katalogen som visas i det här kommandot är källan. Den andra är målet.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Uppdatera en fil resurs så att den matchar innehållet i en resurs ögonblicks bild

Den första fil resursen som visas i det här kommandot är källan. I slutet av URI: n lägger du till strängen `&sharesnapshot=` följt av ögonblicks bildens **datetime** -värde. 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

Mer information om resurs ögonblicks bilder finns i [Översikt över resurs ögonblicks bilder för Azure Files](../files/storage-snapshots-files.md).

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i någon av följande artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Överföra data](storage-use-azcopy-v10.md#transfer-data)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)
