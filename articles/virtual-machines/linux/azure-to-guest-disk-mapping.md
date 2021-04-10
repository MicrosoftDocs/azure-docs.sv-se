---
title: Så här mappar du Azure-diskar till virtuella Linux-datorer gäst diskar
description: Så här fastställer du de Azure-diskar som Underlay en virtuell Linux-dators gäst diskar.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.collection: linux
ms.openlocfilehash: bc6c6273ab3d1a4403763e4ed0a8c491995fb2df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556731"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Så här mappar du Azure-diskar till virtuella Linux-datorer gäst diskar

Du kan behöva bestämma vilka Azure-diskar som ska återställa en virtuell dators gäst diskar. I vissa fall kan du jämföra disk-eller volym storleken med storleken på de anslutna Azure-diskarna. I scenarier där det finns flera Azure-diskar av samma storlek som är kopplade till den virtuella datorn måste du använda data diskens logiska enhets nummer (LUN). 

## <a name="what-is-a-lun"></a>Vad är en LUN?

Ett logiskt enhets nummer (LUN) är ett tal som används för att identifiera en speciell lagrings enhet. Varje lagrings enhet tilldelas en unik numerisk identifierare som börjar med noll. Den fullständiga sökvägen till en enhet representeras av buss numret, målets ID-nummer och LUN (Logical Unit Number). 

Exempel: ***buss nummer 0, mål-ID 0, LUN 3***

För vår övning behöver du bara använda LUN.

## <a name="finding-the-lun"></a>Hitta LUN

Nedan finns två metoder för att hitta LUN på en disk i Linux.

### <a name="lsscsi"></a>lsscsi

1. Anslut till VM:en
1. `sudo lsscsi`

Den första kolumnen i listan innehåller LUN, formatet är [värd: kanal: Target:**LUN**].

### <a name="listing-block-devices"></a>Visar block enheter

1. Anslut till VM:en
1. `sudo ls -l /sys/block/*/device`

Den sista kolumnen i listan innehåller LUN, formatet är [värd: kanal: mål:**LUN**]

## <a name="finding-the-lun-for-the-azure-disks"></a>Hitta LUN för Azure-diskar

Du kan hitta LUN för en Azure-disk med hjälp av Azure Portal, Azure CLI.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Hitta en Azure-disks LUN i Azure Portal

1. I Azure Portal väljer du "Virtual Machines" om du vill visa en lista över dina Virtual Machines
1. Välj den virtuella datorn
1. Välj "diskar"
1. Välj en datadisk i listan över anslutna diskar.
1. Diskens LUN visas i rutan disk information. Den LUN som visas här motsvarar de LUN som du har sökt i gästen med lsscsi eller som visar block enheterna.

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Hitta en Azure disks LUN med Azure CLI

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
