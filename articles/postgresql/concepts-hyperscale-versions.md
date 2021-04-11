---
title: Versioner som stöds – storskalig (citus)-Azure Database for PostgreSQL
description: PostgreSQL-versioner som är tillgängliga i Azure Database for PostgreSQL-storskalig (citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d8a584b6ba752e8f9220defa575f519828ba07e6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023993"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Databas versioner som stöds i Azure Database for PostgreSQL – storskalig (citus)

## <a name="postgresql-versions"></a>PostgreSQL-versioner

> [!IMPORTANT]
> Anpassningsbara PostgreSQL-versioner i storskalig (citus) är för närvarande en för hands version.  Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Du kan se en fullständig lista över andra nya funktioner i [för hands versions funktioner för skalning (citus)](hyperscale-preview-features.md).

Den version av PostgreSQL som körs i en citus ()-Server grupp är anpassningsbar under skapandet. Att välja något annat än version 11 är för närvarande en för hands versions funktion.

Storskalig (citus) stöder för närvarande följande huvud versioner:

### <a name="postgresql-version-13-preview"></a>PostgreSQL version 13 (för hands version)

Den aktuella del versionen är 13,2. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/13/static/release-13-2.html) .

### <a name="postgresql-version-12-preview"></a>PostgreSQL version 12 (förhands granskning)

Den aktuella del versionen är 12,6. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/12/static/release-12-6.html) .

### <a name="postgresql-version-11"></a>PostgreSQL-version 11

Den aktuella del versionen är 11,11. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/11/static/release-11-11.html) .

### <a name="postgresql-version-10-and-older"></a>PostgreSQL version 10 och äldre

Vi stöder inte PostgreSQL version 10 och äldre för Azure Database for PostgreSQL-storskalig (citus).

## <a name="citus-and-other-extension-versions"></a>Citus och andra tilläggs versioner

Beroende på vilken version av PostgreSQL som körs i en Server grupp, kommer olika [versioner av postgres-tillägg](concepts-hyperscale-extensions.md) också att installeras.  Postgres 13 levereras särskilt med citus 10, och tidigare postgres-versioner levereras med citus 9,5.

## <a name="next-steps"></a>Nästa steg

* Se vilka [tillägg](concepts-hyperscale-extensions.md) som installeras i vilka versioner.
* Lär dig att [skapa en citus-Server (för skalning)](quickstart-create-hyperscale-portal.md).
