---
title: Lägga till anpassade resurser i Azure REST API
description: Lär dig hur du lägger till anpassade resurser i Azure-REST API. Den här artikeln går igenom kraven och bästa praxis för slut punkter som vill implementera anpassade resurser.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75650531"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Lägga till anpassade resurser i Azure REST API

Den här artikeln går igenom kraven och bästa praxis för att skapa Azure-slutpunkter för resurs leverantören som implementerar anpassade resurser. Om du inte är bekant med Azures anpassade resurs leverantörer kan du läsa [Översikt över anpassade resurs leverantörer](overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Definiera en resurs slut punkt

En **slut punkt** är en URL som pekar på en tjänst, som implementerar det underliggande kontraktet mellan det och Azure. Slut punkten definieras i den anpassade resurs leverantören och kan vara en offentligt tillgänglig URL. Exemplet nedan har en **resourceType** som heter `myCustomResource` implementerad av `endpointURL` .

Exempel på **ResourceProvider**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
        "routingType": "Proxy, Cache",
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

## <a name="building-a-resource-endpoint"></a>Skapa en resurs slut punkt

En **slut punkt** som implementerar en **resourceType** måste hantera begäran och svar för det nya API: et i Azure. När en anpassad resurs leverantör med en **resourceType** skapas, genererar den en ny uppsättning API: er i Azure. I det här fallet genererar **resourceType** en ny Azure Resource API för `PUT` , `GET` och `DELETE` för att utföra CRUD på en enskild resurs samt `GET` för att hämta alla befintliga resurser:

Ändra enskild resurs ( `PUT` , `GET` , och `DELETE` ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Hämta alla resurser ( `GET` ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

För anpassade resurser erbjuder anpassade resurs leverantörer två typer av **routingTypes**: " `Proxy` " och " `Proxy, Cache` ".

### <a name="proxy-routing-type"></a>typ av proxy-routning

" `Proxy` " **RoutingType** proxy alla begär ande metoder till den **slut punkt** som anges i den anpassade resurs leverantören. När du ska använda " `Proxy` ":

- Fullständig kontroll över svaret krävs.
- Integrera system med befintliga resurser.

Mer information om `Proxy` resurser finns i [den anpassade resursens proxy-referens](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>Dirigerings typ för proxy-cache

`Proxy, Cache`Endast **routingType** -proxy `PUT` och `DELETE` begär metoder till den **slut punkt** som anges i den anpassade resurs leverantören. Den anpassade resurs leverantören returnerar `GET` begär Anden automatiskt baserat på vad den har lagrat i cacheminnet. Om en anpassad resurs har marker ATS med cache, kommer den anpassade resurs leverantören också att lägga till/skriva över fält i svaret för att göra API: erna Azure-kompatibla. När du ska använda " `Proxy, Cache` ":

- Skapa ett nytt system som inte har några befintliga resurser.
- Arbeta med befintliga Azure-eko system.

Mer information om `Proxy, Cache` resurser finns i [den anpassade Resource cache-referensen](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Skapa en anpassad resurs

Det finns två huvudsakliga sätt att skapa en anpassad resurs av en anpassad resurs leverantör:

- Azure CLI
- Azure Resource Manager-mallar

### <a name="azure-cli"></a>Azure CLI

Skapa en anpassad resurs:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Parameter | Krävs | Beskrivning
---|---|---
är-fullständig-objekt | *Ja* | Anger att egenskaps-objektet innehåller andra alternativ, till exempel plats, taggar, SKU och/eller plan.
id | *Ja* | Resurs-ID för den anpassade resursen. Detta bör finnas utanför **ResourceProvider**
properties | *Ja* | Begär ande texten som ska skickas till **slut punkten**.

Ta bort en anpassad Azure-resurs:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parameter | Krävs | Beskrivning
---|---|---
id | *Ja* | Resurs-ID för den anpassade resursen. Detta bör finnas utanför **ResourceProvider**.

Hämta en anpassad Azure-resurs:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parameter | Krävs | Beskrivning
---|---|---
id | *Ja* | Resurs-ID för den anpassade resursen. Detta bör finnas utanför **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

> [!NOTE]
> Resurser kräver att svaret innehåller lämplig `id` , `name` och `type` från **slut punkten**.

Azure Resource Manager mallar kräver att `id` , `name` och `type` returneras korrekt från slut punkten. Ett returnerat resurs svar ska ha formatet:

Exempel på **slut punkts** svar:

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Exempel på Azure Resource Manager mall:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Parameter | Krävs | Beskrivning
---|---|---
resourceTypeName | *Ja* | **Namnet** på den **resurs** uppsättning som definierats i den anpassade providern.
resourceProviderName | *Ja* | Instans namn för den anpassade resurs leverantören.
customResourceName | *Ja* | Namnet på den anpassade resursen.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azures anpassade resurs leverantörer](overview.md)
- [Snabb start: skapa en anpassad resurs leverantör för Azure och distribuera anpassade resurser](./create-custom-provider.md)
- [Självstudie: skapa anpassade åtgärder och resurser i Azure](./tutorial-get-started-with-custom-providers.md)
- [Gör så här: lägga till anpassade åtgärder i Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Referens: referens för en anpassad resurs-proxy](proxy-resource-endpoint-reference.md)
- [Referens: anpassad Resource cache-referens](proxy-cache-resource-endpoint-reference.md)
