---
title: Aktiv geo-replikering
description: Använd aktiv geo-replikering för att skapa läsbara sekundära databaser för enskilda databaser i Azure SQL Database i samma eller olika data Center regioner.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 08/27/2020
ms.openlocfilehash: 3a678f6280b5f2d0fd372e75bfbeb6eb2e9b1577
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634302"
---
# <a name="creating-and-using-active-geo-replication---azure-sql-database"></a>Skapa och använda aktiv geo-replikering – Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Aktiv geo-replikering är en Azure SQL Database funktion som gör att du kan skapa läsbara sekundära databaser för enskilda databaser på en server i samma eller ett annat data Center (region).

> [!NOTE]
> Aktiv geo-replikering stöds inte av Azure SQL-hanterad instans. Använd [grupper för automatisk redundans](auto-failover-group-overview.md)för geografisk redundans för instanser av SQL-hanterad instans.

> [!NOTE]
> Information om hur du migrerar SQL-databaser från Azure Germany med hjälp av aktiv geo-replikering finns i [migrera SQL Database med hjälp av aktiv geo-replikering](../../germany/germany-migration-databases.md#migrate-sql-database-using-active-geo-replication).

Aktiv geo-replikering är utformad som en verksamhets kontinuitets lösning som gör att programmet kan utföra snabb haveri beredskap för enskilda databaser i händelse av en regional katastrof eller ett storskaligt avbrott. Om geo-replikering är aktiverat kan programmet initiera redundansväxling till en sekundär databas i en annan Azure-region. Upp till fyra sekundära servrar stöds i samma eller olika regioner, och sekundärerna kan också användas för skrivskyddade åtkomst frågor. Redundansväxlingen måste initieras manuellt av programmet eller användaren. Efter redundansväxlingen har den nya primära servern en annan slut punkt för anslutningen.

> [!NOTE]
> Aktiv geo-replikering replikerar ändringar med transaktions loggen för strömnings databasen. Den är inte [relaterad till Transaktionsreplikering](/sql/relational-databases/replication/transactional/transactional-replication), som replikerar ändringar genom att köra DML-kommandon (Insert-, Update-, Delete).

Följande diagram illustrerar en typisk konfiguration av ett Geo-redundant moln program som använder aktiv geo-replikering.

![aktiv geo-replikering](./media/active-geo-replication-overview/geo-replication.png )

> [!IMPORTANT]
> SQL Database stöder även grupper för automatisk redundans. Mer information finns i använda [grupper för automatisk redundans](auto-failover-group-overview.md).

Om den primära databasen Miss lyckas eller om du behöver bli offline kan du initiera redundansväxlingen till någon av dina sekundära databaser. När redundans har Aktiver ATS till en av de sekundära databaserna, länkas alla andra sekundära sekundära automatiskt till den nya primära.

Du kan hantera replikering och redundans för en enskild databas eller en uppsättning databaser på en server eller i en elastisk pool med hjälp av aktiv geo-replikering. Du kan göra det med:

- [Azure Portal](active-geo-replication-configure-portal.md)
- [PowerShell: enkel databas](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell: elastisk pool](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL: enkel databas eller elastisk pool](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: enkel databas](/rest/api/sql/replicationlinks)

Aktiv geo-replikering utnyttjar [Always on-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) i databas motorn för asynkron replikering av genomförda transaktioner på den primära databasen till en sekundär databas med ögonblicks bild isolering. Grupper för automatisk redundans ger gruppsemantiken ovanpå aktiv geo-replikering men samma mekanism för asynkron replikering används. När som helst kan den sekundära databasen vara något bakom den primära databasen, men sekundära data är garanterat att aldrig ha partiella transaktioner. Redundans i flera regioner gör det möjligt för program att snabbt återställa från en permanent förlust av ett helt data Center eller delar av ett Data Center som orsakas av natur katastrofer, katastrof fel eller skadliga åtgärder. Specifika återställnings data finns i [Översikt över affärs kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).

> [!NOTE]
> Om det uppstår ett nätverks fel mellan två regioner försöker vi igen var 10: e sekund för att återupprätta anslutningarna.

> [!IMPORTANT]
> För att garantera att en kritisk ändring av den primära databasen replikeras till en sekundär innan du redundansväxlas, kan du framtvinga synkronisering för att säkerställa replikering av kritiska ändringar (till exempel lösen ords uppdateringar). Tvingad synkronisering påverkar prestanda eftersom den blockerar anrops tråden tills alla allokerade transaktioner har repliker ATS. Mer information finns i [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Information om hur du övervakar replikeringsfördröjning mellan den primära databasen och geo-Secondary finns [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Följande bild visar ett exempel på aktiv geo-replikering som kon figurer ATS med en primär i regionen Norra centrala USA och sekundär i regionen Södra centrala USA.

![relation mellan geo-replikering](./media/active-geo-replication-overview/geo-replication-relationship.png)

Eftersom de sekundära databaserna är läsbara kan de användas för att avlasta skrivskyddade arbets belastningar som rapporterings jobb. Om du använder aktiv geo-replikering är det möjligt att skapa den sekundära databasen i samma region med den primära, men det ökar inte programmets återhämtnings förmåga till oåterkalleliga problem. Om du använder grupper för automatisk redundans skapas alltid den sekundära databasen i en annan region.

Förutom haveri beredskap aktiv geo-replikering kan användas i följande scenarier:

- **Databas migrering**: du kan använda aktiv geo-replikering för att migrera en databas från en server till en annan online med minsta stillestånds tid.
- **Program uppgraderingar**: du kan skapa en extra sekundär kopia som en återställning efter en återställning under program uppgraderingar.

För att uppnå verklig affärs kontinuitet är det bara en del av lösningen att lägga till databas redundans mellan data Center. Att återställa ett program (tjänst) från slut punkt till slut punkt efter ett oåterkalleligt haveri kräver återställning av alla komponenter som utgör tjänsten och eventuella beroende tjänster. Exempel på dessa komponenter är klient program varan (till exempel en webbläsare med ett anpassat java script), webb server delar, lagring och DNS. Det är viktigt att alla komponenter är elastiska för samma problem och blir tillgängliga inom RTO (Recovery Time mål) för ditt program. Därför måste du identifiera alla beroende tjänster och förstå de garantier och funktioner som de tillhandahåller. Sedan måste du vidta lämpliga åtgärder för att säkerställa att tjänsten fungerar under redundansväxlingen av de tjänster som den är beroende av. Mer information om hur du skapar lösningar för haveri beredskap finns i [utforma moln lösningar för haveri beredskap med hjälp av aktiv geo-replikering](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Terminologi och funktioner för aktiv geo-replikering

- **Automatisk asynkron replikering**

  Du kan bara skapa en sekundär databas genom att lägga till den i en befintlig databas. Den sekundära kan skapas på valfri server. När den väl har skapats fylls den sekundära databasen med data som kopieras från den primära databasen. Den här processen kallas för dirigering. När den sekundära databasen har skapats och dirigerats, replikeras uppdateringar till den primära databasen asynkront till den sekundära databasen automatiskt. Asynkron replikering innebär att transaktioner allokeras till den primära databasen innan de replikeras till den sekundära databasen.

- **Läsbara sekundära databaser**

  Ett program kan komma åt en sekundär databas för skrivskyddade åtgärder med samma eller olika säkerhets objekt som används för att komma åt den primära databasen. De sekundära databaserna används i isolerings läget för ögonblicks bilder för att säkerställa replikeringen av uppdateringar av den primära (logga repetition) inte fördröjs av frågor som körs på den sekundära.

> [!NOTE]
> Logg omuppspelningen fördröjs på den sekundära databasen om det finns schema uppdateringar på den primära. Den senare kräver ett schema lås på den sekundära databasen.

> [!IMPORTANT]
> Du kan använda geo-replikering för att skapa en sekundär databas i samma region som den primära. Du kan använda den här sekundära för att belastningsutjämna en skrivskyddad arbets belastning i samma region. En sekundär databas i samma region ger dock inte ytterligare fel återhämtning och är därför inte ett lämpligt failover-mål för haveri beredskap. Det kommer inte heller att garantera isolering av tillgänglighets zoner. Använd tjänst nivån verksamhets kritisk eller Premium med [zon redundant konfiguration](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) eller generell användning Service Tier [Zone-redundant konfiguration](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) för att uppnå isolering av tillgänglighets zoner.
>

- **Planerad redundans**

  Planerad redundans växlar roller för primära och sekundära databaser när den fullständiga synkroniseringen har slutförts. Det är en online-åtgärd som inte leder till data förlust. Tiden för åtgärden beror på storleken på transaktions loggen på den primära som måste synkroniseras. Planerad redundans är utformat för följande scenarier: (a) för att utföra DR-övningar i produktion när data förlust inte är acceptabel. (b) för att flytta databasen till en annan region. och (c) för att returnera databasen till den primära regionen efter att avbrottet har begränsats (failback).

- **Oplanerad redundans**

  Oplanerad eller framtvingad redundansväxling växlar omedelbart den sekundära till den primära rollen utan någon synkronisering med den primära. Alla transaktioner som allokeras till den primära men inte replikeras till den sekundära kommer att gå förlorade. Den här åtgärden är utformad som en återställnings metod under avbrott när den primära inte är tillgänglig, men databasens tillgänglighet måste återställas snabbt. När den ursprungliga primära servern är online igen, kommer den automatiskt att återanslutas och bli en ny sekundär. Alla osynkroniserade transaktioner innan redundansväxlingen bevaras i säkerhets kopian, men kommer inte att synkroniseras med den nya primära för att undvika konflikter. Dessa transaktioner måste slås samman manuellt med den senaste versionen av den primära databasen.

- **Flera läsbara sekundär servrar**

  Upp till 4 sekundära databaser kan skapas för varje primär. Om det bara finns en sekundär databas och den Miss lyckas, exponeras programmet för högre risk tills en ny sekundär databas skapas. Om det finns flera sekundära databaser förblir programmet skyddat även om någon av de sekundära databaserna Miss lyckas. Ytterligare sekundära servrar kan också användas för att skala ut de skrivskyddade arbets belastningarna

  > [!NOTE]
  > Om du använder aktiv geo-replikering för att skapa ett globalt distribuerat program och behöver tillhandahålla skrivskyddad åtkomst till data i mer än fyra regioner kan du skapa sekundärt av en sekundär (en process som kallas länkning). På så sätt kan du uppnå praktiskt taget obegränsad skalning av databasreplikering. Dessutom minskar länkningen av replikeringen från den primära databasen. Kompromissen är den ökade replikeringen för de sekundära databaserna på lövnivå.

- **Geo-replikering av databaser i en elastisk pool**

  Varje sekundär databas kan separat delta i en elastisk pool eller inte vara i någon elastisk pool alls. Valet av pool för varje sekundär databas är inte beroende av konfigurationen av någon annan sekundär databas (oavsett om den är primär eller sekundär). Varje elastisk pool finns i en enda region, och därför kan flera sekundära databaser i samma topologi aldrig dela en elastisk pool.

- **Användarspecifik redundans och återställning efter fel**

  En sekundär databas kan uttryckligen växlas till den primära rollen när som helst av programmet eller användaren. Under ett verkligt avbrott bör alternativet "oplanerat" användas, vilket omedelbart befordrar en sekundär till primär. När de misslyckade primära återställningarna och är tillgängliga igen, markerar systemet automatiskt återställd primär som sekundär och tar den uppdaterad med den nya primära. På grund av den asynkrona typen av replikering kan en liten mängd data gå förlorade under oplanerade växlingar om en primär Miss lyckas innan den replikerar de senaste ändringarna i den sekundära. När en primär med flera sekundära repliker växlar över konfigurerar systemet automatiskt om replikeringsrelationen och länkar de återstående sekundärerna till den nyligen framhävda primären utan att användaren behöver vidta några åtgärder. När det avbrott som orsakade redundansväxlingen har begränsats kan det vara önskvärt att returnera programmet till den primära regionen. För att göra det, ska redundans-kommandot anropas med alternativet "planerat".

## <a name="preparing-secondary-database-for-failover"></a>Förbereder sekundär databas för redundans

För att säkerställa att ditt program omedelbart kan komma åt den nya primära efter redundansväxlingen, kontrollerar du att autentiseringskrav för den sekundära servern och databasen är korrekt konfigurerade. Mer information finns i [SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md). För att garantera efterlevnad efter redundansväxlingen, se till att säkerhets kopieringens bevarande princip på den sekundära databasen matchar den primära. De här inställningarna är inte en del av databasen och replikeras inte. Som standard kommer den sekundära att konfigureras med en standard period för PITR på sju dagar. Mer information finns i [SQL Database automatiska säkerhets kopieringar](automated-backups-overview.md).

> [!IMPORTANT]
> Om databasen är medlem i en failover-grupp kan du inte initiera dess redundans med kommandot geo-replikering redundans. Använd kommandot redundans för gruppen. Om du behöver redundansväxla en enskild databas måste du först ta bort den från gruppen för redundans. Mer information finns i  [failover-grupper](auto-failover-group-overview.md) .

## <a name="configuring-secondary-database"></a>Konfigurerar sekundär databas

Både primära och sekundära databaser måste ha samma tjänst nivå. Vi rekommenderar också starkt att den sekundära databasen skapas med samma redundans för säkerhets kopiering och beräknings storlek (DTU: er eller virtuella kärnor) som primär. Om den primära databasen har en hög Skriv arbets belastning kanske en sekundär med lägre beräknings storlek inte kan hålla sig med den. Det gör att du kan göra en fördröjning på den sekundära och eventuellt inte tillgänglig för den sekundära. För att minimera dessa risker begränsar Active geo-replikering den primära transaktions loggen om det behövs för att tillåta att dess sekundära kan fångas upp.

En annan följd av en obalanserad sekundär konfiguration är att efter redundansväxlingen kan program prestanda försämras på grund av otillräcklig beräknings kapacitet för den nya primära. I så fall är det nödvändigt att skala databas tjänst målet till den nödvändiga nivån, vilket kan ta lång tid och beräkna resurser och kräver en redundansväxling med [hög tillgänglighet](high-availability-sla.md) i slutet av processen.

Om du väljer att skapa den sekundära med lägre beräknings storlek, är loggen i/o procent i Azure Portal ett bra sätt att uppskatta den minimala beräknings storleken för den sekundära som krävs för att upprätthålla belastningen på replikeringen. Om din primära databas till exempel är P6 (1000 DTU) och dess logg skrivnings procent är 50%, måste den sekundära P4 vara minst (500 DTU). Om du vill hämta tidigare logg-IO-data använder du vyn [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) . Använd [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vyn om du vill hämta de senaste logg skrivnings data med högre granularitet som bättre visar kortsiktiga toppar i logg takt.

Begränsning av transaktions logg frekvensen på den primära på grund av lägre beräknings storlek på en sekundär rapporteras med hjälp av HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO vänte typ, synlig i [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) och [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) databasvyer.

Som standard är den sekundära lagringen för säkerhets kopieringen densamma som den primära databasen. Du kan välja att konfigurera den sekundära med en annan redundans för lagring av säkerhets kopior. Säkerhets kopieringar görs alltid på den primära databasen. Om den sekundära har kon figurer ATS med en annan redundans för säkerhets kopiering, efter redundansväxlingen när den sekundära platsen höjs till den primära, faktureras säkerhets kopior enligt den lagrings redundans som valts på den nya primära (tidigare sekundära). 

> [!NOTE]
> Transaktions logg frekvensen på den primära kan begränsas av orsaker som inte är relaterade till lägre beräknings storlek på en sekundär. Den här typen av begränsning kan inträffa även om den sekundära har samma eller högre beräknings storlek än den primära. Mer information, inklusive vänte typer för olika typer av logg frekvenss begränsning, finns i [transaktions logg frekvens styrning](resource-limits-logical-server.md#transaction-log-rate-governance).

> [!NOTE]
> Azure SQL Database konfigurerbar redundans för säkerhets kopiering är för närvarande tillgängligt i offentlig för hands version i södra Brasilien och är allmänt tillgängligt i Sydostasien Azure-region. När käll databasen skapas med lokalt redundant eller zon-redundant lagring redundans, stöds inte att skapa en sekundär databas i en annan Azure-region. 

Mer information om SQL Database beräknings storlekarna finns i [SQL Database Service nivåer](purchasing-models.md).

## <a name="cross-subscription-geo-replication"></a>Geo-replikering mellan prenumerationer

Om du vill konfigurera aktiv geo-replikering mellan två databaser som tillhör olika prenumerationer (oavsett om de finns under samma klient organisation eller inte) måste du följa den särskilda proceduren som beskrivs i det här avsnittet.  Proceduren baseras på SQL-kommandon och kräver:

- Skapa en privilegie rad inloggning på båda servrarna
- Lägga till IP-adressen i listan över tillåtna klienter för klienten som utför ändringen på båda servrarna (till exempel IP-adressen för värden som kör SQL Server Management Studio).

Klienten som utför ändringarna behöver nätverks åtkomst till den primära servern. Även om samma IP-adress för klienten måste läggas till i listan över tillåtna på den sekundära servern, krävs ingen nätverks anslutning till den sekundära servern.

### <a name="on-the-master-of-the-primary-server"></a>På den primära serverns huvud server

1. Lägg till IP-adressen i listan över tillåtna klienter för klienten som utför ändringarna (mer information finns i [Konfigurera brand vägg](firewall-configure.md)).
1. Skapa en inloggning som är dedikerad för att konfigurera aktiv geo-replikering (och justera autentiseringsuppgifterna efter behov):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Skapa en motsvarande användare och tilldela den till DBManager-rollen:

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. Anteckna SID för den nya inloggningen med hjälp av den här frågan:

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>På käll databasen på den primära servern

1. Skapa en användare för samma inloggning:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Lägg till användaren i rollen db_owner:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>På den sekundära serverns huvud server

1. Lägg till klientens IP-adress i listan över tillåtna under brand Väggs regler för den sekundära servern. Kontrol lera att exakt samma klient-IP-adress som har lagts till på den primära servern också har lagts till i den sekundära. Detta är ett obligatoriskt steg att utföra innan du kör kommandot ALTER DATABASE ADD SECONDARY för att initiera geo-replikering.

1. Skapa samma inloggning som på den primära servern med samma lösen ord för användar namn och SID:

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Skapa en motsvarande användare och tilldela den till DBManager-rollen:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>På den primära serverns huvud server

1. Logga in på den primära serverns huvud server med den nya inloggningen.
1. Skapa en sekundär replik av käll databasen på den sekundära servern (ändra databas namn och servername efter behov):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Efter den första installationen kan användare, inloggningar och brand Väggs regler som skapats tas bort.

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Bevara autentiseringsuppgifter och brand Väggs regler i synkronisering

Vi rekommenderar att du använder [IP brand Väggs regler på databas nivå](firewall-configure.md) för geo-replikerade databaser så att dessa regler kan replikeras med databasen för att säkerställa att alla sekundära databaser har samma IP-brandvägg som primär. Den här metoden eliminerar behovet av att kunderna manuellt ska konfigurera och underhålla brand Väggs regler på servrar som är värdar för både de primära och sekundära databaserna. Om du använder [inneslutna databas användare](logins-create-manage.md) för data åtkomst säkerställer både primära och sekundära databaser alltid samma autentiseringsuppgifter för användaren, vilket innebär att det inte finns några avbrott på grund av fel matchningar med inloggningar och lösen ord. Med att lägga till [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)kan kunderna hantera användar åtkomst till både primära och sekundära databaser och eliminera behovet av att hantera autentiseringsuppgifter i databaserna helt och hållet.

## <a name="upgrading-or-downgrading-primary-database"></a>Uppgradera eller nedgradera primär databas

Du kan uppgradera eller nedgradera en primär databas till en annan beräknings storlek (inom samma tjänst nivå, inte mellan Generell användning och Affärskritisk) utan att koppla från några sekundära databaser. Vid uppgraderingen rekommenderar vi att du uppgraderar den sekundära databasen först och sedan uppgraderar den primära. När du nedgraderar ska du använda omvänd ordning: nedgradera den primära databasen först och sedan den sekundära. När du uppgraderar eller nedgraderar databasen till en annan tjänstnivå tillämpas den här rekommendationen.

> [!NOTE]
> Om du har skapat en sekundär databas i konfigurationen av redundansgruppen bör du inte nedgradera den sekundära databasen. Detta är för att säkerställa att data nivån har tillräckligt med kapacitet för att bearbeta din normala arbets belastning När redundansväxlingen har Aktiver ATS.

> [!IMPORTANT]
> Den primära databasen i en redundansgrupp kan inte skalas om till en högre nivå om inte den sekundära databasen först skalas om till den högre nivån. Om du försöker skala den primära databasen innan den sekundära databasen skalas, kan följande fel meddelande visas:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Förhindra förlust av kritiska data

På grund av den höga svars tiden för Wide Area Networks använder kontinuerlig kopiering en metod för asynkron replikering. Asynkron replikering gör att data kan gå förlorade om ett fel uppstår. Vissa program kan dock kräva ingen data förlust. För att skydda dessa viktiga uppdateringar kan en programutvecklare anropa [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) system proceduren direkt efter att transaktionen har genomförts. Om du anropar **sp_wait_for_database_copy_sync** blockeras anrops tråden tills den senaste genomförda transaktionen har överförts till den sekundära databasen. Det väntar dock inte på att överförda transaktioner ska spelas upp och allokeras på den sekundära. **sp_wait_for_database_copy_sync** är begränsad till en bestämd kontinuerlig kopierings länk. Alla användare med anslutnings behörighet till den primära databasen kan anropa den här proceduren.

> [!NOTE]
> **sp_wait_for_database_copy_sync** förhindrar data förlust efter redundansväxlingen, men garanterar inte fullständig synkronisering för Läs behörighet. Fördröjningen som orsakas av ett **sp_wait_for_database_copy_sync** procedur anrop kan vara betydande och beror på storleken på transaktions loggen vid tidpunkten för anropet.

## <a name="monitoring-geo-replication-lag"></a>Övervakning av geo-replikering – fördröjning

Om du vill övervaka fördröjningen med avseende på återställnings punkt använder du *replication_lag_sec* kolumn för [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) på den primära databasen. Det visar fördröjning i sekunder mellan de transaktioner som har allokerats på den primära och behålls på den sekundära. T.ex. Om värdet för fördröjningen är 1 sekund, innebär det att om den primära påverkas av ett avbrott vid detta tillfälle och redundansväxlingen initieras, kommer 1 sekund av de senaste över gångarna inte att sparas.

För att mäta en fördröjning med avseende på ändringar på den primära databasen som har tillämpats på den sekundära, som är tillgänglig att läsa från den sekundära, jämför *last_commit* tid på den sekundära databasen med samma värde i den primära databasen.

> [!NOTE]
> Ibland har *replication_lag_sec* på den primära databasen ett null-värde, vilket innebär att den primära inte för närvarande vet hur långt den sekundära är.   Detta inträffar vanligt vis när processen har startats om och bör vara ett tillfälligt tillstånd. Överväg att avisera programmet om *replication_lag_sec* returnerar null under en längre tids period. Det indikerar att den sekundära databasen inte kan kommunicera med den primära databasen på grund av ett permanent anslutnings problem. Det finns också villkor som kan orsaka skillnaden mellan *last_commit* tid på den sekundära och den primära databasen som blir stor. T.ex. om ett genomförande görs på den primära efter en lång period utan ändringar, kommer skillnaden att gå upp till ett stort värde innan den snabbt returneras till 0. Betrakta det som ett fel tillstånd när skillnaden mellan dessa två värden förblir stor under en längre tid.

## <a name="programmatically-managing-active-geo-replication"></a>Hantera aktiv geo-replikering program mässigt

Som tidigare nämnts kan aktiv geo-replikering även hanteras via programmering med hjälp av Azure PowerShell och REST API. I följande tabeller beskrivs en uppsättning kommandon som är tillgängliga. Aktiv geo-replikering innehåller en uppsättning Azure Resource Manager-API: er för hantering, inklusive [Azure SQL Database REST API](/rest/api/sql/) och [Azure PowerShell-cmdletar](/powershell/azure/). Dessa API: er kräver användning av resurs grupper och stöder rollbaserad åtkomst kontroll i Azure (Azure RBAC). För ytterligare information om hur du implementerar åtkomst roller, se [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: hantera redundans för enkla databaser och databaser i pooler

> [!IMPORTANT]
> De här Transact-SQL-kommandona gäller endast för aktiv geo-replikering och gäller inte för failover-grupper. De gäller inte heller för instanser av SQL-hanterad instans, eftersom de bara stöder Redundansrelationer.

| Kommando | Beskrivning |
| --- | --- |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Använd Lägg till SEKUNDÄRt på SERVER argument för att skapa en sekundär databas för en befintlig databas och starta datareplikering |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Använd REDUNDANS eller FORCE_FAILOVER_ALLOW_DATA_LOSS för att växla en sekundär databas till att vara primär för att initiera redundans |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Använd ta bort sekundär på servern om du vill avsluta en datareplikering mellan en SQL Database och den angivna sekundära databasen. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Returnerar information om alla befintliga länkar för replikering för varje databas på en server. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Hämtar den senaste replikerings tiden, den senaste replikeringsfördröjning och annan information om replikeringslänken för en specifik databas. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Visar status för alla databas åtgärder inklusive status för replikeringslänken. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |gör att programmet väntar tills alla genomförda transaktioner replikeras och bekräftas av den aktiva sekundära databasen. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: hantera redundans för enkla databaser och databaser i pooler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

| Cmdlet | Beskrivning |
| --- | --- |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Hämtar en eller flera databaser. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary) |Skapar en sekundär databas för en befintlig databas och startar datareplikeringen. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary) |Växlar en sekundär databas till att vara primär för att initiera redundans. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) |Avslutar datareplikering mellan en SQL Database och den angivna sekundära databasen. |
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Hämtar Länkar för geo-replikering mellan en Azure SQL Database och en resurs grupp eller logisk SQL-Server. |
|  | |

> [!IMPORTANT]
> Exempel skript finns i [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/setup-geodr-and-failover-database-powershell.md) och [Konfigurera och redundansväxla en poolad databas med aktiv geo-replikering](scripts/setup-geodr-and-failover-elastic-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: hantera redundans för enkla databaser och databaser i pooler

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera databas (createMode = Restore)](/rest/api/sql/databases/createorupdate) |Skapar, uppdaterar eller återställer en primär eller sekundär databas. |
| [Hämta databas status för att skapa eller uppdatera](/rest/api/sql/databases/createorupdate) |Returnerar status under en åtgärd för att skapa. |
| [Ange sekundär databas som primär (planerad redundansväxling)](/rest/api/sql/replicationlinks/failover) |Anger vilken sekundär databas som är primär genom att redundansväxla från den aktuella primära databasen. **Det här alternativet stöds inte för SQL-hanterad instans.**|
| [Ange sekundär databas som primär (oplanerad redundansväxling)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Anger vilken sekundär databas som är primär genom att redundansväxla från den aktuella primära databasen. Den här åtgärden kan leda till data förlust. **Det här alternativet stöds inte för SQL-hanterad instans.**|
| [Hämta replikeringslänk](/rest/api/sql/replicationlinks/get) |Hämtar en specifik replikeringslänk för en angiven databas i en partnerskap för geo-replikering. Den hämtar informationen som visas i vyn sys.geo_replication_links katalog. **Det här alternativet stöds inte för SQL-hanterad instans.**|
| [Länkar för replikering – lista efter databas](/rest/api/sql/replicationlinks/listbydatabase) | Hämtar alla länkar för replikering för en specifik databas i en partnerskap med geo-replikering. Den hämtar informationen som visas i vyn sys.geo_replication_links katalog. |
| [Ta bort replikeringslänk](/rest/api/sql/replicationlinks/delete) | Tar bort en länk till databasreplikering. Kan inte utföras under redundansväxling. |
|  | |




## <a name="next-steps"></a>Nästa steg

- Exempel skript finns i:
  - [Konfigurera och redundansväxla en enskild databas med aktiv geo-replikering](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Konfigurera och redundansväxla en pooldatabas med aktiv geo-replikering](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- SQL Database stöder även grupper för automatisk redundans. Mer information finns i använda [grupper för automatisk redundans](auto-failover-group-overview.md).
- För en översikt över verksamhets kontinuitet och scenarier, se [Översikt över verksamhets kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Mer information om hur du Azure SQL Database automatiserade säkerhets kopieringar finns [SQL Database automatiska säkerhets kopieringar](automated-backups-overview.md).
- Information om hur du använder automatiska säkerhets kopieringar för återställning finns i [återställa en databas från de säkerhets kopior som initieras av tjänsten](recovery-using-backups.md).
- Information om autentiseringskrav för en ny primär server och databas finns i [SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md).
