---
title: Hantera Azure Log Analytics-agenten
description: I den här artikeln beskrivs de olika hanterings aktiviteter som du vanligt vis utför under livs cykeln för den Log Analytics Windows-eller Linux-Agent som distribueras på en dator.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: ca36c35d859e651c0d949f4b7fbb28137d01af90
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734969"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Hantera och underhålla Log Analytics-agenten för Windows och Linux

Efter den första distributionen av Log Analytics Windows-eller Linux-agenten i Azure Monitor kan du behöva konfigurera om agenten, uppgradera den eller ta bort den från datorn om den har nått indragnings fasen i livs cykeln. Du kan enkelt hantera dessa rutin underhålls aktiviteter manuellt eller via Automation, vilket minskar både drift fel och kostnader.

## <a name="upgrading-agent"></a>Uppgraderar agent

Log Analytics agent för Windows och Linux kan uppgraderas till den senaste versionen manuellt eller automatiskt beroende på distributions scenariot och den miljö där den virtuella datorn körs. Följande metoder kan användas för att uppgradera agenten.

| Miljö | Installations metod | Uppgraderingsmetod |
|--------|----------|-------------|
| Azure VM | Log Analytics agent VM-tillägg för Windows/Linux | Agenten uppgraderas automatiskt som standard om du inte har konfigurerat din Azure Resource Manager-mall för att välja att ange egenskapen *aktiverat autoupgrademinorversion* till **false**. |
| Anpassade Azure VM-avbildningar | Manuell installation av Log Analytics agent för Windows/Linux | Uppdatering av virtuella datorer till den senaste versionen av agenten måste utföras från kommando raden som kör Windows Installer-paketet eller Linux-paketet för självextraherande och installerbara gränssnitt.|
| Icke-virtuella datorer i Azure | Manuell installation av Log Analytics agent för Windows/Linux | Uppdatering av virtuella datorer till den senaste versionen av agenten måste utföras från kommando raden som kör Windows Installer-paketet eller Linux-paketet för självextraherande och installerbara gränssnitt. |

### <a name="upgrade-windows-agent"></a>Uppgradera Windows-agent 

Om du vill uppdatera agenten på en virtuell Windows-dator till den senaste versionen som inte installerats med Log Analytics VM-tillägget, kan du antingen köra från kommando tolken, skriptet eller någon annan Automation-lösning eller genom att använda installations guiden för MMASetup- \<platform\> MSI.  

Du kan ladda ned den senaste versionen av Windows-agenten från din Log Analytics arbets yta genom att utföra följande steg.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Klicka på **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics arbets ytor**.

3. I listan med Log Analytics arbets ytor väljer du arbets ytan.

4. I arbets ytan Log Analytics väljer du **Avancerade inställningar** och väljer sedan **anslutna källor** och slutligen **Windows-servrar**.

5. På sidan **Windows-servrar** väljer du lämplig version av **Windows-agenten** som ska laddas ned beroende på processor arkitekturen i Windows-operativsystemet.

>[!NOTE]
>Under uppgraderingen av Log Analytics agent för Windows, stöder den inte konfiguration eller omkonfiguration av en arbets yta att rapportera till. Om du vill konfigurera agenten måste du följa en av de metoder som stöds under [lägga till eller ta bort en arbets yta](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Uppgradera med installations guiden

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Kör **MMASetup- \<platform\> . exe** för att starta installations guiden.

3. Klicka på **Nästa** på den första sidan i installations guiden.

4. I dialog rutan **installations program för Microsoft Monitoring Agent** klickar **du på jag accepterar** att godkänna licens avtalet.

5. I dialogrutan **Installationsprogram för Microsoft Monitoring Agent** klickar du på **Uppgradera**. På statussidan visas uppgraderingsförloppet.

6. När **Microsoft Monitoring Agent-konfigurationen har slutförts.** visas klickar du på **Slutför**.

#### <a name="to-upgrade-from-the-command-line"></a>Uppgradera från kommando raden

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Om du vill extrahera installationsfilerna för agenten, kan du köra från en upphöjd kommando tolk `MMASetup-<platform>.exe /c` och uppmana dig att ange sökvägen för att extrahera filer till. Alternativt kan du ange sökvägen genom att skicka argumenten `MMASetup-<platform>.exe /c /t:<Full Path>` .

3. Kör följande kommando, där D:\ är platsen för uppgraderingsloggfilen.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Uppgradera Linux-agenten 

Uppgradering från tidigare versioner (>1.0.0-47) stöds. Genom att utföra installationen med `--upgrade` kommandot uppgraderas alla komponenter i agenten till den senaste versionen.

Kör följande kommando för att uppgradera agenten.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Lägga till eller ta bort en arbets yta

### <a name="windows-agent"></a>Windows-agent
Stegen i det här avsnittet är nödvändiga om du inte bara vill konfigurera om Windows-agenten för att rapportera till en annan arbets yta eller ta bort en arbets yta från dess konfiguration, men även när du vill konfigurera agenten att rapportera till fler än en arbets yta (kallas ofta multi-värdar). Att konfigurera Windows-agenten att rapportera till flera arbets ytor kan bara utföras efter den första installationen av agenten och med de metoder som beskrivs nedan.    

#### <a name="update-settings-from-control-panel"></a>Uppdatera inställningar från kontroll panelen

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Öppna **kontroll panelen**.

3. Välj **Microsoft Monitoring Agent** och klicka sedan på fliken **Azure-Log Analytics** .

4. Om du tar bort en arbets yta markerar du den och klickar på **ta bort**. Upprepa det här steget för en annan arbets yta som du vill att agenten ska sluta rapportera till.

5. Om du lägger till en arbets yta klickar du på **Lägg till** och i dialog rutan **lägg till en Log Analytics arbets yta** klistrar du in arbetsyte-ID och arbets ytans nyckel (primär nyckel). Om datorn ska rapportera till en Log Analytics-arbetsyta i Azure Government-molnet väljer du Azure US Government i listrutan Azure Cloud.

6. Spara ändringarna genom att klicka på **OK**.

#### <a name="remove-a-workspace-using-powershell"></a>Ta bort en arbets yta med PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Lägg till en arbets yta i Azure Commercial med PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Lägg till en arbets yta i Azure för amerikanska myndigheter med hjälp av PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Om du har använt kommando raden eller skriptet tidigare för att installera eller konfigurera agenten `EnableAzureOperationalInsights` ersattes av `AddCloudWorkspace` och `RemoveCloudWorkspace` .
>

### <a name="linux-agent"></a>Linux-Agent
Följande steg visar hur du konfigurerar om Linux-agenten om du väljer att registrera den med en annan arbets yta eller ta bort en arbets yta från dess konfiguration.

1. Kör följande kommando för att kontrol lera att den är registrerad på en arbets yta:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Den bör returnera en status som liknar följande exempel:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Det är viktigt att statusen även visar att agenten körs, annars slutförs inte följande steg för att konfigurera om agenten.

2. Om den redan är registrerad på en arbets yta tar du bort den registrerade arbets ytan genom att köra följande kommando. Annars fortsätter du till nästa steg om den inte är registrerad.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Om du vill registrera dig för en annan arbets yta kör du följande kommando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Kör följande kommando för att kontrol lera att ändringarna har genomförts:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Den bör returnera en status som liknar följande exempel:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Agent tjänsten behöver inte startas om för att ändringarna ska börja gälla.

## <a name="update-proxy-settings"></a>Uppdatera proxyinställningar
Om du vill konfigurera agenten för att kommunicera med tjänsten via en proxyserver eller [Log Analytics Gateway](./gateway.md) efter distributionen använder du någon av följande metoder för att slutföra uppgiften.

### <a name="windows-agent"></a>Windows-agent

#### <a name="update-settings-using-control-panel"></a>Uppdatera inställningar med kontroll panelen

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Öppna **kontroll panelen**.

3. Välj **Microsoft Monitoring Agent** och klicka sedan på fliken **proxyinställningar** .

4. Klicka på **Använd en proxyserver** och ange URL och portnummer för proxyservern eller gatewayen. Om proxyservern eller Log Analytics-gatewayen kräver autentisering anger du användarnamn och lösenord för att autentisera och klickar sedan på **OK**.

#### <a name="update-settings-using-powershell"></a>Uppdatera inställningar med PowerShell

Kopiera följande PowerShell-exempel PowerShell-kod, uppdatera den med information som är speciell för din miljö och spara den med ett PS1 fil namns tillägg. Kör skriptet på varje dator som ansluter direkt till Log Analytics arbets ytan i Azure Monitor.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Linux-Agent
Utför följande steg om dina Linux-datorer behöver kommunicera via en proxyserver eller Log Analytics Gateway. Konfigurationsvärdet för proxyn har följande syntax `[protocol://][user:password@]proxyhost[:port]`. Egenskapen *proxyhost* accepterar ett fullständigt domännamn eller en fullständig IP-adress för proxyservern.

1. Redigera filen `/etc/opt/microsoft/omsagent/proxy.conf` genom att köra följande kommandon och ändra värdena enligt dina specifika inställningar.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Starta om agenten genom att köra följande kommando:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Avinstallera agent
Använd någon av följande procedurer för att avinstallera Windows-eller Linux-agenten med hjälp av kommando raden eller installations guiden.

### <a name="windows-agent"></a>Windows-agent

#### <a name="uninstall-from-control-panel"></a>Avinstallera från kontroll panelen
1. Logga in på datorn med ett konto som har administratörs behörighet.

2. I **kontroll panelen** klickar du på **program och funktioner**.

3. Klicka på **Microsoft Monitoring Agent** i **program och funktioner**, klicka på **Avinstallera** och klicka sedan på **Ja**.

>[!NOTE]
>Installations guiden för agenten kan också köras genom att dubbelklicka på **MMASetup- \<platform\> . exe**, som är tillgänglig för nedladdning från en arbets yta i Azure Portal.

#### <a name="uninstall-from-the-command-line"></a>Avinstallera från kommando raden
Den hämtade filen för agenten är ett fristående installations paket som skapas med IExpress. Installations programmet för agenten och de filer som stöds finns i paketet och måste extraheras för att avinstallationen ska slutföras med kommando raden som visas i följande exempel.

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Om du vill extrahera installationsfilerna för agenten, kan du köra från en upphöjd kommando tolk `extract MMASetup-<platform>.exe` och uppmana dig att ange sökvägen för att extrahera filer till. Alternativt kan du ange sökvägen genom att skicka argumenten `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>` . Mer information om kommando rads växlar som stöds av IExpress finns i [kommando rads växlar för IExpress](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/197147) och uppdatera sedan exemplet så att det passar dina behov.

3. Skriv i prompten `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb` .

### <a name="linux-agent"></a>Linux-Agent
Kör följande kommando för att ta bort agenten på Linux-datorn. Argumentet *--purge* tar bort agenten och dess konfiguration fullständigt.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Konfigurera agenten att rapportera till en Operations Manager hanterings grupp

### <a name="windows-agent"></a>Windows-agent
Utför följande steg för att konfigurera Log Analytics agent för Windows att rapportera till en System Center Operations Manager hanterings grupp.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Öppna **kontroll panelen**.

3. Klicka på **Microsoft Monitoring Agent** och klicka sedan på fliken **Operations Manager** .

4. Om Operations Manager-servrarna har integration med Active Directory klickar du på **Uppdatera hanterings grupp tilldelningar automatiskt från AD DS**.

5. Klicka på **Lägg till** för att öppna dialog rutan **Lägg till en hanterings grupp** .

6. I fältet **namn på hanterings grupp** anger du namnet på hanterings gruppen.

7. I fältet **primär hanterings Server** anger du dator namnet för den primära hanterings servern.

8. I fältet **hanterings server port** anger du TCP-portnumret.

9. Under **agent åtgärds konto** väljer du antingen lokalt system konto eller ett lokalt domän konto.

10. Klicka på **OK** för att stänga dialog rutan **Lägg till en hanterings grupp** och klicka sedan på **OK** för att stänga dialog rutan **Egenskaper för Microsoft Monitoring Agent** .

### <a name="linux-agent"></a>Linux-Agent
Utför följande steg för att konfigurera Log Analytics agent för Linux att rapportera till en System Center Operations Manager hanterings grupp.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Redigera filen `/etc/opt/omi/conf/omiserver.conf`

2. Se till att raden som börjar med `httpsport=` definierar port 1270. Till exempel: `httpsport=1270`

3. Starta om OMI-servern: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Nästa steg

- Granska [fel sökning av Linux-agenten](agent-linux-troubleshoot.md) om det uppstår problem när du installerar eller hanterar Linux-agenten.

- Granska [fel sökning av Windows-agenten](agent-windows-troubleshoot.md) om det uppstår problem när du installerar eller hanterar Windows-agenten.