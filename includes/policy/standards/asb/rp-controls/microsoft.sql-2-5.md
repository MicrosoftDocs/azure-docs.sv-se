---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: baca2a64b6d1999d28e020391f022b197291b9ab
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879514"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL-servrar med granskning till lagringskontots mål bör konfigureras med 90 dagars kvarhållning eller högre](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |I incidentundersökningssyfte rekommenderar vi att du ställer in datalagringen för SQL Server granskning till lagringskontots mål till minst 90 dagar. Bekräfta att du uppfyller de nödvändiga kvarhållningsreglerna för de regioner där du arbetar. Detta krävs ibland för efterlevnad av regelstandarder. |AuditIfNotExists, inaktiverad |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
