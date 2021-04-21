---
title: Skapa en pool med angivna offentliga IP-adresser
description: Lär dig hur du skapar en Batch-pool som använder dina egna offentliga IP-adresser.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: 82a37f96a91bdad37c1a7828ef0cf71b3581ca82
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768403"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Skapa en Azure Batch med angivna offentliga IP-adresser

När du skapar en Azure Batch-pool kan du etablera poolen i ett undernät för ett [virtuellt Azure-nätverk](batch-virtual-network.md) (VNet) som du anger. Virtuella datorer i Batch-poolen nås via offentliga IP-adresser som skapas av Batch. Dessa offentliga IP-adresser kan ändras under poolens livslängd, vilket innebär att nätverksinställningarna kan bli inaktuella om IP-adresserna inte uppdateras.

Du kan skapa en lista över statiska offentliga IP-adresser som ska användas med de virtuella datorerna i poolen. På så sätt kan du kontrollera listan över offentliga IP-adresser och se till att de inte ändras oväntat. Detta kan vara särskilt användbart om du arbetar med alla externa tjänster, till exempel en databas, som begränsar åtkomsten till vissa IP-adresser.

Information om hur du skapar pooler utan offentliga IP-adresser finns [i Skapa en Azure Batch utan offentliga IP-adresser.](./batch-pool-no-public-ip-address.md)

## <a name="prerequisites"></a>Förutsättningar

- **Autentisering**. Om du vill använda en offentlig IP-adress måste Batch-klient-API:et [använda Azure Active Directory -autentisering (AD).](batch-aad-auth.md)

- **Ett azure-VNet.** Du måste använda ett [virtuellt nätverk](batch-virtual-network.md) från samma Azure-prenumeration där du skapar din pool och dina IP-adresser. Endast Azure Resource Manager virtuella nätverk kan användas. Se till att det virtuella nätverket uppfyller alla [allmänna krav.](batch-virtual-network.md#vnet-requirements)

- **Minst en offentlig IP-adress i Azure.** Om du vill skapa en eller flera offentliga [IP-adresser kan du använda Azure Portal,](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)Azure Command-Line Interface [(CLI)](/cli/azure/network/public-ip#az_network_public_ip_create) [eller Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress). Se till att följa kraven som anges nedan.

> [!NOTE]
> Batch allokerar automatiskt ytterligare nätverksresurser i resursgruppen som innehåller de offentliga IP-adresserna. För varje 100 dedikerade noder allokerar Batch vanligtvis en nätverkssäkerhetsgrupp (NSG) och en lastbalanserare. Dessa resurser begränsas av prenumerationens resurskvoter. När du använder större pooler kan du behöva [begära en kvotökning](batch-quota-limit.md#increase-a-quota) för en eller flera av dessa resurser.

## <a name="public-ip-address-requirements"></a>Krav för offentliga IP-adresser

Tänk på följande krav när du skapar dina offentliga IP-adresser:

- De offentliga IP-adresserna måste finnas i samma prenumeration och region som det Batch-konto som du använder för att skapa poolen.
- **IP-adresstilldelningen** måste vara inställd på **Statisk.**
- **SKU** måste anges till **Standard**.
- Ett DNS-namn måste anges.
- De offentliga IP-adresserna får bara användas för konfigurationspoolerna för virtuella datorer. Inga andra resurser bör använda dessa IP-adresser, eller så kan det uppstå allokeringsfel i poolen.
- Inga säkerhetsprinciper eller resurslås bör begränsa en användares åtkomst till den offentliga IP-adressen.
- Antalet offentliga IP-adresser som anges för poolen måste vara tillräckligt stort för att hantera antalet virtuella datorer som är mål för poolen. Detta måste minst vara summan av egenskaperna **targetDedicatedNodes** och    **targetLowPriorityNodes**   för poolen. Om det inte finns tillräckligt med IP-adresser allokerar poolen delvis beräkningsnoderna och ett storleksändringsfel uppstår. Batch använder för närvarande en offentlig IP-adress för varje 100 virtuella datorer.
- Ha alltid ytterligare en buffert med offentliga IP-adresser. Vi rekommenderar att du lägger till minst en ytterligare offentlig IP-adress eller cirka 10 % av de totala offentliga IP-adresserna som du lägger till i en pool, beroende på vilket som är större. Den här extra bufferten hjälper Batch med sin interna optimering vid nedskalning, samt möjliggör snabbare uppskalning efter en misslyckad uppskalning eller nedskalning.
- När poolen har skapats kan du inte lägga till eller ändra listan över offentliga IP-adresser som används av poolen. Om du behöver ändra listan måste du ta bort poolen och sedan återskapa den.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Skapa en Batch-pool med offentliga IP-adresser

Exemplet nedan visar hur du använder Azure Batch Tjänst-REST-API [för att](/rest/api/batchservice/pool/add) skapa en pool som använder offentliga IP-adresser.

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

- Lär dig mer [om Batch-tjänstens arbetsflöde och primära](batch-service-workflow-features.md) resurser, till exempel pooler, noder, jobb och uppgifter.
- Lär dig [mer om att skapa en pool i ett undernät i ett virtuellt Azure-nätverk](batch-virtual-network.md).
- Lär dig [mer om att Azure Batch en pool utan offentliga IP-adresser.](./batch-pool-no-public-ip-address.md)
