---
title: Självstudie – ACR uppgift för flera steg
description: I den här självstudien får du lära dig hur du konfigurerar en Azure Container Registry aktivitet för att automatiskt utlösa ett arbets flöde för flera steg för att skapa, köra och push-behållar avbildningar i molnet när du allokerar käll koden till en git-lagringsplats.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: be5c8c4012267dc7ce6362502c806a9f238732b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920281"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Självstudie: köra ett arbets flöde för flera steg i molnet när du genomför käll koden

Förutom en [snabb uppgift](container-registry-tutorial-quick-task.md)stöder ACR-aktiviteter multi-Step-baserade arbets flöden med flera behållare som automatiskt kan utlösas när du allokerar käll koden till en git-lagringsplats. 

I den här självstudien får du lära dig att använda exempel YAML-filer för att definiera aktiviteter med flera steg som skapar, kör och pushar en eller flera behållar avbildningar till ett register när du allokerar käll koden. Information om hur du skapar en uppgift som bara automatiserar en enda avbildnings version vid kod genomförande finns i [självstudie: automatisera behållar avbildningar i molnet när du allokerar käll koden](container-registry-tutorial-build-task.md). En översikt över ACR-aktiviteter finns i [Automatisera OS-och Framework-korrigering med ACR-aktiviteter](container-registry-tasks-overview.md),

I de här självstudierna har du

> [!div class="checklist"]
> * Definiera en multi-Step-aktivitet med en YAML-fil
> * Skapa en uppgift
> * Du kan också lägga till autentiseringsuppgifter till uppgiften för att ge åtkomst till ett annat register
> * Testa uppgiften
> * Visa status för aktivitet
> * Utlösa uppgiften med en kodincheckning

Självstudien förutsätter att du redan har slutfört stegen i den [föregående självstudien](container-registry-tutorial-quick-task.md). Om du inte har gjort det, slutför du stegen i avsnittet [Krav](container-registry-tutorial-quick-task.md#prerequisites) i föregående självstudie innan du fortsätter.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-a-multi-step-task"></a>Skapa en aktivitet med flera steg

Nu när du har slutfört stegen som krävs för att aktivera ACR-aktiviteter för att läsa inchecknings status och skapa Webhooks i en lagrings plats, skapar du en aktivitet med flera steg som utlöser skapandet, körning och push-överföring av en behållar avbildning

### <a name="yaml-file"></a>YAML-fil

Du definierar stegen för en aktivitet med flera steg i en [yaml-fil](container-registry-tasks-reference-yaml.md). Det första exemplet på flera steg i den här självstudien definieras i filen `taskmulti.yaml` , som finns i roten av GitHub-lagrings platsen som du har klonat:

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

Den här aktiviteten i flera steg gör följande:

1. Kör ett `build` steg för att bygga en avbildning från Dockerfile i arbets katalogen. Bilden är riktad `Run.Registry` mot, registret där aktiviteten körs och är Taggad med ett unikt ACR-ID för aktiviteter. 
1. Kör ett `cmd` steg för att köra avbildningen i en tillfällig behållare. Det här exemplet startar en långvarig behållare i bakgrunden och returnerar container-ID: t och stoppar sedan behållaren. I ett verkligt scenario kan du inkludera steg för att testa den behållare som körs för att säkerställa att den fungerar som den ska.
1. I ett `push` steg skickar den avbildning som har skapats i körnings registret.

### <a name="task-command"></a>Uppgifts kommando

Fyll först i de här gränssnittsmiljövariablerna med värden som är lämpliga för din miljö. Det här steget är inte obligatoriskt, men det gör det lite enklare att köra de flerradiga Azure CLI-kommandona i den här självstudien. Om du inte fyller i de här miljövariablerna måste du ersätta varje värde manuellt var det visas i exempel kommandona.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Skapa nu uppgiften genom att köra följande [AZ ACR uppgift Create][az-acr-task-create] -kommando:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Den här uppgiften anger att en viss tidskod ska allokeras till *huvud* grenen i den databas som anges av `--context` , ACR-aktiviteter kommer att köra aktiviteten multi-Step från koden i den grenen. YAML-filen som anges av `--file` från lagrings platsens rot definierar stegen. 

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

## <a name="test-the-multi-step-workflow"></a>Testa arbets flödet för flera steg

Om du vill testa aktiviteten i flera steg aktiverar du den manuellt genom att köra kommandot [AZ ACR Task Run][az-acr-task-run] :

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Som standard strömmar kommandot `az acr task run` loggens utdata till konsolen när du kör kommandot. Utdata visar förloppet för att köra varje aktivitets steg. Utdata nedan komprimeras för att Visa viktiga steg.

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

När du har skickat en incheckning till din lagrings plats, utlöses webhooken som skapats av ACR-aktiviteter och startar uppgiften i Azure Container Registry. Visa loggarna för den uppgift som körs för att kontrollera och övervaka versionsförloppet:

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

## <a name="create-a-multi-registry-multi-step-task"></a>Skapa en aktivitet med flera register flera steg

ACR-uppgifter som standard har behörighet att skicka eller hämta bilder från registret där aktiviteten körs. Du kanske vill köra en aktivitet med flera steg som är riktad mot en eller flera register utöver körnings registret. Du kan till exempel behöva skapa avbildningar i ett register och lagra bilder med olika Taggar i ett andra register som används av ett produktions system. Det här exemplet visar hur du skapar en sådan uppgift och anger autentiseringsuppgifter för ett annat register.

Om du inte redan har ett andra register skapar du ett för det här exemplet. Om du behöver ett register kan du gå till [föregående självstudie](container-registry-tutorial-quick-task.md) eller [Snabbstart: Skapa ett containerregister med hjälp av Azure CLI](container-registry-get-started-azure-cli.md).

Om du vill skapa uppgiften behöver du namnet på registrerings servern för registret, som har formatet *mycontainerregistrydate.azurecr.io* (alla gemener). I det här exemplet använder du det andra registret för att lagra bilder som taggats efter build-datum.

### <a name="yaml-file"></a>YAML-fil

Det andra exemplet på flera steg i den här självstudien definieras i filen `taskmulti-multiregistry.yaml` , som finns i roten av GitHub-lagrings platsen som du har klonat:

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

Den här aktiviteten i flera steg gör följande:

1. Kör två `build` steg för att bygga avbildningar från Dockerfile i arbets katalogen:
    * Det första målet är `Run.Registry` , registret där aktiviteten körs och är märkt med körnings-ID: t för ACR Tasks. 
    * Den andra är målet för registret som identifieras av värdet för `regDate` , som du anger när du skapar aktiviteten (eller genom att ange en extern `values.yaml` fil som skickas till `az acr task create` ). Den här bilden är märkt med körnings datumet.
1. Kör ett `cmd` steg för att köra en av de inbyggda behållarna. Det här exemplet startar en långvarig behållare i bakgrunden och returnerar container-ID: t och stoppar sedan behållaren. I ett verkligt scenario kan du testa en behållare som körs för att säkerställa att den fungerar som den ska.
1. I ett `push` steg skickar de avbildningar som har skapats, först till körnings registret, den andra till registret som identifieras av `regDate` .

### <a name="task-command"></a>Uppgifts kommando

Använd de variabler som definierats ovan för att skapa uppgiften genom att köra följande [AZ ACR uppgift Create][az-acr-task-create] -kommando. Ersätt namnet på registret för *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Lägg till autentiseringsuppgifter för aktivitet

Om du vill skicka avbildningar till registret som identifieras med värdet för `regDate` använder du kommandot [AZ ACR Task Credential Add][az-acr-task-credential-add] för att lägga till inloggnings uppgifter för det registret till aktiviteten.

I det här exemplet rekommenderar vi att du skapar ett [huvud namn för tjänsten](container-registry-auth-service-principal.md) med åtkomst till registret som omfattas av *AcrPush* -rollen, så att det har behörighet att skicka avbildningar. Information om hur du skapar tjänstens huvud namn finns i det här [Azure CLI-skriptet](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Överför program-ID och lösen ord för tjänstens huvud namn i följande `az acr task credential add` kommando. Se till att uppdatera inloggnings Server namnet *mycontainerregistrydate* med namnet på ditt andra register:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

CLI returnerar namnet på den registrerings Server för registret som du har lagt till.

### <a name="test-the-multi-step-workflow"></a>Testa arbets flödet för flera steg

Som i föregående exempel för att testa multi-Step-aktiviteten utlöser du den manuellt genom att köra kommandot [AZ ACR Task Run][az-acr-task-run] . Information om hur du utlöser uppgiften med en incheckning av Git-lagringsplatsen finns i avsnittet [utlösa en version med incheckning](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Som standard strömmar kommandot `az acr task run` loggens utdata till konsolen när du kör kommandot. Som tidigare visar utdata förloppet för att köra varje aktivitets steg. Utdata komprimeras för att Visa viktiga steg.

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

I den här självstudien har du lärt dig hur du skapar multi-Step, multi-container-baserade uppgifter som automatiskt utlöses när du allokerar käll koden till en git-lagringsplats. Avancerade funktioner i aktiviteter i flera steg, inklusive parallell-och beroende steg körning, finns i [ACR tasks yaml Reference](container-registry-tasks-reference-yaml.md). Gå vidare till nästa självstudie för att lära dig att skapa uppgifter som utlöser versioner när en containeravbildnings basavbildning uppdateras.

> [!div class="nextstepaction"]
> [Automatisera versioner i basavbildningsuppdateringar](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
