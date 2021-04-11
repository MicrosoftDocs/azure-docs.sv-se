---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 355a74ded8b8d01632aff70b35f96f2502c970b7
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "107285862"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Automatisk etablering av den Log Analytics agenten ska vara aktive rad för din prenumeration](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center samlar in data från dina virtuella Azure-datorer för att övervaka säkerhets problem och hot. Data samlas in av Log Analytics agent, tidigare kallat Microsoft Monitoring Agent (MMA), som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din Log Analytics-arbetsyta för analys. Vi rekommenderar att du aktiverar automatisk etablering för att automatiskt distribuera agenten till alla virtuella Azure-datorer som stöds och eventuella nya som skapas. |AuditIfNotExists, inaktiverat |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
