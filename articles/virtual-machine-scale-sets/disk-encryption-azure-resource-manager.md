---
title: Skapa och kryptera en skalnings uppsättning för virtuella datorer med Azure Resource Manager mallar
description: I den här snabb starten får du lära dig hur du använder Azure Resource Manager mallar för att skapa och kryptera en skalnings uppsättning för virtuella datorer
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 4284e94f8d8d0effd160c5048f54fcbede417e38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86129754"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Kryptera skalnings uppsättningar för virtuella datorer med Azure Resource Manager

Du kan kryptera eller dekryptera skalnings uppsättningar för virtuella Linux-datorer med hjälp av Azure Resource Manager mallar.

## <a name="deploying-templates"></a>Distribuera mallar

Först väljer du den mall som passar ditt scenario.

- [Aktivera disk kryptering på en virtuell dators skalnings uppsättning med virtuella Linux-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Aktivera disk kryptering på en virtuell Windows-dator med skal uppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Distribuera en virtuell dators skalnings uppsättning med virtuella Linux-datorer med en hopp och möjliggör kryptering på virtuella Linux-datorers skalnings uppsättningar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Distribuera en virtuell dators skalnings uppsättning med virtuella Windows-datorer med en hopp och möjliggör kryptering på Windows Virtual Machine Scale Sets](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Inaktivera disk kryptering på en virtuell dators skalnings uppsättning med virtuella Linux-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Inaktivera disk kryptering på en virtuell Windows-dator med skal uppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Följ sedan de här stegen:

1. Klicka på **Distribuera till Azure**.
2. Fyll i de obligatoriska fälten och godkänn de allmänna villkoren.
3. Klicka på **köp** för att distribuera mallen.

## <a name="next-steps"></a>Nästa steg

- [Azure Disk Encryption för skalnings uppsättningar för virtuella datorer](disk-encryption-overview.md)
- [Kryptera en skalnings uppsättning för virtuella datorer med hjälp av Azure CLI](disk-encryption-cli.md)
- [Kryptera en skalnings uppsättning för virtuella datorer med hjälp av Azure PowerShell](disk-encryption-powershell.md)
- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md)
- [Använd Azure Disk Encryption med sekvensering av tillägg för skalnings uppsättning för virtuell dator](disk-encryption-extension-sequencing.md)
