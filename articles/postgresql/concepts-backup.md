---
title: Säkerhets kopiering och återställning – Azure Database for PostgreSQL-enskild server
description: Lär dig mer om automatisk säkerhets kopiering och att återställa Azure Database for PostgreSQL Server – en server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: db3b62e7ce07c1e10bc5030c37cb8957d281ea05
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517305"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Säkerhets kopiering och återställning i Azure Database for PostgreSQL-enskild server

Azure Database for PostgreSQL skapar automatiskt Server säkerhets kopior och lagrar dem i användar konfiguration lokalt redundant eller Geo-redundant lagring. Säkerhetskopieringar kan användas för att återställa servern till en vald tidpunkt. Säkerhetskopiering och återställning är en viktig del i strategin för affärskontinuitet, eftersom de skyddar dina data från oavsiktlig skada eller borttagning.

## <a name="backups"></a>Säkerhetskopior

Azure Database for PostgreSQL säkerhetskopierar datafilerna och transaktions loggen. Beroende på den maximala lagrings storleken som stöds tar vi antingen fullständiga och differentiella säkerhets kopieringar (4 TB max lagrings servrar) eller säkerhets kopior av ögonblicks bilder (upp till 16 TB max lagrings servrar). Med dessa säkerhets kopieringar kan du återställa en server till alla tidpunkter inom den konfigurerade kvarhållningsperioden för säkerhets kopior. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Du kan också konfigurera det upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering.

Det går inte att exportera de här säkerhetskopierade filerna. Säkerhets kopieringarna kan bara användas för återställnings åtgärder i Azure Database for PostgreSQL. Du kan använda [pg_dump](howto-migrate-using-dump-and-restore.md) för att kopiera en databas.

### <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

#### <a name="servers-with-up-to-4-tb-storage"></a>Servrar med upp till 4 TB lagrings utrymme

För servrar som har stöd för upp till 4 TB högsta lagrings utrymme sker fullständiga säkerhets kopieringar varje vecka. Differentiella säkerhets kopieringar sker två gånger om dagen. Säkerhetskopieringar av transaktionsloggar sker var femte minut.


#### <a name="servers-with-up-to-16-tb-storage"></a>Servrar med upp till 16 TB lagring

I en delmängd av [Azure-regioner](./concepts-pricing-tiers.md#storage)kan alla nyligen etablerade servrar stödja upp till 16 TB lagring. Säkerhets kopieringar på dessa stora lagrings servrar är Snapshot-baserade. Den första fullständiga säkerhetskopieringen schemaläggs omedelbart efter att en server har skapats. Den första fullständiga säkerhets kopieringen behålls som serverns grundläggande säkerhets kopiering. Efterföljande säkerhetskopieringar av ögonblicksbilder är bara differentiella säkerhetskopieringar. Differentiella säkerhetskopieringar av ögonblicksbilder sker inte enligt ett fast schema. Under en dag utförs tre differentiella ögonblicks bilds säkerhets kopieringar. Säkerhetskopieringar av transaktionsloggar sker var femte minut. 

### <a name="backup-retention"></a>Kvarhållningsperiod för säkerhetskopior

Säkerhets kopior bevaras baserat på inställningen för kvarhållning av säkerhets kopior på servern. Du kan välja en kvarhållningsperiod på 7 till 35 dagar. Standard kvarhållningsperioden är 7 dagar. Du kan ställa in kvarhållningsperioden när servern skapas eller senare genom att uppdatera säkerhets kopierings konfigurationen med [Azure Portal](./howto-restore-server-portal.md#set-backup-configuration) eller [Azure CLI](./howto-restore-server-cli.md#set-backup-configuration). 

Kvarhållningsperioden för säkerhets kopior styr hur långt tillbaka i tiden en tidpunkts återställning kan hämtas, eftersom den baseras på tillgängliga säkerhets kopior. Kvarhållningsperioden för säkerhets kopior kan också behandlas som ett återställnings fönster från ett återställnings perspektiv. Alla säkerhets kopior som krävs för att utföra en återställning efter en viss tidpunkt inom kvarhållning av säkerhets kopior bevaras i säkerhets kopierings lagringen. Exempel: om kvarhållningsperioden för säkerhets kopior har angetts till 7 dagar, betraktas återställnings fönstret de senaste 7 dagarna. I det här scenariot behålls alla säkerhets kopior som krävs för att återställa servern under de senaste 7 dagarna. Med ett fönster för kvarhållning av säkerhets kopior av sju dagar:
- Servrar med upp till 4 TB lagrings utrymme behåller upp till 2 fullständiga säkerhets kopieringar av databaser, alla differentiella säkerhets kopieringar och säkerhets kopieringar av transaktions loggar som utförs sedan den tidigaste fullständiga säkerhets kopieringen
-   Servrar med upp till 16 TB lagring behåller den fullständiga ögonblicks bilden av databasen, alla differentiella ögonblicks bilder och säkerhets kopieringar av transaktions loggar de senaste 8 dagarna.

### <a name="backup-redundancy-options"></a>Alternativ för redundans för säkerhets kopiering

Azure Database for PostgreSQL ger flexibiliteten att välja mellan lokalt redundant eller Geo-redundant lagring av säkerhets kopior i Generell användning och minnesoptimerade nivåer. När säkerhets kopiorna lagras i Geo-redundant lagring av säkerhets kopior lagras de inte bara i den region där servern finns, men replikeras också till ett [parat Data Center](../best-practices-availability-paired-regions.md). Detta ger bättre skydd och möjlighet att återställa servern i en annan region i händelse av en katastrof. Basic-nivån erbjuder endast lokalt redundant säkerhets kopierings lagring.

> [!IMPORTANT]
> Det går bara att konfigurera lokalt redundant eller geo-redundant lagring för säkerhetskopiering när servern skapas. När servern har etablerats kan du inte ändra alternativet för redundant lagring för säkerhetskopior.

### <a name="backup-storage-cost"></a>Reserv lagrings kostnad

Azure Database for PostgreSQL tillhandahåller upp till 100% av din etablerade Server lagring som säkerhets kopierings lagring utan extra kostnad. Ytterligare lagrings utrymme för säkerhets kopior debiteras i GB per månad. Om du till exempel har etablerad en server med 250 GB lagrings utrymme har du 250 GB ytterligare lagrings utrymme för Server säkerhets kopior utan extra kostnad. Förbrukad lagring för säkerhets kopieringar över 250 GB debiteras enligt [pris sättnings modellen](https://azure.microsoft.com/pricing/details/postgresql/).

Du kan använda måttet [säkerhets kopierings lagring som används](concepts-monitoring.md) i Azure Monitor tillgängligt i Azure Portal för att övervaka säkerhets kopierings lagringen som används av en server. Måttet mått för säkerhets kopierings lagring representerar summan av lagrings utrymme som förbrukas av alla fullständiga säkerhets kopior av databasen, differentiella säkerhets kopior och logg säkerhets kopior som bevaras baserat på den kvarhållna säkerhets kopierings perioden för servern. Säkerhets kopierings frekvensen är tjänsten hanterad och förklaras tidigare. Krävande transaktionsaktivitet på servern kan orsaka att lagringsanvändningen för säkerhetskopior ökar oberoende av databasens totala storlek. För Geo-redundant lagring är lagrings utrymmet för säkerhets kopiering två gånger för det lokalt redundanta lagrings utrymmet. 

Det främsta sättet att kontrol lera lagrings kostnaden för säkerhets kopiering är genom att ställa in lämplig kvarhållningsperiod för säkerhets kopior och välja rätt alternativ för säkerhets kopiering för att uppfylla önskade återställnings mål. Du kan välja en kvarhållningsperiod från mellan 7 och 35 dagar. Generell användning-och Minnesoptimerade servrar kan välja att ha Geo-redundant lagring för säkerhets kopiering.

## <a name="restore"></a>Återställ

I Azure Database for PostgreSQL skapar en återställning en ny server från den ursprungliga serverns säkerhets kopior. 

Det finns två typer av återställning:

- **Återställning av tidpunkt** är tillgängligt med alternativ för redundans och skapar en ny server i samma region som den ursprungliga servern.
- **Geo-återställning** är bara tillgängligt om du har konfigurerat servern för Geo-redundant lagring och du kan återställa servern till en annan region.

Den uppskattade återställnings tiden beror på flera faktorer, till exempel databasens storlek, transaktions loggens storlek, nätverks bandbredden och det totala antalet databaser som återställs i samma region på samma tid. Återställnings tiden varierar beroende på den senaste data säkerhets kopieringen och mängden återställning som måste utföras. Det är vanligt vis mindre än 12 timmar.

> [!NOTE] 
> Om din käll PostgreSQL-Server är krypterad med Kundhanterade nycklar kan du läsa [dokumentationen](concepts-data-encryption-postgresql.md) för ytterligare överväganden. 

> [!NOTE]
> Om du vill återställa en borttagen PostgreSQL-Server följer du proceduren som beskrivs [här](howto-restore-dropped-server.md).

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

Oberoende av ditt alternativ för säkerhets kopiering kan du utföra en återställning till vilken tidpunkt som helst inom lagrings perioden för säkerhets kopiorna. En ny server skapas i samma Azure-region som den ursprungliga servern. Den skapas med den ursprungliga serverns konfiguration för pris nivån, beräknings generering, antalet virtuella kärnor, lagrings storlek, kvarhållning av säkerhets kopior och alternativet för redundans.

Återställning av tidpunkt är användbart i flera scenarier. Till exempel när en användare oavsiktligt tar bort data, släpper en viktig tabell eller databas, eller om ett program av misstag skriver över bra data med felaktiga data på grund av ett program fel.

Du kan behöva vänta tills nästa säkerhets kopiering av transaktions loggen tas innan du kan återställa till en tidpunkt under de senaste fem minuterna.

Om du vill återställa en borttagen tabell 
1. Återställa käll servern med hjälp av metoden för punkt-in-Time.
2. Dumpa tabellen med `pg_dump` från den återställda servern.
3. Byt namn på käll tabellen på den ursprungliga servern.
4. Importera tabell med psql kommando rad på den ursprungliga servern.
5. Du kan också ta bort den återställda servern.

>[!Note]
> Vi rekommenderar att du inte skapar flera återställningar för samma server på samma tid. 

### <a name="geo-restore"></a>Geo-återställning

Du kan återställa en server till en annan Azure-region där tjänsten är tillgänglig om du har konfigurerat servern för geo-redundanta säkerhets kopieringar. Servrar som har stöd för upp till 4 TB lagrings utrymme kan återställas till den geo-kopplade regionen eller till en region som har stöd för upp till 16 TB lagring. För servrar som har stöd för upp till 16 TB lagrings utrymme kan geo-säkerhetskopieringar återställas i valfri region som har stöd för 16 TB-servrar. Granska [Azure Database for PostgreSQL pris nivåer](concepts-pricing-tiers.md) för listan över regioner som stöds.

Geo-återställning är standard alternativet för återställning när servern inte är tillgänglig på grund av en incident i den region där-servern finns. Om en storskalig incident i en region resulterar i att databas programmet inte är tillgängligt, kan du återställa en server från de geo-redundanta säkerhets kopieringarna till en server i någon annan region. Det uppstår en fördröjning mellan när en säkerhets kopia tas och när den replikeras till en annan region. Den här fördröjningen kan vara upp till en timme, så om en katastrof inträffar kan det vara upp till en timmes data förlust.

Vid geo-återställning kan de serverkonfigurationer som kan ändras omfatta beräknings generering, vCore, bevarande period för säkerhets kopior och alternativ för säkerhets kopiering. Det finns inte stöd för att ändra pris nivå (Basic, Generell användning eller Minnesoptimerade) eller lagrings storlek.

> [!NOTE]
> Om din käll Server använder infrastrukturs-Double kryptering för att återställa servern finns det begränsningar, inklusive tillgängliga regioner. Mer information finns i [infrastrukturens dubbla kryptering](concepts-infrastructure-double-encryption.md) .

### <a name="perform-post-restore-tasks"></a>Utföra uppgifter efter återställning

Efter en återställning från återställnings metoden bör du utföra följande uppgifter för att få dina användare och program att säkerhetskopiera och köra dem:

- Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klient program till den nya servern. Ändra även användar namnet till `username@new-restored-server-name` .
- Se till att det finns tillräckligt med brand Väggs-och VNet-regler på server nivå för att användarna ska kunna ansluta. De här reglerna kopieras inte från den ursprungliga servern.
- Se till att lämpliga inloggningar och behörigheter på databas nivå är på plats
- Konfigurera aviseringar efter behov

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du återställer med hjälp [av Azure Portal](howto-restore-server-portal.md).
- Lär dig hur du återställer med hjälp [av Azure CLI](howto-restore-server-cli.md).
- Mer information om verksamhets kontinuitet finns i [Översikt över affärs kontinuitet](concepts-business-continuity.md).