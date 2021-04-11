---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 494cf5e7e16ae7aac516eac6c8f411173256361d
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "107284315"
---
|Name<br /><sub>(Azure Portal)</sub> |Beskrivning |Påverkan (ar) |Version<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL stöder anslutning av Azure Database for PostgreSQL-servern till klient program med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man i de mittersta" angrepp genom att kryptera data strömmen mellan servern och ditt program. Den här konfigurationen tillämpar att SSL alltid är aktiverat för åtkomst till din databas server. |Granskning, inaktive rad |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
