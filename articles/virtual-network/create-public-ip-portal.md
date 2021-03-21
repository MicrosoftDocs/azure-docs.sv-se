---
title: Skapa en offentlig IP-Azure Portal
description: Lär dig hur du skapar en offentlig IP-adress i Azure Portal
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: e6b7648188e2307da4ef40e0ab3daf6201f9d89d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694877"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Skapa en offentlig IP-adress med hjälp av Azure Portal

Den här artikeln visar hur du skapar en offentlig IP-adressresurs med hjälp av Azure Portal. 

Mer information om resurser som kan kopplas till den offentliga IP-adressen och skillnaden mellan Basic-och standard-SKU: er finns i [offentliga IP-adresser](./public-ip-addresses.md). 

Den här artikeln fokuserar på IPv4-adresser. Mer information om IPv6-adresser finns i [IPv6 för Azure VNet](./ipv6-overview.md).

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-create-public-ip-standard-zones)

Använd följande steg för att skapa en standard zon med redundant offentlig IP-adress med namnet **myStandardZRPublicIP**.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Skapa en resurs**. 
3. I rutan Sök anger du **offentlig IP-adress**. Välj **offentlig IP-adress** i Sök resultaten.
4. På sidan **offentlig IP-adress** väljer du **skapa**.
5. På sidan **skapa offentlig IP-adress** anger eller väljer du följande information: 

    | Inställning                 | Värde                       |
    | ---                     | ---                         |
    | IP-version              | Välj IPv4                 |    
    | SKU                     | Välj **standard**         |
    | Nivå                   | Välj **region**         |
    | Name                    | Ange **myStandardZRPublicIP**          |
    | Tilldelning av IP-adress   | Observera att det här alternativet är låst som "statisk"                                        |
    | Cirkulations inställningar      | Lämna standardvärdet för **Microsoft-nätverk**. </br> Mer information om inställningar för routning finns i [Vad är cirkulations inställningar (för hands version)?](./routing-preference-overview.md). |
    | Tids gräns för inaktivitet (minuter)  | Lämna standardvärdet **4**.        |
    | DNS-namnetikett          | Lämna värdet tomt.    |
    | Prenumeration            | Välj din prenumeration.   |
    | Resursgrupp          | Välj **Skapa ny**, ange **myResourceGroup**. </br> Välj **OK**. |
    | Location                | Välj **USA, östra 2**      |
    | Tillgänglighetszon       | Välj **zon – redundant**, ingen zon eller Välj en speciell zon (se anmärkning nedan) |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Skapa standard-IP-adress i Azure Portal" border="false":::

> [!NOTE]
> De här valen är giltiga i regioner med [Tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones). </br>
Du kan välja en zon i dessa regioner, men den kommer inte att vara elastisk till zonindelade-problem. </br> Mer information om tillgänglighets zoner finns i [Översikt över tillgänglighets zoner](https://docs.microsoft.com/azure/availability-zones/az-overview).

\* =-Nivån relaterar till funktionen för [belastnings utjämning i flera regioner](../load-balancer/cross-region-overview.md) , för närvarande i för hands version.

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-create-public-ip-basic)

I det här avsnittet skapar du en grundläggande offentlig IP-adress med namnet **myBasicPublicIP**. 

> [!NOTE]
> Grundläggande offentliga IP-adresser har inte stöd för tillgänglighets zoner.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Skapa en resurs**. 
3. I rutan Sök anger du **offentlig IP-adress**. Välj **offentlig IP-adress** i Sök resultaten.
4. På sidan **offentlig IP-adress** väljer du **skapa**.
5. På sidan **skapa offentlig IP-adress** anger eller väljer du följande information: 

    | Inställning                 | Värde                       |
    | ---                     | ---                         |
    | IP-version              | Välj IPv4                 |    
    | SKU                     | Välj **grundläggande**         |
    | Name                    | Ange *myBasicPublicIP*          |
    | Tilldelning av IP-adress   | Välj **statisk** (se anmärkning nedan)                                     |
    | Tids gräns för inaktivitet (minuter)  | Lämna standardvärdet **4**.       |
    | DNS-namnetikett          | Lämna värdet tomt    |
    | Prenumeration            | Välj din prenumeration.   |
    | Resursgrupp          | Välj **Skapa ny**, ange **myResourceGroup**. </br> Välj **OK**. |
    | Location                | Välj **USA, östra 2**      |

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Skapa standard-IP-adress i Azure Portal" border="false":::

Om det är acceptabelt att IP-adressen kan ändras över tid kan du välja **dynamisk** IP-tilldelning.

---

## <a name="additional-information"></a>Ytterligare information 

Mer information om de enskilda fälten som anges ovan finns i [hantera offentliga IP-adresser](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Nästa steg
- Associera en [offentlig IP-adress till en virtuell dator](./associate-public-ip-address-vm.md#azure-portal)
- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure.
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).