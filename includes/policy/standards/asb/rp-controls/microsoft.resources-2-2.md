---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 90f4131a616d0cac93ac2a08999178ac7ea8a0eb
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "101728550"
---
|Namn<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Automatisk etablering av den Log Analytics agenten ska vara aktive rad för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center samlar in data från dina virtuella Azure-datorer för att övervaka säkerhets problem och hot. Data samlas in av Log Analytics agent, tidigare kallat Microsoft Monitoring Agent (MMA), som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din Log Analytics-arbetsyta för analys. Vi rekommenderar att du aktiverar automatisk etablering för att automatiskt distribuera agenten till alla virtuella Azure-datorer som stöds och eventuella nya som skapas. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Monitor logg profil ska samla in loggar för kategorier "Write," Delete "och" Action "](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Den här principen säkerställer att en logg profil samlar in loggar för kategorier "Write," Delete "och" Action " |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |
|[Azure Monitor ska samla in aktivitets loggar från alla regioner](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Den här principen granskar Azure Monitor logg profil som inte exporterar aktiviteter från alla Azure-regioner som stöds, inklusive global. |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |
