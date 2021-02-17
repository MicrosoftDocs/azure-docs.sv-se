---
title: resurs () uttryck i Azure Monitor logg fråga | Microsoft Docs
description: Resurs uttrycket används i en resurs-inriktad Azure Monitor logg fråga för att hämta data från flera resurser.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624965"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>resurs () uttryck i Azure Monitor logg fråga

`resource`Uttrycket används i en Azure Monitor-fråga som är [begränsad till en resurs](scope.md#query-scope) för att hämta data från andra resurser. 


## <a name="syntax"></a>Syntax

`resource(`*Beteckning*`)`

## <a name="arguments"></a>Argument

- *Identifierare*: resurs-ID för en resurs.

| Identifierare | Beskrivning | Exempel
|:---|:---|:---|
| Resurs | Innehåller data för resursen. | resurs ("/Subscriptions/xxxxxxx-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup/providers/Microsoft.Compute/virtualmachines/myvm") |
| Resurs grupp eller prenumeration | Innehåller data för resursen och alla resurser som den innehåller.  | resurs ("/Subscriptions/xxxxxxx-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Kommentarer

* Du måste ha Läs behörighet till resursen.


## <a name="examples"></a>Exempel

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Nästa steg

- Mer information om ett fråge omfång finns [i omfånget och tidsintervallet för logg frågor i Azure Monitor Log Analytics](scope.md) .
- Få fullständig dokumentation för [Kusto-frågespråket](/azure/kusto/query/).
