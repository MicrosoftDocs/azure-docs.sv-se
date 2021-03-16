---
title: Snabbstart – Skapa ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster med hjälp av en Azure Resource Manager-mall och distribuera ett program i Azure Kubernetes service (AKS)
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: e88c56f050f2f6d1183eef23a844f5eaf1f671c2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492973"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Snabb start: Distribuera ett Azure Kubernetes service-kluster (AKS) med en ARM-mall

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här snabb starten kommer du att:
* Distribuera ett AKS-kluster med hjälp av en Azure Resource Manager-mall. 
* Köra ett program med flera behållare med en webb klient del och en Redis-instans i klustret. 

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.61 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

- Om du vill skapa ett AKS-kluster med en Resource Manager-mall anger du en offentlig SSH-nyckel. Om du behöver den här resursen kan du läsa mer i följande avsnitt: Annars hoppar du till avsnittet [granska mallen](#review-the-template) .

### <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

För att komma åt AKS-noder ansluter du med hjälp av ett SSH-nyckelpar (offentligt och privat) som du genererar med hjälp av `ssh-keygen` kommandot. Som standard skapas de här filerna i katalogen *~/.ssh* . Om du kör `ssh-keygen` kommandot skrivs eventuella SSH-nyckelpar med samma namn redan på den aktuella platsen.

1. Gå till [https://shell.azure.com](https://shell.azure.com) för att öppna Cloud Shell i webbläsaren.

1. Kör kommandot `ssh-keygen`. I följande exempel skapas ett SSH-nyckelpar med RSA-kryptering och en bit-längd på 2048:

    ```console
    ssh-keygen -t rsa -b 2048
    ```

Mer information om hur du skapar SSH-nycklar finns i [skapa och hantera SSH-nycklar för autentisering i Azure][ssh-keys].

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten är från [Azure snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

Fler AKS-exempel finns på webbplatsen för [AKS snabb starts mallar][aks-quickstart-templates] .

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande knapp för att logga in på Azure och öppna en mall.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Välj eller ange följande värden.

    I den här snabb starten lämnar du standardvärdena *för OS-diskens storlek GB*, *antal agenter*, *virtuell dator storlek för virtuell dator*, *OS-typ* och Kubernetes- *version*. Ange dina egna värden för följande mallparametrar:

    * **Prenumeration**: Välj en Azure-prenumeration.
    * **Resurs grupp**: Välj **Skapa ny**. Ange ett unikt namn för resurs gruppen, till exempel *myResourceGroup*, och välj sedan **OK**.
    * **Plats**: Välj en plats, t. ex. **USA, östra**.
    * **Kluster namn**: Ange ett unikt namn för AKS-klustret, till exempel *myAKSCluster*.
    * **DNS-prefix**: Ange ett unikt DNS-prefix för klustret, till exempel *myakscluster*.
    * **Användar namn för Linux-administratör**: Ange ett användar namn för att ansluta med SSH, till exempel *azureuser*.
    * **Offentlig SSH RSA-nyckel**: kopiera och klistra in den *offentliga* delen av ditt SSH-nyckelpar (som standard innehåller innehållet på *~/.ssh/id_rsa. pub*).

    ![Resource Manager-mall för att skapa ett Azure Kubernetes service-kluster i portalen](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Välj **Granska + skapa**.

Det tar några minuter att skapa AKS-klustret. Vänta tills klustret har distribuerats innan du går vidare till nästa steg.

## <a name="validate-the-deployment"></a>Verifiera distributionen

### <a name="connect-to-the-cluster"></a>Anslut till klustret

Om du vill hantera ett Kubernetes-kluster använder du kommando rads klienten Kubernetes, [kubectl][kubectl]. `kubectl` är redan installerat om du använder Azure Cloud Shell. 

1. Installera `kubectl` lokalt med kommandot [AZ AKS install-CLI][az-aks-install-cli] :

    ```azurecli
    az aks install-cli
    ```

2. Konfigurera `kubectl` för att ansluta till ditt Kubernetes-kluster med kommandot [AZ AKS get-credentials][az-aks-get-credentials] . Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Kontrol lera anslutningen till klustret med hjälp av kommandot [kubectl get][kubectl-get] . Det här kommandot returnerar en lista över klusternoderna.

    ```console
    kubectl get nodes
    ```

    Utdata visar de noder som skapades i föregående steg. Kontrol lera att status för alla noder är *klar*:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>Kör programmet

En [Kubernetes manifest fil][kubernetes-deployment] definierar ett klusters önskade tillstånd, till exempel vilka behållar avbildningar som ska köras. 

I den här snabb starten ska du använda ett manifest för att skapa alla objekt som behövs för att köra [Azures röst program][azure-vote-app]. Detta manifest innehåller två [Kubernetes-distributioner][kubernetes-deployment]:
* Azures exempel på python-program för Azure-röstning.
* En Redis-instans. 

Två [Kubernetes-tjänster][kubernetes-service] skapas också:
* En intern tjänst för Redis-instansen.
* En extern tjänst för att få åtkomst till Azures röst program från Internet.

1. Skapa en fil som heter `azure-vote.yaml`.
    * Om du använder Azure Cloud Shell kan filen skapas med `vi` eller `nano` som om du arbetar på ett virtuellt eller fysiskt system
1. Kopiera i följande YAML-definition:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. Distribuera programmet med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    Utdata visar de distributioner och tjänster som har skapats:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

### <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra.

Övervaka förloppet med hjälp av kommandot [kubectl get service][kubectl-get] med `--watch` argumentet.

```console
kubectl get service azure-vote-front --watch
```

Utdata från den **externa IP-adressen** för `azure-vote-front` tjänsten visas i början som *väntande*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När den **externa IP** -adressen ändras från *väntande* till en verklig offentlig IP-adress, använder `CTRL-C` du för att stoppa `kubectl` bevaknings processen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se hur Azure Vote-appen fungerar i praktiken så öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort onödiga resurser för att undvika Azure-avgifter. Använd kommandot [az group delete][az-group-delete] för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete].
> 
> Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="get-the-code"></a>Hämta koden

Befintliga behållar avbildningar användes i den här snabb starten för att skapa en Kubernetes-distribution. Den relaterade program koden, Dockerfile och Kubernetes manifest filen [finns på GitHub.][azure-vote-app]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat ett Kubernetes-kluster och sedan distribuerat ett program med flera behållare till det. [Få åtkomst till Kubernetes-webbinstrumentpanelen][kubernetes-dashboard] för ditt AKS-kluster.

Om du vill lära dig mer om AKS, och gå igenom ett exempel med fullständig distributionskod, fortsätter du till självstudiekursen om Kubernetes-kluster.

> [!div class="nextstepaction"]
> [Självstudiekurs om AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
