---
title: Vanliga frågor om katastrof återställning i VMware med Azure Site Recovery
description: Få svar på vanliga frågor om haveri beredskap för lokala virtuella VMware-datorer till Azure med hjälp av Azure Site Recovery.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: a272486eea111ab8c8e489556986f12f382e3f65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587800"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Vanliga frågor om replikering av VMware till Azure

I den här artikeln besvaras vanliga frågor som kan uppstå när du distribuerar haveri beredskap för lokala virtuella VMware-datorer till Azure.

## <a name="general"></a>Allmänt

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Vad behöver jag för haveri beredskap för virtuella VMware-datorer?

[Läs om de komponenter som ingår](vmware-azure-architecture.md) i haveri beredskap för virtuella VMware-datorer.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Kan jag använda Site Recovery för att migrera virtuella VMware-datorer till Azure?

Ja. Förutom att använda Site Recovery för att konfigurera fullständig haveri beredskap för virtuella VMware-datorer kan du också använda Site Recovery för att migrera lokala virtuella VMware-datorer till Azure. I det här scenariot replikerar du lokala virtuella VMware-datorer till Azure Storage. Sedan växlar du över från en lokal plats till Azure. Efter redundansväxlingen är dina appar och arbets belastningar tillgängliga och körs på virtuella Azure-datorer. Processen är som att konfigurera fullständig haveri beredskap, förutom att i en migrering inte kan återställas från Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Behöver mitt Azure-konto behörigheter för att skapa virtuella datorer?

Om du är prenumerations administratör har du de replikeringsinställningar du behöver. Om du inte är administratör måste du ha behörighet att vidta följande åtgärder:

- Skapa en virtuell Azure-dator i resurs gruppen och det virtuella nätverk som du anger när du konfigurerar Site Recovery.
- Skriv till det valda lagrings kontot eller den hanterade disken utifrån din konfiguration.

[Läs mer](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) om de behörigheter som krävs.

### <a name="what-applications-can-i-replicate"></a>Vilka program kan jag replikera?

Du kan replikera alla appar eller arbets belastningar som körs på en virtuell VMware-dator som uppfyller [kraven för replikering](vmware-physical-azure-support-matrix.md#replicated-machines).

- Site Recovery stöder program medveten replikering, så att appar kan växlas över och växlas tillbaka till ett intelligent tillstånd.
- Site Recovery integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory. Det fungerar också nära ledande leverantörer, inklusive Oracle, SAP, IBM och Red Hat.

[Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Kan jag använda en licens för en gäst operativ system server på Azure?

Ja, Microsoft Software Assurance-kunder kan använda [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) för att spara licens kostnader för Windows Server-datorer som migreras till Azure, eller för att använda Azure för haveri beredskap.

## <a name="security"></a>Säkerhet

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Vilken åtkomst till VMware-servrar behöver Site Recovery?

Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Konfigurera en virtuell VMware-dator som kör Site Recovery konfigurations servern.
- Identifiera virtuella datorer automatiskt för replikering.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Vilken åtkomst till virtuella VMware-datorer behöver Site Recovery?

- För att replikera en virtuell VMware-dator måste tjänsten Site Recovery mobilitet vara installerad och igång. Du kan distribuera verktyget manuellt eller så kan du ange att Site Recovery göra en push-installation av tjänsten när du aktiverar replikering för en virtuell dator.
- Under replikeringen kommunicerar virtuella datorer med Site Recovery enligt följande:
    - Virtuella datorer kommunicerar med konfigurations servern på HTTPS-port 443 för hantering av replikering.
    - Virtuella datorer skickar replikeringsdata till processervern på HTTPS-port 9443. (Den här inställningen kan ändras.)
    - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar de virtuella datorerna med varandra via port 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Skickas replikeringsdata till Site Recovery?

Nej, Site Recovery inte fångar replikerade data och har inte någon information om vad som körs på dina virtuella datorer. Replikeringsdata utbyts mellan VMware-hypervisorer och Azure Storage. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.

Site Recovery är certifierat för ISO 27001:2013 och 27018, HIPAA och DPA. Det håller på att utvärdera SOC2 och FedRAMP JAB.

## <a name="pricing"></a>Priser

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Hur gör jag för att beräkna ungefärliga debiteringar för katastrof återställning i VMware?

Använd [pris kalkylatorn](https://aka.ms/asr_pricing_calculator) för att beräkna kostnaderna när du använder Site Recovery.

Om du vill ha en detaljerad uppskattning av kostnaderna kör du verktyget Deployment Planner för [VMware](./site-recovery-deployment-planner.md) och använder [kostnads uppskattnings rapporten](./site-recovery-vmware-deployment-planner-cost-estimation.md).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Kostar det någon skillnad mellan att replikera till lagring eller direkt till hanterade diskar?

Hanterade diskar debiteras något annorlunda än lagrings konton. [Läs mer](https://azure.microsoft.com/pricing/details/managed-disks/) om priser för hanterad disk.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Finns det någon skillnad i kostnad vid replikering till Generell användning v2-lagrings konto?

Du kan vanligt vis se en ökning av de transaktioner som debiteras för GPv2-lagrings konton eftersom Azure Site Recovery är transaktioner tungt. [Läs mer](../storage/common/storage-account-upgrade.md#pricing-and-billing) om du vill uppskatta ändringen.

## <a name="mobility-service"></a>Mobilitetstjänsten

### <a name="where-can-i-find-the-mobility-service-installers"></a>Var kan jag hitta installations program för mobilitets tjänsten?

Installations programen finns i mappen%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository på konfigurations servern.

## <a name="how-do-i-install-the-mobility-service"></a>Hur gör jag för att du installera mobilitets tjänsten?

På varje virtuell dator som du vill replikera installerar du tjänsten på något av flera sätt:

- [Push-installation](vmware-physical-mobility-service-overview.md#push-installation)
- [Manuell installation](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui) från användar gränssnittet eller PowerShell
- Distribution med hjälp av ett distributions verktyg som [Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Hanterade diskar

### <a name="where-does-site-recovery-replicate-data-to"></a>Var replikerar Site Recovery data till?

Site Recovery replikerar lokala virtuella VMware-datorer och fysiska servrar till hanterade diskar i Azure.

- Site Recovery processervern skriver loggar till ett cache Storage-konto i mål regionen.
- De här loggarna används för att skapa återställnings punkter på Azure-hanterade diskar som har prefixet **asrseeddisk**.
- När redundans inträffar används den återställnings punkt som du väljer för att skapa en ny mål hanterad disk. Den här hanterade disken är ansluten till den virtuella datorn i Azure.
- Virtuella datorer som tidigare har repliker ATS till ett lagrings konto (före mars 2019) påverkas inte.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Kan jag replikera nya datorer till lagrings konton?

Nej. Från och med mars 2019 kan du bara replikera till Azure Managed disks i Azure Portal.

Det går bara att replikera nya virtuella datorer till ett lagrings konto med hjälp av PowerShell ([AZ. RecoveryServices module version 1.4.5](https://www.powershellgallery.com/packages/Az.RecoveryServices/1.4.5)) eller REST API (version 2018-01-10 eller 2016-08-10). [Lär dig](./vmware-azure-disaster-recovery-powershell.md) att konfigurera replikering med hjälp av PowerShell-kommandon.

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Vilka är fördelarna med att replikera till Managed disks?

[Lär dig hur](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery fören klar haveri beredskap med hanterade diskar.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Kan jag ändra typen av hanterad disk efter att datorn skyddas?

Ja, du kan enkelt [ändra vilken typ av hanterad disk](../virtual-machines/windows/convert-disk-storage.md) som ska användas för pågående replikeringar. Innan du ändrar typen måste du se till att ingen URL för signatur för delad åtkomst genereras på den hanterade disken:

1. Gå till resursen för **hanterade diskar** på Azure Portal och kontrol lera om du har en URL-banderoll för signatur för delad åtkomst på bladet **Översikt** .
1. Om banderollen finns väljer du den för att avbryta den pågående exporten.
1. Ändra disk typen inom några minuter. Om du ändrar den hanterade disk typen väntar du tills de nya återställnings punkterna genereras av Azure Site Recovery.
1. Använd de nya återställnings punkterna för redundanstest eller redundans i framtiden.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Kan jag byta replikering från hanterade diskar till ohanterade diskar?

Nej. Det finns inte stöd för att växla från hanterade till ohanterad.

## <a name="replication"></a>Replikering

### <a name="what-are-the-replicated-vm-requirements"></a>Vilka är de replikerade VM-kraven?

[Läs mer](vmware-physical-azure-support-matrix.md#replicated-machines) om support krav för virtuella VMware-datorer och fysiska servrar.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?

Replikering är kontinuerlig när du replikerar virtuella VMware-datorer till Azure.

### <a name="can-i-extend-replication"></a>Kan jag utöka replikeringen?

Utökad eller länkad replikering stöds inte. Begär den här funktionen i [feedback-forumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="how-can-i-track-progress-of-initial-replicationsynchronization"></a>Hur kan jag spåra förloppet för den inledande replikeringen/synkroniseringen?

Den här funktionen har nyligen Site Recovery tjänster. Uppdatera Site Recovery-infrastrukturen (konfigurations servrar, skalbara process servrar) och mobilitets agenten till version 9,36 eller högre för att få korrekt information. Läs mer om hur du spårar förloppet [här](vmware-azure-enable-replication.md#monitor-initial-replication).

### <a name="can-i-do-an-offline-initial-replication"></a>Kan jag göra en inledande replikering offline?

Offline-replikering stöds inte. Begär den här funktionen i [feedback-forumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>Vad är asrseeddisk?

För varje käll disk replikeras data till en hanterad disk i Azure. Den här disken har prefixet **asrseeddisk**. Den lagrar kopian av käll disken och alla ögonblicks bilder av återställnings punkter.

### <a name="can-i-exclude-disks-from-replication"></a>Kan jag utesluta diskar från replikering?

Ja, du kan utesluta diskar.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Kan jag replikera virtuella datorer som har dynamiska diskar?

Dynamiska diskar kan replikeras. Operativ system disken måste vara en standard disk.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Kan jag lägga till en ny virtuell dator i en befintlig replikeringsgrupp om jag använder replikeringsgrupper för konsekvens för flera virtuella datorer?

Ja, du kan lägga till nya virtuella datorer i en befintlig replikeringsgrupp när du aktiverar replikering för dem. Ändå

- Du kan inte lägga till en virtuell dator i en befintlig replikeringsgrupp efter att replikeringen har påbörjats.
- Du kan inte skapa en replikeringsgrupp för befintliga virtuella datorer.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Kan jag ändra virtuella datorer som replikeras genom att lägga till eller ändra storlek på diskar?

För VMware-replikering till Azure kan du ändra disk storleken för virtuella käll datorer. Om du vill lägga till nya diskar måste du lägga till disken och återaktivera skyddet för den virtuella datorn.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Kan jag migrera lokala datorer till en ny vCenter Server utan att påverka pågående replikering?

Se vår [vägledning](vmware-azure-manage-vcenter.md#migrate-all-vms-to-a-new-server) för att migrera datorer till en ny vCenter

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Kan jag replikera till ett cache-eller mål lagrings konto som har ett virtuellt nätverk (med Azure-brandväggar) konfigurerat?

Nej, Site Recovery stöder inte replikering till Azure Storage i virtuella nätverk.

### <a name="what-is-the-frequency-of-generation-of-crash-consistent-recovery-points"></a>Vad är frekvensen för generering av kraschbaserade återställnings punkter?

Site Recovery genererar kraschbaserade återställnings punkter var femte minut.

## <a name="component-upgrade"></a>Komponent uppgradering

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Min version av mobilitets tjänst agenten eller konfigurations servern är gammal och uppgraderingen misslyckades. Vad gör jag nu?

Site Recovery följer support modellen för N-4. [Läs mer](./service-updates-how-to.md#support-statement-for-azure-site-recovery) om hur du uppgraderar från mycket gamla versioner.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Var hittar jag viktig information och Samlad uppdatering för Azure Site Recovery?

[Lär dig mer om nya uppdateringar](site-recovery-whats-new.md)och [Hämta information om sammanslagning](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Var hittar jag uppgraderings information för haveri beredskap till Azure?

[Lär dig mer om att uppgradera](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Måste jag starta om käll datorerna för varje uppgradering?

En omstart rekommenderas men är inte obligatorisk för varje uppgradering. [Läs mer](./service-updates-how-to.md#reboot-after-mobility-service-upgrade).

## <a name="configuration-server"></a>Konfigurationsserver

### <a name="what-does-the-configuration-server-do"></a>Vad gör konfigurations servern?

Konfigurations servern kör lokala Site Recovery-komponenter, inklusive:

- Själva konfigurations servern. Servern samordnar kommunikationen mellan lokala komponenter och Azure och hanterar datareplikering.
- Processervern som fungerar som en gateway för replikering. Den här servern:
    1. Tar emot replikeringsdata.
    2. Optimerar data med cachelagring, komprimering och kryptering.
    3. Skickar data till Azure Storage.
  Processervern installerar också en push-installation av mobilitets tjänsten på virtuella datorer och utför automatisk identifiering av lokala virtuella VMware-datorer.
- Huvud mål servern, som hanterar replikeringsdata under återställning efter fel från Azure.

[Läs mer](vmware-azure-architecture.md) om konfigurations serverns komponenter och processer.

### <a name="where-do-i-set-up-the-configuration-server"></a>Var konfigurerar jag konfigurations servern?

Du behöver en enda, lokal VMware VM för konfigurations servern med hög tillgänglighet. För haveri beredskap för fysiska servrar installerar du konfigurations servern på en fysisk dator.

### <a name="what-do-i-need-for-the-configuration-server"></a>Vad behöver jag för konfigurations servern?

Granska [kraven](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kan jag konfigurera konfigurations servern manuellt i stället för att använda en mall?

Vi rekommenderar att du [skapar den virtuella datorns konfigurations Server](vmware-azure-deploy-configuration-server.md) med den senaste versionen av Open Virtualization format-mallen (OVF). Om du inte kan använda mallen (till exempel om du inte har åtkomst till VMware-servern) laddar du [ned](physical-azure-set-up-source.md) installations filen från portalen och konfigurerar konfigurations servern.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Kan en konfigurations Server replikera till fler än en region?

Nej. Om du vill replikera till mer än en region behöver du en konfigurations server i varje region.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Kan jag vara värd för en konfigurations server i Azure?

Även om det är möjligt måste den virtuella Azure-datorn som kör konfigurations servern kommunicera med din lokala VMware-infrastruktur och virtuella datorer. Den här kommunikationen lägger till latens och påverkar pågående replikering.

### <a name="how-do-i-update-the-configuration-server"></a>Hur gör jag för att uppdatera konfigurations servern?

[Lär dig](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) hur du uppdaterar konfigurations servern.

- Du hittar den senaste uppdaterings informationen på [sidan med Azure-uppdateringar](https://azure.microsoft.com/updates/?product=site-recovery).
- Du kan ladda ned den senaste versionen från portalen. Eller så kan du ladda ned den senaste versionen av konfigurations servern direkt från [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Om din version är mer än fyra versioner som är äldre än den aktuella versionen, se [support policyn](./service-updates-how-to.md#support-statement-for-azure-site-recovery) för uppgraderings vägledning.

### <a name="should-i-back-up-the-configuration-server"></a>Bör jag säkerhetskopiera konfigurations servern?

Vi rekommenderar att du vidtar regelbundna schemalagda säkerhets kopieringar av konfigurations servern.

- För lyckad återställning efter fel måste den virtuella datorn som återställs finnas i konfigurations Server databasen.
- Konfigurations servern måste köras och vara i anslutet tillstånd.
- [Läs mer](vmware-azure-manage-configuration-server.md) om vanliga hanterings uppgifter för konfigurations servrar.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Kan jag hämta och installera MySQL manuellt när jag konfigurerar konfigurations servern?

Ja. Hämta MySQL och placera det i mappen C:\Temp\ASRSetup Installera den manuellt. När du ställer in konfigurations serverns virtuella dator och accepterar villkoren, visas MySQL som **redan är installerat** vid **nedladdning och installation**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kan jag undvika att hämta MySQL men låta Site Recovery installera det?

Ja. Hämta MySQL-installationsprogrammet och placera det i mappen C:\Temp\ASRSetup När du ställer in konfigurations serverns virtuella dator accepterar du villkoren och väljer **Hämta och installera**. Portalen kommer att använda installations programmet som du har lagt till för att installera MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Kan jag använda den virtuella datorns konfigurations Server för något annat?

Nej. Använd endast den virtuella datorn för konfigurations servern.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Kan jag klona en konfigurations Server och använda den för dirigering?

Nej. Konfigurera en ny konfigurations Server för att undvika registrerings problem.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Kan jag ändra valvet som konfigurations servern är registrerad i?

Nej. När ett valv är associerat med konfigurations servern kan det inte ändras. [Lär dig mer](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) om att registrera en konfigurations server med ett annat valv.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Kan jag använda samma konfigurations Server för haveri beredskap för både virtuella VMware-datorer och fysiska servrar?

Ja, men Observera att den fysiska datorn inte kan återställas till en virtuell VMware-dator.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Var kan jag hämta lösen frasen för konfigurations servern?

[Lär dig](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) hur du hämtar lösen frasen.

### <a name="where-can-i-download-vault-registration-keys"></a>Var kan jag hämta valv registrerings nycklar?

I Recovery Services-valvet väljer du **konfigurations servrar** i **Site Recovery infrastruktur**  >  **hantering**. I **servrar** väljer du sedan **Hämta registrerings nyckel** för att ladda ned valv filen med autentiseringsuppgifter.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Kan en enda konfigurations Server användas för att skydda flera vCenter-instanser?

Ja, en enda konfigurations Server kan skydda virtuella datorer över flera vCenter.  Det finns inte någon gräns för hur många vCenter-instanser som kan läggas till i konfigurations servern, men gränserna för hur många virtuella datorer en enda konfigurations Server kan skydda gäller.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Kan en enda konfigurations Server skydda flera kluster i vCenter?

Ja, Azure Site Recovery kan skydda virtuella datorer i olika kluster.

## <a name="process-server"></a>Processerver

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Varför kan jag inte välja processervern när jag aktiverar replikering?

Uppdateringar i version 9,24 och senare visar nu [hälsan för processervern när du aktiverar replikering](vmware-azure-enable-replication.md#enable-replication). Den här funktionen hjälper till att undvika begränsning av process Server och minimera användningen av servrar med felaktig bearbetning.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Vill du Hur gör jag för att uppdatera processervern till version 9,24 eller senare för att få korrekt hälso information?

Från och med [version 9,24](service-updates-how-to.md#links-to-currently-supported-update-rollups)har fler aviseringar lagts till för att indikera hälso tillståndet för processervern. [Uppdatera Site Recovery-komponenter till version 9,24 eller senare](service-updates-how-to.md#links-to-currently-supported-update-rollups) så att alla aviseringar genereras.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Hur kan jag garantera hög tillgänglighet för processervern?

Genom att konfigurera fler än en processerver ger designen flexibilitet att flytta skyddade datorer från en skadad processerver till arbets process servern. Förflyttning av en dator från en processerver till en annan måste initieras uttryckligen/manuellt via de definierade stegen här: [flytta virtuella datorer mellan process servrar](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

## <a name="failover-and-failback"></a>Redundans och återställning efter fel

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Kan jag använda den lokala processervern för återställning efter fel?

Vi rekommenderar starkt att du skapar en processerver i Azure för återställning efter fel för att undvika data överförings fördröjningar. Dessutom är det viktigt att använda processervern som skapats i Azure för återställning efter fel, om du har separerat det virtuella käll dator nätverket med Azure-nätverket på konfigurations servern.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Kan jag behålla IP-adressen vid redundans?

Ja, du kan behålla IP-adressen vid redundans. Se till att du anger mål-IP-adressen i **beräknings-och nätverks** inställningarna för den virtuella datorn före redundansväxlingen. Stäng också av datorerna vid redundansväxling för att undvika IP-adress konflikter under återställning efter fel.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Kan jag ändra storlek på virtuell måldator eller virtuell dator typ före redundans?

Ja, du kan ändra den virtuella datorns typ eller storlek när som helst före redundansväxlingen. I portalen använder du **beräknings-och nätverks** inställningarna för den REPLIKERADE virtuella datorn.

### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?

Den äldsta återställnings punkten för VMware till Azure kan du använda 72 timmar.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hur gör jag för att åtkomst till virtuella Azure-datorer efter redundans?

Efter redundansväxlingen kan du komma åt virtuella Azure-datorer via en säker Internet anslutning via en VPN-anslutning från plats till plats eller via Azure ExpressRoute. För att ansluta måste du förbereda flera saker. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Är det fel på data som är elastiska?

Azure är utformat med flexibilitet i fokus. Site Recovery är utformad för redundans till ett sekundärt Azure-datacenter, enligt vad som krävs i Azure service avtal (SLA). När redundans inträffar ser vi till att dina metadata och valv finns kvar i samma geografiska region som du valde för ditt valv.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?

[Redundans](site-recovery-failover.md) är inte automatiskt. Du startar en redundansväxling genom att göra ett val i portalen, eller så kan du använda [PowerShell](/powershell/module/az.recoveryservices) för att utlösa en redundansväxling.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan jag växla tillbaka till en annan plats?

Ja. Om du har växlat till Azure kan du växla tillbaka till en annan plats om den ursprungliga inte är tillgänglig. [Läs mer](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Varför behöver jag en VPN-eller ExpressRoute med privat peering för att återställa igen?

När du växlar tillbaka från Azure kopieras data från Azure tillbaka till din lokala virtuella dator och privat åtkomst krävs.


## <a name="automation-and-scripting"></a>Automatisering och skript

### <a name="can-i-set-up-replication-with-scripting"></a>Kan jag konfigurera replikering med skript?

Ja. Du kan automatisera Site Recovery arbets flöden med hjälp av REST API, PowerShell eller Azure SDK. [Läs mer](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestanda och kapacitet

### <a name="can-i-throttle-replication-bandwidth"></a>Kan jag begränsa replikeringens bandbredd?

Ja. [Läs mer](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Nästa steg

- [Granska](vmware-physical-azure-support-matrix.md) support kraven.
- [Konfigurera](vmware-azure-tutorial.md) VMware till Azure-replikering.