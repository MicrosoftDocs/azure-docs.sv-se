---
title: ACR-uppgifts exempel
description: Exempel på Azure Container Registry uppgifter (ACR-aktiviteter) för att skapa, köra och uppdatera behållar avbildningar
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74456095"
---
# <a name="acr-tasks-samples"></a>Exempel på ACR-uppgifter

Den här artikeln länkar till exempel `task.yaml` filer och associerade Dockerfiles för flera [Azure Container Registry uppgifter](container-registry-tasks-overview.md) (ACR tasks) scenarier. 

Fler exempel finns i Azure- [exemplen][task-examples] lagrings platsen.

## <a name="scenarios"></a>Scenarier

* **Bygg avbildning**  -  [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Kör behållare**  -  [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Bygga och push-avbildning**  -  [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Skapa och kör avbildning**  -  [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Bygga och skicka flera avbildningar**  -   [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Bygg och testa avbildningar parallellt**  -   [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Bygg och skicka avbildningar till flera register**  -  [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Nästa steg

Läs mer om ACR-uppgifter:

* [Aktiviteter med flera steg](container-registry-tasks-multi-step.md) – ACR uppgiftsbaserade arbets flöden för att skapa, testa och korrigera behållar avbildningar i molnet.
* [Uppgifts referens](container-registry-tasks-reference-yaml.md) – uppgifts typer, egenskaper och användning.
* [Cmd-lagrings platsen](https://github.com/AzureCR/cmd) – en samling behållare som kommandon för ACR-uppgifter.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
