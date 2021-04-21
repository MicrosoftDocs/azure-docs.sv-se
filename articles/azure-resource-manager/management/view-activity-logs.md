---
title: Visa Azure-aktivitetsloggar för att övervaka resurser
description: Använd aktivitetsloggarna för att granska användaråtgärder och fel. Visar Azure Portal PowerShell, Azure CLI och REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 7612146a0f9407663631f87c57f30ea4c590c7a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773935"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Visa aktivitetsloggar för att övervaka åtgärder för resurser

Via aktivitetsloggarna kan du fastställa:

* vilka åtgärder som har vidtagits på resurserna i din prenumeration
* vem som startade åtgärden
* när åtgärden inträffade
* status för åtgärden
* värdena för andra egenskaper som kan hjälpa dig undersöka åtgärden

Aktivitetsloggen innehåller alla skrivåtgärder (PUT, POST, DELETE) för dina resurser. Det omfattar inte läsåtgärder (GET). En lista över resursåtgärder finns i Åtgärder [för Azure-resursprovider.](../../role-based-access-control/resource-provider-operations.md) Du kan använda aktivitetsloggarna för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrat en resurs.

Aktivitetsloggar behålls i 90 dagar. Du kan fråga efter alla datumintervall förutsatt att startdatumet inte är mer än 90 dagar bakåt i tiden.

Du kan hämta information från aktivitetsloggarna via portalen, PowerShell, Azure CLI, Insights REST API eller [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Azure Portal

Följ dessa steg om du vill visa aktivitetsloggarna via portalen:

1. På Azure Portal väljer du **Övervaka** eller söker efter och väljer **Övervaka på** valfri sida.

    ![Välj övervakare](./media/view-activity-logs/select-monitor-from-menu.png)

1. Välj **Aktivitetslogg.**

    ![Välj aktivitetslogg](./media/view-activity-logs/select-activity-log.png)

1. Du ser en sammanfattning av de senaste åtgärderna. En standarduppsättning med filter tillämpas på åtgärderna. Observera att informationen i sammanfattningen innehåller vem som startade åtgärden och när den inträffade.

    ![Visa sammanfattning av de senaste åtgärderna](./media/view-activity-logs/audit-summary.png)

1. Om du snabbt vill köra en fördefinierad uppsättning filter väljer **du Snabbinsikter**.

    ![Välj snabbinsikter](./media/view-activity-logs/select-quick-insights.png)

1. Välj ett av alternativen. Välj till exempel Misslyckade **distributioner för** att se fel från distributioner.

    ![Välj misslyckade distributioner](./media/view-activity-logs/select-failed-deployments.png)

1. Observera att filtren har ändrats för att fokusera på distributionsfel under de senaste 24 timmarna. Endast åtgärder som matchar filtren visas.

    ![Vyfilter](./media/view-activity-logs/view-filters.png)

1. Om du vill fokusera på specifika åtgärder ändrar du filtren eller tillämpar nya. Följande bild visar till exempel ett nytt värde för **Tidsspann och** **Resurstyp är** inställt på lagringskonton.

    ![Ange filteralternativ](./media/view-activity-logs/set-filter.png)

1. Om du behöver köra frågan igen senare väljer du **Fäst aktuella filter.**

    ![Fästa filter](./media/view-activity-logs/pin-filters.png)

1. Ge filtret ett namn.

    ![Namnfilter](./media/view-activity-logs/name-filters.png)

1. Filtret är tillgängligt på instrumentpanelen. I menyn i Azure-portalen väljer du **Instrumentpanel**.

    ![Visa filter på instrumentpanelen](./media/view-activity-logs/activity-log-on-dashboard.png)

1. Från portalen kan du visa ändringar i en resurs. Gå tillbaka till standardvyn i Övervakare och välj en åtgärd som innebar att en resurs ändrades.

    ![Välj åtgärd](./media/view-activity-logs/select-operation.png)

1. Välj **Ändra historik (förhandsversion)** och välj en av de tillgängliga åtgärderna.

    ![Välj ändringshistorik](./media/view-activity-logs/select-change-history.png)

1. Ändringarna i resursen visas.

    ![Visa ändringar](./media/view-activity-logs/show-changes.png)

Mer information om ändringshistorik finns i [Hämta resursändringar.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Kör kommandot **Get-AzLog** för att hämta loggposter. Du anger ytterligare parametrar för att filtrera listan över poster. Om du inte anger någon start- och sluttid returneras poster för de senaste sju dagarna.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

I följande exempel visas hur du använder aktivitetsloggen för att undersöka åtgärder som vidtagits under en angiven tid. Start- och slutdatumen anges i ett datumformat.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Eller så kan du använda datumfunktioner för att ange datumintervallet, till exempel de senaste 14 dagarna.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Du kan leta upp de åtgärder som vidtas av en viss användare.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Du kan filtrera efter misslyckade åtgärder.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Du kan fokusera på ett fel genom att titta på statusmeddelandet för posten.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Du kan välja specifika värden för att begränsa vilka data som returneras.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

Beroende på vilken starttid du anger kan föregående kommandon returnera en lång lista över åtgärder för resursgruppen. Du kan filtrera resultaten efter det du letar efter genom att ange sökkriterier. Du kan till exempel filtrera efter typ av åtgärd.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Du kan använda Resource Graph för att se ändringshistoriken för en resurs. Mer information finns i [Hämta resursändringar.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="azure-cli"></a>Azure CLI

Om du vill hämta loggposter kör du [kommandot az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) med en offset för att ange tidsintervallet.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

I följande exempel visas hur du använder aktivitetsloggen för att undersöka åtgärder som vidtas under en angiven tid. Start- och slutdatumen anges i ett datumformat.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Du kan leta upp de åtgärder som vidtas av en viss användare, även för en resursgrupp som inte längre finns.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Du kan filtrera efter misslyckade åtgärder.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Du kan fokusera på ett fel genom att titta på statusmeddelandet för posten.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Du kan välja specifika värden för att begränsa vilka data som returneras.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

Beroende på den starttid som du anger kan föregående kommandon returnera en lång lista över åtgärder för resursgruppen. Du kan filtrera resultaten efter det du letar efter genom att ange sökkriterier. Du kan till exempel filtrera efter typ av åtgärd.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Du kan använda Resource Graph för att se ändringshistoriken för en resurs. Mer information finns i [Hämta resursändringar.](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="rest-api"></a>REST-API

REST-åtgärderna för att arbeta med aktivitetsloggen ingår i [Insights REST API](/rest/api/monitor/). Information om hur du hämtar aktivitetslogghändelser finns i [Lista hanteringshändelser i en prenumeration](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Nästa steg

* Azure-aktivitetsloggar kan användas med Power BI för att få bättre insikter om åtgärderna i din prenumeration. Se [Visa och analysera Azure-aktivitetsloggar i Power BI med mera.](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)
* Mer information om hur du ställer in säkerhetsprinciper [finns i Rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../../role-based-access-control/role-assignments-portal.md)
* Mer information om ändringarna i dina program från infrastrukturlagret ända till programdistributionen finns i [Använda Programändringsanalys i Azure Monitor](../../azure-monitor/app/change-analysis.md).
* Mer information om kommandon för att visa distributionsåtgärder finns i [Visa distributionsåtgärder.](../templates/deployment-history.md)
* Information om hur du förhindrar borttagningar på en resurs för alla användare finns i [Låsa resurser med Azure Resource Manager](lock-resources.md).
* En lista över åtgärder som är tillgängliga för varje Microsoft Azure Resource Manager provider finns i Åtgärder för [Azure-resursprovider](../../role-based-access-control/resource-provider-operations.md)
