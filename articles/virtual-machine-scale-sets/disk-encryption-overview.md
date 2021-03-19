---
title: Aktivera Azure Disk Encryption för Virtual Machine Scale Sets
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure disk kryptering för Virtual Machine Scale Sets
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 6b9805d66149a18216a200bc89a79b3e06106c9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83195112"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption för Virtual Machine Scale Sets

Azure Disk Encryption tillhandahåller volym kryptering för operativ system och data diskar för dina virtuella datorer, vilket hjälper till att skydda och skydda dina data för att uppfylla organisationens säkerhet och efterlevnad. Mer information finns i [Azure Disk Encryption: virtuella Linux-datorer](../virtual-machines/linux/disk-encryption-overview.md) och [Azure Disk Encryption: virtuella Windows-datorer](../virtual-machines/windows/disk-encryption-overview.md)  

Azure Disk Encryption kan också tillämpas på skalnings uppsättningar för virtuella Windows-och Linux-datorer, i följande instanser:
- Skalnings uppsättningar som skapats med hanterade diskar. Azure Disk Encryption stöds inte för interna (eller ohanterade) disk skalnings uppsättningar.
- OS-och data volymer i Windows skalnings uppsättningar.
- Data volymer i Linux-skalnings uppsättningar. Operativ system disk kryptering stöds inte för närvarande för Linux Scale Sets.

Du kan lära dig grunderna i Azure Disk Encryption för skalnings uppsättningar för virtuella datorer på bara några minuter med [krypteringen av skalnings uppsättningar för virtuella datorer med hjälp av Azure CLI](disk-encryption-cli.md) eller [kryptera en virtuell dators skalnings uppsättningar med hjälp av Azure PowerShell](disk-encryption-powershell.md) självstudier.

## <a name="next-steps"></a>Nästa steg

- [Kryptera en skalnings uppsättning för virtuella datorer med hjälp av Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md)
- [Använd Azure Disk Encryption med sekvensering av tillägg för skalnings uppsättning för virtuell dator](disk-encryption-extension-sequencing.md)
