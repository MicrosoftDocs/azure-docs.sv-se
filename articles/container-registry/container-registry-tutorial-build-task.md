---
title: Självstudie – Bygg avbildning vid kod genomförande
description: I självstudien får du lära dig att konfigurera en Azure Container Registry-uppgift till att utlösa containeravbildningsversioner i molnet automatiskt när du checkar in källkod på en Git-lagringsplats.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 139acf60ba20fd613b2dd2b190881d6bd574c21f
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919509"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>Självstudier: Automatisera containeravbildningar i molnet när du checkar in källkod

Förutom en [snabb uppgift](container-registry-tutorial-quick-task.md)har ACR-aktiviteter stöd för automatiserade Docker-behållar avbildningar som bygger i molnet när du allokerar käll koden till en git-lagringsplats. Git-kontexter som stöds för ACR-uppgifter inkluderar offentlig eller privat GitHub eller Azure databaser.

> [!NOTE]
> För närvarande stöder ACR-aktiviteter inte utlösare för commit eller pull-begäranden i GitHub Enterprise databaser.

I den här självstudien skapar din ACR-uppgift och push-överför en enda behållar avbildning som anges i en Dockerfile när du allokerar käll koden till en git-lagrings platsen. Om du vill skapa en [multi-Step-aktivitet](container-registry-tasks-multi-step.md) som använder en yaml-fil för att definiera steg för att skapa, skicka och välja att testa flera behållare på kod commit, se [Självstudier: köra ett arbets flöde för flera steg i molnet när du ska genomföra käll koden](container-registry-tutorial-multistep-task.md). En översikt över ACR-aktiviteter finns i [Automatisera OS-och Framework-korrigering med ACR-uppgifter](container-registry-tasks-overview.md)

I de här självstudierna har du

> [!div class="checklist"]
> * Skapa en uppgift
> * Testa uppgiften
> * Visa status för aktivitet
> * Utlösa uppgiften med en kodincheckning

Självstudien förutsätter att du redan har slutfört stegen i den [föregående självstudien](container-registry-tutorial-quick-task.md). Om du inte har gjort det, slutför du stegen i avsnittet [Krav](container-registry-tutorial-quick-task.md#prerequisites) i föregående självstudie innan du fortsätter.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-the-build-task"></a>Skapa versionsuppgiften

Nu när du har slutfört de steg som krävs för att göra så att ACR Tasks kan läsa incheckningsstatus och skapa webhooks på en lagringsplats kan du skapa en uppgift som utlöser en containeravbildningsversion på incheckningar till lagringsplatsen.

Fyll först i de här gränssnittsmiljövariablerna med värden som är lämpliga för din miljö. Det här steget är inte obligatoriskt, men det gör det lite enklare att köra de flerradiga Azure CLI-kommandona i den här självstudien. Om du inte fyller i de här miljövariablerna måste du ersätta varje värde manuellt var det visas i exempel kommandona.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Skapa nu uppgiften genom att köra följande [AZ ACR uppgift Create][az-acr-task-create] -kommando:

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```


Den här uppgiften anger att en viss tids kod allokeras till *huvud* grenen i den databas som anges av `--context` , ACR-aktiviteter skapar behållar avbildningen från koden i den grenen. Den Dockerfile som anges av `--file` från lagringsplatsroten används för att skapa avbildningen. Argumentet `--image` anger ett parametriserat värde på `{{.Run.ID}}` för versionsdelen av avbildningstaggen, vilket säkerställer att versionsavbildningen motsvarar en viss version och är unikt taggad.

Utdata från kommandot [az acr task create][az-acr-task-create] liknar följande:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2010-11-19T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node#main",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
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
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node#main",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
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

## <a name="test-the-build-task"></a>Testa versionsuppgiften

Nu har du en uppgift som definierar din version. Om du vill testa versionspipelinen utlöser du en version manuellt genom att köra kommandot [az acr task run][az-acr-task-run]:

```azurecli
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Som standard strömmar kommandot `az acr task run` loggens utdata till konsolen när du kör kommandot. Utdata komprimeras för att Visa viktiga steg.

```output
2020/11/19 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2020/11/19 22:51:00 Setting up Docker configuration...
2020/11/19 22:51:02 Successfully set up Docker configuration
2020/11/19 22:51:02 Logging in to registry: myregistry.azurecr.io
2020/11/19 22:51:03 Successfully logged in
2020/11/19 22:51:03 Executing step: build
2020/11/19 22:51:03 Obtaining source code and scanning for dependencies...
2020/11/19 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:15-alpine
[...]
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2020/11/19 22:51:11 Executing step: push
2020/11/19 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2020/11/19 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2020/11/19 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2020/11/19 22:51:21 Populating digests for step id: build...
2020/11/19 22:51:22 Successfully populated digests for step id: build
2020/11/19 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: ca6 was successful after 27s
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

```azurecli
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

När du har skickat en incheckning till lagringsplatsen startar den webhook som skapades av ACR Tasks en version i Azure Container Registry. Visa loggarna för den uppgift som körs för att kontrollera och övervaka versionsförloppet:

```azurecli
az acr task logs --registry $ACR_NAME
```

Utdata liknar följande och visar den uppgift som körs för närvarande (eller som kördes senast):

```output
Showing logs of the last created run.
Run ID: ca7

[...]

Run ID: ca7 was successful after 38s
```

## <a name="list-builds"></a>Versionslista

Om du vill se en lista över de uppgiftskörningar som ACR Tasks har slutfört för ditt register kör du kommandot [az acr task list-runs][az-acr-task-list-runs]:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Utdatan från kommandot ska se ut ungefär som följande. De körningar som ACR Tasks har kört visas och ”Git Commit” (Git-incheckning) visas i kolumnen TRIGGER (Utlösare) för den senaste uppgiften:

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
ca7       taskhelloworld  linux       Succeeded  Commit     2020-11-19T22:54:34Z  00:00:29
ca6       taskhelloworld  linux       Succeeded  Manual     2020-11-19T22:51:47Z  00:00:24
ca5                       linux       Succeeded  Manual     2020-11-19T22:23:42Z  00:00:23
```

## <a name="next-steps"></a>Nästa steg

I självstudien har du lärt dig att använda en uppgift för att utlösa containeravbildningsversioner i Azure automatiskt när du checkar in källkod på en Git-lagringsplats. Gå vidare till nästa självstudie för att lära dig att skapa uppgifter som utlöser versioner när en containeravbildnings basavbildning uppdateras.

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
[az-login]: /cli/azure/reference-index#az-login



