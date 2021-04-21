---
title: Låsa avbildningar
description: Ange attribut för en containeravbildning eller lagringsplats så att den inte kan tas bort eller skrivas över i ett Azure-containerregister.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 340beb1bb6666ddf0de7de38adee6be71f5f52bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772351"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Låsa en containeravbildning i ett Azure-containerregister

I ett Azure-containerregister kan du låsa en avbildningsversion eller en lagringsplats så att den inte kan tas bort eller uppdateras. Om du vill låsa en avbildning eller en lagringsplats uppdaterar du dess attribut med hjälp av Azure CLI-kommandot [az acr repository update][az-acr-repository-update]. 

Den här artikeln kräver att du kör Azure CLI i Azure Cloud Shell lokalt (version 2.0.55 eller senare rekommenderas). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

> [!IMPORTANT]
> Den här artikeln gäller inte för låsning av ett helt register, till exempel med hjälp **av inställningar >** lås i Azure Portal eller kommandon i Azure `az lock` CLI. Att låsa en registerresurs hindrar dig inte från att skapa, uppdatera eller ta bort data i lagringsplatsen. Låsning av ett register påverkar endast hanteringsåtgärder som att lägga till eller ta bort replikeringar eller att ta bort själva registret. Mer information finns i [Låsa resurser för att förhindra oväntade ändringar.](../azure-resource-manager/management/lock-resources.md)

## <a name="scenarios"></a>Scenarier

Som standard är en taggad avbildning i Azure Container Registry *föränderlig,* så med lämpliga behörigheter kan du uppdatera och push-installera en avbildning upprepade gånger med samma tagg till ett register. Containeravbildningar kan också [tas bort](container-registry-delete.md) efter behov. Det här beteendet är användbart när du utvecklar avbildningar och behöver underhålla en storlek för registret.

När du distribuerar en containeravbildning till produktion kan du dock behöva en *oföränderlig containeravbildning.* En oföränderlig avbildning är en avbildning som du inte kan ta bort eller skriva över av misstag.

Se [Rekommendationer för taggning och versionshantering av containeravbildningar för](container-registry-image-tag-version.md) strategier för att tagga och versionsavbildningar i registret.

Använd kommandot [az acr repository update för][az-acr-repository-update] att ange lagringsplatsens attribut så att du kan:

* Låsa en avbildningsversion eller en hel lagringsplats

* Skydda en avbildningsversion eller lagringsplats från borttagning, men tillåt uppdateringar

* Förhindra läsåtgärder (pull-åtgärder) på en avbildningsversion eller en hel lagringsplats

Se följande avsnitt för exempel. 

## <a name="lock-an-image-or-repository"></a>Låsa en avbildning eller lagringsplats 

### <a name="show-the-current-repository-attributes"></a>Visa de aktuella lagringsplatsattributen
Om du vill se de aktuella attributen för en lagringsplats kör du [följande az acr repository show-kommando:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Visa de aktuella bildattributen
Om du vill se de aktuella attributen för en tagg kör du följande [az acr repository show-kommando:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Låsa en avbildning efter tagg

Om du vill *låsa avbildningen myrepo/myimage:tag* i *myregistry* kör du följande [az acr repository update-kommando:][az-acr-repository-update]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Låsa en avbildning efter manifest

Om du vill *låsa en myrepo/myimage-avbildning* som identifierats av manifestet digest (SHA-256-hash, som representeras `sha256:...` som ), kör du följande kommando. (Du hittar manifestet som är associerat med en eller flera avbildningstaggar genom att köra [kommandot az acr repository show-manifests.)][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Låsa en lagringsplats

Kör följande kommando *för att låsa lagringsplatsen myrepo/myimage* och alla avbildningar i den:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Skydda en avbildning eller lagringsplats från borttagning

### <a name="protect-an-image-from-deletion"></a>Skydda en bild från borttagning

Kör följande *kommando för att tillåta att avbildningen myrepo/myimage:tag* uppdateras men inte tas bort:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Skydda en lagringsplats från borttagning

Följande kommando anger *lagringsplatsen myrepo/myimage* så att den inte kan tas bort. Enskilda avbildningar kan fortfarande uppdateras eller tas bort.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Förhindra läsåtgärder på en avbildning eller lagringsplats

Om du vill förhindra läsåtgärder (pull)-åtgärder *på avbildningen myrepo/myimage:tag* kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Om du vill förhindra läsåtgärder på alla avbildningar *i lagringsplatsen myrepo/myimage* kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Låsa upp en avbildning eller lagringsplats

Om du vill återställa standardbeteendet för *avbildningen myrepo/myimage:tag* så att den kan tas bort och uppdateras kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Om du vill återställa standardbeteendet för *lagringsplatsen myrepo/myimage* och alla avbildningar så att de kan tas bort och uppdateras kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder [kommandot az acr repository update][az-acr-repository-update] för att förhindra borttagning eller uppdatering av avbildningsversioner på en lagringsplats. Ytterligare attribut finns i kommandoreferensen [az acr repository update.][az-acr-repository-update]

Om du vill se de attribut som angetts för en avbildningsversion eller lagringsplats använder du [kommandot az acr repository show.][az-acr-repository-show]

Mer information om borttagningsåtgärder finns i Ta [bort containeravbildningar i Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az_acr_repository_update
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md
