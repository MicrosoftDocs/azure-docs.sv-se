---
title: Ta bort avbildnings resurser
description: Information om hur du effektivt hanterar register storlek genom att ta bort behållar avbildnings data med hjälp av Azure CLI-kommandon.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "78403346"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Ta bort behållar avbildningar i Azure Container Registry med Azure CLI

För att upprätthålla storleken på ditt Azure Container Registry bör du regelbundet ta bort inaktuella avbildnings data. Vissa behållar avbildningar som distribueras till produktion kan kräva lagring på längre sikt, men andra kan vanligt vis tas bort snabbare. I ett automatiserat build-och test scenario kan du till exempel snabbt fylla i registret med avbildningar som aldrig har distribuerats, och de kan tas bort strax efter att du har slutfört build-och test-passet.

Eftersom du kan ta bort avbildnings data på flera olika sätt är det viktigt att förstå hur varje borttagnings åtgärd påverkar lagrings användningen. Den här artikeln beskriver flera metoder för att ta bort bilddata:

* Ta bort en [lagrings plats](#delete-repository): tar bort alla bilder och alla unika lager i lagrings platsen.
* Ta bort efter [tagg](#delete-by-tag): tar bort en bild, taggen, alla unika skikt som refereras av bilden och alla andra taggar som är associerade med bilden.
* Ta bort av [manifest sammandrag](#delete-by-manifest-digest): tar bort en bild, alla unika skikt som refereras av bilden och alla Taggar som är associerade med avbildningen.

Exempel skript tillhandahålls för att automatisera borttagnings åtgärder.

En introduktion till dessa begrepp finns i [om register, databaser och avbildningar](container-registry-concepts.md).

## <a name="delete-repository"></a>Ta bort lagrings plats

Om du tar bort en lagrings plats raderas alla avbildningar i lagrings platsen, inklusive alla Taggar, unika lager och manifest. När du tar bort en lagrings plats återställer du det lagrings utrymme som används av de avbildningar som refererar till unika lager i lagrings platsen.

Följande Azure CLI-kommando tar bort lagrings platsen "ACR-HelloWorld" och alla Taggar och manifest i lagrings platsen. Om lager som refereras till av de borttagna manifesten inte refereras till av andra bilder i registret, raderas även lager data, vilket återställer lagrings utrymmet.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Ta bort efter tagg

Du kan ta bort enskilda avbildningar från en lagrings plats genom att ange databasens namn och tagg i borttagnings åtgärden. När du tar bort med tagg återställer du det lagrings utrymme som används av alla unika lager i avbildningen (lager som inte delas av andra bilder i registret).

Om du vill ta bort by-taggen använder du [AZ ACR-lagringsplats ta bort][az-acr-repository-delete] och anger avbildnings namnet i `--image` parametern. Alla skikt som är unika för bilden och alla andra taggar som är associerade med bilden tas bort.

Du kan till exempel ta bort bilden "ACR-HelloWorld: den senaste" från registret "Registry":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> Borttagning *av tagg* bör inte förväxlas med borttagning av en tagg (avtaggning). Du kan ta bort en tagg med Azure CLI-kommandot [AZ ACR-lagringsplats Avtagga][az-acr-repository-untag]. Inget utrymme frigörs när du Avtagga en avbildning eftersom dess [manifest](container-registry-concepts.md#manifest) -och lager data finns kvar i registret. Endast taggens referens tas bort.

## <a name="delete-by-manifest-digest"></a>Ta bort av manifest sammandrag

En [manifest Sammanfattning](container-registry-concepts.md#manifest-digest) kan associeras med en, ingen eller flera taggar. När du tar bort en sammanfattning raderas alla Taggar som manifestet refererar till, som lager data för alla lager som är unika för bilden. Delade lager data tas inte bort.

Om du vill ta bort från sammandrag ska du först Visa manifest sammanfattningarna för den lagrings plats som innehåller de avbildningar som du vill ta bort. Exempel:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

Ange sedan den sammanfattning som du vill ta bort i kommandot [AZ ACR databas Delete][az-acr-repository-delete] . Formatet för kommandot är:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Om du till exempel vill ta bort det sista manifestet som anges i föregående utdata (med taggen "v2"):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

`acr-helloworld:v2`Avbildningen tas bort från registret, vilket är alla skikt data som är unika för avbildningen. Om ett manifest är associerat med flera taggar, raderas även alla tillhör ande taggar.

## <a name="delete-digests-by-timestamp"></a>Ta bort sammandrag efter tidsstämpel

Om du vill behålla storleken på en lagrings plats eller ett register kan du regelbundet behöva ta bort manifest sammandrag som är äldre än ett visst datum.

Följande Azure CLI-kommando visar en lista över alla manifest sammandrag i en lagrings plats som är äldre än en angiven tidsstämpel, i stigande ordning. Ersätt `<acrName>` och `<repositoryName>` med lämpliga värden för din miljö. Tidsstämpeln kan vara ett fullständigt datum/tid-uttryck eller ett datum, som i det här exemplet.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

När du har identifierat inaktuella manifest sammandrag kan du köra följande bash-skript för att ta bort manifest sammandrag som är äldre än en angiven tidsstämpel. Det kräver Azure CLI och **xargs**. Som standard utför skriptet ingen borttagning. Ändra `ENABLE_DELETE` värdet till `true` för att aktivera borttagning av bilder.

> [!WARNING]
> Använd följande exempel skript med varning-borttagna bilddata kan inte ÅTERSTÄLLAs. Om du har system som hämtar bilder av manifest sammandrag (i stället för avbildnings namn) bör du inte köra dessa skript. Om du tar bort manifest sammanfattningarna hindras dessa system från att hämta avbildningarna från registret. Överväg att använda ett unikt taggnings schema i stället för att hämta ett *unikt taggnings* schema, en [rekommenderad metod](container-registry-image-tag-version.md). 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Ta bort otaggade bilder

Som vi nämnt i avsnittet [manifest Sammanfattning](container-registry-concepts.md#manifest-digest) skickar du en ändrad avbildning med hjälp av en befintlig tagg som **avtaggar** den tidigare publicerade avbildningen, vilket resulterar i en överbliven (eller "Dangling") bild. Den tidigare publicerade avbildningens manifest – och dess lager data – finns kvar i registret. Tänk på följande händelser:

1. Push *-avbildning ACR-HelloWorld* med tagga **senaste**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Kontrol lera manifest för databasen *ACR-HelloWorld*:

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Ändra *ACR-HelloWorld* Dockerfile
1. Push *-avbildning ACR-HelloWorld* med tagga **senaste**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Kontrol lera manifest för databasen *ACR-HelloWorld*:

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Som du kan se i resultatet av det sista steget i sekvensen finns det nu ett överblivna manifest vars `"tags"` egenskap är en tom lista. Detta manifest finns fortfarande i registret, tillsammans med alla unika skikt data som det refererar till. **Om du vill ta bort sådana överblivna avbildningar och lager data, måste du ta bort manifest sammandrag**.

## <a name="delete-all-untagged-images"></a>Ta bort alla otaggade bilder

Du kan visa alla otaggade avbildningar i lagrings platsen med hjälp av följande Azure CLI-kommando. Ersätt `<acrName>` och `<repositoryName>` med lämpliga värden för din miljö.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Med det här kommandot i ett skript kan du ta bort alla otaggade bilder i en lagrings plats.

> [!WARNING]
> Använd följande exempel skript med varning – borttagna bilddata kan inte ÅTERSTÄLLAs. Om du har system som hämtar bilder av manifest sammandrag (i stället för avbildnings namn) bör du inte köra dessa skript. Om du tar bort otaggade bilder så förhindras dessa system från att hämta avbildningarna från registret. Överväg att använda ett unikt taggnings schema i stället för att hämta ett *unikt taggnings* schema, en [rekommenderad metod](container-registry-image-tag-version.md).

**Azure CLI i bash**

Följande bash-skript tar bort alla otaggade bilder från en lagrings plats. Det kräver Azure CLI och **xargs**. Som standard utför skriptet ingen borttagning. Ändra `ENABLE_DELETE` värdet till `true` för att aktivera borttagning av bilder.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Azure CLI i PowerShell**

Följande PowerShell-skript tar bort alla otaggade bilder från en lagrings plats. Det kräver PowerShell och Azure CLI. Som standard utför skriptet ingen borttagning. Ändra `$enableDelete` värdet till `$TRUE` för att aktivera borttagning av bilder.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Rensa automatiskt taggar och manifest (förhandsversion)

Som ett alternativ till skript för Azure CLI-kommandon kör du en aktivitet på begäran eller en schemalagd ACR för att ta bort alla Taggar som är äldre än en viss varaktighet eller matchar ett angivet namn filter. Mer information finns i [Rensa avbildningar automatiskt från ett Azure Container Registry](container-registry-auto-purge.md).

Du kan också ange en [bevarande princip](container-registry-retention-policy.md) för varje register för att hantera otaggade manifest. När du aktiverar en bevarande princip, kommer avbildnings manifest i registret som inte har några associerade taggar, och underliggande skikt data, tas bort automatiskt efter en angiven period.

## <a name="next-steps"></a>Nästa steg

Mer information om avbildnings lagring i Azure Container Registry se [lagring för behållar avbildningar i Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
