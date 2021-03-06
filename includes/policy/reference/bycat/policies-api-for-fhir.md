---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 28de9d80a1e5568b70f48e789233af38388fbe70
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866968"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure API for FHIR bör använda en kund hanterad nyckel för att kryptera vilodata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Använd en kund hanterad nyckel för att styra krypteringen i vila av de data som lagras i Azure API for FHIR när detta är ett regel- eller efterlevnadskrav. Kund hanterade nycklar levererar också dubbel kryptering genom att lägga till ett andra lager med kryptering ovanpå standardnyckeln som görs med tjänst-hanterade nycklar. |audit, disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Azure API for FHIR bör använda private link](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR bör ha minst en godkänd privat slutpunktsanslutning. Klienter i ett virtuellt nätverk kan på ett säkert sätt komma åt resurser som har privata slutpunktsanslutningar via privata länkar. Mer information finns i: [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink) . |Granska, inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[CORS bör inte tillåta att alla domäner får åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |Resursdelning för korsande ursprung (CORS) bör inte tillåta att alla domäner får åtkomst till ditt API för FHIR. Om du vill skydda ditt API för FHIR tar du bort åtkomsten för alla domäner och definierar uttryckligen vilka domäner som tillåts att ansluta. |audit, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
