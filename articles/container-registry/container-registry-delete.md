---
title: Ta bort avbildningsresurser
description: Information om hur du effektivt hanterar registerstorleken genom att ta bort containeravbildningsdata med hjälp av Azure CLI-kommandon.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: af277d0c02960c989b4e9119f2ecbfd8f6d7ce07
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783997"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Ta bort containeravbildningar Azure Container Registry med Hjälp av Azure CLI

För att behålla storleken på Ditt Azure-containerregister bör du regelbundet ta bort inaktuella avbildningsdata. Vissa containeravbildningar som distribueras i produktion kan kräva långsiktig lagring, men andra kan vanligtvis tas bort snabbare. I till exempel ett automatiserat bygg- och testscenario kan registret snabbt fyllas med avbildningar som kanske aldrig distribueras och kan tas bort strax efter att bygg- och testkörningen har slutförts.

Eftersom du kan ta bort avbildningsdata på flera olika sätt är det viktigt att förstå hur varje borttagningsåtgärd påverkar lagringsanvändningen. Den här artikeln beskriver flera metoder för att ta bort bilddata:

* Ta bort [en lagringsplats:](#delete-repository)Tar bort alla avbildningar och alla unika lager på lagringsplatsen.
* Ta bort [efter](#delete-by-tag)tagg: Tar bort en avbildning, taggen, alla unika lager som avbildningen refererar till och alla andra taggar som är associerade med avbildningen.
* Ta bort [med manifestet sammanfattning:](#delete-by-manifest-digest)Tar bort en avbildning, alla unika lager som avbildningen refererar till och alla taggar som är associerade med avbildningen.

Exempelskript tillhandahålls för att automatisera borttagningsåtgärder.

En introduktion till dessa begrepp finns [i Om register, databaser och avbildningar.](container-registry-concepts.md)

## <a name="delete-repository"></a>Ta bort lagringsplats

Om du tar bort en lagringsplats tas alla avbildningar i lagringsplatsen bort, inklusive alla taggar, unika lager och manifest. När du tar bort en lagringsplats återställer du det lagringsutrymme som används av avbildningarna som refererar till unika lager på lagringsplatsen.

Följande Azure CLI-kommando tar bort lagringsplatsen "acr-helloworld" och alla taggar och manifest i lagringsplatsen. Om lager som refereras av de borttagna manifesten inte refereras till av andra avbildningar i registret tas även deras lagerdata bort, vilket återställer lagringsutrymmet.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Ta bort efter tagg

Du kan ta bort enskilda avbildningar från en lagringsplats genom att ange lagringsplatsens namn och tagg i borttagningsåtgärden. När du tar bort efter tagg återställer du det lagringsutrymme som används av alla unika lager i avbildningen (skikt som inte delas av andra avbildningar i registret).

Om du vill ta bort efter tagg använder [du az acr repository delete][az-acr-repository-delete] och anger avbildningens namn i `--image` parametern . Alla lager som är unika för avbildningen och alla andra taggar som är associerade med avbildningen tas bort.

Ta till exempel bort avbildningen "acr-helloworld:latest" från registret "myregistry":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> Att *ta bort efter* tagg bör inte förväxlas med att ta bort en tagg (avtagga). Du kan ta bort en tagg med Azure CLI-kommandot [az acr repository untag][az-acr-repository-untag]. Inget utrymme frigörs när du avtaggar en avbildning eftersom [dess manifest-](container-registry-concepts.md#manifest) och lagerdata finns kvar i registret. Endast själva taggreferensen tas bort.

## <a name="delete-by-manifest-digest"></a>Ta bort efter manifest

Manifestet [kan associeras](container-registry-concepts.md#manifest-digest) med en, ingen eller flera taggar. När du tar bort efter sammanfattning tas alla taggar som manifestet refererar till bort, liksom lagerdata för alla lager som är unika för avbildningen. Delade lagerdata tas inte bort.

Om du vill ta bort efter sammanfattning listar du först manifestet med sammanfattningar för lagringsplatsen som innehåller de avbildningar som du vill ta bort. Exempel:

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

Ange sedan den sammanfattning som du vill ta bort i kommandot [az acr repository delete.][az-acr-repository-delete] Formatet för kommandot är:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Om du till exempel vill ta bort det sista manifestet som visas i föregående utdata (med taggen "v2"):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

`acr-helloworld:v2`Avbildningen tas bort från registret, liksom alla lagerdata som är unika för avbildningen. Om ett manifest är associerat med flera taggar tas även alla associerade taggar bort.

## <a name="delete-digests-by-timestamp"></a>Ta bort sammanfattningar efter tidsstämpel

Om du vill behålla storleken på en lagringsplats eller ett register kan du behöva ta bort manifestet som är äldre än ett visst datum med jämna mellanrum.

Följande Azure CLI-kommando visar alla manifest som sammanfattas i en lagringsplats som är äldre än en angiven tidsstämpel i stigande ordning. Ersätt `<acrName>` och med värden som är lämpliga för din `<repositoryName>` miljö. Tidsstämpeln kan vara ett fullständigt datum/tid-uttryck eller ett datum, som i det här exemplet.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

När du har identifierat inaktuella manifestet kan du köra följande Bash-skript för att ta bort manifestet som är äldre än en angiven tidsstämpel. Det kräver Azure CLI och **xargs**. Som standard utför skriptet ingen borttagning. Ändra värdet `ENABLE_DELETE` till för att aktivera borttagning av `true` bilder.

> [!WARNING]
> Använd följande exempelskript med försiktighet – borttagna bilddata kan inte återställas. Om du har system som hämtar avbildningar efter manifest (till skillnad från avbildningens namn) ska du inte köra dessa skript. Om du tar bort manifestet kan systemen inte hämta avbildningarna från registret. I stället för att hämta efter manifest bör du överväga *att använda ett unikt taggningsschema,* en [rekommenderad metod.](container-registry-image-tag-version.md) 

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

Som vi nämnde i **sammanfattningsavsnittet** för [manifestet](container-registry-concepts.md#manifest-digest) tar push-push av en ändrad avbildning med hjälp av en befintlig tagg bort taggen från den tidigare push-avbildningen, vilket resulterar i en frånkopplad (eller "dinglande") avbildning. Manifestet för den tidigare push-avbildningen – och dess lagerdata – finns kvar i registret. Tänk på följande händelsesekvens:

1. Push-avbildning *acr-helloworld* med **taggen latest**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Kontrollera manifest för lagringsplatsen *acr-helloworld:*

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

1. Ändra *dockerfile för acr-helloworld*
1. Push-avbildning *acr-helloworld* med **taggen latest**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Kontrollera manifest för lagringsplatsen *acr-helloworld:*

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

Som du ser i utdata från det sista steget i sekvensen finns det nu ett överblivet manifest `"tags"` vars egenskap är en tom lista. Det här manifestet finns fortfarande i registret, tillsammans med alla unika lagerdata som det refererar till. **Om du vill ta bort sådana överblivna avbildningar och deras lagerdata måste du ta bort med manifestet digest**.

## <a name="delete-all-untagged-images"></a>Ta bort alla otaggade bilder

Du kan lista alla otaggade avbildningar på lagringsplatsen med hjälp av följande Azure CLI-kommando. Ersätt `<acrName>` och med värden som är lämpliga för din `<repositoryName>` miljö.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Med det här kommandot i ett skript kan du ta bort alla otaggade avbildningar på en lagringsplats.

> [!WARNING]
> Använd följande exempelskript med försiktighet – borttagna bilddata kan inte återställas. Om du har system som hämtar avbildningar efter manifest (till skillnad från avbildningens namn) bör du inte köra dessa skript. Om du tar bort otaggade avbildningar förhindras dessa system från att hämta avbildningarna från registret. I stället för att hämta efter manifest bör du överväga *att införa ett unikt taggningsschema,* vilket är en [rekommenderad metod.](container-registry-image-tag-version.md)

**Azure CLI i Bash**

Följande Bash-skript tar bort alla otaggade avbildningar från en lagringsplats. Det kräver Azure CLI och **xargs**. Som standard utför skriptet ingen borttagning. Ändra värdet `ENABLE_DELETE` till för att aktivera borttagning av `true` avbildning.

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

Följande PowerShell-skript tar bort alla otaggade avbildningar från en lagringsplats. Det kräver PowerShell och Azure CLI. Som standard utför skriptet ingen borttagning. Ändra värdet `$enableDelete` till för att aktivera borttagning av `$TRUE` avbildning.

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

Som ett alternativ till att skripta Azure CLI-kommandon kör du en ACR-aktivitet på begäran eller en schemalagd ACR-aktivitet för att ta bort alla taggar som är äldre än en viss varaktighet eller matchar ett angivet namnfilter. Mer information finns i Rensa [avbildningar automatiskt från ett Azure-containerregister.](container-registry-auto-purge.md)

Du kan också ange [en bevarandeprincip](container-registry-retention-policy.md) för varje register för att hantera otaggade manifest. När du aktiverar en kvarhållningsprincip tas avbildningsmanifest i registret som inte har några associerade taggar och underliggande lagerdata bort automatiskt efter en a viss period.

## <a name="next-steps"></a>Nästa steg

Mer information om avbildningslagring i Azure Container Registry finns i [Containeravbildningslagring i Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az_acr_repository_untag
