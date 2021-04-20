---
title: Skapa en pool med diskkryptering aktiverat
description: Lär dig hur du använder diskkrypteringskonfiguration för att kryptera noder med en plattforms hanterad nyckel.
author: pkshultz
ms.topic: how-to
ms.date: 04/16/2021
ms.author: peshultz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01d2ea03768a09c1ad4e019b9e8ed43a26443637
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728526"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Skapa en pool med diskkryptering aktiverat

När du skapar en Azure Batch-pool med hjälp av Konfiguration av virtuell dator [kan](nodes-and-pools.md#virtual-machine-configuration)du kryptera beräkningsnoder i poolen med en plattform hanterad nyckel genom att ange diskkrypteringskonfigurationen.

Den här artikeln beskriver hur du skapar en Batch-pool med diskkryptering aktiverat.

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Varför ska jag använda en pool med diskkrypteringskonfiguration?

Med en Batch-pool kan du komma åt och lagra data på operativsystemet och temporära diskar på beräkningsnoden. Kryptering av disken på serversidan med en plattforms hanterad nyckel skyddar dessa data med låga omkostnader och bekvämlighet.

Batch tillämpar någon av dessa diskkrypteringstekniker på beräkningsnoder, baserat på poolkonfiguration och regional support.

- [Hanterad diskkryptering i vila med plattform hanterade nycklar](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Kryptering på värden med en plattforms hanterad nyckel](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

Du kan inte ange vilken krypteringsmetod som ska tillämpas på noderna i poolen. I stället anger du de måldiskar som du vill kryptera på deras noder, och Batch kan välja lämplig krypteringsmetod, vilket säkerställer att de angivna diskarna krypteras på beräkningsnoden.

> [!IMPORTANT]
> Om du skapar din pool med en anpassad [avbildning kan](batch-sig-images.md)du bara aktivera diskkryptering om du använder virtuella Windows-datorer.

## <a name="azure-portal"></a>Azure Portal

När du skapar en Batch-pool i Azure Portal väljer du antingen **TemporaryDisk** eller **OsAndTemporaryDisk** under **Disk Encryption Configuration (Diskkrypteringskonfiguration).**

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Skärmbild av alternativet Diskkrypteringskonfiguration i Azure Portal.":::

När poolen har skapats kan du se diskkrypteringskonfigurationens mål i avsnittet Egenskaper **för** poolen.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Skärmbild som visar konfigurationsmål för diskkryptering i Azure Portal.":::

## <a name="examples"></a>Exempel

I följande exempel visas hur du krypterar operativsystemet och temporära diskar i en Batch-pool med hjälp av Batch .NET SDK, Batch REST API och Azure CLI.

### <a name="batch-net-sdk"></a>Batch .NET SDK

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch-REST API

REST API URL:

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

- Läs mer om [kryptering på serversidan av Azure-disklagring](../virtual-machines/disk-encryption.md).
- En detaljerad översikt över Batch finns i [Batch-tjänstens arbetsflöde och resurser.](batch-service-workflow-features.md)
