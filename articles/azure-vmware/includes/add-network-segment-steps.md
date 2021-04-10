---
title: Lägg till ett NSX-T-nätverks segment
description: Steg för att lägga till ett NSX-T nätverks segment för Azure VMware-lösningen.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 14d698413d31af2dcbbdea5f37ec7f24f65199ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462140"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. I NSX-T Manager väljer du **nätverks**  >  **segment** och väljer sedan **Lägg till segment**. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Skärm bild som visar hur du lägger till ett nytt segment":::

1. Ange ett namn för segmentet.

1. Välj nivån-1 Gateway (TNTxx-T1) som den **anslutna gatewayen** och lämna **typen** till flexibel.

1. Välj den förkonfigurerade överliggande **transport zonen** (TNTXX-överlägg-TZ) och välj sedan **Ange undernät**. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Ange segment namn, ansluten gateway och typ och transport zon och välj sedan ange undernät.":::

1. Ange IP-adressen för gatewayen och välj sedan **Lägg till**. 

   >[!IMPORTANT]
   >IP-adressen måste finnas i ett icke-överlappande RFC1918-adressblock som garanterar anslutning till de virtuella datorerna i det nya segmentet.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Ange IP-adressen för gatewayen för det nya segmentet och välj sedan Lägg till.":::

1. Välj **tillämpa** och **Spara**.

1. Välj **Nej** om du vill neka alternativet att fortsätta konfigurera segmentet. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Neka att ytterligare konfigurera det nyligen skapade nätverks segmentet genom att välja Nej.":::

1. Bekräfta förekomst av det nya nätverks segmentet. I det här exemplet är **ls01** det nya nätverks segmentet.

   1. I NSX-T Manager väljer du **nätverks**  >  **segment**. 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Bekräfta att det nya nätverks segmentet finns i NSX-T.":::

   1. I vCenter väljer du **nätverks > SDDC-datacenter**.

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Bekräfta att det nya nätverks segmentet finns i vCenter.":::