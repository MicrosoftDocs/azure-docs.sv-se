---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979081"
---
## <a name="extract-the-zip-file"></a>Extrahera zip-filen

Extrahera zip-filen. Filen innehåller följande mappar:

* AzureVPN
* Allmänna
* OpenVPN (om du har aktiverat OpenVPN med inställningar för **Azure-certifikat** eller **RADIUS-autentisering** på gatewayen). Välj lämplig artikel som motsvarar konfigurationen för att skapa en klient.

  * [VPN gateway – skapa en klient](../articles/vpn-gateway/openvpn-azure-ad-tenant.md).
  * [Virtuellt WAN – skapa en klient](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="retrieve-information"></a>Hämta information

I mappen **AzureVPN** navigerar du till **_azurevpnconfig.xml_** -filen och öppnar den med anteckningar. Anteckna texten mellan följande taggar.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profil information

När du lägger till en anslutning använder du den information som du samlade in i föregående steg för profil informations sidan. Fälten motsvarar följande information:

* **Mål grupp:** Identifierar den mottagar resurs som token är avsedd för.
* **Utfärdare:** Identifierar säkerhetstokentjänst som utsänt token samt Azure AD-klienten.
* **Klient organisation:** Innehåller en oföränderlig, unik identifierare för den katalog klient som utfärdade token.
* **Fullständigt domän namn:** Det fullständigt kvalificerade domän namnet (FQDN) på Azure VPN-gatewayen.
* **ServerSecret:** Nyckeln för den fördelade VPN-gatewayen.

## <a name="folder-contents"></a>Mappinnehåll

* Den **allmänna mappen** innehåller det offentliga Server certifikatet och den VpnSettings.xml filen. VpnSettings.xml-filen innehåller information som krävs för att konfigurera en allmän klient.

* Den hämtade ZIP-filen kan också innehålla **WindowsAmd64** -och **WindowsX86** -mappar. Dessa mappar innehåller installations programmet för SSTP och IKEv2 för Windows-klienter. Du måste ha administratörs behörighet på klienten för att installera dem.
