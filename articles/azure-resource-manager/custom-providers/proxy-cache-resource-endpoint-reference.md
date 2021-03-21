---
title: Skapa referens för resurscache
description: Anpassad Resource cache-referens för Azure-anpassade resurs leverantörer. Den här artikeln går igenom kraven för slut punkter som implementerar cache-anpassade resurser.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650388"
---
# <a name="custom-resource-cache-reference"></a>Anpassad Resource cache-referens

Den här artikeln går igenom kraven för slut punkter som implementerar cache-anpassade resurser. Om du inte är bekant med Azures anpassade resurs leverantörer kan du läsa [Översikt över anpassade resurs leverantörer](overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>Definiera en slut punkt för en cache-resurs

Du kan skapa en proxyadress genom att ange **routingType** till "proxy, cache".

Exempel på anpassad resurs leverantör:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
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

## <a name="building-proxy-resource-endpoint"></a>Skapar resurs slut punkt för proxy

En **slut punkt** som implementerar en "proxy, cache"-resurs **slut punkt** måste hantera begäran och svara på det nya API: et i Azure. I det här fallet genererar **resourceType** ett nytt Azure Resource API för `PUT` , `GET` och `DELETE` för att utföra CRUD på en enskild resurs, samt `GET` för att hämta alla befintliga resurser:

> [!NOTE]
> Azure API genererar förfrågnings metoderna `PUT` , `GET` och `DELETE` , men cache- **slutpunkten** behöver bara hantera `PUT` och `DELETE` .
> Vi rekommenderar också att **slut punkten** också implementerar `GET` .

### <a name="create-a-custom-resource"></a>Skapa en anpassad resurs

Inkommande Azure API-begäran:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Den här begäran kommer sedan att vidarebefordras till **slut punkten** i formuläret:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

På samma sätt vidarebefordras svaret från **slut punkten** tillbaka till kunden. Svaret från slut punkten ska returnera:

- Ett giltigt JSON-objekt dokument. Alla matriser och strängar ska kapslas under ett översta objekt.
- `Content-Type`Rubriken ska vara inställd på "Application/JSON; charset = utf-8 ".
- Den anpassade resurs leverantören kommer att skriva över `name` `type` fälten,, och `id` för begäran.
- Den anpassade resurs leverantören kommer bara att returnera fält under `properties` objektet för en cache-slutpunkt.

**Slut punkt** Svarade

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

`name`Fälten, `id` och `type` kommer automatiskt att genereras för den anpassade resursen av den anpassade resurs leverantören.

Svar från Azures anpassade resurs leverantör:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Ta bort en anpassad resurs

Inkommande Azure API-begäran:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Den här begäran kommer sedan att vidarebefordras till **slut punkten** i formuläret:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

På samma sätt vidarebefordras svaret från **slut punkten** tillbaka till kunden. Svaret från slut punkten ska returnera:

- Ett giltigt JSON-objekt dokument. Alla matriser och strängar ska kapslas under ett översta objekt.
- `Content-Type`Rubriken ska vara inställd på "Application/JSON; charset = utf-8 ".
- Den anpassade Azure-providern tar bara bort objektet från cachen om ett svar på 200 nivå returneras. Även om resursen inte finns ska **slut punkten** returnera 204.

**Slut punkt** Svarade

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Svar från Azures anpassade resurs leverantör:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Hämta en anpassad resurs

Inkommande Azure API-begäran:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Begäran kommer **inte** att vidarebefordras till **slut punkten**.

Svar från Azures anpassade resurs leverantör:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Räkna upp alla anpassade resurser

Inkommande Azure API-begäran:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Den här begäran kommer **inte** att vidarebefordras till **slut punkten**.

Svar från Azures anpassade resurs leverantör:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azures anpassade resurs leverantörer](overview.md)
- [Snabb start: skapa en anpassad resurs leverantör för Azure och distribuera anpassade resurser](./create-custom-provider.md)
- [Självstudie: skapa anpassade åtgärder och resurser i Azure](./tutorial-get-started-with-custom-providers.md)
- [Gör så här: lägga till anpassade åtgärder i Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Referens: referens för en anpassad resurs-proxy](proxy-resource-endpoint-reference.md)
