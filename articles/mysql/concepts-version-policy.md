---
title: Princip för versions support – Azure Database for MySQL-enskild server och flexibel Server (för hands version)
description: Beskriver principen kring de större och lägre MySQL-versionerna i Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 8ad79f2f27864b4fbc78b7c104828230ff7f93bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103465665"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Princip för Azure Database for MySQL versions support

Den här sidan beskriver principen för Azure Database for MySQL versions hantering och gäller för distributions lägena Azure Database for MySQL-enskild server och Azure Database for MySQL-flexibel Server (för hands version).

## <a name="supported--mysql-versions"></a>MySQL-versioner som stöds

Azure Database for MySQL har utvecklats från [MySQL Community Edition](https://www.mysql.com/products/community/)med hjälp av lagrings motorn InnoDB. Tjänsten stöder all nuvarande huvud version som stöds av communityn, nämligen MySQL 5,6, 5,7 och 8,0. MySQL använder namngivnings schemat X. Y. Z där X är huvud versionen, Y är den lägre versionen och Z är versionen av fel korrigeringen. Mer information om schemat finns i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> I distributions alternativet för enskild server används en gateway för att omdirigera anslutningarna till Server instanser. När anslutningen har upprättats visar MySQL-klienten den version av MySQL som har angetts i gatewayen, inte den faktiska versionen som körs på MySQL-serverinstansen. Du kan ta reda på vilken version MySQL-serverinstansen har med hjälp av kommandot `SELECT VERSION();` i MySQL-prompten.

Azure Database for MySQL stöder för närvarande följande högre och lägre versioner av MySQL:

| Version | Enskild server <br/> Aktuell del version |Flexibel server (förhandsversion) <br/> Aktuell del version  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL version 5,6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(dras tillbaka) | Stöds inte|
|MySQL version 5,7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL version 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Läs versions support policyn för indragna versioner i [dokumentation om versions policyn.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="major-version-support"></a>Huvud versions stöd
Varje huvud version av MySQL kommer att stödjas av Azure Database for MySQL från det datum då Azure börjar stödja versionen tills versionen har dragits tillbaka av MySQL-communityn, som anges i [versions principen](https://www.mysql.com/support/eol-notice.html).

## <a name="minor-version-support"></a>Lägre versions stöd
Azure Database for MySQL utför automatiskt lägre versions uppgraderingar till den Azure Preferred MySQL-version som en del av det periodiska underhållet. 

## <a name="major-version-retirement-policy"></a>Princip för tillbakadragning av huvud version
I tabellen nedan visas pensions information för MySQLs huvud versioner. Datumen följer [MySQL-versions principen](https://www.mysql.com/support/eol-notice.html).

| Version | Nyheter | Start datum för Azure-support | Datum för indragning|
| ----- | ----- | ------ | ----- |
| [MySQL 5,6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funktioner](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 mars 2018 | Februari 2021
| [MySQL 5,7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Funktioner](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 mars 2018 | Oktober 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Funktioner](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11 december 2019 | April 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Tillbakadragna MySQL Engine-versioner som inte stöds i Azure Database for MySQL

Tänk på följande begränsningar när du fortsätter att köra den indragna versionen för varje version av MySQL-databasen:
- Eftersom communityn inte kommer att släppa några ytterligare fel korrigeringar eller säkerhets korrigeringar, kommer Azure Database for MySQL inte att uppdatera den indragna databas motorn för några buggar eller säkerhets problem eller på annat sätt vidta säkerhets åtgärder avseende den indragna databas motorn. Men Azure fortsätter att utföra periodiskt underhåll och korrigering för värden, operativ systemet, behållare och andra service komponenter.
- Om ett support ärende som du kan stöta på är relaterat till MySQL-databasen kanske vi inte kan ge support. I sådana fall måste du uppgradera databasen för att vi ska kunna ge dig all support.
<!-- - You will not be able to create new database servers for the retired version. However, you will be able to perform point-in-time recoveries and create read replicas for your existing servers. -->
- Nya tjänst funktioner som har utvecklats av Azure Database for MySQL kanske bara är tillgängliga för databas server versioner som stöds.
- Drift tiden service avtal gäller enbart för Azure Database for MySQL tjänstrelaterade problem och inte avbrotts tid som orsakas av databas motor-relaterade buggar.  
- I extrema händelse av ett allvarligt hot mot tjänsten som orsakas av säkerhets problemet för MySQL-databasmotorn som identifierats i den indragna databas versionen, kan Azure välja att stoppa beräknings noden på databas servern för att skydda tjänsten först. Du uppmanas att uppgradera servern innan du tar servern online. Under uppgraderings processen skyddas dina data alltid med automatiska säkerhets kopieringar som utförs på tjänsten som kan användas för att återställa till den äldre versionen om du vill. 



## <a name="next-steps"></a>Nästa steg
- Se Azure Database for MySQL-versioner som [stöds](./concepts-supported-versions.md) av en server
- Se Azure Database for MySQL-flexibel Server (för hands version) [versioner som stöds](flexible-server/concepts-supported-versions.md)
- Se MySQL [dump and Restore](./concepts-migrate-dump-restore.md) för att utföra uppgraderingar.
