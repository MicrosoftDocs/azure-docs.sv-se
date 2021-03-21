---
title: Använda moduler i Azure Automation
description: Den här artikeln beskriver hur du använder PowerShell-moduler för att aktivera cmdletar i Runbooks och DSC-resurser i DSC-konfigurationer.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: ae3329401a138bc0566ea93a8fbf2071fd44f02c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503427"
---
# <a name="manage-modules-in-azure-automation"></a>Använda moduler i Azure Automation

Azure Automation använder ett antal PowerShell-moduler för att aktivera cmdletar i Runbooks och DSC-resurser i DSC-konfigurationer. Moduler som stöds är:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az).
* [Azure PowerShell AzureRM. Automation](/powershell/module/azurerm.automation/).
* Andra PowerShell-moduler.
* Intern `Orchestrator.AssetManagement.Cmdlets` modul.
* Python 2-moduler.
* Anpassade moduler som du skapar.

När du skapar ett Automation-konto importerar Azure Automation vissa moduler som standard. Se [standardmoduler](#default-modules).

## <a name="sandboxes"></a>Sandbox-miljöer

När Automation kör Runbook-och DSC-kompileringar, läses modulerna in i sand lådor där Runbooks kan köras och DSC-konfigurationerna kan kompileras. Automation placerar även DSC-resurser i moduler på DSC-pull-servern automatiskt. Datorer kan hämta resurserna när de använder DSC-konfigurationer.

>[!NOTE]
>Se till att bara importera de moduler som dina runbooks och DSC-konfigurationer kräver. Vi rekommenderar inte att du importerar root AZ-modulen. Den innehåller många andra moduler som du kanske inte behöver, vilket kan orsaka prestanda problem. Importera enskilda moduler, till exempel AZ. Compute, i stället.

Cloud sandbox stöder högst 48 system anrop och begränsar alla andra anrop av säkerhets skäl. Andra funktioner som hantering av autentiseringsuppgifter och vissa nätverk stöds inte i Cloud sandbox.

På grund av antalet moduler och cmdletar som ingår är det svårt att veta i förväg vilken av cmdletarna kommer att göra anrop som inte stöds. I allmänhet har vi sett problem med cmdletar som kräver utökad åtkomst, kräver en autentiseringsuppgift som en parameter eller-cmdlet: ar som är relaterade till nätverk. Alla cmdletar som utför fullständiga stack nätverks åtgärder stöds inte i sandbox, inklusive [Connect-AipService](/powershell/module/aipservice/connect-aipservice) från AipService PowerShell-modulen och [matcha-DnsName](/powershell/module/dnsclient/resolve-dnsname) från DNSclient-modulen.

Dessa är kända begränsningar i sand boxen. Den rekommenderade lösningen är att distribuera en [hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) eller använda [Azure Functions](../../azure-functions/functions-overview.md).

## <a name="default-modules"></a>Standardmoduler

I följande tabell visas moduler som Azure Automation importerar som standard när du skapar ett Automation-konto. Automation kan importera nyare versioner av dessa moduler. Du kan dock inte ta bort den ursprungliga versionen från ditt Automation-konto, även om du tar bort en nyare version. Observera att dessa standardmoduler innehåller flera AzureRM-moduler.

Standardmodulerna kallas även globala moduler. I Azure Portal kommer den **globala modulen** att vara **sann** när du visar en modul som har importer ATS när kontot skapades.

![Skärm bild av egenskapen global modul i Azure Portal](../media/modules/automation-global-modules.png)

Automation importerar inte root AZ-modulen automatiskt till nya eller befintliga Automation-konton. Mer information om hur du arbetar med dessa moduler finns i [migrera till AZ-moduler](#migrate-to-az-modules).

> [!NOTE]
> Vi rekommenderar inte att ändra moduler och Runbooks i Automation-konton som används för att distribuera den [Starta/stoppa virtuella datorer när de inte används](../automation-solution-vm-management.md) funktionen.

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
| Microsoft. PowerShell. Core | 0 |
| Microsoft. PowerShell. Diagnostics |  |
| Microsoft. PowerShell. Management |  |
| Microsoft. PowerShell. Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft. WSMan. Management |  |
| Orchestrator. AssetManagement. cmdletar | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>AZ-moduler

För AZ. Automation har majoriteten av cmdletarna samma namn som de som används för AzureRM-modulerna, förutom att `AzureRM` prefixet har ändrats till `Az` . En lista över AZ-moduler som inte följer denna namngivnings konvention finns i [listan över undantag](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Interna cmdletar

Azure Automation stöder den interna `Orchestrator.AssetManagement.Cmdlets` modulen för Log Analytics agent för Windows, som installeras som standard. I följande tabell definieras de interna cmdletarna. Dessa cmdletar är avsedda att användas i stället för Azure PowerShell cmdlets för att interagera med delade resurser. De kan hämta hemligheter från krypterade variabler, autentiseringsuppgifter och krypterade anslutningar.

>[!NOTE]
>De interna cmdletarna är bara tillgängliga när du kör Runbooks i sandbox-miljön i Azure eller på en Windows Hybrid Runbook Worker. 

|Name|Beskrivning|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Observera att de interna cmdletarna skiljer sig från namn från AZ-och AzureRM-cmdletarna. Interna cmdlet-namn innehåller inte ord som `Azure` eller `Az` i substantiv, men Använd ordet `Automation` . Vi rekommenderar användning av AZ-eller AzureRM-cmdletar under körning av Runbook i en Azure-sandbox eller på en Windows-Hybrid Runbook Worker. De kräver färre parametrar och körs i kontexten för jobbet som redan körs.

Använd AZ-eller AzureRM-cmdletar för att manipulera Automation-resurser utanför en Runbook-kontext. 

## <a name="python-modules"></a>Python-moduler

Du kan skapa python 2-Runbooks i Azure Automation. Information om python-moduler finns [i hantera python 2-paket i Azure Automation](../python-packages.md).

## <a name="custom-modules"></a>Anpassade moduler

Azure Automation stöder anpassade PowerShell-moduler som du skapar för användning med dina runbooks och DSC-konfigurationer. En typ av anpassad modul är en integrerings modul som alternativt innehåller en fil med metadata som definierar de anpassade funktionerna för modul-cmdletar. Ett exempel på användningen av en integrerings modul finns i [Lägg till en Anslutnings typ](../automation-connections.md#add-a-connection-type).

Azure Automation kan importera en anpassad modul så att dess cmdlets är tillgängliga. I bakgrunden lagras modulen och används i Azure-sandbox, precis som i andra moduler.

## <a name="migrate-to-az-modules"></a>Migrera till AZ-moduler

Det här avsnittet beskriver hur du migrerar till AZ-modulerna i Automation. Mer information finns i [migrera Azure PowerShell från AzureRM till AZ](/powershell/azure/migrate-from-azurerm-to-az).

Vi rekommenderar inte att du kör AzureRM-moduler och AZ-moduler i samma Automation-konto. När du är säker på att du vill migrera från AzureRM till AZ, är det bäst att fullständigt genomföra en fullständig migrering. Automation återanvänder ofta sandbox i Automation-kontot för att spara vid start tider. Om du inte gör en fullständig flyttning av modulen kan du starta ett jobb som bara använder AzureRM-moduler och sedan starta ett annat jobb som bara använder AZ-moduler. Sand boxen kraschar snart och du får ett fel meddelande om att modulerna inte är kompatibla. I den här situationen uppstår slumpmässiga krascher för en viss Runbook eller konfiguration.

>[!NOTE]
>När du skapar ett nytt Automation-konto, även efter migrering till AZ-moduler, installerar Automation AzureRM-modulerna som standard. Du kan fortfarande uppdatera självstudiernas Runbooks med AzureRM-cmdletarna. Du bör dock inte köra dessa Runbooks.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Testa dina runbooks och DSC-konfigurationer innan du migrerar en modul

Se till att testa alla Runbooks och DSC-konfigurationer noggrant, i ett separat Automation-konto, innan du migrerar till AZ-modulerna. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Stoppa och ta bort schemat för alla Runbooks som använder AzureRM-moduler

För att se till att du inte kör några befintliga Runbooks eller DSC-konfigurationer som använder AzureRM-moduler måste du stoppa och ta bort schema för alla påverkade Runbooks och konfigurationer. Kontrol lera först att du granskar varje Runbook-eller DSC-konfiguration och dess scheman separat, för att säkerställa att du kan schemalägga om objektet i framtiden om det behövs.

När du är redo att ta bort dina scheman kan du antingen använda Azure Portal eller cmdleten [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) . Se [ta bort ett schema](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>Ta bort AzureRM-moduler

Det är möjligt att ta bort AzureRM-modulerna innan du importerar AZ-modulerna. Men om du gör det kan du avbryta synkronisering av käll kontroll och köra skript som fortfarande är schemalagda att fungera. Om du bestämmer dig för att ta bort modulerna, se [Avinstallera AzureRM](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm).

### <a name="import-az-modules"></a>Importera AZ-moduler

Om du importerar en AZ-modul till ditt Automation-konto importeras modulen inte automatiskt till PowerShell-sessionen som används av Runbooks. Moduler importeras till PowerShell-sessionen i följande situationer:

* När en Runbook anropar en cmdlet från en modul.
* När en Runbook importerar modulen explicit med cmdleten [import-module](/powershell/module/microsoft.powershell.core/import-module) .
* När en Runbook importerar modulen uttryckligen med instruktionen [using-module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) . Using-instruktionen stöds från och med Windows PowerShell 5,0 och stöder import av klasser och Enum-typ.
* När en Runbook importerar en annan beroende modul.

Du kan importera AZ-modulerna i Azure Portal. Kom ihåg att endast importera de AZ-moduler som du behöver, inte hela AZ. Automation-modulen. Eftersom [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) är ett beroende för de andra AZ-modulerna måste du importera den här modulen innan andra.

1. Från ditt Automation-konto, under **delade resurser**, väljer du **moduler**.
2. Välj **Bläddra i galleriet**.  
3. I Sök fältet anger du modulnamnet (till exempel `Az.Accounts` ).
4. På sidan PowerShell-modul väljer du **Importera** för att importera modulen till ditt Automation-konto.

    ![Skärm bild av importera moduler till ditt Automation-konto](../media/modules/import-module.png)

Du kan också göra den här importen via [PowerShell-galleriet](https://www.powershellgallery.com)genom att söka efter modulen att importera. När du hittar modulen väljer du den och klickar på fliken **Azure Automation** . Välj **distribuera till Azure Automation**.

![Skärm bild av importera moduler direkt från PowerShell-galleriet](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testa dina runbooks

När du har importerat AZ-modulerna till Automation-kontot kan du börja redigera dina runbooks och DSC-konfigurationer för att använda de nya modulerna. Ett sätt att testa ändringen av en Runbook att använda de nya cmdletarna är genom att använda `Enable-AzureRmAlias -Scope Process` kommandot i början av runbooken. Genom att lägga till det här kommandot i din Runbook kan skriptet köras utan ändringar.

## <a name="author-modules"></a>Author-moduler

Vi rekommenderar att du följer de överväganden som beskrivs i det här avsnittet när du skapar en anpassad PowerShell-modul för användning i Azure Automation. För att förbereda modulen för import måste du skapa minst en. psd1-,. psm1-eller PowerShell-modul **. dll** -fil med samma namn som module-mappen. Sedan registrerar du mappen module så att Azure Automation kan importera den som en enda fil. **Zip** -paketet ska ha samma namn som mappen för den inneslutna modulen.

Mer information om hur du redigerar en PowerShell-modul finns i [så här skriver du en PowerShell-modul för skript](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

### <a name="version-folder"></a>Versions mapp

Med PowerShell-versioner sida-vid-sida-modul kan du använda mer än en version av en modul i PowerShell. Detta kan vara användbart om du har äldre skript som har testats och endast fungerar med en viss version av en PowerShell-modul, men andra skript kräver en nyare version av samma PowerShell-modul.

Om du vill konstruera PowerShell-moduler så att de innehåller flera versioner skapar du mappen module och skapar sedan en mapp i den här modulen för varje version av modulen som du vill använda. I följande exempel tillhandahåller en modul som heter *TestModule* två versioner, 1.0.0 och 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

I var och en av versions mapparna kopierar du PowerShell. psm1-,. psd1-eller PowerShell-modulens **DLL** -filer som utgör en modul till respektive version-mapp. Zippa upp mappen modul så att Azure Automation kan importera den som en enda. zip-fil. Även om Automation endast visar den högsta versionen av modulen som importer ATS, om modulfönstret innehåller versioner sida vid sida av modulen, är de tillgängliga för användning i dina runbooks-eller DSC-konfigurationer.  

Automation stöder moduler som innehåller sida-vid-sida-versioner i samma paket, men har inte stöd för användning av flera versioner av en modul i en moduls paket import. Du kan till exempel importera **modul A**, som innehåller version 1 och 2 till ditt Automation-konto. Senare uppdaterar du **modul A** för att inkludera version 3 och 4, när du importerar till ditt Automation-konto, är det bara version 3 och 4 som kan användas i alla RUNBOOKS och DSC-konfigurationer. Om du vill att alla versioner – 1, 2, 3 och 4 ska vara tillgängliga kan du använda. zip-filen som importen ska innehålla version 1, 2, 3 och 4.

Om du ska använda olika versioner av samma modul mellan Runbooks, bör du alltid deklarera den version som du vill använda i din Runbook med hjälp av `Import-Module` cmdleten och inkludera parametern `-RequiredVersion <version>` . Även om den version som du vill använda är den senaste versionen. Detta beror på att Runbook-jobb kan köras i samma sandbox. Om sandbox redan har läst in en modul med ett visst versions nummer, eftersom ett tidigare jobb i sand boxen har nämnts, läser inte framtida jobb i sand Box automatiskt den senaste versionen av modulen. Det beror på att en viss version av den redan har lästs in i sand boxen.

För en DSC-resurs använder du följande kommando för att ange en viss version:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Hjälp information

Ta med en sammanfattning, beskrivning och hjälp-URI för varje cmdlet i modulen. I PowerShell kan du definiera hjälp information för cmdlets med hjälp av `Get-Help` cmdleten. I följande exempel visas hur du definierar en sammanfattning och hjälp-URI i en **. psm1** -modul-fil.

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
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Genom att ange den här informationen visas hjälp text via `Get-Help` cmdleten i PowerShell-konsolen. Den här texten visas också i Azure Portal.

  ![Skärm bild av hjälp för integrations modul](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Anslutningstyp

Om modulen ansluter till en extern tjänst definierar du en Anslutnings typ med hjälp av en [anpassad integrerings modul](#custom-modules). Varje cmdlet i modulen bör godkänna en instans av anslutnings typen (anslutnings objekt) som en parameter. Användarna mappar parametrarna för anslutnings till gången till cmdletens motsvarande parametrar varje gång de anropar en cmdlet.

![Använd en anpassad anslutning i Azure Portal](../media/modules/connection-create-new.png)

I följande Runbook-exempel används en Contoso-tillgång `ContosoConnection` som kallas för att komma åt contoso-resurser och returnera data från den externa tjänsten. I det här exemplet mappas fälten till `UserName` `Password` egenskaperna och för ett `PSCredential` objekt och skickas sedan till cmdleten.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Ett enklare och bättre sätt att utnyttja det här beteendet är genom att skicka anslutningsobjektet direkt till cmdleten:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

Du kan aktivera liknande beteende för dina cmdlets genom att tillåta att de accepterar ett anslutnings objekt direkt som en parameter, i stället för bara anslutnings fält för parametrar. Vanligt vis vill du ha en parameter uppsättning för varje, så att en användare som inte använder Automation kan anropa dina cmdletar utan att skapa en hash-post som fungerar som anslutningsobjektet. Parameter uppsättningen `UserAccount` används för att skicka anslutnings fält egenskaper. `ConnectionObject` gör att du kan skicka anslutningen direkt genom.

### <a name="output-type"></a>Utdatatyp

Definiera utdatatypen för alla cmdletar i modulen. Genom att definiera en utdatatyp för en cmdlet kan du använda IntelliSense i design tid för att avgöra cmdletens utmatnings egenskaper under redigeringen. Den här metoden är särskilt användbar vid redigering av grafiska Runbook-jobb för vilka design tids kunskaper är en nyckel till en enkel användar upplevelse med modulen.

Lägg till `[OutputType([<MyOutputType>])]` , där `MyOutputType` är en giltig typ. Läs mer om `OutputType` [funktionerna i OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Följande kod är ett exempel på att lägga till `OutputType` i en-cmdlet:

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

  ![Skärm bild av grafisk Runbook-Utdatatyp](../media/modules/runbook-graphical-module-output-type.png)

  Det här beteendet liknar funktionen "Type Ahead" för cmdletens utdata i PowerShell integration service-miljön, utan att behöva köra den.

  ![Skärm bild av POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet-tillstånd

Gör alla cmdlets i modulen tillstånds lösa. Flera Runbook-jobb kan samtidigt köras i samma `AppDomain` process och begränsat läge. Om det finns ett tillstånd som delas på dessa nivåer kan jobben påverka varandra. Det här beteendet kan leda till tillfälliga och svåra problem. Här är ett exempel på vad du inte ska göra:

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

### <a name="module-dependency"></a>Modul-beroende

Se till att modulen är helt i ett paket som kan kopieras med hjälp av XCOPY. Automation-moduler distribueras till Automation-sandbox när Runbooks körs. Modulerna måste fungera oberoende av den värd som kör dem.

Du bör kunna lägga upp och flytta ett modul-paket och få det att fungera som vanligt när det importeras till en annan värds PowerShell-miljö. För att detta ska ske kontrollerar du att modulen inte är beroende av filer utanför modulen modul som är zippande när modulen importeras till Automation.

Modulen bör inte vara beroende av unika register inställningar på en värd. Exempel är de inställningar som görs när en produkt installeras.

### <a name="module-file-paths"></a>Fil Sök vägar för modul

Se till att alla filer i modulen har sökvägar med färre än 140 tecken. Alla sökvägar som är över 140 tecken orsakar problem med att importera runbooks. Automation kan inte importera en fil med Sök vägs storlek över 140 tecken till PowerShell-sessionen med `Import-Module` .

## <a name="import-modules"></a>Importera moduler

I det här avsnittet definieras flera sätt att importera en modul till ditt Automation-konto.

### <a name="import-modules-in-the-azure-portal"></a>Importera moduler i Azure Portal

Så här importerar du en modul i Azure Portal:

1. Gå till ditt Automation-konto.
2. Under **delade resurser** väljer du **moduler**.
3. Välj **Lägg till en modul**.
4. Välj den **. zip** -fil som innehåller modulen.
5. Välj **OK** för att börja importera processen.

### <a name="import-modules-by-using-powershell"></a>Importera moduler med hjälp av PowerShell

Du kan använda cmdleten [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) för att importera en modul till ditt Automation-konto. Cmdleten tar en URL för en module. zip-paket.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Du kan också använda samma cmdlet för att importera en modul från PowerShell-galleriet direkt. Kom ihåg att hämta `ModuleName` och `ModuleVersion` från [PowerShell-galleriet](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Importera moduler från PowerShell-galleriet

Du kan importera [PowerShell-galleriet](https://www.powershellgallery.com) moduler direkt från galleriet eller från ditt Automation-konto.

Så här importerar du en modul direkt från PowerShell-galleriet:

1. Gå till https://www.powershellgallery.com och Sök efter den modul som ska importeras.
2. Under **installations alternativ** på fliken **Azure Automation** väljer du **distribuera till Azure Automation**. Den här åtgärden öppnar Azure Portal. 
3. På sidan Importera väljer du ditt Automation-konto och väljer **OK**.

![Skärm bild av modulen för PowerShell-galleriet import](../media/modules/powershell-gallery.png)

Så här importerar du en PowerShell-galleriet-modul direkt från ditt Automation-konto:

1. Under **delade resurser** väljer du **moduler**. 
2. Välj **Browse Gallery** och Sök sedan i galleriet efter en modul. 
3. Välj den modul som ska importeras och välj **Importera**. 
4. Välj **OK** för att starta import processen.

![Skärm bild av import av en PowerShell-galleriet-modul från Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Ta bort moduler

Om du har problem med en modul eller om du behöver återställa till en tidigare version av en modul kan du ta bort den från ditt Automation-konto. Du kan inte ta bort de ursprungliga versionerna av [standardmodulerna](#default-modules) som importeras när du skapar ett Automation-konto. Om modulen som ska tas bort är en nyare version av en av [standardmodulerna](#default-modules)återställs den till den version som installerades med ditt Automation-konto. Annars tas alla moduler som du tar bort från Automation-kontot bort.

### <a name="delete-modules-in-the-azure-portal"></a>Ta bort moduler i Azure Portal

Så här tar du bort en modul i Azure Portal:

1. Gå till ditt Automation-konto. Under **delade resurser** väljer du **moduler**.
2. Välj den modul som du vill ta bort.
3. På sidan modul väljer du **ta bort**. Om den här modulen är en av [standardmodulerna](#default-modules)återställs den till den version som fanns när Automation-kontot skapades.

### <a name="delete-modules-by-using-powershell"></a>Ta bort moduler med PowerShell

Om du vill ta bort en modul via PowerShell kör du följande kommando:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder Azure PowerShell moduler finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps).

* Mer information om hur du skapar PowerShell-moduler finns i [skriva en Windows PowerShell-modul](/powershell/scripting/developer/module/writing-a-windows-powershell-module).
