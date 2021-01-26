---
title: Kopiera data från Amazon S3 till Azure Storage med hjälp av AzCopy | Microsoft Docs
description: Använd AzCopy för att kopiera data från Amazon S3 till Azure Storage. AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b94cb6d6302cd92816fe25f6e672b1ce3bb9398d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792005"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Kopiera data från Amazon S3 till Azure Storage med hjälp av AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att kopiera objekt, kataloger och buckets från Amazon Web Services (AWS) S3 till Azure Blob Storage med AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Välj hur du tillhandahåller autentiseringsuppgifter

* Om du vill auktorisera med Azure Storage använder du Azure Active Directory (AD) eller en signatur för signatur för delad åtkomst (SAS).

* Om du vill auktorisera med AWS S3 använder du en AWS-åtkomst nyckel och en hemlig åtkomst nyckel.

### <a name="authorize-with-azure-storage"></a>Auktorisera med Azure Storage

Se artikeln [Kom igång med AZCopy](storage-use-azcopy-v10.md) för att hämta AzCopy och välj hur du ska ange autentiseringsuppgifter för lagrings tjänsten.

> [!NOTE]
> I exemplen i den här artikeln förutsätter vi att du har autentiserat din identitet med hjälp av `AzCopy login` kommandot. AzCopy använder sedan ditt Azure AD-konto för att ge åtkomst till data i Blob Storage.
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till BLOB-data kan du lägga till denna token i resurs-URL: en i varje AzCopy-kommando.
>
> Exempel: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Auktorisera med AWS S3

Samla in din AWS-åtkomst nyckel och den hemliga åtkomst nyckeln och ange följande miljövariabler:

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **macOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Kopiera objekt, kataloger och buckets

AzCopy använder plats [blocket från URL](/rest/api/storageservices/put-block-from-url) -API, så data kopieras direkt mellan AWS S3-och lagrings servrar. Dessa kopierings åtgärder använder inte datorns nätverks bandbredd.

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

 De här exemplen fungerar också med konton som har ett hierarkiskt namn område. [Med åtkomst till flera protokoll på data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) kan du använda samma URL-syntax ( `blob.core.windows.net` ) för dessa konton. 

### <a name="copy-an-object"></a>Kopiera ett objekt

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Exempel** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> I den här artikeln används URL: er för Sök vägs format för AWS S3-buckets (till exempel: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> Du kan också använda virtuella webb adresser i värd format (till exempel: `http://bucket.s3.amazonaws.com` ). 
>
> Om du vill veta mer om virtuell värd för buckets, se [virtuell värd för buckets]] ( https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) .

### <a name="copy-a-directory"></a>Kopiera en katalog

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exempel** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> I det här exemplet läggs `--recursive` flaggan till för att kopiera filer i alla under kataloger.

### <a name="copy-the-contents-of-a-directory"></a>Kopiera innehållet i en katalog

Du kan kopiera innehållet i en katalog utan att kopiera den innehåller själva katalogen genom att använda jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exempel** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Kopiera en Bucket

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Exempel** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Kopiera alla buckets i alla regioner

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exempel** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Kopiera alla buckets i en angiven S3-region

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exempel** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Hantera skillnader i namngivnings regler för objekt

AWS S3 har en annan uppsättning namn konventioner för Bucket-namn jämfört med Azure Blob-behållare. Du kan läsa om dem [här](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Om du väljer att kopiera en grupp med buckets till ett Azure Storage-konto kan kopierings åtgärden Miss Miss kan uppstå på grund av namngivnings skillnader.

AzCopy hanterar två av de vanligaste problemen som kan uppstå. buckets som innehåller punkter och buckets som innehåller flera bindestreck. AWS S3 Bucket-namn kan innehålla punkter och flera bindestreck, men en behållare i Azure kan inte. AzCopy ersätter punkter med bindestreck och efterföljande bindestreck med ett tal som representerar antalet bindestreck i rad (till exempel: en Bucket-namn `my----bucket` blir `my-4-bucket` . 

Som AzCopy kopior över filer söker den också efter namngivning av kollisioner och försöker lösa dem. Om det till exempel finns buckets med namnet `bucket-name` och `bucket.name` , matchar AzCopy en Bucket `bucket.name` som heter First till `bucket-name` och sedan till `bucket-name-2` .

## <a name="handle-differences-in-object-metadata"></a>Hantera skillnader i metadata för objekt

AWS S3 och Azure tillåter olika uppsättningar av tecken i namn på objekt nycklar. Du kan läsa om de tecken som AWS S3 använder [här](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). På Azure-sidan följer BLOB-objektens namngivnings regler för C#- [identifierare](/dotnet/csharp/language-reference/).

Som en del av ett AzCopy `copy` -kommando kan du ange ett värde för valfri `s2s-handle-invalid-metadata` flagga som anger hur du vill hantera filer där filens metadata innehåller inkompatibla nyckel namn. I följande tabell beskrivs varje flagg värde.

| Flagg värde | Beskrivning  |
|--------|-----------|
| **ExcludeIfInvalid** | (Standard alternativet) Metadata ingår inte i det överförda objektet. AzCopy loggar en varning. |
| **FailIfInvalid** | Objekt kopieras inte. AzCopy loggar ett fel och inkluderar det felet i det antal fel som visas i överförings sammanfattningen.  |
| **RenameIfInvalid**  | AzCopy matchar den ogiltiga metadata-nyckeln och kopierar objektet till Azure med det matchade nyckel värde paret för metadata. Om du vill veta exakt vilka steg AzCopy tar för att byta namn på objekt nycklar, se avsnittet [hur AzCopy byter namn på objekt nycklar](#rename-logic) nedan. Om AzCopy inte kan byta namn på nyckeln kopieras inte objektet. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Hur AzCopy byter namn på objekt nycklar

AzCopy utför följande steg:

1. Ersätter ogiltiga tecken med _.

2. Lägger till strängen `rename_` i början av en ny giltig nyckel.

   Den här nyckeln används för att spara det ursprungliga metadata- **värdet**.

3. Lägger till strängen `rename_key_` i början av en ny giltig nyckel.
   Den här nyckeln används för att spara de ursprungliga metadata- **nyckeln** är ogiltig.
   Du kan använda den här nyckeln för att försöka återställa metadata på Azure-sidan eftersom metadata-nyckeln bevaras som ett värde i Blob Storage-tjänsten.

## <a name="next-steps"></a>Nästa steg

Hitta fler exempel i någon av följande artiklar:

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)

- [Överföra data](storage-use-azcopy-v10.md#transfer-data)

- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)
