---
title: Azure CLI-exempel – nätverk
description: Lär dig mer om Azure CLI-exempel för nätverk, inklusive exempel för anslutning mellan Azure-resurser och exempel för belastnings utjämning och trafik riktning.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: c038f0d238646f43b93ba2a2c6a1120ab5feccee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87497298"
---
# <a name="azure-cli-samples-for-networking"></a>Azure CLI-exempel för nätverk

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| Skript | Beskrivning |
|-|-|
|**Anslutning mellan Azure-resurser**||
| [Skapa ett virtuellt nätverk för flernivåprogram](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP och SSH, medan trafik till serverdelsundernätet är begränsad till MySQL, port 3306. |
| [Peerkoppla två virtuella nätverk](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar och kopplar samman två virtuella nätverk i samma region. |
| [Dirigera trafik via en virtuell nätverksinstallation](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät och en virtuell dator som kan dirigera trafik mellan de två undernäten. |
| [Filtrera inkommande och utgående VM-nätverkstrafik](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar ett virtuellt nätverk med klient- och serverdelsundernät. Inkommande nätverks trafik till klient dels under nätet är begränsad till HTTP, HTTPS och SSH. Utgående trafik till Internet från Server dels under nätet är inte tillåten. |
|**Belastnings utjämning och trafik riktning**||
| [Belastningsutjämna flera webbplatser på virtuella datorer](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Skapar två virtuella datorer med flera IP-konfigurationer, anslutna till en Azures tillgänglighets uppsättning, tillgänglig via en Azure Load Balancer. |
| [Direkt trafik över flera regioner för hög program tillgänglighet](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Skapar två App Service-planer, två webbappar, en Traffic Manager-profil och två Traffic Manager-slutpunkter. |
| | |
