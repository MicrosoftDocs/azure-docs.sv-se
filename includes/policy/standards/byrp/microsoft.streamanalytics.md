---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e61c494071cff38008bbfe70821bd385b2bcb530
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873007"
---
## <a name="azure-security-benchmark"></a>Benchmark för Azure-säkerhet

[Azure Security Benchmark ger](/security/benchmark/azure/introduction) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Loggning och hotidentifiering |LT-4 |Aktivera loggning för Azure-resurser |[Resursloggar i Azure Stream Analytics ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Benchmark v1 för Azure-säkerhet

[Azure Security Benchmark ger](/security/benchmark/azure/introduction) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Loggning och övervakning |2.3 |Aktivera granskningsloggning för Azure-resurser |[Resursloggar i Azure Stream Analytics ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-130"></a>CIS Microsoft Azure Foundations Benchmark 1.3.0

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns [i Azure Policy Regulatory Compliance - CIS Microsoft Azure Foundations Benchmark 1.3.0](../../../../articles/governance/policy/samples/cis-azure-1-3-0.md).
Mer information om den här efterlevnadsstandarden finns [i CIS Microsoft Azure Foundations Benchmark.](https://www.cisecurity.org/benchmark/azure/)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Loggning och övervakning |5.3 |Se till att Diagnostikloggar är aktiverade för alla tjänster som stöder det. |[Resursloggar i Azure Stream Analytics ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC Nivå 3

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – CMMC Nivå 3.](../../../../articles/governance/policy/samples/cmmc-l3.md)
Mer information om den här efterlevnadsstandarden finns [i CmMC (Cybersecurity Maturity Model Certification).](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|System- och kommunikationsskydd |SC.3.177 |Använd FIPS-verifierad kryptografi när det används för att skydda sekretessen för CUI. |[Azure Stream Analytics ska använda kund hanterade nycklar för att kryptera data](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87ba29ef-1ab3-4d82-b763-87fcd4f531f7) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_CMK_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappas till den här efterlevnadsstandarden finns i [Azure Policy Regulatory Compliance - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Mer information om den här efterlevnadsstandarden finns [i HIPAA HITRUST 9.2.](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Granskningsloggning |1207.09aa2System.4 – 09.aa |Granskningsposter sparas i 90 dagar och äldre granskningsposter arkiveras i ett år. |[Resursloggar i Azure Stream Analytics ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |

