---
title: Distribuera om Virtuella Linux-datorer i Azure | Microsoft Docs
description: Hur du distribuerar om virtuella Linux-datorer i Azure för att undvika problem med SSH-anslutningen.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: d553fb6b2061f987e3e098ae47ebca9cd3f60984
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203413"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Distribuera om en virtuell Linux-dator till en ny Azure-nod
Om du har problem med att felsöka SSH eller program åtkomst till en virtuell Linux-dator i Azure kan det hjälpa att distribuera om den virtuella datorn. När du distribuerar om en virtuell dator flyttar den den virtuella datorn till en ny nod i Azure-infrastrukturen och ger sedan den behörighet igen. Alla konfigurations alternativ och tillhör ande resurser behålls. Den här artikeln visar hur du distribuerar om en virtuell dator med hjälp av Azure CLI eller Azure Portal.

> [!NOTE]
> När du har distribuerat om en virtuell dator, går den temporära disken förlorad och dynamiska IP-adresser som är kopplade till det virtuella nätverks gränssnittet uppdateras. 


## <a name="use-the-azure-cli"></a>Använda Azure CLI
Installera den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) och logga in på ditt Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

Distribuera om den virtuella datorn med [AZ VM redeploy](/cli/azure/vm). I följande exempel distribueras den virtuella datorn med namnet *myVM* i resurs gruppen med namnet *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Använd den klassiska Azure-CLI

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Installera den [senaste Azure Classic CLI](/cli/azure/install-classic-cli) och logga in på ditt Azure-konto. Se till att du är i Resource Manager-läge ( `azure config mode arm` ).

I följande exempel distribueras den virtuella datorn med namnet *myVM* i resurs gruppen med namnet *myResourceGroup*:

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till din virtuella dator kan du hitta information om hur du [felsöker SSH-anslutningar](troubleshoot-ssh-connection.md) eller [detaljerade SSH-felsöknings steg](detailed-troubleshoot-ssh-connection.md). Om du inte kan komma åt ett program som körs på den virtuella datorn kan du också läsa [fel söknings problem i programmet](troubleshoot-app-connection.md).
