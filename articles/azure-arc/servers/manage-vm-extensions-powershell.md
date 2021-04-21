---
title: Aktivera VM-tillägg med Azure PowerShell
description: Den här artikeln beskriver hur du distribuerar tillägg för virtuella datorer till Azure Arc-aktiverade servrar som körs i hybridmolnmiljöer med Azure PowerShell.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d5723655b61040c7ddf99e5f11488fff379d96a0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832886"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Aktivera Azure VM-tillägg med Azure PowerShell

Den här artikeln visar hur du distribuerar och avinstallerar Azure VM-tillägg som stöds av Azure Arc-aktiverade servrar till en Linux- eller Windows-hybriddator med Azure PowerShell.

> [!NOTE]
> Azure Arc-aktiverade servrar stöder inte distribution och hantering av VM-tillägg till virtuella Azure-datorer. För virtuella Azure-datorer kan du läsa följande [översiktsartikel om VM-tillägg.](../../virtual-machines/extensions/overview.md)

## <a name="prerequisites"></a>Förutsättningar

- En dator med Azure PowerShell. Anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).

Innan du Azure PowerShell hantera VM-tillägg på hybridservern som hanteras av Arc-aktiverade servrar måste du installera `Az.ConnectedMachine` modulen. Kör följande kommando på den Arc-aktiverade servern:

`Install-Module -Name Az.ConnectedMachine`.

När installationen är klar returneras följande meddelande:

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Aktivera tillägg

Om du vill aktivera ett VM-tillägg på din Arc-aktiverade server använder du [parametrarna New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) med parametrarna `-Name` , , , , , - och `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` `ExtensionType` `-Settings` .

I följande exempel aktiveras Log Analytics VM-tillägget på en Arc-aktiverad Linux-server:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Om du vill aktivera Log Analytics VM-tillägget på en Arc-aktiverad Windows-server ändrar du värdet för `-ExtensionType` parametern `"MicrosoftMonitoringAgent"` till i föregående exempel.

I följande exempel aktiveras tillägget för anpassat skript på en Arc-aktiverad server:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Key Vault VM-tillägg (förhandsversion)

> [!WARNING]
> PowerShell-klienter lägger `\` ofta `"` till i settings.js, vilket leder till akvvm_service misslyckas med felet: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

I följande exempel aktiveras Key Vault VM-tillägg (förhandsversion) på en Arc-aktiverad server:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>Lista över installerade tillägg

Om du vill hämta en lista över VM-tilläggen på din Arc-aktiverade server använder du [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) med `-MachineName` `-ResourceGroupName` parametrarna och .

Exempel:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Ta bort ett installerat tillägg

Om du vill ta bort ett installerat VM-tillägg på din Arc-aktiverade server använder du [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) med `-Name` parametrarna och `-MachineName` `-ResourceGroupName` .

Om du till exempel vill ta bort Log Analytics VM-tillägget för Linux kör du följande kommando:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Nästa steg

- Du kan distribuera, hantera och ta bort VM-tillägg med hjälp av [Azure CLI](manage-vm-extensions-cli.md) [från Azure Portal](manage-vm-extensions-portal.md)eller Azure Resource Manager [mallar](manage-vm-extensions-template.md).

- Felsökningsinformation finns i felsökningsguiden [för VM-tillägg.](troubleshoot-vm-extensions.md)
