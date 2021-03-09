---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 0eeb82245a53c93af75fc3ce3f37cb588295e5b7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508103"
---
Posterna i `deploymentconfig.json` dokument översikten till parametrarna för [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration). I följande tabell beskrivs mappningen mellan entiteterna i JSON-dokumentet och parametrarna för-metoden:

| JSON-entitet | Metod parameter | Beskrivning |
| ----- | ----- | ----- |
| `computeType` | NA | Beräkningsmålet. För lokala mål måste värdet vara `local` . |
| `port` | `port` | Den lokala porten där tjänstens HTTP-slutpunkt ska exponeras. |

Denna JSON är en exempel distributions konfiguration för användning med CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```