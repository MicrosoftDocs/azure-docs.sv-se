---
title: Azure CLI-exempel för virtuellt nätverk
description: Lär dig mer om olika exempel skript som du kan använda för att slutföra uppgifter i Azure CLI, inklusive hur du skapar ett virtuellt nätverk för program på flera nivåer.
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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 539d11205ffead52d7f40526f2c712e8cf8b5cdd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87501448"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Azure CLI-exempel för virtuellt nätverk

Följande tabell innehåller länkar till bash-skript med Azure CLI-kommandon:

| Skript | Beskrivning |
|----|----|
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP och SSH, medan trafik till serverdelsundernätet är begränsad till MySQL, port 3306. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverkstrafik till klientdelsundernätet är begränsad till HTTP, HTTPS och SSH. Utgående trafik till Internet från serverdelsundernätet är inte tillåten. |
|[Konfigurera IPv4 + IPv6-virtuellt nätverk med dubbla stackar med grundläggande Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Distribuerar ett virtuellt nätverk med dubbla stackar (IPv4 + IPv6) med två virtuella datorer och en Azure Basic-Load Balancer med IPv4-och IPv6-offentliga IP-adresser. |
|[Konfigurera IPv4 + IPv6-virtuellt nätverk med dubbla stackar med Standard Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Distribuerar ett virtuellt nätverk med dubbla stackar (IPv4 + IPv6) med två virtuella datorer och en Azure-Standard Load Balancer med offentliga IPv4-och IPv6-IP-adresser. |
|[Självstudie: skapa och testa en NAT-gateway – Azure CLI](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|Skapa och verifiera en NAT-gateway med en virtuell käll-och mål dator. |
