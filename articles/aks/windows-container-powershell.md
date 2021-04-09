---
title: Skapa en Windows Server-behållare i ett AKS-kluster med hjälp av PowerShell
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster, distribuerar ett program i en Windows Server-behållare i Azure Kubernetes service (AKS) med hjälp av PowerShell.
services: container-service
ms.topic: article
ms.date: 03/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b877ecbdca06ff73d152e1b491e993798a99f98a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103233522"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>Skapa en Windows Server-behållare i ett Azure Kubernetes service-kluster (AKS) med PowerShell

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här artikeln distribuerar du ett AKS-kluster med hjälp av PowerShell. Du distribuerar också ett `ASP.NET` exempel program i en Windows Server-behållare till klustret.

![Bild av bläddring till ASP.NET exempel program](media/windows-container-powershell/asp-net-sample-app.png)

Den här artikeln förutsätter grundläggande kunskaper om Kubernetes-koncept. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell][install-azure-powershell]. Du måste också installera PowerShell-modulen AZ. AKS: 

```azurepowershell-interactive
Install-Module Az.Aks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj ett angivet prenumerations-ID med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder flera Node-pooler:

* Du kan inte ta bort den första noden.

Följande ytterligare begränsningar gäller för Windows Server Node-pooler:

* AKS-klustret kan ha högst 10 noder i pooler.
* AKS-klustret kan ha högst 100 noder i varje Node-pool.
* Namnet på Windows Server-adresspoolen innehåller högst 6 tecken.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En [Azure-resurs grupp](../azure-resource-manager/management/overview.md) är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. Den här platsen är den plats där resurs gruppens metadata lagras, men det är även där dina resurser körs i Azure om du inte anger någon annan region när du skapar en resurs. Skapa en resurs grupp med cmdlet: en [New-AzResourceGroup][new-azresourcegroup] .

I följande exempel skapas en resursgrupp med namnet **myResourceGroup** på platsen **eastus**.

> [!NOTE]
> I den här artikeln används PowerShell-syntax för kommandona i den här självstudien. Om du använder Azure Cloud Shell kontrollerar du att List rutan längst upp till vänster i Cloud Shells fönstret är inställd på **PowerShell**.

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

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Använd `ssh-keygen` kommando rads verktyget för att generera ett SSH-nyckelpar. Mer information finns i [snabb steg: skapa och använda ett offentligt privat privat nyckel par för virtuella Linux-datorer i Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

Om du vill köra ett AKS-kluster som har stöd för resurspooler för Windows Server-behållare måste klustret använda en nätverks princip som använder [Azure cni][azure-cni-about] (avancerat) nätverks-plugin. Mer detaljerad information om hur du planerar ut nödvändiga undernät och nätverks överväganden finns i [Konfigurera Azure cni Networking][use-advanced-networking]. Använd cmdleten [New-AzAks][new-azaks] nedan om du vill skapa ett AKS-kluster med namnet **myAKSCluster**. I följande exempel skapas nödvändiga nätverks resurser om de inte finns.

> [!NOTE]
> För att säkerställa att klustret fungerar på ett tillförlitligt sätt bör du köra minst två (två) noder i standardnodens adresspool.

```azurepowershell-interactive
$Username = Read-Host -Prompt 'Please create a username for the administrator credentials on your Windows Server containers: '
$Password = Read-Host -Prompt 'Please create a password for the administrator credentials on your Windows Server containers: ' -AsSecureString
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName $Username -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> Om du inte kan skapa AKS-klustret eftersom versionen inte stöds i den här regionen kan du använda `Get-AzAksVersion -Location eastus` kommandot för att hitta listan över versioner som stöds för den här regionen.

Efter några minuter slutförs kommandot och returnerar information om klustret. Ibland kan det ta längre tid än några minuter att etablera klustret. Tillåt upp till 10 minuter i dessa fall.

## <a name="add-a-windows-server-node-pool"></a>Lägga till en pool för Windows Server-noder

Som standard skapas ett AKS-kluster med en Node-pool som kan köra Linux-behållare. Använd `New-AzAksNodePool` cmdlet för att lägga till en noduppsättning som kan köra Windows Server-behållare tillsammans med Linux-noden.

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -VmSetType VirtualMachineScaleSets -OsType Windows -Name npwin
```

Kommandot ovan skapar en ny Node-pool med namnet **npwin** och lägger till den i **myAKSCluster**. När du skapar en Node-pool för att köra Windows Server-behållare, är standardvärdet för **VmSize** **Standard_D2s_v3**. Om du väljer att ange parametern **VmSize** kontrollerar du listan med [begränsade VM-storlekar][restricted-vm-sizes]. Den minsta rekommenderade storleken är **Standard_D2s_v3**. Föregående kommando använder också standard under nätet i det virtuella nätverk som skapas när det körs `New-AzAks` .

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att hantera Kubernetes-kluster använder du [kubectl][kubectl], Kubernetes kommandoradsklient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. Om du vill installera `kubectl` lokalt använder du `Install-AzAksKubectl` cmdleten:

```azurepowershell-interactive
Install-AzAksKubectl
```

Om du vill konfigurera `kubectl` för att ansluta till Kubernetes-klustret använder du cmdleten [import-AzAksCredential][import-azakscredential] . Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```azurepowershell-interactive
kubectl get nodes
```

I följande exempel på utdata visas alla noder i klustret. Kontrol lera att status för alla noder är **klar**:

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>Kör programmet

En Kubernetes-manifestfil definierar ett önskat tillstånd för klustret, till exempel vilka containeravbildningar som ska köras. I den här artikeln används ett manifest för att skapa alla objekt som behövs för att köra ASP.NET-exempel programmet i en Windows Server-behållare. Det här manifestet innehåller en [Kubernetes-distribution][kubernetes-deployment] för exempel programmet ASP.net och en extern [Kubernetes-tjänst][kubernetes-service] för att komma åt programmet från Internet.

Exempel programmet ASP.NET tillhandahålls som en del av .NET Framework- [exempel][dotnet-samples] och körs i en Windows Server-behållare. AKS kräver att Windows Server-behållare baseras på avbildningar av **Windows server 2019** eller senare. Manifest filen Kubernetes måste också definiera en [Node-selektor][node-selector] som talar om för ditt AKS-kluster att köra ASP.NET-Pod på en nod som kan köra Windows Server-behållare.

Skapa en fil med namnet `sample.yaml` och kopiera följande YAML-definition. Om du använder Azure Cloud Shell, kan du skapa filen med `vi` eller `nano` som om du arbetar i ett virtuellt eller fysiskt system:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Distribuera programmet med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

Följande exempel på utdata visar att distributionen och tjänsten har skapats:

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs exponerar en Kubernetes-tjänst programfrontend-programmet till Internet.
Den här processen kan ta ett par minuter att slutföra. Ibland kan tjänsten ta längre tid än några minuter att etablera. Tillåt upp till 10 minuter i dessa fall.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`.

```azurepowershell-interactive
kubectl get service sample --watch
```

Inlednings vis visas den **externa IP-adressen** för **exempel** tjänsten som **väntar**.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När **EXTERNAL-IP**-adressen ändras från **väntande** till en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se exempel appen i praktiken öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Bild av bläddring till ASP.NET exempel program](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> Om du får en tids gräns för anslutningen när du försöker läsa in sidan bör du kontrol lera att exempel appen är klar med följande kommando `kubectl get pods --watch` . Ibland kommer Windows-behållaren inte att startas när den externa IP-adressen är tillgänglig.

## <a name="delete-cluster"></a>Ta bort klustret

När klustret inte längre behövs använder du cmdleten [Remove-AzResourceGroup][remove-azresourcegroup] för att ta bort resurs gruppen, behållar tjänsten och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete]. Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte borttagning.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du distribuerat ett Kubernetes-kluster och distribuerat ett `ASP.NET` exempel program i en Windows Server-behållare till det. [Öppna Kubernetes-webbinstrumentpanelen][kubernetes-dashboard] för det kluster som du skapade.

Om du vill lära dig mer om AKS, och gå igenom ett exempel med fullständig distributionskod, fortsätter du till självstudiekursen om Kubernetes-kluster.

> [!div class="nextstepaction"]
> [Självstudiekurs om AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
