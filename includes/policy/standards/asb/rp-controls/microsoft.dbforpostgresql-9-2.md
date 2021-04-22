---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 25011470c85338fcc20565130c2d6765d90e74ad
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880683"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL kan du välja redundansalternativ för databasservern. Den kan ställas in på en geo-redundant lagringsplats för säkerhetskopiering där data inte bara lagras i den region där servern finns, utan även replikeras till en länkad region för att tillhandahålla återställningsalternativ om det uppstår ett regionfel. Konfiguration av geo-redundant lagring för säkerhetskopiering tillåts endast när servern skapas. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
