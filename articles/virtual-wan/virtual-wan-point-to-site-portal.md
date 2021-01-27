---
title: 'Självstudie: Använd Azure Virtual WAN för att skapa en punkt-till-plats-anslutning till Azure'
description: I den här självstudien förklarar vi hur du skapar en punkt-till-plats-VPN-anslutning till Azure med Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/09/2020
ms.author: cherylmc
ms.openlocfilehash: 1876ab86e6f4c46edc23361dd884d8b32328f36c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919080"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Självstudie: skapa en VPN-anslutning för användare med Azure Virtual WAN

I den här självstudien förklarar vi hur du använder Virtual WAN för att ansluta till dina resurser i Azure via en IPsec/IKE-anslutning (IKEv2) eller en OpenVPN VPN-anslutning. Den här typen av anslutning kräver att VPN-klienten konfigureras på klient datorn. Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt WAN
> * Skapa en P2S-konfiguration
> * Skapa en virtuell hubb
> * Välj klient adress grupper
> * Ange DNS-servrar
> * Generera konfigurations paket för VPN-klient profil
> * Konfigurera VPN-klienter
> * Visa virtuellt WAN

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Skapa ett virtuellt WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Skapa en P2S-konfiguration

En punkt-till-plats-konfiguration (P2S) definierar parametrar för att ansluta fjärrklienter.

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>Skapa virtuell hubb och gateway

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]


## <a name="choose-p2s-client-address-pools"></a><a name="chooseclientpools"></a> Välj P2S-klient adress grupper

[!INCLUDE [Choose pools](../../includes/virtual-wan-allocating-p2s-pools.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Ange DNS-Server

Du kan konfigurera den här inställningen när du skapar hubben, eller ändra den vid ett senare tillfälle. Du ändrar genom att leta upp den virtuella hubben. Under **användar-VPN (peka på plats)**, väljer du **Konfigurera** och anger DNS-serverns IP-adress (er) i den **anpassade DNS-** serverns text ruta (er). Du kan ange upp till fem DNS-servrar.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="anpassad DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>Generera profil paket för VPN-klienten

Skapa och ladda ned VPN-klientens profil paket för att konfigurera dina VPN-klienter.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>Konfigurera VPN-klienter

Använd det hämtade profil paketet för att konfigurera VPN-klienter för fjärråtkomst. Proceduren för varje operativ system är annorlunda. Följ anvisningarna som gäller ditt system.
När du har konfigurerat klienten kan du ansluta.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visa virtuellt WAN

1. Navigera till det virtuella WAN-nätverket.
1. På sidan **Översikt** representerar varje punkt på kartan ett nav.
1. I avsnittet **hubbar och anslutningar** kan du Visa nav status, plats, region, status för VPN-anslutning och byte in och ut.



## <a name="clean-up-resources"></a><a name="cleanup"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Sedan kan du läsa mer om virtuellt WAN-nätverk i:

> [!div class="nextstepaction"]
> * [Vanliga frågor om Virtual WAN](virtual-wan-faq.md)
