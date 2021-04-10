---
title: Ladda upp filer till Azure Blob Storage med hjälp av AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling av AzCopy-exempel kommandon som hjälper dig att ladda upp filer till Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 7cdc10720b6fa93cf1893d2040fd1c1f3e9e1f02
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728870"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy-v10"></a>Ladda upp filer till Azure Blob Storage med hjälp av AzCopy v10

Du kan ladda upp filer och kataloger till Blob Storage med hjälp av kommando rads verktyget AzCopy v10. 

För att se exempel för andra typer av uppgifter som att hämta blobbar, synkronisera med Blob Storage eller kopiera blobbar mellan konton, se länkarna som visas i avsnittet [Nästa steg](#next-steps) i den här artikeln.

## <a name="get-started"></a>Kom igång

Se artikeln [Kom igång med AZCopy](storage-use-azcopy-v10.md) för att ladda ned AzCopy och Läs om hur du kan ange autentiseringsuppgifter för lagrings tjänsten.

> [!NOTE] 
> I exemplen i den här artikeln förutsätter vi att du har angett autentiseringsuppgifter för auktorisering genom att använda Azure Active Directory (Azure AD).
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till BLOB-data kan du lägga till denna token i resurs-URL: en i varje AzCopy-kommando. Exempel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Skapa en container

Du kan använda kommandot [AzCopy make](storage-ref-azcopy-make.md) för att skapa en behållare.

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

| Syntax/exempel | Kod |
|--------|-----------|
| **Syntax** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Exempel** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Exempel** (hierarkiskt namn område) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

För detaljerade referens dokument, se [AzCopy-fabrikat](storage-ref-azcopy-make.md).

## <a name="upload-a-file"></a>Ladda upp en fil

Ladda upp en fil med hjälp av kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Exempel** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Du kan också ladda upp en fil med en jokertecken (*) var som helst i fil Sök vägen eller fil namnet. Till exempel: `'C:\myDirectory\*.txt'` , eller `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Ladda upp en katalog

Ladda upp en katalog med hjälp av kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) . 

I det här exemplet kopieras en katalog (och alla filer i katalogen) till en BLOB-behållare. Resultatet är en katalog i behållaren med samma namn.

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Om du vill kopiera till en katalog i behållaren anger du bara namnet på den katalogen i kommando strängen.

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Om du anger namnet på en katalog som inte finns i behållaren, skapar AzCopy en ny katalog med det namnet.

## <a name="upload-directory-contents"></a>Ladda upp katalog innehåll

Ladda upp innehållet i en katalog med hjälp av kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) . Använd jokertecknet (*) för att överföra innehållet utan att kopiera den innehåller själva katalogen.

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Exempel** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |


Lägg till `--recursive` flaggan för att ladda upp filer i alla under kataloger.

## <a name="upload-specific-files"></a>Ladda upp vissa filer

Du kan ladda upp vissa filer genom att använda fullständiga fil namn, partiella namn med jokertecken (*) eller med hjälp av datum och tider.

> [!TIP]
> Dessa exempel omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga fil namn

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-path` alternativet. Separera enskilda fil namn genom att använda semikolon ( `;` ).

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

I det här exemplet överför AzCopy `C:\myDirectory\photos` katalogen och `C:\myDirectory\documents\myFile.txt` filen. Inkludera `--recursive` alternativet för att överföra alla filer i `C:\myDirectory\photos` katalogen.

Du kan också utesluta filer med hjälp av `--exclude-path` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

### <a name="use-wildcard-characters"></a>Använd jokertecken

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-pattern` alternativet. Ange partiella namn som innehåller jokertecken. Separera namn med hjälp av en semicolin ( `;` ). 

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exempel** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Du kan också utesluta filer med hjälp av `--exclude-pattern` alternativet. Mer information finns i referens dokument för [AzCopy Copy](storage-ref-azcopy-copy.md) .

`--include-pattern`Alternativen och `--exclude-pattern` gäller endast för fil namn och inte till sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalog träd, använder du `–recursive` alternativet för att hämta hela katalog trädet och använder sedan `–include-pattern` och anger `*.txt` för att hämta alla textfiler.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>Ladda upp filer som har ändrats före eller efter ett datum och en tid 

Använd [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot med `--include-before` alternativet eller `--include-after` . Ange ett datum och en tid i ISO-8601-format (till exempel: `2020-08-19T15:04:00Z` ). 

I följande exempel överförs filer som ändrades på eller efter det angivna datumet.

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Exempel** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Mer detaljerad information finns i referens dokument för [AzCopy-kopiering](storage-ref-azcopy-copy.md) .

## <a name="upload-with-index-tags"></a>Ladda upp med index Taggar

Du kan ladda upp en fil och lägga till [taggar för BLOB-index (för hands version)](../blobs/storage-manage-find-blobs.md) till mål-bloben.  

Om du använder Azure AD-auktorisering måste ditt säkerhets objekt tilldelas rollen som [ägare av lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) eller så måste den ges behörighet till `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure Resource Provider-åtgärden](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) via en anpassad Azure-roll. Om du använder en SAS-token (signatur för delad åtkomst) måste denna token ge åtkomst till blobens Taggar via `t` SAS-behörigheten.

Om du vill lägga till taggar använder du `--blob-tags` alternativet tillsammans med ett URL-kodat nyckel/värde-par. Om du till exempel vill lägga till nyckeln `my tag` och ett värde `my tag value` lägger du till `--blob-tags='my%20tag=my%20tag%20value'` mål parametern. 

Avgränsa flera index-Taggar med hjälp av ett et-tecken ( `&` ).  Om du till exempel vill lägga till en nyckel `my second tag` och ett värde blir `my second tag value` den fullständiga alternativ strängen `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

I följande exempel visas hur du använder `--blob-tags` alternativet.

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Ladda upp en fil** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Ladda upp en katalog** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **Ladda upp katalog innehåll** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> Om du anger en katalog för källan, kommer alla blobar som kopieras till målet att ha samma taggar som du anger i kommandot.

## <a name="upload-with-optional-flags"></a>Ladda upp med valfria flaggor

Du kan ändra uppladdnings åtgärden med valfria flaggor. Här är några exempel.

|Scenario|Flagga|
|---|---|
|Ladda upp filer som bifogade blobar eller sidblobar.|**--BLOB-Type** = \[ BlockBlob \| PageBlob- \| AppendBlob\]|
|Ladda upp till en viss åtkomstnivå (till exempel arkivnivå).|**--Block-Blob-Tier** = \[ Inget \| varmt \| coolt \| Arkiv\]|

En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i de här artiklarna:

- [Exempel: Ladda ned](storage-use-azcopy-blobs-download.md)
- [Exempel: Kopiera mellan konton](storage-use-azcopy-blobs-copy.md)
- [Exempel: Synkronisera](storage-use-azcopy-blobs-synchronize.md)
- [Exempel: Amazon S3-bucketar](storage-use-azcopy-s3.md)
- [Exempel: Azure Files](storage-use-azcopy-files.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)