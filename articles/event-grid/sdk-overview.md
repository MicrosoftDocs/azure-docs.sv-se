---
title: 'Azure Event Grid SDK: er'
description: 'Beskriver SDK: er för Azure Event Grid. Dessa SDK: er ger hantering, publicering och konsumtion.'
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: aa53aaa89a703bb88df6611cdb0dd38341248a55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955932"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Event Grid SDK: er för hantering och publicering

Event Grid innehåller SDK: er som gör att du kan hantera dina resurser och publicera händelser via programmering.

## <a name="management-sdks"></a>Hanterings-SDK: er

Med SDK: er för hantering kan du skapa, uppdatera och ta bort event Grid-ämnen och-prenumerationer. För närvarande är följande SDK: er tillgängliga:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Kör](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/@azure/arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>SDK: er för data plan

Med data Plans-SDK: erna kan du publicera händelser till ämnen genom att ta hand om autentisering, bilda händelsen och asynkront publicera till den angivna slut punkten. De gör det också möjligt att använda första part händelser. För närvarande är följande SDK: er tillgängliga:

| Programmeringsspråk | SDK | 
| -------------------- | ---------- | 
| .NET | Stabil SDK: [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)<p>Preview SDK: [Azure. Messaging. EventGrid](https://www.nuget.org/packages/Azure.Messaging.EventGrid/) |
| Java | Stabil SDK: [Azure-eventgrid](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)<p>Preview SDK: [Azure-Messaging – eventgrid](https://search.maven.org/artifact/com.azure/azure-messaging-eventgrid/)</p> |  
| Python | [Azure-eventgrid](https://pypi.org/project/azure-eventgrid/#history) (se de senaste stabila och för hands versionerna på sidan **versions historik** ) |
| JavaScript | [@azure/eventgrid](https://www.npmjs.com/package/@azure/eventgrid/) (växla till fliken **versioner** för att se de senaste stabila och beta versions paketen). | 
| Go | [Azure SDK för Go](https://github.com/Azure/azure-sdk-for-go) |
| Ruby | [azure_event_grid](https://rubygems.org/gems/azure_event_grid) |


## <a name="next-steps"></a>Nästa steg

* Exempel program finns i [Event Grid kod exempel](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* En introduktion till Event Grid finns i [Vad är event Grid?](overview.md)
* Event Grid-kommandon i Azure CLI finns i [Azure CLI](/cli/azure/eventgrid).
* För Event Grid kommandon i PowerShell, se [PowerShell](/powershell/module/az.eventgrid).
