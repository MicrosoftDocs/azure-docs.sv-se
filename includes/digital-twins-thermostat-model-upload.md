---
author: baanders
description: inkludera fil för att ladda upp en modell till Azure Digitals dubbla instanser
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304311"
---
Modellen ser ut så här:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Kör följande Azure CLI-kommando om du vill **överföra den här modellen till en-instansen**, som överför modellen ovan som inline-JSON. Du kan köra kommandot i [Azure Cloud Shell](../articles/cloud-shell/overview.md) i webbläsaren (Använd **bash** -miljön) eller på din dator om du har CLI [installerat lokalt](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```