---
title: Skapa referens för resursproxy
description: Anpassad Resource proxy-referens för Azure-anpassade resurs leverantörer. Den här artikeln går igenom kraven för slut punkter som implementerar Proxy-anpassade resurser.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650466"
---
# <a name="custom-resource-proxy-reference"></a>Referens för anpassad resurs-proxy

Den här artikeln går igenom kraven för slut punkter som implementerar Proxy-anpassade resurser. Om du inte är bekant med Azures anpassade resurs leverantörer kan du läsa [Översikt över anpassade resurs leverantörer](overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Definiera en resurs slut punkt för proxy

Du kan skapa en proxyvärd genom att ange **routingType** till "proxy".

Exempel på anpassad resurs leverantör:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
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

## <a name="building-proxy-resource-endpoint"></a>Skapar resurs slut punkt för proxy

En **slut punkt** som implementerar en "proxy"-resurs **slut punkt** måste hantera begäran och svar för det nya API: et i Azure. I det här fallet genererar **resourceType** ett nytt Azure Resource API för `PUT` , `GET` och `DELETE` för att utföra CRUD på en enskild resurs, samt `GET` för att hämta alla befintliga resurser.

> [!NOTE]
> `id`Fälten, `name` och krävs `type` inte, men behövs för att integrera den anpassade resursen med ett befintligt Azure-eko system.

Exempel resurs:

``` JSON
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

Parameter referens:

Egenskap | Exempel | Beskrivning
---|---|---
name | '{myCustomResourceName}' | Namnet på den anpassade resursen.
typ | Microsoft. CustomProviders/resourceProviders/{resourceTypeName} | Namn området för resurs typen.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | Resurs-ID.

### <a name="create-a-custom-resource"></a>Skapa en anpassad resurs

Inkommande Azure API-begäran:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

**Slut punkt** Svarade

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

- Giltigt JSON-objekt dokument. Alla matriser och strängar ska kapslas under ett översta objekt.
- `Content-Type`Rubriken ska vara inställd på "Application/JSON; charset = utf-8 ".

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

Den här begäran kommer sedan att vidarebefordras till **slut punkten** i formuläret:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

På samma sätt vidarebefordras svaret från **slut punkten** tillbaka till kunden. Svaret från slut punkten ska returnera:

- Ett giltigt JSON-objekt dokument. Alla matriser och strängar ska kapslas under ett översta objekt.
- `Content-Type`Rubriken ska vara inställd på "Application/JSON; charset = utf-8 ".

**Slut punkt** Svarade

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

Den här begäran kommer sedan att vidarebefordras till **slut punkten** i formuläret:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

På samma sätt vidarebefordras svaret från **slut punkten** tillbaka till kunden. Svaret från slut punkten ska returnera:

- Ett giltigt JSON-objekt dokument. Alla matriser och strängar ska kapslas under ett översta objekt.
- `Content-Type`Rubriken ska vara inställd på "Application/JSON; charset = utf-8 ".
- Listan över resurser ska placeras under den översta nivån `value` .

**Slut punkt** Svarade

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
- [Referens: anpassad Resource cache-referens](proxy-cache-resource-endpoint-reference.md)
