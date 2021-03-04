---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048273"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. Gå till **alla resurser** och välj det virtuella WAN som du skapade och välj sedan **User VPN-konfigurationer** på menyn till vänster.
1. På sidan **VPN-konfigurationer för användare** väljer du **+ skapa användare VPN-konfiguration** längst upp på sidan för att öppna sidan **Skapa ny användare VPN-konfiguration** .

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Skärm bild av sidan med VPN-konfigurationer för användare.":::

1. På fliken **grundläggande** , under **information om instans**, anger du det **namn** som du vill tilldela VPN-konfigurationen.
1. För **tunnel typ**, i list rutan, väljer du den tunnel typ som du vill använda. Alternativen för tunnel typ är: **IKEV2 VPN**, **OpenVPN** och **OpenVPN och IKEv2**.
1. Använd följande steg som motsvarar den tunnel typ som du har valt. När du har angett alla värden klickar du på **Granska + skapa** och sedan **skapa** för att skapa konfigurationen.

   **IKEv2 VPN**

   * **Krav:** När du väljer **IKEv2** -tunnel typen visas ett meddelande som uppmanar dig att välja en autentiseringsmetod. För IKEv2 kan du bara ange en autentiseringsmetod. Du kan välja Azure-certifikat, Azure Active Directory eller RADIUS-baserad autentisering.

   * **Anpassade IPsec-parametrar:** Om du vill anpassa parametrarna för IKE fas 1 och IKE fas 2 växlar du till **anpassad** IPSec-växel och väljer parameter värden. Mer information om anpassningsbara parametrar finns i artikeln [anpassad IPsec](../articles/virtual-wan/point-to-site-ipsec.md) .

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Skärm bild av IPsec-växel till anpassad.":::

   * **Autentisering:** Navigera till den autentiseringsmekanism som du vill använda genom att antingen klicka på **Nästa** längst ned på sidan för att gå vidare till autentiseringsmetoden, eller klicka på lämplig flik överst på sidan. Välj metoden genom att växla växeln till **Ja** .

     I det här exemplet är RADIUS-autentisering valt. För RADIUS-baserad autentisering kan du ange en sekundär RADIUS-servers IP-adress och Server hemlighet.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="Skärm bild av IKE.":::

   **OpenVPN**

   * **Krav:** När du väljer tunnel typen **OpenVPN** visas ett meddelande som uppmanar dig att välja en autentiseringsmekanism. Om OpenVPN har valts som tunnel typ kan du ange flera autentiseringsmetoder. Du kan välja valfri delmängd av Azure-certifikat, Azure Active Directory eller RADIUS-baserad autentisering. För RADIUS-baserad autentisering kan du ange en sekundär RADIUS-servers IP-adress och Server hemlighet.

   * **Autentisering:** Navigera till den eller de autentiseringsmetoder som du vill använda genom att antingen klicka på **Nästa** längst ned på sidan för att gå vidare till autentiseringsmetoden, eller klicka på lämplig flik överst på sidan.
   För varje metod som du vill välja växlar du till **Ja** och anger lämpliga värden.

     I det här exemplet är Azure Active Directory markerat.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="Skärm bild av OpenVPN-sidan.":::
