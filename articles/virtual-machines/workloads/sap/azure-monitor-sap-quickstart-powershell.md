---
title: Distribuera Azure Monitor för SAP-lösningar med Azure PowerShell
description: Distribuera Azure Monitor för SAP-lösningar med Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.devlang: azurepowershell
ms.date: 09/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b1cea4b72049386ad7a1cc3e67003861c694812c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671955"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Snabb start: Distribuera Azure Monitor för SAP-lösningar med Azure PowerShell

I den här artikeln beskrivs hur du kan skapa Azure Monitor för resurser för SAP-lösningar med PowerShell-modulen [AZ. HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) .

> [!CAUTION]
> Azure Monitor for SAP-lösningar finns för närvarande i offentlig för hands version. Förhandsversionen tillhandahålls utan serviceavtal. Den rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell](/powershell/azure/install-az-ps). Om du väljer att använda Cloud Shell, se [Översikt över Azure Cloud Shell](../../../cloud-shell/overview.md) för mer information.

> [!IMPORTANT]
> Även om **AZ. HanaOnAzure** PowerShell-modulen är i för hands version måste du installera den separat med hjälp av `Install-Module` cmdleten. När den här PowerShell-modulen blir allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj en speciell prenumeration med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resurs grupp](../../../azure-resource-manager/management/overview.md) med cmdlet: en [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resurs grupp med det angivna namnet och på den angivna platsen.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP-övervakare

Om du vill skapa en SAP-övervakare använder du cmdleten [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor) . I följande exempel skapas en SAP-Övervakare för den angivna prenumerationen, resurs gruppen och resurs namnet.

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

Om du vill hämta egenskaperna för en SAP-övervakare använder du cmdleten [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor) . I följande exempel hämtas egenskaper för en SAP-Övervakare för den angivna prenumerationen, resurs gruppen och resurs namnet.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Instans av Provider

Om du vill skapa en provider-instans använder du cmdleten [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) . I följande exempel skapas en provider-instans för den angivna prenumerationen, resurs gruppen och resurs namnet.

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

Om du vill hämta egenskaper för en provider-instans använder du cmdleten [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) . I följande exempel hämtas egenskaper för en provider-instans för den angivna prenumerationen, resurs gruppen, SapMonitor-namnet och resurs namnet.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Rensa resurser

Om resurserna som skapas i den här artikeln inte behövs kan du ta bort dem genom att köra följande exempel.

### <a name="delete-the-provider-instance"></a>Ta bort Provider-instansen

Om du vill ta bort en provider-instans använder du cmdleten [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) . I följande exempel tar bort en provider-instans för den angivna prenumerationen, resurs gruppen, SapMonitor namn och resurs namnet.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Ta bort SAP-övervakaren

Om du vill ta bort en SAP-övervakare använder du cmdleten [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor) . I följande exempel tar bort en SAP-Övervakare för den angivna prenumerationen, resurs gruppen och övervakarens namn.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

> [!CAUTION]
> I följande exempel tas den angivna resurs gruppen och alla resurser som ingår i den bort.
> Om resurser utanför omfånget för den här artikeln finns i den angivna resurs gruppen kommer de också att tas bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Monitor för SAP-lösningar](azure-monitor-overview.md).