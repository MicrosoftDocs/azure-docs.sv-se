---
title: Övervaknings Media Services data referens
description: Viktigt referensmaterial som krävs när du övervakar Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.date: 03/17/2021
ms.openlocfilehash: 866b2faf473f06fc3f85cdb434d6555504a7f6a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598192"
---
# <a name="monitoring-media-services-data-reference"></a>Övervaknings Media Services data referens

Den här artikeln beskriver de data som är användbara för att övervaka Media Services. För ytterligare information om alla plattforms mått som stöds i Azure Monitor, granska de [mått som stöds med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).

## <a name="media-services-metrics"></a>Media Services mått

Mått samlas in med jämna mellanrum oavsett om värdet ändras eller inte. De är användbara för aviseringar eftersom de kan samplas ofta, och en avisering kan utlösas snabbt med relativt enkel logik.

Media Services stöder övervaknings mått för följande resurser:

* Konto
* Slutpunkt för direktuppspelning

### <a name="account"></a>Konto

Du kan övervaka följande konto mått.

|Måttnamn|Visningsnamn|Description|
|---|---|---|
|AssetCount|Antal till gångar|Till gångar i ditt konto.|
|AssetQuota|Till gångs kvot|Till gångs kvot i ditt konto.|
|AssetQuotaUsedPercentage|Använd procent andel till till gångs kvot|Procent andelen av till gångs kvoten som redan används.|
|ContentKeyPolicyCount|Antal nyckel principer för innehåll|Nyckel principer för innehåll i ditt konto.|
|ContentKeyPolicyQuota|Kvot för innehålls nyckel princip|Kvoten för innehålls nyckel principer i ditt konto.|
|ContentKeyPolicyQuotaUsedPercentage|Procent andel av nyckel princip för innehåll|Procent andelen av innehålls nyckelns princip kvot som redan används.|
|StreamingPolicyCount|Antal strömmande principer|Strömmande principer i ditt konto.|
|StreamingPolicyQuota|Kvot för strömmande princip|Kvot för strömning av principer i ditt konto.|
|StreamingPolicyQuotaUsedPercentage|Använd procent andel av princip för strömning|Procent andelen av den strömmande princip kvoten används redan.|

Du bör också granska [konto kvoter och begränsningar](../limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Slutpunkt för direktuppspelning

Följande Media Services [slut punkter för direkt uppspelnings](/rest/api/media/streamingendpoints) mått stöds:

|Måttnamn|Visningsnamn|Description|
|---|---|---|
|Begäranden|Begäranden|Visar det totala antalet HTTP-begäranden som hanteras av slut punkten för direkt uppspelning.|
|Utgående|Utgående|Utgående byte totalt per minut per slut punkt för direkt uppspelning.|
|SuccessE2ELatency|Slutför svars tid för slut punkt till slut punkt|Varaktighet från när strömnings slut punkten fick begäran till när den sista byten av svaret skickades.|
|CPU-användning| | CPU-användning för förstklassiga strömnings slut punkter. Dessa data är inte tillgängliga för standard slut punkter för direkt uppspelning. |
|Utgående bandbredd | | Utgående bandbredd i bitar per sekund.|

## <a name="metric-dimensions"></a>Mått dimensioner

Mer information om vilka mått dimensioner som finns i [flerdimensionella mått](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics).

<!--**PLACEHOLDER** for dimensions table.-->

## <a name="resource-logs"></a>Resursloggar

## <a name="media-services-diagnostic-logs"></a>Media Services diagnostikloggar

Diagnostikloggar ger omfattande och frekventa data om driften av en Azure-resurs. Mer information finns i [så här samlar du in och använder loggdata från dina Azure-resurser](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md).

Media Services stöder följande diagnostikloggar:

* Nyckel leverans

### <a name="key-delivery"></a>Nyckel leverans

|Name|Beskrivning|
|---|---|
|Begäran om nyckel leverans tjänst|Loggar som visar information om viktiga leverans tjänst begär Anden. Mer information finns i [scheman](monitor-media-services-data-reference.md).|

## <a name="schemas"></a>Scheman

Detaljerad beskrivning av schemat på den högsta nivån av diagnostiska loggar finns i [tjänster, scheman och kategorier som stöds för Azure Diagnostic-loggar](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs-schema.md).

## <a name="key-delivery-log-schema-properties"></a>Schema egenskaper för nyckel leverans logg

De här egenskaperna är speciella för logg schema för nyckel leverans.

|Name|Beskrivning|
|---|---|
|keyId|ID för den begärda nyckeln.|
|keyType|Kan vara något av följande värden: "Clear" (ingen kryptering), "FairPlay", "PlayReady" eller "Widevine".|
|policyName|Principens Azure Resource Manager namn.|
|tokenType|Tokentyp.|
|statusMessage|Status meddelandet.|

### <a name="example"></a>Exempel

Egenskaper för nyckel leverans begär ande schema.

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
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
