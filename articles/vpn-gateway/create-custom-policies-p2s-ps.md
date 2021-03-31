---
title: 'Skapa och ange anpassade IPsec-principer för punkt-till-plats: PowerShell'
titleSuffix: Azure VPN Gateway
description: Den här artikeln hjälper dig att skapa och ange anpassade IPSec-principer för VPN Gateway P2S-konfigurationer
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/09/2020
ms.author: cherylmc
ms.openlocfilehash: a77a870304e20c179e22b4a4ffe404315894d8a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91743700"
---
# <a name="create-and-set-custom-ipsec-policies-for-point-to-site-preview"></a>Skapa och ange anpassade IPsec-principer för punkt-till-plats (för hands version)

Om din miljö kräver en anpassad IPsec-princip för kryptering kan du enkelt konfigurera ett princip objekt med nödvändiga inställningar. Den här artikeln hjälper dig att skapa ett anpassat princip objekt och sedan ange det med PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

### <a name="prerequisites"></a>Förutsättningar

Kontrol lera att din miljö uppfyller följande krav:

* Du har redan konfigurerat en fungerande punkt-till-plats-VPN. Om du inte gör det konfigurerar du en med hjälp av stegen **skapa en punkt-till-plats-VPN-**  artikel med antingen [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)eller [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="1-set-variables"></a><a name="signin"></a>1. Ange variabler

Deklarera de variabler som du vill använda. Använd följande exempel och ersätt värdena med dina egna när det behövs. Om du stänger PowerShell/Cloud Shell-sessionen när som helst under övningen ska du bara kopiera och klistra in värdena igen för att omdeklarera variablerna.

  ```azurepowershell-interactive
  $RG = "TestRG"
  $GWName = "VNet1GW"
  ```

## <a name="2-create-policy-object"></a><a name="create"></a>2. Skapa princip objekt

Skapa ett anpassat IPsec-princip objekt. Du kan justera värdena så att de uppfyller de villkor du behöver.

```azurepowershell-interactive
$vpnclientipsecpolicy = New-AzVpnClientIpsecPolicy -IpsecEncryption AES256 -IpsecIntegrity SHA256 -SALifeTime 86471 -SADataSize 429496 -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup2 -PfsGroup PFS2
```

## <a name="3-update-gateway-and-set-policy"></a><a name="update"></a>3. uppdatera gateway och ange princip

I det här steget uppdaterar du din befintliga P2S VPN-gateway och anger IPsec-principen.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway -VpnClientIpsecPolicy $vpnclientipsecpolicy
```

## <a name="next-steps"></a>Nästa steg

Mer information om P2S-konfigurationer finns i [om punkt-till-plats-VPN](point-to-site-about.md).
