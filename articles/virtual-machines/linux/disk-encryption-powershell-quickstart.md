---
title: Skapa och kryptera en virtuell Linux-dator med Azure PowerShell
description: I den här snabb starten får du lära dig hur du använder Azure PowerShell för att skapa och kryptera en virtuell Linux-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1833b25554df53a480af9a8fc643755dbc2bbdb8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564687"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Snabb start: skapa och kryptera en virtuell Linux-dator i Azure med Azure PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här snabb starten visar hur du använder modulen Azure PowerShell för att skapa en virtuell Linux-dator (VM), skapar en Key Vault för lagringen av krypterings nycklar och krypterar den virtuella datorn. I den här snabb starten används Ubuntu 16,04 LTS Marketplace-avbildningen från kanoniskt format och en VM Standard_D2S_V3 storlek. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell Azure-dator med [New-AzVM](/powershell/module/az.compute/new-azvm)och skicka den till den virtuella dator konfigurations objekt som du skapade ovan.

```powershell-interactive
$cred = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:18.04-LTS:latest -Size Standard_D2S_V3
```

Det tar några minuter för den virtuella datorn att distribueras. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Skapa en Key Vault konfigurerad för krypterings nycklar

Azure Disk Encryption lagrar sin krypterings nyckel i en Azure Key Vault. Skapa en Key Vault med [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Om du vill aktivera Key Vault att lagra krypterings nycklar använder du parametern-EnabledForDiskEncryption.

> [!Important]
> Varje nyckel valv måste ha ett namn som är unikt i Azure. I exemplen nedan ersätter du <ditt-Unique-nyckel-valv> med det namn som du väljer.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

Kryptera den virtuella datorn med [set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension kräver vissa värden från Key Vault-objektet. Du kan hämta dessa värden genom att skicka det unika namnet på nyckel valvet till [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Efter några minuter kommer processen att returnera följande:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Du kan kontrol lera krypterings processen genom att köra [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

När kryptering är aktiverat visas följande i utdata som returneras:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du cmdleten [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en virtuell dator, skapade en Key Vault som aktiverades för krypterings nycklar och krypterade den virtuella datorn.  Fortsätt till nästa artikel om du vill lära dig mer om Azure Disk Encryption för virtuella Linux-datorer.

> [!div class="nextstepaction"]
> [Översikt över Azure Disk Encryption](disk-encryption-overview.md)
