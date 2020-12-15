---
title: Hämta mått med REST API
titleSuffix: Azure Load Balancer
description: 'I den här artikeln får du börja använda Azure REST-API: erna för att samla in hälso-och användnings statistik för Azure Load Balancer.'
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: how-to
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: fbf21ba359ae7914acd2d812ec9e12f9f3ee557a
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511951"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Hämta användnings statistik för Load Balancer med hjälp av REST API

Samla in antalet byte som bearbetats av en [standard Load Balancer](./load-balancer-overview.md) under ett tidsintervall med hjälp av [Azure REST API](/rest/api/azure/).

Fullständig referens dokumentation och ytterligare exempel för REST API finns i [Azure Monitor rest-referensen](/rest/api/monitor). 

## <a name="build-the-request"></a>Skapa begäran

Använd följande GET-begäran för att samla in [ByteCount-måttet](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics) från en standard Load Balancer. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Begärandehuvuden

Följande huvuden krävs: 

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange till `application/json`.|  
|*Auktorisering:*|Krävs. Ange till en giltig `Bearer`-[åtkomsttoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-parametrar

| Namn | Beskrivning |
| :--- | :---------- |
| subscriptionId | Det prenumerations-ID som identifierar en Azure-prenumeration. Om du har flera prenumerationer kan du läsa mer i [arbeta med flera prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Namnet på den resurs grupp som innehåller resursen. Du kan hämta det här värdet från Azure Resource Manager API, CLI eller portalen. |
| loadBalancerName | Namnet på Azure Load Balancer. |
| mått namn | Kommaavgränsad lista över giltiga  [load BALANCERS mått](./load-balancer-standard-diagnostics.md). |
| api-version | Den API-version som ska användas för begäran.<br /><br /> Det här dokumentet beskriver API-versionen `2018-01-01` , som ingår i ovanstående URL.  |
| tidsintervall | Frågans TimeSpan. Det är en sträng med följande format `startDateTime_ISO/endDateTime_ISO` . Den här valfria parametern är inställd på att returnera en dags värde i exemplet. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Begärandetext

Ingen begär ande text krävs för den här åtgärden.

## <a name="handle-the-response"></a>Hantera svaret

Status koden 200 returneras när listan med mått värden returneras. En fullständig lista över felkoder finns i [referens dokumentationen](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Exempelsvar 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```