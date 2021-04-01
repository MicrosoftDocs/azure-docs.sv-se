---
title: Skapa en pool med diskkryptering aktiverat
description: Lär dig hur du använder disk krypterings konfiguration för att kryptera noder med en plattforms-hanterad nyckel.
author: pkshultz
ms.topic: how-to
ms.date: 01/27/2021
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 41fc827459b454e2bcb120a925cdab8fcd46e310
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99055322"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Skapa en pool med diskkryptering aktiverat

När du skapar en Azure Batch pool med [konfiguration av virtuell dator](nodes-and-pools.md#virtual-machine-configuration)kan du kryptera datornoder i poolen med en plattforms hanterad nyckel genom att ange disk krypterings konfigurationen.

Den här artikeln beskriver hur du skapar en batch-pool med disk kryptering aktiverat.

> [!IMPORTANT]
> Stöd för kryptering på värden med en plattforms hanterad nyckel i Azure Batch finns för närvarande i en offentlig för hands version för USA, västra USA, västra USA 2, södra centrala, US Gov, Virginia och US Gov, Arizona regioner.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Varför ska jag använda en pool med disk krypterings konfiguration?

Med en batch-pool kan du komma åt och lagra data på de operativ system och temporära diskarna i Compute-noden. Kryptering av Server sidan med en plattforms-hanterad nyckel skyddar dessa data med låg belastning och bekvämlighet.

Batch använder en av dessa disk krypterings tekniker på datornoderna, baserat på konfiguration av pooler och regional support.

- [Hanterad disk kryptering i vila med plattforms hanterade nycklar](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Kryptering på värden med en plattforms-hanterad nyckel](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

Du kan inte ange vilken krypterings metod som ska användas för noderna i poolen. I stället anger du de mål diskar som du vill kryptera på deras noder, och batch kan välja lämplig krypterings metod, vilket säkerställer att de angivna diskarna krypteras på Compute-noden.

> [!IMPORTANT]
> Om du skapar en pool med en [anpassad avbildning](batch-sig-images.md)kan du bara aktivera disk kryptering om du använder virtuella Windows-datorer.

## <a name="azure-portal"></a>Azure Portal

När du skapar en batch-pool i Azure Portal väljer du antingen **TemporaryDisk** eller **OsAndTemporaryDisk** under **disk krypterings konfiguration**.

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Skärm bild av konfigurations alternativet disk kryptering i Azure Portal.":::

När poolen har skapats kan du se konfigurations målen för disk kryptering i poolens **Egenskaper** -avsnitt.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Skärm bild som visar konfigurations målen för disk kryptering i Azure Portal.":::

## <a name="examples"></a>Exempel

I följande exempel visas hur du krypterar operativ systemet och temporära diskar i en batch-pool med hjälp av batch .NET SDK, batch-REST API och Azure CLI.

### <a name="batch-net-sdk"></a>Batch .NET SDK

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch-REST API

REST API-URL:

```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Begärandetext:

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "taskSlotsPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [kryptering på Server sidan av Azure-disklagring](../virtual-machines/disk-encryption.md).
- En djupgående översikt över batch finns i [batch-tjänstens arbets flöde och resurser](batch-service-workflow-features.md).
