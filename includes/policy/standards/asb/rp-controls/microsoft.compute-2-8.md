---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 14ae156cdcf24005f958c17c9b28e7b50d9be2ed
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511688"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Endpoint Protection-lösningen ska installeras på VM-skalningsuppsättningar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Granska förekomsten av och hälsotillståndet för en lösning för slutpunktsskydd på dina virtuella datorers skalningsuppsättningar för att skydda dem mot hot och sårbarheter. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft Antimalware för Azure ska konfigureras för att automatiskt uppdatera skyddssignaturer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |Den här principen granskar alla virtuella Windows-datorer som inte har konfigurerats med automatisk uppdatering Microsoft Antimalware signaturerna för skydd. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Övervaka saknat Endpoint Protection i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servrar utan en installerad Endpoint Protection-agent övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
