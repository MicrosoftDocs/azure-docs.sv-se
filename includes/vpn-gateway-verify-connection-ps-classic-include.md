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
ms.openlocfilehash: bd096e5a562bd9117ee895534c087d6fde48cb7e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92217979"
---
Du kan kontrol lera att anslutningen har slutförts med cmdleten "Get-AzureVNetConnection".

1. Använd följande cmdlet-exempel genom att konfigurera värdena för att matcha dina egna. Namnet på det virtuella nätverket måste vara inom citat tecken om det innehåller blank steg.

   ```azurepowershell
   Get-AzureVNetConnection "Group ClassicRG TestVNet1"
   ```
2. Visa värdena när cmdlet:en har slutförts. I exemplet nedan visas anslutnings statusen som ansluten och du kan se inkommande och utgående byte.

   ```output
   ConnectivityState         : Connected
   EgressBytesTransferred    : 181664
   IngressBytesTransferred   : 182080
   LastConnectionEstablished : 10/19/22020 12:40:54 AM
   LastEventID               : 24401
   LastEventMessage          : The connectivity state for the local network site 'F7F7BFC7_SiteVNet4' changed from Connecting to
                               Connected.
   LastEventTimeStamp        : 10/19/2020 12:40:54 AM
   LocalNetworkSiteName      : F7F7BFC7_SiteVNet4
   ```
