---
title: Synkronisera med Azure Blob Storage med hjälp av AzCopy v10-| Microsoft Docs
description: Den här artikeln innehåller en samling AzCopy-exempelkommandon som hjälper dig att synkronisera med Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8b3340c00d856b13edefc7728d5baa327399a441
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502937"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy"></a>Synkronisera med Azure Blob Storage med hjälp av AzCopy

Du kan synkronisera lokal lagring med Azure Blob Storage med hjälp av kommandoradsverktyget AzCopy v10. 

Du kan synkronisera innehållet i ett lokalt filsystem med en blobcontainer. Du kan också synkronisera containrar och virtuella kataloger med varandra. Synkronisering är ett sätt. Med andra ord väljer du vilken av dessa två slutpunkter som är källan och vilken som är målet. Synkroniseringen använder också server-till-server-API:er. Exemplen som visas i det här avsnittet fungerar också med konton som har en hierarkisk namnrymd. 

> [!NOTE]
> Den aktuella versionen av AzCopy synkroniserar inte mellan andra källor och mål (till exempel: File Storage eller Amazon Web Services (AWS) S3-buckets).

Om du vill se exempel på andra typer av uppgifter, till exempel att ladda upp filer, [](#next-steps) ladda ned blobar eller kopiera blobar mellan konton, kan du läsa länkarna som visas i avsnittet Nästa steg i den här artikeln.

## <a name="get-started"></a>Kom igång

I artikeln [Kom igång med AzCopy kan](storage-use-azcopy-v10.md) du ladda ned AzCopy och lära dig mer om hur du kan ange autentiseringsuppgifter för auktorisering till lagringstjänsten.

> [!NOTE] 
> Exemplen i den här artikeln förutsätter att du har angett autentiseringsuppgifter för auktorisering med hjälp Azure Active Directory (Azure AD).
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till blobdata kan du lägga till denna token i resurs-URL:en i varje AzCopy-kommando. Exempel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Riktlinjer

- [Synkroniseringskommandot](storage-ref-azcopy-sync.md) jämför filnamn och senast ändrade tidsstämplar. Ange den valfria flaggan till värdet eller för att ta bort filer i målkatalogen om dessa `--delete-destination` filer inte längre finns i `true` `prompt` källkatalogen.

- Om du anger flaggan `--delete-destination` till `true` tar AzCopy bort filer utan att ange någon uppmaning. Om du vill att en uppmaning ska visas innan AzCopy tar bort en fil anger du `--delete-destination` flaggan till `prompt` .

- Om du planerar att ange flaggan till eller bör du överväga att använda kopieringskommandot i stället för synkroniseringskommandot `--delete-destination` `prompt` och ange `false` [](storage-ref-azcopy-copy.md) [](storage-ref-azcopy-sync.md) `--overwrite` parametern till `ifSourceNewer` . [Kopieringskommandot](storage-ref-azcopy-copy.md) förbrukar mindre minne och medför lägre faktureringskostnader eftersom en kopieringsåtgärd inte behöver indexera källan eller målet innan filerna flyttas. 

- Om du vill förhindra oavsiktliga borttagningar måste du aktivera [funktionen för mjuk](../blobs/soft-delete-blob-overview.md) borttagning innan du använder flaggan `--delete-destination=prompt|true` .

- Datorn där du kör synkroniseringskommandot bör ha en korrekt systemklocka eftersom de senaste ändringstiderna är viktiga för att avgöra om en fil ska överföras. Om systemet har en betydande klockförsnedring undviker du att ändra filer vid målet för nära den tid som du planerar att köra ett synkroniseringskommando.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Uppdatera en container med ändringar i ett lokalt filsystem

I det här fallet är containern målet och det lokala filsystemet är källan. 

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

**Syntax**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Exempel**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Uppdatera ett lokalt filsystem med ändringar i en container

I det här fallet är det lokala filsystemet målet och containern är källan.

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt förutom Windows Command Shell (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

**Syntax**

`azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive`

**Exempel**

```azcopy
azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive
```

## <a name="update-a-container-with-changes-in-another-container"></a>Uppdatera en container med ändringar i en annan container

Den första containern som visas i det här kommandot är källan. Den andra är målet.

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt förutom Windows Command Shell (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe), omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Exempel**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Uppdatera en katalog med ändringar i en katalog i en annan container

Den första katalogen som visas i det här kommandot är källan. Det andra är målet.

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ('').

**Syntax**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive`

**Exempel**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive
```

## <a name="synchronize-with-optional-flags"></a>Synkronisera med valfria flaggor

Du kan justera synkroniseringsåtgärden med hjälp av valfria flaggor. Här är några exempel.

|Scenario|Flagga|
|---|---|
|Ange hur strikt MD5-hash-värden ska verifieras vid nedladdning.|**--check-md5** = \[ NoCheck \| LogOnly \| FailIfDifferent \| FailIfDifferentOrMissing\]|
|Undanta filer baserat på ett mönster.|**--exclude-path**|
|Ange hur detaljerade du vill att dina synkroniseringsrelaterade loggposter ska vara.|**--log-level** = \[ INFORMATION \| OM \| VARNINGSFEL \| INGEN\]|

En fullständig lista över flaggor finns i [alternativ](storage-ref-azcopy-sync.md#options).

> [!NOTE]
> Flaggan `--recursive` är inställd på som `true` standard. Flaggorna `--exclude-pattern` och gäller endast för filnamn och inte andra delar av `--include-pattern` sökvägen. 

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i de här artiklarna:

- [Exempel: Ladda upp](storage-use-azcopy-blobs-upload.md)
- [Exempel: Ladda ned](storage-use-azcopy-blobs-download.md)
- [Exempel: Kopiera mellan konton](storage-use-azcopy-blobs-copy.md)
- [Exempel: Amazon S3-bucketar](storage-use-azcopy-s3.md)
- [Exempel: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Exempel: Azure Files](storage-use-azcopy-files.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Se de här artiklarna för att konfigurera inställningar, optimera prestanda och felsöka problem:

- [AzCopy-konfigurationsinställningar](storage-ref-azcopy-configuration-settings.md)
- [Optimera prestanda för AzCopy](storage-use-azcopy-optimize.md)
- [Felsöka Problem med AzCopy V10 i Azure Storage med hjälp av loggfiler](storage-use-azcopy-configure.md)

