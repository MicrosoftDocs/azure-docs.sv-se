---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92479586"
---
1. Öppna sidan för din virtuella nätverksgateway. Du kan navigera till gatewayen genom att gå till **namnet på din VNet-> översikt – > anslutna enheter – > namnet på din gateway**, även om det finns flera andra sätt att navigera till.
1. Välj **anslutningar** på sidan för gatewayen. Längst upp på sidan anslutningar väljer du **+ Lägg** till för att öppna sidan **Lägg till anslutning** .

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="Plats-till-plats-anslutning":::
1. På sidan **Lägg till anslutning** ställer du in värdena för anslutningen.

   * **Namn:** Namnge din anslutning.
   * **Anslutnings typ:** Välj **plats-till-plats (IPSec)**.
   * **Virtuell nätverksgateway:** Värdet är låst eftersom du ansluter från den här gatewayen.
   * **Lokal nätverksgateway:** Välj **Välj en lokal** nätverksgateway och välj den lokala nätverksgateway som du vill använda.
   * **Delad nyckel:** Värdet måste matcha det värde som du använder för din lokala VPN-enhet. I det här exemplet använder vi abc123. Du kan (och bör) dock använda ett mer komplext värde. Det är viktigt att värdet du anger här är samma värde som du anger när du konfigurerade VPN-enheten.
   * Lämna alternativet **Använd Azures privata IP-adress** avmarkerat.
   * Låt **Enable BGP** vara avmarkerat.
   * Välj **IKEv2**.
   * De återstående värdena för **Prenumeration**, **Resursgrupp** och **Plats** är låsta.

1. Välj **OK** för att skapa anslutningen. Du kommer att se *Skapar anslutningen* blinka fram på skärmen.
1. Anslutningen visas på sidan **Anslutningar** för den virtuella nätverksgatewayen. Statusen kommer att gå från *Okänd* till *Ansluter* och sedan till *Klar*.
