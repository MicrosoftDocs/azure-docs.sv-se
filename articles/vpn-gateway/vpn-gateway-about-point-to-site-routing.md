---
title: 'Azure VPN Gateway: om routning av P2S'
description: Lär dig mer om Azures plats-till-plats-VPN-routning för olika operativ system, protokoll för fjärråtkomst och konfigurationer för virtuella nätverk.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0b9b8ba555cddd56c49c750709e69ec180291c95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91827173"
---
# <a name="about-point-to-site-vpn-routing"></a>Om VPN-routning från punkt till plats

Den här artikeln hjälper dig att förstå hur Azure punkt-till-plats-VPN-routning fungerar. Beteendet för P2S VPN-routning är beroende av klientens operativ system, protokollet som används för VPN-anslutningen och hur de virtuella nätverken (virtuella nätverk) är anslutna till varandra.

Azure har för närvarande stöd för två protokoll för fjärråtkomst, IKEv2 och SSTP. IKEv2 stöds på många klient operativ system, inklusive Windows, Linux, macOS, Android och iOS. SSTP stöds endast i Windows. Om du gör en ändring i nätverkets topologi och har Windows VPN-klienter, måste VPN-klientprogrammet för Windows-klienter laddas ned och installeras igen för att ändringarna ska tillämpas på klienten.

> [!NOTE]
> Den här artikeln gäller endast IKEv2.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>Om diagram

Det finns ett antal olika diagram i den här artikeln. I varje avsnitt visas en annan topologi eller konfiguration. I den här artikeln fungerar plats-till-plats (S2S) och VNet-till-VNet-anslutningar på samma sätt, eftersom båda är IPsec-tunnlar. Alla VPN-gatewayer i den här artikeln är Route-baserade.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Ett isolerat VNet

Punkt-till-plats-VPN gateway-anslutningen i det här exemplet är för ett VNet som inte är anslutet eller peer-kopplas till något annat virtuellt nätverk (VNet1). I det här exemplet kan klienterna komma åt VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg" alt-text="Isolerad VNet-routning" lightbox="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg":::

### <a name="address-space"></a>Adressutrymme

* VNet1:10.1.0.0/16

### <a name="routes-added"></a>Tillagda vägar

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som lagts till för klienter som inte kommer från Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter har åtkomst till VNet1

* Icke-Windows-klienter har åtkomst till VNet1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Flera peer-virtuella nätverk

I det här exemplet är punkt-till-plats-VPN gateway-anslutningen för VNet1. VNet1 är peer-kopplad med VNet2. VNet 2 är peer-kopplat med VNet3. VNet1 är peer-kopplad med VNet4. Det finns ingen direkt peering mellan VNet1 och VNet3. VNet1 har inställningen "Tillåt Gateway-överföring" och VNet2 och VNet4 har aktiverat "Använd fjärrgatewayer".

Klienter som använder Windows kan komma åt direkt peer-virtuella nätverk, men VPN-klienten måste hämtas igen om några ändringar görs i VNet-peering eller nätverk sto pol Ogin. Icke-Windows-klienter kan komma åt direkt peer-virtuella nätverk. Åtkomsten är inte transitiv och är begränsad till endast direkt peer-virtuella nätverk.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg" alt-text="Flera peer-virtuella nätverk" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg":::

### <a name="address-space"></a>Adress utrymme:

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* VNet4:10.4.0.0/16

### <a name="routes-added"></a>Tillagda vägar

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Vägar som har lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1, VNet2 och VNet4, men VPN-klienten måste laddas ned igen för att alla ändringar i miljön ska börja gälla.

* Icke-Windows-klienter kan komma åt VNet1, VNet2 och VNet4

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Flera virtuella nätverk anslutna med en S2S VPN

I det här exemplet är punkt-till-plats-VPN gateway-anslutningen för VNet1. VNet1 är anslutet till VNet2 med en VPN-anslutning från plats till plats. VNet2 är anslutet till VNet3 med en VPN-anslutning från plats till plats. Det finns ingen direkt peering-eller plats-till-plats-VPN-anslutning mellan VNet1 och VNet3. Alla plats-till-plats-anslutningar kör inte BGP för routning.

Klienter som använder Windows, eller ett annat operativ system som stöds, kan bara komma åt VNet1. BGP måste användas för att få åtkomst till ytterligare virtuella nätverk.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg" alt-text="Flera virtuella nätverk och S2S" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg":::

### <a name="address-space"></a>Adressutrymme

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>Tillagda vägar

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan endast komma åt VNet1

* Icke-Windows-klienter kan endast komma åt VNet1

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Flera virtuella nätverk som är anslutna via en S2S VPN (BGP)

I det här exemplet är punkt-till-plats-VPN gateway-anslutningen för VNet1. VNet1 är anslutet till VNet2 med en VPN-anslutning från plats till plats. VNet2 är anslutet till VNet3 med en VPN-anslutning från plats till plats. Det finns ingen direkt peering-eller plats-till-plats-VPN-anslutning mellan VNet1 och VNet3. Alla plats-till-plats-anslutningar kör BGP för routning.

Klienter som använder Windows, eller ett annat operativ system som stöds, har åtkomst till alla virtuella nätverk som är anslutna via en plats-till-plats-VPN-anslutning, men vägar till anslutna virtuella nätverk måste läggas till manuellt på Windows-klienterna.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg" alt-text="Flera virtuella nätverk och S2S (BGP)" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg":::

### <a name="address-space"></a>Adressutrymme

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>Tillagda vägar

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16

* Vägar som har lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1, VNet2 och VNet3, men vägar till VNet2 och VNet3 måste läggas till manuellt.

* Icke-Windows-klienter kan komma åt VNet1, VNet2 och VNet3

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Ett VNet och ett avdelnings kontor

I det här exemplet är punkt-till-plats-VPN gateway-anslutningen för VNet1. VNet1 är inte anslutet/peer-kopplas till andra virtuella nätverk, men är anslutet till en lokal plats via en VPN-anslutning från plats till plats som inte kör BGP.

Windows-och icke-Windows-klienter kan bara komma åt VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg" alt-text="Routning med ett VNet och ett avdelnings kontor" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg":::

### <a name="address-space"></a>Adressutrymme

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Tillagda vägar

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som lagts till för klienter som inte kommer från Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter har endast åtkomst till VNet1

* Icke-Windows-klienter kan endast komma åt VNet1

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Ett VNet och ett avdelnings kontor (BGP)

I det här exemplet är punkt-till-plats-VPN gateway-anslutningen för VNet1. VNet1 är inte anslutet eller peer-kopplas till andra virtuella nätverk, men är anslutet till en lokal plats (Site1) via en VPN-anslutning från plats till plats som kör BGP.

Windows-klienter kan komma åt VNet och avdelnings kontoret (Site1), men vägarna till Site1 måste läggas till manuellt i klienten. Icke-Windows-klienter kan komma åt VNet samt det lokala avdelnings kontoret.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg" alt-text="Routning med ett VNet och ett avdelnings kontor – BGP" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg":::

### <a name="address-space"></a>Adressutrymme

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Tillagda vägar

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter har åtkomst till VNet1 och Site1, men vägar till Site1 måste läggas till manuellt.

* Icke-Windows-klienter kan komma åt VNet1 och Site1.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Flera virtuella nätverk anslutna med S2S och ett avdelnings kontor

I det här exemplet är punkt-till-plats-VPN gateway-anslutningen för VNet1. VNet1 är anslutet till VNet2 med en VPN-anslutning från plats till plats. VNet2 är anslutet till VNet3 med en VPN-anslutning från plats till plats. Det finns ingen direkt peering-eller plats-till-plats-VPN-tunnel mellan VNet1-och VNet3-nätverken. VNet3 är anslutet till ett avdelnings kontor (Site1) med en VPN-anslutning från plats till plats. Alla VPN-anslutningar kör inte BGP.

Alla klienter har endast åtkomst till VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg" alt-text="Diagram som visar en S2S för flera VNet och ett avdelnings kontor" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg":::

### <a name="address-space"></a>Adressutrymme

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Tillagda vägar

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som har lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienterna har endast åtkomst till VNet1

* Icke-Windows-klienter kan endast komma åt VNet1

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Flera virtuella nätverk anslutna med S2S och ett avdelnings kontor (BGP)

I det här exemplet är punkt-till-plats-VPN gateway-anslutningen för VNet1. VNet1 är anslutet till VNet2 med en VPN-anslutning från plats till plats. VNet2 är anslutet till VNet3 med en VPN-anslutning från plats till plats. Det finns ingen direkt peering-eller plats-till-plats-VPN-tunnel mellan VNet1-och VNet3-nätverken. VNet3 är anslutet till ett avdelnings kontor (Site1) med en VPN-anslutning från plats till plats. Alla VPN-anslutningar kör BGP.

Klienter som använder Windows kan komma åt virtuella nätverk och platser som är anslutna via en plats-till-plats-VPN-anslutning, men vägarna till VNet2, VNet3 och Site1 måste läggas till manuellt i klienten. Icke-Windows-klienter kan komma åt virtuella nätverk och platser som är anslutna via en plats-till-plats-VPN-anslutning utan någon manuell åtgärd. Åtkomsten är transitiv och klienter kan komma åt resurser i alla anslutna virtuella nätverk och platser (lokalt).

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg" alt-text="S2S för flera VNet och avdelnings kontor" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg":::

### <a name="address-space"></a>Adressutrymme

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Tillagda vägar

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som har lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienterna har åtkomst till VNet1, VNet2, VNet3 och Site1, men vägar till VNet2, VNet3 och Site1 måste läggas till manuellt i klienten.

* Icke-Windows-klienter kan komma åt VNet1, Vnet2, VNet3 och Site1.

## <a name="next-steps"></a>Nästa steg

Se [skapa en P2s VPN med hjälp av Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md) för att börja skapa din P2s VPN.
