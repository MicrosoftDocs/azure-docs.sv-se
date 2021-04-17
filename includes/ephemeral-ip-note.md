---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529577"
---
> [!NOTE]
> Azure tillhandahåller en ohemlig IP-adress för Azure Virtual Machines som inte har tilldelats en offentlig IP-adress eller finns i backend-poolen för en intern Basic-Azure Load Balancer. Den tillfälliga IP-mekanismen ger en utgående IP-adress som inte kan konfigureras. 
>
>Den tillfälliga IP-adressen inaktiveras när en offentlig IP-adress tilldelas till den virtuella datorn eller om den virtuella datorn placeras i serverpoolen för en Standard Load Balancer med eller utan regler för utgående trafik. Om [](../articles/virtual-network/nat-overview.md) en Azure Virtual Network NAT-gatewayresurs tilldelas till undernätet för den virtuella datorn inaktiveras den tillfälliga IP-adressen.
>
> Mer information om utgående anslutningar i Azure finns i [Använda källnätverksadressöversättning (SNAT) för utgående anslutningar.](../articles/load-balancer/load-balancer-outbound-connections.md)