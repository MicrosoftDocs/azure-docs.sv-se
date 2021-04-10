---
title: Begär auktoriseringskod för ExpressRoute
description: Steg för att begära en auktoriseringskod för ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 99d9fba33d64fca1d9c5b960041fbabe1f9060db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027019"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. I Azure Portal, under avsnittet **anslutning** , på fliken **ExpressRoute** väljer du **+ begär en nyckel för autentisering**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Skärm bild som visar hur du begär en ExpressRoute-Authorization-nyckel." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Ange ett namn och välj **skapa**. 
      
   Det kan ta ungefär 30 sekunder att skapa nyckeln. När den nya nyckeln har skapats visas den i listan över auktoriseringsregler för det privata molnet.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Skärm bild som visar ExpressRoute Global Reach Authorization Key.":::
  
1. Kopiera verifierings nyckeln och ExpressRoute-ID: t. Du använder dem för att slutföra peer kopplingen.  

   > [!NOTE]
   > Verifierings nyckeln försvinner efter en stund, så kopiera den så snart den visas.