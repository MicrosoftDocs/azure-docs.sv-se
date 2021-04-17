---
title: Distribuera ett program från källan till Azure Red Hat OpenShift
description: Lär dig hur du distribuerar ett program Azure Red Hat OpenShift från källkod med hjälp av S2I-byggstrategin (Source-to-Image)
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, s2i, source to image
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559247"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>Distribuera ett program från källan till Azure Red Hat OpenShift

I den här artikeln distribuerar du ett program till ett Azure Red Hat OpenShift-kluster från källkod med hjälp av en S2I-version (source-to-image). [Source-to-Image (S2I)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds) är en byggprocess för att skapa reproducerbara containeravbildningar från källkod. S2I skapar färdiga avbildningar genom att mata in källkod i en containeravbildning och låta containern förbereda källkoden för körning. Du kan ha OpenShift som skapar ett program från källan för att distribuera det, så att du inte behöver skapa en container för hand med varje ändring. OpenShift kan sedan skapa och distribuera nya versioner automatiskt när de meddelas om ändringar i källkoden.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>Skapa ett projekt

Kör kommandot för att skapa `demoproject` ett nytt projekt med namnet :

```azurecli-interactive
oc new-project demoproject
```

Du bör se utdata som liknar följande:

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>Starta webbkonsolen

Ta reda på klusterwebbkonsolens URL genom att köra:

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

Du bör få en URL som liknar den här.

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

Starta konsolens URL i en webbläsare och logga in med `kubeadmin` autentiseringsuppgifterna.

:::image type="content" source="media/login.png" alt-text="Azure Red Hat OpenShift inloggningsskärm":::

Växla till *developer-perspektivet* i stället *för* administratörsperspektivet på menyn till vänster och `demoproject` välj i listan över projekt. Du bör då vara på *sidan Topologi* för projektet.

:::image type="content" source="media/s2i/project-topology.png" alt-text="Azure Red Hat OpenShift projekttopologi":::

Eftersom projektet är tomt bör inga arbetsbelastningar hittas och du får olika alternativ för hur du kan distribuera ett program.

## <a name="deploying-using-the-web-console"></a>Distribuera med webbkonsolen

Från de alternativ som visas för att distribuera ett program väljer du *Från Git.* Då hamnar du på *sidan Importera från Git.* Använd `https://github.com/sclorg/django-ex.git` som **Git-lagringsplatsens URL.** Exempelwebbappen implementeras med programmeringsspråket Python.

:::image type="content" source="media/s2i/from-git.png" alt-text="Azure Red Hat OpenShift projekt från Git":::

> [!NOTE]
> OpenShift identifierar att det här är ett Python-projekt och väljer lämplig byggaravbildning.

Rulla ned till *Avancerade alternativ* och kontrollera att Skapa en väg **till programmet** är markerat. Den här åtgärden skapar en OpenShift-väg, ett sätt att exponera en tjänst genom att ge den ett externt nåbart värdnamn.

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Azure Red Hat OpenShift projekt från Git – Route-konfiguration":::

När du är klar klickar du på Skapa längst ned på **sidan.** Detta skapar resurser för att hantera programmets version och distribution. Du omdirigeras sedan till topologiöversikten för projektet.

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Azure Red Hat OpenShift projekt från Git – topologi":::

Topologiöversikten ger en visuell representation av programmet som du har distribuerat. I den här vyn kan du se den övergripande programstrukturen.

Du kan klicka på Git-ikonen för att ta dig till Git-lagringsplatsen där källkoden för programmet har skapats. Ikonen som visas längst ned till vänster visar programmets build-status. Om du klickar på den här ikonen kommer du till avsnittet bygginformation. Om programmet har exponerade vägar kan du klicka på ikonen längst upp till höger för att öppna URL:en för programvägen som skapades.

När programmet skalar upp eller ned, startar distribuerar och återskapar poddar, animeras programrepresentationen i topologivyn för att ge dig en realtidsvy över vad som händer.

Om du klickar på programikonen visas mer information enligt nedan.

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Azure Red Hat OpenShift-projekt från Git – Information":::

## <a name="viewing-the-builder-logs"></a>Visa byggarloggarna

När bygget har startat klickar du på *länken Visa loggar* som visas på *panelen* Resurser.

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Azure Red Hat OpenShift-projekt från Git – Skapa loggar":::

På så sätt kan du övervaka förloppet för bygget när det körs. Byggaravbildningen Python matar i det här fallet in programmets källkod i den slutliga avbildningen innan den push-lar den till det interna OpenShift-avbildningsregistret. Bygget har slutförts när du ser ett slutligt meddelande om "Push lyckades".

## <a name="accessing-the-application"></a>Åtkomst till programmet

När programavbildningen har byggts distribueras den.

Klicka på *Topologi* i den vänstra menyraden för att återgå till topologivyn för projektet. När du skapade programmet med hjälp  av webbkonsolen skapades en väg automatiskt för programmet och den exponeras utanför klustret. Url:en som kan användas för att komma åt programmet från en webbläsare var synlig på *fliken Resurser* för det program som du visade tidigare.

Från topologivyn kan du gå till URL:en för det distribuerade programmet genom att klicka på ikonen längst upp till höger i ringen. När distributionen är klar klickar du på ikonen så bör du se programmet som du distribuerade.

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Azure Red Hat OpenShift från Git – Bläddra i appen":::

## <a name="deploying-using-the-command-line"></a>Distribuera med hjälp av kommandoraden

Du har lärt dig hur du distribuerar ett program med hjälp av webbkonsolen. Nu kan du distribuera samma webbapp, men den här gången `oc` med hjälp av kommandoradsverktyget.

Kör följande kommando för att ta bort projektet och börja om:

```azurecli-interactive
oc delete project demoproject
```

Du bör få ett bekräftelsemeddelande om att `demoproject` har tagits bort.

```output
project.project.openshift.io "demoproject" deleted
```

Skapa igen `demoproject` genom att köra:

```azurecli-interactive
oc new-project demoproject
```

I projektet skapar du ett nytt program från källan på GitHub och anger S2I Builder för den senaste versionen av Python.

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

Utdata bör se ut ungefär så här:

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

OpenShift använder namnet på Git-lagringsplatsen som namn på programmet. Granska statusen för versionen och distributionen genom att köra:

```azurecli-interactive
oc status
```

När bygget och distributionen har slutförts bör du se utdata som liknar dem.

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

Om du vill exponera programmet utanför OpenShift-klustret måste du skapa en väg genom att köra:

```azurecli-interactive
oc expose service/django-ex
```

Du bör få en bekräftelse.

```output
route.route.openshift.io/django-ex exposed
```

Hämta URL:en genom att köra:

```azurecli-interactive
oc get route django-ex
```

Du bör få tillbaka värdnamnet som tilldelats till den väg som du kan använda för att bläddra till det distribuerade programmet.

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>Utlösa en ny binär version

När du arbetar med programmet vill du förmodligen göra ändringar och se dem distribuerade. Du kan enkelt konfigurera en webhook som utlöser en ny version och distribution med varje kod som genomför. Detta kanske dock inte är önskvärt eftersom du ibland vill se ändringarna utan att behöva skicka varje kodändring till lagringsplatsen.

I de fall där du snabbt iterera om ändringar kan du använda det som kallas för ett binärt bygge. För att demonstrera det här scenariot klonar du Git-lagringsplatsen för programmet lokalt genom att köra:

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

Detta skapar en underkatalog `django-ex` som innehåller källkoden för programmet:

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

Ändra till underkatalogen:

```azurecli-interactive
cd django-ex
```

Öppna det integrerade Azure Cloud Shell redigeringsprogram:

```azurecli-interactive
code welcome/templates/welcome/index.html
```

Rulla nedåt och ändra raden som säger `Welcome to your Django application on OpenShift` `Welcome to Azure Red Hat OpenShift` . Spara filen och stäng redigeringsredigeraren `...` via menyn längst upp till höger.

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Azure Red Hat OpenShift från Git – Redigera program i Azure Cloud Shell redigeraren":::

Starta en ny version genom att köra kommandot :

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

Genom att skicka flaggan laddar OpenShift-kommandoraden upp källkoden från den angivna katalogen `--from-dir=.` och startar sedan bygg- och distributionsprocessen. Du bör få utdata som liknar nedanstående, och efter några minuter bör bygget slutföras.

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

Om du uppdaterar webbläsaren med programmet bör du se den uppdaterade rubriken.

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Azure Red Hat OpenShift projekt från Git – Bläddra i uppdaterad app":::

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med programmet kan du köra följande kommando för att ta bort projektet:

```azurecli-interactive
oc delete project demoproject
```

Du kan också ta bort klustret genom att följa anvisningarna i [Självstudie: Ta bort en Azure Red Hat OpenShift 4-kluster.](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig att:
> [!div class="checklist"]
>
> * Skapa ett projekt
> * Distribuera ett program från källkoden med hjälp av webbkonsolen
> * Distribuera ett program från källkoden med hjälp av OpenShift-kommandoraden
> * Utlösa en binär version med hjälp av OpenShift-kommandoraden

Läs mer om hur du skapar och distribuerar program med hjälp av käll-till-avbildning och [andra byggstrategier.](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html)
