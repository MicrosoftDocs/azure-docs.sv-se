---
title: Aktivera konfiguration av Azure Automation tillstånd
description: Den här artikeln beskriver hur du konfigurerar datorer för hantering med Azure Automation tillstånds konfiguration.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c0dc68bd7dacf0cd7f4be9732d45831e2dbb712c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897011"
---
# <a name="enable-azure-automation-state-configuration"></a>Aktivera konfiguration av Azure Automation tillstånd

I det här avsnittet beskrivs hur du kan konfigurera dina datorer för hantering med Azure Automation tillstånds konfiguration. Mer information om den här tjänsten finns i [Översikt över Azure Automation tillstånds konfiguration](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Aktivera virtuella Azure-datorer

Med Azure Automation tillstånds konfiguration kan du enkelt aktivera virtuella Azure-datorer för konfigurations hantering med hjälp av Azure Portal, Azure Resource Manager mallar eller PowerShell. Under huven, och utan att en administratör måste fjärrans luta till en virtuell dator, registreras den virtuella datorn med Azure Automation tillstånds konfiguration under det önskade tillstånds konfigurations tillägget i Azure Eftersom Azure-tillägget körs asynkront finns steg för att följa förloppet i [kontrol lera status för VM-installationen](#check-status-of-vm-setup).

> [!NOTE]
>Om du distribuerar DSC till en Linux-nod används mappen **katalogen/tmp** . Moduler som `nxautomation` tillfälligt hämtas för verifiering innan de installeras på lämpliga platser. För att se till att modulerna installeras korrekt behöver Log Analytics agent för Linux Läs-/Skriv behörighet för **katalogen/tmp** -mappen.<br><br>
>Log Analytics agenten för Linux körs som `omsagent` användaren. Om du vill ge användaren >Skriv behörighet `omsagent` kör du kommandot `setfacl -m u:omsagent:rwx /tmp` .

### <a name="enable-a-vm-using-azure-portal"></a>Aktivera en virtuell dator med hjälp av Azure Portal

Så här aktiverar du en virtuell Azure-dator för tillstånds konfiguration via [Azure Portal](https://portal.azure.com/):

1. Navigera till det Azure Automation konto där du vill aktivera virtuella datorer. 

2. På sidan tillstånds konfiguration väljer du fliken **noder** och klickar sedan på **Lägg till**.

3. Välj en virtuell dator som ska aktive ras.

4. Klicka på **Anslut** om datorn inte har önskat PowerShell-tillägg för önskat tillstånd installerat och energi läget körs.

5. Under **registrering** anger du de [lokala PowerShell DSC-Configuration Manager värden](/powershell/scripting/dsc/managing-nodes/metaConfig) som krävs för ditt användnings fall. Alternativt kan du ange en konfiguration för noden som ska tilldelas den virtuella datorn.

![aktiverar virtuell dator](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Aktivera en virtuell dator med Azure Resource Manager mallar

Du kan installera och aktivera en virtuell dator för tillstånds konfiguration med Azure Resource Manager mallar. Se [Server som hanteras av önskad tillstånds konfigurations tjänst](https://azure.microsoft.com/resources/templates/101-automation-configuration/) för en exempel-mall som aktiverar en befintlig virtuell dator för tillstånds konfiguration. Om du hanterar en skalnings uppsättning för virtuella datorer, se exempel mal len i [konfigurationen för skalnings uppsättningar för virtuella datorer som hanteras av Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Aktivera datorer med PowerShell

Du kan använda cmdleten [register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) i PowerShell för att aktivera virtuella datorer för tillstånds konfiguration. 

> [!NOTE]
>`Register-AzAutomationDscNode`Cmdleten implementeras för närvarande endast för datorer som kör Windows, eftersom den utlöser bara Windows-tillägget.

### <a name="register-vms-across-azure-subscriptions"></a>Registrera virtuella datorer i Azure-prenumerationer

Det bästa sättet att registrera virtuella datorer från andra Azure-prenumerationer är att använda DSC-tillägget i en mall för Azure Resource Manager distribution. Exempel finns i [önskat tillstånds konfigurations tillägg med Azure Resource Manager mallar](../virtual-machines/extensions/dsc-template.md).

Om du vill hitta registrerings nyckeln och registrerings-URL: en som ska användas som parametrar i mallen, se [Aktivera datorer på ett säkert sätt med registrering](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Aktivera fysiska/virtuella Windows-datorer

Du kan aktivera Windows-servrar som körs lokalt eller i andra moln miljöer (inklusive AWS EC2-instanser) för att Azure Automation tillstånds konfiguration. Servrarna måste ha [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning).

1. Se till att den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) är installerad på datorerna för att möjliggöra tillstånds konfiguration. Dessutom måste WMF 5 vara installerat på den dator som du använder för att aktivera datorerna.
1. Följ anvisningarna i [skapa DSC-metaconfigurations](#generate-dsc-metaconfigurations) för att skapa en mapp som innehåller den nödvändiga DSC-metaconfigurations. 
1. Använd följande cmdlet för att använda PowerShell DSC-metaconfigurations via fjärr anslutning till de datorer som ska aktive ras. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Om du inte kan använda PowerShell DSC-metaconfigurations via fjärr anslutning kopierar du mappen **metaconfigurations** till de datorer som du aktiverar. Lägg sedan till kod för att anropa [set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) lokalt på datorerna.
1. Med hjälp av Azure Portal-eller cmdlets kontrollerar du att datorerna visas som noder för tillstånds konfiguration som registrerats i ditt Azure Automation-konto.

## <a name="enable-physicalvirtual-linux-machines"></a>Aktivera fysiska/virtuella Linux-datorer

Du kan aktivera Linux-servrar som körs lokalt eller i andra moln miljöer för tillstånds konfiguration. Servrarna måste ha [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning).

1. Se till att den senaste versionen av [PowerShell Desired State Configuration för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) är installerad på datorerna för att möjliggöra tillstånds konfiguration.
2. Om de [lokala POWERSHELL DSC-Configuration Manager standardvärden](/powershell/scripting/dsc/managing-nodes/metaConfig4) matchar ditt användnings fall och du vill aktivera datorer så att de båda hämtar och rapporterar till tillstånds konfiguration:

   - På varje Linux-dator som ska aktive ras använder `Register.py` du för att aktivera datorn med de lokala PowerShell-Configuration Manager standardvärden.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Du hittar registrerings nyckeln och registrerings-URL: en för ditt Automation-konto i [Aktivera datorer på ett säkert sätt med registrering](#enable-machines-securely-using-registration).

3. Om LCM-standardvärdena (PowerShell DSC Local Configuration Manager) inte matchar ditt användnings fall, eller om du vill aktivera datorer som endast rapporterar till Azure Automation tillstånds konfiguration, följer du steg 4-7. Annars fortsätter du direkt till steg 7.

4. Följ anvisningarna i avsnittet [generera DSC-metaconfigurations](#generate-dsc-metaconfigurations) för att skapa en mapp som innehåller de DSC-metaconfigurations som krävs.

5. Se till att den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) är installerad på datorn som används för att aktivera datorerna för tillstånds konfiguration.

6. Lägg till kod enligt följande om du vill använda PowerShell DSC-metaconfigurations via fjärr anslutning till de datorer som ska aktive ras.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Om du inte kan använda PowerShell DSC-metaconfigurations via fjärr anslutning kopierar du metaconfigurations som motsvarar fjärrdatorerna från den mapp som beskrivs i steg 4 till Linux-datorerna.

8. Lägg till kod för att anropa `Set-DscLocalConfigurationManager.py` lokalt på varje Linux-dator för att aktivera tillstånds konfiguration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Använd Azure Portal-eller cmdletarna för att se till att datorerna som ska aktive ras nu visas som DSC-noder registrerade i ditt Azure Automation-konto.

## <a name="generate-dsc-metaconfigurations"></a>Generera DSC-metaconfigurations

Om du vill aktivera en dator för tillstånds konfiguration kan du generera en [DSC-metaconfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig). Den här konfigurationen instruerar DSC-agenten att hämta från och/eller rapportera till Azure Automation tillstånds konfiguration. Du kan generera en DSC-metaconfiguration för konfiguration av Azure Automation tillstånd med antingen en PowerShell DSC-konfiguration eller Azure Automation PowerShell-cmdletar.

> [!NOTE]
> DSC-metaconfigurations innehåller de hemligheter som krävs för att aktivera en dator i ett Automation-konto för hantering. Se till att skydda alla DSC-metaconfigurations som du skapar eller ta bort dem efter användning.

Proxy-stöd för metaconfigurations styrs av den [lokala Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig), som är Windows PowerShell DSC-motorn. LCM körs på alla målnoden och ansvarar för att anropa konfigurations resurserna som ingår i ett DSC metaconfiguration-skript. Du kan inkludera stöd för proxy i en metaconfiguration genom att inkludera definitioner av `ProxyURL` och `ProxyCredential` egenskaper efter behov i `ConfigurationRepositoryWeb` -, `ResourceRepositoryWeb` -och- `ReportServerWeb` block. Ett exempel på URL-inställningen är `ProxyURL = "http://172.16.3.6:3128";` . `ProxyCredential`Egenskapen anges till ett `PSCredential` objekt, enligt beskrivningen i [hantera autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md). 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generera DSC-metaconfigurations med en DSC-konfiguration

1. Öppna VSCode (eller din favorit redigerare) som administratör på en dator i din lokala miljö. Datorn måste ha den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) installerad.
1. Kopiera följande skript lokalt. Det här skriptet innehåller en PowerShell DSC-konfiguration för att skapa metaconfigurations och ett kommando för att starta metaconfiguration skapas.

    > [!NOTE]
    > Konfigurations namn för State Configuration-noden är Skift läges känsliga i Azure Portal. Om skiftet inte stämmer visas inte noden på fliken **noder** .

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Fyll i registrerings nyckeln och URL: en för ditt Automation-konto, samt namnen på de datorer som ska aktive ras. Alla andra parametrar är valfria. Du hittar registrerings nyckeln och registrerings-URL: en för ditt Automation-konto i [Aktivera datorer på ett säkert sätt med registrering](#enable-machines-securely-using-registration).

1. Om du vill att datorerna ska rapportera DSC-statuskoden till Azure Automation tillstånds konfiguration, men inte hämta konfiguration eller PowerShell-moduler, anger du `ReportOnly` parametern till true.

1. Om `ReportOnly` inte har angetts rapporterar datorerna DSC-statusinformation till Azure Automation tillstånds konfiguration och hämtnings konfiguration eller PowerShell-moduler. Ange parametrar enligt dessa i `ConfigurationRepositoryWeb` -, `ResourceRepositoryWeb` -och- `ReportServerWeb` block.

1. Kör skriptet. Nu bör du ha en arbetskatalog-mapp med namnet **DscMetaConfigs**, som innehåller PowerShell DSC-metaconfigurations för de datorer som ska aktive ras (som administratör).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generera DSC-metaconfigurations med hjälp av Azure Automation-cmdletar

Om PowerShell DSC LCM-standardvärden matchar ditt användnings fall och du vill att datorer ska kunna hämta från och rapportera till Azure Automation tillstånds konfiguration kan du generera den nödvändiga DSC-metaconfigurations mer enkelt genom att använda Azure Automation-cmdlet: ar.

1. Öppna PowerShell-konsolen eller VSCode som administratör på en dator i din lokala miljö.
2. Anslut till Azure Resource Manager med [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount).
3. Ladda ned PowerShell DSC-metaconfigurations för de datorer som du vill aktivera från det Automation-konto som du ställer in noder i.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Nu bör du ha en **DscMetaConfigs** -mapp som innehåller PowerShell DSC-metaconfigurations för de datorer som ska aktive ras (som administratör).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Aktivera datorer på ett säkert sätt med registrering

Du kan aktivera datorer på ett säkert sätt för ett Azure Automation-konto via WMF 5 DSC registrerings protokollet. Med det här protokollet kan en DSC-nod autentisera till en PowerShell DSC-pull eller rapport Server, inklusive Azure Automation tillstånds konfiguration. Noden registreras på servern vid registrerings-URL: en och autentiseras med hjälp av en registrerings nyckel. Under registreringen förhandlar DSC-noden och DSC pull/Report Server ett unikt certifikat för noden som ska användas för autentisering till Server efter registreringen. Den här processen förhindrar att aktiverade noder personifierar varandra, till exempel om en nod komprometteras och fungerar skadligt. Efter registreringen används inte registrerings nyckeln för autentisering igen och tas bort från noden.

Du kan hämta den information som krävs för registrerings protokollet för tillstånds konfiguration från **nycklar** under **konto inställningar** i Azure Portal. 

![Azure Automation-nycklar och URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Registrerings-URL är URL-fältet på sidan nycklar.
- Registrerings nyckeln är värdet för fältet **primär åtkomst nyckel** eller fältet **sekundär åtkomst nyckel** på sidan nycklar. Du kan använda någon av nycklarna.

För ökad säkerhet kan du när som helst återskapa de primära och sekundära åtkomst nycklarna för ett Automation-konto när som helst på sidan nycklar. När nyckeln återskapas förhindras framtida registreringar av noder från att använda tidigare nycklar.

## <a name="re-register-a-node"></a>Registrera en nod igen

När du har registrerat en dator som en DSC-nod i Azure Automation tillstånds konfiguration finns det flera orsaker till varför du kan behöva registrera noden i framtiden igen.

- **Certifikat förnyelse.** För versioner av Windows Server före Windows Server 2019 förhandlar varje nod automatiskt ett unikt certifikat för autentisering som upphör att gälla efter ett år. Om ett certifikat upphör att gälla utan att förnyas, kan noden inte kommunicera med Azure Automation och har marker ATS `Unresponsive` . För närvarande kan PowerShell DSC-protokollet för registrering inte automatiskt förnya certifikat när de snart upphör att gälla och du måste registrera om noderna efter ett års tid. Innan du registrerar igen måste du kontrol lera att varje nod kör WMF 5 RTM. 

    Omregistreringen utförde 90 dagar eller mindre från certifikatets förfallo tid, eller när som helst efter förfallo tiden för certifikatet, resulterar i att ett nytt certifikat skapas och används. En lösning på det här problemet ingår i Windows Server 2019 och senare.

- **Ändringar av DSC LCM-värden.** Du kan behöva ändra [POWERSHELL DSC-LCM värden](/powershell/scripting/dsc/managing-nodes/metaConfig4) som anges under den inledande registreringen av noden, till exempel `ConfigurationMode` . För närvarande kan du bara ändra dessa DSC-Gent värden genom omregistrering. Det enda undantaget är det konfigurations värde för noden som tilldelats noden. Du kan ändra detta i Azure Automation DSC direkt.

Du kan registrera om en nod precis som du registrerade noden från början med någon av de metoder som beskrivs i det här dokumentet. Du behöver inte avregistrera en nod från Azure Automation tillstånds konfiguration innan du registrerar om den.

## <a name="check-status-of-vm-setup"></a>Kontrol lera status för VM-installationen

Med tillstånds konfiguration kan du enkelt aktivera virtuella Azure Windows-datorer för konfigurations hantering. Under huven används det önskade tillstånds konfigurations tillägget för Azure VM för att registrera den virtuella datorn med Azure Automation tillstånds konfiguration. Eftersom det önskade tillstånds konfigurations tillägget för Azure VM körs asynkront, kan det vara viktigt att spåra dess förlopp och felsöka körningen.

> [!NOTE]
> Alla metoder för att aktivera virtuella Azure Windows-datorer för tillstånds konfiguration som använder tillägget för önskad tillstånds konfiguration i Azure VM kan ta upp till en timme för Azure Automation för att visa virtuella datorer som registrerade. Den här fördröjningen beror på installationen av WMF 5 på den virtuella datorn med tillägget för önskad tillstånds konfiguration i Azure VM, vilket krävs för att aktivera virtuella datorer för tillstånds konfiguration.

Så här visar du status för det önskade tillägget för Azure VM-tillstånd:

1. I Azure Portal navigerar du till den virtuella dator som är aktive rad.
2. Klicka på **tillägg** under **Inställningar**. 
3. Välj **DSC** eller **DSCForLinux**, beroende på vilket operativ system du har. 
4. Du kan klicka på **Visa detaljerad status** om du vill ha mer information.

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns i [Kom igång med Azure Automation tillstånds konfiguration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera DSC-konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
- Pris information finns i pris information för [Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution finns i [Konfigurera kontinuerlig distribution med choklad](automation-dsc-cd-chocolatey.md).
- Information om fel sökning finns i [felsöka Azure Automation tillstånds konfiguration](./troubleshoot/desired-state-configuration.md).
