---
title: Använda källkontrollsintegrering i Azure Automation
description: Den här artikeln beskriver hur Azure Automation synkroniserar källkontrollen med andra lagringsplatsen.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d94da9792d40a389e3981163e565d85d82a9cdc9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831248"
---
# <a name="use-source-control-integration"></a>Använda källkontrollsintegrering

 Källkontrollsintegrering i Azure Automation enkelriktad synkronisering från lagringsplatsen för källkontroll. Med källkontroll kan du hålla dina runbooks i ditt Automation-konto uppdaterade med skript i din GitHub- eller Azure Repos-lagringsplats för källkontroll. Den här funktionen gör det enkelt att höja upp kod som har testats i utvecklingsmiljön till ditt Automation-produktionskonto.

 Med källkontrollsintegrering kan du enkelt samarbeta med ditt team, spåra ändringar och återställa till tidigare versioner av dina runbooks. Med källkontroll kan du till exempel synkronisera olika grenar i källkontrollen med dina Automation-konton för utveckling, testning och produktion.

## <a name="source-control-types"></a>Typer av källkontroll

Azure Automation har stöd för tre typer av källkontroll:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Förutsättningar

* En lagringsplats för källkontroll (GitHub eller Azure Repos)
* Ett [Kör som-konto](automation-security-overview.md#run-as-accounts)
* Modulen [ `AzureRM.Profile` måste](/powershell/module/azurerm.profile/) importeras till ditt Automation-konto. Observera att motsvarande Az-modul ( `Az.Accounts` ) inte fungerar med Automation-källkontroll.

> [!NOTE]
> Synkroniseringsjobb för källkontroll körs under användarens Automation-konto och debiteras med samma pris som andra Automation-jobb.

## <a name="configure-source-control"></a>Konfigurera källkontroll

Det här avsnittet beskriver hur du konfigurerar källkontroll för ditt Automation-konto. Du kan använda antingen Azure Portal eller PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Konfigurera källkontroll i Azure Portal

Använd den här proceduren för att konfigurera källkontroll med hjälp av Azure Portal.

1. I ditt Automation-konto väljer du **Källkontroll och klickar** på **Lägg till.**

    ![Välj källkontroll](./media/source-control-integration/select-source-control.png)

2. Välj **Typ av källkontroll** och klicka sedan på **Autentisera.**

3. Ett webbläsarfönster öppnas och du uppmanas att logga in. Slutför autentiseringen genom att följa anvisningarna.

4. På sidan Sammanfattning av källkontroll använder du fälten för att fylla i de egenskaper för källkontroll som definieras nedan. Klicka **på Spara** när du är klar.

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Källkontrollnamn     | Ett eget namn för källkontrollen. Det här namnet får bara innehålla bokstäver och siffror.        |
    |Typ av källkontroll     | Typ av källkontrollmekanism. De tillgängliga alternativen är:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Lagringsplats     | Namnet på lagringsplatsen eller projektet. De första 200 lagringsplatsen hämtas. Om du vill söka efter en lagringsplats skriver du namnet i fältet och klickar **på Sök på GitHub.**|
    |Gren     | Gren som källfilerna ska hämtas från. Branch targeting är inte tillgängligt för TFVC-källkontrolltypen.          |
    |Mappsökväg     | Mapp som innehåller runbooks som ska synkroniseras, till exempel **/Runbooks**. Endast runbooks i den angivna mappen synkroniseras. Rekursion stöds inte.        |
    |Automatisk synkronisering<sup>1</sup>     | Inställning som aktiverar eller inaktiverar automatisk synkronisering när ett genomförande görs på lagringsplatsen för källkontroll.        |
    |Publicera runbook     | Inställningen På om runbooks publiceras automatiskt efter synkronisering från källkontrollen och Av i annat fall.           |
    |Description     | Text som anger ytterligare information om källkontrollen.        |

    <sup>1</sup> Om du vill aktivera automatisk synkronisering när du konfigurerar källkontrollintegrering med Azure Repos måste du vara projektadministratör.

   ![Sammanfattning av källkontroll](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Inloggningen för lagringsplatsen för källkontroll kan vara annorlunda än din inloggning för Azure Portal. Se till att du är inloggad med rätt konto för lagringsplatsen för källkontroll när du konfigurerar källkontroll. Om du är tveksam öppnar du en ny flik i webbläsaren, loggar ut från **dev.azure.com**, **visualstudio.com** eller **github.com** och försöker ansluta till källkontrollen igen.

### <a name="configure-source-control-in-powershell"></a>Konfigurera källkontroll i PowerShell

Du kan också använda PowerShell för att konfigurera källkontroll i Azure Automation. Om du vill använda PowerShell-cmdletarna för den här åtgärden behöver du en personlig åtkomsttoken (PAT). Använd [cmdleten New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol) för att skapa källkontrollanslutningen. Denna cmdlet kräver en säker sträng för PAT. Information om hur du skapar en säker sträng finns i [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring).

Följande underavsnitt illustrerar hur PowerShell skapar källkontrollanslutningen för GitHub, Azure Repos (Git) och Azure Repos (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Skapa källkontrollanslutning för GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Skapa källkontrollanslutning för Azure Repos (Git)

> [!NOTE]
> Azure Repos (Git) använder en URL som dev.azure.com **i** stället **för visualstudio.com**, som används i tidigare format. Det äldre `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` URL-formatet är inaktuellt men stöds fortfarande. Det nya formatet är att föredra.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Skapa källkontrollanslutning för Azure Repos (TFVC)

> [!NOTE]
> Azure Repos (TFVC) använder en URL som **dev.azure.com** i stället **för visualstudio.com**, som används i tidigare format. Det äldre `https://<accountname>.visualstudio.com/<projectname>/_versionControl` URL-formatet är inaktuellt men stöds fortfarande. Det nya formatet är att föredra.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Behörigheter för personlig åtkomsttoken (PAT)

Källkontroll kräver vissa minimibehörigheter för PAT:er. Följande underavsnitt innehåller de minsta behörigheter som krävs för GitHub- och Azure-lagringsplatsen.

##### <a name="minimum-pat-permissions-for-github"></a>Minsta PAT-behörigheter för GitHub

I följande tabell definieras de minsta PAT-behörigheter som krävs för GitHub. Mer information om hur du skapar en PAT i GitHub finns [i Skapa en personlig åtkomsttoken för kommandoraden](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Omfång  |Beskrivning  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Status för åtkomst commit         |
|`repo_deployment`      | Åtkomstdistributionsstatus         |
|`public_repo`     | Få åtkomst till offentliga lagringsplatsen         |
|`repo:invite` | Få åtkomst till databasinbjudningar |
|`security_events` | Läsa och skriva säkerhetshändelser |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Skriv lagringsplats hookar         |
|`read:repo_hook`|Läsa lagringsplatsens hookar|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minsta PAT-behörigheter för Azure-lagringsplatsen

I följande lista definieras de minsta PAT-behörigheterna som krävs för Azure Repos. Mer information om hur du skapar en PAT i Azure Repos finns i Authenticate access with personal access tokens (Autentisera [åtkomst med personliga åtkomsttoken).](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)

| Omfång  |  Åtkomsttyp  |
|---------| ----------|
| `Code`      | Läsa  |
| `Project and team` | Läsa |
| `Identity` | Läsa     |
| `User profile` | Läsa     |
| `Work items` | Läsa    |
| `Service connections` | Läsa, fråga, hantera<sup>1</sup>    |

<sup>1</sup> `Service connections` Behörigheten krävs endast om du har aktiverat autosynkronisering.

## <a name="synchronize-with-source-control"></a>Synkronisera med källkontroll

Följ dessa steg om du vill synkronisera med källkontrollen.

1. Välj källa från tabellen på sidan Källkontroll.

2. Klicka **på Starta synkronisering** för att starta synkroniseringsprocessen.

3. Visa status för det aktuella synkroniseringsjobbet eller tidigare genom att klicka på **fliken Synkronisera** jobb.

4. I **listrutan Källkodskontroll** väljer du en källkontrollmekanism.

    ![Synkroniseringsstatus](./media/source-control-integration/sync-status.png)

5. Om du klickar på ett jobb kan du visa jobbutdata. Följande exempel är utdata från ett synkroniseringsjobb för källkontroll.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. Ytterligare loggning är tillgängligt genom att **välja Alla loggar** på sidan Sammanfattning av synkroniseringsjobb för källkontroll. Dessa ytterligare loggposter kan hjälpa dig att felsöka problem som kan uppstå när du använder källkontroll.

## <a name="disconnect-source-control"></a>Koppla från källkontroll

Så här kopplar du från en lagringsplats för källkontroll:

1. Kontroll **med öppen källkod** under **Kontoinställningar** i ditt Automation-konto.

2. Välj den källkontrollmekanism som ska tas bort.

3. På sidan Sammanfattning av källkontroll klickar du på Ta **bort.**

## <a name="handle-encoding-issues"></a>Hantera kodningsproblem

Om flera personer redigerar runbooks på lagringsplatsen för källkontroll med hjälp av olika redigerare kan kodningsproblem uppstå. Mer information om den här situationen finns i [Vanliga orsaker till kodningsproblem.](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="update-the-pat"></a>Uppdatera PAT

För närvarande kan du inte använda Azure Portal för att uppdatera PAT i källkontrollen. När din PAT har upphört att gälla eller återkallats kan du uppdatera källkontrollen med en ny åtkomsttoken på något av följande sätt:

* Använd [REST API](/rest/api/automation/sourcecontrol/update).
* Använd [cmdleten Update-AzAutomationSourceControl.](/powershell/module/az.automation/update-azautomationsourcecontrol)

## <a name="next-steps"></a>Nästa steg

* Information om hur du integrerar källkontroll Azure Automation finns [i Azure Automation: Källkontrollintegrering i Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Information om hur du integrerar runbook-källkontroll med Visual Studio Codespaces finns [i Azure Automation: Integrera Runbook Source Control med hjälp av Visual Studio Codespaces](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/).
