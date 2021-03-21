---
title: Versioner som stöds – Azure Database for MariaDB
description: Lär dig vilka versioner av MariaDB-servern som stöds i Azure Database for MariaDBs tjänsten.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: dbf32626714200e6712c67b701ebc597c4a7ba7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662448"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Azure Database for MariaDB Server versioner som stöds

Azure Database for MariaDB har utvecklats från [MariaDB-servern](https://downloads.mariadb.org/)med öppen källkod med hjälp av InnoDB-motorn.

MariaDB använder namngivnings schemat X. Y. Z. X är huvud versionen, Y är den lägre versionen och Z är korrigerings versionen.

> [!NOTE]
> I tjänsten används en gateway för att omdirigera anslutningarna till serverinstanser. När anslutningen har upprättats visar MySQL-klienten den version av MariaDB som angetts i gatewayen, inte den faktiska versionen som körs på MariaDB-serverinstansen. Använd kommandot för att fastställa versionen av MariaDB Server-instansen `SELECT VERSION();` .

Azure Database for MariaDB stöder för närvarande följande version:

## <a name="mariadb-version-102"></a>MariaDB-version 10,2

Korrigerings version: 10.2.32

Läs mer om förbättringar och korrigeringar i den här versionen i [MariaDB-dokumentationen](https://mariadb.com/kb/en/mariadb-10232-release-notes/) .

## <a name="mariadb-version-103"></a>MariaDB-version 10,3

Korrigerings version: 10.3.23

Läs mer om förbättringar och korrigeringar i den här versionen i [MariaDB-dokumentationen](https://mariadb.com/kb/en/mariadb-10323-release-notes/) .

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt uppgraderingar för uppdaterings uppdateringar. Till exempel 10.2.21 till 10.2.23.  

För närvarande stöds inte uppgradering av lägre och högre versioner. Det finns till exempel inte stöd för att uppgradera från MariaDB 10.2 till MariaDB 10.3. Om du vill uppgradera från 10,2 till 10,3 tar du en [dump och återställer](./howto-migrate-dump-restore.md) den till en server som har skapats med den nya motor versionen.

## <a name="next-steps"></a>Nästa steg

- Information om vilka resurs kvoter och begränsningar som baseras på **tjänst nivån** finns i [tjänst nivåer](./concepts-pricing-tiers.md).
