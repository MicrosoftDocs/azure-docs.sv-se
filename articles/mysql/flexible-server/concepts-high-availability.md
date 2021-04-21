---
title: Översikt över zonredundant hög tillgänglighet med Azure Database for MySQL flexibel server
description: Lär dig mer om begreppen zonredundant hög tillgänglighet med Azure Database for MySQL flexibel server
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 5b5e1491d7f76cd4cff76d0c9a1af4daa49fa483
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813010"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Begrepp för hög tillgänglighet i Azure Database for MySQL Flexibel server (förhandsversion)

> [!IMPORTANT] 
> Azure Database for MySQL – Flexibel server är för närvarande i offentlig förhandsversion.

Azure Database for MySQL Flexibel server (förhandsversion) gör det möjligt att konfigurera hög tillgänglighet med automatisk redundans med **zonredundant** alternativ för hög tillgänglighet. När flexibel server distribueras i en zonredundant konfiguration etablerar den och hanterar automatiskt en standby-replik i en annan tillgänglighetszon. Med hjälp av replikering på lagringsnivå replikeras data **synkront** till standby-servern i den sekundära zonen för att noll data ska gå förlorade efter en redundans. Redundansen är helt transparent från klientprogrammet och kräver inga användaråtgärder. Standby-servern är inte tillgänglig för läs- eller skrivåtgärder, men är ett passivt vänteläge för snabb redundans. Redundansen varierar vanligtvis mellan 60–120 sekunder.

Konfiguration av zonredundant hög tillgänglighet möjliggör automatisk redundans under planerade händelser, till exempel användarinitierade skalningsbearbetningsåtgärder och oplanerade händelser som underliggande maskin- och programvarufel, nätverksfel och till och med fel i tillgänglighetszoner.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="vy över zonredundant hög tillgänglighet":::

## <a name="zone-redundancy-architecture"></a>Arkitektur för zonredundans

Den primära servern distribueras i regionen och en specifik tillgänglighetszon. När den höga tillgängligheten väljs distribueras en reservreplikserver med samma konfiguration som den primära servern automatiskt, inklusive beräkningsnivå, beräkningsstorlek, lagringsstorlek och nätverkskonfiguration. Loggdata replikeras synkront till väntelägesrepliken för att säkerställa noll dataförlust i eventuella fel. Automatiska säkerhetskopieringar, både ögonblicksbilder och loggsäkerhetskopior, utförs från den primära databasservern. 

Hälsotillståndet för ha övervakas kontinuerligt och rapporteras på översiktssidan.

De olika replikeringsstatusarna visas nedan:

| **Status** | **Beskrivning** |
| :----- | :------ |
| <b>NotEnabled | Zonredundant HA är inte aktiverat |
| <b>CreatingStandby | I processen med att skapa ett nytt vänteläge |
| <b>ReplicatingData | När vänteläget har skapats kommer det ikapp den primära servern. |
| <b>Om det inte går att göra det | Databasservern är i en process för att gå över från den primära servern till vänteläget. |
| <b>Felfri | Zonredundant HA är i stabilt och felfritt tillstånd. |
| <b>Ta bortStandby | Baserat på användaråtgärden är standby-repliken under borttagning.| 

## <a name="advantages"></a>Fördelar

Här är några fördelar med att använda funktionen för zonredundans: 

- Standby Replica distribueras i en exakt VM-konfiguration som för primär, till exempel virtuella kärnor, lagring, nätverksinställningar (VNET, brandvägg) osv.
- Möjlighet att ta bort väntelägesrepliker genom att inaktivera hög tillgänglighet.
- Automatiska säkerhetskopieringar är ögonblicksbildbaserade, utförs från den primära databasservern och lagras i zonredundant lagring.
- I händelse av redundans redundans Azure Database for MySQL flexibel server automatiskt över till standby-repliken om hög tillgänglighet är aktiverad. Konfigurationen för hög tillgänglighet övervakar den primära servern och tar den online igen.
- Klienter ansluter alltid till den primära databasservern.
- Om det uppstår en databaskrasch eller nodfel görs ett omstartsförsök först på samma nod. Om det misslyckas utlöses den automatiska redundansen.
- Möjlighet att starta om servern för att hämta eventuella ändringar av statiska serverparametrar.

## <a name="steady-state-operations"></a>Åtgärder med stabilt tillstånd

Program ansluts till den primära servern med databasservernamnet. Väntelägesreplikinformationen exponeras inte för direkt åtkomst. Genomföranden och skrivningar bekräftas endast för programmet efter att loggfilerna har bevarats på både den primära serverns disk och väntelägesrepliken på ett synkront sätt. På grund av detta ytterligare krav för tur och retur kan program förvänta sig längre svarstider vid skrivningar och genomföranden. Du kan övervaka hälsotillståndet för hög tillgänglighet i portalen.

## <a name="failover-process"></a>Redundansprocess 
För affärskontinuering måste du ha en redundansprocess för planerade och oplanerade händelser. 

### <a name="planned-events"></a>Planerade händelser

Planerade avbrott är aktiviteter som schemalagts av Azure, till exempel periodiska programuppdateringar, mindre versionsuppgraderingar eller som initieras av kunder, till exempel skalning av beräknings- och skalningsåtgärder. Alla dessa ändringar tillämpas först på standby-repliken. Under den tiden fortsätter programmen att komma åt den primära servern. När väntelägesrepliken har uppdaterats töms primära serveranslutningar. En redundans utlöses som aktiverar standby-repliken så att den blir primär med samma databasservernamn genom att uppdatera DNS-posten. Klientanslutningar kopplas från och de måste återansluta och kan återuppta sina åtgärder. En ny väntelägesserver upprättas i samma zon som den gamla primära. Den totala redundanstiden förväntas vara 60–120 s. 

>[!NOTE]
> Vid beräkningsskalningsåtgärden skalar vi den sekundära replikservern följt av den primära servern. Det ingår ingen redundans.

### <a name="failover-process---unplanned-events"></a>Redundans – oplanerade händelser
Oplanerade tjänstavbrott omfattar programvarufel som eller infrastrukturfel som beräknings-, nätverks-, lagringsfel eller strömavbrott påverkar databasens tillgänglighet. Om databasen blir otillgänglig avaktiveras replikeringen till standby-repliken och standby-repliken aktiveras för att vara den primära databasen. DNS uppdateras och klienterna återansluter sedan till databasservern och återupptar sina åtgärder. Den totala redundanstiden förväntas ta 60–120 sekunder. Beroende på aktiviteten på den primära databasservern vid tidpunkten för redundansen, till exempel stora transaktioner och återställningstid, kan redundansen ta längre tid.

### <a name="forced-failover"></a>Framtvinga redundans
Azure Database for MySQL framtvingad redundans kan du manuellt framtvinga en redundans, så att du kan testa funktionerna i dina programscenarier och vara redo vid eventuella avbrott. Framtvingad redundans växlar standby-servern till att bli den primära servern genom att utlösa en redundans som aktiverar standby-repliken så att den blir den primära servern med samma databasservernamn genom att uppdatera DNS-posten. Den ursprungliga primära servern startas om och växlas till en reservreplik. Klientanslutningar kopplas från och måste återanslutas för att återuppta driften. Beroende på den aktuella arbetsbelastningen och den senaste kontrollpunkten mäts den totala redundanstiden. I allmänhet förväntas det vara mellan 60–120-tal.

## <a name="schedule-maintenance-window"></a>Schemalägg underhållsfönstret 

Flexibla servrar erbjuder schemaläggningsfunktioner för underhåll där du kan välja en 30-minutersfönster på en dag då Azure-underhållet fungerar, till exempel om det skulle ske korrigeringar eller lägre versionsuppgraderingar. För dina flexibla servrar som konfigurerats med hög tillgänglighet utförs dessa underhållsaktiviteter först på standby-repliken. 

## <a name="point-in-time-restore"></a>Återställning från tidpunkt 
Eftersom flexibel server konfigureras med hög tillgänglighet, replikerar data synkront, är standby-servern uppdaterad med den primära servern. Alla användarfel på den primära – till exempel en oavsiktligt bortrullningsad tabell eller felaktiga uppdateringar replikeras automatiskt till vänteläget. Därför kan du inte använda vänteläge för att återställa från sådana logiska fel. Om du vill återställa från dessa logiska fel måste du utföra en återställning till en tidpunkt precis innan felet inträffade. När du återställer databasen som konfigurerats med hög tillgänglighet med hjälp av den flexibla serverns funktioner för återställning till tidpunkt, återställs en ny databasserver som en ny flexibel server med ett namn som anges av användaren. Du kan sedan exportera objektet från databasservern och importera det till produktionsdatabasservern. På samma sätt kan du välja den senaste återställningspunkten eller en anpassad återställningspunkt om du vill klona databasservern för testnings- och utvecklingssyften, eller om du vill återställa för andra syften. Återställningsåtgärden skapar en flexibel server med en zon.

## <a name="mitigate-downtime"></a>Minimera stilleståndstid 
När du inte använder funktionen Zonredundans med tillgänglighet måste du fortfarande kunna minimera avbrottstiden för ditt program. Planering av tjänstavbrott, till exempel schemalagda korrigeringar, lägre versionsuppgraderingar eller användarinitierade åtgärder kan utföras som en del av schemalagda underhållsfönster. Planerade tjänstavbrott, till exempel schemalagda korrigeringar, lägre versionsuppgraderingar eller användarinitierade åtgärder, till exempel skalningsbearbetning, medför driftstopp. För att minimera programpåverkan för Azure-initierade underhållsaktiviteter kan du välja att schemalägga dem under veckodagen och tiden, vilket påverkar programmet minst. 

Vid oplanerade driftstopp, till exempel databaskrasch eller serverfel, startas den påverkade servern om inom samma zon. Även om det är ovanligt kan du återställa databasen i en annan zon i regionen om hela zonen påverkas. 

## <a name="things-to-know-with-zone-redundancy"></a>Saker att känna till med zonredundans 

Här är några saker att tänka på när du använder zonredundans med hög tillgänglighet: 

- Hög tillgänglighet kan **bara ställas** in under flexibel server-create-tid.
- Hög tillgänglighet stöds inte på beräkningsnivån burstable.
- På grund av synkron replikering till en annan tillgänglighetszon kan den primära databasservern uppleva förhöjd svarstid för skrivning och genomförande.
- Väntelägesreplik kan inte användas för skrivskyddade frågor.
- Beroende på aktiviteten på den primära servern vid tidpunkten för redundansen kan det ta upp till 60–120 sekunder eller längre för redundansen att slutföras.
- När den primära databasservern startas om för att hämta ändringar av statiska parametrar startas även standby-repliken om.
- Konfiguration av skrivskyddade repliker för zonredundant servrar med hög tillgänglighet stöds inte.
- Konfiguration av kundinitierade hanteringsuppgifter kan inte automatiseras under underhållsfönstret.
- Planerade händelser som skalningsbearbetning och lägre versionsuppgraderingar sker i både primärt läge och vänteläge samtidigt. 


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [affärskontinuhet](./concepts-business-continuity.md)
- Läs mer om [zonredundant hög tillgänglighet](./concepts-high-availability.md)
- Lär dig mer [om säkerhetskopiering och återställning](./concepts-backup-restore.md)
