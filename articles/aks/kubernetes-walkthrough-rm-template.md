---
title: Snabbstart – Skapa ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster med Azure Resource Manager mall och distribuerar ett program i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: 4bcaafdb1f465fb8568078dfb5bfaf988d0cd587
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764449"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Snabbstart: Distribuera ett Azure Kubernetes Service (AKS)-kluster med hjälp av en ARM-mall

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här snabbstarten kommer du att:
* Distribuera ett AKS-kluster med en Azure Resource Manager mall. 
* Kör ett program med flera containrar med en webb-frontend och en Redis-instans i klustret. 

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.61 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

- Om du vill skapa ett AKS-kluster Resource Manager en mall anger du en offentlig SSH-nyckel. Om du behöver den här resursen kan du se följande avsnitt: gå annars till [avsnittet Granska](#review-the-template) mallen.

### <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

För att få åtkomst till AKS-noder ansluter du med hjälp av ett SSH-nyckelpar (offentligt och privat), som du genererar med `ssh-keygen` kommandot . Som standard skapas dessa filer i katalogen *~/.ssh.* Om du `ssh-keygen` kör kommandot skriver du över alla SSH-nyckelpar med samma namn som redan finns på den angivna platsen.

1. Gå till [https://shell.azure.com](https://shell.azure.com) för att Cloud Shell i webbläsaren.

1. Kör kommandot `ssh-keygen`. I följande exempel skapas ett SSH-nyckelpar med RSA-kryptering och en bitlängd på 2 048:

    ```console
    ssh-keygen -t rsa -b 2048
    ```

Mer information om hur du skapar SSH-nycklar finns [i Skapa och hantera SSH-nycklar för autentisering i Azure.][ssh-keys]

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten är från [Azure snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-aks/).

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

Fler AKS-exempel finns på webbplatsen [för AKS-snabbstartsmallar.][aks-quickstart-templates]

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande knapp för att logga in på Azure och öppna en mall.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Välj eller ange följande värden.

    I den här snabbstarten lämnar du standardvärdena för *OS-diskstorleken GB*, *Antal agenter,* VM-agentstorlek, *OS-typ* och *Kubernetes-version*.  Ange egna värden för följande mallparametrar:

    * **Prenumeration:** Välj en Azure-prenumeration.
    * **Resursgrupp:** Välj **Skapa ny**. Ange ett unikt namn för resursgruppen, till exempel *myResourceGroup,* och välj sedan **OK.**
    * **Plats:** Välj en plats, till exempel **USA, östra.**
    * **Klusternamn:** Ange ett unikt namn för AKS-klustret, till exempel *myAKSCluster*.
    * **DNS-prefix:** Ange ett unikt DNS-prefix för klustret, till exempel *myakscluster*.
    * **Användarnamn för Linux-administratör:** Ange ett användarnamn för att ansluta med SSH, till *exempel azureuser*.
    * **Offentlig SSH RSA-nyckel:**  Kopiera och klistra in den offentliga delen av ditt SSH-nyckelpar (som standard innehållet i *~/.ssh/id_rsa.pub*).

    ![Resource Manager mall för att skapa Azure Kubernetes Service kluster i portalen](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Välj **Granska + skapa**.

Det tar några minuter att skapa AKS-klustret. Vänta tills klustret har distribuerats innan du går vidare till nästa steg.

## <a name="validate-the-deployment"></a>Verifiera distributionen

### <a name="connect-to-the-cluster"></a>Anslut till klustret

Om du vill hantera ett Kubernetes-kluster använder du Kubernetes-kommandoradsklienten [kubectl][kubectl]. `kubectl` har redan installerats om du använder Azure Cloud Shell. 

1. Installera `kubectl` lokalt med kommandot az [aks install-cli:][az-aks-install-cli]

    ```azurecli
    az aks install-cli
    ```

2. Konfigurera `kubectl` för att ansluta till kubernetes-klustret med kommandot az [aks get-credentials.][az-aks-get-credentials] Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Verifiera anslutningen till klustret med kommandot [kubectl get.][kubectl-get] Det här kommandot returnerar en lista över klusternoderna.

    ```console
    kubectl get nodes
    ```

    Utdata visar noderna som skapades i föregående steg. Kontrollera att statusen för alla noder är *Klar:*

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>Kör programmet

En [Kubernetes-manifestfil][kubernetes-deployment] definierar ett klusters önskade tillstånd, till exempel vilka containeravbildningar som ska köras. 

I den här snabbstarten använder du ett manifest för att skapa alla objekt som behövs för att köra [Azure Vote-programmet][azure-vote-app]. Det här manifestet innehåller [två Kubernetes-distributioner:][kubernetes-deployment]
* Python-exempelprogrammen för Azure Vote.
* En Redis-instans. 

Två [Kubernetes-tjänster][kubernetes-service] skapas också:
* En intern tjänst för Redis-instansen.
* En extern tjänst för att få åtkomst till Azure Vote-programmet från Internet.

1. Skapa en fil som heter `azure-vote.yaml`.
    * Om du använder Azure Cloud Shell kan den här filen skapas med eller som `vi` om du arbetar med ett `nano` virtuellt eller fysiskt system
1. Kopiera följande YAML-definition:

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

Övervaka förloppet med [kommandot kubectl get service][kubectl-get] med argumentet `--watch` .

```console
kubectl get service azure-vote-front --watch
```

**EXTERNAL-IP-utdata** för `azure-vote-front` tjänsten visas inledningsvis som *väntande*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När **EXTERNAL-IP-adressen** ändras från *väntande till* en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl` bevakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se hur Azure Vote-appen fungerar i praktiken så öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan undvika Azure-kostnader genom att rensa bort onödiga resurser. Använd kommandot [az group delete][az-group-delete] för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete].
> 
> Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="get-the-code"></a>Hämta koden

Befintliga containeravbildningar användes i den här snabbstarten för att skapa en Kubernetes-distribution. Relaterad programkod, Dockerfile och Kubernetes-manifestfil [finns på GitHub.][azure-vote-app]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du ett Kubernetes-kluster och distribuerade sedan ett program med flera containrar till det. [Öppna Kubernetes-webbinstrumentpanelen][kubernetes-dashboard] för ditt AKS-kluster.

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
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
