---
title: 'Azure VPN Gateway: om P2S VPN-klientinställningar'
description: Använd den här artikeln för att hitta den information du behöver för en VPN-klient profil.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979084"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>Arbeta med P2S VPN-klient profil filer

Profilmappar innehåller information som krävs för att konfigurera en VPN-anslutning. Den här artikeln hjälper dig att få och förstå den information som krävs för en VPN-klient profil.

## <a name="generate-and-download-profile"></a>Skapa och ladda ned profil

Du kan generera konfigurationsfiler för klienter med hjälp av PowerShell, eller med hjälp av Azure Portal. Båda metoderna returnerar samma zip-fil.

### <a name="portal"></a>Portal

1. I Azure Portal navigerar du till den virtuella Nätverksgatewayen för det virtuella nätverk som du vill ansluta till.
1. På sidan virtuell nätverksgateway väljer du **punkt-till-plats-konfiguration**.
1. Överst på sidan punkt-till-plats-konfiguration väljer du **Hämta VPN-klient**. Det tar några minuter för klient konfigurations paketet att genereras.
1. Din webbläsare indikerar att en zip-fil för klient konfiguration är tillgänglig. Den heter samma namn som din gateway. Zippa upp filen för att visa mapparna.

### <a name="powershell"></a>PowerShell

Om du vill generera med PowerShell kan du använda följande exempel:

1. När du genererar konfigurationsfiler för VPN-klienter är värdet för "-AuthenticationMethod" EapTls ". Generera VPN-klientens konfigurationsfiler med följande kommando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Kopiera URL: en till webbläsaren för att ladda ned ZIP-filen och packa sedan upp filen för att visa mapparna.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Mappen OpenVPN** innehåller den *OVPN* -profil som måste ändras för att inkludera nyckeln och certifikatet. Mer information finns i [konfigurera OpenVPN-klienter för Azure VPN gateway](vpn-gateway-howto-openvpn-clients.md#windows). Om Azure AD-autentisering har valts på VPN-gatewayen finns inte den här mappen i zip-filen. Navigera i stället till mappen AzureVPN och leta upp azurevpnconfig.xml.

## <a name="next-steps"></a>Nästa steg

Mer information om punkt-till-plats finns i [om punkt-till-plats](point-to-site-about.md).
