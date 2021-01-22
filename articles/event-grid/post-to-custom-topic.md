---
title: Avsnittet publicera händelse i anpassade Azure Event Grid
description: Den här artikeln beskriver hur du publicerar en händelse i ett anpassat ämne. Den visar formatet på post-och händelse data.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ed126487938e524264c94544903460854ffc4d41
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681625"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Publicera till anpassat avsnitt för Azure Event Grid

Den här artikeln beskriver hur du publicerar en händelse i ett anpassat ämne. Den visar formatet på post-och händelse data. [Serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) gäller endast för inlägg som matchar det förväntade formatet.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Slutpunkt

När du skickar HTTP-inlägget till ett anpassat ämne, använder du URI-formatet: `https://<topic-endpoint>?api-version=2018-01-01` .

En giltig URI är till exempel: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .

Om du vill hämta slut punkten för ett anpassat ämne med Azure CLI använder du:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Om du vill hämta slut punkten för ett anpassat ämne med Azure PowerShell använder du:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Huvud

I begäran inkluderar du ett huvud värde med namnet `aeg-sas-key` som innehåller en nyckel för autentisering.

Ett giltigt huvud värde är till exempel `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==` .

Använd följande för att hämta nyckeln för ett anpassat ämne med Azure CLI:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Använd följande för att hämta nyckeln för ett anpassat ämne med PowerShell:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Händelsedata

För anpassade ämnen innehåller data på översta nivån samma fält som standard resurs definierade händelser. En av dessa egenskaper är en data egenskap som innehåller egenskaper som är unika för det anpassade ämnet. Som händelse utgivare bestämmer du egenskaperna för det data objektet. Använd följande schema:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

En beskrivning av dessa egenskaper finns i [Azure Event Grid Event schema](event-schema.md). När du publicerar händelser i ett event Grid-ämne kan matrisen ha en total storlek på upp till 1 MB. Den största tillåtna storleken för en händelse är också 1 MB. Händelser över 64 KB debiteras i steg om 64 KB. 

Ett giltigt händelse data schema är till exempel:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Svarsåtgärder

När du har bokfört till ämnes slut punkten får du ett svar. Svaret är en standard-HTTP-svarskod. Några vanliga svar är:

|Resultat  |Svarsåtgärder  |
|---------|---------|
|Klart  | 200 OK  |
|Felaktigt format för händelse data | 400 – Felaktig begäran |
|Ogiltig åtkomst nyckel | 401 – Ej behörig |
|Felaktig slut punkt | 404 – Hittades inte |
|Matris eller händelse överskrider storleks gränser | 413-nyttolasten är för stor |

För fel har meddelande texten följande format:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Nästa steg

* Information om övervakning av händelse leveranser finns i [övervaka Event Grid meddelande leverans](monitor-event-delivery.md).
* Mer information om nyckeln för autentisering finns i [Event Grid säkerhet och autentisering](security-authentication.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
