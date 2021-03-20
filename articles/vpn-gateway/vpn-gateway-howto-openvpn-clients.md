---
title: Så här konfigurerar du OpenVPN-klienter för Azure VPN Gateway | Microsoft Docs
description: Lär dig hur du konfigurerar OpenVPN för Azure-VPN Gateway för Windows-, Linux-och Mac-operativsystem.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4be903fa17ce95e96c82241249b421e1d794c80f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89435787"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurera OpenVPN-klienter för Azure VPN Gateway

Den här artikeln hjälper dig att konfigurera **OpenVPN- &reg; protokoll** klienter.

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har slutfört stegen för att konfigurera OpenVPN för din VPN-gateway. Mer information finns i [konfigurera OpenVPN för Azure VPN gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Nästa steg

Om du vill att VPN-klienter ska kunna komma åt resurser i ett annat VNet följer du anvisningarna i artikeln [VNet-till-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) för att konfigurera en VNet-till-VNET-anslutning. Se till att aktivera BGP på gatewayarna och anslutningarna, annars flödar inte trafiken.

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
