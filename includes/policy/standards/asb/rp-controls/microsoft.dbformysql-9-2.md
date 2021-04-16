---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e2e12cdd06a21d1838ad939a2cebb908bf0452a4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512038"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL kan du välja redundansalternativ för databasservern. Den kan ställas in på en geo-redundant lagringsplats för säkerhetskopiering där data inte bara lagras i den region där servern finns, utan även replikeras till en länkad region för att tillhandahålla återställningsalternativ om det uppstår ett regionfel. Konfiguration av geo-redundant lagring för säkerhetskopiering tillåts endast när servern skapas. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
