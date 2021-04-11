---
title: Versioner som stöds – Azure Database for PostgreSQL-flexibel Server
description: Beskriver de PostgreSQL huvud-och del versioner som stöds i Azure Database for PostgreSQL-flexibel Server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 5fda7ebb5a72dd9bbfab0ba72511540cf141563f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608858"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL huvud versioner som stöds i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Azure Database for PostgreSQL-flexibel Server stöder för närvarande följande huvud versioner:

## <a name="postgresql-version-12"></a>PostgreSQL version 12

Den aktuella del versionen är 12,5. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/12/static/release-12-4.html) .

## <a name="postgresql-version-11"></a>PostgreSQL-version 11

Den aktuella del versionen är 11,10. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/11/static/release-11-9.html) .

## <a name="postgresql-version-10-and-older"></a>PostgreSQL version 10 och äldre

Vi stöder inte PostgreSQL version 10 och äldre för Azure Database for PostgreSQL-flexibel Server. Använd distributions alternativet för [enskild server](../concepts-supported-versions.md) om du behöver äldre versioner.

## <a name="managing-upgrades"></a>Hantera uppgraderingar

PostgreSQL-projektet utfärdar regelbundet mindre versioner för att åtgärda rapporterade buggar. Azure Database for PostgreSQL korrigerar automatiskt servrar med delversioner under tjänstens månatliga distributioner.

Automation för större versions uppgradering stöds inte ännu. Till exempel finns det för närvarande ingen automatisk uppgradering från PostgreSQL 11 till PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->