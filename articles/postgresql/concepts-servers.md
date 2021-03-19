---
title: Servrar – Azure Database for PostgreSQL-enskild server
description: Den här artikeln innehåller överväganden och rikt linjer för att konfigurera och hantera Azure Database for PostgreSQL-enskild server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c8ae129cbad56e3bb63e055f9d843cac1da22fb1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91710472"
---
# <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – enskild server
Den här artikeln innehåller överväganden och rikt linjer för att arbeta med Azure Database for PostgreSQL-en server.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Vad är en Azure Database for PostgreSQL-server?
En server i distributions alternativet Azure Database for PostgreSQL-enskild server är en central administrations plats för flera databaser. Det är samma PostgreSQL Server-konstruktion som du kanske är van vid i den lokala världen. Mer specifikt är PostgreSQL-tjänsten hanterad, ger prestanda garantier, visar åtkomst och funktioner på server nivå.

En Azure Database for PostgreSQL Server:

- Skapas i en Azure-prenumeration.
- Är överordnad resurs för databaser.
- Innehåller ett namn område för databaser.
- Är en behållare med stark livs längd – ta bort en server och tar bort de inneslutna databaserna.
- Samlar resurser i en region.
- Tillhandahåller en anslutnings slut punkt för Server-och databas åtkomst 
- Tillhandahåller omfattning för hanterings principer som gäller för databaserna: inloggning, brand vägg, användare, roller, konfigurationer osv.
- Är tillgänglig i flera versioner. Mer information finns i [PostgreSQL Database-versioner som stöds](concepts-supported-versions.md).
- Är utöknings bar av användare. Mer information finns i [postgresql-tillägg](concepts-extensions.md).

I en Azure Database for PostgreSQL-Server kan du skapa en eller flera databaser. Du kan välja att skapa en databas per server om du vill använda dig av samtliga resurser, eller skapa flera databaser som får dela på resurserna. Priserna är strukturerad per server, baserat på konfigurationen av pris nivån, virtuella kärnor och lagring (GB). Mer information finns i [pris nivåer](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Hur gör jag för att ansluta och autentisera till en Azure Database for PostgreSQL-Server?
Följande element hjälper till att säkerställa säker åtkomst till databasen:

|||
|:--|:--|
| **Autentisering och auktorisering** | Azure Database for PostgreSQL-server stöder ursprunglig PostgreSQL-autentisering. Du kan ansluta och autentisera till servern med serverns administratörsinloggning. |
| **Protokoll** | Tjänsten stöder ett Message-baserat protokoll som används av PostgreSQL. |
| **TCP/IP** | Protokollet stöds över TCP/IP och över UNIX-domänsuffix. |
| **Brandvägg** | För att skydda dina data förhindrar en brand Väggs regel all åtkomst till servern och dess databaser tills du anger vilka datorer som har behörighet. Se [Azure Database for postgresql server brand Väggs regler](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Hantera servern
Du kan hantera Azure Database for PostgreSQL-servrar med hjälp av [Azure Portal](https://portal.azure.com) eller [Azure CLI](/cli/azure/postgres).

När du skapar en server konfigurerar du autentiseringsuppgifterna för administratörs användaren. Administratörs användaren är den högsta behörighets användaren som du har på servern. Den tillhör roll azure_pg_admin. Den här rollen har inte fullständig behörighet för superanvändare. 

Attributet PostgreSQL superanvändare tilldelas till azure_superuser, som tillhör den hanterade tjänsten. Du har inte åtkomst till den här rollen.

En Azure Database for PostgreSQL-Server har standard databaser: 
- **postgres** – en standard databas som du kan ansluta till när servern har skapats.
- **azure_maintenance** – den här databasen används för att avgränsa de processer som tillhandahåller den hanterade tjänsten från användar åtgärder. Du har inte åtkomst till den här databasen.
- **azure_sys** – en databas för frågearkivet. Den här databasen samlar inte data när Frågearkivet är inaktive rad. Detta är standardinställningen. Mer information finns i [Översikt över frågearkivet](concepts-query-store.md).


## <a name="server-parameters"></a>Serverparametrar
PostgreSQL-Server parametrarna bestämmer serverns konfiguration. I Azure Database for PostgreSQL kan listan över parametrar visas och redige ras med hjälp av Azure Portal eller Azure CLI. 

Som en hanterad tjänst för postgres, är de konfigurerbara parametrarna i Azure Database for PostgreSQL en delmängd av parametrarna i en lokal postgres-instans (mer information om postgres-parametrar finns i [postgresql-dokumentationen](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Din Azure Database for PostgreSQL-Server är aktive rad med standardvärden för varje parameter vid skapande. Vissa parametrar som kräver en omstart av servern eller superanvändare för att ändringarna ska börja gälla kan inte konfigureras av användaren.


## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns i [Azure Database for PostgreSQL översikt](overview.md).
- Information om vilka resurs kvoter och begränsningar som baseras på **tjänst nivån** finns i [tjänst nivåer](concepts-pricing-tiers.md).
- Information om hur du ansluter till tjänsten finns i [anslutnings bibliotek för Azure Database for PostgreSQL](concepts-connection-libraries.md).
- Visa och redigera Server parametrar via [Azure Portal](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).
