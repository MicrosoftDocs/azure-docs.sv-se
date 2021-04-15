---
title: Versioner som stöds – Azure Database for MySQL
description: Lär dig vilka versioner av MySQL-servern som stöds i Azure Database for MySQL tjänsten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1be15c16a1897797326ea869c34c3590ffb07691
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363877"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Azure Database for MySQL-serverversioner som stöds

Azure Database for MySQL har utvecklats från [MySQL Community Edition](https://www.mysql.com/products/community/)med hjälp av InnoDB-lagringsmotorn. Tjänsten stöder alla aktuella huvudversion som stöds av communityn, nämligen MySQL 5.6, 5.7 och 8.0. MySQL använder X.Y.Z-namngivningsschemat där X är huvudversionen, Y är delversionen och Z är buggkorrigeringsversionen. Mer information om schemat finns i [MySQL-dokumentationen.](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Ansluta till en gatewaynod som kör en specifik MySQL-version

I distributionsalternativet Enskild server används en gateway för att omdirigera anslutningarna till serverinstanser. När anslutningen har upprättats visar MySQL-klienten den version av MySQL som har angetts i gatewayen, inte den faktiska versionen som körs på MySQL-serverinstansen. Du kan ta reda på vilken version MySQL-serverinstansen har med hjälp av kommandot `SELECT VERSION();` i MySQL-prompten. Läs [Anslutningsarkitektur om](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) du vill veta mer om gatewayer i Azure Database for MySQL-tjänstarkitektur.

Eftersom Azure Database for MySQL stöder huvudversion v5.6, v5.7 och v8.0, kör standardporten 3306 för att ansluta till Azure Database for MySQL MySQL-klientversion 5.6 (minsta gemensamma nämnare) för att stödja anslutningar till servrar av alla tre huvudversioner som stöds. Men om programmet har ett krav på att ansluta till en specifik huvudversion, till exempel v5.7 eller v8.0, kan du göra det genom att ändra porten i serverns anslutningssträng.

I Azure Database for MySQL-tjänsten lyssnar gatewaynoder på port 3308 för v5.7-klienter och port 3309 för v8.0-klienter. Om du vill ansluta till v5.7-gatewayklienten bör du med andra ord använda det fullständiga servernamnet och port 3308 för att ansluta till servern från klientprogrammet. Om du vill ansluta till v8.0-gatewayklienten kan du på samma sätt använda det fullständigt kvalificerade servernamnet och port 3309 för att ansluta till servern. Mer information finns i följande exempel.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Exempel på anslutning via olika mysql-versioner för gateway":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Azure Database for MySQL stöder för närvarande följande större och mindre versioner av MySQL:


| Version | [Enskild server](overview.md) <br/> Aktuell delversion |[Flexibel server (förhandsversion)](/azure/mysql/flexible-server/overview) <br/> Aktuell delversion  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL version 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (har dragits tillbaka) | Stöds inte|
|MySQL version 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL version 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Läs dokumentationen om versionssupportprincip för tillbakadragna [versioner i dokumentationen för versionssupportprincip.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt korrigering av versionsuppdateringar för felkorrigeringar. Till exempel 5.7.20 till 5.7.21.  

Uppgradering av huvudversion stöds för närvarande av tjänsten för uppgraderingar från MySQL v5.6 till v5.7. Mer information finns i utföra [större versionsuppgraderingar.](how-to-major-version-upgrade.md) Om du vill uppgradera från 5.7 till 8.0 [](./concepts-migrate-dump-restore.md) rekommenderar vi att du dumpar och återställer till en server som har skapats med den nya motorversionen.

## <a name="next-steps"></a>Nästa steg

- Mer information om Azure Database for MySQL för versionshantering finns i det [här dokumentet.](concepts-version-policy.md)
- Information om specifika resurskvoter och begränsningar baserat på din **tjänstnivå finns** i [Tjänstnivåer](./concepts-pricing-tiers.md)
