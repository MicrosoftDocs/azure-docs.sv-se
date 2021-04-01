---
title: Publicera händelser med händelse domäner med Azure Event Grid
description: Visar hur du hanterar stora uppsättningar av ämnen i Azure Event Grid och publicerar händelser till dem med hjälp av händelse domäner.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e6861e89def10eec391bf302b1ddc726b38bb98c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97109903"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Hantera ämnen och publicera händelser med hjälp av händelse domäner

Den här artikeln visar hur du:

* Skapa en Event Grid-domän
* Avsnitt om att prenumerera på Event Grid
* Lista nycklar
* Publicera händelser till en domän

Mer information om händelse domäner finns i [förstå händelse domäner för att hantera Event Grid ämnen](event-domains.md).

## <a name="create-an-event-domain"></a>Skapa en händelse domän

Skapa en händelse domän för att hantera stora avsnitts uppsättningar.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
New-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

Lyckad generering returnerar följande värden:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Observera att `endpoint` och `id` när de krävs för att hantera domänen och publicera händelser.

## <a name="manage-access-to-topics"></a>Hantera åtkomst till ämnen

Hantering av åtkomst till ämnen görs via [roll tilldelning](../role-based-access-control/role-assignments-cli.md). Roll tilldelningen använder Azure-rollbaserad åtkomst kontroll för att begränsa åtgärder på Azure-resurser till behöriga användare i ett visst omfång.

Event Grid har två inbyggda roller som du kan använda för att tilldela särskilda användare åtkomst till olika ämnen i en domän. De här rollerna är `EventGrid EventSubscription Contributor (Preview)` , som gör det möjligt att skapa och ta bort prenumerationer och `EventGrid EventSubscription Reader (Preview)` , som endast tillåter att lista över händelse prenumerationer.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
Följande Azure CLI-kommando begränsar `alice@contoso.com` för att skapa och ta bort händelse prenumerationer endast i avsnittet `demotopic1` :

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Följande PowerShell-kommando begränsar `alice@contoso.com` för att skapa och ta bort händelse prenumerationer endast i avsnittet `demotopic1` :

```azurepowershell-interactive
New-AzRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Mer information om hur du hanterar åtkomst för Event Grid åtgärder finns [Event Grid säkerhet och autentisering](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Skapa ämnen och prenumerationer

Event Grid tjänsten skapar och hanterar automatiskt motsvarande ämne i en domän baserat på anropet för att skapa en händelse prenumeration för ett domän ämne. Det finns inget separat steg för att skapa ett ämne i en domän. Även om den sista händelse prenumerationen för ett ämne tas bort, tas även avsnittet bort.

Att prenumerera på ett ämne i en domän är detsamma som att prenumerera på andra Azure-resurser. För käll resurs-ID anger du det händelse domän-ID som du returnerade när du skapade domänen tidigare. Om du vill ange det ämne som du vill prenumerera på lägger du till `/topics/<my-topic>` i slutet av käll resurs-ID: t. Om du vill skapa en händelse prenumeration för domän omfånget som tar emot alla händelser i domänen anger du händelsens domän-ID utan att ange några ämnen.

Användaren som du har beviljat åtkomst till i föregående avsnitt skulle vanligt vis skapa prenumerationen. För att förenkla den här artikeln skapar du prenumerationen. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Om du behöver en test slut punkt för att prenumerera på dina händelser till kan du alltid distribuera en [förskapad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar inkommande händelser. Du kan skicka dina händelser till din test webbplats på `https://<your-site-name>.azurewebsites.net/api/updates` .

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>

Behörigheter som har angetts för ett ämne lagras i Azure Active Directory och måste tas bort uttryckligen. Om du tar bort en händelse prenumeration återkallar inte en användare åtkomst till att skapa händelse prenumerationer om de har Skriv behörighet i ett ämne.


## <a name="publish-events-to-an-event-grid-domain"></a>Publicera händelser till en Event Grid domän

Publicering av händelser till en domän är detsamma som [att publicera till ett anpassat ämne](./post-to-custom-topic.md). Men i stället för att publicera till det anpassade ämnet publicerar du alla händelser till domänens slut punkt. I data för JSON-händelsen anger du det ämne som du vill att händelserna ska gå till. Följande händelse mat ris resulterar i händelse av `"id": "1111"` ämne `demotopic1` när händelse med `"id": "2222"` skulle skickas till ämnet `demotopic2` :

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
Använd för att hämta domän slut punkten med Azure CLI

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Använd följande för att hämta nycklar för en domän:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Använd för att hämta domän slut punkten med PowerShell

```azurepowershell-interactive
Get-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Använd följande för att hämta nycklar för en domän:

```azurepowershell-interactive
Get-AzEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

Och Använd sedan din favorit metod för att göra ett HTTP-inlägg för att publicera dina händelser till din Event Grid-domän.

## <a name="search-lists-of-topics-or-subscriptions"></a>Sök i listor med ämnen eller prenumerationer

Om du vill söka efter och hantera ett stort antal ämnen eller prenumerationer Event Grid programmets API: er som stöder listor och sid brytning.

### <a name="using-cli"></a>Använda CLI
Följande kommando visar till exempel alla ämnen med namn som innehåller `mytopic` . 

```azurecli-interactive
az eventgrid topic list --odata-query "contains(name, 'mytopic')"
```

Mer information om det här kommandot finns i [`az eventgrid topic list`](/cli/azure/eventgrid/topic?#az_eventgrid_topic_list) . 


## <a name="next-steps"></a>Nästa steg

* Mer information om koncept på hög nivå i händelse domäner och varför de är användbara finns i Översikt över [händelse domäner](event-domains.md).
