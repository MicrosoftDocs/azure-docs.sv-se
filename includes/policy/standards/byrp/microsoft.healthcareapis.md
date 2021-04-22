---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 72fb4efc7a30ebaa3299f4c479bc02ad4b6f5386
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862153"
---
## <a name="cmmc-level-3"></a>CMMC Nivå 3

Information om hur tillgängliga Azure Policy för alla Azure-tjänster mappar till den här efterlevnadsstandarden finns i [Azure Policy regelefterlevnad – CMMC Nivå 3.](../../../../articles/governance/policy/samples/cmmc-l3.md)
Mer information om den här efterlevnadsstandarden finns [i CmMC (Cybersecurity Maturity Model Certification).](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)

|Domain |Kontroll-ID |Kontrollrubrik |Policy<br /><sub>(Azure Portal)</sub> |Principversion<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Åtkomstkontroll |AC.1.001 |Begränsa åtkomsten till informationssystemet till behöriga användare, processer som agerar för behöriga användares räkning och enheter (inklusive andra informationssystem). |[CORS bör inte tillåta att alla domäner får åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Åtkomstkontroll |AC.1.002 |Begränsa informationssystemets åtkomst till de typer av transaktioner och funktioner som behöriga användare har behörighet att köra. |[CORS bör inte tillåta att alla domäner får åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Åtkomstkontroll |AC.2.016 |Kontrollera flödet av CUI i enlighet med godkända auktoriseringar. |[CORS bör inte tillåta att alla domäner får åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Konfigurationshantering |CM.3.068 |Begränsa, inaktivera eller förhindra användning av program, funktioner, portar, protokoll och tjänster som inte är viktiga. |[CORS bör inte tillåta att alla domäner får åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|System- och kommunikationsskydd |SC.3.177 |Använd FIPS-verifierad kryptografi när det används för att skydda sekretessen för CUI. |[Azure API for FHIR bör använda en kund hanterad nyckel för att kryptera vilodata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|System- och kommunikationsskydd |SC.3.183 |Neka nätverkskommunikationstrafik som standard och tillåt nätverkskommunikationstrafik via undantag (dvs. neka alla, tillåt som undantag). |[CORS bör inte tillåta att alla domäner får åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

