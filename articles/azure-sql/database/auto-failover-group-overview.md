---
title: Automatiska redundansgrupper
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Med automatiska redundansgrupper kan du hantera replikering och automatisk/samordnad redundans för en grupp databaser på en server eller alla databaser i en hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/26/2021
ms.openlocfilehash: c0149dbb5f17af87229d951cf744c285e54835af
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375958"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Använd automatiska redundansgrupper för att aktivera transparent och samordnad redundans för flera databaser
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Med funktionen automatiska redundansgrupper kan du hantera replikering och redundans för en grupp databaser på en server eller alla databaser i en hanterad instans till en annan region. Det är en deklarativ abstraktion ovanpå den befintliga aktiva [funktionen för geo-replikering](active-geo-replication-overview.md) som utformats för att förenkla distribution och hantering av geo-replikerade databaser i stor skala. Du kan initiera redundans manuellt eller delegera den till Azure-tjänsten baserat på en användardefinierad princip. Med det senare alternativet kan du automatiskt återställa flera relaterade databaser i en sekundär region efter ett oåterkalleligt fel eller andra oplanerade händelser som resulterar i fullständig eller partiell förlust av tillgängligheten för SQL Database eller SQL Managed Instance i den primära regionen. En redundansgrupp kan innehålla en eller flera databaser, som vanligtvis används av samma program. Dessutom kan du använda de läsbara sekundära databaserna för att avlasta skrivskyddade frågearbetsbelastningar. Eftersom automatiska redundansgrupper omfattar flera databaser måste dessa databaser konfigureras på den primära servern. Automatiska redundansgrupper stöder replikering av alla databaser i gruppen till endast en sekundär server eller instans i en annan region.

> [!NOTE]
> Om du vill ha Azure SQL Database andra repliker i samma eller olika regioner använder du [aktiv geo-replikering](active-geo-replication-overview.md).

Om du använder automatiska redundansgrupper med en princip för automatisk redundans, resulterar eventuella avbrott som påverkar en eller flera av databaserna i gruppen i automatisk redundans. Det här är vanligtvis incidenter som inte kan åtgärdas av de inbyggda automatiska åtgärderna för hög tillgänglighet. Exemplen på redundansutlösare är en incident som orsakas av att en SQL Database-klientorganisationsring eller en kontrollring är ur drift på grund av en minnesläcka i operativsystemet på flera beräkningsnoder, eller en incident som orsakats av att en eller flera klientringar låg nere på grund av att en felaktig nätverkskabel avbröts under den rutinmässiga inaktiveringen av maskinvaran.  Mer information finns i [SQL Database hög tillgänglighet.](high-availability-sla.md)

Dessutom tillhandahåller automatiska redundansgrupper skrivskyddade och skrivskyddade lyssnarslutpunkter som förblir oförändrade under redundans. Oavsett om du använder manuell eller automatisk redundansaktivering växlar redundansväxlar alla sekundära databaser i gruppen till den primära. När databas redundansen är klar uppdateras DNS-posten automatiskt för att omdirigera slutpunkterna till den nya regionen. Specifika RPO- och RTO-data finns i [Översikt över affärskontinuui.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

När du använder automatiska redundansgrupper med automatisk redundansprincip, resulterar eventuella avbrott som påverkar databaser på en server eller hanterad instans i automatisk redundans. Du kan hantera automatisk redundansgrupp med hjälp av:

- [Azure-portalen](geo-distributed-application-configure-tutorial.md)
- [Azure CLI: Redundansgrupp](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: Redundansgrupp](scripts/add-database-to-failover-group-powershell.md)
- [REST API: Redundansgrupp](/rest/api/sql/failovergroups)

Efter redundansen ser du till att autentiseringskraven för din databas och server, eller instansen, är konfigurerade på den nya primära instansen. Mer information finns i [SQL Database säkerhet efter haveriberedskap.](active-geo-replication-security-configure.md)

För att uppnå verklig verksamhetskontinui är det bara en del av lösningen att lägga till databasredundans mellan datacenter. Återställning av ett program (tjänst) från slutet till slut efter ett oåterkalleligt fel kräver återställning av alla komponenter som utgör tjänsten och eventuella beroende tjänster. Exempel på dessa komponenter är klientprogramvaran (till exempel en webbläsare med ett anpassat JavaScript), klientdelar för webben, lagring och DNS. Det är viktigt att alla komponenter är motståndskraftiga mot samma fel och blir tillgängliga inom mål för återställningstid (RTO) för ditt program. Därför måste du identifiera alla beroende tjänster och förstå de garantier och funktioner som de tillhandahåller. Sedan måste du vidta lämpliga åtgärder för att säkerställa att tjänsten fungerar under redundansen av de tjänster som den är beroende av. Mer information om hur du utformar lösningar för haveriberedskap finns i Designing Cloud Solutions for Disaster Recovery Using active geo-replication (Utforma molnlösningar för haveriberedskap [med aktiv geo-replikering).](designing-cloud-solutions-for-disaster-recovery.md)

## <a name="terminology-and-capabilities"></a>Terminologi och funktioner

- **Redundansgrupp (DIMM)**

  En redundansgrupp är en namngiven grupp med databaser som hanteras av en enskild server eller inom en hanterad instans som kan redundans som en enhet till en annan region om alla eller vissa primära databaser blir otillgängliga på grund av ett avbrott i den primära regionen. När den skapas för SQL Managed Instance innehåller en redundansgrupp alla användardatabaser i instansen och därför kan endast en redundansgrupp konfigureras på en instans.
  
  > [!IMPORTANT]
  > Namnet på redundansgruppen måste vara globalt unikt inom `.database.windows.net` domänen.

- **Servrar**

     Med servrar kan vissa eller alla användardatabaser på en server placeras i en redundansgrupp. Dessutom har en server stöd för flera redundansgrupper på en enda server.

- **Primär kontakt**

  Den server eller hanterade instans som är värd för de primära databaserna i redundansgruppen.

- **Sekundär**

  Den server eller hanterade instans som är värd för de sekundära databaserna i redundansgruppen. Den sekundära kan inte finnas i samma region som den primära.

- **Lägga till enkla databaser i redundansgrupp**

  Du kan placera flera enkla databaser på samma server i samma redundansgrupp. Om du lägger till en enkel databas i redundansgruppen skapas automatiskt en sekundär databas med samma utgåva och beräkningsstorlek på den sekundära servern.  Du angav den servern när redundansgruppen skapades. Om du lägger till en databas som redan har en sekundär databas på den sekundära servern ärvs den geo-replikeringslänken av gruppen. När du lägger till en databas som redan har en sekundär databas på en server som inte ingår i redundansgruppen skapas en ny sekundär på den sekundära servern.

  > [!IMPORTANT]
  > Kontrollera att den sekundära servern inte har en databas med samma namn såvida det inte är en befintlig sekundär databas. I redundansgrupper för SQL Managed Instance replikeras alla användardatabaser. Du kan inte välja en delmängd av användardatabaserna för replikering i redundansgruppen.

- **Lägga till databaser i elastisk pool i redundansgrupp**

  Du kan placera alla eller flera databaser i en elastisk pool i samma redundansgrupp. Om den primära databasen finns i en elastisk pool skapas den sekundära automatiskt i den elastiska poolen med samma namn (sekundär pool). Du måste se till att den sekundära servern innehåller en elastisk pool med samma exakta namn och tillräckligt med ledigt utrymme för att vara värd för de sekundära databaser som skapas av redundansgruppen. Om du lägger till en databas i poolen som redan har en sekundär databas i den sekundära poolen ärvs den geo-replikeringslänken av gruppen. När du lägger till en databas som redan har en sekundär databas på en server som inte ingår i redundansgruppen skapas en ny sekundär databas i den sekundära poolen.
  
- **Inledande seeding**

  När du lägger till databaser, elastiska pooler eller hanterade instanser i en redundansgrupp finns det en inledande seedingfas innan datareplikeringen startar. Den inledande seeding-fasen är den längsta och dyraste åtgärden. När den inledande seedningen är klar synkroniseras data och sedan replikeras endast efterföljande dataändringar. Hur lång tid det tar att slutföra det inledande startnumret beror på storleken på dina data, antalet replikerade databaser och hastigheten på länken mellan entiteterna i redundansgruppen. Under normala omständigheter är möjlig seeding-hastighet upp till 500 GB per timme för SQL Database, och upp till 360 GB per timme för SQL Managed Instance. Seeding utförs för alla databaser parallellt.

  Du SQL Managed Instance till exempel hastigheten för Express Route-länken mellan de två instanserna när du beräknar tiden för den inledande seeding-fasen. Om hastigheten för länken mellan de två instanserna är långsammare än vad som är nödvändigt påverkas troligen starttiden avsevärt. Du kan använda den angivna seeding-hastigheten, antalet databaser, den totala storleken på data och länkhastigheten för att uppskatta hur lång tid den inledande seeding-fasen tar innan datareplikeringen startar. För en enskild databas på 100 GB skulle till exempel den inledande startfasen ta cirka 1,2 timmar om länken kan push-överför 84 GB per timme och om det inte finns några andra databaser som seedas. Om länken bara kan överföra 10 GB per timme tar seeding av en databas på 100 GB cirka 10 timmar. Om det finns flera databaser som ska replikeras körs seeding parallellt, och i kombination med långsam länkhastighet kan den inledande seeding-fasen ta betydligt längre tid, särskilt om parallell seeding av data från alla databaser överskrider den tillgängliga länkbandbredden. Om nätverksbandbredden mellan två instanser är begränsad och du lägger till flera hanterade instanser i en redundansgrupp bör du överväga att lägga till flera hanterade instanser i redundansgruppen sekventiellt, en i följd. Med en gateway-SKU av lämplig storlek mellan de två hanterade instanserna, och om företagets nätverksbandbredd tillåter det, är det möjligt att uppnå hastigheter så höga som 360 GB per timme.  

- **DNS-zon**

  Ett unikt ID som genereras automatiskt när en ny SQL Managed Instance skapas. Ett SAN-certifikat (Multi-Domain) för den här instansen etableras för att autentisera klientanslutningarna till alla instanser i samma DNS-zon. De två hanterade instanserna i samma redundansgrupp måste dela DNS-zonen.
  
  > [!NOTE]
  > Ett DNS-zon-ID krävs inte för redundansgrupper som skapats för SQL Database.

- **Lyssnare för skrivskyddade redundansgrupp**

  En DNS CNAME-post som pekar på den aktuella primära url:en. Den skapas automatiskt när redundansgruppen skapas och gör att arbetsbelastningen för läsning och skrivning transparent kan återansluta till den primära databasen när den primära ändras efter redundans. När redundansgruppen skapas på en server skapas DNS CNAME-posten för lyssnar-URL:en som `<fog-name>.database.windows.net` . När redundansgruppen skapas på en SQL Managed Instance skapas DNS CNAME-posten för lyssnar-URL:en som `<fog-name>.<zone_id>.database.windows.net` .

- **Skrivskyddade lyssnare för redundansgrupp**

  En DNS CNAME-post som bildas som pekar på den skrivskyddade lyssnaren som pekar på den sekundära url:en. Den skapas automatiskt när redundansgruppen skapas och gör att den skrivskyddade SQL-arbetsbelastningen transparent kan ansluta till den sekundära med hjälp av de angivna belastningsutjämningsreglerna. När redundansgruppen skapas på en server skapas DNS CNAME-posten för lyssnar-URL:en som `<fog-name>.secondary.database.windows.net` . När redundansgruppen skapas på en SQL Managed Instance skapas DNS CNAME-posten för lyssnar-URL:en som `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Princip för automatisk redundans**

  Som standard konfigureras en redundansgrupp med en princip för automatisk redundans. Azure utlöser redundans efter att felet har identifierats och respitperioden har gått ut. Systemet måste kontrollera att avbrottet inte kan åtgärdas av den inbyggda infrastrukturen för [hög](high-availability-sla.md) tillgänglighet på grund av effektens skala. Om du vill styra redundansarbetsflödet från programmet eller manuellt kan du inaktivera automatisk redundans.
  
  > [!NOTE]
  > Eftersom verifiering av skalan för avbrottet och hur snabbt det kan undvikas inbegriper mänskliga åtgärder av driftteamet kan respitperioden inte anges under en timme. Den här begränsningen gäller för alla databaser i redundansgruppen oavsett deras datasynkroniseringstillstånd.

- **Skrivskyddade redundansprinciper**

  Som standard är redundans för den skrivskyddade lyssnaren inaktiverad. Det säkerställer att den primäras prestanda inte påverkas när den sekundära är offline. Men det innebär också att de skrivskyddade sessionerna inte kan ansluta förrän den sekundära har återställts. Om du inte tolererar stilleståndstid för de skrivskyddade sessionerna och kan använda den primära för både skrivskyddad och skrivskyddad trafik på bekostnad av den primära serverns potentiella prestandaförsämring kan du aktivera redundans för den skrivskyddade lyssnaren genom att konfigurera `AllowReadOnlyFailoverToPrimary` egenskapen . I så fall omdirigeras den skrivskyddade trafiken automatiskt till den primära om den sekundära inte är tillgänglig.

  > [!NOTE]
  > Egenskapen `AllowReadOnlyFailoverToPrimary` har endast effekt om principen för automatisk redundans är aktiverad och en automatisk redundans har utlösts av Azure. I så fall, om egenskapen har angetts till Sant, kommer den nya primära att betjäna både skrivskyddade sessioner och skrivskyddade sessioner.

- **Planerad redundans**

   Planerad redundans utför fullständig synkronisering mellan primära och sekundära databaser innan de sekundära växlarna till den primära rollen. Detta garanterar ingen dataförlust. Planerad redundans används i följande scenarier:

  - Utföra haveriberedskapsgranskningar i produktion när dataförlusten inte är acceptabel
  - Flytta databaserna till en annan region
  - Returnera databaserna till den primära regionen efter att avbrottet har åtgärdats (återställning efter fel)

- **Oplanerad redundans**

   Oplanerad eller framtvingad redundans växlar omedelbart den sekundära till den primära rollen utan någon synkronisering med den primära. Den här åtgärden leder till dataförlust. Oplanerad redundans används som en återställningsmetod under avbrott när den primära inte är tillgänglig. När den ursprungliga primära är online igen återansluts den automatiskt utan synkronisering och blir en ny sekundär.

- **Manuell redundans**

  Du kan initiera redundans manuellt när som helst oavsett konfigurationen för automatisk redundans. Om principen för automatisk redundans inte har konfigurerats krävs manuell redundans för att återställa databaser i redundansgruppen till den sekundära. Du kan initiera framtvinga eller användarvänlig redundans (med fullständig datasynkronisering). Det senare kan användas för att flytta den primära till den sekundära regionen. När redundansen är klar uppdateras DNS-posterna automatiskt för att säkerställa anslutningen till den nya primära.

- **Respitperiod med dataförlust**

  Eftersom de primära och sekundära databaserna synkroniseras med asynkron replikering kan redundansen resultera i dataförlust. Du kan anpassa principen för automatisk redundans så att den återspeglar programmets tolerans för dataförlust. Genom att konfigurera kan du styra hur länge systemet väntar innan du initierar `GracePeriodWithDataLossHours` redundansen som kan leda till dataförlust.

- **Flera redundansgrupper**

  Du kan konfigurera flera redundansgrupper för samma serverpar för att styra redundansomfånget. Varje grupp går över oberoende av varandra. Om ditt program för flera innehavare använder elastiska pooler kan du använda den här funktionen för att blanda primära och sekundära databaser i varje pool. På så sätt kan du minska effekten av ett avbrott till endast hälften av klienterna.

  > [!NOTE]
  > SQL Managed Instance stöder inte flera redundansgrupper.
  
## <a name="permissions"></a>Behörigheter

Behörigheter för en redundansgrupp hanteras via [rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../../role-based-access-control/overview.md) Rollen [SQL Server har alla](../../role-based-access-control/built-in-roles.md#sql-server-contributor) behörigheter som krävs för att hantera redundansgrupper.

### <a name="create-failover-group"></a>Skapa redundansgrupp

Om du vill skapa en redundansgrupp behöver du Azure RBAC-skrivåtkomst till både de primära och sekundära servrarna och till alla databaser i redundansgruppen. För en SQL Managed Instance behöver du Azure RBAC-skrivåtkomst till både den primära och sekundära SQL Managed Instance,men behörigheter för enskilda databaser är inte relevanta, eftersom enskilda SQL Managed Instance-databaser inte kan läggas till i eller tas bort från en redundansgrupp.

### <a name="update-a-failover-group"></a>Uppdatera en redundansgrupp

Om du vill uppdatera en redundansgrupp behöver du Azure RBAC-skrivåtkomst till redundansgruppen och alla databaser på den aktuella primära servern eller hanterade instansen.  

### <a name="fail-over-a-failover-group"></a>Redundans för en redundansgrupp

Om du vill växla över en redundansgrupp behöver du Azure RBAC-skrivåtkomst till redundansgruppen på den nya primära servern eller den hanterade instansen.

## <a name="best-practices-for-sql-database"></a>Metodtips för SQL Database

Den automatiska redundansgruppen måste konfigureras på den primära servern och ansluter den till den sekundära servern i en annan Azure-region. Grupperna kan innehålla alla eller vissa databaser på dessa servrar. Följande diagram illustrerar en typisk konfiguration av ett geo-redundant molnprogram med flera databaser och en automatisk redundansgrupp.

![Diagram som visar en typisk konfiguration av ett geo-redundant molnprogram med flera databaser och en automatisk redundansgrupp.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> En [detaljerad SQL Database steg-för-steg-självstudiekurs](failover-group-add-single-database-tutorial.md) om hur du lägger till en databas i en SQL Database till en redundansgrupp finns i Lägga till SQL Database en redundansgrupp.

När du utformar en tjänst med affärskontinu kontinuitet i åtanke bör du följa dessa allmänna riktlinjer:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Använda en eller flera redundansgrupper för att hantera redundans för flera databaser

En eller flera redundansgrupper kan skapas mellan två servrar i olika regioner (primära och sekundära servrar). Varje grupp kan innehålla en eller flera databaser som återställs som en enhet om alla eller vissa primära databaser blir otillgängliga på grund av ett avbrott i den primära regionen. Redundansgruppen skapar geo-sekundär databas med samma tjänstmål som den primära. Om du lägger till en befintlig geo-replikeringsrelation till redundansgruppen kontrollerar du att den geo-sekundära är konfigurerad med samma tjänstnivå och beräkningsstorlek som den primära.
  
> [!IMPORTANT]
> Det går för närvarande inte att skapa redundansgrupper mellan två servrar i olika prenumerationer för Azure SQL Database. Om du flyttar den primära eller sekundära servern till en annan prenumeration när redundansgruppen har skapats kan det leda till fel i redundansbegäranden och andra åtgärder.

### <a name="using-read-write-listener-for-oltp-workload"></a>Använda läs-/skrivlyssnare för OLTP-arbetsbelastning

När du utför OLTP-åtgärder `<fog-name>.database.windows.net` använder du som server-URL och anslutningarna dirigeras automatiskt till den primära. Den här URL:en ändras inte efter redundansväxlingen. Observera att redundansen innebär att uppdatera DNS-posten så att klientanslutningarna omdirigeras till den nya primära först efter att klientens DNS-cache har uppdaterats.

### <a name="using-read-only-listener-for-read-only-workload"></a>Använda skrivskyddade lyssnare för skrivskyddade arbetsbelastningar

Om du har en logiskt isolerad skrivskyddade arbetsbelastning som är tolerant mot viss dataålditet kan du använda den sekundära databasen i programmet. För skrivskyddade sessioner använder du `<fog-name>.secondary.database.windows.net` som server-URL så dirigeras anslutningen automatiskt till den sekundära. Vi rekommenderar också att du anger läs avsikt i anslutningssträngen med hjälp av `ApplicationIntent=ReadOnly` .

> [!NOTE]
> I premium-, Affärskritisk- och hyperskala-tjänstnivåer stöder SQL Database användning av skrivskyddade repliker för att avlasta skrivskyddade frågearbetsbelastningar med hjälp av parametern i [anslutningssträngen.](read-scale-out.md) `ApplicationIntent=ReadOnly` När du har konfigurerat en geo-replikerad sekundär instans kan du använda den här funktionen till att ansluta till antingen en skrivskyddad replik på den primära platsen eller på den geo-replikerade platsen.
>
> - Om du vill ansluta till en skrivskyddade replik på den primära platsen använder du `ApplicationIntent=ReadOnly` och `<fog-name>.database.windows.net` .
> - Om du vill ansluta till en skrivskyddade replik på den sekundära platsen använder du `ApplicationIntent=ReadOnly` och `<fog-name>.secondary.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Förbereda för prestandaförsämring

Ett typiskt Azure-program använder flera Azure-tjänster och består av flera komponenter. Den automatiska redundansen för redundansgruppen utlöses baserat på det tillstånd som Azure SQL-komponenterna. Andra Azure-tjänster i den primära regionen kanske inte påverkas av avbrottet och deras komponenter kan fortfarande vara tillgängliga i den regionen. När de primära databaserna växlar till DR-regionen kan svarstiden mellan de beroende komponenterna öka. För att undvika att längre svarstider påverkar programmets prestanda bör du kontrollera redundansen för alla programkomponenter i DR-regionen och följa dessa riktlinjer för [nätverkssäkerhet.](#failover-groups-and-network-security)

### <a name="preparing-for-data-loss"></a>Förbereda för dataförlust

Om ett avbrott upptäcks väntar Azure på den period som du angav i `GracePeriodWithDataLossHours` . Standardvärdet är 1 timme. Om du inte har råd med dataförlust måste du ange ett `GracePeriodWithDataLossHours` tillräckligt stort antal, till exempel 24 timmar. Använd manuell grupp redundans för att växla tillbaka från den sekundära till den primära.

> [!IMPORTANT]
> Elastiska pooler med 800 eller färre DDU:er och fler än 250 databaser som använder geo-replikering kan stöta på problem, inklusive längre planerade redundanser och försämrade prestanda.  Dessa problem är mer sannolika för skrivintensiva arbetsbelastningar, när geo-replikeringsslutpunkter är mycket åtskilda av geografi eller när flera sekundära slutpunkter används för varje databas.  Symptom på dessa problem indikeras när fördröjningen för geo-replikering ökar med tiden.  Den här fördröjningen kan övervakas med [hjälp av sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Om dessa problem inträffar inkluderar åtgärder att öka antalet pool-DPU:er eller minska antalet geo-replikerade databaser i samma pool.

### <a name="changing-secondary-region-of-the-failover-group"></a>Ändra sekundär region för redundansgruppen

För att illustrera ändringssekvensen antar vi att server A är den primära servern, server B är den befintliga sekundära servern och server C är den nya sekundära i den tredje regionen.  Följ dessa steg för att göra övergången:

1. Skapa ytterligare secondaries för varje databas på server A till server C med aktiv [geo-replikering](active-geo-replication-overview.md). Varje databas på server A har två secondaries, en på server B och en på server C. Detta garanterar att de primära databaserna förblir skyddade under övergången.
2. Ta bort redundansgruppen. Nu misslyckas inloggningarna. Det beror på att SQL-aliasen för redundansgruppens lyssnare har tagits bort och gatewayen inte känner igen namnet på redundansgruppen.
3. Skapa om redundansgruppen med samma namn mellan servrarna A och C. Nu slutar inloggningarna att misslyckas.
4. Lägg till alla primära databaser på server A i den nya redundansgruppen.
5. Ta bort server B. Alla databaser på B tas bort automatiskt.

### <a name="changing-primary-region-of-the-failover-group"></a>Ändra den primära regionen för redundansgruppen

För att illustrera ändringssekvensen antar vi att server A är den primära servern, server B är den befintliga sekundära servern och server C är den nya primära i den tredje regionen.  Följ dessa steg för att göra övergången:

1. Utför en planerad redundans för att växla den primära servern till B. Server A blir den nya sekundära servern. Redundansen kan resultera i flera minuters driftstopp. Den faktiska tiden beror på storleken på redundansgruppen.
2. Skapa ytterligare secondaries för varje databas på server B till server C med aktiv [geo-replikering](active-geo-replication-overview.md). Varje databas på server B har två secondaries, en på server A och en på server C. Detta garanterar att de primära databaserna förblir skyddade under övergången.
3. Ta bort redundansgruppen. Nu misslyckas inloggningarna. Det beror på att SQL-aliasen för redundansgruppens lyssnare har tagits bort och gatewayen inte känner igen namnet på redundansgruppen.
4. Skapa om redundansgruppen med samma namn mellan servrarna B och C. Nu slutar inloggningarna att misslyckas.
5. Lägg till alla primära databaser på B i den nya redundansgruppen.
6. Utför en planerad redundans för redundansgruppen för att växla B och C. Server C blir nu primär och B – den sekundära. Alla sekundära databaser på server A länkas automatiskt till primär databaserna på C. Som i steg 1 kan redundansen resultera i flera minuters driftstopp.
7. Ta bort server A. Alla databaser på A tas bort automatiskt.

> [!IMPORTANT]
> När redundansgruppen tas bort tas även DNS-posterna för lyssnarslutpunkterna bort. Då finns det en sannolikhet som inte är noll för att någon annan skapar en redundansgrupp eller ett serveralias med samma namn, vilket gör att du inte kan använda det igen. Använd inte generiska namn på redundansgrupp för att minimera risken.

## <a name="best-practices-for-sql-managed-instance"></a>Metodtips för SQL Managed Instance

Den automatiska redundansgruppen måste vara konfigurerad på den primära instansen och ansluter den till den sekundära instansen i en annan Azure-region.  Alla databaser i instansen replikeras till den sekundära instansen.

Följande diagram illustrerar en typisk konfiguration av ett geo-redundant molnprogram med hjälp av hanterad instans och automatisk redundansgrupp.

![diagram över automatisk redundans](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> I [Lägg till hanterad instans i](../managed-instance/failover-group-add-instance-tutorial.md) en redundansgrupp finns en detaljerad stegvis självstudie som lägger till en SQL Managed Instance att använda redundansgrupp.

Om ditt program använder SQL Managed Instance som datanivå följer du dessa allmänna riktlinjer när du designar för affärskontinuering:

### <a name="creating-the-secondary-instance"></a>Skapa den sekundära instansen

För att säkerställa oavbruten anslutning till den primära SQL Managed Instance efter redundans måste både den primära och sekundära instansen finnas i samma DNS-zon. Det garanterar att samma SAN-certifikat (multidomän) kan användas för att autentisera klientanslutningarna till någon av de två instanserna i redundansgruppen. När programmet är redo för produktionsdistribution skapar du en sekundär SQL Managed Instance i en annan region och ser till att det delar DNS-zonen med den primära SQL Managed Instance. Du kan göra det genom att ange den valfria parametern när du skapar den. Om du använder PowerShell eller REST API är namnet på den valfria parametern , och namnet på motsvarande valfria fält `DNS Zone Partner` i Azure Portal är Primär hanterad instans.

> [!IMPORTANT]
> Den första hanterade instansen som skapas i undernätet avgör DNS-zonen för alla efterföljande instanser i samma undernät. Det innebär att två instanser från samma undernät inte kan tillhöra olika DNS-zoner.

Mer information om hur du skapar den sekundära SQL Managed Instance i samma DNS-zon som den primära instansen finns i [Skapa en sekundär hanterad instans.](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance)

### <a name="using-geo-paired-regions"></a>Använda geo-parkopplade regioner

Distribuera båda hanterade instanserna [till parkopplade regioner](../../best-practices-availability-paired-regions.md) av prestandaskäl. Hanterade instanser som finns i geo-parade regioner har mycket bättre prestanda jämfört med icke-parkopplade regioner. 

### <a name="enabling-replication-traffic-between-two-instances"></a>Aktivera replikeringstrafik mellan två instanser

Eftersom varje instans är isolerad i sitt eget VNet måste tvåriktad trafik mellan dessa virtuella nätverk tillåtas. Se [Azure VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Skapa en redundansgrupp mellan hanterade instanser i olika prenumerationer

Du kan skapa en redundansgrupp mellan SQL Managed Instances i två olika prenumerationer, så länge prenumerationer är associerade med [samma Azure Active Directory klientorganisation](../../active-directory/fundamentals/active-directory-whatis.md#terminology). När du använder PowerShell API kan du göra det genom att ange `PartnerSubscriptionId` parametern för den sekundära SQL Managed Instance. När du REST API kan varje instans-ID som ingår i `properties.managedInstancePairs` parametern ha sitt eget subscriptionID.
  
> [!IMPORTANT]
> Azure Portal stöder inte skapandet av redundansgrupper i olika prenumerationer. För befintliga redundansgrupper över olika prenumerationer och/eller resursgrupper kan redundans inte initieras manuellt via portalen från den primära SQL Managed Instance. Starta från den geo-sekundära instansen i stället.

### <a name="managing-failover-to-secondary-instance"></a>Hantera redundans till sekundär instans

Redundansgruppen hanterar redundansväxlingen för alla databaser i den hanterade SQL-instansen. När du skapar en grupp geo-replikeras alla databaser i instansen automatiskt till den sekundära hanterade SQL-instansen. Du kan inte använda redundansgrupper till att initiera en delvis redundansväxling av en delmängd av databaserna.

> [!IMPORTANT]
> Om en databas tas bort från den primära SQL Managed Instance tas den också bort automatiskt på den geo-sekundära SQL Managed Instance.

### <a name="using-read-write-listener-for-oltp-workload"></a>Använda läs- och skrivlyssnare för OLTP-arbetsbelastning

När du utför OLTP-åtgärder `<fog-name>.zone_id.database.windows.net` använder du som server-URL och anslutningarna dirigeras automatiskt till den primära. Den här URL:en ändras inte efter redundansväxlingen. Redundansen innebär att du uppdaterar DNS-posten, så klientanslutningarna omdirigeras till den nya primära först efter att klientens DNS-cache har uppdaterats. Eftersom den sekundära instansen delar DNS-zonen med den primära, kommer klientprogrammet att kunna återansluta till den med samma SAN-certifikat.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Använda skrivskyddade lyssnare för att ansluta till den sekundära instansen

Om du har en logiskt isolerad skrivskyddade arbetsbelastning som är tolerant mot viss dataålditet kan du använda den sekundära databasen i programmet. Om du vill ansluta direkt till den geo-replikerade sekundära, använder du som server-URL och anslutningen görs direkt till `<fog-name>.secondary.<zone_id>.database.windows.net` den geo-replikerade sekundära.

> [!NOTE]
> På Affärskritisk-nivån SQL Managed Instance stöd för användning av [](read-scale-out.md) skrivskyddade repliker för att avlasta skrivskyddade frågearbetsbelastningar med hjälp av parametern i `ApplicationIntent=ReadOnly` anslutningssträngen. När du har konfigurerat en geo-replikerad sekundär instans kan du använda den här funktionen till att ansluta till antingen en skrivskyddad replik på den primära platsen eller på den geo-replikerade platsen.
>
> - Om du vill ansluta till en skrivskyddade replik på den primära platsen använder du `ApplicationIntent=ReadOnly` och `<fog-name>.<zone_id>.database.windows.net` .
> - Om du vill ansluta till en skrivskyddade replik på den sekundära platsen använder du `ApplicationIntent=ReadOnly` och `<fog-name>.secondary.<zone_id>.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Förbereda för prestandaförsämring

Ett typiskt Azure-program använder flera Azure-tjänster och består av flera komponenter. Den automatiska redundansen av redundansgruppen utlöses baserat på det tillstånd som Azure SQL-komponenterna. Andra Azure-tjänster i den primära regionen kanske inte påverkas av avbrottet och deras komponenter kan fortfarande vara tillgängliga i den regionen. När de primära databaserna växlar till den sekundära regionen kan svarstiden mellan de beroende komponenterna öka. För att undvika effekten av längre svarstider på programmets prestanda bör du säkerställa redundansen för alla programkomponenter i den sekundära regionen och redundans för programkomponenter tillsammans med databasen. Vid konfigurationstiden följer du [riktlinjerna för nätverkssäkerhet](#failover-groups-and-network-security) för att säkerställa anslutningen till databasen i den sekundära regionen.

### <a name="preparing-for-data-loss"></a>Förbereda för dataförlust

Om ett avbrott upptäcks utlöses en skrivskyddad redundans om det inte finns någon dataförlust, så som vi vet är det bäst. Annars skjuts redundansen upp för den period som du anger med hjälp av `GracePeriodWithDataLossHours` . Om du har `GracePeriodWithDataLossHours` angett bör du förbereda för dataförlust. I allmänhet prioriterar Azure tillgänglighet under avbrott. Om du inte har råd med dataförlust ska du ange GracePeriodWithDataLossHours till ett tillräckligt stort antal, till exempel 24 timmar, eller inaktivera automatisk redundans.

DNS-uppdateringen av läs/skriv-lyssnaren sker omedelbart efter att redundansen har initierats. Den här åtgärden leder inte till dataförlust. Processen med att växla databasroller kan dock ta upp till 5 minuter under normala förhållanden. Tills den är klar är vissa databaser i den nya primära instansen fortfarande skrivskyddade. Om en redundans initieras med PowerShell är åtgärden för att växla den primära replikrollen synkron. Om den initieras med Azure Portal visar användargränssnittet slutförandestatus. Om avsökningsmekanismen REST API standardavsökningsmekanism för att övervaka Azure Resource Manager avsökningsmekanism.

> [!IMPORTANT]
> Använd manuell grupp redundans för att flytta tillbaka försök till den ursprungliga platsen. När avbrottet som orsakade redundansen minimeras kan du flytta dina primära databaser till den ursprungliga platsen. Det gör du genom att starta den manuella redundansen för gruppen.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Ändra den sekundära regionen för redundansgruppen

Anta att instans A är den primära instansen, att instans B är den befintliga sekundära instansen och att instans C är den nya sekundära instansen i den tredje regionen.  Följ dessa steg för att göra övergången:

1. Skapa instans C med samma storlek som A och i samma DNS-zon.
2. Ta bort redundansgruppen mellan instanserna A och B. Nu misslyckas inloggningarna eftersom SQL-aliasen för redundansgruppens lyssnare har tagits bort och gatewayen inte känner igen namnet på redundansgruppen. De sekundära databaserna kopplas bort från primärdatabaserna och blir skrivskyddade databaser.
3. Skapa en redundansgrupp med samma namn mellan instans A och C. Följ anvisningarna i redundansgruppen [med SQL Managed Instance självstudie .](../managed-instance/failover-group-add-instance-tutorial.md) Det här är en datastorleksåtgärd som slutförs när alla databaser från instans A seedas och synkroniseras.
4. Ta bort instans B om det inte behövs för att undvika onödiga kostnader.

> [!NOTE]
> Efter steg 2 och tills steg 3 har slutförts förblir databaserna i instans A oskyddade från ett oåterkalleligt fel i instans A.

### <a name="changing-primary-region-of-the-failover-group"></a>Ändra den primära regionen i redundansgruppen

Anta att instans A är den primära instansen, att instans B är den befintliga sekundära instansen och att instans C är den nya primära instansen i den tredje regionen.  Följ dessa steg för att göra övergången:

1. Skapa instans C med samma storlek som B och i samma DNS-zon.
2. Anslut till instans B och redundans manuellt för att växla den primära instansen till B. Instans A blir automatiskt den nya sekundära instansen.
3. Ta bort redundansgruppen mellan instanserna A och B. Nu misslyckas inloggningarna eftersom SQL-aliasen för redundansgruppens lyssnare har tagits bort och gatewayen inte känner igen namnet på redundansgruppen. De sekundära databaserna kopplas bort från försöken och blir skrivskyddade databaser.
4. Skapa en redundansgrupp med samma namn mellan instans A och C. Följ instruktionerna i självstudien om [redundansgrupp med hanterad instans.](../managed-instance/failover-group-add-instance-tutorial.md) Det här är en datastorleksåtgärd som slutförs när alla databaser från instans A seedas och synkroniseras.
5. Ta bort instans A om det inte behövs för att undvika onödiga kostnader.

> [!CAUTION]
> Efter steg 3 och tills steg 4 har slutförts förblir databaserna i instans A oskyddade från ett oåterkalleligt fel av instans A.

> [!IMPORTANT]
> När redundansgruppen tas bort tas även DNS-posterna för lyssnarslutpunkterna bort. Då finns det en sannolikhet som inte är noll för att någon annan skapar en redundansgrupp eller ett serveralias med samma namn, vilket gör att du inte kan använda det igen. Använd inte allmänna namn på redundansgrupp för att minimera risken.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Aktivera scenarier som är beroende av objekt från systemdatabaserna
Systemdatabaser replikeras inte till den sekundära instansen i en redundansgrupp. Om du vill aktivera scenarier som är beroende av objekt från systemdatabaserna måste du skapa samma objekt på den sekundära instansen på den sekundära instansen. Om du till exempel planerar att använda samma inloggningar på den sekundära instansen ser du till att skapa dem med identiskt SID. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Redundansgrupper och nätverkssäkerhet

För vissa program kräver säkerhetsreglerna att nätverksåtkomsten till datanivån är begränsad till en specifik komponent eller komponenter, till exempel en virtuell dator, webbtjänst osv. Det här kravet medför vissa utmaningar för utformningen av affärskontinuering och användningen av redundansgrupper. Överväg följande alternativ när du implementerar sådan begränsad åtkomst.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Använda redundansgrupper och regler för virtuellt nätverk

Om du [](vnet-service-endpoint-rule-overview.md) använder Virtual Network-tjänstslutpunkter och regler för att begränsa åtkomsten till din databas i SQL Database eller SQL Managed Instance bör du vara medveten om att varje tjänstslutpunkt för virtuellt nätverk endast gäller för en Azure-region. Slutpunkten gör det inte möjligt för andra regioner att acceptera kommunikation från undernätet. Därför kan endast klientprogram som distribueras i samma region ansluta till den primära databasen. Eftersom redundansen leder till att SQL Database-klientsessionerna dirigeras om till en server i en annan (sekundär) region, misslyckas dessa sessioner om de kommer från en klient utanför den regionen. Därför kan inte principen för automatisk redundans aktiveras om deltagande servrar eller instanser ingår i de Virtual Network reglerna. Följ dessa steg om du vill ha stöd för manuell redundans:

1. Etablera redundanta kopior av programmets frontend-komponenter (webbtjänst, virtuella datorer osv.) i den sekundära regionen
2. Konfigurera regler [för virtuellt nätverk](vnet-service-endpoint-rule-overview.md) individuellt för primär och sekundär server
3. Aktivera [frontend-redundans med hjälp av en Traffic Manager-konfiguration](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Initiera manuell redundans när avbrottet identifieras. Det här alternativet är optimerat för program som kräver konsekvent svarstid mellan frontend och datanivån och stöder återställning när antingen frontend, datanivån eller båda påverkas av avbrottet.

> [!NOTE]
> Om du använder  den skrivskyddade lyssnaren för att belastningsutjämna en skrivskyddad arbetsbelastning kontrollerar du att den här arbetsbelastningen körs på en virtuell dator eller en annan resurs i den sekundära regionen så att den kan ansluta till den sekundära databasen.

### <a name="use-failover-groups-and-firewall-rules"></a>Använda redundansgrupper och brandväggsregler

Om din plan för affärskontinu kontinuitet kräver redundans med hjälp av grupper med automatisk redundans kan du begränsa åtkomsten till databasen i SQL Database med hjälp av de traditionella brandväggsreglerna. Följ dessa steg för att stödja automatisk redundans:

1. [Skapa en offentlig IP-adress](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Skapa en offentlig lastbalanserare](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) och tilldela den offentliga IP-adressen till den.
3. [Skapa ett virtuellt nätverk och de virtuella datorerna](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) för dina frontend-komponenter
4. [Skapa en nätverkssäkerhetsgrupp](../../virtual-network/network-security-groups-overview.md) och konfigurera inkommande anslutningar.
5. Se till att de utgående anslutningarna är öppna Azure SQL Database med hjälp av tjänsttaggen ["Sql".](../../virtual-network/network-security-groups-overview.md#service-tags)
6. Skapa en [SQL Database brandväggsregel som](firewall-configure.md) tillåter inkommande trafik från den offentliga IP-adress som du skapade i steg 1.

Mer information om hur du konfigurerar utgående åtkomst och vilken IP-adress som ska användas i brandväggsreglerna finns i Utgående anslutningar [för lastbalanserare.](../../load-balancer/load-balancer-outbound-connections.md)

Konfigurationen ovan säkerställer att den automatiska redundansen inte blockerar anslutningar från frontend-komponenterna och förutsätter att programmet kan tolerera den längre svarstiden mellan frontend och datanivån.

> [!IMPORTANT]
> För att garantera verksamhetskontinu hos regionala avbrott måste du säkerställa geografisk redundans för både klientdelar och databaser.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Aktivera geo-replikering mellan hanterade instanser och deras virtuella nätverk

När du ställer in en redundansgrupp mellan primära och sekundära hanterade SQL-instanser i två olika regioner isoleras varje instans med hjälp av ett oberoende virtuellt nätverk. Om du vill tillåta replikeringstrafik mellan dessa virtuella nätverk ser du till att dessa krav är uppfyllda:

- De två instanserna av SQL Managed Instance måste finnas i olika Azure-regioner.
- De två instanserna SQL Managed Instance måste ha samma tjänstnivå och samma lagringsstorlek.
- Den sekundära instansen av SQL Managed Instance vara tom (inga användardatabaser).
- De virtuella nätverk som används av instanser av SQL Managed Instance måste anslutas via en [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [Express Route](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Se till att inga brandväggsregler blockerar portarna 5022 eller 11000–11999 när två virtuella nätverk ansluter via ett lokalt nätverk. Global VNet-peering stöds med den begränsning som beskrivs i meddelandet nedan.

   > [!IMPORTANT]
   > [2020-09-22 presenterade global peering för virtuella nätverk för nyligen skapade virtuella kluster](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Det innebär att global peering för virtuella nätverk stöds för SQL-hanterade instanser som skapats i tomma undernät efter meddelandedatumet samt för alla efterföljande hanterade instanser som skapats i dessa undernät. För alla andra SQL Managed Instances-peering är stödet begränsat till nätverken i samma region på grund av begränsningarna för global [peering för virtuella nätverk.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Se även relevant avsnitt i artikeln med vanliga frågor och [svar om Virtuella Azure-nätverk](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) för mer information. 

- De två SQL Managed Instance virtuella nätverken kan inte ha överlappande IP-adresser.
- Du måste konfigurera dina nätverkssäkerhetsgrupper (NSG) så att portarna 5022 och intervallet 11000–12000 är öppna för inkommande och utgående anslutningar från den andra hanterade instansens undernät. Det här görs för att tillåta replikeringstrafik mellan instanserna.

   > [!IMPORTANT]
   > Felkonfigurerade NSG-säkerhetsregler leder till att databaskopieringsåtgärder fastnar.

- Den sekundära SQL Managed Instance konfigureras med rätt DNS-zon-ID. DNS-zonen är en egenskap för ett SQL Managed Instance underliggande virtuellt kluster och dess ID ingår i värdnamnsadressen. Zon-ID genereras som en slumpmässig sträng när den första SQL Managed Instance skapas i varje VNet och samma ID tilldelas till alla andra instanser i samma undernät. Dns-zonen kan inte ändras när den har tilldelats. SQL Managed Instances som ingår i samma redundansgrupp måste dela DNS-zonen. Du åstadkommer detta genom att skicka zon-ID:t för den primära instansen som värdet för parametern DnsZonePartner när du skapar den sekundära instansen.

   > [!NOTE]
   > En detaljerad självstudie om hur du konfigurerar redundansgrupper med SQL Managed Instance finns i [lägga till en SQL Managed Instance till en redundansgrupp.](../managed-instance/failover-group-add-instance-tutorial.md)

## <a name="upgrading-or-downgrading-a-primary-database"></a>Uppgradera eller nedgradera en primär databas

Du kan uppgradera eller nedgradera en primär databas till en annan beräkningsstorlek (inom samma tjänstnivå, inte mellan Generell användning och Affärskritisk) utan att koppla från några sekundära databaser. När du uppgraderar rekommenderar vi att du först uppgraderar alla sekundära databaser och sedan den primära. När du nedgraderar ska du ändra ordning: nedgradera den primära först och sedan nedgradera alla sekundära databaser. När du uppgraderar eller nedgraderar databasen till en annan tjänstnivå tillämpas den här rekommendationen.

Den här sekvensen rekommenderas särskilt för att undvika problemet där den sekundära instansen med en lägre SKU blir överbelastad och måste dirigeras om under en uppgradering eller nedgradering. Du kan också undvika problemet genom att göra den primära instansen skrivskyddad, vilket dock påverkar alla arbetsbelastningar med skrivningar mot den primära instansen.

> [!NOTE]
> Om du har skapat en sekundär databas som en del av konfigurationen för redundansgruppen rekommenderar vi inte att du nedgraderar den sekundära databasen. Detta säkerställer att datanivån har tillräckligt med kapacitet för att bearbeta den vanliga arbetsbelastningen när redundans har aktiverats.

## <a name="preventing-the-loss-of-critical-data"></a>Förhindra förlust av kritiska data

På grund av den höga svarstiden i breda nätverk använder kontinuerlig kopiering en asynkron replikeringsmekanism. Asynkron replikering gör att vissa data går förlorade om ett fel inträffar. Vissa program kan dock kräva att inga data går förlorade. För att skydda dessa viktiga uppdateringar [](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) kan en programutvecklare anropa sp_wait_for_database_copy_sync-systemproceduren omedelbart efter att transaktionen har genomförs. Anrop `sp_wait_for_database_copy_sync` blockerar anropstråden tills den senast införda transaktionen har överförts till den sekundära databasen. Den väntar dock inte på att de överförda transaktionerna ska spelas upp och genomförs på den sekundära. `sp_wait_for_database_copy_sync` är begränsad till en specifik kontinuerlig kopieringslänk. Alla användare med anslutningsbehörighet till den primära databasen kan anropa den här proceduren.

> [!NOTE]
> `sp_wait_for_database_copy_sync` förhindrar dataförlust efter redundans, men garanterar inte fullständig synkronisering för läsåtkomst. Fördröjningen som orsakas av ett procedursamtal kan vara betydande och beror på storleken `sp_wait_for_database_copy_sync` på transaktionsloggen vid tidpunkten för anropet.

## <a name="failover-groups-and-point-in-time-restore"></a>Redundansgrupper och återställning till tidpunkt

Information om hur du använder återställning till tidpunkt med redundansgrupper finns i [Tidpunktsåterställning (PITR).](recovery-using-backups.md#point-in-time-restore)

## <a name="limitations-of-failover-groups"></a>Begränsningar för redundansgrupper

Tänk på följande begränsningar:

- Redundansgrupper kan inte skapas mellan två servrar eller instanser i samma Azure-regioner.
- Redundansgrupper kan inte byta namn. Du måste ta bort gruppen och skapa den på nytt med ett annat namn.
- Namnbyte på databas stöds inte för instanser i redundansgruppen. Du måste tillfälligt ta bort redundansgruppen för att kunna byta namn på en databas.
- Systemdatabaser replikeras inte till den sekundära instansen i en redundansgrupp. Scenarier som är beroende av objekt från systemdatabaserna är därför omöjliga på den sekundära instansen såvida inte objekten skapas manuellt på den sekundära instansen.

## <a name="programmatically-managing-failover-groups"></a>Hantera redundansgrupper programmatiskt

Som vi nämnt tidigare kan automatiska redundansgrupper och aktiv geo-replikering också hanteras programmatiskt med hjälp av Azure PowerShell och REST API. Följande tabeller beskriver uppsättningen kommandon som är tillgängliga. Aktiv geo-replikering innehåller en uppsättning Azure Resource Manager API:er för hantering, inklusive [cmdletarna Azure SQL Database REST API](/rest/api/sql/) [och Azure PowerShell](/powershell/azure/). Dessa API:er kräver användning av resursgrupper och har stöd för rollbaserad åtkomstkontroll i Azure (Azure RBAC). Mer information om hur du implementerar åtkomstroller finns i [Rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../../role-based-access-control/overview.md)

### <a name="manage-sql-database-failover"></a>Hantera SQL Database redundans

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Beskrivning |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära servrar|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Tar bort en redundansgrupp från servern |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar en redundansgrupps konfiguration |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Ändrar konfigurationen av en redundansgrupp |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Utlöser redundans för en redundansgrupp till den sekundära servern |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Lägger till en eller flera databaser i en redundansgrupp|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Kommando | Beskrivning |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära servrar|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Tar bort en redundansgrupp från servern |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Hämtar en konfiguration för redundansgrupp |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Ändrar en redundansgrupps konfiguration och/eller lägger till en eller flera databaser i en redundansgrupp|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Utlöser redundans för en redundansgrupp till den sekundära servern |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera redundansgrupp](/rest/api/sql/failovergroups/createorupdate) | Skapar eller uppdaterar en redundansgrupp |
| [Ta bort redundansgrupp](/rest/api/sql/failovergroups/delete) | Tar bort en redundansgrupp från servern |
| [Redundans (planerad)](/rest/api/sql/failovergroups/failover) | Utlöser redundans från den aktuella primära servern till den sekundära servern med fullständig datasynkronisering.|
| [Tvinga fram redundans för att tillåta dataförlust](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Utlöser redundans från den aktuella primära servern till den sekundära servern utan att synkronisera data. Den här åtgärden kan leda till dataförlust. |
| [Hämta redundansgrupp](/rest/api/sql/failovergroups/get) | Hämtar en redundansgrupps konfiguration. |
| [Lista redundansgrupper efter server](/rest/api/sql/failovergroups/listbyserver) | Visar en lista över redundansgrupper på en server. |
| [Uppdatera redundansgrupp](/rest/api/sql/failovergroups/update) | Uppdaterar konfigurationen för en redundansgrupp. |

---

### <a name="manage-sql-managed-instance-failover"></a>Hantera SQL Managed Instance redundans


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Beskrivning |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära instanser|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Ändrar konfigurationen av en redundansgrupp|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Hämtar en redundansgrupps konfiguration|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Utlöser redundans för en redundansgrupp till den sekundära instansen|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Tar bort en redundansgrupp|


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Kommando | Beskrivning |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Det här kommandot skapar en redundansgrupp och registrerar den på både primära och sekundära servrar|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Tar bort en redundansgrupp från servern |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Hämtar en konfiguration av en redundansgrupp |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Ändrar en redundansgrupps konfiguration och/eller lägger till en eller flera databaser i en redundansgrupp|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Utlöser redundans för en redundansgrupp till den sekundära servern |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | Beskrivning |
| --- | --- |
| [Skapa eller uppdatera redundansgrupp](/rest/api/sql/instancefailovergroups/createorupdate) | Skapar eller uppdaterar en redundansgrupps konfiguration |
| [Ta bort redundansgrupp](/rest/api/sql/instancefailovergroups/delete) | Tar bort en redundansgrupp från instansen |
| [Redundans (planerad)](/rest/api/sql/instancefailovergroups/failover) | Utlöser redundans från den aktuella primära instansen till den här instansen med fullständig datasynkronisering. |
| [Tvinga fram redundans för att tillåta dataförlust](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Utlöser redundans från den aktuella primära instansen till den sekundära instansen utan att synkronisera data. Den här åtgärden kan leda till dataförlust. |
| [Hämta redundansgrupp](/rest/api/sql/instancefailovergroups/get) | hämtar en redundansgrupps konfiguration. |
| [Lista redundansgrupper – lista efter plats](/rest/api/sql/instancefailovergroups/listbylocation) | Visar en lista över redundansgrupper på en plats. |

---

## <a name="next-steps"></a>Nästa steg

- Detaljerade självstudier finns i
  - [Lägga SQL Database till en redundansgrupp](failover-group-add-single-database-tutorial.md)
  - [Lägga till en elastisk pool till en redundansgrupp](failover-group-add-elastic-pool-tutorial.md)
  - [Lägga till SQL Managed Instance till en redundansgrupp](../managed-instance/failover-group-add-instance-tutorial.md)
- Exempelskript finns i:
  - [Använd PowerShell för att konfigurera aktiv geo-replikering för Azure SQL Database](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Använda PowerShell för att konfigurera aktiv geo-replikering för en pooldatabas i Azure SQL Database](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Använda PowerShell för att lägga till Azure SQL Database till en redundansgrupp](scripts/add-database-to-failover-group-powershell.md)
- En översikt över och scenarier för affärskontinuier finns i [Översikt över affärskontinuhet](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Mer information om Azure SQL Database säkerhetskopieringar finns i [SQL Database säkerhetskopieringar.](automated-backups-overview.md)
- Mer information om hur du använder automatiska säkerhetskopieringar för återställning finns [i Återställa en databas från tjänstinitierade säkerhetskopior.](recovery-using-backups.md)
- Mer information om autentiseringskrav för en ny primär server och databas finns i [SQL Database säkerhet efter haveriberedskap.](active-geo-replication-security-configure.md)
