---
title: Så här mappar du Azure-diskar till gäst diskar i Windows-datorer
description: Ta reda på vilka Azure-diskar som Underlay en virtuell Windows-dators gäst diskar.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 7a4fad066af37217eb42060d5fc5a7ef716770c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560998"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Så här mappar du Azure-diskar till gäst diskar i Windows-datorer

Du kan behöva bestämma vilka Azure-diskar som ska återställa en virtuell dators gäst diskar. I vissa fall kan du jämföra disk-eller volym storleken med storleken på de anslutna Azure-diskarna. I scenarier där det finns flera Azure-diskar av samma storlek som är kopplade till den virtuella datorn måste du använda data diskens logiska enhets nummer (LUN). 

## <a name="what-is-a-lun"></a>Vad är en LUN?

Ett logiskt enhets nummer (LUN) är ett tal som används för att identifiera en speciell lagrings enhet. Varje lagrings enhet tilldelas en unik numerisk identifierare som börjar med noll. Den fullständiga sökvägen till en enhet representeras av buss numret, målets ID-nummer och LUN (Logical Unit Number). 

Exempel: ***buss nummer 0, mål-ID 0, LUN 3***

För vår övning behöver du bara använda LUN.

## <a name="finding-the-lun"></a>Hitta LUN

Det finns två sätt att hitta det LUN som du väljer beror på om du använder [lagrings utrymmen](/windows-server/storage/storage-spaces/overview) eller inte.

### <a name="disk-management"></a>Diskhantering

Om du inte använder lagringspooler kan du använda [disk hantering](/windows-server/storage/disk-management/overview-of-disk-management) för att hitta LUN.

1. Anslut till den virtuella datorn och öppna disk hantering a. Högerklicka på Start-knappen och välj "disk hantering" a. Du kan också skriva `diskmgmt.msc` i rutan Starta sökning
1. Högerklicka på någon av diskarna i det nedre fönstret och välj egenskaper
1. LUN visas i egenskapen "plats" på fliken "allmänt"

### <a name="storage-pools"></a>Lagringspooler

1. Anslut till den virtuella datorn och öppna Serverhanteraren
1. Välj "fil-och lagrings tjänster", "volymer", "lagringspooler"
1. I det nedre högra hörnet av Serverhanteraren visas avsnittet "fysiska diskar". Diskarna som utgör lagringspoolen visas här samt LUN för varje disk.

## <a name="finding-the-lun-for-the-azure-disks"></a>Hitta LUN för Azure-diskar

Du kan hitta LUN för en Azure-disk med hjälp av Azure Portal, Azure CLI eller Azure PowerShell

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Hitta en Azure-disks LUN i Azure Portal

1. I Azure Portal väljer du "Virtual Machines" om du vill visa en lista över dina Virtual Machines
1. Välj den virtuella datorn
1. Välj "diskar"
1. Välj en datadisk i listan över anslutna diskar.
1. Diskens LUN visas i rutan disk information. Den LUN som visas här motsvarar de LUN som har sökts upp i gästen med hjälp av Enhetshanteraren eller Serverhanteraren.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Hitta en Azure disks LUN med Azure CLI eller Azure PowerShell

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---