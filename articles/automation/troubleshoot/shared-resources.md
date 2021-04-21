---
title: Felsöka Azure Automation med delade resurser
description: Den här artikeln beskriver hur du felsöker och löser problem Azure Automation delade resurser.
services: automation
ms.subservice: ''
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b497b0a8f34b4310e3f11beed982c4453fc79159
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830834"
---
# <a name="troubleshoot-shared-resource-issues"></a>Felsöka problem med delade resurser

Den här artikeln beskriver problem som kan uppstå när du använder [delade resurser i](../automation-intro.md#shared-resources) Azure Automation.

## <a name="modules"></a>Moduler

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenario: En modul fastnar under importen

#### <a name="issue"></a>Problem

En modul har fastnat i *importtillståndet* när du importerar eller uppdaterar dina Azure Automation moduler.

#### <a name="cause"></a>Orsak

Eftersom import av PowerShell-moduler är en komplex process i flera steg kanske en modul inte importeras korrekt och kan fastna i ett tillfälligt tillstånd. Mer information om importprocessen finns i Importera [en PowerShell-modul](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Lösning

För att lösa problemet måste du ta bort modulen som har fastnat med hjälp av cmdleten [Remove-AzAutomationModule.](/powershell/module/Az.Automation/Remove-AzAutomationModule) Du kan sedan försöka importera modulen igen.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenario: AzureRM-moduler fastnar under importen efter ett uppdateringsförsök

#### <a name="issue"></a>Problem

En banderoll med följande meddelande finns kvar på ditt konto när du har försökt uppdatera dina AzureRM-moduler:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Orsak

Det finns ett känt problem med att uppdatera AzureRM-modulerna i ett Automation-konto. Mer specifikt inträffar problemet om modulerna finns i en resursgrupp med ett numeriskt namn som börjar med 0.

#### <a name="resolution"></a>Lösning

Om du vill uppdatera Dina AzureRM-moduler i ditt Automation-konto måste kontot finnas i en resursgrupp med ett alfanumeriskt namn. Resursgrupper med numeriska namn som börjar med 0 kan inte uppdatera AzureRM-moduler just nu.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: Det går inte att importera modulen eller så går det inte att köra cmdlets efter import

#### <a name="issue"></a>Problem

En modul kan inte importeras eller importeras korrekt, men inga cmdlets extraheras.

#### <a name="cause"></a>Orsak

Några vanliga orsaker till att en modul inte kan importeras till Azure Automation är:

* Strukturen matchar inte den struktur som Automation behöver.
* Modulen är beroende av en annan modul som inte har distribuerats till ditt Automation-konto.
* Modulen saknar sina beroenden i mappen .
* Cmdleten [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) används för att ladda upp modulen och du har inte angett den fullständiga lagringssökvägen eller inte har läst in modulen med hjälp av en offentligt tillgänglig URL.

#### <a name="resolution"></a>Lösning

Använd någon av dessa lösningar för att åtgärda problemet:

* Kontrollera att modulen följer formatet: ModuleName.zip -> ModuleName eller Versionsnummer -> (ModuleName.psm1, ModuleName.psd1).
* Öppna **.psd1-filen** och se om modulen har några beroenden. I så fall laddar du upp de här modulerna till Automation-kontot.
* Kontrollera att alla refererade **DLL-filer** finns i modulmappen.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: Update-AzureModule.ps1 pausas vid uppdatering av moduler

#### <a name="issue"></a>Problem

När du använder den virtuella [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) för att uppdatera dina Azure-moduler pausas moduluppdateringsprocessen.

#### <a name="cause"></a>Orsak

För den här runbooken är standardinställningen för att avgöra hur många moduler som uppdateras samtidigt 10. Uppdateringsprocessen är känslig för fel när för många moduler uppdateras samtidigt.

#### <a name="resolution"></a>Lösning

Det är inte vanligt att alla AzureRM- eller Az-moduler krävs i samma Automation-konto. Du bör bara importera de specifika moduler som du behöver.

> [!NOTE]
> Undvik att importera hela `Az.Automation` modulen eller , som importerar alla `AzureRM.Automation` inneslutna moduler.

Om uppdateringsprocessen pausar lägger du till parametern iUpdate-AzureModules.ps1skriptet och anger ett lägre värde än `SimultaneousModuleImportJobCount` standardvärdet 10.  Om du implementerar den här logiken kan du prova att börja med värdet 3 eller 5. `SimultaneousModuleImportJobCount` är en parameter för **runbooken Update-AutomationAzureModulesForAccount** som används för att uppdatera Azure-moduler. Om du gör den här justeringen körs uppdateringsprocessen längre, men har en bättre chans att slutföras. I följande exempel visas parametern och var den ska placera den i runbooken:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Kör som-konton

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scenario: Du kan inte skapa eller uppdatera ett Kör som-konto

#### <a name="issue"></a>Problem

När du försöker skapa eller uppdatera ett Kör som-konto får du ett felmeddelande som liknar följande:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Orsak

Du har inte de behörigheter som du behöver för att skapa eller uppdatera Kör som-kontot, eller så är resursen låst på resursgruppsnivå.

#### <a name="resolution"></a>Lösning

Om du vill skapa eller uppdatera ett Kör som-konto måste du ha [rätt behörighet](../automation-security-overview.md#permissions) till de olika resurser som används av Kör som-kontot.

Om problemet beror på ett lås kontrollerar du att låset kan tas bort. Gå sedan till den resurs som är låst Azure Portal, högerklicka på låset och välj Ta **bort**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: Du får felmeddelandet "Det går inte att hitta en startpunkt med namnet "GetPerAdapterInfo" i DLLiplpapi.dll" när du kör en runbook

#### <a name="issue"></a>Problem

När du kör en runbook får du följande undantag:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Orsak

Det här felet orsakas troligen av ett felaktigt konfigurerat [Kör som-konto.](../automation-security-overview.md)

#### <a name="resolution"></a>Lösning

Kontrollera att Kör som-kontot är korrekt konfigurerat. Kontrollera sedan att du har rätt kod i din runbook för att autentisera med Azure. I följande exempel visas ett kodfragment för att autentisera till Azure i en Runbook med hjälp av ett Kör som-konto.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Nästa steg

Om den här artikeln inte löser problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen.](https://azure.microsoft.com/support/forums/)
* Anslut med [@AzureSupport](https://twitter.com/azuresupport) . Detta är det officiella Microsoft Azure för att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Skapa en azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/)och välj **Få support.**
