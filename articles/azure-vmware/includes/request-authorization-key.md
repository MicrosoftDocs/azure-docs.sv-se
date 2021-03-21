---
title: Begär auktoriseringskod för ExpressRoute
description: Steg för att begära en auktoriseringskod för ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 54a610c8b0f3f3fe9d3ebe39291bba7767007839
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491864"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. I Azure Portal, under avsnittet **anslutning** , på fliken **ExpressRoute** väljer du **+ begär en nyckel för autentisering**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Skärm bild som visar hur du begär en ExpressRoute-Authorization-nyckel." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Ange ett namn och välj **skapa**. 
      
   Det kan ta ungefär 30 sekunder att skapa nyckeln. När den nya nyckeln har skapats visas den i listan över auktoriseringsregler för det privata molnet.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Skärm bild som visar ExpressRoute Global Reach Authorization Key.":::
  
1. Anteckna auktoriseringsvärdet och ExpressRoute-ID: t. Du använder dem för att slutföra peer kopplingen.  

   > [!NOTE]
   > Verifierings nyckeln försvinner efter en stund, så kopiera den så snart den visas.