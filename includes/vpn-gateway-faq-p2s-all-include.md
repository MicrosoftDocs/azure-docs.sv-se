---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a3c10ca35ee2f085d4ce41e862a895ff17ff63a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84317647"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Hur många slutpunkter på VPN-klienter kan jag ha i min punkt-till-plats-konfiguration?

Det beror på Gateway-SKU: n. Mer information om antalet anslutningar som stöds finns i Gateway- [SKU: er](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a><a name="supportedclientos"></a>Vilka klient operativ system kan jag använda med punkt-till-plats?

Följande klientoperativsystem stöds:

* Windows 7 (32-bitars och 64-bitars)
* Windows Server 2008 R2 (endast 64-bitars)
* Windows 8.1 (32-bitars och 64-bitars)
* Windows Server 2012 (endast 64-bitars)
* Windows Server 2012 R2 (endast 64-bitars)
* Windows Server 2016 (endast 64-bitars)
* Windows Server 2019 (endast 64-bitars)
* Windows 10
* Mac OS X version 10,11 eller senare
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Kan jag korsa proxyservrar och brandväggar med punkt-till-plats-funktionen?

Azure stöder tre typer av VPN-alternativ för punkt-till-plats:

* SSTP (Secure Socket Tunneling Protocol). SSTP är en Microsoft-patentskyddad SSL-baserad lösning som kan tränga brand väggar eftersom de flesta brand väggar öppnar den utgående TCP-port som 443 SSL använder.

* OpenVPN. OpenVPN är en SSL-baserad lösning som kan tränga in brand väggar eftersom de flesta brand väggar öppnar den utgående TCP-port som 443 SSL använder.

* IKEv2 VPN. IKEv2 VPN är en standardbaserad IPsec VPN-lösning som använder utgående UDP-portar på 500 och 4500 och IP-protokoll nr. 50. Eftersom inte alla brandväggar öppnar dessa portar kan det hända att IKEv2 VPN inte kan komma igenom proxyservrar och brandväggar.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Om jag startar om en klientdator som konfigurerats för punkt-till-plats, kommer då VPN att återansluta automatiskt?

Som standard kommer klientdatorn inte återupprätta VPN-anslutningen automatiskt.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Har punkt-till-plats stöd för automatisk återanslutning och DDNS på VPN-klienterna?

Automatisk återanslutning och DDNS stöds för närvarande inte i punkt-till-plats-VPN:er.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Kan jag ha plats-till-plats- och punkt-till-plats-konfigurationer samtidigt på samma virtuella nätverk?

Ja. Med Resource Manager-distributionsmodellen krävs en gateway med routningsbaserad VPN. I den klassiska distributionsmodellen måste du ha en dynamisk gateway. Punkt-till-plats stöds inte för VPN-gatewayer med statisk routning eller principbaserade VPN-gatewayer.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-network-gateways-at-the-same-time"></a>Kan jag konfigurera en punkt-till-plats-klient för att ansluta till flera virtuella nätverksgateway samtidigt?

Beroende på vilket VPN-klientprogramvaran som används kan det hända att du kan ansluta till flera Virtual Network gatewayer förutsatt att de virtuella nätverk som är anslutna till inte har några adress utrymmen i konflikt mellan dem eller nätverket från-klienten som ansluter från.  Även om Azure VPN-klienten har stöd för många VPN-anslutningar kan endast en anslutning anslutas vid en specifik tidpunkt.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Kan jag konfigurera en punkt-till-plats-klient så att den ansluter till flera virtuella nätverk samtidigt?

Ja, punkt-till-plats-anslutningar till en Virtual Network gateway som distribueras i ett VNet som peer-kopplas med andra virtuella nätverk kan ha åtkomst till andra peer-virtuella nätverk.  Under förutsättning att peer-virtuella nätverk använder UseRemoteGateway/AllowGatewayTransit-funktionerna kommer punkt-till-plats-klienten att kunna ansluta till dessa peer-virtuella nätverk.  Mer information finns i [den här](../articles/vpn-gateway/vpn-gateway-about-point-to-site-routing.md) artikeln.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Hur högt dataflöde kan jag förvänta mig vid anslutningar för plats-till-plats eller punkt-till-plats?

Det är svårt att bibehålla ett exakt dataflöde i VPN-tunnlarna. IPsec och SSTP är kryptografifrekventa VPN-protokoll. Dataflödet är också begränsat av svarstiden och bandbredden mellan din lokala plats och Internet. För VPN Gateway med endast IKEv2-baserade VPN-anslutningar från punkt till plats beror det totala dataflödet som du kan förvänta dig på Gateway-SKU:n. Mer information om dataflödet finns i avsnittet om [Gateway-SKU:er](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Kan jag använda alla VPN-programklienter för punkt-till-plats som har stöd för SSTP och/eller IKEv2?

Nej. Du kan bara använda den inbyggda VPN-klienten i Windows för SSTP, och den inbyggda VPN-klienten i Mac för IKEv2. Du kan dock använda OpenVPN-klienten på alla plattformar för att ansluta över OpenVPN-protokollet. Se listan över klientoperativsystem som stöds.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Har Azure stöd för IKEv2 VPN-anslutningar i Windows?

IKEv2 stöds på Windows 10 och Server 2016. Om du vill använda IKEv2 måste du installera uppdateringar och ange ett registreringsnyckelvärde lokalt. OS-versioner före Windows 10 stöds inte och kan endast använda SSTP-eller **OpenVPN-® protokoll**.

Förbereda Windows 10 eller Server 2016 för IKEv2:

1. Installera uppdateringen.

   | OS-version | Datum | Antal/länk |
   |---|---|---|
   | Windows Server 2016<br>Windows 10, version 1607 | 17 januari 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, version 1703 | 17 januari 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 version 1709 | Den 22 mars 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. Ange registernyckelvärdet. Skapa eller ange "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload" REG_DWORD-nyckeln i registret till 1.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>Vad händer när jag konfigurerar både SSTP och IKEv2 för P2S VPN-anslutningar?

När du konfigurerar både SSTP och IKEv2 i en blandad miljö (som består av Windows- och Mac-enheter) testar alltid Windows VPN-klienten IKEv2-tunneln först, men går tillbaka till SSTP om IKEv2-anslutningen inte lyckas. MacOSX ansluter endast via IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>För vilka andra plattformar förutom Windows och Mac har Azure stöd för P2S VPN?

Azure har stöd för Windows, Mac och Linux för P2S-VPN.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Jag har redan en Azure VPN Gateway distribuerad. Kan jag aktivera RADIUS och/eller IKEv2 VPN på den?

Ja, du kan aktivera dessa nya funktioner på gatewayer som redan har distribuerats genom Powershell- eller Azure Portal, förutsatt att gateway-SKU:n som du använder stöder RADIUS och/eller IKEv2. Till exempel stöder inte SKU:n för VPN-gatewayen RADIUS eller IKEv2 på Basic-nivån.

### <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a><a name="removeconfig"></a>Hur gör jag för att bort konfigurationen av en P2S-anslutning?

En P2S-konfiguration kan tas bort med hjälp av Azure CLI och PowerShell med följande kommandon:

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$gw=Get-AzVirtualNetworkGateway -name <gateway-name>`  
$gw.VPNClientConfiguration = $null`  
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw`
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"
```
