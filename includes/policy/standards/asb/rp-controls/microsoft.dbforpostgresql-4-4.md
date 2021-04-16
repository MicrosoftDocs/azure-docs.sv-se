---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4f02a1cb7efacadbdd3f77ddceebb6792eb2f95c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511087"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Effekt(er) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Framtvinga SSL-anslutning ska vara aktiverat för PostgreSQL-databasservrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av din Azure Database for PostgreSQL-server till klientprogram med hjälp av Secure Sockets Layer (SSL). Genom att framtvinga SSL-anslutningar mellan databasservern och klientprogrammen kan du skydda dig mot "man in the middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program. Den här konfigurationen framtvingar att SSL alltid är aktiverat för åtkomst till databasservern. |Granska, inaktiverad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
