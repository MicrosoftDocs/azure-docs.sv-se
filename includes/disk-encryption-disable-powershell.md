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
ms.openlocfilehash: 82318a94a6a095016fe1177ee486f035d101589c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776799"
---
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI eller med en Resource Manager mall. Att inaktivera kryptering av datadiskar på Windows-datorer när både operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

- **Inaktivera diskkryptering med Azure PowerShell:** Om du vill inaktivera krypteringen använder [du cmdleten Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du [kommandot az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Inaktivera kryptering med en Resource Manager mall:** 

    1. Klicka **på Distribuera till Azure från** mallen Inaktivera [diskkryptering vid körning av virtuella Windows-datorer.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
    2. Välj prenumeration, resursgrupp, plats, virtuell dator, volymtyp, juridiska villkor och avtal.
    3.  Klicka **på Köp** för att inaktivera diskkryptering på en virtuell Windows-dator som körs. 
