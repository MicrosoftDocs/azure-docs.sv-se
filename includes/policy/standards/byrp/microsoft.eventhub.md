---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cca378322d6ced73ecdfecf038aa4231699a280d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496664"
---
## <a name="azure-security-benchmark"></a>Benchmark för Azure-säkerhet

[Azure Security Benchmark ger](../../../../articles/security/benchmarks/overview.md) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur de tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Loggning och hotidentifiering |LT-4 |Aktivera loggning för Azure-resurser |[Resursloggar i Händelsehubb ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Benchmark v1 för Azure-säkerhet

[Azure Security Benchmark ger](../../../../articles/security/benchmarks/overview.md) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur de tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Nätverkssäkerhet |1.1 |Skydda resurser med hjälp av nätverkssäkerhetsgrupper eller Azure Firewall på Virtual Network |[Händelsehubben bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Loggning och övervakning |2.3 |Aktivera granskningsloggning för Azure-resurser |[Resursloggar i händelsehubben ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-130"></a>CIS Microsoft Azure Foundations Benchmark 1.3.0

Information om hur tillgängliga Azure Policy inbyggda funktioner för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy Regulatory Compliance - CIS Microsoft Azure Foundations Benchmark 1.3.0](../../../../articles/governance/policy/samples/cis-azure-1-3-0.md).
Mer information om den här efterlevnadsstandarden finns [i CIS Microsoft Azure Foundations Benchmark.](https://www.cisecurity.org/benchmark/azure/)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Loggning och övervakning |5.3 |Kontrollera att Diagnostikloggar är aktiverade för alla tjänster som stöder det. |[Resursloggar i händelsehubben ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappas till den här efterlevnadsstandarden finns i [Azure Policy Regulatory Compliance - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Mer information om den här efterlevnadsstandarden finns [i HIPAA HITRUST 9.2.](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Uppdelning i nätverk |0805.01m1Organizational.12 -01.m |Organisationens säkerhetsgatewayer (t.ex. brandväggar) tillämpar säkerhetsprinciper och är konfigurerade för att filtrera trafik mellan domäner, blockera obehörig åtkomst och används för att upprätthålla uppdelningen mellan interna kabelanslutna, interna trådlösa och externa nätverkssegment (t.ex. Internet) inklusive DMZ:er och framtvinga principer för åtkomstkontroll för var och en av domänerna. |[Händelsehubben bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Uppdelning i nätverk |0806.01m2Organizational.12356 - 01.m |Organisationens nätverk är logiskt och fysiskt segmenterat med en definierad säkerhets perimeter och en graderad uppsättning kontroller, inklusive undernät för offentligt tillgängliga systemkomponenter som är logiskt åtskilda från det interna nätverket, baserat på organisationens krav. och trafik styrs baserat på funktioner som krävs och klassificering av data/system baserat på en riskbedömning och deras respektive säkerhetskrav. |[Händelsehubben bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Uppdelning i nätverk |0894.01m2Organizational.7 – 01.m |Nätverk är åtskilda från nätverk på produktionsnivå vid migrering av fysiska servrar, program eller data till virtualiserade servrar. |[Händelsehubben bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|Granskningsloggning |1207.09aa2System.4 – 09.aa |Granskningsposter sparas i 90 dagar och äldre granskningsposter arkiveras i ett år. |[Resursloggar i Händelsehubb ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|Nätverkskontroller |0863.09m2Organizational.910 -09.m |Organisationen skapar en brandväggskonfiguration som begränsar anslutningar mellan ej betrodda nätverk och eventuella systemkomponenter i den informationsmiljö som omfattas; och eventuella ändringar i brandväggskonfigurationen uppdateras i nätverksdiagrammet. |[Händelsehubben bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

