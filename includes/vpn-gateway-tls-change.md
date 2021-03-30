---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2ed141847f923a847443d2293e850519357cdae2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "67187234"
---
Från och med 1 juli 2018 tas stödet för TLS 1.0 och 1.1 bort från Azure VPN Gateway. VPN Gateway kommer endast att stödja TLS 1.2. Endast punkt-till-plats-anslutningar påverkas; plats-till-plats-anslutningar kommer inte att påverkas. Om du använder TLS för punkt-till-plats-VPN på Windows 10-klienter behöver du inte vidta några åtgärder. Om du använder TLS för punkt-till-plats-anslutningar på Windows 7-och Windows 8-klienter kan du läsa mer i [VPN gateway vanliga frågor och svar](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) om uppdaterings instruktioner.