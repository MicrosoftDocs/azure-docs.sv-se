---
title: Versioner som stöds – Azure Database for MySQL
description: Lär dig vilka versioner av MySQL-servern som stöds i Azure Database for MySQLs tjänsten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8b85307f01a11366a2147c947f26658f548932e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467722"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Azure Database for MySQL Server versioner som stöds

Azure Database for MySQL har utvecklats från [MySQL Community Edition](https://www.mysql.com/products/community/)med hjälp av lagrings motorn InnoDB. Tjänsten stöder all nuvarande huvud version som stöds av communityn, nämligen MySQL 5,6, 5,7 och 8,0. MySQL använder namngivnings schemat X. Y. Z där X är huvud versionen, Y är den lägre versionen och Z är versionen av fel korrigeringen. Mer information om schemat finns i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> I distributions alternativet för enskild server används en gateway för att omdirigera anslutningarna till Server instanser. När anslutningen har upprättats visar MySQL-klienten den version av MySQL som har angetts i gatewayen, inte den faktiska versionen som körs på MySQL-serverinstansen. Du kan ta reda på vilken version MySQL-serverinstansen har med hjälp av kommandot `SELECT VERSION();` i MySQL-prompten.

Azure Database for MySQL stöder för närvarande följande högre och lägre versioner av MySQL:


| Version | Enskild server <br/> Aktuell del version |Flexibel server (förhandsversion) <br/> Aktuell del version  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL version 5,6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (dras tillbaka) | Stöds inte|
|MySQL version 5,7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL version 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Läs versions support policyn för indragna versioner i [dokumentation om versions policyn.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt korrigeringar för fel korrigering av versions uppdateringar. Till exempel 5.7.20 till 5.7.21.  

Huvud versions uppgradering stöds för närvarande av tjänsten för uppgraderingar från MySQL v 5.6 till v 5.7. Mer information finns [i så här utför du viktiga uppgraderingar av versioner](how-to-major-version-upgrade.md). Om du vill uppgradera från 5,7 till 8,0 rekommenderar vi att du utför [dumpning och återställning](./concepts-migrate-dump-restore.md) till en server som har skapats med den nya motor versionen.

## <a name="next-steps"></a>Nästa steg

- Mer information kring Azure Database for MySQL versions policy finns i [det här dokumentet](concepts-version-policy.md).
- Information om vilka resurs kvoter och begränsningar som baseras på **tjänst nivån** finns i [tjänst nivåer](./concepts-pricing-tiers.md)
