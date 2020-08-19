---
title: 'Snabb start: Distribuera ett AKS-kluster med hjälp av PowerShell'
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster, distribuerar ett program och övervakar prestanda i Azure Kubernetes service (AKS) med hjälp av PowerShell.
services: container-service
ms.topic: quickstart
ms.date: 08/18/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4dde1dcd131a497b60a314513df44cc0443d28ed
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590021"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Snabb start: Distribuera ett Azure Kubernetes service-kluster med PowerShell

I den här snabb starten distribuerar du ett Azure Kubernetes service-kluster (AKS) med PowerShell. AKS är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. Ett program med flera behållare som innehåller en webb-frontend och en Redis-instans körs i klustret. Då ser du hur du övervakar hälsotillståndet för klustret och poddar som kör programmet.

Mer information om hur du skapar en pool med Windows Server-noder finns i [skapa ett AKS-kluster som stöder Windows Server-behållare][windows-container-powershell].

![Röstnings app distribuerad i Azure Kubernetes-tjänsten](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj ett angivet prenumerations-ID med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En [Azure-resurs grupp](../azure-resource-manager/management/overview.md) är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. Den här platsen är den plats där resurs gruppens metadata lagras, men det är även där dina resurser körs i Azure om du inte anger någon annan region när du skapar en resurs. Skapa en resurs grupp med cmdlet: en [New-AzResourceGroup][new-azresourcegroup] .

I följande exempel skapas en resurs grupp med namnet **myResourceGroup** i regionen **östra** .

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Följande exempelutdata visar den resursgrupp som skapats:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Skapa AKS-kluster

Använd `ssh-keygen` kommando rads verktyget för att generera ett SSH-nyckelpar. Mer information finns i [snabb steg: skapa och använda ett offentligt privat privat nyckel par för virtuella Linux-datorer i Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

Använd cmdleten [New-AzAks][new-azaks] för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet **myAKSCluster** och en enda nod. Azure Monitor för behållare är också aktiverat som standard. Det tar flera minuter att slutföra.

> [!NOTE]
> När du skapar ett AKS-kluster skapas automatiskt en andra resurs grupp för att lagra AKS-resurserna. Mer information finns i [Varför är två resurs grupper skapade med AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

```azurepowershell-interactive
New-AzAks -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
```

Efter några minuter slutförs kommandot och returnerar information om klustret.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att hantera Kubernetes-kluster använder du [kubectl][kubectl], Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. Om du vill installera `kubectl` lokalt använder du `Install-AzAksKubectl` cmdleten:

```azurepowershell
Install-AzAksKubectl
```

Om du vill konfigurera `kubectl` för att ansluta till Kubernetes-klustret använder du cmdleten [import-AzAksCredential][import-azakscredential] . I följande exempel hämtas autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```azurepowershell-interactive
.\kubectl get nodes
```

Följande exempelutdata visar den enskilda nod som skapades i föregående steg. Kontrollera att status för noden är **Klar**:

```plaintext
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
```

## <a name="run-the-application"></a>Kör programmet

En Kubernetes-manifestfil definierar ett önskat tillstånd för klustret, till exempel vilka containeravbildningar som ska köras. I den här snabbstarten används ett manifest för att skapa alla objekt som behövs för att köra Azure Vote-programmet. Det här manifestet innehåller två [Kubernetes-distributioner][kubernetes-deployment] – en för exemplet på Azure Vote Python-program och den andra för en Redis-instans. Två [Kubernetes Services skapas också – en intern tjänst för Redis-instansen och en extern tjänst för att få åtkomst till Azures röst program från Internet.

Skapa en fil med namnet `azure-vote.yaml` och kopiera följande YAML-definition. Om du använder Azure Cloud Shell, kan du skapa filen med `vi` eller `nano` som om du arbetar i ett virtuellt eller fysiskt system:

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
        image: redis
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
        image: microsoft/azure-vote-front:v1
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

Distribuera programmet med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```azurepowershell-interactive
.\kubectl apply -f azure-vote.yaml
```

Följande exempelutdata visar de distributioner och tjänster som skapats:

```plaintext
deployment.apps/azure-vote-back created
service/azure-vote-back created
deployment.apps/azure-vote-front created
service/azure-vote-front created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs exponerar en Kubernetes-tjänst programfrontend-programmet till Internet.
Den här processen kan ta ett par minuter att slutföra.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

Till en början visas **EXTERNAL-IP** för **azure-vote-front**-tjänsten som **väntande**.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När **EXTERNAL-IP**-adressen ändras från **väntande** till en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se hur Azure Vote-appen fungerar i praktiken så öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Röstnings app distribuerad i Azure Kubernetes-tjänsten](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

När AKS-klustret skapades har [Azure Monitor för behållare](../azure-monitor/insights/container-insights-overview.md) Aktiver ATS för att avbilda hälso mått för både klusternoderna och poddar. De här hälsomåtten är tillgängliga i Azure-portalen.

## <a name="delete-the-cluster"></a>Ta bort klustret

För att undvika Azure-avgifter bör du rensa resurser som inte behövs. När klustret inte längre behövs använder du cmdleten [Remove-AzResourceGroup][remove-azresourcegroup] för att ta bort resurs gruppen, behållar tjänsten och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete]. Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="get-the-code"></a>Hämta koden

I den här snabbstarten har fördefinierade containeravbildningar användes för att skapa en Kubernetes-distribution. Den tillhörande programkoden, Dockerfile och Kubernetes-manifestfilen finns på GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguiden distribuerade du ett Kubernetes-kluster och distribuerade sedan ett flercontainerprogram till det. Du kan också [komma åt Kubernetes-webbinstrumentpanelen][kubernetes-dashboard] för ditt AKS-kluster.

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
