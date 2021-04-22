---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6c54ddf232d16b0555ec8e1f5fee57e1f597488f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866945"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Management ska använda en SKU som stöder virtuella nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |Med stödda SKU:er för API Management ger distributionstjänsten till ett virtuellt nätverk tillgång till avancerade API Management-nätverk och säkerhetsfunktioner som ger dig större kontroll över nätverkssäkerhetskonfigurationen. Läs mer på: [https://aka.ms/apimvnet](https://aka.ms/apimvnet) . |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management ska använda ett virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network-distributionen ger förbättrad säkerhet, isolering och gör att du kan placera din API Management-tjänst i ett dirigerbart nätverk som du inte använder internet för att styra åtkomsten till. Dessa nätverk kan sedan anslutas till dina lokala nätverk med hjälp av olika VPN-tekniker, vilket ger åtkomst till dina backend-tjänster i nätverket och/eller lokalt. Utvecklarportalen och API-gatewayen kan konfigureras för att vara tillgängliga antingen från Internet eller bara inom det virtuella nätverket. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
