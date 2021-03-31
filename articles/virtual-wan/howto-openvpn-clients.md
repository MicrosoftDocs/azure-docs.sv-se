---
title: Konfigurera OpenVPN-klienter för Azure Virtual WAN
description: Steg för att konfigurera OpenVPN-klienter för Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 05502b344b1224dff5d12f95b96b05baace98970
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491009"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Konfigurera en OpenVPN-klient för Azure Virtual WAN

Den här artikeln hjälper dig att konfigurera **OpenVPN- &reg; protokoll** klienter. Du kan också använda Azure VPN-klienten för Windows 10 för att ansluta via OpenVPN-protokollet. Du hittar fler instruktioner [här](openvpn-azure-ad-client.md)

## <a name="before-you-begin"></a>Innan du börjar

Skapa en användare VPN-konfiguration (punkt-till-plats). Kontrol lera att du väljer "OpenVPN" som tunnel typ. Anvisningar finns i [skapa en P2s-konfiguration för Azure Virtual WAN](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om användar-VPN (punkt-till-plats) finns i [skapa användare VPN-anslutningar](virtual-wan-point-to-site-portal.md).

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
