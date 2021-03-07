---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e58be69273e691c4553406ddda28e310de35811a
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442622"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Granskning på SQL Server måste vara aktiverat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Granskning på din SQL Server ska vara aktive rad för att spåra databas aktiviteter i alla databaser på servern och spara dem i en Gransknings logg. |AuditIfNotExists, inaktiverat |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Inställningarna för SQL-granskning ska ha Action-Groups kon figurer ATS för att samla in kritiska aktiviteter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |Egenskapen AuditActionsAndGroups ska innehålla minst SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP, FAILED_DATABASE_AUTHENTICATION_GROUP, BATCH_COMPLETED_GROUP för att säkerställa en grundlig gransknings loggning |AuditIfNotExists, inaktiverat |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |
