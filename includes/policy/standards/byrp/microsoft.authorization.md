---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7bab88a46a1efa7953d86da6451c6287bdf47843
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875149"
---
## <a name="azure-security-benchmark"></a>Benchmark för Azure-säkerhet

[Azure Security Benchmark ger](/security/benchmark/azure/introduction) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Privilegierad åtkomst |PA-7 |Följ precis tillräcklig administration (principen om minsta privilegium) |[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Prestandatest för Azure-säkerhet v1

[Azure Security Benchmark ger](/security/benchmark/azure/introduction) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Dataskydd |4,6 |Använda Azure RBAC för att styra åtkomsten till resurser |[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-110"></a>CIS Microsoft Azure Foundations Benchmark 1.1.0

Information om hur de tillgängliga Azure Policy inbyggda tjänsterna för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy Regulatory Compliance – CIS Microsoft Azure Foundations Benchmark 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Mer information om den här efterlevnadsstandarden finns [i CIS Microsoft Azure Foundations Benchmark.](https://www.cisecurity.org/benchmark/azure/)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Identitets- och åtkomsthantering |1.23 |Se till att inga anpassade prenumerationens ägarroller skapas |[Anpassade prenumerationsägare bör inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-130"></a>CIS Microsoft Azure Foundations Benchmark 1.3.0

Information om hur tillgängliga Azure Policy inbyggda funktioner för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy Regulatory Compliance - CIS Microsoft Azure Foundations Benchmark 1.3.0](../../../../articles/governance/policy/samples/cis-azure-1-3-0.md).
Mer information om den här efterlevnadsstandarden finns [i CIS Microsoft Azure Foundations Benchmark.](https://www.cisecurity.org/benchmark/azure/)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Identitets- och åtkomsthantering |1.21 |Se till att inga anpassade prenumerationsägaresroller skapas |[Anpassade prenumerationsägare bör inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC Nivå 3

Information om hur de tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – CMMC Nivå 3.](../../../../articles/governance/policy/samples/cmmc-l3.md)
Mer information om den här efterlevnadsstandarden finns [i CmMC (Cybersecurity Maturity Model Certification).](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Åtkomstkontroll |AC.3.018 |Förhindra icke-privilegierade användare från att köra privilegierade funktioner och avbilda körningen av sådana funktioner i granskningsloggar. |[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Information om hur de tillgängliga Azure Policy för alla Azure-tjänster mappas till den här efterlevnadsstandarden finns [i Azure Policy-regelefterlevnad – HIPAA HITRUST 9.2.](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)
Mer information om den här efterlevnadsstandarden finns [i HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Behörighetshantering |1148.01c2System.78 – 01.c |Organisationen begränsar åtkomsten till privilegierade funktioner och all säkerhetsreleverad information. |[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|Ansvarsfördelning |1230.09c2Organizational.1 – 09.c |Ingen enskild person kan komma åt, ändra eller använda informationssystem utan auktorisering eller identifiering. |[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|Ansvarsfördelning |1276.09c2Organizational.2 – 09.c |Säkerhetsgranskningsaktiviteter är oberoende av varandra. |[Anpassade prenumerationsägare bör inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
|Ansvarsfördelning |1278.09c2Organizational.56 - 09.c |Organisationen identifierar uppgifter som kräver separation och definierar informationssystemets åtkomstauktoriseringar för att stödja uppdelning av uppgifter. och inkompatibla skyldigheter är åtskilda mellan flera användare för att minimera risken för missbruk eller bedrägeri. |[Anpassade prenumerationsägare bör inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns [i Azure Policy Regelefterlevnad – ISO 27001:2013.](../../../../articles/governance/policy/samples/iso-27001.md)
Mer information om den här efterlevnadsstandarden finns [i ISO 27001:2013.](https://www.iso.org/isoiec-27001-information-security.html)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Åtkomstkontroll |9.2.3 |Hantering av privilegierade åtkomsträttigheter |[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns [i Azure Policy Regelefterlevnad – NIST SP 800-53 R4.](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md)
Mer information om den här efterlevnadsstandarden finns [i NIST SP 800-53 R4.](https://nvd.nist.gov/800-53)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Åtkomstkontroll |AC-2 (7) |Scheman för \| Role-Based kontohantering |[Granska användningen av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

