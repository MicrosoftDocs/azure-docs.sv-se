---
title: Princip för versionsstöd – Azure Database for MySQL – enskild server och flexibel server (förhandsversion)
description: Beskriver principen kring MySQL-huvudversioner och mindre versioner i Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 49e59c43e9eaedf770b1a8e052dd73aa331d31ce
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389578"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Azure Database for MySQL princip för versionsstöd

Den här sidan beskriver Azure Database for MySQL-versionshanteringsprincipen och gäller för distributionslägen Azure Database for MySQL – enskild server och Azure Database for MySQL – flexibel server (förhandsversion).

## <a name="supported--mysql-versions"></a>MySQL-versioner som stöds

Azure Database for MySQL har utvecklats från [MySQL Community Edition](https://www.mysql.com/products/community/)med hjälp av InnoDB-lagringsmotorn. Tjänsten stöder alla aktuella huvudversion som stöds av communityn, nämligen MySQL 5.6, 5.7 och 8.0. MySQL använder X.Y.Z-namngivningsschemat där X är huvudversionen, Y är delversionen och Z är buggkorrigeringsversionen. Mer information om schemat finns i [MySQL-dokumentationen.](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)

Azure Database for MySQL stöder för närvarande följande större och mindre versioner av MySQL:

| Version | [Enskild server](overview.md) <br/> Aktuell delversion |[Flexibel server (förhandsversion)](/azure/mysql/flexible-server/overview) <br/> Aktuell delversion  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL version 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(har dragits tillbaka) | Stöds inte|
|MySQL version 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL version 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> I distributionsalternativet Enskild server används en gateway för att omdirigera anslutningarna till serverinstanser. När anslutningen har upprättats visar MySQL-klienten den version av MySQL som har angetts i gatewayen, inte den faktiska versionen som körs på MySQL-serverinstansen. Du kan ta reda på vilken version MySQL-serverinstansen har med hjälp av kommandot `SELECT VERSION();` i MySQL-prompten. Om ditt program har ett krav på att ansluta till en specifik huvudversion, till exempel v5.7 eller v8.0, kan du göra det genom att ändra porten i serveranslutningssträngen enligt förklaringen i vår dokumentation [här.](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version)

> [!IMPORTANT]
> MySQL v5.6 dras tillbaka på enskild server från och med febuary 2021. Från och med 1 september 2021 kommer du inte att kunna skapa nya v5.6-servrar på Azure Database for MySQL – distributionsalternativ för enskild server. Du kommer dock att kunna utföra återställningar till tidpunkt och skapa skrivskyddade repliker för dina befintliga servrar.

Läs versionssupportprincipen för tillbakadragna versioner i [dokumentationen för versionssupportprincip.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="major-version-support"></a>Stöd för huvudversion
Varje huvudversion av MySQL kommer att stödjas av Azure Database for MySQL från det datum då Azure börjar stödja versionen tills versionen dras tillbaka av MySQL-communityn, enligt vad som anges i [versionspolicyn](https://www.mysql.com/support/eol-notice.html).

## <a name="minor-version-support"></a>Stöd för lägre versioner
Azure Database for MySQL automatiskt lägre versionsuppgraderingar till Den MySQL-version som rekommenderas av Azure som en del av det periodiska underhållet. 

## <a name="major-version-retirement-policy"></a>Tillbakadragandeprincip för huvudversion
Tabellen nedan innehåller information om tillbakagången för MySQL-huvudversioner. Datumen följer [MySQL-versionshanteringsprincipen](https://www.mysql.com/support/eol-notice.html).

| Version | Nyheter | Startdatum för Azure-support | Förfallodatum|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funktioner](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | Den 20 mars 2018 | Februari 2021
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Funktioner](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | Den 20 mars 2018 | Oktober 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Funktioner](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | Den 11 december 2019 | April 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Tillbakadragna MySQL-motorversioner stöds inte i Azure Database for MySQL

Observera följande begränsningar om du fortsätter att köra den tillbakadragna versionen efter tillbakadragna versioner för varje MySQL-databasversion:
- Eftersom communityn inte kommer att släppa några ytterligare felkorrigeringar eller säkerhetskorrigeringar kommer Azure Database for MySQL inte att korrigera den tillbakadragna databasmotorn för några buggar eller säkerhetsproblem eller på annat sätt vidta säkerhetsåtgärder med avseende på den tillbakadragna databasmotorn. Azure fortsätter dock att utföra regelbundet underhåll och korrigeringar för värden, operativsystemet, containrar och andra tjänstrelaterade komponenter.
- Om något supportproblem som kan uppstå gäller MySQL-databasen kanske vi inte kan ge dig support. I sådana fall måste du uppgradera din databas för att vi ska kunna ge dig all support.
- Du kommer inte att kunna skapa nya databasservrar för den tillbakadragna versionen. Du kommer dock att kunna utföra återställningar till tidpunkt och skapa skrivskyddade repliker för dina befintliga servrar.
- Nya tjänstfunktioner som utvecklats av Azure Database for MySQL kanske bara är tillgängliga för databasserverversioner som stöds.
- Serviceavtal för drifttid gäller endast för Azure Database for MySQL av tjänstrelaterade problem och inte på driftstopp som orsakas av fel relaterade till databasmotorn.  
- I extrema fall av ett allvarligt hot mot tjänsten som orsakas av säkerhetsrisken för MySQL-databasmotorn som identifierades i den tillbakadragna databasversionen kan Azure välja att stoppa beräkningsnoden på databasservern för att skydda tjänsten först. Du uppmanas att uppgradera servern innan du tar servern online. Under uppgraderingsprocessen skyddas dina data alltid med hjälp av automatiska säkerhetskopieringar som utförs på tjänsten och som kan användas för att återställa tillbaka till den äldre versionen om så önskas. 



## <a name="next-steps"></a>Nästa steg
- Se Azure Database for MySQL – versioner som stöds av [en server](./concepts-supported-versions.md)
- Se Azure Database for MySQL – flexibel server (förhandsversion) [som stöds](flexible-server/concepts-supported-versions.md)
- Se MySQL-dump [och återställning för](./concepts-migrate-dump-restore.md) att utföra uppgraderingar.
