---
author: baanders
description: inkludera fil för att ladda upp en modell till Azure Digitals dubbla instanser
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950608"
---
Modellen ser ut så här:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Kör följande Azure CLI-kommando om du vill **överföra den här modellen till en-instansen**, som överför modellen ovan som inline-JSON. Du kan köra kommandot i [Azure Cloud Shell](../articles/cloud-shell/overview.md) i webbläsaren eller på din dator om du har CLI [installerat lokalt](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> Om du använder Cloud Shell i PowerShell-miljön kan du behöva undanta citat tecken i de infogade JSON-fälten för att deras värden ska kunna parsas korrekt. Här är kommandot för att överföra modellen med den här ändringen:
>
> Överförings modell:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```