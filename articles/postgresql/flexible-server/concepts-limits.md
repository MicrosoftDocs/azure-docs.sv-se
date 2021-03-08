---
title: Gränser – Azure Database for PostgreSQL-flexibel Server
description: Den här artikeln beskriver gränser i Azure Database for PostgreSQL-flexibel Server, till exempel antalet anslutnings-och lagrings motor alternativ.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 58f71ddc470c06e17ff73dd5681cd343bcf8ac7b
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102451905"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Gränser i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

I följande avsnitt beskrivs kapacitets-och funktions gränser i databas tjänsten. Om du vill lära dig mer om resurs (beräknings-, minnes-, lagrings-) nivåer, se artikeln [beräkning och lagring](concepts-compute-storage.md) .

## <a name="maximum-connections"></a>Maximalt antal anslutningar

Det maximala antalet anslutningar per pris nivå och virtuella kärnor visas nedan. Azure-systemet kräver tre anslutningar för att övervaka den Azure Database for PostgreSQL-flexibla servern.

| SKU-namn             | Virtuella kärnor | Minnes storlek | Max antal anslutningar | Maximalt antal användar anslutningar |
|----------------------|--------|-------------|-----------------|----------------------|
| **Burstable**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **Generell användning**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32 GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64 GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192 GiB     | 5000            | 4997                 |
| D64s_v3              | 64     | 256 GiB     | 5000            | 4997                 |
| **Minnesoptimerade** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32 GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64 GiB      | 5000            | 4997                 |
| E16s_v3              | 16     | 128 GiB     | 5000            | 4997                 |
| E32s_v3              | 32     | 256 GiB     | 5000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5000            | 4997                 |

När anslutningar överskrider gränsen kan följande fel meddelande visas:
> OÅTERKALLELIGt: det finns för många klienter redan

> [!IMPORTANT]
> För bästa möjliga upplevelse rekommenderar vi att du använder en anslutningspool som PgBouncer för att effektivt hantera anslutningar.

En PostgreSQL-anslutning, även inaktiv, kan uppta cirka 10 MB minne. Det tar också tid att skapa nya anslutningar. De flesta program begär många anslutningar för kort period, vilket utvärderar den här situationen. Resultatet är färre resurser som är tillgängliga för den faktiska arbets belastningen, vilket leder till försämrade prestanda. Anslutningspoolen kan användas för att minska inaktiva anslutningar och återanvända befintliga anslutningar. Mer information finns i vårt [blogg inlägg](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Funktionella begränsningar

### <a name="scale-operations"></a>Skalnings åtgärder

- Att skala Server lagringen kräver att servern startas om.
- Server lagring kan bara skalas i 2x-steg, se [Compute and Storage](concepts-compute-storage.md) för mer information.
- Det finns för närvarande inte stöd för att minska lagrings storleken för servern.

### <a name="server-version-upgrades"></a>Uppgraderingar av Server version

- Automatisk migrering mellan huvud versioner av databas motorn stöds inte för närvarande. Om du vill uppgradera till nästa högre version kan du göra en [dumpning och återställa](../howto-migrate-using-dump-and-restore.md) den till en server som har skapats med den nya motor versionen.

### <a name="storage"></a>Storage

- Det går inte att minska lagrings storleken när den har kon figurer ATS. Du skulle behöva skapa en ny server med önskad lagrings storlek och utföra manuella dumpnings-och återställnings processer för att migrera dina databaser.
- För närvarande är inte funktionen för automatisk storleks ökning tillgänglig. Övervaka användningen och öka lagrings utrymmet till en högre storlek. 
- När lagrings användningen når 95% eller om den tillgängliga kapaciteten är mindre än 5 GiB växlas servern automatiskt till **skrivskyddat läge** för att undvika fel som är kopplade till disk-fulla situationer. 
- Vi rekommenderar att du ställer in varnings regler för `storage used` eller `storage percent` när de överskrider vissa tröskelvärden, så att du proaktivt kan vidta åtgärder som att öka lagrings storleken. Du kan till exempel ange en avisering om lagrings procenten överstiger 80% användning.
  
### <a name="networking"></a>Nätverk

- Det finns för närvarande inte stöd för att flytta in och ut i VNET.
- Det finns för närvarande inte stöd för att kombinera offentlig åtkomst med distribution i ett VNET.
- Brand Väggs regler stöds inte i VNET, nätverks säkerhets grupper kan användas i stället.
- Offentliga åtkomst databas servrar kan ansluta till offentligt Internet, till exempel via `postgres_fdw` , och den här åtkomsten kan inte begränsas. VNET-baserade servrar kan ha begränsad utgående åtkomst med hjälp av nätverks säkerhets grupper.

### <a name="high-availability-ha"></a>Hög tillgänglighet (HA)

- Zone-Redundant HA stöds för närvarande inte för servrar med burst.
- Databas serverns IP-adress ändras när servern växlar över till förväntat vänte läge. Se till att du använder DNS-posten i stället för serverns IP-adress.
- Om logisk replikering har kon figurer ATS med en HA kon figurer ATS med hög tillgänglighet, i händelse av en redundansväxling till standby-servern, kopieras inte de logiska replikerings platserna till standby-servern. 
- Mer information om Zone-redundanta användare, inklusive begränsningar, finns på [dokumentations sidan för Concepts-ha](concepts-high-availability.md) .

### <a name="availability-zones"></a>Tillgänglighetszoner

- Det finns för närvarande inte stöd för att flytta servrar till en annan tillgänglighets zon.
- Det går inte att konfigurera tillgänglighets zonen för vänte läges servern manuellt.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Postgres-motor, tillägg och PgBouncer

- Postgres 10 och äldre stöds inte. Vi rekommenderar att du använder alternativet [enskild server](../overview-single-server.md) om du behöver äldre postgres-versioner.
- Stöd för tillägg är för närvarande begränsat till postgres- `contrib` tilläggen.
- Inbyggd PgBouncer-anslutningspoolen är för närvarande inte tillgänglig för databas servrar i ett VNET eller för servrar med burst.

### <a name="stopstart-operation"></a>Stoppa/starta åtgärd

- Servern kan inte stoppas i mer än sju dagar.

### <a name="scheduled-maintenance"></a>Schemalagt underhåll

- Om du ändrar underhålls perioden som är mindre än fem dagar innan en redan planerad uppgradering, påverkas inte uppgraderingen. Ändringarna börjar gälla med nästa schemalagda underhåll.

### <a name="backing-up-a-server"></a>Säkerhetskopiera en server

- Säkerhets kopieringarna hanteras av systemet och det finns för närvarande inget sätt att köra säkerhets kopiorna manuellt. Vi rekommenderar att du använder `pg_dump` i stället.
- Säkerhets kopieringar är alltid ögonblicks bildbaserade fullständiga säkerhets kopieringar (inte differentiella säkerhets kopior), vilket kan leda till högre lagrings belastning för säkerhets kopiering Observera att transaktions loggar (Skriv loggar i förväg WAL) skiljer sig från de fullständiga/differentiella säkerhets kopiorna och arkiveras kontinuerligt.

### <a name="restoring-a-server"></a>Återställa en server

- När du använder funktionen för att återställa en viss tidpunkt skapas den nya servern med samma beräknings-och lagrings konfiguration som den server som den baseras på.
- VNET-baserade databas servrar återställs till samma VNET när du återställer från en säkerhets kopia.
- Den nya servern som skapades under en återställning har inte de brand Väggs regler som fanns på den ursprungliga servern. Brand Väggs regler måste skapas separat för den nya servern.
- Det finns inte stöd för att återställa en borttagen Server.
- Återställning mellan regioner stöds inte.

### <a name="other-features"></a>Andra funktioner

* Azure AD-autentisering stöds inte ännu. Vi rekommenderar att du använder alternativet [enskild server](../overview-single-server.md) om du behöver Azure AD-autentisering.
* Läs repliker stöds inte ännu. Vi rekommenderar att du använder alternativet [enskild server](../overview-single-server.md) om du behöver läsa repliker.
* Det finns inte stöd för att flytta resurser till en annan prenumeration. 


## <a name="next-steps"></a>Nästa steg

- Förstå [vad som är tillgängligt för beräknings-och lagrings alternativ](concepts-compute-storage.md)
- Läs mer om [PostgreSQL-databas versioner som stöds](concepts-supported-versions.md)
- Granska [hur du säkerhetskopierar och återställer en server i Azure Database for PostgreSQL att använda Azure Portal](how-to-restore-server-portal.md)
