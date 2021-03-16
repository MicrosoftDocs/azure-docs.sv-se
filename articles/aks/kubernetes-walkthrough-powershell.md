---
title: 'Snabb start: Distribuera ett AKS-kluster med hjälp av PowerShell'
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster, distribuerar ett program och övervakar prestanda i Azure Kubernetes service (AKS) med hjälp av PowerShell.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b61c791390200beac4a18422a4de58dd94fa711
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492905"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Snabb start: Distribuera ett Azure Kubernetes service-kluster med PowerShell

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här snabb starten kommer du att:
* Distribuera ett AKS-kluster med PowerShell. 
* Köra ett program med flera behållare med en webb klient del och en Redis-instans i klustret. 
* Övervaka hälso tillståndet för klustret och poddar som kör ditt program.

Mer information om hur du skapar en pool med Windows Server-noder finns i [skapa ett AKS-kluster som stöder Windows Server-behållare][windows-container-powershell].

![Röstnings app distribuerad i Azure Kubernetes-tjänsten](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du kör PowerShell lokalt installerar du AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du har flera Azure-prenumerationer väljer du rätt prenumerations-ID som resurserna ska faktureras i med hjälp av cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En [Azure-resurs grupp](../azure-resource-manager/management/overview.md) är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resurs grupp uppmanas du att ange en plats. Den här platsen är: 
* Lagrings platsen för dina metadata för resurs gruppen.
* Var dina resurser ska köras i Azure om du inte anger någon annan region när du skapar en resurs. 

I följande exempel skapas en resurs grupp med namnet **myResourceGroup** i regionen **östra** .

Skapa en resurs grupp med cmdlet: en [New-AzResourceGroup][new-azresourcegroup] .

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Utdata för resurs gruppen har skapats:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Skapa AKS-kluster

1. Generera ett SSH-nyckelpar med `ssh-keygen` kommando rads verktyget. 
    * Mer information finns i [snabb steg: skapa och använda ett offentligt privat privat nyckel par för virtuella Linux-datorer i Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Skapa ett AKS-kluster med cmdleten [New-AzAks][new-azaks] . Azure Monitor for containers är aktiverat som standard.

    I följande exempel skapas ett kluster med namnet **myAKSCluster** och en enda nod. 

    ```azurepowershell-interactive
    New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
    ```

Efter några minuter slutförs kommandot och returnerar information om klustret.

> [!NOTE]
> När du skapar ett AKS-kluster skapas automatiskt en andra resurs grupp för att lagra AKS-resurserna. Mer information finns i [Varför är två resurs grupper skapade med AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Om du vill hantera ett Kubernetes-kluster använder du kommando rads klienten Kubernetes, [kubectl][kubectl]. `kubectl` är redan installerat om du använder Azure Cloud Shell. 

1. Installera `kubectl` lokalt med hjälp av `Install-AzAksKubectl` cmdleten:

    ```azurepowershell
    Install-AzAksKubectl
    ```

2. Konfigurera `kubectl` för att ansluta till ditt Kubernetes-kluster med cmdleten [import-AzAksCredential][import-azakscredential] . Följande cmdlet hämtar autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

    ```azurepowershell-interactive
    Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
    ```

3. Kontrol lera anslutningen till klustret med hjälp av kommandot [kubectl get][kubectl-get] . Det här kommandot returnerar en lista över klusternoderna.

    ```azurepowershell-interactive
    .\kubectl get nodes
    ```

    Utdata visar den enskilda noden som skapades i föregående steg. Kontrol lera att nodens status är *klar*:

    ```plaintext
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
    ```

## <a name="run-the-application"></a>Kör programmet

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

    ```azurepowershell-interactive
    .\kubectl apply -f azure-vote.yaml
    ```

    Utdata visar de distributioner och tjänster som har skapats:

    ```plaintext
    deployment.apps/azure-vote-back created
    service/azure-vote-back created
    deployment.apps/azure-vote-front created
    service/azure-vote-front created
    ```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra.

Övervaka förloppet med hjälp av kommandot [kubectl get service][kubectl-get] med `--watch` argumentet.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

Utdata från den **externa IP-adressen** för `azure-vote-front` tjänsten visas i början som *väntande*.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När den **externa IP** -adressen ändras från *väntande* till en verklig offentlig IP-adress, använder `CTRL-C` du för att stoppa `kubectl` bevaknings processen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se hur Azure Vote-appen fungerar i praktiken så öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Röstnings app distribuerad i Azure Kubernetes-tjänsten](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Visa de klusternoder och poddar-hälsomått som registrerats av Azure Monitor för behållare i Azure Portal. 

## <a name="delete-the-cluster"></a>Ta bort klustret

Ta bort onödiga resurser för att undvika Azure-avgifter. Använd cmdleten [Remove-AzResourceGroup][remove-azresourcegroup] för att ta bort resurs gruppen, behållar tjänsten och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
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
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
