---
title: Använda moduler i Azure Automation
description: Den här artikeln beskriver hur du använder PowerShell-moduler för att aktivera cmdlets i runbooks och DSC-resurser i DSC-konfigurationer.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eaff96907b48ddc0fc92296a015ceb063149e6ec
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832760"
---
# <a name="manage-modules-in-azure-automation"></a>Använda moduler i Azure Automation

Azure Automation använder ett antal PowerShell-moduler för att aktivera cmdlets i runbooks och DSC-resurser i DSC-konfigurationer. Moduler som stöds är:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az).
* [Azure PowerShell AzureRM.Automation](/powershell/module/azurerm.automation/).
* Andra PowerShell-moduler.
* Intern `Orchestrator.AssetManagement.Cmdlets` modul.
* Python 2-moduler.
* Anpassade moduler som du skapar.

När du skapar ett Automation-konto Azure Automation vissa moduler som standard. Se [Standardmoduler](#default-modules).

## <a name="sandboxes"></a>Sandbox-miljöer

När Automation kör runbook- och DSC-kompileringsjobb läser den in modulerna i sandbox-miljöerna där runbooks kan köras och DSC-konfigurationerna kan kompileras. Automation placerar också automatiskt DSC-resurser i moduler på DSC-pull-servern. Datorer kan hämta resurser när de tillämpar DSC-konfigurationerna.

>[!NOTE]
>Se till att endast importera de moduler som dina runbooks och DSC-konfigurationer kräver. Vi rekommenderar inte att du importerar Az-rotmodulen. Den innehåller många andra moduler som du kanske inte behöver, vilket kan orsaka prestandaproblem. Importera enskilda moduler, till exempel Az.Compute, i stället.

Sandbox-miljön i molnet stöder högst 48 systemsamtal och begränsar alla andra anrop av säkerhetsskäl. Andra funktioner som hantering av autentiseringsuppgifter och vissa nätverk stöds inte i sandbox-miljön i molnet.

På grund av antalet moduler och cmdlets som ingår är det svårt att veta i förväg vilka av cmdletarna som gör anrop som inte stöds. I allmänhet har vi sett problem med cmdlets som kräver förhöjd åtkomst, kräver autentiseringsuppgifter som en parameter eller cmdlets som rör nätverk. Cmdlets som utför fullständiga stacknätverksåtgärder stöds inte i sandbox-miljön, inklusive [Connect-AipService](/powershell/module/aipservice/connect-aipservice) från AIPService PowerShell-modulen och [Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname) från DNSClient-modulen.

Det här är kända begränsningar i sandbox-miljön. Den rekommenderade lösningen är att distribuera en [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) eller använda [Azure Functions](../../azure-functions/functions-overview.md).

## <a name="default-modules"></a>Standardmoduler

I följande tabell visas moduler som Azure Automation importerar som standard när du skapar ditt Automation-konto. Automation kan importera nyare versioner av dessa moduler. Du kan dock inte ta bort den ursprungliga versionen från ditt Automation-konto, även om du tar bort en nyare version. Observera att dessa standardmoduler innehåller flera AzureRM-moduler.

Standardmodulerna kallas även för globala moduler. I Azure Portal är egenskapen **Global modul** **true när** du visar en modul som importerades när kontot skapades.

![Skärmbild av egenskapen global modul i Azure-portalen](../media/modules/automation-global-modules.png)

Automation importerar inte Az-rotmodulen automatiskt till nya eller befintliga Automation-konton. Mer information om hur du arbetar med dessa moduler [finns i Migrera till Az-moduler.](#migrate-to-az-modules)

> [!NOTE]
> Vi rekommenderar inte att du ändrar moduler och runbooks [](../automation-solution-vm-management.md) i Automation-konton som används för distribution av Starta/stoppa virtuella datorer när de inte används-funktionen.

|Modulnamn|Version|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Az-moduler

För Az.Automation har de flesta cmdletarna samma namn som de som används för AzureRM-modulerna, förutom att `AzureRM` prefixet har ändrats till `Az` . En lista över Az-moduler som inte följer den här namngivningskonventionen finns i [listan över undantag.](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)

## <a name="internal-cmdlets"></a>Interna cmdlets

Azure Automation stöder den interna `Orchestrator.AssetManagement.Cmdlets` modulen för Log Analytics-agenten för Windows, som är installerad som standard. I följande tabell definieras de interna cmdletarna. Dessa cmdlets är utformade för att användas i stället för Azure PowerShell cmdlets för att interagera med delade resurser. De kan hämta hemligheter från krypterade variabler, autentiseringsuppgifter och krypterade anslutningar.

>[!NOTE]
>De interna cmdletarna är bara tillgängliga när du kör runbooks i Sandbox-miljön i Azure eller på en Windows-Hybrid Runbook Worker. 

|Name|Beskrivning|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Observera att de interna cmdletarna skiljer sig i namngivning från Az- och AzureRM-cmdlets. Interna cmdlet-namn innehåller inte ord `Azure` som eller `Az` i substantivet, men använder ordet `Automation` . Vi rekommenderar att de använder över användningen av Az- eller AzureRM-cmdlets under runbook-körning i en Azure-sandbox-miljö eller på en Windows-Hybrid Runbook Worker. De kräver färre parametrar och körs i kontexten för ditt jobb som redan körs.

Använd Az- eller AzureRM-cmdlets för att manipulera Automation-resurser utanför kontexten för en runbook. 

## <a name="python-modules"></a>Python-moduler

Du kan skapa Python 2-runbooks i Azure Automation. Information om Python-moduler finns [i Hantera Python 2-paket i Azure Automation](../python-packages.md).

## <a name="custom-modules"></a>Anpassade moduler

Azure Automation har stöd för anpassade PowerShell-moduler som du skapar för användning med dina runbooks och DSC-konfigurationer. En typ av anpassad modul är en integreringsmodul som eventuellt innehåller en fil med metadata för att definiera de anpassade funktionerna för modul-cmdletarna. Ett exempel på användning av en integreringsmodul finns i [Lägg till en anslutningstyp](../automation-connections.md#add-a-connection-type).

Azure Automation kan importera en anpassad modul för att göra dess cmdlets tillgängliga. I bakgrunden lagrar den modulen och använder den i Azure-sandbox-miljöerna, precis som andra moduler.

## <a name="migrate-to-az-modules"></a>Migrera till Az-moduler

Det här avsnittet beskriver hur du migrerar till Az-modulerna i Automation. Mer information finns i [Migrera Azure PowerShell från AzureRM till Az.](/powershell/azure/migrate-from-azurerm-to-az)

Vi rekommenderar inte att du kör AzureRM-moduler och Az-moduler i samma Automation-konto. När du är säker på att du vill migrera från AzureRM till Az är det bäst att helt genomföra en fullständig migrering. Automation återanvänder ofta sandbox-miljöerna i Automation-kontot för att spara vid starttider. Om du inte gör en fullständig modulmigrering kan du starta ett jobb som endast använder AzureRM-moduler och sedan starta ett annat jobb som endast använder Az-moduler. Sandbox-miljön kraschar snart och du får ett felmeddelande om att modulerna inte är kompatibla. Den här situationen resulterar i slumpmässigt förekommande krascher för en viss runbook eller konfiguration.

>[!NOTE]
>När du skapar ett nytt Automation-konto, även efter migreringen till Az-moduler, installerar Automation AzureRM-modulerna som standard. Du kan fortfarande uppdatera självstudie-runbooks med AzureRM-cmdlets. Du bör dock inte köra dessa runbooks.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Testa dina runbooks och DSC-konfigurationer före modulmigreringen

Se till att testa alla runbooks och DSC-konfigurationer noggrant i ett separat Automation-konto innan du migrerar till Az-modulerna. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Stoppa och avplanera alla runbooks som använder AzureRM-moduler

För att säkerställa att du inte kör några befintliga runbooks eller DSC-konfigurationer som använder AzureRM-moduler måste du stoppa och avplanera alla berörda runbooks och konfigurationer. Kontrollera först att du granskar varje runbook eller DSC-konfiguration och dess scheman separat, så att du kan schemalägga om objektet i framtiden om det behövs.

När du är redo att ta bort dina scheman kan du antingen använda Azure Portal eller cmdleten [Remove-AzureRmAutomationSchedule.](/powershell/module/azurerm.automation/remove-azurermautomationschedule) Se [Ta bort ett schema.](schedules.md#remove-a-schedule)

### <a name="remove-azurerm-modules"></a>Ta bort AzureRM-moduler

Du kan ta bort AzureRM-modulerna innan du importerar Az-modulerna. Men om du gör det kan du avbryta synkroniseringen av källkontrollen och orsaka att skript som fortfarande är schemalagda misslyckas. Om du bestämmer dig för att ta bort modulerna kan du [gå till Avinstallera AzureRM.](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm)

### <a name="import-az-modules"></a>Importera Az-moduler

Om du importerar en Az-modul till ditt Automation-konto importeras inte modulen automatiskt till den PowerShell-session som runbooks använder. Moduler importeras till PowerShell-sessionen i följande situationer:

* När en runbook anropar en cmdlet från en modul.
* När en runbook importerar modulen explicit med [cmdleten Import-Module.](/powershell/module/microsoft.powershell.core/import-module)
* När en runbook importerar modulen explicit med hjälp av [modulsatsen](/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) . Using-instruktionen stöds från och med Windows PowerShell 5.0 och stöder import av klasser och uppräkningstyper.
* När en Runbook importerar en annan beroende modul.

Du kan importera Az-modulerna till Automation-kontot från Azure Portal. Kom ihåg att bara importera de Az-moduler som du behöver, inte alla Az-moduler som är tillgängliga. Eftersom [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) är ett beroende för de andra Az-modulerna måste du importera den här modulen före andra.

1. Från ditt Automation-konto går du **till Delade resurser** och väljer **Moduler**.
2. Välj **Bläddra i galleriet**.  
3. I sökfältet anger du modulnamnet (till exempel `Az.Accounts` ).
4. På sidan PowerShell-modul väljer du **Importera för** att importera modulen till ditt Automation-konto.

    ![Skärmbild av att importera moduler till ditt Automation-konto](../media/modules/import-module.png)

Du kan också göra den här importen [via PowerShell-galleriet](https://www.powershellgallery.com)genom att söka efter den modul som ska importeras. När du hittar modulen markerar du den och väljer **Azure Automation** fliken. Välj **Distribuera för att Azure Automation**.

![Skärmbild av att importera moduler direkt från PowerShell-galleriet](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testa dina runbooks

När du har importerat Az-modulerna till Automation-kontot kan du börja redigera dina runbooks och DSC-konfigurationer för att använda de nya modulerna. Ett sätt att testa ändringen av en Runbook för att använda de nya cmdletarna är att använda kommandot `Enable-AzureRmAlias -Scope Process` i början av runbooken. Genom att lägga till det här kommandot i din runbook kan skriptet köras utan ändringar.

## <a name="author-modules"></a>Skapa moduler

Vi rekommenderar att du följer övervägandena i det här avsnittet när du skapar en anpassad PowerShell-modul för användning i Azure Automation. För att förbereda modulen för import måste du skapa minst en .psd1-, .psm1- eller PowerShell-modul-DLL-fil med samma namn som modulmappen.  Sedan zippa du upp modulmappen så att Azure Automation kan importera den som en enda fil. **ZIP-paketet** ska ha samma namn som den inneslutna modulmappen.

Mer information om hur du skapar en PowerShell-modul finns i [Skriva en PowerShell-skriptmodul.](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)

### <a name="version-folder"></a>Versionsmapp

Med PowerShell sida vid sida-modulversionshantering kan du använda mer än en version av en modul i PowerShell. Detta kan vara användbart om du har äldre skript som har testats och endast fungerar mot en viss version av en PowerShell-modul, men andra skript kräver en nyare version av samma PowerShell-modul.

Skapa PowerShell-moduler så att de innehåller flera versioner genom att skapa modulmappen och sedan skapa en mapp i den här modulmappen för varje version av modulen som du vill ska kunna användas. I följande exempel tillhandahåller en modul som heter *TestModule* två versioner, 1.0.0 och 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

I var och en av versionsmapparna kopierar du dina PowerShell .psm1-, .psd1- eller **PowerShell-modul-DLL-filer** som utgör en modul till respektive versionsmapp. Zippa upp modulmappen så att Azure Automation kan importera den som en enda ZIP-fil. Automation visar endast den högsta versionen av den importerade modulen, men om modulpaketet innehåller sida-vid-sida-versioner av modulen, är de alla tillgängliga för användning i dina runbooks eller DSC-konfigurationer.  

Automation stöder moduler som innehåller versioner sida vid sida i samma paket, men det stöder inte användning av flera versioner av en modul i modulpaketimporter. Du kan till exempel importera **modul A** som innehåller version 1 och 2 till ditt Automation-konto. Senare uppdaterar du **modul A** för att inkludera version 3 och 4. När du importerar till ditt Automation-konto kan endast versionerna 3 och 4 användas i runbooks eller DSC-konfigurationer. Om du vill att alla versioner – 1, 2, 3 och 4 ska vara tillgängliga ska ZIP-filen som du importerar innehålla versionerna 1, 2, 3 och 4.

Om du ska använda olika versioner av samma modul mellan runbooks bör du alltid deklarera den version som du vill använda i din runbook med hjälp av cmdleten och inkludera `Import-Module` parametern `-RequiredVersion <version>` . Även om den version som du vill använda är den senaste versionen. Det beror på att runbook-jobb kan köras i samma sandbox-miljö. Om sandbox-miljön redan uttryckligen har läst in en modul med ett visst versionsnummer, eftersom ett tidigare jobb i sandbox-miljön sa det, kommer framtida jobb i sandbox-miljön inte att automatiskt läsa in den senaste versionen av modulen. Det beror på att vissa versioner av den redan har lästs in i sandbox-miljön.

För en DSC-resurs använder du följande kommando för att ange en viss version:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Hjälpinformation

Inkludera en sammanfattning, beskrivning och hjälp-URI för varje cmdlet i modulen. I PowerShell kan du definiera hjälpinformation för cmdlets med hjälp av `Get-Help` cmdleten . I följande exempel visas hur du definierar en sammanfattning och hjälp-URI i en **.psm1-modulfil.**

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Om du anger den här informationen visas hjälptext via `Get-Help` cmdleten i PowerShell-konsolen. Den här texten visas också i Azure Portal.

  ![Skärmbild av hjälp för integreringsmoduler](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Anslutningstyp

Om modulen ansluter till en extern tjänst definierar du en anslutningstyp med hjälp av en [anpassad integrationsmodul](#custom-modules). Varje cmdlet i modulen ska acceptera en instans av den anslutningstypen (anslutningsobjektet) som en parameter. Användare mappar parametrar för anslutningstillgången till cmdletens motsvarande parametrar varje gång de anropar en cmdlet.

![Använda en anpassad anslutning i Azure Portal](../media/modules/connection-create-new.png)

I följande runbook-exempel används en Contoso-anslutningstillgång som anropas för att komma `ContosoConnection` åt Contoso-resurser och returnera data från den externa tjänsten. I det här exemplet mappas fälten till egenskaperna `UserName` och för ett objekt och skickas sedan till `Password` `PSCredential` cmdleten .

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Ett enklare och bättre sätt att använda det här beteendet är genom att direkt skicka anslutningsobjektet till cmdleten:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

Du kan aktivera liknande beteende för dina cmdlets genom att tillåta att de accepterar ett anslutningsobjekt direkt som en parameter, i stället för bara anslutningsfält för parametrar. Vanligtvis vill du ha en parameteruppsättning för var och en, så att en användare som inte använder Automation kan anropa dina cmdlets utan att skapa en hash-tabell som fungerar som anslutningsobjektet. Parameteruppsättningen `UserAccount` används för att skicka egenskaperna för anslutningsfältet. `ConnectionObject` gör att du kan skicka anslutningen direkt.

### <a name="output-type"></a>Utdatatyp

Definiera utdatatypen för alla cmdlets i modulen. Genom att definiera en utdatatyp för en cmdlet kan IntelliSense i designtid avgöra utdataegenskaperna för cmdleten under redigeringen. Den här praxis är särskilt användbar vid grafisk runbook-redigering, där designkunskaper är nyckeln till en enkel användarupplevelse med din modul.

Lägg `[OutputType([<MyOutputType>])]` till , där är en giltig `MyOutputType` typ. Mer information om `OutputType` finns i Om Functions [OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Följande kod är ett exempel på hur du lägger `OutputType` till i en cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Skärmbild av utdatatypen för en grafisk runbook](../media/modules/runbook-graphical-module-output-type.png)

  Det här beteendet liknar funktionen "type ahead" i en cmdlet:s utdata i PowerShell-integreringstjänstmiljön, utan att behöva köra den.

  ![Skärmbild av POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet-tillstånd

Gör alla cmdlets i modulen tillståndslösa. Flera runbook-jobb kan köras samtidigt i samma `AppDomain` process och sandbox-miljö. Om något tillstånd delas på dessa nivåer kan jobben påverka varandra. Det här beteendet kan leda till tillfälliga och svåra diagnostiseringsproblem. Här är ett exempel på vad du inte ska göra:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Modulberoende

Kontrollera att modulen finns i ett paket som kan kopieras med hjälp av xcopy. Automation-moduler distribueras till Automation-sandbox-miljöerna när runbooks körs. Modulerna måste fungera oberoende av värden som kör dem.

Du bör kunna zippa upp och flytta ett modulpaket och få det att fungera som vanligt när det importeras till en annan värds PowerShell-miljö. Se till att modulen inte är beroende av filer utanför modulmappen som komprimeras när modulen importeras till Automation.

Modulen ska inte vara beroende av några unika registerinställningar på en värd. Exempel är de inställningar som görs när en produkt installeras.

### <a name="module-file-paths"></a>Sökvägar till modulfiler

Kontrollera att alla filer i modulen har sökvägar med färre än 140 tecken. Sökvägar över 140 tecken orsakar problem med att importera runbooks. Automation kan inte importera en fil med en sökväg som är större än 140 tecken till PowerShell-sessionen med `Import-Module` .

## <a name="import-modules"></a>Importera moduler

I det här avsnittet definieras flera sätt att importera en modul till ditt Automation-konto.

### <a name="import-modules-in-the-azure-portal"></a>Importera moduler i Azure Portal

Så här importerar du en modul i Azure Portal:

1. Gå till ditt Automation-konto.
2. Under **Delade resurser** väljer du **Moduler.**
3. Välj **Lägg till en modul**.
4. Välj den **.zip-fil** som innehåller din modul.
5. Välj **OK** för att börja importera processen.

### <a name="import-modules-by-using-powershell"></a>Importera moduler med hjälp av PowerShell

Du kan använda cmdleten [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) för att importera en modul till ditt Automation-konto. Cmdleten tar en URL för en moduls .zip-paket.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Du kan också använda samma cmdlet för att importera en modul från PowerShell-galleriet direkt. Se till att hämta `ModuleName` `ModuleVersion` och från [PowerShell-galleriet](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Importera moduler från PowerShell-galleriet

Du kan importera [PowerShell-galleriet](https://www.powershellgallery.com) moduler antingen direkt från galleriet eller från ditt Automation-konto.

Så här importerar du en modul direkt från PowerShell-galleriet:

1. Gå till https://www.powershellgallery.com och sök efter den modul som ska importeras.
2. Under **Installationsalternativ** går du till **fliken Azure Automation** och väljer Distribuera till **Azure Automation**. Den här åtgärden öppnar Azure Portal. 
3. På sidan Importera väljer du ditt Automation-konto och sedan **OK.**

![Skärmbild av PowerShell-galleriet importmodulen](../media/modules/powershell-gallery.png)

Importera en PowerShell-galleriet direkt från ditt Automation-konto:

1. Under **Delade resurser** väljer du **Moduler.** 
2. Välj **Bläddra i** galleriet och sök sedan i galleriet efter en modul. 
3. Välj den modul som ska importeras och välj **Importera**. 
4. Välj **OK** för att starta importen.

![Skärmbild av att importera PowerShell-galleriet en modul från Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Ta bort moduler

Om du har problem med en modul eller om du behöver återställa till en tidigare version av en modul kan du ta bort den från ditt Automation-konto. Du kan inte ta bort de ursprungliga versionerna av [standardmodulerna](#default-modules) som importeras när du skapar ett Automation-konto. Om modulen som ska tas bort är en nyare version av en av standardmodulerna [återställs](#default-modules)den till den version som installerades med ditt Automation-konto. Annars tas alla moduler som du tar bort från Ditt Automation-konto bort.

### <a name="delete-modules-in-the-azure-portal"></a>Ta bort moduler i Azure Portal

Så här tar du bort en modul i Azure Portal:

1. Gå till ditt Automation-konto. Under **Delade resurser** väljer du **Moduler.**
2. Välj den modul som du vill ta bort.
3. På sidan Modul väljer du Ta **bort**. Om den här modulen är en [av standardmodulerna](#default-modules)återställs den till den version som fanns när Automation-kontot skapades.

### <a name="delete-modules-by-using-powershell"></a>Ta bort moduler med hjälp av PowerShell

Om du vill ta bort en modul via PowerShell kör du följande kommando:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder Azure PowerShell-moduler [finns i Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps).

* Mer information om hur du skapar PowerShell-moduler finns i [Skriva en Windows PowerShell modul.](/powershell/scripting/developer/module/writing-a-windows-powershell-module)
