---
title: Över gång till OpenVPN eller IKEv2 från SSTP | Azure-VPN Gateway
description: Den här artikeln hjälper dig att förstå hur du kan lösa 128 samtidiga anslutnings gränsen för SSTP.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: e2fa265e580bc0e752498284ed50e398b59423fd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97657149"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Över gång till OpenVPN-protokoll eller IKEv2 från SSTP

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här artikeln gäller distributions modellen i Resource Manager och pratar om sätt att lösa 128 samtidiga anslutnings gränsen för SSTP genom att övergå till OpenVPN-protokoll eller IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Vilket protokoll används av P2S?

Punkt-till-plats-VPN kan använda något av följande protokoll:

* **OpenVPN &reg; Protokoll**, ett SSL/TLS-baserat VPN-protokoll. En SSL VPN-lösning kan tränga in brand väggar, eftersom de flesta brand väggar öppnar TCP-port 443 utgående, som SSL använder. OpenVPN kan användas för att ansluta från Android, iOS (version 11,0 och senare), Windows-, Linux-och Mac-enheter (OSX-versioner 10,13 och senare).

* **SSTP (Secure Socket Tunneling Protocol)**, ett patentskyddat SSL-baserat VPN-protokoll. En SSL VPN-lösning kan tränga in brand väggar, eftersom de flesta brand väggar öppnar TCP-port 443 utgående, som SSL använder. SSTP stöds endast på Windows-enheter. Azure stöder alla versioner av Windows som har SSTP (Windows 7 och senare). **SSTP har stöd för upp till 128 samtidiga anslutningar endast oavsett Gateway-SKU: n**.

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).


>[!NOTE]
>IKEv2-och OpenVPN för P2S är endast tillgängliga för distributions modellen Resource Manager. De är inte tillgängliga för den klassiska distributions modellen. Basic Gateway SKU stöder inte IKEv2-eller OpenVPN-protokoll. Om du använder Basic SKU måste du ta bort och återskapa en Virtual Network Gateway för produktions-SKU.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migrera från SSTP till IKEv2 eller OpenVPN

Det kan finnas fall när du vill stödja fler än 128 samtidiga P2S-anslutningar till en VPN-gateway men använder SSTP. I sådana fall måste du gå över till IKEv2-eller OpenVPN-protokollet.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Alternativ 1 – Lägg till IKEv2 förutom SSTP på gatewayen

Detta är det enklaste alternativet. SSTP och IKEv2 kan samverka på samma gateway och ge dig ett högre antal samtidiga anslutningar. Du kan helt enkelt aktivera IKEv2 på den befintliga gatewayen och ladda ned klienten igen.

Att lägga till IKEv2 till en befintlig SSTP VPN gateway påverkar inte befintliga klienter och du kan konfigurera dem att använda IKEv2 i små batchar eller bara konfigurera nya klienter att använda IKEv2. Om en Windows-klient har kon figurer ATS för både SSTP och IKEv2 försöker den ansluta med IKEV2 först och om det Miss lyckas, kommer det att gå tillbaka till SSTP.

**IKEv2 använder UDP-portar som inte är standard, så du måste se till att portarna inte är blockerade i användarens brand vägg. Portarna som används är UDP 500 och 4500.**

Om du vill lägga till IKEv2 till en befintlig gateway går du bara till fliken "punkt-till-plats-konfiguration" under Virtual Network gateway i portalen och väljer **IKEv2 och SSTP (SSL)** i list rutan.

![Skärm bild som visar sidan "punkt-till-plats-konfiguration" med list rutan "tunnel typ" öppen, och "IKEv2 och SSTP (SSL)" har valts.](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Alternativ 2 – ta bort SSTP och aktivera OpenVPN på gatewayen

Eftersom SSTP och OpenVPN båda är TLS-baserade protokoll, kan de inte finnas på samma Gateway. Om du väljer att flytta bort från SSTP till OpenVPN måste du inaktivera SSTP och aktivera OpenVPN på gatewayen. Den här åtgärden gör att befintliga klienter förlorar anslutningen till VPN-gatewayen tills den nya profilen har kon figurer ATS på klienten.

Du kan aktivera OpenVPN längs sidan med IKEv2 om du vill. OpenVPN är TLS-baserat och använder standard porten TCP 443. Om du vill växla till OpenVPN går du till fliken "punkt-till-plats-konfiguration" under Virtual Network gateway i portalen och väljer **OpenVPN (SSL)** eller **IKEv2 och OpenVPN (SSL)** i list rutan.

![punkt-till-plats](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

När gatewayen har kon figurer ATS kommer befintliga klienter inte att kunna ansluta förrän du [distribuerar och konfigurerar OpenVPN-klienterna](./vpn-gateway-howto-openvpn-clients.md).

Om du använder Windows 10 kan du också använda [Azure VPN-klienten för Windows](./openvpn-azure-ad-client.md#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
### <a name="what-are-the-client-configuration-requirements"></a>Vilka är klientkonfigurationskraven?

>[!NOTE]
>För Windows-klienter måste du ha administratörs behörighet på klient enheten för att kunna initiera VPN-anslutningen från klient enheten till Azure.
>

Användarna använder inbyggda VPN-klienter på Windows-och Mac-enheter för P2S. Azure tillhandahåller en zip-fil för VPN-klientkonfiguration som innehåller inställningar som krävs av dessa interna klienter för att ansluta till Azure.

* VPN-klientens konfiguration för Windows-enheter består av ett installations paket som användarna installerar på sina enheter.
* För Mac-enheter består den av den mobileconfig-fil som användarna installerar på sina enheter.

Zip-filen innehåller också värdena för några av de viktiga inställningarna på Azure-sidan som du kan använda för att skapa din egen profil för dessa enheter. Några av värdena är VPN gateway-adressen, konfigurerade tunnel typer, vägar och rot certifikatet för gateway-verifiering.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Vilka Gateway-SKU: er stöder P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Rekommendationer för gateway-SKU finns i [om VPN Gateway inställningar](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Bas-SKU:n stöder inte IKEv2- eller RADIUS-autentisering.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Vilka IKE/IPsec-principer har kon figurer ATS på VPN-gatewayer för P2S?


**IKEv2**

| **Chiffer** | **Integritet** | **PRF** | **DH-grupp** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **Chiffer** | **Integritet** | **PFS-grupp** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Vilka TLS-principer är konfigurerade på VPN-gatewayer för P2S?
**TLS**

|**Principer** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Hur gör jag för att konfigurerar du en P2S-anslutning?

En P2S-konfiguration kräver ett par olika steg. Följande artiklar innehåller stegen för att vägleda dig genom P2S-konfigurationen och länkar till att konfigurera VPN-klient enheter:

* [Konfigurera en P2S-anslutning – RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – autentisering med internt Azure-certifikat](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurera OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera en P2S-anslutning – RADIUS-autentisering](point-to-site-how-to-radius-ps.md)

* [Konfigurera en P2S-anslutning – autentisering med internt Azure-certifikat](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**