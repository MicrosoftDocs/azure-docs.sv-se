---
title: Visa aktivitets loggar för Azure RBAC-ändringar
description: Visa aktivitets loggar för Azure-rollbaserad åtkomst kontroll (Azure RBAC) ändringar av Azure-resurser under de senaste 90 dagarna.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 53b72ac22df845f88dc82b14aa5dfaa57973b0d1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595833"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Visa aktivitets loggar för Azure RBAC-ändringar

Ibland behöver du information om ändringar i Azure rollbaserad åtkomst kontroll (Azure RBAC), till exempel för gransknings-eller fel söknings syfte. När en användare gör ändringar i roll tilldelningarna eller roll definitionerna i dina prenumerationer registreras ändringarna i [Azures aktivitets logg](../azure-monitor/essentials/platform-logs-overview.md). Du kan visa aktivitets loggarna för att se alla Azure RBAC-ändringar för de senaste 90 dagarna.

## <a name="operations-that-are-logged"></a>Åtgärder som loggas

Här är de Azure RBAC-relaterade åtgärder som är loggade i aktivitets loggen:

- Skapa roll tilldelning
- Ta bort roll tilldelning
- Skapa eller uppdatera anpassad roll definition
- Ta bort anpassad roll definition

## <a name="azure-portal"></a>Azure Portal

Det enklaste sättet att komma igång på är att visa aktivitetsloggarna i Azure Portal. Följande skärm bild visar ett exempel på roll tilldelnings åtgärder i aktivitets loggen. Det innehåller också ett alternativ för att ladda ned loggarna som en CSV-fil.

![Aktivitets loggar med portalen-skärm bild](./media/change-history-report/activity-log-portal.png)

Aktivitets loggen på portalen har flera filter. Här är Azure RBAC-relaterade filter:

| Filtrera | Värde |
| --------- | --------- |
| Händelsekategori | <ul><li>Administrativ</li></ul> |
| Åtgärd | <ul><li>Skapa roll tilldelning</li><li>Ta bort roll tilldelning</li><li>Skapa eller uppdatera anpassad roll definition</li><li>Ta bort anpassad roll definition</li></ul> |

Mer information om aktivitets loggar finns i [Visa aktivitets loggar för att övervaka åtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Om du vill visa aktivitets loggar med Azure PowerShell använder du kommandot [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) .

Det här kommandot visar alla ändringar i roll tilldelningen i en prenumeration under de senaste sju dagarna:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Det här kommandot visar en lista över alla roll definitions ändringar i en resurs grupp under de senaste sju dagarna:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Det här kommandot visar alla ändringar av roll tilldelning och roll definition i en prenumeration under de senaste sju dagarna och visar resultatet i en lista:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111
                          eventCategory  : Administrative

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

Om du använder ett huvud namn för tjänsten för att skapa roll tilldelningar, är egenskapen anropare ett objekt-ID. Du kan använda [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) för att hämta information om tjänstens huvud namn.

```Example
Caller                  : 44444444-4444-4444-4444-444444444444
EventTimestamp          : 6/4/2020 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: 55555555-5555-5555-5555-555555555555
                          category       : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Om du vill visa aktivitets loggar med Azure CLI använder du kommandot [AZ Monitor Activity-Log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) .

Det här kommandot visar en lista över aktivitets loggarna i en resurs grupp från den 27 februari, som ser fram sju dagar:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Det här kommandot visar aktivitets loggarna för providern för auktorisering från den 27 februari, som ser fram sju dagar:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md) är ett annat verktyg som du kan använda för att samla in och analysera Azure RBAC-ändringar för alla dina Azure-resurser. Azure Monitors loggar har följande fördelar:

- Skriv komplexa frågor och logik
- Integrera med aviseringar, Power BI och andra verktyg
- Spara data för längre Retentions perioder
- Kors referens med andra loggar som säkerhet, virtuell dator och anpassad

Här är de grundläggande stegen för att komma igång:

1. [Skapa en Log Analytics-arbetsyta](../azure-monitor/logs/quick-create-workspace.md).

1. [Konfigurera Aktivitetslogganalys-lösningen](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution) för din arbets yta.

1. [Visa aktivitets loggarna](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution). Ett snabbt sätt att navigera till sidan med översikt över Aktivitetslogganalys lösning är att klicka på alternativet **loggar** .

   ![Alternativ för Azure Monitor-loggar i portalen](./media/change-history-report/azure-log-analytics-option.png)

1. Du kan också använda [Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) för att fråga och visa loggarna. Mer information finns i [Kom igång med Azure Monitor logg frågor](../azure-monitor/logs/get-started-queries.md).

Här är en fråga som returnerar nya roll tilldelningar organiserade efter mål resurs leverantör:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Här är en fråga som returnerar roll tilldelnings ändringar som visas i ett diagram:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Aktivitets loggar med hjälp av Advanced Analytics-portalen – skärm bild](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Nästa steg
* [Visa händelser i aktivitetsloggen](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Övervaka prenumerationsaktivitet med Azure-aktivitetsloggen](../azure-monitor/essentials/platform-logs-overview.md)