---
title: Kryptera diskar för Azures skalnings uppsättningar med Azure PowerShell
description: Lär dig hur du använder Azure PowerShell för att kryptera VM-instanser och anslutna diskar i en skalnings uppsättning för virtuella Windows-datorer
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 59b015232fcfe2fd98e1b9f28c2eb4fa86606049
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519579"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Kryptera OS och anslutna data diskar i en skalnings uppsättning för virtuella datorer med Azure PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript.  Den här artikeln visar hur du använder Azure PowerShell för att skapa och kryptera en skalnings uppsättning för virtuella datorer. Mer information om hur du tillämpar Azure Disk Encryption på en skalnings uppsättning för virtuella datorer finns i [Azure Disk Encryption för Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Skapa en Azure Key Vault som är aktive rad för disk kryptering

Azure Key Vault kan lagra nycklar, hemligheter eller lösen ord som gör det möjligt att på ett säkert sätt implementera dem i dina program och tjänster. Krypterings nycklar lagras i Azure Key Vault med hjälp av program varu skydd, eller så kan du importera eller generera nycklar i HSM: er (Hardware Security modules) som är certifierade enligt standarden FIPS 140-2 nivå 2. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behåller kontrollen över dessa kryptografiska nycklar och kan granska deras användning.

Skapa en Key Vault med [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Ange parametern *EnabledForDiskEncryption* om du vill tillåta att Key Vault används för disk kryptering. I följande exempel definieras variabler för resurs grupp namn, Key Vault namn och plats. Ange ett unikt Key Vault namn:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Använd en befintlig Key Vault

Det här steget krävs endast om du har ett befintligt Key Vault som du vill använda med disk kryptering. Hoppa över det här steget om du har skapat ett Key Vault i föregående avsnitt.

Du kan aktivera en befintlig Key Vault i samma prenumeration och region som skalnings uppsättningen för disk kryptering med [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Definiera namnet på din befintliga Key Vault i *$vaultName* -variabeln på följande sätt:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Först anger du ett administratörsanvändarnamn och lösenord för virtuella datorer med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa en VM-skalningsuppsättning med [New-AzVmss](/powershell/module/az.compute/new-azvmss). För att distribuera trafik till flera virtuella datorinstanser så skapas även en lastbalanserare. Lastbalanseraren innehåller regler för att distribuera trafik på TCP-port 80 och för att tillåta trafik för fjärrskrivbordet på TCP-port 3389 och PowerShell-fjärrkommunikation på TCP-port 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Aktivera kryptering

Om du vill kryptera VM-instanser i en skalnings uppsättning får du först lite information om Key Vault-URI och resurs-ID med [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Dessa variabler används för att starta krypterings processen med [set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

När du uppmanas till det skriver du *y* för att fortsätta disk krypterings processen på de virtuella dator instanserna för skalnings uppsättningen

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Aktivera kryptering med KEK för att omsluta nyckeln

Du kan också använda en nyckel krypterings nyckel för ökad säkerhet när du krypterar skalnings uppsättningen för den virtuella datorn.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  Syntaxen för värdet för parametern Disk-Encryption-nyckel valv är den fullständiga ID-strängen:</br>
/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[resurs grupp-namn]/providers/Microsoft.KeyVault/vaults/[nyckel valv-namn]</br></br>
> Syntaxen för värdet för nyckeln Key-Encryption-Key är fullständig URI till KEK som i:</br>
https://[nyckel valv-namn]. valv. Azure. net/Keys/[kekname]/[KEK-Unique-ID]

## <a name="check-encryption-progress"></a>Kontrol lera krypterings förlopp

Använd [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption)för att kontrol lera statusen för disk kryptering:


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

När de virtuella dator instanserna är  krypterade rapporterar EncryptionSummary *-koden ProvisioningState/lyckades* som visas i följande exempel på utdata:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Inaktivera kryptering

Om du inte längre vill använda diskar med krypterade virtuella dator instanser kan du inaktivera kryptering med [disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) på följande sätt:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Nästa steg

- I den här artikeln använde du Azure PowerShell för att kryptera en skalnings uppsättning för virtuella datorer. Du kan också använda [Azure CLI](disk-encryption-cli.md) eller [Azure Resource Manager mallar](disk-encryption-azure-resource-manager.md).
- Om du vill att Azure Disk Encryption ska användas efter att ett annat tillägg har tillhandahållits, kan du använda [fil namns sekvenseringen](virtual-machine-scale-sets-extension-sequencing.md).
