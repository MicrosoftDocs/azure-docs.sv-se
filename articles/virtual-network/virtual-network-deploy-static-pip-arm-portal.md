---
title: Skapa en virtuell dator med en statisk offentlig IP-adress – Azure Portal
description: Lär dig hur du skapar en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure Portal.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/12/2020
ms.author: allensu
ms.openlocfilehash: d416af3d3a8eb8ab8057f13cc0d9a133adcb849a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221164"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Skapa en virtuell dator med en statisk offentlig IP-adress med hjälp av Azure Portal

Med en offentlig IP-adress kan du kommunicera med en virtuell dator från Internet. 

Tilldela en statisk offentlig IP-adress i stället för en dynamisk adress för att säkerställa att adressen aldrig ändras.   

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator** eller Sök efter **virtuell dator** i sökrutan.
   
2. I **skapa en virtuell dator** skriver eller väljer du värdena på fliken **grundläggande** :

    | Inställning | Värde                                          |
    |-----------------------|----------------------------------|
    | **Projekt information** |  |
    | Prenumeration | Välj din Azure-prenumeration |
    | Resursgrupp | Välj **Skapa ny**. </br> I **namn** anger du **myResourceGroup**. </br> Välj **OK**. |
    | **Instansinformation** |  |
    | Namn på virtuell dator | Ange **myVM** |
    | Region | Välj **USA, östra** |
    | Tillgänglighets alternativ | Välj **ingen redundans för infrastruktur krävs** |
    | Bild | Välj **Windows Server 2019 Data Center – gen1** |
    | Azure Spot-instans | Välj **Nej** |
    | Storlek | Välj storlek på virtuell dator eller Ställ in standardinställningen |
    | **Administratörs konto** |  |
    | Användarnamn | Ange ett användar namn |
    | Lösenord | Ange ett lösen ord |
    | Bekräfta lösenordet | Ange lösenordet igen |

3. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
4. På fliken nätverk väljer eller anger du:

    | Inställning | Värde |
    |-|-|
    | **Nätverksgränssnitt** |  |
    | Virtuellt nätverk | Acceptera standard nätverks namnet. |
    | Undernät | Acceptera standard under näts konfigurationen. |
    | Offentlig IP-adress | Välj **Skapa ny**. </br> I **skapa offentlig IP-adress**, i namn, anger du **myPublicIP**. </br> För **SKU** väljer du **standard**. </br> **Tilldelning** väljer du **statisk**. </br> Välj **OK**.  |
    | Nätverks säkerhets grupp för nätverkskort | Välj **grundläggande**|
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **RDP (3389)** |

    > [!WARNING]
    > Portal 3389 är markerad, för att aktivera fjärråtkomst till den virtuella Windows Server-datorn från Internet. Det rekommenderas inte att öppna port 3389 på Internet för att hantera produktions arbets belastningar. </br> För säker åtkomst till Azure Virtual Machines, se **[Vad är Azure skydds?](../bastion/bastion-overview.md)**
   
5. Välj **Granska + skapa**. 
  
6. Granska inställningarna och välj sedan **Skapa**.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller:

1. Skriv **myResourceGroup** i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv **myResourceGroup** i **SKRIV RESURSGRUPPSNAMNET:** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Se [lägga till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md):

* Ändra en offentlig IP-adress från dynamisk till statisk.
* Arbeta med privata IP-adresser.

Offentliga IP-adresser har en [nominell avgift](https://azure.microsoft.com/pricing/details/ip-addresses). Det finns en [gräns](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) för hur många offentliga IP-adresser som du kan använda per prenumeration.

SKU: n för den virtuella datorns offentliga IP-adress måste matcha den offentliga IP-SKU: n för Azure Load Balancer när den läggs till i en backend-pool. Mer information finns i [Azure Load Balancer](../load-balancer/skus.md).

Du kan ladda ned listan över intervall (prefix) för [offentliga](https://www.microsoft.com/download/details.aspx?id=56519) Azure-moln och för Azure-moln för [amerikanska myndigheter](https://www.microsoft.com/download/details.aspx?id=57063), [Kina](https://www.microsoft.com/download/details.aspx?id=57062) eller [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064).

- Läs mer om [statiska offentliga IP-adresser](./public-ip-addresses.md#allocation-method).
- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure.
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Lär dig mer om [privata IP-adresser](./private-ip-addresses.md) och tilldela en [statisk privat IP-adress](virtual-network-network-interface-addresses.md#add-ip-addresses) till en virtuell Azure-dator.
