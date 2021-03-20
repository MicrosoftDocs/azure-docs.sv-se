---
title: Kopiera blobbar mellan Azure Storage-konton med AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling av AzCopy-exempel kommandon som hjälper dig att kopiera blobbar mellan lagrings konton.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 524e3f70f9588cfae2c739722fc1a44e683f9a7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97617297"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>Kopiera blobbar mellan Azure Storage-konton med hjälp av AzCopy v10

Du kan kopiera blobbar, kataloger och behållare mellan lagrings konton med hjälp av kommando rads verktyget AzCopy v10. 

Se de länkar som visas i avsnittet [Nästa steg](#next-steps) i den här artikeln om du vill se exempel på andra typer av uppgifter, till exempel överföra filer, Hämta blobbar och synkronisera med Blob Storage.

AzCopy använder [Server-till-Server-](/rest/api/storageservices/put-block-from-url) [API: er](/rest/api/storageservices/put-page-from-url), så data kopieras direkt mellan lagrings servrar. Dessa kopierings åtgärder använder inte datorns nätverks bandbredd.

Information om hur du hämtar AzCopy och lär dig hur du kan ange autentiseringsuppgifter för lagrings tjänsten finns i [Kom igång med AZCopy](storage-use-azcopy-v10.md). 

## <a name="guidelines"></a>Riktlinjer

Använd följande rikt linjer för dina AzCopy-kommandon. 

- Klienten måste ha nätverks åtkomst till både käll-och mål lagrings kontona. Information om hur du konfigurerar nätverks inställningar för varje lagrings konto finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](storage-network-security.md?toc=/azure/storage/blobs/toc.json).

- Lägg till en SAS-token till varje käll-URL. 

  Om du anger autentiseringsuppgifter för auktorisering genom att använda Azure Active Directory (Azure AD) kan du utelämna SAS-token endast från mål-URL: en. Kontrol lera att du har konfigurerat rätt roller i ditt mål konto. Se [alternativ 1: använd Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  I exemplen i den här artikeln förutsätter vi att du har autentiserat din identitet med hjälp av Azure AD, så att exemplen utelämnar SAS-token från mål-URL: en.

-  Om du kopierar till ett Premium Block Blob Storage-konto utelämnar du åtkomst nivån för en BLOB från kopierings åtgärden genom att ställa in `s2s-preserve-access-tier` till `false` (till exempel: `--s2s-preserve-access-tier=false` ). Premium Block-Blob Storage-konton stöder inte åtkomst nivåer. 

- Om du kopierar till eller från ett konto som har ett hierarkiskt namn område, använder du i `blob.core.windows.net` stället för `dfs.core.windows.net` i URL-syntaxen. [Med åtkomst med flera protokoll på data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) kan du använda `blob.core.windows.net` , och det är den enda syntax som stöds för konto kopierings scenarier. 

- Du kan öka data flödet för kopierings åtgärder genom att ange värdet för `AZCOPY_CONCURRENCY_VALUE` miljö variabeln. Mer information finns i [optimera data flöde](storage-use-azcopy-configure.md#optimize-throughput). 

- Om käll-blobarna har index-Taggar och du vill behålla dessa taggar måste du tillämpa dem igen på mål-blobarna. Information om hur du ställer in index taggar finns i avsnittet [Kopiera blobbar till ett annat lagrings konto med index Taggar](#copy-between-accounts-and-add-index-tags) i den här artikeln.

## <a name="copy-a-blob"></a>Kopiera en BLOB

Kopiera en blob till ett annat lagrings konto med hjälp av kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats. 

## <a name="copy-a-directory"></a>Kopiera en katalog

Kopiera en katalog till ett annat lagrings konto med hjälp av kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats.

## <a name="copy-a-container"></a>Kopiera en behållare

Kopiera en behållare till ett annat lagrings konto med hjälp av kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats.

## <a name="copy-containers-directories-and-blobs"></a>Kopiera behållare, kataloger och blobbar

Kopiera alla behållare, kataloger och blobbar till ett annat lagrings konto med hjälp av kommandot [AzCopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Exempel** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Kopiera blobbar och Lägg till index Taggar

Kopiera blobbar till ett annat lagrings konto och Lägg till [BLOB-Taggar (för hands version)](../blobs/storage-manage-find-blobs.md) till mål-bloben.

Om du använder Azure AD-auktorisering måste ditt säkerhets objekt tilldelas rollen som [ägare av lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) eller så måste den ges behörighet till `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure Resource Provider-åtgärden](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) via en anpassad Azure-roll. Om du använder en SAS-token (signatur för delad åtkomst) måste denna token ge åtkomst till blobens Taggar via `t` SAS-behörigheten.

Om du vill lägga till taggar använder du `--blob-tags` alternativet tillsammans med ett URL-kodat nyckel/värde-par. 

Om du till exempel vill lägga till nyckeln `my tag` och ett värde `my tag value` lägger du till `--blob-tags='my%20tag=my%20tag%20value'` mål parametern. 

Avgränsa flera index-Taggar med hjälp av ett et-tecken ( `&` ).  Om du till exempel vill lägga till en nyckel `my second tag` och ett värde blir `my second tag value` den fullständiga alternativ strängen `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

I följande exempel visas hur du använder `--blob-tags` alternativet.

> [!TIP]
> Dessa exempel omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Katalog** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Container** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Konto** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats.

> [!NOTE]
> Om du anger en katalog, behållare eller ett konto för källan, kommer alla blobar som kopieras till målet att ha samma taggar som du anger i kommandot.

## <a name="copy-with-optional-flags"></a>Kopiera med valfria flaggor

Du kan ändra kopierings åtgärden genom att använda valfria flaggor. Här är några exempel.

|Scenario|Flagga|
|---|---|
|Kopiera blobbar som block, Page eller append-blobbar.|**--BLOB-Type** = \[ BlockBlob \| PageBlob- \| AppendBlob\]|
|Kopiera till en speciell åtkomst nivå (till exempel Arkiv nivån).|**--Block-Blob-Tier** = \[ Inget \| varmt \| coolt \| Arkiv\]|
|Expandera filer automatiskt.|**--Decompress** = \[ gzip- \| smal\]|

En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i de här artiklarna:

- [Exempel: Ladda upp](storage-use-azcopy-blobs-upload.md)
- [Exempel: Ladda ned](storage-use-azcopy-blobs-download.md)
- [Exempel: Synkronisera](storage-use-azcopy-blobs-synchronize.md)
- [Exempel: Amazon S3-bucketar](storage-use-azcopy-s3.md)
- [Exempel: Azure Files](storage-use-azcopy-files.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)