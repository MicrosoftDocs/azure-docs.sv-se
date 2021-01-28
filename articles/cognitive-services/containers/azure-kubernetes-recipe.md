---
title: Kör Språkidentifiering behållare i Kubernetes-tjänsten
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Distribuera behållaren för språk identifiering, med ett exempel som körs, till Azure Kubernetes-tjänsten och testa det i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6918218d8434c06f59b0738e60cad53b94b0a0b5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939840"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>Distribuera Textanalys språk identifierings behållare till Azure Kubernetes-tjänsten

Lär dig hur du distribuerar behållaren för språk identifiering. Den här proceduren visar hur du skapar de lokala Docker-behållare, push-överför behållarna till ditt eget privata behållar register, kör behållaren i ett Kubernetes-kluster och testar det i en webbläsare.

## <a name="prerequisites"></a>Förutsättningar

Den här proceduren kräver flera verktyg som måste installeras och köras lokalt. Använd inte Azure Cloud Shell.

* Använd en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services) innan du börjar.
* [Git](https://git-scm.com/downloads) för ditt operativ system så att du kan klona [exemplet](https://github.com/Azure-Samples/cognitive-services-containers-samples) som används i den här proceduren.
* [Azure CLI](/cli/azure/install-azure-cli).
* [Docker-motorn](https://www.docker.com/products/docker-engine) och kontrollerar att Docker CLI fungerar i ett konsol fönster.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* En Azure-resurs med rätt pris nivå. Alla pris nivåer fungerar inte med den här behållaren:
  * **Textanalys** endast resurs med F0 eller standard pris nivåer.
  * **Cognitive Services** resurs med pris nivån S0.

## <a name="running-the-sample"></a>Köra exemplet

Den här proceduren läser in och kör Cognitive Services container-exemplet för språk identifiering. Exemplet har två behållare, en för klient programmet och en för Cognitive Services container. Vi ska skicka båda avbildningarna till Azure Container Registry. När de finns i ditt eget register skapar du en Azure Kubernetes-tjänst för att få åtkomst till de här avbildningarna och kör behållarna. När behållarna körs använder du **kubectl** CLI för att se behållarens prestanda. Få åtkomst till klient programmet med en HTTP-begäran och se resultatet.

![Koncept idé för att köra exempel behållare](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Exempel behållare

Exemplet har två behållar avbildningar, en för klient delens webbplats. Den andra bilden är språk identifierings behållaren som returnerar textens identifierade språk (kultur). Båda behållarna är tillgängliga från en extern IP-adress när du är färdig.

### <a name="the-language-frontend-container"></a>Språk-frontend-behållare

Den här webbplatsen motsvarar ditt eget program på klient sidan som gör begäran av språk identifierings slut punkten. När proceduren är färdig får du det identifierade språket för en tecken sträng genom att komma åt webbplats behållaren i en webbläsare med `http://<external-IP>/<text-to-analyze>` . Ett exempel på denna URL är `http://132.12.23.255/helloworld!`. Resultatet i webbläsaren är `English` .

### <a name="the-language-container"></a>Språk behållare

Behållaren för språk identifiering, i den här specifika proceduren, är tillgänglig för alla externa förfrågningar. Behållaren har inte ändrats på något sätt så att standard-Cognitive Services-API för språk identifiering är tillgängligt.

Detta API är en POST-begäran för språk identifiering för den här behållaren. Precis som med alla Cognitive Services behållare kan du läsa mer om behållaren från den värdbaserade Swagger-informationen `http://<external-IP>:5000/swagger/index.html` .

Port 5000 är standard porten som används med Cognitive Services behållare.

## <a name="create-azure-container-registry-service"></a>Skapa Azure Container Registry tjänst

För att distribuera behållaren till Azure Kubernetes-tjänsten måste behållar avbildningarna vara tillgängliga. Skapa din egen Azure Container Registry-tjänst som värd för avbildningarna.

1. Logga in på Azure CLI

    ```azurecli-interactive
    az login
    ```

1. Skapa en resurs grupp med namnet `cogserv-container-rg` för att lagra alla resurser som skapats i den här proceduren.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Skapa en egen Azure Container Registry med namnet på ditt namn och sedan `registry` , till exempel `pattyregistry` . Använd inte bindestreck eller understrukna tecken i namnet.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Spara resultaten för att hämta egenskapen **namnet** . Detta kommer att ingå i den värd behållares adress, som används senare i `language.yml` filen.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Logga in i behållar registret. Du måste logga in innan du kan push-överföra avbildningar till registret.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Hämta webbplats Docker-avbildning

1. Exempel koden som används i den här proceduren finns i databasen för Cognitive Services containers-exempel. Klona lagrings platsen så att den har en lokal kopia av exemplet.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    När lagrings platsen finns på din lokala dator hittar du webbplatsen i [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) -katalogen. Den här webbplatsen fungerar som klient programmet som anropar API: t för språk identifiering som finns i behållaren för språk identifiering.  

1. Bygg Docker-avbildningen för den här webbplatsen. Kontrol lera att konsolen finns i [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) -katalogen där Dockerfile finns när du kör följande kommando:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Om du vill spåra versionen i behållar registret lägger du till taggen med versions format, till exempel `v1` .

1. Push-överför avbildningen till behållar registret. Det kan ta några minuter.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    `unauthorized: authentication required`Logga in med kommandot om du får ett fel meddelande `az acr login --name <your-container-registry-name>` . 

    När processen är färdig bör resultatet likna följande:

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Hämta Docker-avbildning för språk identifiering

1. Hämta den senaste versionen av Docker-avbildningen till den lokala datorn. Det kan ta några minuter. Om det finns en nyare version av behållaren ändrar du värdet från `1.1.006770001-amd64-preview` till den nyare versionen.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Tagga avbildningen med ditt behållar register. Hitta den senaste versionen och ersätt versionen `1.1.006770001-amd64-preview` om du har en senare version. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Push-överför avbildningen till behållar registret. Det kan ta några minuter.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Hämta Container Registry autentiseringsuppgifter

Följande steg krävs för att hämta den information som krävs för att ansluta ditt behållar register med Azure Kubernetes-tjänsten som du skapar senare i den här proceduren.

1. Skapa tjänstens huvud namn.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    Spara resultat `appId` värdet för tilldelnings parametern i steg 3, `<appId>` . Spara `password` för nästa avsnitts klient hemlighet-parameter `<client-secret>` .

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Hämta ditt behållar register-ID.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Spara utdata för värdet för parametern scope `<acrId>` i nästa steg. Det ser ut så här:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Spara det fullständiga värdet för steg 3 i det här avsnittet.

1. Skapa en roll tilldelning för att ge rätt åtkomst för AKS-klustret till att använda avbildningar som lagras i behållar registret. Ersätt `<appId>` och `<acrId>` med de värden som samlats in i föregående två steg.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Skapa Azure Kubernetes-tjänst

1. Skapa Kubernetes-klustret. Alla parameter värden är från föregående avsnitt förutom parametern name. Välj ett namn som visar vem som skapade det och dess syfte, till exempel `patty-kube` .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Det här steget kan ta några minuter. Resultatet är:

    ```output
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    Tjänsten har skapats, men den har inte en webbplats behållare eller en behållare för språk identifiering ännu.  

1. Hämta autentiseringsuppgifter för Kubernetes-klustret.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Läs in Orchestration-definitionen i din Kubernetes-tjänst

I det här avsnittet används **kubectl** CLI för att kommunicera med Azure Kubernetes-tjänsten.

1. Innan du läser in Orchestration-definitionen kontrollerar du att **kubectl** har åtkomst till noderna.

    ```console
    kubectl get nodes
    ```

    Svaret ser ut så här:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Kopiera följande fil och ge den namnet `language.yml` . Filen har ett `service` avsnitt och ett `deployment` avsnitt för de två behållar typerna, `language-frontend` webbplats behållaren och `language` identifierings behållaren.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Ändra distributions raderna för språk klient delen av som `language.yml` baseras på följande tabell för att lägga till dina egna avbildnings namn, klient hemlighet och text analys inställningar.

    Distributions inställningar för språk klient del|Syfte|
    |--|--|
    |Rad 32<br> `image` immaterialrätt|Avbildnings plats för klient dels avbildningen i Container Registry<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Rad 44<br> `name` immaterialrätt|Container Registry hemlighet för avbildningen, som kallas `<client-secret>` i ett föregående avsnitt.|

1. Ändra språk distributions raderna i `language.yml` baserat på följande tabell om du vill lägga till egna avbildnings namn, klient hemlighet och text analys inställningar för behållare.

    |Språk distributions inställningar|Syfte|
    |--|--|
    |Rad 78<br> `image` immaterialrätt|Avbildnings plats för språk avbildningen i din Container Registry<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Rad 95<br> `name` immaterialrätt|Container Registry hemlighet för avbildningen, som kallas `<client-secret>` i ett föregående avsnitt.|
    |Rad 91<br> `apiKey` immaterialrätt|Din nyckel för text analys resurs|
    |Rad 92<br> `billing` immaterialrätt|Fakturerings slut punkten för din text Analytics-resurs.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Eftersom **apiKey** och **fakturerings slut punkten** anges som en del av Kubernetes Orchestration-definitionen behöver inte webbplats behållaren känna till dessa eller skicka dem som en del av begäran. Webbplats behållaren refererar till behållaren för språk identifiering efter dess Orchestrator-namn `language` .

1. Läs in Orchestration-definitions filen för det här exemplet från den mapp där du skapade och sparade `language.yml` .

    ```console
    kubectl apply -f language.yml
    ```

    Svaret är:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Hämta externa IP-adresser för behållare

För de två behållarna kontrollerar du att `language-frontend` och `language` -tjänsterna körs och hämtar den externa IP-adressen.

```console
kubectl get all
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Om `EXTERNAL-IP` tjänsten för tjänsten visas som väntande kör du kommandot igen tills IP-adressen visas innan du går vidare till nästa steg.

## <a name="test-the-language-detection-container"></a>Testa språk identifierings behållaren

Öppna en webbläsare och gå till den externa IP-adressen för `language` behållaren från föregående avsnitt: `http://<external-ip>:5000/swagger/index.html` . Du kan använda `Try it` funktionen i API: et för att testa slut punkten för språk identifiering.

![Visa behållarens dokumentation om Swagger](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testa klient program behållaren

Ändra URL: en i webbläsaren till den externa IP-adressen för `language-frontend` behållaren med följande format: `http://<external-ip>/helloworld` . Den engelska kultur texten i `helloworld` är förväntad som `English` .

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med klustret tar du bort Azure-resurs gruppen.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Relaterad information

* [kubectl för Docker-användare](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Cognitive Services-containrar](../cognitive-services-container-support.md)
