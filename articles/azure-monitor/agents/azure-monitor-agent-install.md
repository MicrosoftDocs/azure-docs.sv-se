---
title: Installera Azure Monitor Agent
description: Alternativ för att installera Azure Monitor Agent (AMA) på virtuella Azure-datorer och Azure Arc-aktiverade servrar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: db9ad08f9a939a22e1e0e1cfba0537e6356394ed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731543"
---
# <a name="install-the-azure-monitor-agent-preview"></a>Installera Azure Monitor Agent (förhands granskning)
Den här artikeln innehåller de olika alternativ som för närvarande är tillgängliga för att installera [Azure Monitor Agent](azure-monitor-agent-overview.md) på både virtuella Azure-datorer och Azure Arc-aktiverade servrar, samt alternativen för att skapa [associationer med data insamlings regler](data-collection-rule-azure-monitor-agent.md) som definierar vilka data som agenten ska samla in.

## <a name="prerequisites"></a>Förutsättningar
Följande förutsättningar måste vara uppfyllda innan du installerar Azure Monitor agenten.

- [Hanterad system identitet](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) måste vara aktiverat på virtuella Azure-datorer. Detta krävs inte för Azure Arc-aktiverade servrar. System identiteten aktive ras automatiskt om agenten installeras som en del av processen för att [skapa och tilldela en data insamlings regel med hjälp av Azure Portal](#install-with-azure-portal).
- [AzureResourceManager-tjänst tag gen](../../virtual-network/service-tags-overview.md) måste vara aktive rad på det virtuella nätverket för den virtuella datorn.

> [!IMPORTANT]
> Den Azure Monitor-agenten stöder för närvarande inte nätverks-proxyservrar.

## <a name="virtual-machine-extension-details"></a>Tilläggs information för virtuell dator
Azure Monitor agenten implementeras som ett [Azure VM-tillägg](../../virtual-machines/extensions/overview.md) med informationen i följande tabell. Den kan installeras med någon av metoderna för att installera tillägg för virtuella datorer, inklusive de som beskrivs i den här artikeln.

| Egenskap | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure. Monitor  | Microsoft. Azure. Monitor |
| Typ      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |


## <a name="install-with-azure-portal"></a>Installera med Azure Portal
Om du vill installera Azure Monitor agenten med hjälp av Azure Portal följer du processen för att [skapa en data insamlings regel](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) i Azure Portal. På så sätt kan du associera data insamlings regeln med en eller flera virtuella Azure-datorer eller Azure Arc-aktiverade servrar. Agenten installeras på någon av de virtuella datorerna som inte redan har den.


## <a name="install-with-resource-manager-template"></a>Installera med Resource Manager-mall
Du kan använda Resource Manager-mallar för att installera Azure Monitor Agent på virtuella Azure-datorer och på Azure Arc-aktiverade servrar och för att skapa en Association med data insamlings regler. Du måste skapa en data insamlings regel innan du skapar associationen.

Hämta exempel mallar för att installera agenten och skapa associationen från följande: 

- [Mall för att installera Azure Monitor Agent (Azure och Azure-båge)](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Mall för att skapa Association med data insamlings regel](./resource-manager-data-collection-rules.md)

Installera mallarna med [valfri distributions metod för Resource Manager-mallar](../../azure-resource-manager/templates/deploy-powershell.md) , till exempel följande kommandon.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Installera med PowerShell
Du kan installera Azure Monitor agenten på virtuella Azure-datorer och på Azure Arc-aktiverade servrar med hjälp av PowerShell-kommandot för att lägga till ett tillägg för virtuell dator. 

### <a name="azure-virtual-machines"></a>Virtuella Azure-datorer
Använd följande PowerShell-kommandon för att installera Azure Monitor-agenten på virtuella Azure-datorer.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
---

### <a name="azure-arc-enabled-servers"></a>Azure Arc-aktiverade servrar
Använd följande PowerShell-kommandon för att installera Azure Monitor Agent onAzure-Arc-aktiverade servrar.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Azure CLI
Du kan installera Azure Monitor-agenten på virtuella Azure-datorer och på Azure Arc-aktiverade servrar med Azure CLI-kommandot för att lägga till ett tillägg för virtuell dator. 

### <a name="azure-virtual-machines"></a>Virtuella Azure-datorer
Använd följande CLI-kommandon för att installera Azure Monitor-agenten på virtuella Azure-datorer.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Azure Arc-aktiverade servrar
Använd följande CLI-kommandon för att installera Azure Monitor Agent onAzure-Arc-aktiverade servrar.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>Nästa steg

- [Skapa en data insamlings regel](data-collection-rule-azure-monitor-agent.md) för att samla in data från agenten och skicka den till Azure Monitor.
