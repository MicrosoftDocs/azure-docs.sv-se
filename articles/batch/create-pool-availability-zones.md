---
title: Skapa en pool över tillgänglighets zoner
description: Lär dig hur du skapar en batch-pool med zonindelade-princip som skyddar mot fel.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 56e718bedf504b8e69598c2d99ab8b889a470b89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725296"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Skapa en Azure Batch pool över Tillgänglighetszoner

Azure-regioner som har stöd för [Tillgänglighetszoner](https://azure.microsoft.com/global-infrastructure/availability-zones/) har minst tre separata zoner, var och en med sina egna oberoende system för ström källa, nätverk och kylning. När du skapar en Azure Batch pool med konfiguration av virtuell dator kan du välja att etablera en batch-pool över Tillgänglighetszoner. Om du skapar poolen med den här zonindelade-principen kan du skydda dina batch Compute-noder från Azure Data Center-nivå haverier.

Du kan till exempel skapa poolen med zonindelade-princip i en Azure-region som stöder tre Tillgänglighetszoner. Om ett Azure-datacenter i en tillgänglighets zon har ett infrastruktur fel kommer batch-poolen fortfarande att ha felfria noder i de andra två Tillgänglighetszoner, så poolen är fortfarande tillgänglig för schemaläggning.

## <a name="regional-support-and-other-requirements"></a>Regional support och andra krav

Batch upprätthåller paritet med Azure på support Tillgänglighetszoner. Om du vill använda alternativet zonindelade måste poolen skapas i en Azure- [region som stöds](../availability-zones/az-region.md).

För att batch-poolen ska kunna allokeras mellan tillgänglighets zoner måste den Azure-region där poolen skapas ha stöd för den begärda VM-SKU: n i fler än en zon. Du kan verifiera detta genom att anropa [API: et för Resource SKU-listan](/rest/api/compute/resourceskus/list) och kontrol lera **locationInfo** -fältet för [resourceSku](/rest/api/compute/resourceskus/list#resourcesku). Se till att fler än en zon stöds för den begärda VM-SKU: n.

För [batch-konton för användar prenumerations läge](accounts.md#batch-accounts)kontrollerar du att prenumerationen där du skapar poolen inte har en begränsning för zon erbjudandet på den BEGÄRda VM-SKU: n. Du kan kontrol lera detta genom att anropa [API: et för Resource SKU](/rest/api/compute/resourceskus/list) och kontrol lera [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions). Om det finns en zon begränsning kan du skicka ett [support ärende](/troubleshoot/azure/general/region-access-request-process) för att ta bort zon begränsningen.

Observera också att du inte kan skapa en pool med en zonindelade-princip om kommunikationen mellan noder är aktive rad och använder en [VM-SKU som stöder InfiniBand](../virtual-machines/workloads/hpc/enable-infiniband.md).

## <a name="create-a-batch-pool-across-availability-zones"></a>Skapa en batch-pool över Tillgänglighetszoner

I följande exempel visas hur du skapar en batch-pool över Tillgänglighetszoner.

> [!NOTE]
> När du skapar en pool med en zonindelade-princip försöker batch-tjänsten allokera poolen för alla Tillgänglighetszoner i den valda regionen. Du kan inte ange en viss allokering i zonerna.

### <a name="batch-management-client-net-sdk"></a>Batch Management Client .NET SDK

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>Batch-REST API

REST API-URL

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Begärandetext

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Lär dig mer om [att skapa en pool i ett undernät för ett virtuellt Azure-nätverk](batch-virtual-network.md).
- Lär dig mer om hur du [skapar en Azure Batch pool utan offentliga IP-adresser](./batch-pool-no-public-ip-address.md).