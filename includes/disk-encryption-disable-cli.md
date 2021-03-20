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
ms.openlocfilehash: ed2b13077ff5809899097254dde0fa8406e1db3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85601400"
---
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI eller med en Resource Manager-mall. 

>[!IMPORTANT]
>Det går bara att inaktivera kryptering med Azure Disk Encryption på virtuella Linux-datorer för data volymer. Det stöds inte på data-eller OS-volymer om operativ system volymen har krypterats.  

- **Inaktivera disk kryptering med Azure PowerShell:** Om du vill inaktivera krypteringen använder du cmdleten [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Inaktivera kryptering med Azure CLI:** Om du vill inaktivera kryptering använder du kommandot [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** Använd [inaktivera kryptering på en virtuell Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) -mall för virtuella datorer för att inaktivera kryptering.
     1. Klicka på **Distribuera till Azure**.
     2. Välj prenumeration, resurs grupp, plats, virtuell dator, juridiska villkor och avtal.
