---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 11e14a435f6ea33cf9f4f8a7dd3804c95187c9bf
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99106266"
---
## <a name="cmmc-level-3"></a>CMMC nivå 3

Om du vill se hur tillgängliga Azure Policy inbyggda program för alla Azure-tjänster mappar till den här standarden för efterlevnad, se [Azure policy regelefterlevnad-CMMC nivå 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Mer information om den här standarden för efterlevnad finns i [cybersäkerhet förfallo modell certifiering (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Domain |Kontroll-ID |Kontroll rubrik |Policy<br /><sub>(Azure Portal)</sub> |Princip version<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Åtkomstkontroll |AC. 1.001 |Begränsa informations systemets åtkomst till behöriga användare, processer som agerar på uppdrag av behöriga användare och enheter (inklusive andra informations system). |[CORS bör inte tillåta varje domän åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Åtkomstkontroll |AC. 1.002 |Begränsa informations systemets åtkomst till de typer av transaktioner och funktioner som behöriga användare tillåts att köra. |[CORS bör inte tillåta varje domän åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Åtkomstkontroll |AC. 2.016 |Styr flödet av CUI i enlighet med godkända auktoriseringar. |[CORS bör inte tillåta varje domän åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Konfigurationshantering |CM. 3.068 |Begränsa, inaktivera eller förhindra användning av icke-viktiga program, funktioner, portar, protokoll och tjänster. |[CORS bör inte tillåta varje domän åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|System-och kommunikations skydd |SC. 3.177 |Använd FIPS-validerad kryptografi när den används för att skydda konfidentialiteten hos CUI. |[Azure API för FHIR bör använda en kundhanterad nyckel (CMK) för att kryptera data i vila](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|System-och kommunikations skydd |SC. 3.183 |Neka nätverks kommunikations trafik som standard och Tillåt nätverks kommunikations trafik genom undantag (t. ex. neka alla, Tillåt undantag). |[CORS bör inte tillåta varje domän åtkomst till ditt API för FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

