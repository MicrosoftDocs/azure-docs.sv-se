---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b70057229a2d1fe47ca5e27f9ee4f1338521ff46
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867126"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Logic Apps Integration Service Environment krypteras med kund hanterade nycklar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fafeaf6-7927-4059-a50a-8eb2a7a6f2b5) |Distribuera till Integration Service Environment för att hantera kryptering i vila Logic Apps data med kundhanterade nycklar. Som standard krypteras kunddata med tjänst hanterade nycklar, men kund hanterade nycklar krävs ofta för att uppfylla regelefterlevnadsstandarder. Kund hanterade nycklar gör att data kan krypteras med en Azure Key Vault nyckel som skapas och ägs av dig. Du har fullständig kontroll och ansvar för nyckellivscykeln, inklusive rotation och hantering. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_ISEWithCustomerManagedKey_AuditDeny.json) |
|[Logic Apps ska distribueras till Integration Service Environment](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc595cb1-1cde-45f6-8faf-f88874e1c0e1) |Distribution Logic Apps till Integration Service Environment i ett virtuellt nätverk låser upp avancerade Logic Apps och säkerhetsfunktioner och ger dig större kontroll över din nätverkskonfiguration. Läs mer på: [https://aka.ms/integration-service-environment](https://aka.ms/integration-service-environment) . Distribution till Integration Service Environment också kryptering med kundhanterade nycklar som ger förbättrat dataskydd genom att låta dig hantera dina krypteringsnycklar. Detta är ofta för att uppfylla efterlevnadskrav. |Granska, Neka, Inaktiverad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_LogicAppsInISE_AuditDeny.json) |
|[Resursloggar i Logic Apps ska vara aktiverade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Granska aktivering av resursloggar. På så sätt kan du återskapa aktivitetsloggar som ska användas i undersökningssyfte. när en säkerhetsincident inträffar eller när nätverket har komprometterats |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
