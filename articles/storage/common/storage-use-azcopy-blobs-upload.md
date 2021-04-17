---
title: Ladda upp filer till Azure Blob Storage med hjälp av AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling AzCopy-exempelkommandon som hjälper dig att ladda upp filer till Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 352497f0f4d23250abe9f84121f358589664002b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502920"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy"></a>Ladda upp filer till Azure Blob Storage med hjälp av AzCopy

Du kan ladda upp filer och kataloger till Blob Storage med hjälp av kommandoradsverktyget AzCopy v10. 

Exempel på andra typer av uppgifter, till exempel att ladda ned blobar, synkronisera med Blob Storage eller [](#next-steps) kopiera blobar mellan konton, finns i länkarna i avsnittet Nästa steg i den här artikeln.

## <a name="get-started"></a>Kom igång

I artikeln [Kom igång med AzCopy](storage-use-azcopy-v10.md) kan du ladda ned AzCopy och lära dig mer om hur du kan ange autentiseringsuppgifter för lagringstjänsten.

> [!NOTE] 
> Exemplen i den här artikeln förutsätter att du har angett autentiseringsuppgifter för auktorisering med hjälp av Azure Active Directory (Azure AD).
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till blobdata kan du lägga till denna token i resurs-URL:en i varje AzCopy-kommando. Exempel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Skapa en container

Du kan använda kommandot [azcopy make](storage-ref-azcopy-make.md) för att skapa en container.

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe), omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

Det här är ett tabellexempel:

**Syntax**

`azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'`

**Exempel**

```azcopy
https://mystorageaccount.blob.core.windows.net/mycontainer
```

**Exempel (hierarkisk namnrymd)**

```azcopy
https://mystorageaccount.dfs.core.windows.net/mycontainer
```

Detaljerade referensdokument finns i [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-a-file"></a>Ladda upp en fil

Ladda upp en fil med hjälp av [kommandot azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

**Syntax**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'`

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'
```

Du kan också ladda upp en fil med ett jokertecken (*) var som helst i sökvägen eller filnamnet. Till exempel: `'C:\myDirectory\*.txt'` , eller `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Ladda upp en katalog

Ladda upp en katalog med hjälp av [kommandot azcopy copy.](storage-ref-azcopy-copy.md) 

I det här exemplet kopieras en katalog (och alla filer i katalogen) till en blobcontainer. Resultatet är en katalog i containern med samma namn.

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ('').

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive`

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive
```

Om du vill kopiera till en katalog i containern anger du bara namnet på katalogen i kommandosträngen.

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

Om du anger namnet på en katalog som inte finns i containern skapar AzCopy en ny katalog med det namnet.

## <a name="upload-directory-contents"></a>Ladda upp kataloginnehåll

Ladda upp innehållet i en katalog med hjälp av [kommandot azcopy copy.](storage-ref-azcopy-copy.md) Använd jokertecknet (*) för att ladda upp innehållet utan att kopiera den innehållande katalogen.

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ('').

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` 

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'
```

Lägg till flaggan `--recursive` för att ladda upp filer i alla underkataloger.

## <a name="upload-specific-files"></a>Ladda upp specifika filer

Du kan ladda upp specifika filer med fullständiga filnamn, partiella namn med jokertecken (*), eller genom att använda datum och tider.

> [!TIP]
> De här exemplen omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt förutom Windows Command Shell (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

### <a name="specify-multiple-complete-file-names"></a>Ange flera fullständiga filnamn

Använd [kommandot azcopy copy](storage-ref-azcopy-copy.md) med `--include-path` alternativet . Avgränsa enskilda filnamn med semikolon ( `;` ).

**Syntax** 

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` 

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

I det här exemplet överför AzCopy `C:\myDirectory\photos` katalogen och `C:\myDirectory\documents\myFile.txt` filen. Inkludera alternativet `--recursive` för att överföra alla filer i `C:\myDirectory\photos` katalogen.

Du kan också undanta filer med hjälp av `--exclude-path` alternativet . Mer information finns i [azcopy copy](storage-ref-azcopy-copy.md) reference docs.

### <a name="use-wildcard-characters"></a>Använda jokertecken

Använd [kommandot azcopy copy](storage-ref-azcopy-copy.md) med `--include-pattern` alternativet . Ange partiella namn som innehåller jokertecken. Avgränsa namn med hjälp av semikolon ( `;` ). 

**Syntax**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` 

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

Du kan också undanta filer med hjälp av `--exclude-pattern` alternativet . Mer information finns i [referensdokumenten för azcopy-kopiering.](storage-ref-azcopy-copy.md)

Alternativen `--include-pattern` och gäller endast för filnamn och inte för `--exclude-pattern` sökvägen.  Om du vill kopiera alla textfiler som finns i ett katalogträd använder du alternativet för att hämta hela katalogträdet och använder sedan och anger för att hämta alla `–recursive` `–include-pattern` `*.txt` textfiler.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>Ladda upp filer som har ändrats före eller efter ett datum och en tidpunkt 

Använd kommandot [azcopy copy](storage-ref-azcopy-copy.md) med `--include-before` alternativet eller `--include-after` . Ange ett datum och en tid i ISO-8601-format (till exempel: `2020-08-19T15:04:00Z` ). 

I följande exempel laddas filer som ändrades på eller efter det angivna datumet upp.

**Syntax**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` 

**Exempel**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Exempel (hierarkisk namnrymd)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'
```

Detaljerad referens finns i [referensdokumenten för azcopy](storage-ref-azcopy-copy.md) copy.

## <a name="upload-with-index-tags"></a>Ladda upp med indextaggar

Du kan ladda upp en fil och lägga [till blobindextaggar (förhandsversion)](../blobs/storage-manage-find-blobs.md) till målbloben.  

Om du använder Azure AD-auktorisering måste säkerhetsobjekt tilldelas rollen Storage [Blob Data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) eller ges behörighet till Azure-resursprovideråtgärden via en anpassad `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) Azure-roll. Om du använder en SAS-token (signatur för delad åtkomst) måste denna token ge åtkomst till blobens taggar via `t` SAS-behörigheten.

Om du vill lägga till taggar `--blob-tags` använder du alternativet tillsammans med ett URL-kodat nyckel/värde-par. Om du till exempel vill lägga till `my tag` nyckeln och ett värde lägger du till i `my tag value` `--blob-tags='my%20tag=my%20tag%20value'` målparametern. 

Avgränsa flera indextaggar med ett et-nummer ( `&` ).  Om du till exempel vill lägga till en nyckel `my second tag` och ett värde blir den fullständiga `my second tag value` alternativsträngen `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

I följande exempel visas hur du använder `--blob-tags` alternativet .

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe), omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

**Ladda upp en fil**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Ladda upp en katalog**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Ladda upp kataloginnehåll**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

> [!NOTE]
> Om du anger en katalog för källan har alla blobar som kopieras till målet samma taggar som du anger i kommandot.

## <a name="upload-with-optional-flags"></a>Ladda upp med valfria flaggor

Du kan justera uppladdningsåtgärden med hjälp av valfria flaggor. Här är några exempel.

|Scenario|Flagga|
|---|---|
|Ladda upp filer som bifogade blobar eller sidblobar.|**--blob-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Ladda upp till en viss åtkomstnivå (till exempel arkivnivå).|**--block-blob-tier** = \[ Inget \| hot \| \| cool-arkiv\]|

En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i de här artiklarna:

- [Exempel: Ladda ned](storage-use-azcopy-blobs-download.md)
- [Exempel: Kopiera mellan konton](storage-use-azcopy-blobs-copy.md)
- [Exempel: Synkronisera](storage-use-azcopy-blobs-synchronize.md)
- [Exempel: Amazon S3-bucketar](storage-use-azcopy-s3.md)
- [Exempel: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Exempel: Azure Files](storage-use-azcopy-files.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Se de här artiklarna för att konfigurera inställningar, optimera prestanda och felsöka problem:

- [AzCopy-konfigurationsinställningar](storage-ref-azcopy-configuration-settings.md)
- [Optimera prestanda för AzCopy](storage-use-azcopy-optimize.md)
- [Felsöka Problem med AzCopy V10 i Azure Storage med hjälp av loggfiler](storage-use-azcopy-configure.md)