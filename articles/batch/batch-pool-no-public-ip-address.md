---
title: Skapa en Azure Batch-pool utan offentliga IP-adresser
description: Lär dig hur du skapar en pool utan offentliga IP-adresser
author: pkshultz
ms.topic: how-to
ms.date: 12/9/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 806e85fca0a509d56e248fc7779fba0f0a59a61d
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007678"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>Skapa en Azure Batch-pool utan offentliga IP-adresser

När du skapar en Azure Batch pool kan du etablera den virtuella datorns konfigurations pool utan en offentlig IP-adress. Den här artikeln förklarar hur du konfigurerar en batch-pool utan offentliga IP-adresser.

## <a name="why-use-a-pool-without-public-ip-addresses"></a>Varför ska jag använda en pool utan offentliga IP-adresser?

Som standard tilldelas alla datornoder i en Azure Batch pool för konfiguration av virtuella datorer en offentlig IP-adress. Den här adressen används av batch-tjänsten för att schemalägga aktiviteter och för kommunikation med Compute-noder, inklusive utgående åtkomst till Internet.

Om du vill begränsa åtkomsten till dessa noder och minska identifieringen av dessa noder från Internet kan du etablera poolen utan offentliga IP-adresser.

> [!IMPORTANT]
> Stöd för pooler utan offentliga IP-adresser i Azure Batch är för närvarande en offentlig för hands version för följande regioner: Frankrike, centrala, Asien, östra, västra centrala USA, södra centrala USA, västra USA 2, östra USA, norra Europa, östra USA 2, centrala USA, västra Europa, norra centrala USA, västra USA, östra Australien, Östra Japan, västra Japan.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

- **Autentisering**. Om du vill använda en pool utan offentliga IP-adresser i ett [virtuellt nätverk](./batch-virtual-network.md)måste batch-klientens API använda Azure Active Directory (AD)-autentisering. Mer dokumentation om stödet för Azure Batch i Azure Active Directory finns i [Authenticate Batch service solutions with Active Directory](batch-aad-auth.md) (Autentisera lösningar för Batch-tjänsten med Active Directory). Om du inte skapar poolen i ett virtuellt nätverk kan du använda Azure AD-autentisering eller nyckelbaserad autentisering.

- **Ett Azure VNet**. Om du skapar poolen i ett [virtuellt nätverk](batch-virtual-network.md)följer du dessa krav och konfigurationer. För att förbereda ett VNet med ett eller flera undernät i förväg, kan du använda Azure Portal, Azure PowerShell, gränssnittet i Azure Command-Line-gränssnittet (CLI) eller andra metoder.
  - Det virtuella nätverket måste vara i samma prenumeration och region som det Batch-konto som du använder för att skapa din pool.
  - Det undernät som anges för poolen måste ha tillräckliga otilldelade IP-adresser för det antal virtuella datorer som är mål för poolen. Summan av egenskaperna `targetDedicatedNodes` och `targetLowPriorityNodes` för poolen. Om undernätet inte har tillräckligt med lediga IP-adresser, allokerar poolen datornoderna partiellt och ett storleksändringsfel inträffar.
  - Du måste inaktivera tjänsten för privata länkar och nätverks principer för slut punkten. Detta kan göras med hjälp av Azure CLI: ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --resouce-group <resourcegroup> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```

> [!IMPORTANT]
> För varje 100-dedikerad eller låg prioritets nod allokerar batch en privat länk tjänst och en belastningsutjämnare. Dessa resurser begränsas av prenumerationens [resurskvoter](../azure-resource-manager/management/azure-subscription-service-limits.md). För stora pooler kan du behöva [begära en kvot ökning](batch-quota-limit.md#increase-a-quota) för en eller flera av dessa resurser. Dessutom bör inga resurs lås användas för någon resurs som skapats av batch, eftersom detta förhindrar att resurser rensas till följd av användar initierade åtgärder som att ta bort en pool eller ändra storlek till noll.

## <a name="current-limitations"></a>Aktuella begränsningar

1. Pooler utan offentliga IP-adresser måste använda konfiguration av virtuell dator och inte Cloud Services konfiguration.
1. [Anpassad slut punkts konfiguration](pool-endpoint-configuration.md) för batch Compute-noder fungerar inte med pooler utan offentliga IP-adresser.
1. Eftersom det inte finns några offentliga IP-adresser kan du inte [använda dina egna angivna offentliga IP-adresser](create-pool-public-ip.md) med den här typen av pool.

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Skapa en pool utan offentliga IP-adresser i Azure Portal

1. Navigera till ditt Batch-konto i Azure Portal.
1. I fönstret **Inställningar** till vänster väljer du **pooler**.
1. I fönstret **pooler** väljer du **Lägg till**.
1. I fönstret **Lägg till pool** väljer du det alternativ som du vill använda från List rutan **Bildtyp** .
1. Välj rätt **utgivare/erbjudande/SKU** för din avbildning.
1. Ange de återstående nödvändiga inställningarna, inklusive **nodens storlek**, **dedikerade noder** och **noder med låg prioritet** samt önskade valfria inställningar.
1. Du kan också välja ett virtuellt nätverk och undernät som du vill använda. Det här virtuella nätverket måste finnas i samma resurs grupp som den pool som du skapar.
1. I **etablerings typ för IP-adress** väljer du **NoPublicIPAddresses**.

![Skärm bild av skärmen Lägg till pool med NoPublicIPAddresses valt.](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>Använd batch-REST API för att skapa en pool utan offentliga IP-adresser

Exemplet nedan visar hur du använder [Azure Batch REST API](/rest/api/batchservice/pool/add) för att skapa en pool som använder offentliga IP-adresser.

### <a name="rest-api-uri"></a>REST API-URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>Begärandetext

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>Utgående åtkomst till Internet

I en pool utan offentliga IP-adresser kommer de virtuella datorerna inte att kunna komma åt det offentliga Internet om du inte konfigurerar din nätverks konfiguration på lämpligt sätt, till exempel med hjälp av det [virtuella nätverkets NAT](../virtual-network/nat-overview.md). Observera att NAT endast tillåter utgående åtkomst till Internet från de virtuella datorerna i det virtuella nätverket. Det går inte att använda batch-skapade datornoder eftersom de inte har några associerade offentliga IP-adresser.

Ett annat sätt att tillhandahålla utgående anslutningar är att använda en användardefinierad väg (UDR). På så sätt kan du dirigera trafik till en proxyserver som har offentlig Internet åtkomst.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att [skapa pooler i ett virtuellt nätverk](batch-virtual-network.md).
- Lär dig hur du [använder privata slut punkter med batch-konton](private-connectivity.md).
