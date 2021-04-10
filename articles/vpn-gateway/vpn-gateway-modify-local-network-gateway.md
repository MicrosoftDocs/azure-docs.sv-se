---
title: 'Ändra gatewayens IP-adress inställningar: PowerShell'
description: Den här artikeln vägleder dig genom att ändra IP-adressprefix för din lokala nätverksgateway med hjälp av PowerShell
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 07d9124105424d42970800b1c5bca956d512722d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388676"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Ändra inställningar för den lokala nätverksgatewayen med PowerShell

Ibland ändras inställningarna för din lokala nätverksgateway AddressPrefix eller GatewayIPAddress. Den här artikeln visar hur du ändrar inställningarna för din lokala nätverksgateway. Du kan också ändra dessa inställningar med en annan metod genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Ändra IP-adressprefix

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Ändra IP-adressen för gatewayen

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Nästa steg

Du kan verifiera din gateway-anslutning. Se [Verifiera en gateway-anslutning](vpn-gateway-verify-connection-resource-manager.md).