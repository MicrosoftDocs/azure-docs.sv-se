---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 067b099bee447c559d7196e0f7e72bcaa1d00bf5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867921"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Rekommendationer för anpassningsbar nätverkshärdning bör tillämpas på internetuppspelade virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center trafikmönster för Internetuppspelade virtuella datorer och tillhandahåller regelrekommendationer för nätverkssäkerhetsgruppen som minskar risken för angrepp |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[Internetuppriktade virtuella datorer bör skyddas med nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Skydda dina virtuella datorer mot potentiella hot genom att begränsa åtkomsten till dem med nätverkssäkerhetsgrupper (NSG). Läs mer om att styra trafik med NSG:er på [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[IP-vidarebefordran på den virtuella datorn ska vara inaktiverad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |Genom att aktivera IP-vidarebefordran på en virtuell dators nätverkskort kan datorn ta emot trafik adresserad till andra mål. IP-vidarebefordran krävs sällan (t.ex. när du använder den virtuella datorn som en virtuell nätverksinstallation), och därför bör detta granskas av nätverkssäkerhetsteamet. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Hanteringsportar för virtuella datorer ska skyddas med just-in-time-åtkomstkontroll för nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Möjlig JIT-åtkomst (Just-In-Time) för nätverk övervakas av Azure Security Center som rekommendationer |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Hanteringsportar bör stängas på dina virtuella datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Öppna fjärrhanteringsportar exponerar den virtuella datorn för en hög risknivå från Internetbaserade attacker. Dessa attacker försöker råstyra autentiseringsuppgifter för att få administratörsåtkomst till datorn. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
