---
title: Aktivera VM-tillägg med Azure CLI
description: Den här artikeln beskriver hur du distribuerar tillägg för virtuella datorer till Azure Arc-aktiverade servrar som körs i hybridmolnmiljöer med hjälp av Azure CLI.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 25e75ede30139201789cd86e6ebddda09a664eb4
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388745"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Aktivera Azure VM-tillägg med hjälp av Azure CLI

Den här artikeln visar hur du distribuerar och avinstallerar VM-tillägg som stöds av Azure Arc-aktiverade servrar, till en Linux- eller Windows-hybriddator med hjälp av Azure CLI.

> [!NOTE]
> Azure Arc-aktiverade servrar stöder inte distribution och hantering av VM-tillägg till virtuella Azure-datorer. För virtuella Azure-datorer kan du läsa följande [översiktsartikel om VM-tillägg.](../../virtual-machines/extensions/overview.md)

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

ConnectedMachine-kommandon levereras inte som en del av Azure CLI. Innan du använder Azure CLI för att hantera VM-tillägg på hybridservern som hanteras av Arc-aktiverade servrar måste du läsa in ConnectedMachine-tillägget. Kör följande kommando för att hämta det:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Aktivera tillägg

Om du vill aktivera ett VM-tillägg på din Arc-aktiverade server använder du [az connectedmachine extension create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) med `--machine-name` `--extension-name` parametrarna , , , `--location` , och `--type` `settings` `--publisher` .

I följande exempel aktiveras Log Analytics VM-tillägget på en Arc-aktiverad server:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

I följande exempel aktiveras tillägget för anpassat skript på en Arc-aktiverad server:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

I följande exempel aktiveras Key Vault VM-tillägg (förhandsversion) på en Arc-aktiverad server:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Lista över installerade tillägg

Om du vill hämta en lista över VM-tilläggen på din Arc-aktiverade server använder du [az connectedmachine extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) med `--machine-name` `--resource-group` parametrarna och .

Exempel:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Som standard är utdata från Azure CLI-kommandon i JSON (JavaScript Object Notation). Om du vill ändra standardutdata till en lista eller tabell använder du till exempel [az configure --output](/cli/azure/reference-index). Du kan också lägga `--output` till ett kommando för en enda ändring i utdataformat.

I följande exempel visas partiella JSON-utdata från `az connectedmachine extension -list` kommandot:

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Ta bort ett installerat tillägg

Om du vill ta bort ett installerat VM-tillägg på din Arc-aktiverade server använder du [az connectedmachine extension delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) med `--extension-name` `--machine-name` parametrarna , `--resource-group` och .

Om du till exempel vill ta bort Log Analytics VM-tillägget för Linux kör du följande kommando:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

- Du kan distribuera, hantera och ta bort [VM-tillägg med hjälp Azure PowerShell](manage-vm-extensions-powershell.md), från [Azure Portal](manage-vm-extensions-portal.md)eller [Azure Resource Manager mallar](manage-vm-extensions-template.md).

- Felsökningsinformation finns i guiden Felsöka [VM-tillägg.](troubleshoot-vm-extensions.md)

- Mer information om kommandona finns [i artikeln](/cli/azure/ext/connectedmachine/connectedmachine/extension) Översikt över azure CLI VM-tillägg.
