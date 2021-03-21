---
title: Hantera jakt-och liveström-frågor i Azure Sentinel med REST API | Microsoft Docs
description: I den här artikeln beskrivs hur du kan använda Azure Sentinel-funktionerna för att dra nytta av Log Analytics REST API för att hantera frågor om jakt och liveström.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 64f05e18ff757d9f086cf06d74109bf64e32a05c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795685"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>Hantera livestream- och jaktfrågor i Azure Sentinel med hjälp av REST API

Azure Sentinel, som bygger delvis på Azure Monitor Log Analytics, gör att du kan använda Log Analytics REST API för att hantera jakt-och liveström-frågor. Det här dokumentet visar hur du skapar och hanterar frågor om jakt med hjälp av REST API.  Frågor som skapas på det här sättet kommer att visas i användar gränssnittet för Azure Sentinel.

Se den slutgiltiga REST API referensen för mer information om [API: et för sparade sökningar](/rest/api/loganalytics/savedsearches).

## <a name="api-examples"></a>API-exempel

I följande exempel ersätter du plats hållarna med den ersättning som föreskrivs i följande tabell:

| Platshållare | Ersätt med |
|-|-|
| **SubscriptionId** | namnet på den prenumeration som du använder jakt-eller liveström-frågan till. |
| **ResourceGroupName** | namnet på den resurs grupp som du använder jakt-eller liveström-frågan till. |
| **{savedSearchId}** | ett unikt ID (GUID) för varje jakt fråga. |
| **WorkspaceName** | namnet på Log Analytics arbets ytan som är mål för frågan. |
| **DisplayName** | ett visnings namn som du väljer för frågan. |
| **Beteckning** | en beskrivning av jakt-eller liveström-frågan. |
| **Taktiker** | relevanta MITRE att&CK taktiker som gäller för frågan. |
| **Frågeterm** | frågans frågeuttryck. |
|  

### <a name="example-1"></a>Exempel 1

Det här exemplet visar hur du skapar eller uppdaterar en jakt fråga för en specifik Azure Sentinel-arbetsyta.  För en liveström-fråga ersätter du *"Category": "jakt frågor"* med *"Category": "liveström-frågor"* i **begär ande texten**: 

#### <a name="request-header"></a>Begärandehuvud

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>Begärandetext

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>Exempel 2

Det här exemplet visar hur du tar bort en jakt-eller liveström-fråga för en specifik Azure Sentinel-arbetsyta:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>Exempel 3

Det här exemplet visar dig att hämta en jakt-eller liveström-fråga för en specifik arbets yta:

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hanterar jakt-och liveström-frågor i Azure Sentinel med hjälp av Log Analytics API. Mer information om Azure Sentinel finns i följande artiklar:

- [Söker proaktivt efter hot](hunting.md)
- [Använd antecknings böcker för att köra automatiserade jakt kampanjer](notebooks.md)