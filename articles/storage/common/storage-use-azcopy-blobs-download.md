---
title: Ladda ned blobbar från Azure Blob Storage med hjälp av AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling av AzCopy-exempel kommandon som hjälper dig att ladda ned blobbar från Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 20da05399eed4cb9c5a4b69a82b0b1e799997751
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880126"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>Ladda ned blobbar från Azure Blob Storage med hjälp av AzCopy v10

Du kan ladda ned blobbar och kataloger från Blob Storage med hjälp av kommando rads verktyget AzCopy v10. 

Se de länkar som visas i avsnittet [Nästa steg](#next-steps) i den här artikeln om du vill se exempel för andra typer av uppgifter som att ladda upp filer, synkronisera med blob-lagring eller kopiera blobbar mellan konton.

## <a name="get-started"></a>Kom igång

Se artikeln [Kom igång med AZCopy](storage-use-azcopy-v10.md) för att ladda ned AzCopy och Läs om hur du kan ange autentiseringsuppgifter för lagrings tjänsten.

> [!NOTE] 
> I exemplen i den här artikeln förutsätter vi att du har angett autentiseringsuppgifter för auktorisering genom att använda Azure Active Directory (Azure AD).
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till BLOB-data kan du lägga till denna token i resurs-URL: en i varje AzCopy-kommando. Exempel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Ladda ned en blob

Hämta en blob med hjälp av kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Om `Content-md5` egenskap svärdet för en BLOB innehåller en hash beräknar AzCopy en MD5-hash för hämtade data och verifierar att MD5-hashen som lagras i blobens `Content-md5` egenskap matchar den beräknade hashen. Om dessa värden inte matchar, Miss lyckas nedladdningen om du inte åsidosätter det här beteendet genom att lägga till `--check-md5=NoCheck` eller `--check-md5=LogOnly` till kommandot Kopiera.

## <a name="download-a-directory"></a>Ladda ned en katalog

Ladda ned en katalog med hjälp av kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Det här exemplet resulterar i en katalog med namnet `C:\myDirectory\myBlobDirectory` som innehåller alla hämtade blobbar.

## <a name="download-directory-contents"></a>Hämta katalog innehåll

Du kan ladda ned innehållet i en katalog utan att kopiera själva katalogen med hjälp av jokertecknet (*).

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

> [!NOTE]
> För närvarande stöds det här scenariot endast för konton som inte har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

Lägg till `--recursive` flaggan för att ladda ned filer i alla under kataloger.

## <a name="download-specific-blobs"></a>Hämta vissa blobbar

Du kan hämta vissa blobbar genom att använda fullständiga fil namn, partiella namn med jokertecken (*) eller med hjälp av datum och tider. 

> [!TIP]
> Dessa exempel omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

#### <a name="specify-multiple-complete-blob-names"></a>Ange flera fullständiga BLOB-namn

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-path` alternativet. Separera enskilda BLOB-namn med hjälp av en semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

I det här exemplet överför AzCopy `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalogen och `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` filen. Inkludera `--recursive` alternativet för att överföra alla blobbar i `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` katalogen.

Du kan också utesluta blobbar med hjälp av `--exclude-path` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Använd jokertecken

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-pattern` alternativet. Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av en semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta blobbar med hjälp av `--exclude-pattern` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

`--include-pattern`Alternativen och `--exclude-pattern` gäller endast för BLOB-namn och inte sökvägen.  Om du vill kopiera alla textfiler (blobbar) som finns i ett katalog träd, använder du `–recursive` alternativet för att hämta hela katalog trädet och använder sedan `–include-pattern` och anger `*.txt` för att hämta alla textfiler.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>Ladda ned blobbar som ändrades före eller efter ett datum och en tid 

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-before` alternativet eller `--include-after` . Ange ett datum och en tid i ISO-8601-format (till exempel: `2020-08-19T15:04:00Z` ). 

I följande exempel hämtas filer som ändrades på eller efter det angivna datumet.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Mer detaljerad information finns i referens dokument för [AzCopy-kopiering](storage-ref-azcopy-copy.md) .

#### <a name="download-previous-versions-of-a-blob"></a>Hämta tidigare versioner av en BLOB

Om du har aktiverat [BLOB-versioner](../blobs/versioning-enable.md)kan du ladda ned en eller flera tidigare versioner av en blob. 

Skapa först en textfil som innehåller en lista med [versions-ID: n](../blobs/versioning-overview.md). Varje versions-ID måste visas på en separat rad. Exempel: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Använd sedan kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) med `--list-of-versions` alternativet. Ange platsen för text filen som innehåller listan över versioner (till exempel: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Ladda ned en BLOB-ögonblicksbild

Du kan ladda ned en [BLOB-ögonblicksbild](../blobs/snapshots-overview.md) genom att referera till **datetime** -värdet för en BLOB-ögonblicksbild. 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'` |
| **Exempel** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Om du använder en SAS-token för att auktorisera åtkomst till BLOB-data ska du lägga till **datetime-datum** efter SAS-token. Exempel: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Hämta med valfria flaggor

Du kan anpassa nedladdnings åtgärden genom att använda valfria flaggor. Här är några exempel.

|Scenario|Flagga|
|---|---|
|Expandera filer automatiskt.|**--Decompress**|
|Ange hur detaljerad du vill att dina kopie-relaterade logg poster ska vara.|**--inloggnings nivå** = \[ VARNINGs \| fel \| information \| ingen\]|
|Ange om och hur de konfliktskapande filerna och Blobbarna ska skrivas över vid målet.|**--Skriv över** = \[ Sant \| falskt \| ifSourceNewer- \| prompt\]|

En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i de här artiklarna:

- [Exempel: Ladda upp](storage-use-azcopy-blobs-upload.md)
- [Exempel: kopiera mellan konto](storage-use-azcopy-blobs-copy.md)
- [Exempel: Synkronisera](storage-use-azcopy-blobs-synchronize.md)
- [Exempel: Amazon S3-bucketar](storage-use-azcopy-s3.md)
- [Exempel: Azure Files](storage-use-azcopy-files.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)