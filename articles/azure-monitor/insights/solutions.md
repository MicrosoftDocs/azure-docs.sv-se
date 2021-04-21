---
title: Övervakningslösningar i Azure Monitor | Microsoft Docs
description: Övervakningslösningar i Azure Monitor är en samling logik-, visualiserings- och datainsamlingsregler som tillhandahåller mått som pivoteras runt ett visst problemområde.  Den här artikeln innehåller information om hur du installerar och använder övervakningslösningar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 8a18a47331eb5d4a9ed5578cca320beef5e0ba45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766995"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Övervakningslösningar i Azure Monitor

Övervakningslösningar i Azure Monitor en analys av driften av ett visst Azure-program eller en viss Azure-tjänst. Den här artikeln innehåller en kort översikt över övervakningslösningar i Azure och information om hur du använder och installerar dem. Du kan lägga till övervakningslösningar för Azure Monitor program och tjänster som du använder. De är vanligtvis tillgängliga utan kostnad, men samlar in data som kan anropa användningsavgifter.

## <a name="use-monitoring-solutions"></a>Använda övervakningslösningar

Sidan Översikt **för lösningar** i Azure Monitor visar en panel för varje lösning som är installerad på en Log Analytics-arbetsyta. Öppna den här sidan genom att **gå Azure Monitor** i [Azure Portal](https://ms.portal.azure.com). Under menyn **Insikter** väljer du **Mer för** att öppna **Insights Hub** och klickar sedan på **Log Analytics-arbetsytor.**

[![Insights Hub](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


Använd listrutorna överst på skärmen för att ändra arbetsytan eller det tidsperiod som används för panelerna. Klicka på panelen för en lösning för att öppna dess vy som innehåller mer detaljerad analys av insamlade data.

[![Skärmbild som visar Azure Portal med Lösningar valt och lösningar som visas i fönstret Lösningar.](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

Övervakningslösningar kan innehålla flera typer av Azure-resurser och du kan visa alla resurser som ingår i en lösning precis som andra resurser. Loggfrågor som ingår i lösningen visas till exempel under **Lösningsfrågor i** [Frågeutforskaren.](../logs/log-analytics-tutorial.md) Du kan använda dessa frågor när du utför ad hoc-analys [med loggfrågor](../logs/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Lista över installerade övervakningslösningar

### <a name="portal"></a>[Portal](#tab/portal)

Använd följande procedur för att visa en lista över de övervakningslösningar som är installerade i din prenumeration.

1. Gå till [Azure-portalen](https://ms.portal.azure.com). Sök efter och välj **Lösningar**.
1. Lösningar som är installerade på alla dina arbetsytor visas. Namnet på lösningen följs av namnet på den arbetsyta som den är installerad på.
1. Använd listrutorna överst på skärmen för att filtrera efter prenumeration eller resursgrupp.

![Visa en lista över alla lösningar](media/solutions/list-solutions-all.png)

Klicka på namnet på en lösning för att öppna dess sammanfattningssida. Den här sidan visar alla vyer som ingår i lösningen och innehåller olika alternativ för själva lösningen och dess arbetsyta. Visa sammanfattningssidan för en lösning genom att använda någon av procedurerna ovan för att lista lösningar och klicka sedan på namnet på lösningen.

![Lösningsegenskaper](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [az monitor log-analytics solution list för](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-list) att visa en lista över de övervakningslösningar som är installerade i din prenumeration.   Innan du kör `list` kommandot följer du förutsättningarna som finns i [Installera en övervakningslösning](#install-a-monitoring-solution).

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Använd [cmdleten Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) för att visa en lista över de övervakningslösningar som är installerade i din prenumeration. Innan du kör de här kommandona följer du förutsättningarna i [Installera en övervakningslösning](#install-a-monitoring-solution).

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Installera en övervakningslösning

### <a name="portal"></a>[Portal](#tab/portal)

Övervakningslösningar från Microsoft och partner är tillgängliga från [Azure Marketplace](https://azuremarketplace.microsoft.com). Du kan söka efter tillgängliga lösningar och installera dem med hjälp av följande procedur. När du installerar en lösning måste du välja en [Log Analytics-arbetsyta](../logs/manage-access.md) där lösningen ska installeras och var dess data ska samlas in.

1. I listan [över lösningar för din prenumeration klickar du](#list-installed-monitoring-solutions)på Lägg **till**.
1. Bläddra eller sök efter en lösning. Du kan också bläddra bland lösningar från [den här söklänken](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Leta upp den övervakningslösning som du vill använda och läs igenom dess beskrivning.
1. Klicka **på Skapa** för att starta installationsprocessen.
1. När installationsprocessen startar uppmanas du att ange Log Analytics-arbetsytan och ange den konfiguration som krävs för lösningen.

![Installera en lösning](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installera en lösning från communityn

Medlemmar i communityn kan skicka hanteringslösningar till Azure-snabbstartsmallar. Du kan installera dessa lösningar direkt eller ladda ned dem mallar för senare installation.

1. Följ processen som beskrivs i [Log Analytics-arbetsytan och Automation-kontot för att](#log-analytics-workspace-and-automation-account) länka en arbetsyta och ett konto.
2. Gå till [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
3. Sök efter en lösning som du är intresserad av.
4. Välj lösningen i resultatet för att visa dess information.
5. Klicka på **knappen Distribuera till Azure.**
6. Du uppmanas att ange information som resursgrupp och plats utöver värden för alla parametrar i lösningen.
7. Klicka **på Köp** för att installera lösningen.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Förbered din miljö

1. Installera Azure CLI

   Du måste installera [Azure CLI innan du kör](/cli/azure/install-azure-cli) CLI-referenskommandon.  Om du vill kan du också använda Azure Cloud Shell för att slutföra stegen i den här artikeln.  Azure Cloud Shell är en interaktiv gränssnittsmiljö som du använder via webbläsaren.  Börja Cloud Shell med någon av följande metoder:

   - Öppna Cloud Shell genom att gå till [https://shell.azure.com](https://shell.azure.com)

   - Välj knappen **Cloud Shell** på menyraden i det övre högra hörnet i [Azure Portal](https://portal.azure.com)

1. Logga in.

   Om du använder en lokal installation av CLI loggar du in med kommandot [az login.](/cli/azure/reference-index#az_login)  Slutför autentiseringsprocessen genom att följa anvisningarna i terminalen.

    ```azurecli
    az login
    ```

1. Installera `log-analytics-solution` tillägget

   Kommandot `log-analytics-solution` är en experimentell utökning av Grundläggande Azure CLI. Läs mer om tilläggsreferenser [i Använda tillägg med Azure CLI.](/cli/azure/azure-cli-extensions-overview?)

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   Följande varning förväntas.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Installera en lösning med Azure CLI

När du installerar en lösning måste du välja en [Log Analytics-arbetsyta](../logs/manage-access.md) där lösningen ska installeras och var dess data ska samlas in.  Med Azure CLI kan du hantera arbetsytor med hjälp av [referenskommandona az monitor log-analytics workspace.](/cli/azure/monitor/log-analytics/workspace)  Följ processen som beskrivs i [Log Analytics-arbetsytan och Automation-kontot för att](#log-analytics-workspace-and-automation-account) länka en arbetsyta och ett konto.

Använd [az monitor log-analytics solution create för](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution) att installera en övervakningslösning.  Parametrar inom hakparenteser är valfria.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Här är ett kodexempel som skapar en Log Analytics-lösning för planprodukten i OMSGallery/Containers.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>Förbered din miljö

1. Installera Azure PowerShell

   Du måste installera [Azure PowerShell innan](/powershell/azure/install-az-ps) du kör Azure PowerShell-referenskommandon. Om du vill kan du också använda Azure Cloud Shell för att slutföra stegen i den här artikeln. Azure Cloud Shell är en interaktiv gränssnittsmiljö som du använder via webbläsaren. Börja Cloud Shell med någon av följande metoder:

   - Öppna Cloud Shell genom att gå till [https://shell.azure.com](https://shell.azure.com)

   - Välj knappen **Cloud Shell** på menyraden i det övre högra hörnet i [Azure Portal](https://portal.azure.com)

   > [!IMPORTANT]
   > När **PowerShell-modulen Az.MonitoringSolutions** är i förhandsversion måste du installera den separat med hjälp av `Install-Module` cmdleten . När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Logga in.

   Om du använder en lokal installation av PowerShell loggar du in med cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Slutför autentiseringsprocessen genom att följa stegen som visas i PowerShell.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Installera en lösning med Azure PowerShell

När du installerar en lösning måste du välja en [Log Analytics-arbetsyta](../logs/manage-access.md) där lösningen ska installeras och var dess data ska samlas in. Med Azure PowerShell hanterar du arbetsytor med hjälp av cmdletarna i [PowerShell-modulen Az.MonitoringSolutions.](/powershell/module/az.monitoringsolutions) Följ processen som beskrivs i [Log Analytics-arbetsytan och Automation-kontot för att](#log-analytics-workspace-and-automation-account) länka en arbetsyta och ett konto.

Använd [cmdleten New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) för att installera en övervakningslösning. Parametrar inom hakparenteser är valfria.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

I följande exempel skapas en lösning för övervakning av logganalys för Log Analytics-arbetsytan.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-arbetsyta och Automation-konto

Alla övervakningslösningar kräver en [Log Analytics-arbetsyta](../logs/manage-access.md) för att lagra data som samlas in av lösningen och som värd för dess loggsökningar och vyer. Vissa lösningar kräver också att [ett Automation-konto](../../automation/automation-security-overview.md) innehåller runbooks och relaterade resurser. Arbetsytan och kontot måste uppfylla följande krav.

* Varje installation av en lösning kan bara använda en Log Analytics-arbetsyta och ett Automation-konto. Du kan installera lösningen separat på flera arbetsytor.
* Om en lösning kräver ett Automation-konto måste Log Analytics-arbetsytan och Automation-kontot länkas till varandra. En Log Analytics-arbetsyta kan bara länkas till ett Automation-konto och ett Automation-konto kan bara länkas till en Log Analytics-arbetsyta.

När du installerar en lösning via Azure Marketplace uppmanas du att ange en arbetsyta och ett Automation-konto. Länken mellan dem skapas om de inte redan är länkade.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Kontrollera länken mellan en Log Analytics-arbetsyta och ett Automation-konto

Du kan verifiera länken mellan en Log Analytics-arbetsyta och ett Automation-konto med hjälp av följande procedur.

1. Välj Automation-kontot i Azure Portal.
1. Bläddra till avsnittet **Relaterade resurser** på menyn och välj **Länkad arbetsyta.**
1. Om arbetsytan **är** länkad till ett Automation-konto visar den här sidan arbetsytan som den är länkad till. Om du väljer namnet på arbetsytan i listan omdirigeras du till översiktssidan för arbetsytan.

## <a name="remove-a-monitoring-solution"></a>Ta bort en övervakningslösning

### <a name="portal"></a>[Portal](#tab/portal)

Om du vill ta bort en installerad lösning med hjälp av portalen letar du upp den [i listan över installerade lösningar.](#list-installed-monitoring-solutions) Klicka på namnet på lösningen för att öppna sammanfattningssidan och klicka sedan på Ta **bort.**

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ta bort en installerad lösning med hjälp av Azure CLI använder du [kommandot az monitor log-analytics solution delete.](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-delete)

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Om du vill ta bort en installerad Azure PowerShell med hjälp av cmdleten [Remove-AzMonitorLogAnalyticsSolution.](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution)

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Nästa steg

* Hämta en [lista över övervakningslösningar från Microsoft](../monitor-reference.md).
* Lär dig hur [du skapar frågor för](../logs/log-query-overview.md) att analysera data som samlas in av övervakningslösningar.
* Se alla [Azure CLI-kommandon för Azure Monitor](/cli/azure/azure-cli-reference-for-monitor).