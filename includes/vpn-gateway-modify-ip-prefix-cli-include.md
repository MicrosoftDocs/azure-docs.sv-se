---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: f222d4a7f4724506112a47eff61ccc48354dd622
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027078"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Ändra IP-adressprefix för nätverksgateway – ingen gatewayanslutning

Om du inte har någon gatewayanslutning och du vill lägga till eller ta bort IP-adressprefix använder du samma kommando som du använder för att skapa den lokala nätverksgatewayen, [az network local-gateway create](/cli/azure/network/local-gateway). Du kan också använda det här kommandot för att uppdatera gatewayens IP-adress för VPN-enheten. Använd det befintliga namnet på din lokala nätverksgateway när du ska skriva över befintliga inställningar. Om du inte gör det skapar du en ny lokal nätverksgateway i stället för att skriva över den som redan finns.

Varje gång du gör en ändring måste hela listan med prefix specificeras, inte bara de prefix du vill ändra. Ange endast de prefix du vill behålla. I det här fallet 10.0.0.0/24 och 20.0.0.0/24

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Ändra IP-adressprefix för nätverksgateway – existerande gatewayanslutning

Om du har en gatewayanslutning och vill lägga till eller ta bort IP-adressprefix kan du uppdatera prefixen med [az network local-gateway update](/cli/azure/network/local-gateway). Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressprefixen behöver du inte ta bort VPN-gatewayen.

Varje gång du gör en ändring måste hela listan med prefix specificeras, inte bara de prefix du vill ändra. I det här exemplet är 10.0.0.0/24 och 20.0.0.0/24 redan med. Vi lägger till prefixen 30.0.0.0/24 och 40.0.0.0/24 och anger alla 4 prefixen vid uppdateringen.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```