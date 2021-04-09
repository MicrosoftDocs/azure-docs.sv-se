---
title: Hantera roll behörigheter och säkerhet i Azure Automation
description: Den här artikeln beskriver hur du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC), vilket möjliggör åtkomst hantering för Azure-resurser.
keywords: automation rbac, rollbaserad åtkomstkontroll, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 07/21/2020
ms.topic: conceptual
ms.openlocfilehash: 320668f9596376cf7aa12ed97872671404a07658
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98895925"
---
# <a name="manage-role-permissions-and-security"></a>Hantera rollbehörigheter och säkerhet

Rollbaserad åtkomst kontroll i Azure (Azure RBAC) möjliggör åtkomst hantering för Azure-resurser. Med hjälp av [Azure RBAC](../role-based-access-control/overview.md)kan du åtskilja uppgifter i ditt team och endast bevilja den mängd åtkomst till användare, grupper och program som de behöver för att utföra sina jobb. Du kan bevilja rollbaserad åtkomst till användare med hjälp av Azure Portal, Azure Command-Line-verktyg eller Azure Management-API: er.

## <a name="roles-in-automation-accounts"></a>Roller i Automation-konton

I Azure Automation beviljas åtkomst genom att tilldela lämplig Azure-roll till användare, grupper och program i Automation-kontots omfattning. Följande är de inbyggda roller som stöds av ett Automation-konto:

| **Role** | **Beskrivning** |
|:--- |:--- |
| Ägare |Ägar rollen ger till gång till alla resurser och åtgärder i ett Automation-konto, inklusive att ge åtkomst till andra användare, grupper och program för att hantera Automation-kontot. |
| Deltagare |Med deltagarrollen kan du hantera allt, men du kan inte ändra andra användares åtkomstbehörighet till ett Automation-konto. |
| Läsare |Med läsarrollen kan du visa alla resurser i ett Automation-konto men inte göra några ändringar. |
| Automation-operatör |Med rollen som Automation-operatör kan du Visa Runbook-namn och egenskaper och skapa och hantera jobb för alla Runbooks i ett Automation-konto. Den här rollen är användbar om du vill skydda dina Automation-konto resurser som autentiseringsuppgifter och Runbooks från att visas eller ändras, men ändå tillåta medlemmar i organisationen att köra dessa Runbooks. |
|Automatiserings jobb operatör|Med rollen automatiserings jobb operatör kan du skapa och hantera jobb för alla Runbooks i ett Automation-konto.|
|Automation Runbook-operator|Med rollen Automation Runbook-operatör kan du Visa namn och egenskaper för en Runbook.|
| Log Analytics Contributor | Med rollen Log Analytics Contributor kan du läsa alla övervaknings data och redigera övervaknings inställningar. Genom att redigera övervaknings inställningarna kan du lägga till VM-tillägget till virtuella datorer, läsa lagrings konto nycklar för att kunna konfigurera insamling av loggar från Azure Storage, skapa och konfigurera Automation-konton, lägga till Azure Automation funktioner och konfigurera Azure Diagnostics på alla Azure-resurser.|
| Log Analytics Reader | Med rollen Log Analytics läsare kan du Visa och söka i alla övervaknings data samt Visa övervaknings inställningar. Detta inkluderar visning av Azure Diagnostics-konfigurationen på alla Azure-resurser. |
| Övervaknings deltagare | Med rollen övervaknings deltagare kan du läsa alla övervaknings data och uppdatera övervaknings inställningar.|
| Övervaknings läsare | Med rollen övervaknings läsare kan du läsa alla övervaknings data. |
| Administratör för användaråtkomst |Med rollen Administratör för användaråtkomst kan du hantera användaråtkomsten till Azure Automation-konton. |

## <a name="role-permissions"></a>Roll behörigheter

I följande tabeller beskrivs de angivna behörigheterna för varje roll. Detta kan omfatta åtgärder som ger behörigheter och NotActions, vilket begränsar dem.

### <a name="owner"></a>Ägare

En ägare kan hantera allt, inklusive åtkomst. Följande tabell visar de behörigheter som har beviljats för rollen:

|Åtgärder|Beskrivning|
|---|---|
|Microsoft. Automation/automationAccounts/|Skapa och hantera resurser av alla typer.|

### <a name="contributor"></a>Deltagare

En deltagare kan hantera allt utom åtkomst. Följande tabell visar de behörigheter som beviljas och nekas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/|Skapa och hantera resurser av alla typer|
|**Inte åtgärder**||
|Microsoft. Authorization/*/Delete| Ta bort roller och roll tilldelningar.       |
|Microsoft. Authorization/*/Write     |  Skapa roller och roll tilldelningar.       |
|Microsoft. Authorization/elevateAccess/åtgärd    | Nekar möjligheten att skapa en administratör för användar åtkomst.       |

### <a name="reader"></a>Läsare

En läsare kan visa alla resurser i ett Automation-konto, men kan inte göra några ändringar.

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/Read|Visa alla resurser i ett Automation-konto. |

### <a name="automation-operator"></a>Automation-operatör

En Automation-operatör kan skapa och hantera jobb och läsa Runbook-namn och egenskaper för alla Runbooks i ett Automation-konto.

>[!NOTE]
>Om du vill kontrol lera operatörs åtkomst till enskilda Runbooks anger du inte den här rollen. Använd i stället operatorn för **automatiserings jobb operatör** och rollen **automatiserings Runbook-operator** i kombination.

Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft. Authorization/*/Read|Läs behörighet.|
|Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read|Läs Hybrid Runbook Worker-resurser.|
|Microsoft. Automation/automationAccounts/Jobs/Read|Lista jobb för Runbook.|
|Microsoft. Automation/automationAccounts/Jobs/återuppta/åtgärd|Återuppta ett jobb som har pausats.|
|Microsoft. Automation/automationAccounts/Jobs/stoppa/åtgärd|Avbryt ett pågående jobb.|
|Microsoft. Automation/automationAccounts/Jobs/Streams/Read|Läs jobb strömmar och utdata.|
|Microsoft. Automation/automationAccounts/Jobs/utdata/Read|Hämta utdata för ett jobb.|
|Microsoft. Automation/automationAccounts/Jobs/PAUSE/åtgärd|Pausa ett pågående jobb.|
|Microsoft. Automation/automationAccounts/Jobs/Write|Skapa jobb.|
|Microsoft. Automation/automationAccounts/jobSchedules/Read|Hämta ett jobb schema för Azure Automation.|
|Microsoft. Automation/automationAccounts/jobSchedules/Write|Skapa ett jobb schema för Azure Automation.|
|Microsoft. Automation/automationAccounts/linkedWorkspace/Read|Hämta arbets ytan som är länkad till Automation-kontot.|
|Microsoft. Automation/automationAccounts/Read|Hämta ett Azure Automation-konto.|
|Microsoft. Automation/automationAccounts/Runbooks/Read|Hämta en Azure Automation Runbook.|
|Microsoft. Automation/automationAccounts/-scheman/-läsningar|Få en Azure Automation schema till gång.|
|Microsoft. Automation/automationAccounts/-scheman/-skrivning|Skapa eller uppdatera en Azure Automation schema till gång.|
|Microsoft. Resources/Subscriptions/resourceGroups/Read      |Läs roller och roll tilldelningar.         |
|Microsoft. Resources/Deployments/*      |Skapa och hantera distributioner av resurs grupper.         |
|Microsoft. Insights/alertRules/*      | Skapa och hantera aviserings regler.        |
|Microsoft. support/* |Skapa och hantera support ärenden.|

### <a name="automation-job-operator"></a>Automatiserings jobb operatör

En rollen automatiserings jobb operatör beviljas i omfånget Automation-konto.Detta gör det möjligt för operatörs behörighet att skapa och hantera jobb för alla Runbooks i kontot. Om rollen jobb operatör beviljas Läs behörighet för resurs gruppen som innehåller Automation-kontot kan medlemmar i rollen starta Runbooks. De har dock inte möjlighet att skapa, redigera eller ta bort dem.

Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft. Authorization/*/Read|Läs behörighet.|
|Microsoft. Automation/automationAccounts/Jobs/Read|Lista jobb för Runbook.|
|Microsoft. Automation/automationAccounts/Jobs/återuppta/åtgärd|Återuppta ett jobb som har pausats.|
|Microsoft. Automation/automationAccounts/Jobs/stoppa/åtgärd|Avbryt ett pågående jobb.|
|Microsoft. Automation/automationAccounts/Jobs/Streams/Read|Läs jobb strömmar och utdata.|
|Microsoft. Automation/automationAccounts/Jobs/PAUSE/åtgärd|Pausa ett pågående jobb.|
|Microsoft. Automation/automationAccounts/Jobs/Write|Skapa jobb.|
|Microsoft. Resources/Subscriptions/resourceGroups/Read      |  Läs roller och roll tilldelningar.       |
|Microsoft. Resources/Deployments/*      |Skapa och hantera distributioner av resurs grupper.         |
|Microsoft. Insights/alertRules/*      | Skapa och hantera aviserings regler.        |
|Microsoft. support/* |Skapa och hantera support ärenden.|

### <a name="automation-runbook-operator"></a>Automation Runbook-operator

En rollen Automation Runbook-operatör beviljas i Runbook-omfånget. En Automation Runbook-operatör kan visa Runbooks namn och egenskaper.Den här rollen tillsammans med rollen **automatiserings jobb operatör** gör det möjligt för operatören att även skapa och hantera jobb för runbooken. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/Runbooks/Read     | Visa en lista över Runbooks.        |
|Microsoft. Authorization/*/Read      | Läs behörighet.        |
|Microsoft. Resources/Subscriptions/resourceGroups/Read      |Läs roller och roll tilldelningar.         |
|Microsoft. Resources/Deployments/*      | Skapa och hantera distributioner av resurs grupper.         |
|Microsoft. Insights/alertRules/*      | Skapa och hantera aviserings regler.        |
|Microsoft. support/*      | Skapa och hantera support ärenden.        |

### <a name="log-analytics-contributor"></a>Log Analytics Contributor

En Log Analytics deltagare kan läsa alla övervaknings data och redigera övervaknings inställningar. Genom att redigera övervaknings inställningarna lägger du till VM-tillägget till virtuella datorer. läsning av lagrings konto nycklar för att kunna konfigurera samling av loggar från Azure Storage. Skapa och konfigurera Automation-konton. lägger till funktioner; och konfigurera Azure Diagnostics på alla Azure-resurser. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/read|Läs resurser av alla typer, förutom hemligheter.|
|Microsoft. Automation/automationAccounts/*|Hantera Automation-konton.|
|Microsoft. ClassicCompute/virtualMachines/Extensions/*|Skapa och hantera tillägg för virtuella datorer.|
|Microsoft. ClassicStorage/storageAccounts/Listnycklar/Action|Lista klassiska lagrings konto nycklar.|
|Microsoft. Compute/virtualMachines/Extensions/*|Skapa och hantera klassiska tillägg för virtuella datorer.|
|Microsoft. Insights/alertRules/*|Läsa/skriva/ta bort aviserings regler.|
|Microsoft. Insights/diagnosticSettings/*|Läsa/skriva/ta bort diagnostikinställningar.|
|Microsoft. OperationalInsights/*|Hantera Azure Monitor loggar.|
|Microsoft. OperationsManagement/*|Hantera Azure Automation funktioner på arbets ytor.|
|Microsoft. Resources/Deployments/*|Skapa och hantera distributioner av resurs grupper.|
|Microsoft. Resources/Subscriptions/ResourceGroups/distributions/*|Skapa och hantera distributioner av resurs grupper.|
|Microsoft. Storage/storageAccounts/Listnycklar/åtgärd|Lista lagrings konto nycklar.|
|Microsoft. support/*|Skapa och hantera support ärenden.|

### <a name="log-analytics-reader"></a>Log Analytics Reader

En Log Analytics läsare kan visa och söka i alla övervaknings data samt Visa övervaknings inställningar, inklusive Visa konfigurationen av Azure Diagnostics på alla Azure-resurser. Följande tabell visar de behörigheter som beviljas eller nekas för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/read|Läs resurser av alla typer, förutom hemligheter.|
|Microsoft. OperationalInsights/arbets ytor/analys/fråga/åtgärd|Hantera frågor i Azure Monitor loggar.|
|Microsoft. OperationalInsights/arbets ytor/search/åtgärd|Sök Azure Monitor loggdata.|
|Microsoft. support/*|Skapa och hantera support ärenden.|
|**Inte åtgärder**| |
|Microsoft. OperationalInsights/arbets ytor/sharedKeys/Read|Det går inte att läsa nycklar för delad åtkomst.|

### <a name="monitoring-contributor"></a>Övervaknings deltagare

En övervaknings deltagare kan läsa alla övervaknings data och uppdatera övervaknings inställningar. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/read|Läs resurser av alla typer, förutom hemligheter.|
|Microsoft. AlertsManagement/Alerts/*|Hantera aviseringar.|
|Microsoft. AlertsManagement/alertsSummary/*|Hantera aviserings instrument panelen.|
|Microsoft. Insights/AlertRules/*|Hantera aviserings regler.|
|Microsoft. Insights/komponenter/*|Hantera Application Insights-komponenter.|
|Microsoft.Insights/DiagnosticSettings/*|Hantera diagnostikinställningar.|
|Microsoft. Insights/eventtypes/*|Visa lista över aktivitets logg händelser (hanterings händelser) i en prenumeration. Den här behörigheten gäller för både program mässig och Portal åtkomst till aktivitets loggen.|
|Microsoft. Insights/LogDefinitions/*|Den här behörigheten krävs för användare som behöver åtkomst till aktivitets loggar via portalen. Lista logg kategorier i aktivitets loggen.|
|Microsoft. Insights/MetricDefinitions/*|Läs mått definitioner (lista över tillgängliga mått typer för en resurs).|
|Microsoft. Insights/Metrics/*|Läs mått för en resurs.|
|Microsoft. Insights/register/åtgärd|Registrera Microsoft. Insights-providern.|
|Microsoft. Insights/webtests/*|Hantera Application Insights webbtester.|
|Microsoft. OperationalInsights/arbetsytes/intelligencepacks/*|Hantera lösnings paket för Azure Monitor loggar.|
|Microsoft. OperationalInsights/arbetsytes/savedSearches/*|Hantera sparade sökningar i Azure Monitor loggar.|
|Microsoft. OperationalInsights/arbets ytor/search/åtgärd|Sök Log Analytics arbets ytor.|
|Microsoft. OperationalInsights/arbets ytor/sharedKeys/åtgärd|Lista nycklar för en Log Analytics-arbetsyta.|
|Microsoft. OperationalInsights/arbetsytes/storageinsightconfigs/*|Hantera Azure Monitor loggar lagrings Insight-konfigurationer.|
|Microsoft. support/*|Skapa och hantera support ärenden.|
|Microsoft. WorkloadMonitor/arbets belastningar/*|Hantera arbets belastningar.|

### <a name="monitoring-reader"></a>Övervaknings läsare

En övervaknings läsare kan läsa alla övervaknings data. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/read|Läs resurser av alla typer, förutom hemligheter.|
|Microsoft. OperationalInsights/arbets ytor/search/åtgärd|Sök Log Analytics arbets ytor.|
|Microsoft. support/*|Skapa och hantera support biljetter|

### <a name="user-access-administrator"></a>Administratör för användaråtkomst

En administratör för användar åtkomst kan hantera användar åtkomst till Azure-resurser. Följande tabell visar de behörigheter som har beviljats för rollen:

|**Åtgärder**  |**Beskrivning**  |
|---------|---------|
|*/read|Läs alla resurser|
|Microsoft. Authorization/*|Hantera auktorisering|
|Microsoft. support/*|Skapa och hantera support biljetter|

## <a name="feature-setup-permissions"></a>Funktions installations behörigheter

I följande avsnitt beskrivs minimi kraven för de behörigheter som krävs för att aktivera Uppdateringshantering-och Ändringsspårning-och inventerings funktionerna.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>Behörigheter för att aktivera Uppdateringshantering och Ändringsspårning och inventering från en virtuell dator

|**Åtgärd**  |**Behörighet**  |**Minsta omfång**  |
|---------|---------|---------|
|Skriv ny distribution      | Microsoft. Resources/Deployments/*          |Prenumeration          |
|Skriv ny resurs grupp      | Microsoft. Resources/Subscriptions/resourceGroups/Write        | Prenumeration          |
|Skapa ny standard arbets yta      | Microsoft. OperationalInsights/arbets ytor/Skriv         | Resursgrupp         |
|Skapa nytt konto      |  Microsoft. Automation/automationAccounts/Write        |Resursgrupp         |
|Länka arbets yta och konto      |Microsoft. OperationalInsights/arbets ytor/Skriv</br>Microsoft. Automation/automationAccounts/Read|Arbetsyta</br>Automation-konto
|Skapa MMA-tillägg      | Microsoft. Compute/virtualMachines/Write         | Virtuell dator         |
|Skapa Sparad sökning      | Microsoft. OperationalInsights/arbets ytor/Skriv          | Arbetsyta         |
|Skapa omfattnings konfiguration      | Microsoft. OperationalInsights/arbets ytor/Skriv          | Arbetsyta         |
|Registrerings tillstånds kontroll – Läs arbets yta      | Microsoft. OperationalInsights/arbets ytor/läsa         | Arbetsyta         |
|Registrerings tillstånds kontroll – Läs den länkade arbets ytans egenskap för konto     | Microsoft. Automation/automationAccounts/Read      | Automation-konto        |
|Registrerings tillstånds kontroll – Läs lösning      | Microsoft. OperationalInsights/arbets ytor/intelligencepacks/Read          | Lösning         |
|Registrerings tillstånds kontroll – läsa virtuell dator      | Microsoft. Compute/virtualMachines/Read         | Virtuell dator         |
|Registrerings tillstånds kontroll – Läs konto      | Microsoft. Automation/automationAccounts/Read  |  Automation-konto   |
| Kontroll av arbets ytan för VM<sup>1</sup>       | Microsoft. OperationalInsights/arbets ytor/läsa         | Prenumeration         |
| Registrera Log Analytics-providern |Microsoft. Insights/register/åtgärd | Prenumeration|

<sup>1</sup> den här behörigheten krävs för att aktivera funktioner via den virtuella datorns Portal upplevelse.

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>Behörigheter för att aktivera Uppdateringshantering och Ändringsspårning och inventering från ett Automation-konto

|**Åtgärd**  |**Behörighet** |**Minsta omfång**  |
|---------|---------|---------|
|Skapa ny distribution     | Microsoft. Resources/Deployments/*        | Prenumeration         |
|Skapa ny resurs grupp     | Microsoft. Resources/Subscriptions/resourceGroups/Write         | Prenumeration        |
|Bladet AutomationOnboarding – skapa ny arbets yta     |Microsoft. OperationalInsights/arbets ytor/Skriv           | Resursgrupp        |
|AutomationOnboarding blad – Läs länkad arbets yta     | Microsoft. Automation/automationAccounts/Read        | Automation-konto       |
|AutomationOnboarding-lösning för blad läsning     | Microsoft. OperationalInsights/arbets ytor/intelligencepacks/Read         | Lösning        |
|AutomationOnboarding blad – Läs arbets yta     | Microsoft. OperationalInsights/arbets ytor/intelligencepacks/Read        | Arbetsyta        |
|Skapa länk för arbets yta och konto     | Microsoft. OperationalInsights/arbets ytor/Skriv        | Arbetsyta        |
|Skriv konto för sko      | Microsoft. Automation/automationAccounts/Write        | Konto        |
|Skapa/redigera Sparad sökning     | Microsoft. OperationalInsights/arbets ytor/Skriv        | Arbetsyta        |
|Skapa/redigera omfattnings konfiguration     | Microsoft. OperationalInsights/arbets ytor/Skriv        | Arbetsyta        |
| Registrera Log Analytics-providern |Microsoft. Insights/register/åtgärd | Prenumeration|
|**Steg 2 – aktivera flera virtuella datorer**     |         |         |
|Bladet VMOnboarding – skapa MMA-tillägg     | Microsoft. Compute/virtualMachines/Write           | Virtuell dator        |
|Skapa/redigera Sparad sökning     | Microsoft. OperationalInsights/arbets ytor/Skriv           | Arbetsyta        |
|Skapa/redigera omfattnings konfiguration  | Microsoft. OperationalInsights/arbets ytor/Skriv   | Arbetsyta|

## <a name="update-management-permissions"></a>Behörigheter för hantering av uppdateringar

Uppdaterings hanteringen når över flera tjänster för att tillhandahålla tjänsten. I följande tabell visas de behörigheter som krävs för att hantera distributioner av uppdaterings hantering:

|**Resurs**  |**Role**  |**Omfång**  |
|---------|---------|---------|
|Automation-konto     | Log Analytics Contributor       | Automation-konto        |
|Automation-konto    | Virtuell datordeltagare        | Resurs grupp för kontot        |
|Log Analytics-arbetsyta     | Log Analytics Contributor| Log Analytics-arbetsyta        |
|Log Analytics-arbetsyta |Log Analytics Reader| Prenumeration|
|Lösning     |Log Analytics Contributor         | Lösning|
|Virtuell dator     | Virtuell datordeltagare        | Virtuell dator        |

## <a name="configure-azure-rbac-for-your-automation-account"></a>Konfigurera Azure RBAC för ditt Automation-konto

I följande avsnitt lär du dig hur du konfigurerar Azure RBAC på ditt Automation-konto via [Azure Portal](#configure-azure-rbac-using-the-azure-portal) och [PowerShell](#configure-azure-rbac-using-powershell).

### <a name="configure-azure-rbac-using-the-azure-portal"></a>Konfigurera Azure RBAC med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com/) och öppna Automation-kontot från sidan Automation-konton.
2. Klicka på **åtkomst kontroll (IAM)** för att öppna sidan åtkomst kontroll (IAM). Du kan använda den här sidan för att lägga till nya användare, grupper och program för att hantera ditt Automation-konto och visa befintliga roller som kan konfigureras för Automation-kontot.
3. Klicka på fliken **Rolltilldelningar**.

   ![Knappen Åtkomst](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Lägga till en ny användare och tilldela en roll

1. Klicka på **+ Lägg till roll tilldelning** på sidan åtkomst kontroll (IAM). Den här åtgärden öppnar sidan Lägg till roll tilldelning där du kan lägga till en användare, grupp eller ett program och tilldela en motsvarande roll.

2. Välj en roll i listan över tillgängliga roller. Du kan välja någon av de tillgängliga inbyggda rollerna som ett Automation-konto har stöd för eller en anpassad roll som du kan ha definierat.

3. Skriv namnet på den användare som du vill ge behörighet till i fältet **Välj** . Välj användaren i listan och klicka på **Spara**.

   ![Lägga till användare](media/automation-role-based-access-control/automation-04-add-users.png)

   Nu bör du se användaren som har lagts till på sidan användare med den valda rollen tilldelad.

   ![Visa användare](media/automation-role-based-access-control/automation-05-list-users.png)

   Du kan också tilldela en roll till användaren från sidan Roller.

4. Öppna sidan roller genom att klicka på **roller** på sidan åtkomst kontroll (IAM). Du kan visa namnet på rollen och antalet användare och grupper som har tilldelats rollen.

    ![Tilldela en roll från sidan Användare](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Du kan bara ställa in rollbaserad åtkomst kontroll i Automation-kontots omfattning och inte på någon resurs under Automation-kontot.

#### <a name="remove-a-user"></a>Ta bort en användare

Du kan ta bort åtkomst behörigheten för en användare som inte hanterar Automation-kontot eller som inte längre fungerar för organisationen. Nedan följer stegen för att ta bort en användare:

1. På sidan åtkomst kontroll (IAM) väljer du användaren som du vill ta bort och klickar på **ta bort**.
2. Klicka på knappen **Ta bort** på sidan med tilldelningsinformation.
3. Bekräfta borttagningen genom att klicka på **Ja**.

   ![Ta bort användare](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>Konfigurera Azure RBAC med PowerShell

Du kan också konfigurera rollbaserad åtkomst till ett Automation-konto med hjälp av följande [Azure PowerShell-cmdlet: ar](../role-based-access-control/role-assignments-powershell.md):

[Get-AzRoleDefinition](/powershell/module/Az.Resources/Get-AzRoleDefinition) visar en lista över alla Azure-roller som är tillgängliga i Azure Active Directory. Du kan använda den här cmdleten med `Name` parametern för att visa en lista över alla åtgärder som en speciell roll kan utföra.

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

[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) visar Azure Role-tilldelningar i det angivna omfånget. Utan parametrar returnerar denna cmdlet alla roll tilldelningar som har gjorts under prenumerationen. Använd `ExpandPrincipalGroups` parametern för att visa en lista över åtkomst tilldelningar för den angivna användaren, samt de grupper som användaren tillhör.

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

**Exempel:** Använd följande kommando för att tilldela rollen "Automation-operatör" för en användare i Automation-kontots omfattning.

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

Använd [Remove-AzRoleAssignment](/powershell/module/Az.Resources/Remove-AzRoleAssignment) om du vill ta bort åtkomsten till en angiven användare, grupp eller program från ett visst omfång.

**Exempel:** Använd följande kommando för att ta bort användaren från rollen som Automation-operatör i Automation-kontots omfång.

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

I föregående exempel ersätter `sign-in ID of a user you wish to remove` du,, `SubscriptionID` `Resource Group Name` och `Automation account name` med din konto information. Välj **Ja** när du uppmanas att bekräfta innan du fortsätter att ta bort användar roll tilldelningar.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Användar upplevelse för rollen Automation-operatör – Automation-konto

När en användare som är tilldelad rollen som Automation-operatör i Automation-kontots omfattning visar det Automation-konto som han/hon har tilldelats, kan användaren bara visa listan över Runbooks, Runbook-jobb och scheman som skapats i Automation-kontot. Den här användaren kan inte Visa definitionerna för dessa objekt. Användaren kan starta, stoppa, pausa, återuppta eller schemalägga Runbook-jobbet. Användaren har dock inte åtkomst till andra Automation-resurser, till exempel konfigurationer, hybrid Worker-grupper eller DSC-noder.

![Ingen åtkomst till resurser](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>Konfigurera Azure RBAC för Runbooks

Med Azure Automation kan du tilldela Azure-roller till vissa Runbooks. Det gör du genom att köra följande skript för att lägga till en användare till en angiven Runbook. En administratör för Automation-kontot eller en klient administratör kan köra det här skriptet.

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

När skriptet har körts ska användaren logga in på Azure Portal och välja **alla resurser**. I listan kan användaren se den Runbook som han/hon har lagts till som en Automation Runbook-operator.

![Runbook-Azure RBAC i portalen](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Användar upplevelse för Automation-operatörs roll – Runbook

När en användare som är tilldelad rollen som Automation-operatör i Runbook-omfånget visar en tilldelad Runbook, kan användaren bara starta runbooken och Visa Runbook-jobben.

![Har bara åtkomst att starta](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure RBAC med hjälp av PowerShell i [lägga till eller ta bort Azure Role-tilldelningar med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Mer information om typer av Runbooks finns i [Azure Automation Runbook-typer](automation-runbook-types.md).
* Information om hur du startar en Runbook finns [i starta en Runbook i Azure Automation](start-runbooks.md).
