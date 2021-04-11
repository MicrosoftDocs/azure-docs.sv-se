---
title: Vad är Azure SQL?
description: 'Lär dig mer om de olika alternativen i Azure SQL-serien med tjänster: Azure SQL Database, Azure SQL-hanterad instans och SQL Server på Azure VM.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server Cloud, SQL Server i molnet, PaaS Database, Cloud SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: ea097119111d5dbd5eba3c11aba549d201186e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592191"
---
# <a name="what-is-azure-sql"></a>Vad är Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL är en familj av hanterade, säkra och intelligenta produkter som använder SQL Server Database Engine i Azure-molnet.

- **Azure SQL Database**: stöd för moderna moln program i en intelligent, hanterad databas tjänst som innehåller Server lös beräkning. 
- **Azure SQL-hanterad instans**: modernisera dina befintliga SQL Server program i stor skala med en intelligent, fullständigt hanterad instans som en tjänst, med nästan 100% funktions paritet med SQL Server databas motorn. Bäst för de flesta migreringar till molnet.
- **SQL Server på virtuella Azure-datorer**: lyft-och-flytta dina SQL Server-arbetsbelastningar med enkel och underhåll av 100% SQL Server kompatibilitet och operativ Systems nivå åtkomst. 
 
Azure SQL bygger på den välbekanta SQL Server motorn, så att du kan migrera program snabbt och fortsätta att använda verktygen, språken och resurserna som du är van vid. Dina kunskaper och erfarenhet till molnet, så att du kan göra ännu mer med det du redan har. 

Lär dig hur varje produkt passar in i Microsofts Azure SQL data-plattform för att matcha det rätta alternativet för dina affärs behov. Oavsett om du prioriterar kostnads besparingar eller minimal administration kan den här artikeln hjälpa dig att avgöra vilken metod som passar bäst för de affärs krav du bryr dig om.

Om du är nybörjare på Azure SQL kan du titta närmare *på Azure SQL-* video från vår djupgående [Azure SQL-video serie](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

> [!TIP]
> Hur kan vi göra Azure SQL bättre? [Ta undersökningen](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="overview"></a>Översikt

I dagens data drivna värld beror den digitala omvandlingen på vår möjlighet att hantera stora mängder data och ge dess potential. Men dagens datafastighetser är alltmer komplexa, med data som finns lokalt, i molnet eller i nätverkets gräns. Utvecklare som skapar intelligenta och fördjupade program kan upptäcka sig själva med begränsningar som i slut ändan kan påverka deras upplevelse. Begränsningar som orsakas av inkompatibla plattformar, otillräcklig data säkerhet, otillräckliga resurser och pris prestanda begränsningar skapa komplexitet som kan hindra modernisering och utveckling av appar. 

En av de första sakerna man ska förstå när det gäller Azure kontra lokala SQL Server-databaser, är att det är möjligt att använda allt. Microsofts data plattform utnyttjar SQL Server teknik och gör den tillgänglig på fysiska lokala datorer, privata moln miljöer, privata moln miljöer från tredje part och det offentliga molnet. 


### <a name="fully-managed-and-always-up-to-date"></a>Fullständigt hanterad och alltid uppdaterad 

Ägna mer tid åt att förnya och mindre tids korrigeringar, uppdatera och säkerhetskopiera dina databaser. Azure är det enda molnet med Evergreen SQL som automatiskt tillämpar de senaste uppdateringarna och korrigeringarna så att dina databaser alltid är uppdaterade, vilket eliminerar support för slut på support. Även avancerade aktiviteter som prestanda justering, hög tillgänglighet, haveri beredskap och säkerhets kopieringar automatiseras, vilket gör att du kan fokusera på program.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Skydda dina data med inbyggd intelligent säkerhet 

Azure övervakar ständigt dina data för hot. Med Azure SQL kan du:

- Åtgärda potentiella hot i real tid med intelligent [Avancerad hot identifiering](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) och proaktiva varningar för sårbarhets bedömning. 
- Få branschledande skydd med flera lager med [inbyggda säkerhets kontroller](https://azure.microsoft.com/overview/security/) , inklusive T-SQL, autentisering, nätverk och nyckel hantering. 
- Dra nytta av den mest omfattande [efterlevnaden](https://azure.microsoft.com/overview/trusted-cloud/compliance/) av alla moln databas tjänster. 


### <a name="business-motivations"></a>Affärs motivation

Det finns flera faktorer som kan påverka ditt beslut att välja mellan olika data erbjudanden:

- [Kostnad](#cost): både PaaS-och IaaS-alternativet inkluderar bas priset som täcker underliggande infrastruktur och licensiering. Men med alternativet IaaS måste du investera ytterligare tid och resurser för att hantera databasen, men i PaaS får du de här administrations funktionerna som ingår i priset. Med IaaS kan du stänga av resurser medan du inte använder dem för att minska kostnaderna, medan PaaS alltid körs, såvida du inte släpper och återskapar dina resurser när de behövs.
- [Administration](#administration): PaaS alternativ minskar den tid som du måste investera för att administrera-databasen. Det begränsar dock också intervallet för anpassade administrations uppgifter och skript som du kan utföra eller köra. CLR stöds till exempel inte med SQL Database, men stöds för en instans av SQL-hanterad instans. Dessutom stöder inga distributions alternativ i PaaS användningen av spårnings flaggor.
- [Service nivå avtal](#service-level-agreement-sla): både IaaS och PaaS ger hög service avtal med bransch standard. Alternativet PaaS garanterar 99,99% SLA, medan IaaS garanterar 99,95% SLA for Infrastructure, vilket innebär att du måste implementera ytterligare mekanismer för att säkerställa tillgängligheten för dina databaser. Du kan uppnå service nivå avtal på 99,99% genom att skapa en ytterligare virtuell SQL-dator och implementera SQL Server Always on-tillgänglighets gruppens lösning för hög tillgänglighet. 
- [Tid för att flytta till Azure](#market): SQL Server på Azure VM är den exakta matchningen av din miljö, så migreringen från lokala datorer till den virtuella Azure-datorn är inte något annat än att flytta databaserna från en lokal server till en annan. SQL-hanterad instans möjliggör enkel migrering. Det kan dock finnas några ändringar som du måste tillämpa innan du migrerar. 


## <a name="service-comparison"></a>Jämförelse av tjänster

   ![Alternativ för moln SQL Server: SQL Server på IaaS eller SaaS SQL Database i molnet.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Som det visas i diagrammet kan varje tjänst erbjudande karakteriseras av den hanterings nivå som du har över infrastrukturen, och utifrån kostnads effektiviteten.

I Azure kan du ha SQL Server arbets belastningar som körs som en värdbaserad tjänst ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) eller en värdbaserad infrastruktur ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). I PaaS har du flera produkt alternativ och tjänst nivåer inom varje alternativ. Den viktiga fråga som du behöver fråga när du väljer mellan PaaS eller IaaS är att du vill hantera din databas, tillämpa korrigeringar och göra säkerhets kopior, eller vill du delegera dessa åtgärder till Azure?

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL Database](database/sql-database-paas-overview.md) är en Relations databas som en tjänst (DBaaS) som är värd för Azure och som ingår i bransch kategorin för *PaaS (Platform-as-a-Service)*. 
- Bäst för moderna moln program som vill använda de senaste stabila SQL Server funktionerna och har tidsbegränsningar i utvecklingen och marknadsföringen. 
- En helt hanterad SQL Server-databasmotor, baserat på den senaste stabila Enterprise-versionen av SQL Server. SQL Database har två distributions alternativ som bygger på standardiserad maskin-och program vara som ägs, hanteras av och underhålls av Microsoft. 

Med SQL Server kan du använda inbyggda funktioner och funktioner som kräver omfattande konfiguration (antingen lokalt eller i en virtuell Azure-dator). När du använder SQL Database, betalar du per användning med alternativ att skala upp eller ut för mer kraft utan avbrott. SQL Database har ytterligare funktioner som inte är tillgängliga i SQL Server, till exempel inbyggd hög tillgänglighet, intelligens och hantering.


Azure SQL Database erbjuder följande distributions alternativ:
  - Som en [*enskild databas*](database/single-database-overview.md) med en egen uppsättning resurser som hanteras via en [logisk SQL-Server](database/logical-servers.md). En enda databas liknar en [innesluten databas](/sql/relational-databases/databases/contained-databases) i SQL Server. Det här alternativet är optimerat för modern program utveckling av nya molnbaserade program. Alternativen för [storskalig](database/service-tier-hyperscale.md) och [Server](database/serverless-tier-overview.md) lös är tillgängliga.
  - En [*elastisk pool*](database/elastic-pool-overview.md), som är en samling databaser med en delad uppsättning resurser som hanteras via en [logisk SQL-Server](database/logical-servers.md). Enskilda databaser kan flyttas till och från en elastisk pool. Det här alternativet är optimerat för modern program utveckling av nya molnbaserade program med hjälp av SaaS program mönster för flera innehavare. Elastiska pooler ger en kostnads effektiv lösning för att hantera prestanda för flera databaser som har varierande användnings mönster.

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

Den [hanterade Azure SQL-instansen](managed-instance/sql-managed-instance-paas-overview.md) hamnar i bransch kategorin för *PaaS (Platform-as-a-Service)* och är bäst för de flesta migreringar till molnet. SQL-hanterad instans är en samling system-och användar databaser med en delad uppsättning resurser som lyfter och Skift klar.  
- Bäst för nya program eller befintliga lokala program som vill använda de senaste stabila SQL Server funktionerna och som migreras till molnet med minimala ändringar. En instans av SQL-hanterad instans liknar en instans av [Microsoft SQL Server databas motor](/sql/database-engine/sql-server-database-engine-overview) som erbjuder delade resurser för databaser och ytterligare funktioner som omfattas av instanser. 
- SQL-hanterad instans har stöd för migrering av databasen från lokala datorer med minimal till ingen databas ändring. Det här alternativet ger alla PaaS-förmåner för Azure SQL Database men lägger till funktioner som tidigare endast fanns tillgängliga i SQL Server virtuella datorer. Detta inkluderar ett internt virtuellt nätverk och nära 100%-kompatibilitet med lokala SQL Server. Instanser av SQL-hanterad instans ger fullständig SQL Server åtkomst och funktions kompatibilitet för migrering av SQL-servrar till Azure.

### <a name="sql-server-on-azure-vm"></a>SQL Server på virtuell Azure-dator

[SQL Server på den virtuella Azure-datorn](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) tillhör bransch kategorin *Infrastructure-as-a-Service (IaaS)* och gör det möjligt att köra SQL Server i en fullständigt hanterad virtuell dator i Azure. 
- Bäst för migreringar och program som kräver åtkomst till operativ system nivå. Virtuella SQL-datorer i Azure är klara och Skift klara för befintliga program som kräver snabb migrering till molnet med minimala förändringar eller inga ändringar. Virtuella SQL-datorer ger fullständig administrativ kontroll över SQL Server-instansen och underliggande operativ system för migrering till Azure. 
- Snabba utvecklings- och test-scenarier där du inte vill köpa lokal SQL Server-maskinvara som inte är för produktion. Virtuella SQL-datorer körs också på standardiserad maskin vara som ägs, hanteras och underhålls av Microsoft. När du använder virtuella SQL-datorer kan du antingen betala per användning för en SQL Server licens som redan finns i en SQL Server avbildning eller enkelt använda en befintlig licens. Du kan också stoppa eller återuppta den virtuella datorn efter behov. 
- SQL Server installerat och som finns i molnet körs på virtuella Windows Server-eller Linux-datorer som körs på Azure, även kallat en infrastruktur som en tjänst (IaaS). Virtuella SQL-datorer är ett lämpligt alternativ för att migrera lokala SQL Server databaser och program utan några databas ändringar. Alla nya versioner och utgåvor av SQL Server är tillgängliga för installation på en virtuell IaaS-dator. 

    Den viktigaste skillnaden från SQL Database-och SQL-hanterad instans är att SQL Server på Azure Virtual Machines tillåter fullständig kontroll över databas motorn. Du kan välja när du vill starta underhåll/korrigering, ändra återställnings modellen till enkel eller Mass utloggning, pausa eller starta tjänsten vid behov, och du kan anpassa den SQL Server databas motorn helt. Med den här extra kontrollen får du extra ansvar att hantera den virtuella datorn.
- Optimerad för migrering av befintliga program till Azure eller för att utöka befintliga lokala program till molnet i hybrid distributioner. Du kan också använda SQL Server på en virtuell dator för att utveckla och testa traditionella SQL Server-program. Med virtuella SQL-datorer har du fullständiga administrativa rättigheter över en dedikerad SQL Server instans och en molnbaserad virtuell dator. Det är det perfekta valet när en organisation redan har IT-resurser tillgängliga för att underhålla de virtuella datorerna. Med dessa funktioner kan du skapa ett ytterst anpassat system för ditt programs specifika prestanda- och tillgänglighetsbehov.

Ytterligare skillnader visas i följande tabell, men *både SQL Database-och SQL-hanterade instanser är optimerade för att minska de totala hanterings kostnaderna till minst för etablering och hantering av många databaser.* Pågående administrations kostnader minskas eftersom du inte behöver hantera några virtuella datorer, operativ system eller databas program. Du behöver inte hantera uppgraderingar, hög tillgänglighet eller [säkerhetskopieringar](database/automated-backups-overview.md). 

I allmänhet kan SQL Database och SQL-hanterad instans dramatiskt öka antalet databaser som hanteras av en enda IT-eller utvecklings resurs. [Elastiska pooler](database/elastic-pool-overview.md) stöder även SaaS program arkitekturer med flera innehavare med funktioner som klient isolering och möjlighet att skala för att minska kostnaderna genom att dela resurser mellan databaser. [SQL-hanterad instans](managed-instance/sql-managed-instance-paas-overview.md) har stöd för instans omfattnings funktioner som möjliggör enkel migrering av befintliga program, samt delning av resurser mellan databaser.

### <a name="comparison-table"></a>Jämförelsetabell

| Azure SQL Database | Hanterad Azure SQL-instans | SQL Server på virtuell Azure-dator |
| :--- | :--- | :--- |
|Stöder de flesta lokala funktioner på databas nivå. De vanligaste SQL Server funktionerna är tillgängliga.<br/>99,995% tillgänglighet garanterad.<br/>Inbyggda säkerhets kopieringar, korrigeringar och återställning.<br/>Senaste versionen av stabil databas motor.<br/>Möjlighet att tilldela nödvändiga resurser (CPU/lagring) till enskilda databaser.<br/>Inbyggd avancerad intelligens och säkerhet.<br/>Ändring av resurser online (CPU/lagring).| Har stöd för nästan alla lokala funktioner på instans nivå och databas nivå. Hög kompatibilitet med SQL Server.<br/>99,99% tillgänglighet garanterad.<br/>Inbyggda säkerhets kopieringar, korrigeringar och återställning.<br/>Senaste versionen av stabil databas motor.<br/>Enkel migrering från SQL Server.<br/>Privat IP-adress i Azure Virtual Network.<br/>Inbyggd avancerad intelligens och säkerhet.<br/>Ändring av resurser online (CPU/lagring).| Du har fullständig kontroll över SQL Servers motorn. Stöder alla lokala funktioner.<br/>Upp till 99,99% tillgänglighet.<br/>Fullständig paritet med den matchande versionen av lokala SQL Server.<br/>Fast och välkänd databas motor version.<br/>Enkel migrering från SQL Server.<br/>Privat IP-adress i Azure Virtual Network.<br/>Du kan distribuera program eller tjänster på värden där SQL Server placeras.|
|Det kan vara svårt att migrera från SQL Server.<br/>Vissa SQL Server funktioner är inte tillgängliga.<br/>Ingen garanterad, exakt underhålls tid (men nästan transparent).<br/>Kompatibilitet med SQL Server-versionen kan bara uppnås med hjälp av kompatibilitetsnivån för databaser.<br/>Stöd för privata IP-adresser med en [privat Azure-länk](database/private-endpoint-overview.md).|Det finns fortfarande ett minimalt antal SQL Server funktioner som inte är tillgängliga.<br/>Ingen garanterad, exakt underhålls tid (men nästan transparent).<br/>Kompatibilitet med SQL Server-versionen kan bara uppnås med hjälp av kompatibilitetsnivån för databaser.|Du måste hantera dina säkerhets kopieringar och uppdateringar.<br>Du måste implementera din egen High-Availability-lösning.<br/>Det finns ett avbrott när du ändrar resurser (CPU/lagring)|
| Databaser på upp till 100 TB. | Upp till 8 TB. | SQL Server instanser med upp till 256 TB lagrings utrymme. Instansen har stöd för så många databaser som behövs. |
| Lokala program kan komma åt data i Azure SQL Database. | [Inbyggd implementering av virtuella nätverk](managed-instance/vnet-existing-add-subnet.md) och anslutning till din lokala miljö med hjälp av Azure Express Route eller VPN gateway. | Med virtuella SQL-datorer kan du ha program som körs delvis i molnet och delvis lokalt. Du kan till exempel utöka ditt lokala nätverk och Active Directory-domän till molnet via [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Mer information om hybrid moln lösningar finns i [utöka lokala data lösningar till molnet](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |


## <a name="cost"></a>Cost

Oavsett om du är en dålig för kontanter eller ett team i ett etablerat företag som arbetar under tätt budget begränsningar, är den begränsade finansieringen ofta den primära driv rutinen när du bestämmer dig för att vara värd för dina databaser. I det här avsnittet får du lära dig om fakturerings-och licensierings grunderna i Azure som är kopplade till Azure SQL-serien med tjänster.  Du lär dig också hur du beräknar den totala programkostnaden.

### <a name="billing-and-licensing-basics"></a>Debitering och licensiering

För närvarande säljs både **SQL Database** -och **SQL-hanterade instanser** som en tjänst och är tillgängliga med flera alternativ och på flera tjänst nivåer med olika priser för resurser, som alla debiteras per timme enligt en fast taxa baserat på tjänst nivån och den beräknings storlek du väljer. Den senaste informationen om aktuella tjänst nivåer, beräknings storlek och lagrings utrymme som stöds finns i [DTU-baserad inköps modell för SQL Database](database/service-tiers-dtu.md) och [vCore-baserad inköps modell för både SQL Database-och SQL-hanterade instanser](database/service-tiers-vcore.md).

- Med SQL Database kan du välja en tjänst nivå som passar dina behov från ett brett utbud av priser som börjar från 5 $/månad för Basic-nivån och du kan skapa [elastiska pooler](database/elastic-pool-overview.md) för att dela resurser mellan databaser för att minska kostnaderna och hantera användnings toppar.
- Med SQL-hanterad instans kan du också ta med din egen licens. Mer information om att använda en egen licensiering finns [licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/) eller Använd [Azure Hybrid-förmån kalkylatorn](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) för att se hur du **sparar upp till 40%**.

Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/). Du kan justera tjänst nivåerna dynamiskt och beräknings storlekarna så att de överensstämmer med programmets varierande data flödes behov.

Med **SQL Database** -och **SQL-hanterad instans** konfigureras, korrigeras och uppgraderas databas program varan automatiskt av Azure, vilket minskar administrations kostnaderna. Dessutom gör dess [inbyggda säkerhetskopierings](database/automated-backups-overview.md)-funktioner att du kan uppnå markanta kostnadsbesparingar, speciellt om du har ett stort antal databaser.

Med **SQL på virtuella Azure-datorer** kan du använda vilken plattform som helst SQL Server avbildningar (som innehåller en licens) eller ta med din SQL Server-licens. Alla SQL Server-versioner som stöds (2008R2, 2012, 2014, 2016, 2017, 2019) och utgåvor (Developer, Express, Web, standard, Enterprise) är tillgängliga. Det finns också BYOL-versioner (Bring-Your-Own-License) av avbildningarna. När du använder de avbildningar som Azure tillhandahåller beror driftskostnaderna på storleken på de virtuella datorerna samt vilken utgåva av SQL Server du väljer. Oavsett storleken på virtuella datorer eller SQL Server Edition betalar du licens kostnaderna per minut för SQL Server och Windows-eller Linux-servern, tillsammans med Azure Storage kostnaden för de virtuella dator diskarna. Betalningsalternativet per minut låter dig använda SQL Server så länge du behöver utan att köpa ytterligare SQL Server-licenser. Om du använder din egen SQL Server-licens till Azure debiteras du bara för Server-och lagrings kostnader. Mer information om att använda sin egen licensiering finns i [Licensera Mobility via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Beräkna den totala programkostnaden

När du börjar använda en moln plattform inkluderar kostnaden för att köra ditt program kostnaden för nya utvecklings-och pågående administrations kostnader samt kostnaderna för den offentliga moln tjänsten.

Mer information om priser finns i följande resurser:

- [Prissättning för SQL Database & SQL-hanterad instans](https://azure.microsoft.com/pricing/details/sql-database/)
- [Priser för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) för [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) och för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administration

För många företag handlar beslutet att övergå till en molntjänst lika mycket om att minska administrativ komplexitet som kostnad. Med IaaS och PaaS, administrerar Azure den underliggande infrastrukturen och replikerar automatiskt alla data för att tillhandahålla haveri beredskap, konfigurera och uppgradera databas program vara, hanterar belastnings utjämning och transparent redundans om det uppstår ett Server fel i ett Data Center.

- Med **SQL Database** -och **SQL-hanterad instans** kan du fortsätta att administrera din databas, men du behöver inte längre hantera databas motorn, operativ systemet eller maskin varan. Exempel på saker som du kan fortsätta att administrera inkluderar databaser och inloggningar, index- och frågejusteringar samt granskning och säkerhet. Dessutom krävs minimal konfiguration och administration för att konfigurera hög tillgänglighet till ett annat data Center.
- Med **SQL på den virtuella Azure-datorn** har du fullständig kontroll över operativ systemet och SQL Server instans konfiguration. Med en virtuell dator är det upp till dig att bestämma när du ska uppdatera/uppgradera operativ systemet och databas program vara och när du ska installera ytterligare program vara, till exempel virus skydd. Vissa automatiska funktioner kan avsevärt underlätta arbetet med korrigeringar, säkerhetskopiering och hög tillgänglighet. Du kan dessutom styra storleken på VM:n, antalet diskar och deras lagringskonfigurationer. Med Azure kan du ändra storleken på en virtuell dator efter behov. Mer information finns i [Storlekar för virtuella Azure-datorer och Azure-molntjänster](../virtual-machines/sizes.md).

## <a name="service-level-agreement-sla"></a>Service nivå avtal (SLA)

För många IT-avdelningar är det högsta prioriteten att uppfylla kraven på service avtal (SLA). I det här avsnittet tittar vi på de SLA som är tillämpliga för varje databasalternativ.

För både **Azure SQL Database** och **Azure SQL-hanterad instans** tillhandahåller Microsoft ett SLA för tillgänglighet på 99,99%. Den senaste informationen finns i [service nivå avtal](https://azure.microsoft.com/support/legal/sla/sql-database/).

För **SQL på virtuella Azure-datorer** tillhandahåller Microsoft ett SLA för tillgänglighet på 99,95% som bara täcker den virtuella datorn. Det här SLA:t omfattar inte de processer som körs på den virtuella datorn (till exempel SQL Server) som kräver att du är värd för minst två VM-instanser i en tillgänglighetsuppsättning. Den senaste informationen finns på [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För hög tillgänglighet för databasen (HA) i virtuella datorer bör du konfigurera en av de alternativ för hög tillgänglighet som stöds i SQL Server, till exempel [Always on-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Användningen av ett alternativ för hög tillgänglighet som stöds medför inget nytt SLA, men ger > 99,99 % databastillgänglighet.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Tid att flytta till Azure

**Azure SQL Database** är rätt lösning för molnbaserade program när produktiviteten för utvecklare och snabb tids till marknad för nya lösningar är viktiga. Med sin programmässiga DBA-lika funktionalitet, är det perfekt för molnarkitekter och utvecklare, eftersom det sänker behovet av att hantera det underliggande operativsystemet och databasen.

Med **Azure SQL Managed instance** kan du avsevärt förenkla migreringen av befintliga program till Azure, så att du snabbt kan flytta migrerade databas program till marknaden i Azure.

**SQL på den virtuella Azure-datorn** är perfekt om dina befintliga eller nya program kräver stora databaser eller åtkomst till alla funktioner i SQL Server eller Windows/Linux, och du vill undvika tiden och kostnaden för att förvärva ny lokal maskin vara. Det är också en bra anpassning när du vill migrera befintliga lokala program och databaser till Azure som de är i fall där SQL Database eller SQL-hanterad instans inte passar bra. Eftersom du inte behöver ändra presentations-, program-och data lager sparar du tid och budget när du skapar en ny konstruktion för din befintliga lösning. Istället kan du fokusera på att migrera alla dina lösningar till Azure och på att genomföra prestandaoptimeringar som kan krävas av Azure-plattformen. Mer information finns i [Bästa praxis för prestanda i SQL Server på Azure Virtual Machines](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Nästa steg

- Se [Din första Azure SQL-databas](database/single-database-create-quickstart.md) för att komma igång med SQL Database.
- Se [din första Azure SQL-hanterade instans](managed-instance/instance-create-quickstart.md) för att komma igång med SQL-hanterad instans. 
- Se [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Se [Etablera en virtuell SQL Server-dator i Azure](virtual-machines/windows/create-sql-vm-portal.md) för att komma igång med SQL Server på virtuella Azure-datorer.
- [Identifiera rätt SQL Database eller SQL-hanterad instans-SKU för din lokala databas](/sql/dma/dma-sku-recommend-sql-db/).