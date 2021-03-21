---
title: Felsöka Azure plats-till-plats-VPN kopplas från period vis
description: Lär dig hur du felsöker problemet där VPN-anslutningen från plats till plats kopplas från regelbundet.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: genli
ms.openlocfilehash: 582077c46f5fc7176b457cf0d392af48fbe7d40b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369347"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Felsökning: Azure plats-till-plats-VPN-anslutningen kopplas från periodvis

Problemet kan uppstå om att en ny eller befintlig Microsoft Azure plats-till-plats-VPN-anslutning inte är stabil eller kopplas från regelbundet. Den här artikeln innehåller fel söknings steg som hjälper dig att identifiera och lösa orsaken till problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

### <a name="prerequisite-step"></a>Förutsättnings steg

Kontrol lera typen av Azure Virtual Network Gateway:

1. Gå till [Azure Portal](https://portal.azure.com).
2. Gå till **översikts** sidan för den virtuella Nätverksgatewayen för typ information.
    
    ![Översikt över gatewayen](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Steg 1 kontrol lera om den lokala VPN-enheten är verifierad

1. Kontrol lera om du använder en [verifierad VPN-enhet och operativ system version](vpn-gateway-about-vpn-devices.md#devicetable). Om VPN-enheten inte verifieras kan du behöva kontakta enhets tillverkaren för att se om det finns några kompatibilitetsproblem.
2. Kontrol lera att VPN-enheten är korrekt konfigurerad. Mer information finns i [Redigera enhets konfigurations exempel](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Steg 2 kontrol lera inställningarna för säkerhets Association (för principbaserad Azure virtuella nätverksgateway)

1. Kontrol lera att det virtuella nätverket, undernät och, intervall i den **lokala Nätverksgatewayen** i Microsoft Azure är samma som konfigurationen på den lokala VPN-enheten.
2. Kontrol lera att inställningarna för säkerhets associationen matchar.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Steg 3 Sök efter User-Defined vägar eller nätverks säkerhets grupper i Gateway-undernätet

En användardefinierad väg i Gateway-undernätet kan begränsa viss trafik och tillåta annan trafik. Detta gör att VPN-anslutningen inte är tillförlitlig för viss trafik och är lämpligt för andra. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Steg 4 kontrol lera inställningen "en VPN-tunnel per undernät" (för principbaserad virtuella nätverksgateway)

Se till att den lokala VPN-enheten har angetts till att ha **en VPN-tunnel per undernät** för principbaserad virtuell nätverksgateway.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Steg 5 kontrol lera säkerhets Associations begränsningen (för principbaserad virtuella nätverksgateway)

Den principbaserad virtuella Nätverksgatewayen har en gräns på 200 under nätets säkerhets Association par. Om antalet virtuella undernät i Azure-nätverk multipliceras med antalet gånger som antalet lokala undernät är större än 200, kan du se sporadiska undernät från koppling.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Steg 6 kontrol lera den lokala VPN-enhetens externa gränssnitts adress

- Om VPN-enhetens IP-adress på Internet ingår i definitionen för den **lokala** Nätverksgatewayen i Azure kan det uppstå sporadiska från kopplingar.
- Enhetens externa gränssnitt måste vara direkt på Internet. Det får inte finnas någon NAT (Network Address Translation) eller brand vägg mellan Internet och enheten.
-  Om du konfigurerar brand Väggs kluster att ha en virtuell IP-adress måste du bryta klustret och exponera VPN-enheten direkt till ett offentligt gränssnitt som gatewayen kan använda.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Steg 7 kontrol lera om den lokala VPN-enheten har Perfect Forward Secrecy aktiverat

Funktionen för **PFS (Perfect Forward Secrecy** ) kan orsaka från kopplings problem. Om VPN-enheten har **Perfect Forward Secrecy** aktiverat inaktiverar du funktionen. [Uppdatera sedan IPsec-principen för den virtuella Nätverksgatewayen](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en plats-till-plats-anslutning till ett virtuellt nätverk](./tutorial-site-to-site-portal.md)
- [Konfigurera IPsec/IKE-princip för plats-till-plats-VPN-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md)
