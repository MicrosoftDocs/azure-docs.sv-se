---
title: Felsöka Azure Automation problem med delade resurser
description: Den här artikeln beskriver hur du felsöker och löser problem med Azure Automation delade resurser.
services: automation
ms.subservice: ''
ms.date: 03/12/2019
ms.topic: troubleshooting
ms.openlocfilehash: c4ede0bffedc256f4af621d4945ebbbea0f8a4b6
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896299"
---
# <a name="troubleshoot-shared-resource-issues"></a>Felsöka problem med delade resurser

Den här artikeln beskriver problem som kan uppstå när du använder [delade resurser](../automation-intro.md#shared-resources) i Azure Automation.

## <a name="modules"></a>Moduler

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenario: en modul fastnar under importen

#### <a name="issue"></a>Problem

En modul fastnar i *import* tillstånd när du importerar eller uppdaterar dina Azure Automation-moduler.

#### <a name="cause"></a>Orsak

Eftersom importen av PowerShell-moduler är en komplex process i flera steg kanske en modul inte importeras korrekt och kan fastna i ett tillfälligt tillstånd. Mer information om import processen finns i [Importera en PowerShell-modul](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Lösning

För att lösa det här problemet måste du ta bort modulen som fastnat med cmdleten [Remove-AzAutomationModule](/powershell/module/Az.Automation/Remove-AzAutomationModule) . Sedan kan du försöka importera modulen igen.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenario: AzureRM-moduler fastnar under importen efter ett uppdaterings försök

#### <a name="issue"></a>Problem

En banderoll med följande meddelande är kvar i ditt konto när du försöker uppdatera AzureRM-modulerna:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Orsak

Det finns ett känt problem med att uppdatera AzureRM-modulerna i ett Automation-konto. Mer specifikt uppstår problemet om modulerna finns i en resurs grupp med ett numeriskt namn som börjar med 0.

#### <a name="resolution"></a>Lösning

Om du vill uppdatera dina AzureRM-moduler i ditt Automation-konto måste kontot finnas i en resurs grupp med ett alfanumeriskt namn. Resurs grupper med numeriska namn som börjar med 0 kan inte uppdatera AzureRM-moduler för tillfället.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: modulen kan inte importeras eller så går det inte att köra cmdletar efter importen

#### <a name="issue"></a>Problem

En modul kan inte importeras eller så har den importer ATS, men inga cmdlet: ar extraheras.

#### <a name="cause"></a>Orsak

Några vanliga orsaker till att en modul inte kan importeras till Azure Automation är:

* Strukturen matchar inte den struktur som Automation behöver.
* Modulen är beroende av en annan modul som inte har distribuerats till ditt Automation-konto.
* Dess beroenden saknas i mappen.
* Cmdleten [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) används för att ladda upp modulen och du har inte angett den fullständiga lagrings Sök vägen eller inte har läst in modulen genom att använda en offentligt tillgänglig URL.

#### <a name="resolution"></a>Lösning

Använd någon av dessa lösningar för att åtgärda problemet:

* Se till att modulen följer formatet: ModuleName.zip-> Modulnamn eller versions nummer – > (modulnamn. psm1, ModuleName.psd1).
* Öppna **. psd1** -filen och se om modulen har några beroenden. Om det gör det laddar du upp dessa moduler till Automation-kontot.
* Se till att alla refererade **DLL** -filer finns i mappen modul.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: Update-AzureModule.ps1 pausar vid uppdatering av moduler

#### <a name="issue"></a>Problem

När du använder [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook för att uppdatera dina Azure-moduler, pausas uppdaterings processen.

#### <a name="cause"></a>Orsak

I denna Runbook är standardinställningen att avgöra hur många moduler som uppdateras samtidigt är 10. Uppdaterings processen är känslig för fel när för många moduler uppdateras samtidigt.

#### <a name="resolution"></a>Lösning

Det är inte vanligt att alla AzureRM-eller AZ-moduler krävs i samma Automation-konto. Du bör bara importera de vissa moduler som du behöver.

> [!NOTE]
> Undvik att importera hela `Az.Automation` eller `AzureRM.Automation` -modulen, som importerar alla inneslutna moduler.

Om uppdaterings processen pausas lägger du till `SimultaneousModuleImportJobCount` parametern i **Update-AzureModules.ps1** -skriptet och anger ett lägre värde än standardvärdet 10. Om du implementerar den här logiken kan du försöka med att börja med värdet 3 eller 5. `SimultaneousModuleImportJobCount` är en parameter för den **Update-AutomationAzureModulesForAccount** system Runbook som används för att uppdatera Azure-moduler. Om du gör den här ändringen körs uppdaterings processen längre, men har en bättre chans att slutföra. I följande exempel visas parametern och var den ska läggas till i runbooken:

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scenario: det går inte att skapa eller uppdatera ett Kör som-konto

#### <a name="issue"></a>Problem

När du försöker skapa eller uppdatera ett Kör som-konto får du ett fel som liknar följande:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Orsak

Du har inte de behörigheter som du behöver för att skapa eller uppdatera kör som-kontot, eller så är resursen låst på en resurs grupps nivå.

#### <a name="resolution"></a>Lösning

Om du vill skapa eller uppdatera ett Kör som-konto måste du ha nödvändig [behörighet](../manage-runas-account.md#permissions) för de olika resurser som används av kör som-kontot. 

Om problemet beror på ett lås kontrollerar du att låset kan tas bort. Gå sedan till den resurs som är låst i Azure Portal, högerklicka på låset och välj **ta bort**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: du får fel meddelandet "Det gick inte att hitta en start punkt med namnet" GetPerAdapterInfo "i DLL iplpapi.dll" när en runbook körs

#### <a name="issue"></a>Problem

När du kör en Runbook visas följande undantag:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Orsak

Det här felet beror sannolikt på ett felaktigt konfigurerat [Kör som-konto](../manage-runas-account.md).

#### <a name="resolution"></a>Lösning

Kontrol lera att kör som-kontot har kon figurer ATS korrekt. Kontrol lera sedan att du har rätt kod i din Runbook för att autentisera med Azure. I följande exempel visas ett kod avsnitt för att autentisera till Azure i en Runbook med hjälp av ett Kör som-konto.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Nästa steg

Om den här artikeln inte löser problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) . Detta är det officiella Microsoft Azure kontot för att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**.

