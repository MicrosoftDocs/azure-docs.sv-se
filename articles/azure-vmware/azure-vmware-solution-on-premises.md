---
title: Anslut Azure VMware-lösningen till din lokala miljö
description: Lär dig hur du ansluter Azure VMware-lösningen till din lokala miljö.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 0b26dc4756cb37544c2b2f8c5a75df0ac1a9d629
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491800"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Anslut Azure VMware-lösningen till din lokala miljö

I den här artikeln fortsätter du med den [information som samlats in under planeringen](production-ready-deployment-steps.md) för att ansluta Azure VMware-lösningen till din lokala miljö.

Innan du börjar måste du ha två krav för att ansluta Azure VMware-lösningen till din lokala miljö:

- En ExpressRoute-krets från din lokala miljö till Azure.
- A/29 icke-överlappande CIDR-adressblock för ExpressRoute Global Reach-peering, som du definierade som en del av [planerings fasen](production-ready-deployment-steps.md).

>[!NOTE]
> Du kan ansluta via VPN, men det är utanför omfånget för det här snabb starts dokumentet.

## <a name="establish-an-expressroute-global-reach-connection"></a>Upprätta en ExpressRoute Global Reach anslutning

Om du vill upprätta lokal anslutning till ditt privata moln i Azure VMware-lösningen med hjälp av ExpressRoute Global Reach, följer du själv studie kursen [om peer-datorer i ett privat moln](tutorial-expressroute-global-reach-private-cloud.md) .

Den här självstudien resulterar i en anslutning som visas i diagrammet.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Global Reach lokala nätverks anslutnings diagram." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>Verifiera lokal nätverks anslutning

Du bör nu se i din **lokala Edge-router** där EXPRESSROUTE ansluter NSX-T-datasegmenten och Azure VMware lösnings hanterings segmenten.

>[!IMPORTANT]
>Alla har en annan miljö och vissa måste tillåta att dessa vägar sprids tillbaka till det lokala nätverket.  

Vissa miljöer har brand väggar som skyddar ExpressRoute-kretsen.  Om inga brand väggar och ingen väg rensning inträffar pingar du din Azure VMware-lösning vCenter-Server eller en [virtuell dator på NSX-T-segmentet](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) från din lokala miljö. Från den virtuella datorn i NSX-T-segmentet kan du dessutom komma åt resurser i din lokala miljö.

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa avsnitt för att distribuera och konfigurera VMware-HCX

> [!div class="nextstepaction"]
> [Distribuera och konfigurera VMware HCX](tutorial-deploy-vmware-hcx.md)