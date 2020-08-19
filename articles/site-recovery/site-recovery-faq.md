---
title: Allmänna frågor om tjänsten Azure Site Recovery
description: I den här artikeln beskrivs populära allmänna frågor om Azure Site Recovery.
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 8b5730fba1a0267ab72497bc65b51de75654f970
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263392"
---
# <a name="general-questions-about-azure-site-recovery"></a>Allmänna frågor om Azure Site Recovery

I den här artikeln sammanfattas vanliga frågor om Azure Site Recovery. Granska de här artiklarna för vissa scenarier

- [Frågor om haveri beredskap för virtuella Azure-datorer till Azure](azure-to-azure-common-questions.md)
- [Frågor om haveri beredskap för virtuella VMware-datorer till Azure](vmware-azure-common-questions.md)
- [Frågor om haveri beredskap för virtuella Hyper-V-datorer till Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Allmänt

### <a name="what-does-site-recovery-do"></a>Vad gör Site Recovery?

Site Recovery bidrar till din strategi för affärs kontinuitet och haveri beredskap (BCDR) genom att dirigera och automatisera replikeringen av virtuella Azure-datorer mellan regioner, lokala virtuella datorer och fysiska servrar till Azure och lokala datorer till ett sekundärt Data Center. [Läs mer](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Kan jag skydda en virtuell dator som har en Docker-disk?

Nej, det här är ett scenario som inte stöds.

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>Vad gör Site Recovery för att säkerställa data integriteten?

Det finns olika åtgärder som vidtas av Site Recovery för att säkerställa data integriteten. En säker anslutning upprättas mellan alla tjänster med hjälp av HTTPS-protokollet. Detta säkerställer att alla skadlig kod eller utanför entiteter inte kan manipulera data. Ett annat mått har gjorts med hjälp av kontroll summor. Data överföringen mellan källa och mål utförs genom beräkning av data kontroll summor mellan dem. Detta säkerställer att överförda data är konsekventa.

## <a name="service-providers"></a>Tjänst leverantörer

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jag är en tjänste leverantör. Fungerar Site Recovery för dedikerade och delade infrastruktur modeller?
Ja, Site Recovery stöder både dedikerade och delade infrastrukturmodeller.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Är identiteten för min klient som delas med Site Recovery tjänsten för en tjänst leverantör?
Nej. Klient identiteten är fortfarande anonym. Klienterna behöver inte åtkomst till Site Recovery-portalen. Endast tjänstproviderns administratör interagerar med portalen.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Kommer klient program data någonsin att gå till Azure?
När du replikerar mellan platser som ägs av tjänstproviders hamnar programdata aldrig hos Azure. Data krypteras under överföringen och replikeras direkt mellan tjänste leverantörs platserna.

Om du replikera till Azure skickas programdata till Azure-lagring men inte till Site Recovery-tjänsten. Data krypteras under överföringen och förblir krypterade i Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Får mina klienter en faktura för några Azure-tjänster?
Nej. Azures faktureringsrelation sker direkt med tjänstprovidern. Tjänstprovidern ansvarar för att skapa fakturor för sina klienter.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Måste jag alltid köra virtuella datorer i Azure om jag replikerar till Azure?
Nej, data replikeras till Azure Storage i din prenumeration. När du utför ett redundanstest (DR-test) eller en riktig redundansväxling skapar Site Recovery automatiskt virtuella datorer i din prenumeration.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garanterar ni isolering på klientnivå när jag replikerar till Azure?
Ja.

### <a name="what-platforms-do-you-currently-support"></a>Vilka plattformar stöds för närvarande?
Vi har stöd för Azure-paket, moln plattforms system och System Center-baserade (2012 och högre) distributioner. [Läs mer](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) om Azure-paket och Site Recovery-integrering.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Finns det stöd för distributioner med ett enda Azure Pack och en enda VMM-server?
Ja, du kan replikera virtuella Hyper-V-datorer till Azure, eller mellan Service Provider-platser.  Observera att Azure Runbook-integrering inte är tillgängligt om du replikerar mellan platser för tjänst leverantörer.

## <a name="pricing"></a>Prissättning

### <a name="where-can-i-find-pricing-information"></a>Var kan jag hitta pris information?
Granska [Site Recovery pris](https://azure.microsoft.com/pricing/details/site-recovery/) information.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Hur kan jag beräkna ungefärliga avgifter vid användning av Site Recovery?

Du kan använda [pris kalkylatorn](https://aka.ms/asr_pricing_calculator) för att beräkna kostnader när du använder Site Recovery.

Om du vill ha en detaljerad uppskattning av kostnader kör du verktyget Deployment Planner för [VMware](https://aka.ms/siterecovery_deployment_planner) eller [Hyper-V](https://aka.ms/asr-deployment-planner)och använder [rapporten kostnads uppskattning](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Hanterade diskar används nu för att replikera virtuella VMware-datorer och fysiska servrar. Debiteras jag ytterligare avgifter för cache Storage-kontot med Managed disks?

Nej, det finns inga ytterligare avgifter för cachelagring. När du replikerar till ett standard lagrings konto är denna cache del av samma mål lagrings konto.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Jag har varit Azure Site Recovery användare under en månad. Får jag fortfarande de första 31 dagarna kostnads fritt för varje skyddad instans?

Ja. Alla skyddade instanser debiteras inga Azure Site Recovery avgifter under de första 31 dagarna. Om du till exempel har skyddat 10 instanser under de senaste 6 månaderna och ansluter en 11: e instans till Azure Site Recovery, finns det inga avgifter för den 11: e instansen under de första 31 dagarna. De första 10 instanserna fortsätter att ådra sig Azure Site Recovery avgifter eftersom de har skyddats i mer än 31 dagar.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Under de första 31 dagarna kommer jag att debiteras andra Azure-avgifter?

Ja, även om Site Recovery är kostnads fritt under de första 31 dagarna i en skyddad instans kan du debiteras för Azure Storage, lagrings transaktioner och data överföring. En återställd virtuell dator kan också ådra sig Azure Compute-avgifter.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Är det en kostnad som är kopplad till att utföra haveri beredskap/redundanstest?

Det finns ingen separat kostnad för katastrof granskning. Det kommer att finnas beräknings avgifter när den virtuella datorn har skapats efter redundanstest.



## <a name="security"></a>Säkerhet

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Skickas replikeringsdata till Site Recovery-tjänsten?
Nej, Site Recovery inte fångar upp replikerade data och har inte någon information om vad som körs på dina virtuella datorer eller fysiska servrar.
Replikeringsdata utbyts mellan lokala Hyper-V-värdar, VMware-hypervisorer eller fysiska servrar och Azure-lagring eller din sekundära plats. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är ISO 27001:2013, 27018, HIPAA, DPA Certified och är i processen för SOC2-och FedRAMP JAB-utvärderingar.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Av kompatibilitetsskäl måste även våra lokala metadata finnas kvar i samma geografiska region. Kan Site Recovery hjälpa oss?
Ja. När du skapar ett Site Recovery valv i en region ser vi till att alla metadata som vi behöver för att aktivera och dirigera replikering och redundans kvarstår inom regionens geografiska gränser.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
För virtuella datorer och fysiska servrar stöds replikering mellan lokala platser vid kryptering under överföring. För virtuella datorer och fysiska servrar som replikeras till Azure stöds både kryptering under överföring och [kryptering vid vila (i Azure)](../storage/common/storage-service-encryption.md) .

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Använder Azure-till-Azure Site Recovery TLS 1,2 för all kommunikation mellan mikrotjänster i Azure?
Ja, TLS 1,2-protokollet tillämpas som standard för Azure-till-Azure Site Recovery-scenario. 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>Hur kan jag använda TLS 1,2 på VMware-till-Azure och fysiska server-till-Azure Site Recovery-scenarier?
Mobilitets agenter som är installerade på replikerade objekt kommunicerar endast med processervern på TLS 1,2. Kommunikationen från konfigurations servern till Azure och från processervern till Azure kan dock finnas i TLS 1,1 eller 1,0. Följ [anvisningarna för att](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) genomdriva TLS 1,2 på alla konfigurations servrar och process servrar som du har skapat.

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>Hur kan jag använda TLS 1,2 på HyperV-to-Azure Site Recovery-scenarier?
All kommunikation mellan mikrotjänster för Azure Site Recovery sker på TLS 1,2-protokollet. Site Recovery använder säkerhetsproviders som kon figurer ATS i systemet (OS) och använder det senaste tillgängliga TLS-protokollet. En måste uttryckligen Aktivera TLS 1,2 i registret och sedan börjar Site Recovery använda TLS 1,2 för kommunikation med tjänster. 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>Hur gör jag för att genomdriva begränsad åtkomst till mina lagrings konton som används av Site Recovery-tjänsten för att läsa/skriva replikeringsdata?
Du kan växla till den hanterade identiteten för Recovery Services-valvet genom att gå till *identitets* inställningen. När valvet har registrerats med Azure Active Directory kan du gå till dina lagrings konton och ge följande roll tilldelningar till valvet:

- Resource Manager-baserade lagrings konton (standard typ):
  - [Deltagare](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage Blob Data-deltagare](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager-baserade lagrings konton (Premium typ):
  - [Deltagare](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage BLOB data-ägare](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klassiska lagrings konton:
  - [Klassisk lagrings konto deltagare](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Klassisk lagrings kontots nyckel operatörs tjänst roll](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

## <a name="disaster-recovery"></a>Haveriberedskap

### <a name="what-can-site-recovery-protect"></a>Vad kan Site Recovery skydda?
* **Virtuella Azure-datorer**: Site Recovery kan replikera alla arbets belastningar som körs på en virtuell Azure-dator som stöds
* **Virtuella Hyper-v-datorer**: Site Recovery kan skydda alla arbets belastningar som körs på en virtuell Hyper-v-dator.
* **Fysiska servrar**: Site Recovery kan skydda fysiska servrar som kör Windows eller Linux.
* **Virtuella VMware-datorer**: Site Recovery kan skydda alla arbets belastningar som körs i en virtuell VMware-dator.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Vilka arbetsbelastningar kan jag skydda med Site Recovery?
Du kan använda Site Recovery för att skydda de flesta arbets belastningar som körs på en virtuell dator eller fysisk server som stöds. Site Recovery ger stöd för program medveten replikering, så att appar kan återställas till ett intelligent tillstånd. Den integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och fungerar nära ledande leverantörer, inklusive Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Kan jag hantera haveriberedskap för mina avdelningskontor med Site Recovery?
Ja. När du använder Site Recovery för att dirigera replikering och redundans på avdelnings kontoren får du ett enhetligt dirigering och en översikt över alla arbets belastningar för arbets belastnings kontor på en central plats. Du kan enkelt köra redundansväxlingar och administrera haveriberedskap för alla avdelningskontor från huvudkontoret, utan att besöka avdelningskontoren.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Stöds haveri beredskap för virtuella Azure-datorer?

Ja, Site Recovery stöder haveri beredskap för virtuella Azure-datorer mellan Azure-regioner. [Läs vanliga frågor](azure-to-azure-common-questions.md) om haveri beredskap för virtuella Azure-datorer. Om du vill replikera mellan två Azure-regioner på samma kontinent använder du Azure till Azure DR-erbjudandet. Du behöver inte konfigurera konfigurations servern/processervern och ExpressRoute-anslutningar.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Stöds haveri beredskap för virtuella VMware-datorer?

Ja, Site Recovery stöder haveri beredskap för lokala virtuella VMware-datorer. [Läs vanliga frågor](vmware-azure-common-questions.md) om haveri beredskap för virtuella VMware-datorer.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Stöds haveri beredskap för virtuella Hyper-V-datorer?
Ja, Site Recovery stöder haveri beredskap för lokala virtuella Hyper-V-datorer. [Läs vanliga frågor](hyper-v-azure-common-questions.md) om haveri beredskap för virtuella Hyper-V-datorer.

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>Stöds haveri beredskap för fysiska servrar?
Ja, Site Recovery stöder haveri beredskap för lokala fysiska servrar som kör Windows och Linux till Azure eller till en sekundär plats. Läs om kraven för haveri beredskap till [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)och[en sekundär plats](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Observera att fysiska servrar kommer att köras som virtuella datorer i Azure efter en redundansväxling. Återställning efter fel från Azure till en lokal fysisk server stöds inte för närvarande. Du kan bara växla tillbaka till en virtuell VMware-dator.





## <a name="replication"></a>Replikering

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Kan jag replikera via en VPN-anslutning från plats till plats till Azure?
Azure Site Recovery replikerar data till ett Azure Storage-konto eller Managed disks över en offentlig slut punkt. Replikeringen är inte över en plats-till-plats-VPN. 

### <a name="why-cant-i-replicate-over-vpn"></a>Varför kan jag inte replikera via VPN?

När du replikerar till Azure når replikeringstrafiken de offentliga slut punkterna för en Azure Storage. Därför kan du bara replikera via offentliga Internet eller via ExpressRoute (Microsoft-peering eller en befintlig offentlig peering).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Kan jag använda Riverbed SteelHeads för replikering?

Vår partner, Riverbed, ger detaljerad vägledning om hur du arbetar med Azure Site Recovery. Granska sin [lösnings guide](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Kan jag använda ExpressRoute för att replikera virtuella datorer till Azure?
Ja, [ExpressRoute kan användas](concepts-expressroute-with-site-recovery.md) för att replikera lokala virtuella datorer till Azure.

- Azure Site Recovery replikerar data till en Azure Storage över en offentlig slut punkt. Du måste konfigurera Microsoft- [peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) eller använda en befintlig [offentlig peering](../expressroute/about-public-peering.md) (inaktuell för nya kretsar) för att använda ExpressRoute för Site Recovery replikering.
- Microsoft-peering är den rekommenderade routningsdomänen för replikering.
- Replikering stöds inte för privat peering.
- Om du skyddar VMware-datorer eller fysiska datorer kontrollerar du att [nätverks kraven](vmware-azure-configuration-server-requirements.md#network-requirements) för konfigurations servern också är uppfyllda. Anslutning till vissa URL: er krävs av konfigurations servern för att dirigera Site Recovery replikering. ExpressRoute kan inte användas för den här anslutningen.
- När de virtuella datorerna har redundansväxlats till ett virtuellt Azure-nätverk kan du komma åt dem med hjälp av den [privata peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) -installationen med det virtuella Azure-nätverket.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Om jag replikerar till Azure, vilken typ av lagrings konto eller hanterad disk behöver jag?

Du behöver en LRS-eller GRS-lagring. Vi rekommenderar GRS så att dina data är flexibla i händelse av ett regionalt strömavbrott, eller om det inte går att återställa den primära regionen. Kontot måste finnas i samma region som Recovery Services-valvet. Premium Storage stöds för virtuella VMware-datorer, virtuella Hyper-V-datorer och replikering av fysiska servrar när du distribuerar Site Recovery i Azure Portal. Managed disks stöder endast LRS.

### <a name="how-often-can-i-replicate-data"></a>Hur ofta kan jag replikera data?
* **Hyper-V:** Virtuella Hyper-V-datorer kan replikeras var 30: e sekund (med undantag för Premium Storage), fem minuter eller 15 minuter.
* **Virtuella Azure-datorer, virtuella VMware-datorer och fysiska servrar:** En replikeringsfrekvens är inte relevant här. Replikeringen är kontinuerlig.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kan jag utöka replikeringen från den befintliga återställnings platsen till en annan tertiär plats?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [feedback-forumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Kan jag göra en offlinereplikering första gången jag replikerar till Azure?
Det stöds inte. Begär den här funktionen i [feedback-forumet](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kan jag undanta specifika diskar från replikeringen?
Detta stöds när du replikerar virtuella VMware-datorer och virtuella Hyper-V-datorer till Azure med hjälp av Azure Portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar stöds vid replikering av virtuella Hyper-V-datorer och vid replikering av virtuella VMware-datorer och fysiska datorer till Azure. Operativ system disken måste vara en standard disk.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Kan jag begränsa bandbredden som tilldelas för replikeringstrafik?
Ja. Du kan läsa mer om hur du begränsar bandbredden i de här artiklarna:

* [Kapacitets planering för att replikera virtuella VMware-datorer och fysiska servrar](site-recovery-plan-capacity-vmware.md)
* [Kapacitets planering för att replikera virtuella Hyper-V-datorer till Azure](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Kan jag aktivera replikering med program konsekvens i Linux-servrar? 
Ja. Azure Site Recovery för Linux-åtgärds system har stöd för anpassade skript för program-konsekvens. Det anpassade skriptet med pre och post-Options används av Azure Site Recovery Mobility-agenten när appen är konsekvent. Nedan visas stegen för att aktivera det.

1. Logga in som rot i datorn.
2. Ändra katalogen till Azure Site Recovery mobilitets agentens installations plats. Standardvärdet är "/usr/local/ASR"<br>
    `# cd /usr/local/ASR`
3. Ändra katalogen till "VX/scripts" under installations plats<br>
    `# cd VX/scripts`
4. Skapa ett bash shell-skript med namnet "customscript.sh" med körnings behörigheter för rot användare.<br>
    a. Skriptet ska ha stöd för "--pre" och "--post" (Observera de dubbla strecken) kommando rads alternativ<br>
    b. När skriptet anropas med för hands alternativ ska det låsa programdata/utdata och när de anropas med alternativet post-option, ska det Tina in program indata/utdata.<br>
    c. En exempel-mall –<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. Lägg till kommandona Freeze och unfrysa indata/utdata i för-och efter-steg för de program som kräver program konsekvens. Du kan välja att lägga till ett annat skript som anger de och anropar det från "customscript.sh" med för-och efter-alternativ.

>[!Note]
>Site Recovery agent-versionen ska vara 9,24 eller högre för att stödja anpassade skript.

## <a name="replication-policy"></a>Replikeringsprincip

### <a name="what-is-a-replication-policy"></a>Vad är en replikeringsprincip?

En replikeringsprincip definierar inställningarna för lagrings historiken för återställnings punkter. Principen definierar också frekvensen för programkonsekventa ögonblicks bilder. Som standard skapar Azure Site Recovery en ny replikeringsprincip med standardinställningar för:

- 24 timmar för Retentions historiken för återställnings punkter.
- 4 timmar för frekvensen av programkonsekventa ögonblicks bilder.

[Läs mer om replikeringsinställningar](./azure-to-azure-tutorial-enable-replication.md#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Vad är en krasch-konsekvent återställnings punkt?

En kraschad återställnings punkt har data på disken som om du hämtade ström sladden från servern under ögonblicks bilden. Den kraschbaserade återställnings punkten innehåller inte något som fanns i minnet när ögonblicks bilden togs.

Idag kan de flesta program återställa sig väl från kraschbaserade ögonblicks bilder. En kraschad återställnings punkt är vanligt vis tillräckligt för operativ system och program som inte kommer från en databas, t. ex. fil servrar, DHCP-servrar och utskrifts servrar.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Vad är frekvensen för generering av kraschbaserade återställnings punkter?

Site Recovery skapar en kraschad återställnings punkt var femte minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Vad är en programkonsekvent återställnings punkt?

Programkonsekventa återställnings punkter skapas från programkonsekventa ögonblicks bilder. Programkonsekventa återställnings punkter fångar samma data som kraschbaserade ögonblicks bilder samtidigt som data i minnet och alla transaktioner i processen fångas.

På grund av det extra innehållet är programkonsekventa ögonblicks bilder de mest inblandade och ta den längsta. Vi rekommenderar programkonsekventa återställnings punkter för databas operativ system och program som SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Vad är effekten av programkonsekventa återställnings punkter för program prestanda?

Programkonsekventa återställnings punkter fångar alla data i minnet och i processen. Eftersom återställnings punkter fångar dessa data, kräver de ramverk som tjänsten Volume Shadow Copy i Windows för att inaktive ring av programmet. Om insamlings processen används ofta kan den påverka prestanda när arbets belastningen redan är upptagen. Vi rekommenderar inte att du använder låg frekvens för programkonsekventa återställnings punkter för arbets belastningar som inte är databas. Det räcker med 1 timme för databas arbets belastningen.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Vad är den minsta frekvensen för generering av programkonsekventa återställnings punkter?

Site Recovery kan skapa en programkonsekvent återställnings punkt med en minsta frekvens på 1 timme.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hur genereras och sparas återställnings punkter?

För att förstå hur Site Recovery genererar återställnings punkter ser vi ett exempel på en replikeringsprincip. Den här replikeringsprincipen har en återställnings punkt med en 24-timmarsperiod och en programkonsekvent frekvens ögonblicks bild på 1 timme.

Site Recovery skapar en kraschad återställnings punkt var femte minut. Du kan inte ändra den här frekvensen. Under den senaste timmen kan du välja mellan 12 kraschbaserade punkter och 1 app-konsekvent punkt. När tiden pågår Site Recovery rensar alla återställnings punkter bortom den senaste timmen och sparar bara 1 återställnings punkt per timme.

Följande skärm bild illustrerar exemplet. I skärm bilden:

- Under den senaste timmen finns återställnings punkter med en frekvens på 5 minuter.
- Under den senaste timmen behålls Site Recovery endast en återställnings punkt.

   ![Lista över genererade återställnings punkter](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?

Den äldsta återställnings punkten som du kan använda är 72 timmar.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Jag har en replikeringsprincip på 24 timmar. Vad händer om ett problem förhindrar Site Recovery att skapa återställnings punkter i mer än 24 timmar? Kommer mina tidigare återställnings punkter att gå förlorade?

Nej, Site Recovery kommer att behålla alla dina tidigare återställnings punkter. Beroende på återställnings punkternas kvarhållning fönster ersätter Site Recovery den äldsta punkten endast om den genererar nya punkter. På grund av problemet kan Site Recovery inte skapa några nya återställnings punkter. Tills det finns nya återställnings punkter är alla gamla punkter kvar när du har nått fönstret med kvarhållning.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Hur ändrar jag replikeringsprincipen när replikering har Aktiver ATS på en virtuell dator?

Gå till **Site Recovery valv**  >  **Site Recovery Infrastructure**  >  **Replication policies**. Välj den princip som du vill redigera och spara ändringarna. Alla ändringar kommer också att gälla för alla befintliga replikeringar.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Är alla återställnings punkter en fullständig kopia av den virtuella datorn eller en differentiell?

Den första återställnings punkten som genereras har en fullständig kopia. Eventuella återställnings punkter har delta ändringar.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Ökar lagrings kostnaden genom att öka lagrings tiden för återställnings punkter?

Ja, om du ökar kvarhållningsperioden från 24 timmar till 72 timmar kommer Site Recovery att spara återställnings punkterna under ytterligare 48 timmar. Den tillagda tiden debiteras lagrings kostnader. En enda återställnings punkt kan till exempel ha delta ändringar på 10 GB med en kostnad per GB $0,16 per månad. Ytterligare kostnader blir $1,60 × 48 per månad.


## <a name="failover"></a>Redundans
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Hur får jag åtkomst till virtuella Azure-datorer efter redundansväxlingen om jag växlar över till Azure?

Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller över Azure ExpressRoute. Du måste förbereda ett antal saker för att kunna ansluta. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Om jag växlar över till Azure hur ser Azure till att mina data är elastiska?
Azure är utformat med flexibilitet i fokus. Site Recovery har redan byggts för redundansväxling till ett sekundärt Azure-datacenter, i enlighet med Azure SLA. Om detta inträffar ser vi till att dina metadata och valv finns kvar i samma geografiska region som du valde för ditt valv.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Om jag replikerar mellan två Data Center vad händer om mitt primära Data Center upplever ett oväntat avbrott?
Du kan utlösa en oplanerad redundansväxling från den sekundära platsen. Site Recovery behöver ingen anslutning från den primära platsen för att utföra redundansväxlingen.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
Den sker inte automatiskt. Du initierar redundans med ett enda klick i portalen, eller så kan du använda [Site Recovery PowerShell](/powershell/module/az.recoveryservices) för att utlösa en redundansväxling. Att återställa igen är en enkel åtgärd i Site Recovery portalen.

Om du vill automatisera kan du använda lokala Orchestrator eller Operations Manager för att identifiera ett fel i en virtuell dator och sedan utlösa redundansväxlingen med hjälp av SDK.

* [Läs mer](site-recovery-create-recovery-plans.md) om återställnings planer.
* [Läs mer](site-recovery-failover.md) om redundans.
* [Läs mer](./vmware-azure-failback.md) om att återställa virtuella VMware-datorer och fysiska servrar

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Kan jag växla tillbaka till en annan värd om min lokala värd inte svarar eller kraschar?
Ja, du kan använda den alternativa plats återställningen för återställning efter fel till en annan värd från Azure.

* [För virtuella VMware-datorer](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [För virtuella Hyper-V-datorer](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan jag automatisera Site Recovery scenarier med ett SDK?
Ja. Du kan automatisera Site Recovery-arbetsflöden med hjälp av REST-API:et, PowerShell och Azure SDK. Scenarier som stöds för närvarande för distribution av Site Recovery med hjälp av PowerShell:

* [Replikera virtuella Hyper-V-datorer i VMMs-moln till Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replikera virtuella Hyper-V-datorer utan VMM till Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replikera VMware till Azure med PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Komponent/Provider-uppgradering

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Var hittar jag viktig information/samlade uppdateringar av Site Recovery uppgraderingar

[Lär dig mer](site-recovery-whats-new.md) om nya uppdateringar och [Hämta information om sammanslagning](service-updates-how-to.md).

## <a name="next-steps"></a>Nästa steg
* Läs [Site Recovery-översikten](site-recovery-overview.md)
