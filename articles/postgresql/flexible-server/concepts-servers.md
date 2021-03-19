---
title: Servrar i Azure Database for PostgreSQL-flexibel Server (för hands version)
description: Den här artikeln innehåller överväganden och rikt linjer för att konfigurera och hantera Azure Database for PostgreSQL-flexibel Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 5cd4454d5b77d18940f6693a25a691a01f4fedec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90942012"
---
# <a name="servers---azure-database-for-postgresql---flexible-server"></a>Servrar-Azure Database for PostgreSQL – flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Den här artikeln innehåller överväganden och rikt linjer för att arbeta med Azure Database for PostgreSQL-flexibel Server.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Vad är en Azure Database for PostgreSQL-server?

En server i alternativet Azure Database for PostgreSQL-flexibel Server distribution är en central administrations plats för flera databaser. Det är samma PostgreSQL Server-konstruktion som du kanske är van vid i den lokala världen. Mer specifikt är PostgreSQL-tjänsten hanterad, ger prestanda garantier, visar åtkomst och funktioner på server nivå.

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

I en Azure Database for PostgreSQL-Server kan du skapa en eller flera databaser. Du kan välja att skapa en databas per server om du vill använda dig av samtliga resurser, eller skapa flera databaser som får dela på resurserna. Priserna är strukturerad per server, baserat på konfigurationen av pris nivån, virtuella kärnor och lagring (GB). Mer information finns i [alternativ för beräkning och lagring](concepts-compute-storage.md).

## <a name="how-do-i-connect-and-authenticate-to-the-database-server"></a>Hur gör jag för att ansluta och autentisera till databas servern?

Följande element hjälper till att säkerställa säker åtkomst till databasen:

|||
|:--|:--|
| **Autentisering och auktorisering** | Azure Database for PostgreSQL-server stöder ursprunglig PostgreSQL-autentisering. Du kan ansluta och autentisera till servern med serverns administratörsinloggning. |
| **Protokoll** | Tjänsten stöder ett Message-baserat protokoll som används av PostgreSQL. |
| **TCP/IP** | Protokollet stöds över TCP/IP och över UNIX-domänsuffix. |
| **Brandvägg** | För att skydda dina data förhindrar en brand Väggs regel all åtkomst till servern och dess databaser tills du anger vilka datorer som har behörighet. Se [Azure Database for postgresql server brand Väggs regler](how-to-manage-firewall-portal.md). |

## <a name="managing-your-server"></a>Hantera servern

Du kan hantera Azure Database for PostgreSQL-servrar med hjälp av [Azure Portal](https://portal.azure.com) eller [Azure CLI](/cli/azure/postgres).

När du skapar en server konfigurerar du autentiseringsuppgifterna för administratörs användaren. Administratörs användaren är den högsta behörighets användaren som du har på servern. Den tillhör roll azure_pg_admin. Den här rollen har inte fullständig behörighet för superanvändare. 

Attributet PostgreSQL superanvändare tilldelas till azure_superuser, som tillhör den hanterade tjänsten. Du har inte åtkomst till den här rollen.

En Azure Database for PostgreSQL-Server har standard databaser: 

- **postgres** – en standard databas som du kan ansluta till när servern har skapats.
- **azure_maintenance** – den här databasen används för att avgränsa de processer som tillhandahåller den hanterade tjänsten från användar åtgärder. Du har inte åtkomst till den här databasen.

## <a name="server-parameters"></a>Serverparametrar

PostgreSQL-Server parametrarna bestämmer serverns konfiguration. I Azure Database for PostgreSQL kan listan över parametrar visas och redige ras med hjälp av Azure Portal eller Azure CLI.

Som en hanterad tjänst för postgres, är de konfigurerbara parametrarna i Azure Database for PostgreSQL en delmängd av parametrarna i en lokal postgres-instans (mer information om postgres-parametrar finns i [postgresql-dokumentationen](https://www.postgresql.org/docs/12/static/runtime-config.html)). Din Azure Database for PostgreSQL-Server är aktive rad med standardvärden för varje parameter vid skapande. Vissa parametrar som kräver en omstart av servern eller superanvändare för att ändringarna ska börja gälla kan inte konfigureras av användaren.

## <a name="next-steps"></a>Nästa steg

- En översikt över tjänsten finns i [Azure Database for PostgreSQL översikt](overview.md).
- Information om vissa resurs kvoter och begränsningar utifrån **konfigurationen** finns i [alternativ för beräkning och lagring](concepts-compute-storage.md).
- Visa och redigera Server parametrar via [Azure Portal](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md).
