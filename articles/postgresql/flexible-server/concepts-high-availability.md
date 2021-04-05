---
title: Översikt över zonens redundant hög tillgänglighet med Azure Database for PostgreSQL flexibel Server (för hands version)
description: Lär dig mer om begreppen redundanta zoner med hög tillgänglighet med Azure Database for PostgreSQL-flexibel Server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: c0d9b6042ae695caa73d926653f237b756bf4971
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94366731"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Koncept med hög tillgänglighet i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Azure Database for PostgreSQL-flexibel Server erbjuder en konfiguration med hög tillgänglighet med automatisk redundans med hjälp av **Zone-redundant** Server distribution. När flexibel server distribueras i en zonredundant konfiguration etablerar den och hanterar automatiskt en standby-replik i en annan tillgänglighetszon. Med PostgreSQL streaming-replikering replikeras data till vänte läges replik servern i **synkront** läge. 

Med redundanta zoner i zonen aktive ras automatisk redundans utan data förlust under planerade händelser som användardefinierad skalnings beräknings åtgärd och även vid oplanerade händelser som underliggande maskin-och program varu fel, nätverks fel och fel i tillgänglighets zoner. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="zon redundant hög tillgänglighet"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Arkitektur för redundant hög tillgänglighet för zonen

Du kan välja den region och tillgänglighetszon där du vill distribuera den primära databasservern. En standby-replikserver etableras i en annan tillgänglighetszon med samma konfiguration som den primära servern, inklusive beräkningsnivå, beräkningsstorlek, lagringsstorlek och nätverkskonfiguration. Transaktions loggar replikeras i synkront läge till standby-repliken med PostgreSQL streaming-replikering. Automatiska säkerhets kopieringar utförs regelbundet från den primära databas servern, medan transaktions loggarna arkiveras kontinuerligt till säkerhets kopierings lagringen från vänte repliken. 

Hälso tillståndet för konfigurationen med hög tillgänglighet övervakas kontinuerligt och rapporteras på portalen. Zonens redundanta status för hög tillgänglighet visas nedan:

| **Status** | **Beskrivning** |
| ------- | ------ |
| <b> Initierar | I processen för att skapa en ny standby-Server |
| <b> Replikerar data | När standby har skapats, fångas det upp med den primära. |
| <b> Felfri | Replikeringen är i stabilt tillstånd och felfri. |
| <b> Växlar över | Databas servern håller på att växla över till vänte läge. |
| <b> Tar bort vänte läge | Processen att ta bort standby-servern. | 
| <b> Inte aktiverat | Zon redundant hög tillgänglighet har inte Aktiver ATS.  |

## <a name="steady-state-operations"></a>Stabila tillstånds åtgärder

PostgreSQL klient program är anslutna till den primära servern med hjälp av DB-servernamnet. Program läsningar betjänas direkt från den primära servern, medan incheckningar och skrivningar bekräftas till programmet endast efter att data har sparats på både den primära servern och i standby-repliken. På grund av detta ytterligare krav för tur och retur kan program förväntas förhöjd svars tid för skrivningar och incheckningar. Du kan övervaka hälso tillståndet för hög tillgänglighet på portalen.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="redundant hög tillgänglighet för zonens redundant status"::: 

1. Klienterna ansluter till den flexibla servern och utför Skriv åtgärder.
2. Ändringar replikeras till vänte läges platsen.
3. Bekräftelse av primär mottagning.
4. Skrivningar/incheckningar bekräftas.

## <a name="failover-process---planned-downtimes"></a>Redundansväxling – planerade stillestånds tider

Planerade stillestånds händelser inkluderar Azure schemalagda regelbundna program uppdateringar och uppgraderingar av lägre versioner. När de konfigureras i hög tillgänglighet tillämpas de här åtgärderna först på vänte läges repliken medan programmen fortsätter att komma åt den primära servern. När standby-repliken har uppdaterats töms primära server anslutningar och en redundansväxling utlöses som aktiverar den primära repliken med samma databas server namn. Klient program måste återansluta till samma databas server namn till den nya primära servern och kan återuppta sina åtgärder. En ny standby-server kommer att upprättas i samma zon som den gamla primära. 

För andra åtgärder som initieras av användaren, till exempel Scale-Compute eller Scale-Storage, tillämpas ändringarna först i standby, följt av den primära. För närvarande växlar inte anslutningarna över till vänte läge och kan därför medföra avbrott när åtgärden utförs på den primära servern.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Minska planerat avbrott med hanterade underhålls fönster

 Du kan schemalägga initierade underhålls aktiviteter i Azure genom att välja en 30-minuters period i en dag med din inställning där aktiviteterna i databaserna förväntas vara låga. Azures underhålls uppgifter, till exempel korrigeringar eller lägre versions uppgraderingar, sker under det fönstret.  För flexibla servrar som kon figurer ATS med hög tillgänglighet utförs dessa underhålls aktiviteter på den förväntande repliken först och sedan aktive ras den. Program återansluter till den nya primära servern och återupptar deras åtgärder medan ett nytt vänte läge har allokerats.

## <a name="failover-process---unplanned-downtimes"></a>Redundansväxling – oplanerade stillestånd

Oplanerade avbrott innefattar program varu fel eller infrastruktur komponent fel påverkar databasens tillgänglighet. Om servern inte är tillgänglig upptäcks av övervaknings systemet, så är replikeringen till standby-repliken mycket stor och standby-repliken är aktive rad som den primära databas servern. Klienter kan återansluta till databas servern med samma anslutnings sträng och återuppta sina åtgärder. Den totala redundansväxlingen förväntas ta 60-120S. Beroende på aktiviteten på den primära databas servern vid tidpunkten för redundansväxlingen, till exempel stora transaktioner och återställnings tid, kan redundansväxlingen ta längre tid.

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="zonens redundant hög tillgänglighet – redundans"::: 

1. Den primära databas servern är avstängd och klienterna förlorar databas anslutningen. 
2. Standby-servern aktive ras för att bli den nya primära servern. Klienten ansluter till den nya primära servern med samma anslutnings sträng. Att ha klient programmet i samma zon som den primära databas servern minskar svars tiden och förbättrar prestandan.
3. Standby-servern upprättas i samma zon som den gamla primära servern och den strömmande replikeringen initieras. 
4. När replikeringen med stabilt tillstånd har upprättats, bekräftas klient programmet och skrivningar efter att data har sparats på båda platserna.

## <a name="point-in-time-restore"></a>Återställning från tidpunkt 

Flexibla servrar som är konfigurerade med hög tillgänglighet replikerar data i real tid till standby-servern för att hålla sig uppdaterade. Alla användar fel på den primära servern, till exempel en oavsiktlig minskning av en tabell eller felaktiga data uppdateringar, replikeras till standby-repliken. Därför kan du inte använda vänte läge för att återställa från sådana logiska fel. Om du vill återställa från sådana fel måste du utföra återställning vid en viss tidpunkt från säkerhets kopieringar.  Med hjälp av en flexibel servers funktion för återställning av tidpunkt kan du återställa till tiden innan felet inträffade. För databaser som kon figurer ATS med hög tillgänglighet återställs en ny databas server som en enda zon flexibel server med ett namn som användaren anger. Sedan kan du exportera objektet från databas servern och importera det till produktions databas servern. Om du vill klona din databas server för testnings-och utvecklings ändamål, eller om du vill återställa i något annat syfte, kan du utföra återställningar på plats-i-tid.

## <a name="zone-redundant-high-availability---features"></a>Zon redundant hög tillgänglighet – funktioner

-   Standby-repliken distribueras i en exakt VM-konfiguration på samma sätt som den primära servern, inklusive virtuella kärnor, lagring, nätverks inställningar (VNET, brand vägg) osv.

-   Möjlighet att lägga till hög tillgänglighet för en befintlig databas server.

-   Möjlighet att ta bort standby-replikering genom att inaktivera hög tillgänglighet.

-   Möjlighet att välja din tillgänglighets zon för den primära databas servern.

-   Möjlighet att stoppa, starta och starta om både primära och vänte databas servrar.

-   Automatiska säkerhets kopieringar utförs från den primära databas servern och lagras i en redundant zon lagring.

-   Klienterna ansluter alltid till den primära databas servern.

-   Möjlighet att starta om servern för att hämta eventuella ändringar av statiska Server parametrar.
  
-   Periodiska underhålls aktiviteter, till exempel smärre versions uppgraderingar sker först i standby och tjänsten växlar över till att minska stillestånds tiden.  

## <a name="zone-redundant-high-availability---limitations"></a>Zon redundant hög tillgänglighet – begränsningar

-   Hög tillgänglighet stöds inte med burstbar beräkningsnivå.
-   Hög tillgänglighet stöds endast i regioner där flera zoner är tillgängliga.
-   På grund av synkron replikering till en annan tillgänglighets zon kan program uppleva förhöjda Skriv-och bekräftelse svar.

-   Det går inte att använda standby-repliker för Läs frågor.

-   Beroende på arbets belastningen och aktiviteten på den primära servern kan redundansväxlingen ta längre tid än 120 sekunder.

-   Att starta om den primära databas servern startar även om replikeringen i vänte läge. 

-   Det finns inte stöd för att konfigurera ytterligare Läs repliker.

-   Konfigurering av kundens initierade hanterings uppgifter kan inte schemaläggas under hanterat underhålls fönster.

-   Planerade händelser som att skala beräknings- och skalningslagring sker först på standby-repliken och sedan på den primära servern. Servern har inte redundansväxlats för dessa planerade åtgärder. 

-  Om logisk avkodning eller logisk replikering har kon figurer ATS med en HA kon figurer ATS med hög tillgänglighet, i händelse av en redundansväxling till standby-servern, kopieras inte de logiska replikerings platserna över till standby-servern.  

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om [verksamhets kontinuitet](./concepts-business-continuity.md)
-   Lär dig hur du [hanterar hög tillgänglighet](./how-to-manage-high-availability-portal.md)
-   Läs mer om [säkerhets kopiering och återställning](./concepts-backup-restore.md)