---
title: Distribuera ett Java-program med öppen frihet eller WebSphere-frihet på ett Azure Kubernetes service-kluster (AKS)
description: Distribuera ett Java-program med öppen frihet eller WebSphere-frihet på ett Azure Kubernetes service-kluster (AKS).
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: Java, jakartaee, Java-part, mikroprofil, öppen-frihet, WebSphere-frihet, AKS, Kubernetes
ms.openlocfilehash: d0e6f2fea6894378da736ba83a90ee28402ec7f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100007145"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Distribuera ett Java-program med öppen frihet eller WebSphere-frihet på ett Azure Kubernetes service-kluster (AKS)

Den här artikeln visar hur du:  
* Kör ditt Java-, Java-, Jakarta-eller mikroprofil-program på den öppna frihets-eller WebSphere frihets körningen.
* Skapa program Docker-avbildningen med hjälp av Open frihets behållar avbildningar.
* Distribuera programmet för behållare till ett AKS-kluster med hjälp av Open frihet-operatorn.   

Operatorn Open frihet fören klar distributionen och hanteringen av program som körs på Kubernetes-kluster. Med Open frihet-operatör kan du också utföra mer avancerade åtgärder, t. ex. insamling av spår och dum par. 

Mer information om öppen frihet finns [på sidan öppna frihets projekt](https://openliberty.io/). Mer information om IBM WebSphere-frihet finns på [produkt sidan för WebSphere-frihet](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Den här artikeln kräver den senaste versionen av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.
* Om du kör kommandona i den här hand boken lokalt (i stället för Azure Cloud Shell):
  * Förbered en lokal dator med UNIX-liknande operativ system installerat (till exempel Ubuntu, macOS, Windows-undersystem för Linux).
  * Installera en Java SE-implementering (till exempel [AdoptOpenJDK openjdk 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Installera [maven](https://maven.apache.org/download.cgi) 3.5.0 eller högre.
  * Installera [Docker](https://docs.docker.com/get-docker/) för ditt operativ system.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras.  

Skapa en resurs grupp med namnet *Java-frihet-projekt* med kommandot [AZ Group Create](/cli/azure/group#az_group_create) på platsen *öster* . Den här resurs gruppen kommer att användas senare för att skapa Azure Container Registry-instansen (ACR) och AKS-klustret. 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>Skapa en ACR-instans

Använd kommandot [AZ ACR Create](/cli/azure/acr#az_acr_create) för att skapa ACR-instansen. I följande exempel skapas en ACR-instans med namnet *youruniqueacrname*. Kontrol lera att *youruniqueacrname* är unikt i Azure.

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

Efter en kort stund bör du se ett JSON-utdata som innehåller:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Ansluta till ACR-instansen

Du måste logga in på ACR-instansen innan du kan push-överföra en avbildning till den. Verifiera anslutningen genom att köra följande kommandon:

```azurecli-interactive
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

Du bör se `Login Succeeded` i slutet av kommandots utdata om du har loggat in på ACR-instansen.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Använd kommandot [az aks create](/cli/azure/aks#az_aks_create) för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* och en enda nod. Det tar flera minuter att slutföra.

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret, inklusive följande:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Ansluta till AKS-klustret

För att hantera Kubernetes-kluster använder du [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. För att installera `kubectl` lokalt använder du kommandot [az aks install-cli](/cli/azure/aks#az_aks_install_cli):

```azurecli-interactive
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials). Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
```

> [!NOTE]
> Kommandot ovan använder standard platsen för [konfigurations filen Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), som är `~/.kube/config` . Du kan ange en annan plats för Kubernetes-konfigurations filen med *--File*.

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Följande exempelutdata visar den enskilda nod som skapades i föregående steg. Kontrollera att status för noden är *Klar*:

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Installera Open frihet-operatör

När du har skapat och anslutit till klustret installerar du [operatorn Open frihet](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) genom att köra följande kommandon.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Bygg program avbildning

För att distribuera och köra ditt frihets program på AKS-klustret, Använd ditt program som en Docker-avbildning med hjälp av [Open frihet behållar avbildningar](https://github.com/OpenLiberty/ci.docker) eller [WebSphere frihets behållar avbildningar](https://github.com/WASdev/ci.docker)

1. Klona exempel koden för den här guiden. Exemplet finns på [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks).
1. Ändra katalog till `javaee-app-simple-cluster` av din lokala klon.
1. Kör `mvn clean package` för att paketera programmet.
1. Kör `mvn liberty:dev` för att testa programmet. Du bör se `The defaultServer server is ready to run a smarter planet.` utdata för kommandot om det lyckas. Används `CTRL-C` för att stoppa programmet.
1. Kör något av följande kommandon för att skapa program avbildningen och push-överföra den till ACR-instansen.
   * Skapa med öppen frihets bas avbildning om du föredrar att använda Open frihet som en lätt öppen källa Java™ Runtime:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Skapa med WebSphere frihets bas avbildning om du föredrar att använda en kommersiell version av Open frihet:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Distribuera program i AKS-klustret

Följ stegen nedan för att distribuera frihets programmet i AKS-klustret.

1. Skapa en pull-hemlighet så att AKS-klustret autentiseras för att hämta avbildning från ACR-instansen.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Kontrol lera att den aktuella arbets katalogen är `javaee-app-simple-cluster` av din lokala klon.
1. Kör följande kommandon för att distribuera ditt frihets program med 3 repliker till AKS-klustret. Kommandoutdata visas också infogat.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. Vänta tills du ser `3/3` under `READY` kolumnen och `3` under `AVAILABLE` kolumnen använder `CTRL-C` du för att stoppa `kubectl` bevaknings processen.

### <a name="test-the-application"></a>Testa programmet

När programmet körs visar en Kubernetes belastnings Utjämnings tjänst program klient delen till Internet. Den här processen kan ta en stund att slutföra.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) med argumentet `--watch`.

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

När den *externa IP* -adressen ändras från *väntande* till en verklig offentlig IP-adress, använder `CTRL-C` du för att stoppa `kubectl` bevaknings processen.

Öppna en webbläsare till den externa IP-adressen för din tjänst ( `52.152.189.57` i exemplet ovan) för att se programmets start sida. Du bör se namnet på Pod för dina program repliker som visas längst upp till vänster på sidan. Vänta några minuter och uppdatera sidan om du vill se ett annat Pod-namn som visas på grund av belastnings utjämning som tillhandahålls av AKS-klustret.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="Java-frihets program har distribuerats på AKS":::

>[!NOTE]
> - Programmet använder inte HTTPS för närvarande. Vi rekommenderar att du [aktiverar TLS med dina egna certifikat](ingress-own-tls.md).

## <a name="clean-up-the-resources"></a>Rensa resurserna

För att undvika Azure-avgifter bör du rensa onödiga resurser.  När klustret inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az_group_delete) för att ta bort resurs gruppen, Container Service, container Registry och alla relaterade resurser.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om referenser som används i den här guiden:

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Öppen frihet](https://openliberty.io/)
* [Öppen frihets operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Öppna frihets Server konfigurationen](https://openliberty.io/docs/ref/config/)
* [Frihets maven-plugin](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Öppna frihets behållar avbildningar](https://github.com/OpenLiberty/ci.docker)
* [WebSphere frihets behållar avbildningar](https://github.com/WASdev/ci.docker)
