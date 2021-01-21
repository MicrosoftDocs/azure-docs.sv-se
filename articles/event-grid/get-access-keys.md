---
title: Hämta åtkomst nyckel för en Event Grid resurs
description: Den här artikeln beskriver hur du får åtkomst nyckeln för ett Event Grid ämne eller en domän
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: a642affbac79766684dc75a37dae0373450d20e8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632537"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Få åtkomst nycklar för Event Grid resurser (ämnen eller domäner)
Åtkomst nycklar används för att autentisera ett program publicerings händelser för att Azure Event Grid resurser (ämnen och domäner). Vi rekommenderar att du återskapar dina nycklar regelbundet och sparar dem på ett säkert sätt. Du får två åtkomst nycklar så att du kan underhålla anslutningar med en nyckel när du återskapar den.

Den här artikeln beskriver hur du får åtkomst nycklar för en Event Grid resurs (ämne eller domän) med hjälp av Azure Portal, PowerShell eller CLI. 

## <a name="azure-portal"></a>Azure Portal
I Azure Portal växlar du till fliken **åtkomst nycklar** i **avsnittet Event Grid** eller **Event Grid domän** för ämnet eller domänen.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Sidan åtkomst nycklar":::

## <a name="azure-powershell"></a>Azure PowerShell
Använd kommandot [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey) för att få åtkomst nycklar för ämnen. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Använd [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) -kommandot för att få åtkomst nycklar för domäner. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
Använd [AZ eventgrid ämnes nyckel lista](/cli/azure/eventgrid/topic/key#az-eventgrid-topic-key-list) för att få åtkomst nycklar för ämnen. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Använd [AZ eventgrid-domän nyckel lista](/cli/azure/eventgrid/domain/key#az-eventgrid-domain-key-list) för att få åtkomst nycklar för domäner. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Nästa steg
Se följande artikel: [autentisera publicerings klienter](security-authenticate-publishing-clients.md). 
