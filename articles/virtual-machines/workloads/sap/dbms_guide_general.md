---
title: Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning | Microsoft Docs
description: Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad6bbc1d3c20659441b8b062898526471f4d713a
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510240"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Den här guiden är en del av dokumentationen om hur du implementerar och distribuerar SAP-program på Microsoft Azure. Läs [planerings-och implementerings guiden][planning-guide]innan du läser den här guiden. Det här dokumentet beskriver allmänna distributions aspekter av SAP-relaterade DBMS-system på Microsoft Azure virtuella datorer (VM) med hjälp av Azure Infrastructure as a Service (IaaS)-funktioner.

Pappret kompletterar SAP-installations dokumentation och SAP-anteckningar, som representerar de primära resurserna för installationer och distributioner av SAP-program på vissa plattformar.

I det här dokumentet introduceras överväganden för att köra SAP-relaterade DBMS-system i virtuella Azure-datorer. Det finns några referenser till vissa DBMS-system i det här kapitlet. I stället hanteras de olika DBMS-systemen i det här dokumentet efter det här dokumentet.

## <a name="definitions"></a>Definitioner
Dessa villkor används i hela dokumentet:

* **IaaS**: infrastruktur som en tjänst.
* **PaaS**: plattform som en tjänst.
* **SaaS**: program vara som en tjänst.
* **SAP-komponent**: ett enskilt SAP-program som ERP Central Component (ECC), Business WAREHOUSE (BW), Solution Manager eller Enterprise Portal (EP). SAP-komponenter kan baseras på traditionella ABAP-eller Java-tekniker eller i ett icke-NetWeaver program, till exempel företags objekt.
* **SAP-miljö**: en eller flera SAP-komponenter grupperade logiskt för att utföra en affärs funktion som utveckling, kvalitets säkring, utbildning, haveri beredskap eller produktion.
* **SAP, liggande**: den här termen avser hela SAP-till gångar i en KUNDs IT-liggande. SAP-landskapet innehåller alla produktions-och produktions miljöer.
* **SAP-system**: kombinationen av ett DBMS-lager och ett program lager, till exempel ett SAP ERP-utvecklingssystem, ett SAP Business Warehouse-testsystem eller ett SAP CRM-produktionssystem. I Azure-distributioner stöds inte uppdelning av dessa två skikt mellan lokalt och Azure. Det innebär att ett SAP-system antingen distribueras lokalt eller att det distribueras i Azure. Du kan distribuera de olika systemen i ett SAP-landskap i Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utvecklings-och test system i Azure men distribuera SAP CRM-operativsystemet lokalt.
* **Olika platser**: beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration som har plats-till-plats-, multisite-, multisite-eller Azure ExpressRoute-anslutning mellan lokala data Center och Azure. I gemensam Azure-dokumentation beskrivs även de här typerna av distributioner som scenarier med olika platser. 

    Orsaken till anslutningen är att utöka lokala domäner, lokala Active Directory och lokalt DNS till Azure. Det lokala landskapet utökas till Azure-till gångar för prenumerationen. Med det här tillägget kan de virtuella datorerna ingå i den lokala domänen. Domän användare av den lokala domänen kan komma åt servrarna och köra tjänster på dessa virtuella datorer, t. ex. DBMS-tjänster. Kommunikation och namn matchning mellan virtuella datorer som distribuerats lokalt och virtuella datorer som distribueras i Azure är möjliga. Det här scenariot är det vanligaste scenariot som används för att distribuera SAP-tillgångar i Azure. Mer information finns i [planering och design för VPN gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md).

> [!NOTE]
> Distributioner mellan olika platser i SAP-system är där virtuella Azure-datorer som kör SAP-system är medlemmar i en lokal domän och som stöds för produktion av SAP-system. Konfigurationer mellan platser stöds för att distribuera delar eller slutföra SAP-landskap till Azure. Även om du kör hela SAP liggande i Azure måste de virtuella datorerna ingå i en lokal domän och Active Directory/LDAP. 
>
> I tidigare versioner av dokumentationen nämndes hybrid scenarier. Termen *hybrid* är rotad i det faktum att det finns en anslutning mellan olika platser mellan lokal och Azure. I det här fallet innebär hybrid också att de virtuella datorerna i Azure är en del av den lokala Active Directory.
>
>

I vissa Microsoft-dokumentation beskrivs olika scenarier i olika platser, särskilt för konfigurationer med hög tillgänglighet för DBMS. Om det gäller SAP-relaterade dokument, låser sig det kors lokala scenariot med plats-till-plats-eller privat [ExpressRoute](https://azure.microsoft.com/services/expressroute/) -anslutning och ett SAP-landskap som distribueras mellan lokalt och Azure.

## <a name="resources"></a>Resurser
Det finns andra artiklar tillgängliga på SAP-arbetsbelastningar på Azure. Börja med [SAP-arbetsbelastning på Azure: kom igång](./get-started.md) och välj sedan ditt intresse områden.

Följande SAP-anteckningar är relaterade till SAP på Azure i förhållande till det utrymme som beskrivs i det här dokumentet.

| Antecknings nummer | Rubrik |
| --- | --- |
| [1928533] |SAP-program på Azure: produkter och typer av virtuella Azure-datorer som stöds |
| [2015553] |SAP på Microsoft Azure: stöd för krav |
| [1999351] |Felsöka utökad Azure-övervakning för SAP |
| [2178632] |Nyckel övervaknings mått för SAP på Microsoft Azure |
| [1409604] |Virtualisering i Windows: förbättrad övervakning |
| [2191498] |SAP på Linux med Azure: förbättrad övervakning |
| [2039619] |SAP-program på Microsoft Azure som använder Oracle Database: produkter och versioner som stöds |
| [2233094] |DB6: SAP-program på Azure med IBM DB2 för Linux, UNIX och Windows: ytterligare information |
| [2243692] |Linux på Microsoft Azure (IaaS) virtuell dator: SAP-licens problem |
| [1984787] |SUSE LINUX Enterprise Server 12: installations information |
| [2002167] |Red Hat Enterprise Linux 7. x: installation och uppgradering |
| [2069760] |Oracle Linux 7. x SAP-installation och uppgradering |
| [1597355] |Rekommendation för växlings utrymme för Linux |
| [2171857] |Oracle Database 12C: stöd för fil system på Linux |
| [1114181] |Oracle Database 11g: stöd för fil system på Linux |


Information om alla SAP-anteckningar för Linux finns i [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Du behöver en fungerande kunskap om Microsoft Azure arkitektur och hur Microsoft Azure virtuella datorer distribueras och hanteras. Mer information finns i [Azure-dokumentationen](../../../index.yml).

I allmänhet är installationen och konfigurationen av Windows, Linux och DBMS i princip samma som alla virtuella datorer eller datorer utan operativ system som du installerar lokalt. Det finns vissa beslut om arkitektur-och system hanterings implementering som skiljer sig åt när du använder Azure-IaaS. Det här dokumentet beskriver de olika skillnaderna för arkitektur och system hantering som förbereds för när du använder Azure-IaaS.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Lagrings struktur för en virtuell dator för RDBMS-distributioner
Om du vill följa det här kapitlet kan du läsa och förstå informationen i [det här kapitlet][deployment-guide-3] i [distributions guiden][deployment-guide]. Du måste förstå och veta om de olika VM-serierna och skillnaderna mellan standard-och Premium-lagring innan du läser det här kapitlet. 

Mer information om Azure Storage för virtuella Azure-datorer finns i:

- [Introduktion till hanterade diskar för virtuella Azure Windows-datorer](../../windows/managed-disks-overview.md).
- [Introduktion till hanterade diskar för virtuella Azure Linux-datorer](../../linux/managed-disks-overview.md).

I en grundläggande konfiguration rekommenderar vi vanligt vis en distributions struktur där operativ systemet, DBMS och eventuell SAP-binärfiler är åtskilda från databasfilerna. Vi rekommenderar att SAP-system som körs på virtuella Azure-datorer har den grundläggande virtuella hård disken, eller disken, som installeras med operativ systemet, databas hanterings systemets körbara filer och SAP-körbara filer. 

DBMS-data och loggfiler lagras i standard lagring eller Premium Storage. De lagras på separata diskar och bifogas som logiska diskar till den ursprungliga virtuella dator avbildningen av Azure operativ systemet. För Linux-distributioner dokumenteras olika rekommendationer, särskilt för SAP HANA.

När du planerar din disklayouten, hittar du den bästa balansen mellan dessa objekt:

* Antalet datafiler.
* Antalet diskar som innehåller filerna.
* IOPS-kvoterna för en enskild disk.
* Data flödet per disk.
* Antalet ytterligare data diskar som är möjliga per VM-storlek.
* Den totala lagrings data flödet som en virtuell dator kan tillhandahålla.
* Svars tiden för olika Azure Storages typer kan tillhandahålla.
* VM-service avtal.

Azure tillämpar en IOPS-kvot per data disk. Dessa kvoter skiljer sig åt för diskar som finns i standard lagring och Premium Storage. I/O-latenen skiljer sig också åt mellan de två lagrings typerna. Premium Storage ger bättre I/O-latens. 

Var och en av de olika VM-typerna har ett begränsat antal data diskar som du kan koppla. En annan begränsning är att endast vissa typer av virtuella datorer kan använda Premium Storage. Normalt väljer du att använda en viss VM-typ baserat på processor-och minnes krav. Du kan också överväga de IOPS-, fördröjnings-och disk data flödes krav som vanligt vis skalas med antalet diskar eller typen av Premium Storage-diskar. Antalet IOPS och det data flöde som ska uppnås av varje disk kan bestämma disk storlek, särskilt med Premium Storage.

> [!NOTE]
> För DBMS-distributioner rekommenderar vi att du använder Premium Storage för alla data, transaktions loggar eller gör om filer. Det spelar ingen roll om du vill distribuera produktions-eller andra produkter än produktions system.

> [!NOTE]
> Om du vill dra nytta av Azures unika [service nivå avtal för virtuell dator](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)måste alla diskar som är anslutna vara av typen Premium Storage, som innehåller den virtuella hård disken.

> [!NOTE]
> Det finns inte stöd för att vara värd för huvuddatabasfiler, till exempel data-och loggfiler, för SAP-databaser på lagrings maskin vara som finns i samordnade tredje parts Data Center som är intill Azure Data Center. För SAP-arbetsbelastningar stöds endast lagring som representeras som inbyggd Azure-tjänst för data-och transaktions logg filen för SAP-databaser.

Placeringen av databasfilerna och loggen och göra om filerna och vilken typ av Azure Storage som du använder definieras av IOPS, latens och data flödes krav. Om du vill ha tillräckligt med IOPS kan du tvingas använda flera diskar eller använda en större Premium Storage-disk. Om du använder flera diskar skapar du en programs rand på de diskar som innehåller datafilerna eller loggen och gör om filerna. I sådana fall är IOPS-och disk data flödes service avtal för de underliggande Premium Storage-diskarna eller det högsta tillåtna IOPS för standard lagrings diskar accumulative för den resulterande stripe-uppsättningen.

Om ditt IOPS-krav överskrider vad en enskild virtuell hård disk kan tillhandahålla, måste du balansera antalet IOPS som behövs för databasfilerna över ett antal virtuella hård diskar. Det enklaste sättet att distribuera IOPS-belastningen mellan diskar är att bygga en programs rand över de olika diskarna. Placera sedan ett antal datafiler för SAP-DBMS på LUN-hämtas utanför program varan. Antalet diskar i stripe styrs av IOPs-krav, disk data flödes krav och volym krav.


---
> ![Windows][Logo_Windows] Windows
>
> Vi rekommenderar att du använder Windows lagrings utrymmen för att skapa stripe-uppsättningar över flera Azure-VHD: er. Använd minst Windows Server 2012 R2 eller Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Endast MDADM och Logical Volume Manager (LVM) stöds för att bygga en programvaru-RAID i Linux. Mer information finns i:
>
> - [Konfigurera programvaru-RAID på Linux](../../linux/configure-raid.md) med MDADM
> - [Konfigurera LVM på en virtuell Linux-dator i Azure](../../linux/configure-lvm.md) med LVM
>
>

---

> [!NOTE]
> Eftersom Azure Storage behåller tre avbildningar av de virtuella hård diskarna är det inte klokt att konfigurera en redundans när du stripar. Du behöver bara konfigurera striping så att I/o distribueras över de olika virtuella hård diskarna.
>

### <a name="managed-or-nonmanaged-disks"></a>Hanterade eller icke-hanterade diskar
Ett Azure Storage-konto är en administrativ konstruktion och även ett objekt av begränsningar. Begränsningarna skiljer sig mellan standard lagrings konton och Premium Storage-konton. Information om funktioner och begränsningar finns i [Azure Storage skalbarhets-och prestanda mål](../../../storage/common/scalability-targets-standard-account.md).

Kom ihåg att det finns en gräns för IOPS per lagrings konto för standard lagring. Se raden som innehåller den **totala begär ande frekvensen** i artikeln [Azure Storage skalbarhets-och prestanda mål](../../../storage/common/scalability-targets-standard-account.md). Det finns också en ursprunglig gräns för antalet lagrings konton per Azure-prenumeration. Balansera virtuella hård diskar för större SAP-landskap över olika lagrings konton för att undvika att överskrida gränserna för dessa lagrings konton. Detta är omständligt när du pratar om några hundra virtuella datorer med mer än tusen virtuella hård diskar.

Eftersom användning av standard lagring för DBMS-distributioner tillsammans med en SAP-arbetsbelastning inte rekommenderas, är referenser och rekommendationer till standard lagring begränsad till den här korta [artikeln](/archive/blogs/mast/configuring-azure-virtual-machines-for-optimal-storage-performance)

För att undvika det administrativa arbetet med att planera och distribuera virtuella hård diskar på olika Azure Storage-konton introducerade Microsoft [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) i 2017. Hanterade diskar är tillgängliga för standard lagring och Premium Storage. De största fördelarna med hanterade diskar jämfört med icke-hanterade diskar är:

- För hanterade diskar distribuerar Azure de olika virtuella hård diskarna mellan olika lagrings konton automatiskt vid distributions tillfället. På det här sättet når inte lagrings konto gränserna för data volym, I/O-genomflöde och IOPS.
- Med hjälp av hanterade diskar följer Azure Storage koncepten i Azures tillgänglighets uppsättningar. Om den virtuella datorn ingår i en tillgänglighets uppsättning i Azure distribueras den virtuella hård disken och den anslutna disken i olika fel-och uppdaterings domäner.


> [!IMPORTANT]
> Med tanke på fördelarna med Azure Managed Disks rekommenderar vi att du använder Azure Managed Disks för dina DBMS-distributioner och SAP-distributioner i allmänhet.
>

Information om hur du konverterar från ohanterade till hanterade diskar finns i:

- [Konvertera en virtuell Windows-dator från ohanterade diskar till hanterade diskar](../../windows/convert-unmanaged-to-managed-disks.md).
- [Konvertera en virtuell Linux-dator från ohanterade diskar till hanterade diskar](../../linux/convert-unmanaged-to-managed-disks.md).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cachelagring för virtuella datorer och data diskar
När du monterar diskar på virtuella datorer kan du välja om I/O-trafiken mellan den virtuella datorn och de diskar som finns i Azure Storage cachelagras. Standard-och Premium-lagring använder två olika tekniker för den här typen av cache.

Följande rekommendationer antar dessa I/O-egenskaper för standard-DBMS:

- Det är oftast en Läs arbets belastning mot datafiler i en databas. Dessa läsningar är prestanda kritiska för DBMS-systemet.
- Skrivning mot datafiler sker i burst-baserade på kontroll punkter eller en konstant ström. Genomsnitt över en dag, det finns färre skrivningar än läsningar. Motsatsen till läsningar från datafiler är dessa skrivningar asynkrona och innehåller inte några användar transaktioner.
- Det finns inga läsningar från transaktions loggen eller gör om filerna. Undantag är stora I/o när du utför säkerhets kopiering av transaktions loggar.
- Den huvudsakliga belastningen mot transaktion eller gör om loggfiler skrivningar. Beroende på arbets Belastningens natur kan du ha I/O-typ så litet som 4 KB eller, i/O-storlek på 1 MB eller mer.
- Alla skrivningar måste vara kvar på disken på ett tillförlitligt sätt.

För standard lagring är möjliga cache-typer:

* Inget
* Läsa
* Läsa/Skriva

Om du vill få konsekvent och deterministisk prestanda ställer du in cachelagring på standard lagring för alla diskar som innehåller DBMS-relaterade datafiler, loggar och gör om filer och tabell utrymme till **ingen**. Cachelagringen av den virtuella bas hård disken kan vara kvar med standardinställningen.

För Premium Storage finns följande alternativ för cachelagring:

* Inget
* Läsa
* Läsa/skriva
* Ingen + Skrivningsaccelerator, som endast gäller för virtuella datorer i Azure M-serien
* Läs + Skrivningsaccelerator, som endast gäller för virtuella datorer i Azure M-serien

För Premium Storage rekommenderar vi att du använder **cachelagring av utdata för datafiler** i SAP-databasen och väljer **ingen cachelagring för diskarna i logg filen (erna)**.

För distributioner i M-serien rekommenderar vi att du använder Azure Skrivningsaccelerator för din DBMS-distribution. Mer information, begränsningar och distribution av Azure Skrivningsaccelerator finns i [aktivera Skrivningsaccelerator](../../how-to-enable-write-accelerator.md).


### <a name="azure-nonpersistent-disks"></a>Azure-fasta diskar
Virtuella Azure-datorer erbjuder inte permanenta diskar när en virtuell dator har distribuerats. Vid omstart av virtuella datorer rensas allt innehåll på enheterna. Det är en tanke på att datafiler och logg-och återställnings bara filer i databaser under inga omständigheter finns på de ej beständiga enheterna. Det kan finnas undantag för vissa databaser, där dessa beständiga enheter kan vara lämpliga för tempdb och temporära tabell utrymmen. Undvik att använda dessa enheter för virtuella datorer i A-serien eftersom dessa ej beständiga enheter är begränsade i data flöde med den virtuella dator serien. 

Mer information finns i [förstå den temporära enheten på virtuella Windows-datorer i Azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines).

---
> ![Windows][Logo_Windows] Windows
>
> Enhet D i en virtuell Azure-dator är en icke-beständig enhet som backas upp av vissa lokala diskar på Azure Compute-noden. Eftersom den inte är beständig försvinner alla ändringar som gjorts i innehållet på enhet D när den virtuella datorn startas om. Ändringarna inkluderar filer som lagrats, kataloger som skapats och program som har installerats.
>
> ![Linux][Logo_Linux] Linux
>
> Virtuella Linux Azure-datorer monterar automatiskt en enhet på/mnt/Resource, vilket är en icke beständig enhet som backas upp av lokala diskar på Azure Compute-noden. Eftersom den inte är beständig försvinner alla ändringar som gjorts i innehållet i/mnt/Resource när den virtuella datorn startas om. Ändringarna inkluderar filer som lagrats, kataloger som skapats och program som har installerats.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage återhämtning
Microsoft Azure Storage lagrar den grundläggande virtuella hård disken, med OS och anslutna diskar eller blobbar, på minst tre separata lagringsnoder. Den här typen av lagring kallas lokalt redundant lagring (LRS). LRS är standard för alla typer av lagring i Azure.

Det finns andra metoder för redundans. Mer information finns i [Azure Storage replikering](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Premium Storage är den rekommenderade typen av lagring för virtuella DBMS-datorer och diskar som lagrar databaser och loggar och gör om filer. Den enda tillgängliga redundans metoden för Premium Storage är LRS. Därför måste du konfigurera databas metoder för att aktivera replikering av databas data till en annan Azure-region eller tillgänglighets zon. Databas metoder är SQL Server Always on, Oracle data Guard och HANA-systemreplikering.


> [!NOTE]
> För DBMS-distributioner rekommenderas inte användning av Geo-redundant lagring (GRS) för standard lagring. GRS allvarligt påverkar prestanda och följer inte Skriv ordningen mellan olika virtuella hård diskar som är kopplade till en virtuell dator. Det går inte att respektera Skriv ordningen mellan olika virtuella hård diskar som potentiellt kan leda till inkonsekventa databaser på målsidan. Detta inträffar om databasen och loggen och gör om filerna sprids över flera virtuella hård diskar, vilket vanligt vis är fallet på den virtuella käll datorn.



## <a name="vm-node-resiliency"></a>Återhämtning av VM-nod
Azure erbjuder flera olika service avtal för virtuella datorer. Mer information finns i den senaste versionen av [SLA för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Eftersom DBMS-skiktet vanligt vis är viktigt för att vara tillgängligt i ett SAP-system, måste du förstå tillgänglighets uppsättningar, tillgänglighets zoner och underhålls händelser. Mer information om dessa begrepp finns i [Hantera tillgängligheten för virtuella Windows-datorer i Azure](../../windows/manage-availability.md) och [Hantera tillgängligheten för virtuella Linux-datorer i Azure](../../linux/manage-availability.md).

Den minsta rekommendationen för scenarier med produktions-DBMS med en SAP-arbets belastning är att:

- Distribuera två virtuella datorer i en separat tillgänglighets uppsättning i samma Azure-region.
- Kör dessa två virtuella datorer i samma virtuella Azure-nätverk och har nätverkskort som är anslutna till samma undernät.
- Använd databas metoder om du vill ha en snabb växling till den andra virtuella datorn. Metoder kan vara SQL Server Always on, Oracle data Guard eller HANA System Replication.

Du kan också distribuera en tredje virtuell dator i en annan Azure-region och använda samma databas metoder för att tillhandahålla en asynkron replik i en annan Azure-region.

Information om hur du konfigurerar Azures tillgänglighets uppsättningar finns i [den här självstudien](../../windows/tutorial-availability-sets.md).



## <a name="azure-network-considerations"></a>Nätverks överväganden för Azure
I storskaliga SAP-distributioner använder du skissen av [Azure Virtual Data Center](/azure/architecture/vdc/networking-virtual-datacenter). Använd den för konfiguration av virtuella nätverk och behörigheter och roll tilldelningar till olika delar av organisationen.

Dessa bästa metoder är resultatet av hundratals kund distributioner:

- De virtuella nätverk som SAP-programmet har distribuerats till har inte åtkomst till Internet.
- De virtuella databas datorerna körs i samma virtuella nätverk som program lagret.
- De virtuella datorerna i det virtuella nätverket har en statisk allokering av den privata IP-adressen. Mer information finns i [IP-diagramtyper och autentiseringsmetoder i Azure](../../../virtual-network/public-ip-addresses.md).
- Begränsningar för routning till och från de virtuella DBMS-datorerna har *inte* ställts in med brand väggar som är installerade på lokala DBMS-datorer. I stället definieras trafik dirigeringen med [nätverks säkerhets grupper (NSG: er)](../../../virtual-network/security-overview.md).
- Om du vill avgränsa och isolera trafik till den virtuella DBMS-datorn tilldelar du olika nätverkskort till den virtuella datorn. Varje nätverkskort får en annan IP-adress och varje nätverkskort tilldelas till ett annat virtuellt nätverks under nät. Varje undernät har olika NSG-regler. Isolering eller separering av nätverks trafik är ett mått för routning. Den används inte för att ange kvoter för nätverks data flöde.

> [!NOTE]
> Genom att tilldela statiska IP-adresser via Azure kan du tilldela dem till enskilda virtuella nätverkskort. Tilldela inte statiska IP-adresser i gäst operativ systemet till ett virtuellt nätverkskort. Vissa Azure-tjänster som Azure Backup är beroende av att minst det primära virtuella NÄTVERKSKORTet är inställt på DHCP och inte för statiska IP-adresser. Mer information finns i [Felsöka säkerhets kopiering av virtuella Azure-datorer](../../../backup/backup-azure-vms-troubleshoot.md#networking). Tilldela flera virtuella nätverkskort till en virtuell dator om du vill tilldela flera statiska IP-adresser till en virtuell dator.
>


> [!IMPORTANT]
> Konfigurera [virtuella nätverks](https://azure.microsoft.com/solutions/network-appliances/) installationer i kommunikations vägen mellan SAP-programmet och DBMS-skiktet i ett SAP NetWeaver-, hybris-eller S/4HANA-baserat SAP-system stöds inte. Den här begränsningen är av funktions-och prestanda skäl. Kommunikations vägen mellan SAP-program lagret och DBMS-skiktet måste vara en direkt. Begränsningen omfattar inte [program säkerhets gruppen (grupperna) och NSG-regler](../../../virtual-network/security-overview.md) om de grupperna och NSG reglerna tillåter en direkt kommunikations väg. 
>
> Andra scenarier där virtuella nätverks enheter inte stöds finns i:
>
> * Kommunikations vägar mellan virtuella Azure-datorer som representerar Linux pacemaker-klusternoder och SBD-enheter enligt beskrivningen i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](./high-availability-guide-suse.md).
> * Kommunikations vägar mellan virtuella Azure-datorer och Windows Server Skalbar filserver (SOFS) som beskrivs i [klustra en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en fil resurs i Azure](./sap-high-availability-guide-wsfc-file-share.md). 
>
> Nätverks virtuella apparater i kommunikations vägar kan enkelt dubblera nätverks fördröjningen mellan två kommunikations partner. De kan också begränsa data flödet i kritiska sökvägar mellan SAP-program skiktet och DBMS-skiktet. I vissa kund scenarier kan virtuella nätverks enheter orsaka att pacemaker Linux-kluster inte fungerar. Detta är fall där kommunikationen mellan Linux pacemaker-klusternoderna kommunicerar med sin SBD-enhet via en virtuell nätverks installation.
>

> [!IMPORTANT]
> En annan design som *inte* stöds är en uppdelning av SAP-program skiktet och DBMS-skiktet i olika virtuella Azure-nätverk som inte är [peer](../../../virtual-network/virtual-network-peering-overview.md) -kopplade med varandra. Vi rekommenderar att du åtskiljer SAP-programlagret och DBMS-skiktet med hjälp av undernät i ett virtuellt Azure-nätverk i stället för att använda olika virtuella Azure-nätverk. 
>
> Om du väljer att inte följa rekommendationen och i stället åtskiljer de två lagren i olika virtuella nätverk måste de två virtuella nätverken vara [peer](../../../virtual-network/virtual-network-peering-overview.md)-kopplade. 
>
> Observera att nätverks trafiken mellan två [peer-datorer](../../../virtual-network/virtual-network-peering-overview.md) i Azure är beroende av överförings kostnader. Enorma data volymer som består av många terabyte utbyts mellan SAP-program skiktet och DBMS-skiktet. Du kan ackumulera avsevärda kostnader om SAP-programlagret och DBMS-skiktet åtskiljs mellan två peer-datorer i virtuella nätverk.

Använd två virtuella datorer för din produktions-DBMS-distribution i en Azures tillgänglighets uppsättning. Använd också separat routning för SAP-programlagret och hanterings-och drifts trafik till de två virtuella DBMS-datorerna. Se följande bild:

![Diagram över två virtuella datorer i två undernät](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Använd Azure Load Balancer för att omdirigera trafik
Användningen av privata virtuella IP-adresser som används i funktioner som SQL Server Always on eller HANA System Replication kräver konfigurationen av en Azure Load Balancer. Belastningsutjämnaren använder avsöknings portar för att fastställa den aktiva DBMS-noden och dirigera trafiken enbart till den aktiva noden. 

Om det finns en redundansväxling av Database-noden behöver du inte konfigurera om SAP-programmet. I stället återansluter de vanligaste SAP-programarkitekturerna till den privata virtuella IP-adressen. Under tiden reagerar belastningsutjämnaren på nodens redundansväxling genom att dirigera om trafiken mot den privata virtuella IP-adressen till den andra noden.

Azure erbjuder två olika [SKU: er för belastnings utjämning](../../../load-balancer/load-balancer-overview.md): en grundläggande SKU och en standard-SKU. Om du inte vill distribuera i Azures tillgänglighets zoner fungerar den grundläggande belastnings Utjämnings-SKU: n.

Dirigeras trafiken mellan de virtuella datorerna i DBMS och SAP-programlagret alltid via belastningsutjämnaren. Svaret beror på hur du konfigurerar belastningsutjämnaren. 

För närvarande dirigeras inkommande trafik till den virtuella DBMS-datorn alltid via belastningsutjämnaren. Utgående trafik väg från den virtuella DBMS-datorn till den virtuella program nivån beror på konfigurationen av belastningsutjämnaren. 

Belastningsutjämnaren erbjuder ett alternativ för DirectServerReturn. Om det här alternativet har kon figurer ATS dirigeras *inte* trafiken från den virtuella datorns DBMS till SAP-program skiktet via belastningsutjämnaren. I stället går det direkt till program lagret. Om DirectServerReturn inte har kon figurer ATS dirigeras retur trafiken till SAP-program lagret via belastningsutjämnaren.

Vi rekommenderar att du konfigurerar DirectServerReturn i kombination med belastningsutjämnare som är placerade mellan SAP-program skiktet och DBMS-skiktet. Den här konfigurationen minskar nätverks fördröjningen mellan de två lagren.

Ett exempel på hur du konfigurerar den här konfigurationen med SQL Server Always on finns i [Konfigurera en ILB-lyssnare för Always on-tillgänglighetsgrupper i Azure](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Om du använder publicerade GitHub JSON-mallar som referens för dina distributioner av SAP-infrastruktur i Azure, kan du studera den här [mallen för ett SAP 3-nivåsystem](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). I den här mallen kan du också se rätt inställningar för belastningsutjämnaren.

### <a name="azure-accelerated-networking"></a>Azure-accelererat nätverk
För att ytterligare minska nätverks fördröjningen mellan virtuella Azure-datorer rekommenderar vi att du väljer [Azure accelererat nätverk](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Använd den när du distribuerar virtuella Azure-datorer för en SAP-arbetsbelastning, särskilt för SAP-program skiktet och SAP-DBMS-skiktet.

> [!NOTE]
> Alla typer av virtuella datorer har inte stöd för accelererat nätverk. I föregående artikel visas de VM-typer som har stöd för accelererat nätverk.
>

---
> ![Windows][Logo_Windows] Windows
>
> Information om hur du distribuerar virtuella datorer med accelererat nätverk för Windows finns i [skapa en virtuell Windows-dator med accelererat nätverk](../../../virtual-network/create-vm-accelerated-networking-powershell.md).
>
> ![Linux][Logo_Linux] Linux
>
> Mer information om Linux-distribution finns i [skapa en virtuell Linux-dator med accelererat nätverk](../../../virtual-network/create-vm-accelerated-networking-cli.md).
>
>

---

> [!NOTE]
> I fallet med SUSE, Red Hat och Oracle Linux stöds accelererat nätverk med de senaste versionerna. Äldre versioner som SLES 12 SP2 eller RHEL 7,2 stöder inte Azure-accelererat nätverk.
>


## <a name="deployment-of-host-monitoring"></a>Distribution av värd övervakning
För produktions användning av SAP-program i Azure Virtual Machines, kräver SAP möjligheten att hämta värd övervaknings data från de fysiska värdar som kör de virtuella Azure-datorerna. En speciell uppdaterings nivå för SAP-värd agent krävs som aktiverar den här funktionen i SAPOSCOL och SAP-värda Gent. Den exakta korrigerings nivån dokumenteras i SAP NOTE [1409604].

Mer information om hur du distribuerar komponenter som levererar värd data till SAPOSCOL och SAP-värda Gent och livs cykel hantering av dessa komponenter finns i [distributions guiden][deployment-guide].


## <a name="next-steps"></a>Nästa steg
Mer information om ett visst DBMS finns i:

- [DBMS-distribution för SAP-arbetsbelastning för SQL Server på Azure Virtual Machines](dbms_guide_sqlserver.md)
- [DBMS-distribution för SAP-arbetsbelastning för Oracle på Azure Virtual Machines](dbms_guide_oracle.md)
- [IBM DB2 Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_ibm.md)
- [DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines](dbms_guide_sapase.md)
- [SAP maxDB, Live cache och Content Server-distribution på Azure](dbms_guide_maxdb.md)
- [Användarguide för SAP HANA på Azure](hana-vm-operations.md)
- [SAP HANA hög tillgänglighet för virtuella Azure-datorer](sap-hana-availability-overview.md)
- [Säkerhets kopierings guide för SAP HANA på virtuella Azure-datorer](sap-hana-backup-guide.md)