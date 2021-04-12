---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560330"
---
Användardefinierade vägar med målet 0.0.0.0/0 och NSG: er på GatewaySubnet **stöds inte**. Gatewayer som har skapats med den här konfigurationen blockeras från att skapas. Gatewayer kräver åtkomst till hanterings styrenheterna för att fungera korrekt. [Instabilitetsdämpning av BGP-vägar](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) ska ställas in på "aktive rad" på GatewaySubnet för att säkerställa att gatewayen är tillgänglig. Om inställningen är inaktiverat fungerar inte gatewayen.