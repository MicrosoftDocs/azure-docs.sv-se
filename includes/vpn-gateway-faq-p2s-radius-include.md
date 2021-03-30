---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b802cab7d52fd886770c326e97d0378a0da64762
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75752312"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>Stöds RADIUS-autentisering med alla Azure VPN Gateway-SKU:er?

RADIUS-autentisering stöds med VpnGw1-, VpnGw2- och VpnGw3-SKU:er. Om du använder äldre SKU:er stöds RADIUS-autentisering med SKU:er på nivåerna Standard och Hög kapacitet. RADIUS-autentiserings stöds inte med Gateway-SKU:n på Basic-nivå. 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>Stöds RADIUS-autentisering med den klassiska distributionsmodellen?
 
Nej. RADIUS-autentisering stöds inte med den klassiska distributionsmodellen.
 
### <a name="are-3rd-party-radius-servers-supported"></a>Stöds RADIUS-servrar från tredje part?

Ja, RADIUS-servrar från tredje part stöds.
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>Vilka anslutningskrav måste vara uppfyllda för att Azure-gatewayen ska kunna ansluta till en lokal RADIUS-server?

En VPN-anslutning för plats-till-plats till den lokala platsen, med rätt konfigurerade vägar, krävs.  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>Kan trafik till en lokal RADIUS-server (från Azure VPN-gatewayen) skickas via en ExpressRoute-anslutning?

Nej. Den kan bara skickas via en plats-till-plats-anslutning.
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>Har antalet SSTP-anslutningar som stöds med RADIUS-autentisering ändrats? Hur många SSTP- och IKEv2-anslutningar stöds?

Det högsta antalet SSTP-anslutningar som stöds på en gateway med RADIUS-autentisering har inte ändrats. Den förblir 128 för SSTP, men den är beroende av Gateway-SKU: n för IKEv2. Mer information om antalet anslutningar som stöds finns i Gateway- [SKU: er](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trusted-certificate-to-azure"></a>Vad är skillnaden mellan att utföra certifikatautentisering med hjälp av en RADIUS-server och att använda Azures interna certifikatautentisering (genom att ladda upp ett betrott certifikat till Azure).

När RADIUS-certifikatautentisering används vidarebefordras autentiseringsbegäran till en RADIUS-server som hanterar själva certifikatverifieringen. Det här alternativet är bra om du vill integrera med en infrastruktur för certifikatautentisering som du redan har via RADIUS.
  
När Azure används för certifikatautentisering utförs certifikatverifieringen av Azure VPN-gatewayen. Du måste ladda upp den offentliga nyckeln för ditt certifikat till gatewayen. Du kan också ange en lista med återkallade certifikat som inte har tillåtelse att ansluta.

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>Fungerar RADIUS-autentisering med både IKEv2 och SSTP VPN?

Ja, RADIUS-autentisering stöds för både IKEv2 och SSTP VPN. 

### <a name="does-radius-authentication-work-with-the-openvpn-client"></a>Fungerar RADIUS-autentisering med OpenVPN-klienten?

RADIUS-autentisering stöds endast för OpenVPN-protokollet via PowerShell.
