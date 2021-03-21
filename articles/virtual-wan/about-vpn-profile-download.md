---
title: 'Azure Virtual WAN: användar profiler för VPN-klienter'
description: Detta hjälper dig att arbeta med klient profil filen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980923"
---
# <a name="working-with-user-vpn-client-profile-files"></a>Arbeta med användares VPN-klient profil filer

Profilmappar innehåller information som krävs för att konfigurera en VPN-anslutning. Den här artikeln hjälper dig att få och förstå den information som krävs för en användares VPN-klientkonfiguration.

## <a name="download-the-profile"></a>Ladda ned profilen

Du kan använda stegen i artikeln [Ladda ned profiler](global-hub-profile.md) för att ladda ned ZIP-filen för klient profilen.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Mappen OpenVPN** innehåller den *OVPN* -profil som måste ändras för att inkludera nyckeln och certifikatet. Mer information finns i [konfigurera OpenVPN-klienter](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Nästa steg

Mer information om VPN för virtuella WAN-användare finns i [skapa en VPN-anslutning för användare](virtual-wan-point-to-site-portal.md).
