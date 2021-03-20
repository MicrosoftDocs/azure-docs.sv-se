---
title: Felsök problem med replikering för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller felsöknings information för vanliga replikeringsfel vid haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 8b44a1d6119cc658b9460e0a52fa0629f759964a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336213"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Felsöka problem med replikering för virtuella VMware-datorer och fysiska servrar

Den här artikeln beskriver några vanliga problem och specifika fel som du kan stöta på när du replikerar lokala virtuella VMware-datorer och fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Steg 1: övervaka process Server hälsa

Site Recovery använder [processervern](vmware-physical-azure-config-process-server-overview.md#process-server) för att ta emot och optimera replikerade data och skicka dem till Azure.

Vi rekommenderar att du övervakar hälso tillståndet för processervern i portalen för att se till att de är anslutna och fungerar som de ska och att replikeringen fortskrider för käll datorerna som är kopplade till processervern.

- [Lär dig mer om](vmware-physical-azure-monitor-process-server.md) övervakning av process servrar.
- [Granska metodtips](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Felsök](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) process Server hälsa.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Steg 2: Felsök problem med anslutning och replikering

Inledande och pågående replikeringsfel orsakas ofta av anslutnings problem mellan käll servern och processervern eller mellan processervern och Azure.

Lös problemen genom att [Felsöka anslutningar och replikering](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Steg 3: Felsöka käll datorer som inte är tillgängliga för replikering

När du försöker välja käll datorn för att aktivera replikering med hjälp av Site Recovery kanske datorn inte är tillgänglig av någon av följande orsaker:

* **Två virtuella datorer med samma instans-UUID**: om två virtuella datorer under vCenter har samma instans-UUID, visas den första virtuella datorn som identifieras av konfigurations servern i Azure Portal. Lös problemet genom att se till att det inte finns två virtuella datorer med samma instans-UUID. Det här scenariot visas vanligt vis i instanser där en säkerhets kopia av virtuella datorer blir aktiv och är inloggad i våra identifierings poster. Se [Azure Site Recovery VMware-till-Azure: så här rensar du dubbletter eller inaktuella poster](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) för att lösa problemet.
* **Felaktiga vCenter**-användarautentiseringsuppgifter: se till att du har lagt till rätt vCenter-autentiseringsuppgifter när du konfigurerade konfigurations servern med hjälp av OVF-mallen eller den enhetliga installationen. Information om hur du kontrollerar de autentiseringsuppgifter som du lade till under installationen finns i [ändra autentiseringsuppgifter för automatisk identifiering](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **vCenter otillräcklig behörighet**: om behörigheterna som har angetts för åtkomst till vCenter inte har de behörigheter som krävs, kan det hända att det inte går att identifiera virtuella datorer. Kontrol lera att behörigheterna som beskrivs i [förbereda ett konto för automatisk identifiering](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) läggs till i vCenter-användarkontot.
* **Azure Site Recovery hanterings servrar**: om den virtuella datorn används som hanterings Server under en eller flera av följande roller – konfigurations servern/Scale-Out processervern/huvud mål servern, kan du inte välja den virtuella datorn från portalen. Hanterings servrar kan inte replikeras.
* **Redan skyddad/misslyckad via Azure Site Recovery Services**: om den virtuella datorn redan är skyddad eller misslyckad via Site Recovery, är den virtuella datorn inte tillgänglig för att välja skydd i portalen. Se till att den virtuella dator som du söker efter i portalen inte redan skyddas av någon annan användare eller under en annan prenumeration.
* **vCenter är inte ansluten**: kontrol lera om vCenter är i anslutet tillstånd. För att verifiera går du till Recovery Services valv > Site Recovery infrastruktur > konfigurations servrar > klickar på respektive konfigurations Server > ett blad öppnas till höger med information om associerade servrar. Kontrol lera om vCenter är ansluten. Om det är i läget "inte ansluten" löser du problemet och [uppdaterar sedan konfigurations servern](vmware-azure-manage-configuration-server.md#refresh-configuration-server) på portalen. Därefter visas den virtuella datorn i portalen.
* **ESXi** avstängd: om ESXi-värden som den virtuella datorn finns i är avstängd, kommer den virtuella datorn inte att listas eller går inte att välja i Azure Portal. Starta ESXi-värden och [Uppdatera konfigurations servern](vmware-azure-manage-configuration-server.md#refresh-configuration-server) på portalen. Därefter visas den virtuella datorn i portalen.
* **Väntar på omstart**: om det finns en väntande omstart på den virtuella datorn kommer du inte att kunna välja datorn på Azure Portal. Se till att slutföra de väntande omstarts aktiviteterna, [Uppdatera konfigurations servern](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Därefter visas den virtuella datorn i portalen.
* **IP hittades inte**: om den virtuella datorn inte har en giltig IP-adress som är kopplad till den, kan du inte välja datorn på Azure Portal. Se till att tilldela en giltig IP-adress till den virtuella datorn och [Uppdatera konfigurations servern](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Därefter visas den virtuella datorn i portalen.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Felsöka skyddade virtuella datorer som är grå i portalen

Virtuella datorer som replikeras under Site Recovery är inte tillgängliga i Azure Portal om det finns dubbla poster i systemet. Information om hur du tar bort inaktuella poster och löser problemet finns i [Azure Site Recovery VMware-till-Azure: så här rensar du dubbletter eller inaktuella poster](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Ingen kraschad återställnings punkt är tillgänglig för den virtuella datorn under de senaste XXX minuterna

Några av de vanligaste problemen visas nedan

### <a name="initial-replication-issues-error-78169"></a>Problem med inledande replikering [fel 78169]

Över ett ovan garanterar att det inte finns några problem med anslutning, bandbredd eller tidssynkronisering, se till att:

- Inga antivirus program blockerar Azure Site Recovery. Läs [mer](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) om undantag från mappar som krävs för Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Käll datorer med hög omsättning [fel 78188]

Möjliga orsaker:
- Data ändrings hastigheten (skrivna byte/s) på de angivna diskarna i den virtuella datorn är mer än [Azure Site Recovery gränser som stöds](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) för lagrings konto typen för replikering.
- Det finns en plötslig ökning i omsättnings takten på grund av vilken hög mängd data som väntar på uppladdning.

Så här löser du problemet:
- Se till att mål lagrings konto typen (standard eller Premium) har tillhandahållits enligt kravet på omsättnings skatt vid källan.
- Om du redan replikerar till en Premium-hanterad disk (asrseeddisk-typ) kontrollerar du att storleken på disken stöder den observerade omsättnings takten enligt Site Recovery gränser. Du kan öka storleken på asrseeddisk om det behövs. Följ stegen nedan:
    - Gå till bladet diskar på den berörda replikerade datorn och kopiera replik diskens namn
    - Navigera till den här replik hanterade disken
    - Du kan se en banderoll på bladet översikt som anger att en SAS-webbadress har skapats. Klicka på den här banderollen och Avbryt exporten. Ignorera det här steget om du inte ser banderollen.
    - Så snart SAS-URL: en har återkallats går du till konfigurations bladet för den hanterade disken och ökar storleken så att Azure Site Recovery stöder den observerade omsättnings takten på käll disken
- Om den observerade omsättningen är tillfällig väntar du några timmar på att den väntande data uppladdningen ska fångas upp och skapa återställnings punkter.
- Om disken innehåller icke-kritiska data, t. ex. tillfälliga loggar, test data osv., bör du överväga att flytta dessa data någon annan stans eller helt undanta den här disken från replikeringen
- Om problemet kvarstår ska du använda Site Recovery [Deployment Planner](site-recovery-deployment-planner.md#overview) för att planera replikeringen.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Käll datorer utan pulsslag [fel 78174]

Detta inträffar när Azure Site Recovery mobilitets agenten på käll datorn inte kommunicerar med konfigurations servern (CS).

Lös problemet genom att använda följande steg för att kontrol lera nätverks anslutningen från den virtuella käll datorn till konfigurations servern:

1. Kontrol lera att käll datorn körs.
2. Logga in på käll datorn med ett konto som har administratörs behörighet.
3. Kontrol lera att följande tjänster körs och starta inte om tjänsterna:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application Service
4. På käll datorn granskar du loggarna på platsen för fel information:

    *C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\svagents \* . log*

### <a name="process-server-with-no-heartbeat-error-806"></a>Processerver utan pulsslag [fel 806]
Om det inte finns något pulsslag från processervern (PS) kontrollerar du att:
1. Den virtuella PS-datorn är igång
2. Följande loggar finns på PS för fel information:

    *C:\ProgramData\ASR\home\svsystems\eventmanager \* . log*\
    särskilt
    *C:\ProgramData\ASR\home\svsystems\ monitor_protection \* . log*

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Huvud mål server utan pulsslag [fel 78022]

Detta inträffar när Azure Site Recovery mobilitets agent på huvud målet inte kommunicerar med konfigurations servern.

Lös problemet genom att följa stegen nedan för att kontrol lera tjänst status:

1. Kontrol lera att den virtuella huvud mål datorn körs.
2. Logga in på den virtuella huvud mål datorn med ett konto som har administratörs behörighet.
    - Kontrol lera att svagents-tjänsten körs. Om den körs startar du om tjänsten
    - Fel information finns i loggarna på platsen:

        *C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\svagents \* . log*
3. Om du vill registrera huvud målet med konfigurations servern navigerar du till mappen **%programdata%\ASR\Agent** och kör följande kommando tolk:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Fel-ID 78144 – ingen programkonsekvent återställnings punkt är tillgänglig för den virtuella datorn under de senaste XXX minuterna

Förbättringar har gjorts i Mobility agent [9,23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher)  &  [9,27](site-recovery-whats-new.md#update-rollup-39) -versioner för att hantera problem med VSS-installation. Se till att du har de senaste versionerna för bästa vägledning om hur du felsöker VSS-fel.

Några av de vanligaste problemen visas nedan

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Orsak 1: kända problem i SQL Server 2008/2008 R2
**Så här åtgärdar du** : det finns ett känt problem med SQL Server 2008/2008 R2. Det går inte att säkerhetskopiera den här KB-artikeln [Azure Site Recovery agent eller annan VSS-säkerhetskopiering som inte är en komponent för en server som är värd för SQL Server 2008](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Orsak 2: Azure Site Recovery jobb fungerar inte på servrar som är värdar för någon version av SQL Server-instanser med AUTO_CLOSE databaser
**Så här åtgärdar du** : se KB- [artikel](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Orsak 3: kända problem i SQL Server 2016 och 2017
**Så här åtgärdar du** : se KB- [artikel](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component)

#### <a name="cause-4-app-consistency-not-enabled-on-linux-servers"></a>Orsak 4: App-Consistency inte aktiverat på Linux-servrar
**Så här åtgärdar du** : Azure Site Recovery för Linux-åtgärds system har stöd för anpassade skript för program-konsekvens. Det anpassade skriptet med för-och-post-alternativen används av Azure Site Recovery Mobility-agenten för program konsekvens. Så [här](./site-recovery-faq.md#replication) aktiverar du det.

### <a name="more-causes-due-to-vss-related-issues"></a>Fler orsaker till följd av VSS-relaterade problem:

Om du vill felsöka ytterligare kan du kontrol lera filerna på käll datorn för att få en exakt felkod för felet:

*C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log*

Hur hittar du felen i filen?
Sök efter strängen "vacpError" genom att öppna filen vacp. log i en redigerare

`Ex: `**`vacpError`**`:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|`

I ovanstående exempel **2147754994** är felkoden som visar dig om felet som visas nedan

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS-skrivaren är inte installerad-fel 2147221164

*Så här åtgärdar* du: om du vill skapa en program konsekvenss etikett använder Azure Site Recovery Microsoft Volume Shadow Copy Service (VSS). Den installerar en VSS-Provider för åtgärden att ta ögonblicks bilder av program konsekvens. Den här VSS-providern installeras som en tjänst. Om tjänsten VSS Provider inte är installerad, Miss lyckas skapandet av program konsekvensen med fel-ID 0x80040154 "-klassen har inte registrerats". </br>
Se [artikeln om fel sökning av VSS Writer-installation](./vmware-azure-troubleshoot-push-install.md#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS-skrivaren är inaktive rad-fel 2147943458

**Så här åtgärdar** du: om du vill skapa en program konsekvenss etikett använder Azure Site Recovery Microsoft Volume Shadow Copy Service (VSS). Den installerar en VSS-Provider för åtgärden att ta ögonblicks bilder av program konsekvens. Den här VSS-providern installeras som en tjänst. Om tjänsten VSS Provider är inaktive rad kan inte skapandet av program konsekvens skapas med fel-ID: t "den angivna tjänsten är inaktive rad och kan inte startas (0x80070422)". </br>

- Om VSS är inaktiverat
    - Kontrol lera att start typen för tjänsten VSS Provider är inställd på **Automatisk**.
    - Starta om följande tjänster:
        - VSS-tjänst
        - Azure Site Recovery VSS-Provider
        - VDS-tjänst

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS-PROVIDER NOT_REGISTERED-fel 2147754756

**Så här åtgärdar** du: om du vill skapa en program konsekvenss etikett använder Azure Site Recovery Microsoft Volume Shadow Copy Service (VSS).
Kontrol lera om tjänsten Azure Site Recovery VSS Provider är installerad eller inte. </br>

- Försök att installera providern med följande kommandon:
- Avinstallera befintlig provider: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Uninstall. cmd
- Installera om: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Install. cmd

Kontrol lera att start typen för tjänsten VSS Provider är inställd på **Automatisk**.
    - Starta om följande tjänster:
        - VSS-tjänst
        - Azure Site Recovery VSS-Provider
        - VDS-tjänst

## <a name="error-id-95001---insufficient-permissions-found"></a>Fel-ID 95001-otillräckliga behörigheter hittades

Det här felet uppstår när du försöker aktivera replikering och programmapparna inte har tillräcklig behörighet.

**Korrigera**: Lös problemet genom att kontrol lera att IUSR-användaren har ägar rollen för alla nedanstående mappar –

- *C\ProgramData\Microsoft Azure Site Recovery\private*
- Installations katalogen. Om installations katalogen till exempel är F Drive, anger du rätt behörighet till-
    - *F:\Program-filer (x86) \Microsoft Azure Site Recovery\home\svsystems*
- Mappen *\pushinstallsvc* i installations katalogen. Om installations katalogen till exempel är F Drive, anger du rätt behörighet till-
    - *F:\Program-filer (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc*
- Mappen *\Etc* i installations katalogen. Om installations katalogen till exempel är F Drive, anger du rätt behörighet till-
    - *F:\Program-filer (x86) \Microsoft Azure Site Recovery\home\svsystems\etc*
- *C:\Temp*
- *C:\thirdparty\php5nts*
- Alla objekt under sökvägen nedan –
    - *C:\thirdparty\rrdtool-1.2.15-win32-perl58\rrdtool\Release\**

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du publicera din fråga på [sidan Microsoft Q&en fråga för Azure Site Recovery](/answers/topics/azure-site-recovery.html). Vi har en aktiv community och en av våra tekniker kan hjälpa dig.
