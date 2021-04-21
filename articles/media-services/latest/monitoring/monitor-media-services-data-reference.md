---
title: Referens Media Services övervakningsdata
description: Viktigt referensmaterial som behövs när du övervakar Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 4e4c65966ec8a6a5b47d5f68596f6d2d496fda17
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835514"
---
# <a name="monitoring-media-services-data-reference"></a>Referens Media Services övervakningsdata

Den här artikeln beskriver de data som är användbara för övervakning Media Services. Mer information om alla plattformsmått som stöds i Azure Monitor finns i [Mått som stöds med Azure Monitor](../../../azure-monitor/essentials/metrics-supported.md).

## <a name="media-services-metrics"></a>Media Services mått

Mått samlas in med jämna mellanrum oavsett om värdet ändras eller inte. De är användbara för aviseringar eftersom de kan samplas ofta, och en avisering kan snabbt fås ut med relativt enkel logik.


Media Services har stöd för övervakningsmått för följande resurser:

|Måtttyp | Resursprovider/typnamnrymd<br/> och länka till enskilda mått |
|-------|-----|
| Media Services allmänt | [Allmänt](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservices) |
| Livehändelser | [Microsoft.Media/mediaservices/liveEvents](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesliveevents) 
| Slutpunkter för direktuppspelning | [Microsoft.Media/mediaservices/streamingEndpoints](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesstreamingendpoints), som är relevanta för slutpunkterna [för direktuppspelning REST API](/rest/api/media/streamingendpoints). 


Du bör också granska [kontokvoter och begränsningar.](../limits-quotas-constraints-reference.md)


## <a name="metric-dimensions"></a>Måttdimensioner

Mer information om vilka måttdimensioner är finns [i Flerdimensionella mått.](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)

<!--**PLACEHOLDER** for dimensions table.-->
OutputFormat, HttpStatusCode, ErrorCode, TrackName

## <a name="resource-logs"></a>Resursloggar

## <a name="media-services-resource-logs"></a>Media Services resursloggar

Resursloggar ger omfattande och frekventa data om driften av en Azure-resurs. Mer information finns i Samla [in och använda loggdata från dina Azure-resurser.](../../../azure-monitor/essentials/platform-logs-overview.md)

Media Services har stöd för följande [resursloggar: Microsoft.Media/mediaservices](/azure/azure-monitor/essentials/resource-logs-categories#microsoftmediamediaservices)

## <a name="schemas"></a>Scheman

En detaljerad beskrivning av schemat för diagnostikloggar på den översta nivån finns [i Tjänster, scheman och kategorier som stöds för Azure-diagnostikloggar.](../../../azure-monitor/essentials/resource-logs-schema.md)

## <a name="key-delivery-log-schema-properties"></a>Schemaegenskaper för nyckelleveranslogg

De här egenskaperna är specifika för schemat för nyckelleveransloggen.

|Name|Beskrivning|
|---|---|
|keyId|ID för den begärda nyckeln.|
|Keytype|Kan vara något av följande värden: "Clear" (ingen kryptering), "FairPlay", "PlayReady" eller "Widevine".|
|policyName|Namnet Azure Resource Manager principen.|
|tokenType|Tokentyp.|
|statusMessage|Statusmeddelandet.|

### <a name="example"></a>Exempel

Egenskaper för schemat för nyckelleveransbegäranden.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

>[!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användningsvillkoren och sekretesspolicyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
