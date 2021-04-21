---
title: Aktivera Azure Automation State Configuration
description: Den här artikeln beskriver hur du ställer in datorer för hantering med Azure Automation State Configuration.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: d338c5f34d49663345582198ff53ba50a2919d7e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829430"
---
# <a name="enable-azure-automation-state-configuration"></a>Aktivera Azure Automation State Configuration

Det här avsnittet beskriver hur du kan konfigurera dina datorer för hantering med Azure Automation State Configuration. Mer information om den här tjänsten finns [Azure Automation State Configuration översikt](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Aktivera virtuella Azure-datorer

Azure Automation State Configuration kan du enkelt aktivera virtuella Azure-datorer för konfigurationshantering med hjälp av Azure Portal, Azure Resource Manager mallar eller PowerShell. Under huven och utan att en administratör behöver fjärransluta till en virtuell dator registrerar Azure VM Desired State Configuration-tillägget den virtuella datorn med Azure Automation State Configuration. Eftersom Azure-tillägget körs asynkront finns steg för att spåra förloppet i [Kontrollera status för VM-installationen](#check-status-of-vm-setup).

> [!NOTE]
>När du distribuerar DSC till en Linux-nod används **mappen /tmp.** Moduler som `nxautomation` laddas ned tillfälligt för verifiering innan de installeras på lämpliga platser. För att säkerställa att modulerna installeras korrekt behöver Log Analytics-agenten för Linux läs-/skrivbehörighet för **mappen /tmp.**<br><br>
>Log Analytics-agenten för Linux körs som `omsagent` användaren. Om du >ge användaren `omsagent` skrivbehörighet kör du kommandot `setfacl -m u:omsagent:rwx /tmp` .

### <a name="enable-a-vm-using-azure-portal"></a>Aktivera en virtuell dator med Azure Portal

Så här aktiverar du en virtuell Azure-State Configuration genom [Azure Portal:](https://portal.azure.com/)

1. Gå till det Azure Automation konto där du vill aktivera virtuella datorer. 

2. På State Configuration väljer du fliken Noder **och klickar** sedan på Lägg **till**.

3. Välj en virtuell dator att aktivera.

4. Om det önskade tillståndstillägget för PowerShell inte är installerat på datorn och energispartillståndet körs klickar du på **Anslut.**

5. Under **Registrering** anger du de [lokala PowerShell DSC Konfigurationshanteraren värden som](/powershell/scripting/dsc/managing-nodes/metaConfig) krävs för ditt användningsfall. Du kan också ange en nodkonfiguration som ska tilldelas till den virtuella datorn.

![aktivera virtuell dator](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Aktivera en virtuell dator med hjälp Azure Resource Manager mallar

Du kan installera och aktivera en virtuell dator för State Configuration med hjälp Azure Resource Manager mallar. Se [Server som hanteras av Desired State Configuration-tjänsten](https://azure.microsoft.com/resources/templates/101-automation-configuration/) för en exempelmall som aktiverar en befintlig virtuell dator för State Configuration. Om du hanterar en VM-skalningsuppsättning kan du se exempelmallen i Konfiguration av [VM-skalningsuppsättning som hanteras av Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Aktivera datorer med PowerShell

Du kan använda cmdleten [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) i PowerShell för att aktivera virtuella datorer för State Configuration. 

> [!NOTE]
>`Register-AzAutomationDscNode`Cmdleten implementeras för närvarande endast för datorer som kör Windows, eftersom den bara utlöser Windows-tillägget.

### <a name="register-vms-across-azure-subscriptions"></a>Registrera virtuella datorer i Azure-prenumerationer

Det bästa sättet att registrera virtuella datorer från andra Azure-prenumerationer är att använda DSC-tillägget i en Azure Resource Manager distributionsmall. Exempel finns i Desired State Configuration [med Azure Resource Manager mallar](../virtual-machines/extensions/dsc-template.md).

Information om hur du hittar registreringsnyckeln och registrerings-URL:en som ska användas som parametrar i mallen finns i [Aktivera datorer på ett säkert sätt med hjälp av registrering](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Aktivera fysiska/virtuella Windows-datorer

Du kan aktivera Windows-servrar som körs lokalt eller i andra molnmiljöer (inklusive AWS EC2-instanser) för att Azure Automation State Configuration. Servrarna måste ha [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning).

1. Kontrollera att den senaste versionen av [WMF 5 är](https://aka.ms/wmf5latest) installerad på datorerna för att aktivera för State Configuration. DESSUTOM måste WMF 5 vara installerat på den dator som du använder för att aktivera datorerna.
1. Följ anvisningarna i [Generate DSC metaconfigurations (Generera DSC-metakonfigurationer)](#generate-dsc-metaconfigurations) för att skapa en mapp som innehåller nödvändiga DSC-metakonfigurationer. 
1. Använd följande cmdlet för att fjärrstyra PowerShell DSC-metakonfigurationerna på de datorer som ska aktiveras. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Om du inte kan använda PowerShell DSC-metakonfigurationerna via fjärrkonfiguration kopierar du mappen **metakonfigurationer** till de datorer som du aktiverar. Lägg sedan till kod för [att anropa Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) lokalt på datorerna.
1. Med hjälp Azure Portal eller cmdlets kontrollerar du att datorerna visas som State Configuration noder som registrerats i ditt Azure Automation konto.

## <a name="enable-physicalvirtual-linux-machines"></a>Aktivera fysiska/virtuella Linux-datorer

Du kan aktivera Linux-servrar som körs lokalt eller i andra molnmiljöer för att State Configuration. Servrarna måste ha [utgående åtkomst till Azure](automation-dsc-overview.md#network-planning).

1. Kontrollera att den senaste versionen av [PowerShell Desired State Configuration för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) är installerad på datorerna för att aktivera för State Configuration.
2. Om [standardinställningarna för PowerShell DSC Local Konfigurationshanteraren ditt användningsfall](/powershell/scripting/dsc/managing-nodes/metaConfig4) och du vill aktivera datorer så att de både hämtar från och rapporterar till State Configuration:

   - På varje Linux-dator som ska aktiveras använder du för att aktivera datorn `Register.py` med PowerShell DSC Local Konfigurationshanteraren standardinställningarna.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Information om hur du hittar registreringsnyckeln och registrerings-URL:en för ditt Automation-konto finns i [Aktivera datorer på ett säkert sätt med hjälp av registrering.](#enable-machines-securely-using-registration)

3. Om standardinställningarna för PowerShell DSC Local Konfigurationshanteraren (LCM) inte matchar ditt användningsfall, eller om du vill aktivera datorer som endast rapporterar till Azure Automation State Configuration, följer du steg 4–7. Annars fortsätter du direkt till steg 7.

4. Följ anvisningarna i [avsnittet Generera DSC-metakonfigurationer](#generate-dsc-metaconfigurations) för att skapa en mapp som innehåller nödvändiga DSC-metakonfigurationer.

5. Kontrollera att den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) är installerad på den dator som används för att aktivera dina datorer för State Configuration.

6. Lägg till kod på följande sätt för att fjärrstyra PowerShell DSC-metakonfigurationerna på de datorer som ska aktiveras.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Om du inte kan använda PowerShell DSC-metakonfigurationerna via fjärrkonfiguration kopierar du de metakonfigurationer som motsvarar fjärrdatorerna från mappen som beskrivs i steg 4 till Linux-datorerna.

8. Lägg till kod för att `Set-DscLocalConfigurationManager.py` anropa lokalt på varje Linux-dator för att aktivera för State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Med hjälp Azure Portal eller cmdlets ser du till att datorerna som ska aktiveras nu visas som DSC-noder som är registrerade i ditt Azure Automation konto.

## <a name="generate-dsc-metaconfigurations"></a>Generera DSC-metakonfigurationer

Om du vill aktivera valfri dator för State Configuration kan du generera en [DSC-metakonfiguration](/powershell/scripting/dsc/managing-nodes/metaConfig). Den här konfigurationen talar om för DSC-agenten att den ska hämta från och/eller rapportera till Azure Automation State Configuration. Du kan generera en DSC-metakonfiguration för Azure Automation State Configuration med antingen en PowerShell DSC-konfiguration eller Azure Automation PowerShell-cmdlets.

> [!NOTE]
> DSC-metakonfigurationer innehåller de hemligheter som behövs för att aktivera en dator i ett Automation-konto för hantering. Se till att skydda alla DSC-metakonfigurationer som du skapar eller ta bort dem efter användning.

Proxystöd för metakonfigurationer styrs av [Local Konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaconfig), som är den Windows PowerShell DSC-motorn. LCM körs på alla målnoder och ansvarar för att anropa konfigurationsresurserna som ingår i ett DSC-metakonfigurationsskript. Du kan inkludera proxystöd i en metakonfiguration genom att inkludera definitioner av `ProxyURL` egenskaperna och efter behov i `ProxyCredential` `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` blocken , och `ReportServerWeb` . Ett exempel på URL-inställningen är `ProxyURL = "http://172.16.3.6:3128";` . Egenskapen `ProxyCredential` anges till ett -objekt, enligt `PSCredential` beskrivningen i Hantera [autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md). 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generera DSC-metakonfigurationer med hjälp av en DSC-konfiguration

1. Öppna VSCode (eller din favoritredigerare) som administratör på en dator i din lokala miljö. Datorn måste ha den senaste versionen av [WMF 5](https://aka.ms/wmf5latest) installerad.
1. Kopiera följande skript lokalt. Det här skriptet innehåller en PowerShell DSC-konfiguration för att skapa metakonfigurationer och ett kommando för att starta skapandet av metakonfigurationen.

    > [!NOTE]
    > State Configuration node-konfigurationsnamn är fallkänsliga i Azure Portal. Om ärendet är felmatchat visas inte noden under **fliken** Noder.

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

1. Fyll i registreringsnyckeln och URL:en för ditt Automation-konto, samt namnen på de datorer som ska aktiveras. Alla andra parametrar är valfria. Information om hur du hittar registreringsnyckeln och registrerings-URL:en för ditt Automation-konto finns i [Aktivera datorer på ett säkert sätt med hjälp av registrering.](#enable-machines-securely-using-registration)

1. Om du vill att datorerna ska rapportera DSC-statusinformation till Azure Automation State Configuration, men inte hämta konfiguration eller PowerShell-moduler, anger du `ReportOnly` parametern till true.

1. Om `ReportOnly` inte har angetts rapporterar datorerna DSC-statusinformation för att Azure Automation State Configuration hämta konfiguration eller PowerShell-moduler. Ange parametrarna i `ConfigurationRepositoryWeb` blocken `ResourceRepositoryWeb` , och `ReportServerWeb` .

1. Kör skriptet. Nu bör du ha en arbetskatalogmapp med namnet **DscMetaConfigs** som innehåller PowerShell DSC-metakonfigurationer som datorerna kan aktivera (som administratör).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generera DSC-metakonfigurationer med hjälp Azure Automation cmdlets

Om PowerShell DSC LCM-standardvärdena matchar ditt användningsfall och du vill göra det möjligt för datorer att både hämta från och rapportera till Azure Automation State Configuration, kan du generera nödvändiga DSC-metakonfigurationer med hjälp av Azure Automation-cmdlets.

1. Öppna PowerShell-konsolen eller VSCode som administratör på en dator i din lokala miljö.
2. Anslut till Azure Resource Manager med [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount).
3. Ladda ned PowerShell DSC-metakonfigurationerna för de datorer som du vill aktivera från Automation-kontot där du konfigurerar noder.

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

1. Nu bör du ha en **DscMetaConfigs-mapp** som innehåller PowerShell DSC-metakonfigurationer som datorerna ska aktivera (som administratör).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Aktivera datorer på ett säkert sätt med hjälp av registrering

Du kan aktivera datorer säkert för ett Azure Automation-konto via WMF 5 DSC-registreringsprotokollet. Med det här protokollet kan en DSC-nod autentisera till en PowerShell DSC-pull- eller rapportserver, inklusive Azure Automation State Configuration. Noden registreras hos servern på registrerings-URL:en och autentiseras med hjälp av en registreringsnyckel. Under registreringen förhandlar DSC-noden och DSC-pull/rapportservern ett unikt certifikat för noden som ska användas för autentisering till servern efter registreringen. Den här processen förhindrar att aktiverade noder personifierar varandra, till exempel om en nod komprometteras och beter sig illa. Efter registreringen används inte registreringsnyckeln för autentisering igen och tas bort från noden.

Du kan hämta den information som krävs för State Configuration registreringsprotokoll **från Nycklar** under **Kontoinställningar** i Azure Portal. 

![Azure Automation-nycklar och URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Registrerings-URL är URL-fältet på sidan Nycklar.
- Registreringsnyckel är värdet för fältet **Primär åtkomstnyckel** eller **fältet Sekundär åtkomstnyckel** på sidan Nycklar. Båda nycklarna kan användas.

För ökad säkerhet kan du återskapa de primära och sekundära åtkomstnycklarna för ett Automation-konto när som helst på sidan Nycklar. Återskapande av nycklar förhindrar framtida nodregistreringar från att använda tidigare nycklar.

## <a name="re-register-a-node"></a>Registrera om en nod

När du har registrerat en dator som en DSC-nod i Azure Automation State Configuration finns det flera orsaker till varför du kan behöva registrera om noden i framtiden.

- **Certifikatförnyelse.** För versioner av Windows Server före Windows Server 2019 förhandlar varje nod automatiskt ett unikt certifikat för autentisering som upphör att gälla efter ett år. Om ett certifikat upphör att gälla utan förnyelse kan noden inte kommunicera med Azure Automation och markeras `Unresponsive` . För närvarande kan registreringsprotokollet PowerShell DSC inte förnya certifikat automatiskt när de snart upphör att gälla, och du måste registrera noderna igen efter ett år. Kontrollera att varje nod kör WMF 5 RTM innan du registrerar om. 

    Omregistreringen utfördes 90 dagar eller mindre från utgångstiden för certifikatet, eller när som helst efter certifikatets giltighetstid, vilket resulterar i att ett nytt certifikat genereras och används. En lösning på det här problemet ingår i Windows Server 2019 och senare.

- **Ändringar av DSC LCM-värden.** Du kan behöva ändra [de PowerShell DSC LCM-värden som](/powershell/scripting/dsc/managing-nodes/metaConfig4) angetts under den första registreringen av noden, till exempel `ConfigurationMode` . För närvarande kan du bara ändra dessa DSC-agentvärden genom omregistrering. Det enda undantaget är nodkonfigurationsvärdet som tilldelats noden. Du kan ändra detta i Azure Automation DSC direkt.

Du kan registrera om en nod precis som du registrerade noden från början, med någon av de metoder som beskrivs i det här dokumentet. Du behöver inte avregistrera en nod från Azure Automation State Configuration innan du registrerar den igen.

## <a name="check-status-of-vm-setup"></a>Kontrollera status för vm-konfiguration

State Configuration kan du enkelt aktivera virtuella Azure Windows-datorer för konfigurationshantering. Under huven används Tillägget Azure VM Desired State Configuration för att registrera den virtuella datorn med Azure Automation State Configuration. Eftersom Azure VM Desired State Configuration tillägget körs asynkront kan det vara viktigt att spåra förloppet och felsöka körningen.

> [!NOTE]
> Alla metoder för att aktivera virtuella Azure Windows-datorer för State Configuration som använder Azure VM Desired State Configuration-tillägget kan ta upp till en timme för Azure Automation att visa virtuella datorer som registrerade. Den här fördröjningen beror på installationen av WMF 5 på den virtuella datorn av Azure VM Desired State Configuration-tillägget, vilket krävs för att aktivera virtuella datorer för State Configuration.

Så här visar du status för den virtuella Azure Desired State Configuration datorn:

1. I Azure Portal navigerar du till den virtuella dator som aktiveras.
2. Klicka **på Tillägg** under **Inställningar.** 
3. Välj **DSC** eller **DSCForLinux** beroende på ditt operativsystem. 
4. Om du vill ha mer information klickar du **på Visa detaljerad status.**

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att gå [till Kom igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns i [Kompilera DSC-konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation).
- Prisinformation finns i [Azure Automation State Configuration priser.](https://azure.microsoft.com/pricing/details/automation/)
- Ett exempel på hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Konfigurera kontinuerlig distribution med Chocolatey](automation-dsc-cd-chocolatey.md).
- Felsökningsinformation finns i [Felsöka Azure Automation State Configuration](./troubleshoot/desired-state-configuration.md).
