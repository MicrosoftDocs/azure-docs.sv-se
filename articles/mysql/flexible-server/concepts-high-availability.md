---
title: Översikt över zonens redundant hög tillgänglighet med Azure Database for MySQL flexibel Server
description: Lär dig mer om begreppen redundanta zoner med hög tillgänglighet med Azure Database for MySQL flexibel Server
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 6629beacb5c3edc6fe1d21509051b915c0894479
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109700"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Koncept med hög tillgänglighet i Azure Database for MySQL flexibel Server (för hands version)

> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Azure Database for MySQL flexibel Server (för hands version) gör att du kan konfigurera hög tillgänglighet med automatisk redundans med hjälp av **zon redundant** alternativ för hög tillgänglighet. När flexibel server distribueras i en zonredundant konfiguration etablerar den och hanterar automatiskt en standby-replik i en annan tillgänglighetszon. Med replikering på lagrings nivå **replikeras data synkront** till vänte läges servern i den sekundära zonen för att möjliggöra ingen data förlust efter en redundansväxling. Redundansväxlingen är helt transparent från klient programmet och kräver inte några åtgärder från användaren. Standby-servern är inte tillgänglig för läsnings-eller Skriv åtgärder, men är ett passivt vänte läge för att möjliggöra snabb redundans. Redundansväxlingen sker vanligt vis i intervallet 60-120 sekunder.

Zon redundant konfiguration för hög tillgänglighet möjliggör automatisk redundans under planerade händelser, t. ex. åtgärder för att skala beräknings åtgärder och oplanerade händelser, till exempel underliggande maskin-och program varu fel, nätverks fel och även tillgänglighets zon fel.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="vy över zonens redundanta hög tillgänglighet":::

## <a name="zone-redundancy-architecture"></a>Arkitektur för zon redundans

Den primära servern distribueras i regionen och i en speciell tillgänglighets zon. När du väljer hög tillgänglighet distribueras en reserv replik server med samma konfiguration som den primära servern automatiskt, inklusive beräknings nivå, beräknings storlek, lagrings storlek och nätverks konfiguration. Loggdata replikeras synkront till standby-repliken för att säkerställa att ingen data förlust uppstår i någon annan situation. Automatisk säkerhets kopiering, både ögonblicks bilder och logg säkerhets kopior, utförs från den primära databas servern. 

Hälso tillståndet för HA övervakas kontinuerligt och rapporteras på översikts sidan.

De olika replikeringsstatus visas nedan:

| **Status** | **Beskrivning** |
| :----- | :------ |
| <b>NotEnabled | Zonens redundanta HA är inte aktive rad |
| <b>CreatingStandby | Håller på att skapa ett nytt vänte läge |
| <b>ReplicatingData | När standby har skapats, fångas det upp med den primära servern. |
| <b>FailingOver | Databas servern håller på att redundansväxla från den primära till vänte läge. |
| <b>Felfri | Zonens redundanta HA är i stabilt tillstånd och felfritt. |
| <b>RemovingStandby | Den vänte repliken håller på att tas bort baserat på användar åtgärd.| 

## <a name="advantages"></a>Fördelar

Här är några fördelar med att använda zon redundans HA-funktionen: 

- Standby-replikering distribueras i en exakt VM-konfiguration som primär, till exempel virtuella kärnor, lagring, nätverks inställningar (VNET, brand vägg) osv.
- Möjlighet att ta bort standby-replikering genom att inaktivera hög tillgänglighet.
- Automatiska säkerhets kopieringar är ögonblicks bilds, utförda från den primära databas servern och lagras i en zon redundant lagring.
- I händelse av redundans växlar Azure Database for MySQL flexibla servern automatiskt över till vänte läge om hög tillgänglighet är aktiverat. Installations programmet för hög tillgänglighet övervakar den primära servern och ansluter den igen.
- Klienterna ansluter alltid till den primära databas servern.
- Om det uppstår ett databas krasch-eller nodfel görs försök att starta om först på samma nod. Om detta Miss lyckas utlöses den automatiska redundansväxlingen.
- Möjlighet att starta om servern för att hämta eventuella ändringar av statiska Server parametrar.

## <a name="steady-state-operations"></a>Stabila tillstånds åtgärder

Program är anslutna till den primära servern med hjälp av databas Server namnet. Information om standby-replikering visas inte för direkt åtkomst. Incheckningar och skrivningar bekräftas till programmet endast efter att loggfilerna har sparats på både den primära serverns disk och vänte läges repliken på ett synkront sätt. På grund av detta ytterligare krav för tur och retur kan program förväntas förhöjd svars tid för skrivningar och incheckningar. Du kan övervaka hälso tillståndet för hög tillgänglighet på portalen.

## <a name="failover-process"></a>Redundansväxling 
För affärs kontinuitet måste du ha en failover-process för planerade och oplanerade händelser. 

### <a name="planned-events"></a>Planerade händelser

Planerade stillestånds händelser omfattar aktiviteter som schemalagts av Azure, till exempel periodiska program uppdateringar, lägre versions uppgraderingar eller som initieras av kunder, till exempel skalning av beräknings-och skalnings lagrings åtgärder. Alla dessa ändringar tillämpas först på den förväntande repliken. Under den tiden fortsätter programmen att komma åt den primära servern. När standby-repliken har uppdaterats töms de primära server anslutningarna, en redundansväxling utlöses, vilket aktiverar vänte läges repliken så att den blir primär med samma databas server namn genom att uppdatera DNS-posten. Klient anslutningarna är frånkopplade och de måste återanslutas och de kan återuppta sina åtgärder. En ny standby-Server upprättas i samma zon som den gamla primära. Den totala redundansväxlingen förväntas vara 60-120 s. 

>[!NOTE]
> I händelse av beräknings skalnings åtgärden skalar vi den sekundära replik servern följt av den primära servern. Ingen redundans är involverad.

### <a name="failover-process---unplanned-events"></a>Redundansväxling – oplanerade händelser
Oplanerade avbrott i tjänsten innefattar program varu fel som eller infrastruktur fel som beräkning, nätverk, lagrings fel eller strömavbrott eller strömavbrott påverkar databasens tillgänglighet. Om databasen inte är tillgänglig är replikeringen till standby-repliken svår och standby-repliken är aktive rad som den primära databasen. DNS uppdateras och klienter återansluter till databas servern och återupptar sina åtgärder. Den totala redundansväxlingen förväntas ta 60-120 s. Beroende på aktiviteten på den primära databas servern vid tidpunkten för redundansväxlingen, till exempel stora transaktioner och återställnings tid, kan redundansväxlingen ta längre tid.

## <a name="schedule-maintenance-window"></a>Schemalägg underhålls period 

I flexibla servrar finns funktioner för underhålls schemaläggning där du kan välja en 30-minuters period under en dag i din preferens då Azure-underhållet fungerar, till exempel uppdatering eller lägre versions uppgraderingar. För dina flexibla servrar som kon figurer ATS med hög tillgänglighet utförs dessa underhålls aktiviteter på den förväntande repliken först. 

## <a name="point-in-time-restore"></a>Återställning från tidpunkt 
Eftersom flexibel Server har kon figurer ATS vid hög tillgänglighet replikeras data i synkront läge, så är standby-servern uppdaterad med den primära. Alla användar fel i den primära, till exempel en oavsiktlig släpp av en tabell eller felaktiga uppdateringar, replikeras till vänte läge. Därför kan du inte använda standby för att återställa från sådana logiska fel. Om du vill återställa från de logiska felen måste du utföra återställning av tidpunkt till höger innan felet inträffade. När du återställer databasen som kon figurer ATS med hög tillgänglighet med hjälp av en flexibel servers funktion för återställning av en viss tidpunkt återställs en ny databas server som en ny flexibel server med ett namn som användaren anger. Sedan kan du exportera objektet från databas servern och importera det till produktions databas servern. På samma sätt kan du antingen välja den senaste återställnings punkten eller en anpassad återställnings punkt om du vill klona databas servern för testnings-och utvecklings ändamål, eller om du vill återställa i något annat syfte. Återställnings åtgärden skapar en flexibel server med en zon.

## <a name="mitigate-downtime"></a>Minimera drift stopp 
När du inte använder zon redundans måste du fortfarande kunna minimera stillestånds tiden för programmet. Planera avbrott i tjänsten, till exempel schemalagda uppdateringar, mindre versions uppgraderingar eller åtgärder som initieras av användaren kan utföras som en del av det schemalagda underhålls fönstret. Planerade avbrott i tjänsten, till exempel schemalagda uppdateringar, mindre versions uppgraderingar eller initierade åtgärder som skalnings beräkning medför drift stopp. För att minimera program påverkan för Azure initierade underhålls aktiviteter kan du välja att schemalägga dem under den veckodag och den tid som minst påverkar programmet. 

Vid oplanerade stillestånds händelser, till exempel databas krasch eller Server haveri, startas den berörda servern om i samma zon. I sällsynta fall kan du, om hela zonen påverkas, återställa databasen på en annan zon inom regionen. 

## <a name="things-to-know-with-zone-redundancy"></a>Saker att känna till med zon redundans 

Här är några saker som du bör tänka på när du använder zon-redundans hög tillgänglighet: 

- Hög tillgänglighet kan **bara** ställas in under flexibel Server skapande tid.
- Hög tillgänglighet stöds inte i en data behandlings nivå med burst.
- På grund av synkron replikering till en annan tillgänglighets zon kan den primära databas servern uppleva förhöjda Skriv-och bekräftelse svars tider.
- Det går inte att använda standby-repliker för skrivskyddade frågor.
- Beroende på aktivitet på den primära servern vid redundansväxlingen kan det ta upp till 60-120 sekunder eller längre tid att slutföra redundansväxlingen.
- Att starta om den primära databas servern för att hämta statiska parameter ändringar startar även om repliken för vänte läge.
- Det finns inte stöd för att konfigurera Läs repliker för redundanta zoner med hög tillgänglighet.
- Konfigurering av kundens initierade hanterings uppgifter kan inte automatiseras under hanterat underhålls fönster.
- Planerade händelser som skalnings beräknings-och del versions uppgraderingar sker både i primär och standby på samma gång. 


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [verksamhets kontinuitet](./concepts-business-continuity.md)
- Lär dig mer om [Zone-redundant hög tillgänglighet](./concepts-high-availability.md)
- Läs mer om [säkerhets kopiering och återställning](./concepts-backup-restore.md)
