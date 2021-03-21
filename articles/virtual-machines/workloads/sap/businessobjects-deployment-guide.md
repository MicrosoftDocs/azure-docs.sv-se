---
title: SAP BusinessObjects BI Platform-distribution på Azure | Microsoft Docs
description: Planera, distribuera och konfigurera SAP BusinessObjects BI-plattformen på Azure
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 3b3c313df5704e49481c66ad682faccd48d180ea
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505910"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Planerings- och implementeringsguide för SAP BusinessObjects BI-plattform i Azure

## <a name="overview"></a>Översikt

Syftet med den här guiden är att ge rikt linjer för att planera, distribuera och konfigurera SAP BusinessObjects BI-plattformen, även kallat SAP BOBI-plattformen på Azure. Den här guiden är avsedd att gälla vanliga Azure-tjänster och-funktioner som är relevanta för SAP BOBI-plattformen. Den här guiden är inte en fullständig lista över alla möjliga konfigurations alternativ. Den täcker vanliga lösningar på vanliga distributions scenarier.

Den här guiden ersätter inte standard installations-och administrations guider för SAP BOBI-plattformen, operativ systemet eller någon databas dokumentation.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Planera och implementera SAP BusinessObjects BI-plattformen på Azure

Microsoft Azure erbjuder ett brett utbud av tjänster, inklusive data bearbetning, lagring, nätverk och många andra för företag för att bygga program utan längd för att sätta samman inköp. Azure Virtual Machines (VM) hjälper företag att distribuera på begäran och skalbara dator resurser för olika SAP-program som SAP NetWeaver-baserade program, SAP Hybris, SAP BusinessObjects BI Platform, baserat på deras affärs behov. Azure har även stöd för anslutningar mellan platser, vilket gör det möjligt för företag att integrera virtuella Azure-datorer i sina lokala domäner, deras privata moln och deras lokala SAP-system.

Det här dokumentet ger vägledning om planering och implementering av SAP BusinessObjects BI-plattformen på Azure. Den kompletterar SAP-installations dokumentationen och SAP-anteckningar, som representerar de primära resurserna för installationer och distributioner av SAP BOBI.

### <a name="architecture-overview"></a>Översikt över arkitekturen

SAP BusinessObjects BI-plattformen är ett fristående system som kan finnas på en virtuell Azure-dator eller som kan skalas till ett kluster med många Azure-Virtual Machines som kör olika komponenter. SAP BOBI-plattformen består av sex konceptuella nivåer: klient nivå, webb nivå, hanterings nivå, lagrings nivå, bearbetnings nivå och data nivå. (Mer information om varje nivå finns i administratörs guiden i [SAP BusinessObjects Business Intelligence Platform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) Help Portal). Nedan följer information på hög nivå på varje nivå:

- **Klient nivå:** Den innehåller alla Skriv bords klient program som interagerar med BI-plattformen för att tillhandahålla olika typer av rapporterings-, analys-och administrations funktioner.
- **Webb nivå:** Den innehåller webb program som distribueras till JAVA-webbprogram-servrar. Webb program tillhandahåller funktioner i BI-plattformen för slutanvändare via en webbläsare.
- **Hanterings nivå:** Den samordnar och styr alla komponenter som gör BI-plattformen. Den omfattar Central Management Server (CMS) och händelse servern och tillhör ande tjänster
- **Lagrings nivå:** Den ansvarar för att hantera filer, till exempel dokument och rapporter. Den hanterar också cachelagring av rapporter för att spara system resurser när användaren ansluter till rapporter.
- **Bearbetnings nivå:** Den analyserar data och genererar rapporter och andra typer av utdata. Det är den enda nivån som ansluter till databaser som innehåller rapport data.
- **Datanivå:** Det består av databas servrar som är värdar för CMS-system databaser och gransknings data lager.

SAP BI-plattformen består av en samling servrar som kör på en eller flera värdar. Det är viktigt att du väljer rätt distributions strategi utifrån storlek, företags behov och typ av miljö. För små installationer som utveckling och testning kan du använda en enda virtuell Azure-dator för webb program Server, databas server och alla BI-plattforms servrar. Om du använder DBaaS-erbjudande (Database-as-a-Service) från Azure körs databas servern separat från andra komponenter. För medelhög och stor installation kan du ha servrar som körs på flera virtuella Azure-datorer.

I bilden nedan visas arkitekturen för storskalig distribution av SAP BOBI-plattformen på virtuella Azure-datorer, där varje komponent distribueras och placeras i tillgänglighets uppsättningar som kan upprätthålla redundans om det uppstår avbrott i tjänsten.

![SAP BusinessObjects BI Platform Architecture på Azure](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>Arkitektur information

- Lastbalanserare

  I SAP BOBI distribution av flera instanser körs webb program servrar (eller webb nivå) på två eller flera värdar. Om du vill distribuera användar belastningen jämnt på webb servrar kan du använda en belastningsutjämnare mellan slutanvändare och webb servrar. I Azure kan du antingen använda [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) eller [Azure Application Gateway](../../../application-gateway/overview.md) för att hantera trafik till dina webb servrar.

- Webb program servrar

  Webb servern är värd för webb program för SAP BOBI-plattformen som CMC och BI Launch pad. För att uppnå hög tillgänglighet för webb server måste du distribuera minst två webb program servrar för att hantera redundans och belastnings utjämning. I Azure kan de här webb program servrarna placeras antingen i tillgänglighets uppsättningar eller tillgänglighets zoner för bättre tillgänglighet.

  Tomcat är standard webb programmet för SAP BI-plattformen. För att uppnå hög tillgänglighet för Tomcat kan du aktivera replikering med hjälp av [spärren för statiskt medlemskap](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) i Azure. Det garanterar att användaren kan komma åt SAP BI-webbprogram även när Tomcat-tjänsten har avbrutits.

  > [!Important]
  > Som standard använder Tomcat multicast IP och port för kluster som inte stöds på Azure (SAP NOTE [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- BI-plattforms servrar

  BI-plattforms servrar innehåller alla tjänster som är en del av SAP BOBI-programmet (hanterings nivå, bearbetnings nivå och lagrings nivå). När en webb server tar emot en begäran, identifierar den varje BI-plattforms Server (särskilt alla CMS-servrar i ett kluster) och tar automatiskt bort balanser om deras begär Anden. Om en av de DUBBELRIKTAde plattforms värdarna Miss lyckas skickar webb servern automatiskt begär anden till en annan värd.

  Om du vill uppnå hög tillgänglighet eller redundans för BI-plattformar måste du distribuera programmet på minst två virtuella Azure-datorer. Utifrån storleks ändringen kan du skala din BI-plattform så att den körs på fler Azure Virtual Machines.

- Fil lagrings Server (FRS)

  Fil lagrings servern innehåller alla rapporter och andra BI-dokument som har skapats. I distribution med flera instanser körs BI-plattforms servrar på flera virtuella datorer och varje virtuell dator måste ha åtkomst till dessa rapporter och andra BI-dokument. Därför måste ett fil system delas mellan alla BI-plattforms servrar.

  I Azure kan du antingen använda [Azure Premium-filer](../../../storage/files/storage-files-introduction.md) eller [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) för fil lagrings Server. Båda dessa Azure-tjänster har inbyggd redundans.

  > [!Important]
  > SMB-protokollet för Azure Files är allmänt tillgängligt, men NFS-protokollet stöd för Azure Files är för närvarande en för hands version. Mer information finns [i stöd för NFS 4,1 för Azure Files nu i för hands version](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

- CMS & gransknings databas
  
  SAP BOBI-plattformen kräver en databas för att lagra dess system data, vilket kallas CMS-databas. Den används för att lagra information om BI-plattformen, till exempel information om användare, Server, mapp, dokument, konfiguration och autentisering.

  Azure erbjuder [MySQL-databasen](https://azure.microsoft.com/en-us/services/mysql/) och [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) -DBaaS-erbjudandet (Database-as-a-Service) som kan användas för CMS-databasen och gransknings databasen. Som det här är ett PaaS erbjudande behöver kunderna inte bekymra sig om drift, tillgänglighet och underhåll av databaserna. Kunden kan också välja sin egen databas för CMS och gransknings lager utifrån deras affärs behov.

## <a name="support-matrix"></a>Stödmatris

I det här avsnittet beskrivs stöd för olika SAP BOBI-komponenter som SAP BusinessObjects BI Platform-version, operativ system och databaser i Azure.

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI-plattform

Med Azure Infrastructure as a Service (IaaS) kan du distribuera och konfigurera SAP BusinessObjects BI-plattformen på Azure Compute. Det stöder följande version av SAP BOBI-plattform –

- SAP BusinessObjects BI-plattform 4,3
- SAP BusinessObjects BI Platform 4,2 SP04 +
- SAP BusinessObjects BI Platform 4,1 SP05 +

SAP BI-plattformen körs på olika operativ system och databaser. Stöd för SAP BOBI-plattformen mellan operativ system och databas version finns i [produkt tillgänglighets mat ris](https://apps.support.sap.com/sap/support/pam) för SAP Bobi.

### <a name="operating-system"></a>Operativsystem

Azure stöder följande operativ system för SAP BusinessObjects BI Platform-distribution.

- Microsoft Windows Server
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

Den version av operativ systemet som listas i [pam (Product Availability Matrix) för SAP BUSINESSOBJECTS BI-plattformen](https://support.sap.com/pam) stöds så länge de är kompatibla med att köras i Azure-infrastrukturen.

### <a name="databases"></a>Databaser

BI-plattformen behöver databas för CMS-och gransknings data lager, som kan installeras på alla databaser som stöds och som finns i [produkt tillgänglighets matrisen för SAP](https://support.sap.com/pam) som innehåller följande –

- Microsoft SQL Server

- [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) (endast databas som stöds för SAP Bobi-plattformen i Windows)

  Det är en helt hanterad SQL Server databas motor, baserat på den senaste stabila Enterprise-versionen av SQL Server. Azure SQL Database hanterar de flesta av funktionerna för databas hantering, till exempel uppgradering, uppdatering och övervakning utan inblandning av användaren. Med Azure SQL Database kan du skapa ett lager för datalagring med hög tillgänglighet och höga prestanda för program och lösningar i Azure. Mer information hittar du i [Azure SQL Database](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) -dokumentationen.

- [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql/) (följ samma rikt linjer för kompatibilitet som anges för MySQL AB i SAP PAM)

  Det är en Relations databas tjänst som drivs av MySQL Community Edition. Ett fullständigt hanterat DBaaS-erbjudande (Database-as-a-Service) kan hantera verksamhets kritiska arbets belastningar med förutsägbar prestanda och dynamisk skalbarhet. Den har inbyggd hög tillgänglighet, automatisk säkerhets kopiering, program uppdatering, automatisk identifiering av problem och återställning efter en viss tid i upp till 35 dagar, vilket avsevärt minskar drifts uppgifterna. Mer information hittar du i [Azure Database for MySQL](../../../mysql/overview.md) -dokumentationen.

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (för version och begränsning, kontrol lera SAP anmärkning [2039619](https://launchpad.support.sap.com/#/notes/2039619))

- MaxDB

Det här dokumentet illustrerar rikt linjerna för att distribuera **SAP Bobi-plattformen på Windows med Azure SQL Database** -och **SAP Bobi-plattform på Linux med Azure Database for MySQL**. Det är också vår rekommenderade metod för att köra SAP BusinessObjects BI-plattformen på Azure.

## <a name="sizing"></a>Storlekar

Storleks ändring är en process för att fastställa maskin varu kraven för att köra programmet på ett effektivt sätt. För SAP BOBI-plattformen måste storleken göras med hjälp av SAP-storleks verktyget som kallas [Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer)-verktyget. Verktyget ger SAPS baserat på indatamängden, som sedan måste mappas till certifierade Azure Virtual Machines-typer för SAP. SAP NOTE [1928533](https://launchpad.support.sap.com/#/notes/1928533) innehåller en lista över SAP-produkter och Azure VM-typer som stöds tillsammans med SAPS. Mer information om storleks ändringar finns i [storleks guide för SAP BI](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons).

För lagrings behov för SAP BOBI-plattformen erbjuder Azure olika typer av [Managed disks](../../managed-disks-overview.md). För SAP BOBI-installationsmappen rekommenderar vi att du använder Premium-hanterad disk och för den databas som körs på virtuella datorer, följer de rikt linjer som finns i [DBMS-distribution för SAP-arbetsbelastningar](dbms_guide_general.md).

Azure har stöd för två DBaaS-erbjudanden för SAP BOBI Platform data Tier- [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database) (BI-program som körs på Windows) och [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql) (BI-program som körs i Linux och Windows). Utifrån storleks resultatet kan du välja inköps modell som passar ditt behov bäst.

> [!Tip]
> För snabb storleks referens bör du överväga 800 SAPS = 1 vCPU medan du mappar SAPS-resultatet av SAP BOBI-plattformens databas nivå till Azure Database-as-a-Service (Azure SQL Database eller Azure Database for MySQL).

### <a name="sizing-models-for-azure-sql-database"></a>Storleks modeller för Azure SQL Database

Azure SQL Database erbjuder följande tre inköps modeller:

- vCore-baserad

  Du kan välja antalet virtuella kärnor, mängden minne och lagrings hastigheten. Med den vCore-baserade inköps modellen kan du också använda [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) för att få kostnads besparingar. Den här modellen passar kunden som är flexibel, kontroll och transparens.

  Det finns tre [alternativ för tjänst nivå](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) som erbjuds i vCore-modellen som omfattar-Generell användning, affärskritisk och storskalig. Tjänst nivån definierar lagrings arkitektur, utrymme, I/O-gränser och affärs kontinuitets alternativ som är relaterade till tillgänglighet och katastrof återställning. Nedan följer information på hög nivå om varje tjänst nivå alternativ –

  1. **Generell användning** tjänst nivå passar bäst för företags arbets belastningar. Det erbjuder budget orienterade, balanserade och skalbara beräknings-och lagrings alternativ. Mer information finns i [resurs alternativ och begränsningar](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5).
  2. **Affärskritisk** tjänst nivån erbjuder affärs program den högsta återhämtningen till problem genom att använda flera isolerade repliker och ger den högsta i/O-prestandan per databas replik. Mer information finns i [resurs alternativ och begränsningar](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5).
  3. **Storskalig** Service Tier är bäst för företags arbets belastningar med mycket skalbara lagrings-och Läs skalnings krav. Den ger högre återhämtning till problem genom att tillåta konfiguration av mer än en isolerad databas replik. Mer information finns i [resurs alternativ och begränsningar](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

- DTU-baserad

  Den DTU-baserade inköps modellen erbjuder en blandning av beräknings-, minnes-och I/O-resurser i tre tjänst nivåer för att stödja ljusa och tungt databas arbets belastningar. Beräknings storlekar inom varje nivå ger en annan blandning av dessa resurser, där du kan lägga till ytterligare lagrings resurser. Det passar bäst för kunder som vill ha enkla, förkonfigurerade resurs alternativ.

  [Tjänst nivåer](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) i den DTU-baserade inköps modellen särskiljs med en mängd beräknings storlekar med en fast mängd av lagrings utrymme, fast kvarhållningsperiod för säkerhets kopior och fast pris.

- Utan server

  Den serverbaserade modellen skalar automatiskt beräkning baserat på arbets belastnings behov och fakturerar för mängden data bearbetning som används per sekund. Den serverbaserade beräknings nivån pausar automatiskt databaser under inaktiva perioder när endast lagring faktureras och återupptar automatiskt databaser när aktiviteten returnerar. Mer information finns i [resurs alternativ och begränsningar](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
  
  Det är mer lämpligt för återkommande, oförutsägbar användning med låg genomsnittlig beräknings användning över tid. Den här modellen kan användas för distribution av SAP-BOBI som inte är produktion.

> [!Note]
> För SAP BOBI är det praktiskt att använda vCore-baserad modell och välja antingen Generell användning eller Affärskritisk tjänst nivå baserat på affärs behov.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Storleks modeller för Azure Database for MySQL

Azure Database for MySQL levereras med tre olika pris nivåer. De skiljer sig åt från beräknings mängden i virtuella kärnor, minne per vCore och lagrings teknik som används för att lagra datumet. Följande är en övergripande detalj information om alternativen och för mer information om olika attribut, se [pris nivån](../../../mysql/concepts-pricing-tiers.md) för Azure Database for MySQL.

- Grundläggande

  Den används för de mål arbets belastningar som kräver ljus beräkning och I/O-prestanda.

- Generell användning

  Det passar för de flesta företags arbets belastningar som kräver balanserade beräkning och minne med skalbart I/O-genomflöde.

- Minnesoptimerad

  För databas arbets belastningar med höga prestanda som kräver minnes prestanda för snabbare bearbetning av transaktioner och högre samtidighet.

> [!Note]
> För SAP BOBI är det praktiskt att använda Generell användning eller minnesoptimerade pris nivå baserat på affärs belastningen.

## <a name="azure-resources"></a>Azure-resurser

### <a name="choosing-regions"></a>Välja regioner

Azure-regionen är en eller en samling data Center som innehåller den infrastruktur som ska köras och som är värd för olika Azure-tjänster. Den här infrastrukturen innehåller ett stort antal noder som fungerar som datornoderna eller lagringsnoder, eller som kör nätverksfunktioner. Alla regioner erbjuder inte samma tjänster.

SAP BI-plattformen innehåller olika komponenter som kan kräva specifika VM-typer, lagring som Azure Files eller Azure NetApp Files eller databas som en tjänst (DBaaS) för data nivån som kanske inte är tillgänglig i vissa regioner. Du kan ta reda på exakt information om VM-typer, Azure Storage typer eller andra Azure-tjänster i [produkter som är tillgängliga efter regions](https://azure.microsoft.com/en-us/global-infrastructure/services/) plats. Om du redan kör dina SAP-system på Azure har du antagligen identifierat din region. I så fall måste du först undersöka att de nödvändiga tjänsterna är tillgängliga i dessa regioner för att bestämma arkitekturen hos SAP BI-plattformen.

### <a name="availability-zones"></a>Tillgänglighetszoner

Tillgänglighetszoner är fysiskt åtskilda platser inom en Azure-region. Varje tillgänglighets zon består av ett eller flera data Center som är utrustade med oberoende strömförsörjning, kylning och nätverk.

För att uppnå hög tillgänglighet på varje nivå för SAP BI-plattformen kan du distribuera virtuella datorer mellan tillgänglighets zoner genom att implementera ramverk för hög tillgänglighet, vilket kan ge bästa service avtal i Azure. För service avtal för virtuell dator i Azure kontrollerar du den senaste versionen av [service avtal för virtuella datorer](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

För data nivån tillhandahåller DBaaS-tjänsten (Azure Database as a Service) som standard ramverk för hög tillgänglighet. Du behöver bara välja den region och tjänst som är av hög tillgänglighet, redundans och återhämtnings kapacitet för att minimera databas stillestånd från planerade och oplanerade avbrott, utan att du behöver konfigurera några ytterligare komponenter. Mer information om SLA för DBaaS-erbjudande som stöds på Azure finns [i hög tillgänglighet i Azure Database for MySQL](../../../mysql/concepts-high-availability.md) och [hög tillgänglighet för Azure SQL Database](../../../azure-sql/database/high-availability-sla.md).

### <a name="availability-sets"></a>Tillgänglighetsuppsättningar

Tillgänglighets uppsättningen är en logisk grupperings funktion för att isolera virtuella dator resurser (VM) från varandra vid distribution. Azure ser till att de virtuella datorer som du placerar i en tillgänglighets uppsättning körs över flera fysiska servrar, beräknings rack, lagrings enheter och nätverks växlar. Om ett maskinvaru-eller program varu haveri inträffar påverkas endast en del av dina virtuella datorer och den övergripande lösningen fortsätter att fungera. Så när virtuella datorer placeras i tillgänglighets uppsättningar distribuerar Azure Fabric Controller de virtuella datorerna över olika [fel](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) -och [uppgraderings](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) domäner för att förhindra att alla virtuella datorer blir otillgängliga på grund av infrastruktur underhåll eller fel i en feldomän.

SAP BI-plattformen innehåller många olika komponenter och när du utformar arkitekturen måste du se till att var och en av de här komponenterna är elastisk för eventuella avbrott. Du kan åstadkomma detta genom att placera Azure Virtual Machines för varje komponent i tillgänglighets uppsättningar. Tänk på att när du blandar virtuella datorer av olika VM-familjer inom en tillgänglighets uppsättning, kan du komma över problem som gör att du inte kan inkludera en viss VM-typ i en sådan tillgänglighets uppsättning. Det finns en separat tillgänglighets uppsättning för webb program, BI-program för SAP BI-plattform som marker ATS i arkitektur översikt.

Dessutom är antalet uppdaterings-och fel domäner som kan användas av en Azure-tillgänglighets uppsättning i en Azure-Scale-enhet begränsad. Så om du fortsätter att lägga till virtuella datorer i en enda tillgänglighets uppsättning upphör två eller fler virtuella datorer i samma fel-eller uppdaterings domän. Mer information finns i avsnittet om [Azures tillgänglighets uppsättningar](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) i Azure Virtual Machines planering och implementering för SAP Document.

För att förstå konceptet med tillgänglighets uppsättningar i Azure och hur tillgänglighets uppsättningar relaterar till fel-och uppgraderings domäner, Läs [Hantera tillgänglighets](../../availability.md) artikel.

> [!Important]
> Begreppen för Azure-tillgänglighetszoner och Azures tillgänglighets uppsättningar kan inte anges samtidigt. Det innebär att du kan antingen distribuera ett par eller flera virtuella datorer till en angiven tillgänglighets zon eller en Azure-tillgänglighets uppsättning. Men inte båda.

### <a name="virtual-machines"></a>Virtuella datorer

Virtuell Azure-dator är ett tjänst erbjudande som gör att du kan distribuera anpassade avbildningar till Azure som IaaS-instanser (Infrastructure-as-a-Service). Det fören klar underhåll och drift program genom att tillhandahålla beräknings-och lagrings utrymme på begäran för att vara värd för, skala och hantera webb program och anslutna program.

Azure erbjuder olika sorters virtuella datorer för alla dina program behov. Men för SAP-arbetsbelastningen har Azure begränsat valet till olika VM-familjer som är lämpliga för SAP-arbetsbelastningar och SAP HANA arbets belastningen mer specifikt. För mer information, se [vilka SAP-program som stöds för Azure-distributioner](sap-supported-product-on-azure.md).

Baserat på storleken på SAP BI-plattformen måste du mappa ditt krav till Azure Virtual Machine, som stöds i Azure för SAP-produkten. SAP NOTE [1928533](https://launchpad.support.sap.com/#/notes/1928533) är en lämplig start punkt som visar de Azure VM-typer som stöds för SAP-produkter på Windows och Linux. Dessutom måste du kontrol lera om de virtuella dator typerna är tillgängliga i en viss region, förutom att välja de rent virtuella dator typer som stöds. Du kan kontrol lera tillgängligheten för VM-typen på [produkter som är tillgängliga](https://azure.microsoft.com/global-infrastructure/services/) på sidan region. För att välja pris modell kan du referera till [Azure Virtual Machines för SAP-arbetsbelastningar](planning-guide.md#azure-virtual-machines-for-sap-workload)

### <a name="storage"></a>Storage

Azure Storage är en Azure-hanterad moln tjänst som tillhandahåller lagring som är mycket tillgänglig, säker, hållbar, skalbar och redundant. Vissa av lagrings typerna har begränsad användning för SAP-scenarier. Men flera Azure Storage typer är väl lämpade eller optimerade för vissa scenarier med SAP-arbetsbelastningar. Mer information finns [Azure Storage typer för SAP-arbetsbelastnings](planning-guide-storage.md) guide, eftersom det visar olika lagrings alternativ som är lämpliga för SAP.

Azure Storage har olika lagrings typer som är tillgängliga för kunder och information för samma kan läsas i artikeln [vilka disk typer som är tillgängliga i Azure?](../../disks-types.md). SAP BOBI-plattformen använder följande Azure Storage för att bygga program-

- Azure-hanterade diskar

  Det är en lagrings volym på Block nivå som hanteras av Azure. Du kan använda diskarna för SAP BOBI-plattformens program servrar och databaser när de installeras på virtuella Azure-datorer. Det finns olika typer av [Azure-Managed disks](../../managed-disks-overview.md) tillgängliga, men vi rekommenderar att du använder [Premium-SSD](../../disks-types.md#premium-ssd) för SAP Bobi Platform Application och Database.

  I det här exemplet används Premium-SSD för BOBI-plattformens installations katalog. För en databas som är installerad på den virtuella datorn kan du använda Managed disks för data och logg volym enligt rikt linjerna. CMS-och gransknings databaser är vanligt vis små och har inte samma krav på lagrings prestanda som för andra SAP OLTP/OLAP-databaser.

- Azure Premium-filer eller Azure NetApp Files

  I SAP BOBI-plattformen refererar fil lagrings Server (FRS) till de disk kataloger där innehåll som rapporter, universum och anslutningar lagras som används av alla program servrar i systemet. [Azure Premium-filer](../../../storage/files/storage-files-introduction.md) eller [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) -lagring kan användas som ett delat fil system för SAP Bobi-program FRS. Eftersom detta lagrings erbjudande inte är tillgängligt i alla regioner, se [produkter som är tillgängliga efter regions](https://azure.microsoft.com/en-us/global-infrastructure/services/) plats för att ta reda på uppdaterad information.

  Om tjänsten inte är tillgänglig i din region kan du skapa en NFS-server som du kan använda för att dela fil systemet till SAP BOBI-programmet. Men du måste också tänka på dess hög tillgänglighet.

![SAP BusinessObjects BI Platform Storage-layout på Azure](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>Nätverk

SAP BOBI är en rapport-och Analytics BI-plattform som inte innehåller några affärs data. Systemet är därför anslutet till andra databas servrar från där den hämtar alla data och ger insyn för användarna. Azure tillhandahåller en nätverks infrastruktur som möjliggör mappning av alla scenarier som kan realiseras med SAP BI-plattformen som att ansluta till lokala system, system i olika virtuella nätverk och andra. Mer information finns i [Microsoft Azure nätverk för SAP-arbetsbelastningar](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

För databas-som-tjänst erbjudande har alla nyligen skapade databaser (Azure SQL Database eller Azure Database for MySQL) en brand vägg som blockerar alla externa anslutningar. Du måste ange en eller flera brand Väggs regler på server nivå för att ge åtkomst till DBaaS-servern för att få åtkomst till DBaaS-tjänsten från virtuella datorer i BI-plattformen. Mer information finns i [brand Väggs regler](../../../mysql/concepts-firewall-rules.md) för Azure Database for MySQL-och [Network Access Controls](../../../azure-sql/database/network-access-controls-overview.md) -avsnitt för Azure SQL Database.

## <a name="next-steps"></a>Nästa steg

- [SAP BusinessObjects BI Platform-distribution på Linux](businessobjects-deployment-guide-linux.md)
- [Azure Virtual Machines planera och implementera SAP](planning-guide.md)
- [Azure Virtual Machines distribution för SAP](deployment-guide.md)
- [Azure Virtual Machines DBMS-distribution för SAP](./dbms_guide_general.md)