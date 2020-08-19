---
title: Hög tillgänglighet – Azure Database for PostgreSQL-enskild server
description: Den här artikeln innehåller information om hög tillgänglighet i Azure Database for PostgreSQL-enskild server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/15/2020
ms.openlocfilehash: 16ce5b42e35ff3d650ba18aa95ab80b83fdbfdad
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547689"
---
# <a name="high-availability-in-azure-database-for-postgresql--single-server"></a>Hög tillgänglighet i Azure Database for PostgreSQL – enskild server
Tjänsten Azure Database for PostgreSQL – enskild server ger en garanterad hög tillgänglighets nivå med det ekonomiskt service avtal (SLA) på [99,99%](https://azure.microsoft.com/support/legal/sla/postgresql) drift tid. Azure Database for PostgreSQL ger hög tillgänglighet under planerade händelser som initated Scale Compute operation och även när oplanerade händelser som underliggande maskin vara, program eller nätverks fel inträffar. Azure Database for PostgreSQL kan snabbt återställas från de mest kritiska förhållandena, vilket säkerställer att det är praktiskt taget ingen program tids period när tjänsten används.

Azure Database for PostgreSQL är lämpligt för att köra verksamhets kritiska databaser som kräver hög drift tid. Tjänsten har byggts på Azure-arkitektur och har funktioner för hög tillgänglighet, redundans och återhämtning för att minimera databas stillestånd från planerade och oplanerade avbrott, utan att du behöver konfigurera några ytterligare komponenter. 

## <a name="components-in-azure-database-for-postgresql--single-server"></a>Komponenter i Azure Database for PostgreSQL – enskild server

| **Komponent** | **Beskrivning**|
| ------------ | ----------- |
| <b>PostgreSQL-databasserver | Azure Database for PostgreSQL tillhandahåller säkerhet, isolering, resurs skydd och snabb omstart för databas servrar. Dessa funktioner underlättar åtgärder som skalnings-och databas server återställnings åtgärder när ett avbrott inträffar på några sekunder. <br/> Data ändringar i databas servern sker vanligt vis i samband med en databas transaktion. Alla databas ändringar registreras synkront i form av Skriv Ahead-loggar (WAL) på Azure Storage – som är kopplad till databas servern. Under [kontroll punkts](https://www.postgresql.org/docs/11/sql-checkpoint.html) processen för databasen rensas data sidor från databas serverns minne också till lagringen. |
| <b>Fjärrlagring | Alla PostgreSQL fysiska datafiler och WAL-filer lagras på Azure Storage, vilket är utformat för att lagra tre kopior av data inom en region för att säkerställa dataredundans, tillgänglighet och tillförlitlighet. Lagrings lagret är också oberoende av databas servern. Den kan kopplas från en misslyckad databas server och återkopplas till en ny databas server inom några sekunder. Dessutom övervakas Azure Storage kontinuerligt för eventuella lagrings fel. Om en blockerande skada identifieras korrigeras den automatiskt genom att en ny lagrings kopia instansieras. |
| <b>Nyckeln | Gatewayen fungerar som en databas-proxy och dirigerar alla klient anslutningar till databas servern. |

## <a name="planned-downtime-mitigation"></a>Minskning av planerad stillestånds tid
Azure Database for PostgreSQL konstrueras för att ge hög tillgänglighet under planerade stillestånds åtgärder. 

![vy över elastisk skalning i Azure PostgreSQL](./media/concepts-high-availability/azure-postgresql-elastic-scaling.png)

1. Skala upp och ned PostgreSQL databas servrar på några sekunder
2. Gateway som fungerar som en proxy för att dirigera klienten ansluter till rätt databas server
3. En skalning av lagrings utrymmet kan utföras utan avbrott. Fjärrlagring möjliggör snabb från koppling/åter anslutning efter redundansväxlingen.
Här följer några planerade underhålls scenarier:

| **Scenario** | **Beskrivning**|
| ------------ | ----------- |
| <b>Beräknings skala upp/ned | När användaren utför en beräknings beräknings åtgärd, etableras en ny databas server med den skalade beräknings konfigurationen. På den gamla databas servern tillåts aktiva kontroll punkter att slutföras, klient anslutningar töms, eventuella obekräftade transaktioner avbryts och sedan stängs den av. Lagringen kopplas sedan bort från den gamla databas servern och kopplas till den nya databas servern. När klient programmet försöker ansluta igen eller försöker skapa en ny anslutning, dirigerar gatewayen anslutningsbegäran till den nya databas servern.|
| <b>Skala upp lagring | Att skala upp lagringen är en online-åtgärd och avbryter inte databas servern.|
| <b>Ny program varu distribution (Azure) | Nya funktioner distribution eller fel korrigeringar sker automatiskt som en del av tjänstens planerade underhåll. Mer information finns i [dokumentationen](https://docs.microsoft.com/azure/postgresql/concepts-monitoring#planned-maintenance-notification)och kontrol lera också [portalen](https://aka.ms/servicehealthpm).|
| <b>Lägre versions uppgraderingar | Azure Database for PostgreSQL automatiskt korrigering av databas servrar till den lägre versionen som bestäms av Azure. Det inträffar som en del av tjänstens planerade underhåll. Detta skulle innebära en kort stillestånds tid i sekunder och databas servern startas automatiskt om med den nya lägre versionen. Mer information finns i [dokumentationen](https://docs.microsoft.com/azure/postgresql/concepts-monitoring#planned-maintenance-notification)och kontrol lera också [portalen](https://aka.ms/servicehealthpm).|


##  <a name="unplanned-downtime-mitigation"></a>Minskning av oplanerade stillestånd

Oplanerade stillestånd kan uppstå på grund av oförutsedda fel, inklusive underliggande maskin varu fel, nätverks problem och program fel. Om databas servern slutar fungera som den ska skapas en ny databas server automatiskt på några sekunder. Fjärrlagringen ansluts automatiskt till den nya databas servern. PostgreSQL-motorn utför återställnings åtgärden med WAL-och databasfiler och öppnar databas servern så att klienter kan ansluta. Ej allokerade transaktioner förloras och måste göras om av programmet. En oplanerad stillestånds tid kan inte undvikas, Azure Database for PostgreSQL minimerar stillestånds tiden genom att automatiskt utföra återställnings åtgärder på både databas server och lagrings lager utan mänsklig inblandning. 


![vy över hög tillgänglighet i Azure PostgreSQL](./media/concepts-high-availability/azure-postgresql-built-in-high-availability.png)

1. Azure PostgreSQL-servrar med snabba skalnings funktioner.
2. Gateway som fungerar som proxy för att dirigera klient anslutningar till rätt databas server
3. Azure Storage med tre kopior för tillförlitlighet, tillgänglighet och redundans.
4. Fjärrlagring möjliggör även snabb från koppling/omkoppling efter serverns redundans.
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Oplanerat avbrott: haveri-scenarier och tjänst återställning
Här följer några fel scenarier och hur Azure Database for PostgreSQL återställer automatiskt:

| **Scenario** | **Automatisk återställning** |
| ---------- | ---------- |
| <B>Databas Server-problem | Om databas servern är nere på grund av ett underliggande maskin varu fel, släpps aktiva anslutningar och eventuella synlighetssekvensnummer transaktioner avbryts. En ny databas server distribueras automatiskt och fjärrlagringsplatsen är kopplad till den nya databas servern. När databas återställningen är klar kan klienter ansluta till den nya databas servern via gatewayen. <br /> <br /> Återställnings tiden (RTO) är beroende av olika faktorer, inklusive aktiviteten vid tidpunkten för felet, till exempel stor transaktion och mängden återställning som ska utföras under start processen för databas servern. <br /> <br /> Program som använder PostgreSQL-databaserna måste byggas på ett sätt som de kan identifiera och försöka ta bort anslutningar och misslyckade transaktioner.  När programmet försöker igen omdirigerar gatewayen transparent anslutning till den nyligen skapade databas servern. |
| <B>Lagrings problem | Program kan inte se någon inverkan på eventuella problem som rör lagring, till exempel diskfel eller fysiska blockerade fel. När data lagras i tre kopior betjänas data kopian av den kvarvarande lagringen. Block skador korrigeras automatiskt. Om en kopia av data går förlorad skapas automatiskt en ny kopia av data. |

Här följer några fel scenarier som kräver användar åtgärd för att återställa:

| **Scenario** | **Återställnings plan** |
| ---------- | ---------- |
| <b> Regions haveri | En regions haveri är en sällsynt händelse. Men om du behöver skydd från ett regions haveri kan du konfigurera en eller flera Läs repliker i andra regioner för haveri beredskap (DR). (Mer information finns i [den här artikeln](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) om att skapa och hantera Läs repliker). I händelse av ett problem på regions nivå kan du manuellt befordra den skrivskyddade replik som kon figurer ATS i den andra regionen som din produktions databas server. |
| <b> Logiska/användar fel | Återställning från användar fel, till exempel oavsiktligt borttagna tabeller eller felaktigt uppdaterade data, innebär att utföra en [tidpunkts återställning](https://docs.microsoft.com/azure/postgresql/concepts-backup) (PITR), genom att återställa och återställa data tills tiden strax innan felet uppstod.<br> <br>  Om du bara vill återställa en delmängd av databaser eller vissa tabeller i stället för alla databaser på databas servern, kan du återställa databas servern i en ny instans, exportera tabellen/tabellerna via [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)och sedan använda [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) för att återställa tabellerna till din databas. |



## <a name="summary"></a>Sammanfattning

Azure Database for PostgreSQL ger snabb omstart av databas servrar, redundant lagring och effektiv routning från gatewayen. För ytterligare data skydd kan du konfigurera säkerhets kopieringar så att de blir geo-replikerade och även distribuera en eller flera Läs repliker i andra regioner. Med funktioner med hög tillgänglighet kan Azure Database for PostgreSQL skydda dina databaser från de flesta vanliga drifts avbrott och erbjuder en branschledande, finans [99,99% av drift tid SLA](https://azure.microsoft.com/support/legal/sla/postgresql). Alla dessa tillgänglighets-och Tillförlitlighets funktioner gör att Azure är den perfekta plattformen för att köra verksamhets kritiska program.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure-regioner](../availability-zones/az-overview.md)
- Lär dig mer om [hantering av tillfälliga anslutnings fel](concepts-connectivity.md)
- Lär dig hur du [replikerar dina data med Läs repliker](howto-read-replicas-portal.md)
