---
title: Självstudie – Lägg till ett NSX-T-nätverks segment i Azure VMware-lösning
description: Lär dig hur du skapar ett NSX-T nätverks segment som ska användas för virtuella datorer (VM) i vCenter.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9125e552f9641a2d26b9584b66a4447f9c152161
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462139"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Självstudie: Lägg till ett nätverks segment i Azure VMware-lösningen 

De virtuella datorerna (VM) som har skapats i vCenter placeras på de nätverks segment som skapats i NSX-T och visas i vCenter.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Navigera i NSX-T-hanteraren för att lägga till nätverks segment
> * Lägg till ett nytt nätverks segment
> * Observera det nya nätverks segmentet i vCenter

## <a name="prerequisites"></a>Förutsättningar

Ett privat moln i Azure VMware-lösning med åtkomst till de vCenter-och NSX Manager-gränssnitten. Mer information finns i själv studie kursen [Konfigurera nätverk](tutorial-configure-networking.md) .

## <a name="add-a-network-segment"></a>Lägg till ett nätverks segment

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett NSX-T-nätverks-segment som ska användas för virtuella datorer i vCenter. 

Nu kan du: 

- [Skapa och hantera DHCP för Azure VMware-lösningen](manage-dhcp.md)
- [Skapa ett innehålls bibliotek för att distribuera virtuella datorer i Azure VMware-lösningen](deploy-vm-content-library.md) 
- [Lokala peer-miljöer i ett privat moln](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
