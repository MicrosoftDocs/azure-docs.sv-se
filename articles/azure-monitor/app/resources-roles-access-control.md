---
title: Resurser, roller och åtkomst kontroll i Azure Application Insights | Microsoft Docs
description: Ägare, deltagare och läsare av organisationens insikter.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4254170ad100b634439cc846dc381e305ae71d1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589593"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Resurser, roller och åtkomst kontroll i Application Insights

Du kan styra vem som har Läs-och uppdaterings åtkomst till dina data i Azure [Application Insights][start]med hjälp av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Tilldela åtkomst till användare i **resurs gruppen eller prenumerationen** som program resursen tillhör, inte i själva resursen. Tilldela rollen **Application Insights komponent deltagare** . Detta säkerställer en enhetlig kontroll av åtkomst till webbtester och aviseringar tillsammans med program resursen. [Läs mer](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Resurser, grupper och prenumerationer

För det första, vissa definitioner:

* **Resurs** -en instans av en Microsoft Azure-tjänst. Din Application Insights-resurs samlar in, analyserar och visar telemetri-data som skickas från ditt program.  Andra typer av Azure-resurser är bland annat webbappar, databaser och virtuella datorer.
  
    Om du vill se dina resurser öppnar du [Azure Portal][portal], loggar in och klickar på alla resurser. Du hittar en resurs genom att skriva en del av namnet i fältet filter.
  
    ![Lista över Azure-resurser](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Resurs grupp**][group] -varje resurs tillhör en grupp. En grupp är ett bekvämt sätt att hantera relaterade resurser, särskilt för åtkomst kontroll. Till exempel i en resurs grupp kan du exempelvis placera en webbapp, en Application Insights resurs för att övervaka appen och en lagrings resurs för att behålla exporterade data.

* [**Prenumeration**](https://portal.azure.com) – om du vill använda Application Insights eller andra Azure-resurser loggar du in på en Azure-prenumeration. Varje resurs grupp tillhör en Azure-prenumeration där du väljer ditt pris paket och väljer medlemmar och deras åtkomst behörigheter om det är en organisations prenumeration.
* [**Microsoft-konto**][account] -det användar namn och lösen ord som du använder för att logga in på Microsoft Azure prenumerationer, Xbox Live, Outlook.com och andra Microsoft-tjänster.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a> Kontrol lera åtkomst i resurs gruppen

Det är viktigt att förstå att förutom den resurs som du skapade för ditt program, finns det också separata dolda resurser för aviseringar och webbtester. De är kopplade till samma [resurs grupp](#resource-group) som din Application Insights-resurs. Du kanske också har lagt till andra Azure-tjänster i dit, t. ex. webbplatser eller lagring.

## <a name="to-provide-access-to-another-user"></a>Ge åtkomst till en annan användare

Du måste ha ägar behörighet till prenumerationen eller resurs gruppen.

Användaren måste ha ett [Microsoft-konto][account]eller ha åtkomst till sitt [organisations Microsoft-konto](../../active-directory/fundamentals/sign-up-organization.md). Du kan ge åtkomst till enskilda användare och även till användar grupper som definierats i Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Navigera till resurs grupp eller direkt till själva resursen

Välj **åtkomst kontroll (IAM)** på den vänstra menyn.

![Skärm bild av knappen åtkomst kontroll i Azure Portal](./media/resources-roles-access-control/0001-access-control.png)

Välj **Lägg till roll tilldelning**

![Skärm bild av menyn åtkomst kontroll med knappen Lägg till markerad i rött](./media/resources-roles-access-control/0002-add.png)

Vyn **Lägg till behörigheter** nedan är främst avsedd för Application Insights resurser, om du visar behörigheter för åtkomst kontroll från en högre nivå, t. ex. resurs grupper, så ser du ytterligare icke-Application Insights-centrerade roller.

Om du vill visa information om alla inbyggda roller som baseras på Azure rollbaserad åtkomst kontroll använder du det [officiella referens innehållet](../../role-based-access-control/built-in-roles.md).

![Skärm bild av listan användar roll för åtkomst kontroll](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Välj en roll

Där så är tillämpligt länkar vi till den tillhör ande officiella referens dokumentationen.

| Roll | I resurs gruppen |
| --- | --- |
| [Ägare](../../role-based-access-control/built-in-roles.md#owner) |Kan ändra vad som helst, inklusive användar åtkomst. |
| [Deltagare](../../role-based-access-control/built-in-roles.md#contributor) |Kan redigera vad som helst, inklusive alla resurser. |
| [Application Insights komponent deltagare](../../role-based-access-control/built-in-roles.md#application-insights-component-contributor) |Kan redigera Application Insights-resurser. |
| [Läsare](../../role-based-access-control/built-in-roles.md#reader) |Kan visa men inte ändra något. |
| [Application Insights Snapshot Debugger](../../role-based-access-control/built-in-roles.md#application-insights-snapshot-debugger) | Ger användaren behörighet att använda Application Insights Snapshot Debugger-funktioner. Observera att den här rollen ingår varken i rollen ägare eller deltagare. |
| Azure Service Deploy Release Management Contributor | Deltagar rollen för tjänster som distribueras via Azure Service Deploy. |
| [Data rensning](../../role-based-access-control/built-in-roles.md#data-purger) | Särskild roll för att rensa personliga data. Se vår [vägledning för person uppgifter](../logs/personal-data-mgmt.md) för mer information.   |
| ExpressRoute-administratör | Kan skapa ta bort och hantera Express vägar.|
| [Log Analytics Contributor](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) | Log Analytics deltagare kan läsa alla övervaknings data och redigera övervaknings inställningar. Genom att redigera övervaknings inställningarna lägger du till VM-tillägget till virtuella datorer. läsning av lagrings konto nycklar för att kunna konfigurera samling av loggar från Azure Storage. Skapa och konfigurera Automation-konton. lägga till lösningar. och konfigurera Azure Diagnostics på alla Azure-resurser.  |
| [Log Analytics Reader](../../role-based-access-control/built-in-roles.md#log-analytics-reader) | Log Analytics läsaren kan visa och söka i alla övervaknings data samt Visa övervaknings inställningar, inklusive Visa konfigurationen av Azure Diagnostics på alla Azure-resurser. |
| masterreader | Tillåter en användare att Visa allting men inte göra ändringar. |
| [Övervaknings deltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor) | Kan läsa alla övervaknings data och uppdatera övervaknings inställningar.|
| [Övervaknings mått utgivare](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher) | Möjliggör publicering av mått mot Azure-resurser. |
| [Övervaknings läsare](../../role-based-access-control/built-in-roles.md#monitoring-reader) | Kan läsa alla övervaknings data. |
| Resurs princip deltagare (för hands version) | Användare med egna användare från EA, med rättigheter att skapa/ändra resurs principer, skapa support ärende och läsa resurs/hierarki.  |
| [Administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) | Tillåter en användare att hantera åtkomst för andra användare till Azure-resurser.|
| [Webbplats deltagare](../../role-based-access-control/built-in-roles.md#website-contributor) | Gör att du kan hantera webbplatser (inte webb planer), men inte till gång till dem.|

Redigering inkluderar att skapa, ta bort och uppdatera:

* Resurser
* Webbtester
* Aviseringar
* Löpande export

#### <a name="select-the-user"></a>Välj användaren

Om användaren som du vill använda inte finns i katalogen kan du bjuda in alla med en Microsoft-konto.
(Om de använder tjänster som Outlook.com, OneDrive, Windows Phone eller XBox Live har de en Microsoft-konto.)

## <a name="related-content"></a>Relaterat innehåll

* [Azure RBAC (rollbaserad åtkomstkontroll)](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>PowerShell-fråga för att fastställa roll medlemskap

Eftersom vissa roller kan länkas till aviseringar och e-postaviseringar kan det vara bra att kunna generera en lista över användare som tillhör en viss roll. För att hjälpa till med att skapa dessa typer av listor erbjuder vi följande exempel frågor som kan justeras så att de passar dina behov:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Fråga hela prenumerationen för administratörs roller + deltagar roller

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Fråga inom kontexten för en speciell Application Insights resurs för ägare och deltagare

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Fråga inom kontexten för en speciell resurs grupp för ägare och deltagare

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md
