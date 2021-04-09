---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97936772"
---


| Funktion |[Förbrukningsplan](../articles/azure-functions/consumption-plan.md)|[Premiumplan](../articles/azure-functions/functions-premium-plan.md)|[Dedikerad plan](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Inkommande IP-begränsningar och åtkomst till privata platser](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integrering av virtuella nätverk](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nej|✅Ja (regional)|✅Ja (regional och gateway)|✅Ja| ✅Ja|
|[Virtuella nätverks utlösare (icke-HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nej| ✅Ja |✅Ja|✅Ja|✅Ja|
|[Hybrid anslutningar](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (endast Windows)|❌Nej|✅Ja|✅Ja|✅Ja|✅Ja|
|[Utgående IP-begränsningar](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nej| ✅Ja|✅Ja|✅Ja|✅Ja|