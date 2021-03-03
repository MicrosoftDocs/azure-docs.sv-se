---
title: Översikt över verksamhets kontinuitet – Azure Database for MySQL flexibel Server
description: Lär dig mer om begreppen affärs kontinuitet med Azure Database for MySQL flexibel Server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 4f9cc8321d5d1d19dbcb8294ad6205b01337ee72
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715062"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---flexible-server-preview"></a>Översikt över affärs kontinuitet med Azure Database for MySQL flexibel Server (för hands version)

> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Azure Database for MySQL flexibla Server ger affärs kontinuitets funktioner som skyddar dina databaser i händelse av ett planerat och oplanerat avbrott. Funktioner som automatiserade säkerhets kopieringar och hög tillgänglighet hanterar olika nivåer av fel skydd med olika återställnings tider och exponeringar för data förlust. När du skapar ditt program för att skydda mot fel bör du överväga återställnings tid målet (RTO) och återställnings punkt mål (återställnings punkt mål) för varje program. RTO är toleransen för stillestånds tiden och driften är data förlust toleransen efter ett avbrott i databas tjänsten.

Tabellen nedan visar de funktioner som flexibla Server erbjuder.

| **Funktion** | **Beskrivning** | **Begränsningar** |
| ---------- | ----------- | ------------ |
| **Säkerhets kopiering & återställning** | Flexibel Server utför automatiskt dagliga säkerhets kopieringar av databasfilerna och säkerhetskopierar regelbundet transaktions loggar. Säkerhets kopior kan bevaras under en period mellan 1 och 35 dagar. Du kommer att kunna återställa din databas server till alla tidpunkter inom säkerhets kopierings perioden. Återställnings tiden kommer att vara beroende av storleken på de data som ska återställas och för att utföra logg återställningen. Se [begrepp – säkerhets kopiering och återställning](./concepts-backup-restore.md) för mer information. |Säkerhets kopierings data förblir inom regionen |
| **Lokal redundant säkerhets kopiering** | Flexibla Server säkerhets kopieringar lagras automatiskt på ett säkert sätt i en lokal redundant lagring inom en region och i samma tillgänglighets zon. De lokalt redundanta säkerhets kopieringarna replikerar datafilerna för Server säkerhets kopiering tre gånger inom en fysisk plats i den primära regionen. Lokalt redundant lagring för säkerhets kopiering ger minst 99,999999999% (11 nio) objekt hållbarhet under ett angivet år. Se [begrepp – säkerhets kopiering och återställning](./concepts-backup-restore.md) för mer information.| Tillämplig i alla regioner |
| **Zon redundant hög tillgänglighet** | Flexibel Server kan distribueras i läget för hög tillgänglighet, vilket distribuerar primära och standby-servrar i två olika tillgänglighets zoner inom en region. Detta skyddar från felaktiga zon nivåer och hjälper till att minska avbrotts tiden för programmet under planerade och oplanerade drift stopp. Data från den primära servern replikeras synkront till standby-repliken. Under en händelse av stillestånds tid växlar databas servern automatiskt över till standby-repliken. Se [begrepp – hög tillgänglighet](./concepts-high-availability.md) för mer information. | Stöds i generell användnings-och minnesoptimerade beräknings nivåer. Endast tillgängligt i regioner där flera zoner är tillgängliga.|
| **Premium fil resurser** | Databasfiler lagras i en mycket tålig och pålitliga Azure Premium-filresurser som ger dataredundans med tre kopior av repliker lagrade i en tillgänglighets zon med funktioner för automatisk data återställning. Mer information finns i [Premium File-resurser](../../storage/files/storage-how-to-create-file-share.md) . | Data som lagras i en tillgänglighets zon |

> [!IMPORTANT]
> Ingen drift tid, RTO och återställnings service avtal erbjuds under för hands perioden. Information som anges på den här sidan för din information och planerings syfte.

## <a name="planned-downtime-mitigation"></a>Minskning av planerad stillestånds tid

Här följer några planerade underhålls scenarier som ådrar sig avbrott:

| **Scenario** | **Process**|
| :------------ | :----------- |
| **Beräknings skalning (användare)**| När du utför beräknings skalnings åtgärden etablerades en ny flexibel server med hjälp av den skalade beräknings konfigurationen. I den befintliga databas servern tillåts aktiva kontroll punkter att slutföras, klient anslutningar töms, eventuella obekräftade transaktioner avbryts och sedan stängs den av. Lagringen bifogas sedan till den nya servern och databasen startas, som utför återställningen om det behövs innan klient anslutningarna accepteras. |
| **Ny program varu distribution (Azure)** | Nya funktioner distribution eller fel korrigeringar sker automatiskt som en del av tjänstens planerade underhåll, och du kan schemalägga när aktiviteterna ska ske. Mer information finns i [dokumentationen](https://aka.ms/servicehealthpm)och kontrol lera även [portalen](https://aka.ms/servicehealthpm) |
| **Lägre versions uppgraderingar (Azure)** | Azure Database for MySQL automatiskt korrigering av databas servrar till den lägre versionen som bestäms av Azure. Det inträffar som en del av tjänstens planerade underhåll. Detta skulle innebära en kort stillestånds tid i sekunder och databas servern startas automatiskt om med den nya lägre versionen. Mer information finns i [dokumentationen](../concepts-monitoring.md#planned-maintenance-notification)och kontrol lera även [portalen](https://aka.ms/servicehealthpm).|

När den flexibla servern har kon figurer ATS med aktive rad **hög tillgänglighet för zonen** utför den flexibla servern åtgärder på vänte läges servern först och sedan på den primära servern utan redundans. Se [begrepp – hög tillgänglighet](./concepts-high-availability.md) för mer information.

## <a name="unplanned-downtime-mitigation"></a>Minskning av oplanerade stillestånd

Oplanerade stillestånd kan uppstå på grund av oförutsedda fel, inklusive underliggande maskin varu fel, nätverks problem och program fel. Om databas servern slutar fungera, om den har kon figurer ATS med hög tillgänglighet [HA], aktive ras standby-repliken. Annars tillhandahålls en ny databas server automatiskt. Även om det inte går att undvika ett oplanerat avbrott kan du lösa drift stoppen genom att automatiskt utföra återställnings åtgärder på både databas server och lagrings lager utan mänsklig inblandning.

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Oplanerat avbrott: haveri-scenarier och tjänst återställning

Här följer några oplanerade haveri-och återställnings processer:

| **Scenario** | **Återställnings process [icke-HA]** | **Återställnings process [HA]** |
| :---------- | ---------- | ------- |
| **Databas Server-problem** | Om databas servern är nere på grund av ett underliggande maskin varu fel, släpps aktiva anslutningar och eventuella synlighetssekvensnummer transaktioner avbryts. Azure kommer att försöka starta om databas servern. Om detta lyckas utförs databas återställningen. Om omstarten Miss lyckas görs ett försök att starta om databas servern på en annan fysisk nod.  <br /> <br /> Återställnings tiden (RTO) är beroende av olika faktorer, inklusive aktiviteten vid tidpunkten för felet, till exempel stor transaktion och mängden återställning som ska utföras under start processen för databas servern. <br /> <br /> Program som använder MySQL-databaserna måste byggas på ett sätt som identifierar och försöker släppa anslutningar och misslyckade transaktioner.  När programmet försöker igen dirigeras anslutningarna till den nyligen skapade databas servern. | Om databas servern kraschar aktive ras standby-databasservern, vilket minskar stillestånds tiden. Mer information finns på [sidan med ha begrepp](concepts-high-availability.md) . RTO förväntas vara 60-120 s, med återställnings punkt = 0 |
| **Lagrings problem** | Program kan inte se någon inverkan på eventuella problem som rör lagring, till exempel diskfel eller fysiska blockerade fel. När data lagras i tre kopior betjänas data kopian av den kvarvarande lagringen. Block skador korrigeras automatiskt. Om en kopia av data går förlorad skapas automatiskt en ny kopia av data. | För icke-återställnings bara fel växlar den flexibla servern över till standby-repliken för att minska stillestånds tiden. Mer information finns på [sidan med ha begrepp](./concepts-high-availability.md) . |
| **Logiska/användar fel** | Återställning från användar fel, till exempel oavsiktligt borttagna tabeller eller felaktigt uppdaterade data, innebär att utföra en [tidpunkts återställning](concepts-backup-restore.md) (PITR), genom att återställa och återställa data tills tiden strax innan felet uppstod.<br> <br>  Om du bara vill återställa en delmängd av databaser eller vissa tabeller i stället för alla databaser på databas servern, kan du återställa databas servern i en ny instans, exportera tabellen/tabellerna via [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html)och sedan använda [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) för att återställa tabellerna till din databas. | Dessa användar fel skyddas inte med hög tillgänglighet på grund av det faktum att alla användar åtgärder replikeras till standby. |
| **Tillgänglighets zon haveri** | Även om det är en sällsynt händelse, om du vill återställa från ett fel på zon nivå, kan du utföra återställning vid en viss tidpunkt med hjälp av säkerhets kopieringen och välja anpassad återställnings punkt för att komma till den senaste informationen. En ny flexibel server kommer att distribueras i en annan zon. Hur lång tid det tar att återställa beror på den tidigare säkerhets kopian och antalet transaktions loggar som ska återställas. | Flexibel Server utför automatisk redundans på platsen för vänte läge. Mer information finns på [sidan med ha begrepp](./concepts-high-availability.md) . |
| **Regions haveri** | Replikering mellan regioner och geo-återställnings funktioner stöds inte ännu i för hands versionen. | |

> [!IMPORTANT]
> **Det går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas. Använd [Azure Resource lock](../../azure-resource-manager/management/lock-resources.md) för att förhindra oavsiktlig borttagning av servern.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Zone-redundant hög tillgänglighet](./concepts-high-availability.md)
- Läs mer om [säkerhets kopiering och återställning](./concepts-backup-restore.md)