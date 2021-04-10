---
title: Begränsningar och begränsningar – storskalig (citus) – Azure Database for PostgreSQL
description: Aktuella gränser för citus-Server grupper
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023996"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Gränser och begränsningar för Azure Database for PostgreSQL – citus-storlek

I följande avsnitt beskrivs kapacitets-och funktions gränser i citus-tjänsten (storskalig).

## <a name="maximum-connections"></a>Maximalt antal anslutningar

Varje PostgreSQL anslutning (även inaktiv) använder minst 10 MB minne, så det är viktigt att begränsa samtidiga anslutningar. Här följer de gränser som vi valde för att hålla noderna felfria:

* Koordinator-nod
   * Högsta antal anslutningar: 300
   * Högsta antal användar anslutningar: 297
* Arbetsnoden
   * Högsta antal anslutningar: 600
   * Högsta antal användar anslutningar: 597

> [!NOTE]
> I en Server grupp med aktiverade för [hands funktioner](hyperscale-preview-features.md) är anslutnings gränserna till koordinatorn något annorlunda:
>
> * Koordinator nod Max antal anslutningar
>    * 300 för 0-3 virtuella kärnor
>    * 500 för 4-15 virtuella kärnor
>    * 1000 för 16 + virtuella kärnor

Försök att ansluta bortom dessa gränser kommer att Miss lyckas med ett fel. Systemet reserverar tre anslutningar för övervakning av noder, vilket är anledningen till att det finns tre fler anslutningar tillgängliga för användar frågor än totalt antal anslutningar.

### <a name="connection-pooling"></a>Anslutningspoolen

Det tar tid att upprätta nya anslutningar. Det fungerar mot de flesta program, som begär många anslutningar för kort period. Vi rekommenderar att du använder en anslutningspool, både för att minska inaktiva transaktioner och återanvända befintliga anslutningar. Mer information finns i vårt [blogg inlägg](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

Du kan köra en egen anslutningspool eller använda PgBouncer som hanteras av Azure.

#### <a name="managed-pgbouncer-preview"></a>Hanterad PgBouncer (förhands granskning)

> [!IMPORTANT]
> Den hanterade PgBouncer-anslutningspoolen i storskalig (citus) är för närvarande en för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Du kan se en fullständig lista över andra nya funktioner i [för hands versions funktioner för skalning (citus)](hyperscale-preview-features.md).

Anslutningspooler som PgBouncer tillåter att fler klienter ansluter till koordinator-noden på en gång. Program ansluter till poolen och programpoolen vidarebefordrar kommandon till mål databasen.

När klienter ansluter via PgBouncer ändras inte antalet anslutningar som kan köras aktivt i databasen. I stället kan PgBouncer-köerna överskrida anslutningar och köra dem när databasen är klar.

Citus (storskalig) erbjuder nu en hanterad instans av PgBouncer för Server grupper (i för hands version). Den har stöd för upp till 2 000 samtidiga klient anslutningar.
Följ dessa steg om du vill ansluta via PgBouncer:

1. Gå till sidan **anslutnings strängar** för Server gruppen i Azure Portal.
2. Aktivera kryss rutan **PgBouncer anslutnings strängar**. (De listade anslutnings strängarna kommer att ändras.)
3. Uppdatera klient program för att ansluta till den nya strängen.

## <a name="storage-scaling"></a>Lagrings skalning

Lagring på koordinator-och arbetsnoder kan skalas upp (ökas), men kan inte skalas ned (minskat).

## <a name="storage-size"></a>Lagrings storlek

Upp till 2 TiB-lagring stöds för koordinator-och arbetsnoder. Se tillgängliga lagrings alternativ och IOPS-beräkning [ovan](concepts-hyperscale-configuration-options.md#compute-and-storage) för nodernas och kluster storlekarna.

## <a name="database-creation"></a>Skapa databas

Azure Portal innehåller autentiseringsuppgifter för att ansluta till exakt en databas per storskalig (citus) Server grupp, `citus` databasen. Det går inte att skapa en annan databas för tillfället, och kommandot Skapa databas kommer inte att fungera med ett fel.

## <a name="columnar-storage"></a>Kolumn lagring

Citus (storskalig) har för närvarande följande begränsningar med [kolumn tabeller](concepts-hyperscale-columnar.md):

* Komprimeringen är på disk, inte i minnet
* Enbart tillägg (stöd för uppdatering/borttagning)
* Ingen utrymmes återtagning (t. ex. återställnings transaktioner kanske fortfarande tar upp disk utrymme)
* Det finns inget stöd för index, index genomsökningar eller Bitmap-index
* Ingen tidscans
* Inga exempel genomsökningar
* Inget popup-stöd (stora värden som stöds infogas)
* Inget stöd för vid konflikt-instruktioner (förutom inga åtgärder utan mål har angetts).
* Inget stöd för tupel lås (Välj... FÖR RESURS VÄLJER DU... FÖR UPPDATERING)
* Inget stöd för isolerings nivån för serialiserbar
* Stöd för PostgreSQL-Server versioner 12 + endast
* Inget stöd för externa nycklar, unika begränsningar eller undantags begränsningar
* Ingen stöd för logisk avkodning
* Inget stöd för parallella genomsökningar inom noder
* Inget stöd för efter... FÖR varje rad utlösare
* Inga inloggade kolumn tabeller
* Inga tillfälliga kolumn tabeller

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en citus-Server (för skalning) i portalen](quickstart-create-hyperscale-portal.md).
