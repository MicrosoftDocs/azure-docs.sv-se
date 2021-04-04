---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4e958026b1167d65f47bddbe5e89ec4d6fed7ee3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217988"
---
Du kan kontrollera att anslutningen har utförts med hjälp av följande kommandot [az network vpn-connection show](/cli/azure/network/vpn-connection). I exemplet avser '-name' namnet på den anslutning du vill testa. När anslutningen håller på att upprättas visas status Ansluter. När anslutningen har upprättats ändras status till ”Ansluten”.

```azurecli-interactive
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
