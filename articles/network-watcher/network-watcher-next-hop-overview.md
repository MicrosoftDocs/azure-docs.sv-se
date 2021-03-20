---
title: Introduktion till nästa hopp i Azure Network Watcher | Microsoft Docs
description: Den här artikeln innehåller en översikt över Network Watcher nästa hopp-funktion.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 33c22b5b68b6677e8cf271dc185007316ec44500
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76844066"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Använd nästa hopp för att diagnostisera problem med Routning av virtuell dator

Trafik från en virtuell dator (VM) skickas till ett mål baserat på de effektiva vägar som är associerade med ett nätverks gränssnitt (NIC). Nästa hopp hämtar nästa hopp typ och IP-adress för ett paket från en angiven virtuell dator och NIC. Att känna till nästa hopp hjälper dig att avgöra om trafiken dirigeras till det avsedda målet, eller om trafiken skickas i ett helt ställe. En felaktig konfigurering av vägar, där trafik dirigeras till en lokal plats, eller en virtuell installation, kan leda till anslutnings problem. Nästa hopp returnerar också den routningstabell som är associerad med nästa hopp. Om vägen definieras som en användardefinierad väg returneras den vägen. Annars returnerar nästa hopp **system väg**.

![Översikt över nästa hopp](./media/network-watcher-next-hop-overview/figure1.png)

Följande hopp som kan returneras av nästa hopp-funktion är följande:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Inget

Mer information om varje hopp-typ finns i [routning – översikt](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Information om hur du använder nästa hopp för att diagnosticera problem med Routning av virtuella dator nätverk finns i diagnostisera problem med att vidarebefordra VM-nätverk med hjälp av [Azure Portal](diagnose-vm-network-routing-problem.md), [POWERSHELL](diagnose-vm-network-routing-problem-powershell.md)eller [Azure CLI](diagnose-vm-network-routing-problem-cli.md).
