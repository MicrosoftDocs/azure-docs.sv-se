---
title: Låsa avbildningar
description: Ange attribut för en behållar avbildning eller lagrings plats så att den inte kan tas bort eller skrivas över i ett Azure Container Registry.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "77659704"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Låsa en behållar avbildning i ett Azure Container Registry

I ett Azure Container Registry kan du låsa en avbildnings version eller en lagrings plats så att den inte kan tas bort eller uppdateras. Om du vill låsa en avbildning eller en lagrings plats uppdaterar du dess attribut med Azure CLI-kommandot [AZ ACR lagrings plats uppdatering][az-acr-repository-update]. 

Den här artikeln kräver att du kör Azure CLI i Azure Cloud Shell eller lokalt (version 2.0.55 eller senare rekommenderas). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

> [!IMPORTANT]
> Den här artikeln gäller inte för att låsa ett helt register, till exempel med hjälp av **inställningar > lås** i Azure Portal eller `az lock` kommandon i Azure CLI. Att låsa en register resurs hindrar dig inte från att skapa, uppdatera eller ta bort data i databaserna. Att låsa ett register påverkar bara hanterings åtgärder som att lägga till eller ta bort replikeringar eller ta bort själva registret. Mer information i [Lås resurser för att förhindra oväntade ändringar](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Scenarier

Som standard är en taggad bild i Azure Container Registry *föränderligt*, så med lämpliga behörigheter kan du uppdatera och skicka en avbildning med samma tagg till ett register upprepade gånger. Behållar avbildningar kan också tas [bort](container-registry-delete.md) efter behov. Det här beteendet är användbart när du utvecklar avbildningar och behöver ha en storlek för registret.

Men när du distribuerar en *behållar* avbildning till produktion kan du behöva en behållar avbildning. En oföränderlig avbildning är en som du inte kan ta bort eller skriva över av misstag.

Se [rekommendationer för taggning och versions behållar avbildningar](container-registry-image-tag-version.md) för strategier till tagg-och versions avbildningar i registret.

Använd [uppdaterings kommandot AZ ACR-lagringsplats][az-acr-repository-update] för att ange attribut för lagring så att du kan:

* Låsa en avbildnings version eller en hel lagrings plats

* Skydda en avbildnings version eller lagrings plats från borttagning, men Tillåt uppdateringar

* Förhindra Läs åtgärder (pull) på en avbildnings version eller en hel lagrings plats

Se följande avsnitt för exempel. 

## <a name="lock-an-image-or-repository"></a>Låsa en bild eller lagrings plats 

### <a name="show-the-current-repository-attributes"></a>Visa aktuella attribut för databas
Om du vill se de aktuella attributen för en lagrings plats kör du följande kommando för [AZ ACR-databasen show][az-acr-repository-show] :

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Visa aktuella bildattribut
Om du vill se de aktuella attributen för en tagg kör du följande kommando för [AZ ACR-databasen show][az-acr-repository-show] :

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Lås en bild efter tagg

För att låsa *myrepo/min avbildning: tag* -bilden i *registret* kör du följande [AZ ACR-plats uppdaterings][az-acr-repository-update] kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Lås en avbildning av manifest Sammanfattning

Kör följande kommando för att låsa en avbildning av *myrepo/bild* som identifieras av manifest sammandrag (SHA-256-hash, representeras som `sha256:...` ). (Om du vill hitta manifest sammandrag som är associerad med en eller flera bildtaggar kör du kommandot [AZ ACR-lagringsplats show-Manifests][az-acr-repository-show-manifests] .)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Låsa en lagrings plats

Om du vill låsa lagrings platsen för *myrepo/min-avbildningen* och alla avbildningar i den kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Skydda en avbildning eller lagrings plats från borttagning

### <a name="protect-an-image-from-deletion"></a>Skydda en avbildning från att tas bort

Kör följande kommando för att tillåta *myrepo/image: tag-* avbildningen som ska uppdateras men inte tas bort:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Skydda en lagrings plats från borttagning

Följande kommando anger *myrepo/image-* lagringsplatsen så att den inte kan tas bort. Enskilda avbildningar kan fortfarande uppdateras eller tas bort.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Förhindra Läs åtgärder på en avbildning eller lagrings plats

För att förhindra Läs åtgärder (pull) på *myrepo/avbildningen: tag* -avbildning kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Kör följande kommando för att förhindra Läs åtgärder på alla avbildningar i *myrepo/image* -lagringsplatsen:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Låsa upp en avbildning eller lagrings plats

För att återställa standard beteendet för *myrepo/image: tag* -bilden så att den kan tas bort och uppdateras, kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Om du vill återställa standard beteendet för lagrings platsen *myrepo/min avbildning* och alla avbildningar så att de kan tas bort och uppdateras, kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder [uppdaterings kommandot AZ ACR-lagringsplats][az-acr-repository-update] för att förhindra borttagning eller uppdatering av avbildnings versioner i en lagrings plats. Om du vill ange ytterligare attribut, se referens för [AZ ACR-lagringsplatsen][az-acr-repository-update] .

Om du vill se de attribut som har angetts för en avbildnings version eller lagrings plats använder du kommandot [AZ ACR databas show][az-acr-repository-show] .

Mer information om borttagnings åtgärder finns i [ta bort behållar avbildningar i Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

