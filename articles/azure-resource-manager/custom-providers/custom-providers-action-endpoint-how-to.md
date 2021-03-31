---
title: Lägga till anpassade åtgärder i Azure REST API
description: Lär dig hur du lägger till anpassade åtgärder i Azure-REST API. Den här artikeln går igenom kraven och bästa praxis för slut punkter som vill implementera anpassade åtgärder.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650401"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Lägga till anpassade åtgärder i Azure REST API

Den här artikeln går igenom kraven och bästa praxis för att skapa Azure-slutpunkter för resurs leverantören som implementerar anpassade åtgärder. Om du inte är bekant med Azures anpassade resurs leverantörer kan du läsa [Översikt över anpassade resurs leverantörer](overview.md).

## <a name="how-to-define-an-action-endpoint"></a>Definiera en åtgärds slut punkt

En **slut punkt** är en URL som pekar på en tjänst, som implementerar det underliggande kontraktet mellan det och Azure. Slut punkten definieras i den anpassade resurs leverantören och kan vara en offentligt tillgänglig URL. Exemplet nedan har en **åtgärd** som kallas `myCustomAction` implementerad av `endpointURL` .

Exempel på **ResourceProvider**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>Skapa en åtgärds slut punkt

En **slut punkt** som implementerar en **åtgärd** måste hantera begäran och svar för det nya API: et i Azure. När en anpassad resurs leverantör med en **åtgärd** skapas, kommer den att generera en ny uppsättning API: er i Azure. I det här fallet genererar åtgärden ett nytt Azure Action API för `POST` anrop:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Inkommande Azure API-begäran:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Den här begäran kommer sedan att vidarebefordras till **slut punkten** i formuläret:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

På samma sätt vidarebefordras svaret från **slut punkten** tillbaka till kunden. Svaret från slut punkten ska returnera:

- Ett giltigt JSON-objekt dokument. Alla matriser och strängar ska kapslas under ett översta objekt.
- `Content-Type`Rubriken ska vara inställd på "Application/JSON; charset = utf-8 ".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Svar från Azures anpassade resurs leverantör:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Anropa en anpassad åtgärd

Det finns två huvudsakliga sätt att anropa en anpassad åtgärd av en anpassad resurs leverantör:

- Azure CLI
- Azure Resource Manager-mallar

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parameter | Krävs | Beskrivning
---|---|---
åtgärd | *Ja* | Namnet på åtgärden som definierats i **ResourceProvider**.
kompatibilitet | *Ja* | Resurs-ID för **ResourceProvider**.
Request-Body | *Nej* | Begär ande texten som ska skickas till **slut punkten**.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

> [!NOTE]
> Åtgärder har begränsat stöd i Azure Resource Manager mallar. För att åtgärden ska kunna anropas inuti en mall måste den innehålla [`list`](../templates/template-functions-resource.md#list) prefixet i sitt namn.

Exempel på **ResourceProvider** med list åtgärd:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Exempel på Azure Resource Manager mall:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parameter | Krävs | Beskrivning
---|---|---
resourceIdentifier | *Ja* | Resurs-ID för **ResourceProvider**.
apiVersion | *Ja* | API-versionen för resurs körningen. Detta bör alltid vara "2018-09-01-för hands version".
functionValues | *Nej* | Begär ande texten som ska skickas till **slut punkten**.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azures anpassade resurs leverantörer](overview.md)
- [Snabb start: skapa en anpassad resurs leverantör för Azure och distribuera anpassade resurser](./create-custom-provider.md)
- [Självstudie: skapa anpassade åtgärder och resurser i Azure](./tutorial-get-started-with-custom-providers.md)
- [Gör så här: lägga till anpassade resurser i Azure REST API](./custom-providers-resources-endpoint-how-to.md)
