---
title: Installera Log Analytics-agenten på Windows-datorer
description: Den här artikeln beskriver hur du ansluter Windows-datorer som finns i andra moln eller lokalt för att Azure Monitor med Log Analytics agent för Windows.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/03/2020
ms.openlocfilehash: 50bfed0ee4346955a9e478e7567f00610d8184f1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708670"
---
# <a name="install-log-analytics-agent-on-windows-computers"></a>Installera Log Analytics-agenten på Windows-datorer
Den här artikeln innehåller information om hur du installerar Log Analytics-agenten på Windows-datorer med följande metoder:

* Manuell installation med [installations guiden](#install-agent-using-setup-wizard) eller [kommando raden](#install-agent-using-command-line).
* [Azure Automation önskad tillstånds konfiguration (DSC)](#install-agent-using-dsc-in-azure-automation). 

>[!IMPORTANT]
> Installations metoderna som beskrivs i den här artikeln används vanligt vis för virtuella datorer lokalt eller i andra moln. Se [installations alternativ](./log-analytics-agent.md#installation-options) för mer effektiva alternativ som du kan använda för Azure Virtual Machines.

> [!NOTE]
> Om du behöver konfigurera agenten för att rapportera till fler än en arbets yta, kan detta inte utföras under den första installationen, bara efteråt genom att uppdatera inställningarna från kontroll panelen eller PowerShell enligt beskrivningen i [lägga till eller ta bort en arbets yta](agent-manage.md#adding-or-removing-a-workspace).  

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Se [Översikt över Azure Monitor agenter](agents-overview.md#supported-operating-systems) för en lista över Windows-versioner som stöds av Log Analytics agenten.

### <a name="sha-2-code-signing-support-requirement"></a>Support krav för SHA-2-kod signering 
Windows-agenten börjar uteslutande använda SHA-2-signering den 17 augusti 2020. Den här ändringen påverkar kunder som använder Log Analytics agenten på ett äldre operativ system som en del av en Azure-tjänst (Azure Monitor, Azure Automation, Azure Uppdateringshantering, Azure Ändringsspårning, Azure Security Center, Azure Sentinel, Windows Defender ATP). Ändringen kräver ingen kund åtgärd om du inte kör agenten på en äldre OS-version (Windows 7, Windows Server 2008 R2 och Windows Server 2008). Kunder som kör på en äldre OS-version måste vidta följande åtgärder på sina datorer före den 17 augusti 2020, annars kommer deras agenter att sluta skicka data till sina Log Analytics arbets ytor:

1. Installera den senaste Service Pack-versionen för ditt operativ system. De nödvändiga service pack versionerna är:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Installera SHA-2-signering av Windows-uppdateringar för ditt operativ system enligt beskrivningen i [2019 SHA-2 kod signering support krav för Windows och WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Uppdatera till den senaste versionen av Windows-agenten (version 10.20.18029).
4. Vi rekommenderar att du konfigurerar agenten att [använda TLS 1,2](agent-windows.md#configure-agent-to-use-tls-12). 

## <a name="network-requirements"></a>Nätverkskrav
Se [Log Analytics agent-översikt](./log-analytics-agent.md#network-requirements) för nätverks kraven för Windows-agenten.


   
## <a name="configure-agent-to-use-tls-12"></a>Konfigurera agenten att använda TLS 1,2
[TLS 1,2](/windows-server/security/tls/tls-registry-settings#tls-12) -protokollet säkerställer säkerheten för data som överförs för kommunikation mellan Windows-agenten och tjänsten Log Analytics. Om du installerar på ett [operativ system utan TLS 1,2 aktiverat som standard](../logs/data-security.md#sending-data-securely-using-tls-12), bör du konfigurera TLS 1,2 med stegen nedan.

1. Leta upp följande register under nyckel: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Skapa en under nyckel under **protokoll** för TLS 1,2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1,2**
3. Skapa en **klient** under nyckel under under nyckeln TLS 1,2 Protocol version som du skapade tidigare. Till exempel **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client**.
4. Skapa följande DWORD-värden under **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client**:

    * **Aktiverat** [värde = 1]
    * **DisabledByDefault** [värde = 0]  

Konfigurera .NET Framework 4,6 eller senare för att stödja säker kryptografi, som standard är inaktiverat. Den [starka kryptografi](/dotnet/framework/network-programming/tls#schusestrongcrypto) användningen använder säkrare nätverks protokoll som TLS 1,2 och blockerar protokoll som inte är säkra. 

1. Leta upp följande register under nyckel: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\ . NETFramework\v4.0.30319**.  
2. Skapa DWORD-värdet **SchUseStrongCrypto** under den här under nyckeln med värdet **1**.  
3. Leta upp följande register under nyckel: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\ . NETFramework\v4.0.30319**.  
4. Skapa DWORD-värdet **SchUseStrongCrypto** under den här under nyckeln med värdet **1**. 
5. Starta om systemet för att inställningarna ska börja gälla. 

## <a name="install-agent-using-setup-wizard"></a>Installera agent med installations guiden
Följande steg installerar och konfigurerar Log Analytics agenten i Azure och Azure Government molnet med hjälp av installations guiden för agenten på datorn. Om du vill lära dig hur du konfigurerar agenten så att den rapporterar till en System Center Operations Manager hanterings grupp läser du [distribuera Operations Manager agent med installations guiden för agenten](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. På arbets ytan Log Analytics, på sidan **Windows-servrar** som du har navigerat till tidigare, väljer du den version av Windows **agent** som du vill ladda ned beroende på processor arkitekturen i Windows-operativsystemet.   
2. Kör installationsprogrammet för att installera agenten på datorn.
2. På sidan **Välkommen** klickar du på **Nästa**.
3. På sidan **Licensvillkor** läser du licensen och klickar sedan på **Jag accepterar**.
4. På sidan **Målmapp** ändrar du eller behåller standardinstallationsmappen och klickar sedan på **Nästa**.
5. På sidan **Installationsalternativ för agent** väljer du att ansluta agenten till Azure Log Analytics och klickar sedan på **Nästa**.   
6. På sidan **Azure Log Analytics** gör du följande:
   1. Klistra in **Id för arbetsyta** och **Arbetsytenyckel (primär nyckel)** som du kopierade tidigare.  Om datorn ska rapportera till en Log Analytics-arbetsyta i Azure Government-molnet väljer du **Azure US Government** i listrutan **Azure Cloud**.  
   2. Om datorn behöver kommunicera via en proxyserver till Log Analytics-tjänsten klickar du på **Avancerat** och anger URL och portnummer för proxyservern.  Om proxyservern kräver autentisering anger du användarnamn och lösenord för att autentisera hos proxyservern och klickar sedan på **Nästa**.  
7. Klicka på **Nästa** när du har gjort de konfigurationsinställningar som krävs.<br><br> ![klistra in ID för arbetsyta och primär nyckel](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. På sidan **Klar att installera** kontrollerar du valen och klickar sedan på **Installera**.
9. På sidan **Konfigurationen har slutförts** klickar du på **Slutför**.

När du är klar visas **Microsoft Monitoring Agent** i **Kontrollpanelen**. Om du vill bekräfta att den rapporterar till Log Analytics granskar du [Verifiera agent anslutningen till Log Analytics](#verify-agent-connectivity-to-azure-monitor). 

## <a name="install-agent-using-command-line"></a>Installera agent med hjälp av kommando raden
Den hämtade filen för agenten är ett fristående installations paket.  Installations programmet för agenten och de filer som stöds finns i paketet och måste extraheras för att kunna installeras på rätt sätt med hjälp av kommando raden som visas i följande exempel.    

>[!NOTE]
>Om du vill uppgradera en agent måste du använda skript-API: et för Log Analytics. Mer information finns i avsnittet [Hantera och underhålla Log Analytics agent för Windows och Linux](agent-manage.md) .

I följande tabell visas de olika parametrarna som stöds av installations programmet för agenten, inklusive när du distribuerar med hjälp av Automation DSC.

|MMA alternativ                   |Kommentarer         |
|---------------------------------------|--------------|
| NOAPM=1                               | Valfri parameter. Installerar agenten utan prestanda övervakning av .NET-program.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Konfigurera agenten att rapportera till en arbets yta                |
|OPINSIGHTS_WORKSPACE_ID                | Arbetsyte-ID (GUID) för arbets ytan som ska läggas till                    |
|OPINSIGHTS_WORKSPACE_KEY               | Arbets ytans nyckel som används för att börja autentisera med arbets ytan |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Ange den moln miljö där arbets ytan finns <br> 0 = Azures kommersiella moln (standard) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | URI som proxyservern ska använda |
|OPINSIGHTS_PROXY_USERNAME               | Användar namn för åtkomst till en autentiserad proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Lösen ord för åtkomst till en autentiserad proxy |

1. Om du vill extrahera installationsfilerna för agenten, kan du köra från en upphöjd kommando tolk `MMASetup-<platform>.exe /c` och uppmana dig att ange sökvägen för att extrahera filer till.  Alternativt kan du ange sökvägen genom att skicka argumenten `MMASetup-<platform>.exe /c /t:<Full Path>` .  
2. Om du vill installera agenten tyst och konfigurera den för att rapportera till en arbets yta i Azures kommersiella moln, från den mapp som du extraherade installationsfilerna till skriver du: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   eller om du vill konfigurera agenten att rapportera till Azure-molnet för amerikanska myndigheter skriver du: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Sträng värden för parametrarna *OPINSIGHTS_WORKSPACE_ID* och *OPINSIGHTS_WORKSPACE_KEY* måste kapslas in i dubbla citat tecken för att instruera Windows Installer till interprit som giltiga alternativ för paketet. 

## <a name="install-agent-using-dsc-in-azure-automation"></a>Installera agenten med DSC i Azure Automation

Du kan använda följande skript exempel för att installera agenten med hjälp av Azure Automation DSC.   Om du inte har ett Automation-konto kan du läsa [komma igång med Azure Automation](../../automation/index.yml) för att förstå krav och anvisningar för att skapa ett Automation-konto som krävs innan du använder Automation DSC.  Om du inte är bekant med Automation DSC kan du läsa [komma igång med Automation DSC](../../automation/automation-dsc-getting-started.md).

I följande exempel installeras 64-bitars agenten som identifieras av `URI` värdet. Du kan också använda 32-bitars versionen genom att ersätta URI-värdet. URI: erna för båda versionerna är:

- Windows 64-bitars agent- https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bitars agent- https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Den här proceduren och skript exemplet stöder inte uppgradering av agenten som redan har distribuerats till en Windows-dator.

32-bitars-och 64-bitars versionerna av agent paketet har olika produkt koder och nya versioner som släpps har också ett unikt värde.  Produkt koden är en GUID som är huvud-ID för ett program eller en produkt och representeras av egenskapen Windows Installer **ProductCode** .  `ProductId`Värdet i **MMAgent.ps1** -skriptet måste matcha produkt koden från installations paketet 32-bitars eller 64-bitars agent.

Om du vill hämta produkt koden från agentens installations paket direkt kan du använda Orca.exe från [Windows SDK-komponenter för Windows Installer utvecklare](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers) som är en komponent i Windows Software Development Kit eller använda PowerShell efter ett [exempel skript](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)  som skrivits av en Microsoft Valuable Professional (MVP).  För båda metoderna måste du först extrahera **MOMagent.msi** -filen från installations paketet för MMASetup.  Detta visas tidigare i det första steget under avsnittet [Installera agenten med hjälp av kommando raden](#install-agent-using-command-line).  

1. Importera xPSDesiredStateConfiguration DSC-modulen från [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) till Azure Automation.  
2.    Skapa Azure Automation variabel till gångar för *OPSINSIGHTS_WS_ID* och *OPSINSIGHTS_WS_KEY*. Ange *OPSINSIGHTS_WS_ID* till ditt Log Analytics arbetsyte-ID och ange *OPSINSIGHTS_WS_KEY* till primär nyckeln för din arbets yta.
3.    Kopiera skriptet och spara det som MMAgent.ps1.

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. Uppdatera `ProductId` värdet i skriptet med produkt koden som extraherats från den senaste versionen av agent installations paketet med hjälp av de metoder som rekommenderas tidigare. 
5. [Importera konfigurations skriptet för MMAgent.ps1](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation) till ditt Automation-konto. 
6. [Tilldela en Windows-dator eller-nod](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration) till konfigurationen. Inom 15 minuter kontrollerar noden konfigurationen och agenten flyttas till noden.

## <a name="verify-agent-connectivity-to-azure-monitor"></a>Verifiera agent anslutningen till Azure Monitor

När installationen av agenten är klar kan du kontrol lera att den har anslutits och rapportering kan utföras på två sätt.  

På datorn går du till **Kontrollpanelen** och letar upp objektet **Microsoft Monitoring Agent**.  Välj det och på fliken **Azure-Log Analytics** bör agenten Visa ett meddelande om **att Microsoft Monitoring Agent har anslutit till Microsoft Operations Management Suites tjänsten.**<br><br> ![MMA-anslutningsstatus till Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Du kan också utföra en enkel logg fråga i Azure Portal.  

1. Sök efter och välj **övervaka** i Azure Portal.
1. Välj **loggar** på menyn.
1. I fönstret **loggar** i fältet frågefält skriver du:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

I Sök resultaten som returneras bör du se pulsslags poster för datorn som indikerar att den är ansluten och rapporterar till tjänsten.

## <a name="cache-information"></a>Cachelagra information

Data från Log Analytics agent cachelagras på den lokala datorn i *C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State* innan den skickas till Azure Monitor. Agenten försöker ladda upp var 20: e sekund. Om det Miss lyckas väntar det en exponentiellt ökande tid tills det lyckas. Det kommer att vänta 30 sekunder innan det andra försöket, 60 sekunder före nästa, 120 sekunder och så vidare till högst 8,5 timmar mellan försök tills den har anslutit igen. Den här vänte tiden är något slumpmässiga för att undvika att alla agenter försöker ansluta samtidigt. Äldsta data tas bort när den maximala bufferten nås.

Standardcache-storleken är 50 MB men kan konfigureras mellan minst 5 MB och högst 1,5 GB. Den lagras i register nyckeln *HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Persistence Cache Maximum*. Värdet representerar antalet sidor, med 8 KB per sida.


## <a name="next-steps"></a>Nästa steg

- Granska [hantering och underhåll av Log Analytics agent för Windows och Linux](agent-manage.md) för att lära dig hur du konfigurerar om, uppgraderar eller tar bort agenten från den virtuella datorn.

- Granska [fel sökning av Windows-agenten](agent-windows-troubleshoot.md) om det uppstår problem när du installerar eller hanterar agenten.