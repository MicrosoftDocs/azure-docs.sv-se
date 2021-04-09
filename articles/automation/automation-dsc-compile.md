---
title: Kompilera DSC-konfigurationer i Azure Automation tillstånds konfiguration
description: Den här artikeln beskriver hur du kompilerar Desired State Configuration (DSC)-konfigurationer för Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: b45aad71e04418c7c7dda4fc3f0c84a5fe99ecdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896350"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilera DSC-konfigurationer i Azure Automation tillstånds konfiguration

Du kan kompilera DSC-konfigurationer (Desired State Configuration) i Azure Automation tillstånds konfiguration på följande sätt:

- Tjänsten Azure State Configuration Compilation
  - Nybörjar metod med interaktivt användar gränssnitt
   - Spåra jobb status enkelt

- Windows PowerShell
  - Anropa från Windows PowerShell på lokal arbets Station eller build-tjänst
  - Integrera med pipeline för utvecklings test
  - Ange komplexa parameter värden
  - Arbeta med nod-och icke-nods data i skala
  - Betydande prestanda förbättringar

Du kan också använda Azure Resource Manager mallar med Azures Desired State Configuration (DSC)-tillägg för att skicka konfigurationer till dina virtuella Azure-datorer. Azure DSC-tillägget använder Azure VM agent Framework för att leverera, införa och rapportera om DSC-konfigurationer som körs på virtuella Azure-datorer. Information om kompilering med Azure Resource Manager-mallar finns i [Desired State Configuration Extension med Azure Resource Manager mallar](../virtual-machines/extensions/dsc-template.md#details). 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Kompilera en DSC-konfiguration i Azure State Configuration

### <a name="portal"></a>Portal

1. I ditt Automation-konto klickar du på **tillstånds konfiguration (DSC)**.
1. Klicka på fliken **konfigurationer** och klicka sedan på konfigurations namnet som ska kompileras.
1. Klicka på **kompilera**.
1. Om konfigurationen inte har några parametrar uppmanas du att bekräfta om du vill kompilera den. Om konfigurationen har parametrar öppnas bladet **kompilera konfiguration** så att du kan ange parameter värden.
1. Sidan för att kompilera jobb öppnas så att du kan spåra jobb status för kompilering. Du kan också använda den här sidan för att spåra nodkonfigurationer (MOF-konfigurationsfilen) som placeras på hämtnings servern för Azure Automation tillstånds konfiguration.

### <a name="azure-powershell"></a>Azure PowerShell

Du kan använda [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) för att börja kompilera med Windows PowerShell. Följande exempel kod börjar kompilera en DSC-konfiguration med namnet **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` Returnerar ett objekt för kompilering av jobb som du kan använda för att spåra jobb status. Du kan sedan använda det här konfigurationsobjektet för kompilering med [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) för att fastställa status för compile-jobbet och [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) för att visa dess strömmar (utdata). Följande exempel startar kompilering av SampleConfig-konfigurationen, väntar tills den har slutförts och visar sedan dess strömmar.

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

Parameter deklaration i DSC-konfigurationer, inklusive parameter typer och egenskaper, fungerar på samma sätt som i Azure Automation runbooks. Mer information om Runbook-parametrar finns i [starta en Runbook i Azure Automation](./start-runbooks.md) .

I följande exempel används `FeatureName` och `IsPresent` Parametrar för att fastställa värdena för egenskaperna i **ParametersExample. exempel** på Node-konfiguration, som genereras under kompilering.

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

Du kan kompilera DSC-konfigurationer som använder grundläggande parametrar i konfigurations portalen för Azure Automation tillstånd eller med Azure PowerShell.

#### <a name="portal"></a>Portal

I portalen kan du ange parameter värden efter att du klickat på **kompilera**.

![Parametrar för att kompilera konfiguration](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell kräver parametrar i en [hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables)-tabellen, där nyckeln matchar parameter namnet och värdet är lika med parametervärdet.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Information om hur `PSCredential` du skickar objekt som parametrar finns i [Credential assets](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompilera konfigurationer som innehåller sammansatta resurser i Azure Automation

Med funktionen **sammansatta resurser** kan du använda DSC-konfigurationer som kapslade resurser i en konfiguration. Den här funktionen gör det möjligt att tillämpa flera konfigurationer på en enda resurs. Se [sammansatta resurser: använda en DSC-konfiguration som en resurs](/powershell/scripting/dsc/resources/authoringresourcecomposite) för att lära dig mer om sammansatta resurser.

> [!NOTE]
> För att konfigurationer som innehåller sammansatta resurser ska kompileras korrekt måste du först importera till Azure Automation alla DSC-resurser som de sammansatta förlitar sig på. Att lägga till en DSC-sammansatt resurs skiljer sig inte från att lägga till någon PowerShell-modul till Azure Automation. Den här processen dokumenteras i [Hantera moduler i Azure Automation](./shared-resources/modules.md).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Hantera ConfigurationData när du kompilerar konfigurationer i Azure Automation

`ConfigurationData` är en inbyggd DSC-parameter som gör att du kan separera strukturell konfiguration från valfri miljö bestämd konfiguration när du använder PowerShell DSC. Mer information finns i [avgränsa "vad" från "där" i POWERSHELL DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> När du kompilerar i Azure Automation tillstånds konfiguration kan du använda `ConfigurationData` i Azure PowerShell men inte i Azure Portal.

Följande exempel på DSC-konfiguration använder `ConfigurationData` via- `$ConfigurationData` och- `$AllNodes` nyckelorden. Du behöver också [xWebAdministration-modulen](https://www.powershellgallery.com/packages/xWebAdministration/) för det här exemplet.

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

Du kan kompilera föregående DSC-konfiguration med Windows PowerShell. Följande skript lägger till två nodkonfigurationer till pull-tjänsten för Azure Automation tillstånds konfiguration: **ConfigurationDataSample. MyVM1** och **ConfigurationDataSample. MyVM3**.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Arbeta med till gångar i Azure Automation under kompileringen

Till gångs referenser är desamma i både Azure Automation tillstånds konfiguration och Runbooks. Mer information finns i följande:

- [Certifikat](./shared-resources/certificates.md)
- [Anslutningar](automation-connections.md)
- [Autentiseringsuppgifter](./shared-resources/credentials.md)
- [Variabler](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Inloggnings till gångar

DSC-konfigurationer i Azure Automation kan referera till till gångar för automatisering av autentiseringsuppgifter med hjälp av `Get-AutomationPSCredential` cmdleten. Om en konfiguration har en parameter som anger ett `PSCredential` objekt, använder du `Get-AutomationPSCredential` genom att skicka sträng namnet för en Azure Automation Credential-till-cmdlet: en för att hämta autentiseringsuppgifterna. Använd sedan objektet för den parameter som kräver `PSCredential` objektet. I bakgrunden hämtas den Azure Automation autentiseringsuppgiften med det namnet och skickas till konfigurationen. Exemplet nedan visar det här scenariot i praktiken.

Att bevara autentiseringsuppgifterna säkra i nodkonfigurationer (MOF-konfigurationsfilen) kräver kryptering av autentiseringsuppgifterna i MOF-filen för nodens konfiguration. För närvarande måste du ge PowerShell DSC behörighet att skriva autentiseringsuppgifter i klartext under MOF-genereringen av nodens konfiguration. PowerShell DSC är inte medveten om att Azure Automation krypterar hela MOF-filen efter att den har genererats via ett kompilerings jobb.

Du kan tala om för PowerShell DSC att det går att ange autentiseringsuppgifter som oformaterad text i den genererade nodens konfigurations MOF: ar med hjälp av konfigurations data. Du bör skicka `PSDscAllowPlainTextPassword = $true` via `ConfigurationData` för varje nodnamn som visas i DSC-konfigurationen och som använder autentiseringsuppgifter.

I följande exempel visas en DSC-konfiguration som använder en till gång för Automation-autentiseringsuppgifter.

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

Du kan kompilera föregående DSC-konfiguration med PowerShell. Följande PowerShell-kod lägger till två nodkonfigurationer till hämtnings servern för Azure Automation tillstånds konfiguration: **CredentialSample. MyVM1** och **CredentialSample. MyVM2**.

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
> När kompileringen är klar kan du få fel meddelandet `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` du kan ignorera det här meddelandet på ett säkert sätt.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Kompilera din DSC-konfiguration i Windows PowerShell

Processen för att kompilera DSC-konfigurationer i Windows PowerShell ingår i PowerShell DSC-dokumentationen [skriva, kompilera och tillämpa en konfiguration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Du kan köra den här processen från en arbets station för utvecklare eller i en build-tjänst, till exempel [Azure DevOps](https://dev.azure.com). Sedan kan du importera MOF-filerna som skapas genom att kompilera konfigurationen till Azure State Configuration-tjänsten.

Kompilering i Windows PowerShell ger också möjlighet att signera konfigurations innehåll. DSC-agenten verifierar en signerad nods konfiguration lokalt på en hanterad nod. Verifiering säkerställer att konfigurationen som används på noden kommer från en auktoriserad källa.

Du kan också importera nodkonfigurationer (MOF-filer) som har kompilerats utanför Azure. Importen inkluderar kompilering från en arbets station för utvecklare eller i en tjänst som [Azure DevOps](https://dev.azure.com). Den här metoden har flera fördelar, inklusive prestanda och tillförlitlighet.

> [!NOTE]
> En konfigurations fil för noden får inte vara större än 1 MB för att Azure Automation ska kunna importeras.

Mer information om hur du signerar nodkonfigurationer finns [i förbättringar i WMF 5,1 – så här signerar du konfiguration och modul](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importera en Node-konfiguration i Azure Portal

1. I ditt Automation-konto klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan tillstånds konfiguration (DSC) klickar du på fliken **konfigurationer** och sedan på **Lägg till**.
1. På sidan Importera klickar du på mappikonen bredvid fältet **konfigurations fil för noden** för att bläddra efter en MOF-fil för nodens konfiguration på den lokala datorn.

   ![Bläddra efter lokal fil](./media/automation-dsc-compile/import-browse.png)

1. Ange ett namn i fältet **konfigurations namn** . Det här namnet måste matcha namnet på den konfiguration från vilken nodkonfigurationer kompilerades.
1. Klicka på **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importera en nods konfiguration med Azure PowerShell

Du kan använda cmdleten [import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) för att importera en konfiguration av en nod till ditt Automation-konto.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns i [Kom igång med Azure Automation tillstånds konfiguration](automation-dsc-getting-started.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera DSC-konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md).
- En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
- Pris information finns i pris information för [Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Ett exempel på att använda tillstånds konfiguration i en pipeline för kontinuerlig distribution finns i [Konfigurera kontinuerlig distribution med choklad](automation-dsc-cd-chocolatey.md).
