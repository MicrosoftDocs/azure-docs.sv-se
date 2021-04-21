---
title: Registertjänstnivåer och -funktioner
description: Lär dig mer om funktioner och begränsningar (kvoter) på tjänstnivåerna Basic, Standard och Premium (SKU:er) för Azure Container Registry.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 323d36fe022d8b8e9618b8beb1facae93d22df4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781261"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry tjänstnivåer

Azure Container Registry finns i flera tjänstnivåer (kallas även SKU:er). Dessa nivåer ger förutsägbar prissättning och flera alternativ för att anpassa sig till kapacitets- och användningsmönstren för ditt privata Docker-register i Azure.

| Nivå | Description |
| --- | ----------- |
| **Basic** | En kostnadsoptimerad startpunkt för utvecklare som lär sig Azure Container Registry. Grundläggande register har samma programmässiga funktioner som Standard och Premium (till exempel [][container-registry-delete]Azure Active Directory för [autentisering,](container-registry-authentication.md#individual-login-with-azure-ad)avbildningsborttagning och [webhooks][container-registry-webhook]). Det inkluderade lagrings- och avbildningsdataflödet är dock lämpligast för scenarier med lägre användning. |
| **Standard** | Standardregister har samma funktioner som Basic, med ökat lagringsutrymme och dataflöde för avbildningar. Standard-register bör uppfylla behoven för de flesta produktionsscenarier. |
| **Premium** | Premium-register ger den högsta mängden lagringsutrymme och samtidiga åtgärder som möjliggör scenarier med stora volymer. Förutom högre genomflöde för avbildningar lägger Premium till funktioner som [geo-replikering][container-registry-geo-replication] för att hantera ett enskilt register över flera [regioner,](container-registry-content-trust.md) innehållsförtroende för signering av bildtaggar, privat länk med privata slutpunkter för att begränsa åtkomsten till registret. [](container-registry-private-link.md) |

Nivåerna Basic, Standard och Premium har alla samma programmässiga funktioner. De kan också dra nytta av [avbildningslagring som][container-registry-storage] hanteras helt av Azure. Om du väljer en nivå på högre nivå får du bättre prestanda och skalning. Med flera tjänstnivåer kan du komma igång med Basic och sedan konvertera till Standard och Premium när registeranvändningen ökar.

## <a name="service-tier-features-and-limits"></a>Funktioner och begränsningar för tjänstnivå

Följande tabell innehåller information om funktionerna och registergränserna för tjänstnivån Basic, Standard och Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Ändra nivåer

Du kan ändra tjänstnivån för ett register med Azure CLI eller i Azure Portal. Du kan flytta fritt mellan nivåer så länge den nivå som du byter till har den maximala lagringskapacitet som krävs. 

Det finns ingen stilleståndstid i registret eller påverkan på registeråtgärder när du flyttar mellan tjänstnivåer.

### <a name="azure-cli"></a>Azure CLI

Om du vill flytta mellan tjänstnivåer i Azure CLI använder du [kommandot az acr update.][az-acr-update] Om du till exempel vill växla till Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

I översikten **för** containerregistret i Azure Portal väljer **du Uppdatera** och sedan en ny **SKU** i listrutan SKU.

![Uppdatera containerregistrets SKU i Azure Portal][update-registry-sku]

## <a name="pricing"></a>Priser

Prisinformation om var och en Azure Container Registry tjänstnivåer finns i [Container Registry priser.][container-registry-pricing]

Mer information om priser för dataöverföringar finns i [Prisinformation för bandbredd.](https://azure.microsoft.com/pricing/details/bandwidth/) 

## <a name="next-steps"></a>Nästa steg

**Azure Container Registry översikt**

Besök [ACR-översikten][acr-roadmap] på GitHub för att hitta information om kommande funktioner i tjänsten.

**Azure Container Registry UserVoice**

Skicka in och rösta på nya funktionsförslag i [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md