---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0f48d14174a880d0de0072cb39cc6853ced1db03
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880402"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Privat slutpunkt ska vara aktiverad för PostgreSQL-servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |Privata slutpunktsanslutningar framtvingar säker kommunikation genom att aktivera privat anslutning till Azure Database for PostgreSQL. Konfigurera en privat slutpunktsanslutning för att ge åtkomst till trafik som endast kommer från kända nätverk och förhindra åtkomst från alla andra IP-adresser, inklusive inom Azure. |AuditIfNotExists, inaktiverad |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
