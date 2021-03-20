---
title: 'VPN Gateway: ändra gatewayens IP-adress inställningar: Azure Portal'
description: Den här artikeln vägleder dig genom att ändra IP-adressprefix för din lokala nätverksgateway med hjälp av Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92143158"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Ändra inställningar för lokal nätverksgateway med Azure-portalen

Ibland ändras inställningarna för din lokala nätverksgateway AddressPrefix eller GatewayIPAddress. Den här artikeln visar hur du ändrar inställningarna för din lokala nätverksgateway. Du kan också ändra dessa inställningar med en annan metod genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Konfiguration av lokal nätverksgateway

Skärm bilden nedan visar **konfigurations** sidan för en lokal nätverks-gateway-resurs med hjälp av den offentliga IP-adress slut punkten:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="IP-adressinställningar" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

Detta är konfigurations sidan med en FQDN-slutpunkt:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="FQDN-inställningar":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>Ändra gatewayens IP-adress eller FQDN

> [!NOTE]
> Det går inte att ändra en lokal nätverksgateway mellan FQDN-slutpunkten och IP-adressens slut punkt. Du måste ta bort alla anslutningar som är associerade med den här lokala Nätverksgatewayen, skapa en ny med den nya slut punkten (IP-adress eller fullständigt domän namn) och sedan återskapa anslutningarna.
>

Om den VPN-enhet som du vill ansluta till har ändrat dess offentliga IP-adress ändrar du den lokala Nätverksgatewayen med följande steg:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway väljer du **konfiguration**.
2. Ändra IP-adressen i rutan **IP-adress** .
3. Spara inställningarna genom att klicka på **Spara**.

Om den VPN-enhet som du vill ansluta till har ändrat FQDN (fullständigt kvalificerat domän namn) ändrar du den lokala Nätverksgatewayen med följande steg:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway väljer du **konfiguration**.
2. I rutan **FQDN** ändrar du domän namnet.
3. Spara inställningarna genom att klicka på **Spara**.

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Ändra IP-adressprefix

Så här lägger du till ytterligare adressprefix:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway väljer du **konfiguration**.
2. Lägg till IP-adressutrymmet i rutan *Lägg till ytterligare adress intervall* .
3. Spara inställningarna genom att välja **Spara** .

Så här tar du bort adressprefix:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway väljer du **konfiguration**.
2. Välj **"..."** på den rad som innehåller prefixet som du vill ta bort.
3. Välj **Ta bort**.
4. Spara inställningarna genom att välja **Spara** .

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>Ändra BGP-inställningar

Lägga till eller uppdatera BGP-inställningar:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway väljer du **konfiguration**.
2. Välj **Konfigurera BGP-inställningar** om du vill visa eller uppdatera BGP-konfigurationerna för den här lokala Nätverksgatewayen
3. Lägg till eller uppdatera det autonoma system numret eller BGP-peer-IP-adressen i motsvarande fält
4. Spara inställningarna genom att välja **Spara** .

Ta bort BGP-inställningar:

1. I avsnittet **Inställningar** på resursen lokal nätverksgateway väljer du **konfiguration**.
2. Avmarkera alternativet för att **Konfigurera BGP-inställningar** för att ta bort den befintliga BGP-ASN och BGP peer IP-adressen
3. Spara inställningarna genom att välja **Spara** .

## <a name="next-steps"></a>Nästa steg

Du kan verifiera din gateway-anslutning. Se [Verifiera en gateway-anslutning](vpn-gateway-verify-connection-resource-manager.md).
