---
title: Kopiera från Google Cloud Storage till Azure Storage med AzCopy | Microsoft Docs
description: Använd AzCopy för att kopiera data från Google Cloud Storage till Azure Storage. AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3b2ad11abb7d1a3e64deef1ca49d9f84f03e5879
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498347"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Kopiera data från Google Cloud Storage till Azure Storage hjälp av AzCopy (förhandsversion)

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att kopiera objekt, kataloger och bucketar från Google Cloud Storage till Azure Blob Storage med hjälp av AzCopy.

> [!IMPORTANT]
> Kopiering av data från Google Cloud Storage till Azure Storage är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Välj hur du tillhandahåller autentiseringsuppgifter

* Om du vill auktorisera med Azure Storage använder du Azure Active Directory (AD) eller en SAS-token (signatur för delad åtkomst).

* Om du vill auktorisera med Google Cloud Storage använder du en nyckel för tjänstkontot.

### <a name="authorize-with-azure-storage"></a>Auktorisera med Azure Storage

I artikeln [Kom igång med AzCopy](storage-use-azcopy-v10.md) kan du ladda ned AzCopy och lära dig mer om hur du kan ange autentiseringsuppgifter för lagringstjänsten.

> [!NOTE] 
> Exemplen i den här artikeln förutsätter att du har angett autentiseringsuppgifter för auktorisering med hjälp av Azure Active Directory (Azure AD).
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till blobdata kan du lägga till denna token i resurs-URL:en i varje AzCopy-kommando. Exempel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Auktorisera med Google Cloud Storage

Om du vill auktorisera med Google Cloud Storage använder du en tjänstkontonyckel. Information om hur du skapar en tjänstkontonyckel finns i [Skapa och hantera tjänstkontonycklar.](https://cloud.google.com/iam/docs/creating-managing-service-account-keys)

När du har fått en tjänstnyckel anger du `GOOGLE_APPLICATION_CREDENTIALS` miljövariabeln till absolut sökväg till nyckelfilen för tjänstkontot:

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Kopiera objekt, kataloger och bucketar

AzCopy använder [API:et Put Block From URL](/rest/api/storageservices/put-block-from-url) så data kopieras direkt mellan Google Cloud Storage och lagringsservrar. Dessa kopieringsåtgärder använder inte datorns nätverksbandbredd.

> [!TIP]
> Exemplen i det här avsnittet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ('').

 De här exemplen fungerar också med konton som har en hierarkisk namnrymd. Åtkomst med flera protokoll på Data Lake Storage gör att du kan använda samma [URL-syntax](../blobs/data-lake-storage-multi-protocol-access.md) ( `blob.core.windows.net` ) på dessa konton. 

### <a name="copy-an-object"></a>Kopiera ett objekt

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namnområde.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Exempel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```


### <a name="copy-a-directory"></a>Kopiera en katalog

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namnområde.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Exempel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> Det här exemplet lägger till `--recursive` flaggan för att kopiera filer i alla underkataloger.

### <a name="copy-the-contents-of-a-directory"></a>Kopiera innehållet i en katalog

Du kan kopiera innehållet i en katalog utan att kopiera själva katalogen med jokertecknet (*).

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Exempel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-cloud-storage-bucket"></a>Kopiera en molnlagrings bucket

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namnområde.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Exempel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Kopiera alla buckets i ett Google Cloud-projekt 

Ange först till `GOOGLE_CLOUD_PROJECT` projekt-ID för Google Cloud-projekt.

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namnområde.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Exempel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Kopiera en delmängd av bucketar i ett Google Cloud-projekt 

Ange först till `GOOGLE_CLOUD_PROJECT` projekt-ID för Google Cloud-projekt.

Kopiera en delmängd av bucketar med hjälp av ett jokertecken (*) i bucketnamnet. Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namnområde.

**Syntax**

`azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Exempel**

```azcopy
azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-bucket-naming-rules"></a>Hantera skillnader i bucketnamnregler

Google Cloud Storage har en annan uppsättning namngivningskonventioner för bucketnamn jämfört med Azure Blob-containrar. Du kan läsa om dem [här.](https://cloud.google.com/storage/docs/naming-buckets) Om du väljer att kopiera en grupp med bucketar till ett Azure Storage-konto kan kopieringsåtgärden misslyckas på grund av namngivningsskillnader.

AzCopy hanterar tre av de vanligaste problemen som kan uppstå: buckets som innehåller punkter, bucketar som innehåller efterföljande bindestreck och bucketar som innehåller understreck. Google Cloud Storage-bucketnamn kan innehålla punkter och efterföljande bindestreck, men en container i Azure kan inte det. AzCopy ersätter punkter med bindestreck och efterföljande bindestreck med ett tal som representerar antalet bindestreck i följd (till exempel: en bucket med namnet `my----bucket` blir `my-4-bucket` . Om bucketnamnet har ett understreck ( ) ersätter AzCopy understrecket med ett bindestreck (till exempel: en bucket med namnet `_` `my_bucket` blir `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Hantera skillnader i namngivningsregler för objekt

Google Cloud Storage har en annan uppsättning namngivningskonventioner för objektnamn jämfört med Azure-blobar. Du kan läsa om dem [här.](https://cloud.google.com/storage/docs/naming-objects)

Azure Storage tillåter inte att objektnamn (eller segment i den virtuella katalogsökvägen) slutar med avslutande punkter (till exempel `my-bucket...` ). Avslutande punkter trimmas när kopieringsåtgärden utförs. 

## <a name="handle-differences-in-object-metadata"></a>Hantera skillnader i objektmetadata

Google Cloud Storage och Azure tillåter olika uppsättningar tecken i namnen på objektnycklar. Du kan läsa om metadata i Google Cloud Storage [här.](https://cloud.google.com/storage/docs/metadata) På Azure-sidan följer blobobjektnycklarna namngivningsreglerna för [C#-identifierare](/dotnet/csharp/language-reference/).

Som en del av ett AzCopy-kommando kan du ange ett värde för den valfria flaggan som anger hur du vill hantera filer där filens metadata innehåller `copy` `s2s-handle-invalid-metadata` inkompatibla nyckelnamn. I följande tabell beskrivs varje flaggvärde.

| Flaggvärde | Beskrivning  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standardalternativ) Metadata ingår inte i det överförda objektet. AzCopy loggar en varning. |
| **FailIfInvalid** | Objekt kopieras inte. AzCopy loggar ett fel och inkluderar felet i det antal misslyckade som visas i överföringssammanfattningen.  |
| **RenameIfInvalid**  | AzCopy matchar den ogiltiga metadatanyckeln och kopierar objektet till Azure med hjälp av nyckelvärdeparet för lösta metadata. Mer information om exakt vilka steg Som AzCopy vidtar för att byta namn på objektnycklar finns i [avsnittet Så här byter AzCopy namn på objektnycklar](#rename-logic) nedan. Om AzCopy inte kan byta namn på nyckeln kopieras inte objektet. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Så här byter AzCopy namn på objektnycklar

AzCopy utför följande steg:

1. Ersätter ogiltiga tecken med '_'.

2. Lägger till `rename_` strängen i början av en ny giltig nyckel.

   Den här nyckeln används för att spara det ursprungliga **metadatavärdet**.

3. Lägger till `rename_key_` strängen i början av en ny giltig nyckel.
   Den här nyckeln används för att spara den ursprungliga ogiltiga **metadatanyckeln**.
   Du kan använda den här nyckeln för att försöka återställa metadata på Azure-sidan eftersom metadatanyckeln bevaras som ett värde i Blob Storage-tjänsten.

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i de här artiklarna:

- [Exempel: Ladda upp](storage-use-azcopy-blobs-upload.md)
- [Exempel: Ladda ned](storage-use-azcopy-blobs-download.md)
- [Exempel: Kopiera mellan konton](storage-use-azcopy-blobs-copy.md)
- [Exempel: Synkronisera](storage-use-azcopy-blobs-synchronize.md)
- [Exempel: Amazon S3-bucketar](storage-use-azcopy-s3.md)
- [Exempel: Azure Files](storage-use-azcopy-files.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Se de här artiklarna för att konfigurera inställningar, optimera prestanda och felsöka problem:

- [AzCopy-konfigurationsinställningar](storage-ref-azcopy-configuration-settings.md)
- [Optimera prestanda för AzCopy](storage-use-azcopy-optimize.md)
- [Felsöka Problem med AzCopy V10 i Azure Storage med hjälp av loggfiler](storage-use-azcopy-configure.md)