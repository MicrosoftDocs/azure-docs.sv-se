---
title: Vanliga frågor om haveri beredskap för Hyper-V med Azure Site Recovery
description: I den här artikeln sammanfattas vanliga frågor om hur du konfigurerar haveri beredskap för lokala virtuella Hyper-V-datorer till Azure med hjälp av Azure Site Recovery-platsen.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 649bd69f14cdf8d81fe05d3a5f5cac3389419fc3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879452"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Vanliga frågor – Haveriberedskap för Hyper-V till Azure

Den här artikeln innehåller svar på vanliga frågor som vi ser vid replikering av lokala virtuella Hyper-V-datorer till Azure. 

## <a name="general"></a>Allmänt

### <a name="how-is-site-recovery-priced"></a>Hur kostar det att Site Recovery?
Granska [Azure Site Recovery pris](https://azure.microsoft.com/pricing/details/site-recovery/) information.

### <a name="how-do-i-pay-for-azure-vms"></a>Hur gör jag för att betala för virtuella Azure-datorer?
Under replikering replikeras data till Azure Storage och du betalar inga ändringar i virtuella datorer. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt virtuella Azure IaaS-datorer. När du har fakturerat för de beräknings resurser som du använder i Azure.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Finns det någon skillnad i kostnad vid replikering till Generell användning v2-lagrings konto?

Du kan vanligt vis se en ökning av de transaktioner som debiteras för GPv2-lagrings konton eftersom Azure Site Recovery är transaktioner tungt. [Läs mer](../storage/common/storage-account-upgrade.md#pricing-and-billing) om du vill uppskatta ändringen.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Vad behöver jag i Hyper-V för att dirigera replikering med Site Recovery?

Vad du behöver för Hyper-V-värdservern beror på distributionsscenariot. Du hittar kraven för Hyper-V i:

* [Replikera virtuella Hyper-V-datorer (utan VMM) till Azure](./hyper-v-azure-tutorial.md)
* [Replikera virtuella Hyper-V-datorer (med VMM) till Azure](./hyper-v-vmm-disaster-recovery.md)
* [Replikera virtuella Hyper-V-datorer till ett sekundärt datacenter](./hyper-v-vmm-disaster-recovery.md)
* Om du replikerar till ett sekundärt Data Center läser du om [gäst operativ system som stöds för virtuella Hyper-V-datorer](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/mt126277(v=ws.11)).
* Om du replikerar till Azure stöder Site Recovery alla gäst operativ system som [stöds av Azure](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10)).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan jag skydda virtuella datorer när Hyper-V körs på ett klient operativ system?
Nej, virtuella datorer måste finnas på en Hyper-V-värdserver som körs på en Windows-serverdator som stöds. Om du behöver skydda en klient dator kan du replikera den som en fysisk dator till [Azure](./vmware-azure-tutorial.md) eller ett [sekundärt Data Center](./vmware-physical-secondary-disaster-recovery.md).

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Behöver Hyper-V-värdar finnas i VMM-moln?
Om du vill replikera till ett sekundärt Data Center måste virtuella Hyper-V-datorer finnas på Hyper-V-värdar som finns i ett VMM-moln. Om du vill replikera till Azure kan du replikera virtuella datorer med eller utan VMM-moln. [Läs mer](./hyper-v-azure-tutorial.md) om Hyper-V-replikering till Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan jag replikera virtuella Hyper-V-datorer i generation 2 till Azure?
Ja. Site Recovery konverterar från generation 2 till generation 1 under redundansväxlingen. Vid återställning efter fel, konverteras datorn tillbaka till generation 2. [Läs mer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Kan jag distribuera Site Recovery med VMM om jag bara har en VMM-server?

Ja. Du kan antingen replikera virtuella datorer i Hyper-V-servrar i VMM-molnet till Azure, eller så kan du replikera mellan VMM-moln på samma server. Vi rekommenderar att du har en VMM-Server på både den primära och den sekundära platsen för lokal replikering. 

### <a name="what-do-i-need-in-azure"></a>Vad behöver jag i Azure?
Du behöver en Azure-prenumeration, ett Recovery Services valv, ett lagrings konto och ett virtuellt nätverk. Valvet, lagrings kontot och nätverket måste finnas i samma region.

### <a name="what-azure-storage-account-do-i-need"></a>Vilket Azure Storage-konto behöver jag?
Du behöver ett LRS-eller GRS-lagrings konto. Vi rekommenderar GRS så att dina data är flexibla i händelse av ett regionalt strömavbrott, eller om det inte går att återställa den primära regionen. Premium Storage stöds.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Behöver mitt Azure-konto behörigheter för att skapa virtuella datorer?
Om du är prenumerations administratör har du de replikeringsinställningar du behöver. Om du inte gör det måste du ha behörighet att skapa en virtuell Azure-dator i resurs gruppen och det virtuella nätverk som du anger när du konfigurerar Site Recovery och behörighet att skriva till det valda lagrings kontot. [Läs mer](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Skickas replikeringsdata till Site Recovery?
Nej, Site Recovery inte fångar upp replikerade data och har inte någon information om vad som körs på dina virtuella datorer. Replikeringsdata utbyts mellan Hyper-V-värdar och Azure Storage. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är ISO 27001:2013, 27018, HIPAA, DPA Certified och är i processen för SOC2-och FedRAMP JAB-utvärderingar.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Kan vi behålla lokala metadata inom en geografisk region?
Ja. När du skapar ett valv i en region ser vi till att alla metadata som används av Site Recovery ligger kvar inom regionens geografiska gränser.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
Ja, både kryptering under överföring och [kryptering i Azure](../storage/common/storage-service-encryption.md) stöds.


## <a name="deployment"></a>Distribution

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Vad kan jag göra med Hyper-V till Azure-replikering?

- **Haveri beredskap**: du kan ställa in fullständig haveri beredskap. I det här scenariot replikerar du lokala virtuella Hyper-V-datorer till Azure Storage:
    - Du kan replikera virtuella datorer till Azure. Om din lokala infrastruktur inte är tillgänglig växlar du över till Azure.
    - När du växlar över skapas virtuella Azure-datorer med hjälp av replikerade data. Du kan komma åt appar och arbets belastningar på virtuella Azure-datorer.
    - När ditt lokala data Center är tillgängligt igen kan du växla tillbaka från Azure till din lokala plats.
- **Migrering**: du kan använda Site Recovery för att migrera lokala virtuella Hyper-V-datorer till Azure Storage. Sedan växlar du över från en lokal plats till Azure. Efter redundansväxlingen är dina appar och arbets belastningar tillgängliga och körs på virtuella Azure-datorer.


### <a name="what-do-i-need-on-premises"></a>Vad behöver jag lokalt?

Du behöver en eller flera virtuella datorer som körs på en eller flera fristående eller klustrade Hyper-V-värdar. Du kan också replikera virtuella datorer som körs på värdar som hanteras av System Center Virtual Machine Manager (VMM).
- Om du inte kör VMM kan du samla in Hyper-V-värdar och-kluster på Hyper-V-platser under Site Recovery distribution. Du installerar Site Recovery agenter (Azure Site Recovery Provider och Recovery Services agent) på varje Hyper-V-värd.
- Om Hyper-V-värdar finns i ett VMM-moln dirigerar du replikeringen i VMM. Du installerar Site Recovery-providern på VMM-servern och Recovery Servicess agenten på varje Hyper-V-värd. Du mappar mellan logiska och virtuella dator nätverk i VMM och Azure-virtuella nätverk.
- [Läs mer](hyper-v-azure-architecture.md) om Hyper-V till Azure-arkitekturen.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Kan jag replikera virtuella datorer som finns i ett Hyper-V-kluster?

Ja, Site Recovery stöder klustrade Hyper-V-värdar. Tänk på följande:

- Alla noder i klustret ska registreras i samma valv.
- Om du inte använder VMM ska alla Hyper-V-värdar i klustret läggas till på samma Hyper-V-plats.
- Du installerar Azure Site Recovery Provider och Recovery Services agent på varje Hyper-V-värd i klustret och lägger till varje värd på en Hyper-V-plats.
- Inga speciella steg måste utföras i klustret.
- Om du kör verktyget Deployment Planner för Hyper-V samlar verktyget in profil data från noden som kör och var den virtuella datorn körs. Verktyget kan inte samla in data från en nod som är inaktive rad, men den kommer att spåra noden. När noden är igång börjar verktyget samla in de virtuella dator profil data från den (om den virtuella datorn är en del av den virtuella profil listan och körs på noden).
- Om en virtuell dator på en Hyper-V-värd i ett Site Recovery valv migreras till en annan Hyper-V-värd i samma kluster, eller till en fristående värd, påverkas inte replikeringen för den virtuella datorn. Hyper-V-värden måste uppfylla [nödvändiga komponenter](hyper-v-azure-support-matrix.md#on-premises-servers)och konfigureras i ett Site Recovery valv. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan jag skydda virtuella datorer när Hyper-V körs på ett klient operativ system?
Nej, virtuella datorer måste finnas på en Hyper-V-värdserver som körs på en Windows-serverdator som stöds. Om du behöver skydda en klient dator kan du [replikera den som en fysisk dator](physical-azure-disaster-recovery.md) till Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan jag replikera virtuella Hyper-V-datorer i generation 2 till Azure?
Ja. Site Recovery konverterar från generation 2 till generation 1 under redundansväxlingen. Vid återställning efter fel, konverteras datorn tillbaka till generation 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan jag automatisera Site Recovery scenarier med ett SDK?
Ja. Du kan automatisera Site Recovery-arbetsflöden med hjälp av REST-API:et, PowerShell och Azure SDK. Scenarier som stöds för närvarande för att replikera Hyper-V till Azure med hjälp av PowerShell:

- [Replikera Hyper-V utan VMM med hjälp av PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replikera Hyper-V med VMM med hjälp av PowerShell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replikering

### <a name="where-do-on-premises-vms-replicate-to"></a>Var replikeras lokala virtuella datorer till?
Data replikeras till Azure Storage. När du kör en redundansväxling skapar Site Recovery automatiskt virtuella Azure-datorer från lagrings kontot.

### <a name="what-apps-can-i-replicate"></a>Vilka appar kan jag replikera?
Du kan replikera alla appar eller arbets belastningar som kör en virtuell Hyper-V-dator som uppfyller [kraven på replikering](hyper-v-azure-support-matrix.md#replicated-vms). Site Recovery ger stöd för program medveten replikering, så att appar kan växlas över och växlas tillbaka till ett intelligent tillstånd. Site Recovery integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och fungerar nära ledande leverantörer, inklusive Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="whats-the-replication-process"></a>Vad är replikeringen?

1. När den inledande replikeringen utlöses tas en ögonblicks bild av virtuella Hyper-V-datorer.
2. Virtuella hård diskar på den virtuella datorn replikeras en i taget tills alla har kopierats till Azure. Detta kan ta en stund, beroende på storleken på den virtuella datorn och nätverks bandbredden. Lär dig hur du ökar nätverks bandbredden.
3. Om disk ändringar sker medan den inledande replikeringen pågår, spårar spårningen för Hyper-V-replikering ändringarna som Hyper-V-replikeringsinställningar (. HRL). Dessa loggfiler finns i samma mapp som diskarna. Varje disk har en associerad. HRL-fil som skickas till sekundär lagring. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den initiala replikeringen är klar tas VM-ögonblicksbilden bort.
5. Alla disk ändringar i loggen synkroniseras och sammanfogas till den överordnade disken.
6. När den inledande replikeringen har slutförts körs jobbet Slutför skydd på den virtuella datorn. Den konfigurerar nätverks-och andra inställningar efter replikering, så att den virtuella datorn skyddas.
7. I det här skedet kan du kontrol lera inställningarna för virtuella datorer för att kontrol lera att de är redo för redundans. Du kan köra en granskning av haveri beredskap (redundanstest) för den virtuella datorn för att kontrol lera att den växlar över som förväntat.
8. Efter den inledande replikeringen börjar delta-replikering, i enlighet med replikeringsprincipen.
9. Ändringarna är loggade. HRL-filer. Varje disk som är konfigurerad för replikering har en associerad .hrl-fil.
10. Loggen skickas till kundens lagrings konto. När en logg överförs till Azure spåras ändringarna på den primära disken i en annan loggfil i samma mapp.
11. Under både inledande och delta-replikering kan du övervaka den virtuella datorn i Azure Portal.

[Läs mer](hyper-v-azure-architecture.md#replication-process) om den replikerade processen.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Kan jag replikera till Azure med en plats-till-plats-VPN?

Site Recovery replikerar data från en lokal plats till Azure Storage över en offentlig slut punkt eller använder ExpressRoute Microsoft-peering. Replikering över ett VPN-nätverk för plats-till-plats stöds inte.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Kan jag replikera till Azure med ExpressRoute?

Ja, ExpressRoute kan användas för att replikera virtuella datorer till Azure. Site Recovery replikerar data till ett Azure Storage konto över en offentlig slut punkt och du måste konfigurera Microsoft- [peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) för Site Recovery replikering. När de virtuella datorerna har redundansväxlats till ett virtuellt Azure-nätverk kan du komma åt dem via [privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Varför kan jag inte replikera via VPN?

När du replikerar till Azure når replikeringstrafiken de offentliga slut punkterna för ett Azure Storage konto. Därför kan du bara replikera över det offentliga Internet med ExpressRoute (Microsoft-peering), och VPN fungerar inte. 

### <a name="what-are-the-replicated-vm-requirements"></a>Vilka är de replikerade VM-kraven?

För replikering måste en virtuell Hyper-V-dator köra ett operativ system som stöds. Dessutom måste den virtuella datorn uppfylla kraven för virtuella Azure-datorer. [Läs mer](hyper-v-azure-support-matrix.md#replicated-vms) i support mat ris.

### <a name="why-is-an-additional-standard-storage-account-required-if-i-replicate-my-virtual-machine-disks-to-premium-storage"></a>Varför krävs ytterligare ett standard lagrings konto om jag replikerar mina virtuella dator diskar till Premium Storage?

När du replikerar dina lokala virtuella datorer/fysiska servrar till Premium Storage, replikeras alla data på den skyddade datorns diskar till Premium Storage-kontot. Det krävs ytterligare ett standard lagrings konto för att lagra loggar för replikering. När den första fasen av replikeringen av disk data har slutförts spåras alla ändringar i de lokala disk data kontinuerligt och lagras som replikeringskonflikter i det här ytterligare standard lagrings kontot.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?

Virtuella Hyper-V-datorer kan replikeras var 30: e sekund (med undantag för Premium Storage) eller 5 minuter.

### <a name="can-azure-site-recovery-and-hyper-v-replica-be-configured-together-on-a-hyper-v-machine"></a>Kan Azure Site Recovery och Hyper-V-replikering konfigureras tillsammans på en Hyper-V-dator?

Ja, både Azure Site Recovery-och Hyper-V-replikering kan konfigureras tillsammans för en dator. Men datorn måste skyddas som en fysisk dator och kommer att replikeras till Azure med hjälp av en konfiguration/Processerver. Lär dig mer om att skydda fysiska datorer [här](./physical-azure-architecture.md).

### <a name="can-i-extend-replication"></a>Kan jag utöka replikeringen?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [feedback-forumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Kan jag göra en inledande replikering offline?
Det stöds inte. Begär den här funktionen i [feedback-forumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kan jag utesluta diskar?
Ja, du kan exkludera diskar från replikering. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar kan replikeras. Operativ system disken måste vara en standard disk.



## <a name="security"></a>Säkerhet

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Vilken åtkomst behöver Site Recovery för Hyper-V-värdar

Site Recovery behöver åtkomst till Hyper-V-värdar för att replikera de virtuella datorer som du väljer. Site Recovery installerar följande på Hyper-V-värdar:

- Om du inte kör VMM installeras Azure Site Recovery-providern och Recovery Services agenten på varje värd.
- Om du kör VMM installeras Recovery Services-agenten på varje värd. Providern körs på VMM-servern.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Vad ska Site Recovery installeras på virtuella Hyper-V-datorer?

Site Recovery installerar inte uttryckligen något på virtuella Hyper-V-datorer som är aktiverade för replikering.




## <a name="failover-and-failback"></a>Redundans och återställning efter fel


### <a name="how-do-i-fail-over-to-azure"></a>Hur gör jag för att redundans till Azure?

Du kan köra en planerad eller oplanerad redundansväxling från lokala Hyper-V virtuella datorer till Azure.

- Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade.
- Du kan köra en oplanerad redundansväxling om den primära platsen inte är tillgänglig.
- Du kan redundansväxla en enskild dator eller skapa återställningsplaner för att samordna redundans för flera datorer.
- Redundansväxlingen är i två delar:
    - När det första steget i redundansväxlingen har slutförts bör du kunna se de skapade virtuella replik datorerna i Azure. Du kan tilldela en offentlig IP-adress till datorn om det behövs.
    - Sedan genomför du redundansväxlingen och startar åtkomst till arbets belastningen från den virtuella repliken Azure-datorn.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hur gör jag för att åtkomst till virtuella Azure-datorer efter redundans?
Efter redundansväxlingen kan du komma åt virtuella Azure-datorer via en säker Internet anslutning via en VPN-anslutning från plats till plats eller via Azure ExpressRoute. Du måste förbereda ett antal saker för att kunna ansluta. [Läs mer](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>Har redundansväxlats data redundansväxlats?
Azure är utformat med flexibilitet i fokus. Site Recovery är utformad för redundans till ett sekundärt Azure-datacenter, i enlighet med Azure SLA. När redundans inträffar ser vi till att dina metadata och valv finns kvar inom samma geografiska region som du valde för ditt valv.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
[Redundans](site-recovery-failover.md) är inte automatiskt. Du initierar redundans med ett enda klick i portalen, eller så kan du använda [PowerShell](/powershell/module/az.recoveryservices) för att utlösa en redundansväxling.

### <a name="how-do-i-fail-back"></a>Hur gör jag för att återställning igen?

När din lokala infrastruktur är igång igen kan du växla tillbaka. Återställning efter fel sker i tre steg:

1. Du kan starta en planerad redundansväxling från Azure till den lokala platsen med ett par olika alternativ:

    - Minimera nedtid: om du använder det här alternativet Site Recovery synkroniserar data före redundans. Den söker efter ändrade data block och laddar ned dem till den lokala platsen, medan den virtuella Azure-datorn fortsätter att köras, vilket minimerar stillestånds tiden. När du anger att redundansväxlingen ska slutföras manuellt stängs den virtuella Azure-datorn, eventuella ändringar i ändringarna kopieras och redundansväxlingen startar.
    - Fullständig nedladdning: med dessa alternativ data synkroniseras under redundansväxlingen. Med det här alternativet hämtas hela disken. Det är snabbare eftersom inga kontroll summor beräknas, men det finns mer stillestånds tid. Använd det här alternativet om du har kört replikeringen av virtuella Azure-datorer under en tid, eller om den lokala virtuella datorn har tagits bort.

2. Du kan välja att växla tillbaka till samma virtuella dator eller till en annan virtuell dator. Du kan ange att Site Recovery ska skapa den virtuella datorn om den inte redan finns.
3. När den första synkroniseringen är klar väljer du att slutföra redundansväxlingen. När den är klar kan du logga in på den lokala virtuella datorn för att kontrol lera att allt fungerar som förväntat. I Azure Portal kan du se att de virtuella Azure-datorerna har stoppats.
4. Du genomför redundansväxlingen så att den slutförs och börjar komma åt arbets belastningen från den lokala virtuella datorn igen.
5. När arbets belastningarna har återställts aktiverar du omvänd replikering så att de lokala virtuella datorerna replikeras till Azure igen.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan jag växla tillbaka till en annan plats?
Ja, om du har växlat till Azure kan du växla tillbaka till en annan plats om den ursprungliga inte är tillgänglig. [Läs mer](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).