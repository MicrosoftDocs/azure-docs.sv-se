---
title: Montera gitRepo-volym till containergrupp
description: Lär dig hur du monterar en gitRepo-volym för att klona en Git-lagringsplats till dina containerinstanser
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 7c1249e3120dd680c52bf74fb045bedf5202b9f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763729"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montera en gitRepo-volym i Azure Container Instances

Lär dig hur du monterar en *gitRepo-volym* för att klona en Git-lagringsplats till dina containerinstanser.

> [!NOTE]
> Montering av *en gitRepo-volym* är för närvarande begränsad till Linux-containrar. Medan vi arbetar med att ta alla funktioner till Windows-containrar kan du hitta aktuella plattformsskillnader i [översikten.](container-instances-overview.md#linux-and-windows-containers)

## <a name="gitrepo-volume"></a>gitRepo-volym

*GitRepo-volymen* monterar en katalog och klonar den angivna Git-lagringsplatsen till den vid containerstart. Genom att använda *en gitRepo-volym* i dina containerinstanser kan du undvika att lägga till koden för att göra det i dina program.

När du monterar *en gitRepo-volym* kan du ange tre egenskaper för att konfigurera volymen:

| Egenskap | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| `repository` | Ja | Den fullständiga URL:en, `http://` inklusive eller , för `https://` Git-lagringsplatsen som ska klonas.|
| `directory` | No | Katalog som lagringsplatsen ska klonas till. Sökvägen får inte innehålla eller börja med " `..` ".  Om du anger `.` " " klonas lagringsplatsen till volymens katalog. Annars klonas Git-lagringsplatsen till en underkatalog med det angivna namnet i volymkatalogen. |
| `revision` | No | Commit-hashen för den revision som ska klonas. Om det inte anges `HEAD` klonas revisionen. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Montera gitRepo-volym: Azure CLI

Om du vill montera en gitRepo-volym när du distribuerar containerinstanser med [Azure CLI,](/cli/azure)tillhandahåller du parametrarna och `--gitrepo-url` till kommandot az container `--gitrepo-mount-path` [create.][az-container-create] Om du vill kan du ange katalogen i volymen som ska klonas till ( ) och genomförandehashvärdet för `--gitrepo-dir` den revision som ska klonas ( `--gitrepo-revision` ).

Det här exempelkommandot klonar Exempelprogrammet Microsoft [aci-helloworld][aci-helloworld] `/mnt/aci-helloworld` till i containerinstansen:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Kontrollera att gitRepo-volymen har monterats genom att starta ett gränssnitt i containern [med az container exec][az-container-exec] och visa katalogen:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Montera gitRepo-volym: Resource Manager

Montera en gitRepo-volym när du distribuerar containerinstanser med [en Azure Resource Manager mall](/azure/templates/microsoft.containerinstance/containergroups)genom att först fylla i matrisen i `volumes` containergruppavsnittet `properties` i mallen. För varje container i containergruppen där du vill montera *gitRepo-volymen* fyller du sedan i matrisen i avsnittet i `volumeMounts` `properties` containerdefinitionen.

Till exempel skapar följande Resource Manager en containergrupp som består av en enda container. Containern klonar två GitHub-lagringsplatsen som anges av *volymblocken gitRepo.* Den andra volymen innehåller ytterligare egenskaper som anger en katalog att klona till, och genomför hashen för en specifik revision att klona.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Den resulterande katalogstrukturen för de två klonade lagringsplatsen som definieras i föregående mall är:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Ett exempel på distribution av containerinstanser med en Azure Resource Manager mall finns i [Distribuera grupper med flera containrar i Azure Container Instances](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Autentisering av privat Git-lagringsplatsen

Om du vill montera en gitRepo-volym för en privat Git-lagringsplats anger du autentiseringsuppgifterna i lagringsplatsens URL. Vanligtvis är autentiseringsuppgifterna i form av ett användarnamn och en personlig åtkomsttoken (PAT) som ger begränsad åtkomst till lagringsplatsen.

Till exempel skulle Azure CLI-parametern för en privat GitHub-lagringsplats se ut ungefär så här `--gitrepo-url` (där "gituser" är GitHub-användarnamnet och "abcdef1234fdsa4321abcdef" är användarens personliga åtkomsttoken):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

För en Azure Repos Git-lagringsplats anger du ett användarnamn (du kan använda "azurereposuser" som i följande exempel) i kombination med en giltig PAT:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Mer information om personliga åtkomsttoken för GitHub och Azure Repos finns i följande:

GitHub: [Skapa en personlig åtkomsttoken för kommandoraden][pat-github]

Azure Repos: [Skapa personliga åtkomsttoken för att autentisera åtkomst][pat-repos]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper i Azure Container Instances:

* [Montera en Azure-filresurs i Azure Container Instances](container-instances-volume-azure-files.md)
* [Montera en emptyDir-volym i Azure Container Instances](container-instances-volume-emptydir.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: /azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
