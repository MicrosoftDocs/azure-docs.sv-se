---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 271aa68fe59a6f660653795b7130cc47688a4907
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510828"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auktoriserade IP-intervall ska definieras på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |Begränsa åtkomsten till Kubernetes-Service Management-API genom att endast bevilja API-åtkomst till IP-adresser i specifika intervall. Vi rekommenderar att du begränsar åtkomsten till auktoriserade IP-intervall så att endast program från tillåtna nätverk kan komma åt klustret. |Granska, inaktiverad |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
