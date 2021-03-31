---
title: Skapa och Använd en anpassad Provider
description: Den här självstudien visar hur du skapar och använder en anpassad Azure-Provider. Använd anpassade providers för att ändra arbets flöden i Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4f425af7681b666b42fbcc70ac0e4c31d9df6d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87503760"
---
# <a name="create-and-use-a-custom-provider"></a>Skapa och Använd en anpassad Provider

En anpassad Provider är ett kontrakt mellan Azure och en slut punkt. Med anpassade providers kan du ändra arbets flöden i Azure. Den här självstudien visar hur du skapar en anpassad Provider. Om du inte är bekant med Azure-anpassade leverantörer kan du läsa [Översikt över Azures anpassade resurs leverantörer](overview.md).

## <a name="create-a-custom-provider"></a>Skapa en anpassad Provider

> [!NOTE]
> Den här självstudien visar inte hur du skapar en slut punkt. Om du inte har en RESTFUL-slutpunkt följer du [själv studie kursen om hur du redigerar RESTful-slutpunkter](./tutorial-custom-providers-function-authoring.md), som är grunden för den aktuella självstudien.

När du har skapat en slut punkt kan du skapa en anpassad Provider för att generera ett kontrakt mellan providern och slut punkten. Med en anpassad Provider kan du ange en lista över slut punkts definitioner:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Egenskap | Krävs | Beskrivning
---|---|---
**Namn** | Ja | Namnet på slut punkts definitionen. Azure exponerar det här namnet via dess API under/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType** | Inga | Slut punktens kontrakt typ. Om värdet inte anges används "proxy" som standard.
**Endpoint** | Ja | Slut punkten för att dirigera begär anden till. Den här slut punkten hanterar svaret och eventuella sido effekter i begäran.

Värdet för **slut punkten** är Utlösar-URL: en för Azure Function-appen. `<yourapp>` `<funcname>` `<functionkey>` Plats hållarna, och måste ersättas med värden för den skapade Function-appen.

## <a name="define-custom-actions-and-resources"></a>Definiera anpassade åtgärder och resurser

Den anpassade providern innehåller en lista över slut punkts definitioner som modelleras under egenskaperna **Actions** och **resourceTypes** . Egenskapen **åtgärder** mappar till de anpassade åtgärder som exponeras av den anpassade providern och egenskapen **resourceTypes** är anpassade resurser. I den här självstudien har den anpassade providern en **Actions** -egenskap med namnet `myCustomAction` och en **resourceTypes** -egenskap med namnet `myCustomResources` .

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

## <a name="deploy-the-custom-provider"></a>Distribuera den anpassade providern

> [!NOTE]
> Du måste ersätta **slut punkts** värden med Utlösar-URL: en från Function-appen som skapades i föregående självstudie.

Du kan distribuera den tidigare anpassade providern med hjälp av en Azure Resource Manager mall:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="use-custom-actions-and-resources"></a>Använda anpassade åtgärder och resurser

När du har skapat en anpassad Provider kan du använda de nya Azure-API: erna. Följande flikar förklarar hur du anropar och använder en anpassad Provider.

### <a name="custom-actions"></a>Anpassade åtgärder

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Du måste ersätta `{subscriptionId}` `{resourceGroupName}` plats hållarna och med den prenumeration och resurs grupp där du har distribuerat den anpassade providern.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parameter | Krävs | Beskrivning
---|---|---
*tgärd* | Ja | Namnet på åtgärden som definierats i den anpassade providern
*kompatibilitet* | Ja | Resurs-ID för den anpassade providern
*Request-Body* | Inga | Begär ande texten som ska skickas till slut punkten

# <a name="template"></a>[Mall](#tab/template)

Inga.

---

### <a name="custom-resources"></a>Anpassade resurser

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Du måste ersätta `{subscriptionId}` `{resourceGroupName}` plats hållarna och med den prenumeration och resurs grupp där du har distribuerat den anpassade providern.

#### <a name="create-a-custom-resource"></a>Skapa en anpassad resurs

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Parameter | Krävs | Beskrivning
---|---|---
*är-fullständig-objekt* | Ja | Anger om egenskaps-objektet innehåller andra alternativ som plats, taggar, SKU eller abonnemang.
*id* | Ja | Resurs-ID för den anpassade resursen. Detta ID är ett tillägg till resurs-ID för den anpassade providern.
*egenskaperna* | Ja | Begär ande texten som ska skickas till slut punkten.

#### <a name="delete-a-custom-resource"></a>Ta bort en anpassad resurs

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Krävs | Beskrivning
---|---|---
*id* | Ja | Resurs-ID för den anpassade resursen. Detta ID är ett tillägg till resurs-ID för den anpassade providern.

#### <a name="retrieve-a-custom-resource"></a>Hämta en anpassad resurs

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Krävs | Beskrivning
---|---|---
*id* | Ja | Resurs-ID för den anpassade resursen. Detta ID är ett tillägg till resurs-ID för den anpassade providern.

# <a name="template"></a>[Mall](#tab/template)

Exempel på en Resource Manager-mall:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Parameter | Krävs | Beskrivning
---|---|---
*resourceTypeName* | Ja | `name`Värdet för egenskapen **resourceTypes** som definierats i den anpassade providern.
*resourceProviderName* | Ja | Instans namn för den anpassade providern.
*customResourceName* | Ja | Namnet på den anpassade resursen.

---

> [!NOTE]
> När du har slutfört distributionen och använder den anpassade providern måste du komma ihåg att rensa alla skapade resurser, inklusive Azure Function-appen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om anpassade leverantörer. Mer information finns i:

- [Gör så här: lägga till anpassade åtgärder i Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Gör så här: lägga till anpassade resurser i Azure REST API](./custom-providers-resources-endpoint-how-to.md)
