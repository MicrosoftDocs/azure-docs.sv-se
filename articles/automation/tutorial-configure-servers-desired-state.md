---
title: Konfigurera datorer till önskat tillstånd i Azure Automation
description: Den här artikeln beskriver hur du konfigurerar datorer till önskad status med Azure Automation tillstånds konfiguration.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: f16db3f55ebd0f09e4d7b75750fa319daf03977e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99053575"
---
# <a name="configure-machines-to-a-desired-state"></a>Konfigurera datorer till ett önskat tillstånd

Med Azure Automation tillstånds konfiguration kan du ange konfigurationer för dina servrar och se till att servrarna är i det angivna läget över tid.

> [!div class="checklist"]
> - Publicera en virtuell dator som ska hanteras av Azure Automation DSC
> - Ladda upp en konfiguration till Azure Automation
> - Kompilera en konfiguration till en Node-konfiguration
> - Tilldela en nods konfiguration till en hanterad nod
> - Kontrol lera status för efterlevnad för en hanterad nod

I den här självstudien använder vi en enkel [DSC-konfiguration](/powershell/scripting/dsc/configurations/configurations) som garanterar att IIS är installerat på den virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure Automation-konto. Mer information om ett Automation-konto och dess krav finns i [Översikt över Automation-kontots autentisering](./automation-security-overview.md).
- En Azure Resource Manager virtuell dator (inte klassisk) som kör Windows Server 2008 R2 eller senare. Anvisningar om hur du skapar en virtuell dator finns [i skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/windows/quick-create-portal.md).
- Azure PowerShell modul version 3,6 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul).
- Förtrogen med önskad tillstånds konfiguration (DSC). Information om DSC finns i [Översikt över önskad tillstånds konfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Stöd för partiella konfigurationer

Azure Automation tillstånds konfiguration stöder [partiella konfigurationer](/powershell/scripting/dsc/pull-server/partialconfigs). I det här scenariot konfigureras DSC för att hantera flera konfigurationer oberoende och varje konfiguration hämtas från Azure Automation. Men bara en konfiguration kan tilldelas till en nod per Automation-konto. Det innebär att om du använder två konfigurationer för en nod behöver du två Automation-konton.

Mer information om hur du registrerar en del konfiguration från en pull-tjänst finns i dokumentationen för [partiella konfigurationer](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Mer information om hur team kan samar beta för att hantera servrar med konfiguration som kod finns i [förstå DSC-rollen i en CI/CD-pipeline](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med cmdleten [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Skapa och ladda upp en konfiguration till Azure Automation

Skriv följande i en text redigerare och spara den lokalt som **TestConfig.ps1**.

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
> I mer avancerade scenarier där du kräver att flera moduler importeras som tillhandahåller DSC-resurser, se till att varje modul har en unik `Import-DscResource` rad i konfigurationen.

Anropa cmdleten [import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration) för att överföra konfigurationen till ditt Automation-konto.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilera en konfiguration till en Node-konfiguration

En DSC-konfiguration måste kompileras till en noduppsättning innan den kan tilldelas till en nod. Se [DSC-konfigurationer](/powershell/scripting/dsc/configurations/configurations).

Anropa cmdleten [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) för att kompilera `TestConfig` konfigurationen till en Node-konfiguration med namnet `TestConfig.WebServer` i ditt Automation-konto.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrera en virtuell dator som ska hanteras av tillstånds konfiguration

Du kan använda Azure Automation tillstånds konfiguration för att hantera virtuella Azure-datorer (både klassiska och Resource Manager), lokala virtuella datorer, Linux-datorer, virtuella AWS-datorer och lokala fysiska datorer. I det här avsnittet beskriver vi hur du registrerar endast Azure Resource Manager virtuella datorer. Information om hur du registrerar andra typer av datorer finns i [onboarding Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md).

Anropa cmdleten [register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode) för att registrera din virtuella dator med Azure Automation tillstånds konfiguration som en hanterad nod. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Ange inställningar för konfigurations läge

Använd cmdleten [register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) för att registrera en virtuell dator som en hanterad nod och ange konfigurations egenskaper. Du kan till exempel ange att datorns tillstånd ska tillämpas bara en gång genom att ange `ApplyOnly` som `ConfigurationMode` egenskaps värde. Tillstånds konfigurationen försöker inte tillämpa konfigurationen efter den första kontrollen.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Du kan också ange hur ofta DSC ska kontrol lera konfigurations statusen med hjälp av `ConfigurationModeFrequencyMins` egenskapen. Mer information om konfigurations inställningar för DSC finns i [Konfigurera den lokala Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Tilldela en nods konfiguration till en hanterad nod

Nu kan vi tilldela den kompilerade nodens konfiguration till den virtuella dator som vi vill konfigurera.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Detta tilldelar nodens konfiguration med namnet `TestConfig.WebServer` till den registrerade DSC-noden `DscVm` . DSC-noden kontrol leras som standard med nodens konfiguration var 30: e minut. Information om hur du ändrar intervallet för kompatibilitetskontroll finns i [Konfigurera den lokala Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Kontrol lera status för efterlevnad för en hanterad nod

Du kan få rapporter om kompatibilitetsstatus för en hanterad nod med hjälp av cmdleten [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) .

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Ta bort noder från tjänsten

När du lägger till en nod i Azure Automation tillstånds konfiguration anges inställningarna i lokala Configuration Manager att registreras med tjänsten och hämta konfigurationer och obligatoriska moduler för att konfigurera datorn.
Om du väljer att ta bort noden från tjänsten kan du göra det med hjälp av antingen Azure Portal eller AZ-cmdletar.

> [!NOTE]
> När du avregistrerar en nod från tjänsten anges bara de lokala Configuration Manager inställningarna så att noden inte längre ansluter till tjänsten.
> Detta påverkar inte den konfiguration som för närvarande används på noden.
> Om du vill ta bort den aktuella konfigurationen använder du [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) eller tar bort den lokala konfigurations filen (detta är det enda alternativet för Linux-noder).

### <a name="azure-portal"></a>Azure Portal

Klicka på **tillstånds konfiguration (DSC)** i innehålls förteckningen från Azure Automation.
Klicka sedan på **noder** för att visa en lista över noder som är registrerade i tjänsten.
Klicka på namnet på den nod som du vill ta bort.
Klicka på **avregistrera** i vyn Node som öppnas.

### <a name="powershell"></a>PowerShell

Om du vill avregistrera en nod från Azure Automation tillstånds konfigurations tjänst med PowerShell följer du dokumentationen för cmdleten [unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode).

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns i [Kom igång med Azure Automation tillstånds konfiguration](automation-dsc-getting-started.md).
- Information om hur du aktiverar noder finns i [aktivera Azure Automation tillstånds konfiguration](automation-dsc-onboarding.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera DSC-konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md).
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [Konfigurera kontinuerlig distribution med choklad](automation-dsc-cd-chocolatey.md).
- Pris information finns i pris information för [Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
