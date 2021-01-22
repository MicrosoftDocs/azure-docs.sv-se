---
title: Hög tillgänglighet – Azure Database for MariaDB
description: Den här artikeln innehåller information om hög tillgänglighet i Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: jroth
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: dc37474a56ddb7d2c48c7acfce881fb812f0b8a4
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664341"
---
# <a name="high-availability-in-azure-database-for-mariadb"></a>Hög tillgänglighet i Azure Database for MariaDB
Tjänsten Azure Database for MariaDB tillhandahåller en garanterad hög tillgänglighets nivå med det ekonomiskt service avtal (SLA) på [99,99%](https://azure.microsoft.com/support/legal/sla/MariaDB) drift tid. Azure Database for MariaDB ger hög tillgänglighet under planerade händelser som initated Scale Compute operation och även när oplanerade händelser som underliggande maskin vara, program eller nätverks fel inträffar. Azure Database for MariaDB kan snabbt återställas från de mest kritiska förhållandena, vilket säkerställer att det är praktiskt taget ingen program tids period när tjänsten används.

Azure Database for MariaDB är lämpligt för att köra verksamhets kritiska databaser som kräver hög drift tid. Tjänsten har byggts på Azure-arkitektur och har funktioner för hög tillgänglighet, redundans och återhämtning för att minimera databas stillestånd från planerade och oplanerade avbrott, utan att du behöver konfigurera några ytterligare komponenter. 

## <a name="components-in-azure-database-for-mariadb"></a>Komponenter i Azure Database for MariaDB

| **Komponent** | **Beskrivning**|
| ------------ | ----------- |
| <b>MariaDB-databasserver | Azure Database for MariaDB tillhandahåller säkerhet, isolering, resurs skydd och snabb omstart för databas servrar. Dessa funktioner underlättar åtgärder som skalnings-och databas server återställnings åtgärder när ett avbrott inträffar på några sekunder. <br/> Data ändringar i databas servern sker vanligt vis i samband med en databas transaktion. Alla databas ändringar registreras synkront i form av Skriv Ahead-loggar (ib_log) på Azure Storage – som är kopplad till databas servern. Under [kontroll punkts](https://mariadb.com/kb/innodb-redo-log/#checkpoints) processen för databasen rensas data sidor från databas serverns minne också till lagringen. |
| <b>Fjärrlagring | Alla MariaDB fysiska datafiler och loggfiler lagras på Azure Storage, vilket är utformat för att lagra tre kopior av data inom en region för att säkerställa dataredundans, tillgänglighet och tillförlitlighet. Lagrings lagret är också oberoende av databas servern. Den kan kopplas från en misslyckad databas server och återkopplas till en ny databas server inom några sekunder. Dessutom övervakas Azure Storage kontinuerligt för eventuella lagrings fel. Om en blockerande skada identifieras korrigeras den automatiskt genom att en ny lagrings kopia instansieras. |
| <b>Gateway | Gatewayen fungerar som en databas-proxy och dirigerar alla klient anslutningar till databas servern. |

## <a name="planned-downtime-mitigation"></a>Minskning av planerad stillestånds tid
Azure Database for MariaDB konstrueras för att ge hög tillgänglighet under planerade stillestånds åtgärder. 

![vy över elastisk skalning i Azure MariaDB](./media/concepts-high-availability/elastic-scaling-mariadb-server.png)

Här följer några planerade underhålls scenarier:

| **Scenario** | **Beskrivning**|
| ------------ | ----------- |
| <b>Beräknings skala upp/ned | När användaren utför en beräknings beräknings åtgärd, etableras en ny databas server med den skalade beräknings konfigurationen. På den gamla databas servern tillåts aktiva kontroll punkter att slutföras, klient anslutningar töms, eventuella obekräftade transaktioner avbryts och sedan stängs den av. Lagringen kopplas sedan bort från den gamla databas servern och kopplas till den nya databas servern. När klient programmet försöker ansluta igen eller försöker skapa en ny anslutning, dirigerar gatewayen anslutningsbegäran till den nya databas servern.|
| <b>Skala upp lagring | Att skala upp lagringen är en online-åtgärd och avbryter inte databas servern.|
| <b>Ny program varu distribution (Azure) | Nya funktioner distribution eller fel korrigeringar sker automatiskt som en del av tjänstens planerade underhåll. Mer information finns i [dokumentationen](concepts-monitoring.md#planned-maintenance-notification)och kontrol lera också [portalen](https://aka.ms/servicehealthpm).|
| <b>Lägre versions uppgraderingar | Azure Database for MariaDB automatiskt korrigering av databas servrar till den lägre versionen som bestäms av Azure. Det inträffar som en del av tjänstens planerade underhåll. Detta skulle innebära en kort stillestånds tid i sekunder och databas servern startas automatiskt om med den nya lägre versionen. Mer information finns i [dokumentationen](concepts-monitoring.md#planned-maintenance-notification)och kontrol lera också [portalen](https://aka.ms/servicehealthpm).|


##  <a name="unplanned-downtime-mitigation"></a>Minskning av oplanerade stillestånd

Oplanerade stillestånd kan uppstå på grund av oförutsedda fel, inklusive underliggande maskin varu fel, nätverks problem och program fel. Om databas servern slutar fungera som den ska skapas en ny databas server automatiskt på några sekunder. Fjärrlagringen ansluts automatiskt till den nya databas servern. MariaDB-motorn utför återställnings åtgärden med WAL-och databasfiler och öppnar databas servern så att klienter kan ansluta. Ej allokerade transaktioner förloras och måste göras om av programmet. En oplanerad stillestånds tid kan inte undvikas, Azure Database for MariaDB minimerar stillestånds tiden genom att automatiskt utföra återställnings åtgärder på både databas server och lagrings lager utan mänsklig inblandning. 


![vy över hög tillgänglighet i Azure MariaDB](./media/concepts-high-availability/availability-mariadb-server.png)

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Oplanerat avbrott: haveri-scenarier och tjänst återställning
Här följer några fel scenarier och hur Azure Database for MariaDB återställer automatiskt:

| **Scenario** | **Automatisk återställning** |
| ---------- | ---------- |
| <B>Databas Server-problem | Om databas servern är nere på grund av ett underliggande maskin varu fel, släpps aktiva anslutningar och eventuella synlighetssekvensnummer transaktioner avbryts. En ny databas server distribueras automatiskt och fjärrlagringsplatsen är kopplad till den nya databas servern. När databas återställningen är klar kan klienter ansluta till den nya databas servern via gatewayen. <br /> <br /> Program som använder MariaDB-databaserna måste byggas på ett sätt som de kan identifiera och försöka ta bort anslutningar och misslyckade transaktioner.  När programmet försöker igen omdirigerar gatewayen transparent anslutning till den nyligen skapade databas servern. |
| <B>Lagrings problem | Program kan inte se någon inverkan på eventuella problem som rör lagring, till exempel diskfel eller fysiska blockerade fel. När data lagras i tre kopior betjänas data kopian av den kvarvarande lagringen. Block skador korrigeras automatiskt. Om en kopia av data går förlorad skapas automatiskt en ny kopia av data. |

Här följer några fel scenarier som kräver användar åtgärd för att återställa:

| **Scenario** | **Återställnings plan** |
| ---------- | ---------- |
| <b> Regions haveri | En regions haveri är en sällsynt händelse. Men om du behöver skydd från ett regions haveri kan du konfigurera en eller flera Läs repliker i andra regioner för haveri beredskap (DR). (Mer information finns i [den här artikeln](howto-read-replicas-portal.md) om att skapa och hantera Läs repliker). I händelse av ett problem på regions nivå kan du manuellt befordra den skrivskyddade replik som kon figurer ATS i den andra regionen som din produktions databas server. |
| <b> Logiska/användar fel | Återställning från användar fel, till exempel oavsiktligt borttagna tabeller eller felaktigt uppdaterade data, innebär att utföra en [tidpunkts återställning](concepts-backup.md) (PITR), genom att återställa och återställa data tills tiden strax innan felet uppstod.<br> <br>  Om du bara vill återställa en delmängd av databaser eller vissa tabeller i stället för alla databaser på databas servern, kan du återställa databas servern i en ny instans, exportera tabellen/tabellerna via [mysqldump](howto-migrate-dump-restore.md)och sedan använda [restore](howto-migrate-dump-restore.md#restore-your-mariadb-database) för att återställa dessa tabeller till databasen. |



## <a name="summary"></a>Sammanfattning

Azure Database for MariaDB ger snabb omstart av databas servrar, redundant lagring och effektiv routning från gatewayen. För ytterligare data skydd kan du konfigurera säkerhets kopieringar så att de blir geo-replikerade och även distribuera en eller flera Läs repliker i andra regioner. Med funktioner med hög tillgänglighet kan Azure Database for MariaDB skydda dina databaser från de flesta vanliga drifts avbrott och erbjuder en branschledande, finans [99,99% av drift tid SLA](https://azure.microsoft.com/support/legal/sla/MariaDB). Alla dessa tillgänglighets-och Tillförlitlighets funktioner gör att Azure är den perfekta plattformen för att köra verksamhets kritiska program.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure-regioner](../availability-zones/az-overview.md)
- Lär dig mer om [hantering av tillfälliga anslutnings fel](concepts-connectivity.md)
- Lär dig hur du [replikerar dina data med Läs repliker](howto-read-replicas-portal.md)