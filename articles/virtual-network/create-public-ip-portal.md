---
title: Skapa en offentlig IP-Azure Portal
description: Lär dig hur du skapar en offentlig IP-adress i Azure Portal
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 7d0c83f1ae18d36557a7a5b0222aee2905e05cb7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550243"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Snabb start: skapa en offentlig IP-adress med hjälp av Azure Portal

Den här artikeln visar hur du skapar en offentlig IP-adressresurs med hjälp av Azure Portal. Mer information om vilka resurser som kan kopplas till, skillnaden mellan Basic-och standard-SKU och annan relaterad information finns i [offentliga IP-adresser](./public-ip-addresses.md).  I det här exemplet kommer vi bara att fokusera på IPv4-adresser. Mer information om IPv6-adresser finns i [IPv6 för Azure VNet](./ipv6-overview.md).

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-create-public-ip-standard-zones)

Använd följande steg för att skapa en standard zon med redundant offentlig IP-adress med namnet **myStandardZRPublicIP**.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Skapa en resurs**. 
3. I rutan Sök skriver du *offentlig IP-adress*.
4. I Sök resultaten väljer du **offentlig IP-adress**. Sedan väljer du **skapa** på sidan **offentlig IP-adress** .
5. På sidan **skapa offentlig IP-adress** anger eller väljer du följande information: 

    | Inställning                 | Värde                       |
    | ---                     | ---                         |
    | IP-version              | Välj IPv4                 |    
    | SKU                     | Välj **standard**         |
    | Nivå (om den visas *)                  | Välj **region**         |
    | Name                    | Ange *myStandardZRPublicIP*          |
    | Tilldelning av IP-adress   | OBS! detta kommer att låsas som "statisk"                                        |
    | Tids gräns för inaktivitet (minuter)  | Lämna värdet vid 4        |
    | DNS-namnetikett          | Lämna värdet tomt    |
    | Prenumeration            | Välj din prenumeration.   |
    | Resursgrupp          | Välj **Skapa ny** , ange myResourceGroup och välj sedan **OK** |
    | Location                | Välj **USA, östra 2**      |
    | Tillgänglighetszon       | Välj **zon – redundant**, ingen zon eller Välj en speciell zon (se anmärkning nedan) |

Observera att dessa endast är giltiga val i regioner med [Tillgänglighetszoner](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).  (Du kan också välja en speciell zon i dessa regioner, men den kommer inte att vara elastisk till zonindelade-problem.)  Mer information om tillgänglighets zoner finns i [Översikt över tillgänglighets zoner](https://docs.microsoft.com/azure/availability-zones/az-overview).

\* =-Nivån relaterar till Load Balancer funktionerna i [flera regioner](../load-balancer/cross-region-overview.md) , för närvarande i för hands version.

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-create-public-ip-basic)

Använd följande steg för att skapa en grundläggande statisk offentlig IP-adress med namnet **myBasicPublicIP**.  Grundläggande offentliga IP-adresser har inte konceptet tillgänglighets zoner.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Skapa en resurs**. 
3. I rutan Sök skriver du *offentlig IP-adress*.
4. I Sök resultaten väljer du **offentlig IP-adress**. Sedan väljer du **skapa** på sidan **offentlig IP-adress** .
5. På sidan **skapa offentlig IP-adress** anger eller väljer du följande information: 

    | Inställning                 | Värde                       |
    | ---                     | ---                         |
    | IP-version              | Välj IPv4                 |    
    | SKU                     | Välj **standard**         |
    | Name                    | Ange *myBasicPublicIP*          |
    | Tilldelning av IP-adress   | Välj **statisk** (se anmärkning nedan)                                     |
    | Tids gräns för inaktivitet (minuter)  | Lämna värdet vid 4        |
    | DNS-namnetikett          | Lämna värdet tomt    |
    | Prenumeration            | Välj din prenumeration.   |
    | Resursgrupp          | Välj **Skapa ny** , ange myResourceGroup och välj sedan **OK** |
    | Location                | Välj **USA, östra 2**      |

Om det är acceptabelt att IP-adressen kan ändras över tid kan du välja **dynamisk** IP-tilldelning.

---

## <a name="additional-information"></a>Ytterligare information 

Mer information om de enskilda fälten ovan finns i [hantera offentliga IP-adresser](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Nästa steg
- Associera en [offentlig IP-adress till en virtuell dator](./associate-public-ip-address-vm.md#azure-portal)
- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure.
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).
