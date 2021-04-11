---
title: Gränser – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver gränser i Azure Database for PostgreSQL-enskild server, till exempel antalet anslutnings-och lagrings motor alternativ.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 7cee2be3c1d7a97b87560873d6cef07ec361ee99
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605186"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Gränser i Azure Database for PostgreSQL-enskild server
I följande avsnitt beskrivs kapacitets-och funktions gränser i databas tjänsten. Om du vill lära dig mer om resurs (beräknings-, minnes-, lagrings-) nivåer, se artikeln [pris nivåer](concepts-pricing-tiers.md) .


## <a name="maximum-connections"></a>Maximalt antal anslutningar
Det maximala antalet anslutningar per pris nivå och virtuella kärnor visas nedan. Azure-systemet kräver fem anslutningar för att övervaka Azure Database for PostgreSQL-servern. 

|**Prisnivå**| **vCore (s)**| **Max antal anslutningar** | **Maximalt antal användar anslutningar** |
|---|---|---|---|
|Grundläggande| 1| 55 | 50|
|Grundläggande| 2| 105 | 100|
|Generell användning| 2| 150| 145|
|Generell användning| 4| 250| 245|
|Generell användning| 8| 480| 475|
|Generell användning| 16| 950| 945|
|Generell användning| 32| 1500| 1495|
|Generell användning| 64| 1900| 1895|
|Minnesoptimerad| 2| 300| 295|
|Minnesoptimerad| 4| 500| 495|
|Minnesoptimerad| 8| 960| 955|
|Minnesoptimerad| 16| 1900| 1895|
|Minnesoptimerad| 32| 1987| 1982|

När anslutningar överskrider gränsen kan följande fel meddelande visas:
> OÅTERKALLELIGt: det finns för många klienter redan

> [!IMPORTANT]
> För bästa möjliga upplevelse rekommenderar vi att du använder en anslutningspool som pgBouncer för att effektivt hantera anslutningar.

En PostgreSQL-anslutning, även inaktiv, kan uppta cirka 10 MB minne. Det tar också tid att skapa nya anslutningar. De flesta program begär många anslutningar för kort period, vilket utvärderar den här situationen. Resultatet är färre resurser som är tillgängliga för den faktiska arbets belastningen, vilket leder till försämrade prestanda. En anslutningspool som minskar inaktiva anslutningar och återanvänder befintliga anslutningar hjälper till att undvika detta. Mer information finns i vårt [blogg inlägg](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Funktionella begränsningar
### <a name="scale-operations"></a>Skalnings åtgärder
- Dynamisk skalning till och från de grundläggande pris nivåerna stöds inte för närvarande.
- Det finns för närvarande inte stöd för att minska lagrings storleken för servern.

### <a name="server-version-upgrades"></a>Uppgraderingar av Server version
- Automatisk migrering mellan huvud versioner av databas motorn stöds inte för närvarande. Om du vill uppgradera till nästa högre version kan du göra en [dumpning och återställa](./howto-migrate-using-dump-and-restore.md) den till en server som har skapats med den nya motor versionen.

> Observera att innan PostgreSQL version 10 ansågs [versions principen för postgresql](https://www.postgresql.org/support/versioning/) en _större versions_ uppgradering för att bli en ökning av det första _eller_ andra numret (till exempel 9,5 till 9,6 ansågs vara en _huvud_ versions uppgradering).
> Från och med version 10 betraktas bara en ändring i det första talet som en huvud versions uppgradering (till exempel 10,0 till 10,1 är en _lägre_ versions uppgradering och 10 till 11 är en _högre_ versions uppgradering).

### <a name="vnet-service-endpoints"></a>VNet-tjänstslutpunkter
- Stöd för VNet-tjänstens slut punkter är bara för Generell användning och minnesoptimerade servrar.

### <a name="restoring-a-server"></a>Återställa en server
- När du använder funktionen PITR skapas den nya servern med samma pris nivå konfiguration som den server den baseras på.
- Den nya servern som skapades under en återställning har inte de brand Väggs regler som fanns på den ursprungliga servern. Brand Väggs regler måste konfigureras separat för den här nya servern.
- Det finns inte stöd för att återställa en borttagen Server.

### <a name="utf-8-characters-on-windows"></a>UTF-8-tecken i Windows
- I vissa fall stöds inte UTF-8-tecken fullständigt i PostgreSQL med öppen källkod i Windows, vilket påverkar Azure Database for PostgreSQL. Mer information hittar du i tråden on [bugg #15476 i postgresql-Archive](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) .

### <a name="gss-error"></a>GSS-fel
Om du ser ett fel som är relaterat till **GSS** använder du förmodligen en nyare klient-/drivrutinsversion som Azure Postgres – enskild server inte har fullständigt stöd för än. Det här felet är känt för att påverka [JDBC-drivrutinsversion 42.2.15 och 42.2.16](https://github.com/pgjdbc/pgjdbc/issues/1868).
   - Uppdateringen förväntas vara klar i slutet av november. Använd en fungerande drivrutinsversion tills dess.
   - Eller också kan du inaktivera GSS-begäran.  Använd en anslutningsparameter som `gssEncMode=disable`.

### <a name="storage-size-reduction"></a>Minska lagrings storlek
Lagrings storleken kan inte minskas. Du måste skapa en ny server med önskad lagrings storlek, utföra manuell [dumpning och återställa](./howto-migrate-using-dump-and-restore.md) och migrera dina databaser till den nya servern.

## <a name="next-steps"></a>Nästa steg
- Förstå [vad som är tillgängligt på varje pris nivå](concepts-pricing-tiers.md)
- Läs mer om [PostgreSQL-databas versioner som stöds](concepts-supported-versions.md)
- Granska [hur du säkerhetskopierar och återställer en server i Azure Database for PostgreSQL att använda Azure Portal](howto-restore-server-portal.md)
