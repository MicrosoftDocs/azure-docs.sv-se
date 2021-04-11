---
title: Synkronisera med Azure Blob Storage med hjälp av AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling av AzCopy-exempel kommandon som hjälper dig att synkronisera med Azure Blob Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ec341243811eaa271511baba04ea1c48a4fefdab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728903"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>Synkronisera med Azure Blob Storage med hjälp av AzCopy v10

Du kan synkronisera lokal lagring med Azure Blob Storage med hjälp av kommando rads verktyget AzCopy v10. 

Du kan synkronisera innehållet i ett lokalt fil system med en BLOB-behållare. Du kan också synkronisera behållare och virtuella kataloger med varandra. Synkronisering är ett sätt. Med andra ord kan du välja vilken av dessa två slut punkter som är källan och vilken som är målet. Synkronisering använder också server-till-Server-API: er. Exemplen som presenteras i det här avsnittet fungerar också med konton som har ett hierarkiskt namn område. 

> [!NOTE]
> Den aktuella versionen av AzCopy synkroniseras inte mellan andra källor och destinationer (till exempel: File Storage eller Amazon Web Services (AWS) S3-buckets).

Se de länkar som visas i avsnittet [Nästa steg](#next-steps) i den här artikeln om du vill se exempel på andra typer av uppgifter, till exempel överföra filer, Hämta blobbar eller kopiera blobbar mellan konton.

## <a name="get-started"></a>Kom igång

Se artikeln [Kom igång med AZCopy](storage-use-azcopy-v10.md) för att ladda ned AzCopy och Läs om hur du kan ange autentiseringsuppgifter för lagrings tjänsten.

> [!NOTE] 
> I exemplen i den här artikeln förutsätter vi att du har angett autentiseringsuppgifter för auktorisering genom att använda Azure Active Directory (Azure AD).
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till BLOB-data kan du lägga till denna token i resurs-URL: en i varje AzCopy-kommando. Exempel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Riktlinjer

- Kommandot [Sync](storage-ref-azcopy-sync.md) jämför fil namn och senaste ändrade tidsstämplar. Ange `--delete-destination` värdet för valfri flagga `true` eller `prompt` ta bort filer i mål katalogen om filerna inte längre finns i käll katalogen.

- Om du ställer in `--delete-destination` flaggan på `true` , tar AzCopy bort filer utan att ange någon prompt. Om du vill att en prompt ska visas innan AzCopy tar bort en fil, ställer du in `--delete-destination` flaggan på `prompt` .

- För att förhindra oavsiktliga borttagningar, se till att aktivera funktionen för [mjuk borttagning](../blobs/soft-delete-blob-overview.md) innan du använder `--delete-destination=prompt|true` flaggan.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Uppdatera en behållare med ändringar i ett lokalt fil system

I det här fallet är containern målet och det lokala filsystemet är källan. 

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Syntax** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Uppdatera ett lokalt filsystem med ändringar i en container

I det här fallet är det lokala fil systemet målet och behållaren är källan.

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Exempel** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>Uppdatera en behållare med ändringar i en annan behållare

Den första behållaren som visas i det här kommandot är källan. Den andra är målet.

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Uppdatera en katalog med ändringar i en katalog i en annan behållare

Den första katalogen som visas i det här kommandot är källan. Den andra är målet.

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

| Syntax/exempel  |  Kod |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Exempel** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>Synkronisera med valfria flaggor

Du kan anpassa din synkronisering genom att använda valfria flaggor. Här är några exempel.

|Scenario|Flagga|
|---|---|
|Ange hur strikta MD5-hashar ska verifieras vid hämtning.|**--kontrol lera-MD5** = \[ Nocheck \| \| FailIfDifferent \| FailIfDifferentOrMissing\]|
|Undanta filer baserat på ett mönster.|**--Exkludera-sökväg**|
|Ange hur detaljerad du vill att dina Sync-relaterade logg poster ska vara.|**--inloggnings nivå** = \[ VARNINGs \| fel \| information \| ingen\]|

En fullständig lista finns i [alternativ](storage-ref-azcopy-sync.md#options).

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i de här artiklarna:

- [Exempel: Ladda upp](storage-use-azcopy-blobs-upload.md)
- [Exempel: Ladda ned](storage-use-azcopy-blobs-download.md)
- [Exempel: Kopiera mellan konton](storage-use-azcopy-blobs-copy.md)
- [Exempel: Amazon S3-bucketar](storage-use-azcopy-s3.md)
- [Exempel: Azure Files](storage-use-azcopy-files.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)