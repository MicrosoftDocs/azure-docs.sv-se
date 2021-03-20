---
title: Säkerhets kopiering och återställning i Azure Database for PostgreSQL-flexibel Server
description: Lär dig mer om begreppen säkerhets kopiering och återställning med Azure Database for PostgreSQL-flexibel Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d0e79e42c7c004638336ada23de663bbe74b7e48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92532653"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Säkerhets kopiering och återställning i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Säkerhets kopior utgör en viktig del av en strategi för affärs kontinuitet. De hjälper till att skydda data från oavsiktlig skada eller borttagning. Azure Database for PostgreSQL-flexibel Server säkerhetskopierar servern automatiskt och behåller säkerhets kopiorna under en varaktighet på upp till 35 dagar. När du återställer kan du ange det datum och den tid som du vill återställa under kvarhållningsperioden. Den totala tiden för att återställa och återställa beror på storleken på databasfilerna och mängden återställning som ska utföras. 

### <a name="backup-process-in-flexible-server"></a>Säkerhets kopierings process i flexibel Server
Den första ögonblicksbilden för säkerhetskopiering schemaläggs omedelbart efter att den flexibla servern har skapats. Därefter utförs en daglig ögonblicks bild säkerhets kopiering av datafiler. Säkerhets kopior lagras i en zon redundant lagring inom en region. Transaktions loggar (Skriv kommande loggar-WAL) arkiveras också kontinuerligt, så att du kan återställa till den senaste genomförda transaktionen. Med dessa data- och loggsäkerhetskopieringar kan du återställa en server till valfri tidpunkt inom den konfigurerade kvarhållningsperioden för säkerhetskopior. Alla säkerhetskopior krypteras med AES 256-bitars kryptering.

Om databasen har kon figurer ATS med hög tillgänglighet utförs dagliga ögonblicks bilder från den primära databasen och de kontinuerliga logg säkerhets kopiorna utförs från vänte läge.

> [!IMPORTANT]
>Säkerhets kopieringar utförs inte på stoppade servrar. Säkerhets kopieringar återupptas dock när databasen antingen startas automatiskt efter sju dagar eller startas av användaren.

Säkerhets kopiorna kan bara användas för återställnings åtgärder i den flexibla servern. Om du vill exportera eller importera data till den flexibla servern använder du [dumpnings-och återställnings](../howto-migrate-using-dump-and-restore.md) metodik.


### <a name="backup-retention"></a>Kvarhållningsperiod för säkerhetskopior

Säkerhets kopior bevaras baserat på inställningen för kvarhållning av säkerhets kopior för servern. Du kan välja en kvarhållningsperiod mellan 7 och 35 dagar. Standard kvarhållningsperioden är sju dagar. Du kan ange kvarhållningsperioden när servern skapas eller så kan du uppdatera den vid ett senare tillfälle. Säkerhets kopior bevaras även för stoppade servrar.

Kvarhållningsperioden för säkerhets kopior styr hur långt tillbaka i tiden en tidpunkts återställning kan hämtas, eftersom den är \' baserad på tillgängliga säkerhets kopior. Kvarhållningsperioden för säkerhets kopior kan också behandlas som ett återställnings fönster från ett återställnings perspektiv. Alla säkerhets kopior som krävs för att utföra en återställning efter en viss tidpunkt inom säkerhets kopie perioden behålls i lagrings utrymmet för säkerhets kopieringen. Exempel: om kvarhållningsperioden för säkerhets kopior anges till sju dagar, betraktas återställnings fönstret som de senaste sju dagarna. I det här scenariot behålls alla data och loggar som krävs för att återställa och återställa servern under de senaste sju dagarna. 


### <a name="backup-storage-cost"></a>Reserv lagrings kostnad

Flexibel Server ger upp till 100% av din etablerade Server lagring som säkerhets kopierings lagring utan extra kostnad. Ytterligare lagrings utrymme för säkerhets kopior debiteras i GB per månad. Om du till exempel har etablerad en server med 250 GiB lagrings utrymme har du 250 GiB backup Storage-kapacitet utan extra kostnad. Om den dagliga säkerhets kopierings användningen är 25 GiB kan du ha upp till 10 dagars kostnads fri säkerhets kopierings lagring. Lagrings förbrukningen för säkerhets kopiering som överstiger 250 GiB debiteras enligt [pris sättnings modellen](https://azure.microsoft.com/pricing/details/postgresql/).

Du kan använda måttet [för använd säkerhetskopieringslagring](../concepts-monitoring.md) i Azure Portal för att övervaka mängden säkerhetskopieringslagring som används av en server. Måttet för använd säkerhetskopieringslagring representerar summan av det lagringsutrymme som förbrukas av alla databassäkerhetskopior och loggsäkerhetskopior som bevaras baserat på den kvarhållningsperiod för säkerhetskopior som angetts för servern.  Krävande transaktionsaktivitet på servern kan orsaka att lagringsanvändningen för säkerhetskopior ökar oberoende av databasens totala storlek.

Det främsta sättet att kontrol lera lagrings kostnaden för säkerhets kopiering är genom att ställa in lämplig kvarhållningsperiod för säkerhets kopior och välja rätt alternativ för säkerhets kopiering för att uppfylla önskade återställnings mål.

> [!IMPORTANT]
> Geo-redundanta säkerhets kopieringar stöds för närvarande inte med en flexibel Server.

## <a name="point-in-time-restore-overview"></a>Översikt över återställning av tidpunkt

I flexibel Server skapar en återställning vid en viss tidpunkt en ny server från den flexibla serverns \' säkerhets kopior i samma region som käll servern. Den skapas med käll serverns konfiguration för pris nivån, beräknings generering, antal virtuella kärnor, lagrings storlek, kvarhållning av säkerhets kopior och alternativet för redundans. Dessutom ärvs taggar och inställningar som till exempel VNET- och brandväggsinställningar från källservern. 

 > [!IMPORTANT]
> Om du återställer en flexibel server som kon figurer ATS med redundant zon hög tillgänglighet, konfigureras den återställda servern utan hög tillgänglighet och i samma region som din primära server. 

 ### <a name="restore-process"></a>Återställ process

De fysiska databasfilerna återställs först från ögonblicks säkerhets kopiorna till serverns data plats. Lämplig säkerhets kopia som togs tidigare än den önskade tidpunkten väljs automatiskt och återställs. En återställnings process initieras sedan med WAL-filer för att försätta databasen i ett konsekvent tillstånd. 

 Låt oss till exempel anta att säkerhets kopiorna utförs vid 23:00 varje natt. Om återställnings punkten är den 15 augusti 2020 kl. 10:00, återställs den dagliga säkerhets kopieringen den 14 augusti 2020. Databasen kommer att återställas fram till 10 25 augusti 2020 med säkerhets kopian av transaktions loggar mellan 24 augusti 23:00 fram till 25 augusti 10. 

 Se [de här stegen](./how-to-restore-server-portal.md) för att återställa databas servern.

> [!IMPORTANT]
> Återställnings åtgärder i flexibel Server skapar en ny databas server och skriver inte över den befintliga databas servern.

Återställning av tidpunkt är användbart i flera scenarier. Till exempel när en användare oavsiktligt tar bort data, släpper en viktig tabell eller databas, eller om ett program av misstag skriver över bra data med felaktiga data på grund av ett program fel. Du kommer att kunna återställa till den senaste transaktionen på grund av kontinuerlig säkerhets kopiering av transaktions loggar.

Du kan välja mellan en tidigaste återställnings punkt och en anpassad återställnings punkt.

-   **Tidigaste återställnings punkt**: beroende på din kvarhållningsperiod, är det den tidigaste tiden som du kan återställa. Den äldsta säkerhets kopierings tiden kommer automatiskt att väljas och visas på portalen. Detta är användbart om du vill undersöka eller göra några tester som startar den tidpunkten.

-   **Anpassad återställnings punkt**: med det här alternativet kan du välja vilken tidpunkt som helst inom den kvarhållningsperiod som definierats för den här flexibla servern. Som standard väljs den senaste tiden i UTC automatiskt och är användbar om du vill återställa till den senaste genomförda transaktionen för ditt test syfte. Du kan också välja andra dagar och tidpunkter. 

Den uppskattade tiden som ska återställas beror på flera faktorer, till exempel databas storlek, volym för transaktions loggar som ska bearbetas, nätverks bandbredden och det totala antalet databaser som återställs i samma region på samma gång. Den totala återställnings tiden tar vanligt vis från några minuter upp till några timmar.


> [!IMPORTANT]
> **Det går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas. För att skydda server resurser, efter distribution, från oavsiktlig borttagning eller oväntade ändringar, kan administratörer utnyttja [hanterings lås](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Utföra uppgifter efter återställning

När du har återställt databasen kan du utföra följande uppgifter för att få dina användare och program att säkerhetskopiera och köra dem:

-   Om den nya servern är avsedd att ersätta den ursprungliga servern omdirigerar du klienter och klient program till den nya servern.

-   Se till att det finns tillräckligt med brand Väggs-och VNet-regler på server nivå för att användarna ska kunna ansluta. De här reglerna kopieras inte från den ursprungliga servern.
  
-   Den återställda serverns beräkning kan skalas upp/ned efter behov.

-   Se till att lämpliga inloggningar och behörigheter på databas nivå är på plats.

-   Konfigurera aviseringar efter behov.
  
-  Om du har återställt databasen som kon figurer ATS med hög tillgänglighet, och om du vill konfigurera den återställda servern med hög tillgänglighet, kan du följa [stegen](./how-to-manage-high-availability-portal.md).


## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om [verksamhets kontinuitet](./concepts-business-continuity.md)
-   Lär dig mer om [Zone-redundant hög tillgänglighet](./concepts-high-availability.md)
-   Lär dig [hur du återställer](./how-to-restore-server-portal.md)