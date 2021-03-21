---
title: Skapa och kryptera en virtuell Windows-dator med Azure PowerShell
description: I den här snabb starten får du lära dig hur du använder Azure PowerShell för att skapa och kryptera en virtuell Windows-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4aed2ce182e535ebb60eae0007353c9c7bddef78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555269"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Snabb start: skapa och kryptera en virtuell Windows-dator i Azure med PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här snabb starten visar hur du använder Azure PowerShell-modulen för att skapa en virtuell Windows-dator (VM), skapar en Key Vault för lagringen av krypterings nycklar och krypterar den virtuella datorn. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell Azure-dator med [New-AzVM](/powershell/module/az.compute/new-azvm). Du måste ange autentiseringsuppgifter för cmdleten. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

Det tar några minuter för den virtuella datorn att distribueras. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Skapa en Key Vault konfigurerad för krypterings nycklar

Azure Disk Encryption lagrar sin krypterings nyckel i en Azure Key Vault. Skapa en Key Vault med [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Om du vill aktivera Key Vault att lagra krypterings nycklar använder du parametern-EnabledForDiskEncryption.

> [!Important]
> Varje Key Vault måste ha ett unikt namn. I följande exempel skapas en Key Vault med namnet *myKV*, men du måste namnge något annat.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

Kryptera den virtuella datorn med [set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension kräver vissa värden från Key Vault-objektet. Du kan hämta dessa värden genom att skicka det unika namnet på nyckel valvet till [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Efter några minuter kommer processen att returnera följande:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Du kan kontrol lera krypterings processen genom att köra [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

När kryptering är aktiverat visas följande i utdata som returneras:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du cmdleten [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en virtuell dator, skapade en Key Vault som aktiverades för krypterings nycklar och krypterade den virtuella datorn.  Gå vidare till nästa artikel om du vill veta mer om Azure Disk Encryption-förhandskrav för virtuella IaaS-datorer.

> [!div class="nextstepaction"]
> [Översikt över Azure Disk Encryption](disk-encryption-overview.md)
