---
title: 'Azure-VPN Gateway: installera ett klient certifikat för punkt-till-plats'
description: Installera klient certifikat för P2S-certifikatautentisering – Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 8c4d37f67d89ad1b7973a85fb6c2a9f7e818a028
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89421670"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Installera klient certifikat för P2S-anslutningar för certifikatautentisering

Alla klienter som ansluter till ett virtuellt nätverk med hjälp av autentisering med punkt-till-plats Azure-certifikat kräver ett klient certifikat. Den här artikeln hjälper dig att installera ett klient certifikat som används för autentisering vid anslutning till ett VNet med hjälp av P2S.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Hämta ett klient certifikat

Oavsett vilket klient operativ system du vill ansluta från måste du alltid ha ett klient certifikat. Du kan generera ett klient certifikat från antingen ett rot certifikat som har genererats med en CA-lösning för företag eller ett självsignerat rot certifikat. Se [PowerShell](vpn-gateway-certificates-point-to-site.md)-, [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)-eller [Linux](vpn-gateway-certificates-point-to-site-linux.md) -instruktionerna för steg för att generera ett klient certifikat. 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>OS

>[!NOTE]
>Mac VPN-klienter stöds endast för distributions modellen i Resource Manager. De stöds inte för den klassiska distributions modellen.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Linux-klientcertifikatet installeras på klienten som en del av klient konfigurationen. Se [klient konfiguration – Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) för instruktioner.

## <a name="next-steps"></a>Nästa steg

Fortsätt med de punkt-till-plats-konfigurations steg som krävs för att [skapa och installera konfigurationsfiler för VPN-klienter](point-to-site-vpn-client-configuration-azure-cert.md).
