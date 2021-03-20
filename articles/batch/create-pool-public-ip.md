---
title: Skapa en pool med angivna offentliga IP-adresser
description: Lär dig hur du skapar en batch-pool som använder dina egna offentliga IP-adresser.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e822311718847e173763847d503335f71457308b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91849336"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Skapa en Azure Batch pool med angivna offentliga IP-adresser

När du skapar en Azure Batch pool kan du [etablera poolen i ett undernät för ett virtuellt Azure-nätverk](batch-virtual-network.md) (VNet) som du anger. Virtuella datorer i batch-poolen nås via offentliga IP-adresser som skapas av batch. Dessa offentliga IP-adresser kan ändras under poolens livstid, vilket innebär att nätverks inställningarna kan bli inaktuella om IP-adresserna inte uppdateras.

Du kan skapa en lista över statiska offentliga IP-adresser som ska användas med de virtuella datorerna i poolen. På så sätt kan du kontrol lera listan över offentliga IP-adresser och se till att de inte ändras utan förvarning. Detta kan vara särskilt användbart om du arbetar med en extern tjänst, till exempel en databas, som begränsar åtkomsten till vissa IP-adresser.

Information om hur du skapar pooler utan offentliga IP-adresser får du genom [att läsa skapa en Azure Batch pool utan offentliga IP-adresser](./batch-pool-no-public-ip-address.md).

## <a name="prerequisites"></a>Förutsättningar

- **Autentisering**. Om du vill använda en offentlig IP-adress måste batch-klientens API använda [Azure Active Directory (AD)-autentisering](batch-aad-auth.md).

- **Ett Azure VNet**. Du måste använda ett [virtuellt nätverk](batch-virtual-network.md) från samma Azure-prenumeration där du skapar poolen och dina IP-adresser. Endast Azure Resource Manager-baserade virtuella nätverk kan användas. Se till att VNet uppfyller alla [allmänna krav](batch-virtual-network.md#vnet-requirements).

- **Minst en offentlig Azure-IP-adress**. Om du vill skapa en eller flera offentliga IP-adresser kan du använda [Azure Portal](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), ett [Azure Command-Line-gränssnitt (CLI)](/cli/azure/network/public-ip#az-network-public-ip-create)eller [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress). Se till att följa kraven i listan nedan.

> [!NOTE]
> Batch allokerar automatiskt ytterligare nätverks resurser i resurs gruppen som innehåller de offentliga IP-adresserna. För varje 100 dedikerade noder tilldelar batch normalt en nätverks säkerhets grupp (NSG) och en belastningsutjämnare. Resurserna begränsas av prenumerationens resurs kvoter. När du använder större pooler kan du behöva [begära en kvot ökning](batch-quota-limit.md#increase-a-quota) för en eller flera av dessa resurser.

## <a name="public-ip-address-requirements"></a>Krav för offentliga IP-adresser

Tänk på följande när du skapar dina offentliga IP-adresser:

- De offentliga IP-adresserna måste finnas i samma prenumeration och region som det batch-konto som du använder för att skapa poolen.
- **IP-adresstilldelning** måste anges till **static**.
- **SKU** måste anges till **standard**.
- Ett DNS-namn måste anges.
- De offentliga IP-adresserna får endast användas för konfigurations pooler för virtuella datorer. Inga andra resurser bör använda dessa IP-adresser, eller så kan poolen uppleva allokeringsfel.
- Inga säkerhets principer eller resurs lås bör begränsa en användares åtkomst till den offentliga IP-adressen.
- Antalet offentliga IP-adresser som har angetts för poolen måste vara tillräckligt stora för att rymma antalet virtuella datorer som är avsedda för poolen. Detta måste vara minst summan av **targetDedicatedNodes**-   och targetLowPriorityNodes- ****   egenskaperna för poolen. Om det inte finns tillräckligt med IP-adresser allokerar poolen Compute-noderna och ett storleks fel uppstår. För närvarande använder batch en offentlig IP-adress för varje 100 virtuella datorer.
- Ha alltid ytterligare en buffert med offentliga IP-adresser. Vi rekommenderar att du lägger till minst en offentlig IP-adress eller cirka 10% av de totala offentliga IP-adresser som du lägger till i en pool, beroende på vilket som är störst. Den här ytterligare bufferten hjälper till med sin interna optimering vid skalning, samt ger snabbare skalning efter en lyckad skalning eller skalning.
- När poolen har skapats kan du inte lägga till eller ändra listan över offentliga IP-adresser som används av poolen. Om du behöver ändra listan måste du ta bort poolen och sedan återskapa den.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Skapa en batch-pool med offentliga IP-adresser

Exemplet nedan visar hur du använder [Azure Batch tjänst REST API](/rest/api/batchservice/pool/add) för att skapa en pool som använder offentliga IP-adresser.

### <a name="batch-service-rest-api"></a>REST API för Batch-tjänsten

REST API-URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Begärandetext

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Lär dig mer om [att skapa en pool i ett undernät för ett virtuellt Azure-nätverk](batch-virtual-network.md).
- Lär dig mer om hur du [skapar en Azure Batch pool utan offentliga IP-adresser](./batch-pool-no-public-ip-address.md).

