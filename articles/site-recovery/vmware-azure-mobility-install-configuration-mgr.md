---
title: Automatisera mobilitets tjänsten för haveri beredskap vid installation i Azure Site Recovery
description: Så här installerar du mobilitets tjänsten för VMware/Physical Server haveri beredskap med Azure Site Recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: 2159ab8c2639f0f87fd53e8559dad518a3daa663
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92544825"
---
# <a name="automate-mobility-service-installation"></a>Automatisera mobilitets tjänst installation

Den här artikeln beskriver hur du automatiserar installation och uppdateringar av mobilitets tjänst agenten i [Azure Site Recovery](site-recovery-overview.md).

När du distribuerar Site Recovery för haveri beredskap för lokala virtuella VMware-datorer och fysiska servrar till Azure, installerar du mobilitets tjänst agenten på varje dator som du vill replikera. Mobilitets tjänsten samlar in data skrivningar på datorn och vidarebefordrar dem till den Site Recovery processervern för replikering. Du kan distribuera mobilitets tjänsten på några få sätt:

- **Push-installation**: låt Site Recovery installera mobilitets tjänst agenten när du aktiverar replikering för en dator i Azure Portal.
- **Manuell installation**: installera mobilitets tjänsten manuellt på varje dator. [Läs mer](vmware-physical-mobility-service-overview.md) om push och manuell installation.
- **Automatiserad distribution**: automatiserad installation med verktyg för program varu distribution, till exempel Microsoft Endpoint Configuration Manager eller verktyg från tredje part, till exempel JetPatch.

Automatisk installation och uppdatering tillhandahåller en lösning om:

- Din organisation tillåter inte push-installation på skyddade servrar.
- Företags principen kräver att lösen ord ändras regelbundet. Du måste ange ett lösen ord för push-installationen.
- Säkerhets principen tillåter inte tillägg av brand Väggs undantag för vissa datorer.
- Du agerar som en värd tjänst leverantör och vill inte tillhandahålla autentiseringsuppgifter för kund datorn som behövs för push-installation med Site Recovery.
- Du måste skala agent installationer till flera servrar samtidigt.
- Du vill schemalägga installationer och uppgraderingar under planerat underhålls fönster.

## <a name="prerequisites"></a>Förutsättningar

Om du vill automatisera installationen behöver du följande objekt:

- En distribuerad program varu installations lösning som [Configuration Manager](/configmgr/) eller [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Distributions krav på plats i [Azure](tutorial-prepare-azure.md) och [lokalt](vmware-azure-tutorial-prepare-on-premises.md) för katastrof återställning i VMware, eller för haveri beredskap för [fysiska servrar](physical-azure-disaster-recovery.md) . Granska [support kraven](vmware-physical-azure-support-matrix.md) för haveri beredskap.

## <a name="prepare-for-automated-deployment"></a>Förbered för automatisk distribution

I följande tabell sammanfattas verktyg och processer för automatisering av mobilitets tjänst distributionen.

**Verktyg** | **Information** | **Instruktioner**
--- | --- | ---
**Configuration Manager** | 1. kontrol lera att du har de [krav](#prerequisites) som anges ovan på plats. <br/><br/> 2. distribuera haveri beredskap genom att konfigurera käll miljön, inklusive Ladda ned en ägg fil för att distribuera Site Recovery konfigurations servern som en virtuell VMware-dator med hjälp av en OVF-mall.<br/><br/> 3. du registrerar konfigurations servern med Site Recovery-tjänsten, ställer in Azure-miljön och konfigurerar en replikeringsprincip.<br/><br/> 4. för distribution av automatiserad mobilitets tjänst skapar du en nätverks resurs som innehåller konfigurations serverns lösen fras och mobilitets tjänstens installationsfiler.<br/><br/> 5. du skapar ett Configuration Manager-paket som innehåller installationen eller uppdateringarna och förbereder distributionen av mobilitets tjänsten.<br/><br/> 6. du kan sedan aktivera replikering till Azure för de datorer där mobilitets tjänsten är installerad. | [Automatisera med Configuration Manager](#automate-with-configuration-manager)
**JetPatch** | 1. kontrol lera att du har de [krav](#prerequisites) som anges ovan på plats. <br/><br/> 2. distribuera haveri beredskap genom att konfigurera käll miljön, inklusive Ladda ned och distribuera JetPatch-Agent Manager för Azure Site Recovery i din Site Recovery-miljö med hjälp av en OVF-mall.<br/><br/> 3. du registrerar konfigurations servern med Site Recovery, ställer in Azure-miljön och konfigurerar en replikeringsprincip.<br/><br/> 4. för automatisk distribution initierar och slutför du konfigurationen av JetPatch Agent Manager.<br/><br/> 5. i JetPatch kan du skapa en Site Recovery-princip för att automatisera distributionen och uppgraderingen av mobilitets tjänst agenten. <br/><br/> 6. du kan sedan aktivera replikering till Azure för de datorer där mobilitets tjänsten är installerad. | [Automatisera med JetPatch Agent Manager](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Felsöka agent installation i JetPatch](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatisera med Configuration Manager

### <a name="prepare-the-installation-files"></a>Förbereda installationsfilerna

1. Kontrol lera att du har förutsättningarna på plats.
1. Skapa en säker nätverks fil resurs (SMB-resurs) som kan nås av den dator som kör konfigurations servern.
1. I Configuration Manager [kategoriserar du de servrar](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) som du vill installera eller uppdatera mobilitets tjänsten på. En samling ska innehålla alla Windows-servrar, de andra alla Linux-servrar.
1. Skapa en mapp på nätverks resursen:

   - Skapa en mapp med namnet _MobSvcWindows_ för installation på Windows-datorer.
   - Skapa en mapp med namnet _MobSvcLinux_ för installation på Linux-datorer.

1. Logga in på Configuration Server-datorn.
1. Öppna en administrativ kommando tolk på datorns konfigurations Server.
1. Kör det här kommandot om du vill generera lösen Frass filen:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Kopiera filen _MobSvc. lösen fras_ till Windows-mappen och Linux-mappen.
1. Om du vill bläddra till mappen som innehåller installationsfilerna kör du följande kommando:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Kopiera dessa installationsfiler till nätverks resursen:

   - För Windows kopierar du _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ till _MobSvcWindows_.
   - För Linux kopierar du följande filer till _MobSvcLinux_:
     - _Microsoft-ASR_UARHEL6 -64release. tar. gz_
     - _Microsoft-ASR_UARHEL7 -64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP3-64release. tar. gz_
     - _Microsoft-ASR_UASLES11-SP4-64release. tar. gz_
     - _Microsoft-ASR_UAOL6 -64release. tar. gz_
     - _Microsoft-ASR_UAUBUNTU-14.04 -64release. tar. gz_

1. Kopiera koden till Windows-eller Linux-mapparna enligt beskrivningen i följande procedurer. Vi antar att:

   - Konfigurations serverns IP-adress är `192.168.3.121` .
   - Den säkra nätverks fil resursen är `\\ContosoSecureFS\MobilityServiceInstallers` .

### <a name="copy-code-to-the-windows-folder"></a>Kopiera kod till Windows-mappen

Kopiera följande kod:

- Spara koden i _MobSvcWindows_ -mappen som _install.bat_.
- Ersätt `[CSIP]` plats hållarna i det här skriptet med de faktiska värdena för IP-adressen för konfigurations servern.
- Skriptet stöder nya installationer av mobilitets tjänst agenten och uppdateringar av agenter som redan har installerats.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Kopiera kod till Linux-mappen

Kopiera följande kod:

- Spara koden i _MobSvcLinux_ -mappen som _install_linux. sh_.
- Ersätt `[CSIP]` plats hållarna i det här skriptet med de faktiska värdena för IP-adressen för konfigurations servern.
- Skriptet stöder nya installationer av mobilitets tjänst agenten och uppdateringar av agenter som redan har installerats.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Skapa ett paket

1. Logga in på Configuration Manager-konsolen och gå till **program bibliotek**  >  **program hanterings**  >  **paket**.
1. Högerklicka på **paket**  >  **skapa paket**.
1. Ange paket information som t. ex. namn, beskrivning, tillverkare, språk och version.
1. Välj **det här paketet innehåller källfiler**.
1. Klicka på **Bläddra** och välj nätverks resursen och mappen som innehåller relevanta installations program (_MobSvcWindows_ eller _MobSvcLinux_). Välj **Nästa**.

   ![Skärm bild av guiden Skapa paket och program](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. I **Välj den program typ som du vill skapa** sidan väljer du **standard program**  >  **Nästa**.

   ![Skärm bild av guiden Skapa paket och program som visar standard program alternativet.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. I **Ange information om den här standard program** sidan anger du följande värden:

    **Parameter** | **Windows-värde** | **Linux-värde**
    --- | --- | ---
    **Namn** | Installera Microsoft Azure Mobility Service (Windows) | Installera Microsoft Azure Mobility Service (Linux).
    **Kommandorad** | install.bat | ./install_linux. sh
    **Programmet kan köra** | Huruvida en användare är inloggad eller inte | Huruvida en användare är inloggad eller inte
    **Andra parametrar** | Använd standardinställningen | Använd standardinställningen

   ![Skärm bild som visar den information som du kan ange för standard programmet.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. I **Ange kraven för det här standard programmet** utför du följande uppgifter:

   - För Windows-datorer väljer **du det här programmet kan bara köras på specificerade plattformar**. Välj sedan de [Windows-operativsystem som stöds](vmware-physical-azure-support-matrix.md#replicated-machines) och välj **Nästa**.
   - För Linux-datorer väljer **du det här programmet kan köras på alla plattformar**. Välj sedan **Nästa**.

1. Slutför guiden.

### <a name="deploy-the-package"></a>Distribuera paketet

1. Högerklicka på paketet i Configuration Manager-konsolen och välj **distribuera innehåll**.

   ![Skärm bild av Configuration Manager-konsolen](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Välj de distributions platser som paketen ska kopieras till. [Läs mer](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Slutför guiden. Paketet börjar sedan replikeras till de angivna distributions platserna.
1. När paket distributionen är klar högerklickar du på paketet > **distribuera**.

   ![Skärm bild av Configuration Manager-konsolen som visar meny alternativet distribuera.](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Välj den Windows-eller Linux-enhets samling som du skapade tidigare.
1. På sidan **Ange innehålls målet väljer du** **distributions platser**.
1. I **Ange inställningar för att styra hur program varan distribueras** , anger du ett **obligatoriskt** **syfte** .

   ![Skärm bild av guiden distribuera program vara](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. I **Ange schemat för distributionen ställer du** in ett schema. [Läs mer](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - Mobilitets tjänsten installeras i enlighet med det schema du anger.
   - Du kan undvika onödiga omstarter genom att schemalägga paket installationen i fönstret för månatlig underhåll eller fönstret program uppdateringar.

1. Konfigurera inställningarna på sidan **distributions platser** och Slutför guiden.
1. Övervaka distributions förloppet i Configuration Manager-konsolen. Gå till **övervaknings**  >  **distributioner**  >  _\<your package name\>_ .

### <a name="uninstall-the-mobility-service"></a>Avinstallera mobilitets tjänsten

Du kan skapa Configuration Manager paket för att avinstallera mobilitets tjänsten. Följande skript avinstallerar till exempel mobilitets tjänsten:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Nästa steg

[Aktivera replikering](vmware-azure-enable-replication.md) för virtuella datorer.
