---
title: Lagring av container avbildning
description: Information om hur behållar avbildningar och andra artefakter lagras i Azure Container Registry, inklusive säkerhet, redundans och kapacitet.
ms.topic: article
ms.date: 03/03/2021
ms.custom: references_regions
ms.openlocfilehash: ec4328b44d5493b8d765fa30c548adc3d747d446
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183275"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Lagrings avbildnings lagring i Azure Container Registry

Alla fördelar med Azure Container Registry för [Basic, standard och Premium](container-registry-skus.md) från avancerade Azure Storage-funktioner, inklusive kryptering vid vila. I följande avsnitt beskrivs funktionerna och gränserna för avbildnings lagring i Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Kryptering vid vila

Alla behållar avbildningar och andra artefakter i registret är krypterade i vila. Azure krypterar automatiskt en avbildning innan den lagras och dekrypterar den direkt när du eller dina program och tjänster hämtar avbildningen. Du kan också använda ett extra krypterings lager med en [kundhanterad nyckel](container-registry-customer-managed-keys.md).

## <a name="regional-storage"></a>Regional lagring

Azure Container Registry lagrar data i den region där registret skapas, för att hjälpa kunderna att uppfylla kraven på data placering och efterlevnad.

För att skydda dig mot data Center avbrott erbjuder vissa regioner [zon redundans](zone-redundancy.md), där data replikeras över flera data Center i en viss region.

Kunder som vill ha sina data lagrade i flera regioner för bättre prestanda i olika geografiska områden eller som vill ha återhämtning i händelse av ett regionalt avbrott bör aktivera [geo-replikering](container-registry-geo-replication.md).

## <a name="geo-replication"></a>Geo-replikering

För scenarier som kräver hög tillgänglighets säkerhet kan du överväga att använda funktionen för [geo-replikering](container-registry-geo-replication.md) i Premium register. Geo-replikering hjälper till att förlora åtkomsten till registret i händelse av ett regionalt haveri. Geo-replikering ger även andra fördelar, t. ex. nätverks nära avbildnings lagring för snabbare push-meddelanden och hämtningar i distribuerade utvecklings-eller distributions scenarier.

## <a name="zone-redundancy"></a>Zonredundans

Om du vill skapa en elastisk och hög tillgänglighet för Azure Container Registry, aktiverar du [zon redundans](zone-redundancy.md) i Välj Azure-regioner. En funktion i Premium service-nivån, zon-redundans använder Azures [tillgänglighets zoner](../availability-zones/az-overview.md) för att replikera registret till minst tre separata zoner i varje aktive rad region. Kombinera geo-replikering och zon redundans för att förbättra både tillförlitlighet och prestanda i ett register. 

## <a name="scalable-storage"></a>Skalbar lagring

Med Azure Container Registry kan du skapa så många databaser, bilder, lager eller taggar som du behöver, upp till [registrets lagrings gräns](container-registry-skus.md#service-tier-features-and-limits). 

Ett stort antal databaser och taggar kan påverka prestanda för registret. Ta regelbundet bort oanvända databaser, taggar och avbildningar som en del av din rutin för register underhåll och om du vill kan du ange en [bevarande princip](container-registry-retention-policy.md) för otaggade manifest. Borttagna register resurser som lagrings platser, bilder och taggar *kan inte* återställas efter borttagning. Mer information om hur du tar bort register resurser finns [i ta bort behållar avbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Lagringskostnad

Fullständig information om priser finns i [Azure Container Registry prissättning][pricing].

## <a name="next-steps"></a>Nästa steg

Mer information om register för standard-, standard-och Premium-behållare finns i [Azure Container Registry tjänst nivåer](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
