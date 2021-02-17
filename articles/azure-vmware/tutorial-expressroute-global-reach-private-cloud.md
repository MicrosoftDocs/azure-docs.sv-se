---
title: Självstudie – lokala peer-miljöer i ett privat moln
description: Lär dig hur du skapar ExpressRoute Global Reach peering till ett privat moln i en Azure VMware-lösning.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558815"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Självstudie: peer-lokala miljöer till ett privat moln

ExpressRoute Global Reach ansluter din lokala miljö till ditt privata moln i Azure VMware-lösningen. ExpressRoute Global Reach-anslutningen upprättas mellan det privata molnet ExpressRoute-kretsen och en befintlig ExpressRoute-anslutning till dina lokala miljöer. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använd Azure Portal om du vill aktivera lokal-till-privat Cloud ExpressRoute Global Reach-peering.


## <a name="before-you-begin"></a>Innan du börjar

Innan du aktiverar anslutning mellan två ExpressRoute-kretsar med ExpressRoute Global Reach bör du läsa dokumentationen om hur du [aktiverar anslutningar i olika Azure-prenumerationer](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Förutsättningar

- En separat, fungerande ExpressRoute-krets som används för att ansluta lokala miljöer till Azure.
- Se till att alla gatewayer, inklusive ExpressRoute-leverantörens tjänst, har stöd för ett autonomt system nummer (ASN) med 4 byte. Azure VMware-lösningen använder 4 bytes offentliga ASN: er för annonserings vägar.


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>Skapa en ExpressRoute-auktoriseringsregel i den lokala ExpressRoute-kretsen

1. Från bladet **ExpressRoute-kretsar** går du till Inställningar och väljer **auktoriseringar**.

2. Ange namnet på verifierings nyckeln och välj **Spara**.

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="Välj auktorisering och ange namnet på verifierings nyckeln.":::
  
     När den nya nyckeln har skapats visas den i listan över Auktoriseringsprinciper för kretsen.
 
 4. Anteckna auktoriseringsvärdet och ExpressRoute-ID: t. Du ska använda dem i nästa steg för att slutföra peer kopplingen.
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>Peer-privat moln till lokalt

1. I **översikten över** det privata molnet under hantera väljer du **anslutnings > ExpressRoute Global Reach > Lägg till**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Från menyn väljer du anslutning, fliken ExpressRoute Global Reach och sedan Lägg till.":::

2. Ange ExpressRoute-ID och den autentiseringsregel som skapades i föregående avsnitt.

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Ange ExpressRoute-ID och verifierings nyckel och välj sedan skapa.":::

3. Välj **Skapa**. Den nya anslutningen visas i listan lokal moln anslutning.  

>[!TIP]
>Du kan ta bort eller koppla från en anslutning från listan genom att välja **mer**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Koppla från eller ta bort en lokal anslutning":::


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du aktiverar det lokala molnets ExpressRoute Global Reach peering. 

Fortsätt till nästa självstudie och lär dig hur du distribuerar och konfigurerar VMware HCX-lösningen för ditt privata moln i Azure VMware-lösningen.

> [!div class="nextstepaction"]
> [Distribuera och konfigurera VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
