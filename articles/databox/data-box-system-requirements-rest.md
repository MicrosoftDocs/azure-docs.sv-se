---
title: Microsoft Azure Data Box-enhet Blob Storage-krav | Microsoft Docs
description: 'Lär dig mer om de versioner som stöds för API: er, SDK: er och klient bibliotek för Azure Data Box Blob Storage'
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: ac5f2de383066d6ee399dac3b0ad8c365b2e72bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91744122"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box Blob Storage-krav

Den här artikeln innehåller en lista över de Azure-API: er, Azures klient bibliotek och verktyg som stöds med Data Box-enhet Blob Storage. Data Box-enhet Blob Storage tillhandahåller BLOB Management-funktioner med Azure-konsekvent semantik. Den här artikeln sammanfattar också kända Azure Data Boxs-Blob Storage-skillnader från Azure Storage tjänsterna.

Vi rekommenderar att du läser informationen noggrant innan du ansluter till Data Box-enhet Blob Storage och sedan går tillbaka till den vid behov.


## <a name="storage-differences"></a>Lagrings skillnader

|     Funktion                                             |     Azure Storage                                     |     Data Box Blob Storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Molnbaserade SMB-filresurser stöds              |    Stöds inte      |
|    Tjänst kryptering för vilande data                  |    256-bitars AES-kryptering                             |    256-bitars AES-kryptering |
|    Typ av lagringskonto                                 |    Generell användnings-och Azure Blob Storage-konton    |    Allmän användning endast v1|
|    Blobnamn                                            |    1 024 tecken (2 048 byte)                     |    880 tecken (1 760 byte)|
|    Maximal storlek för Block Blob                              |    4,75 TB (100 MB X 50 000 block)                   |    4,75 TB (100 MB x 50 000 block) för Azure Data Box v 3,0 och senare.|
|    Maximal storlek för Page BLOB                               |    8 TB                                               |    1 TB                   |
|    Sid storlek för sid-BLOB                                  |    512 byte                                          |    4 kB                   |

## <a name="supported-api-versions"></a>API-versioner som stöds

Följande versioner av Azure Storage Service-API: er stöds med Data Box-enhet Blob Storage.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0 och senare

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Azure-klientcertifikat som stöds

För Data Box-enhet Blob Storage finns det särskilda klient bibliotek och särskilda krav för slut punkts suffix. Data Box-enhet Blob Storage-slutpunkterna har inte fullständig paritet med den senaste versionen av Azure-Blob Storage REST API; Se de [versioner som stöds för Azure Data Box 3,0 och senare](#supported-api-versions). För lagrings klient biblioteken måste du vara medveten om vilken version som är kompatibel med REST API.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0 och senare

Följande versioner av Azure-klientens bibliotek stöds för Data Box-enhet Blob Storage.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-php-client-via-composer---current"></a>Installera PHP-klient via Composer-Current

Installera via Composer: (ta blob som exempel).
1. Skapa en fil med namnet composer.jsi roten i projektet med följande kod:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Ladda ned `composer.phar` till projekt roten.

3. Kör: php Composer. Phar-installation.

### <a name="endpoint-declaration"></a>Slut punkts deklaration

I Data Box-enhet Blob Storage SDK, identifierar Endpoint-suffixet `<device serial number>.microsoftdatabox.com` den data Box-enhet domänen. Mer information om BLOB service-slutpunkten finns i [ansluta via data Box-enhet Blob Storage](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Exempel

### <a name="net"></a>.NET

För Data Box-enhet Blob Storage anges slut punktens suffix i `app.config` filen:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

För Data Box-enhet Blob Storage anges slut punktens suffix i installationen av anslutnings sträng:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

För Data Box-enhet Blob Storage anges slut punktens suffix i deklarations instansen:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

För Data Box-enhet Blob Storage anges slut punktens suffix i installationen av anslutnings sträng:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

För Data Box-enhet Blob Storage anges slut punktens suffix i installationen av anslutnings sträng:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

För Data Box-enhet Blob Storage anges slut punktens suffix i deklarations instansen:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

För Data Box-enhet Blob Storage anges slut punktens suffix i installationen av anslutnings sträng:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)
