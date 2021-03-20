---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 49f48c2d0bf865cf8c8fde831e7a597f8701d213
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187171"
---
Du kan kontrol lera att anslutningen har lyckats med hjälp av cmdleten "Get-AzVirtualNetworkGatewayConnection", med eller utan "-debug". 

1. Använd följande cmdlet-exempel genom att konfigurera värdena för att matcha dina egna. Välj A om du uppmanas, för att köra Alla. I exemplet avser '-Name' namnet på den anslutning du vill testa.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Visa värdena när cmdlet:en har slutförts. I exemplet nedan visas anslutningsstatusen som Ansluten och du kan se ingående och utgående byte.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```