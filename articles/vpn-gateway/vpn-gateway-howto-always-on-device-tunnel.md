---
title: Konfigurera en Always-On VPN-tunnel
titleSuffix: Azure VPN Gateway
description: Lär dig hur du använder gatewayer med Windows 10 Always on för att upprätta och konfigurera permanenta enhets tunnlar till Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7688e42175b2b4e35b63979f5df25702f3bb869d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90986588"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurera en VPN-enhetstunnel för AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurera gatewayen

Konfigurera VPN-gatewayen att använda IKEv2 och certifikatbaserad autentisering med hjälp av artikeln [Konfigurera en punkt-till-plats-VPN-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="configure-the-device-tunnel"></a>Konfigurera enhets tunneln

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Ta bort en profil

Om du vill ta bort profilen kör du följande kommando:

![Skärm bild som visar ett PowerShell-fönster som kör kommandot Remove-VpnConnection-Name MachineCertTest.](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Nästa steg

Information om fel sökning finns i [problem med Azure punkt-till-plats-anslutning](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
