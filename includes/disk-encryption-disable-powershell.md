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
ms.openlocfilehash: 1e3cdfccc36549d2aa127636b15b698a4e19ccf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85610580"
---
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI eller med en Resource Manager-mall. Att inaktivera kryptering av datadiskar på Windows-datorer när både operativsystemet och datadiskarna har krypterats fungerar inte som förväntat. Inaktivera kryptering på alla diskar i stället.

- **Inaktivera disk kryptering med Azure PowerShell:** Om du vill inaktivera krypteringen använder du cmdleten [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du kommandot [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** 

    1. Klicka på **distribuera till Azure** från den [inaktivera disk kryptering vid körning av Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) -mall.
    2. Välj prenumeration, resurs grupp, plats, virtuell dator, volym typ, juridiska villkor och avtal.
    3.  Klicka på **köp** för att inaktivera disk kryptering på en Windows-VM som körs. 
