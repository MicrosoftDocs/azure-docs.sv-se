---
title: Visa aktivitets loggar för Azure RBAC-ändringar
description: Visa aktivitets loggar för Azure-rollbaserad åtkomst kontroll (Azure RBAC) ändringar under de senaste 90 dagarna.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: d9b39bc9a2f00fe83cae0ff78c6346042967e8bf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042149"
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

Klicka på en post för att öppna fönstret Sammanfattning om du vill ha mer information. Klicka på **JSON** -fliken för att få en detaljerad logg.

![Aktivitets loggar med hjälp av portalen med Summary-fönstret öppen skärm bild](./media/change-history-report/activity-log-summary-portal.png)

Aktivitets loggen på portalen har flera filter. Här är Azure RBAC-relaterade filter:

| Filtrera | Värde |
| --------- | --------- |
| Händelsekategori | <ul><li>Administrativ</li></ul> |
| Åtgärd | <ul><li>Skapa roll tilldelning</li><li>Ta bort roll tilldelning</li><li>Skapa eller uppdatera anpassad roll definition</li><li>Ta bort anpassad roll definition</li></ul> |

Mer information om aktivitets loggar finns i [Visa aktivitets loggar för att övervaka åtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).


## <a name="interpret-a-log-entry"></a>Tolka en loggpost

Logg resultatet från JSON-fliken, Azure PowerShell eller Azure CLI kan innehålla mycket information. Här följer några av de nyckel egenskaper som du kan söka efter när du försöker tolka en loggpost. Information om hur du filtrerar logg resultatet med Azure PowerShell eller Azure CLI finns i följande avsnitt.

> [!div class="mx-tableFixed"]
> | Egenskap | Exempelvärden | Beskrivning |
> | --- | --- | --- |
> | auktorisering: åtgärd | Microsoft.Authorization/roleAssignments/write | Skapa roll tilldelning |
> |  | Microsoft. Authorization/roleAssignments/Delete | Ta bort roll tilldelning |
> |  | Microsoft. Authorization/roleDefinitions/Write | Skapa eller uppdatera roll definition |
> |  | Microsoft. Authorization/roleDefinitions/Delete | Ta bort roll definition |
> | auktorisering: omfång | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | Åtgärdens omfång |
> | anroparen | admin@example.com<br/>objectID | Som initierade åtgärden |
> | eventTimestamp | 2021-03-01T22:07:41.126243 Z | Tid då åtgärden utfördes |
> | status: värde | Börjat<br/>Lyckades<br/>Misslyckad | Status för åtgärden |

## <a name="azure-powershell"></a>Azure PowerShell

Om du vill visa aktivitets loggar med Azure PowerShell använder du kommandot [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) .

Det här kommandot visar alla ändringar i roll tilldelningen i en prenumeration under de senaste sju dagarna:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Det här kommandot visar en lista över alla roll definitions ändringar i en resurs grupp under de senaste sju dagarna:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>Filtrera logg utdata

Logg resultatet kan innehålla mycket information. Det här kommandot visar alla ändringar av roll tilldelning och roll definition i en prenumeration under de senaste sju dagarna och filtrerar utdata:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

Följande visar ett exempel på den filtrerade loggen när du skapar en roll tilldelning:

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

Om du använder ett huvud namn för tjänsten för att skapa roll tilldelningar, kommer egenskapen Caller att vara ett objekt-ID för tjänstens huvud namn. Du kan använda [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) för att hämta information om tjänstens huvud namn.

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Om du vill visa aktivitets loggar med Azure CLI använder du kommandot [AZ Monitor Activity-Log List](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) .

Det här kommandot visar en lista över aktivitets loggarna i en resurs grupp från 1 mars, som ser framåt sju dagar:

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

Det här kommandot listar aktivitets loggarna för providern för auktorisering från 1 mars, vilket ser till att vidarebefordra sju dagar:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>Filtrera logg utdata

Logg resultatet kan innehålla mycket information. Detta kommando visar en lista över alla ändringar av roll tilldelning och roll definition i en prenumeration som ser fram sju dagar och filtrerar utdata:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

Följande visar ett exempel på den filtrerade loggen när du skapar en roll tilldelning:

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
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

1. Du kan också använda [Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) för att fråga och visa loggarna. Mer information finns i [Kom igång med logg frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md).

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
* [Visa aktivitets loggar för att övervaka åtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Övervaka prenumerations aktivitet med Azures aktivitets logg](../azure-monitor/essentials/platform-logs-overview.md)