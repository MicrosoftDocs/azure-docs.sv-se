---
title: Hämta åtkomstnyckel för en Event Grid resurs
description: Den här artikeln beskriver hur du får åtkomstnyckel för ett Event Grid ämne eller domän
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: cd60777b2e28b82d72f8f2bf93fe0be301e9e280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775231"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Hämta åtkomstnycklar för Event Grid resurser (ämnen eller domäner)
Åtkomstnycklar används för att autentisera programpubliceringshändelser för Azure Event Grid resurser (ämnen och domäner). Vi rekommenderar att du återskapar dina nycklar regelbundet och lagrar dem på ett säkert sätt. Du får två åtkomstnycklar så att du kan upprätthålla anslutningar med hjälp av en nyckel medan den andra återskapas.

Den här artikeln beskriver hur du hämtar åtkomstnycklar för Event Grid resurs (ämne eller domän) med hjälp Azure Portal, PowerShell eller CLI. 

## <a name="azure-portal"></a>Azure Portal
I Azure Portal du till fliken **Åtkomstnycklar** på **sidan Event Grid ämne** eller Event Grid **domän** för ditt ämne eller din domän.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Sidan Åtkomstnycklar":::

## <a name="azure-powershell"></a>Azure PowerShell
Använd kommandot [Get-AzEventGridTopicKey för](/powershell/module/az.eventgrid/get-azeventgridtopickey) att hämta åtkomstnycklar för ämnen. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Använd [kommandot Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) för att hämta åtkomstnycklar för domäner. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
Använd az [eventgrid topic key list för att](/cli/azure/eventgrid/topic/key#az_eventgrid_topic_key_list) hämta åtkomstnycklar för ämnen. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Använd [az eventgrid domain key list för](/cli/azure/eventgrid/domain/key#az_eventgrid_domain_key_list) att hämta åtkomstnycklar för domäner. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Nästa steg
Se följande artikel: Autentisera [publiceringsklienter.](security-authenticate-publishing-clients.md) 
