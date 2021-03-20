---
title: Hämta konfigurations skript för VPN-enheter för S2S VPN-anslutningar
description: Den här artikeln vägleder dig genom nedladdning av konfigurations skript för VPN-enheter för S2S VPN-anslutningar med Azure VPN-gatewayer med Azure Resource Manager.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: d2593c656e17310c5afb4a897b94cc8c12a618f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879502"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Hämta konfigurations skript för VPN-enheter för S2S VPN-anslutningar

Den här artikeln vägleder dig genom nedladdning av konfigurations skript för VPN-enheter för S2S VPN-anslutningar med Azure VPN-gatewayer med Azure Resource Manager. I följande diagram visas arbets flödet på den högsta nivån.

![Ladda ned – skript](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Följande enheter har tillgängliga skript:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Om konfigurations skript för VPN-enheter

En VPN-anslutning mellan olika platser består av en Azure VPN-gateway, en lokal VPN-enhet och en IPsec-S2S VPN-tunnel som ansluter två. Vanliga arbets flöden innehåller följande steg:

1. Skapa och konfigurera en Azure VPN-gateway (virtuell nätverksgateway)
2. Skapa och konfigurera en lokal Azure-nätverksgateway som representerar ditt lokala nätverk och VPN-enhet
3. Skapa och konfigurera en Azure VPN-anslutning mellan Azure VPN-gatewayen och den lokala Nätverksgatewayen
4. Konfigurera den lokala VPN-enheten som representeras av den lokala Nätverksgatewayen för att upprätta den faktiska S2S VPN-tunneln med Azure VPN-gatewayen

Du kan slutföra steg 1 till 3 med hjälp av Azure [Portal](./tutorial-site-to-site-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)eller [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Det sista steget omfattar att konfigurera lokala VPN-enheter utanför Azure. Med den här funktionen kan du ladda ned ett konfigurations skript för din VPN-enhet med motsvarande värden för din Azure VPN-gateway, virtuella nätverk och lokala nätverks adressprefix och egenskaper för VPN-anslutning osv. redan ifyllda. Du kan använda skriptet som en start punkt eller tillämpa skriptet direkt på dina lokala VPN-enheter via konfigurations konsolen.

> [!IMPORTANT]
> * Syntaxen för varje konfigurations skript för VPN-enheter skiljer sig, och är kraftigt beroende av modeller och versioner av inbyggd program vara. Ägna särskild uppmärksamhet åt din enhets modell och versions information mot tillgängliga mallar.
> * Vissa parameter värden måste vara unika på enheten och kan inte fastställas utan åtkomst till enheten. De Azure-genererade konfigurations skripten fyller dessa värden i förväg, men du måste se till att de angivna värdena är giltiga på enheten. Exempel:
>    * Gränssnitts nummer
>    * Åtkomst kontrol listans nummer
>    * Princip namn eller siffror osv.
> * Sök efter nyckelordet "**replace**", inbäddat i skriptet för att hitta de parametrar som du behöver verifiera innan du använder skriptet.
> * Vissa mallar innehåller avsnittet "**rensning**" som du kan använda för att ta bort konfigurationerna. Rensnings avsnitten är kommenterade som standard.

## <a name="download-the-configuration-script-from-azure-portal"></a>Ladda ned konfigurations skriptet från Azure Portal

Skapa en Azure VPN-gateway, lokal nätverksgateway och en anslutnings resurs som ansluter de två. Följande sida vägleder dig genom stegen:

* [Skapa en plats-till-plats-anslutning på Azure Portal](./tutorial-site-to-site-portal.md)

När anslutnings resursen har skapats följer du anvisningarna nedan för att ladda ned VPN-enhetens konfigurations skript:

1. Från en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto vid behov.
2. Gå till den anslutnings resurs du skapade. Du kan hitta listan över alla anslutnings resurser genom att klicka på alla tjänster, sedan på nätverk och anslutningar.

    ![anslutnings lista](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Klicka på den anslutning som du vill konfigurera.

    ![anslutning – översikt](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Klicka på länken "Ladda ned konfiguration" som marker ATS i rött på sidan anslutnings översikt. Då öppnas sidan "Ladda ned konfiguration".

    ![Ladda ned – skript-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Välj modell familjen och versionen för den inbyggda program varan för VPN-enheten och klicka på knappen Ladda ned konfiguration.

    ![download66 – skript-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Du uppmanas att spara det nedladdade skriptet (en textfil) från webbläsaren.
7. När du har hämtat konfigurations skriptet öppnar du det med en text redigerare och söker efter nyckelordet "Ersätt" för att identifiera och undersöka de parametrar som kan behöva ersättas.

    ![Redigera – skript](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Ladda ned konfigurations skriptet med Azure PowerShell



Du kan också ladda ned konfigurations skriptet med Azure PowerShell, som du ser i följande exempel:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Tillämpa konfigurations skriptet på VPN-enheten

När du har laddat ned och verifierat konfigurations skriptet är nästa steg att tillämpa skriptet på din VPN-enhet. Den faktiska proceduren varierar beroende på din VPN-enhets och modeller. Läs bruksanvisningen eller instruktions sidorna för dina VPN-enheter.

## <a name="next-steps"></a>Nästa steg

Fortsätt att konfigurera din [plats-till-plats-anslutning](./tutorial-site-to-site-portal.md).