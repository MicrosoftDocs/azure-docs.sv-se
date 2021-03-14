---
title: Anslut ExpressRoute till den virtuella Nätverksgatewayen
description: Steg för att ansluta ExpressRoute till den virtuella Nätverksgatewayen.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: cea07f9a75e91b2b8ff444c9dda4a59d421ca37c
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462379"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Navigera till det privata moln som du skapade i självstudien [distribuera vSphere-kluster i Azure](../tutorial-create-private-cloud.md) . Välj **anslutning** under **Hantera**, Välj fliken **ExpressRoute** .

1. Kopiera verifierings nyckeln. Om det inte finns någon auktoriseringsregel måste du skapa en, välja **+ begär en nyckel för autentisering**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Kopiera verifierings nyckeln. Om det inte finns någon auktoriseringsregel måste du skapa en, välja + begär en nyckel för autentisering." border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::

1. Navigera till Virtual Network gateway som du skapade i föregående steg och välj **anslutningar** under **Inställningar**. På sidan **anslutningar** väljer du **+ Lägg till**.

1. På sidan **Lägg till anslutning** anger du värden för fälten och väljer **OK**. 

   | Fält | Värde |
   | --- | --- |
   | **Namn**  | Ange ett namn för anslutningen.  |
   | **Anslutningstyp**  | Välj **ExpressRoute**.  |
   | **Lös in auktorisering**  | Se till att den här rutan är markerad.  |
   | **Virtuell nätverksgateway** | Virtual Network gateway som du skapade tidigare.  |
   | **Auktoriseringsregel**  | Kopiera och klistra in verifierings nyckeln från fliken ExpressRoute för din resurs grupp. |
   | **Peer-krets-URI**  | Kopiera och klistra in ExpressRoute-ID: t från fliken ExpressRoute för din resurs grupp.  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="Skärm bild av sidan Lägg till anslutning för att ansluta ExpressRoute till den virtuella Nätverksgatewayen.":::

Anslutningen mellan din ExpressRoute-krets och din Virtual Network skapas.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Skärm bild av anslutningar för virtuell nätverksgateway.":::