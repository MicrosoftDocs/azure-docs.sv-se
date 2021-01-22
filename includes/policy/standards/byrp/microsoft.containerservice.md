---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e0ee68d8e2f8c02e560b970226f5cddf5ec46d3e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681919"
---
## <a name="azure-security-benchmark-v1"></a>Azure-säkerhet för prestandatest v1

[Azures säkerhets benchmark](../../../../articles/security/benchmarks/overview.md) ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Om du vill se hur den här tjänsten är fullständigt mappad till Azures säkerhets benchmark kan du läsa mer i [Azure-mappningen för säkerhets benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regelefterlevnad – säkerhet i Azure](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Nätverkssäkerhet |1,1 |Skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandväggen på din Virtual Network |[Auktoriserade IP-intervall ska definieras på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|Dataskydd |4,6 |Använd Azure RBAC för att styra åtkomsten till resurser |[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|Sårbarhetshantering |5.3 |Distribuera automatisk hanterings lösning för program uppdatering från tredje part |[Kubernetes Services bör uppgraderas till en icke-sårbar Kubernetes-version](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regler för regelefterlevnad-CIS Microsoft Azure grunderna för benchmark-1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Mer information om den här standarden för efterlevnad finns i [CIS Microsoft Azure Stiftelses benchmark](https://www.cisecurity.org/benchmark/azure/).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Andra säkerhets aspekter |8.5 |Aktivera rollbaserad åtkomst kontroll (RBAC) i Azure Kubernetes Services |[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regler för regelefterlevnad-HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Mer information om den här standarden för efterlevnad finns i [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Behörighets hantering |1149.01 c2System. 9 – 01. c |Organisationen underlättar informations delningen genom att göra det möjligt för behöriga användare att fastställa en affärs partners åtkomst när det är möjligt att tillåta detta som definieras av organisationen och genom att använda manuella processer eller automatiserade mekanismer för att hjälpa användare att fatta informations delning/samarbets beslut. |[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|Behörighets hantering |1153.01 c3System. 35-01. c |All fil system åtkomst som inte uttryckligen krävs är inaktive rad och endast auktoriserade användare får åtkomst till det som uttryckligen krävs för användarens jobb uppgifter. |[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|Ansvars fördelning |1229.09 c1Organizational. 1 – 09. c |Separering av uppgifter används för att begränsa risken för obehörig eller oavsiktlig ändring av information och system. |[Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regler för efterlevnad-NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Mer information om den här standarden för efterlevnad finns i [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|System-och informations integritet |3.14.1 |Identifiera, rapportera och korrigera systemfel i rimlig tid. |[Kubernetes Services bör uppgraderas till en icke-sårbar Kubernetes-version](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

