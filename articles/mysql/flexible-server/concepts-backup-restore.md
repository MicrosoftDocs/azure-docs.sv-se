---
title: Säkerhets kopiering och återställning i Azure Database for MySQL flexibel Server
description: Lär dig mer om begreppen säkerhets kopiering och återställning med Azure Database for MySQL flexibel Server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2d69427f9f11a47cedeccb4b1da38b770952f029
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240774"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>Säkerhets kopiering och återställning i Azure Database for MySQL flexibel Server (för hands version)

> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Azure Database for MySQL flexibel Server skapar automatiskt Server säkerhets kopior och lagrar dem på ett säkert sätt i den lokala redundanta lagringen i regionen. Säkerhetskopieringar kan användas för att återställa servern till en vald tidpunkt. Säkerhetskopiering och återställning är en viktig del i strategin för affärskontinuitet, eftersom de skyddar dina data från oavsiktlig skada eller borttagning.

## <a name="backup-overview"></a>Översikt över Backup

Flexibel server tar ögonblicks bilder av säkerhets kopior av datafilerna och lagrar dem i en lokal redundant lagring. Servern utför också säkerhets kopiering av transaktions loggar och lagrar även dem i lokalt redundant lagring. Med dessa säkerhets kopieringar kan du återställa en server till alla tidpunkter inom den konfigurerade kvarhållningsperioden för säkerhets kopior. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Du kan också konfigurera säkerhets kopian av databasen från 1 till 35 dagar. Alla säkerhets kopior krypteras med AES 256-bitars kryptering för data som lagras i vila.

Det går inte att exportera de här säkerhetskopierade filerna. Säkerhets kopiorna kan bara användas för återställnings åtgärder i flexibel Server. Du kan också använda [mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) från en MySQL-klient för att kopiera en databas.

## <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

Säkerhets kopieringar på flexibla servrar är Snapshot-baserade. Den första fullständiga säkerhetskopieringen schemaläggs omedelbart efter att en server har skapats. Den första fullständiga säkerhets kopieringen behålls som serverns grundläggande säkerhets kopiering. Efterföljande säkerhetskopieringar av ögonblicksbilder är bara differentiella säkerhetskopieringar.

Differentiella säkerhetskopieringar av ögonblicksbilder görs minst en gång per dag. Differentiella säkerhetskopieringar av ögonblicksbilder sker inte enligt ett fast schema. Differentiella säkerhetskopieringar av ögonblicksbilder sker en gång per dygn såvida inte de binära loggarna i MySQL överskrider 50 GB sedan den senaste differentiella säkerhetskopieringen. Högst sex differentiella ögonblicksbilder tillåts under samma dag. Säkerhetskopieringar av transaktionsloggar sker var femte minut.

## <a name="backup-retention"></a>Kvarhållningsperiod för säkerhetskopior

Databas säkerhets kopior lagras i en lokal redundant lagring (LRS), som lagras i tre kopior i en region. Säkerhets kopior bevaras baserat på inställningen för kvarhållning av säkerhets kopior på servern. Du kan välja en kvarhållningsperiod på 1 till 35 dagar med en standard kvarhållningsperiod är sju dagar. Du kan ställa in kvarhållningsperioden när servern skapas eller senare genom att uppdatera säkerhets kopierings konfigurationen med Azure Portal.

Kvarhållningsperioden för säkerhets kopior styr hur långt tillbaka i tiden som kan utföras när en återställning sker vid en viss tidpunkt, eftersom den baseras på tillgängliga säkerhets kopior. Kvarhållningsperioden för säkerhets kopior kan också behandlas som ett återställnings fönster från ett återställnings perspektiv. Alla säkerhets kopior som krävs för att utföra en återställning efter en viss tidpunkt inom kvarhållning av säkerhets kopior bevaras i säkerhets kopierings lagringen. Exempel: om kvarhållningsperioden för säkerhets kopior har angetts till sju dagar, betraktas återställnings fönstret de senaste sju dagarna. I det här scenariot behålls alla säkerhets kopior som krävs för att återställa servern under de senaste sju dagarna. Med en kvarhållning av säkerhets kopior av sju dagar lagras databas ögonblicks bilder och säkerhets kopior av transaktions loggar under de senaste åtta dagarna (1 dag före fönstret).

## <a name="backup-storage-cost"></a>Reserv lagrings kostnad

Flexibel Server ger upp till 100% av din etablerade Server lagring som säkerhets kopierings lagring utan extra kostnad. Ytterligare lagrings utrymme för säkerhets kopior debiteras i GB per månad. Om du till exempel har etablerad en server med 250 GB lagrings utrymme har du 250 GB lagrings utrymme för Server säkerhets kopiering utan extra kostnad. Om den dagliga säkerhets kopierings användningen är 25 GB kan du ha upp till 10 dagars kostnads fri lagring av säkerhets kopior. Förbrukad lagring för säkerhets kopieringar över 250 GB debiteras enligt [pris sättnings modellen](https://azure.microsoft.com/pricing/details/mysql/).

Du kan använda måttet [säkerhets kopierings lagring som används](../concepts-monitoring.md) i Azure Monitor tillgängligt i Azure Portal för att övervaka säkerhets kopierings lagringen som används av en server. Måttet mått för **säkerhets kopierings lagring** representerar summan av lagrings utrymme som förbrukas av alla databas säkerhets kopior och logg säkerhets kopior som bevaras baserat på den kvarhållna säkerhets kopierings perioden som angetts för servern. Krävande transaktionsaktivitet på servern kan orsaka att lagringsanvändningen för säkerhetskopior ökar oberoende av databasens totala storlek.

Det främsta sättet att kontrol lera lagrings kostnaden för säkerhets kopiering är genom att ställa in lämplig kvarhållningsperiod för säkerhets kopior. Du kan välja en kvarhållningsperiod mellan 1 och 35 dagar.

> [!IMPORTANT]
> Säkerhets kopieringar från en databas server som kon figurer ATS i en zon redundant konfiguration för hög tillgänglighet sker från den primära databas servern eftersom omkostnaderna är minimala med ögonblicks säkerhets kopior.

> [!IMPORTANT]
> Geo-redundanta säkerhets kopieringar stöds för närvarande inte med en flexibel Server.

## <a name="point-in-time-restore"></a>Återställning från tidpunkt

I Azure Database for MySQL flexibel Server skapar en återställning vid en viss tidpunkt en ny server från den flexibla serverns säkerhets kopior i samma region som käll servern. Den skapas med den ursprungliga serverns konfiguration för beräknings nivån, antalet virtuella kärnor, lagrings storlek, bevarande period för säkerhets kopior och alternativet för redundans. Dessutom ärvs Taggar och inställningar, till exempel virtuella nätverk och brand väggar från käll servern. Den återställda serverns beräknings-och lagrings nivå, konfigurations-och säkerhets inställningar kan ändras när återställningen har slutförts.

> [!NOTE]
> Det finns två server parametrar som återställs till standardvärdena (och kopieras inte över från den primära servern) efter återställnings åtgärden
> *   time_zone – det här värdet ställs in på standardvärdet SYSTEM
> *   event_scheduler – event_scheduler har angetts till av på den återställda servern

Återställning av tidpunkt är användbart i flera scenarier. Några av de användnings fall som är vanliga inkluderar-
-   När en användare av misstag tar bort data i databasen
-   Användaren släpper en viktig tabell eller databas
-   Användar Programmet skriver av misstag över bra data med felaktiga data på grund av ett program fel.

Du kan välja mellan en senaste återställnings punkt och en anpassad återställnings punkt via [Azure Portal](how-to-restore-server-portal.md).

-   **Senaste återställnings punkt**: den senaste återställnings punkten hjälper dig att återställa servern till den senaste säkerhets kopieringen som utförts på käll servern. Tidsstämpeln för återställning visas också på portalen. Det här alternativet är användbart för att snabbt återställa servern till det mest uppdaterade läget.
-   **Anpassad återställnings punkt**: med det här alternativet kan du välja vilken tidpunkt som helst inom den kvarhållningsperiod som definierats för den här flexibla servern. Det här alternativet är användbart för att återställa servern vid den exakta tidpunkten för att återställa från ett användar fel.

Den uppskattade återställnings tiden beror på flera faktorer, till exempel databas storlek, säkerhets kopierings storlek för transaktions logg, beräknings storlek för SKU: n och även tiden för återställningen. Återställningen av transaktions loggen är den mest tids krävande delen av återställnings processen. Om återställnings tiden väljs närmare säkerhets kopierings schema för fullständig eller differentiell ögonblicks bild är återställningarna snabbare eftersom transaktions logg programmet är minimalt. För att uppskatta den korrekta återställnings tiden för servern rekommenderar vi starkt att du testar den i din miljö eftersom den har för många miljövariabler.

> [!IMPORTANT]
> Om du återställer en flexibel server som kon figurer ATS med redundant zon hög tillgänglighet, konfigureras den återställda servern i samma region och zon som den primära servern, och distribueras som en enda flexibel server i ett icke-förbrukat läge. Se [zonens redundanta hög tillgänglighet](concepts-high-availability.md) för flexibel Server.

> [!IMPORTANT]
> **Det går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas. För att skydda server resurser, efter distribution, från oavsiktlig borttagning eller oväntade ändringar, kan administratörer utnyttja [hanterings lås](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Utföra uppgifter efter återställning

Efter en återställning från antingen den senaste återställnings **punkten** eller en anpassad återställnings **punkt** , bör du utföra följande uppgifter för att få dina användare och program att säkerhetskopiera och köra:

-   Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klient program till den nya servern.
-   Se till att det finns lämpliga regler för brand väggar och virtuella nätverk på server nivå för att användare ska kunna ansluta.
-   Se till att lämpliga inloggningar och behörigheter på databas nivå är på plats.
-   Konfigurera aviseringar efter behov.

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om [verksamhets kontinuitet](./concepts-business-continuity.md)
-   Lär dig mer om [Zone-redundant hög tillgänglighet](./concepts-high-availability.md)
-   Läs mer om [säkerhets kopiering och återställning](./concepts-backup-restore.md)
