---
title: Haveri beredskap för virtuella VMware-datorer/fysiska servrar till en sekundär plats med Azure Site Recovery
description: Lär dig hur du konfigurerar haveri beredskap för virtuella VMware-datorer eller fysiska Windows-och Linux-servrar till en sekundär plats med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84711909"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Konfigurera haveriberedskap för lokala virtuella VMware-datorer eller fysiska servrar till en sekundär plats

InMage Scout i [Azure Site Recovery](site-recovery-overview.md) tillhandahåller replikering i real tid mellan lokala VMware-platser. InMage Scout ingår i Azure Site Recovery tjänst prenumerationer.

## <a name="end-of-support-announcement"></a>Meddelande om upphörande support

Azure Site Recovery scenariot för replikering mellan lokala VMware-eller fysiska data Center når slut för and support.

-   Från augusti 2018 går det inte att konfigurera scenariot i Recovery Services-valvet och InMage Scout-programvaran kan inte laddas ned från valvet. Befintliga distributioner kommer att stödjas. 
-   Från december 31 2020 stöds inte scenariot.
- Befintliga partners kan publicera nya kunder till scenariot tills supporten upphör.

Under 2018 och 2019 publiceras två uppdateringar: 

-   Uppdatering 7: åtgärdar problem med nätverks konfiguration och efterlevnad och ger stöd för TLS 1,2.
-   Uppdatering 8: lägger till stöd för Linux-operativsystem RHEL/CentOS 7.3/7.4/7.5 och för SUSE 12

Efter uppdatering 8 kommer inga ytterligare uppdateringar att släppas. Det finns ett begränsat stöd för snabb korrigeringar för de operativ system som har lagts till i uppdatering 8 och fel korrigeringar baserade på bästa möjliga ansträngning.

Azure Site Recovery fortsätter att utveckla genom att tillhandahålla VMware-och Hyper-V-kunder en sömlös och förstklassig DRaaS-lösning med Azure som en katastrof återställnings plats. Microsoft rekommenderar att befintliga InMage/ASR Scout-kunder förväntar sig att använda Azure Site Recoverys VMware-till-Azure-scenario för företagets kontinuitets behov. Azure Site Recovery s scenario för VMware till Azure är en DR-lösning i företags klass för VMware-program, som erbjuder återställnings-och RTO på minuter, stöd för replikering av flera virtuella datorer och återställning, sömlös onboarding, omfattande övervakning och betydande TCO-fördel.

### <a name="scenario-migration"></a>Scenariomigrering
Alternativt rekommenderar vi att du konfigurerar haveri beredskap för lokala virtuella VMware-datorer och fysiska datorer genom att replikera dem till Azure. Gör på följande sätt:

1.  Granska snabb jämförelsen nedan. Innan du kan replikera lokala datorer måste du kontrol lera att de uppfyller [kraven](./vmware-physical-azure-support-matrix.md#replicated-machines) för replikering till Azure. Om du replikerar virtuella VMware-datorer, rekommenderar vi att du går igenom [rikt linjerna för kapacitets planering](./site-recovery-plan-capacity-vmware.md)och kör [distributions planerings verktyget](./site-recovery-deployment-planner.md) till identitets kapacitets krav och kontrollerar efterlevnad.
2.  När du har kört distributions planeraren kan du konfigurera replikering: o för virtuella VMware-datorer, följa de här självstudierna för att [förbereda Azure](./tutorial-prepare-azure.md), [förbereda din lokala VMware-miljö](./vmware-azure-tutorial-prepare-on-premises.md)och [Konfigurera haveri beredskap](./vmware-azure-tutorial-prepare-on-premises.md).
o för fysiska datorer följer du den här [självstudien](./physical-azure-disaster-recovery.md).
3.  När datorerna har repliker ATS till Azure kan du köra en [haveri beredskap](./site-recovery-test-failover-to-azure.md) för att kontrol lera att allt fungerar som förväntat.

### <a name="quick-comparison"></a>Snabb jämförelse

**Funktion** | **Replikering till Azure** |**Replikering mellan VMware-datacenter**
--|--|--
**Nödvändiga komponenter** |Mobilitets tjänsten på replikerade datorer. Lokal konfigurations Server, processerver, huvud mål server. Tillfällig processerver i Azure för återställning efter fel.|Mobilitets tjänst, Processerver, konfigurations Server och huvud mål
**Konfiguration och dirigering** |Recovery Services valv i Azure Portal | Använda vContinuum 
**Replikerad** |Disk (Windows och Linux) |Volume-Windows<br> Disk-Linux
**Delat disk kluster** |Stöds inte|Stöds
**Data omsättnings gränser (genomsnitt)** |10 MB/s data per disk<br> 25MB/s-data per virtuell dator<br> [Läs mer](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 MB/s data per disk  <br> > 25 MB/s-data per virtuell dator
**Övervakning** |Från Azure Portal|Från CX (konfigurations Server)
**Support mat ris** | [Klicka här om du vill ha mer information](./vmware-physical-azure-support-matrix.md)|[Ladda ned ASR Scout-kompatibel matris](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här kursen behöver du:

- [Granska](vmware-physical-secondary-support-matrix.md) support kraven för alla komponenter.
- Se till att de datorer som du vill replikera följer den [replikerade datorns support](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Hämta och installera komponent uppdateringar

 Granska och installera de senaste [uppdateringarna](#updates). Uppdateringar bör installeras på servrar i följande ordning:

1. RX-Server (om tillämpligt)
2. Konfigurations servrar
3. Process servrar
4. Huvud mål servrar
5. vContinuum-servrar
6. Käll Server (både Windows-och Linux-servrar)

Installera uppdateringarna på följande sätt:

> [!NOTE]
>Alla Scout-komponenters fil uppdaterings version får inte vara samma i Update. zip-filen. Den äldre versionen indikerar att det inte finns någon ändring i komponenten sedan tidigare uppdatering av den här uppdateringen.

Hämta [Update](https://aka.ms/asr-scout-update7) . zip-filen och konfigurationsfiler för [MySQL och php-uppgraderingen](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) . Filen Update. zip innehåller alla de grundläggande binärfilerna och de ackumulerade uppgraderings binärfilerna för följande komponenter: 
- InMage_ScoutCloud_RX_8 InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015. tar. gz
- RX_8 RX_8.0.7.0_GA_Update_7_2965621_28Dec18. tar. gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8 InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release. tar. gz
  1. Extrahera zip-filerna.
  2. **RX-Server**: kopiera **RX_8.0.7.0_GA_Update_7_2965621_28Dec18. tar. gz** till RX-servern och extrahera den. Kör **/install** i den extraherade mappen.
  3. **Konfigurations Server och processerver**: kopiera **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** till konfigurations servern och processervern. Dubbelklicka för att köra den.<br>
  4. **Windows huvud mål server**: om du vill uppdatera Unified agent kopierar du **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** till servern. Dubbelklicka på den för att köra den. Samma fil kan också användas för en ny installation. Samma enhetliga agent uppdatering gäller även för käll servern.
  Uppdateringen behöver inte tillämpas på huvud målet för beredd med **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe**  eftersom det är ett nytt ga-installationsprogram med alla de senaste ändringarna.
  5. **vContinuum-Server**: kopiera **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** till servern.  Kontrol lera att du har stängt guiden vContinuum. Dubbelklicka på filen för att köra den.
  6. **Linux-huvud mål server**: om du vill uppdatera Unified agent kopierar du **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz** till Linux-huvud mål servern och extraherar den. Kör **/install** i den extraherade mappen.
  7. **Windows-käll Server**: om du vill uppdatera Unified agent kopierar du **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** till käll servern. Dubbelklicka på filen för att köra den. 
  8. **Linux-käll Server**: om du vill uppdatera Unified agent kopierar du motsvarande version av Unified agent-filen till Linux-servern och extraherar den. Kör **/install** i den extraherade mappen.  Exempel: för RHEL 6,7 64-bitars server kopierar du **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz** till servern och extraherar den. Kör **/install** i den extraherade mappen.
  9. När du har uppgraderat konfigurations servern, processervern och RX-servern med ovanstående installations program måste PHP-och MySQL-biblioteken uppgraderas manuellt med de steg som beskrivs i avsnitt 7,4 i [snabb installations guiden](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Aktivera replikering

1. Konfigurera replikering mellan käll-och mål VMware-platser.
2. Läs mer om installation, skydd och återställning i följande dokument:

   * [Viktig information](https://aka.ms/asr-scout-release-notes)
   * [Kompatibilitet mat ris](https://aka.ms/asr-scout-cm)
   * [Användarhandbok](https://aka.ms/asr-scout-user-guide)
   * [Användar handbok för RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Snabb installations guide](https://aka.ms/asr-scout-quick-install-guide)
   * [Uppgradera MYSQL-och PHP-bibliotek](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Uppdateringar

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 Update 7 
Uppdaterad: 31 december 2018 Hämta [Scout uppdatering 7](https://aka.ms/asr-scout-update7).
Scout Update 7 är ett fullständigt installations program som kan användas för en ny installation samt för att uppgradera befintliga agenter/MT som finns på tidigare uppdateringar (från uppdatering 1 till uppdatering 6). Den innehåller alla korrigeringar från uppdatering 1 för uppdatering 6 plus de nya korrigeringarna och förbättringarna som beskrivs nedan.
 
#### <a name="new-features"></a>Nya funktioner
* PCI-kompatibilitet
* Stöd för TLS v 1.2

#### <a name="bug-and-security-fixes"></a>Fel-och säkerhets korrigeringar
* Fast: Windows-kluster/fristående datorer har felaktig IP-konfiguration vid återställning/DR-Test.
* Åtgärdat: det går inte att lägga till disk åtgärd för V2V-kluster.
* Åtgärdat: vContinuum-guiden har fastnat under återställnings fasen om huvud målet är Windows Server 2016
* Åtgärdat: säkerhets problem i MySQL begränsas genom uppgradering av MySQL till version 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Manuell uppgradering för PHP och MySQL på CS, PS och RX
PHP skript plattform bör uppgraderas till version 7.2.10 på konfigurations servern, processervern och RX-servern.
Databas hanterings systemet MySQL bör uppgraderas till version 5.7.23 på konfigurations servern, processervern och RX-servern.
Följ de manuella stegen i [snabb installations guiden](https://aka.ms/asr-scout-quick-install-guide) för att uppgradera php-och MySQL-versioner.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Update 6 
Uppdaterat: 12 oktober 2017

Hämta [Scout uppdatering 6](https://aka.ms/asr-scout-update6).

Scout Update 6 är en kumulativ uppdatering. Den innehåller alla korrigeringar från uppdatering 1 till uppdatering 5 plus de nya korrigeringarna och förbättringarna som beskrivs nedan. 

#### <a name="new-platform-support"></a>Nytt plattforms stöd
* Stöd har lagts till för käll-Windows Server 2016
* Stöd har lagts till för följande Linux-operativ system:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6,9
    - Oracle Linux 5,11
    - Oracle Linux 6,8
* Support har lagts till för VMware Center 6,5

Installera uppdateringarna på följande sätt:

> [!NOTE]
>Alla Scout-komponenters fil uppdaterings version får inte vara samma i Update. zip-filen. Den äldre versionen indikerar att det inte finns någon ändring i komponenten sedan tidigare uppdatering av den här uppdateringen.

Hämta filen [Update](https://aka.ms/asr-scout-update6) . zip. Filen innehåller följande komponenter: 
- RX_8 RX_8.0.4.0_GA_Update_4_8725872_16Sep16. tar. gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA Update4 bitar för RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_ \<Linux OS> _8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz
  1. Extrahera zip-filerna.
  2. **RX-Server**: kopiera **RX_8.0.4.0_GA_Update_4_8725872_16Sep16. tar. gz** till RX-servern och extrahera den. Kör **/install** i den extraherade mappen.
  3. **Konfigurations Server och processerver**: kopiera **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** till konfigurations servern och processervern. Dubbelklicka för att köra den.<br>
  4. **Windows huvud mål server**: om du vill uppdatera Unified agent kopierar du **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** till servern. Dubbelklicka på den för att köra den. Samma enhetliga agent uppdatering gäller även för käll servern. Om källan inte har uppdaterats till uppdatering 4, bör du uppdatera den enhetliga agenten.
  Uppdateringen behöver inte tillämpas på huvud målet för beredd med **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**  eftersom det är ett nytt ga-installationsprogram med alla de senaste ändringarna.
  5. **vContinuum-Server**: kopiera **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** till servern.  Kontrol lera att du har stängt guiden vContinuum. Dubbelklicka på filen för att köra den.
  Uppdateringen behöver inte tillämpas på huvud målet för beredd med **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** eftersom det är ett nytt ga-installationsprogram med alla de senaste ändringarna.
  6. **Linux-huvud mål server**: om du vill uppdatera Unified agent kopierar du **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz** till huvud mål servern och extraherar den. Kör **/install** i den extraherade mappen.
  7. **Windows-käll Server**: om du vill uppdatera Unified agent kopierar du **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** till käll servern. Dubbelklicka på filen för att köra den. 
  Du behöver inte installera uppdatering 5-agenten på käll servern om den redan har uppdaterats till uppdatering 4 eller käll agenten har installerats med den senaste bas installations **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Linux-käll Server**: om du vill uppdatera Unified agent kopierar du motsvarande version av Unified agent-filen till Linux-servern och extraherar den. Kör **/install** i den extraherade mappen.  Exempel: för RHEL 6,7 64-bitars server kopierar du **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz** till servern och extraherar den. Kör **/install** i den extraherade mappen.


> [!NOTE]
> * Installations programmet för grundläggande Unified agent (UA) för Windows har uppdaterats för att ge stöd för Windows Server 2016. Det nya installations **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** paketeras med bas Scout ga-paketet (**InMage_Scout_Standard_8.0,1 GA-Oct17.zip**). Samma installations program kommer att användas för alla Windows-versioner som stöds. 
> * Grundläggande Windows-vContinuum & huvud mål installations programmet har uppdaterats för att ge stöd för Windows Server 2016. Det nya installations **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** paketeras med bas Scout ga-paketet (**InMage_Scout_Standard_8.0,1 GA-Oct17.zip**). Samma installations program kommer att användas för att distribuera Windows 2016 huvud mål och Windows 2012R2 huvud mål.
> * Windows Server 2016 på fysisk server stöds inte av ASR Scout. Den stöder endast Windows Server 2016 VMware VM. 
>

#### <a name="bug-fixes-and-enhancements"></a>Fel korrigeringar och förbättringar
- Återställning efter fel för virtuella Linux-datorer med en lista över diskar som ska replikeras är tom i slutet av konfigurationen.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 uppdatering 5
Scout uppdatering 5 är en kumulativ uppdatering. Den innehåller alla korrigeringar från uppdatering 1 till uppdatering 4 och de nya korrigeringarna som beskrivs nedan.
- Korrigeringar från Site Recovery Scout uppdatering 4 till uppdatering 5 är specifika för huvud mål-och vContinuum-komponenterna.
- Om käll servrar kör huvud mål, konfiguration, process och RX-servrar redan uppdatering 4, och Använd den bara på huvud mål servern. 

#### <a name="new-platform-support"></a>Nytt plattforms stöd
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* SLES 11 SP4 64 bitars  **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release. tar. gz** paketeras med Base Scout ga-paketet (**InMage_Scout_Standard_8.0,1 GA.zip**). Ladda ned GA-paketet från portalen, enligt beskrivningen i skapa ett valv.


#### <a name="bug-fixes-and-enhancements"></a>Fel korrigeringar och förbättringar

* Korrigeringar för ökad Windows-kluster support tillförlitlighet:
    * Fast-några av P2V MSCS-kluster diskarna blir råa efter återställningen.
    * Den fasta P2V-kluster återställningen Miss lyckas på grund av en felaktig disk ordning.
    * Fast – MSCS-klustrets åtgärd för att lägga till diskar Miss lyckas med fel matchning av disk storlek.
    * Fast-beredskaps kontroll för käll MSCS-kluster med mappning av LUN-LUN för RDM Miss lyckas vid storleks verifiering.
    * Kluster skydd med en enskild nod Miss lyckas på grund av ett problem med SCSI-matchning. 
    * Fast skydd av P2V-Windows-klusterresursen Miss lyckas om det finns mål kluster diskar. 
    
* Åtgärdat: vid återställning efter fel, om den valda huvud mål servern inte finns på samma ESXi-server som den skyddade käll datorn (under vidarebefordrings skydd), hämtar vContinuum fel huvud mål servern under återställning efter fel och återställnings åtgärden Miss lyckas.

> [!NOTE]
> * De här korrigeringarna för P2V-kluster gäller endast för fysiska MSCS-kluster som nyligen skyddas med Site Recovery Scout uppdatering 5. Om du vill installera kluster korrigeringarna på skyddade P2V MSCS-kluster med äldre uppdateringar följer du de uppgraderings steg som anges i avsnitt 12 i [versions kommentarerna för Site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * om samma uppsättning diskar är aktiva på var och en av klusternoderna när skyddet ursprungligen skyddades, så kan åter skyddet av ett fysiskt MSCS-kluster bara återanvända befintliga mål diskar. Om inte, använder du de manuella stegen i avsnitt 12 i [Site Recovery Scout-versions anteckningar](https://aka.ms/asr-scout-release-notes)för att flytta mål sid diskarna till rätt data lager Sök väg för åter användning under återskydd. Om du skyddar MSCS-klustret i P2V-läge utan att följa uppgraderings stegen skapas en ny disk på mål ESXi-servern. Du måste manuellt ta bort de gamla diskarna från data lagret.
> * När en SLES11-eller SLES11-Server (med alla service pack) startas om på ett smidigt sätt markerar du **rot** diskens replikeringsinställningar manuellt för omsynkronisering. Det finns inget meddelande i CX-gränssnittet. Om du inte markerar rot disken för omsynkronisering kan du märka problem med data integriteten.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 uppdatering 4
Scout Update 4 är en kumulativ uppdatering. Den innehåller alla korrigeringar från uppdatering 1 till uppdatering 3, och de nya korrigeringarna som beskrivs nedan.

#### <a name="new-platform-support"></a>Nytt plattforms stöd

* Support har lagts till för vCenter/vSphere 6,0, 6,1 och 6,2
* Stöd har lagts till för dessa Linux-operativ system:
  * Red Hat Enterprise Linux (RHEL) 7,0, 7,1 och 7,2
  * CentOS 7,0, 7,1 och 7,2
  * Red Hat Enterprise Linux (RHEL) 6,8
  * CentOS 6,8

> [!NOTE]
> RHEL/CentOS 7 64 bitars  **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release. tar. gz** paketeras med Base Scout ga-paketet **InMage_Scout_Standard_8.0,1 GA.zip**. Hämta Scout GA-paketet från portalen enligt beskrivningen i skapa ett valv.

#### <a name="bug-fixes-and-enhancements"></a>Fel korrigeringar och förbättringar

* Förbättrad avslutnings hantering för följande Linux-operativsystem och kloner, för att förhindra oönskade omsynkroniseringar:
    * Red Hat Enterprise Linux (RHEL) 6. x
    * Oracle Linux (OL) 6. x
* För Linux är alla åtkomst behörigheter för mappar i den enhetliga agent installations katalogen begränsad till endast den lokala användaren.
* I Windows, en korrigering för ett tids gräns problem som uppstod vid utfärdande av vanliga distribuerade konsekvens bok märken, på kraftigt laddade distribuerade program som SQL Server-och delnings punkt kluster.
* En logg relaterad korrigering i konfigurations serverns grundläggande installations program.
* En nedladdnings länk till VMware vCLI 6,0 har lagts till i bas installations programmet för Windows huvud mål.
* Ytterligare kontroller och loggar har lagts till för nätverks konfigurations ändringar under redundansväxling och haveri beredskap.
* En korrigering för ett problem som orsakade att kvarhållning av information inte rapporteras till konfigurations servern.  
* För fysiska kluster är det en korrigering av ett problem som gjorde att volym storleks ändringar inte kan utföras i guiden vContinuum vid krympning av käll volymen.
* En korrigering för ett kluster skydds problem som misslyckades med felet: "Det gick inte att hitta disksignaturen", när kluster disken är en PRDM-disk.
* En korrigering för en CXPS-Transport Server krasch, som orsakas av ett undantag utanför intervallet.
* Nu kan du ändra storlek på Server namn och IP-adress kolumner på sidan **push-installation** i guiden vContinuum.
* Förbättringar i RX-API:
  * De fem senaste tillgängliga vanliga konsekvens punkterna är nu tillgängliga (endast garanterade taggar).
  * Information om kapacitet och ledigt utrymme visas för alla skyddade enheter.
  * Det finns ett tillgängligt driv rutins tillstånd för Scout på käll servern.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** base-paketet har:
>     * Ett uppdaterat bas installations program för konfigurations servern (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Ett grundläggande installations program för Windows huvud mål (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * För alla nya installationer använder du den nya konfigurations servern och Windows huvud mål GA-bitar.
> * Uppdatering 4 kan tillämpas direkt på 8.0.1 GA.
> * Konfigurations servern och RX-uppdateringar kan inte återställas när de har tillämpats.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3

Alla Site Recovery uppdateringar är kumulativa. Uppdatering 3 innehåller alla korrigeringar från uppdatering 1 och uppdatering 2. Uppdatering 3 kan tillämpas direkt på 8.0.1 GA. Konfigurations servern och RX-uppdateringar kan inte återställas när de har tillämpats.

#### <a name="bug-fixes-and-enhancements"></a>Fel korrigeringar och förbättringar
Uppdatering 3 åtgärdar följande problem:

* Konfigurations servern och RX är inte registrerade i valvet när de ligger bakom proxyservern.
* Antalet timmar där återställnings punkt mål (återställnings punkt mål) inte nåddes har inte uppdaterats i hälso rapporten.
* Konfigurations servern synkroniseras inte med RX när det finns information om ESX-maskinvara, eller nätverksinformation, som innehåller UTF-8-tecken.
* Domänkontrollanter i Windows Server 2008 R2 startar inte efter återställningen.
* Offlinesynkronisering fungerar inte som förväntat.
* Efter den virtuella datorn går det inte att ta bort replikeringen i konfigurations Server konsolen under en längre tid. Användare kan inte slutföra återställnings-eller återställnings åtgärder.
* De övergripande ögonblicks bild åtgärderna för konsekvens jobbet har optimerats för att hjälpa till att minska program från koppling, till exempel SQL Server klienter.
* Prestanda för konsekvens verktyget (VACP.exe) har förbättrats. Den minnes användning som krävs för att skapa ögonblicks bilder i Windows har minskat.
* Push-installations tjänsten kraschar när lösen ordet är större än 16 tecken.
* vContinuum kontrollerar inte och efterfrågar inga nya vCenter-autentiseringsuppgifter när autentiseringsuppgifterna ändras.
* I Linux laddar inte Master Target cache Manager (cachemgr) filer från processervern. Detta resulterar i en begränsning av replikeringstrafiken.
* När MSCS-disken (Physical failover Cluster) inte är samma på alla noder har replikering inte angetts för några av kluster volymerna. Klustret måste skyddas om för att kunna utnyttja den här korrigeringen.  
* SMTP-funktionen fungerar inte som förväntat, efter att RX har uppgraderats från Scout 7,1 till Scout 8.0.1.
* Mer statistik har lagts till i loggen för återställnings åtgärden för att spåra den tid det tar att slutföra den.
* Stöd har lagts till för Linux-operativsystem på käll servern:
  * Red Hat Enterprise Linux (RHEL) 6 uppdatering 7
  * CentOS 6 uppdatering 7
* Konfigurations servern och RX-konsolerna visar nu meddelanden för paret, som går in i bitmappsläge.
* Följande säkerhets korrigeringar har lagts till i RX:
    * Auktorisering kringgås via parameter manipulation: begränsad åtkomst till icke-tillämpliga användare.
    * Förfalskning av begäran mellan webbplatser: Page-token-konceptet implementerades och genererar slumpvis för varje sida. Det innebär att det bara finns en enda inloggnings instans för samma användare och att sid uppdateringen inte fungerar. I stället omdirigeras den till instrument panelen.
    * Skadlig fil uppladdning: filerna är begränsade till vissa tillägg: z, AIFF, ASF, AVI, BMP, CSV, dok, docx, FLA, FLV, GIF, GZ, gzip, JPEG, jpg, logg, Mid, MOV, MP3, MP4, MPC, MPEG, MPG, ODS, ODT, PDF, PNG, ppt, pptx, PXD, QT, ram, rar, RM, RMI, RMVB, RTF, SDC, SITD, SWF, SXC, SXW, tjär, TGZ, TIF, TIFF, txt, VSD, WAV, WMA, WMV, xls , xlsx, XML och zip.
    * Beständiga skript för Cross-Site: angivna verifieringar har lagts till.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 uppdatering 2 (uppdatering 03Dec15)

Korrigeringar i uppdatering 2 inkluderar:

* **Konfigurations Server**: problem som förhindrade den 30 dagars kostnads fria mätnings funktionen från att fungera som förväntat, när konfigurations servern registrerades till Azure Site Recovery valv.
* **Enhetlig agent**: korrigera ett problem i uppdatering 1 som ledde till att uppdateringen inte installeras på huvud mål servern, under uppgradering från version 8,0 till 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 uppdatering 1
Uppdatering 1 innehåller följande fel korrigeringar och nya funktioner:

* 31 dagars kostnads fritt skydd per Server instans. På så sätt kan du testa funktioner eller ställa in ett koncept bevis.
* Alla åtgärder på servern, inklusive redundans och återställning, är kostnads fria under de första 31 dagarna. Tiden börjar när en server först skyddas med Site Recovery Scout. Från 32nd-dagen debiteras varje skyddad Server enligt standard instans frekvensen för Site Recovery skydd till en kundägda webbplats.
* När som helst är antalet skyddade servrar som debiteras för närvarande tillgängligt på **instrument panelen** i valvet.
* Stöd har lagts till för vSphere Command-Line Interface (vCLI) 5,5 uppdatering 2.
* Stöd har lagts till för de här Linux-operativ systemen på käll servern:
    * RHEL 6 uppdatering 6
    * RHEL 5 uppdatering 11
    * CentOS 6 uppdatering 6
    * CentOS 5 uppdatering 11
* Fel korrigeringar för att åtgärda följande problem:
  * Valv registreringen misslyckades för konfigurations servern eller RX-servern.
  * Kluster volymer visas inte som förväntat när klustrade virtuella datorer skyddas när de återupptas.
  * Det går inte att återställa efter fel när huvud mål servern finns på en annan ESXi-server än den lokala produktions-VM: en.
  * Konfigurations filens behörigheter ändras när du uppgraderar till 8.0.1. Den här ändringen påverkar skydd och åtgärder.
  * Tröskelvärdet för omsynkronisering tillämpas inte som förväntat, vilket orsakar inkonsekvent replikering.
  * Inställningarna för återställningen visas inte korrekt i konfigurations Server konsolen. Det okomprimerade datavärdet visar felaktigt det komprimerade värdet.
  * Åtgärden ta bort tas inte bort som förväntat i guiden vContinuum och replikeringen tas inte bort från konsolen för konfigurations servern.
  * I guiden vContinuum avmarkeras disken automatiskt när du klickar på **information** i vyn disk, under skyddet av virtuella MSCS-datorer.
  * I scenariot för fysisk till virtuell (P2V) är nödvändiga HP-tjänster (till exempel CIMnotify och CqMgHost) inte flyttade till manuell i VM-återställning. Det här problemet resulterar i ytterligare start tid.
  * Linux VM-skydd Miss lyckas när det finns fler än 26 diskar på huvud mål servern.

