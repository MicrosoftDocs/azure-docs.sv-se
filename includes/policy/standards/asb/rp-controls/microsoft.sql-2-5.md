---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1c61f7bbe9f926ad186710cc6e804060d1e30950
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511447"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL-servrar med granskning för lagringskontots mål bör konfigureras med 90 dagars kvarhållning eller högre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |I incidentundersökningssyfte rekommenderar vi att du ställer in datalagringen för din SQL Server till lagringskontots mål till minst 90 dagar. Bekräfta att du uppfyller de nödvändiga kvarhållningsreglerna för de regioner där du arbetar. Detta krävs ibland för efterlevnad av regelkrav. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
