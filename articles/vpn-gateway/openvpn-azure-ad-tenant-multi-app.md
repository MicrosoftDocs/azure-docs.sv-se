---
title: 'VPN Gateway: Azure AD-klient för olika användar grupper: Azure AD-autentisering'
description: Du kan använda P2S VPN för att ansluta till ditt VNet med Azure AD-autentisering
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 1305ca603aef63dafcc7b055d55e3f0fe281f4fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91819668"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Skapa en Active Directory-klient (AD) för P2S OpenVPN-protokoll anslutningar

När du ansluter till ditt VNet kan du använda certifikatbaserad autentisering eller RADIUS-autentisering. Men när du använder det öppna VPN-protokollet kan du också använda Azure Active Directory autentisering. Om du vill att en annan uppsättning användare ska kunna ansluta till olika VPN-gatewayer kan du registrera flera appar i AD och länka dem till olika VPN-gatewayer. Den här artikeln hjälper dig att skapa en Azure AD-klient för P2S OpenVPN-autentisering och skapa och registrera flera appar i Azure AD för att ge olika åtkomst till olika användare och grupper.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Aktivera autentisering på gatewayen

I det här steget ska du aktivera Azure AD-autentisering på VPN-gatewayen.

1. Aktivera Azure AD-autentisering på VPN-gatewayen genom att gå till **punkt-till-plats-konfiguration** och plocknings- **OpenVPN (SSL)** som **tunnel typ**. Välj **Azure Active Directory** som **Autentiseringstyp** och fyll i informationen i avsnittet **Azure Active Directory** .

    ![Azure Portal vy](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > Använd inte Azure VPN-klientens program-ID: det ger alla användare åtkomst till VPN-gatewayen. Använd ID: t för det/de program som du har registrerat.

2. Skapa och ladda ned profilen genom att klicka på länken **Ladda ned VPN-klient** .

3. Extrahera den hämtade ZIP-filen.

4. Bläddra till mappen unzippad "AzureVPN".

5. Anteckna platsen för filen "azurevpnconfig.xml". azurevpnconfig.xml innehåller inställningen för VPN-anslutningen och kan importeras direkt till Azure VPN-klientprogrammet. Du kan också distribuera filen till alla användare som behöver ansluta via e-post eller på annat sätt. Användaren måste ha giltiga autentiseringsuppgifter för Azure AD för att kunna ansluta.

## <a name="next-steps"></a>Nästa steg

För att kunna ansluta till ditt virtuella nätverk måste du skapa och konfigurera en profil för VPN-klienter. Se [Konfigurera en VPN-klient för P2s VPN-anslutningar](openvpn-azure-ad-client.md).
