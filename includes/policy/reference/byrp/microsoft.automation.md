---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8f09bc58c536dc7262405a79c1dedacf76b6ffa0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096269"
---
|Namn<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Variabler för Automation-konton ska vara krypterade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |Det är viktigt att aktivera kryptering av variabel till gångar för Automation-kontot när du lagrar känsliga data |Granska, neka, inaktive rad |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Azure Automation konton ska använda Kundhanterade nycklar för att kryptera data i vila](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56a5ee18-2ae6-4810-86f7-18e39ce5629b) |Använd Kundhanterade nycklar för att hantera krypteringen i resten av dina Azure Automation-konton. Som standard krypteras kund information med tjänst hanterade nycklar, men Kundhanterade nycklar krävs ofta för att uppfylla gällande regler för efterlevnad. Kundhanterade nycklar gör det möjligt att kryptera data med en Azure Key Vault-nyckel som skapats och ägs av dig. Du har fullständig kontroll och ansvar för nyckel livs cykeln, inklusive rotation och hantering. Läs mer på [https://aka.ms/automation-cmk](https://aka.ms/automation-cmk) . |Granska, neka, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_CMK_Audit.json) |
