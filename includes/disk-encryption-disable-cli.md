---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 37571a82ca73342b8edfc4702686ccd9091887c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771516"
---
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI eller med en Resource Manager mall. 

>[!IMPORTANT]
>Inaktivering av kryptering med Azure Disk Encryption på virtuella Linux-datorer stöds endast för datavolymer. Det stöds inte på data- eller OS-volymer om OS-volymen har krypterats.  

- **Inaktivera diskkryptering med Azure PowerShell:** Om du vill inaktivera krypteringen använder [du cmdleten Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du [kommandot az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Inaktivera kryptering med en Resource Manager mall:** Använd mallen [Inaktivera kryptering på en virtuell Linux-dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) som körs för att inaktivera kryptering.
     1. Klicka på **Distribuera till Azure**.
     2. Välj prenumeration, resursgrupp, plats, virtuell dator, juridiska villkor och avtal.
