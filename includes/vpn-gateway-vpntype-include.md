---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010885"
---
* **Principbaserad:** Principbaserad-VPN kallades tidigare för statiska routnings-gatewayer i den klassiska distributions modellen. Principbaserad VPN krypterar och dirigerar paket via IPsec-tunnlar baserat på IPsec-principerna som kon figurer ATS med kombinationer av adressprefix mellan ditt lokala nätverk och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-enhetens konfiguration. Värdet för en principbaserad VPN-typ är *Principbaserad*. Tänk på följande begränsningar när du använder en principbaserad VPN:
  
  * Principbaserad-VPN kan **bara** användas på Basic Gateway-SKU: n. Den här VPN-typen är inte kompatibel med andra gateway-SKU: er.
  * Du kan bara ha 1 tunnel när du använder en principbaserad VPN.
  * Du kan bara använda principbaserad VPN för S2S-anslutningar och endast för vissa konfigurationer. De flesta VPN Gateway konfigurationer kräver en Routningsbaserad VPN.
* **Routningsbaserad**: Routningsbaserad-VPN kallades tidigare för dynamiska routnings-gatewayer i den klassiska distributions modellen. Routningsbaserad VPN använder "vägar" i IP-vidarebefordran eller routningstabellen för att dirigera paket till sina motsvarande tunnel gränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut från tunnlarna. Principen (eller trafik väljaren) för Routningsbaserad-VPN konfigureras som alla-till-alla (eller jokertecken). Värdet för en Routningsbaserad VPN-typ är *routningsbaserad*.
