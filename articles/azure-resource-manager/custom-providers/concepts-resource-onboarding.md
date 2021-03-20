---
title: Registrering av resurser
description: Lär dig mer om hur du utför resurs registrering genom att använda Azure-anpassade providers för att tillämpa hantering eller konfiguration på andra typer av Azure-resurser.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650414"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Översikt över resurs onboarding för Azure Custom providers

Azure Custom providers-resurs onboarding är en utöknings modell för resurs typer i Azure. Det gör att du kan använda åtgärder eller hantering över befintliga Azure-resurser i stor skala. Mer information finns i [så här kan Azure-anpassade leverantörer utöka Azure](overview.md). I den här artikeln beskrivs:

- Vilken resurs onboarding kan göra.
- Grundläggande om resurs registrering och hur du använder det.
- Var du hittar guider och kod exempel för att komma igång.

> [!IMPORTANT]
> Anpassade providers är för närvarande en offentlig för hands version.
> Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar den inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan ha begränsade funktioner.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Vad kan en resurs onboarding göra?

På samma sätt som med [anpassade Azure-anpassade providers-resurser](./custom-providers-resources-endpoint-how-to.md)definieras ett kontrakt som skickar "onboarding"-begär anden till en slut punkt i resurs onboarding. Till skillnad från anpassade resurser skapar inte resurs onboarding en ny resurs typ. I stället tillåts tillägget av befintliga resurs typer. Och resurs onboarding fungerar med Azure Policy, så hantering och konfiguration av resurser kan göras i stor skala. Några exempel på resurs onboarding-arbetsflöden:

- Installera och hantera till tillägg för virtuella datorer.
- Ladda upp och konfigurera standardvärden på Azure Storage-konton.
- Aktivera inställningar för bas linje diagnos i skala.

## <a name="resource-onboarding-basics"></a>Grunder för resurs registrering

Du konfigurerar resurs onboarding via Azure-anpassade providers med hjälp av resurs typerna Microsoft. CustomProviders/resourceProviders och Microsoft. CustomProviders/associationer. Om du vill aktivera resurs registrering för en anpassad Provider, under konfigurations processen, skapar du en **resourceType** med namnet "associationer" med en **routingType** som innehåller "tillägg". Microsoft. CustomProviders/associationer och Microsoft. CustomProviders/resourceProviders behöver inte tillhöra samma resurs grupp.

Här är ett exempel på en anpassad Azure-provider:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Egenskap | Obligatoriskt? | Beskrivning
---|---|---
name | Ja | Namnet på slut punkts definitionen. För resurs onboarding måste namnet vara "associationer".
routingType | Ja | Bestämmer typen av kontrakt med slut punkten. För resurs onboarding är giltiga **routingTypes** "proxy, cache, Extension" och "webhook, cache, Extension".
slutpunkt | Ja | Slut punkten för att dirigera begär anden till. Detta hanterar svaret och eventuella sido effekter i begäran.

När du har skapat den anpassade providern med resurs typen associationer kan du använda Microsoft. CustomProviders/associationer som mål. Microsoft. CustomProviders/associationer är en tilläggs resurs som kan utöka andra Azure-resurser. När en instans av Microsoft. CustomProviders/associationer skapas, tar det en egenskaps- **targetResourceId**, som ska vara ett giltigt resurs-ID för Microsoft. CustomProviders/ResourceProviders eller Microsoft. Solutions/Applications. I dessa fall vidarebefordras begäran till resurs typen kopplingar på den Microsoft. CustomProviders/resourceProviders-instans som du skapade.

> [!NOTE]
> Om ett resurs-ID för Microsoft. Solutions/program tillhandahålls som **targetResourceId**, måste det finnas en Microsoft. CustomProviders/resourceProviders distribuerad i den hanterade resurs gruppen med namnet "offentlig".

Exempel på Azure-anpassade providers-Association:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Egenskap | Obligatoriskt? | Beskrivning
---|---|---
targetResourceId | Ja | Resurs-ID för Microsoft. CustomProviders/resourceProviders eller Microsoft. Solutions/Applications.

## <a name="how-to-use-resource-onboarding"></a>Så här använder du resurs registrering

Resurs onboarding fungerar genom att utöka andra resurser med tilläggs resursen Microsoft. CustomProviders/associationer. I följande exempel görs begäran för en virtuell dator, men alla resurser kan utökas.

Först måste du skapa en anpassad Provider-resurs med resurs typen associationer. Detta deklarerar återanrops-URL: en som kommer att användas när en motsvarande Microsoft. CustomProviders/Associations resurs skapas, vilket riktar sig till den anpassade providern.

Exempel på Microsoft. CustomProviders/resourceProviders Create förfrågan:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

När du har skapat den anpassade providern kan du rikta andra resurser och använda sido effekterna av den anpassade providern.

Exempel på Microsoft. CustomProviders/Associations Create förfrågan:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Den här begäran vidarebefordras sedan till den slut punkt som anges i den anpassade providern som du skapade, som refereras till av **targetResourceId** i det här formuläret:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Slut punkten bör svara med ett program/JSON `Content-Type` och en giltig JSON-svars text. Fält som returneras under JSON- **objektet läggs** till i associationens retur svar.

## <a name="getting-help"></a>Få hjälp

Om du har frågor om utveckling av Azure-anpassade resurs leverantörer kan du prova att be dem om [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). En liknande fråga kanske redan har besvarats. kontrol lera först innan du publicerar. Lägg till taggen ```azure-custom-providers``` för att få ett snabbt svar!

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om anpassade leverantörer. Mer information finns i de här artiklarna:

- [Självstudie: resurs onboarding med anpassade providers](./tutorial-resource-onboarding.md)
- [Självstudie: skapa anpassade åtgärder och resurser i Azure](./tutorial-get-started-with-custom-providers.md)
- [Snabb start: skapa en anpassad resurs leverantör och distribuera anpassade resurser](./create-custom-provider.md)
- [Gör så här: lägga till anpassade åtgärder i en Azure-REST API](./custom-providers-action-endpoint-how-to.md)
- [Gör så här: lägga till anpassade resurser i en Azure-REST API](./custom-providers-resources-endpoint-how-to.md)
