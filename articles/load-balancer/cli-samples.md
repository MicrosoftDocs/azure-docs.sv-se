---
title: Azure CLI-exempel för Load Balancer
titleSuffix: Azure Load Balancer
description: Azure CLI-exempel
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: fcc2a579f2fe9048dd58cd8b52c8c704c894936b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87499355"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Azure CLI-exempel för Load Balancer

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

* [Belastningsutjämna trafik till virtuella datorer för hög tillgänglighet](./scripts/load-balancer-linux-cli-sample-nlb.md)

  Skapar flera virtuella datorer i en hög tillgänglig och belastningsutjämnad konfiguration.

* [Belastningsutjämna virtuella datorer mellan tillgänglighetszoner](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md)

  Skapar tre virtuella datorer i olika tillgänglighets zoner inom en region och Standard Load Balancer med en zon-redundant IP-adress för klient delen. Den här belastnings Utjämnings konfigurationen hjälper till att skydda dina appar och data från ett osannolikt problem eller förlust av ett helt data Center.

* [Belastningsutjämna virtuella datorer i en specifik tillgänglighetszon](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)

  Skapar tre virtuella datorer, en Standard Load Balancer med zonindelade-frontend-IP som hjälper till att justera data Sök vägar och resurser i en enskild zon för en viss region.

* [Belastningsutjämna flera webbplatser på virtuella datorer](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md)

  Skapar två virtuella datorer med flera IP-konfigurationer, anslutna till en Azures tillgänglighets uppsättning, tillgänglig via en Azure Load Balancer.
