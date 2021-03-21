---
title: Versions princip – Azure Database for PostgreSQL-enskild server och flexibel Server (för hands version)
description: Beskriver principen kring postgres-större och lägre versioner i Azure Database for PostgreSQL-enskild server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 62fe1b3391eb4cb2d409a92b936fd3f1ae56d992
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518427"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Versionspolicy för Azure Database for PostgreSQL

Den här sidan beskriver principen för Azure Database for PostgreSQL versions hantering och gäller för distributions lägena Azure Database for PostgreSQL-enskild server och Azure Database for PostgreSQL-flexibel Server (för hands version).

## <a name="supported--postgresql-versions"></a>PostgreSQL-versioner som stöds

Azure Database for PostgreSQL stöder följande databas versioner.

| Version | Enskild server | Flexibel server (förhandsversion) |
| ----- | :------: | :----: |
| PostgreSQL 12 |  | X  | 
| PostgreSQL 11 | X | X |
| PostgreSQL 10 | X |  |
| PostgreSQL 9,6 | X |  |
| *PostgreSQL 9,5 (dras tillbaka)* | X |  |

## <a name="major-version-support"></a>Huvud versions stöd
Varje större version av PostgreSQL kommer att stödjas av Azure Database for PostgreSQL från och med det datum då Azure börjar stödja versionen tills versionen har dragits tillbaka av PostgreSQL-communityn, enligt vad som anges i [postgresql community-versions princip](https://www.postgresql.org/support/versioning/).

## <a name="minor-version-support"></a>Lägre versions stöd
Azure Database for PostgreSQL utför automatiskt lägre versions uppgraderingar till den Azure föredra PostgreSQL-version som en del av det periodiska underhållet. 

## <a name="major-version-retirement-policy"></a>Princip för tillbakadragning av huvud version
I tabellen nedan visas information om pensionering för PostgreSQL-huvud versioner. Datumen följer [policyn för postgresql community-versioner](https://www.postgresql.org/support/versioning/).

| Version | Nyheter | Start datum för Azure-support | Datum för indragning|
| ----- | ----- | ------ | ----- |
| [PostgreSQL 9,5 (dras tillbaka)](https://www.postgresql.org/about/news/postgresql-132-126-1111-1016-9621-and-9525-released-2165/)| [Funktioner](https://www.postgresql.org/docs/9.5/release-9-5.html)  | Den 18 april 2018   | 11 februari 2021
| [PostgreSQL 9,6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [Funktioner](https://wiki.postgresql.org/wiki/NewIn96) | Den 18 april 2018  | 11 november 2021
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [Funktioner](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 4 juni 2018  | 10 november 2022
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [Funktioner](https://www.postgresql.org/docs/11/release-11.html) | 24 juli 2019  | 9 november 2023
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [Funktioner](https://www.postgresql.org/docs/12/release-12.html) | Den 22 september 2020  | 14 november 2024

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>PostgreSQL motor versioner som inte stöds i Azure Database for PostgreSQL

Du kan fortsätta att köra den återkallade versionen i Azure Database for PostgreSQL. Observera dock följande begränsningar efter indragnings datumet för varje PostgreSQL-databas version:
- Eftersom communityn inte kommer att släppa några ytterligare fel korrigeringar eller säkerhets korrigeringar, kommer Azure Database for PostgreSQL inte att uppdatera den indragna databas motorn för några buggar eller säkerhets problem eller på annat sätt vidta säkerhets åtgärder avseende den indragna databas motorn. Du kan råka ut för säkerhets risker eller andra problem. Men Azure fortsätter att utföra periodiskt underhåll och korrigering för värden, operativ systemet, behållare och andra service komponenter.
- Om något av de support ärenden som du kan stöta på rör PostgreSQL-databasen kanske vi inte kan ge support. I sådana fall måste du uppgradera databasen för att vi ska kunna ge dig all support.
- Du kommer inte att kunna skapa nya databas servrar för den tillbakadragna versionen. Du kommer dock att kunna utföra återställningar av tidpunkter och skapa Läs repliker för dina befintliga servrar.
- Nya tjänst funktioner som har utvecklats av Azure Database for PostgreSQL kanske bara är tillgängliga för databas server versioner som stöds.
- Drift tiden service avtal gäller enbart för Azure Database for PostgreSQL tjänstrelaterade problem och inte avbrotts tid som orsakas av databas motor-relaterade buggar.  
- I extrema händelse av ett allvarligt hot mot tjänsten som orsakas av säkerhets problemet med PostgreSQL-databasmotorn som identifierats i den indragna databas versionen, kan Azure välja att stoppa databas servern för att skydda tjänsten. I så fall får du ett meddelande om att uppgradera servern innan du tar servern online.

## <a name="postgresql-version-syntax"></a>Syntax för PostgreSQL-version
Innan PostgreSQL version 10 anses den [postgresql versions principen](https://www.postgresql.org/support/versioning/) en _större versions_ uppgradering för att öka det första _eller_ andra numret. 9,5 till 9,6 ansågs till exempel vara en _huvud_ versions uppgradering. Från och med version 10 betraktas bara en ändring i det första talet som en huvud versions uppgradering. Till exempel är 10,0 till 10,1 en _mindre_ versions uppgradering. Version 10 till 11 är en _huvud_ versions uppgradering.

## <a name="next-steps"></a>Nästa steg
- Se Azure Database for PostgreSQL-versioner som [stöds](./concepts-supported-versions.md) av en server
- Se Azure Database for PostgreSQL-flexibel Server (för hands version) [versioner som stöds](flexible-server/concepts-supported-versions.md)
- Information om hur du utför större versions uppgraderingar finns i dokumentationen för [viktiga uppgraderingar](how-to-upgrade-using-dump-and-restore.md) .
- Information om PostgreSQL-tillägg som stöds finns i [tillägg-dokumentet](concepts-extensions.md).
