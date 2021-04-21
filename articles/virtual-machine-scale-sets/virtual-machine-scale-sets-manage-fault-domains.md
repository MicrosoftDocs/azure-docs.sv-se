---
title: Hantera feldomäner i skalningsuppsättningar för virtuella Azure-datorer
description: Lär dig hur du väljer rätt antal FD:er när du skapar en VM-skalningsuppsättning.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 10d45662f84a354ee4b261c2e7255a57aa81ad0f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774493"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Välja rätt antal feldomäner för skaluppsättningar för den virtuella datorn
VM-skalningsuppsättningar skapas med fem feldomäner som standard i Azure-regioner utan zoner. För de regioner som stöder zonindelig distribution av VM-skalningsuppsättningar och det här alternativet är valt är standardvärdet för antalet feldomäner 1 för var och en av zonerna. FD =1 innebär i det här fallet att de VM-instanser som tillhör skalningsuppsättningen sprids över många rack efter bästa försök.

Du kan också överväga att justera antalet feldomäner för skalningsuppsättningen med antalet Managed Disks feldomäner. Den här justeringen kan förhindra förlust av kvorum om en Managed Disks feldomänen går ned. FD-antalet kan anges till mindre än eller lika med det antal Managed Disks feldomäner som är tillgängliga i var och en av regionerna. Läs det här [dokumentet](../virtual-machines/availability.md) om du vill veta mer om antalet Managed Disks feldomäner efter region.

## <a name="rest-api"></a>REST-API
Du kan ange egenskapen `properties.platformFaultDomainCount` till 1, 2 eller 3 (standardvärdet 3 om inget annat anges). Läs dokumentationen för REST API [här](/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Azure CLI
Du kan ange parametern `--platform-fault-domain-count` till 1, 2 eller 3 (standardvärdet 3 om inget annat anges). Läs dokumentationen för Azure CLI [här.](/cli/azure/vmss#az_vmss_create)

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [funktioner för tillgänglighet och redundans](../virtual-machines/availability.md) för Azure-miljöer.
