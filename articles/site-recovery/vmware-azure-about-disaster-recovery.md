---
title: Katastrof återställning i VMware med Azure Site Recovery
description: Den här artikeln innehåller en översikt över haveri beredskap för virtuella VMware-datorer till Azure med hjälp av tjänsten Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: dfbdff01064b483085233ece47d1d3b635b68743
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87021468"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Om haveri beredskap för virtuella VMware-datorer till Azure

Den här artikeln innehåller en översikt över haveri beredskap för lokala virtuella VMware-datorer till Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .

## <a name="what-is-bcdr"></a>Vad är BCDR?

En strategi för affärs kontinuitet och haveri beredskap (BCDR) hjälper dig att hålla verksamheten igång. Under planerat drift stopp och oväntade avbrott är BCDR säkra och tillgängliga och säkerställer att apparna fortsätter att köras. Förutom plattforms BCDR funktioner som regional länkning och lagring med hög tillgänglighet tillhandahåller Azure Recovery Services som en integrerad del av din BCDR-lösning. Återställnings tjänster är: 

- [Azure Backup](../backup/backup-overview.md) säkerhetskopierar dina lokala och virtuella Azure-Datadata. Du kan säkerhetskopiera en fil och mappar, vissa arbets belastningar eller en hel virtuell dator. 
- [Azure Site Recovery](site-recovery-overview.md) ger återhämtning och haveri beredskap för appar och arbets belastningar som körs på lokala datorer eller virtuella Azure IaaS-datorer. Site Recovery dirigerar replikeringen och hanterar redundansväxling till Azure när avbrott inträffar. Den hanterar också återställning från Azure till din primära plats. 

> [!NOTE]
> Site Recovery flyttar eller lagrar inte kund information från mål regionen, där haveri beredskap har kon figurer ATS för käll datorerna. Kunder kan välja ett Recovery Services valv från en annan region om de så vill. Recovery Services valvet innehåller metadata men inga faktiska kund uppgifter.

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Hur gör Site Recovery haveri beredskap?

1. När du har bearbetat Azure och din lokala plats konfigurerar du och aktiverar replikering för dina lokala datorer.
2. Site Recovery dirigerar den inledande replikeringen av datorn, i enlighet med dina princip inställningar.
3. Efter den inledande replikeringen replikerar Site Recovery delta ändringar till Azure. 
4. När allt replikeras som förväntat kör du en granskning av haveri beredskap.
    - I detalj nivån ser du till att redundansväxlingen fungerar som förväntat när ett reellt behov uppstår.
    - Detalj nivån utför ett redundanstest utan att påverka produktions miljön.
5. Om ett avbrott inträffar kan du köra en fullständig redundans till Azure. Du kan redundansväxla en enskild dator, eller så kan du skapa en återställnings plan som växlar över flera datorer på samma gång.
6. Vid redundans skapas virtuella Azure-datorer från VM-data i hanterade diskar eller lagrings konton. Användare kan fortsätta att komma åt appar och arbets belastningar från den virtuella Azure-datorn
7. När din lokala plats är tillgänglig igen går du tillbaka från Azure.
8. När du har växlat tillbaka och arbetar från din primära plats så kan du starta replikeringen av lokala virtuella datorer till Azure igen.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Hur gör jag för att vet du om min miljö är lämplig för haveri beredskap till Azure?

Site Recovery kan replikera alla arbets belastningar som körs på en virtuell VMware-dator eller fysisk server. Här är de saker du behöver för att kontrol lera din miljö:

- Om du replikerar virtuella VMware-datorer kör du rätt versioner av VMware virtualization-servrar? [Sök här](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Är de datorer som du vill replikera som kör ett operativ system som stöds? [Sök här](vmware-physical-azure-support-matrix.md#replicated-machines).
- För Linux haveri beredskap, är datorer som kör ett fil system/gäst lagring som stöds? [Markera här](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Är de datorer som du vill replikera uppfylla kraven för Azure? [Sök här](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Stöds din nätverks konfiguration? [Sök här](vmware-physical-azure-support-matrix.md#network).
- Stöds lagrings konfigurationen? [Sök här](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Vad behöver jag för att konfigurera i Azure innan jag börjar?

I Azure måste du förbereda följande:

1. Kontrol lera att ditt Azure-konto har behörighet att skapa virtuella datorer i Azure.
2. Skapa ett Azure-nätverk som virtuella Azure-datorer ska ansluta till när de skapas från lagrings konton eller hanterade diskar efter en redundansväxling.
3. Konfigurera ett Azure Recovery Services-valv för Site Recovery. Valvet finns i Azure Portal och används för att distribuera, konfigurera, dirigera, övervaka och felsöka din Site Recovery-distribution.

*Behöver du mer hjälp?*

Lär dig hur du konfigurerar Azure genom [att verifiera ditt konto](tutorial-prepare-azure.md#verify-account-permissions), skapa ett [nätverk](tutorial-prepare-azure.md#set-up-an-azure-network)och [Konfigurera ett valv](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Vad behöver jag för att konfigurera lokalt innan jag börjar?

Det här behöver du göra på plats:

1. Du måste konfigurera ett par konton:

    - Om du replikerar virtuella VMware-datorer behövs ett konto för Site Recovery för att få åtkomst till vCenter Server eller vSphere ESXi-värdar för att automatiskt identifiera virtuella datorer.
    - Det krävs ett konto för att installera Site Recovery Mobility Service Agent på varje fysisk dator eller virtuell dator som du vill replikera.

2. Du måste kontrol lera kompatibiliteten för VMware-infrastrukturen om du inte redan har gjort det.
3. Se till att du kan ansluta till virtuella Azure-datorer efter en redundansväxling. Du konfigurerar RDP på lokala Windows-datorer eller SSH på Linux-datorer.

*Behöver du mer hjälp?*
- Förbered konton för [Automatisk identifiering](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) och för [installation av mobilitets tjänsten](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Kontrol lera](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) att VMware-inställningarna är kompatibla.
- [Förbered](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) dig så att du ansluter i Azure efter en redundansväxling.
- [Läs den här artikeln](concepts-on-premises-to-azure-networking.md)om du vill ha mer detaljerad information om hur du konfigurerar IP-adresser för virtuella Azure-datorer efter en redundansväxling.

## <a name="how-do-i-set-up-disaster-recovery"></a>Hur gör jag för att konfigurera katastrof återställning?

När du har konfigurerat din Azure och lokala infrastruktur på plats kan du konfigurera haveri beredskap.

1. Om du vill förstå de komponenter som du behöver distribuera, granskar du [VMware till Azure-arkitekturen](vmware-azure-architecture.md)och den [fysiska till Azure-arkitekturen](physical-azure-architecture.md). Det finns ett antal komponenter, så det är viktigt att förstå hur de passar ihop.
2. **Käll miljö**: som ett första steg i distributionen ställer du in din källa för replikeringskälla. Du anger vad du vill replikera och var du vill replikera till.
3. **Konfigurations Server**: du måste konfigurera en konfigurations server i din lokala käll miljö:
    - Konfigurations servern är en lokal dator. För katastrof återställning i VMware rekommenderar vi att du distribuerar den som en virtuell VMware-dator som kan distribueras från en mall för nedladdnings bar OVF.
    - Konfigurations servern samordnar kommunikationen mellan både lokalt och Azure
    - Ett par andra komponenter som körs på konfigurations servern.
        - Processervern tar emot, optimerar och skickar replikeringsdata till cache Storage-kontot i Azure. Den hanterar också automatisk installation av mobilitets tjänsten på datorer som du vill replikera och utför automatisk identifiering av virtuella datorer på VMware-servrar.
        - Huvudmålservern hanterar replikeringsdata vid återställning efter fel från Azure.
    - Konfigurera inkluderar registrering av konfigurations servern i valvet, hämtar MySQL-server och VMware-PowerCLI och anger de konton som skapats för automatisk identifiering och mobilitets tjänst installation.
4. **Mål miljö**: du konfigurerar din Azure-miljö genom att ange din Azure-prenumeration och dina nätverks inställningar.
5. **Replikeringsprincip**: du anger hur replikeringen ska ske. Inställningarna omfattar hur ofta återställnings punkter skapas och lagras och om programkonsekventa ögonblicks bilder ska skapas.
6. **Aktivera replikering**. Du aktiverar replikering för lokala datorer. Om du har skapat ett konto för att installera mobilitets tjänsten kommer det att installeras när du aktiverar replikering för en dator. 

*Behöver du mer hjälp?*

- För en snabb genom gång av de här stegen kan du prova vår [VMware-självstudie](vmware-azure-tutorial.md)och [genom gång av fysiska servrar](physical-azure-disaster-recovery.md).
- [Läs mer](vmware-azure-set-up-source.md) om hur du konfigurerar din käll miljö.
- [Lär dig mer om](vmware-azure-deploy-configuration-server.md) konfigurations Server krav och hur du konfigurerar konfigurations servern med en OVF-mall för VMware-replikering. Om du av någon anledning inte kan använda en mall, eller om du replikerar fysiska servrar, [använder du dessa instruktioner](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Läs mer](vmware-azure-set-up-target.md) om mål inställningar.
- [Få mer information](vmware-azure-set-up-replication.md) om hur du konfigurerar en replikeringsprincip.
- [Lär dig](vmware-azure-enable-replication.md) hur du aktiverar replikering och [exkluderar](vmware-azure-exclude-disk.md) diskar från replikering.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Något gick fel, hur felsöker jag?

- Som ett första steg kan du prova att [övervaka distributionen](site-recovery-monitor-and-troubleshoot.md) för att kontrol lera statusen för replikerade objekt, jobb och infrastruktur problem och identifiera eventuella fel.
- Om du inte kan slutföra den inledande replikeringen, eller om pågående replikering inte fungerar som förväntat, kan du [läsa artikeln](vmware-azure-troubleshoot-replication.md) om vanliga fel och fel söknings tips.
- Om du har problem med den automatiska installationen av mobilitets tjänsten på datorer som du vill replikera läser du vanliga fel i [den här artikeln](vmware-azure-troubleshoot-push-install.md).
- Om redundansväxlingen inte fungerar som förväntat, kontrol lera vanliga fel i [den här artikeln](site-recovery-failover-to-azure-troubleshoot.md).
- Om failback inte fungerar kontrollerar du om problemet visas i [den här artikeln](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Nästa steg

Med replikering nu på plats bör du [köra en granskning av haveri beredskap](tutorial-dr-drill-azure.md) för att säkerställa att redundansväxlingen fungerar som förväntat. 
