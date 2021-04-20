---
title: Vad är Azure SQL?
description: 'Lär dig mer om de olika alternativen i Azure SQL med tjänster: Azure SQL Database, Azure SQL Managed Instance och SQL Server på virtuella Azure-datorer.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server moln, SQL Server i molnet, PaaS-databas, SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 394b3390386c60e2a64f52dd944dfcdb0d33951e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727032"
---
# <a name="what-is-azure-sql"></a>Vad är Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL är en familj av hanterade, säkra och intelligenta produkter som använder SQL Server-databasmotorn i Azure-molnet.

- **Azure SQL Database:** Stöd för moderna molnprogram på en intelligent, hanterad databastjänst, som omfattar serverlös beräkning. 
- **Azure SQL Managed Instance:** Modernisera dina befintliga SQL Server-program i stor skala med en intelligent fullständigt hanterad instans som en tjänst, med nästan 100 % funktionsparitet med SQL Server databasmotorn. Bäst för de flesta migreringar till molnet.
- **SQL Server** på virtuella Azure-datorer: Lift and shift-hantera dina SQL Server-arbetsbelastningar på ett enkelt sätt och upprätthålla 100 % SQL Server kompatibilitet och åtkomst på operativsystemnivå. 
 
Azure SQL bygger på den välbekanta SQL Server-motorn, så att du enkelt kan migrera program och fortsätta att använda de verktyg, språk och resurser som du är bekant med. Dina kunskaper och erfarenhet av överföring till molnet, så att du kan göra ännu mer med det du redan har. 

Lär dig hur varje produkt passar in i Microsofts Azure SQL dataplattform för att matcha rätt alternativ för dina affärsbehov. Oavsett om du prioriterar kostnadsbesparingar eller minimal administration kan den här artikeln hjälpa dig att avgöra vilken metod som ger bäst resultat mot de affärskrav som du är mest brydd om.

Om du inte har Azure SQL kan du titta på *videon What is Azure SQL* (Vad är nytt för dig) från vår Azure SQL [videoserie:](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

> [!TIP]
> Hur kan vi göra Azure SQL bättre? [Gör enkäten](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="overview"></a>Översikt

I dagens datadrivna värld är den digitala omvandlingen i allt större utsträckning beroende av vår förmåga att hantera enorma mängder data och utnyttja dess potential. Men dagens dataegenskaper blir allt mer komplexa med data som lagras lokalt, i molnet eller vid nätverkskanten. Utvecklare som skapar intelligenta och integrerande program kan vara begränsade av begränsningar som i slutändan kan påverka deras upplevelse. Begränsningar som härrör från inkompatibla plattformar, otillräcklig datasäkerhet, otillräckliga resurser och prisprestandabarriärer skapar komplexitet som kan förhindra appmodernisering och utveckling. 

En av de första sakerna man ska förstå när det gäller Azure kontra lokala SQL Server-databaser, är att det är möjligt att använda allt. Microsofts dataplattform utnyttjar SQL Server och gör den tillgänglig på fysiska lokala datorer, privata molnmiljöer, externa värdbaserade privata molnmiljöer och det offentliga molnet. 


### <a name="fully-managed-and-always-up-to-date"></a>Fullständigt hanterad och alltid uppdaterad 

Ägna mer tid åt att förnya och mindre tid på korrigering, uppdatering och backning av dina databaser. Azure är det enda molnet med evergreen SQL som automatiskt tillämpar de senaste uppdateringarna och korrigeringarna så att dina databaser alltid är uppdaterade, vilket eliminerar problem med supportens slut. Även komplexa uppgifter som prestandajustering, hög tillgänglighet, haveriberedskap och säkerhetskopieringar automatiseras, vilket gör att du kan fokusera på program.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Skydda dina data med inbyggd intelligent säkerhet 

Azure övervakar ständigt dina data för hot. Med Azure SQL kan du:

- Åtgärda potentiella hot i realtid med intelligent avancerad [hotidentifiering och](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) proaktiva aviseringar för sårbarhetsbedömning. 
- Få branschledande skydd i flera lager med [inbyggda](https://azure.microsoft.com/overview/security/) säkerhetskontroller som T-SQL, autentisering, nätverk och nyckelhantering. 
- Dra nytta av den mest omfattande [efterlevnadstäckningen](https://azure.microsoft.com/overview/trusted-cloud/compliance/) för alla molndatabastjänster. 


### <a name="business-motivations"></a>Affärsmotiver

Det finns flera faktorer som kan påverka ditt beslut att välja mellan de olika dataerbjudandena:

- [Kostnad:](#cost)Både PaaS- och IaaS-alternativet innehåller baspris som täcker underliggande infrastruktur och licensiering. Med IaaS-alternativet måste du dock investera ytterligare tid och resurser för att hantera databasen, medan dessa administrationsfunktioner ingår i priset i PaaS. Med IaaS kan du stänga av resurser när du inte använder dem för att minska kostnaden, medan PaaS alltid körs om du inte släpper och skapar om dina resurser när de behövs.
- [Administration:](#administration)PaaS-alternativ minskar den tid som du behöver investera för att administrera databasen. Men det begränsar också antalet anpassade administrationsuppgifter och skript som du kan utföra eller köra. CLR stöds till exempel inte med SQL Database, men stöds för en instans av SQL Managed Instance. Dessutom har inga distributionsalternativ i PaaS stöd för användning av spårningsflaggor.
- [Serviceavtal: Både](#service-level-agreement-sla)IaaS och PaaS tillhandahåller serviceavtal med hög branschstandard. PaaS-alternativet garanterar ett serviceavtal på 99,99 % medan IaaS garanterar ett serviceavtal på 99,95 % för infrastrukturen, vilket innebär att du måste implementera ytterligare mekanismer för att säkerställa tillgängligheten för dina databaser. Du kan uppnå ett serviceavtal på 99,99 % genom att skapa ytterligare en virtuell SQL-dator och implementera lösningen för SQL Server Always On-tillgänglighetsgrupp med hög tillgänglighet. 
- Tiden för att flytta till [Azure:](#market)SQL Server på en virtuell Azure-dator matchar din miljö exakt, så migreringen från den lokala datorn till den virtuella Azure-datorn skiljer sig inte från att flytta databaserna från en lokal server till en annan. SQL Managed Instance möjliggör även enkel migrering. Det kan dock finnas vissa ändringar som du måste tillämpa före migreringen. 


## <a name="service-comparison"></a>Jämförelse av tjänster

   ![Molnalternativ SQL Server: SQL Server på IaaS eller SaaS SQL Database i molnet.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Som du ser i diagrammet kan varje tjänsteerbjudande kännetecknas av den administrationsnivå du har över infrastrukturen och av graden av kostnadseffektivitet.

I Azure kan du ha dina SQL Server arbetsbelastningar som körs som en värdtjänst[(PaaS)](https://azure.microsoft.com/overview/what-is-paas/)eller en värdad infrastruktur[(IaaS).](https://azure.microsoft.com/overview/what-is-iaas/) I PaaS har du flera produktalternativ och tjänstnivåer i varje alternativ. Den viktigaste frågan som du måste ställa när du väljer mellan PaaS eller IaaS är om du vill hantera din databas, tillämpa korrigeringar och göra säkerhetskopior, eller vill du delegera dessa åtgärder till Azure?

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL Database](database/sql-database-paas-overview.md) är en relationsdatabas som en tjänst (DBaaS) som finns i Azure och som tillhör branschkategorin Plattform som en tjänst *(PaaS).* 
- Bäst för moderna molnprogram som vill använda de senaste stabila SQL Server och har tidsbegränsningar inom utveckling och marknadsföring. 
- En fullständigt SQL Server databasmotor som baseras på den senaste stabila Enterprise Edition av SQL Server. SQL Database två distributionsalternativ som bygger på standardiserad maskin- och programvara som ägs, hanteras och underhålls av Microsoft. 

Med SQL Server kan du använda inbyggda funktioner som kräver omfattande konfiguration (antingen lokalt eller på en virtuell Azure-dator). När du använder SQL Database, betalar du per användning med alternativ att skala upp eller ut för mer kraft utan avbrott. SQL Database har vissa ytterligare funktioner som inte är SQL Server, till exempel inbyggd hög tillgänglighet, intelligens och hantering.


Azure SQL Database erbjuder följande distributionsalternativ:
  - Som en [*enkel databas*](database/single-database-overview.md) med en egen uppsättning resurser som hanteras via en [logisk SQL-server.](database/logical-servers.md) En enkel databas liknar en [innesluten databas i](/sql/relational-databases/databases/contained-databases) SQL Server. Det här alternativet är optimerat för modern programutveckling av nya molnbaserade program. [Hyperskala](database/service-tier-hyperscale.md) [och serverlösa](database/serverless-tier-overview.md) alternativ är tillgängliga.
  - En [*elastisk pool*](database/elastic-pool-overview.md), som är en samling databaser med en delad uppsättning resurser som hanteras via en logisk [SQL-server.](database/logical-servers.md) Enkla databaser kan flyttas till och från en elastisk pool. Det här alternativet är optimerat för modern programutveckling av nya molnbaserade program med saaS-programmönstret för flera innehavare. Elastiska pooler är en kostnadseffektiv lösning för att hantera prestanda för flera databaser med varierande användningsmönster.

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

[Azure SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) hamnar i branschkategorin Plattform som en tjänst *(PaaS)* och passar bäst för de flesta migreringar till molnet. SQL Managed Instance är en samling system- och användardatabaser med en delad uppsättning resurser som är lift and shift-redo.  
- Bäst för nya program eller befintliga lokala program som vill använda de senaste stabila SQL Server och som migreras till molnet med minimala ändringar. En instans av SQL Managed Instance liknar en instans av [Microsoft SQL Server](/sql/database-engine/sql-server-database-engine-overview) databasmotor som erbjuder delade resurser för databaser och ytterligare funktioner som är begränsade till instanser. 
- SQL Managed Instance har stöd för databasmigrering från en lokal plats med minimal eller ingen databasändring. Det här alternativet ger alla PaaS-fördelar med Azure SQL Database, men lägger till funktioner som tidigare endast var tillgängliga i SQL Server virtuella datorer. Detta omfattar ett inbyggt virtuellt nätverk och nära 100 % kompatibilitet med lokala SQL Server. Instanser av SQL Managed Instance ger fullständig SQL Server och funktionskompatibilitet för migrering av SQL-servrar till Azure.

### <a name="sql-server-on-azure-vm"></a>SQL Server på virtuell Azure-dator

[SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) på virtuella Azure-datorer hamnar i branschkategorin *Infrastruktur som en tjänst (IaaS)* och gör att du kan köra SQL Server inuti en fullständigt hanterad virtuell dator (VM) i Azure. 
- SQL Server installeras och finns i molnet körs på virtuella Windows Server- eller Linux-datorer som körs på Azure, även kallat infrastruktur som en tjänst (IaaS). Virtuella SQL-datorer är ett bra alternativ för att migrera lokala datorer SQL Server och program utan någon databasändring. Alla de senaste versionerna och versionerna av SQL Server är tillgängliga för installation på en virtuell IaaS-dator. 
- Bäst för migreringar och program som kräver åtkomst på operativsystemnivå. Virtuella SQL-datorer i Azure är lift and shift-redo för befintliga program som kräver snabb migrering till molnet med minimala ändringar eller inga ändringar. Virtuella SQL-datorer ger fullständig administrativ kontroll över den SQL Server instansen och det underliggande operativsystemet för migrering till Azure. 
- Den största skillnaden jämfört SQL Database och SQL Managed Instance är att SQL Server på Azure Virtual Machines ger fullständig kontroll över databasmotorn. Du kan välja när du vill starta underhåll/uppdatering, ändra återställningsmodellen till enkel eller massloggad, pausa eller starta tjänsten vid behov och du kan anpassa SQL Server databasmotorn. Med den här ytterligare kontrollen kommer det extra ansvaret för att hantera den virtuella datorn.
- Snabba utvecklings- och test-scenarier där du inte vill köpa lokal SQL Server-maskinvara som inte är för produktion. Virtuella SQL-datorer körs också på standardiserad maskinvara som ägs, hanteras och underhålls av Microsoft. När du använder virtuella SQL-datorer kan du antingen betala per användning för en SQL Server-licens som redan ingår i en SQL Server-avbildning eller enkelt använda en befintlig licens. Du kan också stoppa eller återuppta den virtuella datorn efter behov. 
- Optimerad för att migrera befintliga program till Azure eller utöka befintliga lokala program till molnet i hybriddistributioner. Du kan också använda SQL Server på en virtuell dator för att utveckla och testa traditionella SQL Server-program. Med virtuella SQL-datorer har du fullständiga administrativa rättigheter över en dedikerad SQL Server-instans och en molnbaserad virtuell dator. Det är det perfekta valet när en organisation redan har IT-resurser tillgängliga för att underhålla de virtuella datorerna. Med dessa funktioner kan du skapa ett ytterst anpassat system för ditt programs specifika prestanda- och tillgänglighetsbehov.


### <a name="comparison-table"></a>Jämförelsetabell

Ytterligare skillnader visas i följande tabell, men både SQL Database och SQL Managed Instance optimeras för att minska de totala hanteringskostnaderna till ett minimum för etablering och *hantering av många databaser.* Löpande administrationskostnader minskar eftersom du inte behöver hantera några virtuella datorer, operativsystem eller databasprogram. Du behöver inte hantera uppgraderingar, hög tillgänglighet eller [säkerhetskopieringar](database/automated-backups-overview.md). 

I allmänhet kan SQL Database och SQL Managed Instance dramatiskt öka antalet databaser som hanteras av en enskild IT- eller utvecklingsresurs. [Elastiska](database/elastic-pool-overview.md) pooler stöder också SaaS-programarkitekturer för flera innehavare med funktioner som klientisolering och möjligheten att skala för att minska kostnaderna genom att dela resurser mellan databaser. [SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) har stöd för funktioner som är begränsade till instanser, vilket möjliggör enkel migrering av befintliga program, samt delning av resurser mellan databaser. Men SQL Server [virtuella Azure-datorer](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) ger databasadministratörer en upplevelse som liknar den lokala miljö som de är bekanta med. 


| Azure SQL Database | Hanterad Azure SQL-instans | SQL Server på virtuell Azure-dator |
| :--- | :--- | :--- |
|Stöder de flesta lokala funktioner på databasnivå. De vanligaste SQL Server är tillgängliga.<br/>99,995 % garanterad tillgänglighet.<br/>Inbyggda säkerhetskopior, korrigering, återställning.<br/>Senaste stabila databasmotorversion.<br/>Möjlighet att tilldela nödvändiga resurser (CPU/lagring) till enskilda databaser.<br/>Inbyggd avancerad intelligens och säkerhet.<br/>Onlineändring av resurser (CPU/lagring).| Stöder nästan alla lokala funktioner på instans- och databasnivå. Hög kompatibilitet med SQL Server.<br/>99,99 % garanterad tillgänglighet.<br/>Inbyggda säkerhetskopior, korrigering, återställning.<br/>Senaste stabila databasmotorversion.<br/>Enkel migrering från SQL Server.<br/>Privat IP-adress i Azure Virtual Network.<br/>Inbyggd avancerad intelligens och säkerhet.<br/>Onlineändring av resurser (CPU/lagring).| Du har fullständig kontroll över SQL Server motorn. Stöder alla lokala funktioner.<br/>Upp till 99,99 % tillgänglighet.<br/>Fullständig paritet med matchande version av lokala SQL Server.<br/>En välkänd databasmotorversion har åtgärdats.<br/>Enkel migrering från SQL Server.<br/>Privat IP-adress i Azure Virtual Network.<br/>Du kan distribuera program eller tjänster på den värd där SQL Server placeras.|
|Migrering från SQL Server kan vara en utmaning.<br/>Vissa SQL Server funktioner är inte tillgängliga.<br/>Ingen garanterad exakt underhållstid (men nästan transparent).<br/>Kompatibilitet med den SQL Server-versionen kan bara uppnås med hjälp av kompatibilitetsnivåer för databaser.<br/>Stöd för privata IP-adresser [med Azure Private Link](database/private-endpoint-overview.md).|Det finns fortfarande ett minimalt antal SQL Server funktioner som inte är tillgängliga.<br/>Ingen garanterad exakt underhållstid (men nästan transparent).<br/>Kompatibilitet med den SQL Server-versionen kan bara uppnås med hjälp av kompatibilitetsnivåer för databaser.|Du måste hantera dina säkerhetskopior och korrigeringar.<br>Du måste implementera en egen High-Availability lösning.<br/>Det finns ett driftstopp vid ändring av resurserna (CPU/lagring)|
| Databaser på upp till 100 TB. | Upp till 8 TB. | SQL Server instanser med upp till 256 TB lagringsutrymme. Instansen har stöd för så många databaser som behövs. |
| Lokalt program kan komma åt data i Azure SQL Database. | [Intern implementering av virtuellt](managed-instance/vnet-existing-add-subnet.md) nätverk och anslutning till din lokala miljö med hjälp av Azure Express Route eller VPN Gateway. | Med virtuella SQL-datorer kan du ha program som delvis körs i molnet och delvis lokalt. Du kan till exempel utöka ditt lokala nätverk och Active Directory-domän till molnet via [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Mer information om hybridmolnlösningar finns [i Utöka lokala datalösningar till molnet.](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud) |


## <a name="cost"></a>Cost

Oavsett om du är ett nystartat företag som har ont om pengar, eller ett team i ett etablerat företag som verkar under begränsade budgetbegränsningar, är begränsad budget ofta den främsta faktorn när du bestämmer hur du ska hantera dina databaser. I det här avsnittet lär du dig om grunderna för fakturering och licensiering i Azure som är Azure SQL tjänstfamiljen.  Du lär dig också hur du beräknar den totala programkostnaden.

### <a name="billing-and-licensing-basics"></a>Debitering och licensiering

För närvarande säljs **både SQL Database** och **SQL Managed Instance** som en tjänst och är tillgängliga med flera alternativ och i flera tjänstnivåer med olika priser för resurser. Alla debiteras per timme till ett fast pris baserat på den tjänstnivå och beräkningsstorlek du väljer. Den senaste informationen om de aktuella tjänstnivåer, beräkningsstorlekar och lagringsbelopp som stöds finns i [Köpmodell](database/service-tiers-dtu.md) baserad på DTU för SQL Database- och [vCore-baserad](database/service-tiers-vcore.md)köpmodell för både SQL Database och SQL Managed Instance .

- Med SQL Database kan du välja en tjänstnivå som passar dina behov från ett brett prisintervall från 5 [](database/elastic-pool-overview.md) USD/månad för basic-nivån och du kan skapa elastiska pooler för att dela resurser mellan databaser för att minska kostnaderna och hantera användningstoppar.
- Med SQL Managed Instance kan du också ta med din egen licens. Mer information om bring-your-own-licensiering finns i [Licensmobilitet via Software Assurance Azure](https://azure.microsoft.com/pricing/license-mobility/) eller använda [Azure Hybrid-förmån-kalkylatorn för](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) att se hur du sparar upp till **40 %**.

Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/). Du kan justera tjänstnivåer och beräkningsstorlekar dynamiskt så att de matchar programmets varierande dataflödesbehov.

Med **SQL Database** **och SQL Managed Instance** konfigureras, korrigeras och uppgraderas databasprogramvaran automatiskt av Azure, vilket minskar dina administrationskostnader. Dessutom gör dess [inbyggda säkerhetskopierings](database/automated-backups-overview.md)-funktioner att du kan uppnå markanta kostnadsbesparingar, speciellt om du har ett stort antal databaser.

Med **SQL på virtuella Azure-datorer** kan du använda någon av de plattformsbaserade SQL Server-avbildningarna (som innehåller en licens) eller använda din SQL Server licens. Alla versioner av SQL Server (2008R2, 2012, 2014, 2016, 2017, 2019) och utgåvor (Developer, Express, Web, Standard, Enterprise) är tillgängliga. Det finns också BYOL-versioner (Bring-Your-Own-License) av avbildningarna. När du använder de avbildningar som Azure tillhandahåller beror driftskostnaderna på storleken på de virtuella datorerna samt vilken utgåva av SQL Server du väljer. Oavsett vm-storlek eller SQL Server edition betalar du licensieringskostnaden per minut för SQL Server och Windows- eller Linux Server, tillsammans med Azure Storage för VM-diskarna. Betalningsalternativet per minut låter dig använda SQL Server så länge du behöver utan att köpa ytterligare SQL Server-licenser. Om du använder din SQL Server licens till Azure debiteras du endast för server- och lagringskostnader. Mer information om att använda sin egen licensiering finns i [Licensera Mobility via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Dessutom debiteras du för utgående Internettrafik till normal [dataöverföringskostnad](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Beräkna den totala programkostnaden

När du börjar använda en molnplattform inkluderar kostnaden för att köra ditt program kostnaden för ny utveckling och löpande administrationskostnader, plus kostnaderna för plattformen för offentliga moln.

Mer information om priser finns i följande resurser:

- [SQL Database & SQL Managed Instance prissättning](https://azure.microsoft.com/pricing/details/sql-database/)
- [Prissättning för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) [för SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) och [För Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administration

För många företag handlar beslutet att övergå till en molntjänst lika mycket om att minska administrativ komplexitet som kostnad. Med IaaS och PaaS administrerar Azure den underliggande infrastrukturen och replikerar automatiskt alla data för haveriberedskap, konfigurerar och uppgraderar databasprogramvaran, hanterar belastningsutjämning och gör transparent redundans om det uppstår ett serverfel i ett datacenter.

- Med **SQL Database** **och SQL Managed Instance** kan du fortsätta att administrera databasen, men du behöver inte längre hantera databasmotorn, operativsystemet eller maskinvaran. Exempel på saker som du kan fortsätta att administrera inkluderar databaser och inloggningar, index- och frågejusteringar samt granskning och säkerhet. Dessutom kräver konfiguration av hög tillgänglighet till ett annat datacenter minimal konfiguration och administration.
- Med **SQL på virtuella Azure-datorer** har du fullständig kontroll över operativsystemet och SQL Server-instanskonfigurationen. Med en virtuell dator är det upp till dig att bestämma när du ska uppdatera/uppgradera operativsystemet och databasprogramvaran och när du ska installera ytterligare programvara, till exempel antivirusprogram. Vissa automatiska funktioner kan avsevärt underlätta arbetet med korrigeringar, säkerhetskopiering och hög tillgänglighet. Du kan dessutom styra storleken på VM:n, antalet diskar och deras lagringskonfigurationer. Med Azure kan du ändra storleken på en virtuell dator efter behov. Mer information finns i [Storlekar för virtuella Azure-datorer och Azure-molntjänster](../virtual-machines/sizes.md).

## <a name="service-level-agreement-sla"></a>Serviceavtal (SLA)

För många IT-avdelningar är det av högsta prioritet att uppfylla drifttidsåtaganden i ett serviceavtal (SLA). I det här avsnittet tittar vi på de SLA som är tillämpliga för varje databasalternativ.

För både **Azure SQL Database** och **Azure SQL Managed Instance** tillhandahåller Microsoft ett serviceavtal med 99,99 % tillgänglighet. Den senaste informationen finns i [Serviceavtal](https://azure.microsoft.com/support/legal/sla/sql-database/).

För **SQL på virtuella Azure-datorer** tillhandahåller Microsoft ett serviceavtal för tillgänglighet på 99,95 % som bara omfattar den virtuella datorn. Det här SLA:t omfattar inte de processer som körs på den virtuella datorn (till exempel SQL Server) som kräver att du är värd för minst två VM-instanser i en tillgänglighetsuppsättning. Den senaste informationen finns på [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För databas med hög tillgänglighet (HA) i virtuella datorer bör du konfigurera ett av de alternativ för hög tillgänglighet som stöds i SQL Server, till exempel [Always On-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Användningen av ett alternativ för hög tillgänglighet som stöds medför inget nytt SLA, men ger > 99,99 % databastillgänglighet.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Tid för att flytta till Azure

**Azure SQL Database** är rätt lösning för molndesignade program när utvecklarproduktivitet och snabb tid till marknad för nya lösningar är avgörande. Med sin programmässiga DBA-lika funktionalitet, är det perfekt för molnarkitekter och utvecklare, eftersom det sänker behovet av att hantera det underliggande operativsystemet och databasen.

**Azure SQL Managed Instance** förenklar migreringen av befintliga program till Azure, så att du snabbt kan få ut migrerade databasprogram på marknaden i Azure.

**SQL på virtuella Azure-datorer** är perfekt om dina befintliga eller nya program kräver stora databaser eller åtkomst till alla funktioner i SQL Server eller Windows/Linux och du vill undvika tiden och kostnaden för att skaffa ny lokal maskinvara. Det är också ett bra sätt när du vill migrera befintliga lokala program och databaser till Azure som de är – i fall där SQL Database eller SQL Managed Instance inte passar bra. Eftersom du inte behöver ändra presentation, program och datalager sparar du tid och budget på att bygga om din befintliga lösning. Istället kan du fokusera på att migrera alla dina lösningar till Azure och på att genomföra prestandaoptimeringar som kan krävas av Azure-plattformen. Mer information finns i [Bästa praxis för prestanda i SQL Server på Azure Virtual Machines](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Nästa steg

- Se [Din första Azure SQL-databas](database/single-database-create-quickstart.md) för att komma igång med SQL Database.
- Se [Din första Azure SQL Managed Instance](managed-instance/instance-create-quickstart.md) att komma igång med SQL Managed Instance. 
- Se [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Se [Etablera en virtuell SQL Server-dator i Azure](virtual-machines/windows/create-sql-vm-portal.md) för att komma igång med SQL Server på virtuella Azure-datorer.
- [Identifiera rätt SQL Database eller SQL Managed Instance SKU för din lokala databas](/sql/dma/dma-sku-recommend-sql-db/).
