---
title: Konfigurera datorer till önskat tillstånd i Azure Automation
description: Den här artikeln beskriver hur du konfigurerar datorer till önskat tillstånd med hjälp av Azure Automation State Configuration.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 70631490e248c801b9465c51548efe8dff704a3e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830150"
---
# <a name="configure-machines-to-a-desired-state"></a>Konfigurera datorer till ett önskat tillstånd

Azure Automation State Configuration kan du ange konfigurationer för dina servrar och se till att servrarna är i det angivna tillståndet över tid.

> [!div class="checklist"]
> - Registrera en virtuell dator som ska hanteras av Azure Automation DSC
> - Ladda upp en konfiguration till Azure Automation
> - Kompilera en konfiguration till en nodkonfiguration
> - Tilldela en nodkonfiguration till en hanterad nod
> - Kontrollera kompatibilitetsstatusen för en hanterad nod

I den här självstudien använder vi en enkel [DSC-konfiguration](/powershell/scripting/dsc/configurations/configurations) som säkerställer att IIS är installerat på den virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure Automation-konto. Mer information om ett Automation-konto och dess krav finns i [Autentiseringsöversikt för Automation-konto.](./automation-security-overview.md)
- En Azure Resource Manager virtuell dator (inte klassisk) som kör Windows Server 2008 R2 eller senare. Anvisningar om hur du skapar en virtuell dator [finns i Skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/windows/quick-create-portal.md).
- Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul).
- Kunskaper om Desired State Configuration (DSC). Information om DSC finns i [Windows PowerShell Desired State Configuration Översikt.](/powershell/scripting/dsc/overview/overview)

## <a name="support-for-partial-configurations"></a>Stöd för partiella konfigurationer

Azure Automation State Configuration stöder användning av [partiella konfigurationer](/powershell/scripting/dsc/pull-server/partialconfigs). I det här scenariot är DSC konfigurerat för att hantera flera konfigurationer oberoende av varandra, och varje konfiguration hämtas från Azure Automation. Endast en konfiguration kan dock tilldelas till en nod per Automation-konto. Det innebär att om du använder två konfigurationer för en nod behöver du två Automation-konton.

Mer information om hur du registrerar en partiell konfiguration från en pull-tjänst finns i dokumentationen för [partiella konfigurationer.](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)

Mer information om hur team kan samarbeta för att gemensamt hantera servrar med konfiguration som kod finns i Förstå [DSC:s roll i en CI/CD-pipeline.](/powershell/scripting/dsc/overview/authoringadvanced)

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration [med cmdleten Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Skapa och ladda upp en konfiguration till Azure Automation

I en textredigerare skriver du följande och sparar det lokalt som **TestConfig.ps1**.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> I mer avancerade scenarier där du kräver att flera moduler importeras som tillhandahåller DSC-resurser kontrollerar du att varje modul har en `Import-DscResource` unik linje i konfigurationen.

Anropa [cmdleten Import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration) för att ladda upp konfigurationen till ditt Automation-konto.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilera en konfiguration till en nodkonfiguration

En DSC-konfiguration måste kompileras till en nodkonfiguration innan den kan tilldelas till en nod. Se [DSC-konfigurationer.](/powershell/scripting/dsc/configurations/configurations)

Anropa [cmdleten Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) för att kompilera konfigurationen till en `TestConfig` nodkonfiguration med namnet i ditt `TestConfig.WebServer` Automation-konto.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrera en virtuell dator som ska hanteras av State Configuration

Du kan använda Azure Automation State Configuration för att hantera virtuella Azure-datorer (både klassiska och Resource Manager), lokala virtuella datorer, Linux-datorer, virtuella AWS-datorer och lokala fysiska datorer. I det här avsnittet går vi in på hur du endast registrerar Azure Resource Manager virtuella datorer. Information om hur du registrerar andra typer av datorer finns i [Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md).

Anropa [cmdleten Register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode) för att registrera den virtuella datorn Azure Automation State Configuration som en hanterad nod. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Ange inställningar för konfigurationsläge

Använd [cmdleten Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) för att registrera en virtuell dator som en hanterad nod och ange konfigurationsegenskaper. Du kan till exempel ange att datorns tillstånd endast ska tillämpas en gång genom att `ApplyOnly` ange som värdet för egenskapen `ConfigurationMode` . State Configuration försöker inte tillämpa konfigurationen efter den första kontrollen.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Du kan också ange hur ofta DSC kontrollerar konfigurationstillståndet med hjälp av `ConfigurationModeFrequencyMins` egenskapen . Mer information om DSC-konfigurationsinställningar finns [i Konfigurera den lokala Konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Tilldela en nodkonfiguration till en hanterad nod

Nu kan vi tilldela den kompilerade nodkonfigurationen till den virtuella dator som vi vill konfigurera.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Detta tilldelar nodkonfigurationen med namnet `TestConfig.WebServer` till den registrerade DSC-noden `DscVm` . Som standard kontrolleras DSC-noden för kompatibilitet med nodkonfigurationen var 30:e minut. Information om hur du ändrar kompatibilitetskontrollens intervall finns i [Konfigurera den lokala Konfigurationshanteraren](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Kontrollera kompatibilitetsstatusen för en hanterad nod

Du kan hämta rapporter om kompatibilitetsstatus för en hanterad nod med hjälp av cmdleten [Get-AzAutomationDscNodeReport.](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport)

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Ta bort noder från tjänsten

När du lägger till en nod i Azure Automation State Configuration anges inställningarna i Lokal Konfigurationshanteraren att registreras med tjänsten och hämta konfigurationer och nödvändiga moduler för att konfigurera datorn.
Om du väljer att ta bort noden från tjänsten kan du göra det med antingen Azure Portal eller Az-cmdletarna.

> [!NOTE]
> När du avregistrerar en nod från tjänsten anges endast Konfigurationshanteraren lokala inställningar så att noden inte längre ansluter till tjänsten.
> Detta påverkar inte konfigurationen som för närvarande tillämpas på noden.
> Om du vill ta bort den aktuella konfigurationen använder du [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) eller tar bort den lokala konfigurationsfilen (det här är det enda alternativet för Linux-noder).

### <a name="azure-portal"></a>Azure Portal

Från Azure Automation klickar du **på State Configuration (DSC)** i innehållsförteckningen.
Klicka sedan **på** Noder för att visa listan över noder som har registrerats med tjänsten.
Klicka på namnet på den nod som du vill ta bort.
I nodvyn som öppnas klickar du på **Avregistrera**.

### <a name="powershell"></a>PowerShell

Om du vill avregistrera en nod från Azure Automation State Configuration-tjänsten med Hjälp av PowerShell följer du dokumentationen för cmdleten [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode).

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att gå [till Kom igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Information om hur du aktiverar noder finns i [Aktivera Azure Automation State Configuration](automation-dsc-onboarding.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns i [Kompilera DSC-konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- Ett exempel på hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Konfigurera kontinuerlig distribution med Chocolatey](automation-dsc-cd-chocolatey.md).
- Prisinformation finns i [Azure Automation State Configuration priser.](https://azure.microsoft.com/pricing/details/automation/)
- En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation).
