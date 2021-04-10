---
title: Automatiska redundansgrupper
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Med grupper för automatisk redundans kan du hantera replikering och automatisk/koordinera redundans för en grupp databaser på en server eller alla databaser i en hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/26/2021
ms.openlocfilehash: 4d497adf5229819527608157a7a840d514f4292c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732354"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Använd grupper för automatisk redundans för att aktivera transparent och samordnad redundansväxling av flera databaser
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Med funktionen för automatisk redundans grupp kan du hantera replikering och redundans för en grupp databaser på en server eller alla databaser i en hanterad instans till en annan region. Det är en deklarativ abstraktion ovanpå den befintliga funktionen [aktiv geo-replikering](active-geo-replication-overview.md) som är utformad för att förenkla distribution och hantering av geo-replikerade databaser i stor skala. Du kan initiera redundans manuellt eller så kan du delegera den till Azure-tjänsten baserat på en användardefinierad princip. Med det senare alternativet kan du automatiskt återställa flera relaterade databaser i en sekundär region efter ett oåterkalleligt fel eller annan oplanerad händelse som resulterar i fullständig eller partiell förlust av den SQL Database eller SQL-hanterade instans tillgänglighet i den primära regionen. En failover-grupp kan innehålla en eller flera databaser, som vanligt vis används av samma program. Dessutom kan du använda de läsbara sekundära databaserna för att avlasta skrivskyddade arbets belastningar. Eftersom grupper med automatisk redundans omfattar flera databaser, måste dessa databaser konfigureras på den primära servern. Grupper för automatisk redundans stöder replikering av alla databaser i gruppen till endast en sekundär server eller instans i en annan region.

> [!NOTE]
> Om du vill ha flera Azure SQL Database sekundära i samma eller olika regioner använder du [aktiv geo-replikering](active-geo-replication-overview.md).

När du använder grupper för automatisk redundans med automatisk redundansväxling, resulterar alla avbrott som påverkar en eller flera av databaserna i gruppen i automatisk redundans. Detta är vanligt vis incidenter som inte kan begränsas av de inbyggda automatiska hög tillgänglighets åtgärderna. Exempel på utlösare för växling vid fel är en incident som orsakas av en SQL Database klient ringen eller kontroll ringen som är avstängd på grund av en minnes läcka för operativ system på flera datornoder, eller en incident som orsakats av en eller flera klient signaler som är nere, eftersom en felaktig nätverks kabel togs bort under rutinen för maskin varu avställningen.  Mer information finns i [SQL Database hög tillgänglighet](high-availability-sla.md).

Dessutom tillhandahåller grupper för automatisk redundans skrivskyddade och skrivskyddade lyssnar slut punkter som förblir oförändrade under redundansväxling. Oavsett om du använder manuell eller automatisk redundans växlar redundans alla sekundära databaser i gruppen till primär. När databasen har redundans har slutförts uppdateras DNS-posten automatiskt för att omdirigera slut punkterna till den nya regionen. För specifika återställnings-och RTO-data, se [Översikt över affärs kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).

När du använder grupper för automatisk redundans med automatisk redundansväxling, resulterar alla avbrott som påverkar databaser på en server eller hanterad instans i automatisk redundans. Du kan hantera gruppen för automatisk redundans med:

- [Azure-portalen](geo-distributed-application-configure-tutorial.md)
- [Azure CLI: redundans grupp](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: redundans grupp](scripts/add-database-to-failover-group-powershell.md)
- [REST API: redundans grupp](/rest/api/sql/failovergroups)

Efter redundansväxlingen kontrollerar du autentiseringskrav för din databas och Server, eller så har instansen kon figurer ATS på den nya primära servern. Mer information finns i [SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md).

För att uppnå verklig affärs kontinuitet är det bara en del av lösningen att lägga till databas redundans mellan data Center. Att återställa ett program (tjänst) från slut punkt till slut punkt efter ett oåterkalleligt haveri kräver återställning av alla komponenter som utgör tjänsten och eventuella beroende tjänster. Exempel på dessa komponenter är klient program varan (till exempel en webbläsare med ett anpassat java script), webb server delar, lagring och DNS. Det är viktigt att alla komponenter är elastiska för samma problem och blir tillgängliga inom RTO (Recovery Time mål) för ditt program. Därför måste du identifiera alla beroende tjänster och förstå de garantier och funktioner som de tillhandahåller. Sedan måste du vidta lämpliga åtgärder för att säkerställa att tjänsten fungerar under redundansväxlingen av de tjänster som den är beroende av. Mer information om hur du skapar lösningar för haveri beredskap finns i [utforma moln lösningar för haveri beredskap med hjälp av aktiv geo-replikering](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="terminology-and-capabilities"></a>Terminologi och funktioner

- **Failover-grupp (dim)**

  En redundanskonfiguration är en namngiven grupp databaser som hanteras av en enda server eller inom en hanterad instans som kan redundansväxla som en enhet till en annan region, om alla eller vissa primära databaser blir otillgängliga på grund av ett avbrott i den primära regionen. När den skapas för SQL-hanterad instans innehåller en failover-grupp alla användar databaser i instansen och därför kan bara en failover-grupp konfigureras på en instans.
  
  > [!IMPORTANT]
  > Namnet på gruppen för redundans måste vara globalt unikt inom `.database.windows.net` domänen.

- **Servrar**

     Med-servrar kan vissa eller alla användar databaser på en server placeras i en grupp för växling vid fel. Dessutom stöder en server flera grupper för växling vid fel på en enskild server.

- **Primär kontakt**

  Servern eller den hanterade instans som är värd för de primära databaserna i gruppen för växling vid fel.

- **Sekundär**

  Servern eller den hanterade instans som är värd för de sekundära databaserna i gruppen för växling vid fel. Den sekundära kan inte finnas i samma region som den primära.

- **Lägga till enskilda databaser i redundans gruppen**

  Du kan skicka flera databaser på samma server till samma grupp för redundans. Om du lägger till en enda databas i gruppen redundans skapas automatiskt en sekundär databas med samma version och beräknings storlek på den sekundära servern.  Du har angett att servern när gruppen för växling vid fel skapades. Om du lägger till en databas som redan har en sekundär databas på den sekundära servern ärvs den geo-replikeringslänken av gruppen. När du lägger till en databas som redan har en sekundär databas i en server som inte är en del av gruppen redundans, skapas en ny sekundär på den sekundära servern.

  > [!IMPORTANT]
  > Kontrol lera att den sekundära servern inte har en databas med samma namn, om det inte är en befintlig sekundär databas. I grupper för växling vid fel för SQL-hanterad instans replikeras alla användar databaser. Du kan inte välja en delmängd av användar databaser för replikering i gruppen redundans.

- **Lägga till databaser i elastisk pool i redundans gruppen**

  Du kan publicera alla eller flera databaser i en elastisk pool i samma grupp för redundans. Om den primära databasen finns i en elastisk pool skapas den sekundära automatiskt i den elastiska poolen med samma namn (sekundär pool). Du måste se till att den sekundära servern innehåller en elastisk pool med samma exakta namn och tillräckligt med ledigt utrymme som värd för de sekundära databaserna som ska skapas av gruppen för växling vid fel. Om du lägger till en databas i poolen som redan har en sekundär databas i den sekundära poolen ärvs den geo-replikeringslänken av gruppen. När du lägger till en databas som redan har en sekundär databas i en server som inte är en del av gruppen redundans, skapas en ny sekundär i den sekundära poolen.
  
- **Inledande seeding**

  När du lägger till databaser, elastiska pooler eller hanterade instanser till en failover-grupp, finns det en första initierings fas innan datareplikeringen startar. Den första initierings fasen är den längsta och dyraste åtgärden. När den första dirigeringen har slutförts, synkroniseras data och endast efterföljande data ändringar replikeras. Hur lång tid det tar att slutföra det första startvärdet beror på storleken på dina data, antalet replikerade databaser och länkens hastighet mellan entiteterna i gruppen redundans. Under normala omständigheter är möjlig seeding-hastighet upp till 500 GB per timme för SQL Database och upp till 360 GB per timme för SQL-hanterad instans. Dirigering utförs för alla databaser parallellt.

  För SQL-hanterad instans bör du ta hänsyn till hastigheten för ExpressRoute-länken mellan de två instanserna när du uppskattar tidpunkten för den inledande initierings fasen. Om hastigheten för länken mellan de två instanserna är långsammare än vad som är nödvändigt, kommer tiden till Seed förmodligen att påverkas i synnerhet. Du kan använda den angivna Dirigerings hastigheten, antalet databaser, den totala storleken på data och länk hastigheten för att uppskatta hur lång tid det första initierings steget tar innan datareplikeringen startar. För en enskild 100 GB-databas skulle till exempel den inledande fasen start ta cirka 1,2 timmar om länken kan överföra 84 GB per timme och om det inte finns några andra databaser som dirigeras. Om länken bara kan överföra 10 GB per timme tar det cirka 10 timmar att initiera en 100 GB-databas. Om det finns flera databaser att replikera, körs dirigeringen parallellt och, i kombination med en långsam länk hastighet, kan den första initierings fasen ta betydligt längre tid, särskilt om den parallella initieringen av data från alla databaser överskrider den tillgängliga länk bandbredden. Om nätverks bandbredden mellan två instanser är begränsad och du lägger till flera hanterade instanser i en failover-grupp, bör du överväga att lägga till flera hanterade instanser i gruppen redundans i turordning, en i taget. Med en korrekt storleks-SKU för gateway mellan de två hanterade instanserna och om bandbredden i företags nätverket tillåter det, är det möjligt att uppnå hastigheter på så högt som 360 GB.  

- **DNS-zon**

  Ett unikt ID som genereras automatiskt när en ny SQL-hanterad instans skapas. Ett certifikat för flera domäner (SAN) för den här instansen har allokerats för att autentisera klient anslutningarna till en instans i samma DNS-zon. De två hanterade instanserna i samma failover-grupp måste dela DNS-zonen.
  
  > [!NOTE]
  > Det krävs inget ID för DNS-zon för grupper som skapats för SQL Database.

- **Läs-och skriv lyssnare för redundans**

  En DNS CNAME-post som pekar på den aktuella primära URL: en. Den skapas automatiskt när gruppen för växling vid fel skapas och tillåter att Läs-och skriv arbets belastningen transparent återansluter till den primära databasen när den primära ändringen sker efter redundansväxlingen. När gruppen för växling vid fel skapas på en server skapas DNS CNAME-posten för lyssnar-URL: en `<fog-name>.database.windows.net` . När gruppen för växling vid fel skapas på en SQL-hanterad instans, skapas DNS CNAME-posten för lyssnar-URL: en `<fog-name>.<zone_id>.database.windows.net` .

- **Skrivskyddad lyssnare för redundans grupp**

  En DNS CNAME-post som pekar på den skrivskyddade lyssnare som pekar på den sekundära URL: en. Den skapas automatiskt när gruppen för växling vid fel skapas och tillåter skrivskyddad SQL-arbetsbelastning att transparent ansluta till den sekundära med de angivna reglerna för belastnings utjämning. När gruppen för växling vid fel skapas på en server skapas DNS CNAME-posten för lyssnar-URL: en `<fog-name>.secondary.database.windows.net` . När gruppen för växling vid fel skapas på en SQL-hanterad instans, skapas DNS CNAME-posten för lyssnar-URL: en `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Princip för automatisk redundansväxling**

  Som standard konfigureras en failover-grupp med en princip för automatisk redundansväxling. Azure utlöser redundans när ett fel har upptäckts och Grace-perioden har löpt ut. Systemet måste kontrol lera att avbrottet inte kan begränsas av den inbyggda [infrastrukturen för hög tillgänglighet](high-availability-sla.md) på grund av effektens omfattning. Om du vill kontrol lera arbets flödet för redundans från programmet eller manuellt kan du inaktivera automatisk redundans.
  
  > [!NOTE]
  > Eftersom verifieringen av skalningen av avbrottet och hur snabbt det kan lösas innebär att de mänskliga åtgärderna i drifts teamet kan utföras, kan respitperioden inte anges under en timme. Den här begränsningen gäller för alla databaser i gruppen redundans oavsett datasynkroniseringens status.

- **Princip för skrivskyddad redundans**

  Som standard är redundansväxlingen av den skrivskyddade lyssnaren inaktive rad. Det garanterar att den primära prestandan inte påverkas när den sekundära är offline. Det innebär dock också att skrivskyddade sessioner inte kan ansluta förrän den sekundära återställningen har återställts. Om du inte kan tolerera nedtid för skrivskyddade sessioner och kan använda den primära för både skrivskyddad och Läs-och Skriv trafik på kostnaderna för den potentiella prestanda försämringen av den primära, kan du aktivera redundans för den skrivskyddade lyssnaren genom att konfigurera `AllowReadOnlyFailoverToPrimary` egenskapen. I så fall omdirigeras den skrivskyddade trafiken automatiskt till den primära om den sekundära inte är tillgänglig.

  > [!NOTE]
  > `AllowReadOnlyFailoverToPrimary`Egenskapen har bara betydelse om principen för automatisk redundansväxling är aktive rad och en automatisk redundans har utlösts av Azure. I så fall, om egenskapen har angetts till true, kommer den nya primära servern att betjäna både Read-Write-och skrivskyddade sessioner.

- **Planerad redundans**

   Planerad redundans utför fullständig synkronisering mellan primära och sekundära databaser innan sekundära växlar till den primära rollen. Detta garanterar ingen data förlust. Planerad redundans används i följande scenarier:

  - Utföra haveri beredskap (DR) i produktion om data förlust inte är acceptabel
  - Flytta databaserna till en annan region
  - Returnera databaserna till den primära regionen efter att avbrottet har begränsats (failback)

- **Oplanerad redundans**

   Oplanerad eller framtvingad redundansväxling växlar omedelbart den sekundära till den primära rollen utan någon synkronisering med den primära. Den här åtgärden leder till data förlust. Oplanerad redundansväxling används som en återställnings metod under avbrott när den primära inte är tillgänglig. När den ursprungliga primära servern är online igen återansluter den automatiskt utan att synkronisera och bli en ny sekundär.

- **Manuell redundans**

  Du kan starta redundans manuellt när som helst oavsett konfigurationen för automatisk redundans. Om principen för automatisk redundansväxling inte har kon figurer ATS krävs manuell redundans för att återställa databaser i gruppen redundans till den sekundära. Du kan initiera Tvingad eller läsvänlig redundans (med fullständig datasynkronisering). Den senare kan användas för att flytta den primära till den sekundära regionen. När redundansväxlingen är klar uppdateras DNS-posterna automatiskt för att säkerställa anslutning till den nya primära.

- **Respitperiod med data förlust**

  Eftersom de primära och sekundära databaserna synkroniseras med hjälp av asynkron replikering kan redundansväxlingen leda till data förlust. Du kan anpassa principen för automatisk redundans så att den återspeglar programmets tolerans för data förlust. Genom `GracePeriodWithDataLossHours` att konfigurera kan du styra hur lång tid systemet väntar innan redundansväxlingen initieras, vilket sannolikt skulle leda till data förlust.

- **Flera failover-grupper**

  Du kan konfigurera flera grupper för växling vid fel för samma server par för att styra omfattningen av redundans. Varje grupp växlar över oberoende. Om ditt program för flera klienter använder elastiska pooler kan du använda den här funktionen för att blanda primära och sekundära databaser i varje pool. På så sätt kan du minska effekten av ett avbrott till endast hälften av klienterna.

  > [!NOTE]
  > SQL-hanterad instans har inte stöd för flera grupper för växling vid fel.
  
## <a name="permissions"></a>Behörigheter

Behörigheter för en failover-grupp hanteras via [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md). Rollen [SQL Server Contributor](../../role-based-access-control/built-in-roles.md#sql-server-contributor) har alla behörigheter som krävs för att hantera grupper med fel.

### <a name="create-failover-group"></a>Skapa redundans grupp

Om du vill skapa en grupp för växling vid fel måste du ha Azure RBAC-skrivrättigheter till både den primära och sekundära servern och till alla databaser i gruppen redundans. För en SQL-hanterad instans behöver du Azure RBAC-skrivrättigheter både för den primära och sekundära SQL-hanterade instansen, men behörigheter för enskilda databaser är inte relevanta eftersom det inte går att lägga till eller ta bort enskilda SQL-hanterade instans databaser i en grupp för växling vid fel.

### <a name="update-a-failover-group"></a>Uppdatera en failover-grupp

Om du vill uppdatera en failover-grupp behöver du Azure RBAC-skriv åtkomst till gruppen redundans och alla databaser på den aktuella primära servern eller den hanterade instansen.  

### <a name="fail-over-a-failover-group"></a>Redundansväxla en failover-grupp

Om du vill redundansväxla en failover-grupp behöver du Azure RBAC-skriv åtkomst till gruppen redundans på den nya primära servern eller den hanterade instansen.

## <a name="best-practices-for-sql-database"></a>Metod tips för SQL Database

Gruppen för automatisk redundans måste konfigureras på den primära servern och ansluta den till den sekundära servern i en annan Azure-region. Grupperna kan innehålla alla eller vissa databaser på dessa servrar. Följande diagram illustrerar en typisk konfiguration av ett Geo-redundant moln program med hjälp av flera databaser och gruppen för automatisk redundans.

![Diagrammet visar en typisk konfiguration av ett Geo-redundant moln program med hjälp av flera databaser och gruppen för automatisk redundans.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Se [Lägg till SQL Database i en failover-grupp](failover-group-add-single-database-tutorial.md) för en detaljerad steg-för-steg-guide som lägger till en databas i SQL Database till en failover-grupp.

När du utformar en tjänst med affärs kontinuitet i åtanke följer du dessa allmänna rikt linjer:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Använda en eller flera grupper för växling vid fel för att hantera redundans för flera databaser

En eller flera failover-grupper kan skapas mellan två servrar i olika regioner (primära och sekundära servrar). Varje grupp kan innehålla en eller flera databaser som återställs som en enhet om alla eller vissa primära databaser blir otillgängliga på grund av ett avbrott i den primära regionen. Gruppen redundans skapar geo-Secondary-databas med samma tjänst mål som den primära. Om du lägger till en befintlig Geo-replikeringsrelation i gruppen redundans kontrollerar du att geo-Secondary är konfigurerat med samma tjänste nivå och beräknings storlek som den primära.
  
> [!IMPORTANT]
> Det finns för närvarande inte stöd för att skapa redundansväxla grupper mellan två servrar i olika prenumerationer för Azure SQL Database. Om du flyttar den primära eller sekundära servern till en annan prenumeration när redundansväxlingen har skapats kan det leda till fel vid redundansväxlingen och andra åtgärder.

### <a name="using-read-write-listener-for-oltp-workload"></a>Använda Läs-och skriv lyssnare för OLTP-arbetsbelastning

När du utför OLTP-åtgärder ska du använda `<fog-name>.database.windows.net` som server-URL och anslutningarna dirigeras automatiskt till den primära. URL: en ändras inte efter redundansväxlingen. Observera att redundansväxlingen innebär att du uppdaterar DNS-posten så att klient anslutningarna omdirigeras till den nya primära primären först efter det att klient-DNS-cachen har uppdaterats.

### <a name="using-read-only-listener-for-read-only-workload"></a>Använda skrivskyddad lyssnare för skrivskyddad arbets belastning

Om du har en logiskt isolerad skrivskyddad arbets belastning som är tolerant till viss föråldrade data kan du använda den sekundära databasen i programmet. För skrivskyddade sessioner använder du `<fog-name>.secondary.database.windows.net` som server-URL och anslutningen dirigeras automatiskt till den sekundära. Vi rekommenderar också att du anger Läs avsikt i anslutnings strängen med hjälp av `ApplicationIntent=ReadOnly` .

> [!NOTE]
> I Premium-, Affärskritisk-och storskaliga tjänste nivåer kan SQL Database använda [skrivskyddade repliker](read-scale-out.md) för att avlasta skrivskyddade arbets belastningar med hjälp av `ApplicationIntent=ReadOnly` parametern i anslutnings strängen. När du har konfigurerat en geo-replikerad sekundär instans kan du använda den här funktionen till att ansluta till antingen en skrivskyddad replik på den primära platsen eller på den geo-replikerade platsen.
>
> - Om du vill ansluta till en skrivskyddad replik på den primära platsen använder du `ApplicationIntent=ReadOnly` och `<fog-name>.database.windows.net` .
> - Använd och för att ansluta till en skrivskyddad replik på den sekundära platsen `ApplicationIntent=ReadOnly` `<fog-name>.secondary.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Prestanda försämring förbereds

Ett typiskt Azure-program använder flera Azure-tjänster och består av flera komponenter. Den automatiserade redundansväxlingen av gruppen redundans utlöses baserat på tillstånd för enbart Azure SQL-komponenter. Andra Azure-tjänster i den primära regionen kanske inte påverkas av avbrottet och komponenterna kan fortfarande vara tillgängliga i den regionen. När de primära databaserna växlar till DR-regionen kan fördröjningen mellan beroende komponenter öka. För att undvika påverkan på högre latens för programmets prestanda, se till att alla programmets komponenter är i DR-regionen och följ dessa [rikt linjer för nätverks säkerhet](#failover-groups-and-network-security).

### <a name="preparing-for-data-loss"></a>Förbereder för data förlust

Om ett avbrott upptäcks väntar Azure på den period som du har angett `GracePeriodWithDataLossHours` . Standardvärdet är 1 timme. Om du inte kan erbjuda data förlust, se till att ange `GracePeriodWithDataLossHours` ett tillräckligt stort antal, till exempel 24 timmar. Använd manuell grupp växling vid fel för att återställa från den sekundära till den primära.

> [!IMPORTANT]
> Elastiska pooler med 800 eller färre DTU: er och fler än 250 databaser som använder geo-replikering kan stöta på problem, inklusive längre planerade redundanser och försämrade prestanda.  De här problemen är mer sannolika för Skriv intensiva arbets belastningar när geo-replikeringens slut punkter är mycket åtskilda med geografi eller när flera sekundära slut punkter används för varje databas.  Symptom på de här problemen anges när fördröjningen för geo-replikering ökar med tiden.  Den här fördröjningen kan övervakas med hjälp av [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Om dessa problem inträffar, kan du öka antalet DTU: er eller minska antalet geo-replikerade databaser i samma pool.

### <a name="changing-secondary-region-of-the-failover-group"></a>Ändra sekundär region för redundans gruppen

För att illustrera ändrings ordningen antar vi att Server A är den primära servern, att Server B är den befintliga sekundära servern och att Server C är den nya sekundära i den tredje regionen.  Följ dessa steg om du vill göra över gången:

1. Skapa ytterligare sekundär zoner för varje databas på Server A till Server C med hjälp av [aktiv geo-replikering](active-geo-replication-overview.md). Varje databas på Server A har två sekundära servrar, en på Server B och en på Server C. Detta garanterar att de primära databaserna förblir skyddade under över gången.
2. Ta bort gruppen redundans. Inloggningarna fungerar inte. Detta beror på att SQL-aliasen för lyssnarna för redundansen har tagits bort och att gatewayen inte kan identifiera namnet på redundans gruppen.
3. Återskapa redundans-gruppen med samma namn mellan servrarna A och C. Vid det här skedet slutar inloggningen att fungera.
4. Lägg till alla primära databaser på Server A i den nya gruppen för redundans.
5. Släpp Server B. Alla databaser på B tas bort automatiskt.

### <a name="changing-primary-region-of-the-failover-group"></a>Ändra primär region för redundans gruppen

För att illustrera ändrings ordningen kommer vi att anta att Server A är den primära servern, Server B är den befintliga sekundära servern och att Server C är den nya primära servern i den tredje regionen.  Följ dessa steg om du vill göra över gången:

1. Utför en planerad redundansväxling för att växla mellan den primära servern och B. Server A blir den nya sekundära servern. Redundansväxlingen kan resultera i flera minuters stillestånds tid. Den faktiska tiden beror på storleken på gruppen för växling vid fel.
2. Skapa ytterligare sekundär zoner för varje databas på Server B till Server C med hjälp av [aktiv geo-replikering](active-geo-replication-overview.md). Varje databas på Server B har två sekundära servrar, en på Server A och en på Server C. Detta garanterar att de primära databaserna förblir skyddade under över gången.
3. Ta bort gruppen redundans. Inloggningarna fungerar inte. Detta beror på att SQL-aliasen för lyssnarna för redundansen har tagits bort och att gatewayen inte kan identifiera namnet på redundans gruppen.
4. Återskapa redundans gruppen med samma namn mellan servrarna B och C. Vid det här skedet slutar inloggningen att fungera.
5. Lägg till alla primära databaser på B i den nya gruppen för redundans.
6. Utför en planerad redundansväxling av redundans-gruppen för att växla B och C. Nu kommer Server C att bli primär och B – den sekundära. Alla sekundära databaser på Server A kommer automatiskt att länkas till presidentval på C. Som i steg 1 kan redundansväxlingen leda till flera minuters stillestånds tid.
7. Släpp Server A. Alla databaser på en tas bort automatiskt.

> [!IMPORTANT]
> När gruppen redundans tas bort, tas även DNS-posterna för lyssnar slut punkterna bort. I det här läget är det en sannolikhet för att någon annan skapar en redundans grupp eller ett Server Ali Aset med samma namn, vilket gör att du inte kan använda den igen. Använd inte generiska grupp namn för redundans för att minimera risken.

## <a name="best-practices-for-sql-managed-instance"></a>Metod tips för SQL-hanterad instans

Den automatiska redundansgruppen måste vara konfigurerad på den primära instansen och ansluter den till den sekundära instansen i en annan Azure-region.  Alla databaser i instansen replikeras till den sekundära instansen.

Följande diagram illustrerar en typisk konfiguration av ett Geo-redundant moln program med hjälp av en hanterad instans och en grupp för automatisk redundans.

![diagram över automatisk redundans](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Mer information om hur du lägger till en SQL-hanterad instans finns i [lägga till hanterad instans i en failover-grupp](../managed-instance/failover-group-add-instance-tutorial.md) för en detaljerad steg-för-steg-guide.

Om programmet använder SQL-hanterad instans som datanivå, följer du dessa allmänna rikt linjer när du utformar för affärs kontinuitet:

### <a name="creating-the-secondary-instance"></a>Skapa den sekundära instansen

För att säkerställa icke-avbruten anslutning till den primära SQL-hanterade instansen efter redundans måste båda de primära och sekundära instanserna finnas i samma DNS-zon. Det garanterar att samma certifikat för flera domäner (SAN) kan användas för att autentisera klient anslutningarna till någon av de två instanserna i gruppen redundans. När programmet är redo för produktions distribution skapar du en sekundär SQL-hanterad instans i en annan region och kontrollerar att den delar DNS-zonen med den primära SQL-hanterade instansen. Du kan göra det genom att ange den valfria parametern när du skapar den. Om du använder PowerShell eller REST API, är namnet på den valfria parametern `DNS Zone Partner` , och namnet på motsvarande fält i Azure Portal är primär hanterad instans.

> [!IMPORTANT]
> Den första hanterade instans som skapas i under nätet bestämmer DNS-zonen för alla efterföljande instanser i samma undernät. Det innebär att två instanser från samma undernät inte kan tillhöra olika DNS-zoner.

Mer information om hur du skapar den sekundära SQL-hanterade instansen i samma DNS-zon som den primära instansen finns i [skapa en sekundär hanterad instans](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance).

### <a name="using-geo-paired-regions"></a>Använda geo-kopplade regioner

Distribuera båda hanterade instanser till [kopplade regioner](../../best-practices-availability-paired-regions.md) av prestanda skäl. Hanterade instanser i regioner med geo-par har mycket bättre prestanda jämfört med ej kopplade regioner. 

### <a name="enabling-replication-traffic-between-two-instances"></a>Aktivera replikeringstrafik mellan två instanser

Eftersom varje instans är isolerad i sitt eget VNet måste dubbelriktad trafik mellan dessa virtuella nätverk tillåtas. Se [Azure VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Skapa en redundansgrupp mellan hanterade instanser i olika prenumerationer

Du kan skapa en grupp för redundans mellan SQL-hanterade instanser i två olika prenumerationer, så länge prenumerationerna är kopplade till samma [Azure Active Directory-klient](../../active-directory/fundamentals/active-directory-whatis.md#terminology). När du använder PowerShell API kan du göra det genom att ange `PartnerSubscriptionId` parametern för den sekundära SQL-hanterade instansen. När du använder REST API kan varje instans-ID som ingår i `properties.managedInstancePairs` parametern ha sitt eget subscriptionID.
  
> [!IMPORTANT]
> Azure Portal har inte stöd för att skapa redundans grupper över olika prenumerationer. För de befintliga grupperna för växling vid fel i olika prenumerationer och/eller resurs grupper kan redundansväxlingen också inte initieras manuellt via portalen från den primära SQL-hanterade instansen. Starta från den geo-sekundära instansen i stället.

### <a name="managing-failover-to-secondary-instance"></a>Hantera redundans till sekundär instans

Redundansgruppen hanterar redundansväxlingen för alla databaser i den hanterade SQL-instansen. När du skapar en grupp geo-replikeras alla databaser i instansen automatiskt till den sekundära hanterade SQL-instansen. Du kan inte använda redundansgrupper till att initiera en delvis redundansväxling av en delmängd av databaserna.

> [!IMPORTANT]
> Om en databas tas bort från den primära SQL-hanterade instansen kommer den också att släppas automatiskt på den geo-sekundära SQL-hanterade instansen.

### <a name="using-read-write-listener-for-oltp-workload"></a>Använda Läs-och skriv lyssnare för OLTP-arbetsbelastning

När du utför OLTP-åtgärder ska du använda `<fog-name>.zone_id.database.windows.net` som server-URL och anslutningarna dirigeras automatiskt till den primära. URL: en ändras inte efter redundansväxlingen. Redundansväxlingen innebär att du uppdaterar DNS-posten, så att klient anslutningarna omdirigeras till den nya primära primären först efter det att klient-DNS-cachen har uppdaterats. Eftersom den sekundära instansen delar DNS-zonen med den primära, kommer klient programmet att kunna återansluta till den med samma SAN-certifikat.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Använda skrivskyddad lyssnare för att ansluta till den sekundära instansen

Om du har en logiskt isolerad skrivskyddad arbets belastning som är tolerant till viss föråldrade data kan du använda den sekundära databasen i programmet. Om du vill ansluta direkt till den geo-replikerade sekundära använder du `<fog-name>.secondary.<zone_id>.database.windows.net` som server-URL och anslutningen görs direkt till den geo-replikerade sekundära.

> [!NOTE]
> I Affärskritisk nivån stöder SQL-hanterad instans användning av [skrivskyddade repliker](read-scale-out.md) för att avlasta skrivskyddade arbets belastningar med hjälp av `ApplicationIntent=ReadOnly` parametern i anslutnings strängen. När du har konfigurerat en geo-replikerad sekundär instans kan du använda den här funktionen till att ansluta till antingen en skrivskyddad replik på den primära platsen eller på den geo-replikerade platsen.
>
> - Om du vill ansluta till en skrivskyddad replik på den primära platsen använder du `ApplicationIntent=ReadOnly` och `<fog-name>.<zone_id>.database.windows.net` .
> - Använd och för att ansluta till en skrivskyddad replik på den sekundära platsen `ApplicationIntent=ReadOnly` `<fog-name>.secondary.<zone_id>.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Prestanda försämring förbereds

Ett typiskt Azure-program använder flera Azure-tjänster och består av flera komponenter. Den automatiserade redundansväxlingen av gruppen redundans utlöses baserat på tillstånd för enbart Azure SQL-komponenter. Andra Azure-tjänster i den primära regionen kanske inte påverkas av avbrottet och komponenterna kan fortfarande vara tillgängliga i den regionen. När de primära databaserna växlar till den sekundära regionen kan fördröjningen mellan de beroende komponenterna öka. För att undvika effekten av högre latens för programmets prestanda, se till att alla programmets komponenter är i den sekundära regionen och att de växlar över program komponenterna tillsammans med databasen. Vid konfigurations tiden följer du [rikt linjerna för nätverks säkerhet](#failover-groups-and-network-security) för att säkerställa anslutningen till databasen i den sekundära regionen.

### <a name="preparing-for-data-loss"></a>Förbereder för data förlust

Om ett avbrott upptäcks utlöses en Read-Write-redundans om det inte finns någon data förlust till det bästa av vår kunskap. Annars uppskjuts redundansväxlingen för den period som du anger med hjälp av `GracePeriodWithDataLossHours` . Om du har angett `GracePeriodWithDataLossHours` ska du förbereda för data förlust. I allmänhet prioriterar Azure tillgänglighet under drifts störningar. Om du inte kan erbjuda data förlust, se till att ange GracePeriodWithDataLossHours till ett tillräckligt stort antal, till exempel 24 timmar eller inaktivera automatisk redundans.

DNS-uppdateringen av Läs-och skriv lyssnaren sker omedelbart efter det att redundansväxlingen initierats. Den här åtgärden kommer inte att resultera i data förlust. Processen med att växla databas roller kan dock ta upp till 5 minuter under normala förhållanden. När den är klar är vissa databaser i den nya primära instansen fortfarande skrivskyddade. Om en redundansväxling initieras med hjälp av PowerShell är åtgärden för att växla den primära replik rollen synkron. Om den initieras med hjälp av Azure Portal, visar användar gränssnittet slut för ande status. Om den är initierad med REST API använder du standard Azure Resource Managers avsöknings funktion för att övervaka slut för ande.

> [!IMPORTANT]
> Använd manuell gruppredundans för att flytta presidentval tillbaka till den ursprungliga platsen. När det avbrott som orsakade redundansväxlingen minskas kan du flytta dina primära databaser till den ursprungliga platsen. Om du vill göra det ska du initiera den manuella redundansväxlingen av gruppen.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Ändra sekundär region för redundans gruppen

Vi antar att instans A är den primära instansen, instans B är den befintliga sekundära instansen och att instans C är den nya sekundära instansen i den tredje regionen.  Följ dessa steg om du vill göra över gången:

1. Skapa instans C med samma storlek som en och i samma DNS-zon.
2. Ta bort redundans gruppen mellan instanserna A och B. Inloggningarna fungerar inte på grund av att SQL-aliasen för lyssnare för redundans har tagits bort och gatewayen inte kan identifiera namnet på redundans gruppen. De sekundära databaserna kommer att kopplas från presidentval och blir skrivskyddade databaser.
3. Skapa en grupp för växling vid fel med samma namn mellan instans A och C. Följ anvisningarna i [själv studie kursen om failover-grupp med SQL-hanterad instans](../managed-instance/failover-group-add-instance-tutorial.md). Detta är en data storleks åtgärd som slutförs när alla databaser från instans A dirigeras och synkroniseras.
4. Ta bort instans B om det inte behövs för att undvika onödiga kostnader.

> [!NOTE]
> Efter steg 2 och tills steg 3 har slutförts, förblir databaserna i instans A oskyddade från ett oåterkalleligt haveri i instans A.

### <a name="changing-primary-region-of-the-failover-group"></a>Ändra primär region för redundans gruppen

Vi antar att instans A är den primära instansen, instans B är den befintliga sekundära instansen och att instans C är den nya primära instansen i den tredje regionen.  Följ dessa steg om du vill göra över gången:

1. Skapa instans C med samma storlek som B och i samma DNS-zon.
2. Anslut till instans B och manuellt redundans om du vill växla mellan den primära instansen och B. instans A blir den nya sekundära instansen automatiskt.
3. Ta bort redundans gruppen mellan instanserna A och B. Inloggningarna fungerar inte på grund av att SQL-aliasen för lyssnare för redundans har tagits bort och gatewayen inte kan identifiera namnet på redundans gruppen. De sekundära databaserna kommer att kopplas från presidentval och blir skrivskyddade databaser.
4. Skapa en grupp för växling vid fel med samma namn mellan instans A och C. Följ anvisningarna i [självstudien för failover-gruppen med hanterade instanser](../managed-instance/failover-group-add-instance-tutorial.md). Detta är en data storleks åtgärd som slutförs när alla databaser från instans A dirigeras och synkroniseras.
5. Ta bort instans A om det inte behövs för att undvika onödiga kostnader.

> [!CAUTION]
> Efter steg 3 och till dess att steg 4 har slutförts förblir databaserna i instans A oskyddade från ett oåterkalleligt haveri i instans A.

> [!IMPORTANT]
> När gruppen redundans tas bort, tas även DNS-posterna för lyssnar slut punkterna bort. I det här läget är det en sannolikhet för att någon annan skapar en redundans grupp eller ett Server Ali Aset med samma namn, vilket gör att du inte kan använda den igen. Använd inte generiska grupp namn för redundans för att minimera risken.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Aktivera scenarier som är beroende av objekt från system databaser
System databaser replikeras inte till den sekundära instansen i en failover-grupp. Om du vill aktivera scenarier som är beroende av objekt från system databaserna på den sekundära instansen, se till att skapa samma objekt på den sekundära. Om du till exempel planerar att använda samma inloggningar på den sekundära instansen, se till att skapa dem med samma SID. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Failover-grupper och nätverks säkerhet

För vissa program kräver säkerhets reglerna att nätverks åtkomst till data nivån är begränsad till en viss komponent eller komponenter, till exempel en virtuell dator, webb tjänst osv. Detta krav medför vissa utmaningar för utformning av affärs kontinuitet och användning av failover-grupper. Överväg följande alternativ när du implementerar sådan begränsad åtkomst.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Använda failover-grupper och regler för virtuella nätverk

Om du använder [Virtual Network tjänst slut punkter och regler](vnet-service-endpoint-rule-overview.md) för att begränsa åtkomsten till databasen i SQL Database eller SQL-hanterad instans bör du vara medveten om att varje tjänst slut punkt för virtuella nätverk endast gäller en Azure-region. Slut punkten tillåter inte att andra regioner accepterar kommunikation från under nätet. Därför kan bara de klient program som distribueras i samma region ansluta till den primära databasen. Eftersom redundansväxlingen leder till att SQL Database-klientsessioner dirigeras om till en server i en annan (sekundär) region, kommer dessa sessioner att Miss kopie ras om de härstammar från en klient utanför regionen. Därför går det inte att aktivera principen för automatisk redundans om de deltagande servrarna eller instanserna ingår i Virtual Networks reglerna. Följ dessa steg för att stödja manuell redundans:

1. Etablera de redundanta kopiorna av klient dels komponenterna för ditt program (webb tjänst, virtuella datorer osv.) i den sekundära regionen
2. Konfigurera de [virtuella nätverks reglerna](vnet-service-endpoint-rule-overview.md) individuellt för primär och sekundär server
3. Aktivera [redundansväxlingen på klient sidan med en Traffic Manager-konfiguration](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Starta manuell redundansväxling när avbrottet upptäcks. Det här alternativet är optimerat för de program som kräver konsekvent svars tid mellan frontend-och data nivån och som stöder återställning när det gäller antingen klient delen, data nivån eller båda påverkas av avbrottet.

> [!NOTE]
> Om du använder en **skrivskyddad lyssnare** för att belastningsutjämna en skrivskyddad arbets belastning kontrollerar du att arbets belastningen körs i en virtuell dator eller en annan resurs i den sekundära regionen så att den kan ansluta till den sekundära databasen.

### <a name="use-failover-groups-and-firewall-rules"></a>Använd failover-grupper och brand Väggs regler

Om din affärs kontinuitets plan kräver redundans med hjälp av grupper med automatisk redundans, kan du begränsa åtkomsten till databasen i SQL Database med hjälp av traditionella brand Väggs regler. Följ dessa steg för att stödja automatisk redundans:

1. [Skapa en offentlig IP-adress](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Skapa en offentlig belastningsutjämnare](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) och tilldela den till den offentliga IP-adressen.
3. [Skapa ett virtuellt nätverk och de virtuella datorerna](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) för dina klient dels komponenter
4. [Skapa en nätverks säkerhets grupp](../../virtual-network/network-security-groups-overview.md) och konfigurera inkommande anslutningar.
5. Se till att de utgående anslutningarna är öppna för att Azure SQL Database med hjälp av SQL [-tjänst tag gen](../../virtual-network/network-security-groups-overview.md#service-tags).
6. Skapa en [SQL Database brand Väggs regel](firewall-configure.md) som tillåter inkommande trafik från den offentliga IP-adressen som du skapar i steg 1.

Mer information om hur du konfigurerar utgående åtkomst och vilken IP-adress som ska användas i brand Väggs reglerna finns i [utgående anslutningar för belastningsutjämnare](../../load-balancer/load-balancer-outbound-connections.md).

Konfigurationen ovan ser till att den automatiska redundansväxlingen inte blockerar anslutningar från klient dels komponenterna och förutsätter att programmet kan tolerera den längre fördröjningen mellan klient delen och data nivån.

> [!IMPORTANT]
> För att garantera affärs kontinuitet för regionala avbrott måste du säkerställa geografisk redundans för både klient dels komponenter och databaserna.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Aktivera geo-replikering mellan hanterade instanser och deras virtuella nätverk

När du ställer in en redundans grupp mellan primära och sekundära SQL-hanterade instanser i två olika regioner, isoleras varje instans med hjälp av ett oberoende virtuellt nätverk. För att tillåta replikeringstrafik mellan dessa virtuella nätverk, se till att följande krav uppfylls:

- De två instanserna av SQL-hanterad instans måste finnas i olika Azure-regioner.
- De två instanserna av SQL-hanterad instans måste vara samma tjänst nivå och ha samma lagrings storlek.
- Din sekundära instans av SQL-hanterad instans måste vara tom (inga användar databaser).
- De virtuella nätverk som används av instanserna av SQL-hanterad instans måste anslutas via en [VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [Express Route](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Se till att inga brandväggsregler blockerar portarna 5022 eller 11000–11999 när två virtuella nätverk ansluter via ett lokalt nätverk. Global VNet-peering stöds med den begränsning som beskrivs i Obs!

   > [!IMPORTANT]
   > [Den 9/22/2020 vi presenterade global peering för virtuella nätverk för nyligen skapade virtuella kluster](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Det innebär att global virtuell nätverks-peering stöds för SQL-hanterade instanser som skapats i tomma undernät efter meddelandets datum, även för alla efterföljande hanterade instanser som skapats i dessa undernät. För alla andra SQL-hanterade instanser är peering-stödet begränsat till nätverken i samma region på grund av [begränsningarna i den globala virtuella nätverks-peeringen](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Se även det relevanta avsnittet i artikeln [vanliga frågor och svar om Azure Virtual Networks](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) för mer information. 

- De två SQL Managed instance-virtuella nätverk kan inte ha överlappande IP-adresser.
- Du måste konfigurera dina nätverkssäkerhetsgrupper (NSG) så att portarna 5022 och intervallet 11000–12000 är öppna för inkommande och utgående anslutningar från den andra hanterade instansens undernät. Det här görs för att tillåta replikeringstrafik mellan instanserna.

   > [!IMPORTANT]
   > Felkonfigurerade NSG säkerhets regler leder till låsta databas kopierings åtgärder.

- Den sekundära SQL-hanterade instansen har kon figurer ATS med rätt DNS-zon-ID. DNS-zon är en egenskap hos en SQL-hanterad instans och det underliggande virtuella klustret, och dess ID ingår i värd namns adressen. Zon-ID: t genereras som en slumpmässig sträng när den första SQL-hanterade instansen skapas i varje VNet och samma ID tilldelas till alla andra instanser i samma undernät. DNS-zonen kan inte ändras när den har tilldelats. SQL-hanterade instanser som ingår i samma redundans grupp måste dela DNS-zonen. Du gör detta genom att skicka den primära instansens zon-ID som värdet för parametern DnsZonePartner när du skapar den sekundära instansen.

   > [!NOTE]
   > En detaljerad själv studie kurs om hur du konfigurerar redundanskluster med SQL-hanterad instans finns i [lägga till en SQL-hanterad instans i en grupp för redundans](../managed-instance/failover-group-add-instance-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uppgradera eller nedgradera en primär databas

Du kan uppgradera eller nedgradera en primär databas till en annan beräknings storlek (inom samma tjänst nivå, inte mellan Generell användning och Affärskritisk) utan att koppla från några sekundära databaser. När du uppgraderar rekommenderar vi att du uppgraderar alla sekundära databaser först och sedan uppgraderar du den primära. Vid nedgradering ändrar du ordningen: nedgradera först den primära databasen och nedgradera sedan alla sekundära databaser. När du uppgraderar eller nedgraderar databasen till en annan tjänstnivå tillämpas den här rekommendationen.

Den här sekvensen rekommenderas särskilt för att undvika problemet där den sekundära instansen med en lägre SKU blir överbelastad och måste dirigeras om under en uppgradering eller nedgradering. Du kan också undvika problemet genom att göra den primära instansen skrivskyddad, vilket dock påverkar alla arbetsbelastningar med skrivningar mot den primära instansen.

> [!NOTE]
> Om du har skapat en sekundär databas som en del av konfigurationen av redundanskonfiguration bör du inte nedgradera den sekundära databasen. Detta är för att säkerställa att data nivån har tillräckligt med kapacitet för att bearbeta din normala arbets belastning När redundansväxlingen har Aktiver ATS.

## <a name="preventing-the-loss-of-critical-data"></a>Förhindra förlust av kritiska data

På grund av den höga svars tiden för Wide Area Networks använder kontinuerlig kopiering en metod för asynkron replikering. Asynkron replikering gör att data kan gå förlorade om ett fel uppstår. Vissa program kan dock kräva ingen data förlust. För att skydda dessa viktiga uppdateringar kan en programutvecklare anropa [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) system proceduren direkt efter att transaktionen har genomförts. Anrop `sp_wait_for_database_copy_sync` blockerar anrops tråden tills den senaste genomförda transaktionen har överförts till den sekundära databasen. Det väntar dock inte på att överförda transaktioner ska spelas upp och allokeras på den sekundära. `sp_wait_for_database_copy_sync` är begränsad till en bestämd kontinuerlig kopierings länk. Alla användare med anslutnings behörighet till den primära databasen kan anropa den här proceduren.

> [!NOTE]
> `sp_wait_for_database_copy_sync` förhindrar data förlust efter redundansväxlingen, men garanterar inte fullständig synkronisering för Läs behörighet. Fördröjningen som orsakas av ett `sp_wait_for_database_copy_sync` procedur anrop kan vara betydande och beror på storleken på transaktions loggen vid tidpunkten för anropet.

## <a name="failover-groups-and-point-in-time-restore"></a>Redundansväxla grupper och återställning av tidpunkt

Information om hur du använder återställning av punkt-till-tid med failover-grupper finns i [punkt i tids återställning (PITR)](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Begränsningar för redundans grupper

Tänk på följande begränsningar:

- Det går inte att skapa redundanskluster mellan två servrar eller instanser i samma Azure-regioner.
- Det går inte att byta namn på redundans grupper. Du måste ta bort gruppen och skapa den igen med ett annat namn.
- Database Rename stöds inte för instanser i redundans gruppen. Du måste tillfälligt ta bort en växlings grupp för att kunna byta namn på en databas.
- System databaser replikeras inte till den sekundära instansen i en failover-grupp. Därför går det inte att använda scenarier som är beroende av objekt från system databaser på den sekundära instansen om inte objekten skapas manuellt på den sekundära.

## <a name="programmatically-managing-failover-groups"></a>Hantera failover-grupper program mässigt

Som tidigare nämnts kan grupper för automatisk redundans och aktiv geo-replikering också hanteras program mässigt med hjälp av Azure PowerShell och REST API. I följande tabeller beskrivs en uppsättning kommandon som är tillgängliga. Aktiv geo-replikering innehåller en uppsättning Azure Resource Manager-API: er för hantering, inklusive [Azure SQL Database REST API](/rest/api/sql/) och [Azure PowerShell-cmdletar](/powershell/azure/). Dessa API: er kräver användning av resurs grupper och stöder rollbaserad åtkomst kontroll i Azure (Azure RBAC). För ytterligare information om hur du implementerar åtkomst roller, se [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="manage-sql-database-failover"></a>Hantera SQL Database redundans

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Beskrivning |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Det här kommandot skapar en redundans grupp och registrerar den på både primära och sekundära servrar|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Tar bort en failover-grupp från servern |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar en failover-grupps konfiguration |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Ändrar konfigurationen för en failover-grupp |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Utlöser redundans för en failover-grupp till den sekundära servern |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Lägger till en eller flera databaser i en redundans grupp|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Kommando | Beskrivning |
| --- | --- |
| [AZ SQL-redundans – grupp skapa](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Det här kommandot skapar en redundans grupp och registrerar den på både primära och sekundära servrar|
| [AZ SQL-redundans – grupp Delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Tar bort en failover-grupp från servern |
| [AZ SQL-redundans – grupp show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Hämtar en konfiguration för redundans grupp |
| [AZ SQL-redundans – grupp uppdatering](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Ändrar en failover-grupps konfiguration och/eller lägger till en eller flera databaser i en failover-grupp|
| [AZ SQL-redundans – grupp uppsättning – primär](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Utlöser redundans för en failover-grupp till den sekundära servern |

# <a name="rest-api"></a>[REST-API](#tab/rest-api)

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera redundans grupp](/rest/api/sql/failovergroups/createorupdate) | Skapar eller uppdaterar en failover-grupp |
| [Ta bort redundans grupp](/rest/api/sql/failovergroups/delete) | Tar bort en failover-grupp från servern |
| [Redundansväxling (planerad)](/rest/api/sql/failovergroups/failover) | Utlöser redundans från den aktuella primära servern till den sekundära servern med fullständig datasynkronisering.|
| [Framtvinga redundans Tillåt data förlust](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Utlöser redundans från den aktuella primära servern till den sekundära servern utan att synkronisera data. Den här åtgärden kan leda till data förlust. |
| [Hämta redundans grupp](/rest/api/sql/failovergroups/get) | Hämtar en failover-grupps konfiguration. |
| [Visa lista över grupper efter fel per server](/rest/api/sql/failovergroups/listbyserver) | Visar en lista över failover-grupper på en server. |
| [Uppdatera redundans grupp](/rest/api/sql/failovergroups/update) | Uppdaterar konfigurationen för en redundanskonfiguration. |

---

### <a name="manage-sql-managed-instance-failover"></a>Hantera redundans för SQL-hanterad instans


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Beskrivning |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Det här kommandot skapar en redundans grupp och registrerar den på både primära och sekundära instanser|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Ändrar konfigurationen för en failover-grupp|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Hämtar en failover-grupps konfiguration|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Utlöser redundans för en redundansväxling till den sekundära instansen|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Tar bort en failover-grupp|


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Kommando | Beskrivning |
| --- | --- |
| [AZ SQL-redundans – grupp skapa](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Det här kommandot skapar en redundans grupp och registrerar den på både primära och sekundära servrar|
| [AZ SQL-redundans – grupp Delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Tar bort en failover-grupp från servern |
| [AZ SQL-redundans – grupp show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Hämtar en konfiguration för redundans grupp |
| [AZ SQL-redundans – grupp uppdatering](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Ändrar en failover-grupps konfiguration och/eller lägger till en eller flera databaser i en failover-grupp|
| [AZ SQL-redundans – grupp uppsättning – primär](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Utlöser redundans för en failover-grupp till den sekundära servern |

# <a name="rest-api"></a>[REST-API](#tab/rest-api)

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera redundans grupp](/rest/api/sql/instancefailovergroups/createorupdate) | Skapar eller uppdaterar en konfiguration för redundans gruppen |
| [Ta bort redundans grupp](/rest/api/sql/instancefailovergroups/delete) | Tar bort en redundans-grupp från instansen |
| [Redundansväxling (planerad)](/rest/api/sql/instancefailovergroups/failover) | Utlöser redundans från den aktuella primära instansen till den här instansen med fullständig datasynkronisering. |
| [Framtvinga redundans Tillåt data förlust](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Utlöser redundans från den aktuella primära instansen till den sekundära instansen utan att synkronisera data. Den här åtgärden kan leda till data förlust. |
| [Hämta redundans grupp](/rest/api/sql/instancefailovergroups/get) | hämtar en failover-grupps konfiguration. |
| [Visa lista över redundanskluster – lista efter plats](/rest/api/sql/instancefailovergroups/listbylocation) | Visar en lista över failover-grupper på en plats. |

---

## <a name="next-steps"></a>Nästa steg

- Detaljerade självstudier finns i
  - [Lägga till SQL Database i en failover-grupp](failover-group-add-single-database-tutorial.md)
  - [Lägga till en elastisk pool till en redundansgrupp](failover-group-add-elastic-pool-tutorial.md)
  - [Lägga till en SQL-hanterad instans i en failover-grupp](../managed-instance/failover-group-add-instance-tutorial.md)
- Exempel skript finns i:
  - [Använd PowerShell för att konfigurera aktiv geo-replikering för Azure SQL Database](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Använda PowerShell för att konfigurera aktiv geo-replikering för en pooldatabas i Azure SQL Database](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Använda PowerShell för att lägga till en Azure SQL Database i en failover-grupp](scripts/add-database-to-failover-group-powershell.md)
- För en översikt över verksamhets kontinuitet och scenarier, se [Översikt över verksamhets kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Mer information om hur du Azure SQL Database automatiserade säkerhets kopieringar finns [SQL Database automatiska säkerhets kopieringar](automated-backups-overview.md).
- Information om hur du använder automatiska säkerhets kopieringar för återställning finns i [återställa en databas från de säkerhets kopior som initieras av tjänsten](recovery-using-backups.md).
- Information om autentiseringskrav för en ny primär server och databas finns i [SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md).
