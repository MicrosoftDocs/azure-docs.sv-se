---
title: Uppgift i flera steg för att skapa, & korrigeringsavbildning
description: Introduktion till uppgifter i flera steg, en funktion i ACR-uppgifter i Azure Container Registry som tillhandahåller uppgiftsbaserade arbetsflöden för att skapa, testa och korrigera containeravbildningar i molnet.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: d57044fa8a0db7d661eb50284b34a6bbec9a1879
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781027"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Kör uppgifter för att skapa, testa och korrigera flera steg i ACR-uppgifter

Uppgifter i flera steg utökar bygg- och push-funktionen för enskilda avbildningar i ACR-uppgifter med arbetsflöden med flera steg och flera containrar. Använd uppgifter i flera steg för att skapa och push-skicka flera avbildningar i serier eller parallellt. Kör sedan avbildningarna som kommandon i en enda uppgiftskörning. Varje steg definierar en containeravbildningsbygg- eller push-åtgärd och kan även definiera körningen av en container. Varje steg i en uppgift med flera steg använder en container som körningsmiljö.

> [!IMPORTANT]
> Om du tidigare skapade uppgifter i förhandsversionen med kommandot `az acr build-task` behöver de uppgifterna skapas på nytt med hjälp av kommandot [az acr task][az-acr-task].

Du kan till exempel köra en uppgift med steg som automatiserar följande logik:

1. Skapa en avbildning av ett webbprogram
1. Köra webbprogramcontainern
1. Skapa en testavbildning för en webbapp
1. Kör testcontainern för webbprogram som utför tester mot den programcontainer som körs
1. Om testerna är klara skapar du ett arkivpaket för Helm-diagram
1. Utföra en med `helm upgrade` hjälp av det nya arkivpaketet för Helm-diagram

Alla steg utförs i Azure, vilket avlastar arbetet till Azures beräkningsresurser och frigör dig från infrastrukturhantering. Förutom ditt Azure-containerregister betalar du bara för de resurser du använder. Information om priser finns i avsnittet **Container Build (Containerbygge)** [i Azure Container Registry priser.][pricing]


## <a name="common-task-scenarios"></a>Vanliga uppgiftsscenarier

Uppgifter i flera steg möjliggör scenarier som följande logik:

* Skapa, tagga och push-skicka en eller flera containeravbildningar i serier eller parallellt.
* Resultat av körnings- och insamlingsenhetstest och kodtäckning.
* Kör och avbilda funktionstester. ACR-uppgifter har stöd för att köra fler än en container och köra en serie begäranden mellan dem.
* Utför uppgiftsbaserad körning, inklusive för-/eftersteg för en containeravbildningsbygge.
* Distribuera en eller flera containrar med din favoritdistributionsmotor till målmiljön.

## <a name="multi-step-task-definition"></a>Uppgiftsdefinition i flera steg

En uppgift i flera steg i ACR-uppgifter definieras som en serie steg i en YAML-fil. Varje steg kan ange beroenden när ett eller flera föregående steg har slutförts. Följande typer av uppgiftssteg är tillgängliga:

* [`build`](container-registry-tasks-reference-yaml.md#build): Skapa en eller flera containeravbildningar med välbekant `docker build` syntax, i serie eller parallellt.
* [`push`](container-registry-tasks-reference-yaml.md#push): Skicka skapade avbildningar till ett containerregister. Privata register som Azure Container Registry stöds, liksom den offentliga Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Kör en container så att den kan fungera som en funktion i kontexten för den uppgift som körs. Du kan skicka parametrar till containerns och ange egenskaper som `[ENTRYPOINT]` env, detach och andra bekanta `docker run` parametrar. Stegtypen `cmd` möjliggör enhets- och funktionstestning med samtidig containerkörning.

Följande kodfragment visar hur du kombinerar de här aktivitetsstegstyperna. Uppgifter i flera steg kan vara så enkla som att skapa en enda avbildning från en Dockerfile och push-pusha till registret, med en YAML-fil som liknar:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Eller mer komplext, till exempel den här fiktiva definitionen i flera steg som innehåller steg för att skapa, testa, helm-paketet och helm-distributionen (konfiguration av containerregistret och Helm-lagringsplatsen visas inte):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build: -t $Registry/hello-world-tests ./funcTests
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

Se [uppgiftsexempel](container-registry-tasks-samples.md) för YAML-filer och Dockerfiles i flera steg för flera scenarier.

## <a name="run-a-sample-task"></a>Köra en exempelaktivitet

Uppgifter stöder både manuell körning, som kallas "snabbkörning" och automatisk körning vid Git-genomförande eller basavbildningsuppdatering.

För att köra en uppgift definierar du först uppgiftens steg i en YAML-fil och kör sedan Azure CLI-kommandot [az acr run][az-acr-run].

Här är ett exempel på ett Azure CLI-kommando som kör en uppgift med hjälp av en YAML-exempelfil. Dess steg skapar och push-pushar sedan en avbildning. Uppdatera `\<acrName\>` med namnet på ditt eget Azure-containerregister innan du kör kommandot .

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

När du kör uppgiften bör utdata visa förloppet för varje steg som definierats i YAML-filen. I följande utdata visas stegen som `acb_step_0` och `acb_step_1` .

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

Mer information om automatiserade byggen vid Git-genomförande eller uppdatering av basavbildningar finns i självstudieartiklarna Automatisera [avbildningsbyggen](container-registry-tutorial-build-task.md) och Basavbildningsuppdateringsbyggen. [](container-registry-tutorial-base-image-update.md)

## <a name="next-steps"></a>Nästa steg

Du hittar referens och exempel för aktiviteter i flera steg här:

* [Uppgiftsreferens](container-registry-tasks-reference-yaml.md) – Uppgiftsstegtyper, deras egenskaper och användning.
* [Uppgiftsexempel](container-registry-tasks-samples.md) – `task.yaml` Exempel- och Docker-filer för flera scenarier, enkla till komplexa.
* [Cmd-lagringsplatsen](https://github.com/AzureCR/cmd) – En samling containrar som kommandon för ACR-uppgifter.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task]: /cli/azure/acr/task
