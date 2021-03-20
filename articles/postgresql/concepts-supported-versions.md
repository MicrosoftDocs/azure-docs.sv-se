---
title: Versioner som stöds – Azure Database for PostgreSQL-enskild server
description: Beskriver de postgres huvud-och del versioner som stöds i Azure Database for PostgreSQL-enskild server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 41662c5e4cc0ed9458f8b1b1279e2753daed789f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518461"
---
# <a name="supported-postgresql-major-versions"></a>PostgreSQL huvud versioner som stöds

Se [Azure Database for PostgreSQL versions princip](concepts-version-policy.md) för information om support policy.

Azure Database for PostgreSQL stöder för närvarande följande huvud versioner:

## <a name="postgresql-version-11"></a>PostgreSQL-version 11
Den aktuella del versionen är 11,6. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/11/static/release-11-6.html) .

## <a name="postgresql-version-10"></a>PostgreSQL version 10
Den aktuella del versionen är 10,11. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/10/static/release-10-11.html) .

## <a name="postgresql-version-96"></a>PostgreSQL-version 9,6
Den aktuella del versionen är 9.6.16. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) .

## <a name="postgresql-version-95-retired"></a>PostgreSQL version 9,5 (pensionerad)
Genom att anpassa med postgres Communitys [princip för versions hantering](https://www.postgresql.org/support/versioning/)har Azure Database for PostgreSQL dragit tillbaka Postgres version 9,5 från och med 11 februari 2021. Mer information och begränsningar finns i [Azure Database for PostgreSQL versions policy](concepts-version-policy.md) . Om du kör den här huvud versionen uppgraderar du till en högre version, helst till PostgreSQL 11 vid din tidigaste bekvämlighet.

## <a name="managing-upgrades"></a>Hantera uppgraderingar
PostgreSQL-projektet utfärdar regelbundet mindre versioner för att åtgärda rapporterade buggar. Azure Database for PostgreSQL korrigerar automatiskt servrar med delversioner under tjänstens månatliga distributioner. 

Automatiska uppgraderingar på plats för huvud versioner stöds inte. Om du vill uppgradera till en högre huvud version kan du 
   * Använd en av metoderna som dokumenteras i [större versions uppgraderingar med dumpa och Återställ](./how-to-upgrade-using-dump-and-restore.md).
   * Använd [pg_dump och pg_restore](./howto-migrate-using-dump-and-restore.md) för att flytta en databas till en server som skapats med den nya motor versionen.
   * Använd [Azure Database migration service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) för att utföra online-uppgraderingar.

### <a name="version-syntax"></a>Versions syntax
Innan PostgreSQL version 10 anses den [postgresql versions principen](https://www.postgresql.org/support/versioning/) en _större versions_ uppgradering för att öka det första _eller_ andra numret. 9,5 till 9,6 ansågs till exempel vara en _huvud_ versions uppgradering. Från och med version 10 betraktas bara en ändring i det första talet som en huvud versions uppgradering. Till exempel är 10,0 till 10,1 en _mindre_ versions uppgradering. Version 10 till 11 är en _huvud_ versions uppgradering.

## <a name="next-steps"></a>Nästa steg
Information om PostgreSQL-tillägg som stöds finns i [tillägg-dokumentet](concepts-extensions.md).
