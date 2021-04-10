---
title: Uppdatera Network Watcher-tillägget till den senaste versionen
description: Lär dig hur du uppdaterar Azure Network Watcher-tillägget till den senaste versionen.
services: virtual-machines
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: 42efe2927b4d711f7fa66a96ebd25f1a62bf654a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563616"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Uppdatera Network Watcher-tillägget till den senaste versionen

## <a name="overview"></a>Översikt

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en övervaknings-, diagnostik-och analys tjänst för nätverks prestanda som övervakar Azure-nätverk. Tillägget Network Watcher agent virtuell dator (VM) är ett krav för att samla in nätverks trafik på begäran och använda andra avancerade funktioner på virtuella Azure-datorer. Network Watcher-tillägget används av funktioner som anslutnings övervakaren, anslutnings övervakaren (för hands version), anslutnings fel sökning och paket fångst.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har Network Watcher tillägget installerat på den virtuella datorn.

## <a name="latest-version"></a>Senaste version

Den senaste versionen av Network Watcher-tillägget är för närvarande `1.4.1693.1` .

## <a name="update-your-extension-using-a-powershell-script"></a>Uppdatera tillägget med ett PowerShell-skript
Kunder med stora distributioner som behöver uppdatera flera virtuella datorer på en gång. Information om hur du uppdaterar Välj virtuella datorer manuellt finns i nästa avsnitt. 

```powershell
<#
    .SYNOPSIS
    This script will scan all VMs in the provided subscription and upgrade any out of date AzureNetworkWatcherExtensions

    .DESCRIPTION
    This script should be no-op if AzureNetworkWatcherExtensions are up to date
    Requires Azure PowerShell 4.2 or higher to be installed (e.g. Install-Module AzureRM).

    .EXAMPLE
    .\UpdateVMAgentsInSub.ps1 -SubID F4BC4873-5DAB-491E-B713-1358EF4992F2 -NoUpdate

#>
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [string] $SubID,
    [Parameter(Mandatory=$false)]
    [Switch] $NoUpdate = $false,
    [Parameter(Mandatory=$false)]
    [string] $MinVersion = "1.4.1654.1"
)


function NeedsUpdate($version)
{
    if ($version -eq $MinVersion)
    {
        return $false
    }

    $lessThan = $true;
    $versionParts = $version -split '\.';
    $minVersionParts = $MinVersion -split '\.';
    for ($i = 0; $i -lt $versionParts.Length; $i++)
    {
        if ([int]$versionParts[$i] -gt [int]$minVersionParts[$i])
        {
            $lessThan = $false;
            break;
        }
    }

    return $lessThan
}

Write-Host "Scanning all VMs in the subscription: $($SubID)"
Select-AzSubscription -SubscriptionId $SubID;
$vms = Get-AzVM;
$foundVMs = $false;
Write-Host "Starting VM search, this may take a while"

foreach ($vmName in $vms)
{
    # Get Detailed VM info
    $vm = Get-AzVM -ResourceGroupName $vmName.ResourceGroupName -Name $vmName.name -Status;
    $isWindows = $vm.OsVersion -match "Windows";
    foreach ($extension in $vm.Extensions)
    {
        if ($extension.Name -eq "AzureNetworkWatcherExtension")
        {
            if (NeedsUpdate($extension.TypeHandlerVersion))
            {
                $foundVMs = $true;
                if (-not ($NoUpdate))
                {
                    Write-Host "Found VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name) -> Updating " -NoNewline
                    Remove-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Force
                    Write-Host "... " -NoNewline
                    $type = if ($isWindows) { "NetworkWatcherAgentWindows" } else { "NetworkWatcherAgentLinux" };
                    Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -Location $vmName.Location -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type $type -typeHandlerVersion "1.4"
                    Write-Host "Done"
                }
                else
                {
                    Write-Host "Found $(if ($isWindows) {"Windows"} else {"Linux"}) VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name)"
                }
            }
        }
    }
}

if ($foundVMs)
{
    Write-Host "Finished $(if ($NoUpdate) {"searching"} else {"updating"}) out of date AzureNetworkWatcherExtension on VMs"
}
else
{
    Write-Host "All AzureNetworkWatcherExtensions up to date"
}

```

## <a name="update-your-extension-manually"></a>Uppdatera tillägget manuellt

Du måste känna till tilläggs versionen för att kunna uppdatera ditt tillägg.

### <a name="check-your-extension-version"></a>Kontrol lera tilläggs versionen

Du kan kontrol lera tilläggs versionen med hjälp av Azure Portal, Azure CLI eller PowerShell.

#### <a name="usetheazureportal"></a>Använd Azure Portal

1. Gå till rutan **tillägg** på den virtuella datorn i Azure Portal.
1. Välj **AzureNetworkWatcher** -tillägget för att se informations fönstret.
1. Leta reda på versions numret i fältet **version** .  

#### <a name="use-the-azure-cli"></a>Använda Azure CLI

Kör följande kommando från en Azure CLI-kommandotolk:

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
Leta upp **"AzureNetworkWatcherExtension"** i utdata och identifiera versions numret från fältet *"TypeHandlerVersion"* i utdata.  Observera: information om tillägget visas flera gånger i JSON-utdata. Titta under blocket "Extensions" och se det fullständiga versions numret för tillägget. 

Du bör se något som liknar följande: ![ Azure CLI-skärm bild](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>Använd PowerShell

Kör följande kommandon från en PowerShell-prompt:

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
Leta upp Azure Network Watcher-tillägget i utdata och identifiera versions numret från fältet *"TypeHandlerVersion"* i utdata.   

Du bör se något som liknar följande: ![ PowerShell-skärm bild](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>Uppdatera tillägget

Om din version är lägre än den senaste versionen som nämns ovan, uppdaterar du tillägget genom att använda något av följande alternativ.

#### <a name="option-1-use-powershell"></a>Alternativ 1: Använd PowerShell

Kör följande kommandon:

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "NetworkWatcherAgentWindows" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -ForceRerun "True"

```

Om det inte fungerar. Ta bort och installera tillägget igen med hjälp av stegen nedan. Den senaste versionen läggs till automatiskt.

Tar bort tillägget 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

Installerar tillägget igen

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>Alternativ 2: Använd Azure CLI

Framtvinga en uppgradering.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Om det inte fungerar tar du bort och installerar tillägget igen och följer de här stegen för att automatiskt lägga till den senaste versionen.

Ta bort tillägget.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Installera tillägget igen.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>Alternativ 3: starta om dina virtuella datorer

Om du har angett automatisk uppgradering till true för Network Watcher-tillägget startar du om VM-installationen till det senaste tillägget.

## <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du läsa Network Watcher tilläggs dokumentation för [Linux](./network-watcher-linux.md) eller [Windows](./network-watcher-windows.md). Du kan också kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också fil en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
