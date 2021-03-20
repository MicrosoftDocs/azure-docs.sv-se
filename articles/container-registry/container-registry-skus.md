---
title: Register tjänst nivåer och funktioner
description: Lär dig mer om funktionerna och begränsningarna (kvoterna) i Azure Container Registry i tjänst nivåerna Basic, standard och Premium (SKU).
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: b55fc16dcd6dcb544ed4597ce4bdc6ba17b52646
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015676"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry tjänst nivåer

Azure Container Registry finns i flera tjänst nivåer (även kallade SKU: er). Dessa nivåer tillhandahåller förutsägbar prissättning och flera alternativ för att anpassa till kapacitets-och användnings mönstren för ditt privata Docker-register i Azure.

| Nivå | Beskrivning |
| --- | ----------- |
| **Basic** | En kostnadsoptimerad startpunkt för utvecklare som lär sig Azure Container Registry. Grundläggande register har samma programmerings funktioner som standard och Premium (till exempel Azure Active Directory [autentisering](container-registry-authentication.md#individual-login-with-azure-ad), [avbildnings borttagning][container-registry-delete]och [Webhooks][container-registry-webhook]). Den inkluderade lagrings-och avbildnings data flödet är dock lämpligast för lägre användnings scenarier. |
| **Standard** | Standard register ger samma funktioner som Basic, med ökat lagrings utrymme och avbildnings data flöde. Standard-register bör uppfylla behoven för de flesta produktionsscenarier. |
| **Premium** | Premium register ger högsta mängd av inkluderad lagring och samtidiga åtgärder, vilket möjliggör stora volymer. Förutom högre bild data flöde lägger Premium till funktioner som [geo-replikering][container-registry-geo-replication] för att hantera ett enda register över flera regioner, [innehålls förtroende](container-registry-content-trust.md) för signering av bildtagg, [privat länk med privata slut punkter](container-registry-private-link.md) för att begränsa åtkomsten till registret. |

Nivåerna Basic, standard och Premium har samma programmerings funktioner. De har också all nytta av [image Storage][container-registry-storage] som hanteras helt av Azure. Att välja en nivå på högre nivå ger bättre prestanda och skalning. Med flera tjänst nivåer kan du komma igång med Basic och sedan konvertera till standard och Premium allteftersom din användning av registret ökar.

## <a name="service-tier-features-and-limits"></a>Funktioner och begränsningar för service nivå

I följande tabell beskrivs funktionerna och register gränserna för tjänst nivåerna Basic, standard och Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Ändra nivåer

Du kan ändra ett registers tjänst nivå med Azure CLI eller i Azure Portal. Du kan flytta fritt mellan nivåerna så länge nivån som du växlar till har den maximala lagrings kapaciteten som krävs. 

Det finns inget avbrott i registret eller påverkar register åtgärder när du flyttar mellan tjänst nivåer.

### <a name="azure-cli"></a>Azure CLI

Om du vill flytta mellan tjänst nivåer i Azure CLI använder du kommandot [AZ ACR Update][az-acr-update] . Till exempel för att växla till Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

I behållarens register **Översikt** i Azure Portal väljer du **Uppdatera** och väljer sedan en ny **SKU** från List rutan SKU.

![Uppdatera container Registry-SKU: n i Azure Portal][update-registry-sku]

## <a name="pricing"></a>Priser

För pris information om var och en av Azure Container Registry tjänst nivåerna, se [container Registry prissättning][container-registry-pricing].

Mer information om priser för data överföringar finns i [pris information för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Nästa steg

**Azure Container Registrys översikt**

Besök [ACR-översikten][acr-roadmap] om GitHub för att hitta information om kommande funktioner i tjänsten.

**Azure Container Registry UserVoice**

Skicka in och rösta på nya funktions förslag i [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md