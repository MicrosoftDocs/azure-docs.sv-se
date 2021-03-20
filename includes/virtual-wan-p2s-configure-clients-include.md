---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91812782"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. Ladda ned och installera OpenVPN-klienten från den officiella webbplatsen.
1. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken User VPN-konfigurationer i Azure Portal eller New-AzureRmVpnClientConfiguration i PowerShell.
1. Packa upp profilen. Öppna konfigurationsfilen vpnconfig.ovpn från OpenVPN-mappen i Anteckningar.
1. Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM formaterat certifikat kan du öppna. CER-filen och kopiera över den base64-nyckeln mellan certifikat rubrikerna. Anvisningar finns i [så här exporterar du ett certifikat för att hämta den kodade offentliga nyckeln.](../articles/virtual-wan/certificates-point-to-site.md)
1. Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Anvisningar finns i [extrahera privat nyckel.](../articles/virtual-wan/howto-openvpn-clients.md#windows).
1. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
1. Kopiera filen vpnconfig.ovpn till mappen C:\Program Files\OpenVPN\config.
1. Högerklicka på ikonen OpenVPN i system fältet och välj **Anslut**.

##### <a name="ikev2"></a>IKEv2

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. Välj ”VpnClientSetupAmd64”-installationspaketet för en 64-bitars processorarkitektur. Välj ”VpnClientSetupX86”-installationspaketet för en 32-bitars processorarkitektur.
1. Dubbelklicka på paketet för att installera det. Om du ser ett SmartScreen-fönster väljer du **Mer information** och **kör ändå**.
1. På klient datorn går du till **nätverks inställningar** och väljer **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.
1. Innan du försöker ansluta kontrollerar du att du har installerat ett klientcertifikat på klientdatorn. Ett klientcertifikat krävs för autentisering när du använder den interna Azure-certifikatautentiseringstypen. Mer information om hur du skapar certifikat finns i [Skapa certifikat](../articles/virtual-wan/certificates-point-to-site.md). Information om hur du installerar ett klient certifikat finns i [Installera ett klient certifikat](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).
