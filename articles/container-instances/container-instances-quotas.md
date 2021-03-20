---
title: Tjänst kvoter och region tillgänglighet
description: Kvoter, begränsningar och regional tillgänglighet för tjänsten Azure Container Instances.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: eaaa8e0b2d72aaea546a1bc351da40932c1deb14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87384844"
---
# <a name="quotas-and-limits-for-azure-container-instances"></a>Kvoter och begränsningar för Azure Container Instances

Alla Azure-tjänster har vissa standardgränser och kvoter för resurser och funktioner. I den här artikeln beskrivs standard kvoter och gränser för Azure Container Instances.

Tillgänglighet för beräknings-, minnes-och lagrings resurser för Azure Container Instances varierar beroende på region och operativ system. Mer information finns i [resurs tillgänglighet för Azure Container instances](container-instances-region-availability.md).

Använd API för [list användning](/rest/api/container-instances/location/listusage) för att granska den aktuella kvot användningen i en region för en prenumeration.

## <a name="service-quotas-and-limits"></a>Kvoter och begränsningar för tjänsten

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="next-steps"></a>Nästa steg

Vissa standardgränser och kvoter kan ökas. Om du vill begära en ökning av en eller flera resurser som kan ökas skickar du in en [Azure-supportbegäran][azure-support] (välj ”Kvot” för **Typ av problem**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
