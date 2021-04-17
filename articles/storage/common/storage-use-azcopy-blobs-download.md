---
title: Ladda ned blobar från Azure Blob Storage med hjälp av AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling AzCopy-exempelkommandon som hjälper dig att ladda ned blobar från Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 56bb36cfda9d0cf1a8882950c862a73ad1e77898
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502954"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy"></a>Ladda ned blobar från Azure Blob Storage med hjälp av AzCopy

Du kan ladda ned blobar och kataloger från Blob Storage med hjälp av kommandoradsverktyget AzCopy v10. 

Om du vill se exempel på andra typer av uppgifter, till exempel att ladda upp filer, synkronisera [](#next-steps) med Blob Storage eller kopiera blobar mellan konton, kan du se länkarna i avsnittet Nästa steg i den här artikeln.

## <a name="get-started"></a>Kom igång

I artikeln [Kom igång med AzCopy kan](storage-use-azcopy-v10.md) du ladda ned AzCopy och lära dig mer om hur du kan ange autentiseringsuppgifter för auktorisering till lagringstjänsten.

> [!NOTE] 
> Exemplen i den här artikeln förutsätter att du har angett autentiseringsuppgifter för auktorisering med hjälp Azure Active Directory (Azure AD).
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till blobdata kan du lägga till denna token i resurs-URL:en i varje AzCopy-kommando. Exempel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Ladda ned en blob

Ladda ned en blob med hjälp av [kommandot azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ('').

**Syntax**

``azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'``

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> Om egenskapsvärdet för en blob innehåller en hash beräknar AzCopy en MD5-hash för nedladdade data och verifierar att MD5-hashen som lagras i blobens egenskap matchar den beräknade `Content-md5` `Content-md5` hashen. Om dessa värden inte matchar misslyckas nedladdningen om du inte åsidosätter det här beteendet genom att lägga till `--check-md5=NoCheck` `--check-md5=LogOnly` eller till kopieringskommandot.

## <a name="download-a-directory"></a>Ladda ned en katalog

Ladda ned en katalog med hjälp av [kommandot azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe), omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

**Syntax**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

Det här exemplet resulterar i en katalog `C:\myDirectory\myBlobDirectory` med namnet som innehåller alla nedladdade blobar.

## <a name="download-directory-contents"></a>Ladda ned kataloginnehåll

Du kan ladda ned innehållet i en katalog utan att kopiera själva katalogen med hjälp av jokertecknet (*).

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt förutom Windows Command Shell (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

> [!NOTE]
> Det här scenariot stöds för närvarande endast för konton som inte har ett hierarkiskt namnområde.

**Syntax**

`azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'
```

Lägg till flaggan `--recursive` för att ladda ned filer i alla underkataloger.

## <a name="download-specific-blobs"></a>Ladda ned specifika blobar

Du kan ladda ned specifika blobar med fullständiga filnamn, partiella namn med jokertecken (*), eller genom att använda datum och tider. 

> [!TIP]
> De här exemplen omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

#### <a name="specify-multiple-complete-blob-names"></a>Ange flera fullständiga blobnamn

Använd [kommandot azcopy copy](storage-ref-azcopy-copy.md) med `--include-path` alternativet . Avgränsa enskilda blobnamn med hjälp av semikolon ( `;` ).

**Syntax**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive
```

I det här exemplet överför AzCopy `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalogen och `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` filen. Inkludera alternativet `--recursive` för att överföra alla blobar i `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalogen.

Du kan också undanta blobar med hjälp av `--exclude-path` alternativet . Mer information finns i [referensdokumenten för azcopy-kopiering.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Använda jokertecken

Använd [kommandot azcopy copy](storage-ref-azcopy-copy.md) med `--include-pattern` alternativet . Ange partiella namn som innehåller jokertecken. Avgränsa namn med hjälp av semikolon ( `;` ).

**Syntax**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

Du kan också undanta blobar med hjälp av `--exclude-pattern` alternativet . Mer information finns i [azcopy copy](storage-ref-azcopy-copy.md) reference docs.

Alternativen `--include-pattern` och gäller endast för `--exclude-pattern` blobnamn och inte sökvägen.  Om du vill kopiera alla textfiler (blobar) som finns i ett katalogträd använder du alternativet för att hämta hela katalogträdet och använder sedan och anger för att hämta alla `–recursive` `–include-pattern` `*.txt` textfiler.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>Ladda ned blobar som har ändrats före eller efter ett datum och en tid 

Använd kommandot [azcopy copy](storage-ref-azcopy-copy.md) med `--include-before` alternativet eller `--include-after` . Ange ett datum och en tid i ISO-8601-format (till exempel: `2020-08-19T15:04:00Z` ). 

I följande exempel hämtas filer som ändrades den eller efter det angivna datumet.

**Syntax**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

Detaljerad referens finns i [referensdokumenten för azcopy](storage-ref-azcopy-copy.md) copy.

#### <a name="download-previous-versions-of-a-blob"></a>Ladda ned tidigare versioner av en blob

Om du har aktiverat [blobversionshantering](../blobs/versioning-enable.md)kan du ladda ned en eller flera tidigare versioner av en blob. 

Skapa först en textfil som innehåller en lista med [versions-ID:er](../blobs/versioning-overview.md). Varje versions-ID måste visas på en separat rad. Exempel: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Använd sedan kommandot [azcopy copy](storage-ref-azcopy-copy.md) med `--list-of-versions` alternativet . Ange platsen för textfilen som innehåller listan över versioner (till exempel: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Ladda ned en blobögonblicksbild

Du kan ladda ned en [blobögonblicksbild](../blobs/snapshots-overview.md) genom att referera till **DateTime-värdet** för en blobögonblicksbild. 

**Syntax**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'`

**Exempel**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> Om du använder en SAS-token för att auktorisera åtkomst till blobdata lägger du till Snapshot **DateTime** efter SAS-token. Exempel: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Ladda ned med valfria flaggor

Du kan justera nedladdningsåtgärden med hjälp av valfria flaggor. Här är några exempel.

|Scenario|Flagga|
|---|---|
|Dekomprimera filer automatiskt.|**--dekomprimera**|
|Ange hur detaljerad du vill att dina kopieringsrelaterade loggposter ska vara.|**--log-level** = \[ INFORMATION \| OM \| VARNINGSFEL \| INGEN\]|
|Ange om och hur du skriver över de filer och blobar som står i konflikt på målet.|**--overwrite** = \[ true \| false \| ifSourceNewer prompt \|\]|

En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i de här artiklarna:

- [Exempel: Ladda upp](storage-use-azcopy-blobs-upload.md)
- [Exempel: Kopiera mellan konton](storage-use-azcopy-blobs-copy.md)
- [Exempel: Synkronisera](storage-use-azcopy-blobs-synchronize.md)
- [Exempel: Amazon S3-bucketar](storage-use-azcopy-s3.md)
- [Exempel: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Exempel: Azure Files](storage-use-azcopy-files.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Se de här artiklarna för att konfigurera inställningar, optimera prestanda och felsöka problem:

- [AzCopy-konfigurationsinställningar](storage-ref-azcopy-configuration-settings.md)
- [Optimera Prestanda för AzCopy](storage-use-azcopy-optimize.md)
- [Felsöka AzCopy V10-problem i Azure Storage med hjälp av loggfiler](storage-use-azcopy-configure.md)
