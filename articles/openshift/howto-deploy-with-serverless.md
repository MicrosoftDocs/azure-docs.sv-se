---
title: Distribuera ett program till Azure Red Hat OpenShift med Hjälp av OpenShift Serverless
description: Lär dig hur du distribuerar ett program till Azure Red Hat OpenShift med hjälp av OpenShift Serverless
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, serverless
ms.openlocfilehash: 6db87e752745e3df919c93b2ffc8144e5886b319
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532832"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>Distribuera ett program till Azure Red Hat OpenShift med Hjälp av OpenShift Serverless

I den här artikeln distribuerar du ett program till ett Azure Red Hat OpenShift-kluster med [hjälp av OpenShift Serverless](https://www.openshift.com/learn/topics/serverless). OpenShift Serverless hjälper utvecklare att distribuera och köra program som skalar upp eller skalas upp till noll på begäran, vilket eliminerar resursförbrukningen när den inte används.

Programkoden kan paketeras i en container tillsammans med lämpliga körningar, och de serverlösa funktionerna startar programcontainrarna när de utlöses av en händelse. Program kan utlösas av olika händelsekällor, till exempel händelser från dina egna program, molntjänster från flera leverantörer, SaaS-system (Programvara som en tjänst) och andra tjänster.

Hantering av alla aspekter av distributionen av en container på ett serverlöst sätt är inbyggt direkt i OpenShift-gränssnittet. Utvecklare kan visuellt identifiera vilka händelser som driver lanseringen av sina containerprogram, med flera olika sätt att ändra händelseparametrarna. Serverlösa OpenShift-program kan integreras med andra OpenShift-tjänster, till exempel OpenShift Pipelines, Service Mesh och Övervakning, och ger en komplett serverlös programutvecklings- och distributionsupplevelse.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>Installera kommandoradsverktyget Knative (k)

Du kan ladda ned den senaste versionen av CLI som är lämplig för din dator från <https://github.com/knative/client/releases/>

Om du kör kommandona på den här Azure Cloud Shell du den senaste Knative CLI för Linux.

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>Starta webbkonsolen

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

## <a name="install-the-openshift-serverless-operator"></a>Installera OpenShift Serverless-operatorn

När du har loggat in på OpenShift-webbkonsolen kontrollerar du att du är i *administratörsperspektivet.* Öppna *operatorhubben* och leta efter **OpenShift Serverless-operatorn** och välj den.

![Leta efter OpenShift Serverless-operatorn](media/serverless/serverless-operatorhub.png)

Öppna sedan operatörsinstallationssidan genom att klicka på **Installera**.

![Klicka på Installera för att installera operatorn](media/serverless/serverless-clickinstall.png)

Välj lämplig *uppdateringskanal* för Azure Red Hat OpenShift klusterversion och installera operatorn i `openshift-serverless` namnområdet. Rulla nedåt och klicka på **Installera.**

![Installationssida för operatör](media/serverless/serverless-installpage.png)

Efter några minuter bör statussidan visa att operatorn är installerad och är redo att användas. Klicka på knappen **Visa operatör för** att fortsätta.

![Operatorn är installerad och är redo att användas](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>Installera knative serving

Möjligheten att köra alla containrar på ett serverlöst sätt på OpenShift Serverless är möjlig med hjälp av upstream Knative. Knative utökar Kubernetes med en uppsättning komponenter för distribution, körning och hantering av moderna program med hjälp av den serverlösa metoden.

### <a name="create-an-instance-of-the-knative-serving"></a>Skapa en instans av knative serving

Växla till namnområdet genom att klicka på projektlistrutan längst upp till vänster. Under Tillhandahållna API:er klickar du på Skapa instans `knative-serving` på kortet *Knative Serving (Knavativ betjänande).*  

![Klicka för att skapa en Knative Service-instans](media/serverless/serverless-createknativeserving.png)

Behåll standardvärdena och rulla nedåt på sidan *Skapa knavande* betjänande för att klicka på **knappen** Skapa.

![Behåll standardinställningarna och klicka på Skapa](media/serverless/serverless-createknativeserving2.png)

Vänta tills *kolumnen Status* visar **Klar.** Sedan ska OpenShift Serverless installeras och du är redo att skapa ett OpenShift Serverless-projekt.

![Knative Serving Ready](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>Skapa ett serverlöst projekt

Kör kommandot för att skapa ett `demoserverless` nytt projekt med namnet :

```azurecli-interactive
oc new-project demoserverless
```

Du bör se utdata som liknar följande:

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

Växla till *developer-perspektivet* i stället *för* administratörsperspektivet på menyn till vänster och `demoserverless` välj i listan över projekt. Du bör sedan vara på *sidan Topologi* för projektet.

![Azure Red Hat OpenShift för projekttopologi](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>Distribuera med webbkonsolen

Från de alternativ som visas för att distribuera ett program väljer du *Från Git.* Då hamnar du på sidan *Importera från Git.* Använd `https://github.com/sclorg/django-ex.git` som **Git-lagringsplatsens URL.** Exempelwebbappen implementeras med programmeringsspråket Python.

![Azure Red Hat OpenShift projekt från Git](media/serverless/serverless-from-git.png)

> [!NOTE]
> OpenShift identifierar att det här är ett Python-projekt och väljer lämplig byggaravbildning.

Rulla ned till *Resurser* och se till att **Knative Service** är valt som resurstyp att generera. Den här åtgärden skapar en Knative Service, en typ av distribution som möjliggör serverlös OpenShift-skalning till noll när den är inaktiv.

![Azure Red Hat OpenShift projekt – Knative](media/serverless/serverless-knative.png)


När du är klar klickar du på Skapa längst ned på **sidan.** Detta skapar resurser för att hantera programmets version och distribution. Du omdirigeras sedan till topologiöversikten för projektet.

Topologiöversikten ger en visuell representation av programmet som du har distribuerat. Med den här vyn kan du se den övergripande programstrukturen.

Vänta tills bygget har slutförts. Det kan ta några minuter. När bygget är klart visas en grön bockmarkering i det nedre vänstra hörnet av tjänsten.

![Azure Red Hat OpenShift projekttopologi – Kom fram](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>Visa din programskala

I listan *Visningsalternativ* överst i topologivyn klickar du på *Antal poddar.* Vänta tills antalet poddar skalas ned till noll poddar. Nedskalning kan ta några minuter.

![Azure Red Hat OpenShift projekttopologi – skalad till noll](media/serverless/serverless-scaledtozero.png)

Klicka på *ikonen Öppna URL* i det övre högra hörnet på panelen Knativ tjänst. Programmet öppnas på en ny flik. Stäng den nya webbläsarfliken och återgå till vyn Topologi. I vyn Topologi kan du se att programmet har skalats upp till en podd för att hantera din begäran. Efter några minuter skalar programmet ned till noll poddar.

![Azure Red Hat OpenShift projekttopologi – skalad till en](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>Framtvinga en ny revision och ange trafikdistribution

Med varje uppdatering av konfigurationen av en tjänst skapas en ny revision för tjänsten. Tjänstvägen pekar all trafik till den senaste redo revisionen som standard. Du kan ändra det här beteendet genom att definiera vilken revision som hämtar en del av trafiken. De här tjänsterna tillåter trafikmappning, vilket innebär att revisioner av en tjänst kan mappas till en allokerad del av trafiken. Trafikmappning ger också ett alternativ för att skapa unika URL:er för specifika revisioner.

I *topologin* klickar du på revisionen i tjänsten för att visa dess information. Märkena under poddringen och högst upp i informationspanelen ska vara `(REV)` . I sidopanelen på fliken Resurser rullar *du* ned och klickar på konfigurationen som är associerad med din tjänst.

![Azure Red Hat OpenShift-projekttopologi – Revision](media/serverless/serverless-revdetails.png)

Tvinga en konfigurationsuppdatering genom att växla till *YAML-fliken* och rulla ned för att redigera värdet `timeoutSeconds` för till `301` . Klicka på **Spara** för att spara den uppdaterade konfigurationen. I ett verkligt scenario kan en sådan konfigurationsuppdatering också utlösas genom att uppdatera containeravbildningstaggen.

![Framtva en ny revision genom att uppdatera konfigurationen](media/serverless/serverless-confupdate.png)

Gå tillbaka till *vyn Topologi* ser du att en ny revision har distribuerats. Klicka på den tjänst som slutar med märket och klicka på knappen Ange trafikfördelning. Nu bör du kunna dela upp trafiken mellan de `(KSVC)` olika revisionerna av tjänsten. 

![Ange trafikdistribution](media/serverless/serverless-trafficdist.png)

Vyn *Topologi* visar nu hur trafiken fördelas mellan de två revisionerna.

![Granska trafikdistribution](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>Använda kommandoradsverktyget Knative (k)

I föregående steg har du använt OpenShift-webbkonsolen för att skapa och distribuera ett program till OpenShift Serverless. Eftersom OpenShift Serverless kör Knative under, kan du också använda knakativt kommandoradsverktyg (kn) för att skapa Knative-tjänster.

> [!NOTE]
> Om du inte redan har installerat `kn` CLI följer du stegen i avsnittet förutsättningar i den här artikeln. Kontrollera också att du har loggat in med kommandoradsverktyget `oc` OpenShift.

Vi ska använda en containeravbildning som redan har skapats på `quay.io/rhdevelopers/knative-tutorial-greeter` .

### <a name="deploy-a-service"></a>Distribuera en tjänst

Kör följande kommando för att distribuera tjänsten:

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

Du ser utdata som liknar nedanstående.

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Du kan hämta en lista över vägar i projektet genom att köra:

```azurecli-interactive
kn route list
```

Du får tillbaka en lista över vägar i namnområdet. Öppna URL:en i en webbläsare för att se den distribuerade tjänsten.

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>Distribuera en ny version av tjänsten

Distribuera en ny version av programmet genom att köra kommandot nedan och skicka `:latest` avbildningstaggen och en miljövariabel `MESSAGE_PREFIX` :

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

Du får en bekräftelse på att en ny revision `greeter-v2` har distribuerats.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Om du vill visa en lista över alla revisioner och deras trafikdistribution kör du följande kommando:

```azurecli-interactive
kn revision list
```

Du får en lista som liknar den nedan. Observera att den nya versionen får 100 % av trafiken.

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>Blå/grön och kanariedistribution

När en ny revision distribueras får den som standard 100 % av trafiken. Anta att du vill implementera en blå/grön distributionsstrategi där du snabbt kan återställa till den äldre versionen av programmet. Knask gör detta enkelt.

Uppdatera tjänsten för att skapa tre trafiktaggar när 100 % av trafiken skickas till

- **current**: pekar på den aktuella distribuerade versionen
- **prev**: punkter i den tidigare versionen
- **senaste:** pekar alltid på den senaste versionen

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

Du får en bekräftelse som liknar nedanstående.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Visa en lista över vägarna med kommandot nedan:

```azurecli-interactive
kn route describe greeter
```

Du får utdata som visar URL:erna för var och en av taggarna tillsammans med trafikdistributionen.

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Anta att du snabbt vill återställa till den tidigare versionen. Du kan uppdatera trafikdistributionen så att 100 % av trafiken skickas till föregående tagg:

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

Kontrollera igen genom att lista vägarna med hjälp av kommandot nedan:

```azurecli-interactive
kn route describe greeter
```

Du får utdata som visar att 100 % av trafikdistributionen går till den tidigare versionen.

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Spel runt med trafikdistributionen medan du uppdaterar huvudvägen ( `http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io` i det här fallet) i webbläsaren.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med programmet kan du köra följande kommando för att ta bort projektet:

```azurecli-interactive
oc delete project demoserverless
```

Du kan också ta bort klustret genom att följa anvisningarna i [Självstudie: Ta bort en Azure Red Hat OpenShift 4-kluster.](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig att:
> [!div class="checklist"]
>
> * Installera OpenShift Serverless-operatorn och Knative Serving
> * Distribuera ett serverlöst projekt med hjälp av webbkonsolen
> * Distribuera ett serverlöst projekt med hjälp av Knative CLI (k)
> * Konfigurera blå/gröna distributioner och kanariedistributioner med hjälp av KNA (k)

Läs mer om hur du skapar och distribuerar serverlösa, händelsedrivna program på Azure Red Hat OpenShift med hjälp av [OpenShift Serverless,](https://www.openshift.com/learn/topics/serverless)i dokumentationen Komma igång med [OpenShift Serverless](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html) och dokumentationen Skapa och hantera [serverlösa](https://docs.openshift.com/container-platform/4.6/serverless/serving-creating-managing-apps.html) program.
