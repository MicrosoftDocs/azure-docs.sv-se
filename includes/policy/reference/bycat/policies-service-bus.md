---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ed6c120bb5768ffe1fb0b660ae0346e278dc4746
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100566"
---
|Namn<br /><sub>(Azure Portal)</sub> |Description |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Alla auktoriseringsregler utom RootManageSharedAccessKey bör tas bort från Service Bus namnrymd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Service Bus-klienter bör inte använda en åtkomst princip för namn områdes nivå som ger åtkomst till alla köer och ämnen i ett namn område. Om du vill justera med minsta behörighets säkerhets modell bör du skapa åtkomst principer på enhets nivå för köer och ämnen för att ge åtkomst till endast den specifika entiteten |Granska, neka, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[Resurs loggar i Service Bus ska aktive ras](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Granska aktivering av resurs loggar. På så sätt kan du återskapa aktivitets spårningar som ska användas i utrednings syfte. När en säkerhets incident inträffar eller när nätverket komprometteras |AuditIfNotExists, inaktiverat |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Service Bus Premium-namnrymder bör använda en kundhanterad nyckel för kryptering](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |Azure Service Bus stöder möjligheten att kryptera data i vila med antingen Microsoft-hanterade nycklar (standard) eller Kundhanterade nycklar. Genom att välja att kryptera data med Kundhanterade nycklar kan du tilldela, rotera, inaktivera och återkalla åtkomst till de nycklar som Service Bus använder för att kryptera data i namn området. Observera att Service Bus endast stöder kryptering med Kundhanterade nycklar för Premium-namnområden. |Granskning, inaktive rad |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |
