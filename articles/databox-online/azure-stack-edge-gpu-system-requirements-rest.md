---
title: Krav för Microsoft Azure Stack gräns för blob-lagring | Microsoft Docs
description: 'Lär dig mer om de versioner som stöds för API: er, SDK: er och klient bibliotek för Azure Stack Edge Blob Storage'
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 887aaf32592c74a5884916bc7ad63d79544fb554
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99575863"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Krav för Azure Stack gräns-blob-lagring

Den här artikeln innehåller en lista över de Azure-API: er, Azures klient bibliotek och verktyg som stöds med Azure Stack Edge Blob Storage. Azure Stack Edge Blob Storage tillhandahåller BLOB Management-funktioner med Azure-konsekvent semantik. Den här artikeln sammanfattar också de kända skillnaderna för Azure Stack gräns i blob-lagring från Azure Storage tjänsterna.

Vi rekommenderar att du läser informationen noggrant innan du ansluter till Azure Stack Edge Blob Storage och sedan går tillbaka till den vid behov.

## <a name="storage-differences"></a>Lagrings skillnader

|     Funktion                                             |     Azure Storage                                     |     Azure Stack Edge Blob Storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Molnbaserade SMB-filresurser stöds              |    Stöds inte      |
|    Typ av lagringskonto                                 |    Generell användnings-och Azure Blob Storage-konton    |    Allmän användning endast v1|
|    Blobnamn                                            |    1 024 tecken (2 048 byte)                     |    880 tecken (1 760 byte)|
|    Maximal storlek för Block Blob                              |    4,75 TB (100 MB X 50 000 block)                   |    4,75 TB (100 MB x 50 000 block) för Azure Stack Edge|
|    Maximal storlek för Page BLOB                               |    8 TB                                               |    1 TB                   |
|    Sid storlek för sid-BLOB                                  |    512 byte                                          |    4 kB                   |

## <a name="supported-api-versions"></a>API-versioner som stöds

Följande versioner av Azure Storage Service-API: er stöds med Azure Stack Edge Blob Storage.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge-2.1.1377.2170 och senare

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Azure-klientcertifikat som stöds

För Azure Stack Edge Blob Storage finns det särskilda klient bibliotek och särskilda krav för slut punkts suffix. Azure Stack Edge Blob Storage-slutpunkter har inte fullständig paritet med den senaste versionen av Azure Blob Storage REST API; Se de [API-versioner som stöds för Azure Stack Edge](#supported-api-versions). För lagrings klient biblioteken måste du vara medveten om vilken version som är kompatibel med REST API.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge-2.1.1377.2170 och senare

Följande versioner av Azure-klientens bibliotek stöds för Azure Stack Edge Blob Storage.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>Installera PHP-klienten via Composer-Current

Installera PHP-klienten via Composer:

1. Skapa en fil med namnet composer.jsi roten i projektet med följande kod (exempel använder Azure Storage Blob tjänst).

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Ladda ned `composer.phar` till projekt roten.

3. Kör: php Composer. Phar-installation.


## <a name="endpoint-declaration"></a>Slut punkts deklaration

I Azure Stack Edge Blob Storage SDK, identifierar Endpoint-suffixet `<device serial number>.microsoftdatabox.com` Azure Stack Edge-domänen. Mer information om BLOB service-slutpunkten finns i [överföra data via lagrings konton med Azure Stack Edge Pro GPU](azure-stack-edge-j-series-deploy-add-storage-accounts.md).


## <a name="examples"></a>Exempel

### <a name="net"></a>.NET

För Azure Stack Edge Blob Storage anges slut punktens suffix i `app.config` filen:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

För Azure Stack Edge Blob Storage anges slut punktens suffix i installationen av anslutnings strängen:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

För Azure Stack Edge Blob Storage anges slut punktens suffix i deklarations instansen:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

För Azure Stack Edge Blob Storage anges slut punktens suffix i installationen av anslutnings strängen:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

För Azure Stack Edge Blob Storage anges slut punktens suffix i installationen av anslutnings strängen:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

För Azure Stack Edge Blob Storage anges slut punktens suffix i deklarations instansen:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

För Azure Stack Edge Blob Storage anges slut punktens suffix i installationen av anslutnings strängen:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Nästa steg

* [Förbered för att distribuera Azure Stack Edge Pro med GPU](azure-stack-edge-gpu-deploy-prep.md)
