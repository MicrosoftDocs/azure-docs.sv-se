---
title: Kompilera DSC-konfigurationer i Azure Automation State Configuration
description: Den här artikeln beskriver hur du kompilerar Desired State Configuration konfigurationer (DSC) för Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4e0a8bc3057bc098855eb7ff65a8feeb83b7c746
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834830"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilera DSC-konfigurationer i Azure Automation State Configuration

Du kan kompilera Desired State Configuration konfigurationer (DSC) Azure Automation State Configuration på följande sätt:

- Azure State Configuration-kompileringstjänst
  - Nybörjarmetod med interaktivt användargränssnitt
   - Spåra enkelt jobbtillstånd

- Windows PowerShell
  - Anropa från Windows PowerShell lokal arbetsstation eller byggtjänst
  - Integrera med pipeline för utvecklingstest
  - Ange komplexa parametervärden
  - Arbeta med noddata och icke-noddata i stor skala
  - Betydande prestandaförbättring

Du kan också använda Azure Resource Manager med Tillägget Azure Desired State Configuration (DSC) för att skicka konfigurationer till dina virtuella Azure-datorer. Tillägget Azure DSC använder Azure VM Agent-ramverket för att leverera, genomföra och rapportera om DSC-konfigurationer som körs på virtuella Azure-datorer. Kompileringsinformation med Azure Resource Manager-mallar finns [i Desired State Configuration med Azure Resource Manager mallar](../virtual-machines/extensions/dsc-template.md#details). 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Kompilera en DSC-konfiguration i Azure State Configuration

### <a name="portal"></a>Portalen

1. I ditt Automation-konto klickar du **på Tillståndskonfiguration (DSC).**
1. Klicka på fliken **Konfigurationer** och klicka sedan på konfigurationsnamnet för att kompilera.
1. Klicka **på Kompilera**.
1. Om konfigurationen inte har några parametrar uppmanas du att bekräfta om du vill kompilera den. Om konfigurationen har parametrar öppnas bladet **Kompileringskonfiguration** så att du kan ange parametervärden.
1. Sidan Kompileringsjobb öppnas så att du kan spåra status för kompileringsjobbet. Du kan också använda den här sidan för att spåra de nodkonfigurationer (MOF-konfigurationsdokument) som finns på Azure Automation State Configuration pull-servern.

### <a name="azure-powershell"></a>Azure PowerShell

Du kan använda [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) för att börja kompilera med Windows PowerShell. Följande exempelkod börjar kompileringen av en DSC-konfiguration med namnet **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` returnerar ett kompileringsjobbobjekt som du kan använda för att spåra jobbstatus. Du kan sedan använda det här kompileringsjobbobjektet med [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) för att fastställa status för kompileringsjobbet och [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) för att visa dess strömmar (utdata). Följande exempel startar kompileringen av SampleConfig-konfigurationen, väntar tills den har slutförts och visar sedan dess strömmar.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Deklarera grundläggande parametrar

Parameterdeklarationen i DSC-konfigurationer, inklusive parametertyper och egenskaper, fungerar på samma sätt som i Azure Automation runbooks. Mer [information om runbookparametrar finns i Starta en runbook](./start-runbooks.md) i Azure Automation för mer information om runbook-parametrar.

I följande exempel används parametrarna och för att fastställa värdena för egenskaperna i nodkonfigurationen `FeatureName` `IsPresent` **ParametersExample.sample** som genererades under kompileringen.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Du kan kompilera DSC-konfigurationer som använder grundläggande parametrar i Azure Automation State Configuration portalen eller med Azure PowerShell.

#### <a name="portal"></a>Portalen

I portalen kan du ange parametervärden när du har klickat på **Kompilera**.

![Konfigurationskompileringsparametrar](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell kräver parametrar i [en hash-tabell](/powershell/module/microsoft.powershell.core/about/about_hash_tables), där nyckeln matchar parameternamnet och värdet är lika med parametervärdet.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Information om hur du `PSCredential` kan skicka objekt som parametrar finns i [Autentiseringstillgångar](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompilera konfigurationer som innehåller sammansatta resurser i Azure Automation

Med **funktionen Sammansatta** resurser kan du använda DSC-konfigurationer som kapslade resurser i en konfiguration. Den här funktionen gör det möjligt att tillämpa flera konfigurationer på en enda resurs. Mer [information om sammansatta resurser finns i Sammansatta resurser:](/powershell/scripting/dsc/resources/authoringresourcecomposite) Använda en DSC-konfiguration som en resurs.

> [!NOTE]
> Så att konfigurationer som innehåller sammansatta resurser kompileras korrekt måste du först importera till Azure Automation DSC-resurser som de sammansatta är beroende av. Att lägga till en sammansatt DSC-resurs skiljer sig inte från att lägga till en PowerShell-modul Azure Automation. Den här processen dokumenteras [i Hantera moduler i Azure Automation](./shared-resources/modules.md).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Hantera ConfigurationData vid kompilering av konfigurationer i Azure Automation

`ConfigurationData` är en inbyggd DSC-parameter som gör att du kan separera strukturell konfiguration från alla miljöspecifika konfigurationer när du använder PowerShell DSC. Mer information finns i [Avgränsa "Vad" från "Var" i PowerShell DSC.](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)

> [!NOTE]
> När du kompilerar Azure Automation State Configuration kan du använda `ConfigurationData` i Azure PowerShell men inte i Azure Portal.

I följande exempel använder DSC-konfigurationen `ConfigurationData` via `$ConfigurationData` `$AllNodes` nyckelorden och . Du behöver även [modulen xWebAdministration för](https://www.powershellgallery.com/packages/xWebAdministration/) det här exemplet.

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Du kan kompilera föregående DSC-konfiguration med Windows PowerShell. Följande skript lägger till två nodkonfigurationer i Azure Automation State Configuration pull-tjänsten: **ConfigurationDataSample.MyVM1** och **ConfigurationDataSample.MyVM3**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Arbeta med tillgångar i Azure Automation under kompileringen

Tillgångsreferenser är desamma i både Azure Automation State Configuration och runbooks. Mer information finns i följande:

- [Certifikat](./shared-resources/certificates.md)
- [Anslutningar](automation-connections.md)
- [Autentiseringsuppgifter](./shared-resources/credentials.md)
- [Variabler](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Autentiseringstillgångar

DSC-konfigurationer i Azure Automation kan referera till Automation-autentiseringstillgångar med `Get-AutomationPSCredential` hjälp av cmdleten . Om en konfiguration har en parameter som anger ett -objekt använder du genom att skicka strängnamnet för en Azure Automation-autentiseringstillgång till cmdleten för att hämta `PSCredential` `Get-AutomationPSCredential` autentiseringssuppgifter. Använd sedan det objektet för parametern som kräver `PSCredential` objektet. I bakgrunden hämtas Azure Automation autentiseringsuppgifter med det namnet och skickas till konfigurationen. Exemplet nedan visar det här scenariot i praktiken.

För att skydda autentiseringsuppgifter i nodkonfigurationer (MOF-konfigurationsdokument) måste autentiseringsuppgifterna i nodkonfigurationens MOF-fil krypteras. För närvarande måste du ge PowerShell DSC behörighet att mata ut autentiseringsuppgifter i oformaterad text under nodkonfigurationens MOF-generering. PowerShell DSC är inte medveten om Azure Automation krypterar hela MOF-filen efter dess generering via ett kompileringsjobb.

Du kan meddela PowerShell DSC att det är okej att autentiseringsuppgifter matas ut i oformaterad text i den genererade nodkonfigurationens MFS med konfigurationsdata. Du bör skicka `PSDscAllowPlainTextPassword = $true` via för `ConfigurationData` varje nodblocknamn som visas i DSC-konfigurationen och använder autentiseringsuppgifter.

I följande exempel visas en DSC-konfiguration som använder en Automation-autentiseringstillgång.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Du kan kompilera den föregående DSC-konfigurationen med PowerShell. Följande PowerShell-kod lägger till två nodkonfigurationer till Azure Automation State Configuration pull-servern: **CredentialSample.MyVM1** och **CredentialSample.MyVM2.**

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> När kompileringen är klar kan du få felmeddelandet `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` Du kan ignorera det här meddelandet.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Kompilera DSC-konfigurationen i Windows PowerShell

Processen för att kompilera DSC-konfigurationer i Windows PowerShell ingår i PowerShell DSC-dokumentationen [Skriva, kompilera och tillämpa en konfiguration.](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)
Du kan köra den här processen från en arbetsstation för utvecklare eller i en byggtjänst, till exempel [Azure DevOps.](https://dev.azure.com) Du kan sedan importera MOF-filerna som skapas genom att kompilera konfigurationen till Azure State Configuration tjänsten.

Kompilera i Windows PowerShell också alternativet att signera konfigurationsinnehåll. DSC-agenten verifierar en signerad nodkonfiguration lokalt på en hanterad nod. Verifieringen säkerställer att konfigurationen som tillämpas på noden kommer från en auktoriserad källa.

Du kan också importera nodkonfigurationer (MOF-filer) som har kompilerats utanför Azure. Importen innehåller kompilering från en arbetsstation för utvecklare eller i en tjänst som [Azure DevOps.](https://dev.azure.com) Den här metoden har flera fördelar, inklusive prestanda och tillförlitlighet.

> [!NOTE]
> En nodkonfigurationsfil får inte vara större än 1 MB för att Azure Automation importera den.

Mer information om signering av nodkonfigurationer finns i [Förbättringar i WMF 5.1 –](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)Så här signerar du konfiguration och modul .

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importera en nodkonfiguration i Azure Portal

1. I ditt Automation-konto klickar du **på Tillståndskonfiguration (DSC)** under **Konfigurationshantering.**
1. På sidan State Configuration (DSC) klickar du på fliken **Konfigurationer** och sedan på Lägg **till**.
1. På sidan Importera klickar du på  mappikonen bredvid fältet Nodkonfigurationsfil för att bläddra efter en MOF-fil för nodkonfiguration på den lokala datorn.

   ![Bläddra efter lokal fil](./media/automation-dsc-compile/import-browse.png)

1. Ange ett namn i **fältet Konfigurationsnamn.** Det här namnet måste matcha namnet på konfigurationen som nodkonfigurationen kompilerades från.
1. Klicka på **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importera en nodkonfiguration med Azure PowerShell

Du kan använda cmdleten [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) för att importera en nodkonfiguration till ditt Automation-konto.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att gå [till Kom igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns i [Kompilera DSC-konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation).
- Prisinformation finns i [Azure Automation State Configuration priser.](https://azure.microsoft.com/pricing/details/automation/)
- Ett exempel på hur du använder State Configuration i en pipeline för kontinuerlig distribution finns i [Konfigurera kontinuerlig distribution med Chocolatey](automation-dsc-cd-chocolatey.md).
