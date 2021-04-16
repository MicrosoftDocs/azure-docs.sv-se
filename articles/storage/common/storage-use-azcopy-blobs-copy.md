---
title: Kopiera blobar mellan Azure Storage-konton med AzCopy v10 | Microsoft Docs
description: Den här artikeln innehåller en samling AzCopy-exempelkommandon som hjälper dig att kopiera blobar mellan lagringskonton.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: bfdc91ac8f4ce618052cc78e76b27e8bdeabeb77
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502988"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy"></a>Kopiera blobar mellan Azure Storage-konton med hjälp av AzCopy

Du kan kopiera blobar, kataloger och containrar mellan lagringskonton med hjälp av kommandoradsverktyget AzCopy v10. 

Exempel på andra typer av uppgifter, till exempel att ladda upp filer, ladda ned blobar och [](#next-steps) synkronisera med Blob Storage, finns i länkarna i avsnittet Nästa steg i den här artikeln.

AzCopy använder [server-till-server-API:er,](/rest/api/storageservices/put-block-from-url) [](/rest/api/storageservices/put-page-from-url)så data kopieras direkt mellan lagringsservrar. Dessa kopieringsåtgärder använder inte datorns nätverksbandbredd.

Om du vill ladda ned AzCopy och lära dig mer om hur du kan ange autentiseringsuppgifter för lagringstjänsten kan du läsa [Kom igång med AzCopy.](storage-use-azcopy-v10.md) 

## <a name="guidelines"></a>Riktlinjer

Använd följande riktlinjer för dina AzCopy-kommandon. 

- Klienten måste ha nätverksåtkomst till både käll- och mållagringskontona. Information om hur du konfigurerar nätverksinställningarna för varje lagringskonto finns i [Konfigurera Azure Storage och virtuella nätverk.](storage-network-security.md?toc=/azure/storage/blobs/toc.json)

- Lägg till en SAS-token till varje käll-URL. 

  Om du anger autentiseringsuppgifter för auktorisering med hjälp Azure Active Directory (Azure AD) kan du utelämna ENDAST SAS-token från mål-URL:en. Kontrollera att du har ställt in rätt roller i målkontot. Se [Alternativ 1: Använd Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  Exemplen i den här artikeln förutsätter att du har autentiserat din identitet med hjälp av Azure AD, så exemplen utelämnar SAS-token från mål-URL:en.

-  Om du kopierar till ett Premium Block Blob Storage-konto utelämnar du åtkomstnivån för en blob från kopieringsåtgärden genom att ange `s2s-preserve-access-tier` `false` till (till exempel: `--s2s-preserve-access-tier=false` ). Premium-konton för blockbloblagring har inte stöd för åtkomstnivåer. 

- Om du kopierar till eller från ett konto som har ett hierarkiskt namnområde använder du i `blob.core.windows.net` stället för `dfs.core.windows.net` i URL-syntaxen. [Åtkomst med flera protokoll på Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) kan du använda och det är den enda syntax som stöds för `blob.core.windows.net` konto-till-kontokopieringsscenarier. 

- Du kan öka dataflödet för kopieringsåtgärder genom att ange värdet för `AZCOPY_CONCURRENCY_VALUE` miljövariabeln . Mer information finns i [Öka samtidighet.](storage-use-azcopy-optimize.md#increase-concurrency) 

- Om källblobarna har indextaggar och du vill behålla taggarna måste du tillämpa dem på målblobarna igen. Information om hur du anger indextaggar finns i avsnittet Kopiera blobar till [ett annat lagringskonto med indextaggar](#copy-between-accounts-and-add-index-tags) i den här artikeln.

## <a name="copy-a-blob"></a>Kopiera en blob

Kopiera en blob till ett annat lagringskonto med hjälp av [kommandot azcopy copy.](storage-ref-azcopy-copy.md) 

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ('').

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'`

**Exempel**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats. 

## <a name="copy-a-directory"></a>Kopiera en katalog

Kopiera en katalog till ett annat lagringskonto med hjälp av [kommandot azcopy copy.](storage-ref-azcopy-copy.md) 

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ('').

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Exempel**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats.

## <a name="copy-a-container"></a>Kopiera en container

Kopiera en container till ett annat lagringskonto med hjälp av [kommandot azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ('').

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Exempel**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats.

## <a name="copy-containers-directories-and-blobs"></a>Kopiera containrar, kataloger och blobar

Kopiera alla containrar, kataloger och blobar till ett annat lagringskonto med hjälp av [kommandot azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

**Syntax**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive`

**Exempel**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive
```

Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Kopiera blobar och lägga till indextaggar

Kopiera blobar till ett annat lagringskonto och lägg [till blobindextaggar (förhandsversion)](../blobs/storage-manage-find-blobs.md) till målbloben.

Om du använder Azure AD-auktorisering måste säkerhetsobjekt tilldelas rollen Storage [Blob Data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) eller ges behörighet till Azure-resursprovideråtgärden via en `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) anpassad Azure-roll. Om du använder en SAS-token (signatur för delad åtkomst) måste denna token ge åtkomst till blobens taggar via `t` SAS-behörigheten.

Om du vill lägga till taggar `--blob-tags` använder du alternativet tillsammans med ett URL-kodat nyckel/värde-par. 

Om du till exempel vill lägga till nyckeln `my tag` och ett värde lägger du till i `my tag value` `--blob-tags='my%20tag=my%20tag%20value'` målparametern. 

Avgränsa flera indextaggar med ett et-nummer ( `&` ).  Om du till exempel vill lägga till en nyckel `my second tag` och ett värde blir den fullständiga `my second tag value` alternativsträngen `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

I följande exempel visas hur du använder `--blob-tags` alternativet .

> [!TIP]
> De här exemplen omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt förutom Windows Command Shell (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ("").

**Blobexempel**

```azcopy

`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Katalogexempel**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

 **Containerexempel**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Kontoexempel**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

Kopieringsåtgärden är synkron, så när kommandot returneras anger det att alla filer har kopierats.

> [!NOTE]
> Om du anger en katalog, en container eller ett konto för källan har alla blobar som kopieras till målet samma taggar som du anger i kommandot.

## <a name="copy-with-optional-flags"></a>Kopiera med valfria flaggor

Du kan justera kopieringsåtgärden med hjälp av valfria flaggor. Här är några exempel.

|Scenario|Flagga|
|---|---|
|Kopiera blobar som block-, sid- eller tilläggsblobar.|**--blob-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Kopiera till en specifik åtkomstnivå (till exempel arkivnivån).|**--block-blob-tier** = \[ Inget \| arkiv med mycket \| \| kall/kall\]|
|Dekomprimera filer automatiskt.|**--dekomprimera** = \[ gzip \| deflate\]|

En fullständig lista finns i [alternativ](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i de här artiklarna:

- [Exempel: Ladda upp](storage-use-azcopy-blobs-upload.md)
- [Exempel: Ladda ned](storage-use-azcopy-blobs-download.md)
- [Exempel: Synkronisera](storage-use-azcopy-blobs-synchronize.md)
- [Exempel: Amazon S3-bucketar](storage-use-azcopy-s3.md)
- [Exempel: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Exempel: Azure Files](storage-use-azcopy-files.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Se de här artiklarna för att konfigurera inställningar, optimera prestanda och felsöka problem:

- [AzCopy-konfigurationsinställningar](storage-ref-azcopy-configuration-settings.md)
- [Optimera Prestanda för AzCopy](storage-use-azcopy-optimize.md)
- [Felsöka AzCopy V10-problem i Azure Storage med hjälp av loggfiler](storage-use-azcopy-configure.md)