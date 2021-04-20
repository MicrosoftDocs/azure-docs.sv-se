---
title: Ansluta Azure VMware Solution till din lokala miljö
description: Lär dig hur Azure VMware Solution ansluter till din lokala miljö.
ms.topic: tutorial
ms.date: 04/19/2021
ms.openlocfilehash: 392d82a9aca9b60b394a5d5f4a7e6b0111438e59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725627"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Ansluta Azure VMware Solution till din lokala miljö

I den här artikeln fortsätter du att använda informationen som [samlats in under planeringen](production-ready-deployment-steps.md) för Azure VMware Solution ansluta till din lokala miljö.

Innan du börjar måste du ha en ExpressRoute-krets från din lokala miljö till Azure.


>[!NOTE]
> Du kan ansluta via VPN, men det är inte omfånget för det här snabbstartsdokumentet.

## <a name="establish-an-expressroute-global-reach-connection"></a>Upprätta en ExpressRoute Global Reach anslutning

Om du vill upprätta en lokal anslutning till ditt Azure VMware Solution privata moln med expressroute-Global Reach följer du självstudien [Peer on-premises environments to a private cloud (Peer-lokala](tutorial-expressroute-global-reach-private-cloud.md) miljöer till ett privat moln).

Den här självstudien resulterar i en anslutning som visas i diagrammet.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Global Reach diagram över lokal nätverksanslutning." lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

## <a name="verify-on-premises-network-connectivity"></a>Verifiera lokal nätverksanslutning

Nu bör du se i din **lokala gränsrouter** där ExpressRoute ansluter NSX-T-nätverkssegmenten och Azure VMware Solution nätverkshanteringssegmenten.

>[!IMPORTANT]
>Alla har en annan miljö och vissa måste tillåta att dessa vägar sprids tillbaka till det lokala nätverket.  

Vissa miljöer har brandväggar som skyddar ExpressRoute-kretsen.  Om inga brandväggar och ingen vägomsning inträffar pingar du din Azure VMware Solution vCenter-server eller en virtuell dator i [NSX-T-segmentet](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) från din lokala miljö. Från den virtuella datorn i NSX-T-segmentet kan du dessutom nå resurser i din lokala miljö.

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa avsnitt för att distribuera och konfigurera VMware HCX

> [!div class="nextstepaction"]
> [Distribuera och konfigurera VMware HCX](tutorial-deploy-vmware-hcx.md)