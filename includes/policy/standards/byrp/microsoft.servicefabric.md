---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d39f57fe8b80621a18de4bd502fd5d2efd95bb38
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499601"
---
## <a name="azure-security-benchmark"></a>Benchmark för Azure-säkerhet

[Azure Security Benchmark ger](../../../../articles/security/benchmarks/overview.md) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Identitetshantering |IM-1 |Standardisera Azure Active Directory det centrala identitets- och autentiseringssystemet |[Service Fabric kluster ska endast använda Azure Active Directory för klientautentisering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Dataskydd |DP-5 |Kryptera känsliga data i vila |[Service Fabric kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Prestandatest för Azure-säkerhet v1

[Azure Security Benchmark ger](../../../../articles/security/benchmarks/overview.md) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Identitets- och åtkomstkontroll |3.9 |Använda Azure Active Directory |[Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Dataskydd |4.8 |Kryptera känslig information i vila |[Service Fabric kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC Nivå 3

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – CMMC Nivå 3.](../../../../articles/governance/policy/samples/cmmc-l3.md)
Mer information om den här efterlevnadsstandarden finns [i Cybersecurity Maturity Model Certification (CMMC).](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|System- och kommunikationsskydd |SC.3.177 |Använd FIPS-verifierad kryptografi när det används för att skydda sekretessen för CUI. |[Service Fabric kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|System- och kommunikationsskydd |SC.3.191 |Skydda sekretessen för CUI i vila. |[Service Fabric kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

Information om hur de tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns [i Azure Policy regelefterlevnad – ISO 27001:2013.](../../../../articles/governance/policy/samples/iso-27001.md)
Mer information om den här efterlevnadsstandarden [finns i ISO 27001:2013.](https://www.iso.org/isoiec-27001-information-security.html)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Åtkomstkontroll |9.2.3 |Hantering av privilegierade åtkomsträttigheter |[Service Fabric kluster ska endast använda Azure Active Directory för klientautentisering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Kryptografi |10.1.1 |Princip för användning av kryptografiska kontroller |[Service Fabric kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="new-zealand-ism-restricted"></a>Nya Zeeland ISM Restricted

Information om hur de tillgängliga Azure Policy för alla Azure-tjänster mappas till den här efterlevnadsstandarden finns i Azure Policy regelefterlevnad [– Nya Zeeland ISM Restricted](../../../../articles/governance/policy/samples/new-zealand-ism.md).
Mer information om den här efterlevnadsstandarden finns [i Nya Zeeland ISM Restricted](https://www.nzism.gcsb.govt.nz/).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Access Control och lösenord |AC-2 |16.1.32 Systemanvändaridentifiering |[Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns [i Azure Policy Regulatory Compliance - NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Mer information om den här efterlevnadsstandarden finns [i NIST SP 800-53 R4.](https://nvd.nist.gov/800-53)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Åtkomstkontroll |AC-2 (7) |Scheman \| för Role-Based kontohantering |[Service Fabric ska endast använda Azure Active Directory för klientautentisering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

