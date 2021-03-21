---
title: Kopiera data från Google Cloud Storage till Azure Storage med hjälp av AzCopy | Microsoft Docs
description: Använd AzCopy för att kopiera data från Google Cloud Storage till Azure Storage. AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c0f030683954ede013f769bf8584e6cf82bab69f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555771"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Kopiera data från Google Cloud Storage till Azure Storage med hjälp av AzCopy (för hands version)

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att kopiera objekt, kataloger och buckets från Google Cloud Storage till Azure Blob Storage med hjälp av AzCopy.

> [!IMPORTANT]
> Att kopiera data från Google Cloud Storage till Azure Storage finns för närvarande i offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Välj hur du tillhandahåller autentiseringsuppgifter

* Om du vill auktorisera med Azure Storage använder du Azure Active Directory (AD) eller en signatur för signatur för delad åtkomst (SAS).

* Använd en tjänst konto nyckel för att auktorisera med Google Cloud Storage.

### <a name="authorize-with-azure-storage"></a>Auktorisera med Azure Storage

Se artikeln [Kom igång med AZCopy](storage-use-azcopy-v10.md) för att ladda ned AzCopy och Läs om hur du kan ange autentiseringsuppgifter för lagrings tjänsten.

> [!NOTE] 
> I exemplen i den här artikeln förutsätter vi att du har angett autentiseringsuppgifter för auktorisering genom att använda Azure Active Directory (Azure AD).
>
> Om du hellre vill använda en SAS-token för att auktorisera åtkomst till BLOB-data kan du lägga till denna token i resurs-URL: en i varje AzCopy-kommando. Exempel: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Auktorisera med Google Cloud Storage

För att auktorisera med Google Cloud Storage använder du en tjänst konto nyckel. Information om hur du skapar en tjänst konto nyckel finns i [skapa och hantera tjänst konto nycklar](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

När du har fått en tjänst nyckel ställer du in `GOOGLE_APPLICATION_CREDENTIALS` miljövariabeln på den absoluta sökvägen till tjänst kontots nyckel fil:

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Kopiera objekt, kataloger och buckets

AzCopy använder plats [blocket i URL](/rest/api/storageservices/put-block-from-url) -API, så att data kopieras direkt mellan Google Cloud Storage och lagrings servrar. Dessa kopierings åtgärder använder inte datorns nätverks bandbredd.

> [!TIP]
> Exemplen i det här avsnittet omger Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

 De här exemplen fungerar också med konton som har ett hierarkiskt namn område. [Med åtkomst till flera protokoll på data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) kan du använda samma URL-syntax ( `blob.core.windows.net` ) för dessa konton. 

### <a name="copy-an-object"></a>Kopiera ett objekt

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Exempel** | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Exempel** (hierarkiskt namn område) | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |


### <a name="copy-a-directory"></a>Kopiera en katalog

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exempel** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> I det här exemplet läggs `--recursive` flaggan till för att kopiera filer i alla under kataloger.

### <a name="copy-the-contents-of-a-directory"></a>Kopiera innehållet i en katalog

Du kan kopiera innehållet i en katalog utan att kopiera den innehåller själva katalogen genom att använda jokertecknet (*).

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exempel** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-cloud-storage-bucket"></a>Kopiera en moln lagrings Bucket

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exempel** | `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Kopiera alla buckets i ett Google Cloud-projekt 

Börja med att ställa in `GOOGLE_CLOUD_PROJECT` till projekt-ID för Google Cloud Project.

Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exempel** | `azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://storage.cloud.google.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Kopiera en delmängd av buckets i ett Google Cloud-projekt 

Börja med att ställa in `GOOGLE_CLOUD_PROJECT` till projekt-ID för Google Cloud Project.

Kopiera en delmängd av buckets med hjälp av en symbol för jokertecken (*) i Bucket-namnet. Använd samma URL-syntax ( `blob.core.windows.net` ) för konton som har ett hierarkiskt namn område.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exempel** | `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exempel** (hierarkiskt namn område)| `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

## <a name="handle-differences-in-bucket-naming-rules"></a>Hantera skillnader i Bucket-namngivnings regler

Google Cloud Storage har en annan uppsättning namn konventioner för Bucket-namn jämfört med Azure Blob-behållare. Du kan läsa om dem [här](https://cloud.google.com/storage/docs/naming-buckets). Om du väljer att kopiera en grupp med buckets till ett Azure Storage-konto kan kopierings åtgärden Miss Miss kan uppstå på grund av namngivnings skillnader.

AzCopy hanterar tre av de vanligaste problemen som kan uppstå. buckets som innehåller punkter, buckets som innehåller flera bindestreck och buckets som innehåller under streck. Google Cloud Storage Bucket-namn kan innehålla punkter och flera bindestreck, men en behållare i Azure kan inte. AzCopy ersätter punkter med bindestreck och efterföljande bindestreck med ett tal som representerar antalet bindestreck i rad (till exempel: en Bucket-namn `my----bucket` blir `my-4-bucket` . Om Bucket-namnet har ett under streck ( `_` ) ersätter AzCopy under streck med ett bindestreck (till exempel: en Bucket-namn `my_bucket` blir `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Hantera skillnader i namngivnings regler för objekt

Google Cloud Storage har en annan uppsättning namn konventioner för objekt namn jämfört med Azure-blobbar. Du kan läsa om dem [här](https://cloud.google.com/storage/docs/naming-objects).

Azure Storage tillåter inte att objekt namn (eller något segment i den virtuella katalog Sök vägen) slutar med efterföljande punkter (till exempel `my-bucket...` ). Efterföljande punkter rensas bort när kopierings åtgärden utförs. 

## <a name="handle-differences-in-object-metadata"></a>Hantera skillnader i metadata för objekt

Google Cloud Storage och Azure tillåter olika uppsättningar av tecken i namn på objekt nycklar. Du kan läsa om metadata i Google Cloud Storage [här](https://cloud.google.com/storage/docs/metadata). På Azure-sidan följer BLOB-objektens namngivnings regler för C#- [identifierare](/dotnet/csharp/language-reference/).

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

- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)
