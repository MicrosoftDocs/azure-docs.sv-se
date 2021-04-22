---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7a64c3558fb96e55bff2a822ed20b45a76d53545
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876212"
---
## <a name="azure-security-benchmark"></a>Benchmark för Azure-säkerhet

Azure [Security Benchmark ger](/security/benchmark/azure/introduction) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur tillgängliga Azure Policy inbyggda funktioner för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark.](../../../../articles/governance/policy/samples/azure-security-benchmark.md)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Loggning och hotidentifiering |LT-4 |Aktivera loggning för Azure-resurser |[Resursloggar i Service Bus ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Benchmark för Azure-säkerhet v1

Azure [Security Benchmark ger](/security/benchmark/azure/introduction) rekommendationer om hur du kan skydda dina molnlösningar i Azure. Om du vill se hur den här tjänsten helt mappar till Azure Security Benchmark kan du gå till [mappningsfilerna för Azure Security Benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

Information om hur tillgängliga Azure Policy inbyggda funktioner för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – Azure Security Benchmark.](../../../../articles/governance/policy/samples/azure-security-benchmark.md)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Nätverkssäkerhet |1.1 |Skydda resurser med hjälp av nätverkssäkerhetsgrupper eller Azure Firewall på Virtual Network |[Service Bus bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Loggning och övervakning |2.3 |Aktivera granskningsloggning för Azure-resurser |[Resursloggar i Service Bus ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-130"></a>CIS Microsoft Azure Foundations Benchmark 1.3.0

Information om hur tillgängliga Azure Policy inbyggda funktioner för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy Regulatory Compliance - CIS Microsoft Azure Foundations Benchmark 1.3.0](../../../../articles/governance/policy/samples/cis-azure-1-3-0.md).
Mer information om den här efterlevnadsstandarden finns [i CIS Microsoft Azure Foundations Benchmark.](https://www.cisecurity.org/benchmark/azure/)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Loggning och övervakning |5.3 |Kontrollera att Diagnostikloggar är aktiverade för alla tjänster som stöder det. |[Resursloggar i Service Bus ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappas till den här efterlevnadsstandarden finns i [Azure Policy Regulatory Compliance - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Mer information om den här efterlevnadsstandarden finns [i HIPAA HITRUST 9.2.](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Uppdelning i nätverk |0805.01m1Organizational.12 -01.m |Organisationens säkerhetsgatewayer (t.ex. brandväggar) tillämpar säkerhetsprinciper och är konfigurerade för att filtrera trafik mellan domäner, blockera obehörig åtkomst och används för att upprätthålla uppdelningen mellan interna kabelanslutna, interna trådlösa och externa nätverkssegment (t.ex. Internet) inklusive DMZ:er och framtvinga principer för åtkomstkontroll för var och en av domänerna. |[Service Bus bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Uppdelning i nätverk |0806.01m2Organizational.12356 - 01.m |Organisationens nätverk är logiskt och fysiskt segmenterat med en definierad säkerhets perimeter och en graderad uppsättning kontroller, inklusive undernät för offentligt tillgängliga systemkomponenter som är logiskt åtskilda från det interna nätverket, baserat på organisationens krav. och trafik styrs baserat på funktioner som krävs och klassificering av data/system baserat på en riskbedömning och deras respektive säkerhetskrav. |[Service Bus bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Uppdelning i nätverk |0894.01m2Organizational.7 – 01.m |Nätverk är åtskilda från nätverk på produktionsnivå vid migrering av fysiska servrar, program eller data till virtualiserade servrar. |[Service Bus bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|Granskningsloggning |1208.09aa3System.1 – 09.aa |Granskningsloggar underhålls för hanteringsaktiviteter, system- och programstart/avstängning/fel, filändringar och säkerhetsprincipändringar. |[Resursloggar i Service Bus ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|Nätverkskontroller |0860.09m1Organizational.9 – 09.m |Organisationen hanterar formellt utrustning i nätverket, inklusive utrustning i användarområden. |[Service Bus bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

