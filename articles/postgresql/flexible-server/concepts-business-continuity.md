---
title: Översikt över affärs kontinuitet med Azure Database for PostgreSQL flexibel Server
description: Lär dig mer om begreppen affärs kontinuitet med Azure Database for PostgreSQL flexibel Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 341d3c6f0a2d84c2b7b725959db9f7e5dd80d3ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92542972"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---flexible-server"></a>Översikt över affärs kontinuitet med Azure Database for PostgreSQL flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

**Verksamhets kontinuitet** i Azure Database for PostgreSQL-flexibel Server syftar på mekanismer, principer och procedurer som gör det möjligt för din verksamhet att fortsätta att arbeta i störnings miljön, särskilt för dess infrastruktur. I de flesta fall hanterar den flexibla servern de störande händelser som kan uppstå i moln miljön och se till att dina program och affärs processer körs. Det finns dock vissa händelser som inte kan hanteras automatiskt, till exempel:

- Användaren tar oavsiktligt bort eller uppdaterar en rad i en tabell.
- Jord bävning orsakar ett strömavbrott och tillfälligt inaktiverar ett Data Center eller en tillgänglighets zon.
- Databas korrigering krävs för att åtgärda ett fel eller säkerhets problem.

Flexibel Server innehåller funktioner som skyddar data och minimerar stillestånds tiden för dina verksamhets kritiska databaser i händelse av planerade och oplanerade stillestånds händelser. Flexibel Server har byggts ovanpå den Azure-infrastruktur som redan erbjuder robust återhämtning och tillgänglighet, och har funktioner för affärs kontinuitet som ger ytterligare fel skydd, krav för återställnings tid och minskar risken för data förlust. När du skapar dina program bör du ta hänsyn till toleransen för stillestånds tiden, vilket är återställnings tids målet (RTO) och data förlust exponering – vilket är återställnings punkt mål. Din verksamhets kritiska databas kräver till exempel mycket strängare drift tids krav jämfört med en test databas.  

> [!IMPORTANT]
> Drift tid% service nivå avtal (SLA) som inte erbjuds under för hands versionen. 

Tabellen nedan visar de funktioner som flexibla Server erbjuder.


| **Funktion** | **Beskrivning** | **Överväganden** |
| ---------- | ----------- | ------------ |
| **Automatisk säkerhets kopiering** | Flexibel Server utför automatiskt dagliga säkerhets kopieringar av databasfilerna och säkerhetskopierar regelbundet transaktions loggar. Säkerhets kopieringar kan behållas från 7 dagar upp till 35 dagar. Du kommer att kunna återställa din databas server till alla tidpunkter inom säkerhets kopierings perioden. RTO är beroende av storleken på de data som ska återställas och tiden för att utföra logg återställningen. Det kan vara från några minuter upp till 12 timmar. Mer information finns i [begrepp – säkerhets kopiering och återställning](./concepts-backup-restore.md). |Säkerhets kopierings data förblir i regionen. |
| **Zon redundant hög tillgänglighet** | Flexibel Server kan distribueras med Zone-konfiguration med redundant hög tillgänglighet (HA) där primära och standby-servrar distribueras i två olika tillgänglighets zoner inom en region. Med den här underhålls konfigurationen skyddas dina databaser från andra zon nivå problem och kan också hjälpa till att minska avbrotts tiden för programmet under planerade och oplanerade stillestånds händelser. Data från den primära servern replikeras till vänte läges repliken i synkront läge. Om det uppstår avbrott till den primära servern växlar servern automatiskt över till loggen för växling vid fel. RTO i de flesta fall förväntas finnas i 60 s-120S. Återställnings värde förväntas vara noll (ingen data förlust). Mer information finns i [begrepp – hög tillgänglighet](./concepts-high-availability.md). | Stöds i generell användnings-och minnesoptimerade beräknings nivåer. Endast tillgängligt i regioner där flera zoner är tillgängliga. |
| **Premium-hanterade diskar** | Databasfiler lagras i ett mycket tåligt och tillförlitligt Premium-hanterat lagrings utrymme. Detta ger dataredundans med tre kopior av repliker lagrade i en tillgänglighets zon med funktioner för automatisk data återställning. Mer information finns i [dokumentationen om Managed disks](../../virtual-machines/managed-disks-overview.md). | Data som lagras i en tillgänglighets zon. |
| **Redundant säkerhets kopia av zonen** | Flexibla Server säkerhets kopieringar lagras automatiskt och lagras på ett säkert sätt i en zon redundant lagring inom en region. Under ett haveri på zon nivå där servern är etablerad och om servern inte har kon figurer ATS med zon redundans kan du fortfarande återställa databasen med den senaste återställnings punkten i en annan zon. Mer information finns i [begrepp – säkerhets kopiering och återställning](./concepts-backup-restore.md).| Endast tillgängligt i regioner där flera zoner är tillgängliga.|


## <a name="planned-downtime-events"></a>Planerade stillestånds händelser
Nedan visas några planerade underhålls scenarier. Dessa händelser tar vanligt vis upp till några minuter av drift stopp och utan data förlust.

| **Scenario** | **Process**|
| ------------------- | ----------- | 
| <b>Beräknings skalning (användaren har initierats)| Under beräknings skalnings åtgärden kan aktiva kontroll punkter slutföras, klient anslutningar töms, eventuella icke allokerade transaktioner avbryts, lagringen kopplas från och sedan stängs. En ny flexibel server med samma databas server namn etablerades med den skalade beräknings konfigurationen. Lagringen bifogas sedan till den nya servern och databasen startas, som utför återställningen om det behövs innan klient anslutningarna accepteras. |
| <b>Skala upp lagring (användaren har initierats) | När en skalning av lagrings åtgärden initieras, tillåts aktiva kontroll punkter att slutföras, klient anslutningar töms, eventuella obekräftade transaktioner avbryts och sedan stängs den av. Lagrings utrymmet skalas till önskad storlek och kopplas sedan till den nya servern. En återställning utförs vid behov innan klient anslutningar accepteras. Observera att det inte finns stöd för att skala ned lagrings storleken. |
| <b>Ny program varu distribution (Azure-initierad) | Nya funktioner distribution eller fel korrigeringar sker automatiskt som en del av tjänstens planerade underhåll, och du kan schemalägga när aktiviteterna ska ske. Mer information hittar du i [portalen](https://aka.ms/servicehealthpm). | 
| <b>Lägre versions uppgraderingar (Azure-initierad) | Azure Database for PostgreSQL automatiskt korrigering av databas servrar till den lägre versionen som bestäms av Azure. Det inträffar som en del av tjänstens planerade underhåll. Databas servern startas automatiskt om med den nya lägre versionen. Mer information finns i [dokumentationen](../concepts-monitoring.md#planned-maintenance-notification). Du kan också kontrol lera [portalen](https://aka.ms/servicehealthpm).| 

 När den flexibla servern har kon figurer ATS med aktive rad **hög tillgänglighet för zonen** utför den flexibla servern skalningen och underhålls åtgärderna på standby-servern först. Mer information finns i [begrepp – hög tillgänglighet](./concepts-high-availability.md).

##  <a name="unplanned-downtime-mitigation"></a>Minskning av oplanerade stillestånd

Oplanerade stillestånd kan uppstå på grund av oförutsedda störningar, till exempel underliggande maskin varu fel, nätverks problem och program fel. Om databas servern som kon figurer ATS med hög tillgänglighet slutar fungera, aktive ras standby-repliken och klienterna kan återuppta sina åtgärder. Om det inte har kon figurer ATS med hög tillgänglighet (HA), kommer en ny databas server automatiskt att tillhandahållas om försöket att starta om Miss lyckas. Även om det inte går att undvika ett oplanerat avbrott kan du minska drift stoppen genom att utföra återställnings åtgärder automatiskt utan att kräva mänsklig inblandning. 
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Oplanerat avbrott: haveri-scenarier och tjänst återställning
Nedan visas några oplanerade krascher och återställnings processen. 

| **Scenario** | **Återställnings process [icke-HA]** | **Återställnings process [HA]** |
| ---------- | ---------- | ------- |
| <B>Databas Server-problem | Om databas servern inte är igång försöker Azure starta om databas servern. Om det Miss lyckas startas databas servern om på en annan fysisk nod.  <br /> <br /> Återställnings tiden (RTO) är beroende av olika faktorer, inklusive aktiviteten vid tidpunkten för felet, till exempel stor transaktion och volymen av återställningen som ska utföras under start processen för databas servern. <br /> <br /> Program som använder PostgreSQL-databaserna måste byggas på ett sätt som de kan identifiera och försöka ta bort anslutningar och misslyckade transaktioner. | Om databas server fel identifieras växlar servern över till standby-servern, vilket minskar stillestånds tiden. Mer information finns på [sidan med ha koncept](./concepts-high-availability.md). RTO förväntas vara 60-120S med noll data förlust. |
| <B>Lagrings problem | Program kan inte se någon inverkan på eventuella problem som rör lagring, till exempel diskfel eller fysiska blockerade fel. När data lagras i tre kopior betjänas data kopian av den kvarvarande lagringen. Det skadade data blocket repareras automatiskt och en ny kopia av data skapas automatiskt. | För ovanliga och icke-återställnings bara fel, till exempel att hela lagringen inte är tillgänglig, växlas den flexibla servern över till standby-repliken för att minska stillestånds tiden. Mer information finns på [sidan med ha koncept](./concepts-high-availability.md). |
| <b> Logiska/användar fel | Om du vill återställa från användar fel, till exempel oavsiktligt borttagna tabeller eller felaktigt uppdaterade data, måste du utföra en [tidpunkts återställning](../concepts-backup.md) (PITR). När du utför återställnings åtgärden anger du den anpassade återställnings punkten, vilket är tiden precis innan felet inträffade.<br> <br>  Om du bara vill återställa en delmängd av databaser eller vissa tabeller i stället för alla databaser på databas servern, kan du återställa databas servern i en ny instans, exportera tabellen/tabellerna via [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)och sedan använda [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) för att återställa tabellerna till din databas. | Dessa användar fel skyddas inte med hög tillgänglighet eftersom alla ändringar replikeras till standby-replikeringen synkront. Du måste utföra återställning efter en viss tid för att återställa från sådana fel. |
| <b> Tillgänglighets zon haveri | Om du vill återställa från ett fel på zon nivå kan du utföra återställning efter en viss tidpunkt med hjälp av säkerhets kopieringen och välja en anpassad återställnings punkt med den senaste tiden för att återställa den senaste informationen. En ny flexibel Server distribueras i en annan icke-påverkad zon. Hur lång tid det tar att återställa beror på den tidigare säkerhets kopian och volymen av transaktions loggar som ska återställas. | En flexibel Server växlas automatiskt över till standby-servern inom 60-120S med noll data förlust. Mer information finns på [sidan med ha koncept](./concepts-high-availability.md). | 
| <b> Regions haveri | Det finns ännu inte stöd för att läsa replikering mellan regioner och geo-återställning av säkerhetskopierade funktioner i för hands versionen. | |


> [!IMPORTANT]
> **Det går inte** att återställa borttagna servrar. Om du tar bort servern tas även alla databaser som tillhör servern bort och kan inte återställas. Använd [Azure Resource lock](../../azure-resource-manager/management/lock-resources.md) för att förhindra oavsiktlig borttagning av servern.


## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om [Zone-redundant hög tillgänglighet](./concepts-high-availability.md)
-   Läs mer om [säkerhets kopiering och återställning](./concepts-backup-restore.md)