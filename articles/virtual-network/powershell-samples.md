---
title: Azure PowerShell-exempel för virtuellt nätverk
description: Lär dig mer om Azure PowerShell exempel för att hantera virtuella nätverk, inklusive ett exempel på hur du skapar ett virtuellt nätverk för program på flera nivåer.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: b3107d521fb79e3ea6cfe190abd2ec3fb7859cb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87288237"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Azure PowerShell-exempel för virtuellt nätverk

Följande tabell innehåller länkar till Azure PowerShell-skript:

| Skript | Beskrivning |
|----|----|
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP, medan trafik till serverdelsundernätet är begränsad till SQL, port 1433. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverkstrafik till klientdelsundernätet är begränsad till HTTP och HTTPS. Utgående trafik till Internet från serverdelsundernätet är inte tillåten. |
|[Konfigurera IPv4 + IPv6-virtuellt nätverk med dubbla stackar med grundläggande Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Distribuerar ett virtuellt nätverk med dubbla stackar (IPv4 + IPv6) med två virtuella datorer och en Azure Basic-Load Balancer med IPv4-och IPv6-offentliga IP-adresser. |
|[Konfigurera IPv4 + IPv6-virtuellt nätverk med dubbla stackar med Standard Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Distribuerar ett virtuellt nätverk med dubbla stackar (IPv4 + IPv6) med två virtuella datorer och en Azure-Standard Load Balancer med offentliga IPv4-och IPv6-IP-adresser. |
