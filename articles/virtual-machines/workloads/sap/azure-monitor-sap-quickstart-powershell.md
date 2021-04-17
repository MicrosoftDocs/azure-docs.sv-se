---
title: Distribuera Azure Monitor för SAP-lösningar med Azure PowerShell
description: Distribuera Azure Monitor för SAP-lösningar med Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.date: 09/08/2020
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 0af2611bada7a9aad206ce7463ef72ec930c89a2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538691"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Snabbstart: Distribuera Azure Monitor för SAP-lösningar med Azure PowerShell

Den här artikeln beskriver hur du kan skapa Azure Monitor för SAP-lösningar resurser med hjälp av [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell-modulen.

> [!CAUTION]
> Azure Monitor för SAP-lösningar är för närvarande i offentlig förhandsversion. Förhandsversionen tillhandahålls utan serviceavtal. Den rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar Az PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Mer information om hur du installerar Az PowerShell-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). Om du väljer att använda Cloud Shell finns [mer information i Översikt Azure Cloud Shell](../../../cloud-shell/overview.md) information.

> [!IMPORTANT]
> När **PowerShell-modulen Az.HanaOnAzure** är i förhandsversion måste du installera den separat med hjälp av `Install-Module` cmdleten . När den här PowerShell-modulen blir allmänt tillgänglig blir den en del av framtida versioner av Az PowerShell-modulen och blir tillgänglig inbyggt Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj en specifik prenumeration med hjälp [av cmdleten Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../../../azure-resource-manager/management/overview.md) med hjälp [av cmdleten New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resursgrupp med det angivna namnet och på den angivna platsen.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP-övervakare

Om du vill skapa en SAP-övervakare använder du cmdleten [New-AzSapMonitor.](/powershell/module/az.hanaonazure/new-azsapmonitor) I följande exempel skapas en SAP-övervakare för den angivna prenumerationen, resursgruppen och resursnamnet.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Om du vill hämta egenskaperna för en SAP-övervakare använder du cmdleten [Get-AzSapMonitor.](/powershell/module/az.hanaonazure/get-azsapmonitor) I följande exempel hämtar egenskaperna för en SAP-övervakare för den angivna prenumerationen, resursgruppen och resursnamnet.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Providerinstans

Om du vill skapa en providerinstans använder du cmdleten [New-AzSapMonitorProviderInstance.](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) I följande exempel skapas en providerinstans för den angivna prenumerationen, resursgruppen och resursnamnet.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Om du vill hämta egenskaper för en providerinstans använder du cmdleten [Get-AzSapMonitorProviderInstance.](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) I följande exempel hämtar egenskaperna för en providerinstans för den angivna prenumerationen, resursgruppen, SapMonitor-namnet och resursnamnet.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Rensa resurser

Om resurserna som skapades i den här artikeln inte behövs kan du ta bort dem genom att köra följande exempel.

### <a name="delete-the-provider-instance"></a>Ta bort providerinstansen

Om du vill ta bort en providerinstans använder du cmdleten [Remove-AzSapMonitorProviderInstance.](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) I följande exempel tas en providerinstans bort för den angivna prenumerationen, resursgruppen, SapMonitor-namnet och resursnamnet.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Ta bort SAP-övervakaren

Om du vill ta bort en SAP-övervakare använder du cmdleten [Remove-AzSapMonitor.](/powershell/module/az.hanaonazure/remove-azsapmonitor) I följande exempel tas en SAP-övervakare bort för den angivna prenumerationen, resursgruppen och övervakarens namn.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

> [!CAUTION]
> I följande exempel tas den angivna resursgruppen och alla resurser som ingår i den bort.
> Om det finns resurser utanför omfånget för den här artikeln i den angivna resursgruppen tas de också bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Monitor för SAP-lösningar](azure-monitor-overview.md).
