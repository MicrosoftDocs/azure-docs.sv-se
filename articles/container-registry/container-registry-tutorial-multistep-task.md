---
title: Självstudie – ACR-uppgift i flera steg
description: I den här självstudien får du lära dig hur du konfigurerar en Azure Container Registry-uppgift för att automatiskt utlösa ett arbetsflöde i flera steg för att skapa, köra och skicka containeravbildningar i molnet när du genomför källkoden till en Git-lagringsplats.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 6d6ca8c41ce808df4b32bc10a7b537ffae809b3a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780685"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Självstudie: Köra ett containerarbetsflöde i flera steg i molnet när du genomför källkoden

Förutom en snabbuppgift stöder [ACR-uppgifter](container-registry-tutorial-quick-task.md)arbetsflöden med flera steg och flera containrar som automatiskt kan utlösas när du genomför källkoden till en Git-lagringsplats. 

I den här självstudien får du lära dig hur du använder YAML-exempelfiler för att definiera uppgifter i flera steg som skapar, kör och push-överförar en eller flera containeravbildningar till ett register när du genomför källkoden. Om du vill skapa en uppgift som endast automatiserar en enskild avbildningsbygge vid kodin commit (Självstudie: Automatisera [containeravbildningsbyggen i](container-registry-tutorial-build-task.md)molnet när du genomför källkoden). En översikt över ACR-uppgifter finns i [Automatisera korrigering av operativsystem och ramverk med ACR-uppgifter](container-registry-tasks-overview.md),

I de här självstudierna har du

> [!div class="checklist"]
> * Definiera en uppgift i flera steg med hjälp av en YAML-fil
> * Skapa en uppgift
> * Du kan också lägga till autentiseringsuppgifter för uppgiften för att ge åtkomst till ett annat register
> * Testa uppgiften
> * Visa status för aktivitet
> * Utlösa uppgiften med en kodincheckning

Självstudien förutsätter att du redan har slutfört stegen i den [föregående självstudien](container-registry-tutorial-quick-task.md). Om du inte har gjort det, slutför du stegen i avsnittet [Krav](container-registry-tutorial-quick-task.md#prerequisites) i föregående självstudie innan du fortsätter.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-a-multi-step-task"></a>Skapa en uppgift i flera steg

Nu när du har slutfört de steg som krävs för att ACR-uppgifter ska kunna läsa inläsningsstatus och skapa webhooks på en lagringsplats, skapar du en uppgift i flera steg som utlöser att skapa, köra och push-pusha en containeravbildning.

### <a name="yaml-file"></a>YAML-fil

Du definierar stegen för en uppgift i flera steg i en [YAML-fil](container-registry-tasks-reference-yaml.md). Det första exemplet på en uppgift i flera steg för den här självstudien definieras i filen , som finns i roten på GitHub-lagringsplatsen `taskmulti.yaml` som du klonade:

```yml
version: v1.1.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Den här uppgiften i flera steg gör följande:

1. Kör ett `build` steg för att skapa en avbildning från Dockerfile i arbetskatalogen. Avbildningen riktar `Run.Registry` sig mot , registret där aktiviteten körs och taggas med ett unikt ACR-uppgifter körnings-ID. 
1. Kör ett `cmd` steg för att köra avbildningen i en tillfällig container. Det här exemplet startar en långvarig container i bakgrunden och returnerar container-ID:t och stoppar sedan containern. I ett verkligt scenario kan du inkludera steg för att testa containern som körs för att säkerställa att den körs korrekt.
1. I ett `push` steg push-pushar den avbildning som har skapats till körningsregistret.

### <a name="task-command"></a>Aktivitetskommando

Fyll först i de här gränssnittsmiljövariablerna med värden som är lämpliga för din miljö. Det här steget är inte obligatoriskt, men det gör det lite enklare att köra de flerradiga Azure CLI-kommandona i den här självstudien. Om du inte fyller i dessa miljövariabler måste du manuellt ersätta varje värde oavsett var det visas i exempelkommandona.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Skapa nu uppgiften genom att köra följande [az acr task create-kommando:][az-acr-task-create]

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Den här uppgiften anger att varje  gång kod har utförts till huvudgrenen i lagringsplatsen som anges av , kör ACR-uppgifter uppgiften i flera steg från koden i `--context` den grenen. YAML-filen som anges av `--file` från lagringsplatsens rot definierar stegen. 

Utdata från kommandot [az acr task create][az-acr-task-create] liknar följande:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2020-11-20T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git#main",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "main",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git#main",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>Testa arbetsflödet i flera steg

Testa uppgiften i flera steg genom att utlösa den manuellt genom att köra [kommandot az acr task run:][az-acr-task-run]

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Som standard strömmar kommandot `az acr task run` loggens utdata till konsolen när du kör kommandot. Utdata visar förloppet för att köra var och en av uppgiftsstegen. Utdata nedan komprimeras för att visa viktiga steg.

```output
Queued a run with ID: cab
Waiting for an agent...
2020/11/20 00:03:31 Downloading source code...
2020/11/20 00:03:33 Finished downloading source code
2020/11/20 00:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2020/11/20 00:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 00:03:34 Successfully set up Docker network: acb_default_network
2020/11/20 00:03:34 Setting up Docker configuration...
2020/11/20 00:03:34 Successfully set up Docker configuration
2020/11/20 00:03:34 Logging in to registry: myregistry.azurecr.io
2020/11/20 00:03:35 Successfully logged into myregistry.azurecr.io
2020/11/20 00:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:35 Scanning for dependencies...
2020/11/20 00:03:36 Successfully scanned dependencies
2020/11/20 00:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB
[...]
Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:43 Successfully executed container: acb_step_0
2020/11/20 00:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2020/11/20 00:03:44 Successfully executed container: acb_step_1
2020/11/20 00:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1
[...]
2020/11/20 00:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2020/11/20 00:03:46 Populating digests for step ID: acb_step_0...
2020/11/20 00:03:47 Successfully populated digests for step ID: acb_step_0
2020/11/20 00:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2020/11/20 00:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2020/11/20 00:03:47 The following dependencies were found:
2020/11/20 00:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cab was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Utlösa en version med en incheckning

Nu när du har testat denna uppgift genom att köra den manuellt utlöser du den automatiskt med en ändring i källkoden.

Kontrollera först att du är i den katalog som innehåller din lokala klon av [lagringsplatsen][sample-repo]:

```console
cd acr-build-helloworld-node
```

Sedan kör du följande kommandon för att skapa, checka in och skicka en ny fil till din förgrening av lagringsplatsen på GitHub:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin main
```

Du kan bli ombedd att ange dina GitHub-autentiseringsuppgifter när du kör kommandot `git push`. Ange ditt GitHub-användarnamn och din personliga åtkomsttoken som du skapade tidigare för lösenordet.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

När du har push-pushat en commit till lagringsplatsen startar webhooken som skapades av ACR-uppgifter och startar uppgiften i Azure Container Registry. Visa loggarna för den uppgift som körs för att kontrollera och övervaka versionsförloppet:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Utdata liknar följande och visar den uppgift som körs för närvarande (eller som kördes senast):

```output
Showing logs of the last created run.
Run ID: cad

[...]

Run ID: cad was successful after 37s
```

## <a name="list-builds"></a>Versionslista

Om du vill se en lista över de uppgiftskörningar som ACR Tasks har slutfört för ditt register kör du kommandot [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Utdatan från kommandot ska se ut ungefär som följande. De körningar som ACR Tasks har kört visas och ”Git Commit” (Git-incheckning) visas i kolumnen TRIGGER (Utlösare) för den senaste uppgiften:

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cad       example1        linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:35
cac       taskhelloworld  linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:22
cab       example1        linux       Succeeded  Manual     2020-11-20T00:18:36Z  00:00:47
```

## <a name="create-a-multi-registry-multi-step-task"></a>Skapa en uppgift med flera register i flera steg

ACR-uppgifter har som standard behörighet att skicka eller hämta avbildningar från registret där aktiviteten körs. Du kanske vill köra en uppgift i flera steg som är inriktad på ett eller flera register utöver körningsregistret. Du kan till exempel behöva skapa avbildningar i ett register och lagra avbildningar med olika taggar i ett andra register som nås av ett produktionssystem. Det här exemplet visar hur du skapar en sådan uppgift och anger autentiseringsuppgifter för ett annat register.

Om du inte redan har ett andra register skapar du ett för det här exemplet. Om du behöver ett register kan du gå till [föregående självstudie](container-registry-tutorial-quick-task.md) eller [Snabbstart: Skapa ett containerregister med hjälp av Azure CLI](container-registry-get-started-azure-cli.md).

För att skapa uppgiften behöver du namnet på registrets inloggningsserver, som är i *formuläret mycontainerregistrydate.azurecr.io* (endast gemener). I det här exemplet använder du det andra registret för att lagra avbildningar som taggats efter byggdatum.

### <a name="yaml-file"></a>YAML-fil

Det andra exemplet på en uppgift i flera steg för den här självstudien definieras i filen , som finns i roten på GitHub-lagringsplatsen `taskmulti-multiregistry.yaml` som du klonade:

```yml
version: v1.1.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Den här uppgiften i flera steg gör följande:

1. Kör två `build` steg för att skapa avbildningar från Dockerfile i arbetskatalogen:
    * Den första är inriktad `Run.Registry` på , registret där aktiviteten körs och taggas med ACR-uppgifter körnings-ID. 
    * Det andra är det register som identifieras av värdet för , som du anger när du skapar uppgiften (eller anger via `regDate` en extern fil som skickas till `values.yaml` `az acr task create` ). Den här avbildningen är taggad med körningsdatumet.
1. Kör ett `cmd` steg för att köra en av de skapade containrarna. Det här exemplet startar en långvarig container i bakgrunden och returnerar container-ID:t och stoppar sedan containern. I ett verkligt scenario kan du testa en container som körs för att säkerställa att den körs korrekt.
1. I ett `push` steg push-pushar de avbildningar som skapats, den första till körningsregistret, den andra till registret som identifieras av `regDate` .

### <a name="task-command"></a>Aktivitetskommando

Med hjälp av de gränssnittsmiljövariabler som definierades tidigare skapar du uppgiften genom att köra [följande az acr task create-kommando.][az-acr-task-create] Ersätt namnet på ditt register med *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Lägga till uppgiftsuppgift

Om du vill skicka avbildningar till registret som identifieras av värdet för använder du kommandot `regDate` [az acr task credential add][az-acr-task-credential-add] för att lägga till inloggningsuppgifter för registret i uppgiften.

I det här exemplet rekommenderar [](container-registry-auth-service-principal.md) vi att du skapar ett huvudnamn för tjänsten med åtkomst till registret som är begränsad till *rollen AcrPush,* så att den har behörighet att push-pusha avbildningar. Information om hur du skapar tjänstens huvudnamn finns i det här [Azure CLI-skriptet](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Skicka program-ID och lösenord för tjänstens huvudnamn i följande `az acr task credential add` kommando. Se till att uppdatera inloggningsserverns *namn mycontainerregistrydate* med namnet på ditt andra register:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

CLI returnerar namnet på den registerinloggningsserver som du lade till.

### <a name="test-the-multi-step-workflow"></a>Testa arbetsflödet i flera steg

Som i föregående exempel kan du testa uppgiften i flera steg genom att utlösa den manuellt genom att köra [kommandot az acr task run.][az-acr-task-run] Information om hur du utlöser uppgiften med en commit till Git-lagringsplatsen finns i [avsnittet Trigger a build with a commit](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Som standard strömmar kommandot `az acr task run` loggens utdata till konsolen när du kör kommandot. Som tidigare visar utdata förloppet för att köra vart och ett av uppgiftsstegen. Utdata komprimeras för att visa viktiga steg.

Utdata:

```output
Queued a run with ID: cf1g
Waiting for an agent...
2020/11/20 04:33:39 Downloading source code...
2020/11/20 04:33:41 Finished downloading source code
2020/11/20 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2020/11/20 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 04:33:43 Successfully set up Docker network: acb_default_network
2020/11/20 04:33:43 Setting up Docker configuration...
2020/11/20 04:33:44 Successfully set up Docker configuration
2020/11/20 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:47 Scanning for dependencies...
2020/11/20 04:33:47 Successfully scanned dependencies
2020/11/20 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:33:55 Successfully executed container: acb_step_0
2020/11/20 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:55 Scanning for dependencies...
2020/11/20 04:33:56 Successfully scanned dependencies
2020/11/20 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:33:57 Successfully executed container: acb_step_1
2020/11/20 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2020/11/20 04:33:58 Successfully executed container: acb_step_2
2020/11/20 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:58 Launching container with name: acb_step_3
test
2020/11/20 04:34:09 Successfully executed container: acb_step_3
2020/11/20 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]
[...]
2020/11/20 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]
[...]
2020/11/20 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2020/11/20 04:34:19 Populating digests for step ID: acb_step_0...
2020/11/20 04:34:21 Successfully populated digests for step ID: acb_step_0
2020/11/20 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2020/11/20 04:34:21 Populating digests for step ID: acb_step_1...
2020/11/20 04:34:23 Successfully populated digests for step ID: acb_step_1
2020/11/20 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2020/11/20 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2020/11/20 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2020/11/20 04:34:23 The following dependencies were found:
2020/11/20 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar uppgifter med flera steg och flera containrar som utlöses automatiskt när du genomför källkoden på en Git-lagringsplats. Avancerade funktioner för uppgifter i flera steg, inklusive parallell och beroende stegkörning, finns i [ACR-uppgifter YAML-referens .](container-registry-tasks-reference-yaml.md) Gå vidare till nästa självstudie för att lära dig att skapa uppgifter som utlöser versioner när en containeravbildnings basavbildning uppdateras.

> [!div class="nextstepaction"]
> [Automatisera versioner i basavbildningsuppdateringar](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add    
[az-login]: /cli/azure/reference-index#az_login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
