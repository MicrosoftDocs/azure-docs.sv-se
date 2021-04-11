---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ca68c0a770023d0362cb9278b3742a6a136c796b
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095885"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[API Management tjänst bör använda en SKU som stöder virtuella nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |Med SKU: er för API Management som stöds och distribuerar tjänsten till ett virtuellt nätverk för att låsa upp avancerade API Management nätverks-och säkerhetsfunktioner som ger dig större kontroll över din nätverks säkerhets konfiguration. Läs mer på: [https://aka.ms/apimvnet](https://aka.ms/apimvnet) . |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management tjänster ska använda ett virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network-distributionen ger förbättrad säkerhet, isolering och gör att du kan placera din API Management-tjänst i ett icke-Internet-dirigerbart nätverk som du styr åtkomsten till. Dessa nätverk kan sedan anslutas till dina lokala nätverk med olika VPN-tekniker, vilket ger åtkomst till dina Server dels tjänster i nätverket och/eller lokalt. Developer-portalen och API-gatewayen kan konfigureras att vara tillgängliga antingen från Internet eller endast inom det virtuella nätverket. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
