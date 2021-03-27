---
title: Servrar-Azure Database for MariaDB
description: Det här avsnittet innehåller överväganden och rikt linjer för att arbeta med Azure Database for MariaDB-servrar.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9a5cd55576d505114c15028cf637114f3baf7668
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627990"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Server koncept i Azure Database for MariaDB
Den här artikeln innehåller överväganden och rikt linjer för att arbeta med Azure Database for MariaDB-servrar.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Vad är en Azure Database for MariaDB-Server?

En Azure Database for MariaDB-Server är en central administrativ punkt för flera databaser. Det är samma MariaDB Server-konstruktion som du kanske är van vid i den lokala världen. Mer specifikt är Azure Database for MariaDBs tjänsten hanterad, ger prestanda garantier och visar åtkomst och funktioner på server nivå.

En Azure Database for MariaDB Server:

- Skapas i en Azure-prenumeration.
- Är överordnad resurs för databaser.
- Innehåller ett namn område för databaser.
- Är en behållare med stark livs längd – ta bort en server och tar bort de inneslutna databaserna.
- Samlar resurser i en region.
- Tillhandahåller en anslutnings slut punkt för Server-och databas åtkomst.
- Tillhandahåller omfattning för hanterings principer som gäller för databaserna: inloggning, brand vägg, användare, roller, konfigurationer osv.
- Är tillgänglig i MariaDB-motor version 10,2. Mer information finns i [Azure Database for MariaDB databas versioner som stöds](./concepts-supported-versions.md).

Du kan skapa en eller flera databaser på en Azure Database for MariaDB-server. Du kan välja att skapa en enskild databas per server för att använda alla resurser eller för att skapa flera databaser för att dela resurserna. Priserna är strukturerad per server, baserat på konfigurationen av pris nivån, virtuella kärnor och lagring (GB). Mer information finns i [pris nivåer](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Hur gör jag för att säkra en Azure Database for MariaDB-Server?

Följande element hjälper till att säkerställa säker åtkomst till din databas.

| Säkerhets koncept | Description |
| :-- | :-- |
| **Autentisering och auktorisering** | Azure Database for MariaDB Server stöder inbyggd MySQL-autentisering. Du kan ansluta och autentisera till en server med serverns Administratörs inloggning. |
| **Protokoll** | Tjänsten har stöd för ett Message-baserat protokoll som används av MySQL. |
| **TCP/IP** | Protokollet stöds över TCP/IP och över UNIX-domänsuffix. |
| **Brandvägg** | För att skydda dina data förhindrar en brand Väggs regel all åtkomst till din databas server tills du anger vilka datorer som har behörighet. Se [Azure Database for MariaDB Server brand Väggs regler](./concepts-firewall-rules.md). |
| **SSL** | Tjänsten har stöd för att tvinga SSL-anslutningar mellan dina program och databas servern. Se [Konfigurera SSL-anslutning i ditt program för att ansluta till Azure Database for MariaDB på ett säkert sätt](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mariadb-preview"></a>Stoppa/starta en Azure Database for MariaDB (förhands granskning)
Azure Database for MariaDB ger dig möjlighet att **stoppa** servern när den inte används och **Starta** servern när du återupptar aktiviteten. Detta görs i grunden för att spara kostnaderna på databas servrarna och bara betala för resursen när den används. Detta blir ännu mer viktigt för arbets belastningar för utveckling och testning och när du bara använder servern för en del av dagen. När du stoppar servern kommer alla aktiva anslutningar att tas bort. Senare kan du antingen använda [Azure Portal](../mysql/how-to-stop-start-server.md) eller [CLI](../mysql/how-to-stop-start-server.md)när du vill ta tillbaka servern online.

När servern är i **stoppat** läge debiteras inte serverns beräkning. Lagrings utrymmet fortsätter dock att faktureras eftersom serverns lagrings utrymme är kvar för att säkerställa att datafiler är tillgängliga när servern startas igen.

> [!IMPORTANT]
> När du **stoppar** servern förblir den kvar i det läget under de kommande 7 dagarna i en storlek. Om du inte **startar** den manuellt under den här tiden startas servern automatiskt i slutet av sju dagar. Du kan välja att **stoppa** det igen om du inte använder servern.

När tids servern stoppas kan inga hanterings åtgärder utföras på servern. För att kunna ändra konfigurations inställningar på-servern måste du [starta-servern](../mysql/how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Begränsningar för stoppa/starta-åtgärd
- Stöds inte med konfigurationer för Läs-replikering (både källa och repliker).

## <a name="how-do-i-manage-a-server"></a>Hur gör jag för att hantera en server?
Du kan hantera Azure Database for MariaDB-servrar med hjälp av Azure Portal eller Azure CLI.

## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns i [Azure Database for MariaDB översikt](./overview.md)
- Information om vilka resurs kvoter och begränsningar som baseras på **tjänst nivån** finns i [tjänst nivåer](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
