---
title: Moln affärs kontinuitet – databas återställning
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig hur Azure SQL Database-och SQL-hanterad instans stöder moln kontinuitet för företag och databas återställning och hjälper till att hålla verksamhets kritiska moln program igång.
keywords: affärskontinuitet, molnaffärskontinuitet, databashaveriberedskap, databasåterställning
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 7bd991bd709bb4be69325afe967d7e5600a9e1a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222572"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Översikt över affärskontinuitet med Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

**Verksamhets kontinuitet** i Azure SQL Database-och SQL-hanterad instans syftar på mekanismer, principer och procedurer som gör det möjligt för din verksamhet att fortsätta att arbeta i störnings miljön, särskilt för dess dator infrastruktur. I de flesta fall kan SQL Database och SQL-hanterad instans hantera störande händelser som kan uppstå i moln miljön och se till att dina program och affärs processer körs. Det finns dock vissa störande händelser som inte kan hanteras av SQL Database automatiskt, till exempel:

- Användaren råkade ta bort eller uppdaterade en rad i en tabell.
- Skadlig angripare lyckades ta bort data eller släpper en databas.
- Jord bävning orsakade ett strömavbrott och tillfälligt inaktiverat Data Center.

Den här översikten beskriver de funktioner som SQL Database och SQL-hanterad instans tillhandahåller för verksamhets kontinuitet och haveri beredskap. Lär dig mer om alternativ, rekommendationer och självstudier för att komma åt från störande händelser som kan leda till data förlust eller orsaka att databasen och programmet blir otillgängligt. Lär dig vad du ska göra när ett användar-eller program fel påverkar data integriteten, att en Azure-region har ett avbrott eller att programmet kräver underhåll.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>SQL Database-funktioner som du kan använda för att upprätthålla affärskontinuitet

Från ett databas perspektiv finns fyra stora möjliga störningar:

- Lokala maskin-eller program varu problem påverkar noden, till exempel disk enhets haverier.
- Datafel eller borttagning av data orsakas vanligt vis av ett program fel eller ett mänskligt fel. Sådana problem är programspecifika och kan normalt inte identifieras av databas tjänsten.
- Data Center avbrott, eventuellt orsakade av en naturlig katastrof. Det här scenariot kräver en viss nivå av GEO-redundans med programredundans till ett alternativt Data Center.
- Uppgraderings-eller underhålls fel, oväntade problem som inträffar under underhåll eller uppgraderingar av planerad infrastruktur kan kräva snabb återställning till tidigare databas tillstånd.

För att minimera de lokala maskin-och program varu felen innehåller SQL Database en [arkitektur med hög tillgänglighet](high-availability-sla.md)som garanterar automatisk återställning från dessa problem med upp till 99,995% tillgänglighets-SLA.  

För att skydda ditt företag från data förlust, SQL Database och SQL-hanterad instans automatiskt skapa fullständiga säkerhets kopieringar varje vecka, differentiella databas säkerhets kopior var 12: e timme och säkerhets kopior av transaktions loggar var 5-10 minut. Säkerhets kopiorna lagras i RA-GRS-lagring under minst 7 dagar för alla tjänst nivåer. Alla tjänst nivåer utom Basic Support konfigurerbar kvarhållning av säkerhets kopior för återställning till tidpunkt, upp till 35 dagar.

SQL Database och SQL-hanterad instans tillhandahåller också flera funktioner för affärs kontinuitet som du kan använda för att minimera olika oplanerade scenarier.

- Med [temporära tabeller](../temporal-tables.md) kan du återställa radversioner från valfri tidpunkt.
- [Inbyggda automatiserade säkerhets kopieringar](automated-backups-overview.md) och [tidpunkts återställning](recovery-using-backups.md#point-in-time-restore) gör att du kan återställa slutförda databaser till en viss tidpunkt inom den konfigurerade kvarhållningsperioden upp till 35 dagar.
- Du kan [återställa en borttagen databas](recovery-using-backups.md#deleted-database-restore) till den tidpunkt då den togs bort om **servern inte har tagits bort**.
- [Långsiktig kvarhållning av säkerhets kopior](long-term-retention-overview.md) gör att du kan behålla säkerhets kopiorna upp till 10 år. Detta är en begränsad offentlig för hands version för SQL-hanterad instans
- [Aktiv geo-replikering](active-geo-replication-overview.md) gör att du kan skapa läsbara repliker och manuellt redundansväxla till en replik i händelse av ett Data Center avbrott eller program uppgradering.
- [Gruppen automatisk redundans](auto-failover-group-overview.md#terminology-and-capabilities) gör att programmet kan återställas automatiskt i händelse av ett Data Center avbrott.

## <a name="recover-a-database-within-the-same-azure-region"></a>Återställ en databas inom samma Azure-region

Du kan använda automatiska databas säkerhets kopieringar för att återställa en databas till en tidpunkt tidigare. På så sätt kan du återställa från skadade data som orsakas av mänskliga fel. Med den dagliga återställningen kan du skapa en ny databas på samma server som representerar data statusen innan händelsen skadas. För de flesta databaser tar återställnings åtgärderna i mindre än 12 timmar. Det kan ta längre tid att återställa en mycket stor eller mycket aktiv databas. Mer information om återställnings tid finns i [databasens återställnings tid](recovery-using-backups.md#recovery-time).

Om den högsta kvarhållningsperioden för kvarhållning av säkerhets kopior för PITR (Point-in-Time Restore) inte räcker för ditt program kan du utöka det genom att konfigurera en princip för långsiktig kvarhållning (brv) för databaserna. Mer information finns i [långsiktig kvarhållning av säkerhets kopior](long-term-retention-overview.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Jämför geo-replikering med failover-grupper

[Grupper för automatisk redundans](auto-failover-group-overview.md#terminology-and-capabilities) fören klar distributionen och användningen av [geo-replikering](active-geo-replication-overview.md) och Lägg till ytterligare funktioner enligt beskrivningen i följande tabell:

|                                              | Geo-replikering | Redundansgrupper  |
|:---------------------------------------------| :-------------- | :----------------|
| **Automatisk redundans**                          |     Inga          |      Ja         |
| **Redundansväxla flera databaser samtidigt**  |     Inga          |      Ja         |
| **Användaren måste uppdatera anslutningssträngen efter redundansväxlingen**      |     Ja         |      Inga          |
| **Stöd för SQL Managed Instance**                   |     Inga          |      Ja         |
| **Kan vara i samma region som den primära**             |     Ja         |      Inga          |
| **Flera repliker**                            |     Ja         |      Inga          |
| **Stöd för lässkalning**                          |     Ja         |      Ja         |


## <a name="recover-a-database-to-the-existing-server"></a>Återställ en databas till den befintliga servern

Även om det är sällsynt kan ett Azure-datacenter ha ett avbrott. När ett avbrott uppstår orsakar det ett verksamhetsavbrott som kan vara några få minuter eller flera timmar.

- Ett alternativ är att vänta tills din databas kommer tillbaka online när data centretnas avbrott är över. Det här fungerar för program som har råd att ha databasen offline. Till exempel ett utvecklingsprojekt eller en kostnadsfri utvärderingsversion som du inte behöver arbeta med hela tiden. När ett Data Center har ett avbrott vet du inte hur lång tid det tar för avbrottet, så det här alternativet fungerar bara om du inte behöver databasen en stund.
- Ett annat alternativ är att återställa en databas på en server i valfri Azure-region med hjälp av Geo [-redundanta databas säkerhets kopieringar](recovery-using-backups.md#geo-restore) (geo-återställning). Geo-återställning använder en Geo-redundant säkerhets kopia som källa och kan användas för att återställa en databas även om databasen eller data centret inte går att komma åt på grund av ett avbrott.
- Slutligen kan du snabbt återställa efter ett avbrott om du har konfigurerat en geo-Secondary med hjälp av [aktiv geo-replikering](active-geo-replication-overview.md) eller en [grupp för automatisk redundans](auto-failover-group-overview.md) för databasen eller databaserna. Beroende på ditt val av dessa tekniker kan du antingen använda manuell eller automatisk redundans. Även om det bara tar några sekunder att redundansväxla, tar det minst 1 timme för tjänsten att aktivera den. Detta är nödvändigt för att säkerställa att redundansväxlingen är motiverat av avbrottets skala. Dessutom kan redundansväxlingen leda till små data förluster på grund av den asynkrona replikeringens karaktär.

När du utvecklar din affärskontinuitetsplan är det viktigt att du känner till den längsta godkända tiden innan programmet är helt återställt efter en avbrottshändelse. Tiden som krävs för att programmet ska återställas fullständigt kallas för återställnings tids mål (RTO). Du måste också förstå den maximala tiden för de senaste data uppdateringarna (tidsintervall) som programmet kan tolerera vid återställning från en oplanerad störnings händelse. Den potentiella data förlusten kallas för återställnings punkt mål (återställnings punkt mål).

Olika återställnings metoder erbjuder olika nivåer av återställnings-och RTO. Du kan välja en specifik återställnings metod eller använda en kombination av metoder för att uppnå fullständig program återställning. I följande tabell jämförs återställnings-och RTO för varje återställnings alternativ. Grupper för automatisk redundans fören klar distributionen och användningen av geo-replikering och lägger till ytterligare funktioner enligt beskrivningen i följande tabell.

| Återställnings metod | RTO | RPO |
| --- | --- | --- |
| Geo-återställning från geo-replikerade säkerhets kopieringar | 12 h | 1 h |
| Automatiska redundansgrupper | 1 h | 5 s |
| Manuell databas växling vid fel | 30 s | 5 s |

> [!NOTE]
> *Manuell databas växling* syftar på redundansväxling av en enkel databas till dess geo-replikerade sekundära med det [oplanerade läget](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities).
Se tabellen tidigare i den här artikeln för information om RTO för automatisk redundans och återställnings punkt.

Använd grupper för automatisk redundans om ditt program uppfyller något av följande kriterier:

- Är verksamhetskritiskt.
- Har ett service nivå avtal (SLA) som inte tillåter 12 timmar eller mer av drift stopp.
- Stillestånds tiden kan resultera i ekonomiska skulder.
- Har en hög frekvens av data ändringar och 1 timme data förlust är inte acceptabel.
- Den extra kostnaden för aktiv geo-replikering är lägre än de potentiella ekonomiska skyldigheterna och den associerade affärsförlusten.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Du kan välja att använda en kombination av databas säkerhets kopior och aktiv geo-replikering beroende på dina program krav. En beskrivning av design överväganden för fristående databaser och elastiska pooler med hjälp av dessa funktioner för affärs kontinuitet finns i [utforma ett program för haveri beredskap i molnet](designing-cloud-solutions-for-disaster-recovery.md) och [elastiska pooler](disaster-recovery-strategies-for-applications-with-elastic-pool.md).

I följande avsnitt får du en översikt över stegen för att återställa med hjälp av databas säkerhets kopior eller aktiv geo-replikering. Detaljerade anvisningar, till exempel planerings krav, efter återställnings steg och information om hur du simulerar ett avbrott för att utföra en haveri beredskaps granskning finns i [återställa en databas i SQL Database från ett avbrott](disaster-recovery-guidance.md).

### <a name="prepare-for-an-outage"></a>Förbereda för ett avbrott

Oavsett vilken funktion för affärskontinuitet du använder, måste du:

- Identifiera och Förbered mål servern, inklusive regler för IP-brandvägg på server nivå, inloggningar och behörigheter på huvud databas nivå.
- Bestämma hur du ska omdirigera klienter och klientprogram till den nya servern.
- Dokumentera andra beroenden, till exempel granskningsinställningar och aviseringar.

Om du inte förbereder det korrekt, tar du med dina program online efter en redundansväxling eller om databas återställningen tar extra tid och sannolikt också behöver fel sökning vid en tidpunkt då belastningen är felaktig.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Redundansväxla till en geo-replikerad sekundär databas

Om du använder aktiv geo-replikering eller grupper för automatisk redundans som återställnings metod kan du konfigurera en princip för automatisk redundansväxling eller använda [manuell oplanerad redundansväxling](active-geo-replication-configure-portal.md#initiate-a-failover). Efter initieringen gör redundansväxlingen den sekundära att bli den nya primära och redo att registrera nya transaktioner och svara på frågor – med minimal data förlust för data som ännu inte har repliker ATS. Information om hur du utformar redundansväxlingen finns i [utforma ett program för haveri beredskap för molnet](designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> När data centret är online igen återansluter det gamla presidentval automatiskt till den nya primära och blir sekundära databaser. Om du behöver flytta tillbaka den primära tillbaka till den ursprungliga regionen kan du starta en planerad redundansväxling manuellt (failback).

### <a name="perform-a-geo-restore"></a>Utföra en geo-återställning

Om du använder automatiserade säkerhets kopieringar med Geo-redundant lagring (aktiverat som standard) kan du återställa databasen med [geo-återställning](disaster-recovery-guidance.md#recover-using-geo-restore). Återställningen sker vanligt vis inom 12 timmar, med data förlust på upp till en timme, baserat på när den senaste logg säkerhets kopieringen gjordes och replikerades. Databasen kan inte registrera några transaktioner eller svara på frågor förrän återställningen har slutförts. Observera att geo-återställning bara återställer databasen till senaste tillgängliga tidpunkt.

> [!NOTE]
> Om data centret är online igen innan du växlar programmet över till den återställda databasen kan du avbryta återställningen.

### <a name="perform-post-failover--recovery-tasks"></a>Utföra åtgärder efter en redundansväxling eller återställning

Efter återställningen från endera återställningsmetod måste du utföra följande ytterligare uppgifter innan dina användare och program kan komma igång igen:

- Omdirigera klienter och klient program till den nya servern och den återställda databasen.
- Se till att det finns tillräckligt med regler för IP-brandvägg på server nivå för att användarna ska kunna ansluta eller använda [brand väggar på databas nivå](firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) för att aktivera lämpliga regler.
- Se till att lämpliga inloggningar och behörigheter på huvud databas nivå är på plats (eller Använd [inneslutna användare](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).
- Konfigurera granskningen efter behov.
- Konfigurera aviseringar efter behov.

> [!NOTE]
> Om du använder en failover-grupp och ansluter till databaserna med Läs-och skriv lyssnare sker omdirigeringen efter redundansväxlingen automatiskt och transparent till programmet.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Uppgradera ett program med minimal avbrottstid

Ibland måste ett program tas offline på grund av planerat underhåll, till exempel en program uppgradering. [Hantera program uppgraderingar](manage-application-rolling-upgrade.md) beskriver hur du använder aktiv geo-replikering för att aktivera rullande uppgraderingar av ditt moln program för att minimera drift stopp vid uppgraderingar och ange en återställnings väg om något går fel.

## <a name="next-steps"></a>Nästa steg

En beskrivning av program design överväganden för enskilda databaser och för elastiska pooler finns i [utforma ett program för haveri beredskap för moln haveri beredskap](designing-cloud-solutions-for-disaster-recovery.md) och [elastiska pooler](disaster-recovery-strategies-for-applications-with-elastic-pool.md).
