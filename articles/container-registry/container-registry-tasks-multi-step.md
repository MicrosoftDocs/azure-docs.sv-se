---
title: Aktivitet med flera steg för att bygga, testa & korrigerings avbildning
description: Introduktion till aktiviteter med flera steg, en funktion i ACR-aktiviteter i Azure Container Registry som innehåller uppgiftsbaserade arbets flöden för att skapa, testa och korrigera behållar avbildningar i molnet.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "78399697"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Kör åtgärder för att skapa, testa och korrigera flera steg i ACR-aktiviteter

Med aktiviteter i flera steg utökas funktionerna för att bygga och push-funktionen i ACR-aktiviteter med multi-Steps, multi-container-baserade arbets flöden. Använd aktiviteter i flera steg för att bygga och skicka flera bilder i serie eller parallellt. Kör sedan dessa bilder som kommandon i en enda uppgifts körning. Varje steg definierar en behållar avbildnings version eller push-åtgärd och kan även definiera körningen av en behållare. Varje steg i en multi-Step-aktivitet använder en behållare som dess körnings miljö.

> [!IMPORTANT]
> Om du tidigare skapade uppgifter i förhandsversionen med kommandot `az acr build-task` behöver de uppgifterna skapas på nytt med hjälp av kommandot [az acr task][az-acr-task].

Du kan till exempel köra en uppgift med steg som automatiserar följande logik:

1. Bygg en webb program avbildning
1. Kör behållaren för webb program
1. Bygg en test avbildning av webb program
1. Kör test behållaren för webb program som utför tester mot den program behållare som körs
1. Om testerna passerar skapar du ett Helm-diagram Arkiv paket
1. Utföra en `helm upgrade` användning med det nya Helm-diagrammets Arkiv paket

Alla steg utförs i Azure, vilket avlastar arbetet till Azures beräknings resurser och frigör dig från infrastruktur hantering. Förutom ditt Azure Container Registry betalar du bara för de resurser du använder. Mer information om priser finns i avsnittet **behållar version** i [Azure Container Registry prissättning][pricing].


## <a name="common-task-scenarios"></a>Vanliga uppgifts scenarier

Flera steg-uppgifter möjliggör scenarier som följande logik:

* Bygg, tagga och pusha en eller flera behållar avbildningar i serie eller parallellt.
* Kör och hämta enhets test och kod täcknings resultat.
* Kör och avbilda funktionella tester. ACR-aktiviteter stöder körning av fler än en behållare som kör en serie begär Anden mellan dem.
* Utföra uppgiftsbaserade körningar, inklusive förskotts steg i en behållar avbildnings version.
* Distribuera en eller flera behållare med din favorit distributions motor till mål miljön.

## <a name="multi-step-task-definition"></a>Aktivitets definition för flera steg

En aktivitet med flera steg i ACR-aktiviteter definieras som en serie steg i en YAML-fil. Varje steg kan ange beroenden när ett eller flera föregående steg har slutförts. Följande steg typer är tillgängliga:

* [`build`](container-registry-tasks-reference-yaml.md#build): Bygg en eller flera behållar avbildningar med hjälp av välbekant `docker build` syntax, i serie eller parallellt.
* [`push`](container-registry-tasks-reference-yaml.md#push): Push-genererade avbildningar till ett behållar register. Privata register som Azure Container Registry stöds, som är den offentliga Docker-hubben.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Kör en behållare, så att den kan fungera som en funktion inom kontexten för aktiviteten som körs. Du kan skicka parametrar till behållarens `[ENTRYPOINT]` och ange egenskaper som t. ex. kuvert, från koppling och andra välkända `docker run` parametrar. `cmd`Steg typen aktiverar enhets-och funktions testning med körning av samtidiga behållare.

Följande kodfragment visar hur du kan kombinera dessa steg typer för uppgifter. Aktiviteter med flera steg kan vara så enkla som att skapa en enda avbildning från en Dockerfile och överföra till registret, med en YAML-fil som liknar:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Eller mer komplex, t. ex. denna fiktiva definition av flera steg, som innehåller steg för build-, test-, Helm-paket och Helm-distribution (behållar registret och konfigurationen av Helm-lagringsplatsen visas inte):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

Se [exempel på uppgifter](container-registry-tasks-samples.md) för yaml-filer och Dockerfiles för flera steg i flera olika scenarier.

## <a name="run-a-sample-task"></a>Köra en exempel aktivitet

Aktiviteter har stöd för både manuell körning, kallas "snabb körning" och automatisk körning vid git-incheckning eller bas avbildnings uppdatering.

Om du vill köra en uppgift definierar du först uppgiftens steg i en YAML-fil och kör sedan Azure CLI-kommandot [AZ ACR Run][az-acr-run].

Här är ett exempel på ett Azure CLI-kommando som kör en uppgift med en exempel aktivitet YAML-fil. Stegen bygger och skickar sedan en avbildning. Uppdatera `\<acrName\>` med namnet på ditt eget Azure Container Registry innan du kör kommandot.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

När du kör uppgiften ska utdata Visa förloppet för varje steg som definierats i YAML-filen. I följande utdata visas stegen som `acb_step_0` och `acb_step_1` .

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Mer information om automatiserade versioner av Git-incheckning eller bas avbildnings uppdatering finns i artikeln [Automatisera avbildnings versioner](container-registry-tutorial-build-task.md) och [bas avbildnings uppdatering bygger](container-registry-tutorial-base-image-update.md) på självstudier.

## <a name="next-steps"></a>Nästa steg

Du kan hitta aktiviteter med flera steg och exempel här:

* [Uppgifts referens](container-registry-tasks-reference-yaml.md) – uppgifts typer, egenskaper och användning.
* [Uppgifts exempel](container-registry-tasks-samples.md) – exempel- `task.yaml` och Docker-filer för flera scenarier, enkla att komplexa.
* [Cmd-lagrings platsen](https://github.com/AzureCR/cmd) – en samling behållare som kommandon för ACR-uppgifter.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task