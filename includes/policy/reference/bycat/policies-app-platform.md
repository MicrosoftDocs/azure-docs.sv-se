---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1a4fe06d3ef31c9e19d5c721e75e9ca75a9cbd06
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498850"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[Förhandsversion: \] Granska Azure Spring Cloud instanser där distribuerad spårning inte är aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Distribuerade spårningsverktyg i Azure Spring Cloud kan felsöka och övervaka komplexa kopplingar mellan mikrotjänster i ett program. Distribuerade spårningsverktyg ska vara aktiverade och i felfritt tillstånd. |Granska, inaktiverad |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure Spring Cloud bör använda nätverksinjektion](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud instanser ska använda virtuell nätverksinjektion för följande ändamål: 1. Isolera Azure Spring Cloud från Internet. 2. Gör Azure Spring Cloud kan interagera med system i antingen lokala datacenter eller Azure-tjänsten i andra virtuella nätverk. 3. Ge kunderna möjlighet att styra inkommande och utgående nätverkskommunikation för Azure Spring Cloud. |Granska, Inaktiverad, Neka |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
