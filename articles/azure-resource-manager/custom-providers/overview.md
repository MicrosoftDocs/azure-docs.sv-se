---
title: Översikt över anpassade providers
description: Lär dig mer om Azures anpassade resurs leverantörer och hur du utökar Azure API-planet så att det passar dina arbets flöden.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80398466"
---
# <a name="azure-custom-resource-providers-overview"></a>Översikt över Azures anpassade resurs leverantörer

Azures anpassade resurs leverantörer är en utöknings bara plattform till Azure. Det gör att du kan definiera anpassade API: er som kan användas för att utöka Azures standard upplevelse. I den här dokumentationen beskrivs:

- Så här skapar och distribuerar du en Azure-anpassad resurs leverantör.
- Så här använder du Azure-anpassade resurs leverantörer för att utöka befintliga arbets flöden.
- Var du hittar guider och kod exempel för att komma igång.

![Översikt över anpassad Provider](./media/overview/overview.png)

> [!IMPORTANT]
> Anpassade providers är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Vad kan anpassade resurs leverantörer göra

Här följer några exempel på vad du kan uppnå med Azures anpassade resurs leverantörer:

- Utöka Azure Resource Manager REST API för att inkludera interna och externa tjänster.
- Aktivera anpassade scenarier ovanpå befintliga Azure-arbetsflöden.
- Anpassa Azure Resource Manager templates-kontroller och-effekter.

## <a name="what-is-a-custom-resource-provider"></a>Vad är en anpassad resurs leverantör

Azures anpassade resurs leverantörer görs genom att skapa ett kontrakt mellan Azure och en slut punkt. Detta kontrakt definierar en lista över nya resurser och åtgärder via en ny resurs, **Microsoft. CustomProviders/resourceProviders**. Den anpassade resurs leverantören kommer sedan att exponera dessa nya API: er i Azure. Azures anpassade resurs leverantörer består av tre delar: anpassad Resource Provider, **slut punkter** och anpassade resurser.

## <a name="how-to-build-custom-resource-providers"></a>Så här skapar du anpassade resurs leverantörer

Anpassade resurs leverantörer är en lista över kontrakt mellan Azure och slut punkter. Detta avtal beskriver hur Azure ska interagera med en slut punkt. Resurs leverantören fungerar som en proxy och vidarebefordrar begär Anden och svar till och från den angivna **slut punkten**. En resurs leverantör kan ange två typer av kontrakt: [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) och [**åtgärder**](./custom-providers-action-endpoint-how-to.md). Dessa är aktiverade genom slut punkts definitioner. En slut punkts definition består av tre fält: **Name**, **routingType** och **Endpoint**.

Exempel slut punkt:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Egenskap | Krävs | Beskrivning
---|---|---
name | *Ja* | Namnet på slut punkts definitionen. Azure kommer att exponera detta namn via dess API under "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *Nej* | Bestämmer kontrakts typen med **slut punkten**. Om inget värde anges används proxy för standardvärdet.
slutpunkt | *Ja* | Slut punkten för att dirigera begär anden till. Detta hanterar svaret och eventuella sido effekter i begäran.

### <a name="building-custom-resources"></a>Skapa anpassade resurser

**ResourceTypes** beskriver nya anpassade resurser som läggs till i Azure. Dessa visar Basic RESTful CRUD-metoder. Läs [mer om att skapa anpassade resurser](./custom-providers-resources-endpoint-how-to.md)

Exempel på anpassad resurs leverantör med **resourceTypes**:

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
  "location": "eastus"
}
```

API: er som lagts till i Azure för exemplet ovan:

HttpMethod | Exempel-URI | Beskrivning
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-version = 2018-09 01 – för hands version | Azure REST API-anropet för att skapa en ny resurs.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-version = 2018-09 01 – för hands version | Azure REST API-anrop för att ta bort en befintlig resurs.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-version = 2018-09 01 – för hands version | Azure REST API-anropet för att hämta en befintlig resurs.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources? API-version = 2018-09 01 – för hands version | Azure REST API-anropet för att hämta listan över befintliga resurser.

### <a name="building-custom-actions"></a>Skapa anpassade åtgärder

**Åtgärder** beskriver nya åtgärder som läggs till i Azure. Dessa kan exponeras ovanpå resurs leverantören eller kapslas under en **resourceType**. Läs [mer om att skapa anpassade åtgärder](./custom-providers-action-endpoint-how-to.md)

Exempel på anpassad resurs leverantör med **åtgärder**:

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
  "location": "eastus"
}
```

API: er som lagts till i Azure för exemplet ovan:

HttpMethod | Exempel-URI | Beskrivning
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction? API-version = 2018-09 01 – för hands version | Azure REST API-anropet för att aktivera åtgärden.

## <a name="looking-for-help"></a>Söker efter hjälp

Om du har frågor om utveckling av Azure anpassad resurs leverantör kan du prova med att ange [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). En liknande fråga kanske redan har blivit ombeddd och besvarad, så kontrol lera först innan du publicerar. Lägg till taggen ```azure-custom-providers``` för att få ett snabbt svar!

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om anpassade leverantörer. Gå till nästa artikel om du vill skapa en anpassad Provider.

- [Snabb start: skapa en anpassad resurs leverantör för Azure och distribuera anpassade resurser](./create-custom-provider.md)
- [Självstudie: skapa anpassade åtgärder och resurser i Azure](./tutorial-get-started-with-custom-providers.md)
- [Gör så här: lägga till anpassade åtgärder i Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Gör så här: lägga till anpassade resurser i Azure REST API](./custom-providers-resources-endpoint-how-to.md)
