---
title: Självstudie – Utlösa avbildningsbygge vid uppdatering av basavbildning
description: I den här självstudien får du lära dig hur du konfigurerar en Azure Container Registry-uppgift för att automatiskt utlösa containeravbildningsbyggen i molnet när en basavbildning uppdateras i samma register.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 6d83fb7796cecab686d52d561266bd41daef9e01
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780811"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Självstudie: Automatisera containeravbildningsbyggen när en basavbildning uppdateras i ett Azure-containerregister 

[ACR-uppgifter](container-registry-tasks-overview.md) har stöd för automatiserade containeravbildningsbyggen när en containers basavbildning [uppdateras,](container-registry-tasks-base-images.md)till exempel när du korrigerar operativsystemet eller programramverket i en av dina basavbildningar. 

I den här självstudien får du lära dig hur du skapar en ACR-uppgift som utlöser en version i molnet när en containers basavbildning skickas till samma register. Du kan också prova en självstudie för att skapa en ACR-uppgift som utlöser en avbildningsbygge när en basavbildning skickas till ett [annat Azure-containerregister.](container-registry-tutorial-private-base-image-update.md) 

I de här självstudierna har du

> [!div class="checklist"]
> * Skapa basavbildningen
> * Skapa en programavbildning i samma register för att spåra basavbildningen 
> * Uppdatera basavbildningen till att utlösa en programavbildningsuppgift
> * Visa den utlösta uppgiften
> * Kontrollera den uppdaterade programavbildningen

## <a name="prerequisites"></a>Förutsättningar

### <a name="complete-the-previous-tutorials"></a>Slutför de tidigare självstudierna

Den här självstudien förutsätter att du redan har konfigurerat din miljö och slutfört stegen i de första två självstudierna i serien, där du:

- Skapa Azure Container Registry
- Förgrena en exempellagringsplats
- Klona en exempellagringsplats
- Skapa en personlig åtkomsttoken för GitHub

Om du inte redan har gjort det slutför du följande självstudier innan du fortsätter:

[Skapa containeravbildningar i molnet med Azure Container Registry-uppgifter](container-registry-tutorial-quick-task.md)

[Automatisera containeravbildningar med Azure Container Registry-uppgifter](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Konfigurera miljön

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Den här artikeln kräver version 2.0.46 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

Fyll i de här gränssnittsmiljövariablerna med värden som är lämpliga för din miljö. Det här steget är inte obligatoriskt, men det gör det lite enklare att köra de flerradiga Azure CLI-kommandona i den här självstudien. Om du inte fyller i dessa miljövariabler måste du manuellt ersätta varje värde oavsett var det visas i exempelkommandona.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```


### <a name="base-image-update-scenario"></a>Uppdateringsscenario för basavbildningar

Den här självstudien går igenom ett uppdateringsscenario för basavbildningar där en basavbildning och en programavbildning underhålls i ett enda register. 

[Kodexemplet][code-sample] innehåller två Docker-filer: en programavbildning och en avbildning som anges som bas. I följande avsnitt skapar du en ACR-uppgift som automatiskt utlöser en version av programavbildningen när en ny version av basavbildningen skickas till samma containerregister.

* [Dockerfile-app][dockerfile-app]: En liten Node.js-webbapp som återger en statisk webbsida som visar vilken Node.js-version den är baserad på. Versionssträngen är simulerad: den visar innehållet i miljövariabeln `NODE_VERSION`, som definieras i basavbildningen.

* [Dockerfile-base][dockerfile-base]: Avbildningen som `Dockerfile-app` anger som bas. Den är baserad på en [nod][base-node]avbildning och inkluderar miljövariabeln `NODE_VERSION`.

I följande avsnitt skapar du en uppgift, uppdaterar värdet `NODE_VERSION` i basavbildningen Dockerfile och använder sedan ACR Tasks för att skapa basavbildningen. När ACR-uppgiften skickar den nya basavbildningen till registret utlöser den automatiskt en version av programavbildningen. Du kan också köra programmets containeravbildning lokalt om du vill se andra versionssträngar i versionsavbildningarna.

I den här självstudien skapar och pushar din ACR-uppgift en programcontaineravbildning som anges i en Dockerfile. ACR-uppgifter kan också köra uppgifter i flera steg med hjälp av en YAML-fil för att definiera steg för att skapa, [push-skicka](container-registry-tasks-multi-step.md)och eventuellt testa flera containrar.

## <a name="build-the-base-image"></a>Skapa basavbildningen

Börja med att skapa basavbildningen med ACR-uppgifter *snabbuppgift ,* med [hjälp av az acr build][az-acr-build]. Enligt beskrivningen i den [första självstudien](container-registry-tutorial-quick-task.md) i serien skapar denna process inte bara avbildningen, utan skickar den även till containerregistret om den lyckas.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Skapa en uppgift

Skapa sedan en uppgift med [az acr task create][az-acr-task-create]:

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample1 \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

Den här uppgiften liknar den uppgift som skapades i föregående [självstudie.](container-registry-tutorial-build-task.md) Den instruerar ACR Tasks att utlösa en avbildningsversion när incheckningar skickas till den lagringsplats som anges i `--context`. Den Dockerfile som användes för att skapa avbildningen i föregående självstudie anger en offentlig basavbildning ( ), men Dockerfile i den här `FROM node:15-alpine` [uppgiften, Dockerfile-app,][dockerfile-app]anger en basavbildning i samma register:

```dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Den här konfigurationen gör det enkelt att simulera en ramverkskorrigering i basavbildningen senare i den här självstudien.

## <a name="build-the-application-container"></a>Skapa programcontainern

Använd [az acr task run för][az-acr-task-run] att utlösa uppgiften manuellt och skapa programavbildningen. Det här steget krävs så att aktiviteten spårar programavbildningens beroende av basavbildningen.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample1
```

När uppgiften är klar antecknar du **Run ID** (till exempel ”da6”) om du vill slutföra följande valfria steg.

### <a name="optional-run-application-container-locally"></a>Valfritt: Köra programcontainern lokalt

Om du arbetar lokalt (inte i Cloud Shell) och har installerat Docker, kör du containern för att se det program som återges i webbläsaren innan du återskapar dess basavbildning. Hoppa över det här avsnittet om du använder Cloud Shell (Cloud Shell stöder inte `az acr login` eller `docker run`).

Först autentiserar du till containerregistret med [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Kör nu containern lokalt med `docker run`. Ersätt **\<run-id\>** med körnings-ID:t i utdata från föregående steg (till exempel "da6"). Det här exemplet namnger `myapp` containern och innehåller `--rm` parametern för att ta bort containern när du stoppar den.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Gå till `http://localhost:8080` i webbläsaren. Du bör nu se versionsnumret för Node.js som återgavs på webbsidan, liknande nedan. I ett senare steg ökar du versionen genom att lägga till ett ”a” i versionssträngen.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="Skärmbild av exempelprogrammet i webbläsaren":::

Kör följande kommando för att stoppa och ta bort containern:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Lista versionerna

Nu listar du de uppgiftskörningar som ACR Tasks har slutfört för ditt register med hjälp av kommandot [az acr task list-runs][az-acr-task-list-runs]:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Om du slutförde föregående självstudie (och inte tog bort registret) bör du nu se utdata som liknar följande. Anteckna antalet uppgiftskörningar och senaste RUN ID så att du kan jämföra utdata när du har uppdaterat basavbildningen i nästa avsnitt.

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cax       baseexample1    linux       Succeeded  Manual     2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual     2020-11-20T23:07:29Z  00:00:27
```

## <a name="update-the-base-image"></a>Uppdatera basavbildningen

Här simulerar du en ramverkskorrigering i basavbildningen. Redigera **Dockerfile-base** och lägg till ett ”a” efter versionsnumret som definieras i `NODE_VERSION`:

```dockerfile
ENV NODE_VERSION 15.2.1a
```

Kör en snabbuppgift för att skapa den ändrade basavbildningen. Anteckna **Run ID** i utdata.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

När versionen är klar och ACR-uppgiften har skickat den nya basavbildningen till registret utlöser den automatiskt en version av programavbildningen. Det kan ta en stund innan den uppgift som du skapade tidigare utlöser programmets avbildningsversion, eftersom den måste identifiera den nyligen slutförda och skickade basavbildningen.

## <a name="list-updated-build"></a>Lista uppdaterad version

Nu när du har uppdaterat basavbildningen listar du återigen dina uppgiftskörningar för att kunna jämföra med den tidigare listan. Om utdatan inte skiljer sig åt kan du köra kommandot flera gånger för att se när den nya uppgiftskörningen visas i listan.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

De utdata som genereras liknar följande. TRIGGER (utlösaren) för den senast körda versionen ska vara ”Image Update” (Avbildningsuppdatering), vilket anger att uppgiften startades av snabbuppgiften för basavbildningen.

```output
Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual        2020-11-20T23:07:29Z  00:00:27
```

Om du vill utföra följande valfria steg för att köra den nyskapade containern för att se det uppdaterade versionsnumret anteckningen av **run-ID-värdet** för den avbildningsuppdateringsutlösta versionen (i föregående utdata är det "ca11").

### <a name="optional-run-newly-built-image"></a>Valfritt: Köra nyskapad avbildning

Om du arbetar lokalt (inte i Cloud Shell) och du har installerat Docker, kör du den nya programavbildningen när dess version är klar. Ersätt `<run-id>` med det RUN ID som du hämtade i föregående steg. Hoppa över det här avsnittet om du använder Cloud Shell (Cloud Shell stöder inte `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Gå till http://localhost:8081 i webbläsaren. Du bör nu se det uppdaterade versionsnumret för Node.js (med ett ”a”) på webbsidan:

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="Skärmbild av uppdaterat exempelprogram i webbläsaren":::


Observera att du har uppdaterat din **basavbildning** med ett nytt versionsnummer, men den senaste skapade **programavbildningen** visar den nya versionen. ACR Tasks hämtade din ändring av basavbildningen och återskapade din programavbildning automatiskt.

Kör följande kommando för att stoppa och ta bort containern:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda en uppgift till att utlösa containeravbildningsversioner automatiskt när en basavbildning har uppdaterats. Gå nu vidare till nästa självstudie för att lära dig hur du utlöser aktiviteter enligt ett definierat schema.

> [!div class="nextstepaction"]
> [Köra en uppgift enligt ett schema](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-node]: https://hub.docker.com/_/node/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr
