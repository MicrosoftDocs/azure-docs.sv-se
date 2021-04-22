---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d2e6eb6f38ed85a6b943653ed3e1464ebe629799
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873098"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Geo-redundant säkerhetskopiering ska vara aktiverat för Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB kan du välja redundansalternativ för databasservern. Den kan ställas in på en geo-redundant lagringsplats för säkerhetskopiering där data inte bara lagras i den region där servern finns, utan även replikeras till en länkad region för att tillhandahålla återställningsalternativ om det uppstår ett regionfel. Konfiguration av geo-redundant lagring för säkerhetskopiering tillåts endast när servern skapas. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB-servern bör använda en tjänstslutpunkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Brandväggsregler baserade på virtuella nätverk används för att aktivera trafik från ett specifikt undernät till Azure Database for MariaDB samtidigt som trafiken hålls inom Azure-gränsen. Den här principen är ett sätt att granska om Azure Database for MariaDB har tjänstslutpunkt för virtuellt nätverk som används. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Den privata slutpunkten ska vara aktiverad för MariaDB-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Privata slutpunktsanslutningar framtvingar säker kommunikation genom att aktivera privat anslutning till Azure Database for MariaDB. Konfigurera en privat slutpunktsanslutning för att ge åtkomst till trafik som endast kommer från kända nätverk och förhindra åtkomst från alla andra IP-adresser, inklusive inom Azure. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Offentlig nätverksåtkomst ska inaktiveras för MariaDB-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Inaktivera egenskapen för offentlig nätverksåtkomst för att förbättra säkerheten och Azure Database for MariaDB kan endast nås från en privat slutpunkt. Den här konfigurationen inaktiverar endast åtkomst från alla offentliga adressutrymmen utanför Azures IP-intervall och nekar alla inloggningar som matchar IP- eller virtuellt nätverksbaserade brandväggsregler. |Granska, inaktiverad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
