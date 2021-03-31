---
title: Snabb start – Resource Manager-mall VM-säkerhetskopiering
description: Lär dig hur du säkerhetskopierar virtuella datorer med Azure Resource Manager-mall
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: e64aa10d8ddadd367d04b2b480770a99f3ece1dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88826539"
---
# <a name="quickstart-back-up-a-virtual-machine-in-azure-with-an-arm-template"></a>Snabb start: säkerhetskopiera en virtuell dator i Azure med en ARM-mall

[Azure Backup](backup-overview.md) säkerhetskopierar lokala datorer och appar och virtuella Azure-datorer. Den här artikeln visar hur du säkerhetskopierar en virtuell Azure-dator med en Azure Resource Manager-mall (ARM-mall) och Azure PowerShell. Den här snabb starten fokuserar på processen att distribuera en ARM-mall för att skapa ett Recovery Services-valv. Mer information om hur du utvecklar ARM-mallar finns i [Azure Resource Manager-dokumentationen](../azure-resource-manager/index.yml) och [referensen till mallen](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ett [Recovery Services valv](backup-azure-recovery-services-vault-overview.md) är en logisk behållare som lagrar säkerhetskopierade data för skyddade resurser, till exempel virtuella Azure-datorer. När ett säkerhets kopierings jobb körs skapas en återställnings punkt i Recovery Services valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt. Alternativt kan du säkerhetskopiera en virtuell dator med [Azure PowerShell](./quick-backup-vm-powershell.md), [Azure CLI](quick-backup-vm-cli.md)eller i [Azure Portal](quick-backup-vm-portal.md).

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-create-vm-and-configure-backup%2Fazuredeploy.json)

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Med den här mallen kan du distribuera enkel virtuell Windows-dator och Recovery Services valv som kon figurer ATS med DefaultPolicy för skydd.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json":::

De resurser som definieras i mallen är:

- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. RecoveryServices/valv**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft. RecoveryServices/valv/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>Distribuera mallen

Om du vill distribuera mallen väljer du **prova** att öppna Azure Cloud Shell och klistrar in följande PowerShell-skript i Shell-fönstret. Om du vill klistra in koden högerklickar du på Shell-fönstret och väljer **Klistra in**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Azure PowerShell används för att distribuera ARM-mallen i den här snabb starten. [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)och rest- [API: et](../azure-resource-manager/templates/deploy-rest.md) kan också användas för att distribuera mallar.

## <a name="validate-the-deployment"></a>Verifiera distributionen

### <a name="start-a-backup-job"></a>Starta ett säkerhetskopieringsjobb

Mallen skapar en virtuell dator och aktiverar den virtuella datorn igen. När du har distribuerat mallen måste du starta ett säkerhets kopierings jobb. Mer information finns i [starta ett säkerhets kopierings jobb](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Övervaka säkerhetskopieringen

Information om hur du övervakar säkerhets kopierings jobbet finns i [övervaka säkerhets kopierings jobbet](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver säkerhetskopiera den virtuella datorn kan du rensa den.

- Om du vill prova att återställa den virtuella datorn hoppar du över rensningen.
- Om du använde en befintlig virtuell dator kan du hoppa över den slutliga cmdleten [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) så att du lämnar resurs gruppen och den virtuella datorn på plats.

Inaktivera skydd, ta bort återställnings punkterna och valvet. Ta sedan bort resurs gruppen och tillhör ande virtuella dator resurser enligt följande:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett Recovery Services-valv, aktiverat skydd på en virtuell dator och skapat den första återställningspunkten.

- [Lär dig hur](tutorial-backup-vm-at-scale.md) du säkerhetskopierar virtuella datorer i Azure Portal.
- [Lär dig hur](tutorial-restore-disk.md) du snabbt återställer en virtuell dator
- [Lär dig hur](../azure-resource-manager/templates/template-tutorial-create-first-template.md) du skapar arm-mallar.
