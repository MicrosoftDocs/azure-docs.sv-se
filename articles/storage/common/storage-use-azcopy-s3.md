---
title: Kopiera data från Amazon S3 till Azure Storage hjälp av AzCopy | Microsoft Docs
description: Använd AzCopy för att kopiera data från Amazon S3 till Azure Storage. AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: ac73d0e57377a8922691ea06c8de3df5ef577680
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502444"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Kopiera data från Amazon S3 till Azure Storage hjälp av AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att kopiera objekt, kataloger och bucketar från Amazon Web Services (AWS) S3 till Azure Blob Storage med hjälp av AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Välj hur du tillhandahåller autentiseringsuppgifter

* Om du vill auktorisera med Azure Storage använder du Azure Active Directory (AD) eller en SAS-token (signatur för delad åtkomst).

* Om du vill auktorisera med AWS S3 använder du en AWS-åtkomstnyckel och en hemlig åtkomstnyckel.

### <a name="authorize-with-azure-storage"></a>Auktorisera med Azure Storage

Se artikeln [Kom igång med AzCopy för](storage-use-azcopy-v10.md) att ladda ned AzCopy och välj hur du ska ange autentiseringsuppgifter för auktorisering till lagringstjänsten.

> [!NOTE]
> Exemplen i den här artikeln förutsätter att du har autentiserat din identitet med hjälp av `AzCopy login` kommandot . AzCopy använder sedan ditt Azure AD-konto för att auktorisera åtkomst till data i Blob Storage.
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till blobdata kan du lägga till denna token i resurs-URL:en i varje AzCopy-kommando.
>
> Exempel: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Auktorisera med AWS S3

Samla in din AWS-åtkomstnyckel och hemliga åtkomstnyckel och ange sedan följande miljövariabler:

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **macOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopiera objekt, kataloger och buckets

AzCopy använder [API:et Put Block From URL](/rest/api/storageservices/put-block-from-url) så att data kopieras direkt mellan AWS S3 och lagringsservrar. Dessa kopieringsåtgärder använder inte datorns nätverksbandbredd.

> [!TIP]
> Exemplen i det här avsnittet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ('').

 De här exemplen fungerar också med konton som har en hierarkisk namnrymd. [Åtkomst med flera protokoll på Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) gör att du kan använda samma URL-syntax ( ) på dessa `blob.core.windows.net` konton. 

### <a name="copy-an-object"></a>Kopiera ett objekt

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namnområde.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Exempel**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```

> [!NOTE]
> I exemplen i den här artikeln används url:er i sökvägsformat för AWS S3-bucketar (till exempel: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> Du kan också använda URL:er i virtuellt värdformat (till exempel: `http://bucket.s3.amazonaws.com` ). 
>
> Mer information om virtuella värdtjänster för bucketar finns i [Virtual Hosting of Buckets (Virtuell värd för buckets).](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)

### <a name="copy-a-directory"></a>Kopiera en katalog

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namnområde.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Exempel**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> Det här exemplet lägger till `--recursive` flaggan för att kopiera filer i alla underkataloger.

### <a name="copy-the-contents-of-a-directory"></a>Kopiera innehållet i en katalog

Du kan kopiera innehållet i en katalog utan att kopiera själva katalogen med jokertecknet (*).

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Exempel**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-bucket"></a>Kopiera en bucket

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namnområde.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true`

**Exempel**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true
```

### <a name="copy-all-buckets-in-all-regions"></a>Kopiera alla bucketar i alla regioner

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namnområde.

**Syntax**

`azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Exempel**

```azcopy
azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Kopiera alla bucketar i en specifik S3-region

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namnområde.

**Syntax**

`azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Exempel**

```azcopy
azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-object-naming-rules"></a>Hantera skillnader i namngivningsregler för objekt

AWS S3 har en annan uppsättning namngivningskonventioner för bucketnamn jämfört med Azure Blob-containrar. Du kan läsa om dem [här.](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) Om du väljer att kopiera en grupp med bucketar till ett Azure Storage-konto kan kopieringsåtgärden misslyckas på grund av namngivningsskillnader.

AzCopy hanterar två av de vanligaste problemen som kan uppstå: buckets som innehåller punkter och bucketar som innehåller efterföljande bindestreck. AWS S3-bucketnamn kan innehålla punkter och efterföljande bindestreck, men en container i Azure kan inte det. AzCopy ersätter punkter med bindestreck och efterföljande bindestreck med ett tal som representerar antalet på varandra följande bindestreck (till exempel: en bucket med `my----bucket` namnet blir `my-4-bucket` . 

När AzCopy kopierar över filer kontrollerar det också om det finns namngivningskollisioner och försök att lösa dem. Om det till exempel finns bucketar med namnet och matchar AzCopy en bucket med namnet `bucket-name` först till och sedan till `bucket.name` `bucket.name` `bucket-name` `bucket-name-2` .

## <a name="handle-differences-in-object-metadata"></a>Hantera skillnader i objektmetadata

AWS S3 och Azure tillåter olika uppsättningar tecken i namnen på objektnycklar. Du kan läsa om de tecken som AWS S3 använder [här.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys) På Azure-sidan följer blobobjektnycklarna namngivningsreglerna för [C#-identifierare](/dotnet/csharp/language-reference/).

Som en del av ett AzCopy-kommando kan du ange ett värde för den valfria flaggan som anger hur du vill hantera filer där filens metadata innehåller `copy` `s2s-handle-invalid-metadata` inkompatibla nyckelnamn. I följande tabell beskrivs varje flaggvärde.

| Flaggvärde | Beskrivning  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standardalternativ) Metadata ingår inte i det överförda objektet. AzCopy loggar en varning. |
| **FailIfInvalid** | Objekt kopieras inte. AzCopy loggar ett fel och inkluderar felet i det antal misslyckade som visas i överföringssammanfattningen.  |
| **RenameIfInvalid**  | AzCopy matchar den ogiltiga metadatanyckeln och kopierar objektet till Azure med hjälp av nyckelvärdeparet för lösta metadata. Mer information om exakt vilka steg AzCopy vidtar för att byta namn på objektnycklar finns i [avsnittet Så här byter AzCopy namn på objektnycklar](#rename-logic) nedan. Om AzCopy inte kan byta namn på nyckeln kopieras inte objektet. |

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
- [Exempel: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Exempel: Azure Files](storage-use-azcopy-files.md)
- [Självstudie: Migrera lokala data till molnlagring med AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Se de här artiklarna för att konfigurera inställningar, optimera prestanda och felsöka problem:

- [AzCopy-konfigurationsinställningar](storage-ref-azcopy-configuration-settings.md)
- [Optimera prestanda för AzCopy](storage-use-azcopy-optimize.md)
- [Felsöka Problem med AzCopy V10 i Azure Storage med hjälp av loggfiler](storage-use-azcopy-configure.md)
