---
title: Övervaknings data referens för Azure Blob Storage | Microsoft Docs
description: Logg och statistik referens för övervakning av data från Azure Blob Storage.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: b37586f66106e33b2a8dad034a6e7c131484be73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571630"
---
# <a name="azure-blob-storage-monitoring-data-reference"></a>Övervaknings data referens för Azure Blob Storage

Mer information om hur du samlar in och analyserar övervaknings data för Azure Storage finns i [övervaknings Azure Storage](monitor-blob-storage.md) .

## <a name="metrics"></a>Mått

I följande tabeller visas de plattforms mått som samlas in för Azure Storage. 

### <a name="capacity-metrics"></a>Kapacitetsmått

Värdena för kapacitets värden uppdateras dagligen (upp till 24 timmar). Tids kornig het definierar tidsintervallet för vilka mått värden presenteras. Tids kornig het för alla kapacitets mått är en timme (PT1H).

Azure Storage tillhandahåller följande kapacitets mått i Azure Monitor.

#### <a name="account-level"></a>Konto nivå

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="blob-storage"></a>Blob Storage

Den här tabellen visar [Blob Storage-mått](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices).

| Metric | Beskrivning |
| ------------------- | ----------------- |
| BlobCapacity | Summan av Blob Storage som används i lagrings kontot. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 <br/> Dimensioner: **BlobType** och **BlobTier** ([definition](#metrics-dimensions)) |
| BlobCount    | Antalet BLOB-objekt som lagras i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 <br/> Dimensioner: **BlobType** och **BlobTier** ([definition](#metrics-dimensions)) |
| BlobProvisionedSize | Mängden lagrings utrymme som har allokerats i lagrings kontot. Detta mått gäller endast för Premium Storage-konton. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt |
| ContainerCount    | Antalet behållare i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| IndexCapacity     | Mängden lagrings utrymme som används av ADLS Gen2 hierarkiskt index <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |

### <a name="transaction-metrics"></a>Transaktionsmått

Transaktions mått genereras på varje begäran till ett lagrings konto från Azure Storage till Azure Monitor. Om det inte finns någon aktivitet på ditt lagrings konto kommer det inte att finnas några data om transaktions måtten under perioden. Alla transaktions mått är tillgängliga på både konto nivå och Blob Storage servicenivå. Tids kornig het definierar tidsintervallet som Mät värden presenteras. Tids korn som stöds för alla transaktions mått är PT1H och PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Storage stöder följande dimensioner för mått i Azure Monitor.

### <a name="dimensions-available-to-all-storage-services"></a>Dimensioner som är tillgängliga för alla lagrings tjänster

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

### <a name="dimensions-specific-to-blob-storage"></a>Dimensioner som är speciella för Blob Storage

| Dimensions namn | Beskrivning |
| ------------------- | ----------------- |
| **BlobType** | Typ av BLOB för BLOB-mått. De värden som stöds är **BlockBlob**, **PageBlob** och **Azure Data Lake Storage**. Bifogade blobbar ingår i **BlockBlob**. |
| **BlobTier** | Azure Storage erbjuder olika åtkomst nivåer, vilket gör att du kan lagra BLOB-Datadata på det mest kostnads effektiva sättet. Läs mer i [Azure Storage BLOB-nivå](../blobs/storage-blob-storage-tiers.md). De värden som stöds är: <br/> <li>Frekvent **: frekvent** nivå</li> <li>Låg **frekvent: låg** frekvent nivå</li> <li>**Arkiv**: Arkiv lag ring</li> <li>**Premium**: Premium-nivån för Block-Blob</li> <li>**P4/P6/P10/p15/P20/P30/P40/P50/P60**: nivå typer för Premium Page-BLOB</li> <li>**Standard**: nivå typ för standard sid-BLOB</li> <li>Inte i **nivå**: nivå typ för generell användning v1 lagrings konto</li> |

För mått som stöder dimensioner måste du ange dimension svärdet för att se motsvarande mått värden. Om du till exempel tittar på  **transaktions** värde för lyckade svar måste du filtrera **ResponseType** -dimensionen med **lyckat resultat**. Om du tittar på **BlobCount** -värdet för Block-Blob måste du filtrera **BlobType** -dimensionen med **BlockBlob**.

## <a name="resource-logs-preview"></a>Resurs loggar (förhands granskning)

> [!NOTE]
> Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. Den här för hands versionen aktiverar loggar för blobbar (inklusive Azure Data Lake Storage Gen2), filer, köer, tabeller, Premium Storage-konton i generell användning v1-och General-Purpose v2-lagrings konton. Klassiska lagrings konton stöds inte.

I följande tabell visas egenskaperna för Azure Storage resurs loggar när de samlas in i Azure Monitor loggar eller Azure Storage. Egenskaperna beskriver åtgärden, tjänsten och typen av auktorisering som användes för att utföra åtgärden.

### <a name="fields-that-describe-the-operation"></a>Fält som beskriver åtgärden

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Fält som beskriver hur åtgärden autentiserades

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Fält som beskriver tjänsten

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Se även

- En beskrivning av övervaknings Azure Storage finns i [övervaknings Azure Storage](monitor-blob-storage.md) .
- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md) .
