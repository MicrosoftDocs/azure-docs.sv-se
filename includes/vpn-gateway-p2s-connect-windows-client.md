---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041541"
---
>[!NOTE]
>Du måste ha administratörsbehörigheter på den Windows-klientdator som du använder för att ansluta.
>

1. Anslut till ditt VNet genom att gå till VPN-inställningar på klient datorn och leta upp VPN-anslutningen som du skapade. Namnet har samma namn som ditt virtuella nätverk. Välj **Anslut**. Ett popup-meddelande med information om certifikatanvändningen kanske visas. Välj **Fortsätt** om du vill använda utökade privilegier.

1. På statussidan **Anslutning** väljer du **Anslut** för att initiera anslutningen. Om du ser skärmen **Välj certifikat** kontrollerar du att klientcertifikatet som visas är det som du vill använda för att ansluta. Om så inte är fallet använder du listpilen för att välja rätt certifikat och väljer sedan **OK**.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="Anslut från en Windows-dator":::

1. Anslutningen upprättas.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="Ansluta från en dator till ett Azure VNet-punkt-till-plats-anslutnings diagram":::
