---
title: Hantera rollbehörigheter och säkerhet i Azure Automation
description: I den här artikeln beskrivs hur du använder rollbaserad åtkomstkontroll i Azure (Azure RBAC), som möjliggör åtkomsthantering för Azure-resurser.
keywords: automation rbac, rollbaserad åtkomstkontroll, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 07/21/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0727d3342c73d9aa4d15e84aacb82bd8fea01d65
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833588"
---
# <a name="manage-role-permissions-and-security"></a>Hantera rollbehörigheter och säkerhet

Rollbaserad åtkomstkontroll i Azure (Azure RBAC) möjliggör åtkomsthantering för Azure-resurser. Med [Azure RBAC](../role-based-access-control/overview.md)kan du åtseger uppgifter i ditt team och endast bevilja den mängd åtkomst till användare, grupper och program som de behöver för att utföra sitt arbete. Du kan bevilja rollbaserad åtkomst till användare med hjälp av Azure Portal, Azure Command-Line-verktyg eller Azure Management-API:er.

## <a name="roles-in-automation-accounts"></a>Roller i Automation-konton

I Azure Automation beviljas åtkomst genom att tilldela lämplig Azure-roll till användare, grupper och program i Automation-kontoomfånget. Följande är de inbyggda roller som stöds av ett Automation-konto:

| **Role** | **Beskrivning** |
|:--- |:--- |
| Ägare |Rollen Ägare ger åtkomst till alla resurser och åtgärder i ett Automation-konto, inklusive att ge åtkomst till andra användare, grupper och program för att hantera Automation-kontot. |
| Deltagare |Med deltagarrollen kan du hantera allt, men du kan inte ändra andra användares åtkomstbehörighet till ett Automation-konto. |
| Läsare |Med läsarrollen kan du visa alla resurser i ett Automation-konto men inte göra några ändringar. |
| Automation-operatör |Med rollen Automation-operatör kan du visa runbooknamn och egenskaper samt skapa och hantera jobb för alla runbooks i ett Automation-konto. Den här rollen är användbar om du vill skydda dina Automation-kontoresurser som autentiseringstillgångar och runbooks från att visas eller ändras, men ändå tillåta medlemmar i din organisation att köra dessa runbooks. |
|Automation-jobboperator|Med rollen Automation-jobboperatör kan du skapa och hantera jobb för alla runbooks i ett Automation-konto.|
|Automation Runbook-operatör|Med rollen Automation Runbook Operator kan du visa en runbooks namn och egenskaper.|
| Log Analytics Contributor | Med Log Analytics-deltagarrollen kan du läsa alla övervakningsdata och redigera övervakningsinställningar. Redigering av övervakningsinställningar omfattar att lägga till VM-tillägget till virtuella datorer, läsa lagringskontonycklar för att kunna konfigurera insamling av loggar från Azure Storage, skapa och konfigurera Automation-konton, lägga till Azure Automation-funktioner och konfigurera Azure Diagnostics på alla Azure-resurser.|
| Log Analytics Reader | Med Log Analytics-läsarrollen kan du visa och söka efter alla övervakningsdata samt visa övervakningsinställningar. Detta omfattar att visa konfigurationen av Azure Diagnostics för alla Azure-resurser. |
| Övervakningsdeltagare | Med rollen Övervakningsdeltagare kan du läsa alla övervakningsdata och uppdatera övervakningsinställningarna.|
| Övervakningsläsare | Med rollen Övervakningsläsare kan du läsa alla övervakningsdata. |
| Administratör för användaråtkomst |Med rollen Administratör för användaråtkomst kan du hantera användaråtkomsten till Azure Automation-konton. |

## <a name="role-permissions"></a>Rollbehörigheter

I följande tabeller beskrivs de specifika behörigheter som ges till varje roll. Detta kan omfatta Åtgärder, som ger behörigheter och NotActions, som begränsar dem.

### <a name="owner"></a>Ägare

En ägare kan hantera allt, inklusive åtkomst. I följande tabell visas de behörigheter som beviljats för rollen:

|Åtgärder|Beskrivning|
|---|---|
|Microsoft.Automation/automationAccounts/|Skapa och hantera alla typer av resurser.|

### <a name="contributor"></a>Deltagare

En deltagare kan hantera allt utom åtkomst. I följande tabell visas de behörigheter som beviljats och nekats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Skapa och hantera resurser av alla typer|
|**Inte åtgärder**||
|Microsoft.Authorization/*/Ta bort| Ta bort roller och rolltilldelningar.       |
|Microsoft.Authorization/*/Write     |  Skapa roller och rolltilldelningar.       |
|Microsoft.Authorization/elevateAccess/Action    | Nekar möjligheten att skapa en administratör för användaråtkomst.       |

### <a name="reader"></a>Läsare

En läsare kan visa alla resurser i ett Automation-konto, men kan inte göra några ändringar.

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Visa alla resurser i ett Automation-konto. |

### <a name="automation-operator"></a>Automation-operatör

En Automation-operatör kan skapa och hantera jobb och läsa runbook-namn och egenskaper för alla runbooks i ett Automation-konto.

>[!NOTE]
>Om du vill styra operatörsåtkomsten till enskilda runbooks ska du inte ange den här rollen. Använd i stället **rollerna Automation Job Operator** **och Automation Runbook Operator** i kombination.

I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Authorization/*/read|Läsauktorisering.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Läs Hybrid Runbook Worker resurser.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista jobb för runbooken.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Återuppta ett jobb som har pausats.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Avbryt ett pågående jobb.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Läs jobbströmmar och utdata.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Hämta utdata för ett jobb.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausa ett pågående jobb.|
|Microsoft.Automation/automationAccounts/jobs/write|Skapa jobb.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Hämta ett Azure Automation jobbschema.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Skapa ett Azure Automation jobbschema.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Hämta arbetsytan som är länkad till Automation-kontot.|
|Microsoft.Automation/automationAccounts/read|Hämta ett Azure Automation konto.|
|Microsoft.Automation/automationAccounts/runbooks/read|Hämta en Azure Automation runbook.|
|Microsoft.Automation/automationAccounts/schedules/read|Hämta en Azure Automation schematillgång.|
|Microsoft.Automation/automationAccounts/schedules/write|Skapa eller uppdatera en Azure Automation schematillgång.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Läsa roller och rolltilldelningar.         |
|Microsoft.Resources/deployments/*      |Skapa och hantera distributioner av resursgrupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera aviseringsregler.        |
|Microsoft.Support/* |Skapa och hantera supportärenden.|

### <a name="automation-job-operator"></a>Automation-jobboperator

En Automation-jobboperatörsroll beviljas i Automation-kontoomfånget.Detta gör att operatörsbehörigheterna kan skapa och hantera jobb för alla runbooks i kontot. Om rollen Jobboperatör beviljas läsbehörighet för resursgruppen som innehåller Automation-kontot kan medlemmar i rollen starta runbooks. De kan dock inte skapa, redigera eller ta bort dem.

I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Authorization/*/read|Läsauktorisering.|
|Microsoft.Automation/automationAccounts/jobs/read|Lista jobb för runbooken.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Återuppta ett jobb som har pausats.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Avbryt ett pågående jobb.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Läs jobbströmmar och utdata.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausa ett pågående jobb.|
|Microsoft.Automation/automationAccounts/jobs/write|Skapa jobb.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Läsa roller och rolltilldelningar.       |
|Microsoft.Resources/deployments/*      |Skapa och hantera distributioner av resursgrupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera aviseringsregler.        |
|Microsoft.Support/* |Skapa och hantera supportärenden.|

### <a name="automation-runbook-operator"></a>Automation Runbook-operatör

En Automation Runbook Operator-roll beviljas i Runbook-omfånget. En Automation Runbook-operatör kan visa runbookens namn och egenskaper.Den här rollen i kombination **med rollen Automation-jobboperatör** gör det möjligt för operatören att även skapa och hantera jobb för runbooken. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Visa en lista över runbooks.        |
|Microsoft.Authorization/*/read      | Läsauktorisering.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Läsa roller och rolltilldelningar.         |
|Microsoft.Resources/deployments/*      | Skapa och hantera distributioner av resursgrupper.         |
|Microsoft.Insights/alertRules/*      | Skapa och hantera aviseringsregler.        |
|Microsoft.Support/*      | Skapa och hantera supportärenden.        |

### <a name="log-analytics-contributor"></a>Log Analytics Contributor

En Log Analytics-deltagare kan läsa alla övervakningsdata och redigera övervakningsinställningar. Redigering av övervakningsinställningar omfattar att lägga till VM-tillägget till virtuella datorer. läsa lagringskontonycklar för att kunna konfigurera insamling av loggar från Azure Storage; skapa och konfigurera Automation-konton; lägga till funktioner; och konfigurera Azure Diagnostics för alla Azure-resurser. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/läsa|Läs resurser av alla typer, utom hemligheter.|
|Microsoft.Automation/automationAccounts/*|Hantera Automation-konton.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Skapa och hantera tillägg för virtuella datorer.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Lista klassiska lagringskontonycklar.|
|Microsoft.Compute/virtualMachines/extensions/*|Skapa och hantera klassiska tillägg för virtuella datorer.|
|Microsoft.Insights/alertRules/*|Läsa/skriva/ta bort aviseringsregler.|
|Microsoft.Insights/diagnosticSettings/*|Läsa/skriva/ta bort diagnostikinställningar.|
|Microsoft.OperationalInsights/*|Hantera Azure Monitor loggar.|
|Microsoft.OperationsManagement/*|Hantera Azure Automation på arbetsytor.|
|Microsoft.Resources/deployments/*|Skapa och hantera distributioner av resursgrupper.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Skapa och hantera distributioner av resursgrupper.|
|Microsoft.Storage/storageAccounts/listKeys/action|Lista lagringskontonycklar.|
|Microsoft.Support/*|Skapa och hantera supportärenden.|

### <a name="log-analytics-reader"></a>Log Analytics Reader

En Log Analytics-läsare kan visa och söka efter alla övervakningsdata samt visa övervakningsinställningar, inklusive att visa konfigurationen av Azure Diagnostics på alla Azure-resurser. I följande tabell visas de behörigheter som beviljas eller nekas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/läsa|Läs resurser av alla typer, utom hemligheter.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Hantera frågor i Azure Monitor loggar.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök Azure Monitor loggdata.|
|Microsoft.Support/*|Skapa och hantera supportärenden.|
|**Inte åtgärder**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Det går inte att läsa delade åtkomstnycklar.|

### <a name="monitoring-contributor"></a>Övervakningsdeltagare

En övervakningsdeltagare kan läsa alla övervakningsdata och inställningar för uppdateringsövervakning. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/läsa|Läs resurser av alla typer, utom hemligheter.|
|Microsoft.AlertsManagement/alerts/*|Hantera aviseringar.|
|Microsoft.AlertsManagement/alertsSummary/*|Hantera aviseringsinstrumentpanelen.|
|Microsoft.Insights/AlertRules/*|Hantera aviseringsregler.|
|Microsoft.Insights/components/*|Hantera Application Insights komponenter.|
|Microsoft.Insights/DiagnosticSettings/*|Hantera diagnostikinställningar.|
|Microsoft.Insights/eventtypes/*|Lista aktivitetslogghändelser (hanteringshändelser) i en prenumeration. Den här behörigheten gäller för både program- och portalåtkomst till aktivitetsloggen.|
|Microsoft.Insights/LogDefinitions/*|Den här behörigheten är nödvändig för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggkategorier i aktivitetsloggen.|
|Microsoft.Insights/MetricDefinitions/*|Läsa måttdefinitioner (lista över tillgängliga måtttyper för en resurs).|
|Microsoft.Insights/Metrics/*|Läsa mått för en resurs.|
|Microsoft.Insights/Register/Action|Registrera Providern Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Hantera Application Insights webbtester.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Hantera Azure Monitor loggar lösningspaket.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Hantera Azure Monitor loggar sparade sökningar.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök efter Log Analytics-arbetsytor.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Lista nycklar för en Log Analytics-arbetsyta.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Hantera Azure Monitor loggar lagringsinformationskonfigurationer.|
|Microsoft.Support/*|Skapa och hantera supportärenden.|
|Microsoft.WorkloadMonitor/workloads/*|Hantera arbetsbelastningar.|

### <a name="monitoring-reader"></a>Övervakningsläsare

En övervakningsläsare kan läsa alla övervakningsdata. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/läsa|Läs resurser av alla typer, utom hemligheter.|
|Microsoft.OperationalInsights/workspaces/search/action|Sök i Log Analytics-arbetsytor.|
|Microsoft.Support/*|Skapa och hantera supportärenden|

### <a name="user-access-administrator"></a>Administratör för användaråtkomst

En administratör för användaråtkomst kan hantera användaråtkomst till Azure-resurser. I följande tabell visas de behörigheter som beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/läsa|Läsa alla resurser|
|Microsoft.Authorization/*|Hantera auktorisering|
|Microsoft.Support/*|Skapa och hantera supportärenden|

## <a name="feature-setup-permissions"></a>Behörigheter för funktionskonfiguration

I följande avsnitt beskrivs de minsta behörigheter som krävs för att aktivera Uppdateringshantering och Ändringsspårning och inventering funktioner.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>Behörigheter för att aktivera Uppdateringshantering och Ändringsspårning och inventering från en virtuell dator

|**Åtgärd**  |**Behörighet**  |**Minsta omfång**  |
|---------|---------|---------|
|Skriva ny distribution      | Microsoft.Resources/deployments/*          |Prenumeration          |
|Skriva ny resursgrupp      | Microsoft.Resources/subscriptions/resourceGroups/write        | Prenumeration          |
|Skapa en ny standardarbetsyta      | Microsoft.OperationalInsights/workspaces/write         | Resursgrupp         |
|Skapa nytt konto      |  Microsoft.Automation/automationAccounts/write        |Resursgrupp         |
|Länka arbetsyta och konto      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Arbetsyta</br>Automation-konto
|Skapa MMA-tillägg      | Microsoft.Compute/virtualMachines/write         | Virtuell dator         |
|Skapa sparad sökning      | Microsoft.OperationalInsights/workspaces/write          | Arbetsyta         |
|Skapa omfångskonfiguration      | Microsoft.OperationalInsights/workspaces/write          | Arbetsyta         |
|Tillståndskontroll för registrering – Läs arbetsyta      | Microsoft.OperationalInsights/workspaces/read         | Arbetsyta         |
|Tillståndskontroll för registrering – Läs egenskapen för länkad arbetsyta för kontot     | Microsoft.Automation/automationAccounts/read      | Automation-konto        |
|Tillståndskontroll för registrering – läslösning      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Lösning         |
|Tillståndskontroll för registrering – Läsa virtuell dator      | Microsoft.Compute/virtualMachines/read         | Virtuell dator         |
|Tillståndskontroll för registrering – läskonto      | Microsoft.Automation/automationAccounts/read  |  Automation-konto   |
| Arbetsytekontroll för registrering för VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Prenumeration         |
| Registrera Log Analytics-providern |Microsoft.Insights/register/action | Prenumeration|

<sup>1</sup> Den här behörigheten krävs för att aktivera funktioner via VM-portalen.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Behörigheter för att aktivera Uppdateringshantering och Ändringsspårning och inventering från ett Automation-konto

|**Åtgärd**  |**Behörighet** |**Minsta omfång**  |
|---------|---------|---------|
|Skapa ny distribution     | Microsoft.Resources/deployments/*        | Prenumeration         |
|Skapa ny resursgrupp     | Microsoft.Resources/subscriptions/resourceGroups/write         | Prenumeration        |
|AutomationBoarding-bladet – Skapa ny arbetsyta     |Microsoft.OperationalInsights/workspaces/write           | Resursgrupp        |
|AutomationPåboarding-bladet – läs länkad arbetsyta     | Microsoft.Automation/automationAccounts/read        | Automation-konto       |
|AutomationPåboarding-bladet – läslösning     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Lösning        |
|AutomationPåboarding-bladet – läsarbetsyta     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Arbetsyta        |
|Skapa länk för arbetsyta och konto     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
|Skriva konto för shoebox      | Microsoft.Automation/automationAccounts/write        | Konto        |
|Skapa/redigera sparad sökning     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
|Skapa/redigera omfångskonfiguration     | Microsoft.OperationalInsights/workspaces/write        | Arbetsyta        |
| Registrera Log Analytics-providern |Microsoft.Insights/register/action | Prenumeration|
|**Steg 2 – Aktivera flera virtuella datorer**     |         |         |
|VMOnboarding-blad – Skapa MMA-tillägg     | Microsoft.Compute/virtualMachines/write           | Virtuell dator        |
|Skapa/redigera sparad sökning     | Microsoft.OperationalInsights/workspaces/write           | Arbetsyta        |
|Skapa/redigera omfångskonfiguration  | Microsoft.OperationalInsights/workspaces/write   | Arbetsyta|

## <a name="update-management-permissions"></a>Behörigheter för uppdateringshantering

Uppdateringshanteringen når flera tjänster för att tillhandahålla tjänsten. I följande tabell visas de behörigheter som krävs för att hantera distributioner av uppdateringshantering:

|**Resurs**  |**Role**  |**Omfång**  |
|---------|---------|---------|
|Automation-konto     | Log Analytics Contributor       | Automation-konto        |
|Automation-konto    | Virtuell datordeltagare        | Resursgrupp för kontot        |
|Log Analytics-arbetsyta     | Log Analytics Contributor| Log Analytics-arbetsyta        |
|Log Analytics-arbetsyta |Log Analytics Reader| Prenumeration|
|Lösning     |Log Analytics Contributor         | Lösning|
|Virtuell dator     | Virtuell datordeltagare        | Virtuell dator        |

## <a name="configure-azure-rbac-for-your-automation-account"></a>Konfigurera Azure RBAC för ditt Automation-konto

Följande avsnitt visar hur du konfigurerar Azure RBAC på ditt Automation-konto via [Azure Portal](#configure-azure-rbac-using-the-azure-portal) och [PowerShell](#configure-azure-rbac-using-powershell).

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Konfigurera Azure RBAC med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com/) och öppna Automation-kontot från sidan Automation-konton.
2. Klicka på **Åtkomstkontroll (IAM)** för att öppna sidan Åtkomstkontroll (IAM). Du kan använda den här sidan för att lägga till nya användare, grupper och program för att hantera ditt Automation-konto och visa befintliga roller som kan konfigureras för Automation-kontot.
3. Klicka på fliken **Rolltilldelningar**.

   ![Knappen Åtkomst](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Lägga till en ny användare och tilldela en roll

1. På sidan Åtkomstkontroll (IAM) klickar du på **+ Lägg till rolltilldelning**. Den här åtgärden öppnar sidan Lägg till rolltilldelning där du kan lägga till en användare, grupp eller ett program och tilldela en motsvarande roll.

2. Välj en roll i listan över tillgängliga roller. Du kan välja någon av de tillgängliga inbyggda roller som ett Automation-konto stöder eller en anpassad roll som du har definierat.

3. Skriv namnet på den användare som du vill ge behörighet till i **fältet** Välj. Välj användaren i listan och klicka på **Spara.**

   ![Lägga till användare](media/automation-role-based-access-control/automation-04-add-users.png)

   Nu bör du se att användaren har lagts till på sidan Användare med den valda rollen tilldelad.

   ![Visa användare](media/automation-role-based-access-control/automation-05-list-users.png)

   Du kan också tilldela en roll till användaren från sidan Roller.

4. Klicka **på** Roller på sidan Åtkomstkontroll (IAM) för att öppna sidan Roller. Du kan visa namnet på rollen och antalet användare och grupper som har tilldelats rollen.

    ![Tilldela en roll från sidan Användare](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Du kan bara ange rollbaserad åtkomstkontroll i Automation-kontoomfånget och inte på någon resurs under Automation-kontot.

#### <a name="remove-a-user"></a>Ta bort en användare

Du kan ta bort åtkomstbehörigheten för en användare som inte hanterar Automation-kontot eller som inte längre arbetar för organisationen. Nedan följer stegen för att ta bort en användare:

1. På sidan Åtkomstkontroll (IAM) väljer du den användare som du vill ta bort och klickar på Ta **bort.**
2. Klicka på knappen **Ta bort** på sidan med tilldelningsinformation.
3. Bekräfta borttagningen genom att klicka på **Ja**.

   ![Ta bort användare](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>Konfigurera Azure RBAC med PowerShell

Du kan också konfigurera rollbaserad åtkomst till ett Automation-konto med hjälp av följande [Azure PowerShell cmdlets:](../role-based-access-control/role-assignments-powershell.md)

[Get-AzRoleDefinition visar](/powershell/module/Az.Resources/Get-AzRoleDefinition) en lista över alla Azure-roller som är tillgängliga i Azure Active Directory. Du kan använda denna cmdlet med `Name` parametern för att lista alla åtgärder som en viss roll kan utföra.

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

Följande är exempel på utdata:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzRoleAssignment listar](/powershell/module/az.resources/get-azroleassignment) Azure-rolltilldelningar i det angivna omfånget. Utan några parametrar returnerar denna cmdlet alla rolltilldelningar som gjorts under prenumerationen. Använd `ExpandPrincipalGroups` parametern för att lista åtkomsttilldelningar för den angivna användaren samt de grupper som användaren tillhör.

**Exempel:** Använd följande cmdlet för att visa en lista över alla användare och deras roller i ett Automation-konto.

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Följande är exempel på utdata:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

Använd [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) för att tilldela åtkomst till användare, grupper och program till ett visst omfång.

**Exempel:** Använd följande kommando för att tilldela rollen "Automation-operatör" för en användare i Automation-kontoomfånget.

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Följande är exempel på utdata:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Använd [Remove-AzRoleAssignment för](/powershell/module/Az.Resources/Remove-AzRoleAssignment) att ta bort åtkomst för en angiven användare, grupp eller program från ett visst omfång.

**Exempel:** Använd följande kommando för att ta bort användaren från Automation-operatörsrollen i Automation-kontoomfånget.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

I föregående exempel ersätter du `sign-in ID of a user you wish to remove` , , och med din `SubscriptionID` `Resource Group Name` `Automation account name` kontoinformation. Välj **Ja när** du uppmanas att bekräfta innan du fortsätter att ta bort tilldelningar av användarroller.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Användarupplevelse för Automation-operatörsrollen – Automation-konto

När en användare som har tilldelats rollen Automation-operatör i Automation-kontoomfånget visar automationskontot som han/hon är tilldelad, kan användaren bara visa listan över runbooks, runbook-jobb och scheman som skapats i Automation-kontot. Den här användaren kan inte visa definitionerna för dessa objekt. Användaren kan starta, stoppa, pausa, återuppta eller schemalägga runbook-jobbet. Användaren har dock inte åtkomst till andra Automation-resurser, till exempel konfigurationer, Hybrid Worker-grupper eller DSC-noder.

![Ingen åtkomst till resurser](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Konfigurera Azure RBAC för runbooks

Azure Automation kan du tilldela Azure-roller till specifika runbooks. Det gör du genom att köra följande skript för att lägga till en användare i en specifik runbook. En Automation-kontoadministratör eller en innehavaradministratör kan köra det här skriptet.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

När skriptet har körts kan du be användaren logga in på Azure Portal och välja **Alla resurser.** I listan kan användaren se runbooken som han/hon har lagts till för som Automation Runbook-operatör.

![Runbook Azure RBAC i portalen](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Användarupplevelse för Automation-operatörsroll – Runbook

När en användare som tilldelats rollen Automation-operatör i Runbook-omfånget visar en tilldelad runbook kan användaren bara starta runbooken och visa runbook-jobben.

![Har bara åtkomst till start](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure RBAC med Hjälp av PowerShell finns i Lägga till eller ta bort [Azure-rolltilldelningar med hjälp av Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Mer information om typerna av runbooks finns i Azure Automation [runbook-typer.](automation-runbook-types.md)
* Information om hur du startar en runbook finns [i Starta en runbook i Azure Automation](start-runbooks.md).
