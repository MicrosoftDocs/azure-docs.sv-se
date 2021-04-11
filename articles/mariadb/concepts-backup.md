---
title: Säkerhets kopiering och återställning – Azure Database for MariaDB
description: Lär dig mer om automatisk säkerhets kopiering och att återställa Azure Database for MariaDB-servern.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 8/13/2020
ms.openlocfilehash: b46efa53bba3b845fa5837b91a3707f4a85d298e
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258783"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Säkerhets kopiering och återställning i Azure Database for MariaDB

Azure Database for MariaDB skapar automatiskt Server säkerhets kopior och lagrar dem i användar konfiguration lokalt redundant eller Geo-redundant lagring. Säkerhetskopieringar kan användas för att återställa servern till en vald tidpunkt. Säkerhetskopiering och återställning är en viktig del i strategin för affärskontinuitet, eftersom de skyddar dina data från oavsiktlig skada eller borttagning.

## <a name="backups"></a>Säkerhetskopior

Azure Database for MariaDB säkerhetskopierar datafilerna och transaktions loggen. Med dessa säkerhets kopieringar kan du återställa en server till alla tidpunkter inom den konfigurerade kvarhållningsperioden för säkerhets kopior. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Du kan [också konfigurera det](howto-restore-server-portal.md#set-backup-configuration) upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering.

De säkerhetskopierade filerna visas inte för användarna och kan inte exporteras. Dessa säkerhets kopior kan bara användas för återställnings åtgärder i Azure Database for MySQL. Du kan använda [mysqldump](howto-migrate-dump-restore.md) för att kopiera en databas.

Säkerhets kopieringens typ och frekvens beror på Server serverns lagrings utrymme.

### <a name="backup-type-and-frequency"></a>Typ av säkerhets kopiering och frekvens

#### <a name="basic-storage-servers"></a>Grundläggande lagrings servrar

Basic Storage är Server dels lagringen med stöd för [grundläggande nivå servrar](concepts-pricing-tiers.md). Säkerhets kopior på Basic Storage-servrar är Snapshot-baserade. En fullständig ögonblicks bild av databasen utförs dagligen. Det finns inga differentiella säkerhets kopieringar för grundläggande lagrings servrar och alla säkerhets kopior av ögonblicks bilder är bara fullständiga databas säkerhets kopieringar.

Säkerhetskopieringar av transaktionsloggar sker var femte minut.

#### <a name="general-purpose-storage-servers-with-up-to-4-tb-storage"></a>Generella lagrings servrar med upp till 4 TB lagring

Det allmänna lagrings utrymmet är den server dels lagring som stöder [generell användning](concepts-pricing-tiers.md) och minnesoptimerade [nivå](concepts-pricing-tiers.md) Server. För servrar med generell lagring upp till 4 TB sker fullständiga säkerhets kopieringar varje vecka. Differentiella säkerhets kopieringar sker två gånger om dagen. Säkerhetskopieringar av transaktionsloggar sker var femte minut. Säkerhets kopieringarna i generell användning av lagrings utrymme på 4 TB är inte ögonblicks bilds och använder IO-bandbredd vid tidpunkten för säkerhets kopieringen. För stora databaser (> 1 TB) på 4 TB-lagring rekommenderar vi att du

- Etablering av mer IOPs till konto för säkerhets kopiering av IOs eller
- Du kan också migrera till allmän lagring som stöder upp till 16 TB lagrings utrymme om den underliggande lagrings infrastrukturen är tillgänglig i dina önskade [Azure-regioner](./concepts-pricing-tiers.md#storage). Det kostar inget extra att tillhandahålla lagring med upp till 16 TB. Om du vill ha hjälp med migrering till 16 TB-lagring öppnar du ett support ärende från Azure Portal.

#### <a name="general-purpose-storage-servers-with-up-to-16-tb-storage"></a>Generella lagrings servrar med upp till 16 TB lagring

I en delmängd av [Azure-regioner](./concepts-pricing-tiers.md#storage)har alla nyligen etablerade servrar stöd för generell användning av lagrings utrymme på 16 TB. Med andra ord är lagring upp till 16 TB lagrings utrymme standard lagring för alla [regioner](concepts-pricing-tiers.md#storage) där det stöds. Säkerhets kopieringar på dessa 16-TB-lagrings servrar är ögonblicks bilds-baserade. Den första fullständiga säkerhetskopieringen schemaläggs omedelbart efter att en server har skapats. Den första fullständiga säkerhets kopieringen behålls som serverns grundläggande säkerhets kopiering. Efterföljande säkerhetskopieringar av ögonblicksbilder är bara differentiella säkerhetskopieringar.

Differentiella säkerhetskopieringar av ögonblicksbilder görs minst en gång per dag. Differentiella säkerhetskopieringar av ögonblicksbilder sker inte enligt ett fast schema. Differentiella ögonblicks bilds säkerhets kopieringar sker var 24: e timme om transaktions loggen (BinLog i MariaDB) överskrider 50 GB sedan den senaste differentiella säkerhets Högst sex differentiella ögonblicksbilder tillåts under samma dag.

Säkerhetskopieringar av transaktionsloggar sker var femte minut.
 

### <a name="backup-retention"></a>Kvarhållningsperiod för säkerhetskopior

Säkerhets kopior bevaras baserat på inställningen för kvarhållning av säkerhets kopior på servern. Du kan välja en kvarhållningsperiod på 7 till 35 dagar. Standard kvarhållningsperioden är 7 dagar. Du kan ställa in kvarhållningsperioden när servern skapas eller senare genom att uppdatera säkerhets kopierings konfigurationen med [Azure Portal](howto-restore-server-portal.md#set-backup-configuration) eller [Azure CLI](howto-restore-server-cli.md#set-backup-configuration). 

Kvarhållningsperioden för säkerhets kopior styr hur långt tillbaka i tiden en tidpunkts återställning kan hämtas, eftersom den baseras på tillgängliga säkerhets kopior. Kvarhållningsperioden för säkerhets kopior kan också behandlas som ett återställnings fönster från ett återställnings perspektiv. Alla säkerhets kopior som krävs för att utföra en återställning efter en viss tidpunkt inom kvarhållning av säkerhets kopior bevaras i säkerhets kopierings lagringen. Om säkerhets kopierings perioden till exempel är 7 dagar, betraktas återställnings fönstret de senaste 7 dagarna. I det här scenariot behålls alla säkerhets kopior som krävs för att återställa servern under de senaste 7 dagarna. Med ett fönster för kvarhållning av säkerhets kopior av sju dagar:

- Servrar med upp till 4 TB lagrings utrymme behåller upp till 2 fullständiga säkerhets kopieringar av databaser, alla differentiella säkerhets kopieringar och säkerhets kopieringar av transaktions loggar som utförs sedan den tidigaste fullständiga säkerhets kopieringen
-   Servrar med upp till 16 TB lagring behåller den fullständiga ögonblicks bilden av databasen, alla differentiella ögonblicks bilder och säkerhets kopieringar av transaktions loggar de senaste 8 dagarna.

#### <a name="long-term-retention-of-backups"></a>Långsiktig kvarhållning av säkerhetskopior
Långsiktig kvarhållning av säkerhets kopieringar utöver 35 dagar stöds för närvarande inte av tjänsten ännu. Du har möjlighet att använda mysqldump för att göra säkerhets kopior och lagra dem för långsiktig kvarhållning. Vårt support team har blogg en [steg-för-steg-artikel](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157) för att dela hur du kan åstadkomma det. 

### <a name="backup-redundancy-options"></a>Alternativ för redundans för säkerhets kopiering

Azure Database for MariaDB ger flexibiliteten att välja mellan lokalt redundant eller Geo-redundant lagring av säkerhets kopior i Generell användning och minnesoptimerade nivåer. När säkerhets kopiorna lagras i Geo-redundant lagring av säkerhets kopior lagras de inte bara i den region där servern finns, men replikeras också till ett [parat Data Center](../best-practices-availability-paired-regions.md). Detta ger bättre skydd och möjlighet att återställa servern i en annan region i händelse av en katastrof. Basic-nivån erbjuder endast lokalt redundant säkerhets kopierings lagring.

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>Flytta från lokalt redundant till Geo-redundant lagring av säkerhets kopior
Det går bara att konfigurera lokalt redundant eller geo-redundant lagring för säkerhetskopiering när servern skapas. När servern har etablerats kan du inte ändra alternativet för redundant lagring för säkerhetskopior. För att kunna flytta lagrings utrymme för säkerhets kopior från lokalt redundant lagring till Geo-redundant lagring, skapar du en ny server och migrerar data med [dump och Restore](howto-migrate-dump-restore.md) är det enda alternativ som stöds.

### <a name="backup-storage-cost"></a>Reserv lagrings kostnad

Azure Database for MariaDB tillhandahåller upp till 100% av din etablerade Server lagring som säkerhets kopierings lagring utan extra kostnad. Ytterligare lagrings utrymme för säkerhets kopior debiteras i GB per månad. Om du till exempel har etablerad en server med 250 GB lagrings utrymme har du 250 GB ytterligare lagrings utrymme för Server säkerhets kopior utan extra kostnad. Förbrukad lagring för säkerhets kopieringar över 250 GB debiteras enligt [pris sättnings modellen](https://azure.microsoft.com/pricing/details/mariadb/). 

Du kan använda måttet [säkerhets kopierings lagring som används](concepts-monitoring.md) i Azure Monitor tillgängligt via Azure Portal för att övervaka säkerhets kopierings lagringen som används av en server. Måttet mått för säkerhets kopierings lagring representerar summan av lagrings utrymme som förbrukas av alla fullständiga säkerhets kopior av databasen, differentiella säkerhets kopior och logg säkerhets kopior som bevaras baserat på den kvarhållna säkerhets kopierings perioden för servern. Säkerhets kopierings frekvensen är tjänsten hanterad och förklaras tidigare. Krävande transaktionsaktivitet på servern kan orsaka att lagringsanvändningen för säkerhetskopior ökar oberoende av databasens totala storlek. För Geo-redundant lagring är lagrings utrymmet för säkerhets kopiering två gånger för det lokalt redundanta lagrings utrymmet. 

Det främsta sättet att kontrol lera lagrings kostnaden för säkerhets kopiering är genom att ställa in lämplig kvarhållningsperiod för säkerhets kopior och välja rätt alternativ för säkerhets kopiering för att uppfylla önskade återställnings mål. Du kan välja en kvarhållningsperiod från mellan 7 och 35 dagar. Generell användning-och Minnesoptimerade servrar kan välja att ha Geo-redundant lagring för säkerhets kopiering.

## <a name="restore"></a>Återställ

I Azure Database for MariaDB skapar en återställning en ny server från den ursprungliga serverns säkerhets kopior och återställer alla databaser som finns på servern.

Det finns två typer av återställning:

- **Återställning på plats-till-tid** är tillgängligt med alternativ för redundans och skapar en ny server i samma region som den ursprungliga servern som använder kombinationen av fullständiga säkerhets kopieringar och säkerhets kopieringar av transaktions loggar.
- **Geo-återställning** är bara tillgängligt om du har konfigurerat servern för Geo-redundant lagring och du kan återställa servern till en annan region som använder den senaste säkerhets kopian.

Den uppskattade återställnings tiden beror på flera faktorer, till exempel databasens storlek, transaktions loggens storlek, nätverks bandbredden och det totala antalet databaser som återställs i samma region på samma tid. Återställnings tiden är vanligt vis mindre än 12 timmar.

> [!IMPORTANT]
> **Det går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas. För att skydda server resurser, efter distribution, från oavsiktlig borttagning eller oväntade ändringar, kan administratörer utnyttja [hanterings lås](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

Oberoende av ditt alternativ för säkerhets kopiering kan du utföra en återställning till vilken tidpunkt som helst inom lagrings perioden för säkerhets kopiorna. En ny server skapas i samma Azure-region som den ursprungliga servern. Den skapas med den ursprungliga serverns konfiguration för pris nivån, beräknings generering, antalet virtuella kärnor, lagrings storlek, kvarhållning av säkerhets kopior och alternativet för redundans.

Återställning av tidpunkt är användbart i flera scenarier. Till exempel när en användare oavsiktligt tar bort data, släpper en viktig tabell eller databas, eller om ett program av misstag skriver över bra data med felaktiga data på grund av ett program fel.

Du kan behöva vänta tills nästa säkerhets kopiering av transaktions loggen tas innan du kan återställa till en tidpunkt under de senaste fem minuterna.

### <a name="geo-restore"></a>Geo-återställning

Du kan återställa en server till en annan Azure-region där tjänsten är tillgänglig om du har konfigurerat servern för geo-redundanta säkerhets kopieringar. Servrar som har stöd för upp till 4 TB lagrings utrymme kan återställas till den geo-kopplade regionen eller till en region som har stöd för upp till 16 TB lagring. För servrar som har stöd för upp till 16 TB lagrings utrymme kan geo-säkerhetskopieringar återställas i valfri region som har stöd för 16 TB-servrar. Granska [Azure Database for MariaDB pris nivåer](concepts-pricing-tiers.md) för listan över regioner som stöds.

Geo-återställning är standard alternativet för återställning när servern inte är tillgänglig på grund av en incident i den region där-servern finns. Om en storskalig incident i en region resulterar i att databas programmet inte är tillgängligt, kan du återställa en server från de geo-redundanta säkerhets kopieringarna till en server i någon annan region. Geo-återställning använder den senaste säkerhets kopian av servern. Det uppstår en fördröjning mellan när en säkerhets kopia tas och när den replikeras till en annan region. Den här fördröjningen kan vara upp till en timme, så om en katastrof inträffar kan det vara upp till en timmes data förlust.

> [!IMPORTANT]
>Om en geo-återställning utförs för en nyskapad Server kan den första synkroniseringen av säkerhets kopieringen ta mer än 24 timmar, beroende på data storleken eftersom den första säkerhets kopierings tiden för ögonblicks bilder är mycket högre. Efterföljande ögonblicks säkerhets kopieringar är stegvisa kopior och därför går det snabbare att återställa efter 24 timmar efter att servern har skapats. Om du utvärderar geo-Restore för att definiera RTO rekommenderar vi att du väntar och utvärdera geo-återställning **bara efter 24 timmars** Server skapande för bättre uppskattningar.

Vid geo-återställning kan de serverkonfigurationer som kan ändras omfatta beräknings generering, vCore, bevarande period för säkerhets kopior och alternativ för säkerhets kopiering. Det finns inte stöd för att ändra pris nivå (Basic, Generell användning eller Minnesoptimerade) eller lagrings storlek under geo-återställning.

Den uppskattade återställnings tiden beror på flera faktorer, till exempel databasens storlek, transaktions loggens storlek, nätverks bandbredden och det totala antalet databaser som återställs i samma region på samma tid. Återställnings tiden är vanligt vis mindre än 12 timmar.

### <a name="perform-post-restore-tasks"></a>Utföra uppgifter efter återställning

Efter en återställning från återställnings metoden bör du utföra följande uppgifter för att få dina användare och program att säkerhetskopiera och köra dem:

- Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klient program till den nya servern
- Se till att det finns lämpliga VNet-regler för att användare ska kunna ansluta. De här reglerna kopieras inte från den ursprungliga servern.
- Se till att lämpliga inloggningar och behörigheter på databas nivå är på plats
- Konfigurera aviseringar efter behov

## <a name="next-steps"></a>Nästa steg

- Mer information om verksamhets kontinuitet finns i [Översikt över affärs kontinuitet](concepts-business-continuity.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure Portal, se [restore server till en tidpunkt med hjälp av Azure Portal](howto-restore-server-portal.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure CLI kan du se [restore Server to Point-in-Time-using CLI](howto-restore-server-cli.md).
